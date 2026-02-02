# Frontend Architecture

## Component Architecture

### Component Organization

```text
src/
├── app/
│   ├── app-routing.module.ts
│   ├── app.component.html
│   ├── app.component.scss
│   ├── app.component.ts
│   ├── app.module.ts
│   └── core/                 # Singleton services, authentication, error handling
│       ├── guards/
│       ├── interceptors/
│       └── services/
│   ├── features/             # Feature modules (e.g., event-management, user-profile)
│   │   ├── event-management/
│   │   │   ├── components/   # Presentational components specific to this feature
│   │   │   ├── containers/   # Smart/stateful components, interact with NgRx
│   │   │   ├── services/     # Feature-specific services
│   │   │   ├── event-management-routing.module.ts
│   │   │   └── event-management.module.ts
│   │   └── user-profile/
│   │       └── ...
│   ├── shared/               # Reusable components, directives, pipes, modules
│   │   ├── components/       # Common UI elements (e.g., button, dialog, spinner)
│   │   ├── directives/
│   │   ├── pipes/
│   │   └── shared.module.ts
│   └── state/                # Global NgRx store structure (actions, reducers, effects, selectors)
│       └── ...
├── assets/                   # Static assets (images, icons)
├── environments/             # Environment-specific configurations
├── styles/                   # Global styles, variables, Tailwind CSS base
└── main.ts                   # Application entry point
```

### Component Template

```typescript
import { ChangeDetectionStrategy, Component, Input, Output, EventEmitter } from '@angular/core';
import { CommonModule } from '@angular/common'; // Required for common directives like ngIf, ngFor

/**
 * Reusable Card component for displaying various types of content.
 *
 * Usage:
 * <app-card [title]="'Card Title'" [subtitle]="'Card Subtitle'" (cardClick)="onCardClicked($event)">
 *   <p>Card content goes here.</p>
 * </app-card>
 */
@Component({
  selector: 'app-card',
  standalone: true, // Use standalone components where possible for better modularity
  imports: [CommonModule],
  template: `
    <div class="card bg-white shadow-md rounded-lg p-6 mb-4" (click)="onCardClick()">
      <h3 *ngIf="title" class="text-xl font-semibold mb-1">{{ title }}</h3>
      <h4 *ngIf="subtitle" class="text-gray-600 text-sm mb-3">{{ subtitle }}</h4>
      <div class="card-content">
        <ng-content></ng-content>
      </div>
      <button *ngIf="showAction" (click)="onActionClick($event)" class="mt-4 px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600">
        {{ actionLabel }}
      </button>
    </div>
  `,
  styles: [`
    .card {
      cursor: pointer;
      transition: transform 0.2s ease-in-out;
    }
    .card:hover {
      transform: translateY(-2px);
    }
  `],
  changeDetection: ChangeDetectionStrategy.OnPush, // Optimize change detection
})
export class CardComponent {
  @Input() title?: string;
  @Input() subtitle?: string;
  @Input() showAction: boolean = false;
  @Input() actionLabel: string = 'Action';

  @Output() cardClick = new EventEmitter<void>();
  @Output() actionClick = new EventEmitter<MouseEvent>();

  onCardClick(): void {
    this.cardClick.emit();
  }

  onActionClick(event: MouseEvent): void {
    event.stopPropagation(); // Prevent cardClick from firing
    this.actionClick.emit(event);
  }
}
```

## State Management Architecture

### State Structure

```typescript
// src/app/state/app.state.ts
import { OrderEventsState } from '../features/event-management/state/order-events.reducer';

/**
 * Interface for the entire application state.
 * Each feature module will contribute its own slice of state.
 */
export interface AppState {
  orderEvents: OrderEventsState;
  // Other feature states can be added here
  // users: UsersState;
  // settings: SettingsState;
}

// src/app/features/event-management/state/order-events.reducer.ts (conceptual)
// This would be within the feature module itself.

import { OrderEvent } from '@shared/types'; // Assuming shared types package

/**
 * Interface for the OrderEvents feature state.
 */
export interface OrderEventsState {
  events: OrderEvent[]; // Array of order events
  selectedEventId: string | null; // ID of the currently selected event
  loading: boolean; // Flag to indicate if events are being loaded
  error: any | null; // Error object if an operation fails
  filter: {
    type: string | null;
    status: string | null;
    searchTerm: string | null;
  };
  sort: {
    field: 'createdAt' | 'type' | 'status';
    direction: 'asc' | 'desc';
  };
  pagination: {
    pageIndex: number;
    pageSize: number;
    totalCount: number;
  };
}
```

### State Management Patterns

-   **Feature-centric State:** Each major application feature (e.g., `OrderEvents`, `Auth`) will own its slice of the global state, promoting modularity and encapsulation. This helps keep the codebase organized and easier to scale.
-   **Actions as Events:** All changes to the application state must be initiated by dispatching an Action. Actions are plain objects that describe unique events that occurred in the application, ensuring a clear and traceable audit log of state changes.
-   **Pure Reducers:** Reducers are pure functions that take the current state and an action, and return a *new* state. They must not mutate the original state directly and should be free of side effects, making state changes predictable and testable.
-   **Effects for Side Effects:** All asynchronous operations (like API calls to the User Event Hub API, or interactions with the SignalR Service) and other side effects will be handled by NgRx Effects. Effects listen for dispatched actions, perform operations, and then dispatch new actions to update the state, maintaining a clear separation of concerns.
-   **Selectors for State Access:** Components will access state slices using NgRx Selectors. Selectors are pure functions that derive pieces of state from the store, allowing for efficient, memoized access to data and preventing components from having direct knowledge of the state's internal structure.
-   **One-Way Data Flow:** The overall architecture will adhere to a strict one-way data flow: UI Dispatches Action -> Effects handle side effects -> Reducers update State -> Selectors project State to UI. This pattern simplifies debugging and makes the application's behavior more predictable.

## Routing Architecture

### Route Organization

```text
src/app/
├── app-routing.module.ts       # Root routing module
├── core/
│   └── auth/
│       └── auth-routing.module.ts # Auth related routes (login, logout, callback)
├── features/
│   ├── event-management/
│   │   └── event-management-routing.module.ts # Feature-specific routes (list, detail, create)
│   ├── dashboard/
│   │   └── dashboard-routing.module.ts # Dashboard routes
│   └── user-profile/
│       └── user-profile-routing.module.ts # User profile routes
├── shared/
│   └── shared-routing.module.ts # Routes for shared components if any (rarely used)
└── pages/                      # Top-level pages (e.g., home, about, 404)
    ├── home/
    │   └── home-routing.module.ts
    └── not-found/
        └── not-found-routing.module.ts
```

### Protected Route Pattern

```typescript
// src/app/core/guards/auth.guard.ts
import { Injectable } from '@angular/core';
import { CanActivate, ActivatedRouteSnapshot, RouterStateSnapshot, UrlTree, Router } from '@angular/router';
import { Observable, of } from 'rxjs';
import { switchMap, take, tap, catchError } from 'rxjs/operators';
import { AuthService } from '../services/auth.service'; // Our custom auth service
import { Store } from '@ngrx/store';
import { selectIsAuthenticated } from '../state/auth.selectors'; // NgRx selector for auth state
import *as AuthActions from '../state/auth.actions';

@Injectable({
  providedIn: 'root'
})
export class AuthGuard implements CanActivate {
  constructor(
    private authService: AuthService,
    private router: Router,
    private store: Store
  ) {}

  canActivate(
    route: ActivatedRouteSnapshot,
    state: RouterStateSnapshot
  ): Observable<boolean | UrlTree> | Promise<boolean | UrlTree> | boolean | UrlTree {
    return this.store.select(selectIsAuthenticated).pipe(
      take(1), // Take the first value and complete
      switchMap(isAuthenticated => {
        if (isAuthenticated) {
          // User is authenticated, allow access
          return of(true);
        }
        else {
          // User is not authenticated, try to acquire token silently or redirect to login
          return this.authService.acquireTokenSilent().pipe(
            switchMap(success => {
              if (success) {
                // Successfully acquired token silently, user is now authenticated
                this.store.dispatch(AuthActions.loginSuccess()); // Update NgRx state
                return of(true);
              }
              else {
                // Cannot acquire token silently, redirect to login page
                this.router.navigate(['/login'], { queryParams: { returnUrl: state.url } });
                return of(false);
              }
            }),
            catchError(err => {
              // Handle any errors during silent token acquisition (e.g., user needs to log in interactively)
              console.error('Silent token acquisition failed:', err);
              this.router.navigate(['/login'], { queryParams: { returnUrl: state.url } });
              return of(false);
            })
          );
        }
      })
    );
  }
}
```

## Frontend Services Layer

### API Client Setup

```typescript
// src/app/core/services/api-client.service.ts
import { Injectable } from '@angular/core';
import { HttpClient, HttpHeaders } from '@angular/common/http';
import { Observable } from 'rxjs';
import { environment } from '../../../environments/environment';

/**
 * Generic API client service for interacting with the backend.
 * All API calls should go through this service or services built upon it.
 */
@Injectable({
  providedIn: 'root'
})
export class ApiClientService {
  private readonly apiUrl = environment.apiUrl;

  constructor(private http: HttpClient) {}

  private getHeaders(contentType: string = 'application/json'): HttpHeaders {
    return new HttpHeaders({
      'Content-Type': contentType,
      // Authorization header will be added by AuthInterceptor
    });
  }

  get<T>(path: string, options?: { headers?: HttpHeaders, params?: any }): Observable<T> {
    return this.http.get<T>(`${this.apiUrl}/${path}`, { headers: options?.headers || this.getHeaders(), params: options?.params });
  }

  post<T>(path: string, body: any, options?: { headers?: HttpHeaders }): Observable<T> {
    return this.http.post<T>(`${this.apiUrl}/${path}`, body, { headers: options?.headers || this.getHeaders() });
  }

  put<T>(path: string, body: any, options?: { headers?: HttpHeaders }): Observable<T> {
    return this.http.put<T>(`${this.apiUrl}/${path}`, body, { headers: options?.headers || this.getHeaders() });
  }

  delete<T>(path: string, options?: { headers?: HttpHeaders }): Observable<T> {
    return this.http.delete<T>(`${this.apiUrl}/${path}`, { headers: options?.headers || this.getHeaders() });
  }

  // ... other HTTP methods as needed
}

// src/app/core/interceptors/auth.interceptor.ts
import { Injectable } from '@angular/core';
import {
  HttpRequest,
  HttpHandler,
  HttpEvent,
  HttpInterceptor
} from '@angular/common/http';
import { Observable, from } from 'rxjs';
import { switchMap } from 'rxjs/operators';
import { AuthService } from '../services/auth.service'; // Our custom auth service

/**
 * Interceptor to automatically attach authentication tokens to API requests.
 */
@Injectable()
export class AuthInterceptor implements HttpInterceptor {

  constructor(private authService: AuthService) {}

  intercept(request: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    // Only intercept calls to our backend API
    if (request.url.startsWith(environment.apiUrl)) {
      return from(this.authService.getAccessToken()).pipe( // getAccessToken returns a Promise
        switchMap(token => {
          if (token) {
            const authRequest = request.clone({
              setHeaders: {
                Authorization: `Bearer ${token}`
              }
            });
            return next.handle(authRequest);
          }
          return next.handle(request); // No token available, proceed with original request
        })
      );
    }
    return next.handle(request); // Not our API, skip interception
  }
}

// src/app/app.module.ts (or core.module.ts if using modules)
import { HTTP_INTERCEPTORS } from '@angular/common/http';
import { AuthInterceptor } from './core/interceptors/auth';

// ...
providers: [
  {
    provide: HTTP_INTERCEPTORS,
    useClass: AuthInterceptor,
    multi: true // Important: allows multiple interceptors
  }
],
// ...
```

### Service Example

```typescript
// src/app/features/event-management/services/order-events-api.service.ts
import { Injectable } from '@angular/core';
import { ApiClientService } from '../../core/services/api-client.service';
import { Observable } from 'rxjs';
import { OrderEvent, EventType } from '@shared/types'; // Assuming shared types package
import { Store } from '@ngrx/store';
import { AppState } from '../../state/app.state';
import * as OrderEventsActions from '../state/order-events.actions';
import { tap } from 'rxjs/operators';

/**
 * Service responsible for making API calls related to Order Events.
 * It uses the generic ApiClientService and dispatches NgRx actions.
 */
@Injectable({
  providedIn: 'root'
})
export class OrderEventsApiService {
  private readonly baseUrl = 'events/order'; // Path relative to environment.apiUrl

  constructor(
    private apiClient: ApiClientService,
    private store: Store<AppState>
  ) {}

  /**
   * Submits a new OrderEvent to the backend.
   * Dispatches NgRx actions for loading state and success/failure.
   * @param event The OrderEvent to submit.
   * @returns An Observable of the API response.
   */
  submitOrderEvent(event: Omit<OrderEvent, 'id' | 'createdAt' | 'status'>): Observable<{ message: string; eventId: string }> {
    this.store.dispatch(OrderEventsActions.submitOrderEvent()); // Indicate loading state
    return this.apiClient.post<{ message: string; eventId: string }>(this.baseUrl, event).pipe(
      tap(
        response => this.store.dispatch(OrderEventsActions.submitOrderEventSuccess({ eventId: response.eventId })),
        error => this.store.dispatch(OrderEventsActions.submitOrderEventFailure({ error }))
      )
    );
  }

  /**
   * Fetches a list of Order Events from the backend.
   * Dispatches NgRx actions for loading state and success/failure.
   * @param filterParams Optional filter, sort, and pagination parameters.
   * @returns An Observable of an array of OrderEvent.
   */
  getLatestOrderEvents(
    filterParams?: { type?: EventType; status?: string; search?: string; pageIndex?: number; pageSize?: number; sortBy?: string; sortDirection?: 'asc' | 'desc' }
  ): Observable<OrderEvent[]> {
    this.store.dispatch(OrderEventsActions.loadOrderEvents()); // Indicate loading state
    return this.apiClient.get<OrderEvent[]>(this.baseUrl, { params: filterParams }).pipe(
      tap(
        events => this.store.dispatch(OrderEventsActions.loadOrderEventsSuccess({ events })),
        error => this.store.dispatch(OrderEventsActions.loadOrderEventsFailure({ error }))
      )
    );
  }

  // Example of how the SignalR real-time updates might be handled (conceptual) 
  // This would typically be in an NgRx Effect or a dedicated real-time service.
  /*
  listenForRealtimeUpdates(): void {
    this.signalRService.onEventUpdate().subscribe(event => {
      this.store.dispatch(OrderEventsActions.updateOrderEvent({ event }));
    });
  }
  */
}
```

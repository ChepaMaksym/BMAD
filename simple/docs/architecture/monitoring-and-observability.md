# Monitoring and Observability

Define monitoring strategy for fullstack application.

## Monitoring Stack

- **Frontend Monitoring:** Azure Application Insights (for user telemetry, page views, client-side errors)
- **Backend Monitoring:** Azure Application Insights (for request telemetry, dependencies, exceptions)
- **Error Tracking:** Azure Application Insights (integrated with global error handling)
- **Performance Monitoring:** Azure Application Insights (for server-side performance), Lighthouse/Web Vitals (for client-side performance)

## Key Metrics

**Frontend Metrics:**
- Core Web Vitals
- JavaScript errors
- API response times
- User interactions

**Backend Metrics:**
- Request rate
- Error rate
- Response time
- Database query performance

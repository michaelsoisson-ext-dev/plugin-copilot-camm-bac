---
applyTo: "**/*.js,**/*.ts,**/*.json"
description: "Performance optimization guidelines and best practices"
---

# Performance Optimization Guidelines

Apply the repository-wide guidance from `./github/copilot-instructions.md` to all code.

## Database Performance

- Use database indexes appropriately for frequently queried columns
- Avoid N+1 query problems (use JOINs or batch loading)
- Use pagination for large result sets
- Implement query result caching where appropriate
- Monitor slow queries with database logging
- Use database explain plan to analyze query performance
- Connection pooling to avoid connection overhead
- Batch insert/update operations when possible

## API Response Performance

- Implement response compression (gzip) for larger payloads
- Use pagination with reasonable default limits
- Return only necessary fields in API responses
- Implement ETag/conditional requests for caching
- Use HTTP caching headers appropriately (Cache-Control, Expires)
- Implement field selection/projection to reduce data transfer
- Monitor response times and set performance budgets
- Use async operations to avoid blocking

## BullMQ Job Queue Performance

- Process jobs asynchronously, don't wait for completion in API
- Use appropriate concurrency settings based on worker capacity
- Implement job prioritization for critical tasks
- Use job batching when processing multiple similar jobs
- Monitor queue depth and worker performance
- Set appropriate timeout values to prevent hanging jobs
- Use job delays for rate limiting and throttling
- Implement metrics collection for job processing times

## Caching Strategy

- Implement in-memory caching for frequently accessed data (Redis)
- Use cache invalidation strategy appropriate for use case
- Set cache TTL appropriately based on data freshness requirements
- Implement cache warming for critical data
- Monitor cache hit/miss ratios
- Avoid caching sensitive user-specific data
- Use cache versioning to handle cache invalidation

## Memory & Resource Management

- Monitor memory usage and set appropriate limits
- Avoid memory leaks (especially in long-running processes)
- Use streaming for large file operations (don't load into memory)
- Implement proper cleanup in job handlers
- Monitor CPU usage and set resource limits
- Use lazy loading for optional/heavy dependencies
- Implement garbage collection monitoring

## Request Handling Optimization

- Implement request body size limits to prevent DoS
- Use HTTP/2 where applicable for multiplexing
- Implement connection pooling for external service calls
- Set appropriate timeouts for external service calls
- Use request cancellation for abandoned requests
- Implement circuit breaker pattern for external services
- Use connection keep-alive to reduce connection overhead

## Code-level Optimization

- Avoid blocking operations in event loop (use async/await)
- Use native modules for CPU-intensive operations
- Profile code to identify bottlenecks
- Use efficient algorithms and data structures
- Avoid global variables and shared state where possible
- Implement lazy evaluation for expensive computations
- Use spread operator sparingly for large arrays

## Monitoring & Profiling

- Implement performance metrics collection (timing, throughput)
- Use APM (Application Performance Monitoring) tools
- Monitor endpoint response times
- Track job processing times
- Monitor database query performance
- Set up alerts for performance degradation
- Regular performance testing and benchmarking
- Analyze and optimize based on metrics

## Docker & Container Optimization

- Use multi-stage builds to reduce image size
- Implement layer caching in Docker builds
- Set appropriate memory and CPU limits
- Use health checks to identify resource issues
- Monitor container resource usage
- Use container orchestration for scaling

## Load Testing

- Perform load testing before production deployment
- Test with realistic concurrent user patterns
- Identify bottlenecks under load
- Test database under concurrent load
- Test job queue under high job volume
- Implement circuit breakers based on load test results
- Set up auto-scaling policies based on load tests

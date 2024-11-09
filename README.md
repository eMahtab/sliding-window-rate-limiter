# Sliding Window Rate Limiter

```java
import java.time.Instant;
import java.util.ArrayDeque;
import java.util.Deque;

public class SlidingWindowRateLimiter {
    private final int maxRequests;  // Maximum allowed requests in window
    private final long windowSizeInMillis;  // Size of the sliding window in milliseconds
    private final Deque<Long> requestTimestamps;  // Deque to store request timestamps

    public SlidingWindowRateLimiter(int maxRequests, long windowSizeInMillis) {
        this.maxRequests = maxRequests;
        this.windowSizeInMillis = windowSizeInMillis;
        this.requestTimestamps = new ArrayDeque<>();
    }

    public synchronized boolean isAllowed() {
        long currentTime = System.currentTimeMillis();
        // Remove timestamps that are outside the sliding window
        while (!requestTimestamps.isEmpty() &&
                (currentTime - requestTimestamps.peekFirst()) > windowSizeInMillis) {
            requestTimestamps.pollFirst();
        }
        // Check if the number of requests in the current window exceeds the limit
        if (requestTimestamps.size() < maxRequests) {
            requestTimestamps.offerLast(currentTime);  // Add the current request timestamp
            return true;
        } else {
            return false;
        }
    }

    public static void main(String[] args) throws InterruptedException {
        SlidingWindowRateLimiter rateLimiter = new SlidingWindowRateLimiter(5, 10000); // 5 requests per 10 seconds
        // Simulate 22 requests with a small delay
        for (int i = 0; i < 22; i++) {
        	Instant now = Instant.now();
            if (rateLimiter.isAllowed()) {
                System.out.println("Request " + (i + 1) + " is allowed at "+ now);
            } else {
                System.out.println("Request " + (i + 1) + " is denied at " + now);
            }
            Thread.sleep((long)(Math.random() * 3500));  // Delay of 1 second between requests
        }
    }
}
```

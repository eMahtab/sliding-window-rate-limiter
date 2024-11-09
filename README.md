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
            Thread.sleep(1000);  // Delay of 1 second between requests
        }
    }
}
```
### Code Execution Output :
```
Request 1 is allowed at 2024-11-09T04:02:20.262121500Z
Request 2 is allowed at 2024-11-09T04:02:21.301551800Z
Request 3 is allowed at 2024-11-09T04:02:22.313791Z
Request 4 is allowed at 2024-11-09T04:02:23.323556500Z
Request 5 is allowed at 2024-11-09T04:02:24.339159800Z
Request 6 is denied at 2024-11-09T04:02:25.353865700Z
Request 7 is denied at 2024-11-09T04:02:26.366849300Z
Request 8 is denied at 2024-11-09T04:02:27.381426700Z
Request 9 is denied at 2024-11-09T04:02:28.382663800Z
Request 10 is denied at 2024-11-09T04:02:29.398143500Z
Request 11 is allowed at 2024-11-09T04:02:30.401973100Z
Request 12 is allowed at 2024-11-09T04:02:31.406325600Z
Request 13 is allowed at 2024-11-09T04:02:32.417775600Z
Request 14 is allowed at 2024-11-09T04:02:33.433254Z
Request 15 is allowed at 2024-11-09T04:02:34.434122Z
Request 16 is denied at 2024-11-09T04:02:35.435521200Z
Request 17 is denied at 2024-11-09T04:02:36.447427500Z
Request 18 is denied at 2024-11-09T04:02:37.462261100Z
Request 19 is denied at 2024-11-09T04:02:38.477206700Z
Request 20 is denied at 2024-11-09T04:02:39.478813200Z
Request 21 is allowed at 2024-11-09T04:02:40.493071400Z
Request 22 is allowed at 2024-11-09T04:02:41.508283500Z
```

### Simulating random delay between requests : Thread.sleep((long)(Math.random() * 3500));
Sleep randomly between 0 to 3.5 second between requests

Code Execution Output :
```
Request 1 is allowed at 2024-11-09T03:44:57.653659600Z
Request 2 is allowed at 2024-11-09T03:44:59.005025600Z
Request 3 is allowed at 2024-11-09T03:45:01.611763900Z
Request 4 is allowed at 2024-11-09T03:45:02.433560Z
Request 5 is allowed at 2024-11-09T03:45:05.611305600Z
Request 6 is denied at 2024-11-09T03:45:06.983263200Z
Request 7 is allowed at 2024-11-09T03:45:08.210962200Z
Request 8 is denied at 2024-11-09T03:45:08.787640800Z
Request 9 is allowed at 2024-11-09T03:45:09.064418600Z
Request 10 is denied at 2024-11-09T03:45:09.651613500Z
Request 11 is denied at 2024-11-09T03:45:10.016636300Z
Request 12 is denied at 2024-11-09T03:45:11.023326500Z
Request 13 is allowed at 2024-11-09T03:45:12.208614100Z
Request 14 is allowed at 2024-11-09T03:45:13.072809700Z
Request 15 is allowed at 2024-11-09T03:45:15.952182800Z
Request 16 is denied at 2024-11-09T03:45:16.661930Z
Request 17 is denied at 2024-11-09T03:45:17.518609700Z
Request 18 is allowed at 2024-11-09T03:45:18.844815300Z
Request 19 is allowed at 2024-11-09T03:45:19.621371100Z
Request 20 is denied at 2024-11-09T03:45:20.129543900Z
Request 21 is denied at 2024-11-09T03:45:20.565489200Z
Request 22 is allowed at 2024-11-09T03:45:23.631249300Z
```

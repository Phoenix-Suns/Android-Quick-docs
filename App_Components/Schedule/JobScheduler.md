# JobScheduler

Lên lịch, hẹn giờ để thực hiện tác vụ:
- Task khi kết nối nguồn điện, khi kết nối Internet
- Task chạy mỗi ngày 1 lần

```xml
<!-- Manifesh -->
<service
    android:name=".TestJobService"
    android:label="Word service"
    android:permission="android.permission.BIND_JOB_SERVICE" >

</service>
```
```java
package com.vogella.android.localservice;

import android.app.job.JobParameters;
import android.app.job.JobService;
import android.content.Intent;

/**
 * JobService to be scheduled by the JobScheduler.
 * start another service
 */
public class TestJobService extends JobService {
    private static final String TAG = "SyncService";

    @Override
    public boolean onStartJob(JobParameters params) {
        Intent service = new Intent(getApplicationContext(), LocalWordService.class);
        getApplicationContext().startService(service);
        Util.scheduleJob(getApplicationContext()); // reschedule the job
        return true;
    }

    @Override
    public boolean onStopJob(JobParameters params) {
        return true;
    }

}
```
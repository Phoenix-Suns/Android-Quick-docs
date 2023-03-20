# Work Manager

Work Manager là một thư viện được thiết kế cho việc lập lịch và quản lý các tác vụ ngầm. (giống JobScheduler), Dể sử dụng, dể truy cập, dể huỷ, hỗ trợ mọi android 14 trở lên.

```java
class DownloadWorker : Worker() {

    // Define the parameter keys:
    private val KEY_X_ARG = "X"
    private val KEY_Y_ARG = "Y"
    private val KEY_Z_ARG = "Z"

    // The result key:
    private val KEY_RESULT = "result"

    /**
     * This will be called whenever work manager run the work.
     */
    override fun doWork(): WorkerResult {
        // Fetch the arguments (and specify default values):
        val x = inputData.getLong(KEY_X_ARG, 0)
        val y = inputData.getLong(KEY_Y_ARG, 0)
        val z = inputData.getLong(KEY_Z_ARG, 0)

        val timeToSleep = x  + y + z
        Thread.sleep(timeToSleep)

        //...set the output, and we're done!
        val output = Data.Builder()
                .putInt(KEY_RESULT, timeToSleep.toInt())
                .build()

        outputData = output
        // Indicate success or failure with your return value.
        return WorkerResult.SUCCESS
    }
}
```

```java
// === Gọi để chạy ===
// Chạy khi Được Sạc
val myConstraints = Constraints.Builder()
    .setRequiresCharging(true)
    .build()

// Set Input
val myData = Data.Builder()
    .putLong(KEY_X_ARG, 42)
    .putLong(KEY_Y_ARG, 421)
    .putLong(KEY_Z_ARG, 8675309)
    .build()

// Chạy 1 lần
val request = OneTimeWorkRequest.Builder(DownloadWorker::class.java)
    .setConstraints(myConstraints)
    .setInputData(myData)
    .build()
// Chạy định kì
// val request = PeriodicWorkRequest
//     .Builder(DownloadWorker::class.java, 1, TimeUnit.HOURS)
//     .setConstraints(myConstraints)
//     .setInputData(myData)
//     .build()

WorkManager.getInstance().enqueue(request)

// === Nhận Output ===
WorkManager.getInstance()
  .getStatusById(request.id)
  .observe(this@MainActivity, Observer {
    it?.let {
      // Get the output data from the worker.
      val workerResult = it.outputData     
      // Check if the task is finished?
      if (it.state.isFinished) {
        Toast.makeText(this, "Work completed.", Toast.LENGTH_LONG)
            .show()
      } else {
        Toast.makeText(this, "Work failed.", Toast.LENGTH_LONG)
            .show()
      }
    }
})

// === Huỷ tác vụ ===
val compressionWorkId:UUID = compressionWork.getId()
WorkManager.getInstance().cancelWorkById(compressionWorkId)
```

---

## Reference

- https://viblo.asia/p/android-work-manager-lap-lich-cho-cac-tac-vu-maGK7jLa5j2
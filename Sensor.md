# SENSOR
## Các loại Cảm biến trong Android:
1.	Cảm biến gia tốc: Cho biết về lực gia tốc và độ quay của thiết bị theo các trục tọa độ
2.	Cảm biến vị trí: Cho biết về vị trí vật lý của thiết bị
3.	Cảm biến môi trường: Cho biết thông tin về nhiệt độ, độ ẩm ... của môi trường.

![https://github.com/luunghiatran/Android-Quick-docs/blob/master/Images/sensor_1.jpg](./Images/sensor_1.jpg)


## Sử dụng trong Activity:
```ruby
private TextView mTextView_Value;

private SensorManager mSensorManager;
private Sensor mPressure;
//--- Nghe thông tin cảm biến trả về ---
private SensorEventListener mSensorListener = new SensorEventListener() {

    @Override
    public final void onAccuracyChanged(Sensor sensor, int accuracy) {
        // Do something here if sensor accuracy changes.
    }

    @Override
    public final void onSensorChanged(SensorEvent event) {
        float[] values = event.values;
        // Do something with this sensor data.
        mTextView_Value.setText("X:"+values[0]+"\n Y:"+values[1]+"\n Z:"+values[2]);
    }

};

@Override
public final void onCreate(Bundle savedInstanceState) {
    ...
    mTextView_Value = (TextView) findViewById(R.id.textView_Value);
    initSensor();
}

private void initSensor() {
    // Lấy quản lý cảm biến
    mSensorManager = (SensorManager) getSystemService(Context.SENSOR_SERVICE);

	//--- Đặt loại cảm biến
    /*TYPE_AMBIENT_TEMPERATURE	°C	Ambient air temperature.
    TYPE_LIGHT	lx	Illuminance.
    TYPE_PRESSURE hPa or mbar	Ambient air pressure.
    TYPE_RELATIVE_HUMIDITY	%	Ambient relative humidity.
    TYPE_TEMPERATURE °C	Device temperature.1*/
    mPressure = mSensorManager.getDefaultSensor(Sensor.TYPE_ACCELEROMETER);
}

@Override
protected void onResume() {
    super.onResume();
	//--- Nghe sự kiện cảm biến
    mSensorManager.registerListener(mSensorListener, mPressure, SensorManager.SENSOR_DELAY_NORMAL);
}

@Override
protected void onPause() {
    super.onPause();
	//--- không nghe sự kiện nữa
    mSensorManager.unregisterListener(mSensorListener);
}
```
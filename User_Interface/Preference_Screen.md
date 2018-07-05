# Preference Screen - Màn hình cài đặt

 ![image_1](https://github.com/luunghiatran/Android-Quick-docs/blob/master/Images/preference_screen_1.jpg)

## Bước 1: tạo file Setting: res/xml/app_preference.xml

```xml
<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android">
    <!--Nhóm-->
    <PreferenceCategory android:title="Sound">
        <!--Check box-->
        <!--key: key lưu trữ, like Id-->
        <CheckBoxPreference
            android:key="has_sound"
            android:defaultValue="true"
            android:title="Sound"
            android:summary="Enable Sound of Game"
            android:icon="@android:drawable/sym_action_call"/>

        <!--List chọn-->
        <ListPreference
            android:key="sound_index"
            android:title="Select Sound"	
            android:defaultValue="1"
            android:entries="@array/soundArray"
            android:entryValues="@array/soundValues" />
    </PreferenceCategory>
	
    <PreferenceCategory android:title="Player">
        <!--Mở nhập văn bản-->
        <EditTextPreference
            android:key="player_name"
            android:title="Player name"
            android:dialogTitle="Enter your name" />

		<!--Mở Intent-->
		<Preference android:title="Open Google" >
			<intent android:action="android.intent.action.VIEW"
				android:data="http://www.google.com.vn" />
			<!--android:mimeType
			android:targetClass
			android:targetPackage-->
		</Preference>
    </PreferenceCategory>

    <!--Mở màn hình phụ-->
    <PreferenceScreen
        android:key="button_voicemail_category_key"
        android:title="voice mail >"
        android:persistent="false">
        
        <!--Mở chọn Ringtone-->
        <RingtonePreference
            android:key="button_voicemail_ringtone_key"
            android:title="Select Sound"
            android:ringtoneType="notification" />
    </PreferenceScreen>
</PreferenceScreen>
```

**Thuộc tính:**
•	android:key="has_sound": giá trị lưu trong Preference
•	android:title="Sound"
•	android:summary="Enable Sound of Game"
•	android:icon="@android:drawable/sym_action_call"
•	android:defaultValue="true"
•	android:entries="@array/soundArray": Danh sách hiện chọn
•	android:entryValues="@array/soundValues": Danh sách giá trị chọn, lưu giá trị trong list này.
•	android:dialogTitle="Enter your name"

## Bước 2: tạo fragment load file từ Resource: PrefsFragment.java

```java
public class PrefsFragment extends PreferenceFragment {

    @Override
    public void onCreate(Bundle savedInstanceState) {
        // TODO Auto-generated method stub
        super.onCreate(savedInstanceState);

        // Tải preferences từ XML
        addPreferencesFromResource(R.xml.app_preference);
    }
}
```

## Bước 3: Activity Setting load fragment: PreferenceActivity.java

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);

    getFragmentManager().beginTransaction().replace(android.R.id.content,
            new PrefsFragment(), "frag_tag").commit();
}
```

## Bước 4: Tải dữ liệu đã lưu:

```java
/** Lấy Pref */
private void loadPref(){
    SharedPreferences sp = PreferenceManager.getDefaultSharedPreferences(this);

    //get from Check Box
    boolean sound = sp.getBoolean("has_sound", false);
    CheckBox prefCheckBox = (CheckBox)findViewById(R.id.prefCheckBox);
    prefCheckBox.setChecked(sound);
    
    //get from List Array
    String soundName = sp.getString("sound_index", "1");
    TextView vSoundName = (TextView)findViewById(R.id.textview_sound);
    vSoundName.setText(soundName);
    
    //get from Edit text
    String playerName = sp.getString("player_name", "empty");
    TextView vPlayerName = (TextView)findViewById(R.id.textview_player_name);
    vPlayerName.setText(playerName);
}
```


# MENU FRAGMENT – Giao diện menu tùy biến

<!-- TOC -->

- [MENU FRAGMENT – Giao diện menu tùy biến](#menu-fragment--giao-diện-menu-tùy-biến)
    - [Bước 1: tạo fragmentA, hiển thị List](#bước-1-tạo-fragmenta-hiển-thị-list)
        - [File: fragment_a.xml](#file-fragment_axml)
        - [File: FragmentA.java](#file-fragmentajava)
    - [Bước 2: tạo FragmentB, hiển thị Description](#bước-2-tạo-fragmentb-hiển-thị-description)
        - [File: fragment_b.xml](#file-fragment_bxml)
        - [File: FragmentB.java](#file-fragmentbjava)
    - [Bước 3: tạo Main Activity chứa 1 hay cả 2 Fragment](#bước-3-tạo-main-activity-chứa-1-hay-cả-2-fragment)
        - [File: activity_main.xml](#file-activity_mainxml)
        - [File hiển thị khi xoay ngang: layout-land/activity_main.xml](#file-hiển-thị-khi-xoay-ngang-layout-landactivity_mainxml)
        - [File: MainActivity.java](#file-mainactivityjava)
        - [Activity hiện Desciption, màn hình ngang](#activity-hiện-desciption-màn-hình-ngang)
            - [xml: activity_description.xml](#xml-activity_descriptionxml)
            - [DescriptionActivity.java](#descriptionactivityjava)

<!-- /TOC -->

![menu fragment 1](https://github.com/luunghiatran/Android-Quick-docs/blob/master/Images/menu_fragment_1.jpg)

**Giao diện máy dọc: chọn Menu item đến Activity Description.**
![menu fragment 2](https://github.com/luunghiatran/Android-Quick-docs/blob/master/Images/menu_fragment_2.jpg)
![menu fragment 3](https://github.com/luunghiatran/Android-Quick-docs/blob/master/Images/menu_fragment_3.jpg)

**Giao diện máy ngang: hiện cả Menu lẫn Description**
![menu fragment 4](https://github.com/luunghiatran/Android-Quick-docs/blob/master/Images/menu_fragment_4.jpg)

## Bước 1: tạo fragmentA, hiển thị List

### File: fragment_a.xml

```xml
<LinearLayout…>
    <ListView…
        android:id="@+id/listView"/>
</LinearLayout>


```

### File: FragmentA.java

```java
public class FragmentA extends Fragment {

    ICommunicate mCommunicate;
    ListView listView;
    ArrayAdapter mAdapterView;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
    }

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        // khởi tạo giao diện XML
        View view = inflater.inflate(R.layout.fragment_a, container, false);

        //find & init Views
        listView = (ListView) view.findViewById(R.id.listView);
        mAdapterView = ArrayAdapter.createFromResource(getActivity(), R.array.nghia_item, android.R.layout.simple_list_item_1);
        listView.setAdapter(mAdapterView);

        listView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
            @Override
            public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
                listView_onItemClick(parent, view, position, id);
            }
        });

        return view;
    }

    @Override
    public void onActivityCreated(Bundle savedInstanceState) {
        super.onActivityCreated(savedInstanceState);
    }

    @Override
    public void onSaveInstanceState(Bundle outState) {
        super.onSaveInstanceState(outState);
    }

    @Override
    public void onDetach() {
        super.onDetach();
    }

    private void listView_onItemClick(AdapterView<?> parent, View view, int position, long id) {
        this.mCommunicate.fragmentRespone(position);  // gởi postion qua MainActivity
    }

    /**
     * khởi tạo Communicate cầu nối, liên hệ FRAGMENT_B
     * @param communicate
     */
    public void setCommunicate(ICommunicate communicate) {
        this.mCommunicate = communicate;
    }

    /**
     * giao tiếp chuyển đổi dữ liệu: FRAGMENT_A to MainActivity to FRAGMENT_B
     */
    public interface ICommunicate{
        /**
         * phải hồi lại khi FRAGMENT_A GỞI DỮ LIỆU
         * @param position vị trí trong list
         */
        public void fragmentRespone(int position);
    }
}

```

## Bước 2: tạo FragmentB, hiển thị Description

### File: fragment_b.xml

```xml
<LinearLayout…>
    <TextView… android:id="@+id/textView"/>
</LinearLayout>
```

### File: FragmentB.java

```java
public class FragmentB extends Fragment {

    TextView textView;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
    }

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {

        // khởi tạo giao diện XML
        View view = inflater.inflate(R.layout.fragment_b, container, false);

        // find & init views
        textView = (TextView) view.findViewById(R.id.textView);

        return view;
    }

    @Override
    public void onActivityCreated(Bundle savedInstanceState) {
        super.onActivityCreated(savedInstanceState);
    }

    @Override
    public void onSaveInstanceState(Bundle outState) {
        super.onSaveInstanceState(outState);
    }

    @Override
    public void onDetach() {
        super.onDetach();
    }

    public void changeData(int position){
        // gởi position trong array qua để hiển thị lên TEXT VIEW
        String[] arrDescription = getResources().getStringArray(R.array.nghia_item_description);
        textView.setText(arrDescription[position]);
    }
}
```

## Bước 3: tạo Main Activity chứa 1 hay cả 2 Fragment

### File: activity_main.xml

```xml
<RelativeLayout... tools:context=".MainActivity">
    <fragment…
        android:id="@+id/fragA_List"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:name="com.imark.nghia.nghiafragment_modelui_design.FragmentA"
        tools:layout="@layout/fragment_a"/>
</RelativeLayout>
```

### File hiển thị khi xoay ngang: layout-land/activity_main.xml

```xml
<LinearLayout... android:orientation="horizontal">
    <fragment
        android:id="@+id/fragA_List"
        android:layout_width="0dp"
        android:layout_height="match_parent"
        android:layout_weight="0.3"
        android:name="com.imark.nghia.nghiafragment_modelui_design.FragmentA"
        tools:layout="@layout/fragment_a"/>
    <fragment
        android:id="@+id/fragB_Description"
        android:layout_width="0dp"
        android:layout_height="match_parent"
        android:layout_weight="0.7"
        android:name="com.imark.nghia.nghiafragment_modelui_design.FragmentB"
        tools:layout="@layout/fragment_b"/>
</LinearLayout>
```

### File: MainActivity.java

```java
public class MainActivity extends Activity implements FragmentA.ICommunicate {

    public static final String EXTRA_KEY_POSITION = "KEY POSITION";

    FragmentA fragA_List;
    FragmentB fragB_Description;
    FragmentManager fragManager;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        ...
        // find & init views
        fragManager = getFragmentManager();
        fragA_List = (FragmentA) fragManager.findFragmentById(R.id.fragA_List);

        fragA_List.setCommunicate(this);
    }

    @Override
    public void fragmentRespone(int position) {
        // FRAGMENT_A gọi hàm này, hàm này chuyển qua FRAGMENT_B
        fragB_Description = (FragmentB) fragManager.findFragmentById(R.id.fragB_Description);

        if (fragB_Description != null && fragB_Description.isVisible()) {
            // nếu fragment_b tồn tại => gởi dữ liệu đến
            fragB_Description.changeData(position);
        } else {
            // fragment_b không tồn tại => chuyển đến activity khác
            Intent intent = new Intent(this, DescriptionActivity.class);
            intent.putExtra(EXTRA_KEY_POSITION, position);
            startActivity(intent);
        }
    }
}
```

### Activity hiện Desciption, màn hình ngang

#### xml: activity_description.xml

```xml
<RelativeLayout… tools:context="com.imark.nghia.nghiafragment_modelui_design.DescriptionActivity">
    <fragment...
        android:id="@+id/fragB_Description"
        android:name="com.imark.nghia.nghiafragment_modelui_design.FragmentB"
        tools:layout="@layout/fragment_b"/>
</RelativeLayout>
```

#### DescriptionActivity.java

```java
public class DescriptionActivity extends Activity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        …
        // find & init views
        int postion = getIntent().getIntExtra(MainActivity.EXTRA_KEY_POSITION, 0);

        FragmentManager fragmentManager = getFragmentManager();
        FragmentB fragmentB = (FragmentB) fragmentManager.findFragmentById(R.id.fragB_Description);
        fragmentB.changeData(postion);  // đổi dữ liệu FRAGMENT_B
    }
}
```

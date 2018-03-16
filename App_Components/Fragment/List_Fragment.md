# List Fragment

![list fragment](/Images/list_fragment.jpg)

## File: fragment_list_a.xml

```xml
<LinearLayout…>
    <ListView… android:id="@android:id/list"/>
</LinearLayout>

```

## File: FragmentListA.java

```java
public class FragmentListA extends ListFragment{
    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        return inflater.inflate(R.layout.fragment_list_a, container, false);
    }

    @Override
    public void onActivityCreated(Bundle savedInstanceState) {
        super.onActivityCreated(savedInstanceState);

        // init views & event
        ArrayAdapter adapter = ArrayAdapter.createFromResource(getActivity(),
                R.array.array_nghia_random,
                android.R.layout.simple_list_item_1);
        setListAdapter(adapter);

        // init event
        getListView().setOnItemClickListener(new AdapterView.OnItemClickListener() {
            @Override
            public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
                onItemClickListener(parent, view, position, id);
            }
        });
    }

    private void onItemClickListener(AdapterView<?> parent, View view, int position, long id) {
        String value = getResources().getStringArray(R.array.array_nghia_random)[position];
        String message = String.format("Chọn vị trí: %s \n Giá trị: %s", position, value);
        Toast.makeText(getActivity(), message, Toast.LENGTH_LONG).show();
    }
}
```

## Sử dụng trong: activity_main.xml

```xml
<RelativeLayout… tools:context=".MainActivity">
    <fragment…
        android:name="com.imark.nghia.nghiafragment_6_listfragment.FragmentListA"
        android:id="@+id/fragment"
        tools:layout="@layout/fragment_list_a"/>
</RelativeLayout>
```
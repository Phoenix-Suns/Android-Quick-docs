# RECYCLE VIEW

Hiển thị danh sách lên giao diện như ListView.
Hiển thị đa dạng hơn.

## Simple Example

![example 1]("recyclerview_basic_1.jpg")

### Bước 1: Thêm RecycleView trong: build.gradle

build.gradle

```js
dependencies {
    // RecyclerView
    implementation 'com.android.support:recyclerview-v7:26.1.0'
}
```

### Bước 2: Tạo trên giao diện activity layout

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context="com.imark.nghia.nghiacontainer_recyclerview_likelistview.MainActivity">

//Thêm mới
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
		android:background="#000000">
        <EditText
            android:id="@+id/main_edit_text_code"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:hint="Name"/>

        <EditText
            android:id="@+id/main_edit_text_name"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:hint="Code"/>
        <Button
            android:id="@+id/main_button_add"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Add"/>
    </LinearLayout>

//Hiển thị danh sách
    <android.support.v7.widget.RecyclerView
        android:id="@+id/main_recycler_view"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:scrollbars="vertical"/>
</LinearLayout>
```

#### Tạo giao diện mỗi dòng: layout/item_recyclerview.xml

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:gravity="center_vertical"
    android:padding="10dp">

    <ImageButton
        android:id="@+id/button_delete"
        android:layout_width="wrap_content"
        android:layout_height="match_parent"
        android:src="@android:drawable/ic_delete"
        android:layout_alignParentRight="true"
        android:layout_alignParentEnd="true"
        android:layout_centerInParent="true"/>

        <TextView
            android:id="@+id/text_view_name"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:textAppearance="?android:textAppearanceLarge"
            android:layout_toLeftOf="@+id/button_delete"
            android:layout_toStartOf="@+id/button_delete"/>

        <TextView
            android:id="@+id/text_view_code"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_below="@+id/text_view_name"
            android:layout_alignParentLeft="true"
            android:layout_alignParentStart="true"
            android:layout_toLeftOf="@+id/button_delete"
            android:layout_toStartOf="@+id/button_delete"/>
</RelativeLayout>
```

### Bước 3: Tạo ItemModel để lưu trữ Item: ItemModel.java

```java
public class ItemModel {
    public String Code;
    public String Name;

    public ItemModel(String code, String name) {
        this.Code = code;
        this.Name = name;
    }
}
```

#### Khởi tạo Adapter kết nối Item: CustomRecyclerAdapter.java

```java
public class CustomRecyclerAdapter extends RecyclerView.Adapter<CustomRecyclerAdapter.RecyclerViewHolder> {

    private List<ItemModel> mListItem;
	
	public CustomRecyclerAdapter(List<ItemModel> listItem) {
        this.mListItem = listItem;
    }

    @Override
    public RecyclerViewHolder onCreateViewHolder(ViewGroup viewGroup, int position) {
        // Khởi tạo giao diện
        LayoutInflater inflater = LayoutInflater.from(viewGroup.getContext());
        View itemView = inflater.inflate(R.layout.item_recyclerview, viewGroup, false);
        return new RecyclerViewHolder(itemView);
    }

    @Override
    public void onBindViewHolder(RecyclerViewHolder recyclerViewHolder, int position) {
        // Hiển thị giá trị lên giai diện
        ItemModel itemModel = mListItem.get(position);
        recyclerViewHolder.mTextView_Name.setText(itemModel.Name);
        recyclerViewHolder.mTextView_Code.setText(itemModel.Code);
    }

    @Override
    public int getItemCount() {
        return mListItem.size();
    }

    /**ViewHolder for item view of list*/
    public class RecyclerViewHolder extends RecyclerView.ViewHolder {
        TextView mTextView_Code, mTextView_Name;
        ImageButton mButton_Delete;

        public RecyclerViewHolder(View itemView){
            super(itemView);

            // ----- Find View ----
            mTextView_Code = (TextView) itemView.findViewById(R.id.text_view_code);
            mTextView_Name = (TextView) itemView.findViewById(R.id.text_view_name);
            mButton_Delete = (ImageButton) itemView.findViewById(R.id.button_delete);
            // ----- Init ----- Nút xoá Item
            mButton_Delete.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    mListItem.remove(getAdapterPosition());
					notifyItemRemoved(getAdapterPosition());
                }
            });
        }
    }
}
```

### Bước 4: Sử dụng trong Activity

```java
EditText mEditText_Name, mEditText_Code;
Button mButton_Add;

private RecyclerView mRecyclerView;
List<ItemModel> mListItem;
CustomRecyclerAdapter mAdapter;

@Override
protected void onCreate(Bundle savedInstanceState) {
    //...
    findViews();
    initVars();
}

private void findViews() {
    mEditText_Code = (EditText) findViewById(R.id.main_edit_text_code);
    mEditText_Name = (EditText) findViewById(R.id.main_edit_text_name);
    mButton_Add = (Button) findViewById(R.id.main_button_add);
    mRecyclerView = (RecyclerView) findViewById(R.id.main_recycler_view);
}

private void initVars() {
	//Tao Danh Sach
    mListItem = new ArrayList<ItemModel>(){{
			for(int i=0; i<100; i++){
				add(new ItemModel(""+i, "name"+i));
			}
    }};
		
    // If the size of views will not change as the data changes.
    mRecyclerView.setHasFixedSize(true);

    // Setting the LayoutManager.
    LinearLayoutManager layoutManager = new LinearLayoutManager(this);
    mRecyclerView.setLayoutManager(layoutManager);

    mAdapter = new CustomRecyclerAdapter(mListItem);
    mRecyclerView.setAdapter(mAdapter);

	//Thêm Item tại Vị trí đầu tiên
    mButton_Add.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            mListItem.add(0, new ItemModel(
					mEditText_Code.getText()+"",
					mEditText_Name.getText()+""));
				mAdapter.notifyDataSetChanged();
        }
    });
}
```

---

## Reference

# Select group View

## Layout

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <!--Boy-->
        <LinearLayout
            android:id="@+id/view_boy"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:background="@drawable/selector_button"
            android:tag="Boy">
            <ImageView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:src="@drawable/ic_boy"/>
            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Boy"/>
        </LinearLayout>


        <!--Girl-->
        <LinearLayout
            android:id="@+id/view_girl"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:background="@drawable/selector_button"
            android:tag="girl">
            <ImageView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:src="@drawable/ic_girl"/>
            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Girl"/>
        </LinearLayout>


        <!--Others-->
        <LinearLayout
            android:id="@+id/view_others"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:background="@drawable/selector_button"
            android:tag="Other">
            <ImageView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:src="@drawable/ic_boy"/>
            <ImageView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:src="@drawable/ic_girl"/>
            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Others"/>
        </LinearLayout>

    </LinearLayout>

    <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Get Gender"
        android:onClick="getGender"/>

</LinearLayout>
```

## JAVA
```java
int[] listGenderId = new int[]{ R.id.view_boy, R.id.view_girl, R.id.view_others };
    ArrayList<View> listGenderView = new ArrayList<>();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        ///...
        initViewGenders();
    }

    private void initViewGenders() {
        for (int id : listGenderId) {
            View view = findViewById(id);
            view.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View selectedView) {

                    // ========= Set Selected =============
                    for (View view : listGenderView) {
                        if (selectedView == view) {
                            view.setSelected(true);     // Must Set Selector background
                        } else {
                            view.setSelected(false);
                        }
                    }
                }
            });

            listGenderView.add(view);
        }
    }

    public void getGender(View v) {
        // ArrayList<View> listGenderSelected = new ArrayList<>();
        StringBuilder selected = new StringBuilder();
        for (View view : listGenderView) {
            if (view.isSelected()) {
                // listGenderSelected.add(view);
                selected.append(" ").append(view.getTag());     // ======== Select Text by View TAG =======
            }
        }

        Toast.makeText(this, selected.toString(), Toast.LENGTH_SHORT).show();
    }
```

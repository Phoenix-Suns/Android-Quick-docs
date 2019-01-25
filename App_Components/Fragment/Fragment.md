# Fragment - Mảnh, phần giao diện - Phân đoạn

<!-- TOC -->autoauto- [Fragment - Mảnh, phần giao diện - Phân đoạn](#fragment---mảnh-phần-giao-diện---phân-đoạn)auto    - [Fragment là gì](#fragment-là-gì)auto        - [Vòng đời, quy trình tồn tại:](#vòng-đời-quy-trình-tồn-tại)auto        - [Template - Code mẫu](#template---code-mẫu)auto        - [Xử lý Fragment trong Activity](#xử-lý-fragment-trong-activity)autoauto<!-- /TOC -->

## Fragment là gì

Mảnh nhỏ trong giao diện - Giống UserControl
View với sử lý riêng

### Vòng đời, quy trình tồn tại:

![fragment_lifecycle.png](/Images/fragment_lifecycle.png)

### Template - Code mẫu

```java
public class MyFragment extends Fragment {

	// tham số truyền vào Fragment
	private static final String ARG_PARAM1 = "param1";
	private static final String ARG_PARAM2 = "param2";

	// Giá trị tham số Fragment
	private String mParam1;
	private String mParam2;

	// Listener lắng nghe sự kiện
	private OnFragmentInteractionListener mListener;

	/**
	 * Khởi tạo Fragment với tham số truyền vào
	 */
	public static MyFragment newInstance(String param1, String param2) {
		MyFragment fragment = new MyFragment();
		Bundle args = new Bundle();
		args.putString(ARG_PARAM1, param1);
		args.putString(ARG_PARAM2, param2);
		fragment.setArguments(args);
		return fragment;
	}

	public MyFragment() {
		// Required empty public constructor
	}

	/**
	 * Set Lắng nghe sự kiện trả về
	 */
	public void setListener(OnFragmentInteractionListener listener) {
		mListener = listener;
	}

	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);

		setHasOptionsMenu(true); // Khai báo menu

		// Lấy tham số truyền vào Bundle trong newInstance, truyền vào biến
		if (getArguments() != null) {
			mParam1 = getArguments().getString(ARG_PARAM1);
			mParam2 = getArguments().getString(ARG_PARAM2);
		}
	}

	@Override
	public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
		// khởi tạo layout
		return inflater.inflate(R.layout.fragment_blank, container, false);
	}

	@Override
	public void onViewCreated(View view, Bundle savedInstanceState) {
		super.onViewCreated(view, savedInstanceState);

		/***************************************
                TODO: BẮT ĐẦU KHỞI TẠO, MUỐN LÀM GÌ LÀM
                *****************************************/
	}

	@Override
	public void onCreateOptionsMenu(Menu menu, MenuInflater inflater) {
		inflater.inflate(R.menu.menu_layout, menu);
		super.onCreateOptionsMenu(menu, inflater);
	}

	@Override
	public boolean onOptionsItemSelected(MenuItem item) {
		switch (item.getItemId()) {
			default: return super.onOptionsItemSelected(item);
		}
	}

	/**
	 * Hàm gọi sự kiện
	 */
	public void onButtonPressed(Uri uri) {
		if (mListener != null) {
			mListener.onFragmentInteraction(uri);
		}
	}

	@Override
	public void onDetach() {
		super.onDetach();
		mListener = null;
	}

	/**
	 * Interface tương tác với Fragment
	 */
	public interface OnFragmentInteractionListener {
		// TODO: Update argument type and name
		public void onFragmentInteraction(Uri uri);
	}
}
```

### Xử lý Fragment trong Activity

**Bắt đầu xử lý:**

```java
// Gọi Quản lý Fragment
final FragmentManager fragManager = getFragmentManager();
FragmentTransaction transaction = fragManager.beginTransaction();
/**
 * TODO Xử lý Fragment Transaction
 */
transaction.commit();
```

**Xử lý Transaction:**

```java
// Thêm Fragment vào View(fragment_container)
// Nếu thêm nữa thì sẽ hiện cả 2, không Pause Fragment cũ
MyFragment fragment = new MyFragment();
transaction.add(R.id.fragment_container, fragment, "fragment_tag");

// Xóa Fragment
transaction.remove(fragment);
// Thay thế Fagment hiện tại = fragment = remove + add (Pause fragment cũ)
transaction.replace(R.id.fragment_container, fragment, "fragment_tag");

// giống Remove, không đính kèm, còn lưu trạng thái (TAG)
transaction.detach(fragment);
// đính kèm Fragment bị Detach trở lại, (findFragmentByTag() != null)
transaction.attach(fragment);
```

**Tìm Fragment đã tồn tại:**

```java
// tìm Fragment trên UI
FragmentB fragB = (FragmentB) fragManager.findFragmentById(R.id.fragment_b);
// tìm Fragment bằng Tag
FragmentA fragA = (FragmentA) fragManager.findFragmentByTag("fragment_tag");
```

**Xử lý BackStack (hàng đợi trở về):**

```java
// Thêm Entry vào BackStack (hàng đợi) (khi nhấn back => trở lại Fragment cũ)
transaction.addToBackStack("entry_name");
// xóa hết entry cuối cùng trong backstack
fragManager.popBackStack();
// xóa hết entry "entry_name" trong backStack
fragManager.popBackStack("entry_name", FragmentManager.POP_BACK_STACK_INCLUSIVE);
```

**Sự kiện:**

```java
// nghe sự kiện BackStack thay đổi
fragManager.addOnBackStackChangedListener(new FragmentManager.OnBackStackChangedListener() {
    @Override
    public void onBackStackChanged() {

        // hiện tên entry(mục) trong backstack(ngăn xếp)
        int count = fragManager.getBackStackEntryCount();
        for (int i = count - 1; i >= 0; i--) {
            FragmentManager.BackStackEntry entry = fragManager.getBackStackEntryAt(i);
            String tagName = entry.getName(); // lấy TagName
        }
    }
});
```

---

**Trích:**
*   [http://developer.android.com/intl/vi/guide/components/fragments.html](http://developer.android.com/intl/vi/guide/components/fragments.html)


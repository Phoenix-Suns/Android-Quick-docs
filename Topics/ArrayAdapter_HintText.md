# Hint Text: Dòng Hướng dẫn không chọn được

```java
public class KYCCountryAdapter extends ArrayAdapter<KYCCountry> {
    private final ArrayList<KYCCountry> mList;
    private String mHintText;

    ...

    // region make hint
    public void setHintText(String hintText) {
        this.mHintText = hintText;

        KYCCountry item = new KYCCountry(mHintText, mHintText);
        if (mHintText != null && !mHintText.isEmpty()) {

            mList.add(0, item);
        } else {
            mList.remove(item);
        }
        notifyDataSetChanged();
    }

    @Override
    public boolean isEnabled(int position) {
        if (mHintText != null && !mHintText.isEmpty()) {
            if (position == 0) {
                // Disable the first item from Spinner
                // First item will be use for hint
                return false;
            } else {
                return true;
            }
        }
        return true;
    }

    @Override
    public View getDropDownView(int position, View convertView, ViewGroup parent) {
        //Hien thi DropDownList, spinner
        parent.setPadding(0,0,0,0); //fix padding top/bottom on dropdown

        ...

        // ---- Disable Hint Row ----
        if (mHintText != null && !mHintText.isEmpty()) {
            if (position == 0) {
                viewHolder.textView_Country.setLayoutParams(new LinearLayout.LayoutParams(0, 0));
            } else {
                viewHolder.textView_Country.setLayoutParams(new LinearLayout.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT, ViewGroup.LayoutParams.WRAP_CONTENT));
            }
        }

        return convertView;
    }

    // endregion
}

```

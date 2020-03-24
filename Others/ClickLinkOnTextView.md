# Click link on TextView

```java
public class AppContentFormatter {

    // Pattern for recognizing a URL, based off RFC 3986
    private static final Pattern urlPattern = Pattern.compile(
            "(?:^|[\\W])((ht|f)tp(s?):\\/\\/|www\\.)"
                    + "(([\\w\\-]+\\.){1,}?([\\w\\-.~]+\\/?)*"
                    + "[\\p{Alnum}.,%_=?&#\\-+()\\[\\]\\*$~@!:/{};']*)",
            Pattern.CASE_INSENSITIVE | Pattern.MULTILINE | Pattern.DOTALL);
    private ClickUrlListener clickUrlListener;
    private ExistUrlListener existUrlListener;


    public void setClickUrlListener(ClickUrlListener listener) {
        this.clickUrlListener = listener;
    }

    public void setUrlExistListener(ExistUrlListener listener) {
        this.existUrlListener = listener;
    }

    public Spannable format(String content) {
        Spannable spannable = new SpannableString(content);

        // Highlight link click
        Matcher matcher = urlPattern.matcher(spannable);
        while (matcher.find()) {
            int matchStart = matcher.start(1);
            int matchEnd = matcher.end();

            final CharSequence urlStr = spannable.subSequence(matchStart, matchEnd);
            if (existUrlListener != null)
                existUrlListener.onExistUrl(urlStr.toString());
            spannable.setSpan(new ClickableSpan() {
                @Override
                public void onClick(@NonNull View widget) {
                    if (clickUrlListener != null)
                        clickUrlListener.onClickUrl(urlStr.toString());
                }
            }, matchStart, matchEnd, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
        }

        return spannable;
    }

    public interface ClickUrlListener {
        void onClickUrl(String url);
    }

    public interface ExistUrlListener {
        void onExistUrl(String url);
    }
}
```

```java
// USING
AppContentFormatter formatter = new AppContentFormatter();
formatter.setClickUrlListener(new AppContentFormatter.ClickUrlListener() {
    @Override
    public void onClickUrl(String url) {
        Toast.makeText(MainActivity.this, url, Toast.LENGTH_SHORT).show();
        Log.e("nghia click", url);
    }
});
Spannable newContent = formatter.format(textView.getText().toString());


textView.setText(newContent);
textView.setMovementMethod(LinkMovementMethod.getInstance());
```
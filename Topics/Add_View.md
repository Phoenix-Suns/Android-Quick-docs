# Add List View bằng code

```java

val viewReplay = holder.getNullView<LinearLayout>(R.id.linearLayout_replay)

//#region Sublist Comment
val listComment = java.util.ArrayList(FakeDAO.getComment(context).subList(0, 3)) //get 3 items

val inflater = context.getSystemService(Context.LAYOUT_INFLATER_SERVICE) as LayoutInflater

viewReplay?.removeAllViews()
for (comment: Comment in listComment) {
    val rootView = inflater.inflate(R.layout.item_comment_child, null)  // GET VIEW

    //BIND DATA
    rootView.findViewById<TextView>(R.id.textView_userName).text = comment.userName
    rootView.findViewById<TextView>(R.id.textView_commentText).text = comment.commentText
    rootView.findViewById<TextView>(R.id.textView_like).text = comment.totalLike.toString()

    viewReplay?.addView(rootView)
}

//#endregion
```
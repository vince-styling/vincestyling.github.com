title: How to make the ListView's selector influence multiple rows in Android
decorator: post
identifier: 41a3d0cfa28a2b13679b
description: ListView's Selector only enabled for an individual row, but sometime we wish the rows that near by the finger hovering row can do some cooperate work to achieve an fascinating effect.
‡‡‡‡‡‡‡‡‡‡‡‡‡‡
![](/images/listview_selector_before.png)

Above image is a screenshot which display the ListView's row changed since it has been pressed. As you saw the ListView's selector made the row's background color change when we putting down finger on. It looks the highlight color infiltrating into the bottom curved divider. But once you learn deeper about the ListView's mechanism, you'll understand that we can hardly to manipulate the ListView's dividers drawing with current resources in our hands. It is able to work because I have every row to carrying his divider rather than let ListView to draw it. Just as following structure.

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <TextView
            android:id="@+id/txvBookName"/>

        <TextView
            style="@style/finder_book_list_item_capacity"/>

        <TextView
            style="@style/finder_book_list_item_tips"/>

    </LinearLayout>

    <TextView
        style="@style/finder_book_list_item_summary"/>

    <FrameLayout
        android:layout_marginTop="10dp"
        style="@style/finder_book_list_item_divider"/>

</LinearLayout>
```

Right now, to making that effect perfectly, we must concurrently to infiltrating the gaps which you can see at the beginning's screenshot while we change the row's background color. That sounds tempting if we can accomplish this.

But those gaps was belong and maintained by the row which at motion hovering row's above. The ListView's `Selector` only a Drawable therefore it doesn't have power to modify any views. So two questions await to be resolved.

- `First` : We must figure out where the Selector to perform changing the row's background when user pressing it, then attempt to intercept that behavior to do something else.
- `Second` : We must asking ListView to tell us what position is the finger laying on, then taking the particular child view from that ListView to change its divider.

For the first question, I found Selector actually a `StateListDrawable`, and I'm lucky to be aware of that we have a method named `onStateChange()` be able to intercepting. Which make us always keep informed about the state change.

```java
StateListDrawable selector = new StateListDrawable() {
    @Override
    protected boolean onStateChange(int[] stateSet) {
        boolean result = super.onStateChange(stateSet);

        // do the second job here about change the above item.

        return result;
    }
};
selector.addState(new int[]{android.R.attr.state_pressed},
    getResources().getDrawable(R.color.finder_booklist_bg_pressed));
mListView.setSelector(selector);
```

About the second's, I tracing into the ListView's source code. Finally discovered a method named [findMotionRow](http://grepcode.com/file/repository.grepcode.com/java/ext/com.google.android/android/4.0.3_r1/android/widget/ListView.java#ListView.findMotionRow%28int%29) which can calculate the hovering row index. But it's buried deep inside the ListView, we can't invoke that obviously just like the rest of visible methods does. Thus I fetched its source code to putting my customize ListView which extended from original ListView to recover that method's benefit.

```java
public class MyListView extends ListView {
    private int mMotionPosition;

    public int getMotionPosition() {
        return mMotionPosition;
    }

    @Override
    public boolean onInterceptTouchEvent(MotionEvent ev) {
        if (ev.getAction() == MotionEvent.ACTION_DOWN) {
            mMotionPosition = findMotionRow(ev.getY());
        }
        return super.onInterceptTouchEvent(ev);
    }

    /**
     * Find which position is motion on.
     * Note : this method copy into public from 4.0 source code.
     * @param y Y coordinate of the motion event.
     * @return Selected index (starting at 0) of the data item.
     */
    private int findMotionRow(float y) {
        int childCount = getChildCount();
        if (childCount > 0) {
            if (!isStackFromBottom()) {
                for (int i = 0; i < childCount; i++) {
                    View v = getChildAt(i);
                    if (y <= v.getBottom()) {
                        return getFirstVisiblePosition() + i;
                    }
                }
            } else {
                for (int i = childCount - 1; i >= 0; i--) {
                    View v = getChildAt(i);
                    if (y >= v.getTop()) {
                        return getFirstVisiblePosition() + i;
                    }
                }
            }
        }
        return INVALID_POSITION;
    }
}
```

Once we knew the position which finger hovering, we'll be capable of taking the above row then change its divider concurrently.

```java
protected boolean onStateChange(int[] stateSet) {
    boolean result = super.onStateChange(stateSet);

    int aboveChildIndex = mMotionPosition - getFirstVisiblePosition();
    if (--aboveChildIndex < 0) return result;

    if (Arrays.binarySearch(stateSet, android.R.attr.state_pressed) > -1) {
        if (mIsPressed) return result;

        View child = getChildAt(aboveChildIndex);
        child.findViewById(R.id.lotDivider).setBackgroundResource(
            R.drawable.finder_booklist_divider_pressed_layer);
        mIsPressed = true;
    } else if (mIsPressed) {
        View child = getChildAt(aboveChildIndex);
        child.findViewById(R.id.lotDivider).setBackgroundResource(
            R.drawable.finder_booklist_divider_layer);
        mIsPressed = false;
    }

    return result;
}
```

Eventually, with these advantages, we can accomplish our tempting purpose about making the selector influence multiple rows.

![](/images/listview_selector_after.png)

I built a project for this post to help understanding, also play my effort a greater value. The core logical code was concentrated at this [class](https://github.com/vince-styling/multiple-rows-selector-android/blob/master/src/com/vincestyling/multiple_rows_selector/FinderListView.java).

And [here](http://stackoverflow.com/questions/25217975/issue-in-getting-child-row-of-the-listview-on-swipe/25227217#25227217) is my answer for a Stackoverflow question which share the same desired with me.






































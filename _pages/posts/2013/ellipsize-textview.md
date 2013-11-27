---
title: easily to know ellipsize mode of textview in Android
layout: post
format: markdown
type: post
tags: android, canvas, view
timestamp: 1385469587
slug: easily-to-know-ellipsize-mode-of-textview-in-Android.html
---

As you know, the Android UI TextView also allow we to set MaxLines and EllipsizeMode,
but we can't find out how to know if TextView ellipsized, we want to know it because
we can do something for it such as show an expand/collapse Button by the ellipsize
state, that was why we wrote this widget.

this widget looks like the TextView, offer LineSpacing, TextSize, TextColor to let you
customize like you do with TextView via StyledAttributes, of course you can declare more
attributes as you wanted.

## How does it do?

It all start by the onMeasure method, when onMeasure calling, we use Paint.breakText() to
calculate the input string then store every line's start&end index.

    int index = 0;
    int newlineIndex = 0;
    int endCharIndex = 0;
    mLines = new ArrayList<int[]>(mMaxLineCount * 2);

    // breakText line by line and store line's indices
    while (index < mText.length()) {
        if (index == newlineIndex) {
            newlineIndex = mText.indexOf(NEW_LINE_STR, newlineIndex);
            endCharIndex = (newlineIndex != -1) ? newlineIndex : mText.length();
        }

        int charCount = mPaint.breakText(mText, index, endCharIndex, true, maxWidth, null);
        if (charCount > 0) {
            mLines.add(new int[]{index, index + charCount});
            index += charCount;
        }

        if (index == newlineIndex) {
            newlineIndex++;
            index++;
        }
    }

after that, we got the actually line count(`EntireLineCount`) by the entire input string.
according to that we can measure width simply done by Paint.measureText() method. Use
`EntireLineCount` we can decide should expand or not and measure height. when measure
is **done**, we will inform the ellipsize mode via OnMeasureDoneListener who want to know
it was changed. you can toggle the mode to expand/collapse via click-handler if there's
not enough space for the input string(when EntireLineCount > MaxLineCount).

In **onDraw()** method, we use `mDrawLineCount` to draw few lines that calculate by the
**measureHeight()** method, if ellipsize needs, we will loop delete last line one char
of the end then measure until enough to draw ellipsize text.

    StringBuilder line = ...;
    float lineDrawWidth = mPaint.measureText(line, 0, line.length());
    float ellipsisWidth = mPaint.measureText(mStrEllipsis);

    // delete one char then measure until enough to draw ellipsize text
    while (lineDrawWidth + ellipsisWidth > renderWidth) {
        line.deleteCharAt(line.length() - 1);
        lineDrawWidth = mPaint.measureText(line, 0, line.length());
    }
    line.append(mStrEllipsis);

## A problem happen during onDraw() method.

Suppose you have a large string to display, the measure height is greater than screen
height if you want to display entire string, but Android just offer the **Canvas** match
the screen dimension in the **onDraw()** method, that means the Canvas dimension not rely your
measure result. for example, you measure dimension is 720x1400 but Canvas dimension
just 720x1280, therefore the expand mode doesn't show all lines. but the dimension of View
is rely the measure result, it will be displayed as 720x1240 dimension. why this situation
occur? because we calculate the used height after draw per line, if Canvas not enough space
to draw next line, we will stop them. when disable that determine, the problem solved. I didn't
figure out this problem, if you know how to do, please share to me.

    while (some condition) {
        // draw the current line.
        canvas.drawText(line, 0, line.length(), canvasX, canvasY, mPaint);

        canvasY += (-mPaint.ascent() + mPaint.descent()) + mLineSpacing;

        // stop if canvas not enough space to draw next line
        if (canvasY > canvas.getHeight()) break;
    }

To be straightforward, a runtime screenshot attached.

![EllipsizeTextView runtime screenshot](/images/screenshot_of_ellipsize_textview.png "EllipsizeTextView runtime screenshot")

Notes : This widget is pretty basic, it just support left-to-right text and
ellipsize text end. it handled Chinese or some languages like Chinese,
you can modify the **breakText** logic code to implement yourself.

The full source code for EllipsizeTextView and the sample project on [GitHub](https://github.com/vince-styling/ellipsize-textview-android).

### Helpful Posts :

 - [android ellipsize multiline textview](http://stackoverflow.com/questions/2160619/android-ellipsize-multiline-textview/6763689)
 - [android-textview-multiline-ellipse](https://code.google.com/p/android-textview-multiline-ellipse/)
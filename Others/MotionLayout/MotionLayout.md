# Motion Layout - Layout Chuyển động

- [Motion Layout - Layout Chuyển động](#motion-layout---layout-chuyển-động)
  - [Example: kéo ô vuông](#example-kéo-ô-vuông)
    - [Chỉnh Frame](#chỉnh-frame)
  - [Motion Layout với AppBar Layout](#motion-layout-với-appbar-layout)
  - [Reference](#reference)

## Example: kéo ô vuông

- build.gradle

```js
dependencies {
    implementation 'androidx.constraintlayout:constraintlayout:2.0.4'
}
```

- activity_main.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.motion.widget.MotionLayout
    android:id="@+id/motionLayout"
    app:layoutDescription="@xml/activity_main_scene"
    tools:showPaths="true">

    <!-- MotionLayout Attributes:
        app:applyMotionScene="boolean"
        app:showPaths="boolean"
        app:progress="float" (0-1)
        app:currentState="reference"
        app:motionDebug="SHOW_PROGRESS|SHOW_PATH|SHOW_ALL" -->

    <View
        android:id="@+id/button"
        android:layout_width="64dp"
        android:layout_height="64dp"
        android:background="#673AB7"
        android:text="Button" />

</androidx.constraintlayout.motion.widget.MotionLayout>
```

- xml/activity_main_scene.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<MotionScene xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:motion="http://schemas.android.com/apk/res-auto">

    <!-- Config Motion -->
    <Transition
        motion:constraintSetStart="@+id/start"
        motion:constraintSetEnd="@+id/end"
        motion:duration="1000">
    
        <OnSwipe
            motion:touchAnchorId="@+id/button"
            motion:touchAnchorSide="right"
            motion:dragDirection="dragRight" />

        <!-- <OnClick motion:targetId="@+id/button" /> -->

        <!-- Chỉnh đường đi (Frame)
        <KeyFrameSet >
            ...
        </KeyFrameSet> -->

    </Transition>

    <!-- View: Start Motion Constaint (layout) -->
    <ConstraintSet android:id="@+id/start">
        <Constraint
            android:id="@+id/button"
            android:layout_width="64dp"
            android:layout_height="64dp"
            android:layout_marginStart="8dp"
            motion:layout_constraintBottom_toBottomOf="parent"
            motion:layout_constraintStart_toStartOf="parent"
            motion:layout_constraintTop_toTopOf="parent">

            <!-- add more change properties -->
            <CustomAttribute
                motion:attributeName="backgroundColor"
                motion:customColorValue="#D81B60" />

        </Constraint>
    </ConstraintSet>

    <!-- View: End Motion Constaint (layout)-->
    <ConstraintSet android:id="@+id/end">
        <Constraint
            android:id="@+id/button"
            android:layout_width="64dp"
            android:layout_height="64dp"
            android:layout_marginEnd="8dp"
            motion:layout_constraintBottom_toBottomOf="parent"
            motion:layout_constraintEnd_toEndOf="parent"
            motion:layout_constraintTop_toTopOf="parent" >
            <CustomAttribute
                motion:attributeName="backgroundColor"
                motion:customColorValue="#9999FF" />
        </Constraint>
    </ConstraintSet>

</MotionScene>
```

- Properties:
    - motion:touchAnchorId: Id user tương tác
    - motion:constraintSetStart: constraintSet id
    - <Constraint android:id.../>: id chuyển động theo tương tác

### Chỉnh Frame

```xml
<MotionScene>
    <!-- Config Motion -->
    <Transition>
        <!-- Chỉnh đường đi -->
        <KeyFrameSet >
            <!-- Đổi vị trí (Frame ở 50% chuyển động) -->
            <KeyPosition
                motion:motionTarget="@+id/ivStarCenter"
                motion:framePosition="50"
                motion:keyPositionType="parentRelative"
                motion:percentY="0.5" />

                <!-- percentY: lệch .5 chiều cao
                keyPositionType:
                    "parentRelative": tính theo View Container
                    deltaRelative": tính theo View
                    pathRelative: tính theo Đường đi của chuyển động -->

            <!-- Xoay -->
            <KeyAttribute
                motion:motionTarget="@+id/ivStarCenter"
                motion:framePosition="23"
                android:rotation="360" />
                
            <!-- Custom Attributes (đổi màu) -->
            <KeyAttribute
                motion:framePosition="50"
                motion:motionTarget="@id/moon">
                <CustomAttribute
                        motion:attributeName="colorFilter"
                        motion:customColorValue="#FFB612"
                />
            </KeyAttribute>

        </KeyFrameSet>
        <!--  -->
    </Transition>
    <!--  -->
</MotionScene>
```

## Motion Layout với AppBar Layout

```xml
<androidx.coordinatorlayout.widget.CoordinatorLayout
       ...>
   <com.google.android.material.appbar.AppBarLayout
           android:id="@+id/appbar_layout"
           android:layout_width="match_parent"
           android:layout_height="180dp">
       <androidx.constraintlayout.motion.widget.MotionLayout
               android:id="@+id/motion_layout"
               android:layout_width="match_parent"
                android:layout_height="match_parent"
                motion:layoutDescription="@xml/step8"
                motion:motionDebug="SHOW_PATH"
                android:minHeight="80dp"
                motion:layout_scrollFlags="scroll|enterAlways|snap|exitUntilCollapsed" >
           ...
       </androidx.constraintlayout.motion.widget.MotionLayout>
   </com.google.android.material.appbar.AppBarLayout>
  
   <androidx.core.widget.NestedScrollView
           ...
           motion:layout_behavior="@string/appbar_scrolling_view_behavior" >
           ...
   </androidx.core.widget.NestedScrollView>
</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

```java
private fun coordinateMotion() {
    val appBarLayout: AppBarLayout = findViewById(R.id.appbar_layout)
    val motionLayout: MotionLayout = findViewById(R.id.motion_layout)

    val listener = AppBarLayout.OnOffsetChangedListener { unused, verticalOffset ->
        val seekPosition = -verticalOffset / appBarLayout.totalScrollRange.toFloat()
        motionLayout.progress = seekPosition
    }

    appBarLayout.addOnOffsetChangedListener(listener)
}
```

--

## Reference

- <https://developer.android.com/training/constraint-layout/motionlayout>

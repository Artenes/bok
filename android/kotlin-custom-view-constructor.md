# How to make a constructor for a custom view in kotlin

There are 2 cases. When you want to extend from a existing `Widget` or `ViewGroup`; or you want to create a custom view from sratch by extending from `View`.

# Extending from a `Widget` or a `ViewGroup`

```kotlin
import androidx.appcompat.widget.AppCompatEditText

class CustomEditText : AppCompatEditText {

    constructor(context: Context) : super(context)

    constructor(context: Context, attributeSet: AttributeSet) : super(context, attributeSet)

    constructor(context: Context, attributeSet: AttributeSet, defStyleAttr: Int) : super(context, attributeSet, defStyleAttr)

}
```

# Extending from `View` 

```kotlin
import android.view.View

class CustomView @JvmOverloads constructor(context: Context, attributeSet: AttributeSet? = null, defStyleAttr: Int = 0) : View {

}
```

# Explanation

You might want to use `@JvmOverloads` to have less code in the constructor of your custom view. Unfortunately, this kotlin annotation does not play well with views in Android. When you use it, you will be **losing the style of your view**.

`@JvmOverloads`, under the hood, generates for you three constructors:

```java
@JvmOverloads
public CustomView(@NotNull Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
   super(context, attrs, defStyleAttr);
}

@JvmOverloads
public CustomView(@NotNull Context context, @Nullable AttributeSet attrs) {
   this(context, attrs, 0);
}

@JvmOverloads
public CustomView(@NotNull Context context) {
   this(context, null, 0);
}
```

When views are instantiated from a xml layout, Android calls the second constructor. When this happens, a 0 is passed as argument for the `defStyleAttr` parameter in the first constructor. This 0 tells Android that this view has no styling, hence your view will loose its style.

To solve this you must implement each constructor and make it calls its super implementation. This will garantee that the default style available in the super class will be used when creating the view.

```kotlin
import android.view.View

class CustomView : View {

    constructor(context: Context) : super(context)

    constructor(context: Context, attributeSet: AttributeSet) : super(context, attributeSet)

    constructor(context: Context, attributeSet: AttributeSet, defStyleAttr: Int) : super(context, attributeSet, defStyleAttr)

}
```

Note that this is usually irelevant when creating a custom view from scratch, since you will be defining the default styling for your view.

---

**References**

- [**Do not always trust @JvmOverloads** *by Mateusz Młodawski at 2018-11-15*](https://medium.com/@mmlodawski/https-medium-com-mmlodawski-do-not-always-trust-jvmoverloads-5251f1ad2cfe)
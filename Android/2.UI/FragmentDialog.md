# BACK键关闭

* **当设置setCancelable，需要设置BACK键返回，用户体验才好**

```java
        //设置Back键关闭
        dialog.setOnKeyListener { dialog, keyCode, event ->
            if (event.keyCode == KeyEvent.KEYCODE_BACK) {
                dialog.dismiss()
            }
            return@setOnKeyListener false
        }
```

# 关闭dismiss

* **采用普通dismiss关闭，会造成空指针异常**

```java
Fragment prev=activity.getSupportFragmentManager().findFragmentByTag("loadingDialog");
	if (prev != null) {
     	DialogFragment df = (DialogFragment) prev;
        df.dismiss();
     }
```


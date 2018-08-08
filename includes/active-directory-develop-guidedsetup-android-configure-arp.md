## <a name="add-the-applications-registration-information-to-your-app"></a>Dodaj informacje o rejestracji aplikacji do aplikacji

W tym kroku należy dodać identyfikator klienta do projektu.

1.  Otwórz `MainActivity` (w obszarze `app`  >  `java`  >  *`{host}.{namespace}`*)
2.  Zastąp wiersz, rozpoczynając od `final static String CLIENT_ID` za pomocą:
```java
final static String CLIENT_ID = "[Enter the application Id here]";
```
3. Otwórz: `app` > `manifests` > `AndroidManifest.xml`
4. Dodaj następujące działania na `manifest\application` węzła. Ten rejestr `BrowserTabActivity` umożliwia systemu operacyjnego wznowić działanie aplikacji po zakończeniu uwierzytelniania:

```xml
<!--Intent filter to capture System Browser calling back to our app after Sign In-->
<activity
    android:name="com.microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />

        <!--Add in your scheme/host from registered redirect URI-->
        <!--By default, the scheme should be similar to 'msal[appId]' -->
        <data android:scheme="msal[Enter the application Id here]"
            android:host="auth" />
    </intent-filter>
</activity>
```


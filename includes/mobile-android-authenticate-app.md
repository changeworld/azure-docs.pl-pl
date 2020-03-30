---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: eded2d6a9f2c270a2b3ccca296277b0a016733fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183918"
---
1. Otwórz projekt w Android Studio.

2. W **Eksploratorze projektu** `ToDoActivity.java` w programie Android Studio otwórz plik i dodaj następujące instrukcje importu:

    ```java
    import java.util.concurrent.ExecutionException;
    import java.util.concurrent.atomic.AtomicBoolean;

    import android.content.Context;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;

    import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
    import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;
    ```

3. Dodaj następującą metodę do klasy **ToDoActivity:**

    ```java
    // You can choose any unique number here to differentiate auth providers from each other. Note this is the same code at login() and onActivityResult().
    public static final int GOOGLE_LOGIN_REQUEST_CODE = 1;

    private void authenticate() {
        // Sign in using the Google provider.
        mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        // When request completes
        if (resultCode == RESULT_OK) {
            // Check the request code matches the one we send in the login request
            if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
                MobileServiceActivityResult result = mClient.onActivityResult(data);
                if (result.isLoggedIn()) {
                    // sign-in succeeded
                    createAndShowDialog(String.format("You are now signed in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                    createTable();
                } else {
                    // sign-in failed, check the error message
                    String errorMessage = result.getErrorMessage();
                    createAndShowDialog(errorMessage, "Error");
                }
            }
        }
    }
    ```

    Ten kod tworzy metodę obsługi procesu uwierzytelniania Google. W oknie dialogowym zostanie wyświetlony identyfikator uwierzytelnionego użytkownika. Można kontynuować tylko po pomyślnym uwierzytelnieniu.

    > [!NOTE]
    > Jeśli używasz dostawcy tożsamości innego niż Google, zmień wartość przekazyną do metody **logowania** na jedną z następujących wartości: _MicrosoftAccount_, _Facebook,_ _Twitter_lub _windowsazureactivedirectory_.

4. W **onCreate** metody, dodać następujący wiersz kodu po kod, `MobileServiceClient` który wystąpienia obiektu.

    ```java
    authenticate();
    ```

    To wywołanie rozpoczyna proces uwierzytelniania.

5. Przenieś pozostały kod `authenticate();` po w **onCreate** metody do nowej metody **createTable:**

    ```java
    private void createTable() {

        // Get the table instance to use.
        mToDoTable = mClient.getTable(ToDoItem.class);

        mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);

        // Create an adapter to bind the items with the view.
        mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
        ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
        listViewToDo.setAdapter(mAdapter);

        // Load the items from Azure.
        refreshItemsFromTable();
    }
    ```

6. Aby upewnić się, że przekierowanie działa zgodnie `RedirectUrlActivity` `AndroidManifest.xml`z oczekiwaniami, dodaj następujący fragment kodu:

    ```xml
    <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}"
                android:host="easyauth.callback"/>
        </intent-filter>
    </activity>
    ```

7. Dodaj `redirectUriScheme` `build.gradle` do swojej aplikacji na Androida.

    ```gradle
    android {
        buildTypes {
            release {
                // ...
                manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
            }
            debug {
                // ...
                manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
            }
        }
    }
    ```

8. Dodaj `com.android.support:customtabs:23.0.1` do zależności w `build.gradle`:

    ```gradle
    dependencies {
        // ...
        compile 'com.android.support:customtabs:23.0.1'
    }
    ```

9. W menu **Uruchom** kliknij pozycję **Uruchom aplikację,** aby uruchomić aplikację i zalogować się u wybranego dostawcy tożsamości.

> [!WARNING]
> W wymienionym schemacie adresu URL jest rozróżniana wielkość liter. Upewnij się, że `{url_scheme_of_you_app}` wszystkie wystąpienia używać tego samego przypadku.

Po pomyślnym zalogowaniu aplikacja powinna działać bez błędów i powinna być w stanie wykonać kwerendę w usłudze zaplecza i dokonać aktualizacji danych.

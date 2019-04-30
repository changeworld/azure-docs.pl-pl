---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: eded2d6a9f2c270a2b3ccca296277b0a016733fd
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119768"
---
1. Otwórz projekt w programie Android Studio.

2. W **Eksplorator projektów** w programie Android Studio, otwórz `ToDoActivity.java` pliku i dodaj następujące instrukcje importu:

    ```java
    import java.util.concurrent.ExecutionException;
    import java.util.concurrent.atomic.AtomicBoolean;

    import android.content.Context;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;

    import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
    import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;
    ```

3. Dodaj następującą metodę do **ToDoActivity** klasy:

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

    Ten kod tworzy metodę, aby obsługiwać ten proces uwierzytelniania Google. Okno dialogowe wyświetla identyfikator uwierzytelnionego użytkownika. Możesz tylko przejść na pomyślne uwierzytelnienie.

    > [!NOTE]
    > Jeśli używasz dostawcy tożsamości innych niż firmy Google, zmień wartość przekazana do **logowania** metody do jednego z następujących wartości: _MicrosoftAccount_, _Facebook_, _Twitter_, lub _windowsazureactivedirectory_.

4. W **onCreate** metody, Dodaj następujący wiersz kodu po kodzie, która tworzy wystąpienie `MobileServiceClient` obiektu.

    ```java
    authenticate();
    ```

    To wywołanie rozpoczyna się proces uwierzytelniania.

5. Przenieś pozostały kod po `authenticate();` w **onCreate** nową metodę **createTable** metody:

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

6. Aby zapewnić przekierowania działa zgodnie z oczekiwaniami, Dodaj następujący fragment `RedirectUrlActivity` do `AndroidManifest.xml`:

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

7. Dodaj `redirectUriScheme` do `build.gradle` aplikacji dla systemu Android.

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

8. Dodaj `com.android.support:customtabs:23.0.1` zależności w swojej `build.gradle`:

    ```gradle
    dependencies {
        // ...
        compile 'com.android.support:customtabs:23.0.1'
    }
    ```

9. Z **Uruchom** menu, kliknij przycisk **uruchamianie aplikacji** można uruchomić aplikację i zaloguj się przy użyciu dostawcy tożsamości wybrany.

> [!WARNING]
> Schemat adresu URL, do wymienionej jest uwzględniana wielkość liter. Upewnij się, że wszystkie wystąpienia `{url_scheme_of_you_app}` wielkością liter.

Po pomyślnym zalogowaniu się, aplikacja powinna działać bez błędów i powinno być możliwe do tworzenia zapytań usługi zaplecza i dokonać aktualizacji danych.

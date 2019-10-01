---
title: Wprowadzenie do systemu Android — platforma tożsamości firmy Microsoft | Azure
description: Jak aplikacja dla systemu Android może uzyskać token dostępu i wywoływać Microsoft Graph API lub interfejsy API, które wymagają tokenów dostępu z platformy tożsamości firmy Microsoft.
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/09/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a26a7fc27fa13d86eb3b82fd4be70e5b371581f
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677959"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>Logowanie użytkowników i wywoływanie Microsoft Graph z poziomu aplikacji systemu Android

W tym samouczku dowiesz się, jak zintegrować aplikację systemu Android z platformą tożsamości firmy Microsoft. Twoja aplikacja zaloguje użytkownika, uzyskaj token dostępu, aby wywołać interfejs API Microsoft Graph i przetworzyć żądanie do interfejsu API Microsoft Graph.  

Po ukończeniu tego przewodnika aplikacja będzie akceptować logowania do osobistych kont Microsoft (w tym outlook.com, live.com i innych) oraz kont służbowych z dowolnej firmy lub organizacji korzystającej z Azure Active Directory.

## <a name="how-this-tutorial-works"></a>Jak działa ten samouczek

![Pokazuje sposób działania przykładowej aplikacji wygenerowanej przez ten samouczek](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

Aplikacja w tym samouczku zaloguje użytkowników i pobierze dane w ich imieniu.  Dostęp do tych danych uzyskuje się za pomocą chronionego interfejsu API (Microsoft Graph API), który wymaga autoryzacji i jest chroniony przez platformę tożsamości firmy Microsoft.

Więcej szczegółów:

* Twoja aplikacja zaloguje użytkownika za pomocą przeglądarki lub Microsoft Authenticator i Intune — Portal firmy.
* Użytkownik końcowy zaakceptuje uprawnienia wymagane przez aplikację.
* Aplikacja zostanie wystawiona token dostępu dla interfejsu API Microsoft Graph.
* Token dostępu zostanie uwzględniony w żądaniu HTTP do internetowego interfejsu API.
* Przetwórz odpowiedź Microsoft Graph.

Ten przykład używa biblioteki uwierzytelniania firmy Microsoft dla systemu Android (MSAL) w celu zaimplementowania uwierzytelniania: [com. Microsoft. Identity. Client](https://javadoc.io/doc/com.microsoft.identity.client/msal).

 Usługa MSAL automatycznie odnawia tokeny, dostarcza Logowanie jednokrotne między innymi aplikacjami na urządzeniu i zarządza kontami.

## <a name="prerequisites"></a>Wymagania wstępne

* Ten samouczek wymaga Android Studio wersji 3,5.

## <a name="create-a-project"></a>Tworzenie projektu

Ten samouczek spowoduje utworzenie nowego projektu. Jeśli chcesz zamiast tego pobrać ukończony samouczek, [Pobierz kod](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip).

1. Otwórz Android Studio i wybierz pozycję **Rozpocznij nowy projekt Android Studio**.
2. Wybierz **działanie podstawowe** i wybierz pozycję **dalej**.
3. Nadaj nazwę aplikacji.
4. Zapisz nazwę pakietu. Wprowadzisz ją później do Azure Portal.
5. Zmień język z **Kotlin** na **Java**.
6. Ustaw **minimalny poziom interfejsu API** na **interfejs API 19** lub nowszy, a następnie kliknij przycisk **Zakończ**.
7. W widoku Projekt wybierz **projekt** na liście rozwijanej, aby wyświetlić źródło i nieźródłowe pliki projektu, Otwórz **aplikację/Build. gradle** i ustaw `targetSdkVersion` do `28`.

## <a name="register-your-application"></a>Rejestrowanie aplikacji

1. Przejdź do witryny [Azure Portal](https://aka.ms/MobileAppReg).
2. Otwórz [blok rejestracje aplikacji](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) i kliknij pozycję **+ Nowa rejestracja**.
3. Wprowadź **nazwę** aplikacji, a następnie, bez ustawienia identyfikatora URI przekierowania, kliknij pozycję **zarejestruj**.
4. W sekcji **Zarządzanie** w wyświetlonym okienku wybierz pozycję **uwierzytelnianie** >  **+ Dodaj platformę** > **Android**. (Może być konieczne wybranie pozycji "Przełącz do nowego środowiska" w górnej części bloku, aby zobaczyć tę sekcję)
5. Wprowadź nazwę pakietu projektu. Jeśli pobrano kod, ta wartość jest `com.azuresamples.msalandroidapp`.
6. W sekcji **skrót podpisu** na stronie **Konfigurowanie aplikacji systemu Android** kliknij pozycję **generowanie skrótu sygnatury deweloperskiej.** i skopiuj polecenie Narzędzia klawiaturowego, które ma być używane dla danej platformy.

   > [!Note]
   > Narzędzie narzêdzi. exe jest instalowane w ramach zestawu Java Development Kit (JDK). Należy również zainstalować narzędzie OpenSSL, aby wykonać polecenie Narzędzia.

7. Wprowadź **skrót podpisu** wygenerowany przez narzędzie.
8. Kliknij `Configure` i Zapisz **konfigurację MSAL** , która jest wyświetlana na stronie **konfiguracji systemu Android** , aby można ją było wprowadzić podczas późniejszej konfiguracji aplikacji.  Kliknij przycisk **Gotowe**.

## <a name="build-your-app"></a>Tworzenie aplikacji

### <a name="add-your-app-registration"></a>Dodawanie rejestracji aplikacji

1. W okienku projektu Android Studio przejdź do **app\src\main\res**.
2. Kliknij prawym przyciskiem myszy pozycję **res** i wybierz polecenie **Nowy** **katalog** > . Wprowadź `raw` jako nazwę nowego katalogu i kliknij przycisk **OK**.
3. W **aplikacji** > **src** > **main** > **res** > **RAW**, Utwórz nowy plik JSON o nazwie `auth_config.json` i wklej wcześniej zapisaną konfigurację MSAL. [Aby uzyskać więcej informacji, zobacz Konfiguracja MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Configuring-your-app).
4. W **aplikacji** > **src** > **Main** > **pliku AndroidManifest. XML**Dodaj poniższe działanie `BrowserTabActivity` do treści aplikacji. Ten wpis umożliwia firmie Microsoft wywoływanie z powrotem do aplikacji po zakończeniu uwierzytelniania:

    ```xml
    <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
        </intent-filter>
    </activity>
    ```

    Zastąp nazwę pakietu zarejestrowanego w Azure Portal dla wartości `android:host=`.
    Zastąp skrót klucza zarejestrowany w Azure Portal dla wartości `android:path=`. Wartość skrótu podpisu nie powinna być zakodowana w adresie URL.

5. Wewnątrz **pliku AndroidManifest. XML**, tuż nad tagiem `<application>`, Dodaj następujące uprawnienia:

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

### <a name="create-the-apps-ui"></a>Tworzenie interfejsu użytkownika aplikacji

1. W oknie projekt Android Studio przejdź do **aplikacji** > **src** > **main** > **res** > **Układ** i Otwórz **plik activity_main. XML** i Otwórz widok **tekstu** .
2. Zmień układ działania, na przykład: `<androidx.coordinatorlayout.widget.CoordinatorLayout` do `<androidx.coordinatorlayout.widget.DrawerLayout`. 
3. Dodaj właściwość `android:orientation="vertical"` do węzła `LinearLayout`.
4. Wklej następujący kod do węzła `LinearLayout`, zastępując bieżącą zawartość:

    ```xml
    <TextView
        android:text="Welcome, "
        android:textColor="#3f3f3f"
        android:textSize="50px"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_marginTop="15dp"
        android:id="@+id/welcome"
        android:visibility="invisible"/>

    <Button
        android:id="@+id/callGraph"
        android:text="Call Microsoft Graph"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="200dp"
        android:textAllCaps="false" />

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dip"
        android:layout_weight="1"
        android:gravity="center|bottom"
        android:orientation="vertical" >

        <Button
            android:text="Sign Out"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="15dp"
            android:textColor="#FFFFFF"
            android:background="#00a1f1"
            android:textAllCaps="false"
            android:id="@+id/clearCache"
            android:visibility="invisible" />
    </LinearLayout>
    ```

### <a name="add-msal-to-your-project"></a>Dodawanie MSAL do projektu

1. W oknie projekt Android Studio przejdź do **aplikacji** > **src** > **Build. Gradle**.
2. W obszarze **zależności**Wklej następujące elementy:

    ```gradle  
    implementation 'com.android.volley:volley:1.1.1'
    implementation 'com.microsoft.identity.client:msal:1.0.+'
    ```

### <a name="use-msal"></a>Użyj MSAL

Teraz wprowadź zmiany w `MainActivity.java`, aby dodać i używać MSAL w aplikacji.
W oknie projekt Android Studio przejdź do **aplikacji** > **src** > **main** > **Java** > **com. przykład. ( Aplikacja)** i otwórz `MainActivity.java`.

#### <a name="required-imports"></a>Wymagane Importy

Dodaj następujące Importy w górnej części `MainActivity.java`:

```java
import android.app.Activity;
import android.content.Intent;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import com.android.volley.*;
import com.android.volley.toolbox.JsonObjectRequest;
import com.android.volley.toolbox.Volley;
import org.json.JSONObject;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

#### <a name="instantiate-msal"></a>Tworzenie wystąpienia MSAL

Wewnątrz klasy `MainActivity` musimy utworzyć wystąpienie MSAL wraz z kilkoma konfiguracjami dotyczącymi działania aplikacji, w tym zakresów i interfejsów API sieci Web, do których chcemy uzyskać dostęp.

Skopiuj następujące zmienne wewnątrz klasy `MainActivity`:

```java
final static String SCOPES [] = {"https://graph.microsoft.com/User.Read"};
final static String MSGRAPH_URL = "https://graph.microsoft.com/v1.0/me";

/* UI & Debugging Variables */
private static final String TAG = MainActivity.class.getSimpleName();
Button callGraphButton;
Button signOutButton;

/* Azure AD Variables */
private PublicClientApplication sampleApp;
private IAuthenticationResult authResult;
```

Zastąp zawartość `onCreate()` następującym kodem, aby utworzyć wystąpienie MSAL:

```java
super.onCreate(savedInstanceState);
setContentView(R.layout.activity_main);

callGraphButton = (Button) findViewById(R.id.callGraph);
signOutButton = (Button) findViewById(R.id.clearCache);

callGraphButton.setOnClickListener(new View.OnClickListener() {
    public void onClick(View v) {
        onCallGraphClicked();
    }
});

signOutButton.setOnClickListener(new View.OnClickListener() {
    public void onClick(View v) {
        onSignOutClicked();
    }
});

/* Configure your sample app and save state for this activity */
sampleApp = new PublicClientApplication(
        this.getApplicationContext(),
        R.raw.auth_config);

/* Attempt to get a user and acquireTokenSilent */
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {
        if (!accounts.isEmpty()) {
            /* This sample doesn't support multi-account scenarios, use the first account */
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts */
        }
    }
});
```

Powyższy kod próbuje zalogować użytkowników w trybie dyskretnym, gdy otwierają swoją aplikację za pomocą `getAccounts()` i, jeśli się to powiedzie, `acquireTokenSilentAsync()`.  W następnych sekcjach zaimplementujemy procedurę obsługi wywołania zwrotnego dla przypadku, gdy nie ma żadnych zalogowanych kont.

#### <a name="use-msal-to-get-tokens"></a>Użyj MSAL, aby uzyskać tokeny

Teraz możemy zaimplementować logikę przetwarzania interfejsu użytkownika aplikacji oraz interaktywnie uzyskać tokeny za pomocą MSAL.

MSAL uwidacznia dwie podstawowe metody uzyskiwania tokenów: `acquireTokenSilentAsync()` i `acquireToken()`.  

@no__t znak-0 w użytkowniku i uzyskaj tokeny bez interakcji z użytkownikiem, jeśli konto jest obecne. Jeśli zakończy się pomyślnie, MSAL wyśle tokeny do aplikacji, jeśli nie powiedzie się, spowoduje to wygenerowanie `MsalUiRequiredException`.  Jeśli ten wyjątek został wygenerowany lub chcesz, aby użytkownik miał interaktywne logowanie (poświadczenia, uwierzytelnianie wieloskładnikowe lub inne zasady dostępu warunkowego mogą lub nie być wymagane), a następnie użyj `acquireToken()`.  

`acquireToken()` wyświetla interfejs użytkownika podczas próby logowania użytkownika i uzyskiwania tokenów. Może jednak używać plików cookie sesji w przeglądarce lub konta w usłudze Microsoft Authenticator, aby zapewnić interaktywne Logowanie jednokrotne.

Utwórz następujące trzy metody interfejsu użytkownika w klasie `MainActivity`:

```java
/* Set the UI for successful token acquisition data */
private void updateSuccessUI() {
    callGraphButton.setVisibility(View.INVISIBLE);
    signOutButton.setVisibility(View.VISIBLE);
    findViewById(R.id.welcome).setVisibility(View.VISIBLE);
    ((TextView) findViewById(R.id.welcome)).setText("Welcome, " +
            authResult.getAccount().getUsername());
    findViewById(R.id.graphData).setVisibility(View.VISIBLE);
}

/* Set the UI for signed out account */
private void updateSignedOutUI() {
    callGraphButton.setVisibility(View.VISIBLE);
    signOutButton.setVisibility(View.INVISIBLE);
    findViewById(R.id.welcome).setVisibility(View.INVISIBLE);
    findViewById(R.id.graphData).setVisibility(View.INVISIBLE);
    ((TextView) findViewById(R.id.graphData)).setText("No Data");

    Toast.makeText(getBaseContext(), "Signed Out!", Toast.LENGTH_SHORT)
            .show();
}

/* Use MSAL to acquireToken for the end-user
 * Callback will call Graph api w/ access token & update UI
 */
private void onCallGraphClicked() {
    sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
}
```

Dodaj następujące metody, aby uzyskać bieżące działanie i przetworzyć ciche & interaktywne wywołania zwrotne:

```java
public Activity getActivity() {
    return this;
}

/* Callback used in for silent acquireToken calls.
 * Looks if tokens are in the cache (refreshes if necessary and if we don't forceRefresh)
 * else errors that we need to do an interactive request.
 */
private AuthenticationCallback getAuthSilentCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, call graph now */
            Log.d(TAG, "Successfully authenticated");

            /* Store the authResult */
            authResult = authenticationResult;

            /* call graph */
            callGraphAPI();

            /* update the UI to post call graph state */
            updateSuccessUI();
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside the exception */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }

        @Override
        public void onCancel() {
            /* User cancelled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}

/* Callback used for interactive request.  If succeeds we use the access
 * token to call the Microsoft Graph. Does not check cache
 */
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, call graph now */
            Log.d(TAG, "Successfully authenticated");
            Log.d(TAG, "ID Token: " + authenticationResult.getIdToken());

            /* Store the auth result */
            authResult = authenticationResult;

            /* call graph */
            callGraphAPI();

            /* update the UI to post call graph state */
            updateSuccessUI();
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside the exception */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            }
        }

        @Override
        public void onCancel() {
            /* User cancelled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

#### <a name="use-msal-for-sign-out"></a>Użyj MSAL do wylogowania

Następnie Dodaj obsługę wylogowywania.

> [!Important]
> Wylogowanie za pomocą usługi MSAL usuwa wszystkie znane informacje o użytkowniku z aplikacji, ale użytkownik będzie nadal mieć aktywną sesję na swoim urządzeniu. Jeśli użytkownik spróbuje się zalogować ponownie, może wyświetlić interfejs użytkownika logowania, ale może nie być konieczne ponowne wprowadzenie poświadczeń, ponieważ sesja urządzenia jest nadal aktywna.

Aby dodać możliwość wylogowania, Dodaj następującą metodę w klasie `MainActivity`. Ta metoda umożliwia przechodzenie między wszystkimi kontami i usuwanie ich:

```java
/* Clears an account's tokens from the cache.
 * Logically similar to "sign out" but only signs out of this app.
 * User will get interactive SSO if trying to sign back-in.
 */
private void onSignOutClicked() {
    /* Attempt to get a user and acquireTokenSilent
     * If this fails we do an interactive request
     */
    sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
        @Override
        public void onAccountsLoaded(final List<IAccount> accounts) {

            if (accounts.isEmpty()) {
                /* No accounts to remove */

            } else {
                for (final IAccount account : accounts) {
                    sampleApp.removeAccount(
                            account,
                            new PublicClientApplication.AccountsRemovedCallback() {
                        @Override
                        public void onAccountsRemoved(Boolean isSuccess) {
                            if (isSuccess) {
                                /* successfully removed account */
                            } else {
                                /* failed to remove account */
                            }
                        }
                    });
                }
            }

            updateSignedOutUI();
        }
    });
}
```

#### <a name="call-the-microsoft-graph-api"></a>Wywoływanie interfejsu API Microsoft Graph

Po otrzymaniu tokenu możemy wysłać żądanie do [interfejsu API Microsoft Graph](https://graph.microsoft.com) token dostępu będzie wewnątrz `AuthenticationResult` w ramach metody `onSuccess()` wywołania zwrotnego uwierzytelniania. Do skonstruowania autoryzowanego żądania aplikacja będzie musiała dodać token dostępu do nagłówka HTTP:

| klucz nagłówka    | wartość                 |
| ------------- | --------------------- |
| Autoryzacja | Okaziciela \<access-token > |

Dodaj następujące dwie metody wewnątrz klasy `MainActivity` w celu wywołania grafu i zaktualizowania interfejsu użytkownika:

```java
/* Use Volley to make an HTTP request to the /me endpoint from MS Graph using an access token */
private void callGraphAPI() {
    Log.d(TAG, "Starting volley request to graph");

    /* Make sure we have a token to send to graph */
    if (authResult.getAccessToken() == null) {return;}

    RequestQueue queue = Volley.newRequestQueue(this);
    JSONObject parameters = new JSONObject();

    try {
        parameters.put("key", "value");
    } catch (Exception e) {
        Log.d(TAG, "Failed to put parameters: " + e.toString());
    }
    JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
            parameters,new Response.Listener<JSONObject>() {
        @Override
        public void onResponse(JSONObject response) {
            /* Successfully called graph, process data and send to UI */
            Log.d(TAG, "Response: " + response.toString());

            updateGraphUI(response);
        }
    }, new Response.ErrorListener() {
        @Override
        public void onErrorResponse(VolleyError error) {
            Log.d(TAG, "Error: " + error.toString());
        }
    }) {
        @Override
        public Map<String, String> getHeaders() {
            Map<String, String> headers = new HashMap<>();
            headers.put("Authorization", "Bearer " + authResult.getAccessToken());
            return headers;
        }
    };

    Log.d(TAG, "Adding HTTP GET to Queue, Request: " + request.toString());

    request.setRetryPolicy(new DefaultRetryPolicy(
            3000,
            DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
            DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
    queue.add(request);
}

/* Sets the graph response */
private void updateGraphUI(JSONObject graphResponse) {
    TextView graphText = findViewById(R.id.graphData);
    graphText.setText(graphResponse.toString());
}
```

#### <a name="multi-account-applications"></a>Aplikacje wielokonta

Ta aplikacja została skompilowana dla scenariusza jednego konta. MSAL obsługuje również scenariusze obejmujące wiele kont, ale wymagają pewnej dodatkowej pracy z poziomu aplikacji. Należy utworzyć interfejs użytkownika, aby ułatwić użytkownikowi wybranie konta, które ma być używane dla każdej akcji wymagającej tokenów. Alternatywnie aplikacja może zaimplementować heurystykę, aby wybrać konto, które będzie używane przez metodę `getAccounts()`.

## <a name="test-your-app"></a>Testowanie aplikacji

### <a name="run-locally"></a>Uruchamianie lokalnie

Kompiluj i Wdróż aplikację na urządzeniu testowym lub w emulatorze. Powinno być możliwe zalogowanie się i uzyskanie tokenów dla usługi Azure AD lub osobistych kont Microsoft.

Po zalogowaniu aplikacja będzie wyświetlać dane zwrócone w punkcie końcowym Microsoft Graph `/me`.

### <a name="consent"></a>Posiadacz

Gdy użytkownik po raz pierwszy zaloguje się do aplikacji, otrzyma monit o tożsamość firmy Microsoft, aby wyrazić zgodę na wymagane uprawnienia.  Chociaż większość użytkowników może wyrażać zgodę, niektórzy dzierżawy usługi Azure AD mają wyłączoną opcję zgody użytkownika, która wymaga od administratorów zgody w imieniu wszystkich użytkowników. Aby obsłużyć ten scenariusz, należy zarejestrować zakresy aplikacji w Azure Portal.

## <a name="get-help"></a>Uzyskiwanie pomocy

Odwiedź [Pomoc i pomoc techniczną,](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) Jeśli masz problemy z tym samouczkiem lub platformą tożsamości firmy Microsoft.

Pomóż nam ulepszyć platformę tożsamości firmy Microsoft. Powiedz nam, co myślisz, wykonując krótką ankietę z dwoma pytaniami.

> [!div class="nextstepaction"]
> [Microsoft Identity platform — ankieta](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)

---
title: Wprowadzenie do systemu Android — platforma tożsamości firmy Microsoft | Azure
description: Jak aplikacji systemu Android można uzyskać token dostępu i wywołania interfejsu API Microsoft Graph lub interfejsów API, które wymagają tokenów dostępu z platformą Microsoft identity.
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/26/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d8f8c8e98a7a99fc1b94bd5ae84062843ebabbc1
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550591"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>Logowania użytkowników i wywoływania usługi Microsoft Graph w aplikacji systemu Android

W tym samouczku dowiesz się, jak zintegrować aplikację systemu Android z platformą Microsoft identity. W szczególności aplikacji będzie zalogować użytkownika uzyskania tokenu dostępu do wywołania interfejsu API programu Microsoft Graph i wysłać żądanie do interfejsu API programu Microsoft Graph.  

Po zakończeniu przewodnika, aplikacja będzie akceptować logowania osobistych kont Microsoft (w tym outlook.com, live.com i inne) i służbowego konta z firmy lub organizacji, która używa usługi Azure Active Directory.

## <a name="how-this-tutorial-works"></a>Jak działa w tym samouczku

![Pokazuje, jak działa przykładowej aplikacji wygenerowanych w ramach tego samouczka](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

Aplikacja w tym przykładzie będzie logować użytkowników i Pobierz dane w ich imieniu.  Te dane będą uzyskiwać dostęp za pośrednictwem chronionego API (Microsoft interfejsu API programu Graph w tym przypadku), który wymaga autoryzacji.

Więcej szczegółów:

* Aplikacja podpisze użytkownika za pomocą przeglądarki lub Microsoft Authenticator i Portal firmy usługi Intune.
* Użytkownik końcowy będzie akceptować uprawnienia, które zażądał aplikacji. 
* Aplikacja zostanie wystawiony token dostępu do interfejsu API programu Microsoft Graph.
* Token dostępu zostaną uwzględnione w żądaniu HTTP do interfejsu API sieci web.
* Przetworzenie odpowiedzi programu Microsoft Graph.

W tym przykładzie używa biblioteki Microsoft Authentication for Android (MSAL) do zaimplementowania uwierzytelniania Biblioteka MSAL będzie automatycznie odnowić tokenów, dostarczanie SSO między innymi aplikacjami na urządzeniu i zarządzanie konta.

## <a name="prerequisites"></a>Wymagania wstępne

* Ta instalacja z przewodnikiem korzysta z programu Android Studio.
* System android 16 lub nowszy jest wymagany (19 + jest zalecane).

## <a name="library"></a>Biblioteka

W tym przewodniku używane są następujące biblioteki uwierzytelniania:

|Biblioteka|Opis|
|---|---|
|[com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)|Biblioteka uwierzytelniania firmy Microsoft (MSAL)|

## <a name="set-up-your-project"></a>konfigurowanie projektu

W tym samouczku spowoduje utworzenie nowego projektu. Jeśli chcesz zamiast tego Pobierz samouczek ukończone [pobrać kod](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip).

### <a name="create-a-new-project"></a>Tworzenie nowego projektu

1. Otwórz program Android Studio, a następnie wybierz opcję **Utwórz nowy projekt programu Android Studio**.
    - Jeśli program Android Studio jest już otwarty, wybierz opcję **pliku** > **nowy** > **nowy projekt**.
2. Pozostaw **puste działanie** i wybierz **dalej**.
3. Nadaj nazwę aplikacji, ustaw `Minimum API level` do **interfejsu API 19 lub nowszej**, trafień **Zakończ**.
5. W swojej `app/build.gradle`ustaw `targetedSdkVersion` 27. 

## <a name="register-your-application"></a>Rejestrowanie aplikacji

Aplikację można zarejestrować na dwa sposoby, zgodnie z opisem w dwóch następnych sekcjach.

### <a name="register-your-app"></a>Zarejestruj swoją aplikację

1. Przejdź do [witryny Azure portal](https://aka.ms/MobileAppReg) > Wybierz `New registration`. 
2. Wprowadź **nazwa** aplikacji > `Register`. **Identyfikator URI przekierowania nie jest ustawiona na tym etapie**. 
3. W `Manage` sekcję i przejdź do `Authentication` > `Add a platform` > `Android`
    - Wprowadź nazwę pakietu projektu. Jeśli pobrano kod, ta wartość jest `com.azuresamples.msalandroidapp`. 
    - Wprowadź swoje wyznaczania wartości skrótu podpisu debugowania/programowania. Użyj polecenia KeyTool w portalu, aby wygenerować wyznaczania wartości skrótu podpisu. 
4. Trafienia `Configure` i przechowywać ***konfiguracji biblioteki MSAL*** na później. 

## <a name="build-your-app"></a>Tworzenie aplikacji

### <a name="configure-your-android-app"></a>Konfiguruj aplikację systemu Android

1. Kliknij prawym przyciskiem myszy **res** > **New** > **folderu** > **pierwotne Folder zasobów**
2. W **aplikacji** > **res** > **pierwotne**, Utwórz plik JSON o nazwie `auth_config.json` i Wklej swoje ***konfiguracji biblioteki MSAL***. Zobacz [MSAL konfiguracji, aby uzyskać więcej informacji](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Configuring-your-app).
   <!-- Workaround for Docs conversion bug -->
3. W **aplikacji** > **manifesty** > **AndroidManifest.xml**, Dodaj `BrowserTabActivity` działania poniżej. Ten wpis umożliwia firmie Microsoft w celu wywołania zwrotnego do aplikacji po jej zakończeniu uwierzytelniania:

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

    Uwaga: wyznaczania wartości skrótu podpisu używane nie powinny być zakodowane w w adresie URL **AndroidManifest.xml**. 

4. Wewnątrz **AndroidManifest.xml** i tuż nad `<application>` tag, Dodaj następujące uprawnienia:

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

5. W `BrowserTabActivity`, Zastąp ***nazwy pakietu*** i ***wyznaczania wartości skrótu podpisu*** z wartościami zarejestrowanymi w witrynie Azure portal.

### <a name="create-the-apps-ui"></a>Tworzenie aplikacji interfejsu użytkownika

1. Przejdź do **res** > **układ**, a następnie otwórz **activity_main.xml**.
2. Zmienianie układu działania z `android.support.constraint.ConstraintLayout` lub innych do `LinearLayout`.
3. Dodaj `android:orientation="vertical"` właściwość `LinearLayout` węzła.
4. Wklej następujący kod do `LinearLayout` węzła, zastępując bieżącej zawartości:

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

### <a name="add-msal-to-your-project"></a>Dodawanie biblioteki MSAL do projektu

1. W programie Android Studio wybierz **skryptów narzędzia Gradle** > **build.gradle (moduł: aplikacja)** .
2. W obszarze **zależności**, wklej następujący kod:

    ```gradle  
    implementation 'com.android.volley:volley:1.1.1'
    implementation 'com.microsoft.identity.client:msal:0.3.+'
    ```

### <a name="use-msal"></a>Użycia biblioteki MSAL 

Kilka następnych sekcji będzie dokonywania zmiany wewnątrz `MainAcitivty.java`. Firma Microsoft będzie Instruktaż poszczególne kroki wymagane do dodawania i użycia biblioteki MSAL w swojej aplikacji.

#### <a name="required-imports"></a>Wymaganych importów

Dodaj następujące instrukcje importu do projektu: 

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

#### <a name="instantiating-msal"></a>Utworzenie wystąpienia biblioteki MSAL 

Wewnątrz `MainActivity` klasy, będziemy potrzebować do utworzenia wystąpienia MSAL wraz z kilkoma konfiguracjami o jakie są będzie zrobić, w tym zakresy i internetowego interfejsu API, chcemy, aby uzyskać dostęp do aplikacji. 

Skopiuj poniższe zmienne wewnątrz `MainActivity`:

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

Teraz do utworzenia wystąpienia MSAL, skopiuj poniższy kod wewnątrz `onCreate(...)` metody:

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

W powyższym fragmencie kodu spróbuje się zalogować użytkowników dyskretnie kiedy adresat otworzy aplikację za pomocą `getAccounts(...)` i, jeśli operacja się powiedzie, `acquireTokenSilentAsync(...)`.  W kolejnych sekcjach firma Microsoft będzie implementowana obsługi wywołania zwrotnego dla przypadku nie ma żadnych kont zalogowany. 

#### <a name="use-msal-to-get-tokens"></a>Użycia biblioteki MSAL do uzyskania tokenów

Teraz można zaimplementować Interfejsie użytkownika aplikacji logiki przetwarzania i uzyskiwania tokenów interaktywnie przy użyciu biblioteki MSAL. 

Biblioteka MSAL udostępnia dwie podstawowe metody uzyskiwania tokenów: `acquireTokenSilentAsync` i `acquireToken`.  

`acquireTokenSilentAsync` loguje użytkownika i uzyskiwać tokeny bez żadnej interakcji użytkownika, jeśli konto jest obecny. Jeśli się powiedzie, biblioteka MSAL będzie przekazywanie tokenów do swojej aplikacji, jeśli zakończy się niepowodzeniem, spowoduje wygenerowanie `MsalUiRequiredException`.  Jeżeli ten wyjątek jest generowany, lub użytkownika, aby mieć interakcyjnego logowania w środowisku (poświadczenia, mfa lub innych dostępu warunkowego, zasady mogą lub nie mogą być wymagane), można użyć `acquireToken`.  

`acquireToken` zawsze wyświetli interfejsu użytkownika podczas próby zalogowania się użytkownika i uzyskiwanie tokenów. jednak może ona używać plików cookie sesji w przeglądarce lub konta w aplikacji Microsoft authenticator zapewnienie środowiska interaktywnego logowania jednokrotnego. 

Aby rozpocząć, należy utworzyć następujące trzy metody interfejsu użytkownika wewnątrz `MainActivity` klasy:

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

Następnie dodaj metodę, aby uzyskać bieżące działanie i przetwarzać silent & interaktywne wywołań zwrotnych:

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

#### <a name="use-msal-for-sign-out"></a>Użycia biblioteki MSAL do wylogowania

Teraz, up, dodamy obsługę wylogowania do naszej aplikacji. 

Należy pamiętać, wylogowania z biblioteki MSAL usuwa wszelkie informacje o użytkowniku z tej aplikacji, ale użytkownik nadal będzie miał aktywną sesję na swoim urządzeniu. Jeśli użytkownik spróbuje się zalogować ponownie ich może zostać wyświetlony interakcji, ale nie może być konieczne ponowne wprowadzenie poświadczeń z powodu sesji urządzenia są aktywne. 

Aby dodać wylogowania, skopiuj następującą metodę w swojej aplikacji, aby przewijać wszystkich kont i usuwa je:

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

#### <a name="call-the-microsoft-graph-api"></a>Wywołanie interfejsu API programu Microsoft Graph

Po pomyślnym otrzymywaliśmy token, możemy utworzyć żądanie do interfejsu API programu Microsoft Graph. Token dostępu będzie znajdować się w `AuthenticationResult` wewnątrz wywołania zwrotnego uwierzytelniania `onSuccess(...)` metody. Aby utworzyć żądanie autoryzowanych, aplikacji konieczne będzie dodanie token dostępu do nagłówka HTTP:

| Klucz nagłówka    | value                 |
| ------------- | --------------------- |
| Authorization | Elementu nośnego \<token dostępu > |

Aby to zrobić w kodzie, należy dodać do aplikacji, aby wywołanie programu graph i interfejsu użytkownika aktualizacji następujących dwóch metod: 

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

Dowiedz się więcej o [interfejsu API Microsoft Graph](https://graph.microsoft.com)!

#### <a name="multi-account-applications"></a>Aplikacje dla wielu kont

Ta aplikacja została stworzona w scenariuszu jednego konta. Biblioteka MSAL obsługuje również scenariusze wielu konta, ale wymaga wykonania dodatkowych czynności z aplikacji. Należy utworzyć interfejs użytkownika, aby pomóc użytkownika wybierz konto, które mają być użyte dla każdej akcji, które wymagają tokenów. Alternatywnie aplikację można wdrożyć heurystyki o wybranie konta do użycia za pomocą `getAccounts(...)` metody. 

## <a name="test-your-app"></a>Testowanie aplikacji

### <a name="run-locally"></a>Uruchamianie lokalnie

Jeśli wykonano powyższe kodu, spróbuj do tworzenia i wdrażania aplikacji na urządzeniu testowym lub w emulatorze. Powinna być może logować się i uzyskiwać tokeny dla usługi Azure AD lub osobistych kont Microsoft! Po zalogowaniu użytkownika tej aplikacji będą wyświetlane dane zwrócone przez program Microsoft Graph `/me` punktu końcowego. 

Jeśli masz problemy, możesz otworzyć zgłoszenie tego dokumentu lub biblioteka MSAL i Daj nam znać. 

### <a name="consent-to-your-app"></a>Zgoda na aplikację

Dowolny użytkownik zaloguje się do aplikacji, po raz pierwszy zostanie wyświetlony monit, tożsamości firmy Microsoft do wyrażenia zgody na uprawnienia wymagane.  Mimo że większość użytkowników stanie wyrażanie zgody, niektóre dzierżaw usługi Azure AD zostało wyłączone przez zgody użytkownika - wymagające zgody w imieniu wszystkich użytkowników, administratorów.  Aby zapewnić obsługę tego scenariusza, należy zarejestrować zakresy Twojej aplikacji w witrynie Azure portal.

## <a name="help-and-support"></a>Pomoc i obsługa techniczna

Wystąpił problem z dowolnym z tego samouczka lub z platformą tożsamości Microsoft? Zobacz [Pomoc i obsługa techniczna](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options)

---
title: Zaloguj użytkowników do/wyjścia & wywołać Microsoft Graph (Android) - Platforma tożsamości firmy Microsoft | Azure
description: Pobierz token dostępu i wywołaj program Microsoft Graph lub interfejsy API, które wymagają tokenów dostępu z platformy tożsamości firmy Microsoft (Android)
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 11/26/2019
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 5c8bd5accefceee042601c3cf7d71f5e9131e04e
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80880826"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-from-an-android-application"></a>Samouczek: Zaloguj użytkowników i wywołać Microsoft Graph z aplikacji dla systemu Android 

>[!NOTE]
>W tym samouczku przedstawiono uproszczone przykłady pracy z msal dla systemu Android. Dla uproszczenia w tym samouczku używa się tylko trybu pojedynczego konta. Można również wyświetlić repozytorium i sklonować [wstępnie skonfigurowane przykładowej aplikacji](https://github.com/Azure-Samples/ms-identity-android-java/) do eksplorowania bardziej złożonych scenariuszy. Zobacz [szybki start, aby](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-android) uzyskać więcej informacji na temat przykładowej aplikacji, konfiguracji i rejestracji. 

W tym samouczku dowiesz się, jak zintegrować aplikację android z platformą tożsamości firmy Microsoft przy użyciu biblioteki uwierzytelniania firmy Microsoft dla systemu Android. Dowiesz się, jak zalogować się i wylogować użytkownika, uzyskać token dostępu do wywołania interfejsu API programu Microsoft Graph i złożyć żądanie do interfejsu API wykresu. 

> [!div class="checklist"]
> * Integracja aplikacji na Androida z platformą Microsoft Identity Platform 
> * Logowanie użytkownika 
> * Uzyskiwanie tokenu dostępu do wywoływania interfejsu API programu Microsoft Graph 
> * Wywoływanie interfejsu API programu Microsoft Graph 
> * Wyloguj się z użytkownika 

Po zakończeniu tego samouczka aplikacja zaakceptuje logowania osobistych kont Microsoft (w tym outlook.com, live.com i inne), a także konta służbowe lub szkolne z dowolnej firmy lub organizacji korzystającej z usługi Azure Active Directory.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="how-this-tutorial-works"></a>Jak działa ten samouczek

![Pokazuje, jak działa przykładowa aplikacja generowana przez ten samouczek](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

Aplikacja w tym samouczku zaloguje się na użytkowników i pobierze dane w ich imieniu. Te dane będą dostępne za pośrednictwem chronionego interfejsu API (Microsoft Graph API), który wymaga autoryzacji i jest chroniony przez platformę tożsamości firmy Microsoft.

Więcej szczegółów:

* Aplikacja zaloguje się do użytkownika za pośrednictwem przeglądarki lub aplikacji Microsoft Authenticator i portalu firmy usługi Intune.
* Użytkownik końcowy zaakceptuje uprawnienia żądane przez aplikację.
* Aplikacja zostanie wystawiona token dostępu dla interfejsu API programu Microsoft Graph.
* Token dostępu zostanie uwzględniony w żądaniu HTTP do internetowego interfejsu API.
* Przetwórz odpowiedź programu Microsoft Graph.

W tym przykładzie użyto biblioteki uwierzytelniania firmy Microsoft dla systemu Android (MSAL) do zaimplementowania uwierzytelniania: [com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal).

 Usługa MSAL automatycznie odnawia tokeny, dostarcza logowanie jednokrotne między innymi aplikacjami na urządzeniu i zarządza kontami.

### <a name="prerequisites"></a>Wymagania wstępne

* Ten samouczek wymaga wersji Android Studio 3.5+

## <a name="create-a-project"></a>Tworzenie projektu
Jeśli nie masz jeszcze aplikacji systemu Android, wykonaj następujące kroki, aby skonfigurować nowy projekt. 

1. Otwórz Android Studio i wybierz **rozpocznij nowy projekt Android Studio**.
2. Wybierz **pozycję Działanie podstawowe** i wybierz pozycję **Dalej**.
3. Nadaj nazwę aplikacji.
4. Zapisz nazwę pakietu. Wprowadzisz go później do witryny Azure portal.
5. Zmień język z **Kotlin** na **Java**.
6. Ustaw **minimalny poziom interfejsu API** na **API 19** lub wyższy, a następnie kliknij przycisk **Zakończ**.
7. W widoku projektu wybierz pozycję **Projekt** w rozwijanej, aby wyświetlić źródłowe i niezwiązane ze źródłami pliki projektu, otwórz **aplikację/build.gradle** i ustaw `targetSdkVersion` opcję `28`.

## <a name="integrate-with-microsoft-authentication-library"></a>Integracja z biblioteką uwierzytelniania firmy Microsoft 

### <a name="register-your-application"></a>Rejestrowanie aplikacji

1. Przejdź do [witryny Azure portal](https://aka.ms/MobileAppReg).
2. Otwórz [blok Rejestracje aplikacji](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) i kliknij **+Nowa rejestracja**.
3. Wprowadź **nazwę** aplikacji, a **następnie, bez** ustawiania identyfikatora URI przekierowania, kliknij pozycję **Zarejestruj**.
4. W sekcji **Zarządzanie** okienkiem, które się pojawi, wybierz pozycję **Uwierzytelnianie** > **+ Dodaj platformę** > **Android**. (Może być trzeba wybrać "Przełącz do nowego doświadczenia" w górnej części ostrza, aby zobaczyć tę sekcję)
5. Wprowadź nazwę pakietu projektu. Jeśli kod został pobrany, `com.azuresamples.msalandroidapp`ta wartość to .
6. W sekcji **Skrót podpisu** na stronie **Konfigurowanie aplikacji na androida** kliknij pozycję **Generowanie skrótu podpisu dewelopera.** i skopiuj polecenie KeyTool do użycia na platformie.

   > [!Note]
   > Program KeyTool.exe jest instalowany jako część zestawu Java Development Kit (JDK). Aby wykonać polecenie KeyTool, należy również zainstalować narzędzie OpenSSL. Więcej informacji można znaleźć [w dokumentacji systemu Android dotyczącej generowania klucza.](https://developer.android.com/studio/publish/app-signing#generate-key) 

7. Wprowadź skrót **podpisu wygenerowany** przez KeyTool.
8. Kliknij `Configure` i zapisz **konfigurację MSAL,** która pojawia się na stronie **konfiguracji systemu Android,** aby można było ją wprowadzić podczas późniejszej konfiguracji aplikacji.  Kliknij przycisk **Gotowe**.

### <a name="configure-your-application"></a>Konfigurowanie aplikacji 

1. W okienku projektu Programu Android Studio przejdź do **aplikacji\src\main\res**.
2. Kliknij prawym przyciskiem myszy **pozycję Res** i wybierz polecenie **Nowy** > **katalog**. Wprowadź `raw` jako nową nazwę katalogu i kliknij przycisk **OK**.
3. W **aplikacji** > **src** > **main** > **res** > **raw**, `auth_configbn_single_account.json` utwórz nowy plik JSON o nazwie i wklej konfigurację MSAL, która została zapisana wcześniej. 

    Poniżej identyfikatora URI przekierowania wklej: 
    ```json
      "account_mode" : "SINGLE",
    ```
    Twój plik konfiguracyjny powinien przypominać ten przykład: 
    ```json   
    {
      "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
      "authorization_user_agent" : "DEFAULT",
      "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
      "account_mode" : "SINGLE",
      "authorities" : [
        {
          "type": "AAD",
          "audience": {
            "type": "AzureADandPersonalMicrosoftAccount",
            "tenant_id": "common"
          }
        }
      ]
    }
   ```
    
   >[!NOTE]
   >W tym samouczku pokazano tylko, jak skonfigurować aplikację w trybie pojedynczego konta. Więcej informacji na temat [trybu pojedynczego konta i wielu kont](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account) można znaleźć w dokumentacji, aby uzyskać więcej informacji na temat trybu pojedynczego konta i [konfigurowania aplikacji](https://docs.microsoft.com/azure/active-directory/develop/msal-configuration)
   
4. W **aplikacji** > **src** > **główny** > **AndroidManifest.xml**, dodać `BrowserTabActivity` działanie poniżej do treści aplikacji. Ten wpis umożliwia firmie Microsoft oddzwanianie do aplikacji po zakończeniu uwierzytelniania:

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

    Zastąp nazwę pakietu zarejestrowaną `android:host=` w witrynie Azure portal dla wartości.
    Zastąp wartość skrótu klucza `android:path=` zarejestrowanego w witrynie Azure portal. Skrót podpisu **nie** powinien być zakodowany w adresie URL. Upewnij się, że `/` istnieje interwniuszek na początku skrótu podpisu. 
    >[!NOTE]
    >Wartość "Nazwa pakietu", którą `android:host` zastąpisz, powinna wyglądać podobnie do: "com.azuresamples.msalandroidapp" "Hash podpisu", którą zastąpisz wartością, `android:path` powinna wyglądać podobnie do: "/1wIqXSqBj7w+h11ZifsnqwgyKrY=" Będziesz mógł również znaleźć te wartości w bloku uwierzytelniania rejestracji aplikacji. Należy pamiętać, że identyfikator URI przekierowania będzie wyglądać podobnie do: "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D". Podczas gdy hash podpisu jest adres URL zakodowany na końcu tej wartości, hash **podpisu nie** powinny być zakodowane w wartości. `android:path` 

## <a name="use-msal"></a>Korzystanie z usługi MSAL 

### <a name="add-msal-to-your-project"></a>Dodawanie usługi MSAL do projektu

1. W oknie projektu Android Studio przejdź do **aplikacji** > **src** > **build.gradle** i dodaj następujące elementy: 

    ```gradle
    repositories{
        jcenter()
    }  
    dependencies{
        implementation 'com.microsoft.identity.client:msal:1.+'
        implementation 'com.microsoft.graph:microsoft-graph:1.5.+'
    }
    packagingOptions{
        exclude("META-INF/jersey-module-version") 
    }
    ```
    [Więcej informacji na temat sdk programu Microsoft Graph](https://github.com/microsoftgraph/msgraph-sdk-java/)

### <a name="required-imports"></a>Wymagane importy 

Dodaj następujące do góry **aplikacji** > **src** > **main**> **java** > **com.example(yourapp)** > **MainActivity.java** 

```java
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.appcompat.app.AppCompatActivity;
import com.google.gson.JsonObject;
import com.microsoft.graph.authentication.IAuthenticationProvider; //Imports the Graph sdk Auth interface
import com.microsoft.graph.concurrency.ICallback;
import com.microsoft.graph.core.ClientException;
import com.microsoft.graph.http.IHttpRequest;
import com.microsoft.graph.models.extensions.*;
import com.microsoft.graph.requests.extensions.GraphServiceClient;
import com.microsoft.identity.client.AuthenticationCallback; // Imports MSAL auth methods
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

## <a name="instantiate-publicclientapplication"></a>Tworzenie wystąpienia aplikacji PublicClientApplication
#### <a name="initialize-variables"></a>Inicjowanie zmiennych 
```java
private final static String[] SCOPES = {"File.Read"};
/* Azure AD v2 Configs */
final static String AUTHORITY = "https://login.microsoftonline.com/common";
private ISingleAccountPublicClientApplication mSingleAccountApp;

private static final String TAG = MainActivity.class.getSimpleName();

/* UI & Debugging Variables */
Button signInButton;
Button signOutButton;
Button callGraphApiInteractiveButton;
Button callGraphApiSilentButton;
TextView logTextView;
TextView currentUserTextView;
```

### <a name="oncreate"></a>Oncreate
Wewnątrz `MainActivity` klasy, odnoszą się do następujących onCreate() metoda do `SingleAccountPublicClientApplication`tworzenia wystąpienia MSAL przy użyciu .

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    initializeUI();

    PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(),
            R.raw.auth_config_single_account, new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
                @Override
                public void onCreated(ISingleAccountPublicClientApplication application) {
                    mSingleAccountApp = application;
                    loadAccount();
                }
                @Override
                public void onError(MsalException exception) {
                    displayError(exception);
                }
            });
}
```

### <a name="loadaccount"></a>konto loadAccount 

```java
//When app comes to the foreground, load existing account to determine if user is signed in 
private void loadAccount() {
    if (mSingleAccountApp == null) {
        return;
    }

    mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback() {
        @Override
        public void onAccountLoaded(@Nullable IAccount activeAccount) {
            // You can use the account data to update your UI or your app database.
            updateUI(activeAccount);
        }
        
        @Override
        public void onAccountChanged(@Nullable IAccount priorAccount, @Nullable IAccount currentAccount) {
            if (currentAccount == null) {
                // Perform a cleanup task as the signed-in account changed.
                performOperationOnSignOut();
            }
        }

        @Override
        public void onError(@NonNull MsalException exception) {
            displayError(exception);
        }
    });
}
```

### <a name="initializeui"></a>initializeUI
Posłuchaj przycisków i metod wywołań lub odpowiednio rejestruj błędy. 
```java
private void initializeUI(){
        signInButton = findViewById(R.id.signIn);
        callGraphApiSilentButton = findViewById(R.id.callGraphSilent);
        callGraphApiInteractiveButton = findViewById(R.id.callGraphInteractive);
        signOutButton = findViewById(R.id.clearCache);
        logTextView = findViewById(R.id.txt_log);
        currentUserTextView = findViewById(R.id.current_user);
        
        //Sign in user 
        signInButton.setOnClickListener(new View.OnClickListener(){
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.signIn(MainActivity.this, null, SCOPES, getAuthInteractiveCallback());
            }
        });
        
        //Sign out user
        signOutButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
                    @Override
                    public void onSignOut() {
                        updateUI(null);
                        performOperationOnSignOut();
                    }
                    @Override
                    public void onError(@NonNull MsalException exception){
                        displayError(exception);
                    }
                });
            }
        });
        
        //Interactive 
        callGraphApiInteractiveButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.acquireToken(MainActivity.this, SCOPES, getAuthInteractiveCallback());
            }
        });

        //Silent
        callGraphApiSilentButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.acquireTokenSilentAsync(SCOPES, AUTHORITY, getAuthSilentCallback());
            }
        });
    }
```

> [!Important]
> Wylogowanie się za pomocą usługi MSAL powoduje usunięcie wszystkich znanych informacji o użytkowniku z aplikacji, ale użytkownik nadal będzie miał aktywną sesję na swoim urządzeniu. Jeśli użytkownik próbuje zalogować się ponownie mogą zobaczyć logowania interfejsu użytkownika, ale nie może być konieczne ponowne wniesienie swoich poświadczeń, ponieważ sesja urządzenia jest nadal aktywna. 

### <a name="getauthinteractivecallback"></a>getAuthInteractiveCallback
Wywołanie zwrotne używane dla żądań interaktywnych.

```java 
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, use it to call a protected resource - MSGraph */
            Log.d(TAG, "Successfully authenticated");
            /* Update UI */
            updateUI(authenticationResult.getAccount());
            /* call graph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
        @Override
        public void onCancel() {
            /* User canceled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

### <a name="getauthsilentcallback"></a>getAuthSilentCallback
Wywołanie zwrotne używane w przypadku żądań niemych 
```java 
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");
            callGraphAPI(authenticationResult);
        }
        @Override
        public void onError(MsalException exception) {
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
    };
}
```

## <a name="call-microsoft-graph-api"></a>Wywoływanie interfejsu API programu Microsoft Graph 

Poniższy kod pokazuje, jak wywołać GraphAPI przy użyciu sdk wykresu. 

### <a name="callgraphapi"></a>fonografaPI 

```java
private void callGraphAPI(IAuthenticationResult authenticationResult) {

    final String accessToken = authenticationResult.getAccessToken();

    IGraphServiceClient graphClient =
            GraphServiceClient
                    .builder()
                    .authenticationProvider(new IAuthenticationProvider() {
                        @Override
                        public void authenticateRequest(IHttpRequest request) {
                            Log.d(TAG, "Authenticating request," + request.getRequestUrl());
                            request.addHeader("Authorization", "Bearer " + accessToken);
                        }
                    })
                    .buildClient();
    graphClient
            .me()
            .drive()
            .buildRequest()
            .get(new ICallback<Drive>() {
                @Override
                public void success(final Drive drive) {
                    Log.d(TAG, "Found Drive " + drive.id);
                    displayGraphResult(drive.getRawObject());
                }

                @Override
                public void failure(ClientException ex) {
                    displayError(ex);
                }
            });
}
```

## <a name="add-ui"></a>Dodawanie interfejsu użytkownika
### <a name="activity"></a>Działanie 
Jeśli chcesz modelować interfejs użytkownika z tego samouczka, poniższe metody zapewniają przewodnik do aktualizowania tekstu i nasłuchiwania przycisków.

#### <a name="updateui"></a>updateUI
Włączanie/wyłączanie przycisków na podstawie stanu logowania i ustawiania tekstu.  
```java 
private void updateUI(@Nullable final IAccount account) {
    if (account != null) {
        signInButton.setEnabled(false);
        signOutButton.setEnabled(true);
        callGraphApiInteractiveButton.setEnabled(true);
        callGraphApiSilentButton.setEnabled(true);
        currentUserTextView.setText(account.getUsername());
    } else {
        signInButton.setEnabled(true);
        signOutButton.setEnabled(false);
        callGraphApiInteractiveButton.setEnabled(false);
        callGraphApiSilentButton.setEnabled(false);
        currentUserTextView.setText("");
        logTextView.setText("");
    }
}
```
#### <a name="displayerror"></a>displayError
```java 
private void displayError(@NonNull final Exception exception) {
       logTextView.setText(exception.toString());
   }
```

#### <a name="displaygraphresult"></a>displayGraphResult

```java
private void displayGraphResult(@NonNull final JsonObject graphResponse) {
      logTextView.setText(graphResponse.toString());
  }
```
#### <a name="performoperationonsignout"></a>performOperationOnSignOut
Metoda aktualizacji tekstu w interfejsie użytkownika w celu odzwierciedlenia wylogowania. 

```java
private void performOperationOnSignOut() {
    final String signOutText = "Signed Out.";
    currentUserTextView.setText("");
    Toast.makeText(getApplicationContext(), signOutText, Toast.LENGTH_SHORT)
            .show();
}
```
### <a name="layout"></a>Układ 

Przykładowy `activity_main.xml` plik do wyświetlania przycisków i pól tekstowych. 

```xml 
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#FFFFFF"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:paddingTop="5dp"
        android:paddingBottom="5dp"
        android:weightSum="10">

        <Button
            android:id="@+id/signIn"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign In"/>

        <Button
            android:id="@+id/clearCache"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign Out"
            android:enabled="false"/>

    </LinearLayout>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:orientation="horizontal">

        <Button
            android:id="@+id/callGraphInteractive"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Interactively"
            android:enabled="false"/>

        <Button
            android:id="@+id/callGraphSilent"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Silently"
            android:enabled="false"/>
    </LinearLayout>

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <TextView
        android:id="@+id/current_user"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Account info goes here..." />

    <TextView
        android:id="@+id/txt_log"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Output goes here..." />
</LinearLayout>
```

## <a name="test-your-app"></a>Testowanie aplikacji

### <a name="run-locally"></a>Uruchamianie lokalnie

Tworzenie i wdrażanie aplikacji na urządzeniu testowym lub emulatorze. Powinieneś mieć możliwość logowania się i uzyskania tokenów dla usługi Azure AD lub osobistych kont Microsoft.

Po zalogowaniu aplikacja wyświetli dane zwrócone z punktu `/me` końcowego programu Microsoft Graph.

### <a name="consent"></a>Zgody

Gdy użytkownik po raz pierwszy zaloguje się do aplikacji, zostanie poproszony przez firmę Microsoft identity o wyrażenie zgody na żądane uprawnienia. Niektórzy dzierżawcy usługi Azure AD mają wyłączono zgodę użytkownika, która wymaga od administratorów zgody w imieniu wszystkich użytkowników. Aby obsługiwać ten scenariusz, należy utworzyć własną dzierżawę lub uzyskać zgodę administratora. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, usuń obiekt aplikacji, który został utworzony w [kroku Zarejestruj aplikację.](#register-your-application)

## <a name="get-help"></a>Uzyskiwanie pomocy

Jeśli masz problemy z tym samouczkiem lub platformą tożsamości firmy Microsoft, odwiedź [stronę Pomocy i pomocy technicznej.](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options)

Pomóż nam ulepszyć platformę tożsamości firmy Microsoft. Powiedz nam, co myślisz, wypełniając krótką ankietę z dwoma pytaniami.

> [!div class="nextstepaction"]
> [Ankieta na temat platformy tożsamości firmy Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)

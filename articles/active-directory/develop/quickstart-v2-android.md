---
title: Platforma tożsamości firmy Microsoft Szybki start systemu Android | Azure
description: Dowiedz się, jak aplikacje systemu Android mogą wywoływać interfejs API, który wymaga tokenów dostępu przez punkt końcowy platformy tożsamości firmy Microsoft.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/15/2019
ms.author: marsma
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:Android
ms.openlocfilehash: c9e6c6a3eac3193ca503a23d523b78881f6a6738
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473821"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Szybki start: logowanie użytkowników i wywoływanie interfejsu API programu Microsoft Graph z poziomu aplikacji dla systemu Android

Ten przewodnik Szybki start używa przykładu kodu, aby zademonstrować, jak aplikacja systemu Android może logować się do kont osobistych, służbowych lub szkolnych przy użyciu platformy tożsamości firmy Microsoft, a następnie uzyskać token dostępu i wywołać interfejs API programu Microsoft Graph. (Zobacz [Jak działa przykład](#how-the-sample-works) dla ilustracji).

Aplikacje muszą być reprezentowane przez obiekt aplikacji w usłudze Azure Active Directory, aby platforma tożsamości firmy Microsoft mogła dostarczać tokeny do aplikacji.

> [!div renderon="docs"]
> Dla wygody przykładowy kod jest `redirect_uri` dostarczany z domyślną wstępnie skonfigurowaną w `AndroidManifest.xml` pliku, dzięki czemu nie trzeba najpierw zarejestrować własnego obiektu aplikacji. A `redirect_uri` jest częściowo oparty na kluczu podpisywania aplikacji. Przykładowy projekt jest wstępnie skonfigurowany z kluczem `redirect_uri` podpisywania, dzięki czemu pod warunkiem będzie działać. Aby dowiedzieć się więcej na temat rejestrowania obiektu aplikacji i integrowania go z [aplikacją, zobacz Logowania użytkowników i wywołać microsoft graph z samouczka aplikacji systemu Android.](tutorial-v2-android.md)


> [!NOTE]
> **Wymagania wstępne**
> * Android Studio 
> * Android 16+

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1. Konfigurowanie aplikacji w witrynie Azure Portal 
>  Aby przykładowy kod dla tego szybkiego startu do pracy, należy dodać identyfikator URI przekierowania zgodne z brokera Auth.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź zmiany automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Already configured](media/quickstart-v2-android/green-check.png) (Już skonfigurowano) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów
>
> ### <a name="step-2-download-the-project"></a>Krok 2. Pobieranie projektu 
> [!div class="sxs-lookup" renderon="portal"]
> Uruchom projekt za pomocą Android Studio.
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Pobierz przykładowy kod](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip)
>
> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Krok 3: Aplikacja jest skonfigurowana i gotowa do uruchomienia
> Skonfigurowaliśmy projekt z wartościami właściwości aplikacji i jest on gotowy do uruchomienia. 
> Przykładowa aplikacja zostanie uruchomiony na ekranie **Tryb pojedynczego konta.** Domyślny zakres, **user.read,** jest dostarczany domyślnie, który jest używany podczas odczytywania własnych danych profilu podczas wywołania interfejsu API programu Microsoft Graph. Adres URL wywołania interfejsu API programu Microsoft Graph jest domyślnie podany. Możesz zmienić oba te elementy, jeśli chcesz.
>
> ![Przykładowa aplikacja MSAL przedstawiająca użycie pojedynczego i wielu kont](./media/quickstart-v2-android/quickstart-sample-app.png)
>
> Użyj menu aplikacji, aby zmienić tryby jednego i wielu kont.
>
> W trybie pojedynczego konta zaloguj się przy użyciu konta służbowego lub domowego:
>
> 1. Wybierz **opcję Pobierz dane wykresu interaktywnie,** aby monitować użytkownika o ich poświadczenia. Zobaczysz dane wyjściowe z wywołania do interfejsu API programu Microsoft Graph w dolnej części ekranu.
> 2. Po zalogowaniu wybierz **pozycję Pobierz dane wykresu w trybie dyskretnym,** aby nawiązać połączenie z interfejsem API programu Microsoft Graph bez ponownego monitowania użytkownika o poświadczenia. Zobaczysz dane wyjściowe z wywołania do interfejsu API programu Microsoft Graph w dolnej części ekranu.
>
> W trybie wielu kont można powtórzyć te same kroki.  Ponadto można usunąć konto zalogowane, które również usuwa tokeny buforowane dla tego konta.

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> ## <a name="step-1-get-the-sample-app"></a>Krok 1: Pobierz przykładową aplikację
>
> [Pobierz kod](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip).
>
> ## <a name="step-2-run-the-sample-app"></a>Krok 2: Uruchom przykładową aplikację
>
> Wybierz emulator lub urządzenie fizyczne z listy rozwijanej **dostępnych urządzeń** Android Studio i uruchom aplikację.
>
> Przykładowa aplikacja zostanie uruchomiony na ekranie **Tryb pojedynczego konta.** Domyślny zakres, **user.read,** jest dostarczany domyślnie, który jest używany podczas odczytywania własnych danych profilu podczas wywołania interfejsu API programu Microsoft Graph. Adres URL wywołania interfejsu API programu Microsoft Graph jest domyślnie podany. Możesz zmienić oba te elementy, jeśli chcesz.
>
> ![Przykładowa aplikacja MSAL przedstawiająca użycie pojedynczego i wielu kont](./media/quickstart-v2-android/quickstart-sample-app.png)
>
> Użyj menu aplikacji, aby zmienić tryby jednego i wielu kont.
>
> W trybie pojedynczego konta zaloguj się przy użyciu konta służbowego lub domowego:
>
> 1. Wybierz **opcję Pobierz dane wykresu interaktywnie,** aby monitować użytkownika o ich poświadczenia. Zobaczysz dane wyjściowe z wywołania do interfejsu API programu Microsoft Graph w dolnej części ekranu.
> 2. Po zalogowaniu wybierz **pozycję Pobierz dane wykresu w trybie dyskretnym,** aby nawiązać połączenie z interfejsem API programu Microsoft Graph bez ponownego monitowania użytkownika o poświadczenia. Zobaczysz dane wyjściowe z wywołania do interfejsu API programu Microsoft Graph w dolnej części ekranu.
>
> W trybie wielu kont można powtórzyć te same kroki.  Ponadto można usunąć konto zalogowane, które również usuwa tokeny buforowane dla tego konta.

## <a name="how-the-sample-works"></a>Jak działa próbka
![Zrzut ekranu przedstawiający przykładową aplikację](media/quickstart-v2-android/android-intro.svg)


Kod jest zorganizowany na fragmenty, które pokazują, jak napisać jedną i wiele kont aplikacji MSAL. Pliki kodu są zorganizowane w następujący sposób:

| Plik  | Demonstracje  |
|---------|---------|
| MainAktywność | Zarządza interfejsem użytkownika |
| MSGraphRequestWrapper (MSGraphRequestWrapper)  | Wywołuje interfejs API programu Microsoft Graph przy użyciu tokenu dostarczonego przez usługę MSAL |
| MultipleAccountModeFragment  | Inicjuje aplikację z wieloma kontami, ładuje konto użytkownika i pobiera token do wywołania interfejsu API programu Microsoft Graph |
| SingleAccountModeFragment | Inicjuje aplikację z jednym kontem, ładuje konto użytkownika i pobiera token do wywołania interfejsu API programu Microsoft Graph |
| res/auth_config_multiple_account.json  | Plik konfiguracji wielu kont |
| res/auth_config_single_account.json  | Plik konfiguracji pojedynczego konta |
| Gradle Scripts/build.grade (moduł:aplikacja) | Zależności biblioteki MSAL są dodawane tutaj |

Teraz przyjrzymy się tym plikom bardziej szczegółowo i wywołamy kod specyficzny dla MSAL w każdym z nich.

### <a name="adding-msal-to-the-app"></a>Dodawanie usługi MSAL do aplikacji

MSAL ([com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) to biblioteka używana do logowania użytkowników i żądania tokenów używanych do uzyskiwania dostępu do interfejsu API chronionego przez platformę tożsamości firmy Microsoft. Gradle 3.0+ instaluje bibliotekę po dodaniu następujących do **gradle scripts** > **build.gradle (moduł: aplikacja)** w obszarze **Zależności:**

```gradle  
implementation 'com.microsoft.identity.client:msal:1.+'
```

Możesz to zobaczyć w przykładowym projekcie w build.gradle (Moduł: aplikacja):

```java
dependencies {
    ...
    implementation 'com.microsoft.identity.client:msal:1.+'
    ...
}
```

To nakazuje Gradle pobrać i zbudować MSAL z maven central.

### <a name="msal-imports"></a>Import MSAL

Importy, które są istotne dla `com.microsoft.identity.client.*`biblioteki MSAL są .  Na przykład zobaczysz, `import com.microsoft.identity.client.PublicClientApplication;` który jest obszar nazw `PublicClientApplication` dla klasy, która reprezentuje aplikację klienta publicznego.

### <a name="singleaccountmodefragmentjava"></a>SingleAccountModeFragment.java

Ten plik pokazuje, jak utworzyć aplikację MSAL jednego konta i wywołać interfejs API programu Microsoft Graph.

Aplikacje z jednym kontem są używane tylko przez jednego użytkownika.  Na przykład możesz mieć tylko jedno konto, za pomocą którego logujesz się do aplikacji mapowania.

#### <a name="single-account-msal-initialization"></a>Inicjowanie msal jednokonto

W `auth_config_single_account.json`polu `onCreateView()`pojedyncze `PublicClientApplication` konto jest tworzone przy użyciu `auth_config_single_account.json` informacji o konfiguracji przechowywanych w pliku.  W ten sposób zainicjować bibliotekę MSAL do użycia w aplikacji MSAL z jednym kontem:

```java
...
// Creates a PublicClientApplication object with res/raw/auth_config_single_account.json
PublicClientApplication.createSingleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_single_account,
        new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(ISingleAccountPublicClientApplication application) {
                /**
                 * This test app assumes that the app is only going to support one account.
                 * This requires "account_mode" : "SINGLE" in the config json file.
                 **/
                mSingleAccountApp = application;
                loadAccount();
            }

            @Override
            public void onError(MsalException exception) {
                displayError(exception);
            }
        });
```

#### <a name="sign-in-a-user"></a>Logowanie użytkownika

W `SingleAccountModeFragment.java`programie ,kod do logowania `initializeUI()`użytkownika znajduje `signInButton` się w programie obsługi kliknięć.

Wywołanie `signIn()` przed próbą uzyskania tokenów. `signIn()`zachowuje się `acquireToken()` tak, jakby jest wywoływana, co powoduje interaktywny monit dla użytkownika, aby zalogować się.

Logowanie użytkownika jest operacją asynchroniką. Wywołanie zwrotne jest przekazywane, który wywołuje interfejs API programu Microsoft Graph i zaktualizować interfejs użytkownika po zalogowaniu się użytkownika:

```java
mSingleAccountApp.signIn(getActivity(), null, getScopes(), getAuthInteractiveCallback());
```

#### <a name="sign-out-a-user"></a>Wyloguj się z użytkownika

W `SingleAccountModeFragment.java`programie ,kod do wylogowywania się użytkownika znajduje się w `initializeUI()`programie obsługi `signOutButton` kliknięć.  Podpisywanie użytkownika jest operacją asynchroniką. Wylogowywanie użytkownika również czyści pamięć podręczną tokenu dla tego konta. Wywołanie zwrotne jest tworzone w celu zaktualizowania interfejsu użytkownika po wylogowywania konta użytkownika:

```java
mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
    @Override
    public void onSignOut() {
        updateUI(null);
        performOperationOnSignOut();
    }

    @Override
    public void onError(@NonNull MsalException exception) {
        displayError(exception);
    }
});
```

#### <a name="get-a-token-interactively-or-silently"></a>Uzyskaj token interaktywnie lub cicho

Aby przedstawić najmniejszą liczbę monitów do użytkownika, zazwyczaj otrzymasz token po cichu. Następnie, jeśli występuje błąd, spróbuj uzyskać do tokenu interaktywnie. Po raz pierwszy `signIn()`aplikacja wywołuje , skutecznie `acquireToken()`działa jako wywołanie , który poprosi użytkownika o poświadczenia.

W niektórych sytuacjach, gdy użytkownik może zostać poproszony o wybranie konta, wprowadzenie poświadczeń lub wyrażenie zgody na uprawnienia żądane przez aplikację, to:

* Przy pierwszym logowaniu się użytkownika do aplikacji
* Jeśli użytkownik zresetuje swoje hasło, musi wprowadzić swoje poświadczenia
* Jeśli zgoda zostanie cofnięta
* Jeśli aplikacja wyraźnie wymaga zgody
* Gdy aplikacja żąda dostępu do zasobu po raz pierwszy
* Gdy wymagane są zasady usługi mfa lub inne zasady dostępu warunkowego

Kod, aby uzyskać token interaktywnie, czyli z interfejsu użytkownika, `SingleAccountModeFragment.java`który `initializeUI()`będzie obejmować `callGraphApiInteractiveButton` użytkownika, jest w , w , w programie obsługi kliknięć:

```java
/**
 * If acquireTokenSilent() returns an error that requires an interaction (MsalUiRequiredException),
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your Single Sign-On refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 **/
mSingleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

Jeśli użytkownik już się `acquireTokenSilentAsync()` zalogował, umożliwia aplikacjom żądanie `initializeUI()`tokenów `callGraphApiSilentButton` w trybie cichym, jak pokazano w programie , w programie obsługi kliknięć:

```java
/**
 * Once you've signed the user in,
 * you can perform acquireTokenSilent to obtain resources without interrupting the user.
 **/
  mSingleAccountApp.acquireTokenSilentAsync(getScopes(), AUTHORITY, getAuthSilentCallback());
```

#### <a name="load-an-account"></a>Załaduj konto

Kod do załadowania konta `SingleAccountModeFragment.java` `loadAccount()`znajduje się w pliku .  Ładowanie konta użytkownika jest operacją asynchronicznego, więc wywołania zwrotne do obsługi, gdy konto ładuje się, zmienia lub wystąpi błąd jest przekazywany do msal.  Poniższy kod obsługuje `onAccountChanged()`również , który występuje, gdy konto jest usuwane, użytkownik zmienia się na inne konto i tak dalej.

```java
private void loadAccount() {
    ...

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
```

#### <a name="call-microsoft-graph"></a>Zadzwoń do programu Microsoft Graph

Gdy użytkownik jest zalogowany, wywołanie programu Microsoft Graph `callGraphAPI()` jest następuje `SingleAccountModeFragment.java`za pośrednictwem żądania HTTP, które jest zdefiniowane w programie . Ta funkcja jest otoka, która upraszcza próbki, wykonując niektóre zadania, takie jak pobieranie tokenu dostępu z `authenticationResult` i pakowania wywołania do MSGraphRequestWrapper i wyświetlanie wyników wywołania.

```java
private void callGraphAPI(final IAuthenticationResult authenticationResult) {
    MSGraphRequestWrapper.callGraphAPIUsingVolley(
            getContext(),
            graphResourceTextView.getText().toString(),
            authenticationResult.getAccessToken(),
            new Response.Listener<JSONObject>() {
                @Override
                public void onResponse(JSONObject response) {
                    /* Successfully called graph, process data and send to UI */
                    ...
                }
            },
            new Response.ErrorListener() {
                @Override
                public void onErrorResponse(VolleyError error) {
                    ...
                }
            });
}
```

### <a name="auth_config_single_accountjson"></a>auth_config_single_account.json

Jest to plik konfiguracyjny aplikacji MSAL, która używa jednego konta.

Zobacz [Opis pliku konfiguracji MSAL systemu Android,](msal-configuration.md) aby uzyskać wyjaśnienie tych pól.

Zwróć uwagę `"account_mode" : "SINGLE"`na obecność , który konfiguruje tę aplikację do korzystania z jednego konta.

`"client_id"`jest wstępnie skonfigurowany do używania rejestracji obiektu aplikacji, którą przechowuje firma Microsoft.
`"redirect_uri"`jest wstępnie skonfigurowany do używania klucza podpisywania dostarczonego z przykładowym kodem.

```json
{
  "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "account_mode" : "SINGLE",
  "broker_redirect_uri_registered": true,
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

### <a name="multipleaccountmodefragmentjava"></a>MultipleAccountModeFragment.java

Ten plik pokazuje, jak utworzyć aplikację MSAL wielu kont i wywołać interfejs API programu Microsoft Graph.

Przykładem aplikacji z wieloma kontami jest aplikacja poczty, która umożliwia pracę z wieloma kontami użytkowników, takimi jak konto służbowe i konto osobiste.

#### <a name="multiple-account-msal-initialization"></a>Inicjowanie msal wielu kont

W `MultipleAccountModeFragment.java` pliku, `onCreateView()`w , obiekt aplikacji`IMultipleAccountPublicClientApplication`wielu kont ( ) jest `auth_config_multiple_account.json file`tworzony przy użyciu informacji konfiguracyjnej przechowywanych w :

```java
// Creates a PublicClientApplication object with res/raw/auth_config_multiple_account.json
PublicClientApplication.createMultipleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_multiple_account,
        new IPublicClientApplication.IMultipleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(IMultipleAccountPublicClientApplication application) {
                mMultipleAccountApp = application;
                loadAccounts();
            }

            @Override
            public void onError(MsalException exception) {
                ...
            }
        });
```

Utworzony `MultipleAccountPublicClientApplication` obiekt jest przechowywany w zmiennej członkowskiej klasy, dzięki czemu może służyć do interakcji z biblioteką MSAL do uzyskiwania tokenów i ładowania i usuwania konta użytkownika.

#### <a name="load-an-account"></a>Załaduj konto

Wiele aplikacji konta `getAccounts()` zwykle wywołać, aby wybrać konto do użycia dla operacji MSAL. Kod do załadowania konta `MultipleAccountModeFragment.java` znajduje się `loadAccounts()`w pliku w pliku .  Ładowanie konta użytkownika jest operacją asynchronicznego. Tak więc wywołanie zwrotne obsługuje sytuacje, gdy konto jest ładowane, zmiany lub występuje błąd.

```java
/**
 * Load currently signed-in accounts, if there's any.
 **/
private void loadAccounts() {
    if (mMultipleAccountApp == null) {
        return;
    }

    mMultipleAccountApp.getAccounts(new IPublicClientApplication.LoadAccountsCallback() {
        @Override
        public void onTaskCompleted(final List<IAccount> result) {
            // You can use the account data to update your UI or your app database.
            accountList = result;
            updateUI(accountList);
        }

        @Override
        public void onError(MsalException exception) {
            displayError(exception);
        }
    });
}
```

#### <a name="get-a-token-interactively-or-silently"></a>Uzyskaj token interaktywnie lub cicho

W niektórych sytuacjach, gdy użytkownik może zostać poproszony o wybranie konta, wprowadzenie poświadczeń lub wyrażenie zgody na uprawnienia żądane przez aplikację, to:

* Gdy nowi użytkownicy logują się do aplikacji po raz pierwszy.
* Jeśli użytkownik zresetuje swoje hasło, musi wprowadzić swoje poświadczenia 
* Jeśli zgoda zostanie cofnięta 
* Jeśli aplikacja wyraźnie wymaga zgody 
* Gdy aplikacja żąda dostępu do zasobu po raz pierwszy
* Gdy wymagane są zasady usługi mfa lub inne zasady dostępu warunkowego

Wiele aplikacji konta zazwyczaj pobiera tokeny interaktywnie, czyli z interfejsem użytkownika, `acquireToken()`który obejmuje użytkownika, z wywołaniem .  Kod, aby uzyskać token interaktywnie `MultipleAccountModeFragment.java` znajduje `initializeUI()`się `callGraphApiInteractiveButton` w pliku w , w programie obsługi kliknięć:

```java
/**
 * Acquire token interactively. It will also create an account object for the silent call as a result (to be obtained by getAccount()).
 *
 * If acquireTokenSilent() returns an error that requires an interaction,
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your SSO refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 **/
mMultipleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

Aplikacje nie powinny wymagać od użytkownika logowania za każdym razem, gdy żąda tokenu. Jeśli użytkownik już się `acquireTokenSilentAsync()` zalogował, umożliwia aplikacjom żądanie tokenów bez `MultipleAccountModeFragment.java` monitowania`initializeUI()` użytkownika, jak pokazano w pliku, w programie obsługi `callGraphApiSilentButton` kliknięć:

```java
/**
 * Performs acquireToken without interrupting the user.
 *
 * This requires an account object of the account you're obtaining a token for.
 * (can be obtained via getAccount()).
 */
mMultipleAccountApp.acquireTokenSilentAsync(getScopes(),
    accountList.get(accountListSpinner.getSelectedItemPosition()),
    AUTHORITY,
    getAuthSilentCallback());
```

#### <a name="remove-an-account"></a>Usuwanie konta

Kod do usunięcia konta i wszystkie tokeny buforowane dla `MultipleAccountModeFragment.java` konta, `initializeUI()` znajduje się w pliku w programie obsługi przycisku usuń konto. Przed usunięciem konta potrzebny jest obiekt konta, który można uzyskać `getAccounts()` `acquireToken()`z metod MSAL, takich jak i . Ponieważ usunięcie konta jest operacją asynchronicznego, wywołanie `onRemoved` zwrotne jest dostarczane w celu zaktualizowania interfejsu użytkownika.

```java
/**
 * Removes the selected account and cached tokens from this app (or device, if the device is in shared mode).
 **/
mMultipleAccountApp.removeAccount(accountList.get(accountListSpinner.getSelectedItemPosition()),
        new IMultipleAccountPublicClientApplication.RemoveAccountCallback() {
            @Override
            public void onRemoved() {
                ...
                /* Reload account asynchronously to get the up-to-date list. */
                loadAccounts();
            }

            @Override
            public void onError(@NonNull MsalException exception) {
                displayError(exception);
            }
        });
```

### <a name="auth_config_multiple_accountjson"></a>auth_config_multiple_account.json

Jest to plik konfiguracyjny aplikacji MSAL, która używa wielu kont.

Zobacz [Zrozumieć plik konfiguracji MSAL systemu Android,](msal-configuration.md) aby uzyskać wyjaśnienie różnych pól.

W przeciwieństwie do pliku konfiguracyjnego [auth_config_single_account.json,](#auth_config_single_accountjson) ten plik konfiguracyjny ma `"account_mode" : "MULTIPLE"` zamiast, ponieważ jest to aplikacja wielokrotnego `"account_mode" : "SINGLE"` konta.

`"client_id"`jest wstępnie skonfigurowany do używania rejestracji obiektu aplikacji, którą przechowuje firma Microsoft.
`"redirect_uri"`jest wstępnie skonfigurowany do używania klucza podpisywania dostarczonego z przykładowym kodem.

```json
{
  "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "account_mode" : "MULTIPLE",
  "broker_redirect_uri_registered": true,
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

## <a name="next-steps"></a>Następne kroki

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Dowiedz się więcej na temat czynności, które są wymagane to utworzenia aplikacji użytej w tym przewodniku Szybki start

Wypróbuj [logowania użytkowników i wywołać microsoft graph z samouczka aplikacji systemu Android](tutorial-v2-android.md) dla przewodnika krok po kroku do tworzenia aplikacji systemu Android, który pobiera token dostępu i używa go do wywołania interfejsu API programu Microsoft Graph.

> [!div class="nextstepaction"]
> [Samouczek: wywołanie interfejsu API programu Graph dla systemu Android](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-android)

### <a name="msal-for-android-library-wiki"></a>Strona typu wiki: biblioteka MSAL dla systemu Android

Przeczytaj więcej informacji na temat biblioteki MSAL dla systemu Android:

> [!div class="nextstepaction"]
> [Strona typu wiki: biblioteka MSAL dla systemu Android](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Pomóż nam ulepszyć platformę tożsamości firmy Microsoft. Powiedz nam, co myślisz, wypełniając krótką ankietę z dwoma pytaniami.

> [!div class="nextstepaction"]
> [Ankieta na temat platformy tożsamości firmy Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)

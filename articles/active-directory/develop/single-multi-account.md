---
title: Publiczne aplikacje klienckie z jednym i wieloma kontami | Azure
description: Omówienie publicznych aplikacji klienckich jedno- i wielu kont.
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/26/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: 89a383aabf3487a0938604bc28ddb06c0541d13e
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881354"
---
# <a name="single-and-multiple-account-public-client-apps"></a>Publiczne aplikacje klienckie z jednym i wieloma kontami

Ten artykuł pomoże Ci zrozumieć typy używane w publicznych aplikacjach klienckich jedno- i wielu kont, z naciskiem na publiczne aplikacje klienckie z jednym kontem. 

Biblioteka uwierzytelniania usługi Azure Active Directory (ADAL) modeluje serwer.  Zamiast tego biblioteka uwierzytelniania firmy Microsoft (MSAL) modeluje aplikację kliencką.  Większość aplikacji na Androida jest uważana za klientów publicznych. Klient publiczny to aplikacja, która nie może bezpiecznie zachować tajemnicy.  

MSAL specjalizuje się w `PublicClientApplication` powierzchni interfejsu API, aby uprościć i wyjaśnić środowisko programistyczne dla aplikacji, które umożliwiają użycie tylko jednego konta w czasie. `PublicClientApplication`jest podklasyfikowany przez `SingleAccountPublicClientApplication` i `MultipleAccountPublicClientApplication`.  Na poniższym diagramie przedstawiono relację między tymi klasami.

![Diagram klasy UML aplikacji SingleAccountPublicClientApplication](./media/single-multi-account/single-and-multiple-account.png)

## <a name="single-account-public-client-application"></a>Publiczna aplikacja klienta pojedynczego konta

Klasa `SingleAccountPublicClientApplication` umożliwia utworzenie aplikacji opartej na msal, która umożliwia tylko jedno konto, które mają być zalogowane w czasie. `SingleAccountPublicClientApplication`różni się `PublicClientApplication` w następujący sposób:

- MSAL śledzi aktualnie zalogowane konto.
  - Jeśli aplikacja korzysta z brokera (domyślnie podczas rejestracji aplikacji portalu Azure) i jest zainstalowany na urządzeniu, na którym znajduje się broker, MSAL sprawdzi, czy konto jest nadal dostępne na urządzeniu.
- `signIn`umożliwia logowanie się do konta jawnie i oddzielnie od żądania zakresów.
- `acquireTokenSilent`nie wymaga parametru konta.  Jeśli podasz konto, a konto, które podasz, nie będzie zgodne z `MsalClientException` bieżącym kontem śledzonym przez msal, zostanie ono odrzucone.
- `acquireToken`nie zezwala użytkownikowi na przełączanie kont. Jeśli użytkownik próbuje przełączyć się na inne konto, zostanie zgłoszony wyjątek.
- `getCurrentAccount`zwraca obiekt wynikowy, który zapewnia następujące funkcje:
  - Wartość logiczna wskazująca, czy konto uległo zmianie. Konto może zostać zmienione w wyniku usunięcia z urządzenia, na przykład.
  - Poprzednie konto. Jest to przydatne, jeśli trzeba zrobić wszelkie lokalne oczyszczanie danych, gdy konto jest usuwane z urządzenia lub gdy nowe konto jest zalogowany.
  - CurrentAccount.
- `signOut`usuwa wszystkie tokeny skojarzone z klientem z urządzenia.  

Gdy broker uwierzytelniania systemu Android, taki jak Microsoft Authenticator lub Portal firmy usługi Intune, jest zainstalowany na urządzeniu, a aplikacja jest skonfigurowana do korzystania z brokera, `signOut` nie usunie konta z urządzenia.

## <a name="single-account-scenario"></a>Scenariusz pojedynczego konta

Poniższy pseudo kod `SingleAccountPublicClientApplication`ilustruje użycie .

```java
// Construct Single Account Public Client Application
ISingleAccountPublicClientApplication app = PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will get a UI prompt before getting the token.
app.signIn(getActivity(), scopes, new AuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
    }
);

// Load Account Specific Data
getDataForAccount(account);

// Get Current Account
ICurrentAccountResult currentAccountResult = app.getCurrentAccount();
if (currentAccountResult.didAccountChange())
{
    // Account Changed Clear existing account data
    clearDataForAccount(currentAccountResult.getPriorAccount());
    mAccount = currentAccountResult.getCurrentAccount();
    if (account != null)
    {
        //load data for new account
        getDataForAccount(account);
    }
}

// Sign out
if (app.signOut())
{
    clearDataForAccount(mAccount);
    mAccount = null;
}
```

## <a name="multiple-account-public-client-application"></a>Aplikacja klienta publicznego wielu kont

Klasa `MultipleAccountPublicClientApplication` jest używana do tworzenia aplikacji opartych na msal, które umożliwiają logowanie wielu kont w tym samym czasie. To pozwala na uzyskanie, dodanie i usunięcie kont w następujący sposób:

### <a name="add-an-account"></a>Dodaj konto

Użyj jednego lub więcej kont `acquireToken` w aplikacji, dzwoniąc co najmniej jeden razy.  

### <a name="get-accounts"></a>Pobierz konta

- Zadzwoń, `getAccount` aby uzyskać określone konto.
- Zadzwoń, `getAccounts`aby uzyskać listę kont aktualnie znanych aplikacji.

Aplikacja nie będzie mogła wyliczyć wszystkich kont platformy tożsamości firmy Microsoft na urządzeniu znanym aplikacji brokera. Można wyliczyć tylko konta, które zostały użyte przez aplikację.  Konta, które zostały usunięte z urządzenia, nie zostaną zwrócone przez te funkcje.

### <a name="remove-an-account"></a>Usuwanie konta

Usuń konto, `removeAccount` dzwoniąc z identyfikatorem konta.

Jeśli aplikacja jest skonfigurowana do korzystania z brokera, a broker jest zainstalowany na urządzeniu, konto `removeAccount`nie zostanie usunięte z brokera podczas wywoływania .  Usuwane są tylko tokeny skojarzone z klientem.

## <a name="multiple-account-scenario"></a>Scenariusz wielu kont

Poniższy pseudokod pokazuje, jak utworzyć aplikację wielu kont, listy kont na urządzeniu i uzyskać tokeny.

```java
// Construct Multiple Account Public Client Application
IMultipleAccountPublicClientApplication app = PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will be required to interact with a UI to obtain a token
app.acquireToken(getActivity(), scopes, new AuthenticationCallback()
 {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
 });

...

// Get the default authority
String authority = app.getConfiguration().getDefaultAuthority().getAuthorityURL().toString();

// Get a list of accounts on the device
List<IAccount> accounts = app.getAccounts();

// Pick an account to obtain a token from without prompting the user to sign in
IAccount selectedAccount = accounts.get(0);

// Get a token without prompting the user
app.acquireTokenSilentAsync(scopes, selectedAccount, authority, new SilentAuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }
});
```

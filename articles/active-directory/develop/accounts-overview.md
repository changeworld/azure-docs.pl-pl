---
title: Konta platformy tożsamości firmy Microsoft & profilach dzierżaw w systemie Android | Azure
description: Omówienie kont platformy tożsamości firmy Microsoft dla systemu Android
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.devlang: java
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: d0497ad68e7b29e6d8c83dd860ba8f509e229579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77611894"
---
# <a name="accounts--tenant-profiles-android"></a>Konta i profile dzierżaw (Android)

Ten artykuł zawiera omówienie `account` tego, co znajduje się na platformie tożsamości firmy Microsoft.

Interfejs API biblioteki uwierzytelniania firmy Microsoft (MSAL) zastępuje termin *użytkownik* kontem *terminowym*. Jednym z powodów jest to, że użytkownik (człowiek lub agent oprogramowania) może mieć lub może korzystać z wielu kont. Konta te mogą znajdować się w organizacji użytkownika i/lub w innych organizacjach, których użytkownik jest członkiem.

Konto na platformie tożsamości firmy Microsoft składa się z:

- Unikatowy identyfikator.  
- Co najmniej jedno poświadczenia używane do wykazania własności/kontroli konta.
- Jeden lub więcej profili składających się z atrybutów, takich jak:
  - Zdjęcie, Imię i nazwisko, nazwisko, tytuł, lokalizacja biura
- Konto ma źródło uprawnień lub system ewidów. Jest to system, w którym konto jest tworzone i gdzie poświadczenia skojarzone z tym kontem są przechowywane. W systemach wielodostępnych, takich jak platforma `tenant` tożsamości firmy Microsoft, system rejestrowania jest miejscem, w którym utworzono konto. Ten dzierżawca jest `home tenant`również określany jako .
- Konta na platformie tożsamości firmy Microsoft mają następujące systemy:
  - Usługa Azure Active Directory, w tym usługa Azure Active Directory B2C.
  - Konto Microsoft (Live).
- Konta z systemów rejestrowych poza platformą tożsamości firmy Microsoft są reprezentowane na platformie tożsamości firmy Microsoft, w tym:
  - tożsamości z połączonych katalogów lokalnych (Windows Server Active Directory)
  - tożsamości zewnętrznych z LinkedIn, GitHub i tak dalej.
  W takich przypadkach konto ma zarówno system pochodzenia rekordów, jak i system rejestrowania na platformie tożsamości firmy Microsoft.
- Platforma tożsamości firmy Microsoft umożliwia jedno konto, które ma służyć do uzyskiwania dostępu do zasobów należących do wielu organizacji (dzierżawy usługi Azure Active Directory).
  - Aby zarejestrować, że konto z jednego systemu rekordów (dzierżawy AAD A) ma dostęp do zasobu w innym systemie rekordów (dzierżawy AAD B), konto musi być reprezentowane w dzierżawie, w której zasób jest zdefiniowany. Odbywa się to poprzez utworzenie lokalnego rekordu konta z systemu A w systemie B.
  - Ten rekord lokalny, czyli reprezentacja konta, jest powiązany z oryginalnym kontem.
  - MSAL udostępnia ten rekord `Tenant Profile`lokalny jako .
  - Profil dzierżawy może mieć różne atrybuty, które są odpowiednie dla kontekstu lokalnego, takie jak tytuł stanowiska, lokalizacja biura, informacje kontaktowe itp.
- Ponieważ konto może znajdować się w co najmniej jednej dzierżawie, konto może mieć więcej niż jeden profil.

> [!NOTE]
> Msal traktuje system kont Microsoft (Live, MSA) jako inną dzierżawę na platformie tożsamości firmy Microsoft. Identyfikator dzierżawy dzierżawy konta Microsoft jest:`9188040d-6c67-4c5b-b112-36a304b66dad`

## <a name="account-overview-diagram"></a>Diagram przeglądu konta

![Diagram przeglądu konta](./media/accounts-overview/accounts-overview.svg)

Na powyższym diagramie:

- Konto `bob@contoso.com` jest tworzone w lokalnej usłudze Windows Server Active Directory (pochodzenia lokalnego systemu rekordów).
- Konto `tom@live.com` jest tworzone w dzierżawie konta Microsoft.
- `bob@contoso.com`ma dostęp do co najmniej jednego zasobu w następujących dzierżawach usługi Azure Active Directory:
  - contoso.com (system rejestrowania w chmurze — połączony z lokalnym systemem rejestrów)
  - fabrikam.com
  - woodgrovebank.com
  - Profil dzierżawy `bob@contoso.com` istnieje w każdej z tych dzierżaw.
- `tom@live.com`ma dostęp do zasobów w następujących dzierżawach firmy Microsoft:
  - contoso.com
  - fabrikam.com
  - Profil dzierżawy `tom@live.com` istnieje w każdej z tych dzierżaw.
- Informacje o Tom i Bob w innych dzierżawców mogą się różnić od tych w systemie rekordów. Mogą się różnić według atrybutów, takich jak stanowisko, lokalizacja pakietu office i tak dalej. Mogą to być członkowie grup i/lub ról w każdej organizacji (dzierżawa usługi Azure Active Directory). Odnosimy się do bob@contoso.com tych informacji jako profil dzierżawy.

Na diagramie bob@contoso.com tom@live.com i mieć dostęp do zasobów w różnych dzierżaw usługi Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Dodawanie użytkowników współpracy usługi Azure Active Directory B2B w witrynie Azure portal](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

## <a name="accounts-and-single-sign-on-sso"></a>Konta i logowanie jednokrotne (logowanie jednokrotne)

Pamięć podręczna tokenu MSAL przechowuje *pojedynczy token odświeżania* na konto. Ten token odświeżania może służyć do dyskretnego żądania tokenów dostępu od wielu dzierżaw platformy tożsamości firmy Microsoft. Gdy broker jest zainstalowany na urządzeniu, konto jest zarządzane przez brokera i logowania jednokrotnego na całym urządzeniu staje się możliwe.

> [!IMPORTANT]
> Konto Business to Consumer (B2C) i zachowanie tokenu odświeżania różni się od reszty platformy tożsamości firmy Microsoft. Aby uzyskać więcej informacji, zobacz [Zasady B2C & konta .](#b2c-policies--accounts)

## <a name="account-identifiers"></a>Identyfikatory kont

Identyfikator konta MSAL nie jest identyfikatorem obiektu konta. To nie jest przeznaczone do analizowane i / lub polegać na przekazywanie czegokolwiek innego niż unikatowość w ramach platformy tożsamości firmy Microsoft.

Aby uzyskać zgodność z biblioteką uwierzytelniania usługi Azure AD (ADAL) i ułatwić migrację z biblioteki ADAL do usługi MSAL, usługa MSAL może wyszukać konta przy użyciu dowolnego prawidłowego identyfikatora konta dostępnego w pamięci podręcznej MSAL.  Na przykład następujące zawsze pobrać ten sam tom@live.com obiekt konta, ponieważ każdy z identyfikatorów jest prawidłowy:

```java
// The following would always retrieve the same account object for tom@live.com because each identifier is valid

IAccount account = app.getAccount("<tome@live.com msal account id>");
IAccount account = app.getAccount("<tom@live.com contoso user object id>");
IAccount account = app.getAccount("<tom@live.com woodgrovebank user object id>");
```

## <a name="accessing-claims-about-an-account"></a>Uzyskiwanie dostępu do oświadczeń dotyczących konta

Oprócz żądania tokenu dostępu MSAL również zawsze żąda tokenu identyfikatora od każdej dzierżawy. Robi to, zawsze żądając następujących zakresów:

- Openid
- profil

Token identyfikatora zawiera listę oświadczeń. `Claims`są parami nazw/wartości dotyczących konta i są używane do żądania.

Jak wspomniano wcześniej, każda dzierżawa, w której istnieje konto, może przechowywać różne informacje o koncie, w tym między innymi atrybuty, takie jak: stanowisko, lokalizacja biura i tak dalej.

Podczas gdy konto może być członkiem lub gościem w wielu organizacjach, MSAL nie wysyła zapytania do usługi, aby uzyskać listę dzierżawców, których członkiem jest konto. Zamiast tego MSAL tworzy listę dzierżaw, w których znajduje się konto, w wyniku żądań tokenu, które zostały wykonane.

Oświadczenia ujawnione w obiekcie konta są zawsze roszczeniami z "dzierżawy domowej"/{authority} dla konta. Jeśli to konto nie zostało użyte do żądania tokenu dla ich dzierżawy domowej, usługa MSAL nie może dostarczyć oświadczeń za pośrednictwem obiektu konta.  Przykład:

```java
// Psuedo Code
IAccount account = getAccount("accountid");

String username = account.getClaims().get("preferred_username");
String tenantId = account.getClaims().get("tid"); // tenant id
String objectId = account.getClaims().get("oid"); // object id
String issuer = account.getClaims().get("iss"); // The tenant specific authority that issued the id_token
```

> [!TIP]
> Aby wyświetlić listę oświadczeń dostępnych w obiekcie konta, zapoznaj się z [oświadczeniami w id_token](https://docs.microsoft.com/azure/active-directory/develop/id-tokens#claims-in-an-id_token)

> [!TIP]
> Aby uwzględnić dodatkowe oświadczenia w id_token, zapoznaj się z opcjonalną dokumentacją oświadczeń w [obszarze Jak: Podaj opcjonalne oświadczenia do aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)

### <a name="access-tenant-profile-claims"></a>Dostęp do oświadczeń profilu dzierżawy

Aby uzyskać dostęp do oświadczeń dotyczących konta, które pojawiają się w `IMultiTenantAccount`innych dzierżawach, należy najpierw przerzucić obiekt konta na plik . Wszystkie konta mogą być wielodostępne, ale liczba profilów dzierżawy dostępnych za pośrednictwem usługi MSAL jest oparta na tym, na których dzierżawach zażądano tokenów przy użyciu bieżącego konta.  Przykład:

```java
// Psuedo Code
IAccount account = getAccount("accountid");
IMultiTenantAccount multiTenantAccount = (IMultiTenantAccount)account;

multiTenantAccount.getTenantProfiles().get("tenantid for fabrikam").getClaims().get("family_name");
multiTenantAccount.getTenantProfiles().get("tenantid for contoso").getClaims().get("family_name");
```

## <a name="b2c-policies--accounts"></a>Zasady B2C & konta

Tokeny odświeżania konta nie są współużytkowane przez zasady B2C. W rezultacie logowanie jednokrotne przy użyciu tokenów nie jest możliwe. Nie oznacza to, że logowanie jednokrotne nie jest możliwe. Oznacza to, że logowanie jednokrotne musi korzystać z interaktywnego środowiska, w którym plik cookie jest dostępny, aby włączyć logowanie jednokrotne.

Oznacza to również, że w przypadku MSAL, jeśli nabywasz tokeny przy użyciu różnych zasad B2C, są one traktowane jako oddzielne konta — każdy z własnym identyfikatorem. Jeśli chcesz użyć konta, aby zażądać tokenu za pomocą `acquireTokenSilent`, musisz wybrać konto z listy kont, które są zgodne z zasadami, których używasz z żądaniem tokenu. Przykład:

```java
// Get Account For Policy

String policyId = "SignIn";
IAccount signInPolicyAccount = getAccountForPolicyId(app, policyId);

private IAccount getAccountForPolicy(IPublicClientApplication app, String policyId)
{
    List<IAccount> accounts = app.getAccounts();

    foreach(IAccount account : accounts)
   {
        if (account.getClaims().get("tfp").equals(policyId))
        {
            return account;
        }
    }

    return null;
}
```

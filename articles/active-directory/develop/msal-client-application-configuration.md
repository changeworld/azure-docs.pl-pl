---
title: Konfiguracja aplikacji klienta (Microsoft Authentication Library) | Azure
description: Dowiedz się więcej o opcjach konfiguracji dla publicznych klienta i poufne klienta aplikacji w programie Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5e175a8cdd1622add90bd80df63303fe914ab9c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66430812"
---
# <a name="application-configuration-options"></a>Opcje konfiguracji aplikacji

W kodzie inicjowania nowego publicznego lub poufne klienta (lub agenta użytkownika dla MSAL.js) aplikacji w celu uwierzytelniania i uzyskać tokeny. Podczas inicjowania aplikacji klienckiej w Microsoft Authentication Library (MSAL), można ustawić kilka opcji konfiguracji. Te opcje można podzielić na dwie grupy:

- Opcje rejestracji, w tym:
    - [Urząd](#authority) (składające się z dostawcą tożsamości [wystąpienia](#cloud-instance) i zaloguj się [odbiorców](#application-audience) dla aplikacji i prawdopodobnie identyfikator dzierżawy).
    - [Identyfikator klienta](#client-id).
    - [Identyfikator URI przekierowania](#redirect-uri).
    - [klucz tajny klienta](#client-secret) (w przypadku aplikacji poufnych klienta).
- [Opcje rejestrowania](#logging), w tym poziom dziennika, kontrolę nad danymi osobowymi i nazwę składnika za pomocą biblioteki.

## <a name="authority"></a>Urząd
Organ ma adres URL, który określa katalog, w jakiej biblioteki MSAL może żądać tokenów z. Typowe są:

- https://login.microsoftonline.com/&lt ; dzierżawy&gt; /, gdzie &lt; dzierżawy&gt; jest identyfikator dzierżawy w domenie skojarzone z tym dzierżawcą usługi Azure AD lub dzierżawy usługi Azure Active Directory (Azure AD). Używana tylko do logowania użytkowników konkretnej organizacji.
- https://login.microsoftonline.com/common/. Umożliwia logowanie użytkowników mających roboczego i konta służbowe lub osobiste konta Microsoft.
- https://login.microsoftonline.com/organizations/. Używane do logowania użytkowników przy użyciu kont służbowych.
- https://login.microsoftonline.com/consumers/. Używane do logowania użytkowników przy użyciu tylko osobistych kont Microsoft (wcześniej znane jako konta usługi Windows Live ID).

Ustawienie urzędu certyfikacji musi być zgodne z co to jest zadeklarowana w portalu rejestracji aplikacji.

Adres URL urzędu składa się z wystąpieniem i odbiorców.

Uprawnienia można:
- Urząd chmury usługi Azure AD.
- Urząd usługi Azure AD B2C. Zobacz [specyfiki B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics).
- An Active Directory Federation Services (ADFS) authority. Zobacz [usług AD FS obsługuje](https://aka.ms/msal-net-adfs-support).

Urzędy chmury w usłudze Azure AD ma dwie części:
- Dostawca tożsamości *wystąpienia*
- Identyfikator logowania *odbiorców* dla aplikacji

Wystąpienia i odbiorców można połączonych i udostępniane jako adres URL urzędu. W wersjach platformy MSAL.NET wcześniejszej niż 3 biblioteki MSAL. *x*, musieli samodzielnie compose urzędowi, oparte na chmurze chciałeś pod kątem i odbiorców logowania.  Ten diagram przedstawia, jak składa się adres URL urzędu:

![Jak składa się adres URL urzędu](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Wystąpienie w chmurze
*Wystąpienia* służy do określania, jeśli aplikacja loguje się użytkowników z chmury publicznej platformy Azure lub chmur krajowych. Przy użyciu biblioteki MSAL w kodzie, można ustawić wystąpienie w chmurze platformy Azure przy użyciu wyliczenia lub przekazując adres URL do [wystąpienia chmury krajowe](authentication-national-cloud.md#azure-ad-authentication-endpoints) jako `Instance` elementu członkowskiego (jeśli je znasz).

Platformy MSAL.NET spowoduje zgłoszenie wyjątku jawne, jeśli obie `Instance` i `AzureCloudInstance` zostały określone.

Jeśli nie określisz wystąpienia aplikacji będą ukierunkowane na wystąpienia chmury publicznej platformy Azure (wystąpienia adres URL `https://login.onmicrosoftonline.com`).

## <a name="application-audience"></a>Odbiorcami aplikacji

Odbiorcy logowania zależy od potrzeb biznesowych aplikacji:
- Jesteś linię Deweloper aplikaacji biznesowych (LOB), prawdopodobnie będzie wygenerowanie aplikacji pojedynczej dzierżawy, który będzie używany tylko w Twojej organizacji. W takim przypadku musisz określić organizacji przez jego identyfikator dzierżawy (identyfikator wystąpienia usługi Azure AD) lub nazwę domeny skojarzony z wystąpieniem usługi Azure AD.
- Jeśli jesteś niezależnym dostawcą oprogramowania, możesz chcieć logowania użytkowników za pomocą kont służbowych w dowolnej organizacji lub w niektórych organizacjach (aplikacja wielodostępna). Ale można także użytkowników, zaloguj się przy użyciu ich osobistych kont Microsoft.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>Jak określić odbiorców w kodu/konfiguracji
Przy użyciu biblioteki MSAL w kodzie, należy określić odbiorców przy użyciu jednej z następujących wartości:
- Wyliczenie odbiorców urzędu usługi Azure AD
- Identyfikator dzierżawy, który może być:
  - Identyfikator GUID (identyfikator wystąpienia usługi Azure AD), w przypadku aplikacji z jedną dzierżawą
  - Nazwa domeny, skojarzone z wystąpieniem usługi Azure AD (również dla aplikacji jednej dzierżawy)
- Jeden z tych symboli zastępczych, jak identyfikator dzierżawy, zamiast wyliczenie odbiorców urzędu usługi Azure AD:
    - `organizations` w przypadku aplikacji wielodostępnych
    - `consumers` Aby zalogować się tylko użytkownicy z ich osobistych kont
    - `common` Aby logowanie użytkowników mających swoją pracę i kont służbowych lub ich osobistych kont Microsoft

Biblioteka MSAL spowoduje zgłoszenie istotnych wyjątku, jeśli określisz zarówno odbiorców urzędu usługi Azure AD, jak i identyfikator dzierżawy.

Jeśli nie określisz odbiorcami aplikacji będą ukierunkowane na usłudze Azure AD i osobistymi kontami Microsoft jako odbiorcami. (Oznacza to, że będzie ona działać tak, jakby `common` określono.)

### <a name="effective-audience"></a>Skuteczne odbiorców
Skuteczne odbiorców dla twojej aplikacji będą minimum (jeśli występuje część wspólna) odbiorców ustawionych w aplikacji oraz odbiorców, który jest określony w rejestracji aplikacji. W rzeczywistości [rejestracje aplikacji](https://aka.ms/appregistrations) środowisko umożliwia określenie odbiorców (typy obsługiwanych kont) dla aplikacji. Aby uzyskać więcej informacji, zobacz temat [Szybki start: Rejestrowanie aplikacji z platformą tożsamości Microsoft](quickstart-register-app.md).

Obecnie jedynym sposobem uzyskania aplikację na logowanie użytkowników mających tylko osobistych kont Microsoft jest skonfigurowanie oba te ustawienia:
- Ustaw odbiorców rejestracji aplikacji `Work and school accounts and personal accounts`.
- Ustaw odbiorców w kodu/konfiguracji, aby `AadAuthorityAudience.PersonalMicrosoftAccount` (lub `TenantID` = "Użytkownicy").

## <a name="client-id"></a>Identyfikator klienta
Identyfikator klienta jest unikatowy (klient) identyfikator przypisany do aplikacji przez usługę Azure AD, gdy aplikacja została zarejestrowana.

## <a name="redirect-uri"></a>Identyfikator URI przekierowania
Przekierowania URI jest dostawcy tożsamości otrzymają Państwo tokeny zabezpieczające z powrotem do identyfikatora URI.

### <a name="redirect-uri-for-public-client-apps"></a>Identyfikator URI przekierowania dla aplikacji w publicznych klienta
Jeśli jesteś deweloperem aplikacji publicznych klienta, który korzysta z biblioteki MSAL:
- Nie trzeba przekazać `RedirectUri` , ponieważ jest obliczana automatycznie przez biblioteki MSAL. Przekierowanie to identyfikator URI jest ustawiony na jedną z tych wartości, w zależności od platformy:
   - `urn:ietf:wg:oauth:2.0:oob` dla wszystkich platform Windows
   - `msal{ClientId}://auth` dla platformy Xamarin Android i iOS

- Należy skonfigurować przekierowania URI w [rejestracje aplikacji](https://aka.ms/appregistrations):

   ![Identyfikator URI przekierowania w rejestracje aplikacji](media/msal-client-application-configuration/redirect-uri.png)

Identyfikator URI przekierowania można zastąpić za pomocą `RedirectUri` właściwości (na przykład, jeśli używasz brokerów). Poniżej przedstawiono kilka przykładów identyfikatory URI przekierowania w przypadku tego scenariusza:

- `RedirectUriOnAndroid` = "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos` = $"msauth. {Bundle.ID}://auth";

Aby uzyskać więcej informacji, zobacz [dokumentację dla systemów Android i iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS).

### <a name="redirect-uri-for-confidential-client-apps"></a>Identyfikator URI przekierowania dla aplikacji zawierających poufne dane klienta
Identyfikator URI przekierowania (lub identyfikator URI odpowiedzi) dla aplikacji sieci web jest identyfikator URI, który usługa Azure AD będzie używać do odesłania token do aplikacji. Może to być adres URL aplikacji sieci web/internetowy interfejs API, jeśli poufnych aplikacji jest jeden z nich. Identyfikator URI przekierowania musi być zarejestrowana w rejestracji aplikacji. Rejestracja jest szczególnie ważne w przypadku, gdy wdrożysz aplikację, która początkowo zostały przetestowane lokalnie. Następnie należy dodać adres URL odpowiedzi aplikacji wdrożonej w portalu rejestracji aplikacji.

W przypadku aplikacji demona nie trzeba określić identyfikator URI przekierowania.

## <a name="client-secret"></a>Klucz tajny klienta
Ta opcja określa klucz tajny klienta dla aplikacji zawierających poufne dane klienta. To hasło, aplikacji udostępnionych przez portal rejestracji aplikacji lub dostarczane do usługi Azure AD podczas rejestracji aplikacji za pomocą programu PowerShell usługi Azure AD, PowerShell AzureRM lub wiersza polecenia platformy Azure.

## <a name="logging"></a>Rejestrowanie
Inne opcje konfiguracji, Włącz rejestrowanie i rozwiązywanie problemów. Zobacz [rejestrowania](msal-logging.md) artykuł, aby uzyskać szczegółowe informacje na temat sposobu ich używania.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [wystąpienia aplikacji klienckich przy użyciu platformy MSAL.NET](msal-net-initializing-client-applications.md).

Dowiedz się więcej o [wystąpienia aplikacji klienckich za pomocą MSAL.js](msal-js-initializing-client-applications.md).

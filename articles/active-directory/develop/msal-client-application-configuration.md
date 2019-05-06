---
title: Konfiguracja aplikacji klienta (Microsoft Authentication Library) | Azure
description: Dowiedz się więcej o opcjach konfiguracji dla publicznych klienta i poufne klienta aplikacji w programie Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
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
ms.openlocfilehash: 3a48eea9fedd2d82f44693d58b31ee0d5c8c288d
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138549"
---
# <a name="application-configuration-options"></a>Opcje konfiguracji aplikacji

W kodzie inicjowania nowego publicznego lub poufne klienta (lub agenta użytkownika dla MSAL.js) aplikacji w celu uwierzytelniania i uzyskać tokeny.  Istnieje wiele różnych opcji konfiguracji, które można ustawić podczas inicjowania aplikacji klienckiej w MSAL. Te opcje można podzielić na dwie grupy:

- Opcje rejestracji, w tym:
    - [Urząd](#authority) (składające się z dostawcą tożsamości [wystąpienia](#cloud-instance) i zaloguj się [odbiorców](#application-audience) dla aplikacji i prawdopodobnie identyfikator dzierżawy).
    - [Identyfikator klienta](#client-id)
    - [Identyfikator URI przekierowania](#redirect-uri)
    - [klucz tajny klienta](#client-secret) (w przypadku aplikacji poufnych klienta).
- [Opcje rejestrowania](#logging), w tym poziom dziennika, kontrolę nad danymi osobowymi i nazwę składnika za pomocą biblioteki.

## <a name="authority"></a>Urząd
Organ ma adres URL wskazujący katalog, w jakiej biblioteki MSAL może żądać tokenów z. Zwykle są:

- https://login.microsoftonline.com/&lt; dzierżawy&gt;/, gdzie &lt;dzierżawy&gt; jest identyfikator dzierżawy w domenie skojarzone z tym dzierżawcą usługi Azure AD lub dzierżawy usługi Azure AD.  Używana tylko do logowania użytkowników konkretnej organizacji.
- https://login.microsoftonline.com/common/. Umożliwia logowanie użytkowników mających pracy i konta służbowe lub osobiste konto Microsoft.
- https://login.microsoftonline.com/organizations/. Używane do logowania użytkowników przy użyciu kont służbowych.
- https://login.microsoftonline.com/consumers/. Umożliwia logowanie użytkowników mających tylko osobistego konta Microsoft (live).

Ustawienie urzędu certyfikacji muszą być zgodne z co to jest zadeklarowana w portalu rejestracji aplikacji.

Adres URL urzędu składa się z wystąpieniem i odbiorców.

Uprawnienia można:
- Urząd chmury usługi Azure Active directory
- Urząd usługi Azure AD B2C. Zobacz [specyfiki B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics).
- uprawnienia usługi AD FS. Zobacz [usług AD FS obsługuje](https://aka.ms/msal-net-adfs-support).

Urzędy chmury w usłudze Azure AD ma dwie części:
- Dostawca tożsamości **wystąpienia**
- Identyfikator logowania **odbiorców** dla aplikacji

Wystąpienia i odbiorców można połączonych i udostępniane jako adres URL urzędu. W wersjach platformy MSAL.NET przed MSAL 3.x, masz do redagowania urzędowi samodzielnie zależności w chmurze, chciała obiekt docelowy i odbiorców logowania.  Na poniższym diagramie przedstawiono, jak składa się adres URL urzędu.

![Urząd](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Wystąpienie w chmurze
**Wystąpienia** służy do określania, jeśli aplikacja jest podpisywanie użytkowników z chmury publicznej Microsoft Azure lub chmur krajowych. Przy użyciu biblioteki MSAL w kodzie, wystąpienie w chmurze platformy Azure można ustawić przy użyciu wyliczenia lub przekazując adres URL do [wystąpienia chmury krajowe](authentication-national-cloud.md#azure-ad-authentication-endpoints) jako `Instance` elementu członkowskiego (jeśli je znasz).

Platformy MSAL.NET spowoduje zgłoszenie wyjątku jawne, jeśli obie `Instance` i `AzureCloudInstance` zostały określone. 

Jeśli nie określisz wystąpienia aplikacji będą ukierunkowane na wystąpienia chmury publicznej platformy Azure (wystąpienia adres URL `https://login.onmicrosoftonline.com`).

## <a name="application-audience"></a>Odbiorcami aplikacji

Odbiorcy logowania zależy od potrzeb biznesowych aplikacji:
- Wiersz Deweloper aplikaacji biznesowych (LOB) będzie wygenerowanie prawdopodobnie aplikacji pojedynczej dzierżawy, który będzie używany tylko w Twojej organizacji. W takim przypadku należy określić tę organizację nowości, przez jego identyfikator dzierżawy (identyfikator usługi Azure Active Directory) lub nazwę domeny skojarzone z tym usługi Azure Active Directory.
- Jeśli jesteś niezależnym dostawcą oprogramowania, możesz chcieć logowanie użytkowników mających swoją pracę i kont służbowych w każdej organizacji lub w niektórych organizacjach (aplikacja wielodostępna), ale może również być wskazane użytkownikowi logowanie za pomocą swojego osobistego konta Microsoft.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>Jak określić odbiorców w kodu/konfiguracji
Przy użyciu biblioteki MSAL w kodzie, należy określić odbiorców za pomocą:
- albo usługi Azure AD urząd odbiorców wyliczenia. 
- lub identyfikator dzierżawy, który może być:
  - Identyfikator GUID, (identyfikator usługi Azure Active Directory), dla aplikacji jednej dzierżawy
  - Nazwa domeny, skojarzone z usługi Azure Active Directory (również dla aplikacji jednej dzierżawy)
- lub jeden z tych symboli zastępczych, jak identyfikator dzierżawy, zamiast wyliczenie odbiorców urzędu usługi Azure AD:
    - `organizations` dla aplikacji z wieloma dzierżawami
    - `consumers` Aby zalogować się tylko użytkownicy z ich osobistych kont
    - `common` do logowania użytkowników z ich pracy i konto służbowe lub osobiste konto Microsoft

Biblioteka MSAL spowoduje zgłoszenie istotnych wyjątku, jeśli określisz zarówno odbiorców urzędu usługi Azure AD, jak i identyfikator dzierżawy. 

Jeśli nie określisz odbiorcami aplikacji będą ukierunkowane na usłudze Azure AD i osobistymi kontami Microsoft jako grupy odbiorców (to znaczy `common`).

### <a name="effective-audience"></a>Skuteczne odbiorców
Skuteczne odbiorców dla twojej aplikacji będą minimum (jeśli występuje część wspólna) odbiorców ustawionych w aplikacji oraz odbiorców, określone w rejestracji aplikacji. W rzeczywistości, proces rejestracji aplikacji ([rejestracji aplikacji](https://aka.ms/appregistrations)) pozwala na określenie odbiorców (typy obsługiwanych kont) dla aplikacji. Zobacz [Szybki start: Rejestrowanie aplikacji z platformą tożsamości Microsoft](quickstart-register-app.md) Aby uzyskać więcej informacji.

Obecnie jedynym sposobem uzyskania aplikacji logowanie użytkowników mających tylko osobistych kont Microsoft, jest ustalenie:
- Ustaw odbiorców rejestracji aplikacji "Służbowych i kont służbowych i osobistych kont", a
- i ustawić odbiorców w kodzie / konfiguracji `AadAuthorityAudience.PersonalMicrosoftAccount` (lub `TenantID `= "Użytkownicy")

## <a name="client-id"></a>Identyfikator klienta
Identyfikator unikatowy aplikacja (klient) przypisany do aplikacji przez usługę Azure AD, gdy aplikacja została zarejestrowana.

## <a name="redirect-uri"></a>Identyfikator URI przekierowania
Identyfikator URI przekierowania jest identyfikatorem URI, w którym dostawca tożsamości wyśle tokeny zabezpieczające z powrotem. 

### <a name="redirect-uri-for-public-client-applications"></a>Identyfikator URI przekierowania dla aplikacji klienckich publiczne
Dla deweloperów aplikacji publicznych klienta przy użyciu biblioteki MSAL:
- Nie trzeba przekazać ``RedirectUri`` jak jest obliczana automatycznie przez biblioteki MSAL. Przekierowanie to identyfikator URI jest ustawiony na następujące wartości w zależności od platformy:

- ``urn:ietf:wg:oauth:2.0:oob`` dla wszystkich platform Windows
- ``msal{ClientId}://auth`` dla platformy Xamarin Android i iOS

Jednak przekierowania URI musi być skonfigurowana w [rejestracji aplikacji](https://aka.ms/appregistrations).

![Identyfikator URI przekierowania w portalu](media/msal-client-application-configuration/redirect-uri.png)

Można zastąpić za pomocą identyfikatora URI przekierowania `RedirectUri` właściwości, na przykład jeśli używasz brokerów. Poniżej przedstawiono kilka przykładów identyfikatorów URI przekierowań w takiej sytuacji:

- `RedirectUriOnAndroid` = "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos` = $"msauth. {Bundle.ID}://auth";

Szczegółowe informacje można znaleźć szczegóły dla systemu Android i [szczegółowych informacji z systemem iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS)

### <a name="redirect-uri-for-confidential-client-applications"></a>Identyfikator URI przekierowania dla aplikacji zawierających poufne dane klienta
Identyfikator URI (lub w identyfikator URI odpowiedzi) przekierowania dla aplikacji sieci web jest identyfikator URI, w którym usługi Azure AD będzie kontaktować się z powrotem aplikacji przy użyciu tokenu. Może to być adres URL aplikacji sieci Web / interfejs API sieci Web, jeśli poufnych jest jeden z nich.  Ten identyfikator URI przekierowania musi być zarejestrowana w rejestracji aplikacji. Jest to szczególnie ważne w przypadku, gdy wdrożysz aplikację, która początkowo zostały przetestowane lokalnie. Następnie należy dodać adres URL odpowiedzi aplikacji w portalu rejestracji aplikacji.

W przypadku aplikacji demona nie trzeba określić identyfikator URI przekierowania.

## <a name="client-secret"></a>Klucz tajny klienta
Klucz tajny klienta dla aplikacji zawierających poufne dane klienta. To hasło, aplikacji udostępnionych przez portal rejestracji aplikacji lub jest dostarczane do usługi Azure AD podczas rejestracji aplikacji za pomocą programu PowerShell usługi Azure AD, PowerShell AzureRM lub wiersza polecenia platformy Azure.

## <a name="logging"></a>Rejestrowanie
Inne opcje, Włącz rejestrowanie i rozwiązywanie problemów. Aby uzyskać więcej informacji, zobacz [rejestrowania](msal-logging.md) strony, aby uzyskać szczegółowe informacje na temat sposobu ich używania.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [wystąpienia aplikacji klienckich przy użyciu platformy MSAL.NET](msal-net-initializing-client-applications.md).

Dowiedz się więcej o [wystąpienia aplikacji klienckich za pomocą MSAL.js](msal-js-initializing-client-applications.md).

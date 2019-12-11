---
title: Rejestracja aplikacji chronionego interfejsu API sieci Web | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć chroniony internetowy interfejs API i informacje potrzebne do zarejestrowania aplikacji.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86c36661dd8b5d43417ca1d845eef5cdb146f1e9
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962529"
---
# <a name="protected-web-api-app-registration"></a>Chroniony internetowy interfejs API: Rejestracja aplikacji

W tym artykule opisano informacje o rejestracji aplikacji dla chronionego internetowego interfejsu API.

Aby zapoznać się z typowymi krokami rejestracji aplikacji, zobacz [Szybki Start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](quickstart-register-app.md) .

## <a name="accepted-token-version"></a>Zaakceptowana wersja tokenu

Punkt końcowy platformy tożsamości firmy Microsoft może wydać dwa typy tokenów: tokeny v 1.0 i tokeny v 2.0. Aby uzyskać więcej informacji na temat tych tokenów, zobacz [tokeny dostępu](access-tokens.md). Zaakceptowana wersja tokenu zależy od **obsługiwanych typów kont** wybranych podczas tworzenia aplikacji:

- Jeśli wartość **obsługiwanych typów kont** to **konta w dowolnym katalogu organizacyjnym i osobiste konta Microsoft (np. Skype, Xbox, Outlook.com)** , zaakceptowana wersja tokenu to v 2.0.
- W przeciwnym razie zaakceptowany token jest w wersji 1.0.

Po utworzeniu aplikacji możesz określić lub zmienić zaakceptowaną wersję tokenu, wykonując następujące czynności:

1. W Azure Portal wybierz aplikację, a następnie wybierz **manifest** dla swojej aplikacji.
2. W manifeście Wyszukaj ciąg **"accessTokenAcceptedVersion"** . Należy pamiętać, że wartość jest równa **2**. Ta właściwość określa Azure Active Directory (Azure AD), którą internetowy interfejs API akceptuje tokeny v 2.0. Jeśli wartość jest **równa null**, zaakceptowana wersja tokenu to v 1.0.
3. Jeśli została zmieniona wersja tokenu, wybierz pozycję **Zapisz**.

> [!NOTE]
> Internetowy interfejs API określa, która wersja tokenu (v 1.0 lub v 2.0) jest akceptowana. Gdy klienci żądają tokenu dla internetowego interfejsu API z punktu końcowego Microsoft Identity platform (v 2.0), otrzymają Token wskazujący, która wersja jest akceptowana przez internetowy interfejs API.

## <a name="no-redirect-uri"></a>Brak identyfikatora URI przekierowania

Interfejsy API sieci Web nie muszą rejestrować identyfikatora URI przekierowania, ponieważ żaden użytkownik nie jest zalogowany interaktywnie.

## <a name="expose-an-api"></a>Uwidacznianie interfejsu API

Innym ustawieniem specyficznym dla interfejsów API sieci Web jest uwidoczniony interfejs API i udostępniane zakresy.

### <a name="resource-uri-and-scopes"></a>Identyfikator URI zasobu i zakresy

Zakresy są zwykle w formacie `resourceURI/scopeName`. W przypadku Microsoft Graph zakresy mają skróty, takie jak `User.Read`. Ten ciąg jest skrótem do `https://graph.microsoft.com/user.read`.

Podczas rejestracji aplikacji należy zdefiniować następujące parametry:

- Identyfikator URI zasobu. Domyślnie Portal rejestracji aplikacji zaleca korzystanie z `api://{clientId}`. Ten identyfikator URI zasobu jest unikatowy, ale nie jest czytelny. Możesz to zmienić, ale upewnij się, że nowa wartość jest unikatowa.
- Co najmniej jeden *zakres*. (Aplikacje klienckie będą wyświetlane jako *uprawnienia delegowane* dla interfejsu API sieci Web).
- Co najmniej jedna *rola aplikacji*. (Aplikacje klienckie będą wyświetlane jako *uprawnienia aplikacji* dla interfejsu API sieci Web).

Zakresy są również wyświetlane na ekranie wyrażania zgody, który jest prezentowany użytkownikom końcowym aplikacji. W związku z tym należy podać odpowiednie ciągi opisujące zakres:

- Widziane przez użytkownika końcowego.
- Jak widać Administrator dzierżawy, który może udzielić zgody administratora.

### <a name="exposing-delegated-permissions-scopes"></a>Uwidacznianie delegowanych uprawnień (zakresy)

1. Wybierz sekcję **Uwidacznianie interfejsu API** w rejestracji aplikacji.
1. Wybierz polecenie **Dodaj zakres**.
1. Jeśli zostanie wyświetlony monit, zaakceptuj proponowany identyfikator URI aplikacji (`api://{clientId}`), wybierając pozycję **Zapisz i Kontynuuj**.
1. Wprowadź następujące parametry:
      - W obszarze **Nazwa zakresu**Użyj **access_as_user**.
      - Dla **osób, które mogą wyrazić zgodę**, upewnij się, że wybrano opcję **Administratorzy i użytkownicy** .
      - W polu **Nazwa wyświetlana zgody administratora**wpisz **TodoListService dostępu jako użytkownik**.
      - W polu **Opis zgody administratora**wprowadź **dostęp do internetowego interfejsu API TodoListService jako użytkownik**.
      - W polu **Nazwa wyświetlana zgody użytkownika**wpisz **TodoListService dostępu jako użytkownik**.
      - W **opisie zgody użytkownika**wpisz **dostęp do internetowego interfejsu API TodoListService jako użytkownik**.
      - Zachowaj **stan** ustawiony na **włączone**.
      - Wybierz pozycję **Dodaj zakres**.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>Jeśli internetowy interfejs API jest wywoływany przez aplikację demona

W tej sekcji dowiesz się, jak zarejestrować chroniony internetowy interfejs API, aby mógł być bezpiecznie wywoływany przez aplikacje demona.

- Należy uwidocznić *uprawnienia aplikacji*. Zadeklarujesz tylko uprawnienia aplikacji, ponieważ aplikacje demonów nie współpracują z użytkownikami, więc uprawnienia delegowane nie miały sensu.
- Administratorzy dzierżawy mogą wymagać Azure Active Directory (Azure AD), aby wystawiać tokeny dla internetowego interfejsu API tylko do aplikacji, które zostały zarejestrowane w celu uzyskania dostępu do jednego z uprawnień aplikacji internetowego interfejsu API.

#### <a name="exposing-application-permissions-app-roles"></a>Udostępnianie uprawnień aplikacji (ról aplikacji)

Aby uwidocznić uprawnienia aplikacji, należy edytować manifest.

1. W obszarze Rejestracja aplikacji dla aplikacji wybierz pozycję **manifest**.
1. Edytuj manifest, lokalizowanie ustawienia `appRoles` i Dodawanie co najmniej jednej roli aplikacji. Definicja roli znajduje się w następującym przykładowym bloku JSON. Pozostaw `allowedMemberTypes` ustawiony tylko na `"Application"`. Upewnij się, że `id` jest unikatowym identyfikatorem GUID i że `displayName` i `value` nie zawierać spacji.
1. Zapisz manifest.

Poniższy przykład pokazuje zawartość `appRoles`. (`id` może być dowolnym unikatowym identyfikatorem GUID).

```JSon
"appRoles": [
    {
    "allowedMemberTypes": [ "Application" ],
    "description": "Accesses the TodoListService-Cert as an application.",
    "displayName": "access_as_application",
    "id": "ccf784a6-fd0c-45f2-9c08-2f9d162a0628",
    "isEnabled": true,
    "lang": null,
    "origin": "Application",
    "value": "access_as_application"
    }
],
```

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>Upewnienie się, że tokeny wystawia usługi Azure AD dla internetowego interfejsu API tylko dla dozwolonych klientów

Interfejs API sieci Web sprawdza rolę aplikacji. (To jest programista, aby uwidocznić uprawnienia aplikacji). Możesz również skonfigurować usługę Azure AD, aby wystawić token dla internetowego interfejsu API tylko aplikacjom zatwierdzonym przez administratora dzierżawy w celu uzyskania dostępu do interfejsu API. Aby dodać zwiększone zabezpieczenia:

1. Na stronie **Przegląd** aplikacji na potrzeby rejestracji aplikacji wybierz łącze z nazwą aplikacji w obszarze **zarządzana aplikacja w katalogu lokalnym**. Tytuł dla tego pola może zostać obcięty. Możesz na przykład zobaczyć **aplikacja zarządzana w...**

   > [!NOTE]
   >
   > Po wybraniu tego linku nastąpi przejście do strony **przeglądu aplikacji przedsiębiorstwa** skojarzonej z jednostką usługi dla aplikacji w dzierżawie, w której został utworzony. Możesz przejść z powrotem do strony rejestracji aplikacji, korzystając z przycisku Wstecz w przeglądarce.

1. Wybierz stronę **Właściwości** w sekcji **Zarządzanie** na stronach aplikacji dla przedsiębiorstw.
1. Jeśli chcesz, aby usługa Azure AD zezwalała na dostęp do internetowego interfejsu API tylko z niektórych klientów, należy ustawić **wymagane przypisanie użytkownika?** na **wartość tak**.

   > [!IMPORTANT]
   >
   > Jeśli ustawisz opcję **przypisywania użytkownika?** na **wartość tak**, usługa Azure AD sprawdzi przypisania roli aplikacji klientów po zażądaniu tokenu dostępu dla internetowego interfejsu API. Jeśli klient nie jest przypisany do żadnych ról aplikacji, usługa Azure AD zwróci błąd `invalid_client: AADSTS501051: Application <application name> is not assigned to a role for the <web API>`.
   >
   > Jeśli jest **wymagane przypisanie użytkownika?** wartość **nie**, *usługa Azure AD nie będzie sprawdzać przypisań roli aplikacji, gdy klient zażąda tokenu dostępu dla internetowego interfejsu API*. Każdy klient demona (czyli każdy klient korzystający z usługi przepływu poświadczeń klienta) będzie mógł uzyskać token dostępu dla interfejsu API tylko przez określenie jego odbiorców. Każda aplikacja będzie mogła uzyskać dostęp do interfejsu API bez konieczności żądania uprawnień do niego. Jednak interfejs API sieci Web zawsze może, zgodnie z opisem w poprzedniej sekcji, sprawdzić, czy aplikacja ma odpowiednią rolę (która jest autoryzowana przez administratora dzierżawy). Interfejs API wykonuje tę weryfikację, sprawdzając, czy token dostępu ma rolę i czy wartość tego żądania jest poprawna. (W naszym przypadku wartość jest `access_as_application`.)

1. Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfiguracja kodu aplikacji](scenario-protected-web-api-app-configuration.md)

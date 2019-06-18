---
title: Chroniony internetowy interfejs API — rejestrowanie aplikacji | Azure
description: Dowiedz się, jak utworzyć chroniony internetowy interfejs API oraz informacje potrzebne do rejestrowania aplikacji.
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
ms.openlocfilehash: 22fe71c38678ae789a93ecbc956f24f0b0ebeb01
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111132"
---
# <a name="protected-web-api---app-registration"></a>Chroniony internetowy interfejs API — rejestrowanie aplikacji

W tym artykule opisano szczegóły rejestracji aplikacji dla chronionego internetowego interfejsu API.

Zobacz [Szybki start: Rejestrowanie aplikacji z platformą tożsamości Microsoft](quickstart-register-app.md) wspólne kroki dotyczące rejestrowania aplikacji.

## <a name="accepted-token-version"></a>Zaakceptowana wersja tokenu

Punkt końcowy platforma tożsamości firmy Microsoft mogą wystawiać dwóch rodzajów tokenów: v1.0 tokenów i tokenów w wersji 2.0. Dowiedz się więcej o tych tokenów w [tokeny dostępu](access-tokens.md). Zaakceptowana wersja tokenu jest zależna od **obsługiwane typy kont** wybrane podczas tworzenia aplikacji:

- Jeśli wartość **obsługiwane typy kont** jest **kont w dowolnym katalogu organizacji i osobistych kont Microsoft (np. Skype, Xbox, Outlook.com)** , będą akceptowane wersji tokenów w wersji 2.0.
- W przeciwnym razie zaakceptowana wersja tokenu będzie 1.0.

Po utworzeniu aplikacji możesz zmienić zaakceptowana wersja tokenu, wykonując następujące czynności:

1. W witrynie Azure portal wybierz swoją aplikację, a następnie wybierz pozycję **manifestu** dla aplikacji.
2. W manifeście, wyszukaj **"accessTokenAcceptedVersion"** i przekonać się, że jego wartość wynosi **2**. Ta właściwość umożliwia usłudze Azure AD wiedzieć, że internetowy interfejs API akceptuje tokeny w wersji 2.0. Jeśli jest **null**, będą akceptowane tokenu wersji 1.0.
3. Wybierz pozycję **Zapisz**.

> [!NOTE]
> To internetowy interfejs API do określania, akceptuje token wersji (w wersji 1.0 lub 2.0). Gdy klienci żądają tokenu dla internetowego interfejsu API przy użyciu punktu końcowego v2.0 platforma tożsamości firmy Microsoft, uzyska token, który wskazuje, która wersja jest akceptowane przez interfejs API sieci web.

## <a name="no-redirect-uri"></a>Nie identyfikatora URI przekierowania

Interfejsy API sieci Web nie trzeba zarejestrować identyfikator URI przekierowania, ponieważ żaden użytkownik nie jest zalogowany interaktywnie.

## <a name="expose-an-api"></a>Uwidaczniania interfejsu API

Inne ustawienie specyficzne dla interfejsów API sieci web jest ujawniany interfejsu API i narażonych zakresów.

### <a name="resource-uri-and-scopes"></a>Identyfikator URI zasobu i zakresy

Zakresy mają zwykle postać `resourceURI/scopeName`. Dla programu Microsoft Graph zakresy są skróty, takich jak `User.Read`, ale ten ciąg jest po prostu skrót `https://graph.microsoft.com/user.read`.

Podczas rejestrowania aplikacji należy zdefiniować następujące parametry:

- Identyfikator URI - zasobu domyślnie portalu rejestracji aplikacji zaleca, aby przy użyciu `api://{clientId}`. Identyfikator URI zasobu jest unikatowy, ale nie ludzi do odczytu. Można go zmienić, ale upewnij się, że jest ona unikatowa.
- Co najmniej jeden **zakresy** (dla aplikacji klienckich, będą wyświetlani jako **delegowane uprawnienia** dla internetowego interfejsu API)
- Co najmniej jeden **role aplikacji** (dla aplikacji klienckich, będą wyświetlani jako **uprawnienia aplikacji** dla internetowego interfejsu API)

Zakresy są również wyświetlane na ekranie wyrażania zgody, który jest przedstawiony użytkownicy końcowi, którzy korzystają z aplikacji. W związku z tym należy podać odpowiednie ciągi, które opisują zakresu:

- Widziany przez użytkownika końcowego
- Widziany przez administratora dzierżawy, który może Ci udzielić zgody administratora

### <a name="how-to-expose-delegated-permissions-scopes"></a>Jak udostępnić delegowane uprawnienia (zakresy)

1. Wybierz **uwidaczniania interfejsu API** sekcji w rejestracji aplikacji oraz:
   1. Wybierz polecenie **Dodaj zakres**.
   1. Jeśli jest to wymagane, należy zaakceptować proponowane identyfikator URI Identyfikatora aplikacji (api :// {clientId}), wybierając **Zapisz i Kontynuuj**.
   1. Wprowadź następujące parametry:
      - Aby uzyskać **nazwa zakresu**, użyj `access_as_user`.
      - Dla **kto może wyrazić zgodę**, upewnij się, że **Administratorzy i użytkownicy** opcja jest zaznaczona.
      - W **nazwę wyświetlaną zgody administratora**, typ `Access TodoListService as a user`.
      - W **opis zgody administratora**, typ `Accesses the TodoListService Web API as a user`.
      - W **nazwę wyświetlaną zgody użytkownika**, typ `Access TodoListService as a user`.
      - W **opis zgody użytkownika**, typ `Accesses the TodoListService Web API as a user`.
      - Zachowaj **stanu** równa **włączone**.
      - Wybierz **Dodaj zakres**.

### <a name="case-where-your-web-api-is-called-by-daemon-application"></a>Przypadek, gdzie internetowy interfejs API jest wywoływany przez aplikację demona

W tym akapicie dowiesz się, jak zarejestrować chronionego internetowego interfejsu API, dzięki czemu mogą być wywoływane w bezpieczny sposób przez demona aplikacji:

- Musisz udostępnić **uprawnienia aplikacji**. Uprawnienia aplikacji będzie deklarować tylko aplikacje demona nie wchodź w interakcję z użytkownikami i w związku z tym delegowane uprawnienia nie miałoby sensu.
- Administratorzy dzierżawy mogą wymagać usługi Azure AD do wydawania tokenów dla aplikacji sieci Web w taki sposób, aby tylko te aplikacje, które zostały zarejestrowane, że chce uzyskać dostęp do jednego uprawnienia aplikacji interfejsu API sieci Web.

#### <a name="how-to-expose-application-permissions-app-roles"></a>Jak udostępnić uprawnienia aplikacji (role aplikacji)

Aby udostępnić uprawnienia aplikacji, należy edytować manifestu.

1. Rejestrowanie aplikacji dla aplikacji, kliknij **manifestu**.
1. Edytuj manifest, znajdując `appRoles` ustawienie i dodanie jednego lub kilku ról aplikacji. Definicja roli znajduje się w bloku JSON przykładowe poniżej.  Pozostaw `allowedMemberTypes` "Aplikacji" tylko. Upewnij się, że **identyfikator** jest unikatowy identyfikator guid i **displayName** i **wartość** nie zawierają spacji.
1. Umożliwia zapisanie manifestu.

Zawartość `appRoles` powinny być następujące ( `id` może być dowolnym Unikatowy identyfikator GUID)

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

#### <a name="how-to-ensure-that-azure-ad-issues-tokens-for-your-web-api-only-to-allowed-clients"></a>Jak upewnić się, że usługa Azure AD wystawia tokeny interfejsu API sieci Web tylko do dozwolone klientów

Interfejs API sieci Web sprawdza, czy roli aplikacji (czyli Deweloper w sposób jej). Ale nawet można skonfigurować usługi Azure Active Directory na wystawienie tokena dla interfejsu API sieci Web tylko do aplikacji, które zostały zatwierdzone przez administratora dzierżawy, dostęp do Twojego interfejsu API. Aby dodać to dodatkowe zabezpieczenie:

1. W aplikacji **Przegląd** stronie rejestracji aplikacji, wybierz hiperłącze z nazwą aplikacji w **aplikacja zarządzana w katalogu lokalnym**. Tytuł dla tego pola może być obcinana. Można na przykład, przeczytaj: `Managed application in ...`

   > [!NOTE]
   >
   > Po wybraniu tego linku spowoduje przejście do **Omówienie aplikacji przedsiębiorstwa** strony skojarzonej z jednostką usługi dla aplikacji w dzierżawie, w którym zostały utworzone. Korzystając z przycisku Wstecz w przeglądarce, może przejść do strony rejestracji aplikacji.

1. Wybierz **właściwości** strony w **Zarządzaj** części strony aplikacji przedsiębiorstwa
1. Jeśli usługi AAD w celu wymuszenia dostępu do interfejsu API sieci Web z tylko niektórych klientów, należy ustawić **wymagane przypisanie użytkownika?** do **tak**.

   > [!IMPORTANT]
   >
   > Ustawiając **wymagane przypisanie użytkownika?** do **tak**, AAD będzie sprawdzać przypisania roli aplikacji klientów, gdy będą one żądać tokenu dostępu dla interfejsu API sieci Web. Jeśli klient nie został można przypisać do dowolnego AppRoles, usługi AAD po prostu spowoduje zwrócenie następującego błędu: `invalid_client: AADSTS501051: Application xxxx is not assigned to a role for the xxxx`
   >
   > Jeśli zachowasz **wymagane przypisanie użytkownika?** do **nie**, <span style='background-color:yellow; display:inline'>usługi Azure AD nie będzie Sprawdź przypisania roli aplikacji, gdy klient żąda tokenu dostępu dla internetowego interfejsu API</span>. W związku z tym nadal będzie mógł uzyskać token dostępu dla interfejsu API, po prostu, określając jego odbiorcami dowolnego klienta demona, (czyli dowolnego klienta przy użyciu przepływu poświadczeń klienta). Każda aplikacja będzie mogła bez konieczności żądania dla niej uprawnienia dostępu do interfejsu API. Teraz, ta wartość jest następnie zakończoną nazwą, ponieważ internetowego interfejsu API można zawsze, zgodnie z opisem w następnej sekcji, sprawdź, czy aplikacja ma odpowiednią rolę (który został autoryzowany przez administratora dzierżawy), weryfikując, czy token dostępu ma `roles` oświadczeń i odpowiednie wartości dla typu t roszczenia (w naszym przypadku `access_as_application`).

1. Wybierz **Zapisz**

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Konfiguracja kodu aplikacji](scenario-protected-web-api-app-configuration.md)

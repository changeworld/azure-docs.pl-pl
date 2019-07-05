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
ms.openlocfilehash: e4622cffedc159ce85166eafe571ccb26c2c1b4d
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536864"
---
# <a name="protected-web-api-app-registration"></a>Chroniony internetowy interfejs API: Rejestracja aplikacji

W tym artykule opisano szczegółowe informacje na temat rejestracji aplikacji dla chronionego internetowego interfejsu API.

Zobacz [Szybki start: Rejestrowanie aplikacji z platformą tożsamości Microsoft](quickstart-register-app.md) wspólne kroki rejestracji aplikacji.

## <a name="accepted-token-version"></a>Zaakceptowana wersja tokenu

Punkt końcowy platforma tożsamości firmy Microsoft mogą wystawiać dwóch rodzajów tokenów: v1.0 tokenów i tokenów w wersji 2.0. Aby uzyskać więcej informacji na temat tych tokenów, zobacz [tokeny dostępu](access-tokens.md). Zaakceptowana wersja tokenu jest zależna od **obsługiwane typy kont** wybrane podczas tworzenia aplikacji:

- Jeśli wartość **obsługiwane typy kont** jest **kont w dowolnym katalogu organizacji i osobistych kont Microsoft (np. Skype, Xbox, Outlook.com)** , będą akceptowane wersji tokenów w wersji 2.0.
- W przeciwnym razie zaakceptowana wersja tokenu będzie 1.0.

Po utworzeniu aplikacji, można określić lub zmienić zaakceptowana wersja tokenu, wykonaj następujące czynności:

1. W witrynie Azure portal wybierz swoją aplikację, a następnie wybierz pozycję **manifestu** dla aplikacji.
2. W manifeście, wyszukaj **"accessTokenAcceptedVersion"** . Należy zauważyć, że jego wartość wynosi **2**. Ta właściwość określa się do usługi Azure Active Directory (Azure AD), internetowy interfejs API akceptuje tokeny w wersji 2.0. Jeśli wartość jest **null**, zaakceptowana wersja tokenu jest w wersji 1.0.
3. Po zmianie wersji tokenu, wybierz **Zapisz**.

> [!NOTE]
> Internetowy interfejs API określa akceptuje token wersji (w wersji 1.0 lub 2.0). Gdy klienci żądają token dla interfejsu API sieci web z punktem końcowym v2.0 platforma tożsamości firmy Microsoft, uzyska token, który wskazuje, która wersja jest akceptowane przez interfejs API sieci web.

## <a name="no-redirect-uri"></a>Nie identyfikatora URI przekierowania

Interfejsy API sieci Web nie trzeba zarejestrować identyfikator URI przekierowania, ponieważ żaden użytkownik nie jest podpisany interaktywnie.

## <a name="expose-an-api"></a>Uwidaczniania interfejsu API

Inne ustawienie specyficzne dla interfejsów API sieci web jest ujawniany interfejsu API i narażonych zakresów.

### <a name="resource-uri-and-scopes"></a>Identyfikator URI zasobu i zakresy

Zakresy są zwykle w formie `resourceURI/scopeName`. Dla programu Microsoft Graph zakresy są skróty, takich jak `User.Read`. Ten ciąg jest skrót `https://graph.microsoft.com/user.read`.

Podczas rejestracji aplikacji należy zdefiniować następujące parametry:

- Identyfikator URI zasobu. Domyślnie przez portal rejestracji aplikacji zaleca się, przy użyciu `api://{clientId}`. Identyfikator URI zasobu jest unikatowy, ale nie ludzi do odczytu. Można go zmienić, ale upewnij się, że nowa wartość jest unikatowa.
- Co najmniej jeden *zakresy*. (Dla aplikacji klienckich, będą one wyświetlane jako *delegowane uprawnienia* dla interfejsu API sieci web.)
- Co najmniej jeden *role aplikacji*. (Dla aplikacji klienckich, będą one wyświetlane jako *uprawnienia aplikacji* dla interfejsu API sieci web.)

Zakresy są również wyświetlane na ekranie wyrażania zgody, które są prezentowane użytkownikom końcowym aplikacji. Dlatego należy podać odpowiednie ciągi, które opisują zakresu:

- Widziany przez użytkownika końcowego.
- Widziany przez administratora dzierżawy, który może Ci udzielić zgody administratora.

### <a name="exposing-delegated-permissions-scopes"></a>Udostępnianie delegowane uprawnienia (zakresy)

1. Wybierz **uwidaczniania interfejsu API** sekcji w rejestracji aplikacji.
1. Wybierz polecenie **Dodaj zakres**.
1. Po wyświetleniu monitu Zaakceptuj proponowane identyfikator URI Identyfikatora aplikacji (`api://{clientId}`), wybierając **Zapisz i Kontynuuj**.
1. Wprowadź następujące parametry:
      - Aby uzyskać **nazwa zakresu**, użyj **access_as_user**.
      - Dla **kto może wyrazić zgodę**, upewnij się, że **Administratorzy i użytkownicy** jest zaznaczone.
      - W **nazwę wyświetlaną zgody administratora**, wprowadź **TodoListService dostęp jako użytkownik**.
      - W **opis zgody administratora**, wprowadź **uzyskuje dostęp do interfejsu API sieci Web TodoListService jako użytkownik**.
      - W **nazwę wyświetlaną zgody użytkownika**, wprowadź **TodoListService dostęp jako użytkownik**.
      - W **opis zgody użytkownika**, wprowadź **uzyskuje dostęp do interfejsu API sieci Web TodoListService jako użytkownik**.
      - Zachowaj **stanu** równa **włączone**.
      - Wybierz **Dodaj zakres**.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>Jeśli interfejs API sieci web jest wywoływana przez aplikację demona

W tej sekcji dowiesz się, jak zarejestrować chronionego internetowego interfejsu API, dzięki czemu mogą być bezpiecznie wywoływane przez aplikacje demona.

- Musisz udostępnić *uprawnienia aplikacji*. Będziesz deklarować tylko uprawnienia aplikacji, ponieważ aplikacje demona nie wchodzą w interakcję z użytkownikami, więc uprawnień delegowanych nie miałoby sensu.
- Administratorzy dzierżawy mogą wymagać usługi Azure Active Directory (Azure AD) do wydawania tokenów dla interfejsu API sieci web tylko do aplikacji, które zostały zarejestrowane dostęp do jednej z uprawnienia aplikacji internetowego interfejsu API.

#### <a name="exposing-application-permissions-app-roles"></a>Udostępnianie uprawnień aplikacji (role aplikacji)

Aby udostępnić uprawnienia aplikacji, należy edytować manifestu.

1. Rejestrowanie aplikacji dla aplikacji, wybierz **manifestu**.
1. Edytuj manifest, znajdując `appRoles` ustawienie i dodanie jednego lub większej liczby ról aplikacji. Definicja roli znajduje się w następujący blok próbki w formacie JSON. Pozostaw `allowedMemberTypes` równa `"Application"` tylko. Upewnij się, że `id` jest unikatowy identyfikator GUID oraz że `displayName` i `value` nie zawierają spacji.
1. Umożliwia zapisanie manifestu.

Poniższy przykład pokazuje zawartość `appRoles`. ( `id` Może być dowolnym Unikatowy identyfikator GUID.)

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

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>Zapewnienie, że usługa Azure AD wystawia tokeny dla internetowego interfejsu API, aby dozwolone tylko klienci

Interfejs API sieci web sprawdza, czy roli aplikacji. (Jest to sposób udostępnić uprawnienia aplikacji dla deweloperów). Ale można również skonfigurować usługi Azure AD, aby wystawić token dla interfejsu API sieci web tylko do aplikacji, które zostały zatwierdzone przez administratora dzierżawy, dostęp do Twojego interfejsu API. Aby dodać to zwiększenie bezpieczeństwa:

1. W aplikacji **Przegląd** stronie rejestracji aplikacji, wybierz link o nazwie aplikacji w środowisku **aplikacja zarządzana w katalogu lokalnym**. Tytuł dla tego pola może zostać obcięta. Na przykład może zostać wyświetlony **aplikacja zarządzana w...**

   > [!NOTE]
   >
   > Po wybraniu tego linku, aby przejść do **Omówienie aplikacji przedsiębiorstwa** strony skojarzonej z jednostką usługi dla aplikacji w dzierżawie, w którym zostały utworzone. Korzystając z przycisku Wstecz w przeglądarce, może przejść do strony rejestracji aplikacji.

1. Wybierz **właściwości** strony w **Zarządzaj** części strony aplikacji przedsiębiorstwa.
1. Jeśli chcesz, aby zezwolić na dostęp do interfejsu API sieci web z tylko niektórych klientów w usłudze Azure AD, ustaw **wymagane przypisanie użytkownika?** do **tak**.

   > [!IMPORTANT]
   >
   > Jeśli ustawisz **wymagane przypisanie użytkownika?** do **tak**, usługi Azure AD będzie sprawdzać przypisania roli aplikacji klientów, gdy będą one żądać tokenu dostępu dla internetowego interfejsu API. Jeśli klient nie jest przypisany do żadnych ról aplikacji, usługi Azure AD zwróci błąd `invalid_client: AADSTS501051: Application <application name> is not assigned to a role for the <web API>`.
   >
   > Jeśli zachowasz **wymagane przypisanie użytkownika?** równa **nie**, *usługi Azure AD nie będzie Sprawdź przypisania roli aplikacji, gdy klient żąda tokenu dostępu do interfejsu API sieci web*. Dowolny klient demona (czyli dowolnego klienta przy użyciu przepływu poświadczeń klienta) będzie można uzyskać tokenu dostępu dla interfejsu API, po prostu, określając jego odbiorcami. Każda aplikacja będzie można uzyskać dostęp do interfejsu API bez konieczności żądania dla niej uprawnienia. Ale internetowego interfejsu API można zawsze, zgodnie z opisem w poprzedniej sekcji, sprawdź, czy aplikacja ma odpowiednią rolę (który jest autoryzowany przez administratora dzierżawy). Interfejs API wykonuje ta Weryfikacja przez weryfikowanie, czy token dostępu ma role oświadczeń i czy wartość dla tego oświadczenia jest poprawna. (W naszym przypadku jest wartość `access_as_application`.)

1. Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Konfiguracja kodu aplikacji](scenario-protected-web-api-app-configuration.md)

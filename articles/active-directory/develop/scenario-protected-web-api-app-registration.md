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
ms.openlocfilehash: a1d8c8fcf84cd008957fcdb7cd14c4a07d9f3643
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074908"
---
# <a name="protected-web-api---app-registration"></a>Chroniony internetowy interfejs API — rejestrowanie aplikacji

W tym artykule opisano szczegóły rejestracji aplikacji dla chronionego internetowego interfejsu API.

Zobacz [Szybki start: Rejestrowanie aplikacji z platformą tożsamości Microsoft](quickstart-register-app.md) wspólne kroki dotyczące rejestrowania aplikacji.

## <a name="accepted-token-version"></a>Zaakceptowana wersja tokenu

Punkt końcowy platforma tożsamości firmy Microsoft mogą wystawiać dwóch rodzajów tokenów: v1.0 tokenów i tokenów w wersji 2.0. Dowiedz się więcej o tych tokenów w [tokeny dostępu](access-tokens.md). Zaakceptowana wersja tokenu jest zależna od **obsługiwane typy kont** wybrane podczas tworzenia aplikacji:

- Jeśli wartość **obsługiwane typy kont** jest **kont w dowolnym katalogu organizacji i osobistych kont Microsoft (np. Skype, Xbox, Outlook.com)**, będą akceptowane wersji tokenów w wersji 2.0.
- W przeciwnym razie zaakceptowana wersja tokenu będą w wersji 2.0.

Po utworzeniu aplikacji możesz zmienić zaakceptowana wersja tokenu, wykonując następujące czynności:

1. W witrynie Azure portal wybierz swoją aplikację, a następnie wybierz pozycję **manifestu** dla aplikacji.
2. W manifeście, wyszukaj **"accessTokenAcceptedVersion"** i przekonać się, że jego wartość wynosi **2**. Ta właściwość umożliwia usłudze Azure AD wiedzieć, że internetowy interfejs API akceptuje tokeny w wersji 2.0. Jeśli jest **null**, będą akceptowane tokenu wersji 1.0.
3. Wybierz pozycję **Zapisz**.

> [!NOTE]
> To internetowy interfejs API do określania, akceptuje token wersji (w wersji 1.0 lub 2.0). Gdy klienci żądają tokenu dla internetowego interfejsu API przy użyciu punktu końcowego v2.0 platforma tożsamości firmy Microsoft, uzyska token, który wskazuje, która wersja jest akceptowane przez interfejs API sieci web.

## <a name="no-redirect-uri"></a>Nie identyfikatora URI przekierowania

Interfejsy API sieci Web nie trzeba zarejestrować identyfikator URI przekierowania, ponieważ żaden użytkownik nie jest zalogowany interaktywnie.

## <a name="expose-an-api"></a>Uwidocznij interfejs API

Inne ustawienie specyficzne dla interfejsów API sieci web jest ujawniany interfejsu API i narażonych zakresów.

### <a name="resource-uri-and-scopes"></a>Identyfikator URI zasobu i zakresy

Zakresy mają zwykle postać `resourceURI/scopeName`. Dla programu Microsoft Graph zakresy są skróty, takich jak `User.Read`, ale ten ciąg jest po prostu skrót `https://graph.microsoft.com/user.read`.

Podczas rejestrowania aplikacji należy zdefiniować następujące parametry:

- Jeden zasób URI — domyślnie portalu rejestracji aplikacji zaleca, aby przy użyciu `api://{clientId}`. Identyfikator URI zasobu jest unikatowy, ale nie ludzi do odczytu. Można go zmienić, ale upewnij się, że jest ona unikatowa.
- Jeden lub wiele zakresów

Zakresy są również wyświetlane na ekranie wyrażania zgody, które są prezentowane dla użytkowników końcowych, którzy korzystają z aplikacji. W związku z tym należy podać odpowiednie ciągi, które opisują zakresu:

- Widziany przez użytkownika końcowego
- Widziany przez administratora dzierżawy, który może Ci udzielić zgody administratora

### <a name="how-to-expose-the-api"></a>Jak udostępnić interfejs API

1. Wybierz **uwidaczniania interfejsu API** sekcji w rejestracji aplikacji oraz:
   1. Wybierz polecenie **Dodaj zakres**.
   1. Zaakceptuj proponowane identyfikator URI Identyfikatora aplikacji (api :// {clientId}), wybierając **Zapisz i Kontynuuj**.
   1. Wprowadź następujące parametry:
      - Aby uzyskać **nazwa zakresu**, użyj `access_as_user`.
      - Dla **kto może wyrazić zgodę**, upewnij się, że **Administratorzy i użytkownicy** opcja jest zaznaczona.
      - W **nazwę wyświetlaną zgody administratora**, typ `Access TodoListService as a user`.
      - W **opis zgody administratora**, typ `Accesses the TodoListService Web API as a user`.
      - W **nazwę wyświetlaną zgody użytkownika**, typ `Access TodoListService as a user`.
      - W **opis zgody użytkownika**, typ `Accesses the TodoListService Web API as a user`.
      - Zachowaj **stanu** równa **włączone**.
      - Wybierz **Dodaj zakres**.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Konfiguracja kodu aplikacji](scenario-protected-web-api-app-configuration.md)

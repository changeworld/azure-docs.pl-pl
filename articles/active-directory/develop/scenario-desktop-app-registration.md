---
title: Rejestrowanie aplikacji klasycznych, które wywołują interfejsy API sieci Web — Microsoft Identity platform | Azure
description: Dowiedz się, jak utworzyć aplikację klasyczną wywołującą interfejsy API sieci Web (Rejestracja aplikacji)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 878f942bf36fef999b90274b81eaa7735afa73e5
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293355"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>Aplikacja klasyczna, która wywołuje interfejsy API sieci Web: Rejestracja aplikacji

W tym artykule omówiono specyficzne dla rejestracji aplikacji dla aplikacji klasycznych.

## <a name="supported-account-types"></a>Obsługiwane typy konta

Typy kont obsługiwane w aplikacji klasycznej zależą od środowiska, które chcesz wyrównać. Z powodu tej relacji obsługiwane typy kont zależą od przepływów, które mają być używane.

### <a name="audience-for-interactive-token-acquisition"></a>Odbiorcy na potrzeby pozyskiwania tokenów interaktywnych

Jeśli aplikacja klasyczna korzysta z uwierzytelniania interakcyjnego, użytkownicy mogą logować się z dowolnego [typu konta](quickstart-register-app.md#register-a-new-application-using-the-azure-portal).

### <a name="audience-for-desktop-app-silent-flows"></a>Odbiorcy dla przepływów dyskretnych aplikacji klasycznych

- Aby można było używać zintegrowanego uwierzytelniania systemu Windows lub nazwy użytkownika i hasła, aplikacja musi zalogować użytkowników w Twojej dzierżawie, na przykład jeśli jesteś deweloperem biznesowym (LOB). Lub, w Azure Active Directory organizacji, aplikacja musi zalogować użytkowników w Twojej dzierżawie, jeśli jest to scenariusz niezależnego dostawcy oprogramowania. Te przepływy uwierzytelniania nie są obsługiwane w przypadku kont osobistych firmy Microsoft.
- Jeśli chcesz użyć przepływu kodu urządzenia, nie możesz jeszcze zalogować użytkowników przy użyciu kont osobistych firmy Microsoft.
- Jeśli logujesz się do użytkowników przy użyciu tożsamości społecznościowych, które przechodzą przez Urząd i zasady firmy B2C (Business-to-Handle), możesz używać tylko uwierzytelniania interakcyjnego i nazwy użytkownika.

## <a name="redirect-uris"></a>Identyfikatory URI przekierowania

Identyfikatory URI przekierowania do użycia w aplikacji klasycznej zależą od przepływu, którego chcesz użyć.

- W przypadku korzystania z uwierzytelniania interakcyjnego lub przepływu kodu urządzenia należy używać `https://login.microsoftonline.com/common/oauth2/nativeclient`. Aby osiągnąć tę konfigurację, wybierz odpowiedni adres URL w sekcji **uwierzytelnianie** dla swojej aplikacji.
  
  > [!IMPORTANT]
  > Dzisiaj MSAL.NET domyślnie używa innego identyfikatora URI przekierowania w aplikacjach klasycznych, które działają w systemie Windows (`urn:ietf:wg:oauth:2.0:oob`). W przyszłości będziemy chcieć zmienić to ustawienie domyślne, dlatego zalecamy używanie `https://login.microsoftonline.com/common/oauth2/nativeclient`.

- Jeśli utworzysz natywną aplikację "cel-C" lub "Swift" dla macOS, zarejestruj identyfikator URI przekierowania na podstawie identyfikatora pakietu aplikacji w następującym formacie: msauth. < element. app .Binding. ID >://auth. Zastąp < swoją aplikację App .Binding. ID > z identyfikatorem pakietu aplikacji.
- Jeśli aplikacja używa tylko zintegrowanego uwierzytelniania systemu Windows lub nazwy użytkownika i hasła, nie trzeba rejestrować identyfikatora URI przekierowania dla aplikacji. Te przepływy umożliwiają przeprowadzenie rundy w punkcie końcowym Microsoft Identity platform v 2.0. Aplikacja nie zostanie wywołana ponownie na żadnym konkretnym identyfikatorze URI.
- Aby rozróżnić przepływ kodu urządzenia, zintegrowane uwierzytelnianie systemu Windows oraz nazwę użytkownika i hasło z poufnego przepływu aplikacji klienta, który nie ma identyfikatorów URI przekierowania (przepływ poświadczeń klienta używany w aplikacjach demonów), należy wyrazić to Aplikacja jest publiczną aplikacją kliencką. Aby osiągnąć tę konfigurację, przejdź do sekcji **uwierzytelnianie** dla swojej aplikacji. W podsekcji **Ustawienia zaawansowane** w obszarze **domyślny typ klienta** wybierz pozycję **tak** dla **aplikacji Traktuj jako klienta publicznego**.

  ![Zezwalaj na klienta publicznego](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>Uprawnienia do interfejsu API

Aplikacje klasyczne wywoływanie interfejsów API dla zalogowanego użytkownika. Muszą oni zażądać uprawnień delegowanych. Nie mogą żądać uprawnień aplikacji, które są obsługiwane tylko w [aplikacjach demona](scenario-daemon-overview.md).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Aplikacja klasyczna: Konfiguracja aplikacji](scenario-desktop-app-configuration.md)

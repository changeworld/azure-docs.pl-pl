---
title: Aplikacja klasyczna, która wywołuje interfejsy API sieci Web (Rejestracja aplikacji) — Microsoft Identity platform
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
ms.openlocfilehash: 648652ed32a5dea30de665b7fa49190171a7f10a
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268406"
---
# <a name="desktop-app-that-calls-web-apis---app-registration"></a>Aplikacja klasyczna, która wywołuje interfejsy API sieci Web — Rejestracja aplikacji

Ten artykuł zawiera informacje o rejestracji aplikacji dla aplikacji klasycznej.

## <a name="supported-accounts-types"></a>Obsługiwane typy kont

Typy kont obsługiwane w aplikacji klasycznej zależą od środowiska, które chcesz wyrównać. Z powodu tej relacji obsługiwane typy kont zależą od przepływów, które mają być używane.

### <a name="audience-for-interactive-token-acquisition"></a>Odbiorcy na potrzeby pozyskiwania tokenów interaktywnych

Jeśli aplikacja klasyczna korzysta z uwierzytelniania interakcyjnego, użytkownicy mogą logować się z dowolnego [typu konta](quickstart-register-app.md#register-a-new-application-using-the-azure-portal).

### <a name="audience-for-desktop-app-silent-flows"></a>Odbiorcy dla przepływów dyskretnych aplikacji klasycznych

- Aby można było używać zintegrowanego uwierzytelniania systemu Windows lub nazwy użytkownika/hasła, aplikacja musi zalogować użytkowników w Twojej dzierżawie (deweloper LOB) lub w organizacji usługi Azure Active Directory (scenariusz ISV). Te przepływy uwierzytelniania nie są obsługiwane w przypadku kont osobistych firmy Microsoft.
- Jeśli chcesz użyć przepływu kodu urządzenia, nie możesz jeszcze zalogować użytkowników przy użyciu kont osobistych firmy Microsoft.
- Jeśli logujesz się do użytkowników przy użyciu tożsamości społecznościowych przekazujących B2C Urząd i zasady, możesz korzystać tylko z uwierzytelniania interaktywnego i nazwy użytkownika.

## <a name="redirect-uris"></a>Identyfikatory URI przekierowania

Identyfikatory URI przekierowania używane w aplikacji klasycznej będą zależeć od przepływu, którego chcesz użyć.

- Jeśli używasz **interaktywnego uwierzytelniania** lub **przepływu kodu urządzenia**, możesz użyć polecenia `https://login.microsoftonline.com/common/oauth2/nativeclient`. Tę konfigurację można osiągnąć, klikając odpowiedni adres URL w sekcji **uwierzytelnianie** dla aplikacji.
  
  > [!IMPORTANT]
  > Dzisiaj MSAL.NET domyślnie używa innego identyfikatora URI przekierowania w aplikacjach klasycznych działających w`urn:ietf:wg:oauth:2.0:oob`systemie Windows (). W przyszłości będziemy chcieć zmienić to ustawienie domyślne i dlatego zalecamy użycie`https://login.microsoftonline.com/common/oauth2/nativeclient`

- Jeśli tworzysz natywną aplikację "cel-C" lub "Swift" dla macOS, chcesz zarejestrować redirectUri na podstawie identyfikatora pakietu aplikacji w następującym formacie: **msauth. < element. app .Binding. id >://auth** (zastąp < Twoja. app .Binding. ID > z identyfikatorem pakietu aplikacji)
- Jeśli aplikacja używa tylko zintegrowanego uwierzytelniania systemu Windows lub nazwy użytkownika/hasła, nie trzeba rejestrować identyfikatora URI przekierowania dla aplikacji. Te przepływy wykonują rundy w punkcie końcowym Microsoft Identity platform v 2.0, a aplikacja nie zostanie wywołana ponownie na żadnym konkretnym identyfikatorze URI.
- Aby rozróżnić przepływ kodu urządzenia, zintegrowane uwierzytelnianie systemu Windows i nazwę użytkownika/hasło z poufnego przepływu aplikacji klienta, który nie ma identyfikatorów URI przekierowania (przepływ poświadczeń klienta używany w aplikacjach demonów), należy wyrazić, że Aplikacja jest publiczną aplikacją kliencką. Aby osiągnąć tę konfigurację, przejdź do sekcji **uwierzytelnianie** dla swojej aplikacji. Następnie w podsekcji **Ustawienia zaawansowane** w obszarze **domyślny typ klienta** wybierz pozycję **tak** dla pytania **Traktuj aplikację jako klienta publicznego**.

  ![Zezwalaj na klienta publicznego](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>Uprawnienia interfejsu API

Aplikacje klasyczne wywoływanie interfejsów API dla zalogowanego użytkownika. Muszą oni zażądać uprawnień delegowanych. Nie mogą jednak żądać uprawnień aplikacji, które są obsługiwane tylko w [aplikacjach demona](scenario-daemon-overview.md).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Aplikacja klasyczna — konfiguracja aplikacji](scenario-desktop-app-configuration.md)

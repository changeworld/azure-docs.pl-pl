---
title: Rejestrowanie aplikacji klasycznych wywołujących internetowe interfejsy API — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć aplikację klasyczną, która wywołuje internetowe interfejsy API (rejestracja aplikacji)
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
ms.openlocfilehash: c55fc9eb94a88dba1ab9fc915fe84bc2dd7d4d40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76702185"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>Aplikacja komputerowa, która wywołuje internetowe interfejsy API: rejestracja aplikacji

W tym artykule opisano szczegóły rejestracji aplikacji dla aplikacji klasycznej.

## <a name="supported-account-types"></a>Obsługiwane typy konta

Typy kont obsługiwane w aplikacji klasycznej zależą od środowiska, które chcesz zapalić. Z powodu tej relacji obsługiwane typy kont zależą od przepływów, których chcesz użyć.

### <a name="audience-for-interactive-token-acquisition"></a>Publiczność do interaktywnego pozyskiwania tokenów

Jeśli aplikacja komputerowa korzysta z uwierzytelniania interaktywnego, można zalogować użytkowników z dowolnego [typu konta](quickstart-register-app.md#register-a-new-application-using-the-azure-portal).

### <a name="audience-for-desktop-app-silent-flows"></a>Przepływy niemne dla odbiorców aplikacji komputerowych

- Aby używać zintegrowanego uwierzytelniania systemu Windows lub nazwy użytkownika i hasła, aplikacja musi zalogować się do użytkowników we własnej dzierżawie, na przykład, jeśli jesteś deweloperem linii biznesowej (LOB). Lub w organizacjach usługi Azure Active Directory aplikacja musi zalogować się do użytkowników we własnej dzierżawie, jeśli jest to scenariusz niezależnego użytkownika. Te przepływy uwierzytelniania nie są obsługiwane dla kont osobistych firmy Microsoft.
- Jeśli chcesz użyć przepływu kodu urządzenia, nie możesz jeszcze zalogować użytkowników za pomocą ich kont osobistych Microsoft.
- Jeśli logujesz się do użytkowników za pomocą tożsamości społecznościowych, które przechodzą urząd i zasady między firmami (B2C), możesz używać tylko uwierzytelniania interaktywnego i hasła nazwy użytkownika.

## <a name="redirect-uris"></a>Przekierowywanie identyfikatorów URI

Identyfikatory URI przekierowania do użycia w aplikacji klasycznej zależą od przepływu, którego chcesz użyć.

- Jeśli używasz uwierzytelniania interaktywnego `https://login.microsoftonline.com/common/oauth2/nativeclient`lub przepływu kodu urządzenia, użyj . Aby osiągnąć tę konfigurację, wybierz odpowiedni adres URL w sekcji **Uwierzytelnianie** dla aplikacji.
  
  > [!IMPORTANT]
  > Obecnie MSAL.NET domyślnie używa innego identyfikatora URI przekierowania w`urn:ietf:wg:oauth:2.0:oob`aplikacjach klasycznych uruchamianych w systemie Windows ( ). W przyszłości będziemy chcieli zmienić tę wartość domyślną, `https://login.microsoftonline.com/common/oauth2/nativeclient`dlatego zalecamy użycie programu .

- Jeśli tworzysz natywną aplikację Objective-C lub Swift dla systemu macOS, zarejestruj identyfikator URI przekierowania na podstawie identyfikatora pakietu aplikacji w następującym formacie: msauth.<your.app.bundle.id>://auth. Zamień <your.app.bundle.id> identyfikatorem pakietu aplikacji.
- Jeśli aplikacja używa tylko zintegrowanego uwierzytelniania systemu Windows lub nazwy użytkownika i hasła, nie trzeba rejestrować identyfikatora URI przekierowania dla aplikacji. Przepływy te wykonują podróż w obie strony do punktu końcowego platformy tożsamości firmy Microsoft w wersji 2.0. Aplikacja nie zostanie wywołana z powrotem na żadnym określonym identyfikatorze URI.
- Aby odróżnić przepływ kodu urządzenia, zintegrowane uwierzytelnianie systemu Windows oraz nazwę użytkownika i hasło od poufnego przepływu aplikacji klienckiej, który nie ma przekierowanych identyfikatorów URI (przepływ poświadczeń klienta używany w aplikacjach demonów), należy wyrazić to aplikacja jest publiczną aplikacją kliencką. Aby osiągnąć tę konfigurację, przejdź do sekcji **Uwierzytelnianie** dla aplikacji. W podsekcji **Ustawienia zaawansowane** w akapicie **Domyślny typ klienta** wybierz pozycję **Tak** dla **aplikacji Traktuj jako klienta publicznego**.

  ![Zezwalaj na klienta publicznego](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>Uprawnienia aplikacji

Aplikacje klasyczne wywołują interfejsy API dla zalogowanego użytkownika. Muszą zażądać delegowanych uprawnień. Nie mogą żądać uprawnień aplikacji, które są obsługiwane tylko w [aplikacjach demonów.](scenario-daemon-overview.md)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Aplikacja komputerowa: konfiguracja aplikacji](scenario-desktop-app-configuration.md)

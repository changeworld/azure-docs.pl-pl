---
title: Uwierzytelnianie brokerskie w systemie Android | Azure
titlesuffix: Microsoft identity platform
description: Omówienie uwierzytelniania & autoryzacji w systemie Android na platformie tożsamości firmy Microsoft
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman, hahamil, brianmel
ms.openlocfilehash: a734589178438fd65d9a2d156fd91fc82807f578
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76697901"
---
# <a name="brokered-authentication-in-android"></a>Uwierzytelnianie brokerskie w systemie Android

Musisz użyć jednego z brokerów uwierzytelniania firmy Microsoft, aby uczestniczyć w rejestracji jednokrotnej (SSO) całego urządzenia i spełniać zasady organizacyjnego dostępu warunkowego. Integracja z brokerem zapewnia następujące korzyści:

- Logowanie jednokrotne urządzenia
- Dostęp warunkowy dla:
  - Ochrona aplikacji usługi Intune
  - Rejestracja urządzenia (dołączanie do miejsca pracy)
  - Zarządzanie urządzeniami przenośnymi
- Zarządzanie kontem w całym urządzeniu
  -  za pośrednictwem Android AccountManager & ustawienia konta
  - "Konto służbowe" - typ konta niestandardowego

W systemie Android broker uwierzytelniania firmy Microsoft jest składnikiem dołączonym do [aplikacji Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) i [portalu firmy usługi Intune](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)

> [!TIP]
> Tylko jedna aplikacja, która obsługuje brokera będzie aktywny jako broker w czasie. Która aplikacja jest aktywna jako broker jest określana przez kolejność instalacji na urządzeniu. Pierwszy, który zostanie zainstalowany lub ostatni prezent na urządzeniu, staje się aktywnym brokerem.

Na poniższym diagramie przedstawiono relację między aplikacją, biblioteką uwierzytelniania firmy Microsoft (MSAL) i brokerami uwierzytelniania firmy Microsoft.

![Diagram wdrażania brokera](./media/brokered-auth/brokered-deployment-diagram.png)

## <a name="installing-apps-that-host-a-broker"></a>Instalowanie aplikacji hostujących brokera

Aplikacje brokera hostingu mogą być instalowane przez właściciela urządzenia ze sklepu z aplikacjami (zazwyczaj w Sklepie Google Play) w dowolnym momencie. Jednak niektóre interfejsy API (zasoby) są chronione przez zasady dostępu warunkowego, które wymagają urządzeń, które mają być:

- Zarejestrowany (przyłączony do miejsca pracy) i/lub
- Zarejestrowane w Zarządzaniu urządzeniami lub
- Zarejestrowano się w ochronie aplikacji usługi Intune

Jeśli urządzenie nie ma jeszcze zainstalowanej aplikacji brokera, MSAL nakazuje użytkownikowi zainstalować ją, gdy tylko aplikacja spróbuje uzyskać token interaktywnie. Aplikacja będzie następnie trzeba prowadzić użytkownika przez kroki, aby urządzenie zgodne z wymaganymi zasadami.

## <a name="effects-of-installing-and-uninstalling-a-broker"></a>Efekty instalacji i odinstalowywania brokera

### <a name="when-a-broker-is-installed"></a>Po zainstalowaniu brokera

Gdy broker jest zainstalowany na urządzeniu, wszystkie kolejne `acquireToken()`żądania tokenu interaktywnego (wywołania) są obsługiwane przez brokera, a nie lokalnie przez MSAL. Każdy stan samego przysłówka wcześniej dostępny dla usługi MSAL nie jest dostępny dla brokera. W rezultacie użytkownik będzie musiał uwierzytelnić się ponownie lub wybrać konto z istniejącej listy kont znanych urządzeniu.

Instalowanie brokera nie wymaga ponownego logowania użytkownika. Tylko wtedy, gdy użytkownik `MsalUiRequiredException` musi rozwiązać będzie następne żądanie przejść do brokera. `MsalUiRequiredException`jest wyrzucany z wielu powodów i musi być rozwiązany interaktywnie. Oto kilka typowych przyczyn:

- Użytkownik zmienił hasło skojarzone z kontem.
- Konto użytkownika nie spełnia już zasad dostępu warunkowego.
- Użytkownik cofnął zgodę na powiązanie aplikacji z kontem.

### <a name="when-a-broker-is-uninstalled"></a>Po odinstalowaniu brokera

Jeśli zainstalowano tylko jedną aplikację hostingu brokera i zostanie usunięta, użytkownik będzie musiał zalogować się ponownie. Odinstalowanie aktywnego brokera powoduje usunięcie konta i skojarzonych tokenów z urządzenia.

Jeśli program Intune Company Portal jest zainstalowany i działa jako aktywny broker, a program Microsoft Authenticator jest również zainstalowany, jeśli portal firmy usługi Intune (aktywny broker) zostanie odinstalowany, użytkownik będzie musiał zalogować się ponownie. Po ponownym zalogowaniu aplikacja Microsoft Authenticator staje się aktywnym brokerem.

## <a name="integrating-with-a-broker"></a>Integracja z brokerem

### <a name="generating-a-redirect-uri-for-a-broker"></a>Generowanie identyfikatora URI przekierowania dla brokera

Należy zarejestrować identyfikator URI przekierowania, który jest zgodny z brokera. Identyfikator URI przekierowania dla brokera musi zawierać nazwę pakietu aplikacji, a także zakodowaną reprezentację podpisu aplikacji zakodowaną w base64.

Format identyfikatora URI przekierowania jest:`msauth://<yourpackagename>/<base64urlencodedsignature>`

Wygeneruj podpis zakodowany w adresie URL Base64 przy użyciu kluczy podpisywania aplikacji. Oto kilka przykładowych poleceń, które używają kluczy podpisywania debugowania:

#### <a name="macos"></a>macOS

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

#### <a name="windows"></a>Windows

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

Aby uzyskać informacje na temat podpisywania aplikacji, zobacz [Podpisywanie aplikacji.](https://developer.android.com/studio/publish/app-signing)

> [!IMPORTANT]
> Użyj klucza podpisywania produkcji dla produkcyjnej wersji aplikacji.

### <a name="configure-msal-to-use-a-broker"></a>Konfigurowanie usługi MSAL do używania brokera

Aby korzystać z brokera w aplikacji, musisz potwierdzić, że skonfigurowano przekierowanie brokera. Na przykład uwzględnij zarówno identyfikator URI przekierowania włączonego brokera — jak i wskaż, że został zarejestrowany — przez uwzględnienie następujących elementów w pliku konfiguracyjnym MSAL:

```javascript
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

> [!TIP]
> Nowy interfejs użytkownika rejestracji aplikacji witryny Azure portal pomaga wygenerować brokera przekierowania identyfikatora URI. Jeśli aplikacja została zarejestrowana przy użyciu starszego środowiska lub zrobiła to za pomocą portalu rejestracji aplikacji firmy Microsoft, może być konieczne wygenerowanie identyfikatora URI przekierowania i ręczne zaktualizowanie listy identyfikatorów URI przekierowania w portalu.

### <a name="broker-related-exceptions"></a>Wyjątki związane z brokerem

MSAL komunikuje się z brokerem na dwa sposoby:

- Usługa związana z brokerem
- Menedżer konta w systemie Android

MSAL najpierw używa usługi powiązanej z brokerem, ponieważ wywołanie tej usługi nie wymaga żadnych uprawnień systemu Android. Jeśli powiązanie z powiązaną usługą zakończy się niepowodzeniem, usługa MSAL użyje interfejsu API programu Android AccountManager. Usługa MSAL robi to tylko wtedy, `"READ_CONTACTS"` gdy aplikacja została już przyznana uprawnieniu.

Jeśli pojawi `MsalClientException` się kod `"BROKER_BIND_FAILURE"`błędu , istnieją dwie opcje:

- Poproś użytkownika o wyłączenie optymalizacji zasilania aplikacji Microsoft Authenticator i portalu firmy usługi Intune.
- Poproś użytkownika o `"READ_CONTACTS"` udzielenie uprawnień

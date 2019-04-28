---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: a11b291ab89dc9f8159e00e1f2304706f041068e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60297713"
---
## <a name="test-your-code"></a>testowanie kodu

Aby uruchomić projekt, w programie Visual Studio, wybierz **F5**. Aplikacja **MainWindow** jest wyświetlana, jak pokazano poniżej:

![Testowanie aplikacji](./media/active-directory-develop-guidedsetup-windesktop-test/samplescreenshot.png)

Przy pierwszym uruchomieniu aplikacji i wybierz **wywołania interfejsu API Microsoft Graph** przycisku, zostanie wyświetlony monit do logowania. Użyj konta usługi Azure Active Directory (konto służbowe) lub konta Microsoft (live.com, outlook.com), aby ją przetestować.

![Logowanie się do aplikacji](./media/active-directory-develop-guidedsetup-windesktop-test/signinscreenshot.png)

### <a name="provide-consent-for-application-access"></a>Podanie zgody na dostęp do aplikacji

Przy pierwszym logowaniu do aplikacji, zostanie również wyświetlony monit zapewnienie zgody, aby umożliwić aplikacji dostęp do Twojego profilu i logowanie się w programie, jak pokazano poniżej:

![Wyrażenie zgody na dostęp do aplikacji](./media/active-directory-develop-guidedsetup-windesktop-test/consentscreen.png)

### <a name="view-application-results"></a>Wyświetl wyniki aplikacji

Po zalogowaniu, powinny być widoczne informacje o profilu użytkownika, który jest zwracany przez wywołanie interfejsu API programu Microsoft Graph. Wyniki są wyświetlane w **wyników wywołań interfejsu API** pole. Podstawowe informacje o token, który został uzyskany za pośrednictwem wywołania `AcquireTokenInteractive` lub `AcquireTokenSilent` powinny być widoczne w **informacje o tokenie** pole. Wyniki obejmują następujące właściwości:

|Właściwość  |Format  |Opis |
|---------|---------|---------|

|**Nazwa użytkownika**  | <span> user@domain.com </span> | Nazwa użytkownika, który służy do identyfikowania użytkownika. | | **Wygaśnięcia ważności tokenu** | Data i godzina | Czas, w którym token jest ważny. Biblioteka MSAL rozszerza datę wygaśnięcia, odnawianie tokenu zgodnie z potrzebami. |


<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Więcej informacji o zakresach i delegowane uprawnienia

Interfejsu API programu Microsoft Graph wymaga *user.read* zakresu na odczytywanie profilu użytkownika. Ten zakres jest automatycznie dodawany domyślnie każda aplikacja, która jest zarejestrowana w portalu rejestracji aplikacji. Inne interfejsy API dla programu Microsoft Graph, a także niestandardowych interfejsów API dla serwera zaplecza, może być wymagane dodatkowe zakresy. Interfejsu API programu Microsoft Graph wymaga *Calendars.Read* zakres, aby wyświetlić listę kalendarzy użytkownika.

Aby uzyskać dostęp do kalendarzy użytkownika w kontekście aplikacji, należy dodać *Calendars.Read* delegowane uprawnienia do rejestrowania informacji o aplikacji. Następnie należy dodać *Calendars.Read* ograniczyć zakres do `acquireTokenSilent` wywołania.

>[!NOTE]
>Użytkownik może być monitowany o dodatkowe zgody, jak zwiększyć liczbę zakresów.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]

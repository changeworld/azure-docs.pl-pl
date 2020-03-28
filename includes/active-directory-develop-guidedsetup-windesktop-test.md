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
ms.openlocfilehash: f121be4ec8c3d3ab618e2955d9dbd8ab5eea461d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "79128805"
---
## <a name="test-your-code"></a>testowanie kodu

Aby uruchomić projekt, w programie Visual Studio wybierz pozycję **F5**. Aplikacja **MainWindow** jest wyświetlana, jak pokazano poniżej:

![Testowanie aplikacji](./media/active-directory-develop-guidedsetup-windesktop-test/samplescreenshot.png)

Przy pierwszym uruchomieniu aplikacji i wybraniu przycisku **Wywołaj interfejs API programu Microsoft Graph** zostanie wyświetlony monit o zalogowanie się. Użyj konta usługi Azure Active Directory (konta służbowego) lub konta Microsoft (live.com, outlook.com), aby je przetestować.

![Logowanie się do aplikacji](./media/active-directory-develop-guidedsetup-windesktop-test/signinscreenshot.png)

### <a name="provide-consent-for-application-access"></a>Udzielanie zgody na dostęp do aplikacji

Przy pierwszym logowaniu się do aplikacji zostanie wyświetlony monit o wyrażenie zgody na zezwolenie aplikacji na dostęp do twojego profilu i zalogowanie się, jak pokazano poniżej:

![Zgód na dostęp do aplikacji](./media/active-directory-develop-guidedsetup-windesktop-test/consentscreen.png)

### <a name="view-application-results"></a>Wyświetlanie wyników aplikacji

Po zalogowaniu się powinny zostać wyświetlona informacje o profilu użytkownika, które są zwracane przez wywołanie do interfejsu API programu Microsoft Graph. Wyniki są wyświetlane w polu **Wyniki wywołania interfejsu API.** Podstawowe informacje o tokenie, który `AcquireTokenInteractive` został `AcquireTokenSilent` nabyty za pośrednictwem wywołania lub powinny być widoczne w polu **Informacje o tokenie.** Wyniki zawierają następujące właściwości:

|Właściwość  |Format  |Opis |
|---------|---------|---------|
|**Nazwę użytkownika** |<span>user@domain.com</span> |Nazwa użytkownika, która jest używana do identyfikowania użytkownika.|
|**Token wygasa** |DateTime |Czas wygaśnięcia tokenu. MSAL rozszerza datę wygaśnięcia, odnawiając token w razie potrzeby.|


<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Więcej informacji o zakresach i uprawnieniach delegowanych

Interfejs API programu Microsoft Graph wymaga, aby zakres *user.read* odczytywany był w profilu użytkownika. Ten zakres jest domyślnie dodawany w każdej aplikacji zarejestrowanej w portalu rejestracji aplikacji. Inne interfejsy API programu Microsoft Graph, a także niestandardowe interfejsy API serwera zaplecza mogą wymagać dodatkowych zakresów. Interfejs API programu Microsoft Graph wymaga zakresu *Calendars.Read* do listy kalendarzy użytkownika.

Aby uzyskać dostęp do kalendarzy użytkownika w kontekście aplikacji, dodaj *calendars.Read* delegowane uprawnienia do informacji o rejestracji aplikacji. Następnie dodaj *zakres Calendars.Read* `acquireTokenSilent` do wywołania.

>[!NOTE]
>Użytkownik może zostać poproszony o dodatkowe zgody w miarę zwiększania liczby zakresów.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]

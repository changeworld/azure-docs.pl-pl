---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: ac4f826ed1d27ee39d8e35605a3baa7f94b33e64
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2018
ms.locfileid: "50035317"
---
## <a name="test-your-code"></a>Testowanie kodu

Aby uruchomić projekt, w programie Visual Studio, wybierz **F5**. Aplikacja **MainWindow** jest wyświetlana, jak pokazano poniżej:

![Testowanie aplikacji](./media/active-directory-develop-guidedsetup-windesktop-test/samplescreenshot.png)

Przy pierwszym uruchomieniu aplikacji i wybierz **wywołania interfejsu API Microsoft Graph** przycisku, zostanie wyświetlony monit do logowania. Użyj konta usługi Azure Active Directory (konto służbowe) lub konta Microsoft (live.com, outlook.com), aby ją przetestować.

![Logowanie się do aplikacji](./media/active-directory-develop-guidedsetup-windesktop-test/signinscreenshot.png)

### <a name="provide-consent-for-application-access"></a>Podanie zgody na dostęp do aplikacji

Przy pierwszym logowaniu do aplikacji, zostanie również wyświetlony monit zapewnienie zgody, aby umożliwić aplikacji dostęp do Twojego profilu i logowanie się w programie, jak pokazano poniżej:

![Wyrażenie zgody na dostęp do aplikacji](./media/active-directory-develop-guidedsetup-windesktop-test/consentscreen.png)

### <a name="view-application-results"></a>Wyświetl wyniki aplikacji

Po zalogowaniu, powinny być widoczne informacje o profilu użytkownika, który jest zwracany przez wywołanie interfejsu API programu Microsoft Graph. Wyniki są wyświetlane w **wyników wywołań interfejsu API** pole. Podstawowe informacje o token, który został uzyskany za pośrednictwem wywołania `AcquireTokenAsync` lub `AcquireTokenSilentAsync` powinny być widoczne w **informacje o tokenie** pole. Wyniki obejmują następujące właściwości:

|Właściwość  |Format  |Opis |
|---------|---------|---------|
|**Nazwa** |Imię i nazwisko użytkownika |Użytkownik na imię i nazwisko.|
|**Nazwa użytkownika** |<span>user@domain.com</span> |Nazwa użytkownika, który służy do identyfikowania użytkownika.|
|**Token wygasa** |DateTime |Czas, w którym token jest ważny. Biblioteka MSAL rozszerza datę wygaśnięcia, odnawianie tokenu zgodnie z potrzebami.|
|**Token dostępu** |Ciąg |Ciąg tokenu, który jest wysyłany do protokołu HTTP żądania, które wymagają *nagłówek autoryzacji*.|

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Więcej informacji o zakresach i delegowane uprawnienia

Interfejsu API programu Microsoft Graph wymaga *user.read* zakresu na odczytywanie profilu użytkownika. Ten zakres jest automatycznie dodawany domyślnie każda aplikacja, która jest zarejestrowana w portalu rejestracji aplikacji. Inne interfejsy API dla programu Microsoft Graph, a także niestandardowych interfejsów API dla serwera zaplecza, może być wymagane dodatkowe zakresy. Interfejsu API programu Microsoft Graph wymaga *Calendars.Read* zakres, aby wyświetlić listę kalendarzy użytkownika.

Aby uzyskać dostęp do kalendarzy użytkownika w kontekście aplikacji, należy dodać *Calendars.Read* delegowane uprawnienia do rejestrowania informacji o aplikacji. Następnie należy dodać *Calendars.Read* ograniczyć zakres do `acquireTokenSilent` wywołania.

>[!NOTE]
>Użytkownik może być monitowany o dodatkowe zgody, jak zwiększyć liczbę zakresów.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]

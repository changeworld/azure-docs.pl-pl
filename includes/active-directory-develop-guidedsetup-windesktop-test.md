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
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128805"
---
## <a name="test-your-code"></a>testowanie kodu

Aby uruchomić projekt, w programie Visual Studio wybierz **F5**. Zostanie wyświetlona **MainWindow** aplikacji, jak pokazano poniżej:

![Testowanie aplikacji](./media/active-directory-develop-guidedsetup-windesktop-test/samplescreenshot.png)

Przy pierwszym uruchomieniu aplikacji i wybraniu przycisku **Microsoft Graph API wywołania** zostanie wyświetlony monit o zalogowanie się. Użyj konta Azure Active Directory (konto służbowe) lub konto Microsoft (live.com, outlook.com), aby je przetestować.

![Logowanie się do aplikacji](./media/active-directory-develop-guidedsetup-windesktop-test/signinscreenshot.png)

### <a name="provide-consent-for-application-access"></a>Wyrażanie zgody na dostęp do aplikacji

Przy pierwszym zalogowaniu się do aplikacji zostanie również wyświetlony monit o wyrażenie zgody na uzyskanie dostępu do Twojego profilu przez aplikację i zalogowanie się, jak pokazano poniżej:

![Wyrażanie zgody na dostęp do aplikacji](./media/active-directory-develop-guidedsetup-windesktop-test/consentscreen.png)

### <a name="view-application-results"></a>Wyświetlanie wyników aplikacji

Po zalogowaniu powinna zostać wyświetlona informacja o profilu użytkownika, która jest zwracana przez wywołanie do interfejsu API Microsoft Graph. Wyniki są wyświetlane w polu **wyniki wywołania interfejsu API** . Podstawowe informacje o tokenie uzyskanym za pośrednictwem wywołania do `AcquireTokenInteractive` lub `AcquireTokenSilent` powinny być widoczne w polu **Informacje o tokenie** . Wyniki zawierają następujące właściwości:

|Właściwość  |Format  |Opis |
|---------|---------|---------|
|**Nazwa użytkownika** |<span>user@domain.com</span> |Nazwa użytkownika służąca do identyfikowania użytkownika.|
|**Token wygasa** |DateTime |Godzina wygaśnięcia tokenu. MSAL rozszerza datę wygaśnięcia przez odnowienie tokenu w razie potrzeby.|


<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Więcej informacji na temat zakresów i uprawnień delegowanych

Interfejs API Microsoft Graph wymaga, aby *użytkownik. Read* miał zakres do odczytu profilu użytkownika. Ten zakres jest automatycznie dodawany domyślnie w każdej aplikacji, która jest zarejestrowana w portalu rejestracji aplikacji. Inne interfejsy API dla Microsoft Graph, a także niestandardowe interfejsy API dla serwera zaplecza mogą wymagać dodatkowych zakresów. Interfejs API Microsoft Graph wymaga zakresu *Calendars. Read* , aby wyświetlić kalendarze użytkownika.

Aby uzyskać dostęp do kalendarzy użytkownika w kontekście aplikacji, Dodaj *kalendarze. Odczytaj* delegowane uprawnienia do informacji rejestracyjnych aplikacji. Następnie Dodaj zakres *Calendars. Read* do wywołania `acquireTokenSilent`.

>[!NOTE]
>Użytkownik może zostać poproszony o dodatkowe przesłanie w miarę zwiększania liczby zakresów.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]

---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: c13c9ece54d512adf271532a4ac17876a88d733e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60297959"
---
## <a name="test-your-code"></a>testowanie kodu

### <a name="test-with-node"></a>Testowanie za pomocą węzła

Jeśli nie używasz programu Visual Studio, upewnij się, że serwer sieci web jest uruchomiona.

1. Konfigurowanie serwera do nasłuchiwania na portach TCP, który zależy od lokalizacji użytkownika **index.html** pliku. Dla węzła uruchom serwer sieci web do nasłuchiwania na porcie, uruchamiając następujące polecenia w wierszu polecenia z folderu aplikacji:

    ```bash
    npm install
    node server.js
    ```
1. Otwórz przeglądarkę i wpisz http://<span></span>localhost:30662 lub http://<span></span>localhost: {port} gdzie **portu** jest portu nasłuchiwania serwera sieci web. Powinna zostać wyświetlona zawartość pliku index.html i **Sign In** przycisku.

<p><!-- -->

### <a name="test-with-visual-studio"></a>Testowanie za pomocą programu Visual Studio

Jeśli używasz programu Visual Studio upewnij się wybrać projekt rozwiązanie i naciśnij klawisz **F5** Aby uruchomić projekt. Zostanie otwarta przeglądarka http://<span></span>localhost: {port} lokalizacji i **Sign In** przycisku.

## <a name="test-your-application"></a>Testowanie aplikacji

Po załadowaniu pliku index.html przeglądarki kliknij **Sign In**. Użytkownik jest monitowany o zalogowanie do endpoint platforma tożsamości firmy Microsoft:

![Zaloguj się do swojego konta JavaScript SPA](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Podanie zgody na dostęp do aplikacji

Przy pierwszym logowaniu do aplikacji, zostanie wyświetlony monit o wyrażenie zgody, aby umożliwić aplikacji dostęp do Twojego profilu i logowanie się w:

![Wyrażenie zgody na dostęp do aplikacji](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Wyświetl wyniki aplikacji

Po zakończeniu logowania, użytkownik powinien zostać wyświetlony zwracany w odpowiedzi interfejsu API Microsoft Graph, wyświetlany na stronie informacji o profilu użytkownika.

![Oczekiwane wyniki z wywołania interfejsu API Microsoft Graph](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Więcej informacji o zakresach i delegowane uprawnienia

Interfejsu API programu Microsoft Graph wymaga `user.read` zakresu na odczytywanie profilu użytkownika. Ten zakres jest automatycznie dodawany domyślnie każda aplikacja, która jest zarejestrowana w portalu rejestracji. Inne interfejsy API dla programu Microsoft Graph, a także niestandardowych interfejsów API dla serwera zaplecza, może być wymagane dodatkowe zakresy. Na przykład interfejsu API programu Microsoft Graph wymaga `Calendars.Read` zakres, aby wyświetlić listę kalendarzy użytkownika.

Aby uzyskać dostęp do kalendarzy użytkownika w kontekście aplikacji, należy dodać `Calendars.Read` delegowane uprawnienia do rejestrowania informacji o aplikacji. Następnie należy dodać `Calendars.Read` ograniczyć zakres do `acquireTokenSilent` wywołania.

>[!NOTE]
>Użytkownik może być monitowany o dodatkowe zgody, jak zwiększyć liczbę zakresów.

Jeśli interfejs API zaplecza nie wymaga zakresu (niezalecane), możesz użyć `clientId` jako zasięg w wywołaniach można uzyskać tokenów.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
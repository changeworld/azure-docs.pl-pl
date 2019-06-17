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
ms.openlocfilehash: 494f5fb6f2bfdec86cad01a37e57c3ec219a77f9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133238"
---
## <a name="test-your-code"></a>testowanie kodu

Testowanie kodu przy użyciu jednej z następujących środowisk.

### <a name="test-with-nodejs"></a>Testowanie za pomocą środowiska Node.js

Jeśli nie używasz programu Visual Studio, upewnij się, że serwer sieci web jest uruchomiona.

1. Konfigurowanie serwera do nasłuchiwania na portach TCP, który zależy od lokalizacji użytkownika *index.html* pliku. Dla środowiska Node.js uruchom serwer sieci web do nasłuchiwania na porcie, uruchamiając następujące polecenia w wierszu polecenia z folderu aplikacji:

    ```bash
    npm install
    node server.js
    ```
1. W przeglądarce, wprowadź **http://\<span >\</span > localhost:30662** lub **http://\<span >\</span > localhost: {port}** , gdzie *portu* jest portu nasłuchiwania serwera sieci web. Powinna zostać wyświetlona zawartość swojej *index.html* pliku i **Sign In** przycisku.

### <a name="test-with-visual-studio"></a>Testowanie za pomocą programu Visual Studio

Jeśli używasz programu Visual Studio wybierz projekt rozwiązanie, a następnie naciśnij klawisz F5, aby uruchomić projekt. Zostanie otwarta przeglądarka http://<span></span>localhost: {port} lokalizacji i **Sign In** przycisk powinien być widoczny.

## <a name="test-your-application"></a>Testowanie aplikacji

Po przeglądarki ładuje Twoje *index.html* pliku, wybierz opcję **Sign In**. Zostanie wyświetlony monit o zalogowanie do endpoint platforma tożsamości firmy Microsoft:

![JavaScript SPA konta logowania w oknie](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Podanie zgody na dostęp do aplikacji

Przy pierwszym logowaniu do aplikacji, zostanie wyświetlony monit zapewnia dostęp do Twojego profilu i zalogować:

![W oknie "Żądane uprawnienia"](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Wyświetl wyniki aplikacji

Po zalogowaniu się w informacji o profilu użytkownika jest zwracany w odpowiedzi interfejsu API Microsoft Graph, który jest wyświetlany na stronie.

![Wyniki wywołania interfejsu API Microsoft Graph](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Więcej informacji o zakresach i delegowane uprawnienia

Interfejsu API programu Microsoft Graph wymaga *user.read* zakresu na odczytywanie profilu użytkownika. Ten zakres jest automatycznie dodawany domyślnie każda aplikacja, która jest zarejestrowana w portalu rejestracji. Inne interfejsy API dla programu Microsoft Graph, a także niestandardowych interfejsów API dla serwera zaplecza, może być wymagane dodatkowe zakresy. Na przykład interfejsu API programu Microsoft Graph wymaga *Calendars.Read* zakres, aby wyświetlić listę kalendarzy użytkownika.

Aby uzyskać dostęp do kalendarzy użytkownika w kontekście aplikacji, należy dodać *Calendars.Read* delegowane uprawnienia do rejestrowania informacji o aplikacji. Następnie należy dodać *Calendars.Read* ograniczyć zakres do `acquireTokenSilent` wywołania.

>[!NOTE]
>Użytkownik może być monitowany o dodatkowe zgody, jak zwiększyć liczbę zakresów.

Jeśli interfejs API zaplecza nie wymaga zakresu (niezalecane), możesz użyć *clientId* jako zasięg w wywołaniach można uzyskać tokenów.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]

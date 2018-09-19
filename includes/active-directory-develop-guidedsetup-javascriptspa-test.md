---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 9817e94ba77c83b8620274ba41f9d862b6a5ce6d
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "46293499"
---
## <a name="test-your-code"></a>Testowanie kodu

### <a name="test-with-visual-studio"></a>Testowanie za pomocą programu Visual Studio
Jeśli używasz programu Visual Studio, naciśnij klawisz **F5** Aby uruchomić projekt. Zostanie otwarta przeglądarka http://<span></span>localhost: {port} lokalizacji i **wywołania interfejsu API Microsoft Graph** przycisku.

<p/><!-- -->

### <a name="test-with-node-or-other-web-server"></a>Testowanie za pomocą węzła lub inny serwer sieci web
Jeśli nie używasz programu Visual Studio, upewnij się, że serwer sieci web jest uruchomiona. Konfigurowanie serwera do nasłuchiwania na portach TCP, który zależy od lokalizacji użytkownika **index.html** pliku. Dla węzła uruchom serwer sieci web do nasłuchiwania na porcie, uruchamiając następujące polecenia w wierszu polecenia z folderu aplikacji:

```bash
npm install
node server.js
```
Otwórz przeglądarkę i wpisz http://<span></span>localhost:30662 lub http://<span></span>localhost: {port} gdzie **portu** jest portu nasłuchiwania serwera sieci web. Powinna zostać wyświetlona zawartość pliku index.html i **wywołania interfejsu API Microsoft Graph** przycisku.

## <a name="test-your-application"></a>Testowanie aplikacji

Po załadowaniu pliku index.html przeglądarce wybierz **wywołania interfejsu API Microsoft Graph**. Przy pierwszym uruchomieniu aplikacji, przeglądarka przekierowuje do punktu końcowego v2.0 Microsoft Azure Active Directory (Azure AD) i zostanie wyświetlony monit do logowania:

![Zaloguj się do swojego konta JavaScript SPA](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="provide-consent-for-application-access"></a>Podanie zgody na dostęp do aplikacji

Przy pierwszym logowaniu do aplikacji, zostanie wyświetlony monit o wyrażenie zgody, aby umożliwić aplikacji dostęp do Twojego profilu i logowanie się w:

![Wyrażenie zgody na dostęp do aplikacji](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Wyświetl wyniki aplikacji
Po zalogowaniu, powinien zostać wyświetlony informacji o profilu użytkownika w **odpowiedzi wywołań interfejsu API programu Graph** pole.
 
![Oczekiwane wyniki z wywołania interfejsu API Microsoft Graph](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

Należy również wyświetlić podstawowe informacje o token, który został uzyskany w **Token dostępu** i **oświadczeń tokenu Identyfikatora** pola.

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Więcej informacji o zakresach i delegowane uprawnienia

Interfejsu API programu Microsoft Graph wymaga **user.read** zakresu na odczytywanie profilu użytkownika. Ten zakres jest automatycznie dodawany domyślnie każda aplikacja, która jest zarejestrowana w portalu rejestracji. Inne interfejsy API dla programu Microsoft Graph, a także niestandardowych interfejsów API dla serwera zaplecza, może być wymagane dodatkowe zakresy. Interfejsu API programu Microsoft Graph wymaga **Calendars.Read** zakres, aby wyświetlić listę kalendarzy użytkownika.

Aby uzyskać dostęp do kalendarzy użytkownika w kontekście aplikacji, należy dodać **Calendars.Read** delegowane uprawnienia do rejestrowania informacji o aplikacji. Następnie należy dodać **Calendars.Read** ograniczyć zakres do **acquireTokenSilent** wywołania. 

>[!NOTE]
>Użytkownik może być monitowany o dodatkowe zgody, jak zwiększyć liczbę zakresów.

Jeśli interfejs API zaplecza nie wymaga zakresu (niezalecane), możesz użyć **clientId** jako zasięg w **acquireTokenSilent** i **acquireTokenRedirect** wywołania.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]

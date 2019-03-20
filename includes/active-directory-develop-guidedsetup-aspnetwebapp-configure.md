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
ms.date: 05/04/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: cce0bb9d1a9317396d197d182a424a45c8448f1b
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203604"
---
## <a name="register-your-application"></a>Rejestrowanie aplikacji

Aby zarejestrować aplikację i Dodaj informacje dotyczące rejestracji aplikacji do rozwiązania, masz dwie opcje:

### <a name="option-1-express-mode"></a>Opcja 1: Tryb ekspresowy

Aplikację można szybko zarejestrować, wykonując następujące czynności:

1. Zarejestruj swoją aplikację za pośrednictwem [portalu rejestracji aplikacji Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure).
2. Wprowadź nazwę swojej aplikacji i poczty e-mail.
3. Upewnij się, że zaznaczono opcję instrukcje konfiguracji.
4. Postępuj zgodnie z instrukcjami, aby dodać adres URL przekierowania do aplikacji.

### <a name="option-2-advanced-mode"></a>Opcja 2: Tryb zaawansowany

Aby zarejestrować aplikację i dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:

1. Przejdź do [portalu rejestracji aplikacji firmy Microsoft](https://apps.dev.microsoft.com/portal/register-app), aby zarejestrować aplikację.
2. Wprowadź nazwę swojej aplikacji i poczty e-mail.
3. Upewnij się, że zaznaczono opcję instrukcje konfiguracji
4. Wybierz `Add Platform`, a następnie wybierz pozycję `Web`.
5. Przejdź do programu Visual Studio i, w Eksploratorze rozwiązań wybierz projekt i przyjrzyj się w oknie właściwości (Jeśli nie widzisz okna właściwości, naciśnij klawisz F4)
6. Zmienić włączone do protokołu SSL `True`.
7. Kliknij prawym przyciskiem myszy nad projektem w programie Visual Studio, a następnie wybierz **właściwości**i **Web** kartę. W *serwerów* sekcji zmiany *adres Url projektu* być adresem URL protokołu SSL.
8. Skopiuj adres URL protokołu SSL i dodać ten adres URL do listy adresy URL przekierowania w portalu rejestracji listę przekierowywanie adresów URL:<br/><br/>![Właściwości projektu](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
9. Dodaj następujący kod w `web.config` znajduje się w folderze głównym w sekcji `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

10. Zastąp `ClientId` identyfikatorem aplikacji, które właśnie zostało zarejestrowane.
11. Zastąp `redirectUri` za pomocą adresu URL SSL projektu.

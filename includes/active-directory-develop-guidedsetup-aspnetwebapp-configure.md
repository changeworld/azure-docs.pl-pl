---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: faa3ad2376935aee4508b814f1b67fdacb98cf6e
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48842867"
---
## <a name="register-your-application"></a>Rejestrowanie aplikacji

Aby zarejestrować aplikację i Dodaj informacje dotyczące rejestracji aplikacji do rozwiązania, masz dwie opcje:

### <a name="option-1-express-mode"></a>Opcja 1: Tryb ekspresowy

Aplikację można szybko zarejestrować, wykonując następujące czynności:

1. Zarejestruj swoją aplikację za pośrednictwem [portalu rejestracji aplikacji firmy Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure)
2.  Wprowadź nazwę swojej aplikacji i poczty e-mail
3.  Upewnij się, że jest zaznaczona opcja Instalatora z przewodnikiem
4.  Postępuj zgodnie z instrukcjami, aby dodać adres URL przekierowania do aplikacji

### <a name="option-2-advanced-mode"></a>Opcja 2: Tryb zaawansowany

Aby zarejestrować aplikację i dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:

1. Przejdź do [portalu rejestracji aplikacji Microsoft](https://apps.dev.microsoft.com/portal/register-app) do rejestrowania aplikacji
2. Wprowadź nazwę swojej aplikacji i poczty e-mail 
3. Upewnij się, że zaznaczono opcję instrukcje konfiguracji
4. Kliknij przycisk `Add Platform`, a następnie wybierz pozycję `Web`
5. Przejdź do programu Visual Studio i, w Eksploratorze rozwiązań wybierz projekt i przyjrzyj się w oknie właściwości (Jeśli nie widzisz okna właściwości, naciśnij klawisz F4)
6. Zmień opcję Włączony protokół SSL na `True`
7. Kliknij prawym przyciskiem myszy projekt w programie Visual Studio, a następnie wybierz **właściwości**i **Web** kartę. W *serwerów* sekcji zmiany *adres Url projektu* być adresem URL protokołu SSL
8. Skopiuj adres URL protokołu SSL i dodać ten adres URL do listy adresy URL przekierowania w portalu rejestracji listę przekierowywanie adresów URL:<br/><br/>![Właściwości projektu](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
9. Dodaj następujący kod w `web.config` znajduje się w folderze głównym w sekcji `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

10. Zastąp `ClientId` identyfikatorem aplikacji, które właśnie zostało zarejestrowane
11. Zastąp `redirectUri` za pomocą adresu URL SSL projektu


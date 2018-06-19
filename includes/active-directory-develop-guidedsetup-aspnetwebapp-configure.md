---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: f1dc23729f32a7a9535b887acf638cf5464c24bd
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "36206119"
---
## <a name="register-your-application"></a>Rejestrowanie aplikacji

Aby zarejestrować aplikację i dodać swoje informacje rejestracyjne aplikacji do rozwiązania, masz dwie opcje:

### <a name="option-1-express-mode"></a>Opcja 1: Tryb ekspresowy

Można szybko zarejestrować aplikację w następujący sposób:

1. Zarejestrować aplikację za pośrednictwem [portalu rejestracji aplikacji firmy Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure)
2.  Wprowadź nazwę aplikacji i poczty e-mail
3.  Upewnij się, że zaznaczono opcję instrukcje konfiguracji
4.  Postępuj zgodnie z instrukcjami, aby dodać adres URL przekierowania do aplikacji

### <a name="option-2-advanced-mode"></a>Opcja 2: Tryb zaawansowany

Aby zarejestrować aplikację i dodać swoje informacje rejestracyjne aplikacji do rozwiązania, wykonaj następujące czynności:

1. Przejdź do [portalu rejestracji aplikacji Microsoft](https://apps.dev.microsoft.com/portal/register-app) do rejestrowania aplikacji
2. Wprowadź nazwę aplikacji i poczty e-mail 
3. Upewnij się, że jest zaznaczona opcja instrukcje konfiguracji
4. Kliknij przycisk `Add Platform`, a następnie wybierz pozycję `Web`
5. Przejdź do programu Visual Studio i, w Eksploratorze rozwiązań wybierz projekt i przyjrzyj się okna właściwości (Jeśli nie widzisz okna właściwości, naciśnij klawisz F4)
6. Zmiany, aby włączyć protokół SSL `True`
7. Kliknij prawym przyciskiem myszy projekt w programie Visual Studio, a następnie wybierz pozycję **właściwości**i **Web** kartę. W *serwerów* sekcji zmiany *adres Url projektu* być adresem URL protokołu SSL
8. Skopiuj adres URL protokołu SSL i dodać ten adres URL do listy adresów URL przekierowań w portalu rejestracji listę adresów URL przekierowań:<br/><br/>![Właściwości projektu](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
9. Dodaj następujący kod w `web.config` znajduje się w folderze głównym w sekcji `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

10. Zastąp `ClientId` wraz z Identyfikatorem aplikacji został zarejestrowany
11. Zastąp `redirectUri` z adresem URL protokołu SSL projektu


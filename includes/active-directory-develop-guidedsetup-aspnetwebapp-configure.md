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
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 123a2ece06cb58ce6dbb35a914a87eb45fbcf5be
ms.sourcegitcommit: f24b62e352e0512dfa2897362021b42e0cb9549d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59505805"
---
## <a name="register-your-application"></a>Rejestrowanie aplikacji

Aby zarejestrować aplikację i Dodaj informacje dotyczące rejestracji aplikacji do rozwiązania, masz dwie opcje:

### <a name="option-1-express-mode"></a>Opcja 1: Tryb ekspresowy

Aplikację można szybko zarejestrować, wykonując następujące czynności:

1. Przejdź do nowego [witryna Azure portal — rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs) okienka.
1. Wprowadź nazwę aplikacji i kliknij pozycję **Zarejestruj**.
1. Postępuj zgodnie z instrukcjami, aby jednym kliknięciem pobrać i automatycznie skonfigurować nową aplikację.

### <a name="option-2-advanced-mode"></a>Opcja 2: Tryb zaawansowany

Aby ręcznie zarejestrować aplikację i dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:

1. Przejdź do programu Visual Studio oraz:
   - w Eksploratorze rozwiązań wybierz projekt i spójrz na okno właściwości, (Jeśli nie widzisz okna właściwości, naciśnij klawisz F4)
   - Zmienić włączone do protokołu SSL `True`.
   - Kliknij prawym przyciskiem myszy nad projektem w programie Visual Studio, a następnie wybierz **właściwości**i **Web** kartę. W *serwerów* sekcji zmiany *adres Url projektu* być adresem URL protokołu SSL.
   - Skopiuj adres URL protokołu SSL. Ten adres URL spowoduje dodanie do listy adresy URL przekierowania w portalu rejestracji listę adresy URL przekierowania w następnym kroku:<br/><br/>![Właściwości projektu](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
1. Przejdź do platforma tożsamości firmy Microsoft dla deweloperów [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) strony.
1. Wybierz **nowej rejestracji**.
1. Po wyświetleniu strony **Rejestrowanie aplikacji** podaj informacje dotyczące rejestracji aplikacji:
   - W sekcji **Nazwa** podaj znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład `ASPNET-Tutorial`.
   - Dodaj adres URL SSL skopiowany z programu Visual Studio w kroku 1 (na przykład `https://localhost:44368/`) w **adres URL odpowiedzi**i kliknij przycisk **zarejestrować**.
1. Wybierz menu **Uwierzytelnianie**, ustaw wartość **Tokeny identyfikatorów** w obszarze **Niejawne przyznanie**, a następnie wybierz pozycję **Zapisz**.
1. Dodaj następujący kod w `web.config` znajduje się w folderze głównym w sekcji `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. Zastąp `ClientId` identyfikatorem aplikacji, które właśnie zostało zarejestrowane.
1. Zastąp `redirectUri` za pomocą adresu URL SSL projektu.

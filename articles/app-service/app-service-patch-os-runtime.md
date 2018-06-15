---
title: System operacyjny i środowiska uruchomieniowego poprawki w usłudze Azure App Service | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak usługi Azure App Service aktualizacje systemu operacyjnego i środowisk uruchomieniowych i jak wprowadzasz aktualizacji anonsów.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: cephalin
ms.openlocfilehash: 92b6945ad13842e926d53be6dcc0d21554485ff3
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32151012"
---
# <a name="os-and-runtime-patching-in-azure-app-service"></a>System operacyjny i środowiska uruchomieniowego poprawki w usłudze Azure App Service

W tym artykule przedstawiono sposób pobrać niektóre informacje o wersji dotyczące systemu operacyjnego lub oprogramowania w [usługi aplikacji](app-service-web-overview.md). 

Usługi aplikacji jest platformy jako — usługa, co oznacza, że system operacyjny i stosu aplikacji są zarządzane dla Ciebie przez platformę Azure; można zarządzać tylko jego danych i aplikacji. Większa kontrola nad systemu operacyjnego i aplikacji stos jest dostępny w [maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/). Z tym pamiętać jest jednak przydatnych jako użytkownik usługi aplikacji, aby dowiedzieć się więcej informacji, takich jak:

-   Jak i kiedy są stosowane aktualizacje systemu operacyjnego?
-   Jak usługi aplikacji jest poprawiono przed lukami znaczących (np. 0 dni)
-   Które wersje systemu operacyjnego i środowiska uruchomieniowego uruchamiania aplikacji?

Ze względów bezpieczeństwa nie są publikowane niektórych szczegółowych informacji o zabezpieczeniach. Jednak artykuł ma na celu rozwiązanie problemów przez przezroczystość DSI oznacza optymalne wykorzystanie procesu i jak mogą pozostać aktualne na powiadomienia związane z zabezpieczeniami lub aktualizacji środowiska uruchomieniowego.

## <a name="how-and-when-are-os-updates-applied"></a>Jak i kiedy są stosowane aktualizacje systemu operacyjnego?

Azure zarządza poprawek systemu operacyjnego na dwa poziomy, serwerów fizycznych i maszyn wirtualnych gościa (maszyny wirtualne), systemem zasobów usługi aplikacji. Zarówno są aktualizowane co miesiąc, który jest wyrównywany do miesięcznych [wtorek poprawek](https://technet.microsoft.com/security/bulletins.aspx) harmonogramu. Te aktualizacje są stosowane automatycznie, w sposób zapewniający usług umowy SLA platformy Azure o wysokiej dostępności. 

Aby uzyskać szczegółowe informacje dotyczące sposobu stosowania aktualizacji, zobacz [Demystifying magic za aktualizacje systemu operacyjnego z usługi aplikacji](https://blogs.msdn.microsoft.com/appserviceteam/2018/01/18/demystifying-the-magic-behind-app-service-os-updates/).

## <a name="how-does-azure-deal-with-significant-vulnerabilities"></a>Jak Azure postępowania z znaczących luk w zabezpieczeniach?

Gdy poważny usterkami bezpośredniego stosowania poprawek, takich jak [luk w zabezpieczeniach dzień zero](https://wikipedia.org/wiki/Zero-day_(computing)), aktualizacje o wysokim priorytecie są obsługiwane w przypadku przez.

Bądź na bieżąco dzięki anonsów krytycznych na platformie Azure po przejściu na stronę [Azure Security Blog](https://azure.microsoft.com/blog/topics/security/). 

## <a name="when-are-supported-language-runtimes-updated-added-or-deprecated"></a>Gdy środowisk uruchomieniowych obsługiwanego języka aktualizacji, dodane lub przestarzałe?

Nowe wersje stabilna środowisk uruchomieniowych obsługiwanego języka (główne, niewielkie lub poprawka) okresowo zostają dodane do wystąpień usługi aplikacji. Niektóre aktualizacje zastąpić istniejącą instalację, podczas gdy inne są zainstalowane równolegle z istniejącymi wersjami. Zastąp instalacji oznacza, że aplikacja automatycznie działać na zaktualizowane środowiska wykonawczego. Instalacja side-by-side oznacza, że należy ręcznie przeprowadzić migrację aplikacji w taki sposób, aby móc korzystać z nowej wersji środowiska wykonawczego. Aby uzyskać więcej informacji zobacz jedną z podsekcjach.

Środowisko uruchomieniowe aktualizacji i deprecations są ogłaszane w tym miejscu:

- https://azure.microsoft.com/updates/?product=app-service 
- https://github.com/Azure/app-service-announcements/issues

> [!NOTE] 
> Tutaj informacje dotyczą środowiska wykonawczego języka, które są wbudowane w aplikacji usługi app Service. Niestandardowe środowiska uruchomieniowego przekazanymi do usługi aplikacji, na przykład pozostaje niezmieniony chyba, że należy ręcznie uaktualnić.
>
>

### <a name="new-patch-updates"></a>Nowe aktualizacje poprawki

Poprawek i aktualizacji do .NET, PHP, Java SDK lub wersji Tomcat/Jetty są automatycznie stosowane przez zastąpienie istniejącej instalacji przy użyciu nowej wersji. Zainstalowanych aktualizacji poprawki programu node.js równolegle z istniejącymi wersjami (podobnie jak wersje główne i pomocnicze w następnej sekcji). Python nowe wersje poprawki można zainstalować ręcznie za pomocą [lokacji rozszerzenia](https://www.siteextensions.net/packages?q=Tags%3A%22python%22)), obok wbudowanych instalacji języka Python.

### <a name="new-major-and-minor-versions"></a>Nowe wersje główne i pomocnicze

Po dodaniu nowej wersji mniejszym lub zainstalowano równolegle z istniejącymi wersjami. Aplikację można ręcznie uaktualnić do nowej wersji. Jeśli wersja środowiska uruchomieniowego jest skonfigurowany w pliku konfiguracji (takich jak `web.config` i `package.json`), musisz uaktualnić z tej samej metody. Jeśli używasz usługi aplikacji — ustawienie skonfigurować wersji środowiska uruchomieniowego, możesz je zmienić w [portalu Azure](https://portal.azure.com) lub uruchamiając [interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) w [powłoki chmury](../cloud-shell/overview.md), jako przedstawione w poniższych przykładach:

```azurecli-interactive
az webapp config set --net-framework-version v4.7 --resource-group <groupname> --name <appname>
az webapp config set --php-version 7.0 --resource-group <groupname> --name <appname>
az webapp config appsettings set --settings WEBSITE_NODE_DEFAULT_VERSION=8.9.3 --resource-group <groupname> --name <appname>
az webapp config set --python-version 3.4 --resource-group <groupname> --name <appname>
az webapp config set --java-version 1.8 --java-container Tomcat --java-container-version 9.0 --resource-group <groupname> --name <appname>
```

### <a name="deprecated-versions"></a>Przestarzałe wersji

Gdy starsza wersja jest przestarzały, Data usunięcia ogłoszenia tak, aby odpowiednio zaplanować uaktualnienie wersji środowiska wykonawczego. 

## <a name="how-can-i-query-os-and-runtime-update-status-on-my-instances"></a>Jak zbadać stan aktualizacji systemu operacyjnego i środowiska uruchomieniowego na mój wystąpień

Podczas kluczowych informacji systemu operacyjnego jest zablokowana z programu access (zobacz [funkcji systemu operacyjnego w usłudze Azure App Service](web-sites-available-operating-system-functionality.md)), [Kudu konsoli](https://github.com/projectkudu/kudu/wiki/Kudu-console) pozwala na zapytania dotyczące systemu operacyjnego wystąpienia usługi aplikacji Wersja i wersji środowiska wykonawczego. 

W poniższej tabeli przedstawiono sposób wersje systemu Windows i środowisko uruchomieniowe języka uruchamiania aplikacji:

| Informacje | Gdzie można znaleźć go |
|-|-|
| Wersja systemu Windows | Zobacz `https://<appname>.scm.azurewebsites.net/Env.cshtml` (w obszarze informacje o systemie) |
| Wersja platformy .NET | W `https://<appname>.scm.azurewebsites.net/DebugConsole`, uruchom następujące polecenie w wierszu polecenia: <br>`powershell -command "gci 'Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Net Framework Setup\NDP\CDF'"` |
| Wersja platformy .NET core | W `https://<appname>.scm.azurewebsites.net/DebugConsole`, uruchom następujące polecenie w wierszu polecenia: <br> `dotnet --version` |
| Wersja języka PHP | W `https://<appname>.scm.azurewebsites.net/DebugConsole`, uruchom następujące polecenie w wierszu polecenia: <br> `php --version` |
| Domyślną wersję środowiska Node.js | W [powłoki chmury](../cloud-shell/overview.md), uruchom następujące polecenie: <br> `az webapp config appsettings list --resource-group <groupname> --name <appname> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION']"` |
| Wersja języka Python | W `https://<appname>.scm.azurewebsites.net/DebugConsole`, uruchom następujące polecenie w wierszu polecenia: <br> `python --version` |

> [!NOTE]
> Dostęp do lokalizacji w rejestrze `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages`, gdzie informacji na temat [poprawek "KB"]((https://docs.microsoft.com/security-updates/SecurityBulletins/securitybulletins)) są przechowywane, jest zablokowana.
>
>

## <a name="more-resources"></a>Więcej zasobów

[Centrum zaufania: zabezpieczeń](https://www.microsoft.com/TrustCenter/Security/default.aspx)  
[64-bitowy platformy ASP.NET Core w usłudze Azure App Service](https://gist.github.com/glennc/e705cd85c9680d6a8f1bdb62099c7ac7)

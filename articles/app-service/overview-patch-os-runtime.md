---
title: System operacyjny i środowisko uruchomieniowe poprawek tempa — usłudze Azure App Service | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak usługa Azure App Service, aktualizacji systemu operacyjnego i środowisk uruchomieniowych i jak można uzyskać zaktualizować anonsów.
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
ms.custom: seodec18
ms.openlocfilehash: 086f5773a8ae4085a8e5bc0637bdebe5f2df4fb0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65203331"
---
# <a name="os-and-runtime-patching-in-azure-app-service"></a>Systemu operacyjnego i środowiska uruchomieniowego poprawki w usłudze Azure App Service

W tym artykule dowiesz się, jak uzyskać pewne informacje o wersji dotyczące systemu operacyjnego lub oprogramowania w [usługi App Service](overview.md). 

Usługa App Service jest Platform-as-a-Service, co oznacza, że systemu operacyjnego i aplikacji, stosu są zarządzane automatycznie przez platformę Azure; można zarządzać tylko jego danych i aplikacji. Większa kontrola nad systemu operacyjnego i aplikacji stos jest dostępny w [maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/). Mając to na uwadze mimo wszystko pomocne jest dla Ciebie jako użytkownik usługi App Service, aby dowiedzieć się więcej informacji, takich jak:

-   Jak i kiedy są stosowane aktualizacje systemu operacyjnego?
-   Jak usługi App Service jest poprawkami lukami znaczące (na przykład zero day)
-   Które wersje systemu operacyjnego i środowiska uruchomieniowego są uruchomione aplikacje?

Ze względów bezpieczeństwa nie są publikowane niektórych specyficznych opcji informacje o zabezpieczeniach. Jednak artykuł ma na celu złagodzenia wątpliwości, przejrzystości DSI oznacza optymalne wykorzystanie procesu i jak użytkownik może na bieżąco z ogłoszenia związane z zabezpieczeniami lub aktualizacje środowiska uruchomieniowego.

## <a name="how-and-when-are-os-updates-applied"></a>Jak i kiedy są stosowane aktualizacje systemu operacyjnego?

Platforma Azure zarządza stosowania poprawek systemu operacyjnego na dwóch poziomach, serwerów fizycznych i maszyn wirtualnych gościa (VM), które zasoby są uruchamiane w usłudze App Service. Oba są aktualizowane co miesiąc, która wyrównuje na wartość miesięczną [wtorek poprawek](https://technet.microsoft.com/security/bulletins.aspx) harmonogramu. Te aktualizacje są stosowane automatycznie, w sposób zapewniający wysoką dostępność usług umowy SLA platformy Azure. 

Aby uzyskać szczegółowe informacje dotyczące sposobu stosowania aktualizacji, zobacz [Demystifying magic za aktualizacje systemu operacyjnego usługi aplikacji](https://azure.github.io/AppService/2018/01/18/Demystifying-the-magic-behind-App-Service-OS-updates.html).

## <a name="how-does-azure-deal-with-significant-vulnerabilities"></a>Jak Azure postępowania z znaczne lukami w zabezpieczeniach?

Gdy poważnych usterek wymagają natychmiastowego wdrażania poprawek, takich jak [zero day luk w zabezpieczeniach](https://wikipedia.org/wiki/Zero-day_(computing)), aktualizacje o wysokim priorytecie są obsługiwane w przypadku przez.

Aktualne informacje o krytycznych ogłoszenia na platformie Azure, odwiedzając [Blog dotyczący zabezpieczeń platformy Azure](https://azure.microsoft.com/blog/topics/security/). 

## <a name="when-are-supported-language-runtimes-updated-added-or-deprecated"></a>Kiedy są środowisk uruchomieniowych obsługiwanych języków zaktualizowany, dodane lub przestarzałe?

Nowe wersje stabilne środowisk uruchomieniowych obsługiwanych języków (głównych, drobne lub poprawek) okresowo zostają dodane do wystąpień usługi App Service. Niektóre aktualizacje zastępują istniejącą instalację, podczas gdy inne są instalowane obok istniejących wersji. Instalacji Zastąp oznacza, że aplikacja działa automatycznie w systemie zaktualizowanego środowiska uruchomieniowego. Instalacja side-by-side oznacza, że należy ręcznie przeprowadzić migrację aplikacji w taki sposób, aby móc korzystać z nowej wersji środowiska uruchomieniowego. Aby uzyskać więcej informacji zobacz jeden z podsekcje.

Aktualizacje środowiska uruchomieniowego i deprecations obsłudze zostaną opublikowane w tym miejscu:

- https://azure.microsoft.com/updates/?product=app-service 
- https://github.com/Azure/app-service-announcements/issues

> [!NOTE] 
> W tym miejscu informacje mają zastosowanie do środowiska uruchomieniowe języków, które są wbudowane w aplikacji usługi App Service. Niestandardowego środowiska uruchomieniowego, które zostaną przesłane do usługi App Service, na przykład pozostaje bez zmian, chyba że ręcznie uaktualnić.
>
>

### <a name="new-patch-updates"></a>Nowe aktualizacje poprawek

Poprawki aktualizacji platformy .NET, PHP, zestawu SDK Java usługi lub wersja Tomcat/Jetty są stosowane automatycznie przez zastąpienie istniejącej instalacji nowej wersji. Node.js poprawek i aktualizacji są instalowane obok istniejących wersji (podobne do wersji głównych i pomocniczych w następnej sekcji). Python nowej poprawki wersji można zainstalować ręcznie za pomocą [rozszerzeń witryny](https://www.siteextensions.net/packages?q=Tags%3A%22python%22)), obok siebie przy użyciu wbudowanych instalacji języka Python.

### <a name="new-major-and-minor-versions"></a>Nowe wersje główne i pomocnicze

Po dodaniu nowej wersji mniejszym lub większym stopniu, jest on instalowany obok istniejących wersji. Można ręcznie uaktualnić aplikację do nowej wersji. Jeśli wersja środowiska uruchomieniowego jest skonfigurowany w pliku konfiguracji (takich jak `web.config` i `package.json`), musisz uaktualnić przy użyciu tej samej metody. Jeśli używasz usługi App Service ustawienie konfigurowania wersji środowiska uruchomieniowego, można zmienić w [witryny Azure portal](https://portal.azure.com) lub uruchamiając [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) polecenia w pliku [Cloud Shell](../cloud-shell/overview.md), jako pokazano w poniższych przykładach:

```azurecli-interactive
az webapp config set --net-framework-version v4.7 --resource-group <groupname> --name <appname>
az webapp config set --php-version 7.0 --resource-group <groupname> --name <appname>
az webapp config appsettings set --settings WEBSITE_NODE_DEFAULT_VERSION=8.9.3 --resource-group <groupname> --name <appname>
az webapp config set --python-version 3.4 --resource-group <groupname> --name <appname>
az webapp config set --java-version 1.8 --java-container Tomcat --java-container-version 9.0 --resource-group <groupname> --name <appname>
```

### <a name="deprecated-versions"></a>Przestarzałe wersji  

Gdy starsza wersja jest przestarzały, Data usunięcia jest ogłaszany tak, aby odpowiednio zaplanować uaktualnienie wersji środowiska uruchomieniowego. 

## <a name="how-can-i-query-os-and-runtime-update-status-on-my-instances"></a>Jak zbadać stan aktualizacji systemu operacyjnego i środowiska uruchomieniowego na Moje wystąpienia  

Gdy krytyczne informacje systemu operacyjnego jest zablokowane w z programu access (zobacz [funkcjonalność systemu operacyjnego w usłudze Azure App Service](operating-system-functionality.md)), [konsoli Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) oferuje możliwość zapytań wystąpienia usługi App Service dotyczące systemu operacyjnego Wersja i wersje środowiska uruchomieniowego. 

W poniższej tabeli przedstawiono sposób wersji systemu Windows i środowiska uruchomieniowego języka, które są uruchomione aplikacje:

| Informacje | Gdzie można go znaleźć | 
|-|-|
| Wersja systemu Windows | Zobacz `https://<appname>.scm.azurewebsites.net/Env.cshtml` (w obszarze informacje o systemie) |
| Wersja platformy .NET | W `https://<appname>.scm.azurewebsites.net/DebugConsole`, uruchom następujące polecenie w wierszu polecenia: <br>`powershell -command "gci 'Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Net Framework Setup\NDP\CDF'"` |
| Wersja platformy .NET core | W `https://<appname>.scm.azurewebsites.net/DebugConsole`, uruchom następujące polecenie w wierszu polecenia: <br> `dotnet --version` |
| Wersja języka PHP | W `https://<appname>.scm.azurewebsites.net/DebugConsole`, uruchom następujące polecenie w wierszu polecenia: <br> `php --version` |
| Domyślna wersja aparatu Node.js | W [Cloud Shell](../cloud-shell/overview.md), uruchom następujące polecenie: <br> `az webapp config appsettings list --resource-group <groupname> --name <appname> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION']"` |
| Wersja języka Python | W `https://<appname>.scm.azurewebsites.net/DebugConsole`, uruchom następujące polecenie w wierszu polecenia: <br> `python --version` |  

> [!NOTE]  
> Dostęp do lokalizacji w rejestrze `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages`, gdzie informacji na temat [poprawek "KB"](https://docs.microsoft.com/security-updates/SecurityBulletins/securitybulletins) są przechowywane, jest zablokowana.
>
>

## <a name="more-resources"></a>Więcej zasobów

[Centrum zaufania: zabezpieczenia](https://www.microsoft.com/en-us/trustcenter/security)  
[64-bitowy platformy ASP.NET Core w usłudze Azure App Service](https://gist.github.com/glennc/e705cd85c9680d6a8f1bdb62099c7ac7)

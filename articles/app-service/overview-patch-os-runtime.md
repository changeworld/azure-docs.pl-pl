---
title: Rytm łatania systemu operacyjnego i środowiska wykonawczego
description: Dowiedz się, jak usługa Azure App Service aktualizuje system operacyjny i środowiska wykonawcze, jakie środowiska wykonawcze i poziom poprawek mają aplikacje oraz jak można otrzymywać anonse aktualizacji.
ms.topic: article
ms.date: 02/02/2018
ms.custom: seodec18
ms.openlocfilehash: 597964914f4022899ab027b735ec6932105497b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273635"
---
# <a name="os-and-runtime-patching-in-azure-app-service"></a>Poprawki systemu operacyjnego i środowiska wykonawczego w usłudze Azure App Service

W tym artykule pokazano, jak uzyskać pewne informacje o wersji dotyczące systemu operacyjnego lub oprogramowania w [usłudze App Service](overview.md). 

Usługa app service jest platformą jako usługą, co oznacza, że system operacyjny i stos aplikacji są zarządzane przez platformę Azure; zarządzasz tylko aplikacją i jej danymi. Większa kontrola nad systemem operacyjnym i stosem aplikacji jest dostępna w [maszynach wirtualnych platformy Azure.](https://docs.microsoft.com/azure/virtual-machines/) Mając to na uwadze, jest jednak pomocne dla Ciebie jako użytkownika usługi App Service, aby dowiedzieć się więcej informacji, takich jak:

-   Jak i kiedy są stosowane aktualizacje systemu operacyjnego?
-   W jaki sposób usługa App Service jest łatana pod względem istotnych luk w zabezpieczeniach (takich jak zero-day)?
-   Które wersje systemu operacyjnego i środowiska wykonawczego są uruchomione aplikacje?

Ze względów bezpieczeństwa niektóre szczegóły dotyczące informacji zabezpieczających nie są publikowane. Jednak artykuł ma na celu złagodzenie problemów poprzez maksymalizację przejrzystości procesu i jak można być na bieżąco z komunikatami związanymi z zabezpieczeniami lub aktualizacjami środowiska uruchomieniowego.

## <a name="how-and-when-are-os-updates-applied"></a>Jak i kiedy są stosowane aktualizacje systemu operacyjnego?

Platforma Azure zarządza poprawkami systemu operacyjnego na dwóch poziomach, serwerów fizycznych i maszyn wirtualnych gościa, które uruchamiają zasoby usługi App Service. Oba są aktualizowane co miesiąc, co jest zgodne z miesięcznym harmonogramem [aktualizacji wtorek.](https://technet.microsoft.com/security/bulletins.aspx) Te aktualizacje są stosowane automatycznie, w sposób, który gwarantuje wysoką dostępność SLA usług platformy Azure. 

Aby uzyskać szczegółowe informacje na temat sposobu stosowania aktualizacji, zobacz [Demistyfikacja magii aktualizacji systemu operacyjnego usługi App Service](https://azure.github.io/AppService/2018/01/18/Demystifying-the-magic-behind-App-Service-OS-updates.html).

## <a name="how-does-azure-deal-with-significant-vulnerabilities"></a>Jak platforma Azure radzi sobie ze znacznymi lukami w zabezpieczeniach?

Gdy poważne luki wymagają natychmiastowego wprowadzania poprawek, takich jak [luki w zabezpieczeniach typu zero-day,](https://wikipedia.org/wiki/Zero-day_(computing))aktualizacje o wysokim priorytecie są obsługiwane indywidualnie dla każdego przypadku.

Bądź na bieżąco z krytycznymi ogłoszeniami dotyczącymi zabezpieczeń na platformie Azure, odwiedzając [blog dotyczący zabezpieczeń platformy Azure.](https://azure.microsoft.com/blog/topics/security/) 

## <a name="when-are-supported-language-runtimes-updated-added-or-deprecated"></a>Kiedy są obsługiwane środowiska wykonawcze języka aktualizowane, dodawane lub przestarzałe?

Nowe stabilne wersje obsługiwanych środowiska wykonawczego języka (główne, pomocnicze lub poprawki) są okresowo dodawane do wystąpień usługi App Service. Niektóre aktualizacje zastępują istniejącą instalację, podczas gdy inne są instalowane obok istniejących wersji. Instalacja zastępowanie oznacza, że aplikacja jest automatycznie uruchamiana w zaktualizowanym czasie wykonywania. Instalacja side-by-side oznacza, że należy ręcznie przeprowadzić migrację aplikacji, aby korzystać z nowej wersji środowiska uruchomieniowego. Aby uzyskać więcej informacji, zobacz jedną z podsekcji.

Aktualizacje środowiska uruchomieniowego i przestarzałe są ogłaszane tutaj:

- https://azure.microsoft.com/updates/?product=app-service 
- https://github.com/Azure/app-service-announcements/issues

> [!NOTE] 
> Informacje w tym miejscu dotyczy środowiska wykonawczego języka, które są wbudowane w aplikację usługi App Service. Niestandardowy środowiska uruchomieniowego, który można przekazać do usługi App Service, na przykład pozostaje bez zmian, chyba że ręcznie uaktualnić go.
>
>

### <a name="new-patch-updates"></a>Nowe aktualizacje poprawek

Aktualizacje poprawek do wersji .NET, PHP, Java SDK lub Tomcat/Jetty są stosowane automatycznie przez zastąpienie istniejącej instalacji nową wersją. Aktualizacje poprawek node.js są instalowane obok istniejących wersji (podobnie jak wersje główne i pomocnicze w następnej sekcji). Nowe wersje poprawek Pythona można zainstalować ręcznie za pośrednictwem [rozszerzeń lokacji,](https://azure.microsoft.com/blog/azure-web-sites-extensions/)obok wbudowanych instalacji Pythona.

### <a name="new-major-and-minor-versions"></a>Nowe wersje główne i pomocnicze

Po dodaniu nowej wersji głównej lub pomocniczej jest ono instalowane obok istniejących wersji. Możesz ręcznie uaktualnić aplikację do nowej wersji. Jeśli wersja środowiska uruchomieniowego została skonfigurowana `web.config` w `package.json`pliku konfiguracyjnym (na przykład i ), należy uaktualnić za pomocą tej samej metody. Jeśli do skonfigurowania wersji środowiska uruchomieniowego użyto ustawienia usługi App Service, można ją zmienić w [witrynie Azure portal](https://portal.azure.com) lub uruchamiając polecenie [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) w [usłudze Cloud Shell](../cloud-shell/overview.md), jak pokazano w poniższych przykładach:

```azurecli-interactive
az webapp config set --net-framework-version v4.7 --resource-group <groupname> --name <appname>
az webapp config set --php-version 7.0 --resource-group <groupname> --name <appname>
az webapp config appsettings set --settings WEBSITE_NODE_DEFAULT_VERSION=8.9.3 --resource-group <groupname> --name <appname>
az webapp config set --python-version 3.4 --resource-group <groupname> --name <appname>
az webapp config set --java-version 1.8 --java-container Tomcat --java-container-version 9.0 --resource-group <groupname> --name <appname>
```

### <a name="deprecated-versions"></a>Przestarzałe wersje  

Gdy starsza wersja jest przestarzała, data usunięcia jest ogłaszana, dzięki czemu można odpowiednio zaplanować uaktualnienie wersji środowiska uruchomieniowego. 

## <a name="how-can-i-query-os-and-runtime-update-status-on-my-instances"></a>Jak mogę zapytać o stan systemu operacyjnego i stanu aktualizacji środowiska uruchomieniowego w moich wystąpieniach?  

Gdy krytyczne informacje o systemie operacyjnym są zablokowane z dostępem (zobacz [funkcje systemu operacyjnego w usłudze Azure App Service), konsola](operating-system-functionality.md) [Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) umożliwia wykonywanie zapytań o wystąpienie usługi App Service dotyczące wersji systemu operacyjnego i wersji środowiska uruchomieniowego. 

W poniższej tabeli przedstawiono, jak w wersjach systemu Windows i w czasie wykonywania języka, w których są uruchomione aplikacje:

| Informacje | Gdzie go znaleźć | 
|-|-|
| Wersja systemu Windows | Zobacz `https://<appname>.scm.azurewebsites.net/Env.cshtml` (w obszarze Informacje o systemie) |
| wersja .NET | W `https://<appname>.scm.azurewebsites.net/DebugConsole`wierszu polecenia uruchom następujące polecenie: <br>`powershell -command "gci 'Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Net Framework Setup\NDP\CDF'"` |
| Wersja .NET Core | W `https://<appname>.scm.azurewebsites.net/DebugConsole`wierszu polecenia uruchom następujące polecenie: <br> `dotnet --version` |
| Wersja PHP | W `https://<appname>.scm.azurewebsites.net/DebugConsole`wierszu polecenia uruchom następujące polecenie: <br> `php --version` |
| Domyślna wersja node.js | W [usłudze Cloud Shell](../cloud-shell/overview.md)uruchom następujące polecenie: <br> `az webapp config appsettings list --resource-group <groupname> --name <appname> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION']"` |
| Wersja języka Python | W `https://<appname>.scm.azurewebsites.net/DebugConsole`wierszu polecenia uruchom następujące polecenie: <br> `python --version` |  
| Wersja środowiska Java | W `https://<appname>.scm.azurewebsites.net/DebugConsole`wierszu polecenia uruchom następujące polecenie: <br> `java -version` |  

> [!NOTE]  
> Dostęp do `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages`lokalizacji rejestru, w której przechowywane są informacje o [poprawkach "KB",](https://docs.microsoft.com/security-updates/SecurityBulletins/securitybulletins) jest zablokowany.
>
>

## <a name="more-resources"></a>Więcej zasobów

[Centrum zaufania: Zabezpieczenia](https://www.microsoft.com/en-us/trustcenter/security)  
[64-bitowy ASP.NET Core w usłudze Azure App Service](https://gist.github.com/glennc/e705cd85c9680d6a8f1bdb62099c7ac7)

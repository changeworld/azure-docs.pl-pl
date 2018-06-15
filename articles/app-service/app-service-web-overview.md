---
title: Przegląd usługi Web Apps | Microsoft Docs
description: Dowiedz się, w jaki sposób usługa Azure App Service ułatwia tworzenie i hostowanie aplikacji sieci Web
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 01/04/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 79828193e283f0dcb80035cae0c11b050a1639ea
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "33935657"
---
# <a name="web-apps-overview"></a>Przegląd usługi Web Apps

Usługa *Azure App Service Web Apps* (lub po prostu usługa Web Apps) to usługa do hostowania aplikacji internetowych, interfejsów API REST i zapleczy aplikacji mobilnych. Możesz programować w ulubionym języku — .NET, .NET Core, Java, Ruby, Node.js, PHP lub Python. Aplikacje można łatwo uruchamiać i skalować w środowiskach opartych na systemie Windows. W przypadku środowisk opartych na systemie Linux zobacz [Usługa App Service w systemie Linux](containers/app-service-linux-intro.md). 

Usługa Web Apps nie tylko dodaje do aplikacji możliwości platformy Microsoft Azure, takie jak zabezpieczenia, równoważenie obciążenia, automatyczne skalowanie i automatyczne zarządzanie. Umożliwia również korzystanie z funkcji metodyki DevOps, takich jak ciągłe wdrażanie z usług VSTS, GitHub, Docker Hub i innych źródeł, zarządzanie pakietami, środowiska przejściowe, domena niestandardowa i certyfikaty SSL. 

Korzystając z usługi App Service, płacisz tylko za faktycznie wykorzystane zasoby obliczeniowe platformy Azure. Używane zasoby obliczeniowe są określane przez _plan usługi App Service_, w ramach którego uruchamiasz usługę Web Apps. Aby uzyskać więcej informacji, zobacz [Plany usługi App Service w usłudze Azure Web Apps](azure-web-sites-web-hosting-plans-in-depth-overview.md).

## <a name="why-use-web-apps"></a>Dlaczego warto używać usługi Web Apps?

Poniżej przedstawiono kilka najważniejszych funkcji usługi Azure App Service Web Apps:

* **Wiele języków i platform** — usługa Web Apps zapewnia najwyższej jakości obsługę środowisk ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP i Python. Można również uruchamiać [program PowerShell oraz inne skrypty lub pliki wykonywalne](web-sites-create-web-jobs.md) jako usługi w tle.
* **Optymalizacja metodyki DevOps** — konfigurowanie [ciągłej integracji i wdrażania](app-service-continuous-deployment.md) za pomocą usług Visual Studio Team Services, GitHub, BitBucket, Docker Hub lub Azure Container Registry. Promowanie aktualizacji za pośrednictwem [środowisk testowych i przejściowych](web-sites-staged-publishing.md). Zarządzanie aplikacjami w usłudze Web Apps za pomocą programu [Azure PowerShell](/powershell/azureps-cmdlets-docs) lub [międzyplatformowego interfejsu wiersza polecenia](/cli/azure/install-azure-cli).
* **Globalne skalowanie i wysoka dostępność** — ręczne lub automatyczne skalowanie [w pionie](web-sites-scale.md) lub [w poziomie](../monitoring-and-diagnostics/insights-how-to-scale.md). Hostuj aplikacje w dowolnym miejscu globalnej infrastruktury centrum danych firmy Microsoft i korzystaj z wysokiej dostępności usługi App Service zapewnianej w umowie [SLA](https://azure.microsoft.com/support/legal/sla/app-service/).
* **Połączenia z platformami SaaS i danymi lokalnymi** — możliwość wyboru spośród ponad 50 [łączników](../connectors/apis-list.md) obsługujących systemy dla przedsiębiorstw (takie jak SAP), usługi SaaS (takie jak Salesforce) oraz serwisy internetowe (takie jak Facebook). Dostęp do danych lokalnych przy użyciu [połączeń hybrydowych](../biztalk-services/integration-hybrid-connection-overview.md) i [sieci wirtualnych platformy Azure](web-sites-integrate-with-vnet.md).
* **Bezpieczeństwo i zgodność** — usługa App Service jest [zgodna ze standardami ISO, SOC i PCI](https://www.microsoft.com/en-us/trustcenter). Uwierzytelnianie użytkowników za pomocą [usługi Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md) lub danych logowania z serwisów społecznościowych ([Google](app-service-mobile-how-to-configure-google-authentication.md), [Facebook](app-service-mobile-how-to-configure-facebook-authentication.md), [Twitter](app-service-mobile-how-to-configure-twitter-authentication.md) i [ Microsoft](app-service-mobile-how-to-configure-microsoft-authentication.md)). Utwórz [ograniczenia adresów IP](app-service-ip-restrictions.md) i [zarządzaj tożsamościami usługi](app-service-managed-service-identity.md).
* **Szablony aplikacji** — możliwość wyboru z obszernej listy szablonów aplikacji dostępnych w portalu [Azure Marketplace](https://azure.microsoft.com/marketplace/), takich jak WordPress, Joomla i Drupal.
* **Integracja z programem Visual Studio** —dedykowane narzędzia w programie Visual Studio usprawniają procesy tworzenia, wdrażania i debugowania.
* **Interfejs API i funkcje mobilne** — usługa Web Apps zapewnia gotową do użycia obsługę mechanizmu CORS w scenariuszach interfejsów API RESTful i upraszcza scenariusze aplikacji mobilnych dzięki włączeniu uwierzytelniania, synchronizacji danych w trybie offline, powiadomień wypychanych itd.
* **Kod niewymagający serwera** — możliwość uruchamiania fragmentów kodu lub skryptów na żądanie bez konieczności jawnego aprowizowania infrastruktury i zarządzania nią, dzięki czemu płacisz tylko za czas obliczeniowy faktycznie używany przez kod (zobacz [Usługa Azure Functions](/azure/azure-functions/)).

Oprócz usługi Web Apps w usłudze App Service platforma Azure oferuje inne usługi, które mogą służyć do hostowania witryn i aplikacji sieci Web. W przypadku większości scenariuszy najlepszym wyborem jest usługa Web Apps.  W przypadku architektury mikrousług należy rozważyć usługę [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric). W razie konieczności sprawowania większej kontroli nad maszynami wirtualnymi, na których działa kod, należy wziąć pod uwagę usługę [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/). Aby uzyskać więcej informacji o tym, jak dokonać wyboru między usługami Azure, zobacz [Azure App Service, Virtual Machines, Service Fabric, and Cloud Services comparison](choose-web-site-cloud-service-vm.md) (Porównanie usług Azure App Service, Virtual Machines, Service Fabric i Cloud Services).

## <a name="next-steps"></a>Następne kroki

Utwórz swoją pierwszą aplikację internetową.

> [!div class="nextstepaction"]
> [ASP.NET Core](app-service-web-get-started-dotnet.md)

> [!div class="nextstepaction"]
> [ASP.NET](app-service-web-get-started-dotnet-framework.md)

> [!div class="nextstepaction"]
> [PHP](app-service-web-get-started-php.md)

> [!div class="nextstepaction"]
> [Ruby (w systemie Linux)](containers/quickstart-ruby.md)

> [!div class="nextstepaction"]
> [Node.js](app-service-web-get-started-nodejs.md)

> [!div class="nextstepaction"]
> [Java](app-service-web-get-started-java.md)

> [!div class="nextstepaction"]
> [Python](app-service-web-get-started-python.md)

> [!div class="nextstepaction"]
> [HTML](app-service-web-get-started-html.md)

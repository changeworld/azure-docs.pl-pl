---
title: Uruchamianie kodu na domyślnych kontenerach Systemu Linux
description: Usługa Azure App Service może uruchamiać kod na wstępnie utworzonych kontenerach systemu Linux. Dowiedz się, jak możesz uruchamiać aplikacje internetowe systemu Linux na platformie Azure.
keywords: azure app service, linux, oss
author: msangapu-msft
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.topic: overview
ms.date: 1/11/2019
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: 27f085543869b1a77db9c97ca2e7ae7d3d3b7b88
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80046415"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Wprowadzenie do usługi Azure App Service w systemie Linux

[Azure App Service](../overview.md) to w pełni zarządzana platforma obliczeniowa zoptymalizowana pod kątem hostowania witryn i aplikacji internetowych. Klienci mogą używać usługi App Service w systemie Linux do natywnego hostowania aplikacji internetowych w systemie Linux dla obsługiwanych stosów aplikacji.

## <a name="languages"></a>Języki

Usługa App Service w systemie Linux obsługuje szereg wbudowanych obrazów w celu zwiększenia produktywności deweloperów. Języki: Node.js, Java (JRE 8 & JRE 11), PHP, Python, .NET Core i Ruby. Uruchom, [`az webapp list-runtimes --linux`](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes) aby wyświetlić najnowsze języki i obsługiwane wersje. Jeśli aplikacja wymaga środowiska uruchomieniowego, które nie jest obsługiwane we wbudowanych obrazach, skorzystaj z instrukcji dotyczących sposobu [tworzenia własnego obrazu platformy Docker](tutorial-custom-docker-image.md), aby wykonać wdrożenie w usłudze Web App for Containers.

## <a name="deployments"></a>Wdrożenia

* FTP
* Lokalna usługa Git
* GitHub
* Bitbucket

## <a name="devops"></a>DevOps

* Środowiska przejściowe
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-intro) i ciągła integracja/ciągłe dostarczanie w usłudze DockerHub

## <a name="console-publishing-and-debugging"></a>Konsola, publikowanie i debugowanie

* Środowiska
* Wdrożenia
* Podstawowa konsola
* Protokół SSH

## <a name="scaling"></a>Skalowanie

* Klienci mogą skalować aplikacje internetowe w górę i w dół, zmieniając warstwę w swoim [planie usługi App Service](https://docs.microsoft.com/azure/app-service/overview-hosting-plans?toc=%2fazure%2fapp-service-web%2ftoc.json)

## <a name="locations"></a>Lokalizacje

Sprawdź [Pulpit nawigacyjny stanu platformy Azure](https://azure.microsoft.com/status).

## <a name="limitations"></a>Ograniczenia

Witryna Azure Portal wyświetla tylko te funkcje, które obecnie działają dla usługi Web App for Containers. Kiedy włączymy więcej funkcji, będą one widoczne w portalu.

Usługa App Service w systemie Linux jest obsługiwana tylko z planami usługi aplikacji [Bezpłatna, Podstawowa, Standardowa i Premium](https://azure.microsoft.com/pricing/details/app-service/plans/) i nie ma [warstwy udostępnionej.](https://azure.microsoft.com/pricing/details/app-service/plans/) Nie można utworzyć aplikacji sieci Web systemu Linux w planie usługi app service, który już hostuje aplikacje internetowe inne niż Linux.  

Na podstawie bieżącego ograniczenia dla tej samej grupy zasobów nie można mieszać aplikacji systemu Windows i Linux w tym samym regionie.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

> [!NOTE]
> Istnieje nowa zintegrowana funkcja rejestrowania za pomocą [usługi Azure Monitoring (wersja zapoznawcza).](https://docs.microsoft.com/azure/app-service/troubleshoot-diagnostic-logs#send-logs-to-azure-monitor-preview) 
>
>

Jeśli nie można uruchomić aplikacji lub chcesz sprawdzić rejestrowanie z aplikacji, sprawdź dzienniki platformy Docker w katalogu LogFiles. Dostęp do tego katalogu można uzyskać za pomocą witryny funkcji SCM lub za pośrednictwem protokołu FTP. `stdout` Aby zarejestrować `stderr` i z kontenera, należy włączyć **rejestrowanie aplikacji** w obszarze **Dzienniki usługi app service**. To ustawienie jest stosowane od razu. Usługa App Service wykrywa zmianę i automatycznie uruchamia kontener.

Dostęp do witryny SCM można uzyskać za pomocą opcji **Narzędzia zaawansowane** w menu **Narzędzia programistyczne**.

![Wyświetlanie dzienników platformy Docker przy użyciu narzędzia Kudu][1]

## <a name="next-steps"></a>Następne kroki

Następujące artykuły ułatwią rozpoczęcie pracy z usługą App Service w systemie Linux w przypadku aplikacji internetowych napisanych w różnych językach:

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](https://docs.microsoft.com/azure/app-service/containers/quickstart-php)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)
* [Przejdź](quickstart-docker-go.md)
* [Aplikacje z wieloma kontenerami](quickstart-multi-container.md)

Aby uzyskać więcej informacji na temat usługi App Service dla systemu Linux, zobacz:

* [Usługa App Service dla systemu Linux — często zadawane pytania](app-service-linux-faq.md)
* [Obsługa protokołu SSH dla usługi App Service w systemie Linux](app-service-linux-ssh-support.md)
* [Konfigurowanie środowisk przejściowych w usłudze App Service](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Ciągłe wdrażanie w usłudze Docker Hub](app-service-linux-ci-cd.md)

Pytania i uwagi można zamieszczać na [naszym forum](https://docs.microsoft.com/answers/topics/azure-webapps.html).

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png

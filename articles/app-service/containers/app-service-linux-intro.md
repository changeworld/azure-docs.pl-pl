---
title: Wprowadzenie do usługi App Service w systemie Linux — Azure | Microsoft Docs
description: Dowiedz się więcej o usłudze Azure App Service w systemie Linux.
keywords: azure app service, linux, oss
services: app-service
documentationcenter: ''
author: msangapu-msft
manager: gwallace
editor: ''
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: overview
ms.date: 1/11/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 1bbcd5e4f8c6a429def84ad77d7dd93fa11b7324
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819685"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Wprowadzenie do usługi Azure App Service w systemie Linux

[Azure App Service](../overview.md) to w pełni zarządzana platforma obliczeniowa zoptymalizowana pod kątem hostowania witryn i aplikacji internetowych. Klienci mogą używać usługi App Service w systemie Linux do natywnego hostowania aplikacji internetowych w systemie Linux dla obsługiwanych stosów aplikacji.

## <a name="languages"></a>Języki

Usługa App Service w systemie Linux obsługuje szereg wbudowanych obrazów w celu zwiększenia produktywności deweloperów. Języki obejmują: Node. js, Java (JRE 8 & JRE 11), PHP, Python, .NET Core i Ruby. Uruchom [`az webapp list-runtimes --linux`](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes) , aby wyświetlić najnowsze Języki i obsługiwane wersje. Jeśli aplikacja wymaga środowiska uruchomieniowego, które nie jest obsługiwane we wbudowanych obrazach, skorzystaj z instrukcji dotyczących sposobu [tworzenia własnego obrazu platformy Docker](tutorial-custom-docker-image.md), aby wykonać wdrożenie w usłudze Web App for Containers.

## <a name="deployments"></a>Wdrożenia

* FTP
* Lokalna usługa Git
* Witryna GitHub
* Bitbucket

## <a name="devops"></a>DevOps

* Środowiska przejściowe
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-intro) i ciągła integracja/ciągłe dostarczanie w usłudze DockerHub

## <a name="console-publishing-and-debugging"></a>Konsola, publikowanie i debugowanie

* Środowiska
* Wdrożenia
* Podstawowa konsola
* SSH

## <a name="scaling"></a>Skalowanie

* Klienci mogą skalować aplikacje internetowe w górę i w dół, zmieniając warstwę w swoim [planie usługi App Service](https://docs.microsoft.com/azure/app-service/overview-hosting-plans?toc=%2fazure%2fapp-service-web%2ftoc.json)

## <a name="locations"></a>Lokalizacje

Sprawdź [Pulpit nawigacyjny stanu platformy Azure](https://azure.microsoft.com/status).

## <a name="limitations"></a>Ograniczenia

Witryna Azure Portal wyświetla tylko te funkcje, które obecnie działają dla usługi Web App for Containers. Kiedy włączymy więcej funkcji, będą one widoczne w portalu.

App Service w systemie Linux jest obsługiwana tylko w przypadku planów usługi App Service w warstwach [bezpłatna, podstawowa, standardowa i Premium](https://azure.microsoft.com/pricing/details/app-service/plans/) i nie ma warstwy [udostępnionej](https://azure.microsoft.com/pricing/details/app-service/plans/) . Nie można utworzyć aplikacji sieci Web systemu Linux w planie App Service, na którym jest już obsługiwany Web Apps w systemie innym niż Linux.  

W oparciu o bieżące ograniczenie dla tej samej grupy zasobów nie można mieszać aplikacji systemu Windows i Linux w tym samym regionie.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli nie można uruchomić aplikacji lub chcesz sprawdzić rejestrowanie z aplikacji, sprawdź dzienniki platformy Docker w katalogu LogFiles. Dostęp do tego katalogu można uzyskać za pomocą witryny funkcji SCM lub za pośrednictwem protokołu FTP. Aby rejestrować `stdout` i `stderr` z kontenera, należy włączyć **Rejestrowanie kontenerów platformy Docker** w obszarze **dzienniki App Service**. To ustawienie jest stosowane od razu. App Service wykrywa zmianę i automatycznie ponownie uruchamia kontener.

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
* [Go](quickstart-docker-go.md)
* [Aplikacje z wieloma kontenerami](quickstart-multi-container.md)

Aby uzyskać więcej informacji na temat usługi App Service dla systemu Linux, zobacz:

* [Usługa App Service dla systemu Linux — często zadawane pytania](app-service-linux-faq.md)
* [Obsługa protokołu SSH dla usługi App Service w systemie Linux](app-service-linux-ssh-support.md)
* [Konfigurowanie środowisk przejściowych w usłudze App Service](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Ciągłe wdrażanie w usłudze Docker Hub](app-service-linux-ci-cd.md)

Pytania i uwagi można zamieszczać na [naszym forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png

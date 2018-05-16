---
title: Więcej informacji na temat opcji obsługi sieci szkieletowej usług Azure | Dokumentacja firmy Microsoft
description: Bilety obsługi platformy Azure obsługiwane wersje klastra sieci szkieletowej usług i łącza do pliku
services: service-fabric
documentationcenter: .net
author: pkcsf
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/14/2018
ms.author: pkc
ms.openlocfilehash: 503bc07ff904dcb5952a13f12296f14fdd1999c0
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="azure-service-fabric-support-options"></a>Opcje pomocy technicznej usługi Azure Service Fabric

Aby zapewnić odpowiednią obsługę Twoje obciążenie pracą aplikacji uruchomionych na klastrów sieci szkieletowej usług, firma Microsoft zainstalować różne opcje. W zależności od poziomu wsparcia potrzebne i ważność problemu, Pobierz wybierz opcje prawego. 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Zgłoś problemy produkcji lub zażądać płatnej pomocy technicznej platformy Azure

Na zgłaszanie problemów w klastrze usługi sieć szkieletowa wdrożonego na platformie Azure, otwórz bilet pomocy technicznej [w portalu Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) lub [portalu pomocy technicznej firmy Microsoft](http://support.microsoft.com/oas/default.aspx?prid=16146).

Dowiedz się więcej o:
 
- [Obsługa firmy Microsoft Azure](https://azure.microsoft.com/support/plans/?b=16.44).
- [Premier pomocy technicznej firmy Microsoft](https://support.microsoft.com/en-us/premier).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Zgłoś problemy produkcji lub zażądać płatnej pomocy technicznej dla autonomicznego klastrów sieci szkieletowej usług

Raportowanie problemów w klastrze usługi sieć szkieletowa wdrożonych lokalnie lub w innych chmur, otwórz bilet pomocy technicznej professional na [portalu pomocy technicznej firmy Microsoft](http://support.microsoft.com/oas/default.aspx?prid=16146).

Dowiedz się więcej o:

- [Pomoc techniczna Professional firmy Microsoft dla lokalnego](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- [Premier pomocy technicznej firmy Microsoft](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Generuje raport sieć szkieletowa usług Azure 
Firma Microsoft skonfigurowano repozytorium GitHub, problemy z sieci szkieletowej usług raportowania.  Firma Microsoft są także aktywnie monitoruje następujące forach.

### <a name="github-repo"></a>Repozytorium GitHub 
Zgłoś problemy sieci szkieletowej usług Azure na [repozytorium git problemów dotyczących sieci szkieletowej usługi](https://github.com/Azure/service-fabric-issues). Tym repozytorium jest przeznaczony dla raportowania i śledzenia problemów z sieci szkieletowej usług Azure i wykonywania żądania małych funkcji. **Nie używaj to do raportu na żywo lokacji problemów**.

### <a name="stackoverflow-and-msdn-forums"></a>Fora StackOverflow i MSDN
[Tagu sieci szkieletowej usług w witrynie StackOverflow] [ stackoverflow] i [forum usługi Service Fabric w witrynie MSDN] [ msdn-forum] służą najlepsze zadać pytania dotyczące sposobu działania platformy i jak może wykonywać określone zadania z nim.

### <a name="azure-feedback-forum"></a>Azure forum opinii
[Forum opinii Azure dla platformy Service Fabric] [ uservoice-forum] najlepiej składania pomysły big funkcji ma produktu, jak firma Microsoft analizuje najpopularniejszych żądania są częścią naszego średnich planowanie długoterminowej. Firma Microsoft zachęca do rally obsługę sugestii w ramach społeczności.


## <a name="supported-service-fabric-versions"></a>Obsługiwane wersje usługi sieć szkieletowa usług.

Upewnij się, że klastra zawsze działa obsługiwana wersja usługi Service Fabric. Gdy firma Microsoft ogłaszamy wydanie programu nowa wersja usługi Service Fabric, poprzednia wersja jest oznaczony do zakończenia wsparcia po co najmniej 60 dni. Nowe wersje są ogłaszane [na blogu zespołu usługi sieć szkieletowa](https://blogs.msdn.microsoft.com/azureservicefabric/).

Można znaleźć w następujących dokumentach na więcej informacji na temat zachowania klastra uruchomiona obsługiwana wersja usługi Service Fabric.

- [Uaktualnienie wersji platformy Service Fabric w klastrze platformy Azure ](service-fabric-cluster-upgrade.md)
- [Uaktualnienie wersji platformy Service Fabric w klastrze serwerów autonomicznych systemu windows ](service-fabric-cluster-upgrade-windows-server.md)
 
Poniżej przedstawiono listę wersji platformy Service Fabric, które są obsługiwane i ich daty zakończenia wsparcia.

| **Środowisko uruchomieniowe usługi sieć szkieletowa w klastrze** | **Uaktualnienie można przeprowadzić bezpośrednio z klastra w wersji** |**Zestaw SDK zgodne / wersje pakietu NuGet** | **Obsługa Data zakończenia** |
| --- | --- |--- | --- |
| Wszystkie wersje klastra przed 5.3.121 | 5.1.158* |Mniejsze niż w wersji 2.3 |20 stycznia 2017 r. |
| 5.3.* | 5.1.158.* |Mniejsze niż w wersji 2.3 |24 lutego 2017 r. |
| 5.4.* | 5.1.158.* |Mniejsze niż lub równa wersji 2.4 |Może 10,2017       |
| 5.5.* | 5.4.164.* |Mniejsze niż wersja 2.5 |Sierpnia 10,2017    |
| 5.6.* | 5.4.164.* |Mniejsze niż w wersji 2.6 |Październik 13,2017   |
| 5.7.* | 5.4.164.* |Mniejsze niż w wersji 2.7 |Grudnia 15,2017  |
| 6.0.* | 5.6.205.* |Mniejsze niż wersja 2.8 |Marzec 30,2018     | 
| 6.1.* | 5.7.221.* |Mniejsze niż w wersji 3.0 |Lipiec 15,2018      |
| 6.2. * | 6.0.232.* |Mniejsze niż w wersji 3.1 |Bieżąca wersja i dlatego bez daty zakończenia |

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Usługi sieci szkieletowej Podgląd wersje — nieobsługiwany do użytku produkcyjnego.
Od czasu do czasu firma Microsoft wersji mających znaczących funkcji, którą chcemy udostępnić opinię, które są wydawane jako podglądów. Te wersje preview należy używać tylko do celów testowych. Klastra produkcyjnego zawsze powinna działać obsługiwanych, stabilna wersja sieci szkieletowej usług. Wersja zapoznawcza zawsze zaczyna się od numeru wersji głównej i pomocniczej 255. Na przykład jeśli widzisz usługi sieć szkieletowa wersji 255.255.5703.949 tej wersji jest tylko do użycia w klastrach testu i jest w wersji zapoznawczej. Te wersje zapoznawcze również są ogłaszane na [blog zespołu usługi sieć szkieletowa](https://blogs.msdn.microsoft.com/azureservicefabric) i będą zawierały wszystkie szczegółowe informacje o funkcjach dostępnych.

Nie ma żadnych opcji płatnej pomocy technicznej dla tych wersji zapoznawczej. Użyj jednej z opcji wymienionych w obszarze [wystawia sieć szkieletowa usług Azure raport](https://docs.microsoft.com/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues) aby zadać pytania lub wyrazić swoją opinię.

## <a name="next-steps"></a>Kolejne kroki

- [Uaktualnienie wersji sieci szkieletowej usług w klastrze platformy Azure ](service-fabric-cluster-upgrade.md)
- [Uaktualnienie wersji platformy Service Fabric w klastrze serwerów autonomicznych systemu windows ](service-fabric-cluster-upgrade-windows-server.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: http://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: http://aka.ms/servicefabricdocs
[sample-repos]: http://aka.ms/servicefabricsamples

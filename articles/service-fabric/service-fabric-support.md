---
title: Dowiedz się więcej o opcjach pomocy technicznej programu Azure Service Fabric | Dokumentacja firmy Microsoft
description: Bilety obsługi platformy Azure obsługiwane wersje klastra usługi Service Fabric oraz łącza do pliku
services: service-fabric
documentationcenter: .net
author: pkcsf
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: a931de8be07d41cf4daab63aa7691973ee158452
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60545065"
---
# <a name="azure-service-fabric-support-options"></a>Opcje pomocy technicznej usługi Azure Service Fabric

Aby zapewnić odpowiednią obsługę klastrów usługi Service Fabric, które są uruchomione Twoje obciążenie pracą aplikacji na, skonfigurowaliśmy różne opcje. W zależności od poziomu pomocy technicznej potrzebnych i wagę problemu, Pobierz i wybierz odpowiednie opcje. 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Zgłosić problemy w środowisku produkcyjnym lub poprosić płatnej pomocy technicznej dla platformy Azure

Do zgłaszania problemów dotyczących w klastrze usługi Service Fabric wdrożonego na platformie Azure, należy otworzyć bilet pomocy technicznej [w witrynie Azure portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) lub [portalu pomocy technicznej firmy Microsoft](https://support.microsoft.com/oas/default.aspx?prid=16146).

Dowiedz się więcej o usługach:
 
- [Pomocy technicznej firmy Microsoft dla platformy Azure](https://azure.microsoft.com/support/plans/?b=16.44).
- [Firmy Microsoft korzystający z pomocy technicznej](https://support.microsoft.com/en-us/premier).

> [!Note]
> Klastry z systemem w ramach warstwy niezawodności bronze umożliwiają uruchamianie tylko obciążeń testowych. Jeśli występują problemy z klastra działającego w bronze niezawodności, zespół pomocy technicznej firmy Microsoft pomoże Ci korygowanie problemu, ale nie będzie wykonywać analizy głównych przyczyn. Zapoznaj się [charakterystyka niezawodności klastra](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) Aby uzyskać więcej informacji.
>
> Aby uzyskać więcej informacji o tym, co jest wymagane dla produkcyjnego klastra można znaleźć [Lista kontrolna gotowości produkcji](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Zgłosić problemy w środowisku produkcyjnym lub poprosić płatnej pomocy technicznej dla autonomicznych, które klastrów usługi Service Fabric

Dla raportowania problemy w klastrze usługi Service Fabric wdrożone lokalnie lub w innych chmurach, otwórz bilet pomocy technicznej professional na [portalu pomocy technicznej firmy Microsoft](https://support.microsoft.com/oas/default.aspx?prid=16146).

Dowiedz się więcej o usługach:

- [Profesjonalnej pomocy technicznej firmy Microsoft dla środowiska lokalnego](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- [Firmy Microsoft korzystający z pomocy technicznej](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Generuje raport Azure Service Fabric

Skonfigurowaliśmy repozytorium GitHub dla zgłaszania problemów dotyczących usługi Service Fabric.  Firma Microsoft są także aktywnie monitoruje następujących forów.

### <a name="github-repo"></a>Repozytorium GitHub 

Zgłosić problemy dotyczące sieci szkieletowej usługi platformy Azure na [repozytorium git problemy dotyczące sieci szkieletowej usługi](https://github.com/Azure/service-fabric-issues). To repozytorium jest przeznaczony dla raportowania i śledzenia problemów z usługą Service Fabric i składania żądań małych funkcji. **Nie używaj to zgłaszanie problemów aktywnej witryny**.

### <a name="stackoverflow-and-msdn-forums"></a>Fora StackOverflow i MSDN

[Tag usługi Service Fabric w witrynie StackOverflow] [ stackoverflow] i [forum usługi Service Fabric w witrynie MSDN] [ msdn-forum] są używane do zadawania pytań o tym, jak najlepiej Platforma działa i jak można wykonać niektóre zadania z nim.

### <a name="azure-feedback-forum"></a>Forum opinii platformy Azure

[Forum opinii platformy Azure dla usługi Service Fabric] [ uservoice-forum] jest najlepszym miejscem, przesyłania, masz produktu, ponieważ omówimy najpopularniejszych żądań pomysły na funkcje big Data są częścią naszego medium do długoterminowego planowania. Firma Microsoft zachęca do rally obsługę Twoje sugestie społeczności.

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Wersji dodatku Service Fabric w wersji zapoznawczej — obsługiwane do użytku produkcyjnego

Od czasu do czasu wydania wersji, które mają znaczące funkcje żądanej opinii, które są wydawane jako wersje zapoznawcze. Te wersje (wersja zapoznawcza) można używać tylko do celów testowych. Klastrem produkcyjnym zawsze powinna być uruchomiona obsługiwanych, stabilna wersja usługi Service Fabric. Wersja zapoznawcza zawsze zaczyna się od numeru wersji głównych i pomocniczych 255. Na przykład jeśli Usługa Service Fabric w wersji 255.255.5703.949, tej wersji jest tylko wtedy, można użyć w przypadku klastrów testowych i jest w wersji zapoznawczej. Te wersje zapoznawcze również są ogłaszane na [blog zespołu usługi Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric) i będzie zawierał szczegółowe informacje o funkcjach dostępnych.
Nie ma żadnych opcji płatnej pomocy technicznej dla tych wersji (wersja zapoznawcza). Użyj jednej z opcji wymienionych w obszarze [generuje raport Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues) aby zadać pytania lub opinię.

## <a name="next-steps"></a>Kolejne kroki

[Obsługiwane wersje usługi Service Fabric](service-fabric-versions.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: https://aka.ms/servicefabricdocs
[sample-repos]: https://aka.ms/servicefabricsamples
---
title: Dowiedz się więcej o opcjach pomocy technicznej sieci szkieletowej usług Azure
description: Obsługiwane wersje klastra sieci szkieletowej usługi Azure i łącza do biletów pomocy technicznej plików
author: pkcsf
ms.topic: troubleshooting
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: 7494f0072f27f2c9b00db7070f19dfc05627eacf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282096"
---
# <a name="azure-service-fabric-support-options"></a>Opcje pomocy technicznej sieci szkieletowej usług Azure

Aby zapewnić odpowiednią obsługę klastrów sieci szkieletowej usług, na których są uruchomione obciążenia robocze aplikacji, firma We skonfigurowaniu różnych opcji dla Ciebie. W zależności od poziomu potrzebnej pomocy technicznej i ważności problemu możesz wybrać odpowiednie opcje. 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Zgłaszanie problemów z produkcją lub żądanie płatnej pomocy technicznej dla platformy Azure

Aby uzyskać informacje o problemach z klastrem sieci szkieletowej usług wdrożonym na platformie Azure, otwórz bilet do pomocy technicznej [w witrynie Azure portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) lub portalu pomocy technicznej firmy [Microsoft.](https://support.microsoft.com/oas/default.aspx?prid=16146)

Dowiedz się więcej o usługach:
 
- [Pomoc techniczna firmy Microsoft dla platformy Azure](https://azure.microsoft.com/support/plans/?b=16.44).
- [Microsoft premier wsparcie](https://support.microsoft.com/en-us/premier).

> [!Note]
> Klastry działające w warstwie niezawodności z brązu lub w klastrze z jednym węzłem umożliwiają uruchamianie tylko obciążeń testowych. Jeśli wystąpią problemy z klastrem działającym na brązowej niezawodności lub klastrze pojedynczego węzła, zespół pomocy technicznej firmy Microsoft pomoże ci w łagodzeniu problemu, ale nie wykona analizy głównej przyczyny. Aby uzyskać więcej informacji, zapoznaj się [z charakterystyką niezawodności klastra.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster)
>
> Aby uzyskać więcej informacji na temat tego, co jest wymagane dla klastra gotowego do produkcji, zapoznaj się z [listą kontrolną gotowości produkcyjnej.](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Zgłaszanie problemów z produkcją lub żądanie płatnej pomocy technicznej dla autonomicznych klastrów sieci szkieletowej usług

Aby uzyskać informacje o problemach z klastrem sieci szkieletowej usług wdrożonym lokalnie lub w innych chmurach, otwórz bilet do profesjonalnej pomocy technicznej w [portalu pomocy technicznej firmy Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Dowiedz się więcej o usługach:

- [Profesjonalna pomoc techniczna firmy Microsoft dla lokalnego](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0)programu .
- [Microsoft premier wsparcie](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Zgłaszanie problemów z siecią szkieletową usług Azure

Firma We skonfigurowaliśmy repozytorium GitHub do raportowania problemów z siecią szkieletową usług.  Aktywnie monitorujemy również następujące fora.

### <a name="github-repo"></a>Repo GitHub 

Zgłaszaj problemy z siecią szkieletową usług Azure na [temat problemów z repozytorium git.](https://github.com/Azure/service-fabric-issues) To repozytorium jest przeznaczone do raportowania i śledzenia problemów z usługą Azure Service Fabric i do tworzenia małych żądań funkcji. **Nie używaj tego do zgłaszania problemów z witryną na żywo**.

### <a name="stackoverflow-and-msdn-forums"></a>StackOverflow i fora MSDN

[Tag sieci szkieletowej usług na StackOverflow][stackoverflow] i [forum sieci szkieletowej usług w msdn][msdn-forum] są najlepiej używane do zadawania pytań dotyczących sposobu działania platformy i jak można wykonać niektóre zadania z nim.

### <a name="azure-feedback-forum"></a>Forum opinii platformy Azure

[Forum opinii platformy Azure dla sieci szkieletowej usług][uservoice-forum] jest najlepszym miejscem do przesyłania pomysłów na duże funkcje dla produktu, ponieważ przeglądamy najpopularniejsze żądania są częścią naszego planowania średnio- i długoterminowego. Zachęcamy do zebrania poparcia dla swoich sugestii w społeczności.

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Wersje w wersji zapoznawczej sieci szkieletowej usług — nieobsługiwały do użytku produkcyjnego

Od czasu do czasu wydajemy wersje, które mają istotne funkcje, na których chcemy opinie, które są wydawane jako wersje zapoznawcza. Te wersje w wersji zapoznawczej powinny być używane tylko do celów testowych. Klaster produkcyjny powinien zawsze działać w obsługiwanej, stabilnej wersji sieci szkieletowej usług. Wersja zapoznawcza zawsze zaczyna się od głównego i pomocniczego numeru wersji 255. Na przykład jeśli widzisz sieci szkieletowej usług w wersji 255.255.5703.949, tej wersji jest tylko do użycia w klastrach testowych i jest w wersji zapoznawczej. Te wersje zapoznawcza są również ogłaszane w [blogu zespołu sieci szkieletowej usług](https://blogs.msdn.microsoft.com/azureservicefabric) i będą miały szczegółowe informacje na temat funkcji.
Nie ma opcji płatnej pomocy technicznej dla tych wersji zapoznawczych. Użyj jednej z opcji wymienionych w obszarze [Zgłaszanie problemów z siecią szkieletową usług Azure,](https://docs.microsoft.com/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues) aby zadawać pytania lub przekazywać opinie.

## <a name="next-steps"></a>Następne kroki

[Obsługiwane wersje sieci szkieletowej usług](service-fabric-versions.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: https://aka.ms/servicefabricdocs
[sample-repos]: https://aka.ms/servicefabricsamples

---
title: Scenariusze i wzorców usługi sieć szkieletowa usług Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, najlepsze rozwiązania i sprawdzonych wzorców wielokrotnego użytku, projektowanie, tworzenie i obsługi Twojej mikrousług w sieci szkieletowej usług.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: d5aa75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2018
ms.author: ryanwi
ms.openlocfilehash: 5c2cd38001a9645011bbab84cff3b9366aa64b1d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206144"
---
# <a name="service-fabric-patterns-and-scenarios"></a>Scenariusze i wzorce sieci szkieletowej usług
Jeśli rozważasz usługę kompilowania dużych mikrousług przy użyciu usługi Azure Service Fabric, Dowiedz się od ekspertów, którzy zaprojektowany i zbudowany ta platforma jako usługa (PaaS). Wprowadzenie do architektury właściwe, a następnie Dowiedz się, jak zoptymalizować zasobów dla aplikacji. [Usługi sieć szkieletowa Patterns and Practices](https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=mudwqISGD_6005167344) plan odpowiedzi na pytania najczęściej zadawane przez klientów rzeczywistych scenariuszy sieci szkieletowej usług i aplikacji.
 
Dowiedz się, jak projektować i opracowywać mikrousługi oraz korzystać z nich w usłudze Service Fabric, korzystając z najlepszych rozwiązań i sprawdzonych wzorców z możliwością ponownego użycia. Zapoznaj się z omówieniem usługi sieć szkieletowa usług, a następnie głębokość Poznaj tematów, które obejmują optymalizacji klastra i zabezpieczeń, migrowanie starszych aplikacji IoT na dużą skalę, hosting aparaty gier i inne. Obejrzyj ciągłego dostarczania dla różnych obciążeń i nawet uzyskać szczegółowe informacje o pomocy technicznej systemu Linux i kontenerów. 

## <a name="introduction"></a>Wprowadzenie
Eksploruj najlepszych rozwiązań i Dowiedz się więcej o wybieraniu platforma jako usługa (PaaS) przez infrastrukturę jako usługę (IaaS). Szczegółowe informacje o następujących zasadach projektowania sprawdzonych aplikacji.

<table><tr><th>Połączenia wideo</th><th>PowerPoint talii</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=N2KwbbSGD_6405167344">
<img src="./media/service-fabric-patterns-and-scenarios/intro.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=mudwqISGD_6005167344">Wprowadzenie do sieci szkieletowej usług</a></td></tr>
</table>

## <a name="cluster-planning-and-management"></a>Planowanie klastra i zarządzania
Więcej informacji na temat planowania pojemności, optymalizacja klastra i zabezpieczenia klastra, w tym przyjrzeć sieć szkieletowa usług Azure.

<table><tr><th>Połączenia wideo</th><th>PowerPoint talii</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=cyDYZcSGD_2805167344">
<img src="./media/service-fabric-patterns-and-scenarios/cluster.png" WIDTH="360" HEIGHT="244">
</a></td><td> <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=E5B3nJSGD_805167344">Planowanie klastra i zarządzania</a></td></tr>
</table>

## <a name="hyper-scale-web"></a>Funkcji Hyper skali sieci web
Przegląd pojęć wokół hiperskali sieci web, w tym dostępności i niezawodności, hiperskali i zarządzanie stanem.

<table><tr><th>Połączenia wideo</th><th>PowerPoint talii</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=NgldAdSGD_405167344">
<img src="./media/service-fabric-patterns-and-scenarios/hyperscaleweb.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=CPMLBLSGD_7705167344">Funkcji Hyper skali sieci web</a></td></tr>
</table>

## <a name="iot"></a>IoT
Poznaj Internetu rzeczy (IoT) w kontekście sieci szkieletowej usług Azure, w tym potoku, obsługi wielu dzierżawców i IoT Azure IoT na dużą skalę.

<table><tr><th>Połączenia wideo</th><th>PowerPoint talii</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=naFUVeSGD_1505167344">
<img src="./media/service-fabric-patterns-and-scenarios/iot.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=kfqFWMSGD_6205167344">IoT</a></td></tr>
</table>

## <a name="gaming"></a>Gry
Spójrz na Włącz gier, interaktywne gry i hostingu istniejących aparaty gier.

<table><tr><th>Połączenia wideo</th><th>PowerPoint talii</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=6wECzeSGD_3805167344">
<img src="./media/service-fabric-patterns-and-scenarios/gaming.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=kfqFWMSGD_6205167344">Gry</a></td></tr>
</table>

## <a name="continuous-delivery"></a>Ciągłe dostarczanie
Poznaj pojęcia, włącznie z ciągłej integracji/ciągłego dostarczania z Visual Studio Team Services, kompilacji/pakowania/publikowania przepływu pracy, środowisko instalacji i udziału pakietu usług.

<table><tr><th>Połączenia wideo</th><th>PowerPoint talii</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=78h5ofSGD_305167344">
<img src="./media/service-fabric-patterns-and-scenarios/cd.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=VlENvOSGD_105167344">Ciągłego dostarczania</a></td></tr>
</table>

## <a name="migration"></a>Migracja
Więcej informacji na temat migracji z usługi w chmurze, oprócz migracji starsze aplikacje.

<table><tr><th>Połączenia wideo</th><th>PowerPoint talii</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=hd1cMgSGD_5605167344">
<img src="./media/service-fabric-patterns-and-scenarios/migration.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=GQAq4QSGD_8305167344">Migracji</a></td></tr>
</table>

## <a name="containers-and-linux-support"></a>Kontenery i obsługi systemu Linux
Uzyskaj odpowiedzi na pytanie "Dlaczego kontenery?" Więcej informacji na temat podglądu kontenery systemu Windows, Linux obsługuje i aranżacji kontenery systemu Linux. Ponadto dowiedzieć się, jak przeprowadzić migrację aplikacji .NET Core do systemu Linux.

<table><tr><th>Połączenia wideo</th><th>PowerPoint talii</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=V1ERJhSGD_305167344">
<img src="./media/service-fabric-patterns-and-scenarios/containers.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=mlYsZRSGD_2105167344">Kontenery i obsługi systemu Linux</a></td></tr>
</table>

## <a name="next-steps"></a>Kolejne kroki
Teraz, kiedy znasz już o wzorce sieci szkieletowej usług i scenariusze, Dowiedz się więcej o sposobie [tworzenia i zarządzania klastrami](service-fabric-deploy-anywhere.md), [migracji aplikacji usługi w chmurze do sieci szkieletowej usług](service-fabric-cloud-services-migration-worker-role-stateless-service.md), [— Konfiguracja ciągłego dostarczania](service-fabric-tutorial-deploy-app-with-cicd-vsts.md), i [wdrażanie kontenerów](service-fabric-containers-overview.md).

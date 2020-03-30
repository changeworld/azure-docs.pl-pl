---
title: Architektura Menedżera zasobów
description: Omówienie informacji i architektury usługi Azure Service Fabric Cluster Resource Manager.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 94ed906533d108081d620e9b183ecfee249d85ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551696"
---
# <a name="cluster-resource-manager-architecture-overview"></a>Omówienie architektury menedżera zasobów klastra
Menedżer zasobów klastra sieci szkieletowej usług to usługa centralna uruchamiana w klastrze. Zarządza żądanym stanem usług w klastrze, szczególnie w odniesieniu do zużycia zasobów i wszelkich reguł umieszczania. 

Aby zarządzać zasobami w klastrze, Menedżer zasobów klastra sieci szkieletowej usług musi zawierać kilka informacji:

- Jakie usługi obecnie istnieją
- Bieżące (lub domyślne) zużycie zasobów każdej usługi 
- Pozostała pojemność klastra 
- Pojemność węzłów w klastrze 
- Ilość zasobów zużytych w każdym węźle

Zużycie zasobów danej usługi może się zmieniać wraz z czasem, a usługi zwykle dbają o więcej niż jeden typ zasobu. W różnych usługach mogą być mierzone zarówno rzeczywiste zasoby fizyczne, jak i fizyczne. Usługi mogą śledzić metryki fizyczne, takie jak zużycie pamięci i dysku. Częściej usługi mogą dbać o metryki logiczne - takie rzeczy jak "WorkQueueDepth" lub "TotalRequests". Metryki logiczne i fizyczne mogą być używane w tym samym klastrze. Metryki mogą być współużytkowane przez wiele usług lub być specyficzne dla określonej usługi.

## <a name="other-considerations"></a>Inne zagadnienia
Właściciele i operatorzy klastra mogą różnić się od autorów usług i aplikacji lub co najmniej są tymi samymi osobami noszącymi różne kapelusze. Podczas tworzenia aplikacji wiesz kilka rzeczy o tym, co wymaga. Masz oszacowanie zasobów, które będą zużywać i jak różne usługi powinny być wdrażane. Na przykład warstwa sieci web musi działać w węzłach narażonych na Internet, podczas gdy usługi bazy danych nie powinny. Innym przykładem usługi sieci web są prawdopodobnie ograniczone przez procesor i sieć, podczas gdy usługi warstwy danych bardziej dbają o zużycie pamięci i dysku. Jednak osoba obsługująca zdarzenie lokacji na żywo dla tej usługi w produkcji lub która zarządza uaktualnieniem do usługi ma inne zadanie do wykonania i wymaga różnych narzędzi. 

Zarówno klaster, jak i usługi są dynamiczne:

- Liczba węzłów w klastrze może rosnąć i zmniejszać
- Węzły o różnych rozmiarach i typach mogą przyjść i odejść
- Usługi można tworzyć, usuwać i zmieniać żądane alokacje zasobów i reguły umieszczania
- Uaktualnienia lub inne operacje zarządzania mogą toczyć się za pośrednictwem klastra w aplikacji na poziomach infrastruktury
- Awarie mogą się zdarzyć w każdej chwili.

## <a name="cluster-resource-manager-components-and-data-flow"></a>Składniki Menedżera zasobów klastra i przepływ danych
Menedżer zasobów klastra musi śledzić wymagania każdej usługi i zużycie zasobów przez każdy obiekt usługi w ramach tych usług. Menedżer zasobów klastra ma dwie części koncepcyjne: agentów, które działają w każdym węźle i usługi odporne na uszkodzenia. Agenci w każdym węźle śledzą raporty obciążenia z usług, agregują je i okresowo zgłaszają. Usługa Menedżer zasobów klastra agreguje wszystkie informacje od agentów lokalnych i reaguje na podstawie bieżącej konfiguracji.

Spójrzmy na następujący diagram:

<center>

![Architektura bilansicza zasobów][Image1]
</center>

W czasie wykonywania istnieje wiele zmian, które mogą się zdarzyć. Załóżmy na przykład, że ilość zasobów, które niektóre usługi zużywają zmiany, niektóre usługi nie powiodą się, a niektóre węzły dołączają i opuszczają klaster. Wszystkie zmiany w węźle są agregowane i okresowo wysyłane do usługi Menedżer zasobów klastra (1,2), gdzie są ponownie agregowane, analizowane i przechowywane. Co kilka sekund usługa analizuje zmiany i określa, czy konieczne są jakieś działania (3). Na przykład można zauważyć, że niektóre puste węzły zostały dodane do klastra. W rezultacie postanawia przenieść niektóre usługi do tych węzłów. Menedżer zasobów klastra może również zauważyć, że określony węzeł jest przeciążony lub że niektóre usługi nie powiodły się lub zostały usunięte, zwalniając zasoby w innym miejscu.

Spójrzmy na poniższy diagram i zobaczmy, co dzieje się dalej. Załóżmy, że Menedżer zasobów klastra określa, że zmiany są konieczne. Koordynuje z innymi usługami systemowymi (w szczególności Menedżer pracy awaryjnej) w celu dokonania niezbędnych zmian. Następnie niezbędne polecenia są wysyłane do odpowiednich węzłów (4). Załóżmy na przykład, że Menedżer zasobów zauważył, że węzeł5 był przeciążony i dlatego postanowił przenieść usługę B z węzła 5 do węzła4. Po zakończeniu ponownej konfiguracji (5) klaster wygląda następująco:

<center>

![Architektura bilansicza zasobów][Image2]
</center>

## <a name="next-steps"></a>Następne kroki
- Menedżer zasobów klastra ma wiele opcji opisu klastra. Aby dowiedzieć się więcej na ich temat, zapoznaj się z tym [artykułem opisującym klaster sieci szkieletowej usług](./service-fabric-cluster-resource-manager-cluster-description.md)
- Podstawowymi obowiązkami Menedżera zasobów klastra są ponowne zrównoważenie klastra i wymuszanie reguł umieszczania. Aby uzyskać więcej informacji na temat konfigurowania tych zachowań, zobacz [równoważenie klastra sieci szkieletowej usług](./service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png

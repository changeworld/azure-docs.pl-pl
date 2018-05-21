---
title: Architektura usługi Resource Manager | Dokumentacja firmy Microsoft
description: Przegląd architektury programu usługi sieć szkieletowa klastra Menedżera zasobów.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 6c4421f9-834b-450c-939f-1cb4ff456b9b
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 48da92be0eef1154b490fb4829363598d6d66569
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="cluster-resource-manager-architecture-overview"></a>Przegląd architektury Menedżera zasobów klastra
Menedżer zasobów klastra sieci szkieletowej usług jest usługą centralnej, która działa w klastrze. Zarządza żądanego stanu usługi w klastrze, szczególnie w odniesieniu do zużycia zasobów i wszystkie reguły umieszczania. 

Do zarządzania zasobami w klastrze, Menedżer zasobów klastra sieci szkieletowej usług wymaga kilku informacji:

- Usługi, które obecnie istnieje.
- Każda usługa bieżącej (lub domyślna) zużycie zasobów 
- Pozostałe zasoby klastra 
- Pojemność węzłów w klastrze 
- Ilość zasobów używanych w każdym węźle

Zużycie zasobów w danej usługi mogą ulec zmianie, a usługi zazwyczaj się o więcej niż jednego typu zasobu. W różnych usługach może być rzeczywistym fizyczne i fizyczne zasoby mierzony. Usługi mogą śledzić metryki fizycznych, takich jak użycie pamięci i dysku. Zazwyczaj usług mogą interesujących logicznej metryki - np. "WorkQueueDepth" lub "TotalRequests". Metryki zarówno logicznych, jak i fizycznych można w tym samym klastrze. Metryki mogą być współużytkowane przez wiele usług lub być specyficzne dla określonej usługi.

## <a name="other-considerations"></a>Inne zagadnienia
Właściciele i operatory klastra może się różnić od autorów usług i aplikacji lub co najmniej są tego samego trwałych Kapelusze innej osoby. Podczas opracowywania aplikacji znasz kilka rzeczy, o co wymaga. Masz szacunkową go będą korzystać z zasobów i jak powinny być wdrażane różnych usług. Na przykład warstwa sieci web muszą zostać uruchomione na połączenie z Internetem, gdy usługi bazy danych nie powinien węzłów. Inny przykład usługi sieci web prawdopodobnie są ograniczone przez procesor CPU i sieci, podczas opieki danych warstwy usług więcej informacji na temat zużycia pamięci i dysku. Jednak osoba obsługi zdarzenia na żywo lokacji, dla tej usługi w środowisku produkcyjnym lub który zarządza uaktualnienia do usługi ma różne zadania w celu i wymagają różnych narzędzi. 

Klaster i usług są dynamiczne:

- Liczba węzłów w klastrze, można zwiększyć lub zmniejszyć
- Węzły o różnych rozmiarach i typy mogą pochodzić i przejść
- Usługi można utworzyć, usunąć i zmiany ich zasobem alokacji i reguły umieszczania
- Uaktualnienia lub innych operacji zarządzania można przywracać za pomocą klastra z aplikacji na poziomach infrastruktury
- Błędy możliwe, w dowolnym momencie.

## <a name="cluster-resource-manager-components-and-data-flow"></a>Składniki Menedżera zasobów klastra i przepływu danych
Menedżer zasobów klastra musi śledzić wymagania poszczególnych usług i zużycia zasobów przez każdy z obiektów usługi w tych usług. Menedżer zasobów klastra ma dwie części koncepcyjnego: agentów uruchomionych na każdym węźle i usługą odpornej na uszkodzenia. Agentów na każdy węzeł Śledź obciążenia raporty z usług, łączny je i okresowo Raportuj je. Usługa Menedżera zasobów klastra agregowania wszystkich danych z lokalnego agentów i reaguje na podstawie bieżącej konfiguracji.

Oto na poniższym diagramie:

<center>
![Architektura usługi równoważenia zasobów][Image1]
</center>

W czasie wykonywania istnieje wiele zmian, które mogą wystąpić. Na przykład załóżmy powiedz ilości zasobów używanych przez korzystać z niektórych usług zmienia niektórych usług kończyć się niepowodzeniem i niektóre węzły sprzężenia i pozostaw klastra. Wszystkie zmiany w węźle są agregowane i okresowo wysyłane do usługi Menedżer zasobów klastra (1,2), gdzie są agregowane ponownie, analizy i przechowywane. Co kilka sekund, które usługa sprawdza zmiany i określa, czy wszystkie akcje są niezbędne (3). Na przykład można zauważyć, że niektóre węzły pusty zostały dodane do klastra. W związku z tym zdecyduje się przenieść niektórych usług do tych węzłów. Menedżer zasobów klastra można także zauważyć, czy określony węzeł jest przeciążony lub że pewnych usług ma nie powiodło się lub zostało usunięte, zwolnić zasoby w innym miejscu.

Załóżmy Spójrz na poniższym diagramie i zobacz, co dalej. Załóżmy, że Menedżer zasobów klastra Określa, czy konieczne jest wprowadzenie zmian. Współrzędne go z innymi usługami systemu (w szczególności menedżera trybu Failover) do wprowadź niezbędne zmiany. Następnie niezbędne polecenia są wysyłane do odpowiednich węzłów (4). Załóżmy na przykład, że Menedżer zasobów zauważyć Węzeł5 przeciążony i dlatego zdecydowała się przenieść usługi B z Węzeł5 Węzeł4. Na koniec ponownej konfiguracji (5) klastra wygląda następująco:

<center>
![Architektura usługi równoważenia zasobów][Image2]
</center>

## <a name="next-steps"></a>Kolejne kroki
- Menedżer zasobów klastra ma wiele opcji opisujące klastra. Aby dowiedzieć się więcej na ich temat, zapoznaj się w tym artykule na [opisujące klastra sieci szkieletowej usług](./service-fabric-cluster-resource-manager-cluster-description.md)
- Menedżer zasobów klastra podstawowego obowiązki są ponowne równoważenie klastra i wymuszanie reguły umieszczania. Aby uzyskać więcej informacji na temat konfigurowania te zachowania, zobacz [równoważenia klastra sieci szkieletowej usług](./service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png

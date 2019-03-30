---
title: Zarządzanie stanem w usługach Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Definiowanie stanu usługi i zarządzanie usługami usługi Service Fabric.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: f5e618a5-3ea3-4404-94af-122278f91652
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: e3ab36def2d210bd763f3ce2dc5df155e37e2dba
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670357"
---
# <a name="service-state"></a>Stan usługi
**Stan usługi** odnosi się do w pamięci lub na dysku dane usługi wymaga funkcji. Obejmuje, na przykład struktury danych i zmiennych składowych, które Usługa odczytuje i zapisuje do pracy. W zależności od tego, jak usługa została zaprojektowana może to również obejmować pliki lub inne zasoby, które są przechowywane na dysku. Na przykład pliki bazy danych będzie używać do przechowywania danych i dzienników transakcji.

Jako usługa przykład rozważmy Kalkulator. Usługa podstawowe Kalkulator przyjmuje dwie liczby i zwraca ich suma. Wykonanie tego obliczenia pociąga za sobą żadnych zmiennych Członkowskich ani innych informacji.

Teraz należy wziąć pod uwagę samej Kalkulator, ale z dodatkową metodę do przechowywania i zwracanie sumy ostatni ma obliczanej. Ta usługa jest teraz stanowych. Stanowa oznacza, że zawiera on pewnego stanu, która zapisuje do oblicza nowy sum i odczytuje z kiedy możesz zadawać pytania do zwrócenia ostatniego obliczona suma.

W usłudze Azure Service Fabric nosi nazwę pierwszej usługi bezstanowej usługi. Druga usługa nosi nazwę usługi stanowej.

## <a name="storing-service-state"></a>Przechowywanie stanu usługi
Stan może zostać zewnętrznych albo wspólnie z kodem, który jest manipulowanie stanu. O eksternalizację stanu jest zazwyczaj wykonywane za pomocą zewnętrznej bazy danych lub innym magazynie danych, która działa na różnych maszynach, za pośrednictwem sieci lub poza procesem na tym samym komputerze. W naszym przykładzie kalkulatora magazynu danych może być bazy danych SQL database lub wystąpienia Store tabeli platformy Azure. Każde żądanie, aby obliczyć sumę przeprowadzi aktualizację, oparte na tych danych, a żądania do usługi do zwrócenia wyniku wartości w bieżącej wartości, które są pobierane z magazynu. 

Stan może być również wspólnie z kodem, który obsługuje stan. Usług stanowych w usłudze Service Fabric są zazwyczaj tworzone przy użyciu tego modelu. Usługa Service Fabric udostępnia infrastrukturę, aby upewnić się, że ten stan jest wysoko dostępna, spójne i niezawodne i że usługi utworzonych w ten sposób można łatwo skalować.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat pojęć usługi Service Fabric zobacz następujące artykuły:

* [Dostępność usługi Service Fabric](service-fabric-availability-services.md)
* [Skalowalność usługi Service Fabric](service-fabric-concepts-scalability.md)
* [Partycjonowanie usługi Service Fabric](service-fabric-concepts-partitioning.md)
* [Usług usługi Service Fabric Reliable Services](service-fabric-reliable-services-introduction.md)

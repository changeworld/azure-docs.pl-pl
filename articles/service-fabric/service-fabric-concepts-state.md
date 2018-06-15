---
title: Definine i zarządzania stanem w programie Azure mikrousług | Dokumentacja firmy Microsoft
description: Definiowanie i zarządzaniu stanu usługi w sieci szkieletowej usług
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: f5e618a5-3ea3-4404-94af-122278f91652
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 46d2e27b9cdcb03213648982c7e9a0576838bc92
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34213099"
---
# <a name="service-state"></a>Stan usługi
**Usługa stanu** odnosi się do w pamięci lub na dysku danych, którego usługa wymaga, aby funkcja. Zawiera, na przykład struktury danych i zmienne Członkowskie, które Usługa odczytuje i zapisuje do pracy. W zależności od tego, jak usługa została zaprojektowana on może również obejmować plików lub innych zasobów, które są przechowywane na dysku. Na przykład pliki bazy danych będzie używać do przechowywania danych i dzienników transakcji.

Jako przykład usługi zastanówmy Kalkulator. Kalkulator podstawowe usługi ma dwie liczby i zwraca ich sumę. Wykonywanie tego obliczenia pociąga za sobą żadnych zmiennych Członkowskich lub inne informacje.

Teraz należy wziąć pod uwagę tego samego Kalkulator, ale z dodatkową metodę przechowywania i zwracanie ostatniego suma jest obliczana. Ta usługa jest teraz stanowych. Stateful oznacza, że zawiera niektóre stan, który zapisuje do oblicza sumę nowy i odczytuje z podczas możesz poprosić Zwróć ostatni Suma obliczanej.

Sieć szkieletowa usług Azure pierwszej usługi nosi usługę bezstanową. Drugi usługi nosi nazwę usługi stanowej.

## <a name="storing-service-state"></a>Przechowywanie stanu usługi
Stan można externalized lub wspólnie z kodem, który jest manipulowanie stanu. Externalization stan jest zazwyczaj wykonywane za pomocą zewnętrznej bazy danych lub innym magazynie danych, wykonywana na różnych komputerach za pośrednictwem sieci lub z procesu na tym samym komputerze. W naszym przykładzie Kalkulator magazynu danych może być bazy danych SQL lub wystąpienie magazynu tabel Azure. Każde żądanie do obliczenia sumy przeprowadzi aktualizację na tych danych i żądań do usługi do zwrócenia wyniku wartości w bieżącej wartości są pobierane z magazynu. 

Stan może być również znajduje się kod, który zmienia stan. Stanowych usług w sieci szkieletowej usług są zazwyczaj tworzone przy użyciu tego modelu. Sieć szkieletowa usług zapewnia infrastrukturę do zapewnienia, że ten stan jest wysoko dostępnych, spójne i trwałe i że usługi utworzone w ten sposób można łatwo skalować.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji dotyczących pojęć sieci szkieletowej usług zobacz następujące artykuły:

* [Dostępność usług sieci szkieletowej usług](service-fabric-availability-services.md)
* [Skalowalność usługi sieci szkieletowej usług](service-fabric-concepts-scalability.md)
* [Partycjonowanie usług sieci szkieletowej usług](service-fabric-concepts-partitioning.md)
* [Niezawodne usługi sieci szkieletowej usług](service-fabric-reliable-services-introduction.md)

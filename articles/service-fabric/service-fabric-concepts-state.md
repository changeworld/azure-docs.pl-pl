---
title: Zarządzanie stanem w usługach sieci szkieletowej usług Azure
description: Dowiedz się więcej o stanie w sieci szkieletowej usług Azure, w tym o tym, jak zdefiniować stan usługi i zarządzać nim w usługach sieci szkieletowej usług.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 9acd3031d1071d1822791b333976aaf76161600f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614574"
---
# <a name="service-state"></a>Stan usługi
**Stan usługi** odnosi się do danych w pamięci lub na dysku, które usługa wymaga do działania. Obejmuje ona na przykład struktur danych i zmiennych członkowskich, które usługa odczytuje i zapisuje do pracy. W zależności od sposobu ekwiomowania usługi może również zawierać pliki lub inne zasoby przechowywane na dysku. Na przykład pliki bazy danych służy do przechowywania danych i dzienników transakcji.

Jako przykładową usługę rozważmy kalkulator. Podstawowa usługa kalkulatora przyjmuje dwie liczby i zwraca ich sumę. Wykonanie tego obliczenia nie obejmuje żadnych zmiennych członkowskich ani innych informacji.

Teraz należy wziąć pod uwagę ten sam kalkulator, ale z dodatkową metodą przechowywania i zwracania ostatniej sumy, którą wyliczył. Ta usługa jest teraz stanowa. Stanowe oznacza, że zawiera pewne stany, które zapisuje do kiedy oblicza nową sumę i odczytuje od kiedy poprosisz go o zwrócenie ostatniej obliczonej sumy.

W usłudze Azure Service Fabric pierwsza usługa jest nazywana usługą bezstanową. Druga usługa jest nazywana usługą stanową.

## <a name="storing-service-state"></a>Stan usługi przechowywania
Stan może być zewnętrznie lub współlokowany z kodem, który manipuluje stanem. Eksternalizacja stanu odbywa się zazwyczaj przy użyciu zewnętrznej bazy danych lub innego magazynu danych, który działa na różnych komputerach za pośrednictwem sieci lub poza procesem na tym samym komputerze. W naszym przykładzie kalkulatora magazynu danych może być baza danych SQL lub wystąpienie usługi Azure Table Store. Każde żądanie obliczania sumy wykonuje aktualizację tych danych i żąda do usługi, aby zwrócić wynik wartości bieżącej wartości pobieranej z magazynu. 

Stan może być również współlokowany z kodem, który manipuluje stanem. Usługi stanowe w sieci szkieletowej usług są zazwyczaj tworzone przy użyciu tego modelu. Sieci szkieletowej usług zapewnia infrastrukturę, aby upewnić się, że ten stan jest wysoce dostępne, spójne i trwałe i że usługi zbudowane w ten sposób można łatwo skalować.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat koncepcji sieci szkieletowej usług, zobacz następujące artykuły:

* [Dostępność usług sieci szkieletowej usług](service-fabric-availability-services.md)
* [Skalowalność usług sieci szkieletowej usług](service-fabric-concepts-scalability.md)
* [Usługi partycjonowania usługi sieci szkieletowej](service-fabric-concepts-partitioning.md)
* [Niezawodne usługi sieci usług](service-fabric-reliable-services-introduction.md)

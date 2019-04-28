---
title: Skalowalność usługi Azure Service Fabric siatki aplikacji | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat skalowania usługi Azure Service Fabric siatki.
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 10/26/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 1688cac35ea9de43bac529a4994bd4ea55eb0ab7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60811087"
---
# <a name="scaling-service-fabric-mesh-applications"></a>Skalowanie aplikacji usługi Service Fabric siatki

Jedną z głównych zalet wdrażania aplikacji w usłudze Service Fabric Mesh jest możliwość łatwego skalowania usług w pionie lub poziomie. Powinno to służyć do obsługi różnych ilości obciążenia w usługach lub do poprawiania dostępności. Można ręcznie skalować swoje usługi wewnątrz lub na zewnątrz lub ustawienia skalowania automatycznego zasad.

## <a name="manual-scaling-instances"></a>Ręczne skalowanie wystąpień

W szablonie wdrażania dla zasobu aplikacji każda usługa ma właściwość *replicaCount*, która może służyć do ustawiania liczby wdrożeń usługi. Aplikacja może składać się z wielu usług, z których każda ma unikatową liczbę właściwości *replicaCount* oraz które są razem wdrażane i zarządzane. Aby skalować liczbę replik usług, zmodyfikuj wartość *replicaCount* dla każdej usługi, którą chcesz skalować, w szablonie wdrożenia lub pliku parametrów. Następnie uaktualnij aplikację.

Ręczne skalowanie wystąpień usług, zobacz [ręcznie skalować swoje usługi wewnątrz lub na zewnątrz](service-fabric-mesh-tutorial-template-scale-services.md).

## <a name="autoscaling-service-instances"></a>Skalowanie automatyczne wystąpień usługi
Automatyczne skalowanie zapewnia dodatkową funkcję usługi Service Fabric dynamicznie skalować liczbę wystąpień usługi (skalowanie w poziomie). Automatyczne skalowanie zapewnia dużą elastyczność i umożliwia inicjowanie obsługi administracyjnej lub usuwania wystąpień usługi na podstawie użycia procesora CPU lub pamięci.  Automatyczne skalowanie pozwala na uruchamianie odpowiednią liczbę wystąpień usługi dla obciążenia i zoptymalizować koszt.

Automatycznie zasady skalowania jest zdefiniowany dla danej usługi w pliku zasobów usługi. Wszystkie zasady skalowania składa się z dwóch części:

- Wyzwalacz skalowania opisano w przypadku skalowania usługi zostanie przeprowadzone. Istnieją trzy czynniki, które określają, kiedy usługa będzie skalowana. *Dolny próg obciążenia* jest wartość, która określa, kiedy usługa będzie można przeskalować w pionie. Jeśli średnie obciążenie wszystkie wystąpienia elementu partycji jest niższa niż ta wartość, usługa będzie skalowana w. *Próg ładowania górny* jest wartością, która określa, kiedy usługa będzie skalowana w poziomie. Jeśli średnie obciążenie wszystkie wystąpienia partycji jest wyższa niż ta wartość, następnie usługi będzie można skalować w poziomie. *Interwału skalowania* Określa, jak często (w sekundach), zostanie sprawdzony wyzwalacza. Gdy wyzwalacz jest zaznaczone, w razie potrzeby skalowania jest mechanizm zostaną zastosowane. Jeśli skalowanie nie jest wymagana, żadna akcja zostaną wykonane. W obu przypadkach wyzwalacz nie będą sprawdzane ponownie przed wygaśnięciem interwału skalowania.

- Mechanizm skalowania, który opisuje, jak skalowanie zostanie wykonane po jego wyzwoleniu. *Skalowanie przyrostu* Określa, ile wystąpień zostaną dodane lub usunięte po wyzwoleniu mechanizmu. *Maksymalna liczba wystąpień* definiuje górny limit skalowania. Jeśli liczba wystąpień osiągnie ten limit, następnie usługi będzie nie być skalowana niezależnie od obciążenia. *Minimalna liczba wystąpień* określa dolną granicę dla skalowania. Jeśli wiele wystąpień partycji osiągnie ten limit, następnie usługa nie będzie skalowana w niezależnie od obciążenia.

Aby dowiedzieć się, jak ustawić zasady skalowania automatycznego dla Twojej usługi, przeczytaj [automatycznego skalowania usług](service-fabric-mesh-howto-auto-scale-services.md).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacji na temat modelu aplikacji, zobacz [zasobów usługi Service Fabric](service-fabric-mesh-service-fabric-resources.md)

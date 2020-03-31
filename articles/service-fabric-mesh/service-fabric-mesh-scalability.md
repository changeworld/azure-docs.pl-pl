---
title: Skalowalność aplikacji usługi Azure Service Fabric Mesh
description: Jedną z zalet wdrażania aplikacji w sieci szkieletowej usługi Jest możliwość łatwego skalowania usług ręcznie lub za pomocą zasad skalowania automatycznego.
author: dkkapur
ms.author: dekapur
ms.date: 10/26/2018
ms.topic: conceptual
ms.openlocfilehash: 474eda904df653d514fd2ee59fa046f1f87a66aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259177"
---
# <a name="scaling-service-fabric-mesh-applications"></a>Skalowanie aplikacji siatki sieci szkieletowej usług

Jedną z głównych zalet wdrażania aplikacji w sieci szkieletowej usługi Mesh jest możliwość łatwego skalowania usług w lub obecnie. Powinno to być używane do obsługi różnych ilości obciążenia usług lub poprawy dostępności. Można ręcznie skalować usługi w lub obecnie lub konfiguracji zasad skalowania automatycznego.

## <a name="manual-scaling-instances"></a>Ręczne skalowanie wystąpień

W szablonie wdrażania dla zasobu aplikacji każda usługa ma właściwość *replicaCount*, która może służyć do ustawiania liczby wdrożeń usługi. Aplikacja może składać się z wielu usług, z których każda ma unikatową liczbę właściwości *replicaCount* oraz które są razem wdrażane i zarządzane. Aby skalować liczbę replik usług, zmodyfikuj wartość *replicaCount* dla każdej usługi, którą chcesz skalować, w szablonie wdrożenia lub pliku parametrów. Następnie uaktualnij aplikację.

Aby zapoznać się z przykładami ręcznego skalowania wystąpień usług, zobacz [Ręczne skalowanie usług w lub na zewnątrz](service-fabric-mesh-tutorial-template-scale-services.md).

## <a name="autoscaling-service-instances"></a>Wystąpienia usługi skalowania automatycznego
Automatyczne skalowanie jest dodatkową możliwością sieci szkieletowej usług do dynamicznego skalowania liczby wystąpień usługi (skalowanie w poziomie). Automatyczne skalowanie zapewnia dużą elastyczność i umożliwia inicjowanie obsługi administracyjnej lub usuwanie wystąpień usługi na podstawie wykorzystania procesora CPU lub pamięci.  Automatyczne skalowanie umożliwia uruchomienie odpowiedniej liczby wystąpień usługi dla obciążenia i optymalizację pod kątem kosztów.

Zasady automatycznego skalowania są definiowane dla usługi w pliku zasobów usługi. Każda zasada skalowania składa się z dwóch części:

- Wyzwalacz skalowania, który opisuje, kiedy skalowanie usługi zostaną wykonane. Istnieją trzy czynniki, które określają, kiedy usługa zostanie skalowana. *Niższy próg obciążenia* jest wartością, która określa, kiedy usługa będzie skalowana. Jeśli średnie obciążenie wszystkich wystąpień partycji jest niższa niż ta wartość, usługa zostanie przeskalowana. *Górny próg obciążenia* jest wartością, która określa, kiedy usługa zostanie przeskalowana w poziomie. Jeśli średnie obciążenie wszystkich wystąpień partycji jest wyższa niż ta wartość, usługa zostanie przeskalowana w poziomie. *Interwał skalowania* określa, jak często (w sekundach) wyzwalacz będzie sprawdzany. Po zaznaczeniu wyzwalacza, jeśli wymagane jest skalowanie, zostanie zastosowany mechanizm. Jeśli skalowanie nie jest potrzebne, nie zostaną podjęte żadne działania. W obu przypadkach wyzwalacz nie zostanie ponownie sprawdzony przed wygaśnięciem interwału skalowania.

- Mechanizm skalowania, który opisuje sposób skalowania będą wykonywane po jego wyzwoleniu. *Przyrost skali* określa, ile wystąpień zostanie dodanych lub usuniętych po wyzwoleniu mechanizmu. *Maksymalna liczba wystąpień* definiuje górny limit skalowania. Jeśli liczba wystąpień osiągnie ten limit, usługa nie będzie skalowana w poziomie, niezależnie od obciążenia. *Minimalna liczba wystąpień* definiuje dolny limit skalowania. Jeśli liczba wystąpień partycji osiągnie ten limit, usługa nie będzie skalowana niezależnie od obciążenia.

Aby dowiedzieć się, jak ustawić zasady skalowania automatycznego dla usługi, przeczytaj [usługi skalowania automatycznego](service-fabric-mesh-howto-auto-scale-services.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat modelu aplikacji, zobacz [Zasoby sieci szkieletowej usług](service-fabric-mesh-service-fabric-resources.md)

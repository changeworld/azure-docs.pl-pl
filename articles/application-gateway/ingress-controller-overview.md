---
title: Co to jest kontroler transferu danych przychodzących bramy aplikacji platformy Azure?
description: Ten artykuł zawiera wprowadzenie do tego, co kontroler transferu danych przychodzących bramy aplikacji jest.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: d6a63b6276c07b1fe6487b97f5c7fc255b6d3411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335801"
---
# <a name="what-is-application-gateway-ingress-controller"></a>Co to jest kontroler transferu danych przychodzących bramy aplikacji?
Kontroler transferu danych przychodzących bramy aplikacji (AGIC) to aplikacja Kubernetes, która umożliwia klientom [usługi Azure Kubernetes (AKS)](https://azure.microsoft.com/services/kubernetes-service/) korzystanie z natywnego modułu równoważenia obciążenia [bramy aplikacji](https://azure.microsoft.com/services/application-gateway/) platformy Azure L7 w celu udostępnienia oprogramowania w chmurze do Internetu. AGIC monitoruje klaster Kubernetes, na który jest obsługiwany, i stale aktualizuje bramę aplikacji, dzięki czemu wybrane usługi są udostępniane w Internecie.

Kontroler transferu danych przychodzących działa we własnym zasobniku w programie AKS klienta. AGIC monitoruje podzbiór zasobów Kubernetes dla zmian. Stan klastra AKS jest tłumaczony na konfigurację specyficzne dla bramy aplikacji i stosowany do [usługi Azure Resource Manager (ARM)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="benefits-of-application-gateway-ingress-controller"></a>Zalety kontrolera transferu danych przychodzących bramy
AGIC umożliwia wdrożenie do kontrolowania wielu klastrów AKS za pomocą jednego kontrolera transferu danych przychodzących bramy aplikacji. Agic pomaga również wyeliminować potrzebę posiadania innego modułu równoważenia obciążenia/publicznego adresu IP przed klastrem AKS i pozwala uniknąć wielu przeskoków na ścieżce danych, zanim żądania dotrą do klastra AKS. Brama aplikacji prowadzi rozmowy z zasobnikami przy użyciu ich prywatnego adresu IP bezpośrednio i nie wymaga usług NodePort lub KubeProxy. Zapewnia to również lepszą wydajność wdrożeń.

Kontroler transferu danych przychodzących jest obsługiwany wyłącznie przez jednostki SKU Standard_v2 i WAF_v2, co również zapewnia korzyści skalowania automatycznego. Brama aplikacji może reagować w odpowiedzi na zwiększenie lub zmniejszenie obciążenia ruchu i odpowiednio skalować, bez zużywania żadnych zasobów z klastra AKS.

Korzystanie z bramy aplikacji oprócz usługi AGIC pomaga również chronić klaster AKS, zapewniając zasady TLS i funkcję Zapory aplikacji sieci Web (WAF).

![Brama aplikacji platformy Azure + usługi AKS](./media/application-gateway-ingress-controller-overview/architecture.png)

AGIC jest konfigurowany za pośrednictwem zasobu Usługi [przychodzącej](https://kubernetes.io/docs/user-guide/ingress/)Kubernetes, wraz z usług i wdrożeń/zasobników. Zapewnia szereg funkcji, wykorzystując natywnego modułu równoważenia obciążenia bramy aplikacji platformy Azure L7. Aby wymienić tylko kilka:
  - Routing adresów URL
  - Koligacja na podstawie pliku cookie
  - Zakończenie protokołu TLS
  - Kompleksowy tls
  - Obsługa publicznych, prywatnych i hybrydowych witryn internetowych
  - Zintegrowana zapora aplikacji sieci Web

AGIC jest w stanie obsługiwać wiele obszarów nazw i ma ProhibitedTargets, co oznacza, że AGIC może skonfigurować bramę aplikacji specjalnie dla klastrów AKS bez wpływu na inne istniejące zaplecze. 

## <a name="next-steps"></a>Następne kroki

- [**Wdrażanie greenfield:**](ingress-controller-install-new.md)Instrukcje dotyczące instalowania AGIC, AKS i bramy aplikacji w infrastrukturze pustej planszy.
- [**Wdrożenie brownfield:**](ingress-controller-install-existing.md)Zainstaluj AGIC na istniejącym AKS i bramie aplikacji.


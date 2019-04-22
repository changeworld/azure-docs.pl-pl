---
title: Omówienie zestawów skalowania maszyn wirtualnych platformy Azure | Microsoft Docs
description: Dowiedz się więcej o zestawach skalowania maszyn wirtualnych platformy Azure i o sposobie automatycznego skalowania swoich aplikacji
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 03/27/2018
ms.author: manayar
ms.openlocfilehash: 1ace0ddd3b29f844488eb7579934541fa8273fa0
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2019
ms.locfileid: "58199906"
---
# <a name="what-are-virtual-machine-scale-sets"></a>Co to są zestawy skalowania maszyn wirtualnych?
Zestawy skalowania maszyn wirtualnych platformy Azure pozwalają utworzyć grupę identycznych, mających zrównoważone obciążenie maszyn wirtualnych i zarządzać nimi. Liczba wystąpień maszyn wirtualnych może automatycznie zwiększać lub zmniejszać się w reakcji na zapotrzebowanie lub zdefiniowany harmonogram. Zestawy skalowania zapewniają wysoką dostępność Twoim aplikacjom i pozwalają na centralnie zarządzanie, konfigurowanie i aktualizowanie dużej liczby maszyn wirtualnych. Za pomocą zestawów skalowania maszyn wirtualnych możesz tworzyć usługi na dużą skalę dla takich obszarów jak obliczenia, dane big data i obciążenia kontenera.


## <a name="why-use-virtual-machine-scale-sets"></a>Dlaczego warto używać zestawów skalowania maszyn wirtualnych?
Aby zapewnić nadmiarowość i poprawić wydajność, aplikacje są zazwyczaj dystrybuowane w wielu wystąpieniach. Klienci mogą uzyskać dostęp do Twojej aplikacji za pośrednictwem modułu równoważenia obciążenia, który rozdziela żądania do jednego z wystąpień aplikacji. Jeśli musisz przeprowadzić konserwację lub zaktualizować wystąpienie aplikacji, Twoi klienci muszą zostać przekierowani do innego dostępnego wystąpienia aplikacji. Aby nadążyć za dodatkowym zapotrzebowaniem klienta, może być konieczne zwiększenie liczby wystąpień aplikacji, gdzie jest uruchamiana Twoja aplikacja.

Zestawy skalowania maszyn wirtualnych platformy Azure zapewniają możliwości zarządzania aplikacjom działającym na wielu maszynach wirtualnych, [automatyczne skalowanie zasobów](virtual-machine-scale-sets-autoscale-overview.md) i równoważenie obciążenia ruchem. Zestawy skalowania zapewniają następujące kluczowe korzyści:

- **Łatwe tworzenie wielu maszyn wirtualnych i zarządzanie nimi**
    - Jeśli masz wiele maszyn wirtualnych, na których działa Twoja aplikacja, ważne jest zachowanie spójnej konfiguracji w całym środowisku. Zapewniana wydajność aplikacji, rozmiar maszyny wirtualnej, konfiguracja dysku i instalacje aplikacji powinny być zgodne na wszystkich maszynach wirtualnych.
    - Za pomocą zestawów skalowania wszystkie wystąpienia maszyn wirtualnych są tworzone na podstawie tego samego podstawowego obrazu systemu operacyjnego i jego konfiguracji. Takie podejście umożliwia łatwe zarządzanie setkami maszyn wirtualnych bez dodatkowych zadań konfiguracji lub zarządzania siecią.
    - Zestawy skalowania obsługują korzystanie z [modułu równoważenia obciążenia platformy Azure](../load-balancer/load-balancer-overview.md) dla podstawowej dystrybucji ruchu warstwy 4 oraz z [usługi Azure Application Gateway](../application-gateway/application-gateway-introduction.md) dla bardziej zaawansowanej dystrybucji ruchu warstwy 7 i kończenia żądań SSL.

- **Zapewnia wysoką dostępność i odporność aplikacji**
    - Zestawy skalowania służą do uruchamiania wielu wystąpień aplikacji. Jeśli jedno z tych wystąpień maszyny wirtualnej ma problem, klienci nadal mają dostęp do aplikacji za pośrednictwem jednego z innych wystąpień maszyn wirtualnych z minimalną przerwą.
    - Aby uzyskać dodatkową dostępność, możesz użyć [stref dostępności](../availability-zones/az-overview.md) w celu automatycznej dystrybucji wystąpień maszyn wirtualnych w zestawie skalowania w jednym centrum danych lub w wielu centrach danych.

- **Pozwala aplikacji na automatyczne skalowanie odpowiednio do zmian zapotrzebowania na zasoby**
    - Zapotrzebowanie klienta Twojej aplikacji może ulec zmianie w ciągu dnia lub tygodnia. Aby dostosować się do zapotrzebowania klienta, zestawy skalowania mogą automatycznie zwiększać liczbę wystąpień maszyn wirtualnych wraz ze wzrostem zapotrzebowania na aplikację, a następnie zmniejszać liczbę wystąpień maszyn wirtualnych, gdy zmniejsza się zapotrzebowanie.
    - Funkcja automatycznego skalowania minimalizuje również liczbę niepotrzebnych wystąpień maszyn wirtualnych, na których jest uruchomiona Twoja aplikacja, gdy zapotrzebowanie jest niskie, przy czym klienci nadal mają zapewniony akceptowalny poziom wydajności w miarę wzrostu zapotrzebowania, a dodatkowe wystąpienia maszyn wirtualnych są automatycznie dodawane. Ta możliwość ułatwia obniżenie kosztów i efektywnie tworzy zasoby platformy Azure zgodnie z potrzebami.

- **Działa na dużą skalę**
    - Zestaw skalowania obsługuje maksymalnie 1000 wystąpień maszyn wirtualnych. Jeśli utworzysz i przekazania własnych niestandardowych obrazów maszyn wirtualnych limit wynosi 600 wystąpień maszyn wirtualnych.
    - Aby uzyskać najlepszą wydajność dla obciążeń produkcyjnych, użyj funkcji [Dyski zarządzane platformy Azure](../virtual-machines/windows/managed-disks-overview.md).


## <a name="differences-between-virtual-machines-and-scale-sets"></a>Różnice między maszynami wirtualnymi i zestawami skalowania
Zestawy skalowania są tworzone z maszyn wirtualnych. Zestawy skalowania udostępniają warstwy zarządzania i automatyzacji do uruchamiania i skalowania Twoich aplikacji. Zamiast tego możesz ręcznie tworzyć poszczególne maszyny wirtualne i zarządzać nimi lub integrować istniejące narzędzia do tworzenia podobnego poziomu automatyzacji. W poniższej tabeli przedstawiono zarys korzyści z zestawów skalowania w porównaniu do ręcznego zarządzania wieloma wystąpieniami maszyn wirtualnych.

| Scenariusz                           | Ręczne grupowanie maszyn wirtualnych                                                                    | Zestaw skalowania maszyn wirtualnych |
|------------------------------------|----------------------------------------------------------------------------------------|---------------------------|
| Dodawanie kolejnych wystąpień maszyn wirtualnych        | Ręczny proces tworzenia, konfigurowania i zapewniania zgodności                             | Automatyczne tworzenie na podstawie centralnej konfiguracji |
| Równoważenie i dystrybucja ruchu | Ręczny proces tworzenia i konfigurowania modułu równoważenia obciążenia platformy Azure lub usługi Application Gateway      | Umożliwia automatyczne tworzenie i integrowanie z modułem równoważenia obciążenia platformy Azure lub usługą Application Gateway |
| Wysoka dostępność i nadmiarowość   | Ręczne tworzenie zestawu dostępności lub dystrybuowanie i śledzenie maszyn wirtualnych w różnych strefach dostępności | Automatyczna dystrybucja wystąpień maszyn wirtualnych w strefach dostępności lub zestawach dostępności |
| Skalowanie maszyn wirtualnych                     | Ręczne monitorowanie i usługa Azure Automation                                                 | Automatyczne skalowanie na podstawie metryk hosta, metryk gościa, usługi Application Insights lub harmonogramu |

Używanie zestawów skalowania nie pociąga za sobą dodatkowych kosztów. Płacisz tylko za podstawowe zasoby obliczeniowe, takie jak wystąpienia maszyn wirtualnych, moduł równoważenia obciążenia lub miejsce na dysku zarządzanym. Funkcje zarządzania i automatyzacji, takie jak skalowanie automatyczne i nadmiarowość, nie pociągają za sobą dodatkowych opłat za korzystanie z maszyn wirtualnych.


## <a name="next-steps"></a>Kolejne kroki
Aby rozpocząć, utwórz swój pierwszy zestaw skalowania maszyn wirtualnych w witrynie Azure Portal.

> [!div class="nextstepaction"]
> [Tworzenie zestawu skalowania w witrynie Azure Portal](quick-create-portal.md)

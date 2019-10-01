---
title: Omówienie zestawów skalowania maszyn wirtualnych platformy Azure | Microsoft Docs
description: Dowiedz się więcej o zestawach skalowania maszyn wirtualnych platformy Azure oraz o automatycznym skalowaniu aplikacji
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 09/26/2019
ms.author: manayar
ms.openlocfilehash: 73580814dcfe8f967684aca4ce433a40e7bbedc0
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679386"
---
# <a name="what-are-virtual-machine-scale-sets"></a>Co to są zestawy skalowania maszyn wirtualnych?
Zestawy skalowania maszyn wirtualnych platformy Azure umożliwiają tworzenie i Zarządzanie grupą takich samych maszyn wirtualnych o zrównoważonym obciążeniu. Liczba wystąpień maszyn wirtualnych może być automatycznie zwiększana lub zmniejszana w odpowiedzi na zapotrzebowanie lub zdefiniowany harmonogram. Zestawy skalowania zapewniają wysoką dostępność aplikacji i umożliwiają centralne zarządzanie, Konfigurowanie i aktualizowanie dużej liczby maszyn wirtualnych. Za pomocą zestawów skalowania maszyn wirtualnych można tworzyć usługi na dużą skalę dla obszarów takich jak obliczenia, dane Big Data i obciążenia kontenerów.


## <a name="why-use-virtual-machine-scale-sets"></a>Dlaczego warto używać zestawów skalowania maszyn wirtualnych?
Aby zapewnić nadmiarowość i lepszą wydajność, aplikacje są zwykle rozproszone w wielu wystąpieniach. Klienci mogą uzyskać dostęp do aplikacji za pomocą modułu równoważenia obciążenia, który dystrybuuje żądania do jednego z wystąpień aplikacji. Jeśli musisz przeprowadzić konserwację lub zaktualizować wystąpienie aplikacji, klienci muszą zostać przedystrybuowani do innego dostępnego wystąpienia aplikacji. Aby zapewnić dodatkowe zapotrzebowanie na klienta, może być konieczne zwiększenie liczby wystąpień aplikacji, na których działa aplikacja.

Zestawy skalowania maszyn wirtualnych platformy Azure zapewniają funkcje zarządzania dla aplikacji, które działają na wielu maszynach wirtualnych, [Automatyczne skalowanie zasobów](virtual-machine-scale-sets-autoscale-overview.md)i równoważenie obciążenia ruchu sieciowego. Zestawy skalowania zapewniają następujące kluczowe korzyści:

- **Łatwe tworzenie wielu maszyn wirtualnych i zarządzanie nimi**
    - Jeśli masz wiele maszyn wirtualnych, na których działa aplikacja, ważne jest, aby zachować spójną konfigurację w danym środowisku. Aby zapewnić niezawodne działanie aplikacji, rozmiar maszyny wirtualnej, konfigurację dysku i instalacje aplikacji powinny być zgodne ze wszystkimi maszynami wirtualnymi.
    - W przypadku zestawów skalowania wszystkie wystąpienia maszyn wirtualnych są tworzone na podstawie tego samego podstawowego obrazu systemu operacyjnego i konfiguracji. Takie podejście umożliwia łatwe zarządzanie setkami maszyn wirtualnych bez dodatkowych zadań konfiguracyjnych ani zarządzania siecią.
    - Zestawy skalowania obsługują korzystanie z [modułu równoważenia obciążenia platformy Azure](../load-balancer/load-balancer-overview.md) na potrzeby dystrybucji ruchu w warstwie 4 i na [platformie Azure Application Gateway](../application-gateway/application-gateway-introduction.md) w celu uzyskania bardziej zaawansowanej dystrybucji ruchu w warstwie 7 i zakończenia protokołu SSL.

- **Zapewnia wysoką dostępność i odporność aplikacji**
    - Zestawy skalowania są używane do uruchamiania wielu wystąpień aplikacji. Jeśli jedno z tych wystąpień maszyn wirtualnych ma problem, klienci nadal uzyskują dostęp do aplikacji za pomocą jednego z pozostałych wystąpień maszyn wirtualnych z minimalnym zakłóceniem.
    - Aby uzyskać dodatkową dostępność, można użyć [strefy dostępności](../availability-zones/az-overview.md) do automatycznego dystrybuowania wystąpień maszyn wirtualnych w zestawie skalowania w jednym centrum danych lub w wielu centrach danych.

- **Zezwala aplikacji na automatyczne skalowanie jako zmiany zapotrzebowania na zasoby**
    - Żądanie klienta dotyczące aplikacji może się zmienić w ciągu dnia lub tygodnia. Aby dopasować zapotrzebowanie klientów, zestawy skalowania mogą automatycznie zwiększać liczbę wystąpień maszyn wirtualnych w miarę wzrostu zapotrzebowania aplikacji, a następnie zmniejszyć liczbę wystąpień maszyn wirtualnych w miarę zmniejszania zapotrzebowania.
    - Funkcja automatycznego skalowania minimalizuje także liczbę niepotrzebnych wystąpień maszyn wirtualnych, na których jest uruchamiana aplikacja, gdy zapotrzebowanie jest niskie, a klienci nadal otrzymują akceptowalny poziom wydajności w miarę wzrostu zapotrzebowania i automatycznie dodawane są dodatkowe wystąpienia maszyn wirtualnych. Zapewnia to zmniejszenie kosztów i wydajne tworzenie zasobów platformy Azure zgodnie z potrzebami.

- **Działa na dużą skalę**
    - Zestawy skalowania obsługują do 1 000 wystąpień maszyn wirtualnych. W przypadku utworzenia i przekazania własnych niestandardowych obrazów maszyn wirtualnych limit wynosi 600 wystąpień maszyn wirtualnych.
    - Aby uzyskać najlepszą wydajność w przypadku obciążeń produkcyjnych, użyj [usługi Azure Managed disks](../virtual-machines/windows/managed-disks-overview.md).


## <a name="differences-between-virtual-machines-and-scale-sets"></a>Różnice między maszynami wirtualnymi i zestawami skalowania
Zestawy skalowania są tworzone z maszyn wirtualnych. Dzięki zestawom skalowania warstwy zarządzania i automatyzacji są udostępniane do uruchamiania i skalowania aplikacji. Zamiast tego można ręcznie tworzyć poszczególne maszyny wirtualne i zarządzać nimi lub integrować istniejące narzędzia w celu utworzenia podobnego poziomu automatyzacji. W poniższej tabeli przedstawiono zalety zestawów skalowania w porównaniu do ręcznego zarządzania wieloma wystąpieniami maszyn wirtualnych.

| Scenariusz                           | Ręczna Grupa maszyn wirtualnych                                                                    | Zestaw skalowania maszyn wirtualnych |
|------------------------------------|----------------------------------------------------------------------------------------|---------------------------|
| Dodawanie kolejnych wystąpień maszyn wirtualnych        | Proces ręczny do tworzenia, konfigurowania i zapewniania zgodności                             | Automatyczne tworzenie z poziomu konfiguracji centralnej |
| Równoważenie i dystrybucja ruchu | Proces ręczny tworzenia i konfigurowania modułu równoważenia obciążenia platformy Azure lub Application Gateway      | Możliwość automatycznego tworzenia i integrowania z usługą równoważenia obciążenia platformy Azure lub Application Gateway |
| Wysoka dostępność i nadmiarowość   | Ręczne tworzenie zestawu dostępności lub dystrybuowanie i śledzenie maszyn wirtualnych między Strefy dostępności | Automatyczne dystrybuowanie wystąpień maszyn wirtualnych w różnych Strefy dostępności lub zestawach dostępności |
| Skalowanie maszyn wirtualnych                     | Ręczne monitorowanie i Azure Automation                                                 | Automatyczne skalowanie na podstawie metryk hosta, metryki gościa, Application Insights lub harmonogramu |

Nie ma dodatkowych kosztów dla zestawów skalowania. Płacisz tylko za podstawowe zasoby obliczeniowe, takie jak wystąpienia maszyn wirtualnych, moduł równoważenia obciążenia lub magazyn dysk zarządzany. Funkcje zarządzania i automatyzacji, takie jak automatyczne skalowanie i nadmiarowość, nie wiążą się z dodatkowymi opłatami za korzystanie z maszyn wirtualnych.

## <a name="how-to-monitor-your-scale-sets"></a>Jak monitorować zestawy skalowania

Użyj [Azure monitor dla maszyn wirtualnych](../azure-monitor/insights/vminsights-overview.md), który ma prosty proces dołączania i automatyzuje zbieranie ważnych liczników wydajności procesora CPU, pamięci, dysku i sieci z maszyn wirtualnych w zestawie skalowania. Zawiera również dodatkowe możliwości monitorowania i wstępnie zdefiniowane wizualizacje, które ułatwiają skoncentrowanie się na dostępności i wydajności zestawów skalowania.

Włącz monitorowanie [aplikacji zestawu skalowania maszyn wirtualnych](../azure-monitor/app/azure-vm-vmss-apps.md) za pomocą Application Insights, aby zbierać szczegółowe informacje o aplikacji, w tym o widokach stron, żądaniach aplikacji i wyjątkach. Sprawdź dostępność aplikacji, konfigurując [Test dostępności](../azure-monitor/app/monitor-web-app-availability.md) w celu zasymulowania ruchu użytkownika.

## <a name="next-steps"></a>Następne kroki
Aby rozpocząć, Utwórz pierwszy zestaw skalowania maszyn wirtualnych w Azure Portal.

> [!div class="nextstepaction"]
> [Tworzenie zestawu skalowania w Azure Portal](quick-create-portal.md)

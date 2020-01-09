---
title: Co to jest usługa Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)? | Microsoft Docs
description: Omówienie Azure Monitor dla maszyn wirtualnych, które monitorują kondycję i wydajność maszyn wirtualnych platformy Azure, a także automatycznie odnajdują i mapują składniki aplikacji oraz ich zależności.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/14/2019
ms.openlocfilehash: 17aa81c626c1bfa8a8a344552b7a0fe61dd85a7e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75365788"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>Co to jest usługa Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)?

Usługa Azure Monitor dla maszyn wirtualnych monitoruje usługi Azure virtual machines (VM) i zestawy skalowania maszyn wirtualnych na dużą skalę. Analizuje ona wydajność i kondycję Twoich maszyn wirtualnych z systemami Windows i Linux oraz monitoruje ich procesy i zależności od innych zasobów oraz procesów zewnętrznych. 

Obejmuje ona obsługę monitorowania wydajności i zależności aplikacji dla maszyn wirtualnych hostowanych lokalnie lub w innym dostawcy chmury. Następujące kluczowe funkcje zapewniają szczegółowe informacje:

- **Wstępnie zdefiniowane wykresy wydajności trendu**: Wyświetl Podstawowe metryki wydajności z systemu operacyjnego gościa.

- **Mapa zależności**: Wyświetla powiązanych elementów z maszyną Wirtualną z różnych subskrypcji i grupy zasobów.  

>[!NOTE]
>Niedawno [ogłoszono zmiany wprowadzane](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) do funkcji kondycji w oparciu o Opinie otrzymane od naszych klientów publicznej wersji zapoznawczej. Mając na względzie liczbę wprowadzonych zmian, będziemy mogli przestać oferować funkcję kondycji dla nowych klientów. Istniejący klienci mogą nadal korzystać z funkcji kondycji. Aby uzyskać więcej informacji, zobacz nasze [często zadawane pytania dotyczące ogólnej dostępności](vminsights-ga-release-faq.md).  

Integracja z dziennikami Azure Monitor zapewnia zaawansowaną agregację i filtrowanie oraz pozwala na analizowanie trendów danych w czasie. Takie kompleksowe monitorowanie obciążenia nie może być osiągnięte przy użyciu Azure Monitor lub Service Map.  

Możesz wyświetlić te dane bezpośrednio z maszyny wirtualnej lub użyć Azure Monitor, aby dostarczyć Zagregowany widok maszyn wirtualnych, gdzie widok obsługuje tryby zasobów platformy Azure lub kontekstu obszaru roboczego. Aby uzyskać więcej informacji, zobacz [Omówienie trybów dostępu](../platform/design-logs-deployment.md#access-mode).

![Maszyna wirtualna perspektywy szczegółowych informacji w witrynie Azure portal](./media/vminsights-overview/vminsights-azmon-directvm.png)

Azure Monitor dla maszyn wirtualnych może zapewnić przewidywalną wydajność i dostępność ważnych aplikacji. Identyfikuje ona wąskie gardła wydajności i problemy z siecią. Azure Monitor dla maszyn wirtualnych może również pomóc zrozumieć, czy problem jest związany z innymi zależnościami.  

## <a name="data-usage"></a>Użycie danych

Podczas wdrażania usługi Azure Monitor dla maszyn wirtualnych, danych, które są zbierane przez maszyny wirtualne jest pozyskiwane i przechowywane w usłudze Azure Monitor. Zebrane dane dotyczące wydajności i zależności są przechowywane w obszarze roboczym Log Analytics. Oparte na temat cen, które opublikowano na [usługi Azure Monitor stronę z cennikiem](https://azure.microsoft.com/pricing/details/monitor/), usługi Azure Monitor dla maszyn wirtualnych jest naliczana za:

- Dane pozyskiwane i przechowywane.
- Reguły alertów, które są tworzone.
- Powiadomienia, które są wysyłane. 

Rozmiar dziennika zmienia się w zależności od długości ciągu liczników wydajności i może zwiększyć liczbę dysków logicznych i kart sieciowych przypisanych do maszyny wirtualnej. Jeśli już masz obszaru roboczego i są zbierane następujące liczniki są stosowane nie podwójne naliczenie opłaty. Jeśli już korzystasz z rozwiązania Service Map, zobaczysz Jedyna zmiana jest dane dodatkowe połączenie, które są wysyłane do usługi Azure Monitor.

## <a name="next-steps"></a>Następne kroki

Aby poznać wymagania i metod, które pomagają monitorować maszyny wirtualne, zobacz [wdrożenia usługi Azure Monitor dla maszyn wirtualnych](vminsights-enable-overview.md).

---
title: Co to jest usługa Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)? | Microsoft Docs
description: Omówienie Azure Monitor dla maszyn wirtualnych, które monitorują kondycję i wydajność maszyn wirtualnych platformy Azure, a także automatycznie odnajdują i mapują składniki aplikacji oraz ich zależności.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/14/2019
ms.openlocfilehash: 1dcce3ab9f975fcf5910c382df3489d5d4ed425a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670670"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>Co to jest usługa Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)?

Usługa Azure Monitor dla maszyn wirtualnych monitoruje usługi Azure virtual machines (VM) i zestawy skalowania maszyn wirtualnych na dużą skalę. Analizuje ona wydajność i kondycję Twoich maszyn wirtualnych z systemami Windows i Linux oraz monitoruje ich procesy i zależności od innych zasobów oraz procesów zewnętrznych. 

Obejmuje ona obsługę monitorowania wydajności i zależności aplikacji dla maszyn wirtualnych hostowanych lokalnie lub w innym dostawcy chmury. Następujące kluczowe funkcje zapewniają szczegółowe informacje:

- **Wstępnie zdefiniowane wykresy wydajności trendu**: Wyświetl Podstawowe metryki wydajności z systemu operacyjnego gościa.

- **Mapa zależności**: zawiera składniki połączone z maszyną wirtualną z różnych grup zasobów i subskrypcji.  

>[!NOTE]
>Niedawno [ogłoszono zmiany wprowadzane](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) do funkcji kondycji w oparciu o Opinie otrzymane od naszych klientów publicznej wersji zapoznawczej. Mając na względzie liczbę wprowadzonych zmian, będziemy mogli przestać oferować funkcję kondycji dla nowych klientów. Istniejący klienci mogą nadal korzystać z funkcji kondycji. Aby uzyskać więcej informacji, zobacz nasze [często zadawane pytania dotyczące ogólnej dostępności](vminsights-ga-release-faq.md).  

Integracja z dziennikami Azure Monitor zapewnia zaawansowaną agregację i filtrowanie oraz pozwala na analizowanie trendów danych w czasie. Takie kompleksowe monitorowanie obciążenia nie może być osiągnięte przy użyciu Azure Monitor lub Service Map.  

Możesz wyświetlić te dane bezpośrednio z maszyny wirtualnej lub użyć Azure Monitor, aby dostarczyć Zagregowany widok maszyn wirtualnych, gdzie widok obsługuje tryby zasobów platformy Azure lub kontekstu obszaru roboczego. Aby uzyskać więcej informacji, zobacz [Omówienie trybów dostępu](../platform/design-logs-deployment.md#access-mode).

![Maszyna wirtualna perspektywy szczegółowych informacji w witrynie Azure portal](./media/vminsights-overview/vminsights-azmon-directvm.png)

Azure Monitor dla maszyn wirtualnych może zapewnić przewidywalną wydajność i dostępność ważnych aplikacji. Identyfikuje ona wąskie gardła wydajności i problemy z siecią. Azure Monitor dla maszyn wirtualnych może również pomóc zrozumieć, czy problem jest związany z innymi zależnościami.  

## <a name="data-usage"></a>Użycie danych

Podczas wdrażania usługi Azure Monitor dla maszyn wirtualnych, danych, które są zbierane przez maszyny wirtualne jest pozyskiwane i przechowywane w usłudze Azure Monitor. Zebrane dane dotyczące wydajności i zależności są przechowywane w obszarze roboczym Log Analytics. W oparciu o Cennik opublikowany na [stronie cennika Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/)Azure monitor dla maszyn wirtualnych opłaty są naliczane za:

- Dane pozyskiwane i przechowywane.
- Reguły alertów, które są tworzone.
- Powiadomienia, które są wysyłane. 

Rozmiar dziennika zmienia się w zależności od długości ciągu liczników wydajności i może zwiększyć liczbę dysków logicznych i kart sieciowych przypisanych do maszyny wirtualnej. Jeśli już masz obszaru roboczego i są zbierane następujące liczniki są stosowane nie podwójne naliczenie opłaty. Jeśli już korzystasz z rozwiązania Service Map, zobaczysz Jedyna zmiana jest dane dodatkowe połączenie, które są wysyłane do usługi Azure Monitor.

## <a name="next-steps"></a>Następne kroki

Aby poznać wymagania i metody, które ułatwiają monitorowanie maszyn wirtualnych, zobacz [wdrażanie Azure monitor dla maszyn wirtualnych](vminsights-enable-overview.md).

---
title: Co to jest usługa Azure Monitor dla maszyn wirtualnych?
description: Omówienie usługi Azure Monitor dla maszyn wirtualnych, która monitoruje kondycję i wydajność maszyn wirtualnych platformy Azure oprócz automatycznego odnajdywania i mapowania składników aplikacji i ich zależności.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/11/2020
ms.openlocfilehash: 6f65ba96f768a4b88aa13c073cc66920b8c5e049
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480491"
---
# <a name="what-is-azure-monitor-for-vms"></a>Co to jest usługa Azure Monitor dla maszyn wirtualnych?

Usługa Azure Monitor dla maszyn wirtualnych monitoruje na dużą skalę maszyny wirtualne platformy Azure i zestawy skalowania maszyn wirtualnych. Analizuje ona wydajność i kondycję Twoich maszyn wirtualnych z systemami Windows i Linux oraz monitoruje ich procesy i zależności od innych zasobów oraz procesów zewnętrznych. Obejmuje obsługę monitorowania wydajności i zależności aplikacji dla maszyn wirtualnych, które są hostowane lokalnie lub w innym dostawcy chmury. Następujące kluczowe funkcje zapewniają dogłębną wiedzę:

- **Wstępnie zdefiniowane wykresy wydajności trendów:** wyświetlanie metryk wydajności podstawowych z systemu operacyjnego maszyny Wirtualnej gościa.

- **Mapa zależności:** Wyświetla połączone ze sobą składniki z maszyną wirtualną z różnych grup zasobów i subskrypcji.  

>[!NOTE]
>Niedawno [ogłosiliśmy zmiany](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) w funkcji Zdrowie na podstawie opinii, które otrzymaliśmy od naszych klientów publicznej wersji zapoznawczej. Biorąc pod uwagę liczbę zmian, które będziemy wszywać, przestaniemy oferować funkcję Zdrowie dla nowych klientów. Obecni klienci mogą nadal korzystać z funkcji kondycji. Aby uzyskać więcej informacji, zapoznaj się z naszym [najczęściej zadawanymi pytaniami dotyczącymi ogólnej dostępności.](vminsights-ga-release-faq.md)  

Integracja z dziennikami usługi Azure Monitor zapewnia zaawansowana agregacja i filtrowanie, umożliwiając platformie Azure Monitor dla maszyn wirtualnych analizowanie trendów danych w czasie. Można wyświetlić te dane w jednej maszynie wirtualnej bezpośrednio z maszyny wirtualnej lub można użyć usługi Azure Monitor do dostarczania zagregowanego widoku maszyn wirtualnych, gdzie widok obsługuje tryby kontekstu zasobów platformy Azure lub kontekstu obszaru roboczego. Aby uzyskać więcej informacji, zobacz [omówienie trybów dostępu](../platform/design-logs-deployment.md#access-mode).

![Perspektywa szczegółowych informacji o maszynach wirtualnych w witrynie Azure portal](media/vminsights-overview/vminsights-azmon-directvm.png)

Usługa Azure Monitor dla maszyn wirtualnych może zapewnić przewidywalną wydajność i dostępność ważnych aplikacji. Identyfikuje wąskie gardła wydajności i problemy z siecią, a także może pomóc zrozumieć, czy problem jest związany z innymi zależnościami.  

## <a name="data-usage"></a>Użycie danych

Podczas wdrażania usługi Azure Monitor dla maszyn wirtualnych dane zbierane przez maszyny wirtualne są przyjmowane i przechowywane w usłudze Azure Monitor. Dane dotyczące wydajności i zależności są przechowywane w obszarze roboczym usługi Log Analytics. Na podstawie cen opublikowanych na [stronie cennik usługi Azure Monitor naliczane](https://azure.microsoft.com/pricing/details/monitor/)są naliczane rachunki za usługę Azure Monitor dla maszyn wirtualnych:

- Dane, które są pojmowane i przechowywane.
- Reguły alertów, które są tworzone.
- Powiadomienia, które są wysyłane. 

Rozmiar dziennika zależy od długości ciągów liczników wydajności i może wzrosnąć wraz z liczbą dysków logicznych i kart sieciowych przydzielonych do maszyny Wirtualnej. Jeśli masz już obszar roboczy i zbierasz te liczniki, nie są stosowane żadne zduplikowane opłaty. Jeśli korzystasz już z mapy usługi, jedyną zmianą, którą zobaczysz, są dodatkowe dane połączenia wysyłane do usługi Azure Monitor.

## <a name="next-steps"></a>Następne kroki

Aby zrozumieć wymagania i metody, które pomagają monitorować maszyny wirtualne, przejrzyj [wdrażanie usługi Azure Monitor dla maszyn wirtualnych.](vminsights-enable-overview.md)

---
title: Co to jest Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)? | Microsoft Docs
description: Azure Monitor dla maszyn wirtualnych to funkcja Azure Monitor, która łączy kondycję i monitorowanie wydajności systemu operacyjnego Azure VM, a także automatycznie odnajduje składniki aplikacji i zależności z innymi zasobami oraz mapuje komunikację między nimi. Ten artykuł zawiera omówienie.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: c6135f3ab90a2002c3cf0c8d26211d66d0c637e8
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802405"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>Co to jest Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)?

Azure Monitor dla maszyn wirtualnych monitoruje maszyny wirtualne platformy Azure i zestawy skalowania maszyn wirtualnych na dużą skalę. Analizuje ona wydajność i kondycję Twoich maszyn wirtualnych z systemami Windows i Linux oraz monitoruje ich procesy i zależności od innych zasobów oraz procesów zewnętrznych. 

Obejmuje ona obsługę monitorowania wydajności i zależności aplikacji dla maszyn wirtualnych hostowanych lokalnie lub w innym dostawcy chmury. Trzy kluczowe funkcje zapewniają szczegółowe informacje:

- **Składniki logiczne maszyn wirtualnych platformy Azure i zestawów skalowania maszyn wirtualnych z systemem Windows i Linux**są mierzone względem wstępnie skonfigurowanych kryteriów kondycji, a następnie powiadamiają o spełnieniu warunku.  

- **Wstępnie zdefiniowane wykresy wydajności trendu**: Wyświetl Podstawowe metryki wydajności z systemu operacyjnego gościa.

- **Mapa zależności**: zawiera składniki połączone z maszyną wirtualną z różnych grup zasobów i subskrypcji.  

Funkcje są zorganizowane w trzy perspektywy:

- Zdrowie
- Wydajność
- Mapa

>[!NOTE]
>Niedawno [ogłoszono zmiany](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) wprowadzane do funkcji kondycji na podstawie opinii otrzymanej od naszych klientów publicznej wersji zapoznawczej. Mając na względzie liczbę wprowadzonych zmian, będziemy mogli przestać oferować funkcję kondycji dla nowych klientów. Istniejący klienci mogą nadal korzystać z funkcji kondycji. Aby uzyskać więcej informacji, zobacz nasze [często zadawane pytania dotyczące ogólnej dostępności](vminsights-ga-release-faq.md).  

Integracja z dziennikami Azure Monitor zapewnia zaawansowaną agregację i filtrowanie oraz pozwala na analizowanie trendów danych w czasie. Takie kompleksowe monitorowanie obciążenia nie może być osiągnięte przy użyciu Azure Monitor lub Service Map.  

Możesz wyświetlić te dane bezpośrednio z maszyny wirtualnej lub użyć Azure Monitor, aby dostarczyć Zagregowany widok maszyn wirtualnych. Ten widok jest oparty na perspektywie każdej funkcji:

- **Kondycja**: maszyny wirtualne są powiązane z grupą zasobów.
- **Mapa** i **wydajność**: maszyny wirtualne są skonfigurowane do raportowania do określonego obszaru roboczego log Analytics.

![Perspektywa usługi Virtual Machine Insights w Azure Portal](./media/vminsights-overview/vminsights-azmon-directvm-01.png)

Azure Monitor dla maszyn wirtualnych może zapewnić przewidywalną wydajność i dostępność ważnych aplikacji. Identyfikuje krytyczne zdarzenia systemu operacyjnego, wąskie gardła wydajności i problemy z siecią. Azure Monitor dla maszyn wirtualnych może również pomóc zrozumieć, czy problem jest związany z innymi zależnościami.  

## <a name="data-usage"></a>Użycie danych

Podczas wdrażania Azure Monitor dla maszyn wirtualnych dane zbierane przez maszyny wirtualne są pozyskiwane i przechowywane w Azure Monitor. Metryki kryteriów kondycji są przechowywane w Azure Monitor w bazie danych szeregów czasowych, zebrane dane dotyczące wydajności i zależności są przechowywane w Log Analytics obszarze roboczym. W oparciu o Cennik opublikowany na [stronie cennika Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/)Azure monitor dla maszyn wirtualnych opłaty są naliczane za:

- Dane, które są pozyskiwane i przechowywane.
- Liczba monitorowanych szeregów czasowych metryk kryteriów kondycji.
- Utworzone reguły alertów.
- Wysyłane powiadomienia. 

Rozmiar dziennika zmienia się w zależności od długości ciągu liczników wydajności i może zwiększyć liczbę dysków logicznych i kart sieciowych przypisanych do maszyny wirtualnej. Jeśli masz już obszar roboczy i zbierasz te liczniki, nie są stosowane żadne powtarzające się opłaty. Jeśli już używasz Service Map, jedyną zmianą, która zobaczysz, jest dodatkowe dane połączenia, które są wysyłane do Azure Monitor.

## <a name="next-steps"></a>Następne kroki

Aby poznać wymagania i metody, które ułatwiają monitorowanie maszyn wirtualnych, zobacz [wdrażanie Azure monitor dla maszyn wirtualnych](vminsights-enable-overview.md).

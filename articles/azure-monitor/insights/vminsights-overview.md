---
title: Co to jest usługa Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)? | Microsoft Docs
description: Usługa Azure Monitor dla maszyn wirtualnych to funkcja usługi Azure monitor, który łączy kondycji i wydajności monitorowania systemu operacyjnego maszyny Wirtualnej platformy Azure, a także automatycznie odnajdywania składników aplikacji i zależności z innymi zasobami oraz mapuje komunikację między nimi. Ten artykuł zawiera omówienie.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/07/2018
ms.author: magoedte
ms.openlocfilehash: d37ca7d46f1231a8e1b0b258c10089fe6ba81fba
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2018
ms.locfileid: "51715548"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>Co to jest usługa Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)?

Usługa Azure Monitor dla maszyn wirtualnych monitoruje usługi Azure virtual machines (VM) i zestawy skalowania maszyn wirtualnych platformy Azure na dużą skalę, analizując wydajności i kondycji Windows i maszyn wirtualnych systemu Linux, łącznie z różnych procesów i wzajemnie połączonych zależności od innych zasoby i procesy zewnętrzne. Rozwiązanie obejmuje obsługę monitorowania wydajności i zależności aplikacji w przypadku maszyn wirtualnych hostowanych lokalnie lub innego dostawcy chmury. Obejmuje trzy kluczowe funkcje do tego wglądu w szczegółowe dane:

* Logiczne składniki maszyn wirtualnych platformy Azure, systemem operacyjnym Windows i Linux są mierzone w oparciu o określone kryteria kondycji wstępnie skonfigurowanych i alertów na po spełnieniu warunku ocenione.  
* Podstawowe metryki wydajności procesora, pamięci, dysku i karty sieciowej w systemie operacyjnym gościa maszyny Wirtualnej są zbierane i znajdujące się w predefiniowanych popularne wykresy wydajności.
* Mapa zależności pokazujący odnalezionych powiązanych elementów z tej maszyny Wirtualnej z wielu grup zasobów i subskrypcji.  

Te funkcje są podzielone na trzy perspektyw:

* Health
* Wydajność
* Mapa

>[!NOTE]
>Obecnie funkcję kondycji jest oferowana tylko dla maszyn wirtualnych platformy Azure i skalowania maszyn wirtualnych zestawów. Wydajność i mapy obsługuje maszyny wirtualne platformy Azure i maszyn wirtualnych hostowanych w danym środowisku lub innych dostawców chmury.
>

Integracja z usługą Log Analytics oferuje zaawansowane agregacji, filtrowanie i możliwość wykonywania analizy trendu danych wraz z upływem czasu. Kompleksowe monitorowanie obciążenia nie mogą być osiągnięte samodzielnie za pomocą usługi Azure Monitor, Usługa Service Map lub usługi Log Analytics.  

Dane te można wyświetlić bezpośrednio w kontekście jednej maszyny Wirtualnej z maszyny wirtualnej lub z usługą Azure Monitor zapewnia zagregowany widok dla Twoich maszyn wirtualnych, w oparciu o następujące perspektywy dla każdej funkcji:

* Kondycja — maszyny wirtualne powiązane z grupą zasobów
* Mapy i wydajność — maszyny wirtualne są skonfigurowane do zgłaszania do określonego obszaru roboczego usługi Log Analytics

![Maszyna wirtualna perspektywy szczegółowych informacji z portalu](./media/vminsights-overview/vminsights-azmon-directvm-01.png)

DevOps można skutecznie dostarczać przewidywalnej wydajności i dostępności ważnych aplikacji przez identyfikowanie zdarzeń krytycznych systemu operacyjnego i wąskie gardła wydajności, w przypadku problemów z siecią i zrozumieć, czy problem jest związany innych zależności.  

## <a name="data-usage"></a>Użycie danych 

Dołączanie usługi Azure Monitor dla maszyn wirtualnych, danych zbieranych przez maszyny wirtualne jest zaraz po pozyskiwane i przechowywane w usłudze Azure Monitor. Usługa Azure Monitor dla maszyn wirtualnych są naliczane opłaty za dane pozyskiwane i przechowywane szeregu kryteriów kondycji tworzone metryki reguły monitorowanych, alertu szeregów czasowych, powiadomienia wysyłane za opublikowane w usłudze Azure Monitor [stronę z cennikiem](https://azure.microsoft.com/pricing/details/monitor/)

Rozmiar dziennika w zależności od długości parametrów liczników i może zwiększyć liczbę dysków logicznych i karty sieciowe. Jeśli masz już obszar roboczy ale są zbierane następujące liczniki, istnieje nie będzie żadnych zduplikowanych opłaty stosowane. Jeśli używane są rozwiązania Service Map, jedyna zmiana, który będzie wyświetlony jest dodatkowe połączenie danych wysyłanych do usługi Azure Monitor.

## <a name="next-steps"></a>Kolejne kroki
Przegląd [dołączanie usługi Azure Monitor dla maszyn wirtualnych](vminsights-onboard.md) , aby zrozumieć wymagania i metody, aby włączyć monitorowanie maszyn wirtualnych.

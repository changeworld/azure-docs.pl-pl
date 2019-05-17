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
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2019
ms.author: magoedte
ms.openlocfilehash: 7d86b3fe9aeddd603d0c40b1c760cabdee42e396
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2019
ms.locfileid: "65522106"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>Co to jest usługa Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)?

Usługa Azure Monitor dla maszyn wirtualnych monitoruje usługi Azure virtual machines (VM) i zestawy skalowania maszyn wirtualnych na dużą skalę. Analizuje ona wydajność i kondycję Twoich maszyn wirtualnych z systemami Windows i Linux oraz monitoruje ich procesy i zależności od innych zasobów oraz procesów zewnętrznych. 

Rozwiązanie usługi Azure Monitor dla maszyn wirtualnych obejmuje obsługę monitorowania wydajności i aplikacji zależności dla maszyn wirtualnych, które są hostowane lokalnie lub u innego dostawcy chmury. Trzy kluczowe funkcje dostarczać dokładniejsze omówienie:

* **Logiczne składniki maszyn wirtualnych platformy Azure, który uruchamiany Windows i Linux**: Są mierzone względem kryteria kondycji wstępnie skonfigurowane, i ich wysyłania alertów, gdy ocenianą warunek jest spełniony.  

* **Wstępnie zdefiniowane popularne wykresy wydajności**: Wyświetla podstawowe metryki wydajności z systemu operacyjnego gościa maszyny Wirtualnej.

* **Mapa zależności**: Wyświetla powiązanych elementów z maszyną Wirtualną z różnych subskrypcji i grupy zasobów.  

Funkcje są podzielone na trzy perspektyw:

* Health
* Wydajność
* Mapa

>[!NOTE]
>Obecnie funkcję kondycji jest oferowana tylko w przypadku maszyn wirtualnych platformy Azure. Wydajność i funkcje mapy obsługuje maszyny wirtualne platformy Azure, zestawy skalowania maszyn wirtualnych platformy Azure i maszyn wirtualnych, które są hostowane w środowisku lub innych dostawców chmury.

Integracja przy użyciu dzienników usługi Azure Monitor zapewnia zaawansowane agregacji i filtrowania, a jego analizowanie trendów danych wraz z upływem czasu. Takie obciążenia kompleksowe monitorowanie nie można osiągnąć za pomocą usługi Azure Monitor lub mapy usługi autonomicznej.  

Dane te można wyświetlić bezpośrednio w pojedynczej maszyny Wirtualnej z maszyny wirtualnej lub usługi Azure Monitor umożliwia dostarczanie zagregowany widok maszyn wirtualnych. Ten widok jest oparty na perspektywy każdą z tych funkcji:

* **Kondycja**: Maszyny wirtualne są powiązane z grupą zasobów.
* **Mapa** i **wydajności**: Maszyny wirtualne są skonfigurowane do raportu do określonego obszaru roboczego usługi Log Analytics.

![Maszyna wirtualna perspektywy szczegółowych informacji w witrynie Azure portal](./media/vminsights-overview/vminsights-azmon-directvm-01.png)

Usługa Azure Monitor dla maszyn wirtualnych mogą dostarczać przewidywalnej wydajności i dostępności najważniejszych aplikacji. Identyfikuje zdarzeń krytycznych systemu operacyjnego, wąskich gardeł wydajności i problemy z siecią. Usługa Azure Monitor dla maszyn wirtualnych może także ułatwić zrozumienie, czy problem jest związany z innych zależności.  

## <a name="data-usage"></a>Użycie danych 

Podczas wdrażania usługi Azure Monitor dla maszyn wirtualnych, danych, które są zbierane przez maszyny wirtualne jest pozyskiwane i przechowywane w usłudze Azure Monitor. Metryki kryteria kondycji są przechowywane w usłudze Azure Monitor w bazie danych szeregów czasowych, dane wydajności i zależności, zbierane są przechowywane w obszarze roboczym usługi Log Analytics. Oparte na temat cen, które opublikowano na [usługi Azure Monitor stronę z cennikiem](https://azure.microsoft.com/pricing/details/monitor/), usługi Azure Monitor dla maszyn wirtualnych jest naliczana za:

* Dane pozyskiwane i przechowywane.
* Liczba kondycji kryteria metryki szeregów czasowych są monitorowane.
* Reguły alertów, które są tworzone.
* Powiadomienia, które są wysyłane. 

Rozmiar dziennika jest zależna od długości parametrów liczników wydajności i może zwiększyć liczbę dysków logicznych i karty sieciowe, przydzielone do maszyny Wirtualnej. Jeśli już masz obszaru roboczego i są zbierane następujące liczniki są stosowane nie podwójne naliczenie opłaty. Jeśli już korzystasz z rozwiązania Service Map, zobaczysz Jedyna zmiana jest dane dodatkowe połączenie, które są wysyłane do usługi Azure Monitor.

## <a name="next-steps"></a>Kolejne kroki
Aby poznać wymagania i metod, które pomagają monitorować maszyny wirtualne, zobacz [wdrożenia usługi Azure Monitor dla maszyn wirtualnych](vminsights-enable-overview.md).

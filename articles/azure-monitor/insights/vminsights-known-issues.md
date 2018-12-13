---
title: Usługa Azure Monitor znane problemy dotyczące maszyn wirtualnych (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: W tym artykule opisano znane problemy z usługą Azure Monitor dla maszyn wirtualnych, rozwiązanie na platformie Azure, który łączy monitorowania kondycji i wydajności systemu operacyjnego maszyny Wirtualnej platformy Azure. Usługa Azure Monitor dla maszyn wirtualnych automatycznie odnajduje składniki aplikacji i zależności z innymi zasobami i mapuje komunikację między nimi.
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
ms.date: 11/07/2018
ms.author: magoedte
ms.openlocfilehash: b2a60ddee58a7a9c37582085132ee8a1df767cb4
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2018
ms.locfileid: "53190360"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>Znane problemy z usługą Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)

W tym artykule opisano znane problemy z usługą Azure Monitor dla maszyn wirtualnych, rozwiązanie na platformie Azure, który łączy monitorowania kondycji i wydajności systemu operacyjnego maszyny Wirtualnej platformy Azure. 

Następujące znane problemy z funkcją kondycji:

- Funkcja kondycji jest włączona dla wszystkich maszyn wirtualnych, które są połączone z obszarem roboczym usługi Log Analytics. To dlatego nawet kiedy akcja rozpoczyna się i kończy się na jednej maszynie Wirtualnej.
- Po zakończeniu Wyłącz monitorowanie dla maszyny Wirtualnej za pomocą obsługiwanych metod i przystąpieniem do wdrażania go ponownie, należy wdrożyć ją w tym samym obszarze roboczym. Jeśli używasz nowy obszar roboczy i spróbuj, aby wyświetlić stan kondycji dla tej maszyny Wirtualnej, może zawierać nietypowego zachowania.
- Jeśli Maszynę wirtualną platformy Azure zostało usunięte lub usunięte, jest on wyświetlany w widoku listy maszyn wirtualnych przez 3 – 7 dni. Ponadto kliknięcie stan usunięto ani nie usunięto maszyny Wirtualnej spowoduje otwarcie **Diagnostyka kondycji** wyświetlić, a następnie inicjuje pętli ładowania. Wybierając nazwę usuniętej maszyny Wirtualnej powoduje otwarcie okienka z komunikat informujący, że maszyna wirtualna została usunięta.
- W tej wersji nie można zmodyfikować okres czasu i częstotliwość kryteria kondycji. 
- Nie można wyłączyć kryteria kondycji. 
- Po wdrożeniu, może upłynąć czas, po którym dane są wyświetlane w **usługi Azure Monitor** > **maszyn wirtualnych** > **kondycji** okienka lub  **Zasób maszyny Wirtualnej** > **Insights** okienka.
- Diagnostyka kondycji środowiska aktualizacje szybciej niż w innych widokach. Informacje mogą zostać opóźnione, gdy przełączać się między widokami. 
- Podsumowanie alertów, znajdującej się w usłudze Azure Monitor dla maszyny Wirtualnej wyświetla tylko alerty, że w wyniku problemów z kondycją, które są wykrywane przy użyciu monitorowane maszyny wirtualne platformy Azure.
- **Listy alertów** okienko w pojedynczej maszyny Wirtualnej i usługi Azure Monitor wyświetla alerty, w której warunek monitora jest ustawiona na *wywoływane* w ciągu ostatnich 30 dni. Alerty nie są konfigurowalne. Jednak po ukończeniu **Lista alertów** zostanie otwarte okienko, możesz kliknąć pozycję podsumowanie, aby zmienić wartość filtru zarówno monitor stanu i zakres czasu.
- W **listy alertów** okienko, zaleca się nie modyfikowanie **typ zasobu**, **zasobów**, i **usługi Monitor** filtrów. One już skonfigurowano specyficzne dla rozwiązania. Ten widok listy zawiera więcej pól niż **usługa Azure monitor** > **alerty** jest widok listy.   
- Na maszynach wirtualnych z systemem Linux **Diagnostyka kondycji** widoku wyświetla nazwę całej domeny maszyny Wirtualnej, zamiast nazwy maszyny Wirtualnej użytkownika.
- Zamykanie maszyny wirtualne aktualizuje niektóre kryteria kondycji *krytyczne* i nie tylko na *dobrej kondycji*. Net stan maszyny Wirtualnej jest wyświetlany jako *krytyczne*.
- Nie można zmodyfikować ważność alertu kondycji. Można tylko włączone lub wyłączone. Ponadto niektóre poziomy ważności są aktualizowane na podstawie stanu kondycji kryteriów.
- W przypadku zmodyfikowania wszystkie ustawienia wystąpienia kryterium kondycji wszystkich wystąpień tego samego typu na maszynie Wirtualnej kryteria kondycji są modyfikowane. Na przykład w przypadku modyfikowania próg wystąpienia kryterium kondycji wolnego miejsca na dysku, które odnosi się do dysku logicznego C: tego progu ma zastosowanie do wszystkich innych dysków logicznych, które są odnajdywane i monitorowane pod kątem tej samej maszyny Wirtualnej.  
- Progi dla kryteria kondycji, których platformą docelową maszyny Wirtualnej z systemem Windows nie są można modyfikować, ponieważ ich stany kondycji są ustawione na *systemem* lub *dostępne*. Podczas wysyłania zapytania stanu kondycji z [API Monitor obciążenia](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager), wyświetla *OperatorPorównania* wartość **mniejsze** lub **większe** z *próg* wartość **4** usługi lub jednostki jeśli:
   - Kondycja usługi klienta DNS — usługa nie jest uruchomiona. 
   - Kondycja usługi klienta DHCP — usługa nie jest uruchomiona. 
   - Kondycja usługi RPC — usługa nie jest uruchomiona. 
   - Kondycja usługi zapory Windows — usługa nie jest uruchomiona.
   - Kondycja usługi dziennika zdarzeń Windows — usługa nie jest uruchomiona. 
   - Kondycja usługi Serwer — usługa nie jest uruchomiona. 
   - Kondycja usługi zdalnego zarządzania Windows — usługi nie jest uruchomiony. 
   - Błąd systemu plików lub uszkodzeniem — dysku logicznego jest niedostępny.

- Progi następujące kryteria kondycji systemu Linux nie są można modyfikować, ponieważ ich kondycji, są już ustawione na *true*. Wyświetla stan kondycji *OperatorPorównania* z wartością **mniejsze** i *próg* wartość **1** po otrzymaniu kwerendy od Obciążenie monitorowania interfejsu API dla jednostki, w zależności od jej kontekstu:
   - Stan dysku logicznego — dysk logiczny nie jest online / dostępne
   - Stan dysku — dysku nie jest online / dostępne
   - Stan karty sieciowej — karty sieciowej jest wyłączona.  

- *Łączny procesora* kryterium kondycji w Windows wyświetla próg **nie jest równa 4** zarówno w portalu, jak i interfejs API monitorowania obciążenia. Osiągnięciu progu, kiedy *łączny procesora* jest większa niż 95% i system długość kolejki jest większa niż 15. Nie można zmodyfikować tego kryterium kondycji w tej wersji. 
- Nawet wtedy, gdy portal lub interfejsu API Monitor obciążenie może je wykonać natychmiastową aktualizację, zmiany konfiguracji, takie jak aktualizowanie wartości progowej, potrwać do 30 minut. 
- Poszczególne procesora i kryteria kondycji poziomu procesor logiczny nie są dostępne w Windows. Tylko łączne wykorzystanie procesora CPU dostępnej dla maszyn wirtualnych Windows. 
- Reguły alertów, które są zdefiniowane dla każdego kryterium kondycji nie są wyświetlane w witrynie Azure portal. Można włączać lub wyłączać alert o kondycji reguły tylko w [API Monitor obciążenia](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager). 
- Nie można przypisać [grupy akcji usługi Azure Monitor](../../azure-monitor/platform/action-groups.md) dla alertów dotyczących kondycji w witrynie Azure portal. Tylko powiadomienia ustawienie interfejsu API umożliwiają skonfigurowanie grupy akcji wyzwolenie przy każdym wyzwoleniu alertu kondycji. Obecnie można przypisać grup akcji względem maszyny Wirtualnej tak, aby wszystkie *alerty dotyczące kondycji* uruchamiane względem wyzwalacza maszyn wirtualnych tej samej grupy akcji. W przeciwieństwie do tradycyjnych alertów platformy Azure nie obowiązuje koncepcja grupy osobną akcję dla każdej reguły alertu kondycji. Ponadto podczas są wyzwalane przez alerty dotyczące kondycji są obsługiwane tylko grupy akcji, które są skonfigurowane do dostarczania poczty e-mail lub powiadomienia SMS. 

## <a name="next-steps"></a>Kolejne kroki
Aby poznać wymagania i metod umożliwiających monitorowanie maszyn wirtualnych, zobacz [wdrożenia usługi Azure Monitor dla maszyn wirtualnych](vminsights-onboard.md).

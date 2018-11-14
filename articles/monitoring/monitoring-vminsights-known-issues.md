---
title: Usługa Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza), znane problemy z | Dokumentacja firmy Microsoft
description: Usługa Azure Monitor dla maszyn wirtualnych to rozwiązanie na platformie Azure, która łączy kondycji i wydajności monitorowania systemu operacyjnego maszyny Wirtualnej platformy Azure, a także automatycznie odnajdywania składników aplikacji i zależności z innymi zasobami oraz mapuje komunikację między je. W tym artykule opisano znane problemy.
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
ms.openlocfilehash: 0f0ef83feac1a40c93910abdbd6abcada1a4d328
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2018
ms.locfileid: "51613889"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>Znane problemy z usługą Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)

Następujące znane problemy z funkcją usługi Azure Monitor kondycji dla maszyn wirtualnych:

- Funkcja kondycji jest włączona dla wszystkich maszyn wirtualnych, połączone z obszarem roboczym usługi Log Analytics, nawet wtedy, gdy jest inicjowane i ukończyć z jednej maszyny Wirtualnej.
- Jeśli po wyłączeniu monitorowanie dla maszyny Wirtualnej przy użyciu obsługiwanych metod, podczas dołączania zostanie podjęta ponownie, należy to zrobić, na tym samym obszarze roboczym.  Jeśli nowy obszar roboczy jest używany podczas wyświetlania stanu kondycji dla tej maszyny Wirtualnej, może pokazywać nietypowego zachowania.
- Jeśli Maszynę wirtualną platformy Azure nie istnieje więcej, ponieważ został usunięty albo usunięta, jej pojawi się w widoku listy maszyn wirtualnych przez 3 – 7 dni. Ponadto kliknięcie stan usunięto ani nie usunięto maszyny Wirtualnej może uruchomić **Diagnostyka kondycji** widok, który następnie przechodzi w pętli ładowania. Wybierając nazwę usuniętej maszyny Wirtualnej spowoduje uruchomienie bloku komunikat informujący, że maszyna wirtualna została usunięta.
- W tej wersji nie można zmodyfikować okres czasu i częstotliwość kryteria kondycji. 
- Nie można wyłączyć kryteria kondycji. 
- Po dołączeniu, może upłynąć czas, po którym dane są wyświetlane w usłudze Azure Monitor -> maszyny wirtualne -> kondycja lub z bloku zasobu maszyny Wirtualnej -> szczegółowe informacje
- Diagnostyka kondycji środowiska szybciej niż w innych widokach aktualizacji, dzięki czemu mogą wystąpić opóźnienia informacji podczas przełączania się między widokami  
- Podsumowanie alertów zawarte w usłudze Azure Monitor dla maszyny Wirtualnej są przeznaczone tylko dla alertów została wyzwolona dla problemów dotyczących kondycji wykrytych za pomocą monitorowanych maszynach wirtualnych platformy Azure.
- **Listy alertów** widoku strony w pojedynczej maszyny Wirtualnej i usługi Azure Monitor przedstawia alerty, której warunek monitora jest ustawiona na "wyzwolone" w ostatnich 30 dni.  Nie są one konfigurowane. Jednak po raz klikając podsumowania **Lista alertów** strona widoku jest uruchamiany, można zmienić wartość filtru zarówno monitor stanu i zakres czasu.
- Na **listy alertów** strona widoku, zalecamy nie modyfikują **typ zasobu**, **zasobów**, i **usługi Monitor** filtrów w miarę ich skonfigurowano specyficzne dla rozwiązania (ten widok listy przedstawiono niektóre dodatkowe pola w porównaniu do usługi Azure monitor -> Widok listy alertów).    
- Na maszynach wirtualnych z systemem Linux **Diagnostyka kondycji** widok zawiera nazwę domeny całą maszynę Wirtualną zamiast nazwy maszyny Wirtualnej użytkownika.
- Zamykanie maszyny wirtualne zaktualizuje część jej kryteria kondycji do stanu krytycznego i innych użytkowników do prawidłowego stanu ze stanem sieci maszyny wirtualnej w stanie krytycznym.
- Ważność alertu kondycji nie może być modyfikowany, ich można tylko włączać lub wyłączać.  Ponadto niektóre ważności aktualizacji na podstawie stanu kondycji kryteriów.
- Modyfikowanie wszelkie ustawienia wystąpienia kryterium kondycji, doprowadzi do modyfikacji tego samego ustawienia we wszystkich wystąpieniach kryteria kondycji tego samego typu na maszynie Wirtualnej. Na przykład jeśli próg dysku wolnego miejsca na kondycji kryterium wystąpienia odpowiadający dysku logicznego C: zostanie zmodyfikowany, a następnie tego progu będą stosowane do wszystkich innych dysków logicznych odnalezione i monitorowane pod kątem tej samej maszyny Wirtualnej.   
- Progi dla następujące kryteria kondycji przeznaczonych dla maszyny Wirtualnej z systemem Windows nie można modyfikować, ponieważ ich stany kondycji są już ustawione **systemem** lub **dostępne**. Po otrzymaniu kwerendy od [API Monitor obciążenia](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager), przedstawia stan kondycji *OperatorPorównania* wartość **mniejsze** lub **większe**z *próg* wartość **4** usługi lub jednostki jeśli:
   - Kondycja usługi klienta DNS — usługa nie jest uruchomiona. 
   - Kondycja usługi klienta DHCP — usługa nie jest uruchomiona. 
   - Kondycja usługi RPC — usługa nie działa 
   - Kondycja usługi zapory Windows — usługa nie działa
   - Kondycja usługi dziennika zdarzeń Windows — usługa nie działa 
   - Kondycja usługi Serwer — usługa nie działa 
   - Kondycja usługi zdalnego zarządzania Windows — usługa nie jest uruchomiona. 
   - Błąd systemu plików lub uszkodzeniem — dysku logicznego jest niedostępny

- Progi dla następujące kryteria kondycji systemu Linux nie można modyfikować, ponieważ ich kondycji są już ustawione **true**.  Przedstawia stan kondycji *OperatorPorównania* z wartością **mniejsze** i *próg* wartość **1** po otrzymaniu kwerendy od obciążenia Monitorowanie interfejsu API dla jednostki, w zależności od jej kontekstu:
   - Stan dysku logicznego — dysk logiczny nie jest online / dostępne
   - Stan dysku — dysku nie jest online / dostępne
   - Stan karty sieciowej — karty sieciowej jest wyłączona.  

- **Łączna liczba wykorzystanie procesora CPU** kryterium kondycji w Windows pokazuje próg **nie jest równa 4** z portalu i po otrzymaniu kwerendy z interfejsu API monitorowania obciążenia, gdy użycie procesora CPU jest większa niż 95% i długość kolejki systemu większe niż 15. Nie można zmodyfikować tego kryterium kondycji w tej wersji.  
- Zmiany konfiguracji, takie jak aktualizowanie wartości progowej, trwa maksymalnie 30 minut zaczęły obowiązywać, nawet jeśli portal lub interfejsu API Monitor obciążenie może wykonać natychmiastową aktualizację.  
- Poszczególne procesora i kryteria kondycji poziomu procesor logiczny nie są dostępne w Windows, tylko **łączne wykorzystanie procesora CPU** jest dostępna dla maszyn wirtualnych Windows.  
- Reguły alertów zdefiniowane dla każdego kryterium kondycji nie są widoczne w witrynie Azure portal. Są one konfigurowane z [API Monitor obciążenia](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager) Aby włączyć lub wyłączyć regułę alertu kondycji.  
- Przypisywanie [grupy akcji usługi Azure Monitor](../monitoring-and-diagnostics/monitoring-action-groups.md) programu health alertów nie jest możliwe w witrynie Azure portal. Interfejs API ustawienie powiadomień umożliwiają tylko skonfigurowanie grupy akcji wyzwolenie przy każdym wyzwoleniu alertu kondycji. Obecnie można przypisać grup akcji względem maszyny Wirtualnej tak, aby wszystkie *alerty dotyczące kondycji* uruchamiane względem wyzwalacza maszyny Wirtualnej tych samych grup akcji. Nie obowiązuje koncepcja grupy osobną akcję dla każdego kondycji reguły alertu, takie jak tradycyjnych alertów platformy Azure. Ponadto podczas są wyzwalane przez alerty dotyczące kondycji są obsługiwane tylko grupy akcji skonfigurowana do wysyłania powiadomień, wysyłając wiadomość e-mail lub SMS. 

## <a name="next-steps"></a>Kolejne kroki
Przegląd [dołączanie usługi Azure Monitor dla maszyn wirtualnych](monitoring-vminsights-onboard.md) , aby zrozumieć wymagania i metody, aby włączyć monitorowanie maszyn wirtualnych.
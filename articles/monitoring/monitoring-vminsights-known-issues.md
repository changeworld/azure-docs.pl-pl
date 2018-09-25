---
title: Usługa Azure Monitor znanych problemów, maszyny wirtualne | Dokumentacja firmy Microsoft
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
ms.date: 09/18/2018
ms.author: magoedte
ms.openlocfilehash: 819c3e74355cf80c7a998abb8b02b10c9e077059
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47062772"
---
# <a name="known-issues-with-azure-monitor-for-vms"></a>Znane problemy z usługą Azure Monitor dla maszyn wirtualnych

Następujące znane problemy z funkcją usługi Azure Monitor kondycji dla maszyn wirtualnych:

- W tej wersji nie można zmodyfikować okres czasu i częstotliwość kryteria kondycji. 
- Nie można wyłączyć kryteria kondycji. 
- Po dołączeniu, może upłynąć czas, po którym dane są wyświetlane w usłudze Azure Monitor -> maszyny wirtualne -> kondycja lub z bloku zasobu maszyny Wirtualnej -> szczegółowe informacje
- Diagnostyka kondycji środowiska szybciej niż w innych widokach aktualizacji, dzięki czemu mogą wystąpić opóźnienia informacji podczas przełączania się między widokami  
- Podsumowanie alertów przewidzianych w usłudze Azure Monitor kondycji maszyny Wirtualnej są przeznaczone tylko dla alertów została wyzwolona dla problemów dotyczących kondycji wykrytych za pomocą monitorowanych maszynach wirtualnych platformy Azure.
- **Listy alertów** widoku strony w pojedynczej maszyny Wirtualnej i usługi Azure Monitor przedstawia alerty, której warunek monitora jest ustawiona na "wyzwolone" w ostatnich 30 dni.  Nie są one konfigurowane. Jednak po raz klikając podsumowania **Lista alertów** strona widoku jest uruchamiany, można zmienić wartość filtru zarówno monitor stanu i zakres czasu.
- Na **listy alertów** strona widoku, zalecamy nie modyfikują **typ zasobu**, **zasobów**, i **usługi Monitor** filtrów w miarę ich skonfigurowano specyficzne dla rozwiązania (ten widok listy przedstawiono niektóre dodatkowe pola w porównaniu do usługi Azure monitor -> Widok listy alertów).    
- Na maszynach wirtualnych z systemem Linux **Diagnostyka kondycji** widok zawiera nazwę domeny całą maszynę Wirtualną zamiast nazwy maszyny Wirtualnej użytkownika.
- Zamykanie maszyny wirtualne zaktualizuje część jej kryteria kondycji do stanu krytycznego i innych użytkowników do prawidłowego stanu ze stanem sieci maszyny wirtualnej w stanie krytycznym.
- Ważność alertu kondycji nie może być modyfikowany, ich można tylko włączać lub wyłączać.  Ponadto niektóre ważności aktualizacji na podstawie stanu kondycji kryteriów.
- Modyfikowanie wszelkie ustawienia wystąpienia kryterium kondycji, doprowadzi do modyfikacji tego samego ustawienia we wszystkich wystąpieniach kryteria kondycji tego samego typu na maszynie Wirtualnej. Na przykład jeśli próg dysku wolnego miejsca na kondycji kryterium wystąpienia odpowiadający dysku logicznego C: zostanie zmodyfikowany, a następnie tego progu będą stosowane do wszystkich innych dysków logicznych odnalezione i monitorowane pod kątem tej samej maszyny Wirtualnej.   
- Progi dla kryteriów kondycji Windows, takich jak kondycja usługi klienta DNS nie można modyfikować, ponieważ ich dobrej kondycji jest już ograniczona do **systemem**, **dostępne** stanu usługi lub jednostki w zależności od kontekstu.  Zamiast tego wartość jest reprezentowana przez liczbę 4, zostanie przekonwertowany na ciąg wyświetlany w przyszłej wersji.  
- Progi dla niektórych kryteria kondycji systemu Linux nie są można modyfikować, takich jak kondycja dysku logicznego, ponieważ są one już ustawione wyzwalacza w stan złej kondycji.  Oznaczają one, czy coś, co jest online/offline, lub włączone czy wyłączone i są reprezentowane i wskazywać takie same, pokazując wartość 1 lub 0.
- Trwa aktualizowanie filtru grupy zasobów w dowolnej grupie zasobów, podczas korzystania z na dużą skalę usługa Azure monitor -> maszyny wirtualne -> kondycja -> dowolny widok listy z wybranych subskrypcji i grupie zasobów, spowoduje, że widok listy, aby wyświetlić **żadnego wyniku**.  Wróć do usługi Azure Monitor -> maszyny wirtualne -> karta kondycji i wybierz odpowiednią subskrypcję i grupę zasobów, a następnie przejdź do widoku listy.

## <a name="next-steps"></a>Kolejne kroki
Przegląd [dołączanie usługi Azure Monitor dla maszyn wirtualnych](monitoring-vminsights-onboard.md) , aby zrozumieć wymagania i metody, aby włączyć monitorowanie maszyn wirtualnych.
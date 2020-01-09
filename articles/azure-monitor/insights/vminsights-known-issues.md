---
title: Usługa Azure Monitor znane problemy dotyczące maszyn wirtualnych (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: W tym artykule opisano znane problemy dotyczące Azure Monitor dla maszyn wirtualnych, rozwiązania na platformie Azure, które łączy kondycję, odnajdywanie zależności aplikacji i monitorowanie wydajności systemu operacyjnego Azure VM.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/02/2019
ms.openlocfilehash: b59e2d1897557b47bcfeafbc17141f869e2f192e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75450672"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>Znane problemy z usługą Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)

W tym artykule opisano znane problemy związane z Azure Monitor dla maszyn wirtualnych, rozwiązanie na platformie Azure, które łączy kondycję, odnajdywanie składników aplikacji i monitorowanie wydajności systemu operacyjnego Azure VM. 

## <a name="health"></a>Zdrowie 
Poniżej przedstawiono znane problemy związane z bieżącą wersją funkcji kondycji:

- Jeśli maszyna wirtualna platformy Azure zostanie usunięta lub usunięta, zostanie ona wyświetlona w widoku listy maszyn wirtualnych przez jakiś czas. Ponadto kliknięcie stan usunięto ani nie usunięto maszyny Wirtualnej spowoduje otwarcie **Diagnostyka kondycji** wyświetlić, a następnie inicjuje pętli ładowania. Wybierając nazwę usuniętej maszyny Wirtualnej powoduje otwarcie okienka z komunikat informujący, że maszyna wirtualna została usunięta.
- Nawet wtedy, gdy portal lub interfejsu API Monitor obciążenie może je wykonać natychmiastową aktualizację, zmiany konfiguracji, takie jak aktualizowanie wartości progowej, potrwać do 30 minut. 
- Diagnostyka kondycji jest aktualizowana szybciej niż w przypadku innych widoków. Informacje mogą być opóźnione po przełączeniu między nimi. 
- W przypadku maszyn wirtualnych z systemem Linux tytuł strony zawierający kryteria kondycji dla pojedynczego widoku maszyny wirtualnej ma całą nazwę domeny maszyny wirtualnej zamiast nazwy maszyny wirtualnej zdefiniowanej przez użytkownika. 
- Po wyłączeniu monitorowania dla maszyny wirtualnej przy użyciu jednej z obsługiwanych metod i ponowieniu próby wdrożenia należy wdrożyć ją w tym samym obszarze roboczym. Jeśli wybierzesz inny obszar roboczy i spróbujesz wyświetlić stan kondycji tej maszyny wirtualnej, może to spowodować niespójne zachowanie.
- Po usunięciu składników rozwiązania z obszaru roboczego można nadal zobaczyć stan kondycji maszyn wirtualnych platformy Azure; Zapoznaj się z wydajnością i zamapuj dane po przejściu do widoku w portalu. Dane będą ostatecznie wyświetlane w widoku wydajność i mapa po pewnym czasie; jednak widok kondycji będzie nadal przedstawiał stan kondycji maszyn wirtualnych. Opcja **Wypróbuj teraz** będzie dostępna, aby ponownie dołączyć tylko do widoków wydajności i map.

## <a name="next-steps"></a>Następne kroki
Aby poznać wymagania i metody włączania monitorowania maszyn wirtualnych, przejrzyj temat [włączanie Azure monitor dla maszyn wirtualnych](vminsights-enable-overview.md).

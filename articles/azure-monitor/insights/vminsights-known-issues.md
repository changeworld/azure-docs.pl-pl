---
title: Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) znane problemy | Microsoft Docs
description: W tym artykule opisano znane problemy dotyczące Azure Monitor dla maszyn wirtualnych, rozwiązania na platformie Azure, które łączy kondycję, odnajdywanie zależności aplikacji i monitorowanie wydajności systemu operacyjnego Azure VM.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.openlocfilehash: f6719a8c28571faceb6ebad0567d13a4edc60fe6
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553759"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>Znane problemy z Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)

W tym artykule opisano znane problemy związane z Azure Monitor dla maszyn wirtualnych, rozwiązanie na platformie Azure, które łączy kondycję, odnajdywanie składników aplikacji i monitorowanie wydajności systemu operacyjnego Azure VM. 

## <a name="health"></a>Zdrowie 
Poniżej przedstawiono znane problemy związane z bieżącą wersją funkcji kondycji:

- Jeśli maszyna wirtualna platformy Azure zostanie usunięta lub usunięta, zostanie ona wyświetlona w widoku listy maszyn wirtualnych przez jakiś czas. Ponadto kliknięcie stanu usuniętej lub usuniętej maszyny wirtualnej spowoduje otwarcie widoku **Diagnostyka kondycji** , a następnie zainicjowanie pętli ładowania. Wybranie nazwy usuniętej maszyny wirtualnej powoduje otwarcie okienka z komunikatem informującym o usunięciu maszyny wirtualnej.
- Zmiany konfiguracji, takie jak aktualizowanie progu, potrwają do 30 minut, nawet jeśli interfejs API monitora lub obciążenia może natychmiast je zaktualizować. 
- Diagnostyka kondycji jest aktualizowana szybciej niż w przypadku innych widoków. Informacje mogą być opóźnione po przełączeniu między nimi. 
- W przypadku maszyn wirtualnych z systemem Linux tytuł strony zawierający kryteria kondycji dla pojedynczego widoku maszyny wirtualnej ma całą nazwę domeny maszyny wirtualnej zamiast nazwy maszyny wirtualnej zdefiniowanej przez użytkownika. 
- Po wyłączeniu monitorowania dla maszyny wirtualnej przy użyciu jednej z obsługiwanych metod i ponowieniu próby wdrożenia należy wdrożyć ją w tym samym obszarze roboczym. Jeśli wybierzesz inny obszar roboczy i spróbujesz wyświetlić stan kondycji tej maszyny wirtualnej, może to spowodować niespójne zachowanie.
- Po usunięciu składników rozwiązania z obszaru roboczego można nadal zobaczyć stan kondycji maszyn wirtualnych platformy Azure; Zapoznaj się z wydajnością i zamapuj dane po przejściu do widoku w portalu. Dane będą ostatecznie wyświetlane w widoku wydajność i mapa po pewnym czasie; jednak widok kondycji będzie nadal przedstawiał stan kondycji maszyn wirtualnych. Opcja **Wypróbuj teraz** będzie dostępna, aby ponownie dołączyć tylko do widoków wydajności i map.

## <a name="next-steps"></a>Następne kroki
Aby poznać wymagania i metody włączania monitorowania maszyn wirtualnych, przejrzyj temat [włączanie Azure monitor dla maszyn wirtualnych](vminsights-enable-overview.md).

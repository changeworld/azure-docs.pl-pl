---
title: Usługa Azure Monitor znane problemy dotyczące maszyn wirtualnych (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: W tym artykule opisano znane problemy z usługą Azure Monitor dla maszyn wirtualnych, rozwiązanie na platformie Azure, który łączy, wykrywania zależności aplikacji, monitorowania kondycji i wydajności systemu operacyjnego maszyny Wirtualnej platformy Azure.
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
ms.date: 04/02/2019
ms.author: magoedte
ms.openlocfilehash: 86a56e71b89e7408d1bc8ca0ee1dc8112bea368f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65522142"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>Znane problemy z usługą Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)

W tym artykule opisano znane problemy z usługą Azure Monitor dla maszyn wirtualnych z rozwiązań platformy Azure, która łączy zdrowia, odnajdywania składników aplikacji i monitorowania wydajności systemu operacyjnego maszyny Wirtualnej platformy Azure. 

## <a name="health"></a>Health 
Następujące znane problemy związane z bieżącej wersji funkcji kondycji:

- Jeśli Maszynę wirtualną platformy Azure zostało usunięte lub usunięte, jest on wyświetlany w widoku listy maszyn wirtualnych za jakiś czas. Ponadto kliknięcie stan usunięto ani nie usunięto maszyny Wirtualnej spowoduje otwarcie **Diagnostyka kondycji** wyświetlić, a następnie inicjuje pętli ładowania. Wybierając nazwę usuniętej maszyny Wirtualnej powoduje otwarcie okienka z komunikat informujący, że maszyna wirtualna została usunięta.
- Nawet wtedy, gdy portal lub interfejsu API Monitor obciążenie może je wykonać natychmiastową aktualizację, zmiany konfiguracji, takie jak aktualizowanie wartości progowej, potrwać do 30 minut. 
- Diagnostyka kondycji środowiska aktualizacje szybciej niż inne widoki. Informacje mogą zostać opóźnione, podczas przełączania między nimi. 
- W przypadku maszyn wirtualnych systemu Linux tytuł strony ofercie kryteria kondycji dla pojedynczego widoku maszyny Wirtualnej o nazwie całej domeny maszyny Wirtualnej, zamiast nazwy maszyny Wirtualnej użytkownika. 
- Po wyłączeniu monitorowanie dla maszyny Wirtualnej przy użyciu jednej z obsługiwanych metod i przystąpieniem do wdrażania go ponownie, należy wdrożyć ją w tym samym obszarze roboczym. Jeśli wybierzesz inny obszar roboczy i spróbuj, aby wyświetlić stan kondycji dla tej maszyny Wirtualnej, może wyświetlać niespójne zachowanie.
- Po usunięciu składników rozwiązania z obszaru roboczego, może być nadal widoczny stan kondycji z maszynami wirtualnymi portalu Azure; w szczególności mapy danych o wydajności i po przejściu do widoku, albo w portalu. Dane po pewnym czasie zostanie zatrzymane, pojawiają się w widoku wydajności i mapy po pewnym czasie; Widok kondycji będą jednak nadal pokazują stan kondycji dla maszyn wirtualnych. **Wypróbuj teraz usługę** opcji będzie można ponownie dołączyć tylko widokach wydajności i mapy.

## <a name="next-steps"></a>Kolejne kroki
Aby poznać wymagania i metod umożliwiających monitorowanie maszyn wirtualnych, zobacz [włączyć usługi Azure Monitor dla maszyn wirtualnych](vminsights-enable-overview.md).

---
title: Zarządzanie wystąpieniami alertów
description: Zarządzanie wystąpień alertów na platformie Azure
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: a444d0c2ec5f2219dbffdeb6677d5e248c7004fe
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2018
ms.locfileid: "50414472"
---
# <a name="manage-alert-instances"></a>Zarządzanie wystąpieniami alertów
Za pomocą [ujednolicone środowisko alertów](https://aka.ms/azure-alerts-overview) w usłudze Azure Monitor, będą teraz widoczne z różnych typów alertów na platformie Azure, obejmujące wiele subskrypcji, w jednym jedną taflę szkła. W tym artykule przedstawiono sposób wyświetlania alertów wystąpień oraz jak rozszerzony w portalu, aby znaleźć konkretne wystąpienia alertu do rozwiązywania problemów.

1. Istnieją trzy sposoby, aby znaleźć na stronie alertów

   + W [portal](https://portal.azure.com/), wybierz opcję **Monitor** i w sekcji monitorowanie — wybierz **alerty**.  
    ![Monitorowanie](media/monitoring-alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
   + Możesz przejść do alertów z w ramach określonego **zasobów**. Po otwarciu zasobem nawigowanie po jego spisu treści w sekcji monitorowanie i wybierz **alerty**, ze stroną docelową wstępnie filtrowanego dla alertów dotyczących tego konkretnego zasobu.
   
    ![Monitorowanie](media/monitoring-alerts-managing-alert-instances/alert-resource.JPG)
    
   + Możesz przejść do alertów z w ramach określonego **grupy zasobów**. Po otwarciu grupę zasobów, nawigowanie po jego spisu treści w sekcji monitorowanie i wybierz **alerty**, ze stroną docelową wstępnie filtrowanego dla alertów dotyczących tej określonej grupie zasobów.    
   
    ![Monitorowanie](media/monitoring-alerts-managing-alert-instances/alert-rg.JPG)

1.  Nastąpi przejście **Podsumowanie alertów** strony, która zawiera przegląd wszystkich wystąpień alertu na platformie Azure. Widok podsumowania można zmodyfikować, wybierając **wiele subskrypcji** (maksymalnie 5) lub przez filtrowanie między **grup zasobów**, przeznaczone specjalnie **zasobów**, lub **przedziałów czasu**. Kliknij ten przycisk, łączna liczba alertów lub dowolnego z paskami ważności można przejść do widoku listy dla alertów.     
    ![Podsumowanie alertów](media/monitoring-alerts-managing-alert-instances/alerts-summary.jpg)
 
1.  Nastąpi przejście **wszystkie alerty** strony, gdzie zostaną wyświetlone wszystkie wystąpienia alertu na liście w platformie Azure. Jeśli z powiadomień o alertach podchodzisz do portalu, można użyć filtrów można zawęzić w sprawie tego konkretnego wystąpienia alertu. (**Uwaga**: jeśli pochodzi ze stroną, klikając na dowolnym pasm ważność, ta lista będzie wstępnie filtrowanego na tym poziomie ważności zostanie wyświetlony). Oprócz filtrów dostępnych na poprzedniej stronie można teraz również filtrować na podstawie usługi monitor (na przykład platforma dla metryk), warunek monitora (wywoływane lub usunięciu), ważność, stan alertu (nowy/potwierdzone/zamknięty) lub identyfikator grupy inteligentne.

    ![Wszystkie alerty](media/monitoring-alerts-managing-alert-instances/all-alerts.jpg)

    > [!NOTE]
    >  Jeśli pochodzi ze stroną, klikając na dowolnym pasm ważność, ta lista będzie wstępnie filtrowanego na tym poziomie ważności, gdy zostanie wyświetlony na tej stronie.
 
1.  Kliknięcie dowolnego wystąpienia alertu zostanie otwarty **szczegóły alertu** strony, dzięki czemu możesz się ze szczegółowymi analizami do informacji na temat tego konkretnego wystąpienia alertu.   
![Szczegóły alertu](media/monitoring-alerts-managing-alert-instances/alert-details.jpg)  

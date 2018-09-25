---
title: Stany alertów i inteligentne grupy zarządzania
description: Stany wystąpień alertu i inteligentne grupy zarządzania
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: f1c4e34f9c14a9ca273cd115c653f8075286ee2a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46994551"
---
# <a name="manage-alert-and-smart-group-states"></a>Stany alertów i inteligentne grupy zarządzania
Już alertami w usłudze Azure Monitor [alert, stan i warunek monitora](https://aka.ms/azure-alerts-overview) , i podobnie, inteligentne grup [inteligentnego stanu grupy](https://aka.ms/smart-groups). Zmiany stanu teraz są przechwytywane w historii skojarzone z odpowiedniej grupy alertu lub inteligentne. W tym artykule przedstawiono proces zmiany stanu alertu i inteligentne grupy.

## <a name="change-the-state-of-an-alert"></a>Zmienianie stanu alertu
1. Stan alertu można zmienić na następujące sposoby: 
    * Na stronie wszystkie ostrzeżenia kliknij pole wyboru obok alertów, które chcesz zmienić stan, a następnie kliknij przycisk Zmień stan.   
    ![Monitorowanie](./media/monitoring-alerts-managing-alert-states/state-all-alerts.jpg)
    * Na stronie Szczegóły alertu dla konkretnego wystąpienia alertu możesz kliknąć zmiany stanu   
    ![Monitorowanie](./media/monitoring-alerts-managing-alert-states/state-alert-details.jpg)
    * Dla konkretnego wystąpienia alertu na stronie Szczegóły alertu w okienku inteligentnych grup można kliknąć pole wyboru obok alertów, które chcesz    
    ![Monitorowanie](./media/monitoring-alerts-managing-alert-states/state-alert-details-sg.jpg)

    * Na stronie inteligentne szczegóły grupy na liście alertów członka możesz można kliknij pole wyboru obok alertów, które chcesz zmienić stan, a następnie kliknij przycisk Zmień Stateto zmiany stanu i kliknąć zmiany stanu.   
    ![Monitorowanie](./media/monitoring-alerts-managing-alert-states/state-sg-details-alerts.jpg)
1. Po kliknięciu przycisku zmiany stanu, okno podręczne otwiera pozwalając wybrać stan (nowy/potwierdzono/zamknięty) i wprowadź komentarz, jeśli to konieczne.   
![Monitorowanie](./media/monitoring-alerts-managing-alert-states/state-alert-change.jpg)
1. Po zakończeniu tej operacji zmiany stanu są rejestrowane w historii odpowiedni alert. To można wyświetlić, otwierając odpowiednich strony szczegółów i sprawdzanie sekcję historii.    
![Monitorowanie](./media/monitoring-alerts-managing-alert-states/state-alert-history.jpg)

## <a name="change-the-state-of-a-smart-group"></a>Zmiana stanu grupy inteligentne
1. Stan grupy inteligentne można zmienić na następujące sposoby:
    1. Na stronie listy grupy inteligentnych kliknąć pole wyboru obok grup inteligentne, które chcesz zmienić stan, a następnie kliknij przycisk Zmień stan  
    ![Monitorowanie](./media/monitoring-alerts-managing-alert-states/state-sg-list.jpg)
    1. Na stronie szczegółów grupy inteligentne możesz kliknąć zmiany stanu        
    ![Monitorowanie](./media/monitoring-alerts-managing-alert-states/state-sg-details.jpg)
1. Po kliknięciu przycisku zmiany stanu, okno podręczne otwiera pozwalając wybrać stan (nowy/potwierdzono/zamknięty) i wprowadź komentarz, jeśli to konieczne. 
![Monitorowanie](./media/monitoring-alerts-managing-alert-states/state-sg-change.jpg)
   > [!NOTE]
   >  Zmiana stanu inteligentne grupy nie powoduje zmiany stanu alerty poszczególnych elementów członkowskich.

1. Po zakończeniu tej operacji zmiany stanu są rejestrowane w historii odpowiednie właściwości grupy inteligentne. To można wyświetlić, otwierając odpowiednich strony szczegółów i sprawdzanie sekcję historii.     
![Monitorowanie](./media/monitoring-alerts-managing-alert-states/state-sg-history.jpg)

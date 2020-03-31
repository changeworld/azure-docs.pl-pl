---
title: Zarządzanie stanami alertów i grup inteligentnych
description: Zarządzanie stanami wystąpień alertów i grup inteligentnych
ms.subservice: alerts
ms.topic: conceptual
author: anantr
ms.date: 09/24/2018
ms.openlocfilehash: a22d7b7b962b36f93ee804c64b3bc6b08ecceb0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667520"
---
# <a name="manage-alert-and-smart-group-states"></a>Zarządzanie stanami alertów i grup inteligentnych

Alerty w usłudze Azure Monitor mają teraz [stan alertu i stan monitora,](https://aka.ms/azure-alerts-overview) a podobnie grupy inteligentne mają [stan grupy inteligentnej.](https://aka.ms/smart-groups) Zmiany stanu są teraz przechwytywane w historii skojarzonej z odpowiednim alertem lub grupą inteligentną. W tym artykule przeprowadzi Cię przez proces zmiany stanu, zarówno dla alertu, jak i grupy inteligentnej.

## <a name="change-the-state-of-an-alert"></a>Zmienianie stanu alertu

1. Stan alertu można zmienić na następujący sposoby: 
    * Na stronie Wszystkie alerty kliknij pole wyboru obok alertów, których chcesz zmienić stan, i kliknij pozycję zmień stan.   
    ![Monitorowanie](./media/alerts-managing-alert-states/state-all-alerts.jpg)
    * Na stronie Szczegóły alertu dla określonego wystąpienia alertu możesz kliknąć przycisk zmień stan   
    ![Monitorowanie](./media/alerts-managing-alert-states/state-alert-details.jpg)
    * Na stronie Szczegóły alertu dla określonego wystąpienia alertu w okienku Grupa inteligentna możesz kliknąć pole wyboru obok alertów, które chcesz    
    ![Monitorowanie](./media/alerts-managing-alert-states/state-alert-details-sg.jpg)

    * Na stronie Szczegółowe informacje o inteligentnej grupie na liście alertów członków można kliknąć pole wyboru obok alertów, których chcesz zmienić stan, i kliknąć pozycję Zmień stan, aby zmienić stan i kliknąć pozycję Zmień stan.   
    ![Monitorowanie](./media/alerts-managing-alert-states/state-sg-details-alerts.jpg)
1. Po kliknięciu przycisku Zmień stan zostanie otwarte okno dialogowe umożliwiające wybranie stanu (Nowy/Potwierdzony/Zamknięty) i wprowadzenie komentarza w razie potrzeby.   
![Monitorowanie](./media/alerts-managing-alert-states/state-alert-change.jpg)
1. Po wykonaniu tej zmiany stanu jest rejestrowana w historii odpowiedniego alertu. Można to wyświetlić, otwierając odpowiednią stronę Szczegóły i sprawdzając sekcję historii.    
![Monitorowanie](./media/alerts-managing-alert-states/state-alert-history.jpg)

## <a name="change-the-state-of-a-smart-group"></a>Zmienianie stanu grupy inteligentnej
1. Stan grupy inteligentnej można zmienić na następujący sposób:
    1. Na stronie listy Grupy inteligentne możesz kliknąć pole wyboru obok grup inteligentnych, których chcesz zmienić, i kliknąć pozycję Zmień stan  
    ![Monitorowanie](./media/alerts-managing-alert-states/state-sg-list.jpg)
    1. Na stronie Szczegółowe informacje o inteligentnej grupie możesz kliknąć przycisk Zmień stan        
    ![Monitorowanie](./media/alerts-managing-alert-states/state-sg-details.jpg)
1. Po kliknięciu przycisku Zmień stan zostanie otwarte okno dialogowe umożliwiające wybranie stanu (Nowy/Potwierdzony/Zamknięty) i wprowadzenie komentarza w razie potrzeby. 
![Monitorowanie](./media/alerts-managing-alert-states/state-sg-change.jpg)
   > [!NOTE]
   >  Zmiana stanu grupy inteligentnej nie zmienia stanu alertów poszczególnych członków.

1. Po wykonaniu tej funkcji zmiana stanu jest rejestrowana w historii odpowiedniej grupy inteligentnej. Można to wyświetlić, otwierając odpowiednią stronę Szczegóły i sprawdzając sekcję historii.     
![Monitorowanie](./media/alerts-managing-alert-states/state-sg-history.jpg)


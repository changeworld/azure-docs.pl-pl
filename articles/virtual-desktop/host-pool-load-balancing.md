---
title: Równoważenie obciążenia puli hostów pulpitu wirtualnego systemu Windows — platforma Azure
description: Metody równoważenia obciążenia puli hostów dla środowiska pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 193821ed0df09b87f19e45a82ca42026405a0dc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127859"
---
# <a name="host-pool-load-balancing-methods"></a>Metody równoważenia obciążenia puli hostów

Pulpit wirtualny systemu Windows obsługuje dwie metody równoważenia obciążenia. Każda metoda określa, który host sesji będzie hostować sesję użytkownika, gdy połączy się z zasobem w puli hostów.

W wirtualnym pulpicie systemu Windows dostępne są następujące metody równoważenia obciążenia:

- Równoważenie obciążenia o pierwszym rozpiętość umożliwia równomierne rozdzielanie sesji użytkownika między hostami sesji w puli hostów.
- Równoważenie obciążenia po pierwsze głębokości umożliwia nasycenie hosta sesji sesjami użytkownika w puli hostów. Gdy pierwsza sesja osiągnie próg limitu sesji, moduł równoważenia obciążenia kieruje wszystkie nowe połączenia użytkownika do następnego hosta sesji w puli hosta, dopóki nie osiągnie limitu i tak dalej.

Każda pula hostów może skonfigurować tylko jeden typ równoważenia obciążenia specyficzne dla niego. Jednak obie metody równoważenia obciążenia współużytkują następujące zachowania bez względu na to, w której puli hostów się znajdują:

- Jeśli użytkownik ma już sesję w puli hostów i ponownie łączy się z tą sesją, moduł równoważenia obciążenia pomyślnie przekieruje go do hosta sesji z istniejącą sesją. To zachowanie ma zastosowanie nawet wtedy, gdy właściwość AllowNewConnections tego hosta sesji jest ustawiona na False.
- Jeśli użytkownik nie ma jeszcze sesji w puli hostów, moduł równoważenia obciążenia nie będzie uwzględniał hostów sesji, których właściwość AllowNewConnections jest ustawiona na False podczas równoważenia obciążenia.

## <a name="breadth-first-load-balancing-method"></a>Metoda równoważenia obciążenia pierwszego szerokości

Metoda równoważenia obciążenia najpierw szerokość pozwala na dystrybucję połączeń użytkownika w celu optymalizacji w tym scenariuszu. Ta metoda jest idealna dla organizacji, które chcą zapewnić najlepsze środowisko dla użytkowników łączących się z ich buforowanym środowiskiem pulpitu wirtualnego.

Metoda najpierw rozszerzenia najpierw kwerendy hostów sesji, które zezwalają na nowe połączenia. Metoda następnie wybiera hosta sesji z najmniejszą liczbą sesji. Jeśli istnieje remis, metoda wybiera pierwszego hosta sesji w kwerendzie.

## <a name="depth-first-load-balancing-method"></a>Metoda równoważenia obciążenia z pierwszej głębokości

Metoda równoważenia obciążenia najpierw głębokość pozwala nasycić jeden host sesji w czasie, aby zoptymalizować dla tego scenariusza. Ta metoda jest idealna dla organizacji dbających o koszty, które chcą bardziej szczegółową kontrolę nad liczbą maszyn wirtualnych, które zostały przydzielone dla puli hostów.

Metoda najpierw głębokość najpierw kwerendy hostów sesji, które zezwalają na nowe połączenia i nie przekroczyły ich maksymalny limit sesji. Następnie metoda wybiera hosta sesji z największą liczbą sesji. Jeśli istnieje remis, metoda wybiera pierwszego hosta sesji w kwerendzie.
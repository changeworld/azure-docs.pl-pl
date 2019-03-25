---
title: Windows wirtualnego Desktop w wersji zapoznawczej hosta puli metod równoważenia obciążenia — platformy Azure
description: Host puli metod równoważenia obciążenia w środowisku Windows wirtualnego Desktop w wersji zapoznawczej.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 8b18224339654c067d8ab9b543fa49a9c7d55ddd
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400181"
---
# <a name="host-pool-load-balancing-methods"></a>Hostowanie metod równoważenia obciążenia w puli

Windows wirtualnego pulpitu (wersja zapoznawcza) obsługuje dwie metody równoważenia obciążenia. Każda metoda określa, które hostów sesji będzie obsługiwać sesji użytkownika, gdy łączą się z zasobem w puli hosta.

Poniższych metod równoważenia obciążenia są dostępne w Windows pulpitu wirtualnego:

- Równoważenie obciążenia szerokość pierwszej umożliwia równomierne rozkładanie sesje użytkowników na hostach sesji w puli hosta.
- Równoważenie obciążenia pierwszego głębokość umożliwia saturate hosta sesji z sesji użytkownika w puli hosta. Po pierwszej sesji osiągnie swój próg limitu sesji, moduł równoważenia obciążenia kieruje nowych połączeń użytkowników do następnego hosta sesji w puli hosta, dopóki nie zostanie osiągnięty limit i tak dalej.

Każda pula hosta można skonfigurować tylko jeden rodzaj równoważenia obciążenia określonej do niego. Jednak zarówno równoważenia obciążenia metody udostępniania następujące zachowania, niezależnie od tego, którego obsługę puli są:

- Jeśli użytkownik nie ma w puli hosta sesji, a nawiązuje połączenie z tą sesją, moduł równoważenia obciążenia zostaną pomyślnie przekierowywać je do hosta sesji z istniejącą sesją. To zachowanie ma zastosowanie, nawet wtedy, gdy właściwość AllowNewConnections hostujących sesji ma wartość False.
- Jeśli użytkownik nie ma jeszcze sesji w puli hosta, moduł równoważenia obciążenia nie rozważ hostów sesji której właściwość AllowNewConnections jest ustawiany na wartość False podczas równoważenia obciążenia.

## <a name="breadth-first-load-balancing-method"></a>Szerokość pierwszej metody równoważenia obciążenia

Szerokość pierwszej metody równoważenia obciążenia pozwala do dystrybucji połączeń użytkowników, aby zoptymalizować ten scenariusz. Ta metoda jest optymalnym rozwiązaniem dla organizacji, które oferują środowisko o najlepszej dla użytkowników łączących się z ich pulpitów wirtualnych środowiska pulpitu.

Metoda szerokość pierwszej odpytuje najpierw hosty sesji, które zezwala na nowe połączenia. Metoda następnie wybiera hosta sesji o najmniejszej liczby sesji. Jeśli zostanie rozwiązany, metoda wybiera pierwszy hosta sesji w zapytaniu.

## <a name="depth-first-load-balancing-method"></a>Głębokość pierwszej metody równoważenia obciążenia

Metoda równoważenia obciążenia pierwszego głębokość umożliwia saturate jednego hosta sesji w czasie, aby zoptymalizować ten scenariusz. Ta metoda jest optymalnym rozwiązaniem dla organizacji świadome kosztów, które mają bardziej precyzyjną kontrolę nad liczbę maszyn wirtualnych, które zostały przydzielone puli hosta.

Metoda pierwszy głębokość odpytuje najpierw hostów sesji, zezwala na nowe połączenia, które jeszcze nie ma przekroczyli limit maksymalny czas trwania sesji. Metoda następnie wybiera hosta sesji z najwyższą liczbą sesji. Jeśli zostanie rozwiązany, metoda wybiera pierwszy hosta sesji w zapytaniu.
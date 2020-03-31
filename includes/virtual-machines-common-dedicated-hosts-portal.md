---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/10/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 427117fe47294a1db1fa8d3fa1e46ee1efb91b4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128721"
---
## <a name="limitations"></a>Ograniczenia

- Zestawy skalowania maszyny wirtualnej nie są obecnie obsługiwane na dedykowanych hostach.
- Rozmiary i typy sprzętu dostępne dla dedykowanych hostów różnią się w zależności od regionu. Więcej informacji można znaleźć na [stronie z cennikiem hosta.](https://aka.ms/ADHPricing)

## <a name="create-a-host-group"></a>Tworzenie grupy hostów

**Grupa hostów** to nowy zasób reprezentujący kolekcję dedykowanych hostów. Tworzysz grupę hostów w regionie i strefie dostępności i dodajesz do niej hosty. Podczas planowania wysokiej dostępności, istnieją dodatkowe opcje. Z dedykowanymi hostami można użyć jednej lub obu następujących opcji: 
- Obejmuje wiele stref dostępności. W takim przypadku musisz mieć grupę hostów w każdej ze stref, których chcesz użyć.
- Obejmują wiele domen błędów, które są mapowane do stojaków fizycznych. 
 
W obu przypadkach należy podać liczbę domen błędów dla grupy hosta. Jeśli nie chcesz obejmować domen błędów w grupie, użyj liczby domen błędów 1. 

Można również zdecydować się na użycie zarówno stref dostępności, jak i domen błędów. 

W tym przykładzie utworzymy grupę hostów przy użyciu 1 strefy dostępności i 2 domen błędów. 


1. Otwórz [portal](https://portal.azure.com)Azure .
1. Wybierz **pozycję Utwórz zasób** w lewym górnym rogu.
1. Wyszukaj **grupę Host,** a następnie wybierz z wyników pozycję **Grupy hostów.**
1. Na stronie **Grupy hostów** wybierz pozycję **Utwórz**.
1. Wybierz subskrypcję, której chcesz użyć, a następnie wybierz pozycję **Utwórz nowy,** aby utworzyć nową grupę zasobów.
1. Wpisz *myDedicatedHostsRG* jako **nazwę,** a następnie wybierz **przycisk OK**.
1. W przypadku **nazwy grupy hostów**wpisz *myHostGroup*.
1. W obszarze **Lokalizacja**wybierz pozycję **Wschodnie stany USA**.
1. Aby uzyskać **opcję Strefa dostępności,** wybierz **1**.
1. W przypadku **liczby domen błędów**wybierz **2**.
1. Wybierz **pozycję Przejrzyj + utwórz,** a następnie poczekaj na weryfikację.
1. Po wyświetleniu komunikatu **O przekazaniu weryfikacji** wybierz pozycję **Utwórz,** aby utworzyć grupę hostów.

Utworzenie grupy hosta powinno zająć tylko kilka chwil.

## <a name="create-a-dedicated-host"></a>Tworzenie dedykowanego hosta

Teraz utwórz dedykowanego hosta w grupie hosta. Oprócz nazwy hosta, należy podać jednostkę SKU dla hosta. Jednostka SKU hosta przechwytuje obsługiwaną serię maszyn wirtualnych, a także generowanie sprzętu dla dedykowanego hosta.

Aby uzyskać więcej informacji na temat jednostek SKU hosta i cen, zobacz [Cennik hosta dedykowanego platformy Azure](https://aka.ms/ADHPricing).

Jeśli ustawisz liczbę domen błędów dla grupy hostów, zostaniesz poproszony o określenie domeny błędów dla hosta.  

1. Wybierz **pozycję Utwórz zasób** w lewym górnym rogu.
1. Wyszukaj **dedykowanego hosta,** a następnie wybierz **dedykowane hosty** z wyników.
1. Na stronie **Dedykowane hosty** wybierz pozycję **Utwórz**.
1. Wybierz subskrypcję, której chcesz użyć.
1. Wybierz *myDedicatedHostsRG* jako **grupę zasobów**.
1. W **szczegółach instancji**wpisz *myHost* dla **nazwy** i wybierz *wschodnie stany USA* dla lokalizacji.
1. W **obszarze Profil sprzętu**wybierz pozycję *Standardowa rodzina Es3 — typ 1* dla rodziny **Rozmiar**, wybierz *myHostGroup* dla **grupy Host,** a następnie wybierz *1* dla **domeny usterki**. Pozostaw wartości domyślne dla pozostałych pól.
1. Po zakończeniu wybierz **pozycję Przejrzyj + utwórz** i poczekaj na sprawdzenie poprawności.
1. Po wyświetleniu komunikatu **O przekazaniu weryfikacji** wybierz pozycję **Utwórz,** aby utworzyć hosta.



---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/09/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 0e7a814c1607b15e3af0e76a5ae6dfad1594a3b3
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77474228"
---
## <a name="limitations"></a>Ograniczenia

- Zestawy skalowania maszyn wirtualnych nie są obecnie obsługiwane na dedykowanych hostach.
- Początkowa wersja obsługuje następujące serie maszyn wirtualnych: DSv3, ESv3, FSv2, LSv2 i MSv2. 

## <a name="create-a-host-group"></a>Tworzenie grupy hostów

**Grupa hostów** jest nowym zasobem, który reprezentuje kolekcję dedykowanych hostów. Można utworzyć grupę hostów w regionie i strefie dostępności, a następnie dodać do niej hosty. W przypadku planowania wysokiej dostępności dostępne są dodatkowe opcje. W przypadku dedykowanych hostów można użyć jednej lub obu następujących opcji: 
- Obejmuje wiele stref dostępności. W takim przypadku konieczne jest posiadanie grupy hostów w każdej ze stref, które mają być używane.
- Obejmuje wiele domen błędów, które są mapowane na stojaki fizyczne. 
 
W obu przypadkach należy podać liczbę domen błędów dla grupy hostów. Jeśli nie chcesz obejmować domen błędów w grupie, użyj liczby domen błędów wynoszącej 1. 

Możesz również zdecydować się na korzystanie ze stref dostępności i domen błędów. 

W tym przykładzie utworzymy grupę hostów przy użyciu jednej strefy dostępności i dwóch domen błędów. 


1. Otwórz [Portal](https://portal.azure.com)Azure.
1. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu.
1. Wyszukaj **grupę hostów** , a następnie wybierz z wyników pozycję **grupy hostów** .

    ![Wyniki wyszukiwania grup hostów.](./media/virtual-machines-common-dedicated-hosts-portal/host-group.png)
1. Na stronie **grupy hostów** wybierz pozycję **Utwórz**.
1. Wybierz subskrypcję, której chcesz użyć, a następnie wybierz pozycję **Utwórz nową** , aby utworzyć nową grupę zasobów.
1. Wpisz *myDedicatedHostsRG* jako **nazwę** , a następnie wybierz przycisk **OK**.
1. W obszarze **Nazwa grupy hostów**wpisz element *webhost*.
1. W obszarze **Lokalizacja**wybierz pozycję **Wschodnie stany USA**.
1. W **obszarze strefa dostępności**wybierz pozycję **1**.
1. W obszarze **liczba domen błędów**wybierz pozycję **2**.
1. Wybierz pozycję **Przegląd + Utwórz** , a następnie zaczekaj na weryfikację.

    ![Ustawienia grupy hostów](./media/virtual-machines-common-dedicated-hosts-portal/host-group-settings.png)
1. Po wyświetleniu komunikatu **zweryfikowana weryfikacja** wybierz pozycję **Utwórz** , aby utworzyć grupę hostów.

Utworzenie grupy hostów może trwać zaledwie kilka minut.

## <a name="create-a-dedicated-host"></a>Tworzenie dedykowanego hosta

Teraz Utwórz dedykowanego hosta w grupie hostów. Oprócz nazwy hosta, należy podać jednostkę SKU dla hosta. Jednostka SKU hosta przechwytuje obsługiwaną serię maszyn wirtualnych oraz generowanie sprzętu dla dedykowanego hosta.

Aby uzyskać więcej informacji o jednostkach SKU i cenach hosta, zobacz [Cennik dedykowanego hosta platformy Azure](https://aka.ms/ADHPricing).

Jeśli ustawisz liczbę domen błędów dla grupy hostów, zostanie wyświetlony monit o określenie domeny błędów dla hosta.  

1. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu.
1. Wyszukaj **dedykowanego hosta** , a następnie wybierz pozycję **dedykowane hosty** na podstawie wyników.

    ![Wyniki wyszukiwania grup hostów.](./media/virtual-machines-common-dedicated-hosts-portal/host.png)
1. Na stronie **hosty dedykowane** wybierz pozycję **Utwórz**.
1. Wybierz subskrypcję, której chcesz użyć.
1. Wybierz pozycję *myDedicatedHostsRG* jako **grupę zasobów**.
1. W obszarze **szczegóły wystąpienia**wpisz wartość *webhost* dla **nazwy** i wybierz pozycję *Wschodnie stany USA* dla lokalizacji.
1. W obszarze **profil sprzętu**wybierz pozycję *standardowa rodzina Es3 — typ 1* dla **rodziny rozmiarów**, wybierz pozycję *myHostGrup* dla **grupy hostów** , a następnie wybierz pozycję *1* dla **domeny błędów**. Pozostaw wartości domyślne pozostałych pól.
1. Gdy wszystko będzie gotowe, wybierz pozycję **Przegląd + Utwórz** i poczekaj na weryfikację.

    ![Ustawienia hosta](./media/virtual-machines-common-dedicated-hosts-portal/host-settings.png)
1. Po wyświetleniu komunikatu **zweryfikowana weryfikacja** wybierz pozycję **Utwórz** , aby utworzyć hosta.



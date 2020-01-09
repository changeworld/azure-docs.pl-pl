---
title: Tworzenie pamięci podręcznej platformy Azure HPC
description: Jak utworzyć wystąpienie pamięci podręcznej platformy Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: rohogue
ms.openlocfilehash: 793a80e7019e72c1cb3087da02d5642639cb8d5e
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647160"
---
# <a name="create-an-azure-hpc-cache"></a>Tworzenie pamięci podręcznej platformy Azure HPC

Użyj Azure Portal, aby utworzyć pamięć podręczną.

![zrzut ekranu przedstawiający przegląd pamięci podręcznej w Azure Portal z przyciskiem Utwórz u dołu](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>Definiuj podstawowe szczegóły

![zrzut ekranu strony szczegółów projektu w Azure Portal](media/hpc-cache-create-basics.png)

W obszarze **szczegóły projektu**wybierz subskrypcję i grupę zasobów, w której będzie hostowana pamięć podręczna. Upewnij się, że subskrypcja znajduje się na liście [dostęp](hpc-cache-prereqs.md#azure-subscription) .

W obszarze **Szczegóły usługi**Ustaw nazwę pamięci podręcznej i inne atrybuty:

* Lokalizacja — wybierz jeden z [obsługiwanych regionów](hpc-cache-overview.md#region-availability).
* Sieć wirtualna — możesz wybrać istniejącą lub utworzyć nową sieć wirtualną.
* Podsieć — wybierz lub Utwórz podsieć z co najmniej 64 adresami IP (/24), które będą używane tylko dla tego wystąpienia pamięci podręcznej platformy Azure HPC.

## <a name="set-cache-capacity"></a>Ustawianie pojemności pamięci podręcznej
<!-- referenced from GUI - update aka.ms link if you change this header text -->

Na stronie **pamięć podręczna** należy ustawić pojemność pamięci podręcznej. Wartości ustawione w tym miejscu określają ilość danych, które mogą być przechowywane w pamięci podręcznej i jak szybko mogą obsłużyć żądania klientów.

Pojemność ma także wpływ na koszt pamięci podręcznej.

Wybierz pojemność, ustawiając te dwie wartości:

* Maksymalna szybkość transferu danych w pamięci podręcznej (przepływność), w GB/s
* Ilość miejsca do magazynowania przydzieloną dla danych w pamięci podręcznej w TB

Wybierz jedną z dostępnych wartości przepływności i rozmiar pamięci podręcznej.

Należy pamiętać, że Rzeczywista szybkość transferu danych zależy od obciążenia, szybkości sieci i typu miejsca docelowego magazynu. Wybrane wartości ustawiają maksymalną przepływność dla całego systemu pamięci podręcznej, ale niektóre z nich są używane do zadań dodatkowych. Na przykład jeśli klient żąda pliku, który nie jest już przechowywany w pamięci podręcznej, lub jeśli plik jest oznaczony jako przestarzały, pamięć podręczna będzie używać niektórych przepływności do pobrania z magazynu zaplecza.

Pamięć podręczna platformy Azure HPC zarządza, które pliki są buforowane i wstępnie załadowane, aby zmaksymalizować szybkość trafień pamięci podręcznej. Zawartość pamięci podręcznej jest stale oceniana i pliki są przenoszone do magazynu długoterminowego, gdy są one rzadziej używane. Wybierz rozmiar pamięci podręcznej, który może wygodnie przechowywać aktywny zestaw plików roboczych z dodatkowym miejscem dla metadanych i innych obciążeń.

![zrzut ekranu strony zmiany wielkości pamięci podręcznej](media/hpc-cache-create-capacity.png)

## <a name="add-resource-tags-optional"></a>Dodaj Tagi zasobów (opcjonalnie)

Na stronie **Tagi** można dodać [Tagi zasobów](https://go.microsoft.com/fwlink/?linkid=873112) do wystąpienia pamięci podręcznej platformy Azure HPC.

## <a name="finish-creating-the-cache"></a>Zakończ tworzenie pamięci podręcznej

Po skonfigurowaniu nowej pamięci podręcznej kliknij kartę **Recenzja + tworzenie** . Portal sprawdza poprawność wybranych opcji i pozwala przejrzeć wybrane opcje. Jeśli wszystko jest poprawne, kliknij przycisk **Utwórz**.

Tworzenie pamięci podręcznej zajmie około 10 minut. Postęp można śledzić w panelu powiadomienia Azure Portal.

![zrzut ekranu przedstawiający tworzenie stron "wdrażanie" i "powiadomienia" w portalu](media/hpc-cache-deploy-status.png)

Po zakończeniu tworzenia zostanie wyświetlone powiadomienie z linkiem do nowego wystąpienia usługi Azure HPC cache, a pamięć podręczna zostanie wyświetlona na liście **zasobów** subskrypcji.
<!-- double check on notification -->

![zrzut ekranu wystąpienia pamięci podręcznej platformy Azure HPC w Azure Portal](media/hpc-cache-new-overview.png)

## <a name="next-steps"></a>Następne kroki

Po wyświetleniu pamięci podręcznej na liście **zasobów** Zdefiniuj cele magazynu, aby zapewnić dostęp do pamięci podręcznej do źródeł danych.

* [Dodaj cele magazynu](hpc-cache-add-storage.md)

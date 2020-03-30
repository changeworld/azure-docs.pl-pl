---
title: Tworzenie pamięci podręcznej HPC platformy Azure
description: Jak utworzyć wystąpienie pamięci podręcznej HPC platformy Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 11/11/2019
ms.author: rohogue
ms.openlocfilehash: c6090d19ce530829b79dca69636c2123e2519961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129568"
---
# <a name="create-an-azure-hpc-cache"></a>Tworzenie pamięci podręcznej HPC platformy Azure

Użyj witryny Azure portal, aby utworzyć pamięć podręczną.

![zrzut ekranu przedstawiający omówienie pamięci podręcznej w witrynie Azure portal z przyciskiem Utwórz u dołu](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>Definiowanie podstawowych szczegółów

![zrzut ekranu przedstawiający stronę szczegółów projektu w witrynie Azure portal](media/hpc-cache-create-basics.png)

W **sekcji Szczegóły projektu**wybierz grupę subskrypcji i zasobów, która będzie obsługiwać pamięć podręczną. Upewnij się, że subskrypcja znajduje się na liście [dostępu.](hpc-cache-prereqs.md#azure-subscription)

W **obszarze Szczegóły usługi**ustaw nazwę pamięci podręcznej i te inne atrybuty:

* Lokalizacja — wybierz jeden z [obsługiwanych regionów](hpc-cache-overview.md#region-availability).
* Sieć wirtualna — można wybrać istniejącą lub utworzyć nową sieć wirtualną.
* Podsieć — wybierz lub utwórz podsieć z co najmniej 64 adresami IP (/24), które będą używane tylko dla tego wystąpienia pamięci podręcznej HPC platformy Azure.

## <a name="set-cache-capacity"></a>Ustawianie pojemności pamięci podręcznej
<!-- referenced from GUI - update aka.ms link if you change this header text -->

Na stronie **Pamięć podręczna** należy ustawić pojemność pamięci podręcznej. Wartości ustawione w tym miejscu określają, ile danych może przechowywać pamięć podręczna i jak szybko może obsługiwać żądania klientów.

Pojemność wpływa również na koszt pamięci podręcznej.

Wybierz pojemność, ustawiając te dwie wartości:

* Maksymalna szybkość transferu danych dla pamięci podręcznej (przepływność), w GB/sekundę
* Ilość miejsca przeznaczonego na dane w pamięci podręcznej w tb

Wybierz jedną z dostępnych wartości przepływności i rozmiary magazynu pamięci podręcznej.

Należy pamiętać, że rzeczywista szybkość transferu danych zależy od obciążenia, szybkości sieci i typu docelowych magazynów. Wartości, które wybierzesz ustawić maksymalną przepływność dla całego systemu pamięci podręcznej, ale niektóre z nich jest używany do zadań napowietrznych. Na przykład jeśli klient żąda pliku, który nie jest jeszcze przechowywany w pamięci podręcznej lub jeśli plik jest oznaczony jako przestarzały, pamięć podręczna używa niektórych jego przepływności, aby pobrać go z magazynu zaplecza.

Usługa Azure HPC Cache zarządza plikami, które są buforowane i wstępnie załadowane, aby zmaksymalizować współczynnik trafień w pamięci podręcznej. Zawartość pamięci podręcznej jest stale oceniana, a pliki są przenoszone do magazynu długoterminowego, gdy są rzadziej dostępne. Wybierz rozmiar magazynu pamięci podręcznej, który może wygodnie pomieścić aktywny zestaw plików roboczych z dodatkowym miejscem na metadane i inne obciążenie.

![zrzut ekranu przedstawiający stronę do zmiany rozmiaru pamięci podręcznej](media/hpc-cache-create-capacity.png)

## <a name="add-resource-tags-optional"></a>Dodawanie znaczników zasobów (opcjonalnie)

Strona **Tagi** umożliwia dodawanie [tagów zasobów](https://go.microsoft.com/fwlink/?linkid=873112) do wystąpienia pamięci podręcznej HPC platformy Azure.

## <a name="finish-creating-the-cache"></a>Zakończenie tworzenia pamięci podręcznej

Po skonfigurowaniu nowej pamięci podręcznej kliknij kartę **Recenzja + utwórz.** Portal sprawdza twoje wybory i umożliwia przeglądanie wyborów. Jeśli wszystko jest poprawne, kliknij przycisk **Utwórz**.

Tworzenie pamięci podręcznej trwa około 10 minut. Postęp można śledzić w panelu powiadomień witryny Azure portal.

![zrzut ekranu przedstawiający tworzenie pamięci podręcznej "wdrażanie w toku" i "powiadomienia" stron w portalu](media/hpc-cache-deploy-status.png)

Po zakończeniu tworzenia pojawi się powiadomienie z łączem do nowego wystąpienia pamięci podręcznej HPC platformy Azure, a pamięć podręczna pojawi się na liście **Zasoby** subskrypcji.
<!-- double check on notification -->

![zrzut ekranu przedstawiający wystąpienie usługi Azure HPC Cache w witrynie Azure portal](media/hpc-cache-new-overview.png)

## <a name="next-steps"></a>Następne kroki

Po wyświetlenie pamięci podręcznej na liście **Zasoby** zdefiniuj obiekty docelowe magazynu, aby zapewnić dostęp do pamięci podręcznej do źródeł danych.

* [Dodawanie lokalizacji docelowych magazynu](hpc-cache-add-storage.md)

---
title: Ponowne przekładzanie maszyn wirtualnych platformy Azure do regionu podstawowego za pomocą usługi Azure Site Recovery | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób ponownego ceł maszyn wirtualnych platformy Azure po przekroczeniu trybu failover, pomocniczego do regionu podstawowego, przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 73747b8331054cdc3bfa1f4073ccf2cdb62ab326
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283246"
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>Ponowne nadanie ochrony przed programem Azure w celu przekroczenia błędu nie powiodło się w regionie podstawowym

Po [przełączeniu maszyn](site-recovery-failover.md) wirtualnych platformy Azure w stanach **niechronionych** w przypadku korzystania z usługi [Azure Site Recovery](site-recovery-overview.md)maszyny wirtualne są uruchamiane w regionie pomocniczym. Jeśli chcesz przywrócić maszyny wirtualne po awarii do regionu podstawowego, wykonaj następujące zadania:

1. Ponowne przetwarzanie maszyn wirtualnych w regionie pomocniczym, tak aby zaczęły się replikować do regionu podstawowego.
1. Po zakończeniu ponownego podstawowego ekwiwalek i maszyny wirtualne są replikowane, można przejść awaryjnie z pomocniczego do regionu podstawowego.

## <a name="prerequisites"></a>Wymagania wstępne

- Maszyna wirtualna pracy awaryjnej z regionu pierwotnego do pomocniczego musi być zatwierdzona.
- Lokacja docelowa powinna być dostępna i powinna istnieć możliwość uzyskiwania dostępu do zasobów w tym regionie lub tworzenia ich.

## <a name="reprotect-a-vm"></a>Ponowne nadanie ceł maszynom wirtualnym

1. W obszarze**Replikowane elementy** **programu Vault** > kliknij prawym przyciskiem myszy maszynę wirtualną w trybie failed i wybierz polecenie **Re-Protect**. Kierunek ponownego odrzucenia powinien być pokazywalny od pomocniczego do pierwotnego.

   ![Ponowne przekładanie](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

1. Przejrzyj zestawy grupy zasobów, sieci, magazynu i dostępności. Następnie kliknij przycisk **OK**. Jeśli istnieją zasoby oznaczone jako nowe, są one tworzone jako część procesu ponownego przetwarzania.
1. Zadanie ponownego odrzucenia nasiona witryny docelowej z najnowszych danych. Po zakończeniu zadania replikacja różnicowa ma miejsce. Następnie można przejść awaryjnie z powrotem do lokacji głównej. Możesz wybrać konto magazynu lub sieć, której chcesz użyć podczas ponownego wykorzystania, korzystając z opcji dostosuj.

   ![Dostosuj opcję](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>Dostosowywanie ustawień ponownego ceł

Można dostosować następujące właściwości docelowej maszyny Wirtualnej podczas reprotection.

![Dostosowywanie](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Właściwość |Uwagi  |
|---------|---------|
|Docelowa grupa zasobów | Zmodyfikuj docelową grupę zasobów, w której tworzona jest maszyna wirtualna. Jako część reprotection docelowej maszyny Wirtualnej jest usuwany. Można wybrać nową grupę zasobów, w ramach której można utworzyć maszynę wirtualną po przemienniu w stan failover. |
|Docelowa sieć wirtualna | Nie można zmienić sieci docelowej podczas zadania ponownego ceł. Aby zmienić sieć, wykonaj ponownie mapowanie sieci. |
|Magazyn docelowy (dodatkowa maszyna wirtualna nie używa dysków zarządzanych) | Można zmienić konto magazynu używane przez maszynę wirtualną po przemijaniu w stan failover. |
|Replika dysków zarządzanych (pomocnicza maszyna wirtualna używa dysków zarządzanych) | Usługa Site Recovery tworzy dyski zarządzane repliki w regionie podstawowym w celu odzwierciedlenia dysków zarządzanych pomocniczej maszyny Wirtualnej. |
|Magazyn pamięci podręcznej | Można określić konto magazynu pamięci podręcznej, które ma być używane podczas replikacji. Domyślnie tworzone jest nowe konto magazynu pamięci podręcznej, jeśli nie istnieje. |
|Zestaw dostępności | Jeśli maszyna wirtualna w regionie pomocniczym jest częścią zestawu dostępności, można wybrać dostępność ustawioną dla docelowej maszyny Wirtualnej w regionie podstawowym. Domyślnie usługa Site Recovery próbuje znaleźć istniejący zestaw dostępności w regionie podstawowym i używać go. Podczas dostosowywania można określić nowy zestaw dostępności. |

### <a name="what-happens-during-reprotection"></a>Co dzieje się podczas reprotection?

Domyślnie występuje następujące zdarzenia:

1. Konto magazynu pamięci podręcznej jest tworzony w regionie, w którym jest uruchomiona maszyna wirtualna w trybie failed.
1. Jeśli docelowe konto magazynu (oryginalne konto magazynu w regionie podstawowym) nie istnieje, zostanie utworzone nowe. Przypisana nazwa konta magazynu to nazwa konta magazynu używanego przez pomocniczą `asr`maszynę wirtualną, z przyrostkiem .
1. Jeśli maszyna wirtualna używa dysków zarządzanych, dyski zarządzane repliki są tworzone w regionie podstawowym w celu przechowywania danych replikowanych z dysków pomocniczej maszyny Wirtualnej.
1. Jeśli docelowy zestaw dostępności nie istnieje, w razie potrzeby zostanie utworzony nowy jako część zadania ponownego ustalania. Jeśli ustawienia ponownego odwrócia zostały dostosowane, zostanie użyty wybrany zestaw.

Po wyzwoleniu zadania ponownego ceł i istnieje docelowa maszyna wirtualna, wystąpią następujące czynności:

1. Maszyna wirtualna po stronie docelowej jest wyłączona, jeśli jest uruchomiona.
1. Jeśli maszyna wirtualna używa dysków zarządzanych, kopia oryginalnego `-ASRReplica` dysku jest tworzona z sufiksem. Oryginalne dyski zostaną usunięte. Kopie `-ASRReplica` są używane do replikacji.
1. Jeśli maszyna wirtualna używa dysków niezarządzanych, dyski danych docelowej maszyny Wirtualnej są odłączane i używane do replikacji. Kopia dysku systemu operacyjnego jest tworzona i dołączana na maszynie wirtualnej. Oryginalny dysk systemu operacyjnego jest odłączony i używany do replikacji.
1. Synchronizowane są tylko zmiany między dyskiem źródłowym a dyskiem docelowym. Różnice są obliczane przez porównanie obu dysków, a następnie przeniesione. Sprawdź poniżej, aby znaleźć szacowany czas na zakończenie ponownego zakończenia.
1. Po zakończeniu synchronizacji rozpoczyna się replikacja różnicowa i punkt odzyskiwania jest tworzony zgodnie z zasadami replikacji.

Po wyzwoleniu zadania ponownego ceł, a docelowa maszyna wirtualna i dyski nie istnieją, występuje następujące zdarzenia:

1. Jeśli maszyna wirtualna korzysta z dysków zarządzanych, dyski repliki są tworzone z `-ASRReplica` sufiksem. Kopie `-ASRReplica` są używane do replikacji.
1. Jeśli maszyna wirtualna używa dysków niezarządzanych, dyski repliki są tworzone na koncie magazynu docelowego.
1. Całe dyski są kopiowane z regionu trybu failed do nowego regionu docelowego.
1. Po zakończeniu synchronizacji rozpoczyna się replikacja różnicowa i punkt odzyskiwania jest tworzony zgodnie z zasadami replikacji.

#### <a name="estimated-time-to-do-the-reprotection"></a>Szacowany czas na ponowne odwrót

W większości przypadków usługa Azure Site Recovery nie replikuje pełnych danych do regionu źródłowego.
Następujące warunki określają ilość danych replikowanych:

1. Jeśli źródłowe dane maszyny Wirtualnej zostaną usunięte, uszkodzone lub niedostępne z jakiegoś powodu, na przykład zmiana/usunięcie grupy zasobów, podczas ponownego przekazywania nastąpi pełna replikacja początkowa, ponieważ nie ma dostępnych danych w regionie źródłowym.
1. Jeśli źródłowe dane maszyny Wirtualnej są dostępne, wówczas tylko różnice są obliczane przez porównanie zarówno dysków, jak i następnie przeniesione. Sprawdź poniższą tabelę, aby uzyskać szacowany czas.

|Przykładowa sytuacja | Czas spędzony na ponowne przerókcie |
|---|---|
|Region źródłowy ma 1 maszynę wirtualną z dyskiem standardowym o pojemności 1 TB.<br/>Używane jest tylko 127 GB danych, a pozostała część dysku jest pusta.<br/>Typ dysku jest standardowo z przepustowością 60 MiB/S.<br/>Brak zmiany danych po przewijaniu awaryjnym.| Przybliżony czas: 45 minut – 1,5 godziny.<br/>Podczas reprotection, Site Recovery wypełni sumę kontrolną wszystkich danych, które zajmie 127 GB / 45 MB, około 45 minut.<br/>Aby funkcja Site Recovery automatycznie skalować, jest wymagany czas nałdowiowy, czyli około 20–30 minut.<br/>Brak opłat za wyjście. |
|Region źródłowy ma 1 maszynę wirtualną z dyskiem standardowym o pojemności 1 TB.<br/>Używane jest tylko 127 GB danych, a reszta dysku jest pusta.<br/>Typ dysku jest standardowo z przepustowością 60 MiB/S.<br/>45 GB danych zmienia się po przemijawień awaryjnej.| Przybliżony czas: 1 godzina – 2 godziny.<br/>Podczas reprotection, Site Recovery wypełni sumę kontrolną wszystkich danych, które zajmie 127 GB / 45 MB, około 45 minut.<br/>Prześlij czas, aby zastosować zmiany 45 GB, czyli 45 GB / 45 MB/s, około 17 minut.<br/>Opłaty za ruch wychodzący będą dla 45 GB zmian danych, a nie dla sumy kontrolnej. |

Gdy maszyna wirtualna jest ponownie chroniona po niepowodzeniu powrotu do regionu podstawowego (tj. jeśli maszyna wirtualna jest ponownie chroniona z regionu podstawowego do regionu odzyskiwania po awarii), docelowa maszyna wirtualna i skojarzone karty sieciowe są usuwane.

Gdy maszyna wirtualna jest ponownie chroniona z regionu odzyskiwania po awarii do regionu podstawowego, nie usuwamy dawnych podstawowych maszyn wirtualnych i skojarzonych kart sieciowych.

## <a name="next-steps"></a>Następne kroki

Po chronionych maszyn wirtualnych, można zainicjować pracy awaryjnej. Przewijania awaryjnego zamyka maszynę wirtualną w regionie pomocniczym i tworzy i uruchamia maszynę wirtualną w regionie podstawowym, z krótkim przestojem podczas tego procesu. Zaleca się wybranie odpowiedniego czasu dla tego procesu i uruchomienie testu pracy awaryjnej przed rozpoczęciem pełnego trybu failover do lokacji głównej. [Dowiedz się więcej](site-recovery-failover.md) o pracy awaryjnej usługi Azure Site Recovery.

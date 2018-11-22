---
author: rayne-wiselman
ms.service: backup
ms.topic: include
ms.date: 11/09/2018
ms.author: raynew
ms.openlocfilehash: e62771096bc59bc05879ce7b7e2da19f050b27b0
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279823"
---
## <a name="defining-a-backup-policy"></a>Określanie zasad tworzenia kopii zapasowych
Zasady tworzenia kopii zapasowych określają macierz z informacjami, kiedy są pobierane migawki danych i jak długo są przechowywane. W przypadku określania zasad tworzenia kopii zapasowych maszyny wirtualnej można wyzwolić zadanie tworzenia kopii zapasowej *raz dziennie*. W przypadku tworzenia nowych zasad zostają one zastosowane do magazynu. Interfejs zasad tworzenia kopii zapasowych wygląda następująco:

![Zasady tworzenia kopii zapasowych](./media/backup-create-policy-for-vms/backup-policy.png)

Aby utworzyć zasady:

1. Wprowadź nazwę w polu **Nazwa zasad**.
2. Migawki danych mogą być pobierane raz dziennie lub co tydzień. Użyj menu rozwijanego **Częstotliwość tworzenia kopii zapasowych** i wybierz, czy migawki mają być pobierane raz dziennie czy co tydzień.
   
   * Jeśli wybierzesz pobieranie raz dziennie, użyj wyróżnionej kontrolki, aby wybrać godzinę pobierania migawki. Aby zmienić godzinę, usuń jej zaznaczenie, a następnie wybierz nową.
     
     ![Zasady tworzenia kopii zapasowych raz dziennie](./media/backup-create-policy-for-vms/backup-policy-daily.png) <br/>
   * Jeśli wybierzesz pobieranie co tydzień, użyj wyróżnionych kontrolek, aby wybrać dzień lub dni tygodnia oraz godzinę pobierania migawki. W menu dzień wybierz jeden lub kilka dni. W menu godzina wybierz jedną godzinę. Aby zmienić godzinę, usuń jej zaznaczenie, a następnie wybierz nową.
     
     ![Zasady tworzenia kopii zapasowych co tydzień](./media/backup-create-policy-for-vms/backup-policy-weekly.png)
3. Domyślnie wszystkie opcje **Zakres przechowywania** są zaznaczone. Usuń zaznaczenia pola wyboru limitu zakresu przechowywania, których nie chcesz używać. Następnie określ odstępy czasu.
   
    Miesięczne i roczne zakresy przechowywania umożliwiają określenie migawek na podstawie przyrostu tygodniowego lub dziennego.
   
   > [!NOTE]
   > W przypadku ochrony maszyn wirtualnych zadanie tworzenia kopii zapasowej jest uruchamiane raz dziennie. Godzina uruchamiania tworzenia kopii zapasowej jest taka sama dla każdego zakresu przechowywania.
   > 
   > 
4. Po ustawieniu wszystkich opcji zasad kliknij opcję **Zapisz** w górnej części bloku.
   
    Nowe zasady zostaną natychmiast zastosowane do magazynu.


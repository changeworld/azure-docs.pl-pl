---
title: Przywracanie danych w systemie Azure na komputerze z systemem Windows server lub Windows
description: Dowiedz się, jak przywrócić dane przechowywane na platformie Azure na komputerze z systemem Windows server lub Windows.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 9/7/2018
ms.author: saurse
ms.openlocfilehash: 9c2f8ce0dfb866826de549946bc3ee1acc2b677f
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67460262"
---
# <a name="restore-files-to-windows-by-using-the-azure-resource-manager-deployment-model"></a>Przywracanie plików do Windows za pomocą modelu wdrażania usługi Azure Resource Manager

W tym artykule wyjaśniono, jak przywrócić dane z magazynu kopii zapasowych. Aby przywrócić dane, należy użyć Kreatora odzyskiwania danych agenta usługi Microsoft Azure Recovery Services (MARS). Możesz:

* Przywracanie danych na tym samym komputerze, z których wykonano kopie zapasowe.
* Przywracanie danych do alternatywnej maszyny.

Za pomocą funkcji Przywracanie błyskawiczne Zainstaluj migawki punktu odzyskiwania zapisywalny, jako woluminu odzyskiwania. Następnie można eksplorować odzyskiwania woluminu i kopiować pliki na komputerze lokalnym, a tym samym selektywne Przywracanie plików.

> [!NOTE]
> [Stycznia 2017 aktualizacji usługi Azure Backup](https://support.microsoft.com/en-us/help/3216528?preview) jest wymagany, jeśli chcesz użyć błyskawiczne przywracania do przywracania danych. Ponadto dane kopii zapasowych muszą być chronione w magazynach w ustawieniach regionalnych wymienionych w artykule pomocy technicznej. Zapoznaj się z [stycznia 2017 aktualizacji usługi Azure Backup](https://support.microsoft.com/en-us/help/3216528?preview) najbardziej aktualną listę ustawień regionalnych, które obsługują, natychmiastowe przywracanie.
>

Za pomocą natychmiastowe Przywracanie magazynów usługi Recovery Services w witrynie Azure portal. Jeśli dane są przechowywane w magazynach kopii zapasowej, ma został przekonwertowany do magazynów usługi Recovery Services. Jeśli chcesz użyć, natychmiastowe przywracanie, Pobierz aktualizację MARS, a następnie postępuj zgodnie z procedurami, zawierających, natychmiastowe przywracanie.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Użyj błyskawiczne polecenia Restore, aby odzyskać dane na tym samym komputerze

Jeśli przypadkowo usunięty plik i chcesz je przywrócić na tym samym komputerze (w którym jest wykonywana kopia zapasowa), poniższe kroki pomoże Ci odzyskać dane.

1. Otwórz przystawkę **Microsoft Azure Backup**. Jeśli nie wiesz, gdzie zainstalowano przystawkę, wyszukaj komputerze lub serwerze **kopia zapasowa Microsoft Azure**.

    Aplikacja klasyczna powinna zostać wyświetlona w wynikach wyszukiwania.

2. Wybierz **Odzyskaj dane** Aby uruchomić kreatora.

    ![Zrzut ekranu z usługi Azure Backup, za pomocą odzyskać dane, które są wyróżnione](./media/backup-azure-restore-windows-server/recover.png)

3. Na **wprowadzenie** wybierz stronę, aby przywrócić dane w tym samym serwerze lub komputerze, **ten serwer (`<server name>`)**  > **dalej**.

    ![Zrzut ekranu z odzyskać dane kreatora Getting Started page](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. Na **wybierz tryb odzyskiwania** wybierz **pojedyncze pliki i foldery** > **dalej**.

    ![Zrzut ekranu z odzyskać dane kreatora wybierz tryb odzyskiwania strony](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)
   > [!IMPORTANT]
   > Opcję, aby przywrócić pojedyncze pliki i foldery wymaga programu .NET Framework 4.5.2 lub nowszej. Jeśli nie widzisz **pojedyncze pliki i foldery** opcji, należy uaktualnić .NET Framework w wersji 4.5.2 lub nowszej i spróbuj ponownie.

   > [!TIP]
   > **Pojedyncze pliki i foldery** opcja umożliwia szybkie uzyskiwanie dostępu do danych punktu odzyskiwania. Nadaje się do odzyskiwania pojedynczych plików o rozmiarze, co daje łącznie nie więcej niż 80 GB i oferuje transferu lub kopiowania przyspiesza maksymalnie 6 MB/s, podczas odzyskiwania. **Woluminu** opcji odzyskuje wszystkie kopie zapasowe danych w określonym woluminie. Ta opcja zapewnia szybkość transferu szybkości (maksymalnie 60 MB/s), co jest idealnym rozwiązaniem dla odzyskiwania danych i dużej wielkości lub całe woluminy.

5. Na **Wybierz wolumin i datę** Wybierz wolumin, który zawiera pliki i foldery, które mają zostać przywrócone.

    W kalendarzu wybierz punkt odzyskiwania. Daty **bold** wskazującą dostępność co najmniej jeden punkt odzyskiwania. Jeśli wiele punktów odzyskiwania jest dostępnych w danym dniu, wybrać określony punkt odzyskiwania z **czasu** menu rozwijanego.

    ![Zrzut ekranu z odzyskać dane kreatora wybierz wolumin i datę strony](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Po wybraniu punktu odzyskiwania można przywrócić, wybierz **instalacji**.

    Usługa Azure Backup instaluje punkt odzyskiwania lokalnych i wykorzystuje je jako woluminu odzyskiwania.

7. Na **przeglądanie i odzyskiwanie plików** wybierz opcję **Przeglądaj** Otwórz Eksploratora Windows i znaleźć pliki i foldery, które mają.

    ![Zrzut ekranu z odzyskać dane kreatora przeglądanie i odzyskiwanie plików strony](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)


8. W Eksploratorze Windows skopiuj pliki i foldery, które chcesz przywrócić i wklej je do dowolnej lokalizacji lokalnej na serwerze lub komputerze. Można otworzyć lub przesyłanie strumieniowe plików bezpośrednio z poziomu woluminu odzyskiwania i sprawdź, czy są odzyskiwane poprawne wersje.

    ![Zrzut ekranu programu Windows Explorer, kopią wyróżniony](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)


9. Po zakończeniu, na **przeglądanie i odzyskiwanie plików** wybierz opcję **Odinstaluj**. Następnie wybierz pozycję **tak** aby upewnić się, że chcesz odinstalować wolumin.

    ![Zrzut ekranu z odzyskać dane kreatora przeglądanie i odzyskiwanie plików strony](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Jeśli nie zaznaczysz **Odinstaluj**, woluminu odzyskiwania pozostaną zainstalowane przez 6 godzin od momentu, gdy został zainstalowany. Jednak podczas instalacji jest rozszerzany maksymalnie 24 godziny, w przypadku ciągłego kopiowania plików. Żadne operacje tworzenia kopii zapasowej zostanie uruchomiony, gdy wolumin jest zainstalowany. Żadnych operacji tworzenia kopii zapasowej zaplanowane do uruchomienia w czasie, gdy wolumin jest zainstalowany, zostanie uruchomiony po odinstalowane woluminu odzyskiwania.
    >


## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Umożliwia natychmiastowe przywracanie przywrócenie danych do alternatywnej maszynie
W przypadku utraty całego serwera można jednak odzyskać dane z usługi Azure Backup na innym komputerze. Poniższe kroki ilustrują przepływu pracy.


Kroki te obejmują następującą terminologią:

* *Maszyna źródłowa* — oryginalną maszynę, z której wykonano kopię zapasową i który jest obecnie niedostępny.
* *Maszyna docelowa* — machine, do którego dane są odzyskiwane.
* *Vault przykładowe* — magazyn usługi Recovery Services, w którym zarejestrowano maszyny źródłowej i docelowej maszyny. <br/>

> [!NOTE]
> Nie można przywrócić kopii zapasowych na komputerze docelowym z systemem wcześniejszej wersji systemu operacyjnego. Na przykład można przywrócić kopii zapasowej z komputera, Windows 7 na komputerze Windows 7 (lub nowszy). Nie można przywrócić kopii zapasowej z komputera z systemem Windows 8 na komputerze Windows 7.
>
>

1. Otwórz **kopia zapasowa Microsoft Azure** przystawki na komputerze docelowym.

2. Upewnij się, że maszyna źródłowa i docelowa są zarejestrowane w tym samym magazynie usługi Recovery Services.

3. Wybierz **Odzyskaj dane** otworzyć **Kreatorze odzyskiwania danych**.

    ![Zrzut ekranu z usługi Azure Backup, za pomocą odzyskać dane, które są wyróżnione](./media/backup-azure-restore-windows-server/recover.png)

4. Na **wprowadzenie** wybierz opcję **inny serwer**.

    ![Zrzut ekranu z odzyskać dane kreatora Getting Started page](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Podaj plik poświadczeń magazynu, umożliwiająca magazynu próbki, a następnie wybierz **dalej**.

    Jeśli plik poświadczeń magazynu jest nieprawidłowy (lub wygasłą), Pobierz nowy plik poświadczeń magazynu z magazynu próbki w witrynie Azure portal. Po podaniu poświadczeń prawidłowym magazynie pojawia się nazwę odpowiedniego magazynu kopii zapasowych.


6. Na **wybierz pozycję Utwórz kopię zapasową serwera** stronie, wybierz na maszynie źródłowej z listy wyświetlanych maszyn i podaj hasło. Następnie wybierz przycisk **Dalej**.

    ![Zrzut ekranu z odzyskać dane kreatora wybierz kopię zapasową strony serwera](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. Na **wybierz tryb odzyskiwania** wybierz **pojedyncze pliki i foldery** > **dalej**.

    ![Zrzut ekranu z odzyskać dane kreatora wybierz tryb odzyskiwania strony](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. Na **Wybierz wolumin i datę** Wybierz wolumin, który zawiera pliki i foldery, które mają zostać przywrócone.

    W kalendarzu wybierz punkt odzyskiwania. Daty **bold** wskazującą dostępność co najmniej jeden punkt odzyskiwania. Jeśli wiele punktów odzyskiwania jest dostępnych w danym dniu, wybrać określony punkt odzyskiwania z **czasu** menu rozwijanego.

    ![Zrzut ekranu z odzyskać dane kreatora wybierz wolumin i datę strony](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Wybierz **instalacji** należy lokalnie zainstalować punkt odzyskiwania jako wolumin odzyskiwania na komputerze docelowym.

10. Na **przeglądanie i odzyskiwanie plików** wybierz opcję **Przeglądaj** Otwórz Eksploratora Windows i znaleźć pliki i foldery, które mają.

    ![Zrzut ekranu z odzyskać dane kreatora przeglądanie i odzyskiwanie plików strony](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. W Eksploratorze Windows skopiuj pliki i foldery z poziomu woluminu odzyskiwania, a następnie wklej je do docelowej lokalizacji maszyny. Można otworzyć lub przesyłanie strumieniowe plików bezpośrednio z poziomu woluminu odzyskiwania i sprawdź, czy mogą zostać odzyskane poprawne wersje.

    ![Zrzut ekranu programu Windows Explorer, kopią wyróżniony](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. Po zakończeniu, na **przeglądanie i odzyskiwanie plików** wybierz opcję **Odinstaluj**. Następnie wybierz pozycję **tak** aby upewnić się, że chcesz odinstalować wolumin.

    ![Zrzut ekranu z odzyskać dane kreatora przeglądanie i odzyskiwanie plików strony](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Jeśli nie zaznaczysz **Odinstaluj**, woluminu odzyskiwania pozostaną zainstalowane przez 6 godzin od momentu, gdy został zainstalowany. Jednak podczas instalacji jest rozszerzany maksymalnie 24 godziny, w przypadku ciągłego kopiowania plików. Żadne operacje tworzenia kopii zapasowej zostanie uruchomiony, gdy wolumin jest zainstalowany. Żadnych operacji tworzenia kopii zapasowej zaplanowane do uruchomienia w czasie, gdy wolumin jest zainstalowany, zostanie uruchomiony po odinstalowane woluminu odzyskiwania.
    >

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy zostały odzyskane pliki i foldery, możesz [Zarządzanie kopie zapasowe](backup-azure-manage-windows-server.md).

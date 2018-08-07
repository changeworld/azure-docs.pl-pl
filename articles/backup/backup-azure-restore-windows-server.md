---
title: Przywracanie danych w systemie Azure na komputerze z systemem Windows Server lub Windows
description: Dowiedz się, jak przywrócić dane przechowywane na platformie Azure na komputerze z systemem Windows Server lub Windows.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 8/6/2018
ms.author: saurse
ms.openlocfilehash: ddde297de49edb5f6543d03dfdb972771533301b
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39576189"
---
# <a name="restore-files-to-a-windows-server-or-windows-client-machine-using-resource-manager-deployment-model"></a>Przywracanie plików do maszyny z systemem Windows Server lub Client przy użyciu modelu wdrażania używającego usługi Resource Manager

W tym artykule wyjaśniono, jak przywrócić dane z magazynu kopii zapasowych. Aby przywrócić dane, należy użyć Kreatora odzyskiwania danych agenta usługi Microsoft Azure Recovery Services (MARS). Po przywróceniu danych, istnieje możliwość:

* Przywracanie danych na tym samym komputerze, z których wykonano kopie zapasowe.
* Przywracanie danych do alternatywnej maszynie.

W styczniu 2017 r. Firma Microsoft opublikowała aktualizację wersji zapoznawczej do agenta usług MARS. Oraz poprawek błędów Ta aktualizacja umożliwia natychmiastowe przywracanie, dzięki czemu można zainstalować migawkę punktu odzyskiwania zapisywalny, jako woluminu odzyskiwania. Następnie możesz eksplorować odzyskiwania woluminu i kopiować pliki na komputerze lokalnym, a tym samym selektywne Przywracanie plików.

> [!NOTE]
> [Stycznia 2017 aktualizacji usługi Azure Backup](https://support.microsoft.com/en-us/help/3216528?preview) jest wymagany, jeśli chcesz użyć błyskawiczne przywracania do przywracania danych. Również dane kopii zapasowych muszą być chronione w magazynach w ustawieniach regionalnych wymienionych w artykule pomocy technicznej. Zapoznaj się z [stycznia 2017 aktualizacji usługi Azure Backup](https://support.microsoft.com/en-us/help/3216528?preview) najbardziej aktualną listę ustawień regionalnych, które obsługują, natychmiastowe przywracanie. To natychmiastowe Przywracanie **nie** obecnie dostępny w wszystkich ustawień regionalnych.
>

Za pomocą natychmiastowe Przywracanie magazynów usługi Recovery Services w witrynie Azure portal. Jeśli dane są przechowywane w magazynach kopii zapasowej, ma został przekonwertowany do magazynów usługi Recovery Services. Jeśli chcesz użyć, natychmiastowe przywracanie, Pobierz aktualizację MARS, a następnie postępuj zgodnie z procedurami, zawierających, natychmiastowe przywracanie.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Użyj błyskawiczne polecenia Restore, aby odzyskać dane na tym samym komputerze

Jeśli przypadkowo usunięty plik i ma zostać przywrócony na tym samym komputerze (w którym jest wykonywana kopia zapasowa), poniższe kroki pomoże Ci odzyskać dane.

1. Otwórz **kopia zapasowa Microsoft Azure** przyciągania w. Jeśli nie znasz, którym zainstalowano przystawkę, wyszukaj komputerze lub serwerze, aby uzyskać **kopia zapasowa Microsoft Azure**.

    Aplikacja klasyczna powinna zostać wyświetlona w wynikach wyszukiwania.

2. Kliknij przycisk **Odzyskaj dane** Aby uruchomić kreatora.

    ![Odzyskiwanie danych](./media/backup-azure-restore-windows-server/recover.png)

3. Na **wprowadzenie** wybierz okienko, aby przywrócić dane w tym samym serwerze lub komputerze **ten serwer (`<server name>`)** i kliknij przycisk **dalej**.

    ![Wybierz tę opcję serwera, aby przywrócić dane na tym samym komputerze](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. Na **wybierz tryb odzyskiwania** okienku wybierz **pojedyncze pliki i foldery** a następnie kliknij przycisk **dalej**.

    ![Przeglądaj pliki](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)
> [!IMPORTANT]
> Możliwość przywrócenia *pojedyncze pliki i foldery* wymaga programu .NET Framework 4.5.2 lub nowszej. Jeśli nie widzisz *pojedyncze pliki i foldery* opcji, należy uaktualnić .NET Framework w wersji 4.5.2 lub nowszej, a następnie spróbuj ponownie.

5. Na **Wybierz wolumin i datę** okienku, wybierz wolumin, który zawiera pliki i/lub foldery, które mają zostać przywrócone.

    W kalendarzu wybierz punkt odzyskiwania. Dowolny punkt odzyskiwania można przywrócić w czasie. Daty **bold** wskazującą dostępność co najmniej jeden punkt odzyskiwania. Po wybraniu daty, jeśli dostępnych jest wiele punktów odzyskiwania, należy wybrać określony punkt odzyskiwania z **czasu** menu rozwijanego.

    ![Wolumin i datę](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Wybrany punkt odzyskiwania do przywrócenia, kliknij przycisk **instalacji**.

    Usługa Azure Backup instaluje punkt odzyskiwania lokalnych i wykorzystuje je jako woluminu odzyskiwania.

7. Na **przeglądanie i odzyskiwanie plików** okienku kliknij **Przeglądaj** Otwórz Eksploratora Windows i znaleźć pliki i foldery, które mają.

    ![Opcje odzyskiwania](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)


8. W Eksploratorze Windows skopiuj pliki i/lub foldery, które chcesz przywrócić i wklej je do dowolnej lokalizacji lokalnej na serwerze lub komputerze. Można otworzyć lub przesyłanie strumieniowe plików bezpośrednio z poziomu woluminu odzyskiwania i sprawdź, czy są odzyskiwane poprawne wersje.

    ![Skopiuj i Wklej pliki i foldery z zainstalowanego woluminu do lokalizacji lokalnej](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. Po zakończeniu przywracania plików i/lub folderów, na **przeglądanie i odzyskiwanie plików** okienku kliknij **Odinstaluj**. Następnie kliknij przycisk **tak** aby upewnić się, że chcesz odinstalować wolumin.

    ![Odinstalowuje wolumin i upewnij się](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Jeśli klikniesz odinstalowanie, woluminu odzyskiwania pozostaną zainstalowane przez 6 godzin od momentu, gdy został zainstalowany. Jednak podczas instalacji jest rozszerzona wynoszącą maksymalnie 24 godziny, w przypadku ciągłego kopiowania plików. Żadne operacje tworzenia kopii zapasowej zostanie uruchomiony, gdy wolumin jest zainstalowany. Wszelkie operacji tworzenia kopii zapasowej zaplanowane do uruchomienia w czasie, gdy wolumin jest zainstalowany, zostanie uruchomiony po odinstalowane woluminu odzyskiwania.
    >


## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Umożliwia natychmiastowe przywracanie przywrócenie danych do alternatywnej maszynie
W przypadku utraty całego serwera można jednak odzyskać dane z usługi Azure Backup na innym komputerze. Poniższe kroki ilustrują przepływu pracy.


Składa się z terminologią używaną w ramach tej procedury:

* *Maszyna źródłowa* — oryginalny komputer, z którym wykonano kopię zapasową i który jest obecnie niedostępna.
* *Maszyna docelowa* — machine, do którego dane są odzyskiwane.
* *Przykład magazynu* — magazyn usługi Recovery Services, do którego *maszyny źródłowej* i *maszyna docelowa* są rejestrowane. <br/>

> [!NOTE]
> Nie można przywrócić kopii zapasowych na maszynie docelowej, ze starszą wersją systemu operacyjnego. Na przykład kopii zapasowej, Windows 7, komputer może być przywrócona w systemie Windows 8 lub nowszym, komputer. Nie można przywrócić kopii zapasowej z komputera z systemem Windows 8 na komputerze Windows 7.
>
>

1. Otwórz **kopia zapasowa Microsoft Azure** przyciągania w sprawie *maszyna docelowa*.

2. Upewnij się, *maszyna docelowa* i *maszyny źródłowej* zostały zarejestrowane na tym samym magazynie usługi Recovery Services.

3. Kliknij przycisk **Odzyskaj dane** otworzyć **Kreatora odzyskiwania danych**.

    ![Odzyskiwanie danych](./media/backup-azure-restore-windows-server/recover.png)

4. Na **wprowadzenie** okienku wybierz **innego serwera**

    ![Inny serwer](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Podaj plik poświadczeń magazynu, który odpowiada *magazynu przykładowe*i kliknij przycisk **dalej**.

    Jeśli plik poświadczeń magazynu jest nieprawidłowa (lub wygasła), Pobierz nowy plik poświadczeń magazynu z *magazynu przykładowe* w witrynie Azure portal. Po podaniu poświadczeń prawidłowym magazynie pojawia się nazwę odpowiedniego magazynu kopii zapasowych.


6. Na **wybierz pozycję Utwórz kopię zapasową serwera** okienku wybierz *maszyny źródłowej* z listy wyświetlanych maszyn i podaj hasło. Następnie kliknij przycisk **Next** (Dalej).

    ![Lista komputerów](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. Na **wybierz tryb odzyskiwania** okienku wybierz **pojedyncze pliki i foldery** i kliknij przycisk **dalej**.

    ![Wyszukiwanie](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. Na **Wybierz wolumin i datę** okienku, wybierz wolumin, który zawiera pliki i/lub foldery, które mają zostać przywrócone.

    W kalendarzu wybierz punkt odzyskiwania. Dowolny punkt odzyskiwania można przywrócić w czasie. Daty **bold** wskazującą dostępność co najmniej jeden punkt odzyskiwania. Po wybraniu daty, jeśli dostępnych jest wiele punktów odzyskiwania, należy wybrać określony punkt odzyskiwania z **czasu** menu rozwijanego.

    ![Wyszukiwanie elementów](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Kliknij przycisk **instalacji** należy lokalnie zainstalować punkt odzyskiwania jako woluminu odzyskiwania na Twoje *maszyna docelowa*.

10. Na **przeglądanie i odzyskiwanie plików** okienku kliknij **Przeglądaj** Otwórz Eksploratora Windows i znaleźć pliki i foldery, które mają.

    ![Szyfrowanie](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. W Eksploratorze Windows skopiuj pliki i/lub foldery z poziomu woluminu odzyskiwania, a następnie wklej je do swojej *maszyna docelowa* lokalizacji. Można otworzyć lub przesyłanie strumieniowe plików bezpośrednio z poziomu woluminu odzyskiwania i upewnij się, że poprawne wersje są odzyskiwane.

    ![Szyfrowanie](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. Po zakończeniu przywracania plików i/lub folderów, na **przeglądanie i odzyskiwanie plików** okienku kliknij **Odinstaluj**. Następnie kliknij przycisk **tak** aby upewnić się, że chcesz odinstalować wolumin.

    ![Szyfrowanie](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Jeśli klikniesz odinstalowanie, woluminu odzyskiwania pozostaną zainstalowane przez 6 godzin od momentu, gdy został zainstalowany. Jednak podczas instalacji jest rozszerzona wynoszącą maksymalnie 24 godziny, w przypadku ciągłego kopiowania plików. Żadne operacje tworzenia kopii zapasowej zostanie uruchomiony, gdy wolumin jest zainstalowany. Wszelkie operacji tworzenia kopii zapasowej zaplanowane do uruchomienia w czasie, gdy wolumin jest zainstalowany, zostanie uruchomiony po odinstalowane woluminu odzyskiwania.
    >

## <a name="next-steps"></a>Kolejne kroki
* Teraz, gdy zostały odzyskane pliki i foldery, możesz [Zarządzanie kopie zapasowe](backup-azure-manage-windows-server.md).

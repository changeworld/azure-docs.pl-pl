---
title: Odzyskiwanie danych na platformie Azure do systemu Windows Server lub komputer z systemem Windows
description: Dowiedz się, jak przywrócić dane przechowywane na platformie Azure do systemu Windows Server lub komputer z systemem Windows.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 1/4/2018
ms.author: saurse
ms.openlocfilehash: 16f0460dea75b0dc52c3852d9947db0ad15f8fbe
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606328"
---
# <a name="restore-files-to-a-windows-server-or-windows-client-machine-using-resource-manager-deployment-model"></a>Przywracanie plików do maszyny z systemem Windows Server lub Client przy użyciu modelu wdrażania używającego usługi Resource Manager

W tym artykule wyjaśniono, jak przywrócić dane z magazynu kopii zapasowych. Aby przywrócić dane, używasz Kreatora odzyskiwania danych w agencie usług odzyskiwania Azure firmy Microsoft (MARS). Po przywróceniu danych, istnieje możliwość:

* Przywróć dane do tego samego komputera, z którego wykonano kopie zapasowe.
* Przywracanie danych do alternatywnej maszyny.

W stycznia 2017 r firma Microsoft opublikowała Podgląd aktualizacji agenta MARS. Wraz z poprawki ta aktualizacja umożliwia błyskawiczne przywrócić, dzięki czemu można zainstalować migawkę punktu odzyskiwania zapisywalny, jako wolumin odzyskiwania. Następnie można eksplorować odzyskiwania woluminu i kopiować pliki na komputerze lokalnym, a tym samym selektywne Przywracanie plików.

> [!NOTE]
> [Stycznia 2017 aktualizacji kopia zapasowa Azure](https://support.microsoft.com/en-us/help/3216528?preview) jest wymagany, jeśli chcesz użyć błyskawicznych przywracania, aby przywrócić dane. Również dane kopii zapasowej muszą być chronione w magazynach przy ustawieniach regionalnych wymienione w artykule pomocy technicznej. Zapoznaj się [stycznia 2017 aktualizacji kopia zapasowa Azure](https://support.microsoft.com/en-us/help/3216528?preview) najbardziej aktualną listę ustawień regionalnych, obsługujących błyskawicznych przywracania. Przywracanie błyskawicznych jest **nie** dostępne dla wszystkich ustawień regionalnych.
>

Za pomocą błyskawicznych przywrócić Magazyny usług odzyskiwania w portalu Azure. Jeśli dane są przechowywane w magazyny kopii zapasowych, ich został przekonwertowany na magazyny usług odzyskiwania. Jeśli chcesz użyć błyskawicznych przywracania, Pobierz aktualizację MARS i postępuj zgodnie z procedurami, których występuje błyskawicznych przywracania.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Użyj błyskawicznych przywracania, aby odzyskać dane do tej samej maszyny

Jeśli przypadkowo usunięto plik i chcesz przywrócić go do tego samego komputera (z którego jest pobierany kopii zapasowej), poniższe kroki pomoże Ci odzyskania danych.

1. Otwórz **kopia zapasowa Microsoft Azure** przyciągania w. Jeśli nie znasz, którym zainstalowano przystawkę, wyszukiwanie w komputerze lub serwerze dla **kopia zapasowa Microsoft Azure**.

    Aplikacja klasyczna powinny być wyświetlane w wynikach wyszukiwania.

2. Kliknij przycisk **odzyskać dane** Aby uruchomić kreatora.

    ![Odzyskiwanie danych](./media/backup-azure-restore-windows-server/recover.png)

3. Na **wprowadzenie** okienko, aby przywrócić dane do tego samego serwera lub komputera, wybierz **tego serwera (`<server name>`)** i kliknij przycisk **dalej**.

    ![Wybierz tę opcję serwera, aby przywrócić dane do tej samej maszyny](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. Na **wybierz tryb odzyskiwania** okienku wybierz **poszczególnych plików i folderów** , a następnie kliknij przycisk **dalej**.

    ![Przeglądanie plików](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)

5. Na **Wybierz wolumin i data** okienku, wybierz wolumin, który zawiera pliki lub foldery, które mają zostać przywrócone.

    W kalendarzu wybierz punkt odzyskiwania. Można przywrócić z dowolnego punktu odzyskiwania w czasie. Daty w **bold** wskazuje dostępność co najmniej jeden punkt odzyskiwania. Po wybraniu Data, jeśli dostępnych jest wiele punktów odzyskiwania, należy wybrać określony punkt odzyskiwania z **czasu** menu rozwijanego.

    ![Data i woluminu](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Kliknij wybrany punkt odzyskiwania, aby przywrócić **instalacji**.

    Kopia zapasowa Azure instaluje punkt odzyskiwania lokalnych i używa go jako wolumin odzyskiwania.

7. Na **przeglądania i odzyskiwanie plików** okienku, kliknij przycisk **Przeglądaj** Otwórz Eksploratora Windows i wyszukiwanie plików i folderów.

    ![Opcje odzyskiwania](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)


8. W Eksploratorze Windows skopiuj pliki lub foldery, które chcesz przywrócić i wklej je do dowolnej lokalizacji do serwera lub komputera lokalnego. Można otworzyć lub strumienia pliki bezpośrednio z woluminu odzyskiwania i sprawdź, czy poprawne wersje są odzyskiwane.

    ![Skopiuj i Wklej pliki i foldery z zainstalowanego woluminu do lokalizacji lokalnej](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. Po zakończeniu przywracania plików i/lub folderów, na **przeglądania i pliki odzyskiwania** okienku, kliknij przycisk **Odinstaluj**. Następnie kliknij przycisk **tak** potwierdzenie dezinstalacji woluminu.

    ![Dezinstalacji woluminu i Potwierdź](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Jeśli nie kliknij polecenie Odinstaluj, wolumin odzyskiwania pozostaną zainstalowane przez 6 godzin od momentu, gdy został zainstalowany. Czas instalacji jest jednak rozszerzonej maksymalnie maksymalnie 24 godziny, w przypadku bieżących kopiowania plików. Nie operacji tworzenia kopii zapasowej zostanie uruchomiony, gdy wolumin jest zainstalowany. Żadnej operacji tworzenia kopii zapasowej zaplanowane do uruchomienia w czasie, gdy wolumin jest zainstalowany, zostanie uruchomiony po odinstalowane woluminu odzyskiwania.
    >


## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Umożliwia błyskawiczne przywrócić przywracania danych do alternatywnej maszyny
W przypadku utraty cały serwer można jednak odzyskać dane z kopii zapasowej Azure na innym komputerze. Poniższe kroki przedstawiają przepływ pracy.


Składa się z terminologią używaną w następujące kroki:

* *Maszyna źródłowa* — oryginalnego komputera, z którego wykonano kopię zapasową i który jest obecnie niedostępny.
* *Maszyna docelowa* — komputera, do którego dane są odzyskiwane.
* *Przykładowe magazynu* — magazyn usług odzyskiwania i do którego *maszyny źródłowej* i *maszyny docelowej* są rejestrowane. <br/>

> [!NOTE]
> Nie można przywrócić kopii zapasowych na komputerze docelowym, ze starszą wersją systemu operacyjnego. Na przykład kopii zapasowej z systemu Windows 7, komputer może być przywrócona w systemie Windows 8 lub nowszym, komputer. Nie można przywrócić kopii zapasowej z komputera z systemem Windows 8 na komputerze z systemem Windows 7.
>
>

1. Otwórz **kopia zapasowa Microsoft Azure** przyciągania w na *maszyny docelowej*.

2. Upewnij się, *maszyny docelowej* i *maszyny źródłowej* są rejestrowane w tym samym magazynie usług odzyskiwania.

3. Kliknij przycisk **odzyskać dane** otworzyć **Kreatora odzyskiwania danych**.

    ![Odzyskiwanie danych](./media/backup-azure-restore-windows-server/recover.png)

4. Na **wprowadzenie** okienku wybierz **innego serwera**

    ![Inny serwer](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Podaj plik poświadczeń magazynu, który odpowiada *magazynu próbki*i kliknij przycisk **dalej**.

    Jeśli plik poświadczeń magazynu jest nieprawidłowa (lub wygasła), Pobierz nowy plik poświadczeń magazynu z *magazynu próbki* w portalu Azure. Po podaniu poświadczeń ważny magazyn, zostanie wyświetlony nazwę odpowiedniego magazynu kopii zapasowej.


6. Na **wybierz serwer zapasowy** okienku, wybierz *maszyny źródłowej* z listy wyświetlanych maszyn i podaj hasło. Następnie kliknij przycisk **Next** (Dalej).

    ![Lista komputerów](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. Na **wybierz tryb odzyskiwania** okienku, wybierz **poszczególnych plików i folderów** i kliknij przycisk **dalej**.

    ![Wyszukiwanie](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. Na **Wybierz wolumin i data** okienku, wybierz wolumin, który zawiera pliki lub foldery, które mają zostać przywrócone.

    W kalendarzu wybierz punkt odzyskiwania. Można przywrócić z dowolnego punktu odzyskiwania w czasie. Daty w **bold** wskazuje dostępność co najmniej jeden punkt odzyskiwania. Po wybraniu Data, jeśli dostępnych jest wiele punktów odzyskiwania, należy wybrać określony punkt odzyskiwania z **czasu** menu rozwijanego.

    ![Wyszukiwanie elementów](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Kliknij przycisk **instalacji** można lokalnie zainstalować jako woluminu odzyskiwania punkt odzyskiwania na Twojej *maszyny docelowej*.

10. Na **przeglądania i odzyskiwanie plików** okienku, kliknij przycisk **Przeglądaj** Otwórz Eksploratora Windows i wyszukiwanie plików i folderów.

    ![Szyfrowanie](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. W Eksploratorze Windows, skopiuj pliki lub foldery z tego woluminu, odzyskiwania i wklej je do Twojej *maszyny docelowej* lokalizacji. Można otworzyć lub strumienia pliki bezpośrednio z woluminu odzyskiwania i sprawdź, czy poprawne wersje są odzyskiwane.

    ![Szyfrowanie](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. Po zakończeniu przywracania plików i/lub folderów, na **przeglądania i pliki odzyskiwania** okienku, kliknij przycisk **Odinstaluj**. Następnie kliknij przycisk **tak** potwierdzenie dezinstalacji woluminu.

    ![Szyfrowanie](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Jeśli nie kliknij polecenie Odinstaluj, wolumin odzyskiwania pozostaną zainstalowane przez 6 godzin od momentu, gdy został zainstalowany. Czas instalacji jest jednak rozszerzonej maksymalnie maksymalnie 24 godziny, w przypadku bieżących kopiowania plików. Nie operacji tworzenia kopii zapasowej zostanie uruchomiony, gdy wolumin jest zainstalowany. Żadnej operacji tworzenia kopii zapasowej zaplanowane do uruchomienia w czasie, gdy wolumin jest zainstalowany, zostanie uruchomiony po odinstalowane woluminu odzyskiwania.
    >

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Jeśli kopia zapasowa Azure nie jest pomyślnie instalowany woluminu odzyskiwania nawet po kilku minutach klikanie **instalacji** lub kończy się niepowodzeniem, można zainstalować woluminu odzyskiwania z co najmniej jeden błąd, wykonaj poniższe kroki, aby rozpocząć odzyskiwanie normalnie.

1.  Anuluj proces trwającej instalacji w przypadku uruchomienia na kilka minut.

2.  Upewnij się, że jesteś w najnowszej wersji agenta usługi Kopia zapasowa Azure. Aby uzyskać informacje o wersji agenta usługi Kopia zapasowa Azure, kliknij **o Microsoft agenta usług odzyskiwania Azure** na **akcje** kopia zapasowa Microsoft Azure w okienku konsoli i upewnij się, że **Wersji** liczba jest równa lub wyższa niż wersja wspomnianego [w tym artykule](https://go.microsoft.com/fwlink/?linkid=229525). Możesz pobrać najnowszą wersję ze [tutaj](https://go.microsoft.com/fwLink/?LinkID=288905)

3.  Przejdź do **Menedżera urządzeń** -> **kontrolerów magazynu** i upewnij się, że można zlokalizować **inicjatora iSCSI firmy Microsoft**. Jeśli mogą ją odnaleźć, bezpośrednio przejść do kroku 7 poniżej. 

4.  Jeśli nie można zlokalizować Usługa inicjatora iSCSI firmy Microsoft, jak wspomniano w kroku 3, sprawdź, jeśli można odnaleźć wpisu w obszarze **Menedżera urządzeń** -> **kontrolerów magazynu** o nazwie  **Nieznane urządzenie** o identyfikatorze sprzętu **ROOT\ISCSIPRT**.

5.  Kliknij prawym przyciskiem myszy **nieznane urządzenie** i wybierz **aktualizacji sterowników**.

6.  Aktualizacja sterownika, wybierając opcję **Wyszukaj automatycznie zaktualizowane sterowniki**. Zakończenie aktualizacji należy zmienić **nieznane urządzenie** do **inicjatora iSCSI firmy Microsoft** jak pokazano poniżej. 

    ![Szyfrowanie](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Przejdź do **Menedżera zadań** -> **usługi (lokalne)** -> **Usługa inicjatora iSCSI firmy Microsoft**. 

    ![Szyfrowanie](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)
    
8.  Ponownie uruchom usługę inicjatora iSCSI firmy Microsoft przez usługę, klikając prawym przyciskiem myszy **zatrzymać** i ponowne kliknięcie prawym przyciskiem myszy, a następnie klikając **Start**.

9.  Ponów odzyskiwanie przy użyciu błyskawicznych przywracania. 

W przypadku odzyskiwania nadal niepowodzenia ponownego uruchomienia serwera/klienta. Jeśli ponowne uruchomienie komputera nie jest pożądane lub odzyskiwania nadal kończy się niepowodzeniem nawet po ponownego uruchomienia serwera, spróbuj przeprowadzić odzyskiwanie z alternatywnych maszyny i skontaktuj się z Azure obsługuje, przechodząc do [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) i przesyłanie żądania pomocy technicznej.

## <a name="next-steps"></a>Kolejne kroki
* Teraz, gdy zostały odzyskane pliki i foldery, możesz [Zarządzanie kopii zapasowych](backup-azure-manage-windows-server.md).

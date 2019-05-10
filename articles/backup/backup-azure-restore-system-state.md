---
title: 'Usługa Azure Backup: Przywracanie stanu systemu z systemem Windows Server'
description: Krok po kroku wyjaśnienie przywracania stanu systemu Windows Server z kopii zapasowej na platformie Azure.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 8/18/2017
ms.author: saurse
ms.openlocfilehash: 6619611bee96089e465feb6f50d38caeada06dd9
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472505"
---
# <a name="restore-system-state-to-windows-server"></a>Przywracanie stanu systemu do systemu Windows Server

W tym artykule opisano sposób przywracania kopii zapasowych stanu systemu Windows Server z magazynu usługi Azure Recovery Services. Aby przywrócić stan systemu, musi mieć kopię zapasową stanu systemu (utworzony zgodnie z instrukcjami przedstawionymi w [tworzenie kopii zapasowej stanu systemu](backup-azure-system-state.md#back-up-windows-server-system-state)i upewnij się, że zainstalowano [najnowszą wersję programu Microsoft Azure Recovery Services (MARS) Agent](https://aka.ms/azurebackup_agent). Odzyskiwanie stanu systemu Windows Server danych z magazynu usługi Azure Recovery Services jest procesem dwuetapowym:

1. Przywracanie stanu systemu jako pliki z usługi Azure Backup. W przypadku przywracania stanu systemu jako pliki z usługi Azure Backup można wykonać jedną z następujących czynności:
   * Przywracanie stanu systemu na tym samym serwerze których wykonano kopie zapasowe lub
   * Przywracanie stanu systemu plików na alternatywnym serwerze.

2. Zastosuj przywróconych plików stanu systemu z systemem Windows Server.


## <a name="recover-system-state-files-to-the-same-server"></a>Odzyskać pliki stanu systemu na tym samym serwerze
Poniższe kroki wyjaśniają, jak można wycofać konfiguracji systemu Windows Server do poprzedniego stanu. Wycofywanie konfiguracji serwera znanego stanu stabilny, może być bardzo cenne. Poniższa procedura opisuje przywrócenie stanu systemu serwera z magazynu usługi Recovery Services.

1. Otwórz przystawkę **Microsoft Azure Backup**. Jeśli nie wiesz, gdzie zainstalowano przystawkę, wyszukaj komputerze lub serwerze **kopia zapasowa Microsoft Azure**.

    Aplikacja klasyczna powinna zostać wyświetlona w wynikach wyszukiwania.

2. Kliknij przycisk **Odzyskaj dane** Aby uruchomić kreatora.

    ![Odzyskiwanie danych](./media/backup-azure-restore-windows-server/recover.png)

3. Na **wprowadzenie** wybierz okienko, aby przywrócić dane w tym samym serwerze lub komputerze **ten serwer (`<server name>`)** i kliknij przycisk **dalej**.

    ![Wybierz tę opcję serwera, aby przywrócić dane na tym samym komputerze](./media/backup-azure-restore-system-state/samemachine.png)

4. Na **wybierz tryb odzyskiwania** okienku wybierz **stanu systemu** a następnie kliknij przycisk **dalej**.

    ![Przeglądaj pliki](./media/backup-azure-restore-system-state/recover-type-selection.png)

5. W kalendarzu w **Wybierz wolumin i datę** punktu okienku wybierz odzyskiwania.

    Dowolny punkt odzyskiwania można przywrócić w czasie. Daty **bold** wskazującą dostępność co najmniej jeden punkt odzyskiwania. Po wybraniu daty, jeśli dostępnych jest wiele punktów odzyskiwania, należy wybrać określony punkt odzyskiwania z **czasu** menu rozwijanego.

    ![Wolumin i datę](./media/backup-azure-restore-system-state/select-date.png)

6. Wybrany punkt odzyskiwania do przywrócenia, kliknij przycisk **dalej**.

    Usługa Azure Backup instaluje punkt odzyskiwania lokalnych i wykorzystuje je jako woluminu odzyskiwania.

7. W kolejnym okienku określ miejsce docelowe dla odzyskane pliki stanu systemu, a następnie kliknij przycisk **Przeglądaj** Otwórz Eksploratora Windows i znaleźć pliki i foldery, które mają. Opcja **tworzenie kopii, tak aby mieć obie wersje**, tworzy kopie poszczególnych plików w istniejącego archiwum pliku stanu systemu, zamiast tworzyć kopię całego archiwum stanu systemu.

    ![Opcje odzyskiwania](./media/backup-azure-restore-system-state/recover-as-files.png)

8. Sprawdź szczegóły odzyskiwania na **potwierdzenie** okienku i kliknij przycisk **odzyskać**.

   ![Kliknij przycisk Odzyskaj, aby potwierdzić akcję odzyskiwanie](./media/backup-azure-restore-system-state/confirm-recovery.png)

9. Kopiuj *WindowsImageBackup* katalogu w miejscu docelowym odzyskiwania do zastosowania niekrytyczne woluminu serwera. Zwykle wolumin systemu operacyjnego Windows jest wolumin krytyczny.

10. Gdy odzyskiwanie zakończy się pomyślnie, wykonaj kroki opisane w sekcji [Zastosuj przywrócić pliki stanu systemu do systemu Windows Server](backup-azure-restore-system-state.md), aby zakończyć proces odzyskiwania stanu systemu.

## <a name="recover-system-state-files-to-an-alternate-server"></a>Odzyskać pliki stanu systemu na alternatywnym serwerze

Jeśli w systemie Windows Server jest uszkodzony lub niedostępny i chcesz przywrócić go do stanu stabilnego przy odzyskiwaniu stanu systemu Windows Server, można przywrócić uszkodzony serwer stanu systemu z innego serwera. Wykonaj następujące kroki przywracania stanu systemu na osobnym serwerze.  

Składa się z terminologią używaną w ramach tej procedury:

- *Maszyna źródłowa* — oryginalny komputer, z którym wykonano kopię zapasową i który jest obecnie niedostępna.
- *Maszyna docelowa* — machine, do którego dane są odzyskiwane.
- *Przykład magazynu* — magazyn usługi Recovery Services, do którego *maszyny źródłowej* i *maszyna docelowa* są rejestrowane. <br/>

> [!NOTE]
> Nie można przywrócić kopii zapasowych wykonanych z jednego komputera na maszynie z systemem wcześniejszej wersji systemu operacyjnego. Na przykład kopie zapasowe wykonane na komputerze z systemem Windows Server 2016 nie można przywrócić do systemu Windows Server 2012 R2. Możliwe jest jednak odwrotność. Tworzenie kopii zapasowych w systemie Windows Server 2012 R2 umożliwia przywracania systemu Windows Server 2016.
>

1. Otwórz **kopia zapasowa Microsoft Azure** przystawki na *maszyna docelowa*.
2. Upewnij się, że *maszyna docelowa* i *maszyny źródłowej* zostały zarejestrowane na tym samym magazynie usługi Recovery Services.
3. Kliknij przycisk **Odzyskaj dane** do inicjowania przepływu pracy.
4. Wybierz **innego serwera**

    ![Another Server](./media/backup-azure-restore-system-state/anotherserver.png)

5. Podaj plik poświadczeń magazynu, który odpowiada *magazynu przykładowe*. Jeśli plik poświadczeń magazynu jest nieprawidłowa (lub wygasła), Pobierz nowy plik poświadczeń magazynu z *magazynu przykładowe* w witrynie Azure portal. Po ich podaniu plik poświadczeń magazynu jest wyświetlana magazyn usługi Recovery Services, skojarzony plik poświadczeń magazynu.

6. W okienku wybierz pozycję Utwórz kopię zapasową serwera wybierz *maszyny źródłowej* z listy wyświetlanych maszyn.
7. W okienku wybierz tryb odzyskiwania wybierz **stanu systemu** i kliknij przycisk **dalej**.

    ![wyszukaj](./media/backup-azure-restore-system-state/recover-type-selection.png)

8. W kalendarzu w **Wybierz wolumin i datę** punktu okienku wybierz odzyskiwania. Dowolny punkt odzyskiwania można przywrócić w czasie. Daty **bold** wskazującą dostępność co najmniej jeden punkt odzyskiwania. Po wybraniu daty, jeśli dostępnych jest wiele punktów odzyskiwania, należy wybrać określony punkt odzyskiwania z **czasu** menu rozwijanego.

    ![Wyszukiwanie elementów](./media/backup-azure-restore-system-state/select-date.png)

9. Wybrany punkt odzyskiwania do przywrócenia, kliknij przycisk **dalej**.

10. Na **wybierz tryb odzyskiwania stanu systemu** okienku określ miejsce docelowe, do którego pliki stanu systemu można odzyskać, a następnie kliknij przycisk **dalej**.

    ![Szyfrowanie](./media/backup-azure-restore-system-state/recover-as-files.png)

    Opcja **tworzenie kopii, tak aby mieć obie wersje**, tworzy kopie poszczególnych plików w istniejącego archiwum pliku stanu systemu, zamiast tworzyć kopię całego archiwum stanu systemu.

11. Sprawdź szczegóły odzyskiwania, w okienku potwierdzenia, a następnie kliknij przycisk **odzyskać**.

    ![Kliknij przycisk Odzyskaj, aby upewnić się, proces odzyskiwania](./media/backup-azure-restore-system-state/confirm-recovery.png)

12. Kopiuj *WindowsImageBackup* katalogu woluminowi niekrytyczne serwera (na przykład D:\). Zazwyczaj wolumin systemu operacyjnego Windows jest wolumin krytyczny.

13. Aby zakończyć proces odzyskiwania, należy użyć następujących sekcji, aby [zastosować przywróconych plików stanu systemu na serwerze Windows](#apply-restored-system-state-on-a-windows-server).




## <a name="apply-restored-system-state-on-a-windows-server"></a>Zastosować przywrócony stan systemu w systemie Windows Server

Raz odzyskały stan systemu jako pliki przy użyciu agenta usługi Azure Recovery Services, użyj narzędzia Kopia zapasowa systemu Windows Server mają dotyczyć odzyskane dane o stanie systemu Windows Server. Narzędzie Kopia zapasowa systemu Windows Server jest już dostępny na serwerze. Poniższe kroki wyjaśniają, jak zastosować odzyskane dane o stanie systemu.

1. Użyj następujących poleceń do ponownego rozruchu serwera w *trybie naprawy usług katalogowych*. W wierszu polecenia z podwyższonym poziomem uprawnień:

    ```
    PS C:\> Bcdedit /set safeboot dsrepair
    PS C:\> Shutdown /r /t 0
    ```

2. Po ponownym uruchomieniu otwórz przystawkę narzędzia Kopia zapasowa systemu Windows Server. Jeśli nie wiesz, gdzie zainstalowano przystawkę, wyszukaj komputerze lub serwerze **kopia zapasowa systemu Windows Server**.

    Aplikacja klasyczna, zostanie wyświetlony w wynikach wyszukiwania.

3. W przystawce, wybierz **lokalna kopia zapasowa**.

    ![Wybieranie lokalnej kopii zapasowej można przywrócić z tego miejsca](./media/backup-azure-restore-system-state/win-server-backup-local-backup.png)

4. W konsoli lokalnej kopii zapasowej w **okienko akcji**, kliknij przycisk **odzyskać** aby otworzyć Kreatora odzyskiwania.

5. Wybierz opcję **kopia zapasowa jest przechowywana w innej lokalizacji**i kliknij przycisk **dalej**.

   ![Wybierz opcję odzyskiwania do innego serwera](./media/backup-azure-restore-system-state/backup-stored-in-diff-location.png)

6. Podczas określania typu lokalizacji, wybierz **zdalny folder udostępniony** Jeśli odzyskano kopii zapasowej stanu systemu do innego serwera. Jeśli stan systemu został odzyskany lokalnie, wybierz **dyski lokalne**.

    ![Wybierz opcję odzyskiwania z serwera lokalnego lub innego](./media/backup-azure-restore-system-state/ss-recovery-remote-shared-folder.png)

7. Wprowadź ścieżkę do *WindowsImageBackup* katalogu, lub wybierz lokalny dysk zawierający ten katalog (na przykład D:\WindowsImageBackup) odzyskane w ramach odzyskiwania pliki stanu systemu za pomocą usług Azure Recovery Services Agent i kliknij przycisk **dalej**.

    ![Ścieżka do udostępnionego pliku](./media/backup-azure-restore-system-state/ss-recovery-remote-folder.png)

8. Wybierz wersję stanu systemu, który chcesz przywrócić, a następnie kliknij przycisk **dalej**.

9. W okienku wybierz typ odzyskiwania, wybierz **stanu systemu** i kliknij przycisk **dalej**.

10. Do lokalizacji odzyskiwania stanu systemu, wybierz **oryginalnej lokalizacji**i kliknij przycisk **dalej**.

11. Przejrzyj szczegóły dotyczące potwierdzenia, sprawdź ustawienia ponownego uruchomienia, a następnie kliknij przycisk **odzyskać** do zastosowania przywróconych plików stanu systemu.

    ![Uruchom Przywracanie stanu systemu plików](./media/backup-azure-restore-system-state/launch-ss-recovery.png)

## <a name="special-considerations-for-system-state-recovery-on-active-directory-server"></a>Specjalne uwagi dotyczące odzyskiwania stanu systemu na serwerze usługi Active Directory

Kopia zapasowa stanu systemu zawiera dane usługi Active Directory. Umożliwia następujące czynności przywrócenie usług domenowych w usłudze Active Directory (AD DS) z jego bieżącym stanie do poprzedniego stanu.

1. Uruchom ponownie kontroler domeny w trybie przywracania usług katalogu (DSRM).
2. Postępuj zgodnie z instrukcjami [tutaj](https://technet.microsoft.com/library/cc794755(v=ws.10).aspx) odzyskać usług AD DS za pomocą poleceń cmdlet narzędzia Kopia zapasowa systemu Windows Server.


## <a name="troubleshoot-failed-system-state-restore"></a>Rozwiązywanie problemów z niepowodzenia przywracania stanu systemu

Jeśli poprzedniego procesu stosowania stanu systemu nie zostanie ukończone pomyślnie, należy użyć środowiska odzyskiwania Windows (Windows RE) w celu odzyskania systemu Windows Server. Poniższe kroki wyjaśniają, jak można odzyskać za pomocą środowiska Windows RE. Użyj tej opcji tylko wtedy, gdy system Windows Server nie normalny rozruch po przywróceniu stanu systemu. Następujący proces powoduje usunięcie danych-system, należy zachować ostrożność.

1. Rozruch serwera z systemem Windows w środowisku odzyskiwania Windows (Windows RE).

2. Wybierz Rozwiązywanie problemów z trzech dostępnych opcji.

    ![Otwieranie menu](./media/backup-azure-restore-system-state/winre-1.png)

3. Z **zaawansowane opcje** ekranu, wybierz opcję **polecenia** i podaj nazwę użytkownika administratora serwera i hasło.

   ![Otwieranie menu](./media/backup-azure-restore-system-state/winre-2.png)

4. Podaj nazwę administratora serwera i hasło.

    ![Otwieranie menu](./media/backup-azure-restore-system-state/winre-3.png)

5. Gdy otwierasz wiersz polecenia w trybie administratora, uruchom następujące polecenie, aby uzyskać kopii zapasowych stanu systemu.

    ```
    Wbadmin get versions -backuptarget:<Volume where WindowsImageBackup folder is copied>:
    ```
    ![Pobieranie wersji kopii zapasowej stanu systemu](./media/backup-azure-restore-system-state/winre-4.png)

6. Uruchom następujące polecenie, aby uzyskać wszystkie woluminy dostępne w kopii zapasowej.

    ```
    Wbadmin get items -version:<copy version from above step> -backuptarget:<Backup volume>
    ```

    ![Pobieranie wersji kopii zapasowej stanu systemu](./media/backup-azure-restore-system-state/winre-5.png)

7. Następujące polecenie odzyskuje wszystkie woluminy, które są częścią kopii zapasowej stanu systemu. Należy pamiętać, że ten krok przywraca tylko woluminy krytyczne, które są częścią stanu systemu. Wszystkie dane systemu nie są usuwane.

    ```
    Wbadmin start recovery -items:C: -itemtype:Volume -version:<Backupversion> -backuptarget:<backup target volume>
    ```
     ![Pobieranie wersji kopii zapasowej stanu systemu](./media/backup-azure-restore-system-state/winre-6.png)



## <a name="next-steps"></a>Kolejne kroki
* Teraz, gdy zostały odzyskane pliki i foldery, możesz [Zarządzanie kopie zapasowe](backup-azure-manage-windows-server.md).

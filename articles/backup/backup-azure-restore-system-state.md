---
title: 'Kopii zapasowej systemu Azure: Stan systemu przywracania do systemu Windows Server | Dokumentacja firmy Microsoft'
description: Krok wyjaśnienie krok przywracania stanu systemu Windows Server z kopii zapasowej na platformie Azure.
services: backup
documentationcenter: ''
author: saurabhsensharma
manager: shivamg
editor: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/18/2017
ms.author: saurse;trinadhk;markgal;
ms.openlocfilehash: c673cca6a35cfdc0edaecdc69a797f48772d847c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="restore-system-state-to-windows-server"></a>Przywracanie stanu systemu do systemu Windows Server

W tym artykule opisano sposób przywracania kopii zapasowych stanu systemu Windows Server z magazynu usług odzyskiwania Azure. Aby przywrócić stan systemu, musi mieć kopię zapasową stanu systemu (utworzone przy użyciu instrukcji w [Utwórz kopię zapasową stanu systemu](backup-azure-system-state.md#back-up-windows-server-system-state)i upewnij się, że zainstalowano [najnowszą wersję usług odzyskiwania Azure firmy Microsoft (MARS) Agent](http://aka.ms/azurebackup_agent). Odzyskiwanie stanu systemu Windows Server danych z magazynu usług odzyskiwania Azure jest procesem dwuetapowym:

1. Przywróć stan systemu jako pliki kopii zapasowej Azure. Podczas przywracania stanu systemu plików z kopii zapasowej Azure, można wykonać jedną z następujących czynności:
  * Przywracanie stanu systemu na tym samym serwerze których wykonano kopie zapasowe, lub
  * Przywróć stan systemu plików na alternatywnym serwerze.

2. Zastosuj przywróconych plików stanu systemu do systemu Windows Server.


## <a name="recover-system-state-files-to-the-same-server"></a>Odzyskać stan systemu plików na tym samym serwerze
Poniższe kroki objaśniają wycofywaniu konfiguracji systemu Windows Server do poprzedniego stanu. Wycofywanie konfiguracji serwera znanego stanu stabilny, może być bardzo istotne. Poniższe kroki przywrócić stan systemu serwera z magazynu usług odzyskiwania. 

1. Otwórz przystawkę **Microsoft Azure Backup**. Jeśli nie wiadomo, w którym zainstalowano przystawkę, wyszukiwanie w komputerze lub serwerze dla **kopia zapasowa Microsoft Azure**.

    Aplikacja klasyczna powinny być wyświetlane w wynikach wyszukiwania.

2. Kliknij przycisk **odzyskać dane** Aby uruchomić kreatora.

    ![Odzyskiwanie danych](./media/backup-azure-restore-windows-server/recover.png)

3. Na **wprowadzenie** okienko, aby przywrócić dane do tego samego serwera lub komputera, wybierz **tego serwera (`<server name>`)** i kliknij przycisk **dalej**.

    ![Wybierz tę opcję serwera, aby przywrócić dane do tej samej maszyny](./media/backup-azure-restore-system-state/samemachine.png)

4. Na **wybierz tryb odzyskiwania** okienku wybierz **stanu systemu** , a następnie kliknij przycisk **dalej**.

    ![Przeglądanie plików](./media/backup-azure-restore-system-state/recover-type-selection.png)

5. W kalendarzu w **Wybierz wolumin i data** punktu okienku, wybierz odzyskiwania. 

    Można przywrócić z dowolnego punktu odzyskiwania w czasie. Daty w **bold** wskazuje dostępność co najmniej jeden punkt odzyskiwania. Po wybraniu Data, jeśli dostępnych jest wiele punktów odzyskiwania, należy wybrać określony punkt odzyskiwania z **czasu** menu rozwijanego.

    ![Data i woluminu](./media/backup-azure-restore-system-state/select-date.png)

6. Kliknij wybrany punkt odzyskiwania, aby przywrócić **dalej**.

    Kopia zapasowa Azure instaluje punkt odzyskiwania lokalnych i używa go jako wolumin odzyskiwania.

7. W okienku dalej określ miejsce docelowe do odzyskiwanych plików stanu systemu, a następnie kliknij przycisk **Przeglądaj** Otwórz Eksploratora Windows i wyszukiwanie plików i folderów. Opcji **kopie tak, aby obie wersje**, tworzy kopie poszczególnych plików w archiwum istniejącego pliku stanu systemu zamiast tworzyć kopię całego archiwum stanu systemu.

    ![Opcje odzyskiwania](./media/backup-azure-restore-system-state/recover-as-files.png)

8. Sprawdź szczegóły odzyskiwania na **potwierdzenie** okienko i kliknij przycisk **odzyskać**.

   ![Kliknij przycisk Odzyskaj na potwierdzenie akcji odzyskiwania](./media/backup-azure-restore-system-state/confirm-recovery.png)

9. Kopiuj *WindowsImageBackup* katalogu w miejscu docelowym odzyskiwania na woluminie niekrytyczne serwera. Zazwyczaj wolumin systemu operacyjnego jest wolumin krytyczny.

10. Gdy odzyskiwanie zakończy się pomyślnie, wykonaj kroki opisane w sekcji [Zastosuj przywrócić stan systemu plików w systemie Windows Server](backup-azure-restore-system-state.md#apply-restored-system-state-files-to-the-windows-server), aby zakończyć proces odzyskiwania stanu systemu.

## <a name="recover-system-state-files-to-an-alternate-server"></a>Odzyskać stan systemu plików na alternatywnym serwerze

Jeśli serwera z systemem Windows jest uszkodzony lub jest niedostępny, i chcesz przywrócić stabilności przez odzyskiwanie stanu systemu Windows Server, można przywrócić stan systemu serwera uszkodzony z innego serwera. Wykonaj następujące kroki do przywrócenia stanu systemu na osobnym serwerze.  

Składa się z terminologią używaną w następujące kroki:

- *Maszyna źródłowa* — oryginalnego komputera, z którego wykonano kopię zapasową i który jest obecnie niedostępny.
- *Maszyna docelowa* — komputera, do którego dane są odzyskiwane.
- *Przykładowe magazynu* — magazyn usług odzyskiwania i do którego *maszyny źródłowej* i *maszyny docelowej* są rejestrowane. <br/>

> [!NOTE]
> Nie można przywrócić kopii zapasowych wykonanych z jednego komputera na komputerze ze starszą wersją systemu operacyjnego. Na przykład kopii zapasowych wykonanych z komputera z systemem Windows Server 2016 nie można przywrócić do systemu Windows Server 2012 R2. Możliwe jest jednak odwrotność. Tworzenie kopii zapasowych w systemie Windows Server 2012 R2 umożliwia przywracania systemu Windows Server 2016.
>

1. Otwórz **kopia zapasowa Microsoft Azure** przystawki na *maszyny docelowej*.
2. Upewnij się, że *maszyny docelowej* i *maszyny źródłowej* są rejestrowane w tym samym magazynie usług odzyskiwania.
3. Kliknij przycisk **odzyskać dane** do inicjowania przepływu pracy.
4. Wybierz **innego serwera**

    ![Inny serwer](./media/backup-azure-restore-system-state/anotherserver.png)

5. Podaj plik poświadczeń magazynu, który odpowiada *magazynu próbki*. Jeśli plik poświadczeń magazynu jest nieprawidłowa (lub wygasła), Pobierz nowy plik poświadczeń magazynu z *magazynu próbki* w portalu Azure. Po podany plik poświadczeń magazynu zostanie wyświetlony w magazynie usług odzyskiwania skojarzonych z plik poświadczeń magazynu.

6. W okienku wybierz pozycję Utwórz kopię zapasową serwera wybierz *maszyny źródłowej* z listy wyświetlanych maszyn.
7. W okienku wybierz trybie odzyskiwania, wybierz **stanu systemu** i kliknij przycisk **dalej**. 

    ![Wyszukiwanie](./media/backup-azure-restore-system-state/recover-type-selection.png)

8. W kalendarzu w **Wybierz wolumin i data** punktu okienku, wybierz odzyskiwania. Można przywrócić z dowolnego punktu odzyskiwania w czasie. Daty w **bold** wskazuje dostępność co najmniej jeden punkt odzyskiwania. Po wybraniu Data, jeśli dostępnych jest wiele punktów odzyskiwania, należy wybrać określony punkt odzyskiwania z **czasu** menu rozwijanego. 

    ![Wyszukiwanie elementów](./media/backup-azure-restore-system-state/select-date.png)

9. Kliknij wybrany punkt odzyskiwania, aby przywrócić **dalej**.

10. Na **wybierz tryb odzyskiwania stanu systemu** okienka, określ docelowy wybrany stan systemu plików, można odzyskać, a następnie kliknij pozycję **dalej**.

    ![Szyfrowanie](./media/backup-azure-restore-system-state/recover-as-files.png)

    Opcji **kopie tak, aby obie wersje**, tworzy kopie poszczególnych plików w archiwum istniejącego pliku stanu systemu zamiast tworzyć kopię całego archiwum stanu systemu.

11. Sprawdź szczegóły odzyskiwania, w okienku potwierdzenie, a następnie kliknij przycisk **odzyskać**. 

    ![Kliknij przycisk Odzyskaj, aby potwierdzić, proces odzyskiwania](./media/backup-azure-restore-system-state/confirm-recovery.png)

12. Kopiuj *WindowsImageBackup* katalogu do woluminu niekrytyczne serwera (na przykład D:\). Wolumin systemu operacyjnego jest zazwyczaj wolumin krytyczny.

13. Aby zakończyć proces odzyskiwania, należy użyć następujących sekcji, aby [zastosować przywróconych plików stanu systemu w systemie Windows Server](backup-azure-restore-system-state.md#apply-restored-system-state-on-a-windows-server).




## <a name="apply-restored-system-state-on-a-windows-server"></a>Zastosuj przywróconego stanu systemu w systemie Windows Server

Raz odzyskały stan systemu jako plików za pomocą agenta usług odzyskiwania Azure, dotyczą odzyskane stanu systemu Windows Server za pomocą narzędzia Kopia zapasowa systemu Windows Server. Narzędzie Kopia zapasowa systemu Windows Server jest już dostępny na serwerze. Poniższych krokach opisano sposób stosowania odzyskane dane o stanie systemu.

1. Użyj następujących poleceń ponownego uruchomienia serwera w *trybie naprawy usług katalogowych*. W wierszu polecenia z podwyższonym poziomem uprawnień:

    ```
    PS C:\> Bcdedit /set safeboot dsrepair
    PS C:\> Shutdown /r /t 0
    ```

2. Po ponownym uruchomieniu otwórz przystawkę narzędzia Kopia zapasowa systemu Windows Server. Jeśli nie wiadomo, w którym zainstalowano przystawkę, wyszukiwanie w komputerze lub serwerze dla **kopia zapasowa systemu Windows Server**.

    Aplikacja klasyczna pojawia się w wynikach wyszukiwania.

3. W przystawce, wybierz **lokalna kopia zapasowa**.

    ![Wybierz lokalna kopia zapasowa, aby przywrócić z tego miejsca](./media/backup-azure-restore-system-state/win-server-backup-local-backup.png)

4. W konsoli lokalnej kopii zapasowej w **okienka Akcje**, kliknij przycisk **odzyskać** aby otworzyć Kreatora odzyskiwania.

5. Wybierz opcję **kopia zapasowa jest przechowywana w innej lokalizacji**i kliknij przycisk **dalej**.

   ![Wybierz opcję odzyskiwania do innego serwera](./media/backup-azure-restore-system-state/backup-stored-in-diff-location.png)

6. Podczas określania typu lokalizacji, wybierz **zdalnego folderu udostępnionego** Jeśli odzyskano kopii zapasowej stanu systemu do innego serwera. Jeśli stan systemu został odzyskany lokalnie, następnie wybierz **dyski lokalne**. 

    ![Wybierz opcję odzyskiwania z serwera lokalnego lub innego](./media/backup-azure-restore-system-state/ss-recovery-remote-shared-folder.png)

7. Wprowadź ścieżkę do *WindowsImageBackup* katalogu, lub wybierz lokalny dysk zawierający ten katalog (na przykład D:\WindowsImageBackup) odzyskiwane podczas odzyskiwania pliki stanu systemu za pomocą agenta usług odzyskiwania Azure i kliknij przycisk **dalej**.

    ![Ścieżka do pliku udostępnionego](./media/backup-azure-restore-system-state/ss-recovery-remote-folder.png)

8. Wybierz wersję stanu systemu, który chcesz przywrócić, a następnie kliknij przycisk **dalej**.

9. W okienku wybierz typ odzyskiwania, wybierz **stanu systemu** i kliknij przycisk **dalej**.

10. Dla lokalizacji odzyskiwania stanu systemu, wybierz **oryginalnej lokalizacji**i kliknij przycisk **dalej**.

11. Przejrzyj szczegóły potwierdzenie, sprawdź ustawienia ponowne uruchomienie komputera, a następnie kliknij przycisk **odzyskać** do applly przywrócony stan systemu plików.

    ![Uruchamianie przywracania stanu systemu plików](./media/backup-azure-restore-system-state/launch-ss-recovery.png)

## <a name="special-considerations-for-system-state-recovery-on-active-directory-server"></a>Uwagi dotyczące odzyskiwania stanu systemu na serwerze usługi Active Directory

Kopia zapasowa stanu systemu zawiera danych usługi Active Directory. Następujące kroki umożliwiają przywracania usługi domenowe w usłudze Active Directory (AD DS) z jego bieżącym stanie do poprzedniego stanu.

1. Uruchom ponownie kontroler domeny w trybie przywracania usług katalogowych (DSRM).
2. Wykonaj kroki [tutaj](https://technet.microsoft.com/library/cc794755(v=ws.10).aspx) używania poleceń cmdlet narzędzia Kopia zapasowa systemu Windows Server do odzyskania usług AD DS.


## <a name="troubleshoot-failed-system-state-restore"></a>Rozwiązywanie problemów z niepowodzeniem Przywracanie stanu systemu

Jeśli poprzednie proces stosowania stan systemu nie zakończy się pomyślnie, użyj środowiska odzyskiwania systemu Windows (Windows RE) do odzyskania serwera systemu Windows. Poniższych krokach opisano sposób odzyskiwania przy użyciu środowiska Windows RE. Użyj tej opcji tylko wtedy, gdy system Windows Server nie normalny rozruch po przywróceniu stanu systemu. Następujący proces powoduje usunięcie danych z systemem innym niż system, ostrożność. 

1. Przeprowadź rozruch serwera z systemem Windows w środowisku odzyskiwania systemu Windows (Windows RE).

2. Wybierz Rozwiązywanie problemów z trzech dostępnych opcji.

    ![Otwieranie menu](./media/backup-azure-restore-system-state/winre-1.png)

3. Z **zaawansowane opcje** ekranu wybierz **wiersza polecenia** i nazwa użytkownika administratora serwera i podaj hasło.

   ![Otwieranie menu](./media/backup-azure-restore-system-state/winre-2.png)

4. Podaj nazwę użytkownika administratora serwera i hasło.

    ![Otwieranie menu](./media/backup-azure-restore-system-state/winre-3.png)

5. Po otwarciu wiersza polecenia w trybie administratora, uruchom następujące polecenie, aby uzyskać kopii zapasowych stanu systemu.

    ```
    Wbadmin get versions -backuptarget:<Volume where WindowsImageBackup folder is copied>:
    ```
    ![Pobieranie wersji kopii zapasowej stanu systemu](./media/backup-azure-restore-system-state/winre-4.png)

6. Uruchom następujące polecenie, aby uzyskać wszystkie woluminy dostępne w kopii zapasowej.

    ```
    Wbadmin get items -version:<copy version from above step> -backuptarget:<Backup volume>
    ```

    ![Pobieranie wersji kopii zapasowej stanu systemu](./media/backup-azure-restore-system-state/winre-5.png)

7. Polecenie przywraca wszystkie woluminy, które są częścią kopii zapasowej stanu systemu. Należy pamiętać, że ten krok pozwala odzyskać tylko woluminy krytyczne, które są częścią stanu systemu. Wszystkie dane z systemem innym niż System jest usunięte.

    ```
    Wbadmin start recovery -items:C: -itemtype:Volume -version:<Backupversion> -backuptarget:<backup target volume>
    ```
     ![Pobieranie wersji kopii zapasowej stanu systemu](./media/backup-azure-restore-system-state/winre-6.png)



## <a name="next-steps"></a>Kolejne kroki
* Teraz, gdy zostały odzyskane pliki i foldery, możesz [Zarządzanie kopii zapasowych](backup-azure-manage-windows-server.md).

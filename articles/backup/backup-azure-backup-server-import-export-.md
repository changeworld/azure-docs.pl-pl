---
title: Kopia zapasowa offline dla programu DPM i usługi Azure Backup Server
description: Za pomocą usługi Azure Backup można wysyłać dane poza siecią przy użyciu usługi Azure Import/Export. W tym artykule opisano przepływ pracy tworzenia kopii zapasowych w trybie offline dla programu DPM i serwera kopii zapasowych platformy Azure.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 080b0bc53b2058bd186e90f354b8f5bcda510414
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197080"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server"></a>Przepływ pracy tworzenia kopii zapasowych w trybie offline dla programu DPM i serwera kopii zapasowych platformy Azure

Usługa Azure Backup ma kilka wbudowanych wydajności, które pozwalają zaoszczędzić koszty sieci i magazynu podczas początkowych pełnych kopii zapasowych danych na platformie Azure. Początkowe pełne kopie zapasowe zazwyczaj przesyłają duże ilości danych i wymagają większej przepustowości sieci w porównaniu do kolejnych kopii zapasowych, które przesyłają tylko różnice/przyrosty. Usługa Azure Backup kompresuje początkowe kopie zapasowe. W procesie rozmieszczania w trybie offline usługa Azure Backup może używać dysków do przekazywania skompresowanych danych początkowej kopii zapasowej w trybie offline na platformę Azure.

Proces rozmieszczania usługi Azure Backup w trybie offline jest ściśle zintegrowany z [usługą Azure Import/Export.](../storage/common/storage-import-export-service.md) Tej usługi można użyć do przesyłania danych na platformę Azure przy użyciu dysków. Jeśli masz terabajty (TBs) początkowych danych kopii zapasowej, które muszą być przesyłane za pośrednictwem sieci o dużym opóźnieniu i niskiej przepustowości, można użyć przepływu pracy rozmieszczania w trybie offline, aby wysłać początkową kopię zapasową na jednym lub kilku dyskach twardych do centrum danych platformy Azure. Ten artykuł zawiera omówienie i dalsze kroki, które kończą ten przepływ pracy dla Menedżera ochrony danych (System Center Data Protection Manager) i Microsoft Azure Backup Server (MABS).

> [!NOTE]
> Proces tworzenia kopii zapasowej w trybie offline dla agenta usług odzyskiwania platformy Microsoft Azure (MARS) różni się od programów DPM i MABS. Aby uzyskać informacje na temat korzystania z kopii zapasowej w trybie offline z agentem MARS, zobacz [Przepływ pracy tworzenia kopii zapasowych w trybie offline w usłudze Azure Backup](backup-azure-backup-import-export.md). Kopia zapasowa w trybie offline nie jest obsługiwana w przypadku kopii zapasowych stanu systemu wykonywanych przy użyciu agenta usługi Azure Backup Agent.
>

## <a name="overview"></a>Omówienie

Dzięki możliwości rozmieszczania w trybie offline usługi Azure Backup i usługi Azure Import/Export można łatwo przekazać dane w trybie offline na platformę Azure przy użyciu dysków. Proces tworzenia kopii zapasowej w trybie offline obejmuje następujące kroki:

> [!div class="checklist"]
>
> * Dane kopii zapasowej są zapisywane w lokalizacji przejściowej, a nie wysyłane przez sieć.
> * Dane w lokalizacji przemieszczania są następnie zapisywane na co najmniej jednym dyskuch SATA przy użyciu narzędzia *AzureOfflineBackupDiskPrep.*
> * Zadanie importu platformy Azure jest automatycznie tworzone przez narzędzie.
> * Dyski SATA są następnie wysyłane do najbliższego centrum danych platformy Azure.
> * Po zakończeniu przekazywania danych kopii zapasowej na platformę Azure usługa Azure Backup kopiuje dane kopii zapasowej do magazynu kopii zapasowych, a przyrostowe kopie zapasowe są zaplanowane.

## <a name="supported-configurations"></a>Obsługiwane konfiguracje

Kopia zapasowa w trybie offline jest obsługiwana dla wszystkich modeli wdrażania usługi Azure Backup, które tworzą kopie zapasowe danych z lokalnego do chmury firmy Microsoft. Modele te obejmują:

> [!div class="checklist"]
>
> * Tworzenie kopii zapasowych plików i folderów za pomocą agenta MARS lub agenta kopii zapasowej platformy Azure.
> * Tworzenie kopii zapasowych wszystkich obciążeń i plików za pomocą programu DPM.
> * Tworzenie kopii zapasowych wszystkich obciążeń i plików za pomocą programu MABS.

## <a name="prerequisites"></a>Wymagania wstępne

Przed uruchomieniem przepływu pracy kopii zapasowej w trybie offline upewnij się, że spełnione są następujące wymagania wstępne:

* Utworzono [magazyn usług odzyskiwania.](backup-azure-recovery-services-vault-overview.md) Aby go utworzyć, wykonaj czynności opisane w [1.](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Agent tworzenia kopii zapasowych platformy Azure lub mabs lub program DPM został zainstalowany na systemie Windows Server lub na kliencie systemu Windows, w zależności od przypadku, a komputer jest zarejestrowany w magazynie usług odzyskiwania. Upewnij się, że używana jest tylko [najnowsza wersja usługi Azure Backup.](https://go.microsoft.com/fwlink/?linkid=229525)
* [Pobierz plik ustawień publikowania platformy Azure](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) na komputerze, z którego zamierzasz na celu utworzenie kopii zapasowej danych. Subskrypcja, z której pobierana jest usługa publikowania, może różnić się od subskrypcji zawierającej magazyn usług odzyskiwania. Jeśli subskrypcja znajduje się w suwerennych chmurach platformy Azure, użyj następujących łączy, aby pobrać plik ustawień publikowania platformy Azure.

    | Suwerenny region chmury | Łącze do pliku ustawień publikowania na platformie Azure |
    | --- | --- |
    | Stany Zjednoczone | [Link](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | Chiny | [Link](https://portal.azure.cn/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* Konto magazynu platformy Azure z modelem wdrażania usługi Resource Manager został utworzony w ramach subskrypcji, z której pobrano plik ustawień publikowania.

  ![Tworzenie konta magazynu przy tworzeniu Menedżera zasobów](./media/backup-azure-backup-import-export/storage-account-resource-manager.png)

* Zostanie utworzona lokalizacja przejściowa, która może być udziałem sieciowym lub dowolnym dodatkowym dyskiem na komputerze, wewnętrznym lub zewnętrznym, z wystarczającą ilością miejsca na dysku do przechowywania początkowej kopii. Na przykład jeśli chcesz zrobić z powrotem do kopii zapasowej serwer plików o rozmiarze 500 GB, upewnij się, że obszar przemieszczania ma co najmniej 500 GB. (Mniejsza ilość jest używana z powodu kompresji.)
* W przypadku dysków wysyłanych na platformę Azure należy zapewnić użycie tylko 2,5-calowych dysków SSD lub 2,5-calowych lub 3,5-calowych wewnętrznych dysków twardych SATA II/III. Można używać dysków twardych o pojemności do 10 TB. Sprawdź [dokumentację usługi importu/eksportu platformy Azure](../storage/common/storage-import-export-requirements.md#supported-hardware) dla najnowszego zestawu dysków obsługiwanych przez usługę.
* Dyski SATA muszą być podłączone do komputera (nazywanego *kopią komputera),* z którego wykonywana jest kopia danych kopii zapasowej z lokalizacji przejściowej na dyski SATA. Upewnij się, że funkcja BitLocker jest włączona na komputerze kopii.

## <a name="prepare-the-server-for-the-offline-backup-process"></a>Przygotowanie serwera do procesu tworzenia kopii zapasowej w trybie offline

>[!NOTE]
> Jeśli nie można znaleźć wymienionych narzędzi, takich jak *AzureOfflineBackupCertGen.exe*, w instalacji AskAzureBackupTeam@microsoft.com mars agenta, napisz, aby uzyskać do nich dostęp.

* Otwórz wiersz polecenia z podwyższonym poziomem uprawnień na serwerze i uruchom następujące polecenie:

    ```cmd
    AzureOfflineBackupCertGen.exe CreateNewApplication SubscriptionId:<Subs ID>
    ```

    Narzędzie tworzy usługę Azure Offline Backup Active Directory Application, jeśli nie istnieje.

    Jeśli aplikacja już istnieje, ten plik wykonywalny prosi o ręczne przekazanie certyfikatu do aplikacji w dzierżawie. Wykonaj kroki opisane w [tej sekcji,](#manually-upload-an-offline-backup-certificate) aby ręcznie przekazać certyfikat do aplikacji.

* Narzędzie *AzureOfflineBackup.exe* generuje plik *OfflineApplicationParams.xml.* Skopiuj ten plik na serwer za pomocą programu MABS lub DPM.
* Zainstaluj [najnowszego agenta MARS](https://aka.ms/azurebackup_agent) w wystąpieniu programu DPM lub na serwerze usługi Azure Backup.
* Zarejestruj serwer na platformie Azure.
* Uruchom następujące polecenie:

    ```cmd
    AzureOfflineBackupCertGen.exe AddRegistryEntries SubscriptionId:<subscriptionid> xmlfilepath:<path of the OfflineApplicationParams.xml file>  storageaccountname:<storageaccountname configured with Azure Data Box>
    ```

* Poprzednie polecenie tworzy `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch\MicrosoftBackupProvider\OfflineApplicationParams_<Storageaccountname>.xml`plik .

## <a name="manually-upload-an-offline-backup-certificate"></a>Ręczne przekazywanie certyfikatu kopii zapasowej w trybie offline

Wykonaj następujące kroki, aby ręcznie przekazać certyfikat kopii zapasowej w trybie offline do wcześniej utworzonej aplikacji usługi Azure Active Directory przeznaczonej do wykonywania kopii zapasowych w trybie offline.

1. Zaloguj się do Portalu Azure.
1. Przejdź do rejestracji**aplikacji** **usługi Azure Active Directory** > .
1. Na karcie **Posiadane aplikacje** znajdź aplikację `AzureOfflineBackup _<Azure User Id`o formacie nazwy wyświetlanej .

    ![Zlokalizuj aplikację na karcie Posiadane aplikacje](./media/backup-azure-backup-import-export/owned-applications.png)

1. Wybierz aplikację. W obszarze **Zarządzanie** w lewym okienku przejdź do **pozycję Certyfikaty & wpisy tajne**.
1. Sprawdź, czy nie ma istniejących certyfikatów lub kluczy publicznych. Jeśli ich nie ma, można bezpiecznie usunąć aplikację, wybierając przycisk **Usuń** na stronie **Przegląd** aplikacji. Następnie można ponowić próbę przygotowania serwera do procesu [tworzenia kopii zapasowej w trybie offline](#prepare-the-server-for-the-offline-backup-process) i pominąć następujące kroki. W przeciwnym razie wykonaj następujące kroki z wystąpienia programu DPM lub serwera usługi Azure Backup, na którym chcesz skonfigurować kopię zapasową w trybie offline.
1. Wybierz kartę **Zarządzanie aplikacją** > certyfikatu**komputera.** Poszukaj certyfikatu o nazwie `CB_AzureADCertforOfflineSeeding_<ResourceId>`.
1. Zaznacz certyfikat, kliknij prawym przyciskiem myszy **pozycję Wszystkie zadania,** a następnie wybierz polecenie **Eksportuj**bez klucza prywatnego w formacie cer.
1. Przejdź do aplikacji do tworzenia kopii zapasowych w trybie offline platformy Azure w witrynie Azure portal.
1. Wybierz **pozycję Zarządzaj certyfikatami** > **& wpisami tajnymi** > **Przekaż certyfikat**. Przekaż certyfikat wyeksportowany w poprzednim kroku.

    ![Przekazywanie certyfikatu](./media/backup-azure-backup-import-export/upload-certificate.png)

1. Na serwerze otwórz rejestr, wprowadzając **regedit** w oknie uruchomienia.
1. Przejdź do wpisu rejestru *Komputer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider*.
1. Kliknij prawym przyciskiem myszy **pozycję CloudBackupProvider**i `AzureADAppCertThumbprint_<Azure User Id>`dodaj nową wartość ciągu o nazwie .

    >[!NOTE]
    > Aby znaleźć identyfikator użytkownika platformy Azure, wykonaj jedną z następujących czynności:
    >
    >* Z programu PowerShell połączonej `Get-AzureRmADUser -UserPrincipalName “Account Holder’s email as appears in the portal”` z platformą Azure uruchom polecenie.
    >* Przejdź do ścieżki `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup; Name: CurrentUserId;`rejestru .

1. Kliknij prawym przyciskiem myszy ciąg dodany w poprzednim kroku, a następnie wybierz polecenie **Modyfikuj**. W wartości podaj odcisk palca certyfikatu wyeksportowanego w kroku 7. Następnie wybierz przycisk **OK**.
1. Aby uzyskać wartość odcisku palca, kliknij dwukrotnie certyfikat. Wybierz kartę **Szczegóły** i przewiń w dół, aż zobaczysz pole odcisku palca. Wybierz **opcję Odcisk palca**i skopiuj wartość.

    ![Kopiuj wartość z pola odcisku palca](./media/backup-azure-backup-import-export/thumbprint-field.png)

1. Przejdź do sekcji [Przepływ pracy,](#workflow) aby przejść do procesu tworzenia kopii zapasowej w trybie offline.

## <a name="workflow"></a>Przepływ pracy

Informacje zawarte w tej sekcji ułatwia zakończenie przepływu pracy tworzenia kopii zapasowych w trybie offline, dzięki czemu dane mogą być dostarczane do centrum danych platformy Azure i przekazywane do usługi Azure Storage. Jeśli masz pytania dotyczące usługi importowania lub dowolnego aspektu procesu, zobacz [dokumentację omówienia usługi importu,](../storage/common/storage-import-export-service.md) do której odwołuje się wcześniej.

### <a name="initiate-offline-backup"></a>Inicjowanie kopii zapasowej w trybie offline

1. Po zaplanowaniu kopii zapasowej zostanie wyświetlona następująca strona w systemie Windows Server, kliencie systemu Windows lub programie DPM.

    ![Strona importu](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    Oto odpowiednia strona w programie DPM. <br/>
    
    ![Strona importu programu DPM i usługi Azure Backup Server](./media/backup-azure-backup-import-export/dpmoffline.png)

    Wypełniane pola to:

   * **Lokalizacja tymczasowa:** Tymczasowa lokalizacja magazynu, do której zapisywana jest początkowa kopia zapasowa. Lokalizacja przemieszczania może znajdować się na udziale sieciowym lub komputerze lokalnym. Jeśli komputer kopiowania i komputer źródłowy są różne, określ pełną ścieżkę sieciową lokalizacji przejściowej.
   * **Ustawienia publikowania platformy Azure:** ścieżka lokalna do pliku ustawień publikowania.
   * **Nazwa zadania importu platformy Azure:** unikatowa nazwa, za pomocą której usługa Azure Import/Export i usługa Azure Backup śledzą transfer danych wysyłanych na dyski na platformę Azure.
   * **Identyfikator subskrypcji platformy Azure:** identyfikator subskrypcji platformy Azure dla subskrypcji, z której pobrano plik ustawień publikowania platformy Azure.
   * **Konto usługi Azure Storage:** Nazwa konta magazynu w subskrypcji platformy Azure skojarzonej z plikiem ustawień publikowania platformy Azure.
   * **Kontener usługi Azure Storage:** Nazwa obiektu blob magazynu docelowego na koncie magazynu platformy Azure, w którym są importowane dane kopii zapasowej.

   Zapisz **lokalizację tymczasową** i podane informacje o **nazwie zadania importu platformy Azure.** Jest to wymagane do przygotowania dysków.

1. Zakończ przepływ pracy. Aby zainicjować kopię zapasową w trybie offline, wybierz pozycję **Utwórz kopię zapasową teraz** na konsoli zarządzania agentem usługi Azure Backup Agent. Początkowa kopia zapasowa jest zapisywana w obszarze przemieszczania w ramach tego kroku.

    ![Utwórz kopię zapasową teraz](./media/backup-azure-backup-import-export/backupnow.png)

    Aby zakończyć odpowiedni przepływ pracy w programie DPM lub usłudze Azure Backup Server, kliknij prawym przyciskiem myszy **grupę ochrony**. Wybierz opcję **Utwórz punkt odzyskiwania.** Następnie wybierz opcję **Ochrona online.**

    ![DPM i MABS wrócę teraz](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    Po zakończeniu operacji lokalizacja przemieszczania jest gotowa do użycia do przygotowania dysku.

    ![Postęp tworzenia kopii zapasowej](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>Przygotowywanie dysków SATA i wysyłka na platformę Azure

Narzędzie *AzureOfflineBackupDiskPrep* służy do przygotowania dysków SATA, które są wysyłane do najbliższego centrum danych platformy Azure. To narzędzie jest dostępne w katalogu instalacyjnym Agenta usług odzyskiwania w następującej ścieżce:

`*\\Microsoft Azure Recovery Services Agent\Utils\*`

1. Przejdź do katalogu i skopiuj katalog *AzureOfflineBackupDiskPrep* na komputer kopii, na którym są połączone dyski SATA, na którym mają być przygotowane dyski. Upewnij się, że:

   * Komputer kopii może uzyskać dostęp do lokalizacji tymczasowej dla przepływu pracy rozmieszczania w trybie offline przy użyciu tej samej ścieżki sieciowej, która została udostępniona w przepływie pracy w sekcji "Inicjuj kopię zapasową w trybie offline".
   * Funkcja BitLocker jest włączona na komputerze kopii.
   * Komputer kopii może uzyskać dostęp do witryny Azure portal. W razie potrzeby komputer kopii może być taki sam jak komputer źródłowy.

     > [!IMPORTANT]
     > Jeśli komputer źródłowy jest maszyną wirtualną, należy użyć innego serwera fizycznego lub komputera klienckiego jako komputera kopii.

1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień na komputerze kopii z katalogiem narzędzi *AzureOfflineBackupDiskPrep* jako bieżącym katalogiem. Uruchom następujące polecenie:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | Parametr | Opis |
    | --- | --- |
    | s:&lt;*Ścieżka lokalizacji tymczasowej*&gt; |To obowiązkowe dane wejściowe są używane do zapewnienia ścieżki do lokalizacji przejściowej wprowadzonej w przepływie pracy w sekcji "Inicjuj kopię zapasową w trybie offline". |
    | p:&lt;*Ścieżka do PublishSettingsFile*&gt; |To opcjonalne dane wejściowe jest używany do zapewnienia ścieżki do pliku ustawień publikowania platformy Azure, który został wprowadzony w przepływie pracy w sekcji "Inicjuj kopię zapasową w trybie offline". |

    > [!NOTE]
    > Ścieżka &lt;do usługi AzurePublishSettingFile&gt; wartość jest obowiązkowe, gdy komputer kopii i komputer źródłowy są różne.
    >
    >

    Po uruchomieniu polecenia narzędzie żąda wyboru zadania importu platformy Azure, które odpowiada dyskom, które muszą być przygotowane. Jeśli tylko jedno zadanie importu jest skojarzone z podanej lokalizacji tymczasowej, zostanie wyświetlona strona taka jak ta.

    ![Wprowadzanie narzędzia przygotowania dysku platformy Azure](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>

1. Wprowadź literę dysku bez końcowego dwukropka dla zainstalowanego dysku, który chcesz przygotować do przeniesienia na platformę Azure. Po wyświetleniu monitu podaj potwierdzenie formatowania dysku.

    Następnie narzędzie rozpoczyna przygotowywanie dysku i kopiowanie danych kopii zapasowej. Może być konieczne dołączenie dodatkowych dysków po wyświetleniu monitu przez narzędzie w przypadku, gdy podany dysk nie ma wystarczającej ilości miejsca na dane kopii zapasowej. <br/>

    Po pomyślnym zakończeniu narzędzia jeden lub więcej dysków, które zostały dostarczone są przygotowane do wysyłki na platformę Azure. Zadanie importu o nazwie podanej podczas przepływu pracy w sekcji "Inicjuj kopię zapasową w trybie offline" jest również tworzone na platformie Azure. Na koniec narzędzie wyświetla adres wysyłki do centrum danych platformy Azure, gdzie dyski muszą zostać wysłane.

    ![Przygotowanie dysku platformy Azure zakończone](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>

1. Po zakończeniu wykonywania polecenia zostanie również wyświetlenie opcji aktualizacji informacji o wysyłce.

    ![Opcja aktualizowania informacji o wysyłce](./media/backup-azure-backup-import-export/updateshippingutility.png)<br/>

1. Możesz wprowadzić szczegóły od razu. Narzędzie prowadzi użytkownika przez proces obejmujący szereg danych wejściowych. Jeśli nie masz informacji, takich jak numer śledzenia lub inne szczegóły związane z wysyłką, możesz zakończyć sesję. Kroki, aby zaktualizować szczegóły wysyłki później znajdują się w tym artykule.

1. Wyślij dyski na adres podany przez narzędzie. Zachowaj numer śledzenia do wykorzystania w przyszłości.

   > [!IMPORTANT]
   > Żadne dwa zadania importu platformy Azure nie mogą mieć tego samego numeru śledzenia. Upewnij się, że dyski przygotowane przez narzędzie w ramach jednego zadania importu platformy Azure są wysyłane razem w jednym pakiecie i że istnieje jeden unikatowy numer śledzenia dla pakietu. Nie łącz dysków przygotowanych jako część różnych zadań importowania platformy Azure w jednym pakiecie.

1. Jeśli masz informacje o numerze śledzenia, przejdź do komputera źródłowego, który oczekuje na zakończenie zadania importu. Uruchom następujące polecenie w wierszu polecenia z podwyższonym poziomem uprawnień z katalogiem narzędzi *AzureOfflineBackupDiskPrep* jako bieżącym katalogiem.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   Opcjonalnie można uruchomić następujące polecenie z innego komputera, takiego jak komputer kopii, z katalogiem narzędzi *AzureOfflineBackupDiskPrep* jako bieżącym katalogiem.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | Parametr | Opis |
    | --- | --- |
    | U: | To obowiązkowe dane wejściowe służy do aktualizowania szczegółów wysyłki dla zadania importu platformy Azure. |
    | s:&lt;*Ścieżka lokalizacji tymczasowej*&gt; | To obowiązkowe dane wejściowe są używane, gdy polecenie nie jest uruchamiane na komputerze źródłowym. Służy do zapewnienia ścieżki do lokalizacji przemieszczania wprowadzonej w przepływie pracy w sekcji "Inicjuj kopię zapasową w trybie offline". |
    | p:&lt;*Ścieżka do PublishSettingsFile*&gt; | To obowiązkowe dane wejściowe są używane, gdy polecenie nie jest uruchamiane na komputerze źródłowym. Służy do zapewnienia ścieżki do pliku ustawień publikowania platformy Azure, który został wprowadzony w przepływie pracy w sekcji "Inicjowanie kopii zapasowej w trybie offline". |

    Narzędzie automatycznie wykrywa zadanie importu, na które oczekuje komputer źródłowy, lub zadania importu skojarzone z lokalizacją przejściową, gdy polecenie jest uruchamiane na innym komputerze. Następnie udostępnia możliwość aktualizacji informacji o wysyłce za pomocą szeregu danych wejściowych.

    ![Wprowadź informacje o wysyłce](./media/backup-azure-backup-import-export/shippinginputs.png)<br/>

1. Po potwierdzeniu wszystkich danych wejściowych należy dokładnie przejrzeć szczegóły i zatwierdzić informacje o wysyłce, które podałeś, wprowadzając **tak**.

    ![Przejrzyj informacje o wysył](./media/backup-azure-backup-import-export/reviewshippinginformation.png)<br/>

1. Po pomyślnym zaktualizowaniu informacji o wysyłce narzędzie udostępnia lokalną lokalizację, w której przechowywane są wprowadzone dane wysyłki.

    ![Przechowuj informacje o wysyłce](./media/backup-azure-backup-import-export/storingshippinginformation.png)<br/>

   > [!IMPORTANT]
   > Upewnij się, że dyski dotrzeć do centrum danych platformy Azure w ciągu dwóch tygodni od dostarczenia informacji o wysyłce przy użyciu *narzędzia AzureOfflineBackupDiskPrep.* Niezastosowanie się do tego może spowodować, że dyski nie są przetwarzane. 

Po zakończeniu poprzednich kroków centrum danych platformy Azure jest gotowe do odbierania dysków i dalszego przetwarzania ich w celu przeniesienia danych kopii zapasowej z dysków na utworzone konto usługi Azure storage typu klasycznego.

### <a name="time-to-process-the-drives"></a>Czas na przetworzenie dysków

Czas potrzebny do przetworzenia zadania importu platformy Azure jest różny. Czas procesu zależy od czynników, takich jak czas wysyłki, typ zadania, typ i rozmiar kopiowanych danych oraz rozmiaru dostarczonych dysków. Usługa Importu/Eksportu platformy Azure nie ma umowy SLA. Po odebraniu dysków usługa stara się zakończyć kopiowanie kopii zapasowej danych na konto magazynu platformy Azure w ciągu 7 do 10 dni. W następnej sekcji opisano, jak można monitorować stan zadania importowania platformy Azure.

### <a name="monitor-azure-import-job-status"></a>Monitorowanie stanu zadania importu platformy Azure

Gdy dyski są w tranzycie lub w centrum danych platformy Azure do skopiowania do konta magazynu, agenta kopii zapasowej platformy Azure lub programu DPM lub konsoli MABS na komputerze źródłowym pokazuje następujący stan zadania dla zaplanowanych kopii zapasowych:

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

Aby sprawdzić stan zadania importu:

1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień na komputerze źródłowym i uruchom następujące polecenie:

     `AzureOfflineBackupDiskPrep.exe u:`

1. Dane wyjściowe pokazują bieżący stan zadania importu.

    ![Sprawdź stan zadania importu](./media/backup-azure-backup-import-export/importjobstatusreporting.png)<br/>

Aby uzyskać więcej informacji na temat różnych stanów zadania importowania platformy Azure, zobacz [Wyświetlanie stanu zadań importowania/eksportowania platformy Azure](../storage/common/storage-import-export-view-drive-status.md).

### <a name="finish-the-workflow"></a>Zakończenie przepływu pracy

Po zakończeniu zadania importowania początkowe dane kopii zapasowej są dostępne na koncie magazynu. W czasie następnej zaplanowanej kopii zapasowej usługa Azure Backup kopiuje zawartość danych z konta magazynu do magazynu usług odzyskiwania.

   ![Kopiowanie danych do magazynu usług odzyskiwania](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

W czasie następnej zaplanowanej kopii zapasowej usługa Azure Backup wykonuje przyrostową kopię zapasową za pomocą początkowej kopii zapasowej.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać jakiekolwiek pytania dotyczące przepływu pracy usługi Import/Eksportowanie platformy Azure, zobacz [Przekazywanie danych do magazynu obiektów Blob za pomocą usługi Importowanie/Eksportowanie platformy Microsoft Azure.](../storage/common/storage-import-export-service.md)

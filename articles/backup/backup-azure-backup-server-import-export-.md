---
title: Kopia zapasowa offline dla programu DPM i usługi Azure Backup Server
description: Azure Backup umożliwia wysyłanie danych z sieci za pomocą usługi Azure Import/Export. W tym artykule wyjaśniono przepływ pracy kopii zapasowej offline dla programu DPM i Azure Backup Server (serwera usługi MAB).
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 6be75062ab0ce06784d8cd7c833e0070476acf60
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022583"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server"></a>Tryb offline — przepływ pracy tworzenia kopii zapasowych dla programu DPM i Azure Backup Server

Azure Backup ma kilka wbudowanych wydajności, które oszczędzają koszty związane z siecią i magazynem podczas wstępnej pełnej kopii zapasowej danych na platformie Azure. Wstępne pełne kopie zapasowe zwykle przesyłają duże ilości danych i wymagają większej przepustowości sieci w porównaniu z kolejnymi kopiami zapasowymi, które przesyłają tylko różnice/przyrosty. Azure Backup kompresuje początkowe kopie zapasowe. W procesie umieszczania w trybie offline, Azure Backup mogą używać dysków do przekazywania skompresowanych danych początkowej kopii zapasowej w trybie offline do platformy Azure.

Proces rozsadzenia w trybie offline Azure Backup jest ściśle zintegrowany z [usługą Azure Import/Export](../storage/common/storage-import-export-service.md) , która umożliwia transfer danych na platformę Azure przy użyciu dysków. Jeśli masz terabajty (TBs) początkowych danych kopii zapasowej, które muszą być transferowane za pośrednictwem sieci o dużej opóźnieniu i o niskiej przepustowości, możesz użyć przepływu pracy w trybie offline, aby wysłać początkową kopię zapasową na co najmniej jednym dysku twardym do centrum danych platformy Azure. Ten artykuł zawiera omówienie i dalsze szczegóły dotyczące kroków, które uzupełniają ten przepływ pracy dla programu System Center DPM i Azure Backup Server.

> [!NOTE]
> Proces tworzenia kopii zapasowych w trybie offline dla agenta Microsoft Azure Recovery Services (MARS) różni się od programu System Center DPM i Azure Backup Server. Aby uzyskać informacje na temat korzystania z kopii zapasowej offline z agentem MARS, zobacz [ten artykuł](backup-azure-backup-import-export.md). Kopia zapasowa offline nie jest obsługiwana dla kopii zapasowych stanu systemu wykonywanych przy użyciu agenta Azure Backup.
>

## <a name="overview"></a>Przegląd

Korzystając z możliwości wypełniania w trybie offline Azure Backup i usługi Azure Import/Export, można łatwo przekazać dane w trybie offline na platformę Azure przy użyciu dysków. Proces tworzenia kopii zapasowej w trybie offline obejmuje następujące kroki:

> [!div class="checklist"]
>
> * Dane kopii zapasowej, które nie są wysyłane przez sieć, są zapisywane w *lokalizacji tymczasowej*
> * Dane w *lokalizacji tymczasowej* są następnie zapisywane na jednym lub większej liczbie dysków SATA przy użyciu narzędzia *AzureOfflineBackupDiskPrep*
> * Zadanie importowania platformy Azure jest tworzone automatycznie przez narzędzie
> * Dyski SATA są następnie wysyłane do najbliższego centrum danych platformy Azure
> * Po zakończeniu przekazywania danych kopii zapasowej do platformy Azure Azure Backup kopiuje dane kopii zapasowej do magazynu kopii zapasowych, a przyrostowe kopie zapasowe są zaplanowane.

## <a name="supported-configurations"></a>Obsługiwane konfiguracje

Kopia zapasowa offline jest obsługiwana we wszystkich modelach wdrażania Azure Backup, w których dane kopii zapasowej pochodzą z lokalizacji lokalnej do Microsoft Cloud. Obejmuje to

> [!div class="checklist"]
>
> * Tworzenie kopii zapasowych plików i folderów za pomocą agenta Microsoft Azure Recovery Services (MARS) lub agenta Azure Backup.
> * Tworzenie kopii zapasowych wszystkich obciążeń i plików za pomocą programu System Center Data Protection Manager (SC DPM)
> * Tworzenie kopii zapasowych wszystkich obciążeń i plików z Microsoft Azure Backup Server

## <a name="prerequisites"></a>Wymagania wstępne

Przed zainicjowaniem przepływu pracy tworzenia kopii zapasowej offline upewnij się, że zostały spełnione następujące wymagania wstępne

* Utworzono [magazyn Recovery Services](backup-azure-recovery-services-vault-overview.md) . Aby go utworzyć, zapoznaj się z instrukcjami w [tym artykule](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault) .
* Agent Azure Backup lub Azure Backup Server lub SC DPM został zainstalowany na komputerze z systemem Windows Server/kliencie systemu Windows, w zależności od tego, czy komputer jest zarejestrowany w magazynie Recovery Services. Upewnij się, że jest używana tylko [Najnowsza wersja Azure Backup](https://go.microsoft.com/fwlink/?linkid=229525) .
* [Pobierz plik ustawień publikowania platformy Azure](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) na komputerze, z którego planujesz utworzyć kopię zapasową danych. Subskrypcja, z której jest pobierany plik ustawień publikowania, może się różnić od subskrypcji zawierającej magazyn Recovery Services. Jeśli Twoja subskrypcja znajduje się w ramach suwerennych chmur platformy Azure, użyj następujących linków w celu pobrania pliku ustawień publikowania platformy Azure.

    | Region suwerennej chmury | Link pliku ustawień publikowania platformy Azure |
    | --- | --- |
    | Stany Zjednoczone | [Link](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | Chiny | [Link](https://portal.azure.cn/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* Konto usługi Azure Storage z modelem wdrażania *Menedżer zasobów* zostało utworzone w ramach subskrypcji, z poziomu której pobrano plik ustawień publikowania, jak pokazano poniżej:

  ![Tworzenie konta magazynu przy użyciu programu Menedżer zasobów Development](./media/backup-azure-backup-import-export/storage-account-resource-manager.png)

* Tworzona jest lokalizacja tymczasowa, która może być udziałem sieciowym lub dodatkowym dyskiem na komputerze, wewnętrznym lub zewnętrznym, z wystarczającą ilością miejsca na dysku do przechowywania kopii początkowej. Na przykład, jeśli próbujesz utworzyć kopię zapasową serwera plików 500-GB, upewnij się, że obszar przejściowy jest co najmniej 500 GB. (Mniejsza ilość jest używana z powodu kompresji).
* W odniesieniu do dysków, które będą wysyłane do platformy Azure, należy się upewnić, że używane są tylko dyski SSD 2,5 cala lub 2,5 cala lub 3,5 cala SATA II/III. Możesz użyć dysków twardych o pojemności do 10 TB. Zapoznaj się z [dokumentacją usługi Azure Import/Export](../storage/common/storage-import-export-requirements.md#supported-hardware) , aby zapoznać się z najnowszym zestawem dysków obsługiwanych przez usługę.
* Dyski SATA muszą być połączone z komputerem (nazywanym *komputerem kopiującym*), z którego są wykonywane kopie kopii zapasowych z *lokalizacji tymczasowej* do dysków SATA. Upewnij się, że funkcja BitLocker jest włączona na *komputerze kopiowania*

## <a name="prepare-the-server-for-the-offline-backup-process"></a>Przygotowywanie serwera do procesu tworzenia kopii zapasowej w trybie offline

>[!NOTE]
> Jeśli nie możesz znaleźć wymienionych narzędzi, takich jak *AzureOfflineBackupCertGen. exe* , w instalacji agenta Mars, zapisz do AskAzureBackupTeam@microsoft.com, aby uzyskać do nich dostęp.

* Otwórz wiersz polecenia z podwyższonym poziomem uprawnień na serwerze i uruchom następujące polecenie:

    ```cmd
    AzureOfflineBackupCertGen.exe CreateNewApplication SubscriptionId:<Subs ID>
    ```

    Narzędzie utworzy aplikację usługi AD w trybie offline platformy Azure, jeśli taka nie istnieje.

    Jeśli aplikacja już istnieje, ten plik wykonywalny poproszony zostanie o ręczne przekazanie certyfikatu do aplikacji w dzierżawie. Wykonaj kroki opisane poniżej w [tej sekcji](#manually-upload-offline-backup-certificate) , aby ręcznie przekazać certyfikat do aplikacji.

* Narzędzie AzureOfflineBackup. exe wygeneruje plik OfflineApplicationParams. XML.  Skopiuj ten plik na serwer z programem serwera usługi MAB lub DPM.
* Zainstaluj [najnowszego agenta Mars](https://aka.ms/azurebackup_agent) na serwerze DPM/Azure Backup (serwera usługi MAB).
* Zarejestruj serwer na platformie Azure.
* Uruchom następujące polecenie:

    ```cmd
    AzureOfflineBackupCertGen.exe AddRegistryEntries SubscriptionId:<subscriptionid> xmlfilepath:<path of the OfflineApplicationParams.xml file>  storageaccountname:<storageaccountname configured with Azure Data Box>
    ```

* Powyższe polecenie spowoduje utworzenie pliku `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch\MicrosoftBackupProvider\OfflineApplicationParams_<Storageaccountname>.xml`

## <a name="manually-upload-offline-backup-certificate"></a>Ręcznie Przekaż certyfikat kopii zapasowej offline

Wykonaj poniższe kroki, aby ręcznie przekazać certyfikat kopii zapasowej offline do wcześniej utworzonej aplikacji Azure Active Directory przeznaczonej do tworzenia kopii zapasowych w trybie offline.

1. Zaloguj się do Portalu Azure.
2. Przejdź do **Azure Active Directory** > **rejestracje aplikacji**
3. Przejdź do karty **posiadane aplikacje** i Znajdź aplikację z formatem nazwy wyświetlanej `AzureOfflineBackup _<Azure User Id` jak pokazano poniżej:

    ![Znajdź aplikację na karcie aplikacje należące do firmy](./media/backup-azure-backup-import-export/owned-applications.png)

4. Kliknij aplikację. Na karcie **Zarządzanie** w okienku po lewej stronie przejdź do pozycji **Certyfikaty & wpisy tajne**.
5. Sprawdź, czy istnieją już istniejące certyfikaty lub klucze publiczne. Jeśli nie istnieje, możesz bezpiecznie usunąć aplikację, klikając przycisk **Usuń** na stronie **Przegląd** aplikacji. Po wykonaniu tych czynności można ponowić próbę [przygotowania serwera do procesu tworzenia kopii zapasowej w trybie offline](#prepare-the-server-for-the-offline-backup-process) i pominąć poniższe kroki. W przeciwnym razie wykonaj następujące kroki na serwerze DPM/Azure Backup Server (serwera usługi MAB), na którym chcesz skonfigurować kopie zapasowe w trybie offline.
6. Otwórz kartę **Zarządzanie aplikacjami certyfikatów komputerów** > **osobista** i poszukaj certyfikatu z nazwą `CB_AzureADCertforOfflineSeeding_<ResourceId>`
7. Wybierz certyfikat powyżej, kliknij prawym przyciskiem myszy **wszystkie zadania** , a następnie **wyeksportuj**, bez klucza prywatnego, w formacie CER.
8. Przejdź do aplikacji Azure offline Backup w Azure Portal.
9. Kliknij pozycję **Zarządzaj** certyfikatami >  **& wpisy tajne** > **Przekaż certyfikat**i przekaż certyfikat wyeksportowany w poprzednim kroku.

    ![Przekaż certyfikat](./media/backup-azure-backup-import-export/upload-certificate.png)
10. Na serwerze otwórz rejestr, wpisując **regedit** w oknie uruchamiania.
11. Przejdź do wpisu rejestru *Computer \ HKEY_LOCAL_MACHINE \Software\microsoft\windows Azure Backup\Config\CloudBackupProvider*.
12. Kliknij prawym przyciskiem myszy pozycję **CloudBackupProvider** i Dodaj nową wartość ciągu o nazwie `AzureADAppCertThumbprint_<Azure User Id>`

    >[!NOTE]
    > Uwaga: aby znaleźć identyfikator użytkownika platformy Azure, wykonaj jedną z następujących czynności:
    >
    >1. Na platformie Azure połączonej z programem PowerShell uruchom polecenie `Get-AzureRmADUser -UserPrincipalName “Account Holder’s email as appears in the portal”`.
    >2. Przejdź do ścieżki rejestru: `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup; Name: CurrentUserId;`

13. Kliknij prawym przyciskiem myszy ciąg dodany w poprzednim kroku i wybierz polecenie **Modyfikuj**. W polu wartość Podaj odcisk palca certyfikatu wyeksportowanego w kroku 7, a następnie kliknij przycisk **OK**.
14. Aby uzyskać wartość odcisku palca, kliknij dwukrotnie certyfikat, a następnie wybierz kartę **szczegóły** i przewiń w dół do momentu wyświetlenia pola odcisk palca. Kliknij pozycję **odcisk palca** i skopiuj wartość.

    ![Kopiuj wartość z pola odcisku palca](./media/backup-azure-backup-import-export/thumbprint-field.png)

15. Przejdź do sekcji [przepływ pracy](#workflow) , aby kontynuować proces tworzenia kopii zapasowej offline.

## <a name="workflow"></a>Przepływ pracy

Informacje przedstawione w tej sekcji ułatwiają zakończenie przepływu pracy w trybie offline, aby dane mogły zostać dostarczone do centrum danych platformy Azure i przekazane do usługi Azure Storage. Jeśli masz pytania dotyczące usługi import lub dowolnego aspektu procesu, zapoznaj się z dokumentacją dotyczącą [przeglądu usługi Import Service](../storage/common/storage-import-export-service.md) .

### <a name="initiate-offline-backup"></a>Inicjowanie kopii zapasowej offline

1. Po zaplanowaniu kopii zapasowej zobaczysz następujący ekran (w systemie Windows Server, kliencie Windows lub programie System Center Data Protection Manager).

    ![Ekran importowania](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    Oto odpowiedni ekran w programie System Center Data Protection Manager: <br/>
    ![ekranu SC programu DPM i serwera Azure Backup](./media/backup-azure-backup-import-export/dpmoffline.png)

    Opis danych wejściowych jest następujący:

   * **Lokalizacja tymczasowa**: tymczasowa lokalizacja przechowywania, w której zapisano początkową kopię zapasową. Lokalizacja przejściowa może znajdować się w udziale sieciowym lub na komputerze lokalnym. Jeśli komputer kopii i komputer źródłowy są inne, zalecamy określenie pełnej ścieżki sieciowej lokalizacji tymczasowej.
   * **Nazwa zadania importowania platformy Azure**: unikatowa nazwa, za pomocą której usługa Azure Import i Azure Backup śledzi transfer danych wysyłanych na dyskach na platformę Azure.
   * **Ustawienia publikowania platformy Azure**: Podaj ścieżkę lokalną do pliku ustawień publikowania.
   * **Identyfikator subskrypcji platformy Azure**: Identyfikator subskrypcji platformy Azure dla subskrypcji z lokalizacji, w której pobrano plik ustawień publikowania platformy Azure.
   * **Konto usługi Azure Storage**: nazwa konta magazynu w subskrypcji platformy Azure skojarzona z plikiem ustawień publikowania platformy Azure.
   * **Kontener usługi Azure Storage**: Nazwa docelowego obiektu blob magazynu na koncie usługi Azure Storage, do którego są importowane dane kopii zapasowej.

     Zapisz *lokalizację przemieszczania* i *nazwę zadania importowania platformy Azure* podane jako wymagane do przygotowania dysków.  

2. Ukończ przepływ pracy i zainicjuj kopię zapasową offline, a następnie kliknij pozycję **Wykonaj kopię** zapasową teraz w konsoli zarządzania agentami Azure Backup. Początkowa kopia zapasowa jest zapisywana w obszarze przejściowym w ramach tego kroku.

    ![Utwórz kopię zapasową teraz](./media/backup-azure-backup-import-export/backupnow.png)

    Aby zakończyć odpowiedni przepływ pracy w programie System Center Data Protection Manager lub Azure Backup Server, kliknij prawym przyciskiem myszy **grupę ochrony**, a następnie wybierz opcję **Utwórz punkt odzyskiwania** . Następnie wybierz opcję **ochrony w trybie online** .

    ![Tworzenie kopii zapasowej w programie DPM i na serwerze Azure Backup](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    Po zakończeniu operacji lokalizacja tymczasowa jest gotowa do użycia podczas przygotowywania dysku.

    ![Postęp tworzenia kopii zapasowej](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>Przygotowanie dysków SATA i dostarczenie do platformy Azure

Narzędzie *AzureOfflineBackupDiskPrep* służy do przygotowywania dysków SATA, które są wysyłane do najbliższego centrum danych platformy Azure. To narzędzie jest dostępne w katalogu instalacyjnym Agenta Recovery Services w następującej ścieżce:

`*\\Microsoft Azure Recovery Services Agent\Utils\*`

1. Przejdź do katalogu i Skopiuj katalog **AzureOfflineBackupDiskPrep** do komputera kopii, na którym są połączone dyski SATA, które mają zostać przygotowane. Upewnij się, że w odniesieniu do komputera kopii:

   * Komputer kopii może uzyskać dostęp do lokalizacji tymczasowej przepływu pracy w trybie offline przy użyciu tej samej ścieżki sieciowej, która została podana w przepływie pracy **inicjowania kopii zapasowej offline** .
   * Funkcja BitLocker jest włączona na komputerze kopiowania.
   * Komputer kopiowania może uzyskać dostęp do Azure Portal.

     W razie potrzeby komputer kopii może być taki sam jak komputer źródłowy.

     > [!IMPORTANT]
     > Jeśli komputer źródłowy jest maszyną wirtualną, należy użyć innego serwera fizycznego lub komputera klienckiego jako komputera kopii.

2. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień na komputerze kopiowania przy użyciu katalogu narzędzi *AzureOfflineBackupDiskPrep* jako bieżący katalog i uruchom następujące polecenie:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | Parametr | Opis |
    | --- | --- |
    | s:&lt;*ścieżka lokalizacji tymczasowej*&gt; |Obowiązkowe dane wejściowe, które są używane do zapewnienia ścieżki do lokalizacji tymczasowej wprowadzonej w przepływie pracy **inicjowania kopii zapasowej w trybie offline** . |
    | p:&lt;*ścieżka do PublishSettingsFile*&gt; |Opcjonalne dane wejściowe używane do zapewnienia ścieżki do pliku **ustawień publikowania platformy Azure** wprowadzonego w przepływie pracy **inicjowania kopii zapasowej offline** . |

    > [!NOTE]
    > Ścieżka &lt;do wartości&gt; AzurePublishSettingFile jest obowiązkowa, gdy komputer kopii i komputer źródłowy są różne.
    >
    >

    Po uruchomieniu polecenia Narzędzie żąda wyboru zadania importowania platformy Azure odpowiadającego dyskom, które muszą zostać przygotowane. Jeśli tylko jedno zadanie importu jest skojarzone z podaną lokalizacją przemieszczania, zobaczysz ekran podobny do poniższego.

    ![Dane wejściowe narzędzia przygotowywania dysku platformy Azure](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>

3. Wprowadź literę dysku bez kropki końcowej dla zainstalowanego dysku, który ma zostać przygotowany do przeniesienia na platformę Azure. Potwierdź formatowanie dysku po wyświetleniu monitu.

    Następnie narzędzie rozpocznie przygotowanie dysku i skopiowanie danych kopii zapasowej. W przypadku, gdy udostępniony dysk nie ma wystarczającej ilości miejsca na dane kopii zapasowej, może być konieczne dołączenie dodatkowych dysków. <br/>

    Po pomyślnym wykonaniu tego narzędzia zostanie przygotowany co najmniej jeden dostarczony dysk do wysyłki na platformę Azure. Ponadto na platformie Azure jest tworzone zadanie importowania o nazwie podanej podczas przepływu pracy **inicjowania kopii zapasowej offline** . Na koniec narzędzie wyświetla adres wysyłkowy centrum danych platformy Azure, w którym należy dostarczyć dyski.

    ![Ukończono przygotowywanie dysku platformy Azure](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>

4. Po zakończeniu wykonywania polecenia zobaczysz również opcję aktualizowania informacji o wysyłce, jak pokazano poniżej:

    ![Aktualizuj opcję informacji o wysyłce](./media/backup-azure-backup-import-export/updateshippingutility.png)<br/>

5. Szczegóły można wprowadzić od razu. Narzędzie przeprowadzi Cię przez proces obejmujący serię danych wejściowych. Jeśli jednak nie masz informacji, takich jak śledzenie numeru lub inne szczegóły związane z wysyłką, możesz zakończyć sesję. Procedurę aktualizowania szczegółów dotyczących wysyłki można znaleźć w dalszej części tego artykułu.

6. Wyślij dyski na adres dostarczony przez narzędzie i Zachowaj numer śledzenia na potrzeby przyszłego odwołania.

   > [!IMPORTANT]
   > Żadne dwa zadania importowania platformy Azure nie mogą mieć tego samego numeru śledzenia. Upewnij się, że dyski przygotowane przez narzędzie w ramach pojedynczego zadania importowania platformy Azure są dostarczane razem w jednym pakiecie i że istnieje jeden unikatowy numer śledzenia dla pakietu. Nie łącz dysków przygotowanych w ramach **różnych** zadań importowania platformy Azure w jednym pakiecie.

7. Jeśli masz informacje o numerze śledzenia, przejdź do komputera źródłowego, który oczekuje na zakończenie zadania importowania, a następnie uruchom następujące polecenie w wierszu polecenia z podwyższonym poziomem uprawnień przy użyciu katalogu narzędzi *AzureOfflineBackupDiskPrep* jako bieżącego katalogu:

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   Opcjonalnie można uruchomić następujące polecenie z innego komputera, takiego jak *komputer kopiujący*, przy użyciu katalogu narzędzi *AzureOfflineBackupDiskPrep* jako bieżącego katalogu:

   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | Parametr | Opis |
    | --- | --- |
    | u: | Obowiązkowe dane wejściowe używane do aktualizowania szczegółów wysyłki dla zadania importowania platformy Azure |
    | s:&lt;*ścieżka lokalizacji tymczasowej*&gt; | Obowiązkowe dane wejściowe, gdy polecenie nie jest uruchamiane na komputerze źródłowym. Służy do zapewnienia ścieżki do lokalizacji tymczasowej wprowadzonej w przepływie pracy **inicjowania kopii zapasowej offline** . |
    | p:&lt;*ścieżka do PublishSettingsFile*&gt; | Obowiązkowe dane wejściowe, gdy polecenie nie jest uruchamiane na komputerze źródłowym. Służy do podania ścieżki do pliku **ustawień publikowania platformy Azure** , który został wprowadzony w przepływie pracy **inicjowania kopii zapasowej offline** . |

    Narzędzie automatycznie wykrywa zadanie importu, które oczekuje na komputer źródłowy, lub zadania importu skojarzone z lokalizacją przejściową, gdy polecenie jest uruchamiane na innym komputerze. Następnie udostępnia opcję aktualizowania informacji o wysyłce przez serię danych wejściowych, jak pokazano poniżej:

    ![Wprowadzanie informacji o wysyłce](./media/backup-azure-backup-import-export/shippinginputs.png)<br/>

8. Po udostępnieniu wszystkich danych wejściowych Przejrzyj szczegóły i zatwierdź podane informacje o wysyłce, wpisując *tak*.

    ![Przejrzyj informacje o wysyłce](./media/backup-azure-backup-import-export/reviewshippinginformation.png)<br/>

9. Po pomyślnym zaktualizowaniu informacji dotyczących wysyłki Narzędzie udostępnia lokalizację lokalną, w której dane dotyczące wysyłki wprowadzone przez użytkownika są przechowywane jak pokazano poniżej

    ![Przechowywanie informacji o wysyłce](./media/backup-azure-backup-import-export/storingshippinginformation.png)<br/>

   > [!IMPORTANT]
   > Upewnij się, że stacje docierają do centrum danych platformy Azure w ciągu dwóch tygodni od udostępnienia informacji o wysyłce przy użyciu narzędzia *AzureOfflineBackupDiskPrep* . Niewykonanie tej czynności może spowodować, że dyski nie zostaną przetworzone.  

Po wykonaniu powyższych kroków centrum danych platformy Azure jest gotowe do odebrania dysków i przetworzenia ich w celu przetransferowania danych kopii zapasowej z dysków do klasycznego typu konta usługi Azure Storage.

### <a name="time-to-process-the-drives"></a>Czas do przetworzenia dysków

Czas przetwarzania zadania importowania platformy Azure zależy od różnych czynników, takich jak czas wysyłki, typ zadania, typ i rozmiar kopiowanych danych oraz rozmiar udostępnionych dysków. Usługa Azure Import/Export nie ma umowy SLA, ale po odebraniu dysków usługa dokłada starań, aby zakończyć kopiowanie danych kopii zapasowej na konto usługi Azure Storage w ciągu 7 do 10 dni. W następnej sekcji szczegółowo opisano, jak można monitorować stan zadania importowania platformy Azure.

### <a name="monitoring-azure-import-job-status"></a>Monitorowanie stanu zadania importowania platformy Azure

Gdy dyski są w trakcie przesyłania lub do centrum danych platformy Azure, które mają zostać skopiowane do konta magazynu, Agent Azure Backup lub SC DPM lub konsola serwera Azure Backup na komputerze źródłowym pokazuje następujący stan zadania dla zaplanowanych kopii zapasowych.

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

Postępuj zgodnie z poniższymi instrukcjami, aby sprawdzić stan zadania importowania.

1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień na komputerze źródłowym i uruchom następujące polecenie:

     `AzureOfflineBackupDiskPrep.exe u:`

2. Dane wyjściowe przedstawiają bieżący stan zadania importu, jak pokazano poniżej:

    ![Sprawdzanie stanu zadania importowania](./media/backup-azure-backup-import-export/importjobstatusreporting.png)<br/>

Aby uzyskać więcej informacji na temat różnych stanów zadania importowania platformy Azure, zobacz [ten artykuł](../storage/common/storage-import-export-view-drive-status.md) .

### <a name="complete-the-workflow"></a>Ukończ przepływ pracy

Po zakończeniu zadania importowania dane początkowej kopii zapasowej są dostępne na koncie magazynu. W czasie następnej zaplanowanej kopii zapasowej usługa Azure Backup kopiuje zawartość danych z konta magazynu do magazynu Recovery Services, jak pokazano poniżej:

   ![Kopiowanie danych do magazynu Recovery Services](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

Podczas kolejnej zaplanowanej kopii zapasowej Program Azure Backup wykonuje przyrostową kopię zapasową za pomocą początkowej kopii w celu utworzenia.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać odpowiedzi na pytania dotyczące przepływu pracy importowania/eksportowania platformy Azure, zapoznaj się z tematem [transfer danych do magazynu obiektów BLOB za pomocą usługi Microsoft Azure Import/Export](../storage/common/storage-import-export-service.md).

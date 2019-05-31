---
title: Usługa Azure Backup — kopia zapasowa Offline dla programu DPM i Azure Backup Server
description: Dowiedz się, jak usługa Azure Backup umożliwia wysyłanie danych z sieci za pomocą usługi Azure Import/Export. W tym artykule opisano, w trybie offline rozmieszczania danych początkowej kopii zapasowej za pomocą usługi Azure Import/Eksport.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 5/8/2018
ms.author: saurse
ms.openlocfilehash: 18f84062bcaf2766ee0abd5248f876c3d8acef3f
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304019"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server"></a>Przepływ pracy w trybie offline z kopii zapasowej programu DPM i serwer usługi Azure Backup
Usługa Azure Backup ma kilka wbudowanych korzyści, które zmniejsza koszty magazynu i sieci podczas początkowego pełne kopie zapasowe danych na platformę Azure. Początkowa pełne kopie zapasowe zazwyczaj przesyłanie dużych ilości danych, a także wymagają większej przepustowości sieci w porównaniu do kolejnych kopii zapasowych, które przenieść tylko różnic/przyrostowa. Usługa Azure Backup są kompresowane wstępne kopie zapasowe. Proces rozmieszczania w trybie offline usługa Azure Backup umożliwia dysków Przekaż skompresowane dane początkowej kopii zapasowej w trybie offline na platformę Azure.

Proces rozmieszczania offline usługi Azure Backup jest ściśle zintegrowany z [usługa Azure Import/Export](../storage/common/storage-import-export-service.md) , pozwala na przesyłanie danych do platformy Azure przy użyciu dysków. Jeśli masz terabajtów (TB) danych początkowej kopii zapasowej, który musi być przesyłane przez wysokich opóźnieniach i małej przepustowości sieci, można użyć przepływu pracy rozmieszczania offline na potrzeby wysłania początkowej kopii zapasowej na jeden lub więcej dysków twardych do centrum danych platformy Azure. Ten artykuł zawiera omówienie i dalsze kroki szczegółowe informacje, które należy wykonać ten przepływ pracy programu System Center DPM i usługi Azure Backup Server.

> [!NOTE]
> Proces kopii zapasowej Offline dla agenta usługi Microsoft Azure Recovery Services (MARS) różni się od programu System Center DPM i serwer usługi Azure Backup. Aby uzyskać informacje na temat korzystania z kopii zapasowej Offline za pomocą agenta usług MARS, zobacz [w tym artykule](backup-azure-backup-import-export.md). Kopia zapasowa offline nie jest obsługiwana dla kopii zapasowych stanu systemu, odbywa się za pomocą agenta usługi Azure Backup.
>

## <a name="overview"></a>Przegląd
Dzięki możliwości rozmieszczania offline usługi Azure Backup i Azure Import/Export jest prosty do przekazania danych w trybie offline na platformę Azure przy użyciu dysków. Proces kopii zapasowej Offline obejmuje następujące kroki:

> [!div class="checklist"]
> * Dane kopii zapasowej, są one przesyłane za pośrednictwem sieci, są zapisywane *lokalizacji tymczasowej*
> * Dane na *lokalizacji tymczasowej* są następnie zapisywane do co najmniej jeden dysk SATA, za pomocą *AzureOfflineBackupDiskPrep* narzędzia
> * Zadania importu platformy Azure są tworzone automatycznie przez narzędzie
> * Dyski SATA są następnie wysyłane do najbliższego centrum danych platformy Azure
> * Po zakończeniu przekazywania danych kopii zapasowej na platformie Azure, usługa Azure Backup kopiuje dane kopii zapasowej do magazynu kopii zapasowych i przyrostowych kopii zapasowych.

## <a name="supported-configurations"></a>Obsługiwane konfiguracje
Kopia zapasowa offline jest obsługiwana dla wszystkich modelach wdrażania z usługi Azure Backup dane kopii zapasowych poza siedzibą firmy, z lokalnej usługą Microsoft Cloud. Obejmuje to

> [!div class="checklist"]
> * Wykonywanie kopii zapasowej plików i folderów przy użyciu agenta usługi Microsoft Azure Recovery Services (MARS) lub agenta usługi Kopia zapasowa Azure.
> * Tworzenie kopii zapasowych wszystkich obciążeń i pliki z System Center Data Protection Manager (SC DPM)
> * Kopię zapasową wszystkich plików i obciążenia za pomocą programu Microsoft Azure Backup Server <br/>

## <a name="prerequisites"></a>Wymagania wstępne
Upewnij się, że spełniono następujące wymagania wstępne, przed zainicjowaniem przepływ pracy kopii zapasowej Offline
* A [magazyn usługi Recovery Services](backup-azure-recovery-services-vault-overview.md) została utworzona. Aby go utworzyć, skorzystaj z procedury opisanej w [w tym artykule](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Azure agenta usługi Kopia zapasowa lub usługi Azure Backup Server lub SC DPM została zainstalowana na komputerze klienckim albo Windows Server/Windows, zgodnie z wymaganiami i komputer jest zarejestrowany w magazynie usługi Recovery Services. Upewnij się, że tylko [najnowszą wersję usługi Azure Backup](https://go.microsoft.com/fwlink/?linkid=229525) jest używany.
* [Pobierz plik ustawień publikowania na platformie Azure](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) na komputerze, z którego planujesz wykonać kopię zapasową danych. Subskrypcja, w którym możesz pobrać plik ustawień publikowania może różnić się od subskrypcji, która zawiera magazyn usługi Recovery Services. Jeśli Twoja subskrypcja znajduje się w suwerennych chmurach platformy Azure, użyj następujących łączy, zgodnie z potrzebami pobrać plik ustawień publikowania na platformie Azure.

    | Region należących do suwerennej chmury | Łącze pliku ustawień publikowania platformy Azure |
    | --- | --- |
    | Stany Zjednoczone | [Link](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | Chiny | [Link](https://portal.azure.cn/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* Konto usługi Azure Storage za pomocą *klasycznego* modelu wdrażania przy użyciu została utworzona w ramach subskrypcji, z którego został pobrany plik ustawień publikowania w pokazany poniżej:

  ![Tworzenie klasycznego konta magazynu](./media/backup-azure-backup-import-export/storageaccountclassiccreate.png)

* Utworzono lokalizację tymczasową, która może być udział sieciowy lub kolejny dysk na komputerze, wewnętrzne lub zewnętrzne, przy użyciu miejsca na dysku do przechowywania kopii początkowej. Na przykład jeśli chcesz utworzyć kopię zapasową na serwerze plików 500 GB, upewnij się, że obszar przejściowy co najmniej 500 GB. (Mniejszą ilość jest używany z powodu stosowania kompresji).
* W odniesieniu do dysków, które będą wysyłane do platformy Azure upewnij się, który tylko 2,5 SSD lub 2.5 cala lub 3,5 cala SATA II/III wewnętrzne dyski twarde są używane. Można użyć dysków twardych do 10 TB. Sprawdź [dokumentacji usługi Azure Import/Export](../storage/common/storage-import-export-requirements.md#supported-hardware) o najnowszym zestawie dysków obsługiwanych przez usługę.
* Dyski SATA, musi być podłączony do komputera (nazywane *komputera kopiowania*) skąd kopię danych kopii zapasowej z *lokalizacji tymczasowej* do SATA odbywa się dyski. Upewnij się, że funkcja BitLocker jest włączona na *kopia komputera*

## <a name="workflow"></a>Przepływ pracy
Informacje przedstawione w tej sekcji ułatwiają ukończenia przepływu pracy w trybie offline z kopii zapasowej, dzięki czemu dane mogą dostarczone do centrum danych platformy Azure i przekazany do usługi Azure Storage. Jeśli masz pytania dotyczące usługi Import lub dowolnego aspektu procesu, zobacz [Omówienie usługi Import](../storage/common/storage-import-export-service.md) dokumentacji, do których odwołuje się wcześniej.

### <a name="initiate-offline-backup"></a>Inicjowanie kopii zapasowej offline
1. Po zaplanowaniu kopii zapasowej, zostanie wyświetlony następujący ekran (w systemie Windows Server, Windows klienta lub System Center Data Protection Manager).

    ![Ekran importowania](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    Oto odpowiednie ekranu w programie System Center Data Protection Manager: <br/>
    ![Ekran importowania SC programu DPM i usługi Azure Backup server](./media/backup-azure-backup-import-export/dpmoffline.png)

    Opis danych wejściowych jest następująca:

   * **Lokalizacja tymczasowa**: Lokalizacja magazynu tymczasowego, na którym jest zapisany początkowa kopia zapasowa. Lokalizacja tymczasowa mogą znajdować się na udziale sieciowym lub na komputerze lokalnym. Jeśli kopia komputera i komputera źródłowego są różne, zalecane jest, określ pełną ścieżkę sieciową lokalizacji przejściowej.
   * **Nazwa zadania importowania platformy Azure**: Unikatowa nazwa importu platformy Azure, które usługi i usługi Azure Backup śledzić transferu danych na dyskach na platformie Azure.
   * **Ustawienia publikowania na platformie Azure**: Podaj ścieżkę lokalną do pliku ustawień publikowania.
   * **Identyfikator subskrypcji platformy Azure**: Identyfikator subskrypcji platformy Azure dla subskrypcji, z którego został pobrany plik ustawień publikowania na platformie Azure.
   * **Konto usługi Azure Storage**: Nazwa konta magazynu w subskrypcji platformy Azure skojarzone z plikiem ustawień publikowania na platformie Azure.
   * **Azure Storage Container**: Nazwa magazynu docelowego obiektu blob na koncie Azure storage, którego dane kopii zapasowej jest importowany.

     Zapisz *lokalizacji tymczasowej* i *nazwę zadania importowania platformy Azure* podałeś, ponieważ jest wymagane, aby przygotować dyski.  

2. Ukończenia przepływu pracy, a aby zainicjować kopię kopie zapasowe offline, kliknij przycisk **Utwórz kopię zapasową teraz** w konsoli zarządzania usługi Azure Backup agent. Tworzenie początkowej kopii zapasowej są zapisywane do obszaru przejściowego w ramach tego kroku.

    ![Utwórz kopię zapasową teraz](./media/backup-azure-backup-import-export/backupnow.png)

    Ukończenia odpowiedniego przepływu pracy w programie System Center Data Protection Manager lub usługi Azure Backup server, kliknij prawym przyciskiem myszy **grupy ochrony**, a następnie wybierz **Utwórz punkt odzyskiwania** opcji. Następnie wybierz **ochrony w trybie Online** opcji.

    ![Program DPM SC i usługi Azure Backup server wykonaj kopię zapasową teraz](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    Po zakończeniu operacji, lokalizacji przejściowej jest gotowa do użycia w celu przygotowania dysku.

    ![Postęp tworzenia kopii zapasowej](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>Przygotowywanie dysków SATA i dostarczanie na platformę Azure
*AzureOfflineBackupDiskPrep* narzędzie służy do przygotowywania dysków SATA, które są wysyłane do najbliższego centrum danych platformy Azure. To narzędzie jest dostępne w katalogu instalacji agenta usługi Recovery Services w następującej ścieżce:

*\\Microsoft Azure Recovery Services Agent\\Utils\\*

1. Przejdź do katalogu i skopiuj **AzureOfflineBackupDiskPrep** katalogu na komputerze kopiowania, na którym są połączone dyski SATA, które mają zostać przygotowane. Upewnij się, poniżej w odniesieniu do komputera kopii:

   * Komputer kopiowania ma dostęp do lokalizacji tymczasowej dla przepływu pracy w trybie offline rozmieszczania przy użyciu tej samej ścieżki sieciowej została podana w **inicjowanie kopii zapasowej offline** przepływu pracy.
   * Funkcja BitLocker jest włączona na komputerze kopiowania.
   * Komputer kopiowania można uzyskać dostęp do witryny Azure portal.

     Jeśli to konieczne, komputer kopia może być taka sama jak komputera źródłowego.

     > [!IMPORTANT]
     > Jeśli komputer źródłowy jest maszyną wirtualną, następnie jest wymagane, aby użyć różnych serwer fizyczny lub na komputerze klienckim jako komputer kopiowania.


2. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień na tym komputerze kopiowania *AzureOfflineBackupDiskPrep* narzędzie katalogu jako bieżący katalog, a następnie uruchom następujące polecenie:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | Parametr | Opis |
    | --- | --- |
    | s:&lt;*ścieżka lokalizacji przemieszczania*&gt; |Wymagane dane wejściowe, które służy do zapewnienia ścieżkę do lokalizacji tymczasowej, które wprowadziłeś w **inicjowanie kopii zapasowej offline** przepływu pracy. |
    | p:&lt;*ścieżkę do PublishSettingsFile*&gt; |Opcjonalne dane wejściowe, które służy do zapewnienia ścieżkę do **ustawień publikowania platformy Azure** plików, które wprowadziłeś w **inicjowanie kopii zapasowej offline** przepływu pracy. |

    > [!NOTE]
    > &lt;Ścieżkę do AzurePublishSettingFile&gt; wartość jest obowiązkowy w przypadku, gdy kopia komputera i komputera źródłowego są różne.
    >
    >

    Po uruchomieniu polecenia, narzędzie żądań wybór zadania importu platformy Azure, do którego odnosi się do dysków, które muszą być przygotowane. Jeśli tylko zadania importu pojedynczego jest skojarzony z podanej lokalizacji tymczasowej, pojawia się ekran podobne do pokazanego poniżej.

    ![Usługa Azure przygotowanie dysku narzędzia w danych wejściowych](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>

3. Wprowadź literę dysku bez dwukropka końcowe dla zainstalowanego dysku, który ma zostać przygotowana do przeniesienia na platformę Azure. Potwierdź formatowania dysku, po wyświetleniu monitu.

    Narzędzie następnie rozpoczyna się przygotować dysku i skopiowanie danych kopii zapasowej. Może być konieczne dołączanie dodatkowych dysków, po wyświetleniu monitu przez narzędzie, w przypadku, gdy podana dysku nie ma wystarczającej ilości miejsca dla danych kopii zapasowej. <br/>

    Na koniec pomyślne wykonanie tego narzędzia jeden lub więcej dysków, które podane są przygotowywane do wysłania do platformy Azure. Ponadto zadania importu o nazwie podany podczas **inicjowanie kopii zapasowej offline** przepływu pracy jest tworzony na platformie Azure. Na koniec narzędzie wyświetli adres wysyłkowy centrach danych platformy Azure, których dyski muszą być wysyłane.

    ![Zakończenie przygotowywania dysku platformy Azure](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>

4. Po zakończeniu wykonywania polecenia zostanie również wyświetlony opcję, aby zaktualizować informacje o wysyłce, jak pokazano poniżej:

    ![Informacje o opcję wysyłania aktualizacji](./media/backup-azure-backup-import-export/updateshippingutility.png)<br/>

5. Można wprowadzić szczegóły natychmiast. Narzędzie przeprowadzi Cię przez proces obejmujące serię danych wejściowych. Jednak jeśli nie masz informacje, takie jak śledzenie liczby lub inne szczegóły dotyczące wysyłki, możesz zakończyć sesję. W tym artykule znajdują się kroki, aby zaktualizować szczegóły dotyczące wysyłki później.

6. Odeślij dyski z podanym przez narzędzie adres i Zachowaj numer śledzenia do użytku w przyszłości.

   > [!IMPORTANT]
   > Żadne dwa zadania importowania platformy Azure może mieć taką samą liczbę śledzenia. Upewnij się, że dyski przygotowane przez narzędzie w ramach jednego zadania importowania platformy Azure są dostarczane razem w jednym pakiecie oraz czy pojedynczej unikatowy numer identyfikacyjny dla pakietu. Nie wolno łączyć z stacje przygotowany w ramach **różnych** zadania importowania platformy Azure w jednym pakiecie.

5. Jeśli masz śledzenia informacji o numerze, przejdź do komputera źródłowego, który oczekuje na zakończenie zadania importowania i uruchom następujące polecenie w wierszu polecenia z podwyższonym poziomem uprawnień z z *AzureOfflineBackupDiskPrep* narzędzie katalogu bieżący katalog:

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   Użytkownik może opcjonalnie uruchom następujące polecenie z innego komputera takich jak *komputera kopiowania*, za pomocą *AzureOfflineBackupDiskPrep* narzędzie katalogu jako bieżący katalog:

   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | Parametr | Opis |
    | --- | --- |
    | u: | Wymagane dane wejściowe, używane do aktualizowania wysyłanie szczegółów zadania importowania platformy Azure |
    | s:&lt;*ścieżka lokalizacji przemieszczania*&gt; | Wymagane dane wejściowe, jeśli polecenie nie jest wykonywane na komputerze źródłowym. Umożliwia podanie ścieżki do lokalizacji tymczasowej, które wprowadziłeś w **inicjowanie kopii zapasowej offline** przepływu pracy. |
    | p:&lt;*ścieżkę do PublishSettingsFile*&gt; | Wymagane dane wejściowe, jeśli polecenie nie jest wykonywane na komputerze źródłowym. Umożliwia podanie ścieżki do **ustawień publikowania platformy Azure** plików, które wprowadziłeś w **inicjowanie kopii zapasowej offline** przepływu pracy. |

    Narzędzie automatycznie wykrywa zadania importu, który czeka na komputerze źródłowym lub zadań importu skojarzone z lokalizacji tymczasowej, gdy polecenie jest wykonywane na innym komputerze. Zapewnia opcję, aby zaktualizować informacje dotyczące wysyłki za pośrednictwem serii danych wejściowych, jak pokazano poniżej:

    ![Wprowadź informacje dotyczące wysyłki](./media/backup-azure-backup-import-export/shippinginputs.png)<br/>

6. Po ich podaniu wszystkich danych wejściowych są dokładnie przejrzyj szczegóły i zatwierdź informacjami o dostawie dostarczane przez wpisanie *tak*.

    ![Przejrzyj informacje dotyczące wysyłki](./media/backup-azure-backup-import-export/reviewshippinginformation.png)<br/>

7. Na temat aktualizowania informacjami o dostawie pomyślnie, narzędzie zapewnia lokalizacji lokalnej, gdzie są przechowywane szczegóły dotyczące wysyłki wprowadzone przez użytkownika, jak pokazano poniżej

    ![Przechowywanie informacje dotyczące wysyłki](./media/backup-azure-backup-import-export/storingshippinginformation.png)<br/>

   > [!IMPORTANT]
   > Upewnij się, że dyski osiągną centrum danych platformy Azure w ciągu dwóch tygodni związanych z udostępnianiem przy użyciu informacji o wysyłce *AzureOfflineBackupDiskPrep* narzędzia. Niewykonanie tej czynności może spowodować dyski nie są przetwarzane.  

Po ukończeniu powyższych kroków centrum danych platformy Azure jest gotowa do odbierania dysków i dalej przetwarzać je do transferu danych kopii zapasowych z stacje do utworzonego konta magazynu platformy Azure classic-type.

### <a name="time-to-process-the-drives"></a>Czas przetwarzania dysków
Czas potrzebny do procesu zadania importowania platformy Azure w zależności od różnych czynników, takich jak czas dostawy zadania typu, typu i rozmiaru danych, w której są kopiowane i rozmiaru dysków udostępnionych. Usługa Azure Import/Export nie ma umowy SLA, ale po otrzymaniu dyski usługi dokłada starań, aby zakończenie kopiowania danych kopii zapasowej na koncie magazynu platformy Azure w dniach 7 – 10. Następnej sekcji przedstawiono, jak można monitorować stan zadania importowania platformy Azure.

### <a name="monitoring-azure-import-job-status"></a>Monitorowanie stanu zadaniem importowania platformy Azure
Mimo że stacje dysków w trakcie przesyłania lub centrum danych platformy Azure do skopiowania do konta magazynu, agenta usługi Azure Backup lub programu DPM SC lub konsolę serwera usługi Azure Backup na komputerze źródłowym zawiera następujący stan zadania zaplanowane kopie zapasowe.

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

Wykonaj następujące czynności, aby sprawdzić stan zadania importu.
1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień na komputerze źródłowym, a następnie uruchom następujące polecenie:

     `AzureOfflineBackupDiskPrep.exe u:`

2.  Dane wyjściowe pokazują bieżący stan zadania importowania, jak pokazano poniżej:

    ![Sprawdzanie stanu zadania importu](./media/backup-azure-backup-import-export/importjobstatusreporting.png)<br/>

Aby uzyskać więcej informacji na temat różnych stanów zadania importowania platformy Azure, zobacz [w tym artykule](../storage/common/storage-import-export-view-drive-status.md)

### <a name="complete-the-workflow"></a>Kończenie przepływu pracy
Po ukończeniu zadania importu danych początkowej kopii zapasowej jest dostępna w ramach konta magazynu. W momencie następnej zaplanowanej kopii zapasowej usługa Azure backup kopiuje zawartość danych z konta magazynu do magazynu usługi Recovery Services jak pokazano poniżej:

   ![Kopiowanie danych do magazynu usługi Recovery Services](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

W momencie następnej zaplanowanej kopii zapasowej usługi Azure Backup wykonuje przyrostowe kopie zapasowe początkowa kopia zapasowa.

## <a name="next-steps"></a>Kolejne kroki
* Masz pytania dotyczące usługi Azure Import/Export przepływu pracy, można znaleźć [przesyłanie danych do magazynu obiektów Blob za pomocą usługi Microsoft Azure Import/Export](../storage/common/storage-import-export-service.md).
* Zapoznaj się z sekcją kopie zapasowe offline z usługi Azure Backup [— często zadawane pytania](backup-azure-backup-faq.md) w razie jakichkolwiek pytań o przepływie pracy.

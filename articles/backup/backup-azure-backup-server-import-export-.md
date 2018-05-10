---
title: Kopia zapasowa Azure - kopii zapasowej programu DPM w trybie Offline i serwera kopii zapasowej Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Kopia zapasowa Azure umożliwia wysyłanie danych w sieci za pomocą usługi Import/Eksport Azure. W tym artykule opisano, w trybie offline inicjacją dane początkowej kopii zapasowej za pomocą usługi Azure importu wyeksportować.
services: backup
documentationcenter: ''
author: saurabhsensharma
manager: shivamg
editor: ''
ms.assetid: ada19c12-3e60-457b-8a6e-cf21b9553b97
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 5/8/2018
ms.author: saurse;nkolli;trinadhk
ms.openlocfilehash: e3f7ae187bee8680fbff7e5c78c666a0bda7e48f
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server"></a>Przepływ pracy w trybie offline z kopii zapasowej programu DPM i serwera kopii zapasowej Azure
Kopia zapasowa Azure zawiera kilka wbudowanych korzyści, które zapisania koszty sieci i magazynu podczas początkowej pełne kopie zapasowe danych na platformie Azure. Początkowa pełnych kopii zapasowych zwykle transfer dużych ilości danych i wymagają większej przepustowości sieci w porównaniu do kolejnych kopii zapasowych, które transfer tylko delty/przyrostowa. Kopia zapasowa Azure kompresuje początkowej kopii zapasowych. W procesie wstępnego wypełniania w trybie offline kopia zapasowa Azure można użyć dysków do przekazania skompresowane dane początkowej kopii zapasowej w trybie offline na platformie Azure.

Proces w trybie offline wstępne wypełnianie kopia zapasowa Azure jest ściśle zintegrowany z [usługi Import/Eksport Azure](../storage/common/storage-import-export-service.md) który umożliwia przesyłanie danych do platformy Azure przy użyciu dysków. Jeśli masz terabajtów (tabel) dane początkowej kopii zapasowej, który musi być przesyłane przez sieci dużymi opóźnieniami i niskiej przepustowości sieci, można użyć przepływu pracy w trybie offline wstępne wypełnianie na potrzeby wysłania początkowej kopii zapasowej na jeden lub więcej dysków twardych do centrum danych Azure. Ten artykuł zawiera omówienie i dalszych kroków szczegóły kończące ten przepływ pracy dla programu System Center DPM i serwer kopii zapasowej Azure.

> [!NOTE]
> Proces kopia zapasowa Offline dla agenta usług odzyskiwania Azure firmy Microsoft (MARS) różni się od programu System Center DPM i serwera kopii zapasowej Azure. Uzyskać informacji o korzystaniu z kopii zapasowej w trybie Offline z agenta MARS, zobacz [w tym artykule](backup-azure-backup-import-export.md). Kopia zapasowa offline nie jest obsługiwana dla kopii zapasowych stanu systemu odbywa się za pomocą agenta usługi Kopia zapasowa Azure. 
>

## <a name="overview"></a>Przegląd
Z możliwością obsługi w trybie offline Azure Backup i Azure importu/eksportu jest prosty do przekazania danych w trybie offline na platformie Azure przy użyciu dysków. Proces w tryb Offline kopia zapasowa obejmuje następujące kroki:

> [!div class="checklist"]
> * Dane kopii zapasowej, są one przesyłane za pośrednictwem sieci, są zapisywane do *lokalizacji wystawiania*
> * Dane na *lokalizacji przejściowej* następnie są zapisywane w jeden lub więcej dysków SATA przy użyciu *AzureOfflineBackupDiskPrep* narzędzia
> * Zadania importu platformy Azure są tworzone za pomocą narzędzia
> * Dyski SATA są następnie wysyłane do najbliższego centrum danych Azure
> * Po zakończeniu przekazywania danych kopii zapasowej na platformie Azure, kopia zapasowa Azure kopiuje dane kopii zapasowej w magazynie kopii zapasowych i przyrostowych kopii zapasowych.

## <a name="supported-configurations"></a>Obsługiwane konfiguracje 
Kopia zapasowa offline jest obsługiwana dla wszystkich modeli wdrażania programu Kopia zapasowa Azure poza siedzibą firmy dane kopii zapasowej z lokalnej do Microsoft Cloud. Obejmuje to

> [!div class="checklist"]
> * Wykonywanie kopii zapasowej plików i folderów z agenta usług odzyskiwania Azure firmy Microsoft (MARS) lub agenta usługi Kopia zapasowa Azure. 
> * Kopie zapasowe wszystkich obciążeń oraz pliki z System Center Data Protection Manager (SC DPM) 
> * Tworzenie kopii zapasowej wszystkich plików i obciążeń z serwer kopii zapasowej Microsoft Azure <br/>

## <a name="prerequisites"></a>Wymagania wstępne
Upewnij się, że spełniono następujące wymagania wstępne, przed rozpoczęciem przepływu pracy w trybie Offline z kopii zapasowej
* A [magazyn usług odzyskiwania](backup-azure-recovery-services-vault-overview.md) został utworzony. Aby go utworzyć, skorzystaj z procedury opisanej w [w tym artykule](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Azure agenta kopii zapasowej lub serwer kopii zapasowej Azure lub SC DPM została zainstalowana na komputerze klienckim albo Windows Server i Windows, zgodnie z wymaganiami, a komputer jest zarejestrowany w magazynie usług odzyskiwania. Upewnij się, że tylko [najnowszej wersji programu Kopia zapasowa Azure](https://go.microsoft.com/fwlink/?linkid=229525) jest używany. 
* [Pobieranie pliku ustawień publikowania na platformie Azure](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) na komputerze, z którego planujesz do tworzenia kopii zapasowej danych. Subskrypcja, z którego można pobrać plik ustawień publikowania może różnić się od subskrypcji, który zawiera magazyn usług odzyskiwania. W przypadku subskrypcji na suwerenne chmury Azure, użyj następujących łączy, zależnie od potrzeb można pobrać pliku ustawień publikowania na platformie Azure.

    | Region suwerennych chmury | Łącze pliku ustawień publikowania platformy Azure |
    | --- | --- |
    | Stany Zjednoczone | [Link](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | Chiny | [Link](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* Konto magazynu Azure z *klasycznego* modelu wdrażania został utworzony w ramach subskrypcji, z którego pobrano plik ustawień publikowania w sposób przedstawiony poniżej: 

 ![Tworzenie konta magazynu classic](./media/backup-azure-backup-import-export/storageaccountclassiccreate.png)

* Utworzono tymczasowej lokalizacji, która może być udział sieciowy lub dodatkowego dysku na komputerze, wewnętrzne lub zewnętrzne, z miejsca na dysku do przechowywania kopii początkowej. Na przykład jeśli chcesz utworzyć kopię zapasową na serwerze plików 500 GB, upewnij się, że obszaru przemieszczania jest co najmniej 500 GB. (Mniejszą ilość jest używany z powodu kompresji).
* W odniesieniu do dysków, które będą wysyłane do usługi Azure upewnij się, które tylko 2,5 SSD lub 2.5 cala lub 3,5 cala SATA II/III wewnętrzne dyski twarde są używane. Dyski twarde można użyć do 10 TB. Sprawdź [dokumentację usługi Import/Eksport Azure](../storage/common/storage-import-export-service.md#hard-disk-drives) najnowszego zestawu dysków, obsługiwanych przez usługę.
* Dyski SATA musi być podłączony do komputera (nazywane *komputera kopii*) z jakiej lokalizacji kopię danych kopii zapasowej z *lokalizacji przejściowej* do SATA odbywa się dyski. Upewnij się, że funkcja Bitlocker jest włączona na *kopiowania komputera* 

## <a name="workflow"></a>Przepływ pracy
Informacje przedstawione w tej sekcji pomaga ukończenia przepływu pracy w trybie offline z kopii zapasowej, aby dane można dostarczyć do centrum danych Azure i przekazane do magazynu Azure. Jeśli masz pytania dotyczące usługi importu lub dowolnego aspektu procesu, zobacz [Import — Omówienie usługi](../storage/common/storage-import-export-service.md) dokumentacji odwołuje się do wcześniej.

### <a name="initiate-offline-backup"></a>Inicjowanie kopii zapasowej w trybie offline
1. Podczas planowania kopii zapasowej, zostanie wyświetlony następujący ekran (w systemie Windows Server, klient systemu Windows lub System Center Data Protection Manager).

    ![Importuj ekranu](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    Oto odpowiedniego ekranu w System Center Data Protection Manager: <br/>
    ![Ekran importu serwera SC DPM i kopia zapasowa Azure](./media/backup-azure-backup-import-export/dpmoffline.png)

    Opis danych wejściowych jest następujący:

    * **Miejsce przemieszczania**: lokalizacji tymczasowej, do którego początkowa kopia zapasowa jest zapisywany. Lokalizacja przemieszczania mogą znajdować się na udziału sieciowego lub na komputerze lokalnym. Jeśli kopia komputera i komputera źródłowego są różne, zaleca się czy określić pełną ścieżkę sieciową do lokalizacji tymczasowej.
    * **Nazwa zadania importowania platformy Azure**: unikatową nazwę importu platformy Azure, które usługi i kopia zapasowa Azure śledzić transferu danych przesyłanych na dyskach w systemie Azure.
    * **Ustawień publikowania platformy Azure**: Podaj ścieżkę lokalną do pliku ustawień publikowania.
    * **Identyfikator subskrypcji platformy Azure**: identyfikator subskrypcji platformy Azure dla subskrypcji, z którego został pobrany plik ustawień publikowania na platformie Azure. 
    * **Konto usługi Azure Storage**: Nazwa konta magazynu w subskrypcji platformy Azure skojarzone z plikiem ustawień publikowania na platformie Azure.
    * **Kontener magazynu Azure**: Nazwa magazynu docelowego obiektu blob na koncie magazynu Azure, której dane kopii zapasowej zostaną zaimportowane.

     Zapisz *lokalizacji przejściowej* i *Nazwa zadania importowania platformy Azure* podane, ponieważ jest wymagane, aby przygotować dyski.  
     
2. Ukończenia przepływu pracy, a aby zainicjować kopii zapasowej w trybie offline, kliknij przycisk **wykonaj kopię zapasową teraz** w konsoli zarządzania usługi Azure Backup agent. Tworzenie początkowej kopii zapasowej są zapisywane do obszaru przemieszczania w ramach tego kroku.

    ![Wykonaj kopię zapasową](./media/backup-azure-backup-import-export/backupnow.png)

    Do ukończenia odpowiedniego przepływu pracy w programie System Center Data Protection Manager lub kopia zapasowa Azure serwera, kliknij prawym przyciskiem myszy **grupy ochrony**, a następnie wybierz pozycję **Utwórz punkt odzyskiwania** opcji. Następnie wybierz pozycję **ochrony w trybie Online** opcji.

    ![Program DPM SC i serwer usługi Kopia zapasowa Azure wykonaj kopię zapasową teraz](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    Po zakończeniu operacji, przemieszczana lokalizacja jest gotowy do użycia w celu przygotowania dysku.

    ![Postęp tworzenia kopii zapasowej](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>Przygotuj dyski SATA i wysłać do platformy Azure
*AzureOfflineBackupDiskPrep* jest używane narzędzie, aby przygotować dyski SATA, które są wysyłane do najbliższego centrum danych Azure. To narzędzie jest dostępny w katalogu instalacji agenta usług odzyskiwania w następującej ścieżce:

   *\Microsoft* *azure* *odzyskiwania* *usług* * Agent\Utils\*

1. Przejdź do katalogu, a następnie skopiuj **AzureOfflineBackupDiskPrep** katalogu na komputerze kopiowania, na którym są połączone dyski SATA przygotowany. Upewnij się, poniżej w odniesieniu do komputera kopiowania:

    * Komputer kopii można uzyskać dostęp do tymczasowej lokalizacji dla przepływu pracy w trybie offline wstępne wypełnianie przy użyciu tej samej lokalizacji sieciowej, która została dostarczona w **zainicjować kopię zapasową offline** przepływu pracy.
    * Funkcja BitLocker jest włączona na komputerze kopiowania.
    * Komputer kopii mogą uzyskać dostęp do portalu Azure.

    W razie potrzeby kopiowania komputer może być taka sama, co na komputerze źródłowym. 
    
    > [!IMPORTANT] 
    > Jeśli komputer źródłowy jest maszyną wirtualną, następnie należy koniecznie używany jako komputer kopiowania na innym serwerze fizycznego lub komputera klienckiego.
    
    
2. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień na komputerze kopii *AzureOfflineBackupDiskPrep* katalogu narzędzia jako bieżący katalog, a następnie uruchom następujące polecenie:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | Parametr | Opis |
    | --- | --- |
    | s:&lt;*ścieżka lokalizacji przemieszczania*&gt; |Wymagane dane wejściowe, służący do Podaj ścieżkę do lokalizacji tymczasowej, wprowadzony w **zainicjować kopię zapasową offline** przepływu pracy. |
    | p:&lt;*ścieżkę do PublishSettingsFile*&gt; |Opcjonalne dane wejściowe, służący do Podaj ścieżkę do **ustawień publikowania platformy Azure** pliku wprowadzony w **zainicjować kopię zapasową offline** przepływu pracy. |

    > [!NOTE]
    > &lt;Ścieżkę do AzurePublishSettingFile&gt; wartość jest wymagany, jeśli kopia komputera i komputera źródłowego są różne.
    >
    >

    Po uruchomieniu polecenia narzędzie żądań wybór zadania importu platformy Azure, umożliwiająca dyski, które muszą być przygotowane. Jeśli tylko zadania importu pojedynczego jest skojarzony z podanej lokalizacji tymczasowej, zostanie wyświetlony ekran podobny do tego, który jest zgodny.

    ![Azure wprowadzania narzędzie przygotowanie dysku](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>
    
3. Wprowadź literę dysku bez dwukropka końcowe dla zainstalowanego dysku, który ma zostać przygotowane do przeniesienia do usługi Azure. Potwierdź formatowania dysku po wyświetleniu monitu.

    Narzędzie następnie rozpoczyna się przygotować dysku i skopiowanie danych kopii zapasowej. Może być konieczne dołączanie dodatkowych dysków, po wyświetleniu monitu przez narzędzie, w przypadku udostępnionego dysku nie ma wystarczającą ilość miejsca na dane kopii zapasowej. <br/>

    Na koniec pomyślnego wykonania narzędzia jeden lub więcej dysków, które są przygotowywane do wysyłki na platformie Azure. Ponadto zadania importu o nazwie podany podczas **zainicjować kopię zapasową offline** przepływu pracy jest tworzony na platformie Azure. Na koniec narzędzie wyświetla adres wysyłkowy centrum danych Azure, której dyski muszą zostać wysłane.

    ![Zakończenie przygotowywania dysku platformy Azure](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>
    
4. Po zakończeniu wykonywania polecenia również widoczna opcja, aby zaktualizować informacje o wysyłki, jak pokazano poniżej:

    ![Informacje o opcję wysyłania aktualizacji](./media/backup-azure-backup-import-export/updateshippingutility.png)<br/>

5. Można od razu wprowadź szczegóły. Narzędzie prowadzi użytkownika przez proces udziałem serie danych wejściowych. Jednak jeśli nie masz informacje, takie jak śledzenie liczby lub inne szczegóły dotyczące wysyłania, należy zakończyć sesję. Kroki, aby zaktualizować wysyłce później znajdują się w tym artykule. 

6. Wysłać dysków na adres zapewnianej przez narzędzie i Zachowaj numer śledzenia do użytku w przyszłości.

   > [!IMPORTANT] 
   > Nie dwa zadania importowania platformy Azure może mieć ten sam numer śledzenia. Upewnij się, że dyski przygotowane przez narzędzie z jednego zadania importowania platformy Azure są wysłane razem w jednym pakiecie i że istnieje jeden unikatowy numer identyfikacyjny dla tego pakietu. Nie wolno łączyć dyski przygotowany w ramach **różnych** zadania importowania platformy Azure w jednym pakiecie. 

5. Jeśli masz śledzenia informacji o numerze, przejdź do komputera źródłowego, który oczekuje na zakończenie zadania importowania i uruchom następujące polecenie w wierszu polecenia z podwyższonym poziomem uprawnień, z *AzureOfflineBackupDiskPrep* katalogu narzędzia bieżący katalog: 

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   Można uruchomić następujące polecenie z innego komputera, takich jak *komputera kopiowania*, z *AzureOfflineBackupDiskPrep* katalogu narzędzia jako bieżący katalog:
   
   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | Parametr | Opis |
    | --- | --- |
    | U: | Wymagane dane wejściowe, używane do aktualizowania wysyłanie szczegółów dla zadania importu platformy Azure |
    | s:&lt;*ścieżka lokalizacji przemieszczania*&gt; | Obowiązkowe wprowadzania na komputerze źródłowym nie jest uruchamiane polecenie. Pozwala wprowadzić ścieżkę do lokalizacji tymczasowej, wprowadzony w **zainicjować kopię zapasową offline** przepływu pracy. |
    | p:&lt;*ścieżkę do PublishSettingsFile*&gt; | Obowiązkowe wprowadzania na komputerze źródłowym nie jest uruchamiane polecenie. Pozwala wprowadzić ścieżkę do **ustawień publikowania platformy Azure** pliku wprowadzony w **zainicjować kopię zapasową offline** przepływu pracy. |
    
    Narzędzie automatycznie wykrywa zadania importu czeka komputerze źródłowym lub zadania importu skojarzonego z lokalizacji tymczasowej, gdy jest wykonywane na innym komputerze. Zapewnia opcję, aby zaktualizować wysyłanie informacji przez kilka składników, jak pokazano poniżej: 
    
    ![Wprowadzanie wysyłania informacji](./media/backup-azure-backup-import-export/shippinginputs.png)<br/>

6. Gdy wszystkie dane wejściowe są dostarczane, należy dokładnie przejrzeć szczegóły i zatwierdź informacji wysyłki podane przez wpisanie *tak*. 

    ![Przejrzyj informacje o wysyłce](./media/backup-azure-backup-import-export/reviewshippinginformation.png)<br/>

7. Na aktualizowanie informacji o wysyłki pomyślnie, narzędzie udostępnia lokalizacji lokalnej, gdzie wysyłce wprowadzana przez użytkownika są przechowywane w sposób przedstawiony poniżej 

    ![Przechowywanie wysyłania informacji](./media/backup-azure-backup-import-export/storingshippinginformation.png)<br/>

   > [!IMPORTANT] 
   > Upewnij się, że osiągną centrum danych Azure w ciągu dwóch tygodni udostępniania przy użyciu informacji wysyłki *AzureOfflineBackupDiskPrep* narzędzia. Błąd w tym celu może spowodować dyski nie są przetwarzane.  

Po wykonaniu powyższych kroków centrum danych Azure jest gotowa do odbierania dyski i dalszego przetwarzania je, aby przenieść dane kopii zapasowej z dysków do utworzone konto magazynu Azure classic typu. 

### <a name="time-to-process-the-drives"></a>Czas przetwarzania na dyskach 
Czas potrzebny do procesu zadania importowania platformy Azure może być różna w zależności od różnych czynników, takich jak czas dostawy zadania typ, typ i rozmiar danych, w której są kopiowane i rozmiaru dysków podane. Usługa Import/Eksport Azure nie ma umowy dotyczącej poziomu usług, ale po otrzymaniu dyski usługi dokłada starań, aby ukończyć kopii kopii zapasowej danych na koncie magazynu Azure w ciągu 7 do 10 dni. Następna sekcja zawiera szczegóły, jak można monitorować stan zadania importowania platformy Azure. 

### <a name="monitoring-azure-import-job-status"></a>Monitorowanie stanu zadania importowania platformy Azure
Gdy dyski są w trakcie przesyłania lub centrum danych Azure ma zostać skopiowany na konto magazynu, agent usługi Kopia zapasowa Azure lub SC DPM lub kopia zapasowa Azure konsoli serwera na komputerze źródłowym zawiera następujący stan zadania zaplanowane tworzenie kopii zapasowych. 

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

Wykonaj poniższe czynności, aby sprawdzić stan zadania importu. 
1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień na komputerze źródłowym, a następnie uruchom następujące polecenie:
    
     `AzureOfflineBackupDiskPrep.exe u:`
    
2.  Dane wyjściowe zawierają bieżący stan zadania importu, jak pokazano poniżej: 

    ![Sprawdzanie stanu zadania importu](./media/backup-azure-backup-import-export/importjobstatusreporting.png)<br/>

Aby uzyskać więcej informacji o różnych stanów zadania importowania platformy Azure, zobacz [w tym artykule](../storage/common/storage-import-export-service.md#how-does-the-azure-importexport-service-work)

### <a name="complete-the-workflow"></a>Ukończenia przepływu pracy
Po zakończeniu zadania importu danych początkowej kopii zapasowej jest dostępna na koncie magazynu. Podczas następnego zaplanowanego tworzenia kopii zapasowej kopia zapasowa Azure kopiuje zawartość dane z konta magazynu do magazynu usług odzyskiwania w sposób przedstawiony poniżej: 

   ![Kopiowanie danych do magazynu usług odzyskiwania](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

Podczas następnego zaplanowanego tworzenia kopii zapasowej kopia zapasowa Azure wykonuje przyrostowej kopii zapasowej za pośrednictwem początkowa kopia zapasowa.

## <a name="next-steps"></a>Kolejne kroki
* Wszelkie pytania dotyczące przepływu pracy Import/Eksport Azure można znaleźć w temacie [transferu danych do magazynu obiektów Blob za pomocą usługi Import/Eksport Microsoft Azure](../storage/common/storage-import-export-service.md).
* Zapoznaj się z sekcją w trybie offline z kopii zapasowej systemu Azure Backup [— często zadawane pytania](backup-azure-backup-faq.md) wszelkie pytania dotyczące przepływu pracy.

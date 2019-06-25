---
title: Usługa Azure Backup — kopia zapasowa Offline lub pierwszego rozmieszczania przy użyciu usługi Azure Import/Export
description: Dowiedz się, jak usługa Azure Backup umożliwia wysyłanie danych z sieci za pomocą usługi Azure Import/Export. W tym artykule opisano, w trybie offline rozmieszczania danych początkowej kopii zapasowej za pomocą usługi Azure Import/Eksport.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 05/17/2018
ms.author: saurse
ms.openlocfilehash: e08b1d8f847536101d44db266be5cd34e3e6a74c
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155311"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Przepływ pracy tworzenia kopii zapasowych w trybie offline w usłudze Azure Backup
Usługa Azure Backup ma kilka wbudowanych korzyści, które zmniejsza koszty magazynu i sieci podczas początkowego pełne kopie zapasowe danych na platformę Azure. Początkowa pełne kopie zapasowe zazwyczaj przesyłanie dużych ilości danych, a także wymagają większej przepustowości sieci w porównaniu do kolejnych kopii zapasowych, które przenieść tylko różnic/przyrostowa. Proces rozmieszczania w trybie offline usługa Azure Backup można użyć dysków do przekazania danych kopii zapasowej w trybie offline na platformę Azure.

Usługa Azure Backup proces rozmieszczania offline jest ściśle zintegrowany z [usługa Azure Import/Export](../storage/common/storage-import-export-service.md) , umożliwia transfer danych początkowej kopii zapasowej na platformie Azure przy użyciu dysków. Jeśli masz terabajtów (TB) danych początkowej kopii zapasowej, który musi być przesyłane przez wysokich opóźnieniach i małej przepustowości sieci, można użyć przepływu pracy rozmieszczania offline na potrzeby wysłania początkowej kopii zapasowej na jeden lub więcej dysków twardych, do centrum danych platformy Azure. Poniższa ilustracja zawiera omówienie kroków w przepływie pracy.

  ![Omówienie procesu przepływu pracy w trybie offline importu](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

Proces tworzenia kopii zapasowej w trybie offline obejmuje następujące czynności:

1. Zamiast wysyłania danych kopii zapasowej za pośrednictwem sieci, należy zapisać dane kopii zapasowej do lokalizacji tymczasowej.
2. Użyj narzędzia AzureOfflineBackupDiskPrep, aby zapisywać dane w lokalizacji tymczasowej do co najmniej jeden dysk SATA.
3. W ramach przygotowania narzędzie AzureOfflineBackupDiskPrep tworzy zadanie usługi Azure Import. Wyślij dyski SATA do najbliższego centrum danych platformy Azure i odwoływać się do zadania importu do łączenia z działania.
4. W centrum danych platformy Azure danych na dyskach jest kopiowany do konta usługi Azure storage.
5. Usługa Azure Backup kopiuje dane kopii zapasowej z konta magazynu do magazynu usługi Recovery Services i przyrostowych kopii zapasowych.

## <a name="supported-configurations"></a>Obsługiwane konfiguracje 
Następujące funkcje usługi Azure Backup lub obciążeń obsługuje korzystanie z kopii zapasowej Offline.

> [!div class="checklist"]
> * Wykonywanie kopii zapasowej plików i folderów za pomocą agenta usługi Microsoft Azure Recovery Services (MARS), nazywane także agenta usługi Azure Backup. 
> * Tworzenie kopii zapasowych wszystkich obciążeń i pliki z System Center Data Protection Manager (SC DPM) 
> * Kopię zapasową wszystkich plików i obciążenia za pomocą programu Microsoft Azure Backup Server <br/>

   > [!NOTE]
   > Kopia zapasowa offline nie jest obsługiwana dla kopii zapasowych stanu systemu, odbywa się za pomocą agenta usługi Azure Backup. 

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Wymagania wstępne

  > [!NOTE]
  > Następujące wymagania wstępne i przepływ pracy dotyczy tylko w trybie Offline kopii zapasowych plików i folderów za pomocą [najnowszą wersję agenta usług MARS](https://aka.ms/azurebackup_agent). Aby tworzyć kopie zapasowe w trybie Offline dla obciążeń przy użyciu programu System Center DPM lub usłudze Azure Backup Server, zapoznaj się [w tym artykule](backup-azure-backup-server-import-export-.md). 

Przed zainicjowaniem przepływ pracy kopii zapasowej Offline, należy spełnić następujące wymagania wstępne: 
* Tworzenie [magazyn usługi Recovery Services](backup-azure-recovery-services-vault-overview.md). Aby utworzyć magazyn, skorzystaj z procedury opisanej w [w tym artykule](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Upewnij się, który tylko [najnowszą wersję agenta usługi Kopia zapasowa Azure](https://aka.ms/azurebackup_agent) został zainstalowany na kliencie systemu Windows Server/Windows zgodnie z wymaganiami i komputer jest zarejestrowany w magazynie usługi Recovery Services.
* Numer 3.7.0 programu Azure PowerShell jest wymagany na komputerze z uruchomionym agentem usługi Azure Backup. Zalecane jest, możesz pobrać i [instalacji numer 3.7.0 wersję programu Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).
* Na komputerze z uruchomionym agentem usługi Azure Backup upewnij się, że zainstalowano Microsoft Edge lub Internet Explorer 11 i języka JavaScript jest włączona. 
* Utwórz konto usługi Azure Storage w tej samej subskrypcji co magazyn usługi Recovery Services. 
* Upewnij się, że masz [niezbędne uprawnienia](../active-directory/develop/howto-create-service-principal-portal.md) do tworzenia aplikacji usługi Azure Active Directory. Przepływ pracy kopii zapasowej Offline tworzy aplikację usługi Azure Active Directory w ramach subskrypcji skojarzonych z kontem usługi Azure Storage. Celem aplikacji jest udostępnianie w usłudze Azure Import, wymaganych dla przepływu pracy w trybie Offline z kopii zapasowej bezpiecznego i zakresu dostępu usługi Azure Backup. 
* Zarejestruj dostawcę zasobów Microsoft.ImportExport z subskrypcją zawierającą konto usługi Azure Storage. Aby zarejestrować dostawcę zasobów:
    1. W menu głównym kliknij **subskrypcje**.
    2. Gdy subskrybujesz wiele subskrypcji, wybierz subskrypcję, której używasz dla kopii zapasowej offline. Jeśli używasz tylko jedną subskrypcję, zostanie wyświetlony Twojej subskrypcji.
    3. W menu subskrypcja kliknij **dostawców zasobów** do wyświetlania listy dostawców.
    4. Na liście dostawców przewiń w dół do Microsoft.ImportExport. Jeśli stan jest NotRegistered, kliknij **zarejestrować**.
    ![Rejestrowanie dostawcy zasobów](./media/backup-azure-backup-import-export/registerimportexport.png)
* Utworzono lokalizację tymczasową, która może być udział sieciowy lub kolejny dysk na komputerze, wewnętrzne lub zewnętrzne, przy użyciu miejsca na dysku do przechowywania kopii początkowej. Na przykład jeśli chcesz utworzyć kopię zapasową na serwerze plików 500 GB, upewnij się, że obszar przejściowy co najmniej 500 GB. (Mniejszą ilość jest używany z powodu stosowania kompresji).
* Podczas wysyłania dysków na platformie Azure, użyj tylko 2,5 SSD lub 2.5 cala lub 3,5 cala SATA II/III wewnętrzne dyski twarde. Można użyć dysków twardych do 10 TB. Sprawdź [dokumentacji usługi Azure Import/Export](../storage/common/storage-import-export-requirements.md#supported-hardware) o najnowszym zestawie dysków obsługiwanych przez usługę.
* Dyski SATA muszą być podłączone do komputera (określane jako *komputera kopiowania*) lokalizacji kopiowania danych kopii zapasowej z *lokalizacji tymczasowej* do SATA odbywa się dyski. Upewnij się, że funkcja BitLocker jest włączona na *komputera kopiowania*.

## <a name="workflow"></a>Przepływ pracy
W tej sekcji opisano przepływ pracy w trybie offline z kopii zapasowej, dzięki czemu dane mogą dostarczone do centrum danych platformy Azure i przekazany do usługi Azure Storage. Jeśli masz pytania dotyczące usługi Import lub dowolnego aspektu procesu, zobacz [zaimportować dokumentacji Przegląd usługi](../storage/common/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Inicjowanie kopii zapasowej offline
1. Podczas planowania kopii zapasowej na agenta usług MARS zostanie wyświetlony następujący ekran.

    ![Ekran importowania](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

   Opis danych wejściowych jest następująca:

    * **Lokalizacja tymczasowa**: Lokalizacja magazynu tymczasowego, na którym jest zapisany początkowa kopia zapasowa. Lokalizacja tymczasowa mogą znajdować się na udziale sieciowym lub na komputerze lokalnym. Jeśli kopia komputera i komputera źródłowego są różne, zalecane jest, określ pełną ścieżkę sieciową lokalizacji przejściowej.
    * **Konto magazynu w usłudze Azure Resource Manager**: Nazwa usługi Resource Manager typu konta magazynu (ogólnego przeznaczenia w wersji 1 lub ogólnego przeznaczenia, wersja 2) w dowolnej subskrypcji platformy Azure.
    * **Azure Storage Container**: Nazwa magazynu docelowego obiektu blob na koncie usługi Azure Storage, którego dane kopii zapasowej jest importowany są przenoszone do magazynu usługi Recovery Services.
    * **Identyfikator subskrypcji platformy Azure**: Identyfikator subskrypcji platformy Azure, w której tworzone jest konto usługi Azure Storage.
    * **Nazwa zadania importowania platformy Azure**: Unikatowa nazwa importu platformy Azure, które usługi i usługi Azure Backup śledzić transferu danych na dyskach na platformie Azure. 
  
   Podaj dane wejściowe na ekranie, a następnie kliknij przycisk **dalej**. Zapisz podane *Lokalizacja tymczasowa* i *nazwę zadania importowania platformy Azure*, jak te informacje są wymagane, aby przygotować dyski.

2. Po wyświetleniu monitu zaloguj się do subskrypcji platformy Azure. Musisz się zalogować, aby usługa Azure Backup można utworzyć aplikacji usługi Azure Active Directory i zapewniają wymagane uprawnienia dostępu do usługi Azure Import.

    ![Utwórz kopię zapasową teraz](./media/backup-azure-backup-import-export/azurelogin.png)

3. Ukończenia przepływu pracy, a następnie kliknij w konsoli agenta usługi Kopia zapasowa Azure **Utwórz kopię zapasową teraz**.

    ![Utwórz kopię zapasową teraz](./media/backup-azure-backup-import-export/backupnow.png)

4. W kreatorze na stronie potwierdzenia kliknij **Utwórz kopię zapasową**. Tworzenie początkowej kopii zapasowej są zapisywane do obszaru przejściowego jako część instalacji.

   ![Upewnij się, że jesteś gotowy do usługi Kopia zapasowa teraz](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    Po zakończeniu operacji, lokalizacji przejściowej jest gotowa do użycia w celu przygotowania dysku.

   ![Utwórz kopię zapasową teraz](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Przygotowywanie dysków SATA i dostarczanie na platformę Azure
*AzureOfflineBackupDiskPrep* narzędzie przygotowuje dyski SATA, które są wysyłane do najbliższego centrum danych platformy Azure. To narzędzie jest dostępne w katalogu instalacji agenta usługi Kopia zapasowa Azure (w następującej ścieżce):

   *\Microsoft azure Recovery Services Agent\Utils\\*

1. Przejdź do katalogu i skopiuj **AzureOfflineBackupDiskPrep** katalogu do innego komputera, na którym podłączonych dysków SATA. Na komputerze z podłączonych dysków SATA należy zapewnić:

   * Komputer kopiowania ma dostęp do lokalizacji tymczasowej dla przepływu pracy w trybie offline rozmieszczania przy użyciu tej samej ścieżki sieciowej została podana w **inicjowanie kopii zapasowej offline** przepływu pracy.
   * Funkcja BitLocker jest włączona na komputerze kopiowania.
   * Zainstalowano program Azure PowerShell numer 3.7.0.
   * Najnowsze zgodne przeglądarki (Microsoft Edge lub Internet Explorer 11) są zainstalowane, i języka JavaScript jest włączony. 
   * Komputer kopiowania można uzyskać dostęp do witryny Azure portal. Jeśli to konieczne, komputer kopia może być taka sama jak komputera źródłowego.
    
     > [!IMPORTANT] 
     > Jeśli komputer źródłowy jest maszyną wirtualną, na komputerze kopii musi być inny serwer fizyczny lub komputerze klienckim z komputera źródłowego.

2. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień na tym komputerze kopiowania *AzureOfflineBackupDiskPrep* narzędzie katalogu jako bieżący katalog, a następnie uruchom następujące polecenie:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Parametr | Opis |
    | --- | --- |
    | s:&lt;*ścieżka lokalizacji przemieszczania*&gt; |Wymagane dane wejściowe umożliwia podanie ścieżki do lokalizacji tymczasowej, które wprowadziłeś w **inicjowanie kopii zapasowej offline** przepływu pracy. |
    | p:&lt;*ścieżkę do PublishSettingsFile*&gt; |Opcjonalne dane wejściowe, które służy do zapewnienia ścieżkę do **ustawień publikowania platformy Azure** plików, które wprowadziłeś w **inicjowanie kopii zapasowej offline** przepływu pracy. |

    Po uruchomieniu polecenia, narzędzie żądań wybór zadania importu platformy Azure, do którego odnosi się do dysków, które muszą być przygotowane. Jeśli tylko zadania importu pojedynczego jest skojarzony z podanej lokalizacji tymczasowej, pojawia się ekran podobne do pokazanego poniżej.

    ![Usługa Azure przygotowanie dysku narzędzia w danych wejściowych](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

3. Wprowadź literę dysku bez dwukropka końcowe dla zainstalowanego dysku, który ma zostać przygotowana do przeniesienia na platformę Azure. 
4. Potwierdź formatowania dysku, po wyświetleniu monitu.
5. Monit do logowania się do subskrypcji platformy Azure. Wprowadź swoje poświadczenia.

    ![Usługa Azure przygotowanie dysku narzędzia w danych wejściowych](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    Narzędzie następnie rozpoczyna się przygotować dysku i skopiowanie danych kopii zapasowej. Może być konieczne dołączanie dodatkowych dysków, po wyświetleniu monitu przez narzędzie, w przypadku, gdy podana dysku nie ma wystarczającej ilości miejsca dla danych kopii zapasowej. <br/>

    Na koniec pomyślne wykonanie narzędzia wiersza polecenia zawiera trzy informacje:
   1. Co najmniej jeden dysk, podane są przygotowywane do wysłania do platformy Azure. 
   2. Otrzymasz potwierdzenie, że utworzono zadanie importu. Zadania importu używa podanej nazwy.
   3. Narzędzie wyświetla adres wysyłkowy dla centrum danych platformy Azure.

      ![Zakończenie przygotowywania dysku platformy Azure](./media/backup-azure-backup-import-export/console2.png)<br/>

6. Po zakończeniu wykonywania polecenia należy zaktualizować informacje o wysyłce.

7. Odeślij dyski z podanym przez narzędzie adres i Zachowaj numer śledzenia do użytku w przyszłości.

   > [!IMPORTANT] 
   > Żadne dwa zadania importowania platformy Azure może mieć taką samą liczbę śledzenia. Upewnij się, że dyski przygotowane przez narzędzie w ramach jednego zadania importu platformy Azure są dostarczane razem w jednym pakiecie oraz czy pojedynczej unikatowy numer identyfikacyjny dla pakietu. Nie wolno łączyć dyski przygotowany w ramach osobnych zadania importu platformy Azure w jednym pakiecie.



## <a name="update-shipping-details-on-the-azure-import-job"></a>Aktualizuj szczegóły dotyczące wysyłki na zadania importu platformy Azure

Poniższa procedura aktualizuje szczegóły wysyłanie zadania importu platformy Azure. Informacje te obejmują szczegółowe informacje dotyczące:
* Nazwa operatora, który dostarcza dysków na platformie Azure
* Zwraca szczegóły wysyłki dla dysków

1. Zaloguj się do subskrypcji platformy Azure.
2. W menu głównym kliknij **wszystkich usług** i w oknie dialogowym z wszystkich usług, wpisz importu. Po wyświetleniu **zadania importu/eksportu**, kliknij go.
    ![Wprowadź informacje dotyczące wysyłki](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    Lista **zadania importu/eksportu** zostanie otwarte menu i zostanie wyświetlona lista wszystkich zadania importu/eksportu w wybranej subskrypcji. 

3. Jeśli masz wiele subskrypcji, koniecznie wybierz subskrypcję używaną do importowania danych kopii zapasowej. Następnie wybierz nowo utworzone zadanie importu, aby otworzyć jego szczegóły.

    ![Przejrzyj informacje dotyczące wysyłki](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

4. W menu Ustawienia zadania importowania kliknij **Zarządzanie informacje dotyczące wysyłki** i wprowadź szczegóły dotyczące wysyłki zwrotnej.

    ![Przechowywanie informacje dotyczące wysyłki](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

5. W przypadku numeru śledzenia od operatora wysyłki, kliknij transparent, na stronie przeglądu zadania importu platformy Azure i wprowadź następujące informacje:

   > [!IMPORTANT] 
   > Upewnij się, że informacje o operatorze i numer śledzenia zostały zaktualizowane w ciągu dwóch tygodni od utworzenia zadania importowania platformy Azure. Błąd weryfikacji tych informacji w ciągu dwóch tygodni może spowodować usunięcie zadania i dyski, które nie są przetwarzane.

   ![Przechowywanie informacje dotyczące wysyłki](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Przechowywanie informacje dotyczące wysyłki](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Czas przetwarzania dysków 
Czas potrzebny do procesu zadania importowania platformy Azure w zależności od różnych czynników, takich jak czas dostawy zadania typu, typu i rozmiaru danych, w której są kopiowane i rozmiaru dysków udostępnionych. Usługa Azure Import/Export nie ma umowy SLA, ale po otrzymaniu dyski usługi dokłada starań, aby zakończenie kopiowania danych kopii zapasowej na koncie magazynu platformy Azure w dniach 7 – 10. 

### <a name="monitoring-azure-import-job-status"></a>Monitorowanie stanu zadaniem importowania platformy Azure
Możesz monitorować stan zadania importu w witrynie Azure portal, przechodząc do **zadania importu/eksportu** strony i wybierając polecenie zadania. Aby uzyskać więcej informacji o stanie zadań importu, zobacz [magazynu Import/Eksport usługi](../storage/common/storage-import-export-service.md) artykułu.

### <a name="complete-the-workflow"></a>Kończenie przepływu pracy
Po pomyślnym ukończeniu zadania importu danych początkowej kopii zapasowej jest dostępna w ramach konta magazynu. W czasie następnej zaplanowanej kopii zapasowej kopia zapasowa Azure kopiuje zawartość danych z konta magazynu do magazynu usługi Recovery Services, jak pokazano poniżej: 

   ![Kopiowanie danych do magazynu usługi Recovery Services](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

W momencie następnej zaplanowanej kopii zapasowej usługi Azure Backup wykonuje przyrostowej kopii zapasowej.

### <a name="cleaning-up-resources"></a>Oczyszczanie zasobów
Po zakończeniu tworzenia początkowej kopii zapasowej, można bezpiecznie usunąć dane zaimportowane do kontenera magazynu Azure i dane kopii zapasowej w lokalizacji przemieszczania.

## <a name="next-steps"></a>Kolejne kroki
* Masz pytania dotyczące usługi Azure Import/Export przepływu pracy, można znaleźć [przesyłanie danych do magazynu obiektów Blob za pomocą usługi Microsoft Azure Import/Export](../storage/common/storage-import-export-service.md).
* Zapoznaj się z sekcją kopie zapasowe offline z usługi Azure Backup [— często zadawane pytania](backup-azure-backup-faq.md) w razie jakichkolwiek pytań o przepływie pracy.

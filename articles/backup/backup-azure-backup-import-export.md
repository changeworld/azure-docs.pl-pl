---
title: Kopia zapasowa Azure — kopia zapasowa Offline lub początkowe umieszczanie za pomocą usługi Import/Eksport Azure
description: Dowiedz się, jak Kopia zapasowa Azure umożliwia wysyłanie danych w sieci za pomocą usługi Import/Eksport Azure. W tym artykule opisano, w trybie offline inicjacją dane początkowej kopii zapasowej za pomocą usługi Azure importu wyeksportować.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 05/17/2018
ms.author: saurse
ms.openlocfilehash: cb37c94d3ec819823083041708cfc28ead0ed5cf
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606141"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Przepływ pracy tworzenia kopii zapasowych w trybie offline w usłudze Azure Backup
Kopia zapasowa Azure zawiera kilka wbudowanych korzyści, które zapisania koszty sieci i magazynu podczas początkowej pełne kopie zapasowe danych na platformie Azure. Początkowa pełnych kopii zapasowych zwykle transfer dużych ilości danych i wymagają większej przepustowości sieci w porównaniu do kolejnych kopii zapasowych, które transfer tylko delty/przyrostowa. W procesie wstępnego wypełniania w trybie offline kopia zapasowa Azure umożliwia dysków przekazać dane kopii zapasowej w trybie offline na platformie Azure.

Kopia zapasowa Azure w trybie offline wstępne wypełnianie procesu jest ściśle zintegrowany z [usługi Import/Eksport Azure](../storage/common/storage-import-export-service.md) , umożliwia transfer danych początkowej kopii zapasowej na platformie Azure przy użyciu dysków. Jeśli masz terabajtów (tabel) dane początkowej kopii zapasowej, który musi być przesyłane przez sieci dużymi opóźnieniami i niskiej przepustowości sieci, można użyć przepływu pracy w trybie offline wstępne wypełnianie na potrzeby wysłania początkowej kopii zapasowej na jeden lub więcej dysków twardych, do centrum danych Azure. Poniższa ilustracja zawiera omówienie kroków w przepływie pracy.

  ![Omówienie procesu przepływu pracy w trybie offline importu](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

Proces tworzenia kopii zapasowej w trybie offline obejmuje następujące kroki:

1. Zamiast wysyłać dane kopii zapasowej za pośrednictwem sieci, należy zapisać dane kopii zapasowej do lokalizacji tymczasowej.
2. Narzędzie AzureOfflineBackupDiskPrep zapisać danych w tymczasowej lokalizacji na jeden lub więcej dysków SATA.
3. W ramach przygotowania narzędzie AzureOfflineBackupDiskPrep tworzy zadania importu platformy Azure. Wyślij dyski SATA do najbliższego centrum danych Azure, a odwołania zadania importu nawiązać działania.
4. W centrum danych Azure dane na dyskach zostaną skopiowane do konta magazynu platformy Azure.
5. Kopia zapasowa Azure kopiuje dane kopii zapasowej z konta magazynu do magazynu usług odzyskiwania i przyrostowych kopii zapasowych.

## <a name="supported-configurations"></a>Obsługiwane konfiguracje 
Następujące funkcje usługi Kopia zapasowa Azure lub obciążeń obsługuje korzystanie z trybu Offline z kopii zapasowej.

> [!div class="checklist"]
> * Wykonywanie kopii zapasowej plików i folderów w agencie usług odzyskiwania Azure firmy Microsoft (MARS), nazywany również agenta usługi Kopia zapasowa Azure. 
> * Kopie zapasowe wszystkich obciążeń oraz pliki z System Center Data Protection Manager (SC DPM) 
> * Tworzenie kopii zapasowej wszystkich plików i obciążeń z serwer kopii zapasowej Microsoft Azure <br/>

   > [!NOTE]
   > Kopia zapasowa offline nie jest obsługiwana dla kopii zapasowych stanu systemu odbywa się za pomocą agenta usługi Kopia zapasowa Azure. 

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Wymagania wstępne

  > [!NOTE]
  > Następujące wymagania wstępne i przepływ pracy dotyczą tylko w trybie Offline tworzenia kopii zapasowej plików i folderów przy użyciu [najnowsza wersja agenta MARS](https://aka.ms/azurebackup_agent). Aby wykonać kopie zapasowe w trybie Offline dla obciążeń przy użyciu programu System Center DPM lub serwer kopii zapasowej Azure, zapoznaj się [w tym artykule](backup-azure-backup-server-import-export-.md). 

Przed rozpoczęciem przepływu pracy w trybie Offline z kopii zapasowej, należy wykonać następujące wymagania wstępne: 
* Utwórz [magazyn usług odzyskiwania i](backup-azure-recovery-services-vault-overview.md). Aby utworzyć magazyn, skorzystaj z procedury opisanej w [w tym artykule](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Upewnij się, że tylko [najnowszą wersję agenta usługi Kopia zapasowa Azure](https://aka.ms/azurebackup_agent) został zainstalowany na komputerze klienckim z systemem Windows Server i Windows, zgodnie z wymaganiami, a komputer jest zarejestrowany w magazynie usług odzyskiwania.
* Program Azure PowerShell 3.7.0 lub większa jest wymagane na komputerze, na którym jest uruchomiony agent usługi Kopia zapasowa Azure. Zaleca się możesz [zainstaluj najnowszą wersję programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0).
* Na komputerze, na którym jest uruchomiony agent usługi Kopia zapasowa Azure upewnij się, Microsoft Edge lub Internet Explorer 11 jest zainstalowany i włączyć obsługę języka JavaScript. 
* Tworzenie konta usługi Azure Storage w tej samej subskrypcji co magazyn usług odzyskiwania. 
* Upewnij się, że masz [niezbędne uprawnienia](../azure-resource-manager/resource-group-create-service-principal-portal.md) do utworzenia aplikacji usługi Azure Active Directory. Przepływ pracy w trybie Offline Backup tworzy aplikację usługi Azure Active Directory w subskrypcję skojarzoną z kontem magazynu platformy Azure. Celem aplikacji jest zapewnienie kopia zapasowa Azure bezpiecznego i zakresem dostępu do usługi Azure importu, wymagane dla przepływu pracy w trybie Offline z kopii zapasowej. 
* Rejestrowanie dostawcy zasobów Microsoft.ImportExport z subskrypcją zawierającą konta usługi Magazyn Azure. Aby zarejestrować dostawcę zasobów:
    1. W menu głównym kliknij **subskrypcje**.
    2. Gdy subskrybujesz wiele subskrypcji Wybierz subskrypcję, używanej do tworzenia kopii zapasowej w trybie offline. Jeśli używasz tylko jedną subskrypcję, zostanie wyświetlony subskrypcji.
    3. W menu subskrypcji, kliknij przycisk **dostawców zasobów** Aby wyświetlić listę dostawców.
    4. Na liście dostawców przewiń w dół Microsoft.ImportExport. Jeśli stan jest NotRegistered, kliknij **zarejestrować**.
    ![Rejestrowanie dostawcy zasobów](./media/backup-azure-backup-import-export/registerimportexport.png)
* Utworzono tymczasowej lokalizacji, która może być udział sieciowy lub dodatkowego dysku na komputerze, wewnętrzne lub zewnętrzne, z miejsca na dysku do przechowywania kopii początkowej. Na przykład jeśli chcesz utworzyć kopię zapasową na serwerze plików 500 GB, upewnij się, że obszaru przemieszczania jest co najmniej 500 GB. (Mniejszą ilość jest używany z powodu kompresji).
* Podczas wysyłania dysków na platformie Azure, użyj tylko 2,5 SSD lub 2.5 cala lub 3,5 cala SATA II/III wewnętrzne dyski twarde. Dyski twarde można użyć do 10 TB. Sprawdź [dokumentację usługi Import/Eksport Azure](../storage/common/storage-import-export-requirements.md#supported-hardware) najnowszego zestawu dysków, obsługiwanych przez usługę.
* Dyski SATA musi być podłączony do komputera (nazywane *komputera kopii*) z jakiej lokalizacji kopię danych kopii zapasowej z *lokalizacji przejściowej* do SATA odbywa się dyski. Upewnij się, że funkcja Bitlocker jest włączona na *komputera kopiowania*.

## <a name="workflow"></a>Przepływ pracy
W tej sekcji opisano przepływ pracy w trybie offline z kopii zapasowej, aby dane można dostarczyć do centrum danych Azure i przekazane do magazynu Azure. Jeśli masz pytania dotyczące usługi importu lub dowolnego aspektu procesu, zobacz [zaimportować dokumentację Omówienie usługi](../storage/common/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Inicjowanie kopii zapasowej w trybie offline
1. Podczas planowania kopii zapasowej na agenta MARS, zostanie wyświetlony następujący ekran.

    ![Importuj ekranu](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

  Opis danych wejściowych jest następujący:

    * **Miejsce przemieszczania**: lokalizacji tymczasowej, do którego początkowa kopia zapasowa jest zapisywany. Lokalizacja przemieszczania mogą znajdować się na udziału sieciowego lub na komputerze lokalnym. Jeśli kopia komputera i komputera źródłowego są różne, zaleca się czy określić pełną ścieżkę sieciową do lokalizacji tymczasowej.
    * **Konta magazynu usługi Azure Resource Manager**: Nazwa typu konta magazynu Menedżera zasobów w dowolnej subskrypcji platformy Azure.
    * **Kontener magazynu Azure**: Nazwa magazynu docelowego obiektu blob na koncie magazynu Azure, której dane kopii zapasowej jest importowany są przenoszone do magazynu usług odzyskiwania.
    * **Identyfikator subskrypcji platformy Azure**: identyfikator subskrypcji Azure, w której jest utworzone konto magazynu Azure.
    * **Nazwa zadania importowania platformy Azure**: unikatową nazwę importu platformy Azure, które usługi i kopia zapasowa Azure śledzić transferu danych przesyłanych na dyskach w systemie Azure. 
  
  Podaj dane wejściowe na ekranie, a następnie kliknij przycisk **dalej**. Zapisz dostarczonych *lokalizacji tymczasowej* i *Nazwa zadania importowania platformy Azure*, jak te informacje są niezbędne, aby przygotować dyski.

2. Po wyświetleniu monitu zaloguj się do subskrypcji platformy Azure. Musisz się zalogować, aby kopia zapasowa Azure można utworzyć aplikację usługi Azure Active Directory i zapewniają wymagane uprawnienia dostępu do usługi importu platformy Azure.

    ![Wykonaj kopię zapasową](./media/backup-azure-backup-import-export/azurelogin.png)

3. Ukończenia przepływu pracy, a następnie kliknij w konsoli agenta usługi Kopia zapasowa Azure **wykonaj kopię zapasową teraz**.

    ![Wykonaj kopię zapasową](./media/backup-azure-backup-import-export/backupnow.png)

4. W kreatorze na stronie potwierdzenia kliknij **kopię zapasową**. Tworzenie początkowej kopii zapasowej są zapisywane do obszaru przemieszczania jako część instalacji.

   ![Upewnij się, że wszystko jest gotowe do tworzenia kopii zapasowej teraz](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    Po zakończeniu operacji, przemieszczana lokalizacja jest gotowy do użycia w celu przygotowania dysku.

   ![Wykonaj kopię zapasową](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Przygotuj dyski SATA i wysłać do platformy Azure
*AzureOfflineBackupDiskPrep* narzędzie przygotowuje dyski SATA, które są wysyłane do najbliższego centrum danych Azure. To narzędzie jest dostępny w katalogu instalacji agenta usługi Kopia zapasowa Azure (w następującej ścieżce):

   *Agent\Utils usług odzyskiwania azure \Microsoft\\*

1. Przejdź do katalogu i skopiuj **AzureOfflineBackupDiskPrep** katalogu do innego komputera, w której są podłączone dysków SATA. Na komputerze z połączonych SATA zapewnia:

    * Komputer kopii można uzyskać dostęp do tymczasowej lokalizacji dla przepływu pracy w trybie offline wstępne wypełnianie przy użyciu tej samej lokalizacji sieciowej, która została dostarczona w **zainicjować kopię zapasową offline** przepływu pracy.
    * Funkcja BitLocker jest włączona na komputerze kopiowania.
    * Program Azure PowerShell 3.7.0, lub większą, jest zainstalowana.
    * Zainstalowano najnowszych przeglądarkach zgodne (Edge lub Internet Explorer 11) i włączyć obsługę języka JavaScript. 
    * Komputer kopii mogą uzyskać dostęp do portalu Azure. W razie potrzeby kopiowania komputer może być taka sama, co na komputerze źródłowym.
    
    > [!IMPORTANT] 
    > Jeśli komputer źródłowy jest maszyną wirtualną, komputer kopii musi być inny serwer fizyczny lub komputera klienckiego z komputera źródłowego.

2. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień na komputerze kopii *AzureOfflineBackupDiskPrep* katalogu narzędzia jako bieżący katalog, a następnie uruchom następujące polecenie:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Parametr | Opis |
    | --- | --- |
    | s:&lt;*ścieżka lokalizacji przemieszczania*&gt; |Obowiązkowe dane wejściowe, używane do Podaj ścieżkę do lokalizacji tymczasowej, wprowadzony w **zainicjować kopię zapasową offline** przepływu pracy. |
    | p:&lt;*ścieżkę do PublishSettingsFile*&gt; |Opcjonalne dane wejściowe, służący do Podaj ścieżkę do **ustawień publikowania platformy Azure** pliku wprowadzony w **zainicjować kopię zapasową offline** przepływu pracy. |

    Po uruchomieniu polecenia narzędzie żądań wybór zadania importu platformy Azure, umożliwiająca dyski, które muszą być przygotowane. Jeśli tylko zadania importu pojedynczego jest skojarzony z podanej lokalizacji tymczasowej, zostanie wyświetlony ekran podobny do tego, który jest zgodny.

    ![Azure wprowadzania narzędzie przygotowanie dysku](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

3. Wprowadź literę dysku bez dwukropka końcowe dla zainstalowanego dysku, który ma zostać przygotowane do przeniesienia do usługi Azure. 
4. Potwierdź formatowania dysku po wyświetleniu monitu.
5. Zostanie wyświetlony monit logowania się do subskrypcji platformy Azure. Wprowadź swoje poświadczenia.

    ![Azure wprowadzania narzędzie przygotowanie dysku](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    Narzędzie następnie rozpoczyna się przygotować dysku i skopiowanie danych kopii zapasowej. Może być konieczne dołączanie dodatkowych dysków, po wyświetleniu monitu przez narzędzie, w przypadku udostępnionego dysku nie ma wystarczającą ilość miejsca na dane kopii zapasowej. <br/>

    Na koniec pomyślne wykonanie narzędzia wiersza polecenia zawiera trzy informacje:
    1. Jeden lub więcej dysków, podane są przygotowywane do wysyłki na platformie Azure. 
    2. Pojawi się potwierdzenie, że utworzono zadanie importu. Zadania importu użyje nazwy podane.
    3. Narzędzie wyświetla adres wysyłkowy dla centrum danych Azure.

    ![Zakończenie przygotowywania dysku platformy Azure](./media/backup-azure-backup-import-export/console2.png)<br/>

6. Po zakończeniu wykonywania polecenia można zaktualizować informacji dotyczących wysyłki.

7. Wysłać dysków na adres zapewnianej przez narzędzie i Zachowaj numer śledzenia do użytku w przyszłości.

   > [!IMPORTANT] 
   > Nie dwa zadania importowania platformy Azure może mieć ten sam numer śledzenia. Upewnij się, że dyski przygotowane przez narzędzie z jednego zadania importu platformy Azure są wysłane razem w jednym pakiecie i że istnieje jeden unikatowy numer identyfikacyjny dla tego pakietu. Nie wolno łączyć dyski przygotowany w ramach oddzielnego zadania importu platformy Azure w jednym pakiecie.



## <a name="update-shipping-details-on-the-azure-import-job"></a>Aktualizuj szczegóły wysyłki na zadania importu platformy Azure

Poniższa procedura aktualizacji szczegóły wysyłanie zadania importu platformy Azure. Te informacje zawiera szczegóły dotyczące następujących zagadnień:
* Nazwa operatora, który dostarcza dyski na platformie Azure
* Zwraca szczegóły wysyłki dla dysków

1. Zaloguj się do subskrypcji platformy Azure.
2. W menu głównym kliknij **wszystkich usług** i w oknie dialogowym z wszystkich usług, wpisz importu. Po wyświetleniu **zadania importu/eksportu**, kliknij ją.
    ![Wprowadzanie wysyłania informacji](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    Lista **zadania importu/eksportu** zostanie otwarte menu i zostanie wyświetlona lista wszystkich zadań importu/eksportu w wybranej subskrypcji. 

3. Jeśli masz wiele subskrypcji, należy wybrać subskrypcję służą do importowania danych kopii zapasowej. Następnie wybierz nowo utworzony zadania importu, aby otworzyć jego szczegóły.

    ![Przejrzyj informacje o wysyłce](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

4. W menu Ustawienia dla zadania importu, kliknij przycisk **Zarządzanie wysyłanie informacji o** , a następnie wprowadź szczegóły dotyczące zwrotu wysyłki.

    ![Przechowywanie wysyłania informacji](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

5. Jeśli masz numer od operatora wysyłanie kliknij banerze na stronie przeglądu zadania importu platformy Azure, a następnie wprowadź następujące informacje:

   > [!IMPORTANT] 
   > Upewnij się, że informacje operatora i numer śledzenia są aktualizowane w ciągu dwóch tygodni Tworzenie zadania importowania platformy Azure. Błąd weryfikacji tych informacji w ciągu dwóch tygodni może spowodować zadania usuwany, a dyski nie są przetwarzane.

   ![Przechowywanie wysyłania informacji](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Przechowywanie wysyłania informacji](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Czas przetwarzania na dyskach 
Czas potrzebny do procesu zadania importowania platformy Azure może być różna w zależności od różnych czynników, takich jak czas dostawy zadania typ, typ i rozmiar danych, w której są kopiowane i rozmiaru dysków podane. Usługa Import/Eksport Azure nie ma umowy dotyczącej poziomu usług, ale po otrzymaniu dyski usługi dokłada starań, aby ukończyć kopii kopii zapasowej danych na koncie magazynu Azure w ciągu 7 do 10 dni. 

### <a name="monitoring-azure-import-job-status"></a>Monitorowanie stanu zadania importowania platformy Azure
Można monitorować stan zadania importu z portalu Azure, przechodząc do **zadania importu/eksportu** strony i wybierając polecenie zadania. Aby uzyskać więcej informacji o stanie zadania importu, zobacz [wyeksportować importu magazynu usługi](../storage/common/storage-import-export-service.md) artykułu.

### <a name="complete-the-workflow"></a>Ukończenia przepływu pracy
Po pomyślnym zakończeniu zadania importu, dane początkowej kopii zapasowej jest dostępna na koncie magazynu. Podczas następnego zaplanowanego tworzenia kopii zapasowej kopia zapasowa Azure kopiuje zawartość dane z konta magazynu do magazynu usług odzyskiwania, jak pokazano poniżej: 

   ![Kopiowanie danych do magazynu usług odzyskiwania](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

Podczas następnego zaplanowanego tworzenia kopii zapasowej kopia zapasowa Azure wykonuje przyrostowej kopii zapasowej.

### <a name="cleaning-up-resources"></a>Oczyszczanie zasobów
Po zakończeniu tworzenia początkowej kopii zapasowej, można bezpiecznie usunąć dane zaimportowane do kontenera magazynu Azure i dane kopii zapasowej w lokalizacji tymczasowej.

## <a name="next-steps"></a>Kolejne kroki
* Wszelkie pytania dotyczące przepływu pracy Import/Eksport Azure można znaleźć w temacie [transferu danych do magazynu obiektów Blob za pomocą usługi Import/Eksport Microsoft Azure](../storage/common/storage-import-export-service.md).
* Zapoznaj się z sekcją w trybie offline z kopii zapasowej systemu Azure Backup [— często zadawane pytania](backup-azure-backup-faq.md) wszelkie pytania dotyczące przepływu pracy.

---
title: Tworzenie kopii zapasowych w trybie offline za pomocą usługi Azure Backup Import/Export
description: Dowiedz się, jak Azure Backup umożliwia wysyłanie danych z sieci przy użyciu usługi Azure Import/Export. W tym artykule opisano umieszczanie w trybie offline początkowych danych kopii zapasowej za pomocą usługi eksportowej platformy Azure.
ms.reviewer: saurse
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/17/2018
ms.author: dacurwin
ms.openlocfilehash: 1d3dc50d141a4e1d2864a56aff5c3adb3d2ca0b1
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954871"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Przepływ pracy tworzenia kopii zapasowych w trybie offline w usłudze Azure Backup
Azure Backup ma kilka wbudowanych wydajności, które oszczędzają koszty związane z siecią i magazynem podczas wstępnej pełnej kopii zapasowej danych na platformie Azure. Wstępne pełne kopie zapasowe zwykle przesyłają duże ilości danych i wymagają większej przepustowości sieci w porównaniu z kolejnymi kopiami zapasowymi, które przesyłają tylko różnice/przyrosty. W procesie umieszczania w trybie offline, Azure Backup mogą używać dysków do przekazywania danych kopii zapasowej w trybie offline do platformy Azure.

Proces rozsadzenia w trybie offline jest ściśle zintegrowany z [usługą Azure Import/Export](../storage/common/storage-import-export-service.md) , która umożliwia transfer danych początkowej kopii zapasowej na platformę Azure przy użyciu dysków. Azure Backup Jeśli masz terabajty (TBs) początkowych danych kopii zapasowej, które muszą być transferowane za pośrednictwem sieci o dużej opóźnieniu i o niskiej przepustowości, możesz użyć przepływu pracy w trybie offline, aby wysłać początkową kopię zapasową na co najmniej jednym dysku twardym do centrum danych platformy Azure. Poniższy obraz zawiera przegląd kroków w przepływie pracy.

  ![przegląd procesu przepływu pracy importowania w trybie offline](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

Proces tworzenia kopii zapasowej w trybie offline obejmuje następujące kroki:

1. Zamiast wysyłać dane kopii zapasowej za pośrednictwem sieci, Zapisz dane kopii zapasowej w lokalizacji tymczasowej.
2. Użyj narzędzia AzureOfflineBackupDiskPrep, aby zapisać dane w lokalizacji tymczasowej do co najmniej jednego dysku SATA.
3. W ramach pracy przygotowawczej narzędzie AzureOfflineBackupDiskPrep tworzy zadanie importu platformy Azure. Wyślij dyski SATA do najbliższego centrum danych platformy Azure i odwołuje się do zadania importowania, aby połączyć działania.
4. W centrum danych Azure dane na dyskach są kopiowane do konta usługi Azure Storage.
5. Azure Backup kopiuje dane kopii zapasowej z konta magazynu do magazynu Recovery Services, a przyrostowe kopie zapasowe są zaplanowane.

## <a name="supported-configurations"></a>Obsługiwane konfiguracje 
Następujące Azure Backup funkcje lub obciążenia obsługują korzystanie z kopii zapasowej offline.

> [!div class="checklist"]
> * Tworzenie kopii zapasowych plików i folderów za pomocą agenta Microsoft Azure Recovery Services (MARS), nazywanego również agentem Azure Backup. 
> * Tworzenie kopii zapasowych wszystkich obciążeń i plików za pomocą programu System Center Data Protection Manager (SC DPM) 
> * Tworzenie kopii zapasowych wszystkich obciążeń i plików z Microsoft Azure Backup Server <br/>

   > [!NOTE]
   > Kopia zapasowa offline nie jest obsługiwana dla kopii zapasowych stanu systemu wykonywanych przy użyciu agenta Azure Backup. 

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Wymagania wstępne

  > [!NOTE]
  > Poniższe wymagania wstępne i przepływ pracy dotyczą tylko kopii zapasowych plików i folderów w trybie offline przy użyciu [najnowszego agenta Mars](https://aka.ms/azurebackup_agent). Aby wykonywać kopie zapasowe w trybie offline dla obciążeń przy użyciu programu System Center DPM lub Azure Backup Server, zapoznaj się z [tym artykułem](backup-azure-backup-server-import-export-.md). 

Przed zainicjowaniem przepływu pracy kopii zapasowej offline wykonaj następujące wymagania wstępne: 
* Utwórz [magazyn Recovery Services](backup-azure-recovery-services-vault-overview.md). Aby utworzyć magazyn, zapoznaj się z instrukcjami w [tym artykule](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault) .
* Upewnij się, że na komputerze z systemem Windows Server/kliencie systemu Windows została zainstalowana tylko [Najnowsza wersja agenta Azure Backup](https://aka.ms/azurebackup_agent) , a komputer jest zarejestrowany w magazynie Recovery Services.
* Na komputerze z uruchomionym agentem Azure Backup jest wymagany program Azure PowerShell 3.7.0. Zaleca się pobranie i [zainstalowanie wersji 3.7.0 Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).
* Na komputerze z uruchomionym agentem Azure Backup upewnij się, że jest zainstalowany program Microsoft Edge lub Internet Explorer 11, a język JavaScript jest włączony. 
* Utwórz konto usługi Azure Storage w tej samej subskrypcji co magazyn Recovery Services. 
* Upewnij się, że masz odpowiednie [uprawnienia](../active-directory/develop/howto-create-service-principal-portal.md) do tworzenia aplikacji Azure Active Directory. Przepływ pracy kopii zapasowej offline tworzy aplikację Azure Active Directory w subskrypcji skojarzonej z kontem usługi Azure Storage. Celem aplikacji jest zapewnienie Azure Backup z bezpiecznym i dostępnym zakresem do usługi Azure import, która jest wymagana dla przepływu pracy kopii zapasowej offline. 
* Zarejestruj dostawcę zasobów Microsoft. ImportExport z subskrypcją zawierającą konto usługi Azure Storage. Aby zarejestrować dostawcę zasobów:
    1. W menu głównym kliknij pozycję **subskrypcje**.
    2. Jeśli subskrybujesz wiele subskrypcji, wybierz subskrypcję, która jest używana do tworzenia kopii zapasowych w trybie offline. Jeśli używasz tylko jednej subskrypcji, Twoja subskrypcja zostanie wyświetlona.
    3. W menu subskrypcja kliknij pozycję **dostawcy zasobów** , aby wyświetlić listę dostawców.
    4. Na liście dostawców przewiń w dół do Microsoft. ImportExport. Jeśli stan to NotRegistered, kliknij pozycję **zarejestruj**.
    ![Rejestrowanie dostawcy zasobów](./media/backup-azure-backup-import-export/registerimportexport.png)
* Tworzona jest lokalizacja tymczasowa, która może być udziałem sieciowym lub dodatkowym dyskiem na komputerze, wewnętrznym lub zewnętrznym, z wystarczającą ilością miejsca na dysku do przechowywania kopii początkowej. Na przykład, jeśli próbujesz utworzyć kopię zapasową serwera plików 500-GB, upewnij się, że obszar przejściowy jest co najmniej 500 GB. (Mniejsza ilość jest używana z powodu kompresji).
* Podczas wysyłania dysków na platformę Azure należy używać tylko dysków SSD o 2,5 cala lub 2,5 cala lub 3,5 SATA II/III. Możesz użyć dysków twardych o pojemności do 10 TB. Zapoznaj się z [dokumentacją usługi Azure Import/Export](../storage/common/storage-import-export-requirements.md#supported-hardware) , aby zapoznać się z najnowszym zestawem dysków obsługiwanych przez usługę.
* Dyski SATA muszą być połączone z komputerem (nazywanym *komputerem kopiującym*), z którego są wykonywane kopie kopii zapasowych z *lokalizacji tymczasowej* do dysków SATA. Upewnij się, że funkcja BitLocker jest włączona na *komputerze kopiowania*.

## <a name="workflow"></a>Przepływ pracy
W tej sekcji opisano przepływ pracy offline kopii zapasowych, dzięki czemu dane można dostarczyć do centrum danych platformy Azure i przekazać je do usługi Azure Storage. Jeśli masz pytania dotyczące usługi import lub dowolnego aspektu procesu, zapoznaj się z [dokumentacją usługi importowania usług](../storage/common/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Inicjowanie kopii zapasowej offline
1. Po zaplanowaniu kopii zapasowej w agencie MARS zobaczysz następujący ekran.

    ![Ekran importowania](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

   Opis danych wejściowych jest następujący:

    * **Lokalizacja tymczasowa**: Tymczasowa lokalizacja przechowywania, w której jest zapisywana początkowa kopia zapasowa. Lokalizacja przejściowa może znajdować się w udziale sieciowym lub na komputerze lokalnym. Jeśli komputer kopii i komputer źródłowy są inne, zalecamy określenie pełnej ścieżki sieciowej lokalizacji tymczasowej.
    * **Azure Resource Manager konto magazynu**: Nazwa Menedżer zasobówgo typu konta magazynu (ogólnego przeznaczenia w wersji 1 lub 2) w dowolnej subskrypcji platformy Azure.
    * **Kontener usługi Azure Storage**: Nazwa docelowego obiektu blob magazynu na koncie usługi Azure Storage, do którego są importowane dane kopii zapasowej, przed skopiowaniem do magazynu Recovery Services.
    * **Identyfikator subskrypcji platformy Azure**: Identyfikator subskrypcji platformy Azure, w której tworzone jest konto usługi Azure Storage.
    * **Nazwa zadania importowania platformy Azure**: Unikatowa nazwa, za pomocą której usługa Azure import i Azure Backup śledzi transfer danych wysyłanych na dyskach na platformę Azure. 
  
   Podaj dane wejściowe na ekranie i kliknij przycisk **dalej**. Zapisz podaną *lokalizację przemieszczania* i *nazwę zadania importowania platformy Azure*, ponieważ te informacje są wymagane do przygotowania dysków.

2. Po wyświetleniu monitu zaloguj się do subskrypcji platformy Azure. Musisz się zalogować, aby Azure Backup mógł utworzyć aplikację Azure Active Directory i udostępnić wymagane uprawnienia dostępu do usługi Azure import.

    ![Utwórz kopię zapasową teraz](./media/backup-azure-backup-import-export/azurelogin.png)

3. Ukończ przepływ pracy, a następnie w konsoli agenta Azure Backup kliknij pozycję **Utwórz kopię zapasową teraz**.

    ![Utwórz kopię zapasową teraz](./media/backup-azure-backup-import-export/backupnow.png)

4. Na stronie Potwierdzenie kreatora kliknij pozycję **Utwórz kopię zapasową**. Początkowa kopia zapasowa jest zapisywana w obszarze tymczasowym w ramach instalacji.

   ![Potwierdź, że wszystko jest gotowe do wykonania kopii zapasowej teraz](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    Po zakończeniu operacji lokalizacja tymczasowa jest gotowa do użycia podczas przygotowywania dysku.

   ![Utwórz kopię zapasową teraz](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Przygotowanie dysków SATA i dostarczenie do platformy Azure
Narzędzie *AzureOfflineBackupDiskPrep* przygotowuje dyski SATA, które są wysyłane do najbliższego centrum danych platformy Azure. To narzędzie jest dostępne w katalogu instalacji agenta Azure Backup (w następującej ścieżce):

   *\Microsoft Azure Recovery Services Agent\Utils\\*

1. Przejdź do katalogu i Skopiuj katalog **AzureOfflineBackupDiskPrep** do innego komputera, na którym są połączone stacje dysków SATA. Na komputerze z podłączonymi dyskami SATA upewnij się, że:

   * Komputer kopii może uzyskać dostęp do lokalizacji tymczasowej przepływu pracy w trybie offline przy użyciu tej samej ścieżki sieciowej, która została podana w przepływie pracy **inicjowania kopii zapasowej offline** .
   * Funkcja BitLocker jest włączona na komputerze kopiowania.
   * Zainstalowano Azure PowerShell 3.7.0.
   * Są zainstalowane najnowsze zgodne przeglądarki (Microsoft Edge lub Internet Explorer 11), a język JavaScript jest włączony. 
   * Komputer kopiowania może uzyskać dostęp do Azure Portal. W razie potrzeby komputer kopii może być taki sam jak komputer źródłowy.
    
     > [!IMPORTANT] 
     > Jeśli komputer źródłowy jest maszyną wirtualną, komputer do kopiowania musi być innym serwerem fizycznym lub komputerem klienckim z komputera źródłowego.

2. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień na komputerze kopiowania przy użyciu katalogu narzędzi *AzureOfflineBackupDiskPrep* jako bieżący katalog i uruchom następujące polecenie:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Parametr | Opis |
    | --- | --- |
    | s:&lt;*ścieżka lokalizacji tymczasowej*&gt; |Obowiązkowe dane wejściowe używane do zapewnienia ścieżki do lokalizacji tymczasowej wprowadzonej w przepływie pracy **inicjowania kopii zapasowej offline** . |
    | p:&lt;*ścieżka do PublishSettingsFile*&gt; |Opcjonalne dane wejściowe używane do zapewnienia ścieżki do pliku **ustawień publikowania platformy Azure** wprowadzonego w przepływie pracy **inicjowania kopii zapasowej offline** . |

    Po uruchomieniu polecenia Narzędzie żąda wyboru zadania importowania platformy Azure odpowiadającego dyskom, które muszą zostać przygotowane. Jeśli tylko jedno zadanie importu jest skojarzone z podaną lokalizacją przemieszczania, zobaczysz ekran podobny do poniższego.

    ![Dane wejściowe narzędzia przygotowywania dysku platformy Azure](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

3. Wprowadź literę dysku bez kropki końcowej dla zainstalowanego dysku, który ma zostać przygotowany do przeniesienia na platformę Azure. 
4. Potwierdź formatowanie dysku po wyświetleniu monitu.
5. Zostanie wyświetlony monit o zalogowanie się do subskrypcji platformy Azure. Podaj swoje poświadczenia.

    ![Dane wejściowe narzędzia przygotowywania dysku platformy Azure](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    Następnie narzędzie rozpocznie przygotowanie dysku i skopiowanie danych kopii zapasowej. W przypadku, gdy udostępniony dysk nie ma wystarczającej ilości miejsca na dane kopii zapasowej, może być konieczne dołączenie dodatkowych dysków. <br/>

    Po pomyślnym wykonaniu tego narzędzia wiersz polecenia zawiera trzy informacje:
   1. Co najmniej jeden z podanych dysków jest przygotowywany do wysyłania do platformy Azure. 
   2. Otrzymasz potwierdzenie, że zadanie importowania zostało utworzone. Zadanie importowania używa podanej nazwy.
   3. Narzędzie wyświetla adres wysyłkowy centrum danych platformy Azure.

      ![Ukończono przygotowywanie dysku platformy Azure](./media/backup-azure-backup-import-export/console2.png)<br/>

6. Po zakończeniu wykonywania polecenia można zaktualizować informacje o wysyłce.

7. Wyślij dyski na adres dostarczony przez narzędzie i Zachowaj numer śledzenia na potrzeby przyszłego odwołania.

   > [!IMPORTANT] 
   > Żadne dwa zadania importowania platformy Azure nie mogą mieć tego samego numeru śledzenia. Upewnij się, że dyski przygotowane przez narzędzie w ramach pojedynczego zadania importowania platformy Azure są dostarczane razem w jednym pakiecie i że istnieje jeden unikatowy numer śledzenia dla pakietu. Nie łącz dysków przygotowanych w ramach oddzielnych zadań importowania platformy Azure w jednym pakiecie.



## <a name="update-shipping-details-on-the-azure-import-job"></a>Aktualizowanie szczegółów wysyłki w ramach zadania importowania platformy Azure

Poniższa procedura umożliwia zaktualizowanie informacji o wysyłce zadania importowania platformy Azure. Informacje te zawierają szczegółowe informacje na temat:
* Nazwa przewoźnika, który dostarcza dyski do platformy Azure
* Zwróć szczegóły dotyczące wysyłki dla dysków

1. Zaloguj się do subskrypcji platformy Azure.
2. W menu głównym kliknij pozycję **wszystkie usługi** , a następnie w oknie dialogowym wszystkie usługi wpisz import. Gdy zobaczysz **zadania importowania/eksportowania**, kliknij je.
    ![Wprowadzanie informacji o wysyłce](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    Zostanie otwarta lista **zadań Importuj/Eksportuj zadania** i zostanie wyświetlona lista wszystkich zadań importu/eksportu w wybranej subskrypcji. 

3. Jeśli masz wiele subskrypcji, pamiętaj o wybraniu subskrypcji użytej do zaimportowania danych kopii zapasowej. Następnie wybierz nowo utworzone zadanie importu, aby otworzyć jego szczegóły.

    ![Przejrzyj informacje o wysyłce](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

4. W menu Ustawienia zadania importowania kliknij pozycję **Zarządzaj informacjami o wysyłce** , a następnie wprowadź szczegóły dotyczące wysyłki zwrotnej.

    ![Przechowywanie informacji o wysyłce](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

5. Jeśli numer śledzenia jest używany przez firmę wysyłkową, kliknij transparent na stronie Przegląd zadania importowania platformy Azure i wprowadź następujące informacje:

   > [!IMPORTANT] 
   > Upewnij się, że informacje o operatorze i numer śledzenia zostały zaktualizowane w ciągu dwóch tygodni od utworzenia zadania importowania platformy Azure. Niepowodzenie weryfikacji tych informacji w ciągu dwóch tygodni może skutkować usunięciem zadania i nieprzetwarzaniem dysków.

   ![Przechowywanie informacji o wysyłce](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Przechowywanie informacji o wysyłce](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Czas do przetworzenia dysków 
Czas przetwarzania zadania importowania platformy Azure zależy od różnych czynników, takich jak czas wysyłki, typ zadania, typ i rozmiar kopiowanych danych oraz rozmiar udostępnionych dysków. Usługa Azure Import/Export nie ma umowy SLA, ale po odebraniu dysków usługa dokłada starań, aby zakończyć kopiowanie danych kopii zapasowej na konto usługi Azure Storage w ciągu 7 do 10 dni. 

### <a name="monitoring-azure-import-job-status"></a>Monitorowanie stanu zadania importowania platformy Azure
Stan zadania importowania można monitorować z Azure Portal, przechodząc do strony **Importuj/Eksportuj zadania** i wybierając zadanie. Aby uzyskać więcej informacji na temat stanu zadań importowania, zobacz artykuł [Magazyn importu usługi Export](../storage/common/storage-import-export-service.md) .

### <a name="complete-the-workflow"></a>Ukończ przepływ pracy
Po pomyślnym zakończeniu zadania importowania dane początkowej kopii zapasowej są dostępne na koncie magazynu. W czasie następnej zaplanowanej kopii zapasowej Azure Backup kopiuje zawartość danych z konta magazynu do magazynu Recovery Services, jak pokazano poniżej: 

   ![Kopiowanie danych do magazynu Recovery Services](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

W czasie następnej zaplanowanej kopii zapasowej Azure Backup wykonuje przyrostową kopię zapasową.

### <a name="cleaning-up-resources"></a>Czyszczenie zasobów
Po zakończeniu początkowej kopii zapasowej można bezpiecznie usunąć dane zaimportowane do kontenera usługi Azure Storage i dane kopii zapasowej w lokalizacji tymczasowej.

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać odpowiedzi na pytania dotyczące przepływu pracy importowania/eksportowania platformy Azure, zapoznaj się z tematem [transfer danych do magazynu obiektów BLOB za pomocą usługi Microsoft Azure Import/Export](../storage/common/storage-import-export-service.md).
* Zapoznaj się z sekcją kopii zapasowej offline Azure Backup [często zadawane](backup-azure-backup-faq.md) pytania dotyczące pytań dotyczących przepływu pracy.

---
title: Tworzenie kopii zapasowej w trybie źródłowym w trybie offline za pomocą usługi Azure Import/Export
description: Dowiedz się, jak za pomocą usługi Azure Backup wysyłać dane poza siecią przy użyciu usługi Azure Import/Export. W tym artykule opisano rozmieszczanie w trybie offline początkowych danych kopii zapasowej przy użyciu usługi Azure Import/Export.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/17/2018
ms.openlocfilehash: 1359616e074f36a1324a418d5b2c889076ced52d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206762"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Przepływ pracy tworzenia kopii zapasowych w trybie offline w kopii zapasowej platformy Azure

Usługa Azure Backup ma kilka wbudowanych wydajności, które pozwalają zaoszczędzić koszty sieci i magazynu podczas początkowych pełnych kopii zapasowych danych na platformie Azure. Początkowe pełne kopie zapasowe zazwyczaj przesyłają duże ilości danych i wymagają większej przepustowości sieci w porównaniu do kolejnych kopii zapasowych, które przesyłają tylko różnice/przyrosty. W procesie rozmieszczania w trybie offline usługa Azure Backup może używać dysków do przekazywania danych kopii zapasowej w trybie offline na platformę Azure.

Proces rozmieszczania usługi Azure Backup w trybie offline jest ściśle zintegrowany z [usługą Azure Import/Export.](../storage/common/storage-import-export-service.md) Za pomocą tej usługi można przenieść dane początkowej kopii zapasowej na platformę Azure przy użyciu dysków. Jeśli masz terabajty (TBs) początkowych danych kopii zapasowej, które muszą być przesyłane za pośrednictwem sieci o dużym opóźnieniu i niskiej przepustowości, można użyć przepływu pracy rozmieszczania w trybie offline do wysłania początkowej kopii zapasowej na jednym lub kilku dyskach twardych do centrum danych platformy Azure. Poniższy obraz zawiera omówienie kroków w przepływie pracy.

  ![Omówienie procesu przepływu pracy importowania w trybie offline](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

Proces tworzenia kopii zapasowej w trybie offline obejmuje następujące kroki:

1. Zamiast wysyłać dane kopii zapasowej za pośrednictwem sieci, zapisz dane kopii zapasowej w lokalizacji przejściowej.
1. Narzędzie *AzureOfflineBackupDiskPrep* służy do zapisywania danych w lokalizacji tymczasowej na co najmniej jednym dysku sata.
1. W ramach prac przygotowawczych *narzędzie AzureOfflineBackupDiskPrep* tworzy zadanie importowania platformy Azure. Wyślij dyski SATA do najbliższego centrum danych platformy Azure i odwołaj się do zadania importu, aby połączyć działania.
1. W centrum danych platformy Azure dane na dyskach są kopiowane do konta magazynu platformy Azure.
1. Usługa Azure Backup kopiuje dane kopii zapasowej z konta magazynu do magazynu usług odzyskiwania, a przyrostowe kopie zapasowe są zaplanowane.

## <a name="supported-configurations"></a>Obsługiwane konfiguracje

Następujące funkcje lub obciążenia usługi Azure Backup obsługują tworzenie kopii zapasowych w trybie offline:

> [!div class="checklist"]
>
> * Tworzenie kopii zapasowych plików i folderów za pomocą agenta usług odzyskiwania platformy Microsoft Azure (MARS), nazywanego również agentem tworzenia kopii zapasowych platformy Azure.
> * Tworzenie kopii zapasowych wszystkich obciążeń i plików za pomocą programu System Center Data Protection Manager (DPM).
> * Tworzenie kopii zapasowych wszystkich obciążeń i plików za pomocą programu Microsoft Azure Backup Server.
 
   > [!NOTE]
   > Kopia zapasowa w trybie offline nie jest obsługiwana w przypadku kopii zapasowych stanu systemu wykonywanych przy użyciu agenta usługi Azure Backup Agent.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Wymagania wstępne

  > [!NOTE]
  > Poniższe wymagania wstępne i przepływ pracy dotyczą tylko kopii zapasowej plików i folderów w trybie offline przy użyciu [najnowszego agenta usług Azure Recovery Services Agent](https://aka.ms/azurebackup_agent). Aby wykonać kopie zapasowe w trybie offline dla obciążeń przy użyciu programu System Center DPM lub serwera kopii zapasowej platformy Azure, zobacz [Przepływ pracy tworzenia kopii zapasowych w trybie offline dla programu DPM i serwera kopii zapasowych Platformy Azure](backup-azure-backup-server-import-export-.md).

Przed uruchomieniem przepływu pracy tworzenia kopii zapasowej w trybie offline należy wykonać następujące wymagania wstępne:

* Tworzenie [skarbca usług odzyskiwania](backup-azure-recovery-services-vault-overview.md). Aby utworzyć przechowalnię, wykonaj czynności opisane w [10.](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Upewnij się, że tylko [najnowsza wersja agenta kopii zapasowej platformy Azure](https://aka.ms/azurebackup_agent) jest zainstalowana na kliencie systemu Windows Server lub Windows, stosownie do przypadku, a komputer jest zarejestrowany w przechowalni usług odzyskiwania.
* Program Azure PowerShell 3.7.0 jest wymagany na komputerze z uruchomionym agentem tworzenia kopii zapasowych platformy Azure. Pobierz i [zainstaluj wersję programu Azure PowerShell w wersji 3.7.0](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).
* Na komputerze z uruchomionym agentem tworzenia kopii zapasowych platformy Azure upewnij się, że jest zainstalowany program Microsoft Edge lub Internet Explorer 11, a język JavaScript jest włączony.
* Utwórz konto magazynu platformy Azure w tej samej subskrypcji co magazyn usług odzyskiwania.
* Upewnij się, że masz [uprawnienia niezbędne](../active-directory/develop/howto-create-service-principal-portal.md) do utworzenia aplikacji usługi Azure Active Directory. Przepływ pracy kopii zapasowej w trybie offline tworzy aplikację usługi Azure Active Directory w ramach subskrypcji skojarzonej z kontem magazynu platformy Azure. Celem aplikacji jest zapewnienie usługi Azure Backup z bezpiecznym i ograniczonym dostępem do usługi Azure Import/Export, która jest wymagana dla przepływu pracy tworzenia kopii zapasowych w trybie offline.
* Zarejestruj dostawcę zasobów *Microsoft.ImportExport* w ramach subskrypcji zawierającej konto magazynu platformy Azure. Aby zarejestrować dostawcę zasobów:
    1. W menu głównym wybierz pozycję **Subskrypcje**.
    1. Jeśli subskrybujesz wiele subskrypcji, wybierz subskrypcję, której zamierzasz użyć do tworzenia kopii zapasowych w trybie offline. Jeśli używasz tylko jednej subskrypcji, zostanie wyświetlona subskrypcja.
    1. W menu subskrypcji wybierz **pozycję Dostawcy zasobów,** aby wyświetlić listę dostawców.
    1. Na liście dostawców przewiń w dół do *witryny Microsoft.ImportExport*. Jeśli **stan** nie jest **zarejestrowany,** wybierz pozycję **Zarejestruj**.

        ![Rejestrowanie dostawcy zasobów](./media/backup-azure-backup-import-export/registerimportexport.png)

* Zostanie utworzona lokalizacja przejściowa, która może być udziałem sieciowym lub dowolnym dodatkowym dyskiem na komputerze, wewnętrznym lub zewnętrznym, z wystarczającą ilością miejsca na dysku do przechowywania początkowej kopii. Na przykład jeśli chcesz zrobić z powrotem do kopii zapasowej serwer plików o rozmiarze 500 GB, upewnij się, że obszar przemieszczania ma co najmniej 500 GB. (Mniejsza ilość jest używana z powodu kompresji.)
* Podczas wysyłania dysków na platformę Azure należy używać tylko 2,5-calowych dysków SSD lub 2,5-calowych lub 3,5-calowych wewnętrznych dysków twardych SATA II/III. Można używać dysków twardych o pojemności do 10 TB. Sprawdź [dokumentację usługi importu/eksportu platformy Azure](../storage/common/storage-import-export-requirements.md#supported-hardware) dla najnowszego zestawu dysków obsługiwanych przez usługę.
* Dyski SATA muszą być podłączone do komputera (nazywanego *kopią komputera),* z którego wykonywana jest kopia danych kopii zapasowej z lokalizacji przejściowej na dyski SATA. Upewnij się, że funkcja BitLocker jest włączona na komputerze kopii.

## <a name="workflow"></a>Przepływ pracy

W tej sekcji opisano przepływ pracy tworzenia kopii zapasowych w trybie offline, dzięki czemu dane mogą być dostarczane do centrum danych platformy Azure i przekazywane do usługi Azure Storage. Jeśli masz pytania dotyczące usługi importowania lub dowolnego aspektu procesu, zobacz [dokumentację omówienia usługi importu/eksportu platformy Azure](../storage/common/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Inicjowanie kopii zapasowej w trybie offline

1. Po zaplanowaniu kopii zapasowej na Agent usług odzyskiwania, zostanie wyświetlona ta strona.

    ![Strona importu](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

1. Wybierz opcję **Transfer używając własnych dysków**.

    > [!NOTE]
    > Użyj opcji Azure Data Box, aby przenieść dane początkowej kopii zapasowej w trybie offline. Ta opcja pozwala zaoszczędzić nakład pracy wymagany do zakupu własnych dysków zgodnych z platformą Azure. Zapewnia on opatentowane przez firmę Microsoft, bezpieczne i zabezpieczone przed manipulacją urządzenia Azure Data Box, do których dane kopii zapasowej mogą być bezpośrednio zapisywane przez agenta usług odzyskiwania.

1. Wybierz **przycisk Dalej**i dokładnie wypełnij pola.

    ![Wprowadź szczegóły dysku](./media/backup-azure-backup-import-export/your-disk-details.png)

   Wypełniane pola to:

    * **Lokalizacja tymczasowa:** Tymczasowa lokalizacja magazynu, do której zapisywana jest początkowa kopia zapasowa. Lokalizacja przemieszczania może znajdować się na udziale sieciowym lub komputerze lokalnym. Jeśli komputer kopiowania i komputer źródłowy są różne, określ pełną ścieżkę sieciową lokalizacji przejściowej.
    * **Konto usługi Azure Resource Manager Storage:** Nazwa konta magazynu typu Menedżer zasobów (ogólnego przeznaczenia w wersji 1 lub ogólnego przeznaczenia w wersji 2) w dowolnej subskrypcji platformy Azure.
    * **Kontener usługi Azure Storage:** Nazwa docelowego obiektu blob magazynu na koncie magazynu platformy Azure, w którym dane kopii zapasowej są importowane przed skopiowaniem do magazynu usług odzyskiwania.
    * **Identyfikator subskrypcji platformy Azure:** identyfikator subskrypcji platformy Azure, w którym jest tworzone konto magazynu platformy Azure.
    * **Nazwa zadania importu platformy Azure:** unikatowa nazwa, za pomocą której usługa Azure Import/Export i usługa Azure Backup śledzą transfer danych wysyłanych na dyski na platformę Azure.
  
   Po wypełnieniu pól wybierz pozycję **Dalej**. Zapisz informacje o **lokalizacji tymczasowej** i **nazwie zadania importu platformy Azure.** Jest to wymagane do przygotowania dysków.

1. Po wyświetleniu monitu zaloguj się do subskrypcji platformy Azure. Musisz zalogować się, aby usługa Azure Backup mogła utworzyć aplikację usługi Azure Active Directory. Wprowadź wymagane uprawnienia dostępu do usługi Azure Import/Export.

    ![Strona logowania do subskrypcji platformy Azure](./media/backup-azure-backup-import-export/azure-login.png)

1. Zakończ przepływ pracy. Na konsoli agenta tworzenia kopii zapasowych platformy Azure wybierz pozycję **Utwórz kopię zapasową teraz**.

    ![Sprzedaj zapasy teraz](./media/backup-azure-backup-import-export/backupnow.png)

1. Na stronie **Potwierdzenie** kreatora wybierz pozycję **Zapas utworu .** Początkowa kopia zapasowa jest zapisywana w obszarze przemieszczania w ramach instalacji.

   ![Potwierdź, że jesteś gotowy do utworzenia kopii zapasowej teraz](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    Po zakończeniu operacji lokalizacja przemieszczania jest gotowa do użycia do przygotowania dysku.

   ![Strona Kreatora tworzenia kopii zapasowych teraz](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Przygotowywanie dysków SATA i wysyłka na platformę Azure

Narzędzie *AzureOfflineBackupDiskPrep* przygotowuje dyski SATA, które są wysyłane do najbliższego centrum danych platformy Azure. To narzędzie jest dostępne w katalogu instalacji agenta usługi Azure Backup Agent w następującej ścieżce:

```*\Microsoft Azure Recovery Services Agent\Utils\\*```

1. Przejdź do katalogu i skopiuj katalog *AzureOfflineBackupDiskPrep* na inny komputer, na którym są połączone dyski SATA. Na komputerze z podłączonymi dyskami SATA upewnij się, że:

   * Komputer kopii może uzyskać dostęp do lokalizacji tymczasowej dla przepływu pracy rozmieszczania w trybie offline przy użyciu tej samej ścieżki sieciowej, która została udostępniona w przepływie pracy w sekcji "Inicjuj kopię zapasową w trybie offline".
   * Funkcja BitLocker jest włączona na komputerze kopii.
   * Jest zainstalowany program Azure PowerShell 3.7.0.
   * Zainstalowane są najnowsze zgodne przeglądarki (Microsoft Edge lub Internet Explorer 11), a także włączono obsługę języka JavaScript.
   * Komputer kopii może uzyskać dostęp do witryny Azure portal. W razie potrzeby komputer kopii może być taki sam jak komputer źródłowy.

     > [!IMPORTANT]
     > Jeśli komputer źródłowy jest maszyną wirtualną, komputer kopii musi być inny serwer fizyczny lub komputer kliencki z komputera źródłowego.

1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień na komputerze kopii z katalogiem narzędzi *AzureOfflineBackupDiskPrep* jako bieżącym katalogiem. Uruchom następujące polecenie:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Parametr | Opis |
    | --- | --- |
    | s:&lt;*Ścieżka lokalizacji tymczasowej*&gt; |To obowiązkowe dane wejściowe są używane do zapewnienia ścieżki do lokalizacji przejściowej wprowadzonej w przepływie pracy w sekcji "Inicjuj kopię zapasową w trybie offline". |
    | p:&lt;*Ścieżka do PublishSettingsFile*&gt; |To opcjonalne dane wejściowe jest używany do zapewnienia ścieżki do pliku ustawień publikowania platformy Azure, który został wprowadzony w przepływie pracy w sekcji "Inicjuj kopię zapasową w trybie offline". |

    Po uruchomieniu polecenia narzędzie żąda wyboru zadania importu platformy Azure, które odpowiada dyskom, które muszą być przygotowane. Jeśli tylko jedno zadanie importu jest skojarzone z podanej lokalizacji tymczasowej, zostanie wyświetlona strona taka jak ta.

    ![Wprowadzanie narzędzia przygotowania dysku platformy Azure](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

1. Wprowadź literę dysku bez końcowego dwukropka dla zainstalowanego dysku, który chcesz przygotować do przeniesienia na platformę Azure.
1. Po wyświetleniu monitu podaj potwierdzenie formatowania dysku.
1. Zostanie wyświetlony monit o zalogowanie się do subskrypcji platformy Azure. Wprowadź poświadczenia.

    ![Logowanie do subskrypcji platformy Azure](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    Następnie narzędzie rozpoczyna przygotowywanie dysku i kopiowanie danych kopii zapasowej. Może być konieczne dołączenie dodatkowych dysków po wyświetleniu monitu przez narzędzie w przypadku, gdy podany dysk nie ma wystarczającej ilości miejsca na dane kopii zapasowej. <br/>

    Po pomyślnym wykonaniu narzędzia wiersz polecenia zawiera trzy informacje:

   1. Co najmniej jeden dysk, który podałeś, jest przygotowany do wysyłki na platformę Azure.
   1. Otrzymasz potwierdzenie utworzenia zadania importu. Zadanie importu używa podanej nazwy.
   1. Narzędzie wyświetla adres wysyłki dla centrum danych platformy Azure.

      ![Przygotowanie dysku platformy Azure zakończone](./media/backup-azure-backup-import-export/console2.png)<br/>

1. Po zakończeniu wykonywania polecenia można zaktualizować informacje o wysyłce.

1. Wyślij dyski na adres podany przez narzędzie. Zachowaj numer śledzenia do wykorzystania w przyszłości.

   > [!IMPORTANT]
   > Żadne dwa zadania importu platformy Azure nie mogą mieć tego samego numeru śledzenia. Upewnij się, że dyski przygotowane przez narzędzie w ramach jednego zadania importu platformy Azure są wysyłane razem w jednym pakiecie i że istnieje jeden unikatowy numer śledzenia dla pakietu. Nie łącz dysków przygotowanych jako część oddzielnych zadań importowania platformy Azure w jednym pakiecie.

## <a name="update-shipping-details-on-the-azure-import-job"></a>Aktualizowanie szczegółów wysyłki w zadaniu importu platformy Azure

Poniższa procedura aktualizuje szczegóły wysyłki zadania importu platformy Azure. Informacje te obejmują szczegółowe informacje na temat:

* Nazwa operatora, który dostarcza dyski na platformę Azure.
* Zwrot szczegółów wysyłki dysków.

1. Zaloguj się do subskrypcji platformy Azure.
1. W menu głównym wybierz pozycję **Wszystkie usługi**. W oknie dialogowym **Wszystkie usługi** wprowadź pozycję **Importuj**. Po wyświetleniu opcji **Importuj/eksportuj zadania**wybierz je.

    ![Wprowadź informacje o wysyłce](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    Zostanie otwarte menu **Zaimportuj/eksportuj zadania** i zostanie wyświetlona lista wszystkich zadań importu/eksportu w wybranej subskrypcji.

1. Jeśli masz wiele subskrypcji, wybierz subskrypcję używaną do importowania danych kopii zapasowej. Następnie wybierz nowo utworzone zadanie importu, aby otworzyć jego szczegóły.

    ![Przejrzyj informacje o wysył](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

1. W menu **Ustawienia** zadania importu wybierz polecenie **Zarządzaj informacjami o wysyłce**. Wprowadź szczegóły wysyłki zwrotnej.

    ![Przechowuj informacje o wysyłce](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

1. Jeśli masz numer śledzenia od przewoźnika, wybierz baner na stronie Przegląd zadań importu platformy Azure i wprowadź następujące szczegóły.

   > [!IMPORTANT]
   > Upewnij się, że informacje o operatorze i numer śledzenia zostały zaktualizowane w ciągu dwóch tygodni od utworzenia zadania importowania platformy Azure. Nie zweryfikowanie tych informacji w ciągu dwóch tygodni może spowodować usunięcie zadania i nieprzetwochanie dysków.

   ![Alert aktualizacji informacji śledzenia](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Informacje o przewoźniku i numer śledzenia](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Czas na przetworzenie dysków

Czas potrzebny do przetworzenia zadania importu platformy Azure jest różny. Czas procesu zależy od takich czynników, jak czas wysyłki, typ zadania, typ i rozmiar kopiowanych danych oraz rozmiaru dostarczonych dysków. Usługa Importu/Eksportu platformy Azure nie ma umowy SLA. Po odebraniu dysków usługa stara się wykonać kopię zapasową danych na koncie magazynu platformy Azure w ciągu 7 do 10 dni.

### <a name="monitor-azure-import-job-status"></a>Monitorowanie stanu zadania importu platformy Azure

Stan zadania importowania można monitorować za pomocą witryny Azure portal. Przejdź do strony **Importowanie/Eksportowanie zadań** i wybierz zadanie. Aby uzyskać więcej informacji na temat stanu zadań importowania, zobacz [Co to jest usługa Import/Eksportowanie platformy Azure?](../storage/common/storage-import-export-service.md).

### <a name="finish-the-workflow"></a>Zakończenie przepływu pracy

Po pomyślnym zakończeniu zadania importowania początkowe dane kopii zapasowej są dostępne na koncie magazynu. W czasie następnej zaplanowanej kopii zapasowej usługa Azure Backup kopiuje zawartość danych z konta magazynu do magazynu usług odzyskiwania.

   ![Kopiowanie danych do magazynu usług odzyskiwania](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

W czasie następnej zaplanowanej kopii zapasowej usługa Azure Backup wykonuje przyrostową kopię zapasową.

### <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu początkowej kopii zapasowej można bezpiecznie usunąć dane zaimportowane do kontenera usługi Azure Storage i dane kopii zapasowej w lokalizacji tymczasowej.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać jakiekolwiek pytania dotyczące przepływu pracy usługi Import/Eksportowanie platformy Azure, zobacz [Przekazywanie danych do magazynu obiektów Blob za pomocą usługi Importowanie/Eksportowanie platformy Microsoft Azure.](../storage/common/storage-import-export-service.md)

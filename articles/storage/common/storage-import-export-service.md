---
title: Przy użyciu Import/Eksport Azure na przesyłanie danych do i z usługi Azure Storage | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia importowania i eksportowania zadania w portalu Azure do przesyłania danych do i z usługi Azure Storage.
author: muralikk
manager: syadav
services: storage
ms.service: storage
ms.topic: article
ms.date: 03/22/2018
ms.author: muralikk
ms.openlocfilehash: 4d6177fe0a50c531ba6c4b3e87eaa08299af2ddd
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="use-the-microsoft-azure-importexport-service-to-transfer-data-to-azure-storage"></a>Transfer danych do usługi Azure Storage za pomocą usługi Import/Eksport Microsoft Azure
W tym artykule udostępniamy instrukcje krok po kroku na temat używania usługi Import/Eksport Azure do bezpiecznego przesyłania dużych ilości danych do magazynu obiektów Blob platformy Azure i usługi pliki Azure przez wysyłanie dysków do centrum danych platformy Azure. Ta usługa może również przesyłanie danych z magazynu Azure do dysków twardych i wysłać do lokalnych witryn. Dane z pojedynczej stacji dysków SATA wewnętrzny można zaimportować do magazynu obiektów Blob platformy Azure lub usługi pliki Azure. 

> [!IMPORTANT] 
> Wybrana usługa dopuszcza wyłącznie wewnętrznych dysków twardych SATA lub dysków SSD tylko. Nie innych urządzeń jest obsługiwane. Nie wysyłaj zewnętrznych dysków twardych, urządzeniach NAS itp., zgodnie z ich zostanie zwrócony, jeśli to możliwe, lub w inny sposób odrzuconych.
>
>

Wykonaj następujące czynności w przypadku danych na dysku do zaimportowania do usługi Azure Storage.
### <a name="step-1-prepare-the-drives-using-waimportexport-tool-and-generate-journal-files"></a>Krok 1: Przygotowanie za pomocą narzędzia WAImportExport dysku/s i Generuj plik dziennika/s.

1.  Identyfikowanie danych mają być zaimportowane do magazynu Azure. Możesz zaimportować katalogów i plików autonomicznych na serwerze lokalnym lub w udziale sieciowym.
2.  W zależności od całkowity rozmiar danych Uzyskaj wymaganej liczby 2,5 SSD 2,5-calowe lub 3,5" stacje dysków twardych SATA II lub III.
3.  Dołącz dyski twarde bezpośrednio przy użyciu SATA lub z zewnętrznej karty USB do komputera z systemem windows.
1.  Utwórz pojedynczy wolumin NTFS na każdy dysk twardy i przypisać literę dysku do woluminu. Nie punkty instalacji.
2.  Aby włączyć szyfrowanie na komputerze z systemem windows, należy włączyć szyfrowanie skrytki bitowych na woluminie NTFS. Postępuj zgodnie z instrukcjami na https://technet.microsoft.com/library/cc731549(v=ws.10).aspx.
3.  Całkowicie skopiować dane do pojedynczego woluminów NTFS te zaszyfrowane na dyskach przy użyciu kopii & Wklej lub przeciągnij & upuszczania lub Robocopy lub takie narzędzie.
7.  Pobierz WAImportExport V1 z https://www.microsoft.com/en-us/download/details.aspx?id=42659
8.  Rozpakuj do waimportexportv1 folder domyślny. Na przykład C:\WaImportExportV1  
9.  Uruchom jako Administrator i otworzyć programu PowerShell lub wiersza polecenia i zmień katalog na folder rozpakowane. Na przykład dysk cd C:\WaImportExportV1
10. Skopiuj następujący wiersz polecenia do edytora tekstu, a następnie edytuj go w celu utworzenia wiersza polecenia:

    ```
    ./WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1 /sk:***== /t:D /bk:*** /srcdir:D:\ /dstdir:ContainerName/ /skipwrite 
    ```
    
    W poniższej tabeli opisano te opcje wiersza polecenia:

    |Opcja  |Opis  |
    |---------|---------|
    |/j:     |Nazwa pliku dziennika z rozszerzeniem .jrn. Plik dziennika jest generowany na dysk. Zaleca się użycie numeru seryjnego dysku jako nazwa pliku dziennika.         |
    |/SK:     |Klucz konta magazynu Azure.         |
    |/ t:     |Litera dysku do wysłania. Na przykład dysk `D`.         |
    |/bk:     |Klucza funkcji BitLocker dla dysku. Jego numerycznym hasłem z danych wyjściowych ` manage-bde -protectors -get D: `      |
    |/srcdir:     |Litera dysku do wysłania następuje `:\`. Na przykład `D:\`.         |
    |/dstdir:     |Nazwa kontenera docelowego w usłudze Azure Storage         |
    |/skipwrite:     |Opcję określającą, że nie istnieje żadne nowe dane wymagane do skopiowania i istniejące dane na dysku jest przygotowany         |
1. Powtórz krok 10 dla każdego dysku, który ma zostać wysłane.
2. Plik dziennika o nazwie dostarczone z parametrem /j: jest tworzony dla każdego uruchomienia wiersza polecenia.

### <a name="step-2-create-an-import-job-on-azure-portal"></a>Krok 2: Tworzenie zadania importu z portalu Azure.

1. Zaloguj się do https://portal.azure.com/ i w obszarze więcej usług -> MAGAZYNU -> "zadania importu/eksportu" kliknij **zadania importu/eksportu Utwórz**.

2. W sekcji podstawy wybierz "Import do platformy Azure", wprowadź ciąg nazwy zadania, wybierz subskrypcję, wprowadź lub wybierz grupę zasobów. Wprowadź nazwę opisową dla zadania importu. Należy pamiętać, że wprowadzona nazwa może zawierać tylko małe litery, cyfry, łączniki i podkreślenia, musi zaczynać się literą i nie może zawierać spacji. Możesz użyć nazwy wybrane do śledzenia zadań, gdy są one w toku, a po zakończeniu.

3. W sekcji szczegółów zadania przekazywanie plików dziennika dysków, które uzyskane w kroku przygotowania dysku. Jeśli użyto waimportexport.exe version1, należy przekazać jeden plik dla każdego dysku, które zostały przygotowane. Wybierz konto magazynu, które dane zostaną zaimportowane do w sekcji konta magazynu "Docelowe Import". Lokalizacja przyjmowania jest wypełniane automatycznie na podstawie regionu wybrane konta magazynu.
   
   ![Utwórz zadania importu — krok 3](./media/storage-import-export-service/import-job-03.png)
4. W zamian wysyłania informacji o sekcji, wybierz nośnik z listy rozwijanej i wprowadź liczbę konta nieprawidłowy operator utworzoną z tego nośnika. Firma Microsoft używa tego konta na potrzeby wysłania dysków powrotem po zakończeniu zadania importu. Podaj nazwisko osoby kontaktowej w pełne i prawidłowe, telefonu, poczty e-mail, adres, Miasto, zip, stan/proviince i kraj/region.
   
5. W sekcji podsumowania Centrum danych Azure adres wysyłkowy podano służący do wysyłania dysków do kontrolera domeny z platformy Azure. Upewnij się, są wymienione nazwy zadania i pełny adres na etykiecie wysyłki. 

6. Kliknij przycisk OK, na stronie Podsumowanie, aby zakończyć tworzenie zadania importu.

### <a name="step-3-ship-the-drives-to-the-azure-datacenter-shipping-address-provided-in-step-2"></a>Krok 3: Statku dysku/s do centrum danych Azure wysyłania adres podany w kroku 2.
FedEx, UPS lub przez firmę DHL może służyć do wysłania pakietu do kontrolera domeny z platformy Azure.

### <a name="step-4-update-the-job-created-in-step2-with-tracking-number-of-the-shipment"></a>Krok 4: Aktualizowanie zadania utworzone w Step2 ze śledzeniem numer wydania.
Po wysyłki na dyskach, wróć do **importu/eksportu** strony w portalu Azure, aby zaktualizować numer śledzenia, wykonaj kroki opisane poniżej,) przejdź i kliknij przycisk importu zadania b) kliknij na **stan zadania i informacje o śledzeniu aktualizacji gdy dyski są dostarczane**. c) zaznacz pole wyboru "Oznacz jako wydane" d) Podaj numer operatora oraz śledzenie.
Jeśli numer nie zostanie zaktualizowane w ciągu 2 tygodni tworzenia zadania, zadanie wygaśnie. Postęp zadania można śledzić na pulpicie nawigacyjnym portalu. Zobacz, co oznacza przez każdy stan zadania w poprzedniej sekcji [wyświetlanie stan zadania](#viewing-your-job-status).

## <a name="when-should-i-use-the-azure-importexport-service"></a>Kiedy należy użyć usługi Azure Import/Export?
Należy rozważyć użycie usługi Import/Eksport Azure przekazanie lub pobranie danych za pośrednictwem sieci jest zbyt wolno lub pobieranie dodatkowej przepustowości sieci jest drogie.

Tej usługi można użyć w scenariuszach takich jak:

* Migrowanie danych w chmurze: szybkie przenoszenie dużych ilości danych na platformie Azure i tańszy sposób.
* Dystrybucję zawartości: szybko wysyłania danych do lokacji klienta.
* Kopia zapasowa: Korzystać z kopii zapasowych danych lokalnych do przechowywania w magazynie Azure.
* Odzyskiwanie danych: odzyskać dużej ilości danych przechowywanych w magazynie i jest dostarczany do Twojej lokalizacji lokalnej.

## <a name="prerequisites"></a>Wymagania wstępne
W tej sekcji na listę wymagań wstępnych dotyczących tej usługi. Dokładne zapoznanie się je przed dostarczeniem dysków.

### <a name="storage-account"></a>Konto magazynu
Musi mieć istniejącej subskrypcji platformy Azure i co najmniej jedno konto magazynu ma być używana usługa importu i eksportu. Import/Eksport Azure obsługuje tylko klasyczny, konta magazynu obiektów Blob i kont magazynu ogólnego przeznaczenia v1. Każde zadanie może służyć do transferu danych do lub z tylko jedno konto magazynu. Innymi słowy zadanie pojedynczego importu/eksportu nie może obejmować wielu wielu kont magazynu. Aby uzyskać informacje dotyczące tworzenia nowego konta magazynu, zobacz [jak utworzyć konto magazynu](storage-create-storage-account.md#create-a-storage-account).

> [!IMPORTANT] 
> Usługa Azure Importuj Eksportuj nie obsługuje kont magazynu gdzie [punktów końcowych usługi sieci wirtualnej](../../virtual-network/virtual-network-service-endpoints-overview.md) funkcja została włączona. 
> 
> 

### <a name="data-types"></a>Typy danych
Usługa Import/Eksport Azure umożliwia kopiowanie danych do **bloku** obiektów blob, **strony** obiektów blob, lub **pliki**. Z drugiej strony, można wyeksportować tylko **bloku** obiektów blob, **strony** obiektów blob lub **Append** obiekty BLOB z usługi Azure storage przy użyciu tej usługi. Usługa obsługuje tylko importowanie plików Azure do usługi Azure storage. Eksportowanie plików Azure nie jest obecnie obsługiwane.

### <a name="job"></a>Zadanie
Aby rozpocząć proces importowania i eksportowania z magazynu, należy najpierw utworzyć zadania. Zadania mogą być zadania importu lub eksportu:

* Utwórz zadania importu umożliwia transfer danych ma lokalnej z kontem magazynu platformy Azure.
* Utwórz zadanie eksportu, jeśli chcesz przenieść dane przechowywane na koncie magazynu do dysków twardych, które są wysyłane do firmy Microsoft. Podczas tworzenia zadania można powiadomić usługi Import/Eksport czy użytkownik będzie wysyłania jeden lub więcej dysków twardych do centrum danych platformy Azure.

* Dla zadania importu będzie wysyłania dyski twarde zawierające dane.
* Dla zadania eksportu zostanie wysyłania pustych dysków twardych.
* Można wysłać do 10 dysków twardych na zadanie.

Możesz utworzyć importu lub eksportu zadania przy użyciu portalu Azure lub [interfejsu API REST usługi Azure Storage importu/eksportu](/rest/api/storageimportexport).

> [!Note]
> Interfejsy API frontonu REDDOG nie będą obsługiwane 28 lutego 2018 i jego nowszych wersjach. Aby kontynuować korzystanie z usługi, przeprowadzić migrację do [interfejsów API REST importu/eksportu ARM](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/storageimportexport/resource-manager/Microsoft.ImportExport/stable/2016-11-01/storageimportexport.json). 

### <a name="waimportexport-tool"></a>Narzędzie WAImportExport
Pierwszym krokiem tworzenia **zaimportować** zadania jest przygotowanie dysków, które zostaną dostarczone do importu. Aby przygotować dyski, musi on połączyć się z lokalnym serwerem i uruchom narzędzie WAImportExport na lokalnym serwerze. To narzędzie WAImportExport umożliwia kopiowanie danych na dysku, szyfrowanie danych na dysku za pomocą funkcji BitLocker i generowanie plików dziennika dysku.

Pliki dziennika przechowują podstawowe informacje o zadaniu i dysku, takich jak numer seryjny dysk i nazwa konta magazynu. Ten plik dziennika nie są przechowywane na dysku. Jest on używany podczas tworzenia zadania importu. Szczegóły krok po kroku dotyczące tworzenia zadania znajdują się w dalszej części tego artykułu.

Narzędzie WAImportExport jest zgodna tylko z 64-bitowym systemie operacyjnym Windows. Zobacz [systemu operacyjnego](#operating-system) sekcji dla określonych wersji systemu operacyjnego, obsługiwany.

Pobierz najnowszą wersję [narzędzie WAImportExport](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExportV2.zip). Aby uzyskać więcej informacji na temat używania narzędzia WAImportExport, zobacz [za pomocą narzędzia WAImportExport](storage-import-export-tool-how-to.md).

>[!NOTE]
>**Poprzednia wersja:** można [Pobierz WAImportExpot V1](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip) wersję narzędzia i zapoznaj się z [Podręcznik użycia WAImportExpot V1](storage-import-export-tool-how-to-v1.md). WAImportExpot V1 wersję narzędzia zapewniają obsługę **przygotowywania dysków, gdy już wstępnie zapisywania danych do dysku**. Klucz tylko dostępne w przypadku klucza sygnatury dostępu Współdzielonego, należy użyć narzędzia WAImportExpot V1.

>

### <a name="hard-disk-drives"></a>Dyski twarde
Tylko 2,5 SSD lub 2,5-calowe lub 3,5" SATA II lub III wewnętrzny dysk twardy są obsługiwane do użycia z usługą importu i eksportu. Zadania importu/eksportu pojedynczego może mieć maksymalnie 10 HDD/SSD, a każdy dysk twardy poszczególnych/SSD może być o dowolnym rozmiarze. Duża liczba dysków można było ich rozmieszczenie wielu zadań i nie ma żadnych limitów liczby zadań, które mogą zostać utworzone. 

Dla zadania importu są przetwarzane tylko pierwszy ilość danych na dysku. Ilość danych musi być sformatowany jako NTFS.

> [!IMPORTANT]
> Zewnętrzne stacje dysków twardych dołączonych do wbudowanych adaptera USB nie są obsługiwane przez tę usługę. Nie można również używać dysk wewnątrz wielkość liter w wyrazie zewnętrzny dysk twardy; Nie wysyłaj zewnętrznych dysków twardych.
> 
> 

Poniżej znajduje się lista zewnętrznej karty USB umożliwia kopiowanie danych do wewnętrznego dysków twardych. Anker 68UPSATAA - 02BU Anker 68UPSHHDS BU Startech SATADOCK22UE Orico 6628SUS3-C-BK (seria 6628) Thermaltake BlacX gorąca wymiany SATA zewnętrznych twardych dysków stacji dokującej (USB 2.0 & eSATA)

### <a name="encryption"></a>Szyfrowanie
Danych na dysku musi być zaszyfrowany za pomocą szyfrowania dysków funkcją BitLocker. Szyfrowanie chroni Twoje dane podczas przesyłania.

Dla zadania importu istnieją dwa sposoby wykonania szyfrowania. Pierwszym sposobem jest Określ opcje, korzystając z pliku CSV zestawu danych podczas uruchamiania narzędzia WAImportExport podczas przygotowywania dysków. Drugim sposobem jest Włącz szyfrowanie funkcją BitLocker ręcznie na dysku i określ klucz szyfrowania w driveset CSV podczas uruchamiania wiersza polecenia narzędzia WAImportExport podczas przygotowywania dysków.

Dla zadań eksportu po skopiowaniu danych na dyskach, usługa zostanie szyfrowania dysku za pomocą funkcji BitLocker przed dostarczeniem go z powrotem do. Klucz szyfrowania są dostępne za pośrednictwem portalu Azure.  

### <a name="operating-system"></a>System operacyjny
Jedną z następujących 64-bitowe systemy operacyjne umożliwia przygotowanie dysk twardy za pomocą narzędzia WAImportExport przed dostarczeniem dysk do platformy Azure:

Windows 7 Enterprise, Windows 7 Ultimate, Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise, Windows 10, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. Wszystkie systemy operacyjne obsługują szyfrowanie dysków funkcją BitLocker.

### <a name="locations"></a>Lokalizacje
Usługa Import/Eksport Azure obsługuje kopiowanie danych do i z wszystkich kont magazynu Azure publicznego. Mogą być dyski twarde do jednego z podanych lokalizacji. Jeśli Twoje konto magazynu znajduje się w lokalizacji publicznej platformy Azure, która nie jest określana w tym miejscu lokalizacji alternatywnej wysyłki zapewnia się podczas tworzenia zadania przy użyciu portalu Azure lub interfejsu API REST importu i eksportu.

Obsługiwane lokalizacje wysyłki:

* Wschodnie stany USA
* Zachodnie stany USA
* Wschodnie stany USA 2
* Zachodnie stany USA 2
* Środkowe stany USA
* Środkowo-północne stany USA
* Środkowo-południowe stany USA
* Środkowo-zachodnie stany USA
* Europa Północna
* Europa Zachodnia
* Azja Wschodnia
* Azja Południowo-Wschodnia
* Australia Wschodnia
* Australia Południowo-Wschodnia
* Japonia Zachodnia
* Japonia Wschodnia
* Indie Środkowe
* Indie Południowe
* Indie Zachodnie
* Kanada Środkowa
* Kanada Wschodnia
* Brazylia Południowa
* Korea Środkowa
* Administracja USA — Wirginia
* Administracja USA — Iowa
* US DoD — wschodnie stany
* US DoD — środkowe stany
* Chiny Wschodnie
* Chiny Północne
* Południowe Zjednoczone Królestwo
* Niemcy Środkowe
* Niemcy Północno-Wschodnie

### <a name="shipping"></a>Wysyłka
**Dyski wysyłki do centrum danych:**

Podczas tworzenia zadania importu lub eksportu, należy podać adres wysyłkowy jednego z obsługiwanych lokalizacji na potrzeby wysłania dysków. Podany adres wysyłkowy zależy od lokalizacji konta magazynu, ale nie może być taka sama jak lokalizacja konta magazynu.

FedEx, UPS lub przez firmę DHL może służyć do wysłania dysków na adres wysyłkowy.

**Dyski wysyłki z centrum danych:**

Podczas tworzenia zadania importu lub eksportu, musisz podać adres zwrotny dla firmy Microsoft do użycia podczas wysyłania dysków, po zakończeniu zadania. Upewnij się, że Podaj prawidłowy zwrotny adres Aby uniknąć opóźnienia w przetwarzaniu.

Operatora powinny mieć odpowiedni śledzenia w celu utrzymania łańcucha nadzoru. Należy podać prawidłowy FedEx, UPS, lub przez firmę DHL numer konta operator do użycia przez firmę Microsoft do wysyłki na dyskach kopii. FedEx, UPS lub przez firmę DHL numer konta jest wymagany dla wysyłania dysków z lokalizacji USA i Europie. Numer konta przez firmę DHL jest wymagany dla wysyłania dysków z lokalizacji Azja i klientów w Australii. Jeśli nie masz, możesz utworzyć [FedEx](http://www.fedex.com/us/oadr/) (dla Stanów Zjednoczonych i Europie) lub [przez firmę DHL](http://www.dhl.com/) konta operatora (Azja i klientów w Australii). Jeśli masz już operator numer konta, sprawdź, czy jest prawidłowa.

W wysyłania pakietów, należy wykonać warunki na [warunki usługi Microsoft Azure](https://azure.microsoft.com/support/legal/services-terms/).

> [!IMPORTANT]
> Należy pamiętać, że nośnik fizyczny, który jest dostarczany może być konieczne między granicami. Ponosisz odpowiedzialność za zapewnienie, że nośnik fizyczny i danych użytkownika są importowane i/lub wyeksportować zgodnie z prawem. Przed wysyłką nośnik fizyczny, sprawdź z Twojej doradcy do sprawdzenia, czy multimediów i danych legalnie mogą być wysyłane do centrum danych zidentyfikowane. Pomaga to zapewnić osiągnie firmy Microsoft, w odpowiednim czasie. Na przykład dowolny pakiet przecina granicami musi faktury do wraz z pakietem (z wyjątkiem Jeśli przekraczaniu granic w Unii Europejskiej). Można wydrukować kopię wypełniony faktury handlowej z operatora witryny sieci Web. Przykład faktur handlowych są [faktury DHL w sprawie](http://invoice-template.com/wp-content/uploads/dhl-commercial-invoice-template.pdf) i [faktury FedEx](http://images.fedex.com/downloads/shared/shipdocuments/blankforms/commercialinvoice.pdf). Upewnij się, że Microsoft nie zostały wskazane jako eksportera.
> 
> 

## <a name="how-does-the-azure-importexport-service-work"></a>Jak działa usługa Import/Eksport Azure?
Za przesyłanie danych między lokacją lokalną i usługi Azure storage za pomocą usługi Import/Eksport Azure zadań tworzenia i wysyłania dysków twardych do centrum danych platformy Azure. Każdy dysk twardy, który jest skojarzony z jednym zadaniu. Każde zadanie jest skojarzone z kontem magazynu jednego. Przegląd [sekcji wymagania wstępne](#pre-requisites) dokładnie poznać szczegółowe informacje na temat tej usługi, np. obsługiwane typy danych, dysk typów, lokalizacji i wysyłania.

W tej sekcji opisano wysokiego poziomu etapy importowanie i eksportowanie zadań. W dalszej [Szybki Start sekcji](#quick-start), podano instrukcje dotyczące tworzenia importowania i eksportowania zadania.

### <a name="inside-an-import-job"></a>Wewnątrz zadania importu
Na wysokim poziomie zadania importu obejmuje następujące kroki:

* Określ dane do zaimportowania, a liczba dysków, które są potrzebne.
* Określ lokalizację obiektów blob lub pliku docelowego dla danych w magazynie Azure.
* Użyj narzędzia WAImportExport, aby skopiować dane do jednego lub więcej dysków twardych i ich szyfrowania za pomocą funkcji BitLocker.
* Utwórz zadania importu na koncie magazynu docelowego przy użyciu portalu Azure lub interfejsu API REST importu i eksportu. Jeśli używasz portalu Azure, przekazywanie plików dziennika dysku.
* Podaj adres zwrotny i operatora numer konta używanego do wysyłania powrót do dysków.
* Należy najpierw wydać dyski twarde na adres wysyłkowy podany podczas tworzenia zadania.
* Zaktualizuj dostarczania śledzenia liczby w ramach szczegółów zadania importu i przesłać zadanie importu.
* Dyski są odbierane i przetwarzane w centrum danych Azure.
* Dyski są dostarczane przy użyciu konta operator adres zwrotny w zadania importu.
  
    ![Rysunek 1:Import przepływu pracy](./media/storage-import-export-service/importjob.png)

### <a name="inside-an-export-job"></a>Wewnątrz zadania eksportu
> [!IMPORTANT]
> Usługa obsługuje tylko eksportu obiektów blob Azure i nie obsługuje eksportowania plików Azure.
> 
>

Na wysokim poziomie zadania eksportu obejmuje następujące kroki:

* Określ danych do wyeksportowania, a liczba dysków, które są potrzebne.
* Określ źródło obiektów blob lub ścieżki kontenera danych w magazynie obiektów Blob.
* Utwórz zadanie eksportu na koncie magazynu źródłowego przy użyciu portalu Azure lub interfejsu API REST importu i eksportu.
* Określ obiekty BLOB źródła lub ścieżki kontenera danych przez zadanie eksportu.
* Podaj zwrotny adres i numer konta operator służący do wysyłania dyski powrót do.
* Należy najpierw wydać dyski twarde na adres wysyłkowy podany podczas tworzenia zadania.
* Zaktualizuj dostarczania śledzenia liczby szczegółów zadania eksportu i przesłać zadanie eksportu.
* Dyski są odbierane i przetwarzane w centrum danych Azure.
* Dyski są szyfrowane za pomocą funkcji BitLocker; klucze są dostępne za pośrednictwem portalu Azure.  
* Dyski są dostarczane przy użyciu konta operator adres zwrotny w zadania importu.
  
    ![Rysunek 2:Export przepływu pracy](./media/storage-import-export-service/exportjob.png)

### <a name="viewing-your-job-and-drive-status"></a>Wyświetlanie stan zadania i dysku
Można śledzić stan import lub wyeksportować zadań z portalu Azure. Kliknij przycisk **importu/eksportu** kartę. Na stronie zostanie wyświetlona lista zadań.

![Widok stanu zadania](./media/storage-import-export-service/jobstate.png)

Zostanie wyświetlony jeden z następujących stanów zadania w zależności od tego, w którym dysk jest w procesie.

| Stan zadania | Opis |
|:--- |:--- |
| Tworzenie | Po utworzeniu zadania, jego stan jest ustawiony do tworzenia. Gdy zadanie jest w stanie tworzenie, usługa Import/Eksport przyjęto założenie, że dyski nie zostały wysłane do centrum danych. Zadania mogą pozostawać w stanie tworzenie do dwóch tygodni, po których zostanie on automatycznie usunięty przez usługę. |
| Wysyłka | Po wysyłasz do pakietu, należy zaktualizować informacje o śledzeniu w portalu Azure.  To spowoduje wyłączenie zadania na "Wysyłki". Zadanie pozostanie w stanie wysyłania przez maksymalnie dwa tygodnie. 
| Odebrane | Po otrzymaniu wszystkich dysków w centrum danych, stan zadania można ustawić odebrane. |
| Transferowanie | Po rozpoczęciu przetwarzania co najmniej jeden dysk transferowanie będzie można ustawić stan zadania. Zobacz poniższą sekcję stany stacji, aby uzyskać szczegółowe informacje. |
| Pakowanie | Po zakończeniu wszystkich dysków przetwarzania, zadania zostaną umieszczone w stanie pakowania dopóki dyski są wysyłane z powrotem do. |
| Ukończone | Po wszystkie dyski zostały wysłane do klienta, jeśli zadanie zostało ukończone bez błędów, zadanie zostanie ustawiona do stanu ukończone. Zadania zostaną automatycznie usunięte po 90 dniach w stanie ukończone. |
| Zamknięte | Po wszystkie dyski zostały wysłane do klienta, jeśli pojawiły się błędy podczas przetwarzania zadania, zadanie zostanie ustawiona do stanie zamkniętym. Zadania zostaną automatycznie usunięte po 90 dniach w stanie zamkniętym. |

W poniższej tabeli opisano cyklu życia poszczególnych dyskach, ponieważ przechodzi ona za pomocą zadania importu lub eksportu. Bieżący stan każdego dysku w ramach zadania jest teraz widoczne w portalu Azure.
W poniższej tabeli opisano każdy stan każdego dysku w ramach zadania mogą przechodzić przez.

| Stan stacji | Opis |
|:--- |:--- |
| Określona | Dla zadania importu po utworzeniu zadania w portalu Azure, stan początkowy dla dysku jest w stanie określona. Dla zadania eksportu ponieważ dysk nie zostanie wskazany po utworzeniu zadania stanu początkowego dysk jest w stanie odebrane. |
| Odebrane | Dysk przejścia do stanu odebrane, gdy operator usługi Import/eksport został przetworzony dysków, które zostały odebrane od firmy wysyłania dla zadania importu. Dla zadania eksportu stan początkowy dysku jest w stanie Received. |
| NeverReceived | Dysk zostanie przeniesione do stanu NeverReceived po odebraniu pakietu dla zadania, ale pakiet nie zawiera dysku. Dysku można również przenosić w tym stanie, jeśli został dwa tygodnie od momentu usługa odebrała informacji dotyczących wysyłki, ale pakiet nie ma jeszcze dotarły centrum danych. |
| Transferowanie | Dysk zostanie przeniesione do stanu transferowanie, gdy usługa zaczyna się na przesyłanie danych z dysku do systemu Windows Azure Storage. |
| Ukończone | Dysku zostanie przejście do stanu ukończone, gdy usługa pomyślnie przeniósł wszystkich danych bez błędów.
| CompletedMoreInfo | Dysk będzie przejście do stanu CompletedMoreInfo, gdy usługa napotkał problemy podczas kopiowania danych z lub na dysku. Informacje mogą obejmować błędy, ostrzeżenia lub komunikaty informacyjne o zastępowaniu obiektów blob.
| ShippedBack | Dysk spowoduje przejście do stanu ShippedBack, gdy zostały wydane z tyłu centrum danych na adres zwrotny. |

Ten obraz z portalu Azure Wyświetla stan dysku przykładowe zadania:

![Wyświetl stan stacji](./media/storage-import-export-service/drivestate.png)

W poniższej tabeli opisano stanów awarii dysku i akcje wykonywane dla każdego stanu.

| Stan stacji | Wydarzenie | Rozdzielczość / następny krok |
|:--- |:--- |:--- |
| NeverReceived | Dysk, który jest oznaczony jako NeverReceived (ponieważ nie została odebrana jako część zadania wydania) dociera do innego wydania. | Zespół operacyjny przeniesie dysku stanie Received. |
| ND | Dysk, który nie jest częścią wszystkie zadania dociera do centrum danych w ramach innego zadania. | Dysk zostanie oznaczona jako dodatkowy dysk i zostanie zwrócony do klienta po zakończeniu zadania skojarzone z oryginalnego pakietu. |

### <a name="time-to-process-job"></a>Czas procesu zadania
Ilość czasu, jaki zajmuje to przetwarzanie zadania importu/eksportu zależy od wielu czynników, takich jak czas dostawy, obciążenia w centrum danych, typ zadania i rozmiar danych, w której są kopiowane, a liczba dysków w ramach zadania. Usługa Import/Eksport nie ma umowy dotyczącej poziomu usług, ale usługa dokłada starań, aby wykonać kopię w ciągu 7 do 10 dni po otrzymaniu dyski. Oprócz stanu opublikowane w portalu Azure interfejsów API REST można śledzić postęp zadania. Procent pełną parametr w wywołaniu interfejsu API operacji listy zadań zapewnia procent postępu kopiowania.

### <a name="pricing"></a>Cennik
**Opłata obsługę dysku**

Brak opłatą Obsługa dysku dla każdego dysku przetwarzane jako część import lub zadanie eksportowania. Zobacz szczegółowe informacje o [cennik Import/Eksport Azure](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Koszty wysyłki**

Podczas wydawania dysków na platformie Azure, płacisz koszt wysyłki do wysyłki operatora. Po powrocie do Ciebie dyski Microsoft koszt wysyłki obciążających konto operatora, który dostarczony w czasie tworzenia zadania.

**Koszty transakcji**

Podczas importowania danych do usługi Azure Storage są Brak kosztów transakcji oprócz kosztów transakcji magazynu w warstwie standardowa. Opłaty za wyjście standardowe są stosowane, gdy dane są eksportowane z magazynu obiektów Blob. Więcej szczegółów kosztów transakcji, zobacz [ceny transferu danych.](https://azure.microsoft.com/pricing/details/data-transfers/)



## <a name="how-to-import-data-into-azure-file-storage-using-internal-sata-hdds-and-ssds"></a>Jak do importowania danych do usługi Magazyn plików Azure przy użyciu wewnętrznego dysków twardych SATA i dysków SSD?
Wykonaj następujące czynności w przypadku danych na dysku do zaimportowania do usługi Magazyn plików Azure.
Pierwszym krokiem podczas importowania danych za pomocą usługi Import/Eksport Azure jest przygotowanie dysków za pomocą narzędzia WAImportExport. Wykonaj poniższe kroki, aby przygotować dyski.

1. Identyfikowanie danych do zaimportowania do usługi Magazyn plików Azure. Może to być katalogów i plików autonomicznych na serwerze lokalnym lub w udziale sieciowym.  
2. Określ liczbę dysków, które mają być w zależności od całkowity rozmiar danych. Uzyskaj wymaganej liczby 2,5 SSD 2,5-calowe lub 3,5" stacje dysków twardych SATA II lub III.
4. Ustal, katalogów i/lub plików autonomicznych, które zostaną skopiowane do każdego dysku twardego.
5. Utwórz plik CSV dla elementu dataset i driveset.
    
  Poniżej znajdują się jest przykład plik przykładowy zestaw danych CSV do importowania danych jako pliki Azure:
  
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\50M_original\100M_1.csv.txt","fileshare/100M_1.csv.txt",file,rename,"None",None
    "F:\50M_original\","fileshare/",file,rename,"None",None 
    ```
   W powyższym przykładzie 100M_1.csv.txt zostaną skopiowane do katalogu głównego "udziału plików". Jeśli "Udziału plików" nie istnieje, zostanie utworzony. Wszystkie pliki i foldery w obszarze 50M_original będzie cyklicznie skopiowane do udziału plików. Struktura folderów będzie przechowywany.

    Dowiedz się więcej o [przygotowania pliku CSV zestawu danych](storage-import-export-tool-preparing-hard-drives-import.md#prepare-the-dataset-csv-file).
    


    **Plik Driveset CSV**

    Wartość flagi driveset jest plik CSV, który zawiera listę dysków, do których litery dysków są mapowane w celu narzędzia poprawnie wybierz listę dysków można przygotować. 

    Poniżej znajduje się przykład driveset pliku CSV:
    
    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
    H,Format,SilentMode,Encrypt,
    ```

    W powyższym przykładzie zakłada się, że są dołączone dwa dyski i woluminy NTFS podstawowe G:\ litery woluminu i H:\ zostały utworzone. Narzędzie formatowania i szyfrowanie dysku, który jest hostem H:\ i nie będzie formatu lub szyfrowania dysku woluminu G:\ hosting.

    Dowiedz się więcej o [przygotowania pliku CSV driveset](storage-import-export-tool-preparing-hard-drives-import.md#prepare-initialdriveset-or-additionaldriveset-csv-file).

6.  Użyj [WAImportExport narzędzia](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExport.zip) można skopiować danych do jednego lub więcej dysków twardych.
7.  "Szyfruj" można określić w polu szyfrowania w drivset CSV, aby włączyć szyfrowanie funkcji BitLocker na dysku twardym. Alternatywnie można również włączyć szyfrowanie funkcją BitLocker ręcznie na dysku twardym i określ "AlreadyEncrypted" i podaj klucz w driveset CSV podczas uruchamiania narzędzia.

8. Po zakończeniu przygotowywania dysków nie należy modyfikować dane na dyski twarde lub pliku dziennika.

> [!IMPORTANT]
> Każdy dysk twardy, które należy przygotować spowoduje w pliku dziennika. Podczas tworzenia zadania importu przy użyciu portalu Azure, należy przesłać wszystkie pliki dziennika dysków, które są częścią tego zadania importu. Dyski bez arkusza, pliki nie zostaną przetworzone.
> 
>

Poniżej przedstawiono przykłady przygotowania dysk twardy za pomocą narzędzia WAImportExport i poleceń.

Polecenie PrepImport narzędzie WAImportExport dla pierwszej sesji kopiowania do skopiowania katalogów i/lub pliki w nowej sesji kopiowania:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
```

**Przykład importu 1**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

W celu **dodawanie kolejnych dysków**, co można utworzyć nowego pliku driveset i uruchom polecenie zgodnie z poniższymi instrukcjami. Sesje kolejnych kopii na różne dyski niż określona w pliku CSV InitialDriveset Określ plik CSV driveset i podać go jako wartość parametru "AdditionalDriveSet". Użyj **tego samego pliku dziennika** nazwy i podaj **nowy identyfikator sesji**. Format pliku AdditionalDriveset CSV jest taki sam jak InitialDriveSet format.

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
```

**Przykład importu 2**
```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
```

Aby można było dodać dodatkowe dane do tej samej driveset, narzędzie WAImportExport PrepImport polecenia można wywołać kolejnych kopii sesji skopiować dodatkowe pliki lub katalogu: dla kolejnych kopii sesji do tego samego stacje dysków twardych określonych w pliku CSV InitialDriveset, określ **tego samego pliku dziennika** nazwy i podaj **nowy identyfikator sesji**; nie istnieje potrzeba zapewnienie klucz konta magazynu.

```
WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
```

**Przykład importu 3**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

Dowiedz się więcej o używaniu narzędzia WAImportExport w [przygotowywania dysków twardych do importu](storage-import-export-tool-preparing-hard-drives-import.md).

Ponadto można znaleźć [przykładowego przepływu pracy, aby przygotować dyski twarde dla zadania importu](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md) Aby uzyskać szczegółowe instrukcje krok po kroku.  



## <a name="create-an-export-job"></a>Utwórz zadanie eksportu
Utwórz zadanie eksportu powiadomiono usługi Import/eksport, czy użytkownik będzie wysyłać jeden lub więcej dysków pusty centrum danych tak, aby dane można wyeksportować z konta magazynu dysków i stacje następnie wysłany do Ciebie.

### <a name="prepare-your-drives"></a>Przygotowanie dysków
Następujące testy wstępne są zalecane w przypadku przygotowywania dysków dla zadania eksportu:

1. Sprawdź liczbę dysków wymaganych przy użyciu narzędzia WAImportExport PreviewExport polecenia. Aby uzyskać więcej informacji, zobacz [przeglądania użycia dysków dla zadania eksportu](https://msdn.microsoft.com/library/azure/dn722414.aspx). Pomaga Podgląd użycia dysków dla obiektów blob, który wybrano, zależnie od rozmiaru dysków, które zamierzasz używać.
2. Sprawdź, czy użytkownik może odczytu/zapisu na dysku twardym, który ma zostać wysłane dla zadania eksportu.

### <a name="create-the-export-job"></a>Utwórz zadanie eksportu
1. Aby utworzyć zadanie eksportu, przejdź do większej liczby usług -> MAGAZYNU -> "zadania importu/eksportu" w portalu Azure. Kliknij przycisk **zadania importu/eksportu Utwórz**.
2. W kroku 1 podstawy wybierz pozycję "Eksportuj z Azure", wprowadź ciąg nazwy zadania, wybierz subskrypcję, wprowadź lub wybierz grupę zasobów. Wprowadź nazwę opisową dla zadania importu. Należy pamiętać, że wprowadzona nazwa może zawierać tylko małe litery, cyfry, łączniki i podkreślenia, musi zaczynać się literą i nie może zawierać spacji. Możesz użyć nazwy wybrane do śledzenia zadań, gdy są one w toku, a po zakończeniu. Podaj informacje kontaktowe osoby odpowiedzialne za to zadanie eksportu. 

3. W kroku 2 zadania szczegóły wybierz konto magazynu, które zostaną wyeksportowane dane z w sekcji konta magazynu. Lokalizacja przyjmowania zostanie wypełniona automatycznie na podstawie regionu wybrane konta magazynu. Określ dane obiektów blob, do których chcesz eksportować z konta magazynu do pustego dysku lub dysków. Możesz wyeksportować wszystkie dane obiektów blob na koncie magazynu, lub możesz określić, które obiekty BLOB lub ustawia obiektów blob, aby wyeksportować.
   
   Aby określić obiekt blob do wyeksportowania, użyj **równe** selektora i określ ścieżkę względną do obiektu blob, począwszy od nazwy kontenera. Użyj *$root* Aby określić kontener główny.
   
   Aby określić wszystkie obiekty BLOB, począwszy od prefiksu, użyj **rozpoczyna się od** selektora i określ prefiks, rozpoczynający się od ukośnika "/". Prefiks może być prefiksem nazwy kontenera, nazwa kontenera pełną lub nazwę kontenera ukończone, a następnie prefiks nazwy obiektu blob.
   
   W poniższej tabeli przedstawiono przykłady ścieżek nieprawidłowy obiekt blob:
   
   | Selektor | Ścieżka obiektu blob | Opis |
   | --- | --- | --- |
   | Rozpoczyna się od |/ |Eksportuje wszystkie obiekty BLOB na koncie magazynu |
   | Rozpoczyna się od |/$root / |Eksportuje wszystkie obiekty BLOB w kontenerze głównego |
   | Rozpoczyna się od |/Book |Eksportuje wszystkie obiekty BLOB w dowolnym kontenerze, który rozpoczyna się od prefiksu **książki** |
   | Rozpoczyna się od |/Music/ |Eksportuje wszystkie obiekty BLOB w kontenerze **utworów muzycznych** |
   | Rozpoczyna się od |/ music/love |Eksportuje wszystkie obiekty BLOB w kontenerze **utworów muzycznych** się od prefiksu **lubisz** |
   | Równa się |$root/logo.bmp |Eksportowanie obiektów blob **logo.bmp** w kontenerze głównego |
   | Równa się |videos/Story.mp4 |Eksportowanie obiektów blob **story.mp4** w kontenerze **wideo** |
   
   Należy podać ścieżki obiektu blob w prawidłowe formaty, aby uniknąć błędów podczas przetwarzania, jak pokazano na tym zrzucie ekranu.
   
   ![Utwórz zadanie eksportu — krok 3](./media/storage-import-export-service/export-job-03.png)

4. W kroku 3 powrócić wysyłania informacji Wybierz nośnik z listy rozwijanej i wprowadź numer konta nieprawidłowy operator, które zostały utworzone z tego nośnika. Firma Microsoft będzie używać tego konta na potrzeby wysłania dysków powrotem po zakończeniu zadania importu. Podaj nazwisko osoby kontaktowej w pełne i prawidłowe, telefonu, poczty e-mail, adres, Miasto, zip, stan/proviince i kraj/region.
   
 5. Na stronie Podsumowanie podano adres wysyłkowy centrum danych Azure do zastosowania w przypadku wysyłania dysków do kontrolera domeny usługi Azure. Upewnij się, są wymienione nazwy zadania i pełny adres na etykiecie wysyłki. 

6. Kliknij przycisk OK, na stronie Podsumowanie, aby zakończyć tworzenie zadania importu

7. Po wysyłki na dyskach, wróć do **importu/eksportu** strony w portalu Azure) przejdź i zadania importu, kliknij polecenie b) kliknij **zaktualizować stan zadania i informacje o śledzeniu, gdy dyski są dostarczane**. 
     c) zaznacz pole wyboru "Oznacz jako wydane" d) Podaj numer operatora oraz śledzenie.
    
   Jeśli numer nie zostanie zaktualizowane w ciągu 2 tygodni tworzenia zadania, zadanie wygaśnie.
   
8. Postęp zadania można śledzić na pulpicie nawigacyjnym portalu. Zobacz, co oznacza przez każdy stan zadania w poprzedniej sekcji [wyświetlanie stan zadania](#viewing-your-job-status).

   > [!NOTE]
   > Jeśli do wyeksportowania obiektu blob jest używany w czasie kopiowania na dysku twardym, usługi Import/Eksport Azure zostanie utworzenie migawki obiektu blob i skopiuj migawki.
   > 
   > 
 
9. Po otrzymaniu dysków z wyeksportowane dane, można wyświetlać i skopiować klucze funkcji BitLocker, generowane przez usługę dla dysku. Przejdź do wyeksportowania zadania w portalu Azure, a następnie kliknij kartę importu i eksportu. Wybierz z listy zadanie eksportu, a następnie kliknij opcję klucze funkcji BitLocker. Klucze funkcji BitLocker są wyświetlane, jak pokazano poniżej:
   
   ![Wyświetl klucze funkcji BitLocker dla zadania eksportu](./media/storage-import-export-service/export-job-bitlocker-keys.png)

Przeczytaj poniższą sekcję Często zadawane pytania dotyczące jako obejmuje on typowe pytania napotykanych przez klientów podczas korzystania z tej usługi.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Można skopiować magazyn plików Azure za pomocą usługi Import/Eksport Azure?**

Tak, usługa Import/Eksport Azure obsługuje importowanie do magazynu plików Azure. Nie obsługuje eksportowanie plików Azure w tej chwili.

**To jest usługa Import/Eksport Azure dostępne dla dostawcy usług Kryptograficznych subskrypcji?**

Usługa Import/Eksport Azure obsługuje subskrypcje dostawcy usług Kryptograficznych.

**Można pominąć krok przygotowania dysku dla zadania importu lub czy można przygotować dysku bez kopiowania?**

Za pomocą narzędzia Azure WAImportExport należy przygotować dowolnego dysku, który chcesz wysłać do importowania danych. Aby skopiować dane na dysku, należy użyć narzędzia WAImportExport.

**Należy wykonać wszelkie przygotowanie dysku podczas tworzenia zadania eksportu?**

Wstępne sprawdzanie nie, ale niektóre są zalecane. Sprawdź liczbę dysków wymaganych przy użyciu narzędzia WAImportExport PreviewExport polecenia. Aby uzyskać więcej informacji, zobacz [przeglądania użycia dysków dla zadania eksportu](https://msdn.microsoft.com/library/azure/dn722414.aspx). Pomaga Podgląd użycia dysków dla obiektów blob, który wybrano, zależnie od rozmiaru dysków, które zamierzasz używać. Ponadto sprawdź, czy można odczytywać i zapisywać na dysku twardym, który ma zostać wysłane dla zadania eksportu.

**Co się stanie, obsługiwane wymagania przypadkowego wysłania dysk twardy, który nie jest zgodna z?**

Centrum danych Azure zwróci dysk, który nie jest zgodna z obsługiwanych wymagania dla Ciebie. Jeśli tylko niektóre z dysków w pakiecie wymagań obsługi tych dyskach zostaną przetworzone, a dyski, które nie spełniają wymagań, zostanie zwrócony do Ciebie.

**Czy można anulować Moje zadanie?**

Można anulować zadania, gdy jego stan to tworzenie lub wysyłki.

**Jak długo wyświetlanie stanu zadań zakończonych w portalu Azure**

Można wyświetlić stan zakończonych zadań do 90 dni. Zakończonych zadań zostaną usunięte po 90 dniach.

**Jeśli Chcę importowania lub eksportowania więcej niż 10 dysków, co należy zrobić?**

Jeden importu lub eksportu zadanie może odwoływać się tylko 10 dysków w jednym zadaniu usługi Import/Eksport. Jeśli użytkownik chce wysłać więcej niż 10 dysków, można utworzyć wiele zadań. Dyski, które są skojarzone z tym samym zadaniu muszą być wysłane razem w tym samym pakiecie.
Firma Microsoft oferuje wskazówki i pomoc w przypadku możliwości danych obejmuje wiele dysków zadania importu. Skontaktuj się z bulkimport@microsoft.com Aby uzyskać więcej informacji

**Usługa sformatować dyski przed zwróceniem?**

Nie. Wszystkie dyski są szyfrowane za pomocą funkcji BitLocker.

**Firma Microsoft może zakupić dysków dla zadania importu/eksportu?**

Nie. Należy wysłać dysków dla importu i eksportu zadania.

** Jak mają dostęp do danych zaimportowanych przez usługę **

Dane na koncie magazynu Azure są dostępne za pośrednictwem portalu Azure lub za pomocą narzędzia Autonomiczny wywołana Eksploratora usługi Storage. https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer 

**Po zakończeniu zadania importu będzie moich danych jak wygląda na koncie magazynu? Zostaną zachowane Moje hierarchii katalogów?**

Podczas przygotowywania dysk twardy dla zadania importu, miejsce docelowe jest określony przez pole DstBlobPathOrPrefix w zestawie danych CSV. Jest to docelowy kontener na koncie magazynu, do którego jest kopiowany danych z dysku twardego. W tym docelowy kontener katalogi wirtualne są tworzone foldery z dysku twardego i obiekty BLOB są tworzone dla plików. 

**Jeśli dysk ma plików, które już istnieją w moim koncie magazynu, usługa zastąpią istniejące obiekty BLOB lub pliki w moim koncie magazynu?**

Podczas przygotowywania dysku, można określić czy pliki docelowy powinien zostać zastąpione lub ignorowane za pomocą pola w pliku CSV zestawu danych o nazwie dyspozycji: < zmienić | zastąpić nie | zastąpić >. Domyślnie usługa będzie nowych plików zamiast zastąpić istniejące obiekty BLOB lub plików.

**Narzędzie WAImportExport jest zgodny z 32-bitowych systemach operacyjnych?**
Nie. Narzędzie WAImportExport jest zgodna tylko z 64-bitowych systemach operacyjnych Windows. Można znaleźć w sekcji dotyczącej systemów operacyjnych w [wstępne](#pre-requisites) pełną listę obsługiwanych wersji systemu operacyjnego.

**Należy umieszczać innym niż dysk twardy w moim pakiecie?**

Wyślij tylko dyski twarde. Nie dołączaj elementy, takie jak power przewodów zasilania lub USB.

**Czy muszę wysłać Moje dysków przy użyciu FedEx lub przez firmę DHL?**

Mogą być dysków do centrum danych za pomocą dowolnego znanego operatora jak FedEx, DHL w sprawie, UPS lub poczty amerykańskiej. Jednak dla wysyłania dysków powrót do centrum danych, należy podać numer konta FedEx w Stanach Zjednoczonych i Europa lub liczbą konta przez firmę DHL w regionach Azja i klientów w Australii.

**Czy istnieją ograniczeń z wysyłki za granicę dysk?**

Należy pamiętać, że nośnik fizyczny, który jest dostarczany może być konieczne między granicami. Ponosisz odpowiedzialność za zapewnienie, że nośnik fizyczny i danych użytkownika są importowane i/lub wyeksportować zgodnie z prawem. Przed wysyłką nośnik fizyczny, sprawdź z Twojej doradcy do sprawdzenia, czy multimediów i danych legalnie mogą być wysyłane do centrum danych zidentyfikowane. Pomoże to w celu zapewnienia osiągnie firmy Microsoft, w odpowiednim czasie.

**Podczas tworzenia zadania, adres wysyłkowy jest lokalizacji, która różni się od Moje Lokalizacja konta magazynu. Co zrobić?**

Niektóre lokalizacje konta magazynu są mapowane do lokalizacji alternatywnej wysyłki. Wcześniej dostępne lokalizacje wysyłanie można również tymczasowo mapować do alternatywnej lokalizacji. Zawsze należy sprawdzić adres wysyłkowy podany podczas tworzenia zadania przed dostarczeniem dysków.

**Podczas wysyłania dysk, operatora monituje o podanie danych Centrum kontaktu adres i numer telefonu. Co należy zapewnić?**

Numer telefonu i kontroler domeny adresów jest dostępna w ramach zadania tworzenia.

**Aby skopiować PST skrzynki pocztowe i danych programu SharePoint dla usługi Office 365 można użyć usługi Import/Eksport Azure?**

Zapoznaj się z [PST importowanie plików lub danych programu SharePoint do usługi Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

**Aby skopiować kopiach zapasowych w trybie offline z usługą kopia zapasowa Azure można używać usługi Import/Eksport Azure?**

Zapoznaj się z [przepływu pracy w trybie Offline z kopii zapasowej w programie Kopia zapasowa Azure](../../backup/backup-azure-backup-import-export.md).

**Co to jest maksymalna liczba dysków Twardych dla w jednym wydaniu?**

Dowolna liczba dysków twardych może być w jednym wydaniu i jeśli dyski należą do wielu zadań zalecane jest) ma dysk etykietą nazwy zadania. (b) zaktualizować zadania o numerze śledzenia, sufiks na -1,-2 itd.
  
**Jaka jest maksymalna blokowych obiektów Blob i rozmiar obiektu Blob strony obsługiwane przez dysk importu/eksportu?**

Rozmiar maksymalny blokowych obiektów Blob to około 4.768TB lub 5,000,000 MB.
Rozmiar obiektu Blob strony maksymalna liczba wynosi 1TB.

**Dysk importu/eksportu obsługuje szyfrowania AES 256?**

Usługa Import/Eksport Azure domyślnie szyfruje za pomocą szyfrowania AES 128 funkcji bitlocker, ale to należy odpowiednio zwiększyć AES 256 szyfrując ręcznie za pomocą funkcji bitlocker przed skopiowaniem danych. 

Jeśli przy użyciu [WAImportExport V1](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip), poniżej przedstawiono przykład polecenia
```
WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>] 
```
Jeśli przy użyciu [narzędzie WAImportExport](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExport.zip) określ "AlreadyEncrypted" i podaj klucz w driveset CSV.
```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
```
## <a name="next-steps"></a>Kolejne kroki

* [Trwa konfigurowanie narzędzia WAImportExport](storage-import-export-tool-how-to.md)
* [Transfer danych za pomocą narzędzia wiersza polecenia AzCopy](storage-use-azcopy.md)
* [Przykładowe interfejsu API REST wyeksportować importu platformy Azure](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)


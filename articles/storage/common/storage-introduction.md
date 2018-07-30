---
title: Wprowadzenie do usługi Azure Storage — magazynu w chmurze na platformie Azure | Microsoft Docs
description: Usługa Azure Storage to rozwiązanie do magazynowania w chmurze firmy Microsoft. Usługa Azure Storage oferuje magazyn dla obiektów danych cechujący się wysoką dostępnością, bezpieczeństwem, trwałością, wysoką skalowalnością i nadmiarowością.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: get-started-article
ms.date: 07/11/2018
ms.author: tamram
ms.openlocfilehash: e6a3ff22d6224ff0accdf39ad098505626659688
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2018
ms.locfileid: "39238254"
---
# <a name="introduction-to-azure-storage"></a>Wprowadzenie do usługi Azure Storage

Usługa Azure Storage to rozwiązanie do magazynowania w chmurze firmy Microsoft dla nowoczesnych scenariuszy magazynowania danych. Usługa Azure Storage oferuje wysoce skalowalny magazyn obiektów dla obiektów danych, usługę systemu plików dla chmury, magazyn obsługi komunikatów dla niezawodnej obsługi komunikatów i magazyn danych NoSQL. Usługa Azure Storage to:

- **Niezawodność i wysoka dostępność.** Nadmiarowość zapewnia bezpieczeństwo danych w przypadku przejściowych awarii sprzętu. Dla dodatkowej ochrony przed lokalnymi katastrofami lub klęskami żywiołowymi można również zdecydować się na replikowanie danych między centrami danych lub regionami geograficznymi. Dane replikowane w ten sposób pozostają wysoce dostępne w przypadku awarii. 
- **Bezpieczeństwo.** Wszystkie dane zapisane w usłudze Azure Storage są przez nią szyfrowane. Usługa Azure Storage zapewnia precyzyjną kontrolę tego, kto ma dostęp do danych.
- **Skalowalność.** Usługa Azure Storage została zaprojektowana jako wysoce skalowalne rozwiązanie spełniające potrzeby związane z magazynowaniem danych i wydajnością współczesnych aplikacji. 
- **Zarządzanie.** Usługa Microsoft Azure obsługuje za Ciebie konserwację i wszelkie problemy krytyczne.
- **Dostępność.** Dane w usłudze Azure Storage są dostępne z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. Firma Microsoft udostępnia zestawy SDK usługi Azure Storage w różnych językach — .NET, Java, Node.js, Python, PHP, Ruby, Go i innych — oraz dojrzały interfejs API REST. Usługa Azure Storage obsługuje skrypty programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Witryna Azure Portal oraz Eksplorator usługi Azure Storage oferują proste rozwiązania wizualne do pracy z danymi.  

## <a name="azure-storage-services"></a>Usługi Azure Storage

Usługa Azure Storage obejmuje następujące usługi danych: 

- [Azure Blobs](../blobs/storage-blobs-introduction.md): wysoce skalowalny magazyn obiektów dla danych tekstowych i binarnych.
- [Azure Files](../files/storage-files-introduction.md): zarządzane udziały plików dla wdrożeń lokalnych lub w chmurze.
- [Azure Queues](../queues/storage-queues-introduction.md): magazyn obsługi komunikatów zapewniający niezawodną obsługę komunikatów między składnikami aplikacji. 
- [Azure Tables](../tables/table-storage-overview.md): magazyn obiektów NoSQL do magazynowania ustrukturyzowanych danych bez użycia schematu.

Dostęp do każdej usługi uzyskuje się za pośrednictwem konta magazynu. Aby rozpocząć, zobacz artykuł [Tworzenie konta magazynu](storage-quickstart-create-account.md).

## <a name="blob-storage"></a>Blob Storage

Azure Blob Storage to rozwiązanie do magazynowania obiektów w chmurze firmy Microsoft. Usługa Blob Storage jest zoptymalizowana pod kątem przechowywania olbrzymich ilości danych bez struktury, takich jak dane tekstowe lub binarne. 

Usługa Blob Storage to idealne rozwiązanie w następujących przypadkach:

* Obsługiwanie obrazów i dokumentów bezpośrednio w przeglądarce.
* Przechowywanie plików do dostępu rozproszonego.
* Przesyłanie strumieniowe audio i wideo.
* Zapisywanie danych w celu tworzenia kopii zapasowych, przywracania, odzyskiwania po awarii i archiwizowania.
* Przechowywanie danych w celu analizy w usłudze lokalnej lub hostowanej na platformie Azure.

Dostęp do obiektów w usłudze Blob Storage można uzyskać z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. Użytkownicy lub aplikacje klienckie mogą uzyskiwać dostęp do obiektów blob za pomocą adresów URL, [interfejsu API REST usługi Azure Storage](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [programu Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/storage) lub biblioteki klienta usługi Azure Storage. Biblioteki klienta magazynu są dostępne dla wielu języków, w tym [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage/client), [Node.js](http://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/en/latest/index.html), [PHP](http://azure.github.io/azure-storage-php/) i [Ruby](http://azure.github.io/azure-storage-ruby).

Aby uzyskać więcej informacji dotyczących usługi Blob Storage, zobacz artykuł [Introduction to object storage on Azure (Wprowadzenie do magazynowania obiektów na platformie Azure)](../blobs/storage-blobs-introduction.md).

## <a name="azure-files"></a>Azure Files
Usługa [Azure Files](../files/storage-files-introduction.md) umożliwia konfigurowanie wysoce dostępnych udziałów plików sieciowych, do których można uzyskiwać dostęp przy użyciu standardowego protokołu bloku komunikatów serwera (SMB, Server Message Block). Oznacza to, że wiele maszyn wirtualnych może współdzielić te same pliki z dostępem zarówno do odczytu, jak i do zapisu. Pliki można także odczytywać przy użyciu interfejsu REST lub bibliotek klienckich magazynu.

Jedną z różnic między usługą Azure Files i plikami w firmowym udziale plików jest możliwość dostępu do plików z dowolnego miejsca na świecie przy użyciu adresu URL, który wskazuje plik i zawiera token sygnatury dostępu współdzielonego. Tokeny sygnatury dostępu współdzielonego można generować. Umożliwiają one uzyskanie określonego dostępu do prywatnego zasobu przez określony czas.

Udziałów plików można używać w wielu typowych scenariuszach:

* Wiele aplikacji lokalnych korzysta z udziałów plików. Dzięki temu migracja tych aplikacji współdzielących dane na platformę Azure jest łatwiejsza. Jeśli zainstalujesz udział plików przy użyciu tej samej litery dysku, która jest używana przez aplikację lokalną, część aplikacji uzyskująca dostęp do udziału plików powinna działać bez konieczności wprowadzania zmian lub jedynie z minimalnymi zmianami.

* Pliki konfiguracji można przechowywać w udziale plików i uzyskiwać do nich dostęp z wielu maszyn wirtualnych. W udziale plików można przechowywać narzędzia i programy narzędziowe używane przez wielu deweloperów w grupie, dzięki czemu wszyscy będą mogli je znaleźć oraz będą używać tych samych wersji.

* Dzienniki diagnostyczne, metryki i zrzuty awaryjne to tylko trzy przykłady danych, które można zapisywać w udziale plików i przetwarzać lub analizować później.

Obecnie uwierzytelnianie oparte na usłudze Active Directory i listy kontroli dostępu (ACL, access control list) nie są obsługiwane, ale ich obsługa zostanie dodana w przyszłości. W celu uwierzytelniania dostępu do udziału plików są używane poświadczenia konta magazynu. Oznacza to, że każda osoba z zainstalowanym udziałem będzie miała do niego pełny dostęp do odczytu i zapisu.

Aby uzyskać więcej informacji na temat usługi Azure Files, zobacz [Wprowadzenie do usługi Azure Files](../files/storage-files-introduction.md).

## <a name="queue-storage"></a>Queue Storage

Usługa Azure Queue jest używana do przechowywania i pobierania komunikatów. Komunikaty kolejek mogą mieć rozmiar do 64 KB, a jedna kolejka może zawierać miliony komunikatów. Kolejki są zazwyczaj używane do przechowywania list komunikatów, które mają zostać przetworzone asynchronicznie.

Przykładowo załóżmy, że chcesz, aby klienci mogli przekazywać obrazy, i chcesz utworzyć miniatury dla każdego obrazu. Klient może poczekać, aż utworzysz miniatury podczas przekazywania zdjęcia. Alternatywą jest zastosowanie kolejki. Gdy klient zakończy przekazywanie, zapisz komunikat w kolejce. Następnie funkcja usługi Azure Functions pobierze ten komunikat z kolejki i utworzy miniatury. Każdą część tego przetwarzania można skalować oddzielnie, co daje większą kontrolę podczas dostosowywania.

Aby uzyskać więcej informacji na temat usługi Azure Queues, zobacz [Wprowadzenie do usługi Azure Queues](../queues/storage-queues-introduction.md).

## <a name="table-storage"></a>Magazyn tabel

Usługa Azure Table Storage jest teraz częścią usługi Azure Cosmos DB. Aby zapoznać się z dokumentacją usługi Azure Table Storage, przejdź do artykułu [Omówienie usługi Azure Table Storage](../tables/table-storage-overview.md). Oprócz istniejącej usługi Azure Table Storage dostępna jest nowa oferta interfejsu API tabel usługi Azure Cosmos DB, który zapewnia tabele zoptymalizowane pod kątem przepływności, globalną dystrybucję i automatyczne indeksy pomocnicze. Aby dowiedzieć się więcej i wypróbować nowe środowisko wersji Premium, zobacz [Interfejs API tabel usługi Azure Cosmos DB](https://aka.ms/premiumtables).

Aby uzyskać więcej informacji na temat usługi Table Storage, zobacz [Omówienie usługi Azure Table Storage](../tables/table-storage-overview.md).

## <a name="disk-storage"></a>Przechowywanie na dysku

Usługa Azure Storage obejmuje również wszystkie funkcje dysków zarządzanych i niezarządzanych, z których korzystają maszyny wirtualne. Aby uzyskać więcej informacji na temat tych funkcji, zobacz [dokumentację usług Compute](https://docs.microsoft.com/en-gb/azure/#pivot=products&panel=Compute).

## <a name="types-of-storage-accounts"></a>Typy kont magazynu

W poniższej tabeli przedstawiono różne rodzaje kont magazynu i obiekty, których można używać z każdym z nich.

|**Typ konta magazynu**|**Przeznaczenie ogólne w warstwie Standardowa**|**Przeznaczenie ogólne w warstwie Premium**|**Usługa Blob Storage w gorącej i chłodnej warstwie dostępu**|
|-----|-----|-----|-----|
|**Obsługiwane usługi**| Usługi Blob, File i Queue | Usługa Blob | Usługa Blob|
|**Obsługiwane typy obiektów blob**|Blokowe obiekty blob, stronicowe obiekty blob, uzupełnialne obiekty blob | Stronicowe obiekty blob | Blokowe obiekty blob i uzupełnialne obiekty blob|

### <a name="general-purpose-storage-accounts"></a>Konta magazynu ogólnego przeznaczenia

Istnieją dwa rodzaje kont magazynu ogólnego przeznaczenia.

#### <a name="standard-storage"></a>Standard Storage

Najczęściej używane konta magazynu to konta usługi Standard Storage, których można używać dla wszystkich typów danych. W ramach kont usługi Standard Storage dane są przechowywane na nośnikach magnetycznych.

#### <a name="premium-storage"></a>Premium Storage

Usługa Premium Storage zapewnia magazyn o wysokiej wydajności dla stronicowych obiektów blob, które są najczęściej używane w przypadku plików VHD. W ramach kont usługi Premium Storage dane są przechowywane na dyskach SSD. Firma Microsoft zaleca używanie usługi Premium Storage dla wszystkich maszyn wirtualnych.

### <a name="blob-storage-accounts"></a>Konta usługi Blob Storage

Konto usługi Blob Storage to specjalne konto magazynu używane do przechowywania blokowych obiektów blob i uzupełnialnych obiektów blob. Na tych kontach nie można przechowywać stronicowych obiektów blob, a zatem nie można też przechowywać plików VHD. Te konta umożliwiają ustawienie warstwy dostępu Gorąca lub Chłodna. Warstwę można zmienić w dowolnym momencie.

Warstwa dostępu Gorąca jest używana w przypadku często używanych plików — koszt magazynowania jest wyższy, ale koszt uzyskiwania dostępu do obiektów blob jest znacznie niższy. W przypadku obiektów blob przechowywanych w warstwie dostępu Chłodna koszt uzyskiwania dostępu do obiektów blob jest wyższy, ale koszt magazynowania jest znacznie niższy.

## <a name="accessing-your-blobs-files-and-queues"></a>Uzyskiwanie dostępu do obiektów blob, plików i kolejek

Każde konto magazynu ma dwa klucze uwierzytelniania, przy czym każdego z nich można użyć do dowolnej operacji. Klucze są dwa, więc od czasu do czasu można je przerzucić w celu zwiększenia poziomu bezpieczeństwa. Zabezpieczenie tych kluczy jest bardzo ważne, ponieważ posiadanie ich wraz z nazwą konta daje nieograniczony dostęp do wszystkich danych na koncie magazynu.

W tej sekcji omówiono dwa sposoby zabezpieczania konta magazynu i jego danych. Aby uzyskać szczegółowe informacje na temat zabezpieczania konta magazynu i jego danych, zobacz [Przewodnik po zabezpieczeniach usługi Azure Storage](storage-security-guide.md).

### <a name="securing-access-to-storage-accounts-using-azure-ad"></a>Zabezpieczanie dostępu do kont magazynu przy użyciu usługi Azure AD

Jednym ze sposobów na zabezpieczenie danych magazynu jest kontrolowanie dostępu do kluczy konta magazynu. Dzięki kontroli dostępu opartej na rolach (RBAC, Role-Based Access Control) w usłudze Resource Manager możesz przypisywać role użytkownikom, grupom i aplikacjom. Te role są powiązane z określonym zestawem akcji, które są dozwolone lub niedozwolone. Udzielanie dostępu przy użyciu kontroli dostępu opartej na rolach obsługuje tylko operacje zarządzania dla tego konta magazynu, na przykład zmianę warstwy dostępu. Przy użyciu kontroli dostępu opartej na rolach nie można udzielać dostępu do obiektów danych takich jak określony kontener lub udział plików. Za pomocą kontroli dostępu opartej na rolach można natomiast udzielać dostępu do kluczy konta magazynu, przy użyciu których można następnie odczytać obiekty danych.

### <a name="securing-access-using-shared-access-signatures"></a>Zabezpieczanie dostępu przy użyciu sygnatur dostępu współdzielonego

Obiekty danych możesz zabezpieczyć przy użyciu sygnatur dostępu współdzielonego i przechowywanych zasad dostępu. Sygnatura dostępu współdzielonego to ciąg zawierający token zabezpieczający, który można dołączyć do identyfikatora URI dla zasobu, co umożliwia delegowanie dostępu do określonych obiektów oraz określanie ograniczeń takich jak uprawnienia oraz zakres dat/godzin dostępu. Ta funkcja ma rozbudowane możliwości. Zobacz [Używanie sygnatur dostępu współdzielonego (SAS)](storage-dotnet-shared-access-signature-part-1.md), aby uzyskać szczegółowe informacje.

### <a name="public-access-to-blobs"></a>Dostęp publiczny do obiektów blob

Usługa Blob umożliwia zapewnienie publicznego dostępu do kontenera i jego obiektów blob lub do konkretnego obiektu blob. Po wskazaniu, że kontener lub obiekt blob jest publiczny, dowolny użytkownik może go anonimowo odczytać — uwierzytelnianie nie jest wymagane. Można to zrobić na przykład w przypadku witryny internetowej korzystającej z obrazów, klipów wideo lub dokumentów z usługi Blob Storage. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem anonimowym w trybie odczytu do kontenerów i obiektów blob](../blobs/storage-manage-access-to-resources.md)

## <a name="encryption"></a>Szyfrowanie

W przypadku usług Storage dostępne są dwa podstawowe rodzaje szyfrowania. Aby uzyskać więcej informacji na temat bezpieczeństwa i szyfrowania, zobacz [Azure Storage security guide (Przewodnik po zabezpieczeniach usługi Azure Storage)](storage-security-guide.md).

### <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku

Szyfrowanie usługi Azure Storage w spoczynku pomaga chronić dane zgodnie z wymaganiami organizacji w zakresie zabezpieczeń i zgodności. Przy użyciu tej funkcji usługa Azure Storage automatycznie szyfruje dane przed trwałym wprowadzeniem ich do magazynu i odszyfrowuje je przed pobraniem. Szyfrowanie, odszyfrowywanie i zarządzanie kluczami jest całkowicie przezroczyste dla użytkowników.


Usługa SSE automatycznie szyfruje dane we wszystkich warstwach wydajności (Standardowa i Premium), wszystkich modelach wdrażania (model usługi Azure Resource Manager i model klasyczny) oraz wszystkich usługach Azure Storage (Blob, Queue, Table i File). Usługa SSE nie wpływa na wydajność usługi Azure Storage.

Aby uzyskać więcej informacji o szyfrowaniu usługi Storage w spoczynku, zobacz [Szyfrowanie usługi Azure Storage dla danych magazynowanych](storage-service-encryption.md).

### <a name="client-side-encryption"></a>Szyfrowania po stronie klienta

Biblioteki klienta magazynu posiadają metody, które możesz wywołać, aby programowo szyfrować dane przed wysłaniem ich z klienta do platformy Azure. Przechowywane dane są zaszyfrowane, co oznacza, że w stanie spoczynku również są zaszyfrowane. Podczas odczytywania informacji są one odszyfrowywane po ich otrzymaniu.

Aby uzyskać więcej informacji o szyfrowaniu po stronie klienta, zobacz [Client-Side Encryption with .NET for Microsoft Azure Storage (Szyfrowanie po stronie klienta przy użyciu programu .NET dla usługi Microsoft Azure Storage)](storage-client-side-encryption.md).

## <a name="replication"></a>Replikacja

Aby zapewnić trwałość danych, usługa Azure Storage przeprowadza replikację wielu kopii danych. Podczas konfigurowania konta magazynu należy określić typ replikacji. W większości przypadków to ustawienie można zmodyfikować po skonfigurowaniu konta magazynu. 

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Aby uzyskać informacje na temat odzyskiwania po awarii, zobacz [Co zrobić po wystąpieniu awarii usługi Azure Storage](storage-disaster-recovery-guidance.md).

## <a name="transferring-data-to-and-from-azure-storage"></a>Transferowanie danych do i z usługi Azure Storage

Za pomocą narzędzia wiersza polecenia AzCopy możesz kopiować obiekty blob, pliki i dane w ramach jednego lub wielu kont magazynu. Zobacz jeden z następujących artykułów, aby uzyskać pomoc:

* [Transferowanie danych za pomocą narzędzia AzCopy dla systemu Windows](storage-use-azcopy.md)
* [Transferowanie danych za pomocą narzędzia AzCopy dla systemu Linux](storage-use-azcopy-linux.md)

Podstawą narzędzia AzCopy jest [biblioteka przenoszenia danych platformy Azure](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/), która jest aktualnie dostępna w wersji zapoznawczej.

Przy użyciu usługi Azure Import/Export można importować lub eksportować duże ilości danych obiektów blob do lub z konta magazynu. Należy przygotować wiele dysków twardych i wysłać je do centrum danych platformy Azure. Dane zostaną przetransferowane z dysków twardych lub na dyski twarde, po czym dyski zostaną odesłane. Aby uzyskać więcej informacji o usłudze Import/Export, zobacz [Przesyłanie danych do usługi Blob Storage za pomocą usługi Microsoft Azure Import/Export](../storage-import-export-service.md).

W celu importowania dużych ilości danych obiektów blob do konta magazynu w szybki, niedrogi i niezawodny sposób, możesz również użyć usługi Azure Data Box Disk. Firma Microsoft dostarcza do 5 zaszyfrowanych dysków półprzewodnikowych (SSD, solid-state drive) o pojemności 40 TB do Twojego centrum danych za pomocą regionalnego przewoźnika. Szybko konfigurujesz dyski, kopiujesz na nie dane za pomocą połączenia USB i odsyłasz z powrotem do zespołu platformy Azure. W centrum danych platformy Azure dane są automatycznie przekazywane z dysków do chmury. Aby uzyskać więcej informacji na temat tego rozwiązania, zobacz [Azure Data Box Disk — omówienie](https://docs.microsoft.com/azure/databox/data-box-disk-overview).

## <a name="pricing"></a>Cennik

Aby uzyskać szczegółowe informacje o cenach usługi Azure Storage, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="storage-apis-libraries-and-tools"></a>Narzędzia, biblioteki oraz interfejsy API usługi Storage
Zasoby usługi Azure Storage są dostępne za pomocą dowolnego języka, który obsługuje żądania HTTP/HTTPS. Dodatkowo Magazyn Azure oferuje biblioteki programistyczne dla kilku popularnych języków. Te biblioteki upraszczają wiele aspektów pracy z usługą Azure Storage dzięki obsłudze szczegółów, takich jak wywołania synchroniczne i asynchroniczne, przetwarzanie wsadowe operacji, zarządzanie wyjątkami, automatyczne ponawianie, zachowania podczas działania itd. Biblioteki są obecnie dostępne dla następujących języków i platform, a kolejne są planowane:

### <a name="azure-storage-data-api-and-library-references"></a>Dokumentacja bibliotek i interfejsów API danych usługi Azure Storage
* [Interfejs API REST usług Storage](https://docs.microsoft.com/rest/api/storageservices/)
* [Biblioteka klienta usługi Storage dla platformy .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
* [Biblioteka klienta usługi Storage dla języka Java w systemie Android](https://docs.microsoft.com/java/api/overview/azure/storage)
* [Biblioteka klienta usługi Storage dla oprogramowania Node.js](https://docs.microsoft.com/javascript/api/azure-storage)
* [Biblioteka klienta usługi Storage dla języka Python](https://github.com/Azure/azure-storage-python)
* [Biblioteka klienta usługi Storage dla języka PHP](https://github.com/Azure/azure-storage-php)
* [Biblioteka klienta usługi Storage dla języka Ruby](https://github.com/Azure/azure-storage-ruby)
* [Biblioteka klienta usługi Storage dla języka C++](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>Dokumentacja bibliotek i interfejsów API zarządzania usługi Azure Storage
* [Interfejs API REST dostawcy zasobów usługi Storage](https://docs.microsoft.com/rest/api/storagerp/)
* [Biblioteka klienta dostawcy zasobów usługi Storage dla platformy .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/management)
* [Interfejs API REST zarządzania usługą Storage (klasyczny)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-api-and-library-references"></a>Dokumentacja bibliotek i interfejsów API ruchu danych usługi Azure Storage
* [Interfejs API REST usługi Import/Eksport usługi Storage](https://docs.microsoft.com/rest/api/storageimportexport/)
* [Biblioteka klienta przenoszenia danych usługi Storage dla platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.datamovement)

### <a name="tools-and-utilities"></a>Narzędzia i programy narzędziowe
* [Polecenia cmdlet programu Azure PowerShell dla usługi Storage](https://docs.microsoft.com/powershell/module/azure.storage)
* [Polecenia cmdlet interfejsu wiersza polecenia platformy Azure dla usługi Storage](https://docs.microsoft.com/cli/azure/storage)
* [Narzędzie wiersza polecenia AzCopy](http://aka.ms/downloadazcopy)
* [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) jest bezpłatną, autonomiczną aplikacją oferowaną przez firmę Microsoft, która umożliwia wizualną pracę z danymi w usłudze Azure Storage w systemach Windows, macOS i Linux.
* [Azure Storage Client Tools](../storage-explorers.md)
* [Narzędzia deweloperskie platformy Azure](https://azure.microsoft.com/tools/)

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z usługą Azure Storage, zobacz [Tworzenie konta magazynu](storage-quickstart-create-account.md).

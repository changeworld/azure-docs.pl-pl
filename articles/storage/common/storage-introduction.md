---
title: Wprowadzenie do usługi Azure Storage | Microsoft Docs
description: Wprowadzenie do usługi Azure Storage — magazynu danych w chmurze firmy Microsoft.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: get-started-article
ms.date: 03/06/2018
ms.author: tamram
ms.openlocfilehash: 18a8065bba8a4a0ec2025d6b9134fe9fab21eb5f
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="introduction-to-microsoft-azure-storage"></a>Wprowadzenie do usługi Microsoft Azure Storage

Microsoft Azure Storage to zarządzana przez firmę Microsoft usługa w chmurze zapewniająca bezpieczny, trwały, skalowalny i nadmiarowy magazyn o wysokiej dostępności. Firma Microsoft zajmuje się konserwacją oraz rozwiązywaniem krytycznych problemów.

Usługa Azure Storage składa się z trzech usług danych: Blob Storage, File Storage i Queue Storage. Usługa Blob Storage obsługuje magazyn zarówno w warstwie Standardowa, jak i Premium, przy czym w warstwie Premium używane są tylko dyski SSD zapewniające największą wydajność. Kolejną funkcją jest magazyn chłodny, który umożliwia przechowywanie dużych ilości rzadko używanych danych w niższej cenie.

W tym artykule omówiono następujące zagadnienia:
* Usługi Azure Storage
* Typy kont magazynu
* Uzyskiwanie dostępu do obiektów blob, kolejek i plików
* Szyfrowanie
* Replikacja
* Transferowanie danych z lub do magazynu
* Liczne dostępne biblioteki klienta magazynu

Aby rozpocząć pracę z usługą Azure Storage, zobacz [Tworzenie konta magazynu](storage-quickstart-create-account.md).

## <a name="introducing-the-azure-storage-services"></a>Wprowadzenie do usług Azure Storage

Aby móc korzystać z usług udostępnianych przez usługę Azure Storage — Blob Storage, File Storage i Queue Storage — należy najpierw utworzyć konto magazynu, co umożliwi transferowanie danych do/z określonej usługi w ramach tego konta magazynu.

## <a name="blob-storage"></a>Blob Storage

Obiekty blob to pliki podobne do tych, które przechowujesz na komputerze (lub tablecie, urządzeniu przenośnym itp.). Mogą to być obrazy, pliki programu Microsoft Excel, pliki HTML, wirtualne dyski twarde (VHD), dane big data, kopie zapasowe baz danych — w zasadzie dowolny rodzaj danych. Obiekty blob są przechowywane w kontenerach, które są podobne do folderów.

Po umieszczeniu plików w usłudze Blob Storage możesz uzyskać do nich dostęp z dowolnego miejsca na świecie przy użyciu adresów URL, interfejsu REST lub jednej z bibliotek klienta zestawu SDK usługi Azure Storage. Biblioteki klienta magazynu są dostępne dla wielu języków, w tym Node.js, Java, PHP, Ruby, Python i .NET.

Istnieją trzy typy obiektów blob: blokowe obiekty blob, stronicowe obiekty blob (używane w przypadku plików VHD) oraz uzupełnialne obiekty blob.

* Blokowe obiekty blob są używane do przechowywania zwykłych plików o rozmiarze do około 4,7 TB.
* Stronicowe obiekty blob są używane do przechowywania plików o dostępie losowym o rozmiarze do 8 TB. Są one stosowane w przypadku plików VHD wspierających maszyny wirtualne.
* Uzupełnialne obiekty blob składają się z bloków, podobnie jak blokowe obiekty blob, lecz są zoptymalizowane pod kątem operacji dołączania. Są one używane na przykład na potrzeby rejestrowania informacji w tym samym obiekcie blob z różnych maszyn wirtualnych.

W przypadku bardzo dużych zestawów danych, gdy ograniczenia sieci mogą w praktyce uniemożliwić przekazanie lub pobranie danych do usługi Blob Storage, możesz przesłać zestaw dysków twardych do firmy Microsoft, aby zaimportować dane bezpośrednio do centrum danych lub wyeksportować je stamtąd. Zobacz [Przesyłanie danych do usługi Blob Storage za pomocą usługi Microsoft Azure Import/Export](../storage-import-export-service.md).

## <a name="azure-files"></a>Azure Files
Usługa [Azure Files](../files/storage-files-introduction.md) umożliwia konfigurowanie wysoce dostępnych udziałów plików sieciowych, do których można uzyskiwać dostęp przy użyciu standardowego protokołu bloku komunikatów serwera (SMB, Server Message Block). Oznacza to, że wiele maszyn wirtualnych może współdzielić te same pliki z dostępem zarówno do odczytu, jak i do zapisu. Pliki można także odczytywać przy użyciu interfejsu REST lub bibliotek klienckich magazynu.

Jedną z różnic między usługą Azure Files i plikami w firmowym udziale plików jest możliwość dostępu do plików z dowolnego miejsca na świecie przy użyciu adresu URL, który wskazuje plik i zawiera token sygnatury dostępu współdzielonego. Tokeny sygnatury dostępu współdzielonego można generować. Umożliwiają one uzyskanie określonego dostępu do prywatnego zasobu przez określony czas.

Udziałów plików można używać w wielu typowych scenariuszach:

* Wiele aplikacji lokalnych korzysta z udziałów plików. Dzięki temu migracja tych aplikacji współdzielących dane na platformę Azure jest łatwiejsza. Jeśli zainstalujesz udział plików przy użyciu tej samej litery dysku, która jest używana przez aplikację lokalną, część aplikacji uzyskująca dostęp do udziału plików powinna działać bez konieczności wprowadzania zmian lub jedynie z minimalnymi zmianami.

* Pliki konfiguracji można przechowywać w udziale plików i uzyskiwać do nich dostęp z wielu maszyn wirtualnych. W udziale plików można przechowywać narzędzia i programy narzędziowe używane przez wielu deweloperów w grupie, dzięki czemu wszyscy będą mogli je znaleźć oraz będą używać tych samych wersji.

* Dzienniki diagnostyczne, metryki i zrzuty awaryjne to tylko trzy przykłady danych, które można zapisywać w udziale plików i przetwarzać lub analizować później.

Obecnie uwierzytelnianie oparte na usłudze Active Directory i listy kontroli dostępu (ACL, access control list) nie są obsługiwane, ale ich obsługa zostanie dodana w przyszłości. W celu uwierzytelniania dostępu do udziału plików są używane poświadczenia konta magazynu. Oznacza to, że każda osoba z zainstalowanym udziałem będzie miała do niego pełny dostęp do odczytu i zapisu.

## <a name="queue-storage"></a>Queue Storage

Usługa Azure Queue jest używana do przechowywania i pobierania komunikatów. Komunikaty kolejek mogą mieć rozmiar do 64 KB, a jedna kolejka może zawierać miliony komunikatów. Kolejki są zazwyczaj używane do przechowywania list komunikatów, które mają zostać przetworzone asynchronicznie.

Przykładowo załóżmy, że chcesz, aby klienci mogli przekazywać obrazy, i chcesz utworzyć miniatury dla każdego obrazu. Klient może poczekać, aż utworzysz miniatury podczas przekazywania zdjęcia. Alternatywą jest zastosowanie kolejki. Gdy klient zakończy przekazywanie, zapisz komunikat w kolejce. Następnie funkcja usługi Azure Functions pobierze ten komunikat z kolejki i utworzy miniatury. Każdą część tego przetwarzania można skalować oddzielnie, co daje większą kontrolę podczas dostosowywania.

## <a name="table-storage"></a>Magazyn tabel

Usługa Azure Table Storage jest teraz częścią usługi Azure Cosmos DB. Aby zapoznać się z dokumentacją usługi Azure Table Storage, przejdź do artykułu [Omówienie usługi Azure Table Storage](../../cosmos-db/table-storage-overview.md). Oprócz istniejącej usługi Azure Table Storage dostępna jest nowa oferta interfejsu API tabel usługi Azure Cosmos DB, który zapewnia tabele zoptymalizowane pod kątem przepływności, globalną dystrybucję i automatyczne indeksy pomocnicze. Aby dowiedzieć się więcej i wypróbować nowe środowisko wersji Premium, zobacz [Interfejs API tabel usługi Azure Cosmos DB](https://aka.ms/premiumtables).

## <a name="disk-storage"></a>Przechowywanie na dysku

Usługa Azure Storage obejmuje również wszystkie funkcje dysków zarządzanych i niezarządzanych, z których korzystają maszyny wirtualne. Aby uzyskać więcej informacji na temat tych funkcji, zobacz [dokumentację usług Compute](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).

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

## <a name="pricing"></a>Cennik

Aby uzyskać szczegółowe informacje o cenach usługi Azure Storage, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="storage-apis-libraries-and-tools"></a>Narzędzia, biblioteki oraz interfejsy API usługi Storage
Zasoby usługi Azure Storage są dostępne za pomocą dowolnego języka, który obsługuje żądania HTTP/HTTPS. Dodatkowo Magazyn Azure oferuje biblioteki programistyczne dla kilku popularnych języków. Te biblioteki upraszczają wiele aspektów pracy z usługą Azure Storage dzięki obsłudze szczegółów, takich jak wywołania synchroniczne i asynchroniczne, przetwarzanie wsadowe operacji, zarządzanie wyjątkami, automatyczne ponawianie, zachowania podczas działania itd. Biblioteki są obecnie dostępne dla następujących języków i platform, a kolejne są planowane:

### <a name="azure-storage-data-services"></a>Usługi danych usługi Azure Storage
* [Interfejs API REST usług Storage](/rest/api/storageservices/)
* [Biblioteka klienta usługi Storage dla platformy .NET](https://docs.microsoft.com/dotnet/api/?view=azurestorage-8.1.1)
* [Biblioteka klienta usługi Storage dla języka C++](https://github.com/Azure/azure-storage-cpp)
* [Biblioteka klienta usługi Storage dla języka Java w systemie Android](https://azure.microsoft.com/develop/java/)
* [Biblioteka klienta usługi Storage dla oprogramowania Node.js](http://dl.windowsazure.com/nodestoragedocs/index.html)
* [Biblioteka klienta usługi Storage dla języka PHP](https://azure.microsoft.com/develop/php/)
* [Biblioteka klienta usługi Storage dla języka Python](https://azure.microsoft.com/develop/python/)
* [Biblioteka klienta usługi Storage dla języka Ruby](https://azure.microsoft.com/develop/ruby/)
* [Polecenia cmdlet usługi Storage dla programu PowerShell](/powershell/module/azure.storage/?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)
* [Polecenia usługi Storage dla interfejsu wiersza polecenia 2.0](/cli/azure/storage)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o usłudze Blob Storage](../blobs/storage-blobs-introduction.md)
* [Dowiedz się więcej o usłudze File Storage](../storage-files-introduction.md)
* [Dowiedz się więcej o usłudze Queue Storage](../queues/storage-queues-introduction.md)

Aby rozpocząć pracę z usługą Azure Storage, zobacz [Tworzenie konta magazynu](storage-quickstart-create-account.md).

<!-- FIGURE OUT WHAT TO DO WITH ALL THESE LINKS.

Azure Storage resources can be accessed by any language that can make HTTP/HTTPS requests. Additionally, Azure Storage offers programming libraries for several popular languages. These libraries simplify many aspects of working with Azure Storage by handling details such as synchronous and asynchronous invocation, batching of operations, exception management, automatic retries, operational behavior and so forth. Libraries are currently available for the following languages and platforms, with others in the pipeline:

### Azure Storage data services
* [Storage Services REST API](https://docs.microsoft.com/rest/api/storageservices/)
* [Storage Client Library for .NET](https://docs.microsoft.com/dotnet/api/?view=azurestorage-8.1.1)
* [Storage Client Library for C++](https://github.com/Azure/azure-storage-cpp)
* [Storage Client Library for Java/Android](https://azure.microsoft.com/develop/java/)
* [Storage Client Library for Node.js](http://dl.windowsazure.com/nodestoragedocs/index.html)
* [Storage Client Library for PHP](https://azure.microsoft.com/develop/php/)
* [Storage Client Library for Python](https://azure.microsoft.com/develop/python/)
* [Storage Client Library for Ruby](https://azure.microsoft.com/develop/ruby/)
* [Storage Cmdlets for PowerShell](/powershell/module/azure.storage/?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)

### Azure Storage management services
* [Storage Resource Provider REST API Reference](/rest/api/storagerp/)
* [Storage Resource Provider Client Library for .NET](/dotnet/api/microsoft.azure.management.storage)
* [Storage Resource Provider Cmdlets for PowerShell 1.0](/powershell/module/azure.storage)
* [Storage Service Management REST API (Classic)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### Azure Storage data movement services
* [Storage Import/Export Service REST API](../storage-import-export-service.md)
* [Storage Data Movement Client Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)

### Tools and utilities
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) is a free, standalone app from Microsoft that enables you to work visually with Azure Storage data on Windows, macOS, and Linux.
* [Azure Storage Client Tools](../storage-explorers.md)
* [Azure SDKs and Tools](https://azure.microsoft.com/tools/)
* [Azure Storage Emulator](http://www.microsoft.com/download/details.aspx?id=43709)
* [Azure PowerShell](/powershell/azure/overview)
* [AzCopy Command-Line Utility](http://aka.ms/downloadazcopy)

## Next steps
To learn more about Azure Storage, explore these resources:

### Documentation
* [Azure Storage Documentation](https://azure.microsoft.com/documentation/services/storage/)
* [Create a storage account](../storage-create-storage-account.md)

-->

### <a name="for-administrators"></a>Dla administratorów
* [Używanie programu Azure PowerShell z usługą Azure Storage](storage-powershell-guide-full.md)
* [Używanie interfejsu wiersza polecenia platformy Azure z usługą Azure Storage](../storage-azure-cli.md)

### <a name="for-net-developers"></a>Dla deweloperów .NET
* [Rozpoczynanie pracy z usługą Azure Blob Storage przy użyciu platformy .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Tworzenie oprogramowania dla usługi Azure Files przy użyciu platformy .NET](../files/storage-dotnet-how-to-use-files.md)
* [Rozpoczynanie pracy z usługą Azure Table Storage przy użyciu platformy .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Rozpoczynanie pracy z usługą Azure Queue Storage przy użyciu platformy .NET](../storage-dotnet-how-to-use-queues.md)

### <a name="for-javaandroid-developers"></a>Dla deweloperów języka Java w systemie Android
* [Używanie usługi Blob Storage w języku Java](../blobs/storage-java-how-to-use-blob-storage.md)
* [Develop for Azure Files with Java](../files/storage-java-how-to-use-file-storage.md) (Tworzenie oprogramowania dla usługi Azure Files przy użyciu języka Java)
* [Używanie usługi Table Storage w języku Java](../../cosmos-db/table-storage-how-to-use-java.md)
* [Używanie usługi Queue Storage w języku Java](../storage-java-how-to-use-queue-storage.md)

### <a name="for-nodejs-developers"></a>Dla deweloperów oprogramowania Node.js
* [Używanie usługi Blob Storage w oprogramowaniu Node.js](../blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Używanie usługi Table Storage w oprogramowaniu Node.js](../../cosmos-db/table-storage-how-to-use-nodejs.md)
* [Używanie usługi Queue Storage w oprogramowaniu Node.js](../storage-nodejs-how-to-use-queues.md)

### <a name="for-php-developers"></a>Dla deweloperów języka PHP
* [Używanie usługi Blob Storage w języku PHP](../blobs/storage-php-how-to-use-blobs.md)
* [Używanie usługi Table Storage w języku PHP](../../cosmos-db/table-storage-how-to-use-php.md)
* [Używanie usługi Queue Storage w języku PHP](../storage-php-how-to-use-queues.md)

### <a name="for-ruby-developers"></a>Dla deweloperów języka Ruby
* [Używanie usługi Blob Storage w języku Ruby](../blobs/storage-ruby-how-to-use-blob-storage.md)
* [Używanie usługi Table Storage w języku Ruby](../../cosmos-db/table-storage-how-to-use-ruby.md)
* [Używanie usługi Queue Storage w języku Ruby](../storage-ruby-how-to-use-queue-storage.md)

### <a name="for-python-developers"></a>Dla deweloperów języka Python
* [Używanie usługi Blob Storage w języku Python](../blobs/storage-python-how-to-use-blob-storage.md)
* [Develop for Azure Files with Python](../files/storage-python-how-to-use-file-storage.md) (Tworzenie oprogramowania dla usługi Azure Files przy użyciu języka Python)
* [Używanie usługi Table Storage w języku Python](../../cosmos-db/table-storage-how-to-use-python.md)
* [Używanie usługi Queue Storage w języku Python](../storage-python-how-to-use-queue-storage.md)

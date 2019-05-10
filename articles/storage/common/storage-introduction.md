---
title: Wprowadzenie do usługi Azure Storage — magazynu w chmurze na platformie Azure | Microsoft Docs
description: Usługa Azure Storage to rozwiązanie do magazynowania w chmurze firmy Microsoft. Usługa Azure Storage oferuje magazyn dla obiektów danych cechujący się wysoką dostępnością, bezpieczeństwem, trwałością, wysoką skalowalnością i nadmiarowością.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 094c8d45f7f6099b169e25c89dbfc4a7d6522dfc
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510935"
---
# <a name="introduction-to-azure-storage"></a>Wprowadzenie do usługi Azure Storage

Usługa Azure Storage to rozwiązanie do magazynowania w chmurze firmy Microsoft dla nowoczesnych scenariuszy magazynowania danych. Usługa Azure Storage oferuje wysoce skalowalny magazyn obiektów dla obiektów danych, usługę systemu plików dla chmury, magazyn obsługi komunikatów dla niezawodnej obsługi komunikatów i magazyn danych NoSQL. Usługa Azure Storage to:

- **Niezawodność i wysoka dostępność.** Nadmiarowość zapewnia bezpieczeństwo danych w przypadku przejściowych awarii sprzętu. Dla dodatkowej ochrony przed lokalnymi katastrofami lub klęskami żywiołowymi można również zdecydować się na replikowanie danych między centrami danych lub regionami geograficznymi. Dane replikowane w ten sposób pozostają wysoce dostępne w przypadku awarii. 
- **Bezpieczeństwo.** Wszystkie dane zapisane w usłudze Azure Storage są przez nią szyfrowane. Usługa Azure Storage zapewnia precyzyjną kontrolę tego, kto ma dostęp do danych.
- **Skalowalność.** Usługa Azure Storage została zaprojektowana jako wysoce skalowalne rozwiązanie spełniające potrzeby związane z magazynowaniem danych i wydajnością współczesnych aplikacji. 
- **Zarządzanie.** Platforma Microsoft Azure obsługuje konserwację sprzętu, aktualizacje i krytyczne problemy za Ciebie.
- **Dostępność.** Dane w usłudze Azure Storage są dostępne z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. Firma Microsoft udostępnia zestawy SDK usługi Azure Storage w różnych językach — .NET, Java, Node.js, Python, PHP, Ruby, Go i innych — oraz dojrzały interfejs API REST. Usługa Azure Storage obsługuje skrypty programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Witryna Azure Portal oraz Eksplorator usługi Azure Storage oferują proste rozwiązania wizualne do pracy z danymi.  

## <a name="azure-storage-services"></a>Usługi Azure Storage

Usługa Azure Storage obejmuje następujące usługi danych: 

- [Azure Blobs](../blobs/storage-blobs-introduction.md): wysoce skalowalny magazyn obiektów dla danych tekstowych i binarnych.
- [Azure Files](../files/storage-files-introduction.md): zarządzane udziały plików dla wdrożeń lokalnych lub w chmurze.
- [Azure Queues](../queues/storage-queues-introduction.md): magazyn obsługi komunikatów zapewniający niezawodną obsługę komunikatów między składnikami aplikacji. 
- [Azure Tables](../tables/table-storage-overview.md): magazyn obiektów NoSQL do magazynowania ustrukturyzowanych danych bez użycia schematu.

Dostęp do każdej usługi uzyskuje się za pośrednictwem konta magazynu. Aby rozpocząć, zobacz artykuł [Tworzenie konta magazynu](storage-quickstart-create-account.md).

## <a name="blob-storage"></a>Magazyn obiektów blob

Azure Blob Storage to rozwiązanie do magazynowania obiektów w chmurze firmy Microsoft. Usługa Blob Storage jest zoptymalizowana pod kątem przechowywania olbrzymich ilości danych bez struktury, takich jak dane tekstowe lub binarne. 

Usługa Blob Storage to idealne rozwiązanie w następujących przypadkach:

* Obsługiwanie obrazów i dokumentów bezpośrednio w przeglądarce.
* Przechowywanie plików do dostępu rozproszonego.
* Przesyłanie strumieniowe audio i wideo.
* Zapisywanie danych w celu tworzenia kopii zapasowych, przywracania, odzyskiwania po awarii i archiwizowania.
* Przechowywanie danych w celu analizy w usłudze lokalnej lub hostowanej na platformie Azure.

Dostęp do obiektów w usłudze Blob Storage można uzyskać z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. Użytkownicy lub aplikacje klienckie mogą uzyskiwać dostęp do obiektów blob za pomocą adresów URL, [interfejsu API REST usługi Azure Storage](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [programu Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/storage) lub biblioteki klienta usługi Azure Storage. Biblioteki klienta magazynu są dostępne dla wielu języków, w tym [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage/client), [Node.js](https://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/), [PHP](https://azure.github.io/azure-storage-php/) i [Ruby](https://azure.github.io/azure-storage-ruby).

Aby uzyskać więcej informacji dotyczących usługi Blob Storage, zobacz [Introduction to Blob storage (Wprowadzenie do usługi Blob Storage)](../blobs/storage-blobs-introduction.md).

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

Usługa Azure Storage obejmuje również wszystkie funkcje dysków zarządzanych i niezarządzanych, z których korzystają maszyny wirtualne. Aby uzyskać więcej informacji na temat tych funkcji, zobacz [dokumentację usług Compute](https://docs.microsoft.com/azure/#pivot=products&panel=Compute).

## <a name="types-of-storage-accounts"></a>Typy kont magazynu

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

Aby uzyskać więcej informacji dotyczących typów kont magazynu, zobacz temat [Przegląd konta usługi Azure Storage](storage-account-overview.md). 

## <a name="securing-access-to-storage-accounts"></a>Zabezpieczanie dostępu do konta magazynu

Musi być autoryzowana każde żądanie do usługi Azure Storage. Usługa Azure Storage obsługuje następujące metody autoryzacji:

- **Integracja usługi Azure Active Directory (Azure AD) danych obiektów blob i kolejek.** Usługa Azure Storage obsługuje uwierzytelnianie i autoryzacja przy użyciu poświadczeń usługi Azure AD dla obiektów Blob i kolejek usługi za pomocą kontroli dostępu opartej na rolach (RBAC). Autoryzowanie żądań z usługą Azure AD jest zalecana dla wyższego poziomu zabezpieczeń i łatwość użycia. Aby uzyskać więcej informacji, zobacz [uwierzytelnienia dostępu do platformy Azure obiekty BLOB i kolejki, przy użyciu usługi Azure Active Directory](storage-auth-aad.md).
- **Autoryzacja usłudze Azure AD przy użyciu protokołu SMB dla usługi Azure Files (wersja zapoznawcza).** Usługa pliki systemu Azure obsługuje uwierzytelnianie oparte na tożsamości za pośrednictwem protokołu SMB (Server Message Block) za pośrednictwem usługi Azure Active Directory Domain Services. Przyłączone do domeny Windows maszyn wirtualnych (VM) mają dostęp do udziałów plików platformy Azure przy użyciu poświadczeń usługi Azure AD. Aby uzyskać więcej informacji, zobacz [autoryzacja — Omówienie programu Azure Active Directory za pośrednictwem protokołu SMB dla usługi Azure Files (wersja zapoznawcza)](../files/storage-files-active-directory-overview.md).
- **Uwierzytelnianie za pomocą klucza współużytkowanego.** Usługi Azure Storage Blob, kolejki i tabeli i usługi Azure Files obsługuje autoryzacji za pomocą klienta Key.A udostępnione za pomocą klucza wspólnego autoryzacji przekazuje nagłówek z każdym żądaniem, który jest podpisany przy użyciu klucza dostępu konta magazynu. Aby uzyskać więcej informacji, zobacz [autoryzacji za pomocą klucza wspólnego](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key).
- **Autoryzacja przy użyciu udostępnionych access signatures (SAS).** Sygnatury dostępu współdzielonego (SAS) jest ciąg zawierający token zabezpieczający, który można dołączyć do identyfikatora URI dla zasobu magazynu. Token zabezpieczający hermetyzuje ograniczeń, takich jak uprawnienia oraz zakres dostępu. Aby uzyskać więcej informacji, zobacz [przy użyciu dostępu współdzielonego Signatures (SAS)](storage-dotnet-shared-access-signature-part-1.md).
- **Anonimowy dostęp do kontenerów i obiektów blob.** Kontenera i jego obiektów blob może być dostępne publicznie. Po określeniu, kontenera lub obiektu blob jest publiczny, każda osoba może go anonimowo odczytać; nie jest wymagane uwierzytelnienie. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem anonimowym w trybie odczytu do kontenerów i obiektów blob](../blobs/storage-manage-access-to-resources.md)

## <a name="encryption"></a>Szyfrowanie

W przypadku usług Storage dostępne są dwa podstawowe rodzaje szyfrowania. Aby uzyskać więcej informacji na temat bezpieczeństwa i szyfrowania, zobacz [Azure Storage security guide (Przewodnik po zabezpieczeniach usługi Azure Storage)](storage-security-guide.md).

### <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku

Szyfrowanie usługi Azure Storage w spoczynku pomaga chronić dane zgodnie z wymaganiami organizacji w zakresie zabezpieczeń i zgodności. Przy użyciu tej funkcji usługa Azure Storage automatycznie szyfruje dane przed trwałym wprowadzeniem ich do magazynu i odszyfrowuje je przed pobraniem. Szyfrowanie, odszyfrowywanie i zarządzanie kluczami jest całkowicie przezroczyste dla użytkowników.


Usługa SSE automatycznie szyfruje dane we wszystkich warstwach wydajności (Standardowa i Premium), wszystkich modelach wdrażania (model usługi Azure Resource Manager i model klasyczny) oraz wszystkich usługach Azure Storage (Blob, Queue, Table i File). Usługa SSE nie wpływa na wydajność usługi Azure Storage.

Aby uzyskać więcej informacji o szyfrowaniu usługi Storage w spoczynku, zobacz [Szyfrowanie usługi Azure Storage dla danych magazynowanych](storage-service-encryption.md).

### <a name="client-side-encryption"></a>Szyfrowania po stronie klienta

Biblioteki klienta magazynu posiadają metody, które możesz wywołać, aby programowo szyfrować dane przed wysłaniem ich z klienta do platformy Azure. Przechowywane dane są zaszyfrowane, co oznacza, że w stanie spoczynku również są zaszyfrowane. Podczas odczytywania informacji są one odszyfrowywane po ich otrzymaniu.

Aby uzyskać więcej informacji o szyfrowaniu po stronie klienta, zobacz [Client-Side Encryption with .NET for Microsoft Azure Storage (Szyfrowanie po stronie klienta przy użyciu programu .NET dla usługi Microsoft Azure Storage)](storage-client-side-encryption.md).

## <a name="redundancy"></a>Nadmiarowość

Aby zapewnić trwałość danych, usługa Azure Storage przeprowadza replikację wielu kopii danych. Podczas konfigurowania konta magazynu należy wybrać opcję nadmiarowości. 

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Aby uzyskać więcej informacji na temat odzyskiwania po awarii, zobacz [Disaster recovery and storage account failover (preview) in Azure Storage](storage-disaster-recovery-guidance.md) (Odzyskiwanie po awarii i tryb failover konta magazynu w wersji zapoznawczej w usłudze Azure Storage).

## <a name="transferring-data-to-and-from-azure-storage"></a>Transferowanie danych do i z usługi Azure Storage

Jest dostępnych kilka opcji umożliwiających przenoszenie danych do usługi Azure Storage lub poza nią. Wybór opcji zależy od rozmiaru zestawu danych i przepustowości sieci. Aby uzyskać więcej informacji, zobacz [Wybieranie rozwiązania platformy Azure do transferu danych](storage-choose-data-transfer-solution.md).

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
* [Biblioteka klienta przenoszenia danych usługi Storage dla platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.datamovement)

### <a name="tools-and-utilities"></a>Narzędzia i programy narzędziowe
* [Polecenia cmdlet programu Azure PowerShell dla usługi Storage](https://docs.microsoft.com/powershell/module/az.storage)
* [Polecenia cmdlet interfejsu wiersza polecenia platformy Azure dla usługi Storage](https://docs.microsoft.com/cli/azure/storage)
* [Narzędzie wiersza polecenia AzCopy](https://aka.ms/downloadazcopy)
* [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) jest bezpłatną, autonomiczną aplikacją oferowaną przez firmę Microsoft, która umożliwia wizualną pracę z danymi w usłudze Azure Storage w systemach Windows, macOS i Linux.
* [Azure Storage Client Tools](../storage-explorers.md)
* [Narzędzia deweloperskie platformy Azure](https://azure.microsoft.com/tools/)

## <a name="next-steps"></a>Kolejne kroki

Aby rozpocząć pracę z usługą Azure Storage, zobacz [Tworzenie konta magazynu](storage-quickstart-create-account.md).

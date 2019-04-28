---
title: Omówienie usługi Azure BLOB typu Page | Dokumentacja firmy Microsoft
description: Omówienie usługi Azure BLOB typu Page i korzyści, w tym przypadki użycia za pomocą przykładowych skryptów.
services: storage
author: anasouma
ms.service: storage
ms.topic: article
ms.date: 01/03/2019
ms.author: wielriac
ms.subservice: blobs
ms.openlocfilehash: b03da04c97475dcb9ce15f2ed69d7ca333d6f431
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61428390"
---
# <a name="overview-of-azure-page-blobs"></a>Omówienie usługi Azure BLOB typu Page

Usługa Azure Storage udostępnia trzy typy magazynu obiektów blob: Obiekty BLOB typu Block, obiekty BLOB dołączania i stronicowe obiekty BLOB. Blokowe obiekty BLOB składają się z bloków i są idealne do przechowywania tekstu lub pliki binarne i efektywnie przekazywanie dużych plików. Dołącz obiekty BLOB również składają się z bloków, ale są one zoptymalizowane do operacji uzupełnialnych, dzięki czemu idealne rozwiązanie w scenariuszach logowania. Stronicowe obiekty BLOB składają 512-bajtowego stron do 8 TB w całkowity rozmiar i są przeznaczone dla operacji częstego odczytu/zapisu losowych. Stronicowe obiekty BLOB są podstawą dysków IaaS platformy Azure. Ten artykuł koncentruje się na wyjaśniające, funkcje i korzyści dotyczące stronicowych obiektów blob.

Stronicowe obiekty BLOB to zbiór stron 512-bajtowych, które zapewniają możliwość odczytu/zapisu dowolnego zakresów bajtów. W związku z tym stronicowe obiekty BLOB są idealnym rozwiązaniem do przechowywania struktur danych na podstawie indeksu i rozrzedzone, takich jak dyski systemu operacyjnego i danych dla maszyn wirtualnych i baz danych. Na przykład bazy danych SQL Azure używa stronicowych obiektów blob jako podstawowego magazynu trwałego dla baz danych. Ponadto stronicowe obiekty BLOB są również często używane do plików za pomocą opartej na zakresie aktualizacji.  

Najważniejsze funkcje platformy Azure stronicowe obiekty BLOB są jego interfejsu REST, trwałości powiązanego magazynu oraz możliwości bezproblemowej migracji do platformy Azure. Te funkcje zostały omówione bardziej szczegółowo w następnej sekcji. Ponadto usługa Azure stronicowe obiekty BLOB są obecnie obsługiwane na dwa typy magazynów: Usługa Premium Storage i magazynu w warstwie standardowa. Usługa Premium Storage jest zaprojektowany specjalnie dla obciążeń wymagających spójnej wysokiej wydajności i małymi opóźnieniami, co stronicowe obiekty BLOB w warstwie premium jest idealny dla bazy danych magazynu danych o wysokiej wydajności.  Magazynu w warstwie standardowa jest bardziej ekonomiczna w przypadku obciążeń niewrażliwego na opóźnienia.

## <a name="sample-use-cases"></a>Przykładowe przypadki użycia

Omówmy kilka przypadków użycia w przypadku stronicowych obiektów blob, począwszy od dysków IaaS platformy Azure. Usługa Azure BLOB typu Page stanowią szkielet platformy dysków wirtualnych IaaS platformy Azure. Zarówno systemu operacyjnego platformy Azure, jak i dyski danych są zaimplementowane jako dyski wirtualne, których dane są trwale utrwalony na platformie Azure Storage i następnie dostarczane do maszyn wirtualnych, aby osiągnąć najwyższą wydajność. Dyski platformy Azure są utrwalane w funkcji Hyper-V [formatu wirtualnego dysku twardego](https://technet.microsoft.com/library/dd979539.aspx) i przechowywane jako [stronicowych obiektów blob](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs) w usłudze Azure Storage. Oprócz używania wirtualnych dysków maszyn wirtualnych IaaS platformy Azure, stronicowych obiektów blob umożliwia także PaaS i DBaaS scenariuszy, takich jak usługa Azure SQL DB, która aktualnie używa stronicowych obiektów blob do przechowywania danych SQL, umożliwiając szybkie losowe operacje odczytu i zapisu dla bazy danych. Innym przykładem może być w przypadku usługi PaaS dla dostępu do nośnika udostępnionego dla aplikacji współpracy, wideo i edycji stronicowe obiekty BLOB umożliwić szybki dostęp do losowych lokalizacjach na nośniku. Umożliwia również szybkie i wydajne edytowanie i scalanie z tego samego nośnika przez wielu użytkowników. 

Pierwszy usług firmy Microsoft innych firm, takich jak usługi Azure Site Recovery, usługi Azure Backup, a także wielu innych deweloperów wdrożono wiodące w branży innowacje za pomocą interfejsu REST stronicowych obiektów blob. Poniżej przedstawiono niektóre unikatowych scenariuszy implementowane na platformie Azure: 
* Zarządzanie migawek przyrostowych skierowane do aplikacji: Aplikacje mogą korzystać z migawek obiektów blob strony i interfejsów API REST do zapisywania punkty kontrolne aplikacji bez konieczności kosztownych zduplikowanie danych. Usługa Azure Storage obsługuje lokalne migawki dla stronicowych obiektów blob, które nie wymagają kopiowania całego obiektu blob. Te migawki publicznych interfejsów API również włączyć dostęp i kopiowanie różnic między migawkami.
* Migracja na żywo aplikacji i danych z lokalnych do chmury: Kopiowanie w danych lokalnych i zapisują bezpośrednio do platformy Azure stronicowych obiektów blob podczas lokalnych, w których maszyna wirtualna będzie kontynuowane do czasu za pomocą interfejsów API REST. Gdy element docelowy ma zawiera, możesz szybko przejściu w tryb failover maszyny Wirtualnej platformy Azure przy użyciu tych danych. W ten sposób można migrować maszyny wirtualne i wirtualne dyski z lokalnych do chmury przy minimalnych przestojach, ponieważ migracja danych przebiega w tle, gdy będziesz nadal używać maszyny Wirtualnej i przestojów potrzebne w trybie failover będzie krótki (w minutach).
* [Na podstawie sygnatury dostępu Współdzielonego](../common/storage-dotnet-shared-access-signature-part-1.md) udostępnione dostęp, co umożliwia obsługę scenariuszy takich jak czytniki wielu i jednego składnika zapisywania z obsługą mechanizmu kontroli współbieżności.

## <a name="page-blob-features"></a>Cechy stronicowego obiektu blob

### <a name="rest-api"></a>Interfejs API REST
Zobacz poniższy dokument, aby rozpocząć pracę z [opracowywania, za pomocą stronicowe obiekty BLOB](storage-dotnet-how-to-use-blobs.md). Na przykład Przyjrzyj się jak dostęp do stronicowych obiektów blob za pomocą biblioteki klienta usługi Storage dla platformy .NET. 

Na poniższym diagramie przedstawiono ogólny relacje między konto, kontenery i stronicowe obiekty BLOB.

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure1.png)

#### <a name="creating-an-empty-page-blob-of-a-specified-size"></a>Tworzenie pustego stronicowych obiektów blob o określonym rozmiarze
Aby utworzyć obiekt blob typu page, należy najpierw utworzyć **CloudBlobClient** obiektu z podstawowego identyfikatora URI do uzyskiwania dostępu do magazynu obiektów blob dla konta usługi storage (*pbaccount* na rys. 1) wraz z  **StorageCredentialsAccountAndKey** obiektu, jak pokazano w poniższym przykładzie. W przykładzie pokazano następnie utworzenie odwołania do **CloudBlobContainer** obiektu, a następnie tworzenia kontenera (*testvhds*), jeśli jeszcze nie istnieje. Następnie przy użyciu **CloudBlobContainer** obiektów, Utwórz odwołanie do **CloudPageBlob** obiektu, określając nazwę obiektu blob strony (os4.vhd) na dostęp. Aby utworzyć obiekt blob typu page, wywołaj [CloudPageBlob.Create](/dotnet/api/microsoft.windowsazure.storage.blob.cloudpageblob.create?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudPageBlob_Create_System_Int64_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) przekazując maksymalny rozmiar obiektu blob do utworzenia. *BlobSize* musi być wielokrotnością 512 bajtów.

```csharp
using Microsoft.WindowsAzure.StorageClient;
long OneGigabyteAsBytes = 1024 * 1024 * 1024;
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("testvhds");

// Create the container if it doesn't already exist.
container.CreateIfNotExists();

CloudPageBlob pageBlob = container.GetPageBlobReference("os4.vhd");
pageBlob.Create(16 * OneGigabyteAsBytes);
```

#### <a name="resizing-a-page-blob"></a>Zmiana rozmiaru stronicowych obiektów blob
Aby zmienić rozmiar stronicowych obiektów blob, po utworzeniu, należy użyć [rozmiar](/dotnet/api/microsoft.windowsazure.storage.blob.cloudpageblob.resize?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudPageBlob_Resize_System_Int64_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) interfejsu API. Żądany rozmiar powinien być wielokrotnością liczby 512 bajtów.
```csharp
pageBlob.Resize(32 * OneGigabyteAsBytes); 
```

#### <a name="writing-pages-to-a-page-blob"></a>Tworzenie stron do stronicowych obiektów blob
Aby napisać stron, użyj [CloudPageBlob.WritePages](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudpageblob.beginwritepages?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudPageBlob_BeginWritePages_System_IO_Stream_System_Int64_System_String_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_System_AsyncCallback_System_Object_) metody.  Dzięki temu zapisu sekwencyjnego zestaw stron maksymalnie 4MBs. Przesunięcie zapisywana muszą zaczynać się na granicy 512-bajtowego (startingOffset % 512 == 0) i na końcu na granicy 512 - 1.  Poniższy przykład kodu pokazuje sposób wywoływania **WritePages** dla obiektu blob:

```csharp
pageBlob.WritePages(dataStream, startingOffset); 
```

Jak najszybciej żądania zapisu sekwencyjnego zestaw stron powiedzie się w usłudze obiektów blob i jest replikowana w celu zapewnienia trwałości i odporności, zapisu zobowiązał i Powodzenie jest zwracana do klienta.  

Poniżej przedstawiono diagram oddzielnych 2 operacje zapisu:

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure2.png)

1.  Operacji zapisu, zaczynając od przesunięcia 0 o długości 1024 bajty 
2.  Operacja zapisu, zaczynając od przesunięcia 4096 o długości 1024 

#### <a name="reading-pages-from-a-page-blob"></a>Odczytywanie stron z stronicowych obiektów blob
Aby odczytać strony, należy użyć [CloudPageBlob.DownloadRangeToByteArray](/dotnet/api/microsoft.windowsazure.storage.blob.icloudblob.downloadrangetobytearray?view=azure-dotnet) metodę w celu odczytania zakresu bajtów ze stronicowych obiektów blob. Dzięki temu można pobrać pełnej obiektów blob lub zakresu bajtów, zaczynając od dowolnego przesunięcie w obiekcie blob. Podczas odczytu, przesunięcia muszą być uruchamiane w wielu wynoszącej 512. Podczas odczytywania bajtów ze strony NUL, usługa zwraca zero bajtów.
```csharp
byte[] buffer = new byte[rangeSize];
pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, pageBlobOffset, rangeSize); 
```
Na poniższej ilustracji pokazano operację odczytu z BlobOffSet 256 i rangeSize 4352. Dane zwracane jest wyróżniany kolorem pomarańczowym. Zera są zwracane w przypadku stron NUL

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure3.png)

Jeśli słabo wypełnionych obiektów blob, można po prostu pobrać regionów prawidłową stronę, aby uniknąć płacenia za egressing zero bajtów i zmniejszyć opóźnienia pobierania.  Aby określić, które strony są wspierane przez dane, użyj [CloudPageBlob.GetPageRanges](/dotnet/api/microsoft.windowsazure.storage.blob.cloudpageblob.getpageranges?view=azure-dotnet). Można wyliczyć zwrócone zakresów i pobrać dane w każdym zakresie. 
```csharp
IEnumerable<PageRange> pageRanges = pageBlob.GetPageRanges();

foreach (PageRange range in pageRanges)
{
    // Calculate the rangeSize
    int rangeSize = (int)(range.EndOffset + 1 - range.StartOffset);

    byte[] buffer = new byte[rangeSize];

    // Read from the correct starting offset in the page blob and place the data in the bufferOffset of the buffer byte array
    pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, range.StartOffset, rangeSize); 

    // TODO: use the buffer for the page range just read
} 
```

#### <a name="leasing-a-page-blob"></a>Dzierżawa stronicowych obiektów blob
Operacja dzierżawienie obiektu Blob ustanawia i zarządza blokadę obiektu blob dla zapisu operacji i usuwania. Ta operacja jest przydatne w scenariuszach, gdzie stronicowych obiektów blob jest uzyskiwany z wielu klientów, aby upewnić się, że tylko jeden klient może zapisać obiektu blob w danym momencie. Dyskami platformy Azure, na przykład wykorzystuje to mechanizm, aby upewnić się, że dysk zarządza tylko jednej maszyny Wirtualnej dzierżawy. Czas trwania blokady może być 15 do 60 sekund lub mogą być nieskończone. Zobacz dokumentację [tutaj](/rest/api/storageservices/lease-blob) Aby uzyskać więcej informacji.

Oprócz zaawansowanych interfejsów API REST stronicowe obiekty BLOB oferują także dostępu współdzielonego, trwałość i lepsze zabezpieczenia. Omówimy te korzyści bardziej szczegółowo w następnych akapitach. 

### <a name="concurrent-access"></a>Równoczesny dostęp
Stronicowe obiekty BLOB interfejsu API REST i jego mechanizm dzierżawienia umożliwia aplikacjom dostęp do stronicowych obiektów blob z wielu klientów. Załóżmy na przykład, że potrzebne do tworzenia usług rozproszonych w chmurze, które współużytkują magazyn obiektów z wieloma użytkownikami. Może to być aplikacja sieci web obsługująca duży zbiór obrazy na kilku użytkowników. Jedną z opcji wykonywania, to jest Maszynę wirtualną za pomocą dołączonych dysków. Wad to między innymi ograniczenie, że dysk może zostać dołączona tyko z jedną maszyną wirtualną, dlatego ograniczenie skalowalności, elastyczności i zwiększenie ryzyka (i). Jeśli występuje problem z maszyny Wirtualnej lub usługi uruchomione na maszynie Wirtualnej, następnie ze względu na dzierżawę, obraz, który jest niedostępny do momentu wygaśnięcia dzierżawy, lub jest uszkodzona; i (ii) dodatkowy koszt związany z o Maszynie wirtualnej IaaS. 

Jest dostępna Alternatywna opcja używa stronicowych obiektów blob bezpośrednio za pośrednictwem interfejsów API REST usługi Azure Storage. Ta opcja eliminuje potrzebę stosowania kosztownych maszyn wirtualnych IaaS, oferuje pełną elastyczność bezpośredni dostęp z wielu klientów, upraszcza klasycznego modelu wdrażania, eliminując konieczność dołączania/odłączania dysków i eliminuje to ryzyko problemów na maszynie Wirtualnej. I zapewnia takiego samego poziomu wydajności dla operacji odczytu/zapisu w losowych jako dysk

### <a name="durability-and-high-availability"></a>Trwałości i wysokiej dostępności
Magazyn w warstwach standardowa i premium są trwałego magazynu, gdzie dane obiektów blob strony są zawsze replikowane w celu zapewnienia trwałości i wysokiej dostępności. Aby uzyskać więcej informacji na temat nadmiarowości magazynu platformy Azure, zobacz ten [dokumentacji](../common/storage-redundancy.md). Azure ciągle dostarczał niezawodność klasy przedsiębiorstwa dla dysków IaaS i stronicowe obiekty BLOB, dzięki wiodącej w branży, ZEROWEGO % [wycena współczynnik błędów](https://en.wikipedia.org/wiki/Annualized_failure_rate). Oznacza to Azure nigdy nie utraciła danych obiektów blob strony klienta. 

### <a name="seamless-migration-to-azure"></a>Bezproblemową migrację na platformę azure
Dla klientów i deweloperów, którzy są zainteresowani Implementowanie własne dostosowane rozwiązania tworzenia kopii zapasowych platforma Azure oferuje również migawek przyrostowych, w których przechowywane są tylko różnice. Ta funkcja umożliwia uniknięcie kosztów początkowej pełnej kopii, co znacznie zmniejsza koszt kopii zapasowej. Wraz z możliwością efektywnie odczytu i skopiuj dane różnicowe to inną zaawansowaną możliwością, umożliwiająca innowacje zwiększające od deweloperów, co prowadzi do najlepszych w klasie kopia zapasowa i odzyskiwanie po awarii (DR) korzystanie z funkcji odzyskiwania na platformie Azure. Można skonfigurować własną kopię zapasową lub rozwiązania do odzyskiwania po awarii dla maszyn wirtualnych na platformie Azure przy użyciu [migawki obiektu Blob](/rest/api/storageservices/snapshot-blob) wraz z [pobieranie zakresów stron](/rest/api/storageservices/get-page-ranges) interfejsu API i [obiektu Blob kopiowania przyrostowego](/rest/api/storageservices/incremental-copy-blob) interfejsu API, który możesz na użytek łatwo kopiowanie danych pierwotnych do odzyskiwania po awarii. 

Ponadto w wielu przedsiębiorstwach istnieją już uruchomione w lokalnych centrach danych obciążeń o znaczeniu krytycznym. Migrowanie obciążeń do chmury, jednym z głównych problemów będzie czas potrzebny do kopiowania danych i ryzyka nieprzewidzianych problemów po przełączenie przestoju. W wielu przypadkach Przestój może być showstopper pod kątem migracji do chmury. Za pomocą strony obiekty BLOB na interfejs API REST, Azure rozwiązuje ten problem, włączając migracji do chmury przy minimalnym zakłóceniu obciążeń o znaczeniu krytycznym. 

Przykłady w sposób zrobić migawkę i jak przywrócić stronicowy obiekt blob z migawki, można znaleźć na stronie [konfiguracji procesu tworzenia kopii zapasowej przy użyciu migawek przyrostowych](../../virtual-machines/windows/incremental-snapshots.md) artykułu.

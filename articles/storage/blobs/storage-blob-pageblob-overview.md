---
title: Omówienie obiektów blob stron platformy Azure | Dokumenty firmy Microsoft
description: Omówienie obiektów blob strony platformy Azure i ich zalety, w tym przypadków użycia z przykładowych skryptów.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 05/13/2019
ms.author: tamram
ms.reviewer: wielriac
ms.subservice: blobs
ms.openlocfilehash: 060e1d01e5f078bad9852ae35d0af9142192a7b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68985617"
---
# <a name="overview-of-azure-page-blobs"></a>Omówienie obiektów blob stron platformy Azure

Usługa Azure Storage oferuje trzy typy magazynu obiektów blob: blokowe obiekty blob, dołączanie obiektów blob i stronicowych obiektów blob. Blokowe obiekty blob składają się z bloków i są idealne do przechowywania plików tekstowych lub binarnych oraz do efektywnego przesyłania dużych plików. Dołączanie obiektów blob są również składa się z bloków, ale są one zoptymalizowane pod kątem operacji dołączania, dzięki czemu są one idealne dla scenariuszy rejestrowania. Obiekty BLOB strony są wykonane z 512-bajtowych stron o całkowitym rozmiarze do 8 TB i są przeznaczone do częstych operacji losowego odczytu/zapisu. Obiekty BLOB strony są podstawą dysków usługi Azure IaaS. W tym artykule koncentruje się na wyjaśnienie funkcji i zalet stronicowych obiektów blob.

Obiekty BLOB strony są zbiorem stron 512 bajtów, które zapewniają możliwość odczytu/zapisu dowolnych zakresów bajtów. W związku z tym obiekty blob strony są idealne do przechowywania struktur danych opartych na indeksach i rozrzedzonych, takich jak system operacyjny i dyski danych dla maszyn wirtualnych i baz danych. Na przykład usługa Azure SQL DB używa obiektów blob stron jako podstawowy magazyn trwały dla swoich baz danych. Ponadto obiekty blob stron są również często używane dla plików z aktualizacjami opartymi na zakresie.  

Najważniejsze funkcje obiektów blob strony platformy Azure są jego interfejs REST, trwałość podstawowej pamięci masowej i bezproblemowe możliwości migracji do platformy Azure. Te funkcje zostały omówione bardziej szczegółowo w następnej sekcji. Ponadto obiekty blob strony platformy Azure są obecnie obsługiwane w dwóch typach magazynu: magazynu w wersji Premium i magazynu standardowego. Magazyn w wersji Premium został zaprojektowany specjalnie dla obciążeń wymagających spójnej wysokiej wydajności i małych opóźnień, dzięki czemu obiekty blob strony premium są idealne dla scenariuszy magazynu o wysokiej wydajności. Standardowe konta magazynu są bardziej opłacalne w przypadku uruchamiania obciążeń niewrażliwych na opóźnienia.

## <a name="sample-use-cases"></a>Przykładowe przypadki użycia

Omówimy kilka przypadków użycia dla obiektów blob strony, począwszy od dysków usługi Azure IaaS. Obiekty BLOB strony platformy Azure są podstawą platformy dysków wirtualnych dla usługi Azure IaaS. Zarówno usługi Azure OS, jak i dyski danych są implementowane jako dyski wirtualne, na których dane są trwale utrwalane na platformie Azure Storage, a następnie dostarczane do maszyn wirtualnych w celu uzyskania maksymalnej wydajności. Dyski platformy Azure są utrwalone w [formacie Hyper-V VHD](https://technet.microsoft.com/library/dd979539.aspx) i przechowywane jako [obiekt blob strony](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs) w usłudze Azure Storage. Oprócz używania dysków wirtualnych dla maszyn wirtualnych usługi Azure IaaS obiekty blob strony umożliwiają również scenariusze PaaS i DBaaS, takie jak usługa Usługi Azure SQL DB, która obecnie używa obiektów blob stron do przechowywania danych SQL, umożliwiając szybkie losowe operacje odczytu i zapisu dla bazy danych. Innym przykładem może być, jeśli masz usługę PaaS dla udostępnionego dostępu do multimediów dla aplikacji do edycji wideo współpracy, obiekty blob strony umożliwiają szybki dostęp do losowych lokalizacji w mediach. Umożliwia również szybką i wydajną edycję i scalanie tego samego nośnika przez wielu użytkowników. 

Usługi firmy Microsoft, takie jak Azure Site Recovery, Azure Backup, a także wielu deweloperów innych firm zaimplementowały wiodące w branży innowacje przy użyciu interfejsu REST obiektu blob strony. Oto niektóre z unikatowych scenariuszy zaimplementowanych na platformie Azure: 

* Kierowane przez aplikację przyrostowe zarządzanie migawkami: aplikacje mogą wykorzystywać migawki obiektów blob strony i interfejsy API REST do zapisywania punktów kontrolnych aplikacji bez ponoszenia kosztownego powielania danych. Usługa Azure Storage obsługuje migawki lokalne dla obiektów blob strony, które nie wymagają kopiowania całego obiektu blob. Te interfejsy API migawki publicznej umożliwiają również uzyskiwanie dostępu do różnic i kopiowanie między migawkami.
* Migracja na żywo aplikacji i danych z lokalnego do chmury: Skopiuj dane lokalne i użyj interfejsów API REST do zapisu bezpośrednio do obiektu blob strony platformy Azure, podczas gdy lokalna maszyna wirtualna jest nadal uruchamiana. Gdy obiekt docelowy został przechwycony, można szybko przejść w stan failover do maszyny Wirtualnej platformy Azure przy użyciu tych danych. W ten sposób można migrować maszyny wirtualne i dyski wirtualne z lokalnego do chmury przy minimalnym przestoju, ponieważ migracja danych występuje w tle podczas dalszego korzystania z maszyny Wirtualnej, a czas przestoju potrzebny do pracy awaryjnej będzie krótki (w minutach).
* Współdzielony dostęp [oparty na sas,](../common/storage-sas-overview.md) który umożliwia scenariusze, takie jak wielu czytelników i pojedynczego modułu zapisującego z obsługą kontroli współbieżności.

## <a name="page-blob-features"></a>Cechy stronicowego obiektu blob

### <a name="rest-api"></a>Interfejs API REST

Zapoznaj się z następującym dokumentem, aby rozpocząć [tworzenie przy użyciu stronicowych obiektów blob](storage-dotnet-how-to-use-blobs.md). Na przykład przyjrzyj się, jak uzyskać dostęp do obiektów blob stron przy użyciu biblioteki klienta magazynu dla platformy .NET. 

Na poniższym diagramie opisano ogólne relacje między kontem, kontenerami i obiektami blob stron.

![Zrzut ekranu przedstawiający relacje między kontami, kontenerami i obiektami blob stron](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure1.png)

#### <a name="creating-an-empty-page-blob-of-a-specified-size"></a>Tworzenie pustego obiektu blob strony o określonym rozmiarze

Aby utworzyć obiekt blob strony, najpierw utworzyć **CloudBlobClient** obiektu, z podstawowym identyfikatorem URI dostępu do magazynu obiektów blob dla konta magazynu *(pbaccount* na rysunku 1) wraz z **StorageCredentialsAccountAndKey** obiektu, jak pokazano w poniższym przykładzie. W przykładzie pokazano tworzenie odwołania do **CloudBlobContainer** obiektu, a następnie tworzenie kontenera *(testvhds),* jeśli jeszcze nie istnieje. Następnie za pomocą **CloudBlobContainer** obiektu, należy utworzyć odwołanie do **CloudPageBlob** obiektu, określając nazwę obiektu blob strony (os4.vhd) do dostępu. Aby utworzyć obiekt blob strony, wywołać [CloudPageBlob.Create](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.create), przekazywanie w maksymalnym rozmiarze obiektu blob do utworzenia. Rozmiar *obiektów blobSize* musi być wielokrotnością 512 bajtów.

```csharp
using Microsoft.Azure;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;

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

#### <a name="resizing-a-page-blob"></a>Zmiana rozmiaru obiektu blob strony

Aby zmienić rozmiar obiektu blob strony po utworzeniu, należy użyć [resize](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.resize) metody. Żądany rozmiar powinien być wielokrotnością 512 bajtów.

```csharp
pageBlob.Resize(32 * OneGigabyteAsBytes);
```

#### <a name="writing-pages-to-a-page-blob"></a>Pisanie stron do obiektu blob strony

Aby napisać strony, należy użyć [CloudPageBlob.WritePages](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.beginwritepages) metody.  Pozwala to na pisanie sekwencyjnego zestawu stron do 4 MB. Przesunięcie zapisywane do musi rozpocząć się na granicy 512 bajtów (startingOffset % 512 == 0) i zakończyć na granicy 512 - 1.  Poniższy przykład kodu pokazuje, jak wywołać **WritePages** dla obiektu blob:

```csharp
pageBlob.WritePages(dataStream, startingOffset); 
```

Tak szybko, jak żądanie zapisu dla sekwencyjnego zestawu stron powiedzie się w usłudze obiektów blob i jest replikowany pod kątem trwałości i odporności, zapis został zatwierdzony, a powodzenie jest zwracany z powrotem do klienta.  

Poniższy diagram przedstawia 2 oddzielne operacje zapisu:

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure2.png)

1.  Operacja zapisu od odsunięcia 0 długości 1024 bajtów 
2.  Operacja zapisu od odsunięcia 4096 długości 1024 

#### <a name="reading-pages-from-a-page-blob"></a>Czytanie stron z obiektu blob strony

Aby odczytać strony, użyj [CloudPageBlob.DownloadRangeToByteArray](/dotnet/api/microsoft.azure.storage.blob.icloudblob.downloadrangetobytearray) metody odczytu zakresu bajtów z obiektu blob strony. Dzięki temu można pobrać pełny obiekt blob lub zakres bajtów, począwszy od dowolnego przesunięcia w obiekcie blob. Podczas odczytu przesunięcie nie musi rozpoczynać się na wielokrotności 512. Podczas odczytywania bajtów ze strony NUL usługa zwraca zero bajtów.

```csharp
byte[] buffer = new byte[rangeSize];
pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, pageBlobOffset, rangeSize); 
```

Na poniższej ilustracji przedstawiono operację odczytu z przesunięciem 256 i rozmiarem zakresu 4352. Zwrócone dane są wyróżnione na pomarańczowo. Zera są zwracane dla stron NUL.

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure3.png)

Jeśli masz słabo zaludniony obiekt blob, możesz po prostu pobrać prawidłowe regiony strony, aby uniknąć płacenia za wyjście z bajtów zerowych i zmniejszyć opóźnienie pobierania.  Aby określić, które strony są wspierane przez dane, użyj [cloudpageblob.GetPageRanges](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.getpageranges). Następnie można wyliczyć zwrócone zakresy i pobrać dane z każdego zakresu. 

```csharp
IEnumerable<PageRange> pageRanges = pageBlob.GetPageRanges();

foreach (PageRange range in pageRanges)
{
    // Calculate the range size
    int rangeSize = (int)(range.EndOffset + 1 - range.StartOffset);

    byte[] buffer = new byte[rangeSize];

    // Read from the correct starting offset in the page blob and
    // place the data in the bufferOffset of the buffer byte array
    pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, range.StartOffset, rangeSize); 

    // Then use the buffer for the page range just read
}
```

#### <a name="leasing-a-page-blob"></a>Dzierżawa stronicowego obiektu blob

Operacja dzierżawy obiektu blob ustanawia blokadę obiektu blob i zarządza nią. Ta operacja jest przydatna w scenariuszach, w których obiekt blob strony jest dostępny z wielu klientów, aby upewnić się, że tylko jeden klient może zapisywać do obiektu blob w czasie. Dyski platformy Azure, na przykład wykorzystuje ten mechanizm dzierżawy, aby upewnić się, że dysk jest zarządzany tylko przez jedną maszynę wirtualną. Czas trwania blokady może wynosić od 15 do 60 sekund lub może być nieskończony. Zobacz dokumentację [tutaj,](/rest/api/storageservices/lease-blob) aby uzyskać więcej informacji.

Oprócz rozbudowanych interfejsów API REST obiekty blob strony zapewniają również dostęp współużytkowany, trwałość i zwiększone zabezpieczenia. O tych świadczeniach omówimy bardziej szczegółowo w kolejnych akapitach. 

### <a name="concurrent-access"></a>Równoczesny dostęp

Stronicowe obiekty BLOB REST API i jego mechanizm dzierżawy umożliwia aplikacjom dostęp do strony obiektu blob z wielu klientów. Załóżmy na przykład, że należy utworzyć rozproszoną usługę w chmurze, która współumieści obiekty magazynu z wieloma użytkownikami. Może to być aplikacja internetowa obsługująca dużą kolekcję obrazów dla kilku użytkowników. Jedną z opcji implementacji jest użycie maszyny Wirtualnej z dołączonymi dyskami. Wady tego obejmują (i) ograniczenie, że dysk może być dołączony tylko do pojedynczej maszyny Wirtualnej, co ogranicza skalowalność, elastyczność i zwiększa ryzyko. Jeśli występuje problem z maszyną wirtualną lub usługą działającą na maszynie Wirtualnej, a następnie z powodu dzierżawy obraz jest niedostępny, dopóki dzierżawa nie wygaśnie lub zostanie przerwana; oraz (ii) Dodatkowy koszt posiadania maszyny wirtualnej IaaS. 

Alternatywną opcją jest użycie obiektów blob strony bezpośrednio za pośrednictwem interfejsów API rest usługi Azure Storage. Ta opcja eliminuje konieczność stosowania kosztownych maszyn wirtualnych IaaS, oferuje pełną elastyczność bezpośredniego dostępu od wielu klientów, upraszcza klasyczny model wdrażania, eliminując konieczność dołączania/odłączania dysków i eliminuje ryzyko problemów z maszyną wirtualną. Zapewnia również taki sam poziom wydajności dla losowych operacji odczytu/zapisu, jak dysk

### <a name="durability-and-high-availability"></a>Trwałość i wysoka dostępność

Magazyn standardowy i magazyn w wersji premium to magazyn trwałej pamięci masowej, w której dane obiektów blob strony są zawsze replikowane w celu zapewnienia trwałości i wysokiej dostępności. Aby uzyskać więcej informacji na temat nadmiarowości usługi Azure Storage, zobacz tę [dokumentację](../common/storage-redundancy.md). Platforma Azure konsekwentnie zapewnia trwałość klasy korporacyjnej dla dysków IaaS i obiektów blob stron, z wiodącą w branży zerową [procentą rocznego wskaźnika niepowodzeń.](https://en.wikipedia.org/wiki/Annualized_failure_rate)

### <a name="seamless-migration-to-azure"></a>Bezproblemowa migracja na platformę Azure

Dla klientów i deweloperów, którzy są zainteresowani zaimplementowanie własnego rozwiązania do tworzenia kopii zapasowych dostosowane, Platforma Azure oferuje również przyrostowe migawki, które posiadają tylko deltas. Ta funkcja pozwala uniknąć kosztu początkowej pełnej kopii, co znacznie obniża koszt tworzenia kopii zapasowej. Wraz z możliwością efektywnego odczytu i kopiowania danych różnicowych jest to kolejna zaawansowana funkcja, która umożliwia jeszcze więcej innowacji od deweloperów, co prowadzi do najlepszego w swojej klasie środowiska tworzenia kopii zapasowych i odzyskiwania po awarii (DR) na platformie Azure. Można skonfigurować własne rozwiązanie do tworzenia kopii zapasowych lub odzyskiwania po awarii dla maszyn wirtualnych na platformie Azure przy użyciu [migawki obiektów Blob](/rest/api/storageservices/snapshot-blob) wraz z interfejsem API [Get Page Ranges](/rest/api/storageservices/get-page-ranges) i interfejsem API [przyrostowego kopiowania obiektów blob,](/rest/api/storageservices/incremental-copy-blob) którego można użyć do łatwego kopiowania danych przyrostowych dla odzyskiwania po awarii. 

Ponadto wiele przedsiębiorstw ma krytyczne obciążenia już działające w lokalnych centrach danych. W przypadku migracji obciążenia do chmury jednym z głównych problemów będzie ilość przestojów potrzebnych do kopiowania danych i ryzyko nieprzewidzianych problemów po przejściu. W wielu przypadkach przestoje mogą być showstopper migracji do chmury. Korzystając ze interfejsu API REST obiektów stronicowych, platforma Azure rozwiązuje ten problem, włączając migrację do chmury przy minimalnych zakłóceniach w krytycznych obciążeniach. 

Przykłady dotyczące wykonywania migawki i przywracania obiektu blob strony z migawki, zapoznaj się z [konfiguracją procesu tworzenia kopii zapasowej przy użyciu przyrostowych migawek](../../virtual-machines/windows/incremental-snapshots.md) artykułu.

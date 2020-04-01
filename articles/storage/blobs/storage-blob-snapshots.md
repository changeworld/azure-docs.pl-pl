---
title: Tworzenie migawki obiektu blob i zarządzanie nim w witrynie .NET — Usługa Azure Storage
description: Dowiedz się, jak utworzyć migawkę tylko do odczytu obiektu blob do tworzenia kopii zapasowych danych obiektów blob w danym momencie w czasie.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/06/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 9bf5eea55002814f461d375b3db43a37fe4f7aa9
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474086"
---
# <a name="create-and-manage-a-blob-snapshot-in-net"></a>Tworzenie migawki obiektu blob i zarządzanie nim w pliku .NET

Migawka jest tylko do odczytu wersja obiektu blob, który jest wykonany w momencie w czasie. Migawki są przydatne do tworzenia kopii zapasowych obiektów blob. W tym artykule pokazano, jak tworzyć migawki obiektów blob i zarządzać nimi przy użyciu [biblioteki klienta usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="about-blob-snapshots"></a>Migawki obiektów blob – informacje

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

Migawka obiektu blob jest identyczna z jego podstawowy obiekt blob, z tą różnicą, że identyfikator URI obiektu blob ma **datetime** wartość dołączaną do identyfikatora URI obiektu blob, aby wskazać czas, w którym migawka została podjęta. Na przykład, jeśli identyfikator URI `http://storagesample.core.blob.windows.net/mydrives/myvhd`obiektu blob strony `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`jest , identyfikator URI migawki jest podobny do .

> [!NOTE]
> Wszystkie migawki współużytkują identyfikator URI podstawowego obiektu blob. Jedynym rozróżnieniem między podstawowym obiektem blob a migawką jest dołączona wartość **DateTime.**
>

Obiekt blob może mieć dowolną liczbę migawek. Migawki utrzymują się, dopóki nie zostaną jawnie usunięte, co oznacza, że migawka nie może przeżyć jego podstawowy obiekt blob. Można wyliczyć migawki skojarzone z podstawowym obiektem blob, aby śledzić bieżące migawki.

Podczas tworzenia migawki obiektu blob właściwości systemu obiektu blob są kopiowane do migawki o tych samych wartościach. Podstawowe metadane obiektu blob są również kopiowane do migawki, chyba że określisz oddzielne metadane dla migawki podczas jej tworzenia. Po utworzeniu migawki można ją odczytać, skopiować lub usunąć, ale nie można jej zmodyfikować.

Dzierżawy skojarzone z podstawowym obiektem blob nie wpływają na migawkę. Nie można uzyskać dzierżawy na migawkę.

Plik VHD służy do przechowywania bieżących informacji i stanu dysku maszyny Wirtualnej. Można odłączyć dysk z poziomu maszyny Wirtualnej lub zamknąć maszynę wirtualną, a następnie zrobić migawkę jej pliku VHD. Można użyć tego pliku migawki później, aby pobrać plik VHD w tym momencie i ponownie utworzyć maszynę wirtualną.

## <a name="create-a-snapshot"></a>Tworzenie migawki

Aby utworzyć migawkę bloku obiektu blob, należy użyć jednej z następujących metod:

- [UtwórztwoSnapshot](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshot)
- [TworzenieSnapshotAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshotasync)

W poniższym przykładzie kodu pokazano, jak utworzyć migawkę. W tym przykładzie określono dodatkowe metadane dla migawki podczas jej tworzenia.

```csharp
private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
{
    // Create a new block blob in the container.
    CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

    // Add blob metadata.
    baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

    try
    {
        // Upload the blob to create it, with its metadata.
        await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
        await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="delete-snapshots"></a>Usuwanie migawek

Aby usunąć obiekt blob, należy najpierw usunąć wszystkie migawki tego obiektu blob. Migawkę można usunąć pojedynczo lub określić, że wszystkie migawki zostaną usunięte po usunięciu źródłowego obiektu blob. Jeśli spróbujesz usunąć obiekt blob, który nadal ma migawki, powoduje błąd.

Aby usunąć migawki obiektów blob, należy użyć jednej z następujących metod usuwania obiektu blob i dołączyć [wyliczenie DeleteSnapshotsOption.](/dotnet/api/microsoft.azure.storage.blob.deletesnapshotsoption)

- [Usuwanie](/dotnet/api/microsoft.azure.storage.blob.cloudblob.delete)
- [UsuńSync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteasync)
- [UsuńIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexists)
- [UsuńIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexistsasync)

Poniższy przykład kodu pokazuje, jak usunąć obiekt blob i `blockBlob` jego migawki w .NET, gdzie jest obiektem typu [CloudBlockBlob:][dotnet_CloudBlockBlob]

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

## <a name="return-the-absolute-uri-to-a-snapshot"></a>Zwracanie bezwzględnego identyfikatora URI do migawki

Poniższy przykład kodu tworzy migawkę i zapisuje bezwzględny identyfikator URI dla lokalizacji podstawowej.

```csharp
//Create the blob service client object.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";

CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

//Get a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();

//Get a reference to a blob.
CloudBlockBlob blob = container.GetBlockBlobReference("sampleblob.txt");
blob.UploadText("This is a blob.");

//Create a snapshot of the blob and write out its primary URI.
CloudBlockBlob blobSnapshot = blob.CreateSnapshot();
Console.WriteLine(blobSnapshot.SnapshotQualifiedStorageUri.PrimaryUri);
```

## <a name="understand-how-snapshots-accrue-charges"></a>Dowiedz się, jak migawki naliczają opłaty

Tworzenie migawki, która jest tylko do odczytu kopię obiektu blob, może spowodować dodatkowe opłaty za przechowywanie danych do konta. Podczas projektowania aplikacji, należy pamiętać, jak te opłaty mogą być naliczane, dzięki czemu można zminimalizować koszty.

### <a name="important-billing-considerations"></a>Ważne zagadnienia dotyczące rozliczeń

Poniższa lista zawiera kluczowe punkty, które należy wziąć pod uwagę podczas tworzenia migawki.

- Konto magazynu wiąże się z opłatami za unikatowe bloki lub strony, niezależnie od tego, czy znajdują się one w obiekcie blob, czy w migawce. Twoje konto nie ponosi dodatkowych opłat za migawki skojarzone z obiektem blob, dopóki nie zaktualizujesz obiektu blob, na którym są oparte. Po zaktualizowaniu podstawowego obiektu blob, odbiega od jego migawek. W takim przypadku opłaty są naliczane za unikatowe bloki lub strony w każdym obiekcie blob lub migawki.
- Po zastąpieniu bloku w bloku blob, ten blok jest następnie ładowany jako unikatowy blok. Jest to prawdą, nawet jeśli blok ma ten sam identyfikator bloku i te same dane, które ma w migawce. Po zablokowaniu ponownie, odbiega od jego odpowiednika w dowolnej migawki i zostanie naliczona opłata za jego dane. To samo dotyczy strony w stronicowym obiekcie blob, który jest aktualizowany o identyczne dane.
- Zastąpienie bloku blob przez wywołanie [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream]lub [UploadFromByteArray][dotnet_UploadFromByteArray] metoda zastępuje wszystkie bloki w obiekcie blob. Jeśli masz migawkę skojarzoną z tym obiektem blob, wszystkie bloki w podstawowym obiekcie blob i migawka teraz się rozchodzą, a opłaty będą naliczane za wszystkie bloki w obu obiektach blob. Jest to prawdą, nawet jeśli dane w podstawowym obiekcie blob i migawki pozostają identyczne.
- Usługa obiektów Blob platformy Azure nie ma środków do określenia, czy dwa bloki zawierają identyczne dane. Każdy blok, który jest przekazyty i zatwierdzony jest traktowany jako unikatowy, nawet jeśli ma te same dane i ten sam identyfikator bloku. Ponieważ opłaty naliczane są za unikatowe bloki, należy wziąć pod uwagę, że aktualizowanie obiektu blob, który ma migawkę, powoduje dodatkowe unikatowe bloki i dodatkowe opłaty.

### <a name="minimize-cost-with-snapshot-management"></a>Minimalizowanie kosztów dzięki zarządzaniu migawkami

Zalecamy ostrożne zarządzanie migawkami, aby uniknąć dodatkowych opłat. Możesz wykonać następujące najlepsze rozwiązania, aby zminimalizować koszty poniesione przez przechowywanie migawek:

- Usuń i ponownie utworzyć migawki skojarzone z obiektem blob przy każdej aktualizacji obiektu blob, nawet jeśli są aktualizowane z identycznych danych, chyba że projekt aplikacji wymaga obsługi migawek. Usuwając i ponownie tworząc migawki obiektu blob, można zapewnić, że obiekt blob i migawki nie różnią się.
- Jeśli zachowujesz migawki obiektu blob, należy unikać wywoływania [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream]lub [UploadFromByteArray,][dotnet_UploadFromByteArray] aby zaktualizować obiekt blob. Metody te zastępują wszystkie bloki w obiekcie blob, powodując, że podstawowy obiekt blob i jego migawki znacznie się różnią. Zamiast tego należy zaktualizować najmniejszą możliwą liczbę bloków przy użyciu [Metod PutBlock][dotnet_PutBlock] i [PutBlockList.][dotnet_PutBlockList]

### <a name="snapshot-billing-scenarios"></a>Scenariusze rozliczeń migawkami

W poniższych scenariuszach pokazano, jak opłaty naliczane dla bloku obiektu blob i jego migawki.

#### <a name="scenario-1"></a>Scenariusz 1

W scenariuszu 1 podstawowy obiekt blob nie został zaktualizowany po zrobieniu migawki, więc opłaty są naliczane tylko dla unikatowych bloków 1, 2 i 3.

![Zasoby usługi Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>Scenariusz 2

W scenariuszu 2 podstawowy obiekt blob został zaktualizowany, ale migawka nie. Blok 3 został zaktualizowany i mimo że zawiera te same dane i ten sam identyfikator, nie jest taki sam jak blok 3 w migawce. W rezultacie konto jest naliczane za cztery bloki.

![Zasoby usługi Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>Scenariusz 3

W scenariuszu 3 podstawowy obiekt blob został zaktualizowany, ale migawka nie. Blok 3 został zastąpiony blokiem 4 w podstawowym obiekcie blob, ale migawka nadal odzwierciedla blok 3. W rezultacie konto jest naliczane za cztery bloki.

![Zasoby usługi Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>Scenariusz 4

W scenariuszu 4 podstawowy obiekt blob został całkowicie zaktualizowany i nie zawiera żadnego z jego oryginalnych bloków. W rezultacie konto jest naliczane za wszystkie osiem unikatowych bloków. Ten scenariusz może wystąpić, jeśli używasz metody aktualizacji, takich jak [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream]lub [UploadFromByteArray][dotnet_UploadFromByteArray], ponieważ te metody zastępują całą zawartość obiektu blob.

![Zasoby usługi Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Następne kroki

- Więcej informacji na temat pracy z migawkami dysków maszyny wirtualnej (VM) można znaleźć w obszarze [Tworzenie kopii zapasowej niezarządzanych dysków maszyn wirtualnych platformy Azure z migawkami przyrostowymi](../../virtual-machines/windows/incremental-snapshots.md)

- Aby uzyskać dodatkowe przykłady kodu przy użyciu magazynu obiektów Blob, zobacz [Przykłady kodu azure.](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob) Możesz pobrać przykładową aplikację i uruchomić ją lub przeglądać kod w usłudze GitHub.

[dotnet_AccessCondition]: https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.accesscondition
[dotnet_CloudBlockBlob]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob.cloudblockblob
[dotnet_CreateSnapshotAsync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.createsnapshotasync
[dotnet_HTTPStatusCode]: https://docs.microsoft.com/java/api/com.microsoft.store.partnercenter.network.httpstatuscode
[dotnet_PutBlockList]: /dotnet/api/microsoft.azure.storage.blob.cloudblockblob.putblocklist
[dotnet_PutBlock]: /dotnet/api/microsoft.azure.storage.blob.cloudblockblob.putblock
[dotnet_UploadFromByteArray]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob.cloudblob.uploadfrombytearray
[dotnet_UploadFromFile]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob.cloudblob.uploadfromfile
[dotnet_UploadFromStream]: /dotnet/api/microsoft.azure.storage.blob.cloudappendblob.uploadfromstream
[dotnet_UploadText]: /dotnet/api/microsoft.azure.storage.blob.cloudappendblob.uploadtext

---
title: Tworzenie migawki obiektu blob tylko do odczytu w usłudze Azure Storage | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można utworzyć migawki obiektu blob, aby utworzyć kopię zapasową danych obiektów blob w danym momencie w czasie. Dowiedz się, jak są rozliczane migawek i jak z nich korzystać, aby zminimalizować opłaty za pojemność.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/06/2018
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 9c24f613de8bf26331f6fe328358aaf8a320d522
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794232"
---
# <a name="create-a-blob-snapshot"></a>Tworzenie migawki obiektu blob

Migawki to wersja tylko do odczytu obiektu blob, która jest wykonywana w punkcie w czasie. Migawki są przydatne w przypadku tworzenia kopii zapasowej obiektów blob. Po utworzeniu migawki, odczytu, skopiuj lub usuń go, ale nie można go modyfikować.

Migawkę obiektu blob jest taka sama jak podstawowa obiektu blob, z tą różnicą, że ma identyfikator URI obiektu blob **daty/godziny** wartość dołączony do obiektu blob identyfikatora URI, aby wskazać czas, w którym migawka została utworzona. Na przykład, jeśli identyfikator URI obiektu blob strony jest `http://storagesample.core.blob.windows.net/mydrives/myvhd`, migawki, identyfikator URI jest podobne do `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`.

> [!NOTE]
> Wszystkie migawki udostępnić identyfikator URI podstawowego obiektu blob. Tylko rozróżnienie między podstawowy obiekt blob i migawki jest dołączany **daty/godziny** wartość.
>

Obiekt blob może zawierać dowolną liczbę migawek. Migawki są zachowywane do momentu jawnego są usuwane. Migawka nie może on nakreślał jego podstawowego obiektu blob. Można wyliczyć migawki skojarzone z podstawowego obiektu blob do śledzenia Twojej bieżącej migawki.

Podczas tworzenia migawki obiektu blob właściwości systemu obiektu blob są kopiowane do migawki, przy użyciu tych samych wartości. Metadane podstawowego obiektu blob jest też kopiowany do migawki, chyba że określisz oddzielne metadanych dla migawki podczas jego tworzenia.

Wszystkie dzierżawy skojarzony z podstawowego obiektu blob nie wpływają na migawki. Nie można uzyskać dzierżawy migawkę.

Plik wirtualnego dysku twardego jest używany do przechowywania informacji bieżący i stan dysku maszyny Wirtualnej. Można odłączyć dysk od na maszynie wirtualnej lub zamykania maszyny Wirtualnej, a następnie utworzenie migawki jego pliku wirtualnego dysku twardego. Ten plik z migawki można użyć później, pobrania pliku VHD w danym momencie i ponowne utworzenie maszyny Wirtualnej.

## <a name="create-a-snapshot"></a>Tworzenie migawki
Poniższy przykład kodu pokazuje sposób tworzenia migawki za pomocą [biblioteki klienta usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage/client). W tym przykładzie określa dodatkowe metadane dla migawki podczas jego tworzenia.

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
        // Specify metadata at the time that the snapshot is created to specify unique metadata for the snapshot.
        // If no metadata is specified when the snapshot is created, the base blob's metadata is copied to the snapshot.
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

## <a name="copy-snapshots"></a>Kopiowanie migawki
Operacje kopiowania obejmujących obiekty BLOB i migawek należy wykonać następujące czynności:

* Można skopiować migawki za pośrednictwem jego podstawowego obiektu blob. Przez wspieranie migawki do pozycji bazowej obiektu blob, można przywrócić wcześniejszą wersję obiektu blob. Pozostaje migawki, ale podstawowy obiekt blob zostanie zastąpiony zapisywalną kopię migawki.
* Można skopiować migawki do docelowego obiektu blob pod inną nazwą. Wynikowy docelowego obiektu blob jest zapisywalny obiekt blob i nie migawki.
* Po skopiowaniu źródłowego obiektu blob, wszystkie migawki źródłowego obiektu blob nie są kopiowane do lokalizacji docelowej. Gdy docelowego obiektu blob są zastępowane kopią, wszystkie migawki skojarzone z oryginalnego docelowego obiektu blob pozostają bez zmian.
* Po utworzeniu migawki blokowych obiektów blob, obiektu blob zatwierdzone zablokowanych również jest kopiowany do migawki. Niezatwierdzone bloków nie są kopiowane.

## <a name="specify-an-access-condition"></a>Określ warunki dostępu
Gdy wywołujesz [CreateSnapshotAsync][dotnet_CreateSnapshotAsync], można określić warunki dostępu, tak, aby migawki jest tworzony tylko wtedy, gdy warunek jest spełniony. Aby określić warunek dostępu, użyj [AccessCondition] [ dotnet_AccessCondition] parametru. Jeśli określony warunek nie jest spełniony, migawki nie jest tworzony i zwracany jest kod stanu usługi obiektów Blob [HTTPStatusCode][dotnet_HTTPStatusCode]. PreconditionFailed.

## <a name="delete-snapshots"></a>Usuwanie migawek
Nie można usunąć obiektu blob przy użyciu migawek, chyba że migawek również zostaną usunięte. Możesz usunąć migawkę indywidualnie lub określ, czy usunąć wszystkie migawki po usunięciu źródłowego obiektu blob. Jeśli użytkownik podejmie próbę usunięcia obiektów blob, który wciąż ma migawki, powoduje błąd.

Poniższy przykład kodu pokazuje, jak usunąć obiekt blob i jego migawek na platformie .NET, gdzie `blockBlob` jest obiektem typu [CloudBlockBlob][dotnet_CloudBlockBlob]:

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

## <a name="snapshots-with-azure-premium-storage"></a>Migawki za pomocą usługi Azure Premium Storage
Dzięki usłudze Premium Storage, przy użyciu migawek, następujące reguły:

* Maksymalna liczba migawek dla stronicowego obiektu blob na koncie usługi premium storage to 100. Jeśli ten limit zostanie przekroczony, operacja wykonanie migawki obiektu Blob zwraca kod błędu 409 (`SnapshotCountExceeded`).
* Można utworzyć migawkę stronicowych obiektów blob na koncie usługi premium storage raz na 10 minut. Jeśli tego kursu zostanie przekroczony, operacja wykonanie migawki obiektu Blob zwraca kod błędu 409 (`SnapshotOperationRateExceeded`).
* Aby przeczytać migawki, można użyć operacji kopiowania obiektu Blob do kopiowania migawki do innej strony obiektu blob na koncie. Docelowy obiekt blob w operacji kopiowania nie może mieć wszystkie istniejące migawki. Jeśli docelowy obiekt blob ma migawki, a następnie operacja obiektu Blob kopiowania zwraca kod błędu: 409 (`SnapshotsPresent`).

## <a name="return-the-absolute-uri-to-a-snapshot"></a>Zwraca bezwzględny identyfikator URI do migawki
Ten przykładowy kod języka C# tworzy migawkę i zapisuje się bezwzględny identyfikator URI dla lokalizacji głównej.

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

## <a name="understand-how-snapshots-accrue-charges"></a>Zrozumienie sposobu migawek naliczania opłat
Trwa tworzenie migawki, która jest tylko do odczytu kopię obiektu blob, może spowodować opłaty za magazyn dodatkowe dane do swojego konta. Podczas projektowania aplikacji, warto wiedzieć, jak te opłaty może być naliczane tak, aby zminimalizować koszty.

### <a name="important-billing-considerations"></a>Istotne zagadnienia dotyczące rozliczeń
Poniższa lista zawiera klucz wskazuje należy wziąć pod uwagę podczas tworzenia migawki.

* Konta magazynu spowoduje naliczenie opłaty za unikatowy bloków lub stron, czy znajdują się w obiekcie blob lub migawki. Twoje konto nie Naliczanie dodatkowych opłat za migawki związane z obiektu blob, do momentu zaktualizowania obiektu blob, na którym są one oparte. Po zaktualizowaniu podstawowego obiektu blob diverges go z jego migawek. W takim przypadku opłaty są naliczane za unikatowy bloków lub stron w poszczególnych obiektów blob lub migawki.
* Podczas zastępowania bloku, w ramach blokowych obiektów blob tego bloku później jest rozliczane jako unikatowy blok. Ta zasada obowiązuje, nawet wtedy, gdy bloku ma ten sam identyfikator bloku i tych samych danych, ponieważ ma migawki. Po bloku jest zaangażowana ponownie diverges go z jego odpowiednikiem w dowolną migawkę i opłata wyniesie dla swoich danych. To samo dotyczy strony w stronicowych obiektów blob, który jest aktualizowany mają identyczne dane.
* Zastępowanie blokowego obiektu blob przez wywołanie metody [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream] [ dotnet_UploadFromStream], lub [UploadFromByteArray] [ dotnet_UploadFromByteArray] metoda zastępuje wszystkie bloki w obiekcie blob. W przypadku migawki skojarzone z tego obiektu blob teraz rozdzielić wszystkie bloki w podstawowej obiektów blob i migawki, a opłata wyniesie dla wszystkich bloków w obu obiektów blob. Ta zasada obowiązuje, nawet w przypadku danych w podstawowej obiektów blob i migawki pozostać taka sama.
* Usługa Azure Blob service nie ma oznacza, że do ustalenia, czy dwa bloki zawierają identyczne dane. Każdy blok, który jest przekazany i zatwierdzone jest traktowane jako unikatowe, nawet wtedy, gdy ma on te same dane i ten sam identyfikator bloku. Ponieważ opłaty są naliczane dla bloków unikatowe, jest uważają, że aktualizowanie obiektu blob snapshot skutkuje dodatkowe bloki unikatowy dodatkowe opłaty.

### <a name="minimize-cost-with-snapshot-management"></a>Zminimalizowanie kosztów za pomocą funkcji zarządzania migawki

Firma Microsoft zaleca Zarządzanie Usługi migawek, aby uniknąć dodatkowych opłat. Możesz wykonać te najlepsze rozwiązania w celu ułatwienia zminimalizowania kosztów poniesionych przez Magazyn migawek usługi:

* Usunięcie i ponowne utworzenie migawki związane z obiektu blob, po zaktualizowaniu obiektu blob, nawet wtedy, gdy aktualizujesz mają identyczne dane, chyba że projektu aplikacji wymaga, aby zachować migawki. Przez usunięcie i ponowne utworzenie migawki obiektu blob, można zagwarantować, że migawki obiektu blob i różni się.
* Jeśli obsługujesz migawkę obiektu blob, należy unikać wywoływania [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [ UploadFromStream][dotnet_UploadFromStream], lub [UploadFromByteArray] [ dotnet_UploadFromByteArray] można zaktualizować obiektu blob. Te metody zamieniają wszystkich bloków w obiekcie blob, powodując podstawowy obiekt blob i jego migawek, aby rozróżnić znacznie. Zamiast tego należy zaktualizować jak najmniejszej liczby bloków przy użyciu [PutBlock] [ dotnet_PutBlock] i [PutBlockList] [ dotnet_PutBlockList] metody.

### <a name="snapshot-billing-scenarios"></a>Migawka rozliczeń scenariuszy
Następujące scenariusze pokazują, jak opłaty są naliczane za blokowe obiekty blob i jego migawek.

**Scenariusz 1**

W scenariuszu 1 podstawowego obiektu blob nie został zaktualizowany po migawka została utworzona, dzięki czemu opłaty są naliczane tylko dla bloków unikatowy 1, 2 i 3.

![Zasoby usługi Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

**Scenariusz 2**

W scenariuszu 2 podstawowy obiekt blob został zaktualizowany, ale dla migawki istnieje nie. Blok 3 został zaktualizowany, a nawet, jeśli zawiera on te same dane, a tym samym identyfikatorze, nie jest taki sam, jak zablokować 3 w migawce. W rezultacie konto jest opłata za cztery bloki.

![Zasoby usługi Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

**Scenariusz 3**

W scenariuszu 3 podstawowy obiekt blob został zaktualizowany, ale dla migawki istnieje nie. Blok 3 został zastąpiony bloku 4 w obiekcie blob podstawowy, ale migawka odzwierciedla nadal blok 3. W rezultacie konto jest opłata za cztery bloki.

![Zasoby usługi Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

**Scenariusz 4**

W scenariuszu 4 podstawowy obiekt blob został całkowicie zaktualizowany i zawiera żaden z jej oryginalnych bloków. W rezultacie konto jest rozliczane dla wszystkich osiem bloków unikatowy. Ten scenariusz może wystąpić, jeśli używane są metody aktualizacji, takich jak [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [ UploadFromStream][dotnet_UploadFromStream], lub [UploadFromByteArray][dotnet_UploadFromByteArray], ponieważ te metody zamieniają całą zawartość obiektu blob.

![Zasoby usługi Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Kolejne kroki

* Można znaleźć więcej informacji na temat pracy z migawki dysku maszyny wirtualnej (VM) w [tworzenie kopii zapasowej Azure niezarządzanych dysków maszyn wirtualnych przy użyciu migawek przyrostowych](../../virtual-machines/windows/incremental-snapshots.md)

* Przykłady dodatkowego kodu za pomocą magazynu obiektów Blob, zobacz [przykłady kodu platformy Azure](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob). Możesz pobrać przykładową aplikację i uruchom go lub przeglądania kodu w serwisie GitHub.

[dotnet_AccessCondition]: https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.accesscondition
[dotnet_CloudBlockBlob]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_block_blob
[dotnet_CreateSnapshotAsync]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob.generatedblobs.createsnapshotasync
[dotnet_HTTPStatusCode]: https://docs.microsoft.com/java/api/com.microsoft.store.partnercenter.network.httpstatuscode
[dotnet_PutBlockList]: /dotnet/api/microsoft.azure.storage.blob.cloudblockblob.putblocklist
[dotnet_PutBlock]: /dotnet/api/microsoft.azure.storage.blob.cloudblockblob.putblock
[dotnet_UploadFromByteArray]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.uploadfrombytearray
[dotnet_UploadFromFile]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.uploadfromfile
[dotnet_UploadFromStream]: /dotnet/api/microsoft.azure.storage.blob.cloudappendblob.uploadfromstream
[dotnet_UploadText]: /dotnet/api/microsoft.azure.storage.blob.cloudappendblob.uploadtext
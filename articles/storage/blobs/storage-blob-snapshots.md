---
title: Tworzenie migawki tylko do odczytu obiektu blob w usłudze Azure Storage | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć migawki obiektu blob, aby utworzyć kopię zapasową danych obiektów blob w danym momencie w czasie. Dowiedz się, jak są rozliczane migawki i sposób ich użycia, aby zminimalizować koszty pojemności.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/06/2018
ms.author: tamram
ms.openlocfilehash: 1a27dfd61850d9dfa1f232eacf7f09d66202cafe
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2018
ms.locfileid: "29852935"
---
# <a name="create-a-blob-snapshot"></a>Tworzenie migawki obiektu blob

Migawka jest tylko do odczytu wersji obiektu blob, która jest wykonywana w punkcie w czasie. Migawki są przydatne w przypadku tworzenia kopii zapasowej obiektów blob. Po utworzeniu migawki, odczytu, skopiuj lub usuń go, ale nie można go modyfikować.

Migawki obiektu blob jest taka sama jak podstawowa obiektu blob z tą różnicą, że identyfikator URI obiektu blob ma **DateTime** wartość do obiektu blob identyfikatora URI wskazująca czas, w którym migawki. Na przykład jeśli identyfikator URI obiektu blob strony jest `http://storagesample.core.blob.windows.net/mydrives/myvhd`, identyfikator URI jest podobny do migawki `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`.

> [!NOTE]
> Wszystkie migawki mają podstawowego obiektu blob identyfikatora URI. Tylko różnice między podstawową obiektów blob i migawki jest dołączany **DateTime** wartość.
>

Obiekt blob może mieć dowolną liczbę migawek. Migawki zachować, dopóki nie zostaną jawnie usunięte. Migawka nie outlive jego podstawowego obiektu blob. Można wyliczyć migawki skojarzone z podstawowego obiektu blob do śledzenia sieci bieżącej migawki.

Po utworzeniu migawki obiektu blob właściwości systemu obiektu blob są kopiowane do migawki o takich samych wartości. Metadane podstawowego obiektu blob jest również kopiowane do migawki, chyba że zostanie oddzielne metadanych dla migawki podczas jego tworzenia.

Wszystkie dzierżawy skojarzonego z podstawowego obiektu blob nie wpływają na migawki. Nie można pobrać dzierżawy na migawki.

Plik VHD jest używany do przechowywania informacji o bieżącym i stan dysku maszyny Wirtualnej. Można odłączyć dysku z poziomu maszyny Wirtualnej lub zamykania maszyny Wirtualnej, a następnie Utwórz migawkę jego plik VHD. Ten plik migawki można użyć później można pobrać pliku wirtualnego dysku twardego w danym momencie i Utwórz ponownie maszynę Wirtualną.

## <a name="create-a-snapshot"></a>Utwórz migawkę
W poniższym przykładzie przedstawiono sposób tworzenia migawki za pomocą [biblioteki klienta magazynu Azure dla platformy .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). W tym przykładzie określa dodatkowe metadane dla migawki podczas jego tworzenia.

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

## <a name="copy-snapshots"></a>Kopiowanie migawek
Operacje kopiowania obejmujące obiekty BLOB i migawek wykonać następujące czynności:

* Możesz skopiować migawki za pośrednictwem jego podstawowego obiektu blob. Promowanie migawki do położenia podstawowego obiektu blob, można przywrócić wcześniejszą wersję obiektu blob. Pozostaje migawki, ale bazie obiektu blob jest zastępowany kopię zapisu migawki.
* Migawki można skopiować do docelowego obiektu blob o innej nazwie. Wynikowa docelowego obiektu blob jest zapisywalny obiektów blob i nie migawki.
* Po skopiowaniu źródłowego obiektu blob wszystkie migawki źródłowego obiektu blob nie są kopiowane do lokalizacji docelowej. Gdy docelowego obiektu blob jest zastępowany kopii, wszystkie migawki skojarzone z oryginalnego docelowego obiektu blob pozostaną nienaruszone.
* Po utworzeniu migawki blokowych obiektów blob zablokowanych zatwierdzone obiektu blob jest również kopiowany do migawki. Niezatwierdzone bloków nie są kopiowane.

## <a name="specify-an-access-condition"></a>Określ warunek dostępu
Podczas wywoływania [CreateSnapshotAsync][dotnet_CreateSnapshotAsync], można określić warunki dostępu, aby migawki jest tworzony tylko wtedy, gdy warunek jest spełniony. Aby określić warunki dostępu, użyj [AccessCondition] [ dotnet_AccessCondition] parametru. Jeśli nie zostanie spełniony określony warunek, migawki nie zostanie utworzony, a usługa Blob zwraca kod stanu [HTTPStatusCode][dotnet_HTTPStatusCode]. PreconditionFailed.

## <a name="delete-snapshots"></a>Usuń migawki
Nie można usunąć obiektu blob z migawki, chyba że migawki, również zostaną usunięte. Możesz usunąć migawkę indywidualnie lub określić, że wszystkie migawki usunięte po usunięciu źródłowego obiektu blob. Jeśli próbujesz usunąć obiektu blob, które nadal istnieją migawki, powoduje błąd.

W poniższym przykładzie pokazano, jak można usunąć obiektu blob i jego migawek w środowisku .NET, gdzie `blockBlob` jest obiektem typu [CloudBlockBlob][dotnet_CloudBlockBlob]:

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

## <a name="snapshots-with-azure-premium-storage"></a>Migawki z magazynem Azure — warstwa Premium
Korzystając z migawki z magazyn w warstwie Premium, obowiązują następujące reguły:

* Maksymalna liczba migawek na stronicowych obiektów blob na koncie magazynu premium to 100. Po przekroczeniu tego limitu operacji migawki obiektu Blob zwraca kod błędu 409 (`SnapshotCountExceeded`).
* Można utworzyć migawkę stronicowych obiektów blob na koncie magazynu premium co 10 minut. Po przekroczeniu tego kursu operacji migawki obiektu Blob zwraca kod błędu 409 (`SnapshotOperationRateExceeded`).
* Za pomocą operacji kopiowania obiektu Blob można skopiować migawki do innego obiektu blob strony w konto, odczytać migawki. Docelowego obiektu blob dla operacji kopiowania nie może mieć żadnych istniejących migawki. Jeśli docelowego obiektu blob migawki, a następnie zwraca kod błędu 409, operacja kopiowania obiektów Blob (`SnapshotsPresent`).

## <a name="return-the-absolute-uri-to-a-snapshot"></a>Zwraca bezwzględny identyfikator URI do migawki
W tym przykładzie kodu C# tworzy migawkę i zapisuje limit bezwzględny identyfikator URI lokalizacji głównej.

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

## <a name="understand-how-snapshots-accrue-charges"></a>Zrozumienie sposobu migawki naliczania opłat
Tworzenie migawek, która jest tylko do odczytu kopię obiektu blob, może spowodować opłaty za magazyn dodatkowe dane do Twojego konta. Podczas projektowania aplikacji, należy mieć świadomość sposobu naliczania może tych opłat, dzięki czemu można zminimalizować koszty.

### <a name="important-billing-considerations"></a>Istotne zagadnienia dotyczące rozliczeń
Poniższa lista zawiera klucz wskazuje należy wziąć pod uwagę podczas tworzenia migawki.

* Konta magazynu ponosi opłaty za bloki unikatowy lub stron, czy znajdują się w obiekcie blob lub w migawce. Twoje konto nie spowodować naliczenie dodatkowych opłat za migawki skojarzone z obiektu blob, dopóki nie zostanie zaktualizowany obiekt blob, na którym są one oparte na. Po zaktualizowaniu podstawowego obiektu blob diverges go z jego migawek. W takim przypadku są naliczane opłaty za bloki unikatowy lub strony w poszczególnych obiektów blob lub migawki.
* Podczas zastępowania blok w ramach blokowych obiektów blob tego bloku jest następnie rozliczany jako unikatowy bloku. Dotyczy to nawet wtedy, gdy blok ma ten sam identyfikator bloku i samych danych, ponieważ ma ona migawki. Po bloku dba ponownie, diverges go z jego odpowiednikiem w dowolnym migawki i zostanie naliczona danych. To samo dla strony w stronicowych obiektów blob, która jest aktualizowana mają identyczne dane.
* Zastępowanie blokowych obiektów blob, wywołując [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream], lub [UploadFromByteArray] [ dotnet_UploadFromByteArray] metoda zastępuje wszystkie bloki w obiekcie blob. Jeśli masz migawki skojarzone z tego obiektu blob, wszystkie bloki w podstawowej obiektów blob i migawki teraz różni się i zostanie naliczona dla wszystkich bloków w obu obiektów blob. Dotyczy to nawet wtedy, gdy dane w podstawowej obiektów blob i migawki pozostają identyczne.
* Usługa Azure Blob nie ma sposób, aby określić, czy dwa bloki zawierają identyczne dane. Każdy blok przekazane i zatwierdzone jest traktowany jako unikatowy, nawet jeśli ma te same dane i ten sam identyfikator bloku. Ponieważ opłaty naliczane unikatowy bloków, należy wziąć pod uwagę, że aktualizowanie obiektu blob zawierający migawki spowoduje dodatkowe bloki unikatowy dodatkowych opłat.

### <a name="minimize-cost-with-snapshot-management"></a>Minimalizuje koszt z zarządzania migawkami

Zaleca się zarządzanie migawki w dokładnie, aby uniknąć dodatkowych opłat. Możesz wykonać następujące najlepsze rozwiązania, aby zminimalizować kosztów ponoszonych przez Magazyn z migawki:

* Usunięcie i ponowne utworzenie migawki skojarzone z obiektu blob po zaktualizowaniu obiektu blob, nawet jeśli aktualizujesz mają identyczne dane, chyba że projektu aplikacji wymaga, aby zachować migawki. Przez usunięcie i ponowne utworzenie migawki obiektu blob, można zagwarantować, że migawki i obiektów blob nie odbiegają.
* Jeśli obsługujesz migawek dla obiekt blob, należy unikać wywoływania [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream], lub [UploadFromByteArray] [ dotnet_UploadFromByteArray] można zaktualizować obiektu blob. Te metody zastąpić wszystkie bloki w obiekcie blob, powodując sieci podstawowej obiektów blob i jego migawek odchodzenia znacznie. Zamiast tego należy zaktualizować za pomocą najmniejszej liczby bloków [PutBlock] [ dotnet_PutBlock] i [metoda PutBlockList] [ dotnet_PutBlockList] metody.

### <a name="snapshot-billing-scenarios"></a>Migawki rozliczeń scenariuszy
Poniższe scenariusze pokazują, jak Naliczanie opłat dla blokowych obiektów blob i jego migawek.

**Scenariusz 1**

W scenariuszu 1 podstawowego obiektu blob nie został zaktualizowany po migawki, więc jest obciążany tylko dla bloków unikatowy 1, 2 i 3.

![Zasoby usługi Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

**Scenariusz 2**

W scenariuszu 2 podstawowego obiektu blob zostały zaktualizowane, ale migawka nie ma. Blok 3 został zaktualizowany, a nawet, jeśli zawiera te same dane, a tym samym identyfikatorze, nie jest taka sama jak zablokować 3 w migawce. W związku z tym konta jest pobierana cztery bloków.

![Zasoby usługi Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

**Scenariusz 3**

W scenariuszu 3 podstawowego obiektu blob zostały zaktualizowane, ale migawka nie ma. 3 blokowych zamieniono bloku 4 w obiekcie blob podstawowej, ale migawki nadal odzwierciedla blok 3. W związku z tym konta jest pobierana cztery bloków.

![Zasoby usługi Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

**Scenariusz 4**

W scenariuszu 4 podstawowego obiektu blob została całkowicie zaktualizowana i zawiera żaden z jego oryginalnych bloków. W związku z tym rozliczany konta dla wszystkich osiem bloków unikatowy. Ten scenariusz może wystąpić, jeśli używasz metody aktualizacji takich jak [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream], lub [UploadFromByteArray][dotnet_UploadFromByteArray], ponieważ metody te zastąpienie całej zawartości obiektu blob.

![Zasoby usługi Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Kolejne kroki

* Można znaleźć więcej informacji na temat pracy z migawek dysków maszyny wirtualnej (VM) w [kopia zapasowa Azure niezarządzane dysków maszyny Wirtualnej z migawkami przyrostowe](../../virtual-machines/windows/incremental-snapshots.md)

* Dla dodatkowych przykładów kodu przy użyciu magazynu obiektów Blob, zobacz [przykłady kodu Azure](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob). Możesz pobrać przykładową aplikację i uruchom go lub Przeglądaj kodu w witrynie GitHub.

[dotnet_AccessCondition]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.accesscondition.aspx
[dotnet_CloudBlockBlob]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.aspx
[dotnet_CreateSnapshotAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.createsnapshotasync.aspx
[dotnet_HTTPStatusCode]: https://msdn.microsoft.com/library/system.net.httpstatuscode(v=vs.110).aspx
[dotnet_PutBlockList]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.putblocklist.aspx
[dotnet_PutBlock]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.putblock.aspx
[dotnet_UploadFromByteArray]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfrombytearray.aspx
[dotnet_UploadFromFile]: https://msdn.microsoft.com/library/azure/mt705654.aspx
[dotnet_UploadFromStream]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstream.aspx
[dotnet_UploadText]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.uploadtext.aspx
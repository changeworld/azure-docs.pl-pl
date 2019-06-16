---
title: Włączanie publicznego dostępu do odczytu dla kontenerów i obiektów blob w usłudze Azure Blob storage | Dokumentacja firmy Microsoft
description: Dowiedz się udostępnić kontenerów i obiektów blob dla dostępu anonimowego oraz uzyskiwać do nich dostęp programowo.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: e0f93b0a95a228b26fae266129aea4b595b05e0f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65148349"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Zarządzanie dostępem anonimowym w trybie odczytu do kontenerów i obiektów blob

Można włączyć anonimowego, publiczny dostęp do odczytu do kontenera i jego obiektów blob w usłudze Azure Blob storage. Dzięki temu można przyznać dostęp tylko do odczytu do tych zasobów, bez udostępniania klucz konta, a także bez konieczności sygnatury dostępu współdzielonego (SAS).

Publiczny dostęp do odczytu jest najlepsze dla scenariuszy, w którym ma się znaleźć niektórych obiektów blob, które zawsze będą dostępne dla anonimowy dostęp do odczytu. Aby uzyskać bardziej precyzyjną kontrolę można utworzyć sygnaturę dostępu współdzielonego. Sygnatury dostępu współdzielonego umożliwiają ograniczony dostęp przy użyciu różnych uprawnień dla określonego przedziału czasu. Aby uzyskać więcej informacji na temat tworzenia udostępnionych sygnatury dostępu, zobacz [Using shared sygnatur dostępu (SAS) w usłudze Azure Storage](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Przyznawanie uprawnień użytkownikom anonimowym do kontenerów i obiektów blob

Domyślnie kontener i wszystkie obiekty BLOB w niej można uzyskać dostęp tylko przez użytkownika, który ma odpowiednie uprawnienia. Aby udzielić użytkownikom anonimowym dostęp do odczytu kontenera i jego obiektów blob, można ustawić poziom dostępu publicznego do kontenera. Przy udzielaniu dostępu publicznego do kontenera, użytkowników anonimowych może odczytywać obiekty BLOB w kontenerze publicznie dostępny bez autoryzowania żądania.

Można skonfigurować kontenera, z następującymi uprawnieniami:

* **Brak publicznego dostępu do odczytu:** Kontenera i jego obiektów blob są dostępne tylko przez właściciela konta magazynu. Jest to wartość domyślna dla wszystkich nowych kontenerów.
* **Publiczny dostęp do odczytu tylko dla obiektów blob:** Obiekty BLOB w kontenerze mogą być odczytywane przez żądania od użytkowników anonimowych, ale kontenera dane są niedostępne. Anonimowe klientów nie można wyliczyć obiektów blob w kontenerze.
* **Zapoznaj się publiczny dostęp do kontenera i jego obiektów blob:** Wszystkich kontenerów i obiektów blob danych może zostać odczytany przez żądania od użytkowników anonimowych. Klientów można wyliczyć obiektów blob w kontenerze przez żądania od użytkowników anonimowych, ale nie można wyliczyć kontenerów na koncie magazynu.

Następujące służy do ustawiania uprawnień kontenera:

* [Azure Portal](https://portal.azure.com)
* [Azure PowerShell](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Interfejs wiersza polecenia platformy Azure](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-and-manage-blobs)
* Programowo używając jednej z bibliotek klienta usługi storage lub interfejsu API REST

### <a name="set-container-public-access-level-in-the-azure-portal"></a>Ustaw poziom dostępu publicznego do kontenera w witrynie Azure portal

Z [witryny Azure portal](https://portal.azure.com), możesz zaktualizować poziom dostępu publicznego dla co najmniej jeden kontener:

1. W witrynie Azure Portal przejdź do swojego konta magazynu.
1. W obszarze **usługi Blob service** w bloku menu, wybierz **obiektów blob**.
1. Wybierz kontenery, dla których chcesz ustawić poziom dostępu publicznego.
1. Użyj **Zmień poziom dostępu** przycisk, aby wyświetlić ustawienia dostępu publicznego.
1. Wybierz poziom żądanego dostępu publicznego z **poziom dostępu publicznego** listy rozwijanej i kliknij przycisk OK, aby zastosować zmianę na wybrane kontenery.

Poniższy zrzut ekranu pokazuje, jak zmienić poziom dostępu publicznego dla wybranych kontenerów.

![Zrzut ekranu przedstawiający sposób Ustaw poziom dostępu publicznego w portalu](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

> [!NOTE]
> Nie można zmienić poziom dostępu publicznego dla poszczególnych obiektów blob. Poziom dostępu publicznego jest ustawiona tylko na poziomie kontenera.

### <a name="set-container-public-access-level-with-net"></a>Ustaw poziom dostępu publicznego do kontenera przy użyciu platformy .NET

Aby ustawić uprawnienia dla kontenera za pomocą języka C# i biblioteki klienta usługi Storage dla platformy .NET, najpierw pobrać istniejących uprawnień kontenera przez wywołanie metody **GetPermissions** metody. Następnie ustaw **PublicAccess** właściwość **BlobContainerPermissions** obiektu, który jest zwracany przez **GetPermissions** metody. Na koniec Wywołaj **ustawiania** metoda ze zaktualizowanymi uprawnieniami.

W poniższym przykładzie ustawiono kontenera uprawnienia do pełnej publicznego dostępu do odczytu. Aby ustawić uprawnienia do publicznego dostępu do odczytu tylko dla obiektów blob, należy ustawić **PublicAccess** właściwości **BlobContainerPublicAccessType.Blob**. Aby usunąć wszystkie uprawnienia dla użytkowników anonimowych, ustaw właściwość na **BlobContainerPublicAccessType.Off**.

```csharp
public static void SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = container.GetPermissions();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    container.SetPermissions(permissions);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>Anonimowo dostęp do kontenerów i obiektów blob

Klient, który uzyskuje dostęp do kontenerów i obiektów blob anonimowo służy konstruktorów, które nie wymagają poświadczeń. W poniższych przykładach pokazano kilka różnych sposobów, aby odwołać się anonimowo kontenerów i obiektów blob.

### <a name="create-an-anonymous-client-object"></a>Tworzenie obiektu anonimowego klienta

Można utworzyć nowy obiekt klienta usługi dla dostępu anonimowego, zapewniając punktu końcowego magazynu obiektów Blob dla konta. Jednak musisz także wiedzieć nazwę kontenera w ramach tego konta, które są dostępne dla dostępu anonimowego.

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob storage endpoint.
    CloudBlobClient blobClient = new CloudBlobClient(new Uri(@"https://storagesample.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
```

### <a name="reference-a-container-anonymously"></a>Anonimowo odwoływać się do kontenera

Jeśli masz adres URL do kontenera, który znajduje się anonimowo, umożliwia on bezpośrednio odwoływać się do kontenera.

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(new Uri(@"https://storagesample.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
```

### <a name="reference-a-blob-anonymously"></a>Anonimowo odwoływać się do obiektu blob

Jeśli masz adres URL obiektu blob, który jest dostępny dla dostępu anonimowego, możesz odwołać się bezpośrednio przy użyciu tego adresu URL obiektu blob:

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
}
```

## <a name="features-available-to-anonymous-users"></a>Funkcje dostępne dla użytkowników anonimowych

Pokazano w poniższej tabeli operacje, może być wywołana anonimowo gdy kontener jest skonfigurowana dla dostępu publicznego.

| Operacji REST | Publiczny dostęp do odczytu do kontenera | Publiczny dostęp do odczytu tylko do obiektów blob |
| --- | --- | --- |
| Lista kontenerów | Tylko autoryzowanym żądania | Tylko autoryzowanym żądania |
| Tworzenie kontenera | Tylko autoryzowanym żądania | Tylko autoryzowanym żądania |
| Pobierz właściwości kontenera | Dozwolone żądań anonimowych | Tylko autoryzowanym żądania |
| Pobranie metadanych kontenera | Dozwolone żądań anonimowych | Tylko autoryzowanym żądania |
| Metadane kontenera zestawu | Tylko autoryzowanym żądania | Tylko autoryzowanym żądania |
| Pobieranie listy ACL kontenera | Tylko autoryzowanym żądania | Tylko autoryzowanym żądania |
| Ustaw ACL kontenera | Tylko autoryzowanym żądania | Tylko autoryzowanym żądania |
| Usuwanie kontenera | Tylko autoryzowanym żądania | Tylko autoryzowanym żądania |
| Wyświetlanie listy obiektów blob | Dozwolone żądań anonimowych | Tylko autoryzowanym żądania |
| Put Blob | Tylko autoryzowanym żądania | Tylko autoryzowanym żądania |
| Get Blob | Dozwolone żądań anonimowych | Dozwolone żądań anonimowych |
| Pobierz właściwości obiektu Blob | Dozwolone żądań anonimowych | Dozwolone żądań anonimowych |
| Ustaw właściwości obiektu Blob | Tylko autoryzowanym żądania | Tylko autoryzowanym żądania |
| Pobierz metadane obiektu Blob | Dozwolone żądań anonimowych | Dozwolone żądań anonimowych |
| Ustaw metadane obiektu Blob | Tylko autoryzowanym żądania | Tylko autoryzowanym żądania |
| Umieść bloku | Tylko autoryzowanym żądania | Tylko autoryzowanym żądania |
| Pobierz listę zablokowanych (tylko zatwierdzone bloki) | Dozwolone żądań anonimowych | Dozwolone żądań anonimowych |
| Pobieranie listy zablokowanych (tylko bloków niezatwierdzone lub wszystkie bloki) | Tylko autoryzowanym żądania | Tylko autoryzowanym żądania |
| Umieść zablokowanych | Tylko autoryzowanym żądania | Tylko autoryzowanym żądania |
| Usuwanie obiektu Blob | Tylko autoryzowanym żądania | Tylko autoryzowanym żądania |
| Copy Blob | Tylko autoryzowanym żądania | Tylko autoryzowanym żądania |
| Wykonywanie migawki obiektu Blob | Tylko autoryzowanym żądania | Tylko autoryzowanym żądania |
| Dzierżawienie obiektu Blob | Tylko autoryzowanym żądania | Tylko autoryzowanym żądania |
| Umieść strony | Tylko autoryzowanym żądania | Tylko autoryzowanym żądania |
| Pobieranie zakresów stron | Dozwolone żądań anonimowych | Dozwolone żądań anonimowych |
| Obiekt Blob dołączania | Tylko autoryzowanym żądania | Tylko autoryzowanym żądania |

## <a name="next-steps"></a>Kolejne kroki

* [Autoryzacja dla usługi Azure Storage](https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-azure-storage-services)
* [Używanie sygnatur dostępu współdzielonego (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
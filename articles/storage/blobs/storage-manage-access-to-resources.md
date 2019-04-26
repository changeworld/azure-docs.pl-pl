---
title: Włączanie publicznego dostępu do odczytu dla kontenerów i obiektów blob w usłudze Azure Blob storage | Dokumentacja firmy Microsoft
description: Dowiedz się udostępnić kontenerów i obiektów blob dla dostępu anonimowego oraz uzyskiwać do nich dostęp programowo.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/26/2017
ms.author: tamram
ms.openlocfilehash: 3996f22db2f5dc597939995a2699c4fe228821e3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60392577"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Zarządzanie dostępem anonimowym w trybie odczytu do kontenerów i obiektów blob
Można włączyć anonimowego, publiczny dostęp do odczytu do kontenera i jego obiektów blob w usłudze Azure Blob storage. Dzięki temu można przyznać dostęp tylko do odczytu do tych zasobów, bez udostępniania klucz konta, a także bez konieczności sygnatury dostępu współdzielonego (SAS).

Publiczny dostęp do odczytu jest najlepsze dla scenariuszy, w którym ma się znaleźć niektórych obiektów blob, które zawsze będą dostępne dla anonimowy dostęp do odczytu. Aby uzyskać bardziej precyzyjną kontrolę można utworzyć sygnaturę dostępu współdzielonego. Sygnatury dostępu współdzielonego umożliwiają ograniczony dostęp przy użyciu różnych uprawnień dla określonego przedziału czasu. Aby uzyskać więcej informacji na temat tworzenia udostępnionych sygnatury dostępu, zobacz [Using shared sygnatur dostępu (SAS) w usłudze Azure Storage](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Przyznawanie uprawnień użytkownikom anonimowym do kontenerów i obiektów blob
Domyślnie kontener i wszystkie obiekty BLOB w niej można uzyskać dostęp tylko właściciel konta magazynu. Aby dać użytkownikom anonimowym uprawnienia odczytu do kontenera i jego obiektów blob, można ustawić uprawnień kontenera, aby zezwolić na publiczny dostęp. Użytkownicy anonimowi mogą czytać obiektów blob w kontenerze publicznie dostępny bez uwierzytelniania żądania.

Można skonfigurować kontenera, z następującymi uprawnieniami:

* **Brak publicznego dostępu do odczytu:** Kontenera i jego obiektów blob są dostępne tylko przez właściciela konta magazynu. Jest to wartość domyślna dla wszystkich nowych kontenerów.
* **Publiczny dostęp do odczytu tylko dla obiektów blob:** Obiekty BLOB w kontenerze mogą być odczytywane przez żądania od użytkowników anonimowych, ale kontenera dane są niedostępne. Anonimowe klientów nie można wyliczyć obiektów blob w kontenerze.
* **Pełny publiczny dostęp do odczytu:** Wszystkich kontenerów i obiektów blob danych może zostać odczytany przez żądania od użytkowników anonimowych. Klientów można wyliczyć obiektów blob w kontenerze przez żądania od użytkowników anonimowych, ale nie można wyliczyć kontenerów na koncie magazynu.

Następujące służy do ustawiania uprawnień kontenera:

* [Azure Portal](https://portal.azure.com)
* [Azure PowerShell](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Interfejs wiersza polecenia platformy Azure](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-and-manage-blobs)
* Programowo używając jednej z bibliotek klienta usługi storage lub interfejsu API REST

### <a name="set-container-permissions-in-the-azure-portal"></a>Ustaw uprawnienia do kontenera w witrynie Azure portal
Aby ustawić uprawnienia do kontenera w [witryny Azure portal](https://portal.azure.com), wykonaj następujące kroki:

1. Otwórz swoje **konta magazynu** bloku w portalu. Konta usługi storage można znaleźć, wybierając **kont magazynu** w bloku menu głównego portalu.
1. W obszarze **usługę BLOB SERVICE** w bloku menu, wybierz **obiektów blob**.
1. Kliknij prawym przyciskiem myszy w wierszu, kontenera lub wybierz przycisk wielokropka, aby otworzyć kontenera **menu kontekstowe**.
1. Wybierz **zasady dostępu** w menu kontekstowym.
1. Wybierz **dostęp typu** z menu rozwijanego.

    ![Metadane kontenera okno dialogowe Edycja](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

### <a name="set-container-permissions-with-net"></a>Ustaw uprawnienia do kontenera przy użyciu platformy .NET
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
Klient, który uzyskuje dostęp do kontenerów i obiektów blob anonimowo służy konstruktorów, które nie wymagają poświadczeń. W poniższych przykładach pokazano kilka różnych sposobów odwołanie do obiektu Blob service zasobów anonimowo.

### <a name="create-an-anonymous-client-object"></a>Tworzenie obiektu anonimowego klienta
Można utworzyć nowy obiekt klienta usługi dla dostępu anonimowego, podając punkt końcowy usługi Blob dla konta. Jednak musisz także wiedzieć nazwę kontenera w ramach tego konta, które są dostępne dla dostępu anonimowego.

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob service endpoint.
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
Pokazano w poniższej tabeli operacje, może być wywoływana przez użytkowników anonimowych, gdy kontenera lista ACL jest równa pozwolić na publiczny dostęp.

| Operacji REST | Uprawnienia o pełnej publicznego dostępu do odczytu | Uprawnienia za pomocą publicznego dostępu do odczytu tylko dla obiektów blob |
| --- | --- | --- |
| Lista kontenerów |Tylko właściciel |Tylko właściciel |
| Tworzenie kontenera |Tylko właściciel |Tylko właściciel |
| Pobierz właściwości kontenera |Wszyscy |Tylko właściciel |
| Pobranie metadanych kontenera |Wszyscy |Tylko właściciel |
| Metadane kontenera zestawu |Tylko właściciel |Tylko właściciel |
| Pobieranie listy ACL kontenera |Tylko właściciel |Tylko właściciel |
| Ustaw ACL kontenera |Tylko właściciel |Tylko właściciel |
| Usuwanie kontenera |Tylko właściciel |Tylko właściciel |
| Wyświetlanie listy obiektów blob |Wszyscy |Tylko właściciel |
| Put Blob |Tylko właściciel |Tylko właściciel |
| Get Blob |Wszyscy |Wszyscy |
| Pobierz właściwości obiektu Blob |Wszyscy |Wszyscy |
| Ustaw właściwości obiektu Blob |Tylko właściciel |Tylko właściciel |
| Pobierz metadane obiektu blob |Wszyscy |Wszyscy |
| Ustaw metadane obiektu Blob |Tylko właściciel |Tylko właściciel |
| Umieść bloku |Tylko właściciel |Tylko właściciel |
| Pobierz listę zablokowanych (tylko zatwierdzone bloki) |Wszyscy |Wszyscy |
| Pobieranie listy zablokowanych (tylko bloków niezatwierdzone lub wszystkie bloki) |Tylko właściciel |Tylko właściciel |
| Umieść zablokowanych |Tylko właściciel |Tylko właściciel |
| Usuwanie obiektu Blob |Tylko właściciel |Tylko właściciel |
| Copy Blob |Tylko właściciel |Tylko właściciel |
| Wykonywanie migawki obiektu Blob |Tylko właściciel |Tylko właściciel |
| Dzierżawienie obiektu Blob |Tylko właściciel |Tylko właściciel |
| Umieść strony |Tylko właściciel |Tylko właściciel |
| Pobieranie zakresów stron |Wszyscy |Wszyscy |
| Dołącz obiekt blob |Tylko właściciel |Tylko właściciel |

## <a name="next-steps"></a>Kolejne kroki

* [Uwierzytelnianie dla usług Azure Storage](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* [Używanie sygnatur dostępu współdzielonego (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Delegowanie dostępu za pomocą sygnatury dostępu współdzielonego](https://msdn.microsoft.com/library/azure/ee395415.aspx)

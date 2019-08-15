---
title: Włączanie publicznego dostępu do odczytu dla kontenerów i obiektów BLOB w usłudze Azure Blob Storage | Microsoft Docs
description: Dowiedz się, jak udostępniać kontenery i obiekty blob dostępowi Anonimowemu oraz jak korzystać z nich programistycznie.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 6293fc84969c4e246c05da4482f76142263db230
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68985560"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Zarządzanie dostępem anonimowym w trybie odczytu do kontenerów i obiektów blob

Można włączyć anonimowy, publiczny dostęp do odczytu do kontenera i jego obiektów BLOB w usłudze Azure Blob Storage. Dzięki temu można przyznać dostęp tylko do odczytu do tych zasobów bez udostępniania klucza konta, a nie wymagając sygnatury dostępu współdzielonego (SAS).

Publiczny dostęp do odczytu jest najlepszy dla scenariuszy, w których niektóre obiekty blob mają zawsze być dostępne do anonimowego dostępu do odczytu. Aby uzyskać bardziej szczegółowy formant, możesz utworzyć sygnaturę dostępu współdzielonego. Sygnatury dostępu współdzielonego umożliwiają zapewnienie ograniczonego dostępu przy użyciu różnych uprawnień w określonym przedziale czasu. Aby uzyskać więcej informacji na temat tworzenia sygnatur dostępu współdzielonego, zobacz [Używanie sygnatur dostępu współdzielonego (SAS) w usłudze Azure Storage](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Przyznaj anonimowym użytkownikom uprawnienia do kontenerów i obiektów BLOB

Domyślnie kontener i wszystkie obiekty blob w ramach niego mogą być dostępne tylko dla użytkownika, który ma odpowiednie uprawnienia. Aby udzielić anonimowym użytkownikom dostępu do odczytu do kontenera i jego obiektów blob, można ustawić poziom dostępu publicznego kontenera. Po udzieleniu publicznego dostępu do kontenera Użytkownicy anonimowi mogą odczytywać obiekty blob w ramach dostępnego publicznie kontenera bez autoryzowania żądania.

Można skonfigurować kontener z następującymi uprawnieniami:

* **Brak publicznego dostępu do odczytu:** Do kontenera i jego obiektów BLOB można uzyskać dostęp tylko przez właściciela konta magazynu. Jest to wartość domyślna dla wszystkich nowych kontenerów.
* **Publiczny dostęp do odczytu tylko dla obiektów blob:** Obiekty blob w kontenerze mogą być odczytywane przez żądanie anonimowe, ale dane kontenera nie są dostępne. Klienci anonimowi nie mogą wyliczyć obiektów BLOB w kontenerze.
* **Publiczny dostęp do odczytu dla kontenera i jego obiektów blob:** Wszystkie dane kontenera i obiektu BLOB mogą być odczytywane przez żądanie anonimowe. Klienci mogą wyliczać obiekty blob w kontenerze przez żądanie anonimowe, ale nie mogą wyliczać kontenerów na koncie magazynu.

Aby ustawić uprawnienia kontenera, można użyć następujących opcji:

* [Azure Portal](https://portal.azure.com)
* [Azure PowerShell](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Interfejs wiersza polecenia platformy Azure](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-and-manage-blobs)
* Programowo przy użyciu jednej z bibliotek klienta usługi Storage lub interfejsu API REST

### <a name="set-container-public-access-level-in-the-azure-portal"></a>Ustaw poziom dostępu publicznego kontenera w Azure Portal

W [Azure Portal](https://portal.azure.com)można zaktualizować poziom dostępu publicznego dla jednego lub kilku kontenerów:

1. W witrynie Azure Portal przejdź do swojego konta magazynu.
1. W obszarze **BLOB Service** w bloku menu wybierz pozycję **obiekty blob**.
1. Wybierz kontenery, dla których chcesz ustawić poziom dostępu publicznego.
1. Użyj przycisku **Zmień poziom dostępu** , aby wyświetlić ustawienia dostępu publicznego.
1. Wybierz żądany poziom dostępu publicznego z listy rozwijanej **poziom dostępu publicznego** , a następnie kliknij przycisk OK, aby zastosować zmianę do wybranych kontenerów.

Poniższy zrzut ekranu przedstawia sposób zmiany publicznego poziomu dostępu dla wybranych kontenerów.

![Zrzut ekranu przedstawiający sposób ustawiania poziomu dostępu publicznego w portalu](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

> [!NOTE]
> Nie można zmienić publicznego poziomu dostępu dla pojedynczego obiektu BLOB. Poziom dostępu publicznego jest ustawiany tylko na poziomie kontenera.

### <a name="set-container-public-access-level-with-net"></a>Ustawianie publicznego poziomu dostępu kontenera za pomocą platformy .NET

Aby ustawić uprawnienia dla kontenera przy użyciu C# i biblioteki klienta usługi Storage dla platformy .NET, najpierw Pobierz istniejące uprawnienia kontenera przez wywołanie metody getpermissionss. Następnie ustaw właściwość **PublicAccess** dla obiektu **BlobContainerPermissions** , który jest zwracany przez metodę GetPermissions. Na koniec Wywołaj metodę SetPermissions z zaktualizowanymi uprawnieniami.

Poniższy przykład ustawia uprawnienia kontenera na pełny publiczny dostęp do odczytu. Aby ustawić uprawnienia publicznego dostępu do odczytu tylko dla obiektów blob, ustaw właściwość **PublicAccess** na **BlobContainerPublicAccessType. blob**. Aby usunąć wszystkie uprawnienia dla użytkowników anonimowych, należy ustawić właściwość na **BlobContainerPublicAccessType. off**.

```csharp
public static void SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = container.GetPermissions();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    container.SetPermissions(permissions);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>Anonimowe dostęp do kontenerów i obiektów BLOB

Klient, który uzyskuje dostęp do kontenerów i obiektów BLOB anonimowo, może używać konstruktorów, które nie wymagają poświadczeń. W poniższych przykładach przedstawiono kilka różnych sposobów anonimowego odwoływania się do kontenerów i obiektów BLOB.

### <a name="create-an-anonymous-client-object"></a>Tworzenie anonimowego obiektu klienta

Nowy obiekt klienta usługi dla dostępu anonimowego można utworzyć, dostarczając punkt końcowy magazynu obiektów BLOB dla konta. Należy jednak znać nazwę kontenera na tym koncie, który jest dostępny dla dostępu anonimowego.

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

### <a name="reference-a-container-anonymously"></a>Anonimowe odwołanie do kontenera

Jeśli masz adres URL do kontenera, który jest anonimowym dostępnym, możesz go użyć do bezpośredniego odwoływania się do kontenera.

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

### <a name="reference-a-blob-anonymously"></a>Anonimowe odwołanie do obiektu BLOB

Jeśli masz adres URL do obiektu BLOB, który jest dostępny dla dostępu anonimowego, możesz odwołać się do obiektu BLOB bezpośrednio przy użyciu tego adresu URL:

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
}
```

## <a name="features-available-to-anonymous-users"></a>Funkcje dostępne dla użytkowników anonimowych

W poniższej tabeli przedstawiono operacje, które mogą być wywoływane anonimowo, gdy kontener jest skonfigurowany pod kątem dostępu publicznego.

| Operacja REST | Publiczny dostęp do odczytu do kontenera | Publiczny dostęp do odczytu tylko do obiektów blob |
| --- | --- | --- |
| Lista kontenerów | Tylko autoryzowane żądania | Tylko autoryzowane żądania |
| Tworzenie kontenera | Tylko autoryzowane żądania | Tylko autoryzowane żądania |
| Pobierz właściwości kontenera | Dozwolone żądania anonimowe | Tylko autoryzowane żądania |
| Pobierz metadane kontenera | Dozwolone żądania anonimowe | Tylko autoryzowane żądania |
| Ustawianie metadanych kontenera | Tylko autoryzowane żądania | Tylko autoryzowane żądania |
| Pobierz listę ACL kontenerów | Tylko autoryzowane żądania | Tylko autoryzowane żądania |
| Ustawianie listy ACL kontenerów | Tylko autoryzowane żądania | Tylko autoryzowane żądania |
| Usuwanie kontenera | Tylko autoryzowane żądania | Tylko autoryzowane żądania |
| Wyświetl listę obiektów BLOB | Dozwolone żądania anonimowe | Tylko autoryzowane żądania |
| Put Blob | Tylko autoryzowane żądania | Tylko autoryzowane żądania |
| Get Blob | Dozwolone żądania anonimowe | Dozwolone żądania anonimowe |
| Pobierz właściwości obiektu BLOB | Dozwolone żądania anonimowe | Dozwolone żądania anonimowe |
| Ustawianie właściwości obiektu BLOB | Tylko autoryzowane żądania | Tylko autoryzowane żądania |
| Pobierz metadane obiektu blob | Dozwolone żądania anonimowe | Dozwolone żądania anonimowe |
| Ustawianie metadanych obiektu BLOB | Tylko autoryzowane żądania | Tylko autoryzowane żądania |
| Umieść blok | Tylko autoryzowane żądania | Tylko autoryzowane żądania |
| Pobierz listę zablokowanych (tylko bloki zatwierdzone) | Dozwolone żądania anonimowe | Dozwolone żądania anonimowe |
| Pobierz listę zablokowanych (tylko bloki niezatwierdzone lub wszystkie bloki) | Tylko autoryzowane żądania | Tylko autoryzowane żądania |
| Umieść listę zablokowanych | Tylko autoryzowane żądania | Tylko autoryzowane żądania |
| Usuwanie obiektu Blob | Tylko autoryzowane żądania | Tylko autoryzowane żądania |
| Copy Blob | Tylko autoryzowane żądania | Tylko autoryzowane żądania |
| Obiekt BLOB migawek | Tylko autoryzowane żądania | Tylko autoryzowane żądania |
| Obiekt BLOB dzierżawy | Tylko autoryzowane żądania | Tylko autoryzowane żądania |
| Umieść stronę | Tylko autoryzowane żądania | Tylko autoryzowane żądania |
| Pobierz zakresy stron | Dozwolone żądania anonimowe | Dozwolone żądania anonimowe |
| Dołącz obiekt blob | Tylko autoryzowane żądania | Tylko autoryzowane żądania |

## <a name="next-steps"></a>Następne kroki

* [Autoryzacja dla usług Azure Storage](https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-azure-storage-services)
* [Używanie sygnatur dostępu współdzielonego (SAS)](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
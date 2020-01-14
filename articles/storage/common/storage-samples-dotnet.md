---
title: Przykłady usługi Azure Storage korzystające z platformy .NET | Microsoft Docs
description: Wyświetlanie, pobieranie i uruchamianie przykładowego kodu i aplikacji dla usługi Azure Storage. Odkryj przykłady wprowadzenia dla obiektów blob, kolejek, tabel i plików przy użyciu bibliotek klienckich usługi .NET Storage.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/06/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 50c5067c3db2f07da225b72d9ba0a8f0bdc44368
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748144"
---
# <a name="azure-storage-samples-using-net"></a>Przykłady usługi Azure Storage korzystające z platformy .NET

Poniższa tabela zawiera omówienie naszego repozytorium przykładów oraz scenariusze omówione w poszczególnych przykładach. Kliknij linki, aby wyświetlić odpowiedni przykładowy kod w usłudze GitHub.

> [!NOTE]
> Te przykłady używają biblioteki v11 .NET usługi Azure Storage. Aby uzyskać kod V12, zobacz [przykłady](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples) w repozytorium GitHub.

## <a name="blob-samples-v11"></a>Przykłady obiektów BLOB (v11)

| **Scenariusz** | **Przykładowy kod** |
|--------------|-----------------|
| Dołącz obiekt BLOB | [Wprowadzenie z obiektami BLOB](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs#L1144) |
| Blokowy obiekt blob | [Aplikacja internetowa Galeria zdjęć w usłudze Azure Blob Storage](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Szyfrowania po stronie klienta | [Przykłady szyfrowania obiektów BLOB](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/BlobGettingStarted/Program.cs) |
| Copy Blob | [Wprowadzenie z obiektami BLOB](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Tworzenie kontenera | [Aplikacja internetowa Galeria zdjęć w usłudze Azure Blob Storage](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Usuwanie obiektu Blob | [Aplikacja internetowa Galeria zdjęć w usłudze Azure Blob Storage](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Usuwanie kontenera | [Wprowadzenie z obiektami BLOB](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Metadane/właściwości/statystyki obiektu BLOB | [Wprowadzenie z obiektami BLOB](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Lista ACL kontenerów/metadane/właściwości | [Aplikacja internetowa Galeria zdjęć w usłudze Azure Blob Storage](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Pobierz zakresy stron | [Wprowadzenie z obiektami BLOB](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Obiekt BLOB dzierżawy/kontener | [Wprowadzenie z obiektami BLOB](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Lista obiektów BLOB/kontener | [Wprowadzenie z obiektami BLOB](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs) |
| Obiekt BLOB strony | [Wprowadzenie z obiektami BLOB](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs) |
| SAS | [Wprowadzenie z obiektami BLOB](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Właściwości usługi | [Wprowadzenie z obiektami BLOB](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Obiekt BLOB migawek | [Tworzenie kopii zapasowych dysków maszyny wirtualnej platformy Azure przy użyciu migawek przyrostowych](https://github.com/Azure-Samples/storage-blob-dotnet-back-up-with-incremental-snapshots/blob/master/Program.cs) |

## <a name="file-samples-v11"></a>Przykłady plików (v11)

| **Scenariusz** | **Przykładowy kod** |
|--------------|-----------------|
| Tworzenie udziałów/katalogów/plików | [Przykład File Storage .NET usługi Azure Storage](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| Usuwanie udziałów/katalogów/plików | [Wprowadzenie z usługą plików platformy Azure w środowisku .NET](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/master/FileStorage/GettingStarted.cs) |
| Właściwości katalogu/metadane | [Przykład File Storage .NET usługi Azure Storage](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| Pobierz pliki | [Przykład File Storage .NET usługi Azure Storage](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| Właściwości pliku/metadane/metryki | [Przykład File Storage .NET usługi Azure Storage](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| Właściwości usługi plików | [Przykład File Storage .NET usługi Azure Storage](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| Wyświetlanie listy katalogów i plików | [Przykład File Storage .NET usługi Azure Storage](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| Wyświetl listę udziałów | [Przykład File Storage .NET usługi Azure Storage](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| Udostępnianie właściwości/metadanych/statystyk | [Przykład File Storage .NET usługi Azure Storage](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |

## <a name="queue-samples-v11"></a>Przykłady kolejki (v11)

| **Scenariusz** | **Przykładowy kod** |
|--------------|-----------------|
| Dodaj komunikat | [Wprowadzenie z usługą kolejki platformy Azure na platformie .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| Szyfrowania po stronie klienta | [Szyfrowanie po stronie klienta kolejki .NET usługi Azure Storage](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/QueueGettingStarted/Program.cs) |
| Tworzenie kolejek | [Wprowadzenie z usługą kolejki platformy Azure na platformie .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| Usuń komunikat/kolejkę | [Wprowadzenie z usługą kolejki platformy Azure na platformie .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| Wgląd do wiadomości | [Wprowadzenie z usługą kolejki platformy Azure na platformie .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| Lista ACL/metadane/Statystyka kolejki | [Wprowadzenie z usługą kolejki platformy Azure na platformie .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs) |
| Właściwości usługi kolejki | [Wprowadzenie z usługą kolejki platformy Azure na platformie .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs) |
| Aktualizuj komunikat | [Wprowadzenie z usługą kolejki platformy Azure na platformie .NET](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |

## <a name="table-samples-v11"></a>Przykłady tabeli (v11)

| **Scenariusz** | **Przykładowy kod** |
|--------------|-----------------|
| Create Table | [Zarządzanie współbieżnością przy użyciu usługi Azure Storage — Przykładowa aplikacja](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262) |
| Usuń jednostkę/tabelę | [Rozpoczynanie pracy z usługą Azure Table Storage na platformie .NET](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) |
| Wstaw/Scal/Zamień jednostkę | [Zarządzanie współbieżnością przy użyciu usługi Azure Storage — Przykładowa aplikacja](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262) |
| Jednostki zapytań | [Rozpoczynanie pracy z usługą Azure Table Storage na platformie .NET](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) |
| Tabele zapytań | [Rozpoczynanie pracy z usługą Azure Table Storage na platformie .NET](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) |
| Lista ACL/właściwości tabeli | [Rozpoczynanie pracy z usługą Azure Table Storage na platformie .NET](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs) |
| Aktualizuj jednostkę | [Zarządzanie współbieżnością przy użyciu usługi Azure Storage — Przykładowa aplikacja](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262) |

## <a name="azure-code-samples-library"></a>Biblioteka przykładów kodu platformy Azure

Aby wyświetlić kompletną bibliotekę przykładową, przejdź do biblioteki [przykładów kodu platformy Azure](https://azure.microsoft.com/resources/samples/?service=storage) , która zawiera przykłady dla usługi Azure Storage, które można pobrać i uruchomić lokalnie. Biblioteka Przykładowa kodu zawiera przykładowy kod w formacie ZIP. Alternatywnie można przeglądać i klonować repozytorium GitHub dla każdego przykładu.

[!INCLUDE [storage-dotnet-samples-include](../../../includes/storage-dotnet-samples-include.md)]

## <a name="getting-started-guides"></a>Przewodniki wprowadzające

Zapoznaj się z następującymi przewodnikami, jeśli szukasz instrukcji dotyczących sposobu instalacji i rozpoczęcia korzystania z bibliotek klienckich usługi Azure Storage.

* [Wprowadzenie z usługą Azure Blob Service na platformie .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Wprowadzenie z usługą kolejki platformy Azure na platformie .NET](../storage-dotnet-how-to-use-queues.md)
* [Wprowadzenie z usługą Azure Table Service na platformie .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Wprowadzenie z usługą plików platformy Azure w środowisku .NET](../storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat przykładów dla innych języków:

* Java: [przykłady usługi Azure Storage korzystające z języka Java](storage-samples-java.md)
* JavaScript/Node. js: [przykłady usługi Azure Storage przy użyciu języka JavaScript](storage-samples-javascript.md)
* Python: [przykłady usługi Azure Storage przy użyciu języka Python](storage-samples-python.md)
* Wszystkie inne języki: [przykłady usługi Azure Storage](../storage-samples.md)

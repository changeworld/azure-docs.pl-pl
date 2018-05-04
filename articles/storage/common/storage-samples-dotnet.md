---
title: Przykładów dla magazynu platformy Azure przy użyciu platformy .NET | Dokumentacja firmy Microsoft
description: Wyświetlanie, Pobierz i uruchom przykładowy kod i aplikacji usługi Azure Storage. Wykryj, wprowadzenie przykłady dla obiektów blob, kolejek, tabel i plików, za pomocą biblioteki klienta magazynu .NET.
services: storage
documentationcenter: na
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: ''
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 01/12/2017
ms.author: seguler
ms.openlocfilehash: 1e6973f0decc448657d869afb8823dd03c62d272
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="azure-storage-samples-using-net"></a>Przykładów dla magazynu platformy Azure przy użyciu platformy .NET

## <a name="net-sample-index"></a>Indeks przykładów .NET

Poniższa tabela zawiera omówienie naszym repozytorium przykłady i scenariusze w każdej próbce. Kliknij łącza, aby wyświetlić odpowiednie przykładowy kod w serwisie GitHub.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">Endpoint</th><th style="font-size:110%">Scenariusz</th><th style="font-size:110%">Przykładowy kod</th></tr></thead><tbody> 
<tr> 
<td rowspan="16"><b>Blob</b></td>
<td>Dołącz obiektów Blob</td> 
<td><a href="https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.getappendblobreference.aspx">Przykład CloudBlobContainer.GetAppendBlobReference — metoda</a></td> 
</tr> 
<tr> 
<td>Blokowych obiektów Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Galeria fotografii magazynu obiektów Blob Azure aplikacji sieci Web</a></td>
</tr> 
<tr> 
<td>Szyfrowania po stronie klienta</td>
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/BlobGettingStarted/Program.cs">Przykłady szyfrowania obiektów blob</a></td>
</tr> 
<tr> 
<td>Copy Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Rozpoczynanie pracy z obiektami blob</a></td>
</tr> 
<tr> 
<td>Tworzenie kontenera</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Galeria fotografii magazynu obiektów Blob Azure aplikacji sieci Web</a></td>
</tr> 
<tr> 
<td>Usuwanie obiektów Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Galeria fotografii magazynu obiektów Blob Azure aplikacji sieci Web</a></td>
</tr> 
<tr> 
<td>Usunąć kontenera</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Rozpoczynanie pracy z obiektami blob</a></td>
</tr> 
<tr> 
<td>Obiekt blob metadane/właściwości/Stats</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Rozpoczynanie pracy z obiektami blob</a></td>
</tr> 
<tr> 
<td>Kontener właściwości listy ACL/metadanych</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Galeria fotografii magazynu obiektów Blob Azure aplikacji sieci Web</a></td>
</tr> 
<tr> 
<td>Get zakresów stron</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Rozpoczynanie pracy z obiektami blob</a></td>
</tr> 
<tr> 
<td>Kontener obiektów Blob dzierżawy</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Rozpoczynanie pracy z obiektami blob</a></td>
</tr> 
<tr> 
<td>Kontener obiektów Blob listy</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Rozpoczynanie pracy z obiektami blob</a></td>
</tr> 
<tr> 
<td>Stronicowych obiektów Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Rozpoczynanie pracy z obiektami blob</a></td>
</tr>
<tr> 
<td>SAS</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Rozpoczynanie pracy z obiektami blob</a></td>
</tr>   
<tr> 
<td>Właściwości usługi</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Rozpoczynanie pracy z obiektami blob</a></td>
</tr>           
<tr> 
<td>Migawki obiektu Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-back-up-with-incremental-snapshots/blob/master/Program.cs">Dysków kopii zapasowej maszyny wirtualnej platformy Azure przy użyciu przyrostowej migawki</a></td>
</tr> 
<tr> 
<td rowspan="9"><b>Plik</b></td>
<td>Tworzenie plików/katalogów/udziałów</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Przykładowe magazynu plików .NET usługi Azure Storage</a></td> 
</tr>
<tr> 
<td>Usuwanie plików/katalogów/udziałów</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/master/FileStorage/GettingStarted.cs">Wprowadzenie do korzystania z usługi plików na platformę Azure w .NET</a></td> 
</tr> 
<tr> 
<td>Metadanych właściwości katalogów</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Przykładowe magazynu plików .NET usługi Azure Storage</a></td> 
</tr> 
<tr> 
<td>Pobierz pliki</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Przykładowe magazynu plików .NET usługi Azure Storage</a></td> 
</tr> 
<tr> 
<td>Plik właściwości/metadanych/metryk</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Przykładowe magazynu plików .NET usługi Azure Storage</a></td> 
</tr> 
<tr> 
<td>Właściwości usługi plików</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Przykładowe magazynu plików .NET usługi Azure Storage</a></td> 
</tr> 
<tr> 
<td>Lista katalogów i plików</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Przykładowe magazynu plików .NET usługi Azure Storage</a></td> 
</tr>
<tr> 
<td>Listy udziałów</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Przykładowe magazynu plików .NET usługi Azure Storage</a></td> 
</tr>
<tr> 
<td>Udostępnianie właściwości/metadanych/Stats</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Przykładowe magazynu plików .NET usługi Azure Storage</a></td> 
</tr>
<tr> 
<td rowspan="8"><b>Kolejki</b></td>
<td>Dodaj komunikat</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Wprowadzenie do korzystania z usługi kolejek platformy Azure w .NET</a></td> 
</tr> 
<tr> 
<td>Szyfrowania po stronie klienta</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/QueueGettingStarted/Program.cs">Szyfrowanie po stronie klienta kolejki .NET usługi Azure Storage</a></td> 
</tr> 
<tr> 
<td>Tworzenie kolejki</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Wprowadzenie do korzystania z usługi kolejek platformy Azure w .NET</a></td> 
</tr> 
<tr> 
<td>Usuwanie kolejki/komunikatów</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Wprowadzenie do korzystania z usługi kolejek platformy Azure w .NET</a></td> 
</tr> 
<tr> 
<td>Wgląd do wiadomości</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Wprowadzenie do korzystania z usługi kolejek platformy Azure w .NET</a></td> 
</tr> 
<tr> 
<td>Kolejki ACL/metadanych/Stats</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Wprowadzenie do korzystania z usługi kolejek platformy Azure w .NET</a></td> 
</tr> 
<tr> 
<td>Właściwości usługi kolejki</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Wprowadzenie do korzystania z usługi kolejek platformy Azure w .NET</a></td> 
</tr> 
<tr> 
<td>Komunikat dotyczący aktualizacji</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Wprowadzenie do korzystania z usługi kolejek platformy Azure w .NET</a></td> 
</tr> 
<tr> 
<td rowspan="7"><b>Tabela</b></td>
<td>Utwórz tabelę</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Zarządzanie za pomocą usługi Azure Storage — Przykładowa aplikacja współbieżności</a></td> 
</tr> 
<tr> 
<td>Usuń jednostki/tabeli.</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Rozpoczynanie pracy z usługą Azure Table Storage na platformie .NET</a></td> 
</tr> 
<tr> 
<td>Wstaw/Merge/zastępowania jednostki</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Zarządzanie za pomocą usługi Azure Storage — Przykładowa aplikacja współbieżności</a></td> 
</tr> 
<tr> 
<td>Zapytanie jednostek</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Rozpoczynanie pracy z usługą Azure Table Storage na platformie .NET</a></td> 
</tr> 
<tr> 
<td>Tabele kwerendy</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Rozpoczynanie pracy z usługą Azure Table Storage na platformie .NET</a></td> 
</tr> 
<tr> 
<td>Właściwości listy ACL tabeli</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs">Rozpoczynanie pracy z usługą Azure Table Storage na platformie .NET</a></td> 
</tr> 
<tr> 
<td>Aktualizowanie jednostek</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Zarządzanie za pomocą usługi Azure Storage — Przykładowa aplikacja współbieżności</a></td> 
</tr> 
</tbody> 
</table>
<br/>

## <a name="azure-code-samples-library"></a>Biblioteka przykłady kodu platformy Azure

Aby wyświetlić biblioteki kompletnego przykładu, przejdź do [przykłady kodu Azure](https://azure.microsoft.com/resources/samples/?service=storage) biblioteki, która zawiera przykłady dla usługi Azure Storage, który można pobrać i uruchom lokalnie. Biblioteka przykładowy kod zawiera przykładowy kod w formacie zip. Alternatywnie można wybrać i klonowania repozytorium GitHub dla każdej próbki.

[!INCLUDE [storage-dotnet-samples-include](../../../includes/storage-dotnet-samples-include.md)]

## <a name="getting-started-guides"></a>Przewodniki z wprowadzeniem pobierania

Zapoznaj się z następujących przewodników, jeśli chcesz, aby uzyskać instrukcje dotyczące sposobu instalowania i rozpoczynanie pracy z biblioteki klienta magazynu Azure.

* [Wprowadzenie do korzystania z usługi obiektów Blob platformy Azure w .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Wprowadzenie do korzystania z usługi kolejek platformy Azure w .NET](../storage-dotnet-how-to-use-queues.md)
* [Wprowadzenie do korzystania z usługi tabeli platformy Azure w .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Wprowadzenie do korzystania z usługi plików na platformę Azure w .NET](../storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje na próbkach dla innych języków:

* Java: [przykłady magazynu platformy Azure przy użyciu języka Java](storage-samples-java.md)
* Wszystkie inne języki: [przykłady usługi Azure Storage](../storage-samples.md)

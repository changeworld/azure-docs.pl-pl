---
title: Przykłady usługi Azure Storage przy użyciu platformy .NET | Dokumentacja firmy Microsoft
description: Wyświetlanie, Pobierz i uruchom przykładowy kod i aplikacji dla usługi Azure Storage. Dowiedz się, wprowadzenie do przykładów dla obiektów blob, kolejek, tabel i plików, przy użyciu biblioteki klienta .NET magazynu.
services: storage
author: mhopkins-msft
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 05/03/2019
ms.author: mhopkins
ms.subservice: common
ms.openlocfilehash: df7c14f1ee83015303657f9a0babde3d60c92292
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65209685"
---
# <a name="azure-storage-samples-using-net"></a>Przykłady usługi Azure Storage przy użyciu platformy .NET

## <a name="net-sample-index"></a>Indeks przykładów platformy .NET

Poniższa tabela zawiera omówienie naszego repozytorium przykładów i scenariuszy omówione w każdym przykładzie. Kliknij łącza, aby wyświetlić odpowiedni kod przykładowy w usłudze GitHub.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">Endpoint</th><th style="font-size:110%">Scenariusz</th><th style="font-size:110%">Przykładowy kod</th></tr></thead><tbody> 
<tr> 
<td rowspan="16"><b>Obiekt blob</b></td>
<td>Obiekt Blob dołączania</td> 
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs#L1144">Wprowadzenie do obiektów blob</a></td> 
</tr> 
<tr> 
<td>Blokowe obiekty Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Aplikacja sieci Web galerii fotografii systemu Azure Blob Storage</a></td>
</tr> 
<tr> 
<td>Szyfrowania po stronie klienta</td>
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/BlobGettingStarted/Program.cs">Przykłady szyfrowania obiektów blob</a></td>
</tr> 
<tr> 
<td>Copy Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Wprowadzenie do obiektów blob</a></td>
</tr> 
<tr> 
<td>Tworzenie kontenera</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Aplikacja sieci Web galerii fotografii systemu Azure Blob Storage</a></td>
</tr> 
<tr> 
<td>Usuwanie obiektu Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Aplikacja sieci Web galerii fotografii systemu Azure Blob Storage</a></td>
</tr> 
<tr> 
<td>Usuwanie kontenera</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Wprowadzenie do obiektów blob</a></td>
</tr> 
<tr> 
<td>Obiekt blob metadane/właściwości/Stats</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Wprowadzenie do obiektów blob</a></td>
</tr> 
<tr> 
<td>Kontener właściwości listy ACL/metadanych</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Aplikacja sieci Web galerii fotografii systemu Azure Blob Storage</a></td>
</tr> 
<tr> 
<td>Pobieranie zakresów stron</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Wprowadzenie do obiektów blob</a></td>
</tr> 
<tr> 
<td>Kontener obiektów Blob dzierżawy</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Wprowadzenie do obiektów blob</a></td>
</tr> 
<tr> 
<td>Kontener obiektów Blob list</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Wprowadzenie do obiektów blob</a></td>
</tr> 
<tr> 
<td>Obiekt blob typu Page</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Wprowadzenie do obiektów blob</a></td>
</tr>
<tr> 
<td>SAS</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Wprowadzenie do obiektów blob</a></td>
</tr>   
<tr> 
<td>Właściwości usługi</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Wprowadzenie do obiektów blob</a></td>
</tr>           
<tr> 
<td>Wykonywanie migawki obiektu Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-back-up-with-incremental-snapshots/blob/master/Program.cs">Dyski z kopii zapasowej maszyny wirtualnej platformy Azure przy użyciu migawek przyrostowych</a></td>
</tr> 
<tr> 
<td rowspan="9"><b>Plik</b></td>
<td>Tworzenie udziałów/katalogów/plików</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Przykład magazynu plików .NET usługi Azure Storage</a></td> 
</tr>
<tr> 
<td>Usuwanie udziałów/katalogów/plików</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/master/FileStorage/GettingStarted.cs">Wprowadzenie do usługi plików platformy Azure na platformie .NET</a></td> 
</tr> 
<tr> 
<td>Metadane właściwości katalogu</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Przykład magazynu plików .NET usługi Azure Storage</a></td> 
</tr> 
<tr> 
<td>Pobieranie plików</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Przykład magazynu plików .NET usługi Azure Storage</a></td> 
</tr> 
<tr> 
<td>Plik właściwości/metadanych/metryki</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Przykład magazynu plików .NET usługi Azure Storage</a></td> 
</tr> 
<tr> 
<td>Właściwości usługi plików</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Przykład magazynu plików .NET usługi Azure Storage</a></td> 
</tr> 
<tr> 
<td>Lista katalogów i plików</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Przykład magazynu plików .NET usługi Azure Storage</a></td> 
</tr>
<tr> 
<td>Listy udziałów</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Przykład magazynu plików .NET usługi Azure Storage</a></td> 
</tr>
<tr> 
<td>Udostępnianie właściwości/metadanych/Stats</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Przykład magazynu plików .NET usługi Azure Storage</a></td> 
</tr>
<tr> 
<td rowspan="8"><b>kolejki</b></td>
<td>Dodaj komunikat</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Wprowadzenie do usługi Azure Queue Service na platformie .NET</a></td> 
</tr> 
<tr> 
<td>Szyfrowania po stronie klienta</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/QueueGettingStarted/Program.cs">Szyfrowanie po stronie klienta kolejki platformy .NET usługi Azure Storage</a></td> 
</tr> 
<tr> 
<td>Tworzenie kolejki</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Wprowadzenie do usługi Azure Queue Service na platformie .NET</a></td> 
</tr> 
<tr> 
<td>Usuń komunikat/kolejki</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Wprowadzenie do usługi Azure Queue Service na platformie .NET</a></td> 
</tr> 
<tr> 
<td>Wgląd do wiadomości</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Wprowadzenie do usługi Azure Queue Service na platformie .NET</a></td> 
</tr> 
<tr> 
<td>Kolejka listy ACL/metadanych/Stats</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Wprowadzenie do usługi Azure Queue Service na platformie .NET</a></td> 
</tr> 
<tr> 
<td>Właściwości usługi kolejki</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Wprowadzenie do usługi Azure Queue Service na platformie .NET</a></td> 
</tr> 
<tr> 
<td>Aktualizacja wiadomości</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Wprowadzenie do usługi Azure Queue Service na platformie .NET</a></td> 
</tr> 
<tr> 
<td rowspan="7"><b>Tabela</b></td>
<td>Tworzenie tabeli</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Zarządzanie współbieżnością za pomocą usługi Azure Storage — Przykładowa aplikacja</a></td> 
</tr> 
<tr> 
<td>Usuń jednostkę lub tabelę</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Rozpoczynanie pracy z usługą Azure Table Storage na platformie .NET</a></td> 
</tr> 
<tr> 
<td>Wstaw/scalania/zastępowania jednostki</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Zarządzanie współbieżnością za pomocą usługi Azure Storage — Przykładowa aplikacja</a></td> 
</tr> 
<tr> 
<td>Wykonywanie zapytań dotyczących jednostek</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Rozpoczynanie pracy z usługą Azure Table Storage na platformie .NET</a></td> 
</tr> 
<tr> 
<td>Tabele kwerendy</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Rozpoczynanie pracy z usługą Azure Table Storage na platformie .NET</a></td> 
</tr> 
<tr> 
<td>Listy ACL/właściwości tabeli</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs">Rozpoczynanie pracy z usługą Azure Table Storage na platformie .NET</a></td> 
</tr> 
<tr> 
<td>Aktualizuj jednostki</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Zarządzanie współbieżnością za pomocą usługi Azure Storage — Przykładowa aplikacja</a></td> 
</tr> 
</tbody> 
</table>
<br/>

## <a name="azure-code-samples-library"></a>Biblioteka przykładów kodu usługi Azure

Aby wyświetlić pełny przykład biblioteki, przejdź do [przykłady kodu platformy Azure](https://azure.microsoft.com/resources/samples/?service=storage) biblioteki, która zawiera przykłady dla usługi Azure Storage, który można pobrać i uruchomić lokalnie. Biblioteka przykładowy kod zawiera przykładowy kod w formacie zip. Możesz też przeglądać i sklonuj repozytorium GitHub dla każdego przykładu.

[!INCLUDE [storage-dotnet-samples-include](../../../includes/storage-dotnet-samples-include.md)]

## <a name="getting-started-guides"></a>Wprowadzenie — przewodniki

Zapoznaj się z następującymi przewodnikami, jeśli chcesz, aby uzyskać instrukcje dotyczące sposobu instalowania i rozpoczynanie pracy z biblioteki klienta magazynu Azure.

* [Wprowadzenie do usługi Azure Blob Service na platformie .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Wprowadzenie do usługi Azure Queue Service na platformie .NET](../storage-dotnet-how-to-use-queues.md)
* [Wprowadzenie do usługi Azure Table Service na platformie .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Wprowadzenie do usługi plików platformy Azure na platformie .NET](../storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje, przykłady w innych językach:

* Java: [Przykłady usługi Azure Storage korzystające z języka Java](storage-samples-java.md)
* Inne języki: [Przykłady usługi Azure Storage](../storage-samples.md)

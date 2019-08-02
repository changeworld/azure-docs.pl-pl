---
title: Przykłady usługi Azure Storage korzystające z platformy .NET | Microsoft Docs
description: Wyświetlanie, pobieranie i uruchamianie przykładowego kodu i aplikacji dla usługi Azure Storage. Odkryj przykłady wprowadzenia dla obiektów blob, kolejek, tabel i plików przy użyciu bibliotek klienckich usługi .NET Storage.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/03/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: cec6b0498adf55428fade7ae00db6550496a20bd
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721853"
---
# <a name="azure-storage-samples-using-net"></a>Przykłady usługi Azure Storage korzystające z platformy .NET

## <a name="net-sample-index"></a>Przykładowy indeks .NET

Poniższa tabela zawiera omówienie naszego repozytorium przykładów oraz scenariusze omówione w poszczególnych przykładach. Kliknij linki, aby wyświetlić odpowiedni przykładowy kod w usłudze GitHub.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">Endpoint</th><th style="font-size:110%">Scenariusz</th><th style="font-size:110%">Przykładowy kod</th></tr></thead><tbody> 
<tr> 
<td rowspan="16"><b>Obiekt blob</b></td>
<td>Dołącz obiekt blob</td> 
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs#L1144">Wprowadzenie z obiektami BLOB</a></td> 
</tr> 
<tr> 
<td>Blokowy obiekt blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Aplikacja internetowa Galeria zdjęć w usłudze Azure Blob Storage</a></td>
</tr> 
<tr> 
<td>Szyfrowania po stronie klienta</td>
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/BlobGettingStarted/Program.cs">Przykłady szyfrowania obiektów BLOB</a></td>
</tr> 
<tr> 
<td>Copy Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Wprowadzenie z obiektami BLOB</a></td>
</tr> 
<tr> 
<td>Tworzenie kontenera</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Aplikacja internetowa Galeria zdjęć w usłudze Azure Blob Storage</a></td>
</tr> 
<tr> 
<td>Usuwanie obiektu Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Aplikacja internetowa Galeria zdjęć w usłudze Azure Blob Storage</a></td>
</tr> 
<tr> 
<td>Usuwanie kontenera</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Wprowadzenie z obiektami BLOB</a></td>
</tr> 
<tr> 
<td>Metadane/właściwości/statystyki obiektu BLOB</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Wprowadzenie z obiektami BLOB</a></td>
</tr> 
<tr> 
<td>Lista ACL kontenerów/metadane/właściwości</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Aplikacja internetowa Galeria zdjęć w usłudze Azure Blob Storage</a></td>
</tr> 
<tr> 
<td>Pobierz zakresy stron</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Wprowadzenie z obiektami BLOB</a></td>
</tr> 
<tr> 
<td>Obiekt BLOB dzierżawy/kontener</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Wprowadzenie z obiektami BLOB</a></td>
</tr> 
<tr> 
<td>Lista obiektów BLOB/kontener</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Wprowadzenie z obiektami BLOB</a></td>
</tr> 
<tr> 
<td>Stronicowy obiekt blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Wprowadzenie z obiektami BLOB</a></td>
</tr>
<tr> 
<td>SAS</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Wprowadzenie z obiektami BLOB</a></td>
</tr>   
<tr> 
<td>Właściwości usługi</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Wprowadzenie z obiektami BLOB</a></td>
</tr>           
<tr> 
<td>Obiekt BLOB migawek</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-back-up-with-incremental-snapshots/blob/master/Program.cs">Tworzenie kopii zapasowych dysków maszyny wirtualnej platformy Azure przy użyciu migawek przyrostowych</a></td>
</tr> 
<tr> 
<td rowspan="9"><b>Plik</b></td>
<td>Tworzenie udziałów/katalogów/plików</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Przykład File Storage .NET usługi Azure Storage</a></td> 
</tr>
<tr> 
<td>Usuwanie udziałów/katalogów/plików</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/master/FileStorage/GettingStarted.cs">Wprowadzenie z usługą plików platformy Azure w środowisku .NET</a></td> 
</tr> 
<tr> 
<td>Właściwości katalogu/metadane</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Przykład File Storage .NET usługi Azure Storage</a></td> 
</tr> 
<tr> 
<td>Pobierz pliki</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Przykład File Storage .NET usługi Azure Storage</a></td> 
</tr> 
<tr> 
<td>Właściwości pliku/metadane/metryki</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Przykład File Storage .NET usługi Azure Storage</a></td> 
</tr> 
<tr> 
<td>Właściwości usługi plików</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Przykład File Storage .NET usługi Azure Storage</a></td> 
</tr> 
<tr> 
<td>Wyświetlanie listy katalogów i plików</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Przykład File Storage .NET usługi Azure Storage</a></td> 
</tr>
<tr> 
<td>Wyświetl listę udziałów</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Przykład File Storage .NET usługi Azure Storage</a></td> 
</tr>
<tr> 
<td>Udostępnianie właściwości/metadanych/statystyk</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Przykład File Storage .NET usługi Azure Storage</a></td> 
</tr>
<tr> 
<td rowspan="8"><b>Niej</b></td>
<td>Dodaj komunikat</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Wprowadzenie z usługą kolejki platformy Azure na platformie .NET</a></td> 
</tr> 
<tr> 
<td>Szyfrowania po stronie klienta</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/QueueGettingStarted/Program.cs">Szyfrowanie po stronie klienta kolejki .NET usługi Azure Storage</a></td> 
</tr> 
<tr> 
<td>Tworzenie kolejek</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Wprowadzenie z usługą kolejki platformy Azure na platformie .NET</a></td> 
</tr> 
<tr> 
<td>Usuń komunikat/kolejkę</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Wprowadzenie z usługą kolejki platformy Azure na platformie .NET</a></td> 
</tr> 
<tr> 
<td>Wgląd do wiadomości</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Wprowadzenie z usługą kolejki platformy Azure na platformie .NET</a></td> 
</tr> 
<tr> 
<td>Lista ACL/metadane/Statystyka kolejki</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Wprowadzenie z usługą kolejki platformy Azure na platformie .NET</a></td> 
</tr> 
<tr> 
<td>Właściwości usługi kolejki</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Wprowadzenie z usługą kolejki platformy Azure na platformie .NET</a></td> 
</tr> 
<tr> 
<td>Aktualizuj komunikat</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Wprowadzenie z usługą kolejki platformy Azure na platformie .NET</a></td> 
</tr> 
<tr> 
<td rowspan="7"><b>Tabela</b></td>
<td>Utwórz tabelę</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Zarządzanie współbieżnością przy użyciu usługi Azure Storage — Przykładowa aplikacja</a></td> 
</tr> 
<tr> 
<td>Usuń jednostkę/tabelę</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Rozpoczynanie pracy z usługą Azure Table Storage na platformie .NET</a></td> 
</tr> 
<tr> 
<td>Wstaw/Scal/Zamień jednostkę</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Zarządzanie współbieżnością przy użyciu usługi Azure Storage — Przykładowa aplikacja</a></td> 
</tr> 
<tr> 
<td>Jednostki zapytań</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Rozpoczynanie pracy z usługą Azure Table Storage na platformie .NET</a></td> 
</tr> 
<tr> 
<td>Tabele zapytań</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Rozpoczynanie pracy z usługą Azure Table Storage na platformie .NET</a></td> 
</tr> 
<tr> 
<td>Lista ACL/właściwości tabeli</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs">Rozpoczynanie pracy z usługą Azure Table Storage na platformie .NET</a></td> 
</tr> 
<tr> 
<td>Aktualizuj jednostkę</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Zarządzanie współbieżnością przy użyciu usługi Azure Storage — Przykładowa aplikacja</a></td> 
</tr> 
</tbody> 
</table>
<br/>

## <a name="azure-code-samples-library"></a>Biblioteka przykładów kodu platformy Azure

Aby wyświetlić kompletną bibliotekę przykładową, przejdź do biblioteki [przykładów kodu platformy Azure](https://azure.microsoft.com/resources/samples/?service=storage) , która zawiera przykłady dla usługi Azure Storage, które można pobrać i uruchomić lokalnie. Biblioteka Przykładowa kodu zawiera przykładowy kod w formacie ZIP. Alternatywnie można przeglądać i klonować repozytorium GitHub dla każdego przykładu.

[!INCLUDE [storage-dotnet-samples-include](../../../includes/storage-dotnet-samples-include.md)]

## <a name="getting-started-guides"></a>Przewodniki wprowadzające

Zapoznaj się z następującymi przewodnikami, jeśli szukasz instrukcji dotyczących sposobu instalacji i rozpoczęcia korzystania z bibliotek klienckich usługi Azure Storage.

* [Wprowadzenie z usługą Azure Blob Service na platformie .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Wprowadzenie z usługą kolejki platformy Azure na platformie .NET](../storage-dotnet-how-to-use-queues.md)
* [Wprowadzenie z usługą Azure Table Service na platformie .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Wprowadzenie z usługą plików platformy Azure w środowisku .NET](../storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje na temat przykładów dla innych języków:

* Java: [Przykłady usługi Azure Storage korzystające z języka Java](storage-samples-java.md)
* Wszystkie inne języki: [Przykłady usługi Azure Storage](../storage-samples.md)

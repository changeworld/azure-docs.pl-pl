---
title: Przykłady usługi Azure Storage przy użyciu języka Java | Microsoft Docs
description: Wyświetlanie, pobieranie i uruchamianie przykładowego kodu i aplikacji dla usługi Azure Storage. Odkryj przykłady wprowadzenia dla obiektów blob, kolejek, tabel i plików przy użyciu bibliotek klienckich magazynu Java.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/03/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 4348400e66e4b975d1eeb899cab8bb5024260149
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721834"
---
# <a name="azure-storage-samples-using-java"></a>Przykłady usługi Azure Storage korzystające z języka Java

## <a name="java-sample-index"></a>Przykładowy indeks Java

Poniższa tabela zawiera omówienie naszego repozytorium przykładów oraz scenariusze omówione w poszczególnych przykładach. Kliknij linki, aby wyświetlić odpowiedni przykładowy kod w usłudze GitHub.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">Endpoint</th><th style="font-size:110%">Scenariusz</th><th style="font-size:110%">Przykładowy kod</th></tr></thead><tbody>
<tr>
<td rowspan="16"><b>Obiekt blob</b></td>
<td>Dołącz obiekt blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Wprowadzenie z usługą Azure Blob Service w języku Java</a></td>
</tr>
<tr>
<td>Blokowy obiekt blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Wprowadzenie z usługą Azure Blob Service w języku Java</a></td>
</tr>
<tr>
<td>Szyfrowania po stronie klienta</td>
<td><a href="https://github.com/Azure-Samples/storage-java-client-side-encryption">Wprowadzenie z szyfrowaniem po stronie klienta platformy Azure w języku Java</a></td>
</tr>
<tr>
<td>Copy Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Wprowadzenie z usługą Azure Blob Service w języku Java</a></td>
</tr>
<tr>
<td>Tworzenie kontenera</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Wprowadzenie z usługą Azure Blob Service w języku Java</a></td>
</tr>
<tr>
<td>Usuwanie obiektu Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Wprowadzenie z usługą Azure Blob Service w języku Java</a></td>
</tr>
<tr>
<td>Usuwanie kontenera</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Wprowadzenie z usługą Azure Blob Service w języku Java</a></td>
</tr>
<tr>
<td>Metadane/właściwości/statystyki obiektu BLOB</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Wprowadzenie z usługą Azure Blob Service w języku Java</a></td>
</tr>
<tr>
<td>Lista ACL kontenerów/metadane/właściwości</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Wprowadzenie z usługą Azure Blob Service w języku Java</a></td>
</tr>
<tr>
<td>Pobierz zakresy stron</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java#L399">Wprowadzenie z usługą Azure Blob Service w języku Java</a></td>
</tr>
<tr>
<td>Obiekt BLOB dzierżawy/kontener</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Wprowadzenie z usługą Azure Blob Service w języku Java</a></td>
</tr>
<tr>
<td>Lista obiektów BLOB/kontener</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Wprowadzenie z usługą Azure Blob Service w języku Java</a></td>
</tr>
<tr>
<td>Stronicowy obiekt blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Wprowadzenie z usługą Azure Blob Service w języku Java</a></td>
</tr>
<tr>
<td>SAS</td>
<td><a href="https://github.com/Azure/azure-storage-java/blob/89540f018f1160ce55619c6fe7b5f5ff57d0ce10/src/test/java/com/microsoft/azure/storage/Samples.java#L513">Przykład testów SAS</a></td>
</tr>   
<tr>
<td>Właściwości usługi</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Wprowadzenie z usługą Azure Blob Service w języku Java</a></td>
</tr>
<tr>
<td>Obiekt BLOB migawek</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Wprowadzenie z usługą Azure Blob Service w języku Java</a></td>
</tr>
<tr>
<td rowspan="9"><b>Plik</b></td>
<td>Tworzenie udziałów/katalogów/plików</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Wprowadzenie z usługą plików platformy Azure w języku Java</a></td>
</tr>
<tr>
<td>Usuwanie udziałów/katalogów/plików</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Wprowadzenie z usługą plików platformy Azure w języku Java</a></td>
</tr>
<tr>
<td>Właściwości katalogu/metadane</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Wprowadzenie z usługą plików platformy Azure w języku Java</a></td>
</tr>
<tr>
<td>Pobierz pliki</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Wprowadzenie z usługą plików platformy Azure w języku Java</a></td>
</tr>
<tr>
<td>Właściwości pliku/metadane/metryki</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Wprowadzenie z usługą plików platformy Azure w języku Java</a></td>
</tr>
<tr>
<td>Właściwości usługi plików</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Wprowadzenie z usługą plików platformy Azure w języku Java</a></td>
</tr>
<tr>
<td>Wyświetlanie listy katalogów i plików</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Wprowadzenie z usługą plików platformy Azure w języku Java</a></td>
</tr>
<tr>
<td>Wyświetl listę udziałów</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Wprowadzenie z usługą plików platformy Azure w języku Java</a></td>
</tr>
<tr>
<td>Udostępnianie właściwości/metadanych/statystyk</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Wprowadzenie z usługą plików platformy Azure w języku Java</a></td>
</tr>
<tr>
<td rowspan="8"><b>Niej</b></td>
<td>Dodaj komunikat</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java#L63">Wprowadzenie z usługą Azure Queue Service w języku Java</a></td>
</tr>
<tr>
<td>Szyfrowania po stronie klienta</td>
<td><a href="https://github.com/Azure-Samples/storage-java-client-side-encryption/blob/master/src/gettingstarted/KeyVaultGettingStarted.java">Wprowadzenie z szyfrowaniem po stronie klienta platformy Azure w języku Java</a></td>
</tr>
<tr>
<td>Tworzenie kolejek</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Wprowadzenie z usługą Azure Queue Service w języku Java</a></td>
</tr>
<tr>
<td>Usuń komunikat/kolejkę</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Wprowadzenie z usługą Azure Queue Service w języku Java</a></td>
</tr>
<tr>
<td>Wgląd do wiadomości</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Wprowadzenie z usługą Azure Queue Service w języku Java</a></td>
</tr>
<tr>
<td>Lista ACL/metadane/Statystyka kolejki</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java">Wprowadzenie z usługą Azure Queue Service w języku Java</a></td>
</tr>
<tr>
<td>Właściwości usługi kolejki</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java">Wprowadzenie z usługą Azure Queue Service w języku Java</a></td>
</tr>
<tr>
<td>Aktualizuj komunikat</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Wprowadzenie z usługą Azure Queue Service w języku Java</a></td>
</tr>
<tr>
<td rowspan="7"><b>Tabela</b></td>
<td>Utwórz tabelę</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Wprowadzenie do usługi Azure Table Service w języku Java</a></td>
</tr>
<tr>
<td>Usuń jednostkę/tabelę</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Wprowadzenie do usługi Azure Table Service w języku Java</a></td>
</tr>
<tr>
<td>Wstaw/Scal/Zamień jednostkę</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Wprowadzenie do usługi Azure Table Service w języku Java</a></td>
</tr>
<tr>
<td>Jednostki zapytań</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Wprowadzenie do usługi Azure Table Service w języku Java</a></td>
</tr>
<tr>
<td>Tabele zapytań</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Wprowadzenie do usługi Azure Table Service w języku Java</a></td>
</tr>
<tr>
<td>Lista ACL/właściwości tabeli</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableAdvanced.java">Wprowadzenie do usługi Azure Table Service w języku Java</a></td>
</tr>
<tr>
<td>Aktualizuj jednostkę</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Wprowadzenie do usługi Azure Table Service w języku Java</a></td>
</tr>
</tbody>
</table>
<br/>

## <a name="azure-code-samples-library"></a>Biblioteka przykładów kodu platformy Azure

Aby wyświetlić kompletną bibliotekę przykładową, przejdź do biblioteki [przykładów kodu platformy Azure](https://azure.microsoft.com/resources/samples/?service=storage) , która zawiera przykłady dla usługi Azure Storage, które można pobrać i uruchomić lokalnie. Biblioteka Przykładowa kodu zawiera przykładowy kod w formacie ZIP. Alternatywnie można przeglądać i klonować repozytorium GitHub dla każdego przykładu.

[!INCLUDE [storage-java-samples-include](../../../includes/storage-java-samples-include.md)]

## <a name="getting-started-guides"></a>Przewodniki wprowadzające

Zapoznaj się z następującymi przewodnikami, jeśli szukasz instrukcji dotyczących sposobu instalacji i rozpoczęcia korzystania z bibliotek klienckich usługi Azure Storage.

* [Wprowadzenie z usługą Azure Blob Service w języku Java](../blobs/storage-quickstart-blobs-java.md)
* [Wprowadzenie z usługą Azure Queue Service w języku Java](../queues/storage-java-how-to-use-queue-storage.md)
* [Wprowadzenie do usługi Azure Table Service w języku Java](../../cosmos-db/table-storage-how-to-use-java.md)
* [Wprowadzenie z usługą plików platformy Azure w języku Java](../files/storage-java-how-to-use-file-storage.md)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat przykładów dla innych języków:

* .NET: [Przykłady usługi Azure Storage korzystające z platformy .NET](storage-samples-dotnet.md)
* Wszystkie inne języki: [Przykłady usługi Azure Storage](storage-samples.md)

---
title: Przykłady usługi Azure Storage przy użyciu języka Java | Dokumentacja firmy Microsoft
description: Wyświetlanie, Pobierz i uruchom przykładowy kod i aplikacji dla usługi Azure Storage. Dowiedz się, wprowadzenie do przykładów dla obiektów blob, kolejek, tabel i plików, przy użyciu biblioteki klienta magazynu Java.
services: storage
author: seguler
ms.service: storage
ms.devlang: java
ms.topic: article
ms.date: 01/12/2017
ms.author: seguler
ms.component: common
ms.openlocfilehash: bdc25a7aeff88d058eaf3fddf6cec023edff3b9e
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39531479"
---
# <a name="azure-storage-samples-using-java"></a>Przykłady usługi Azure Storage przy użyciu języka Java

## <a name="java-sample-index"></a>Indeks przykładów Java

Poniższa tabela zawiera omówienie naszego repozytorium przykładów i scenariuszy omówione w każdym przykładzie. Kliknij łącza, aby wyświetlić odpowiedni kod przykładowy w usłudze GitHub.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">Endpoint</th><th style="font-size:110%">Scenariusz</th><th style="font-size:110%">Przykładowy kod</th></tr></thead><tbody> 
<tr> 
<td rowspan="16"><b>Obiekt blob</b></td>
<td>Obiekt Blob dołączania</td> 
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Wprowadzenie do usługi obiektów Blob platformy Azure w języku Java</a></td> 
</tr> 
<tr> 
<td>Blokowe obiekty Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Wprowadzenie do usługi obiektów Blob platformy Azure w języku Java</a></td>
</tr> 
<tr> 
<td>Szyfrowania po stronie klienta</td>
<td><a href="https://github.com/Azure-Samples/storage-java-client-side-encryption">Rozpoczęcie korzystania z szyfrowania po stronie klienta platformy Azure w języku Java</a></td>
</tr> 
<tr> 
<td>Copy Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Wprowadzenie do usługi obiektów Blob platformy Azure w języku Java</a></td>
</tr> 
<tr> 
<td>Tworzenie kontenera</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Wprowadzenie do usługi obiektów Blob platformy Azure w języku Java</a></td>
</tr> 
<tr> 
<td>Usuwanie obiektu Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Wprowadzenie do usługi obiektów Blob platformy Azure w języku Java</a></td>
</tr> 
<tr> 
<td>Usuwanie kontenera</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Wprowadzenie do usługi obiektów Blob platformy Azure w języku Java</a></td>
</tr> 
<tr> 
<td>Obiekt blob metadane/właściwości/Stats</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Wprowadzenie do usługi obiektów Blob platformy Azure w języku Java</a></td>
</tr> 
<tr> 
<td>Kontener właściwości listy ACL/metadanych</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Wprowadzenie do usługi obiektów Blob platformy Azure w języku Java</a></td>
</tr> 
<tr> 
<td>Pobieranie zakresów stron</td>
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-test/src/com/microsoft/azure/storage/blob/CloudPageBlobTests.java">Obiekt blob typu Page testy próbki</a></td>
</tr> 
<tr> 
<td>Kontener obiektów Blob dzierżawy</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Wprowadzenie do usługi obiektów Blob platformy Azure w języku Java</a></td>
</tr> 
<tr> 
<td>Kontener obiektów Blob list</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Wprowadzenie do usługi obiektów Blob platformy Azure w języku Java</a></td>
</tr> 
<tr> 
<td>Obiekt blob typu Page</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Wprowadzenie do usługi obiektów Blob platformy Azure w języku Java</a></td>
</tr>
<tr> 
<td>SAS</td>
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-test/src/com/microsoft/azure/storage/blob/SasTests.java">Przykładowe testy sygnatury dostępu Współdzielonego</a></td>
</tr>   
<tr> 
<td>Właściwości usługi</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Wprowadzenie do usługi obiektów Blob platformy Azure w języku Java</a></td>
</tr>           
<tr> 
<td>Wykonywanie migawki obiektu Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Wprowadzenie do usługi obiektów Blob platformy Azure w języku Java</a></td>
</tr> 
<tr> 
<td rowspan="9"><b>Plik</b></td>
<td>Tworzenie udziałów/katalogów/plików</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Wprowadzenie do usługi plików platformy Azure w języku Java</a></td> 
</tr>
<tr> 
<td>Usuwanie udziałów/katalogów/plików</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Wprowadzenie do usługi plików platformy Azure w języku Java</a></td> 
</tr> 
<tr> 
<td>Metadane właściwości katalogu</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Wprowadzenie do usługi plików platformy Azure w języku Java</a></td> 
</tr> 
<tr> 
<td>Pobieranie plików</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Wprowadzenie do usługi plików platformy Azure w języku Java</a></td> 
</tr> 
<tr> 
<td>Plik właściwości/metadanych/metryki</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Wprowadzenie do usługi plików platformy Azure w języku Java</a></td> 
</tr> 
<tr> 
<td>Właściwości usługi plików</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Wprowadzenie do usługi plików platformy Azure w języku Java</a></td> 
</tr> 
<tr> 
<td>Lista katalogów i plików</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Wprowadzenie do usługi plików platformy Azure w języku Java</a></td> 
</tr>
<tr> 
<td>Listy udziałów</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Wprowadzenie do usługi plików platformy Azure w języku Java</a></td> 
</tr>
<tr> 
<td>Udostępnianie właściwości/metadanych/Stats</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Wprowadzenie do usługi plików platformy Azure w języku Java</a></td> 
</tr>
<tr> 
<td rowspan="8"><b>kolejki</b></td>
<td>Dodaj komunikat</td> 
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-samples/src/com/microsoft/azure/storage/queue/gettingstarted/QueueBasics.java">Przykłady biblioteki klienta Java magazynu</a></td> 
</tr> 
<tr> 
<td>Szyfrowania po stronie klienta</td> 
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-samples/src/com/microsoft/azure/storage/encryption/queue/gettingstarted/QueueGettingStarted.java">Przykłady biblioteki klienta Java magazynu</a></td> 
</tr> 
<tr> 
<td>Tworzenie kolejki</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Wprowadzenie do usługi Azure Queue Service w języku Java</a></td> 
</tr> 
<tr> 
<td>Usuń komunikat/kolejki</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Wprowadzenie do usługi Azure Queue Service w języku Java</a></td> 
</tr> 
<tr> 
<td>Wgląd do wiadomości</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Wprowadzenie do usługi Azure Queue Service w języku Java</a></td> 
</tr> 
<tr> 
<td>Kolejka listy ACL/metadanych/Stats</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java">Wprowadzenie do usługi Azure Queue Service w języku Java</a></td> 
</tr> 
<tr> 
<td>Właściwości usługi kolejki</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java">Wprowadzenie do usługi Azure Queue Service w języku Java</a></td> 
</tr> 
<tr> 
<td>Aktualizacja wiadomości</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Wprowadzenie do usługi Azure Queue Service w języku Java</a></td> 
</tr> 
<tr> 
<td rowspan="7"><b>Tabela</b></td>
<td>Utwórz tabelę</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/TableBasics.java">Wprowadzenie do usługi Azure Table Service w języku Java</a></td> 
</tr> 
<tr> 
<td>Usuń jednostkę lub tabelę</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/TableBasics.java">Wprowadzenie do usługi Azure Table Service w języku Java</a></td> 
</tr> 
<tr> 
<td>Wstaw/scalania/zastępowania jednostki</td> 
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-samples/src/com/microsoft/azure/storage/table/gettingtstarted/TableBasics.java">Przykłady biblioteki klienta Java magazynu</a></td> 
</tr> 
<tr> 
<td>Wykonywanie zapytań dotyczących jednostek</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/TableBasics.java">Wprowadzenie do usługi Azure Table Service w języku Java</a></td> 
</tr> 
<tr> 
<td>Tabele kwerendy</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/TableBasics.java">Wprowadzenie do usługi Azure Table Service w języku Java</a></td> 
</tr> 
<tr> 
<td>Listy ACL/właściwości tabeli</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/TableAdvanced.java">Wprowadzenie do usługi Azure Table Service w języku Java</a></td> 
</tr> 
<tr> 
<td>Aktualizuj jednostki</td> 
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-samples/src/com/microsoft/azure/storage/table/gettingtstarted/TableBasics.java">Przykłady biblioteki klienta Java magazynu</a></td> 
</tr> 
</tbody> 
</table>
<br/>

## <a name="azure-code-samples-library"></a>Biblioteka przykładów kodu usługi Azure

Aby wyświetlić pełny przykład biblioteki, przejdź do [przykłady kodu platformy Azure](https://azure.microsoft.com/resources/samples/?service=storage) biblioteki, która zawiera przykłady dla usługi Azure Storage, który można pobrać i uruchomić lokalnie. Biblioteka przykładowy kod zawiera przykładowy kod w formacie zip. Możesz też przeglądać i sklonuj repozytorium GitHub dla każdego przykładu.

[!INCLUDE [storage-java-samples-include](../../../includes/storage-java-samples-include.md)]

## <a name="getting-started-guides"></a>Wprowadzenie — przewodniki

Zapoznaj się z następującymi przewodnikami, jeśli chcesz, aby uzyskać instrukcje dotyczące sposobu instalowania i rozpoczynanie pracy z biblioteki klienta magazynu Azure.

* [Wprowadzenie do usługi obiektów Blob platformy Azure w języku Java](../blobs/storage-quickstart-blobs-java.md)
* [Wprowadzenie do usługi Azure Queue Service w języku Java](../queues/storage-java-how-to-use-queue-storage.md)
* [Wprowadzenie do usługi Azure Table Service w języku Java](../../cosmos-db/table-storage-how-to-use-java.md)
* [Wprowadzenie do usługi plików platformy Azure w języku Java](../files/storage-java-how-to-use-file-storage.md)

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje, przykłady w innych językach:

* .NET: [przykładów usługi azure Storage przy użyciu platformy .NET](storage-samples-dotnet.md)
* Wszystkie inne języki: [przykłady usługi Azure Storage](storage-samples.md)

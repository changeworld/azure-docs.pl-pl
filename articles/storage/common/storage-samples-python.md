---
title: Przykłady usługi Azure Storage przy użyciu języka Python | Microsoft Docs
description: Wyświetlanie, pobieranie i uruchamianie przykładowego kodu i aplikacji dla usługi Azure Storage. Odkryj przykłady wprowadzenia dla obiektów blob, kolejek, tabel i plików przy użyciu bibliotek klienta magazynu języka Python.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/06/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 7f694ee51989023a3e7a72f40700edcbb6a97bae
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75747988"
---
# <a name="azure-storage-samples-using-python"></a>Przykłady usługi Azure Storage przy użyciu języka Python

Poniższe tabele zawierają omówienie naszych repozytorium przykładów oraz scenariusze omówione w poszczególnych przykładach. Kliknij linki, aby wyświetlić odpowiedni przykładowy kod w usłudze GitHub.

> [!NOTE]
> Te przykłady używają biblioteki środowiska Python w usłudze Azure Storage. Aby uzyskać kod V12, zobacz [przykłady](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples) w repozytorium GitHub.

## <a name="blob-samples-v21"></a>Przykłady obiektów BLOB (v 2.1)

| **Scenariusz** | **Przykładowy kod** |
|--------------|-----------------|
| Dołącz obiekt BLOB | [Wprowadzenie z usługą Azure Blob Service w języku Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L166) |
| Blokowy obiekt blob | [Wprowadzenie z usługą Azure Blob Service w języku Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L77) |
| Szyfrowania po stronie klienta | [Zarządzanie kluczami konta magazynu w usłudze Azure key value przy użyciu języka Python](https://github.com/Azure-Samples/key-vault-python-storage-accounts) |
| Copy Blob | [Wprowadzenie z usługą Azure Blob Service w języku Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L102) |
| Tworzenie kontenera | [Wprowadzenie z usługą Azure Blob Service w języku Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L91) |
| Usuwanie obiektu Blob | [Wprowadzenie z usługą Azure Blob Service w języku Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L114) |
| Usuwanie kontenera | [Wprowadzenie z usługą Azure Blob Service w języku Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L118) |
| Metadane/właściwości/statystyki obiektu BLOB | [Wprowadzenie z usługą Azure Blob Service w języku Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L298) |
| Lista ACL kontenerów/metadane/właściwości | [Wprowadzenie z usługą Azure Blob Service w języku Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L268) |
| Pobierz zakresy stron | [Wprowadzenie z usługą Azure Blob Service w języku Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L151) |
| Obiekt BLOB dzierżawy/kontener | [Wprowadzenie z usługą Azure Blob Service w języku Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L377) |
| Lista obiektów BLOB/kontener | [Wprowadzenie z usługą Azure Blob Service w języku Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L103) |
| Obiekt BLOB strony | [Wprowadzenie z usługą Azure Blob Service w języku Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L124) |
| SAS | [Sygnatura dostępu współdzielonego w języku Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L145) |
| Właściwości usługi | [Wprowadzenie z usługą Azure Blob Service w języku Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L540) |
| Obiekt BLOB migawek | [Wprowadzenie z usługą Azure Blob Service w języku Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L214) |

## <a name="file-samples-v21"></a>Przykłady plików (v 2.1)

| **Scenariusz** | **Przykładowy kod** |
|--------------|-----------------|
| Tworzenie udziałów/katalogów/plików | [Wprowadzenie z usługą plików platformy Azure w języku Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L71) |
| Usuwanie udziałów/katalogów/plików | [Wprowadzenie z usługą plików platformy Azure w języku Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L170) |
| Właściwości katalogu/metadane | [Wprowadzenie z usługą plików platformy Azure w języku Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L175) |
| Pobierz pliki | [Wprowadzenie z usługą plików platformy Azure w języku Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L138) |
| Właściwości pliku/metadane/metryki | [Wprowadzenie z usługą plików platformy Azure w języku Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L193) |
| Właściwości usługi plików | [Wprowadzenie z usługą plików platformy Azure w języku Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L125) |
| Wyświetlanie listy katalogów i plików | [Wprowadzenie z usługą plików platformy Azure w języku Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L153) |
| Wyświetl listę udziałów | [Wprowadzenie z usługą plików platformy Azure w języku Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L82) |
| Udostępnianie właściwości/metadanych/statystyk | [Wprowadzenie z usługą plików platformy Azure w języku Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L144) |

## <a name="queue-samples-v21"></a>Przykłady kolejki (v 2.1)

| **Scenariusz** | **Przykładowy kod** |
|--------------|-----------------|
| Dodaj komunikat | [Wprowadzenie z usługą kolejki platformy Azure w języku Python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L94) |
| Szyfrowania po stronie klienta | [Zarządzanie kluczami konta magazynu w Azure Key Vault przy użyciu języka Python](https://github.com/Azure-Samples/key-vault-python-storage-accounts) |
| Tworzenie kolejek | [Wprowadzenie z usługą kolejki platformy Azure w języku Python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L75) |
| Usuń komunikat/kolejkę | [Wprowadzenie z usługą kolejki platformy Azure w języku Python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L144) |
| Wgląd do wiadomości | [Wprowadzenie z usługą kolejki platformy Azure w języku Python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L110) |
| Lista ACL/metadane/Statystyka kolejki | [Wprowadzenie z usługą kolejki platformy Azure w języku Python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_advanced_samples.py#L148) |
| Właściwości usługi kolejki | [Wprowadzenie z usługą kolejki platformy Azure w języku Python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_advanced_samples.py#L128) |
| Aktualizuj komunikat | [Wprowadzenie z usługą kolejki platformy Azure w języku Python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L120) |

## <a name="table-samples-v21"></a>Przykłady tabel (v 2.1)

| **Scenariusz** | **Przykładowy kod** |
|--------------|-----------------|
| Create Table | [Wprowadzenie z usługą Azure Table Service w języku Python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L46) |
| Usuń jednostkę/tabelę | [Wprowadzenie z usługą Azure Table Service w języku Python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L79) |
| Wstaw/Scal/Zamień jednostkę | [Wprowadzenie z usługą Azure Table Service w języku Python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L57) |
| Jednostki zapytań | [Wprowadzenie z usługą Azure Table Service w języku Python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L62) |
| Tabele zapytań | [Wprowadzenie z usługą Azure Table Service w języku Python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py) |
| Lista ACL/właściwości tabeli | [Wprowadzenie z usługą Azure Table Service w języku Python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_advanced_samples.py#L138) |
| Aktualizuj jednostkę | [Wprowadzenie z usługą Azure Table Service w języku Python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L68) |

## <a name="azure-code-samples-library"></a>Biblioteka przykładów kodu platformy Azure

Aby wyświetlić kompletną bibliotekę przykładową, przejdź do biblioteki [przykładów kodu platformy Azure](https://azure.microsoft.com/resources/samples/?service=storage) , która zawiera przykłady dla usługi Azure Storage, które można pobrać i uruchomić lokalnie. Biblioteka Przykładowa kodu zawiera przykładowy kod w formacie ZIP. Alternatywnie można przeglądać i klonować repozytorium GitHub dla każdego przykładu.

[!INCLUDE [storage-python-samples-include](../../../includes/storage-python-samples-include.md)]

## <a name="getting-started-guides"></a>Przewodniki wprowadzające

Zapoznaj się z następującymi przewodnikami, jeśli szukasz instrukcji dotyczących sposobu instalacji i rozpoczęcia korzystania z bibliotek klienckich usługi Azure Storage.

* [Wprowadzenie z usługą Azure Blob Service w języku Python](../blobs/storage-quickstart-blobs-python.md)
* [Wprowadzenie z usługą kolejki platformy Azure w języku Python](../queues/storage-python-how-to-use-queue-storage.md)
* [Wprowadzenie z usługą Azure Table Service w języku Python](../../cosmos-db/table-storage-how-to-use-python.md)
* [Wprowadzenie z usługą plików platformy Azure w języku Python](../files/storage-python-how-to-use-file-storage.md)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat przykładów dla innych języków:

* .NET: [przykłady usługi Azure Storage korzystające z platformy .NET](storage-samples-dotnet.md)
* Java: [przykłady usługi Azure Storage korzystające z języka Java](storage-samples-java.md)
* JavaScript/Node. js: [przykłady usługi Azure Storage przy użyciu języka JavaScript](storage-samples-javascript.md)
* Wszystkie inne języki: [przykłady usługi Azure Storage](storage-samples.md)

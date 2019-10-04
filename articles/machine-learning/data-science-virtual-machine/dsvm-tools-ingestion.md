---
title: Narzędzia do pozyskiwania danych
titleSuffix: Azure Data Science Virtual Machine
description: Dowiedz się więcej o narzędziach do pozyskiwania danych i narzędziach, które są wstępnie zainstalowane na Data Science Virtual Machine.
keywords: narzędzia do nauki dotyczące danych, maszyna wirtualna do nauki o danych, narzędzia do nauki o danych, nauka danych systemu Linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/3/2019
ms.openlocfilehash: 47a701cce348e86359947376b3d6a9915dfb2264
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71950168"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Narzędzia do pozyskiwania danych Data Science Virtual Machine

Jako jeden z pierwszych kroków technicznych w projekcie analizy danych lub AI, należy zidentyfikować zestawy danych, które mają być używane, i umieścić je w środowisku analitycznym. Data Science Virtual Machine (DSVM) oferuje narzędzia i biblioteki do przenoszenia danych z różnych źródeł do magazynu danych analitycznych lokalnie na DSVM lub na platformę danych w chmurze lub lokalnie.

Oto kilka narzędzi do przenoszenia danych, które są dostępne w DSVM.

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| Co to?   | Narzędzie do kopiowania danych z usługi Azure Blob Storage do Azure Data Lake Store. Może również kopiować dane między dwoma kontami Azure Data Lake Store.      |
| Obsługiwane wersje DSVM      | Windows      |
| Typowe zastosowania      | Importowanie wielu obiektów blob z usługi Azure Blob Storage do Azure Data Lake Store.      |
|  Jak używać/uruchamiać?    |   Otwórz wiersz polecenia i wpisz `adlcopy`, aby uzyskać pomoc.    |
| Linki do przykładów      | [Korzystanie z AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| Narzędzia pokrewne na DSVM      | AzCopy, interfejs wiersza polecenia platformy Azure     |

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

|    |           |
| ------------- | ------------- |
| Co to?   | Narzędzie do zarządzania dla platformy Azure. Zawiera również zlecenia poleceń służące do przenoszenia danych z platform danych platformy Azure, takich jak Azure Blob Storage i Azure Data Lake Store.     |
| Obsługiwane wersje DSVM      | Windows, Linux     |
| Typowe zastosowania      | Importowanie i eksportowanie danych do i z usługi Azure Storage oraz do Azure Data Lake Store.      |
|  Jak używać/uruchamiać?    |   Otwórz wiersz polecenia i wpisz `az`, aby uzyskać pomoc.    |
| Linki do przykładów      | [Korzystanie z interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure)     |
| Narzędzia pokrewne na DSVM      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| Co to?   | Narzędzie do kopiowania danych do i z plików lokalnych, magazynu obiektów blob platformy Azure, plików i tabel.      |
| Obsługiwane wersje DSVM      | Windows      |
| Typowe zastosowania      | Kopiowanie plików do usługi Azure Blob Storage i kopiowanie obiektów BLOB między kontami.      |
|  Jak używać/uruchamiać?    |   Otwórz wiersz polecenia i wpisz `azcopy`, aby uzyskać pomoc.    |
| Linki do przykładów      | [AzCopy w systemie Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| Narzędzia pokrewne na DSVM      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Narzędzie do migracji danych Azure Cosmos DB

|    |           |
| ------------- | ------------- |
| Co to?   | Narzędzie do importowania danych z różnych źródeł do Azure Cosmos DB. Te źródła obejmują pliki JSON, pliki CSV, SQL, MongoDB, Azure Table Storage, Amazon DynamoDB i Azure Cosmos DB kolekcje interfejsów API SQL.      |
| Obsługiwane wersje DSVM      | Windows      |
| Typowe zastosowania      | Importowanie plików z maszyny wirtualnej do CosmosDB, importowanie danych z usługi Azure Table Storage do CosmosDB i importowanie danych z bazy danych Microsoft SQL Server do CosmosDB.     |
|  Jak używać/uruchamiać?    |   Aby użyć wersji wiersza polecenia, Otwórz wiersz polecenia i wpisz `dt`. Aby użyć narzędzia graficznego interfejsu użytkownika, Otwórz wiersz polecenia i wpisz `dtui`.    |
| Linki do przykładów      | [CosmosDB Importuj dane](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| Narzędzia pokrewne na DSVM      | AzCopy, AdlCopy      |


## <a name="bcp"></a>narzędzia

|    |           |
| ------------- | ------------- |
| Co to?   | SQL Server Narzędzie do kopiowania danych między SQL Server i plikiem danych.      |
| Obsługiwane wersje DSVM      | Windows      |
| Typowe zastosowania      | Importowanie pliku CSV do tabeli SQL Server i eksportowanie tabeli SQL Server do pliku.      |
|  Jak używać/uruchamiać?    |   Otwórz wiersz polecenia i wpisz `bcp`, aby uzyskać pomoc.    |
| Linki do przykładów      | [Narzędzie bcp](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| Narzędzia pokrewne na DSVM      | SQL Server, SQLCMD      |

## <a name="blobfuse"></a>blobfuse

|    |           |
| ------------- | ------------- |
| Co to?   | Narzędzie do instalowania kontenera magazynu obiektów blob platformy Azure w systemie plików Linux.      |
| Obsługiwane wersje DSVM      | Linux      |
| Typowe zastosowania      | Odczytywanie i zapisywanie obiektów BLOB w kontenerze.      |
|  Jak używać i uruchamiać je?    |   Uruchom _blobfuse_ w terminalu.    |
| Linki do przykładów      | [blobfuse w serwisie GitHub](https://github.com/Azure/azure-storage-fuse)      |
| Narzędzia pokrewne na DSVM      | Interfejs wiersza polecenia platformy Azure      |

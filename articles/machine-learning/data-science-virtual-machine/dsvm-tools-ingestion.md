---
title: Narzędzia do wprowadzania danych
titleSuffix: Azure Data Science Virtual Machine
description: Dowiedz się więcej o narzędziach pozyskiwania danych i narzędziach, które są preinstalowane na maszynie wirtualnej nauki o danych.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: d86858f8d7f09628457b718ca3c481934d720081
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270058"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Narzędzia pozyskiwania danych maszyny wirtualnej do nauki o danych

Jako jeden z pierwszych kroków technicznych w projekcie analizy danych lub sztucznej inteligencji należy zidentyfikować zestawy danych, które mają być używane, i przenieść je do środowiska analitycznego. Maszyna wirtualna do nauki o danych (DSVM) udostępnia narzędzia i biblioteki do wprowadzania danych z różnych źródeł do magazynu danych analitycznych lokalnie na dsvm lub do platformy danych w chmurze lub lokalnie.

Oto kilka narzędzi przenoszenia danych, które są dostępne w DSVM.

## <a name="adlcopy"></a>AdlCopy (Polski)

|    |           |
| ------------- | ------------- |
| co to jest?   | Narzędzie do kopiowania danych z magazynu obiektów blob platformy Azure do magazynu usługi Azure Data Lake Store. Można również skopiować dane między dwoma kontami usługi Azure Data Lake Store.      |
| Obsługiwane wersje DSVM      | Windows      |
| Typowe zastosowania      | Importowanie wielu obiektów blob z magazynu obiektów Blob platformy Azure do magazynu usługi Azure Data Lake Store.      |
|  Jak go używać / uruchomić?    |   Otwórz wiersz polecenia `adlcopy` i wpisz, aby uzyskać pomoc.    |
| Odnośniki do próbek      | [Korzystanie z narzędzia AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| Powiązane narzędzia w systemie DSVM      | AzCopy, Azure CLI     |

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

|    |           |
| ------------- | ------------- |
| co to jest?   | Narzędzie do zarządzania platformą Azure. Zawiera również zlecenia poleceń do przenoszenia danych z platform danych platformy Azure, takich jak magazyn obiektów Blob platformy Azure i usługi Azure Data Lake Store.     |
| Obsługiwane wersje DSVM      | Windows, Linux     |
| Typowe zastosowania      | Importowanie i eksportowanie danych do i z usługi Azure Storage i usługi Azure Data Lake Store.      |
|  Jak go używać / uruchomić?    |   Otwórz wiersz polecenia `az` i wpisz, aby uzyskać pomoc.    |
| Odnośniki do próbek      | [Korzystanie z interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure)     |
| Powiązane narzędzia w systemie DSVM      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>Narzędzie AzCopy

|    |           |
| ------------- | ------------- |
| co to jest?   | Narzędzie do kopiowania danych do i z plików lokalnych, magazynu obiektów Blob platformy Azure, plików i tabel.      |
| Obsługiwane wersje DSVM      | Windows      |
| Typowe zastosowania      | Kopiowanie plików do magazynu obiektów Blob platformy Azure i kopiowanie obiektów blob między kontami.      |
|  Jak go używać / uruchomić?    |   Otwórz wiersz polecenia `azcopy` i wpisz, aby uzyskać pomoc.    |
| Odnośniki do próbek      | [Narzędzie AzCopy w systemie Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| Powiązane narzędzia w systemie DSVM      | AdlCopy (Polski)     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Narzędzie do migracji danych usługi Azure Cosmos DB

|    |           |
| ------------- | ------------- |
| co to jest?   | Narzędzie do importowania danych z różnych źródeł do usługi Azure Cosmos DB, bazy danych NoSQL w chmurze. Źródła te obejmują pliki JSON, pliki CSV, SQL, MongoDB, Usługi Azure Table storage, Amazon DynamoDB i kolekcje interfejsu SQL USŁUGI Azure Cosmos DB.      |
| Obsługiwane wersje DSVM      | Windows      |
| Typowe zastosowania      | Importowanie plików z maszyny Wirtualnej do usługi CosmosDB, importowanie danych z magazynu tabel platformy Azure do usługi CosmosDB i importowanie danych z bazy danych programu Microsoft SQL Server do usługi CosmosDB.     |
|  Jak go używać / uruchomić?    |   Aby użyć wersji wiersza polecenia, otwórz `dt`wiersz polecenia i wpisz polecenie . Aby użyć narzędzia GUI, otwórz wiersz `dtui`polecenia i wpisz polecenie .    |
| Odnośniki do próbek      | [Dane importu usługi CosmosDB](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| Powiązane narzędzia w systemie DSVM      | AzCopy, AdlCopy      |

## <a name="azure-storage-explorer"></a>Eksplorator usługi Azure Storage

|    |           |
| ------------- | ------------- |
| co to jest?   | Graficzny interfejs użytkownika do interakcji z plikami przechowywanymi w chmurze platformy Azure. |
| Obsługiwane wersje DSVM      | Windows      |
| Typowe zastosowania      | Importowanie i eksportowanie danych z dsvm.    |
|  Jak go używać / uruchomić?    | Wyszukaj "Eksplorator usługi Azure Storage" w menu Start. |
| Odnośniki do próbek      | [Eksplorator usługi Azure Storage](vm-do-ten-things.md#access-azure-data-and-analytics-services)      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| co to jest?   | Narzędzie SQL Server do kopiowania danych między programem SQL Server a plikiem danych.      |
| Obsługiwane wersje DSVM      | Windows      |
| Typowe zastosowania      | Importowanie pliku CSV do tabeli programu SQL Server i eksportowanie tabeli programu SQL Server do pliku.      |
|  Jak go używać / uruchomić?    |   Otwórz wiersz polecenia `bcp` i wpisz, aby uzyskać pomoc.    |
| Odnośniki do próbek      | [Bcp](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| Powiązane narzędzia w systemie DSVM      | SQL Server, sqlcmd      |

## <a name="blobfuse"></a>bluzka

|    |           |
| ------------- | ------------- |
| co to jest?   | Narzędzie do instalowania kontenera magazynu obiektów Blob platformy Azure w systemie plików systemu linux.      |
| Obsługiwane wersje DSVM      | Linux      |
| Typowe zastosowania      | Odczytywanie i zapisywanie do obiektów blob w kontenerze.      |
|  Jak go używać i uruchamiać?    |   Uruchom _bluzkę_ na terminalu.    |
| Odnośniki do próbek      | [bluzka na GitHub](https://github.com/Azure/azure-storage-fuse)      |
| Powiązane narzędzia w systemie DSVM      | Interfejs wiersza polecenia platformy Azure      |

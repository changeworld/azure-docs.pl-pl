---
title: Narzędzia wprowadzanie danych maszyny wirtualnej nauki danych - Azure | Dokumentacja firmy Microsoft
description: Narzędzia wprowadzanie danych maszyny wirtualnej nauki danych
keywords: narzędzia do analizy danych, maszyny wirtualnej analizy danych, narzędzia do analizy danych, nauki danych systemu linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 7aeb0476fffb8c9e5cf2b0b5d89a2a387bd6364a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Narzędzia wprowadzanie danych maszyny wirtualnej nauki danych

Pierwszy techniczne czynności w nauce danych lub w projekcie AI jest zidentyfikowanie zestawy danych do użycia i dostosować je do środowiska usługi analytics. Maszyna wirtualna nauki danych (DSVM) zawiera narzędzia i biblioteki, aby przenieść dane z różnych źródeł w magazynie danych analitycznych, lokalnie na DSVM lub na platformie danych w chmurze lub lokalnie. 

Poniżej przedstawiono niektóre narzędzia przepływu danych, które firma Microsoft umieściła na DSVM. 

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| Co to jest?   | Narzędzie można skopiować danych z obiektów blob magazynu Azure do usługi Azure Data Lake Store. Można także skopiować dane między dwa konta usługi Azure Data Lake Store.      |
| DSVM obsługiwane wersje      | Windows      |
| Typowe zastosowania      | Importowanie wielu obiektów blob magazynu Azure do usługi Azure Data Lake Store.      |
|  Jak używać / uruchom go?    |   Otwórz wiersz polecenia, wpisz `adlcopy` Aby uzyskać pomoc.    |
| Łącza do próbek      | [Korzystanie z narzędzia AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| Pokrewne narzędzia na DSVM      | Narzędzia AzCopy, wiersza polecenia platformy Azure     |

## <a name="azure-command-line"></a>Wiersza polecenia platformy Azure

|    |           |
| ------------- | ------------- |
| Co to jest?   | Narzędzie do zarządzania dla platformy Azure. Zawiera także zleceń polecenie, aby przenieść dane z usługi Azure data platform, takich jak obiekty BLOB magazynu Azure, Azure Data Lake Storage     |
| DSVM obsługiwane wersje      | Windows, Linux     |
| Typowe zastosowania      | Importowanie i eksportowanie danych do i z magazynu Azure, Azure Data Lake Store      |
|  Jak używać / uruchom go?    |   Otwórz wiersz polecenia, wpisz `az` Aby uzyskać pomoc.    |
| Łącza do próbek      | [Korzystanie z interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure)     |
| Pokrewne narzędzia na DSVM      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>Narzędzie AzCopy

|    |           |
| ------------- | ------------- |
| Co to jest?   | Narzędzie można skopiować danych do i z lokalnych plików, obiektów blob magazynu Azure, plików i tabele.      |
| DSVM obsługiwane wersje      | Windows      |
| Typowe zastosowania      | Kopiowanie plików do magazynu obiektów blob, kopiowania obiektów blob między kontami.      |
|  Jak używać / uruchom go?    |   Otwórz wiersz polecenia, wpisz `azcopy` Aby uzyskać pomoc.    |
| Łącza do próbek      | [Narzędzie AzCopy w systemie Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| Pokrewne narzędzia na DSVM      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Azure narzędzia migracji danych DB rozwiązania Cosmos

|    |           |
| ------------- | ------------- |
| Co to jest?   | Narzędzie do importowania danych z różnych źródeł, takich jak pliki w formacie JSON, CSV plików SQL, bazy danych MongoDB, Magazyn tabel Azure, Amazon DynamoDB i interfejsu API Azure rozwiązania Cosmos bazy danych SQL kolekcje do bazy danych Azure rozwiązania Cosmos.      |
| DSVM obsługiwane wersje      | Windows      |
| Typowe zastosowania      | Importowanie plików z maszyny Wirtualnej do CosmosDB, importowanie danych z magazynu tabel platformy Azure do CosmosDB lub importowanie danych z bazy danych programu SQL Server do CosmosDB.     |
|  Jak używać / uruchom go?    |   Aby użyć wiersza polecenia wersji, otwórz wiersz polecenia, wpisz `dt`. Aby użyć narzędzia graficznego interfejsu użytkownika, otwórz wiersz polecenia, wpisz `dtui`.    |
| Łącza do próbek      | [Importowanie CosmosDB danych](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| Pokrewne narzędzia na DSVM      | AzCopy, AdlCopy      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| Co to jest?   | Narzędzie SQL Server, aby skopiować danych między programami SQL Server i plik danych.      |
| DSVM obsługiwane wersje      | Windows      |
| Typowe zastosowania      | Importowanie pliku CSV do tabeli programu SQL Server, eksportowanie tabeli programu SQL Server do pliku.      |
|  Jak używać / uruchom go?    |   Otwórz wiersz polecenia, wpisz `bcp` Aby uzyskać pomoc.    |
| Łącza do próbek      | [Bulk Copy Utility](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| Pokrewne narzędzia na DSVM      | SQL Server, narzędzia sqlcmd      |

## <a name="blobfuse"></a>blobfuse

|    |           |
| ------------- | ------------- |
| Co to jest?   | Narzędzie do zainstalowania kontenera obiektów blob platformy Azure w systemie Linux.      |
| DSVM obsługiwane wersje      | Linux      |
| Typowe zastosowania      | Odczytywanie i zapisywanie do obiektów blob w kontenerze      |
|  Jak używać / uruchom go?    |   Uruchom _blobfuse_ w terminalu.    |
| Łącza do próbek      | [blobfuse w witrynie GitHub](https://github.com/Azure/azure-storage-fuse)      |
| Pokrewne narzędzia na DSVM      | Wiersza polecenia platformy Azure      |


## <a name="microsoft-data-management-gateway"></a>Brama zarządzania danymi firmy Microsoft

|    |           |
| ------------- | ------------- |
| Co to jest?   | Narzędzie nawiązać lokalnych źródeł danych do usługi do użycia w chmurze.      |
| DSVM obsługiwane wersje      | Windows      |
| Typowe zastosowania      | Łączenie maszyny Wirtualnej z lokalnego źródła danych.      |
|  Jak używać / uruchom go?    |   Uruchom "Brama zarządzania danymi firmy Microsoft" z Start Menu.    |
| Łącza do próbek      | [Brama zarządzania danymi](https://msdn.microsoft.com/library/dn879362.aspx)      |
| Pokrewne narzędzia na DSVM      | AzCopy, AdlCopy, bcp    |

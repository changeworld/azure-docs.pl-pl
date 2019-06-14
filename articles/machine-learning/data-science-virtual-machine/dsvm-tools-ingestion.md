---
title: Maszyna wirtualna do analizy danych narzędzia do wprowadzania danych — Azure | Dokumentacja firmy Microsoft
description: Informacje na temat narzędzia do wprowadzania danych i narzędzia wstępnie zainstalowane na maszynie wirtualnej do nauki o danych.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 92ff5d21fc30d8fcafe97a2b452ff157a2cd5f86
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60502226"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Narzędzia do wprowadzania danych maszyny wirtualnej do nauki o danych

Jest jedną z pierwszych kroków pomoc do nauki o danych lub projektu sztucznej Inteligencji do identyfikowania zestawów danych, można użyć do dostosowania ich do środowiska usługi analytics. Maszyna wirtualna do nauki o danych (DSVM) udostępnia narzędzia i biblioteki, aby wyświetlić dane z różnych źródeł w magazynie danych analitycznych lokalnie na maszyny DSVM lub na platformie danych w chmurze lub lokalnie. 

Poniżej przedstawiono niektóre narzędzia przenoszenia danych, które udostępniliśmy maszyny DSVM. 

## <a name="adlcopy"></a>Narzędzia AdlCopy

|    |           |
| ------------- | ------------- |
| Co to jest?   | Narzędzie, aby skopiować dane z usługi Azure storage BLOB do usługi Azure Data Lake Store. Ponadto można kopiować dane między dwa konta usługi Azure Data Lake Store.      |
| Wersje maszyny DSVM obsługiwane      | Windows      |
| Typowe zastosowania      | Importowanie wielu obiektów blob z usługi Azure storage do usługi Azure Data Lake Store.      |
|  Jak używać / ją uruchomić?    |   Otwórz wiersz polecenia, a następnie wpisz `adlcopy` Aby uzyskać pomoc.    |
| Zawiera linki do przykładów      | [Korzystanie z narzędzia AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| Pokrewne narzędzia na maszyny DSVM      | Narzędzia AzCopy, wiersza polecenia platformy Azure     |

## <a name="azure-command-line"></a>Wiersza polecenia platformy Azure

|    |           |
| ------------- | ------------- |
| Co to jest?   | Narzędzie do zarządzania dla platformy Azure. Zawiera ona także zlecenia polecenia, aby przenieść dane z usługi Azure data platform, takich jak usługi Azure storage blob usługi Azure Data Lake Storage     |
| Wersje maszyny DSVM obsługiwane      | Windows, Linux     |
| Typowe zastosowania      | Importowanie i eksportowanie danych do i z usługi Azure storage, Azure Data Lake Store      |
|  Jak używać / ją uruchomić?    |   Otwórz wiersz polecenia, a następnie wpisz `az` Aby uzyskać pomoc.    |
| Zawiera linki do przykładów      | [Korzystanie z interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure)     |
| Pokrewne narzędzia na maszyny DSVM      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>Narzędzie AzCopy

|    |           |
| ------------- | ------------- |
| Co to jest?   | Narzędzie do kopiowania danych do i z plików lokalnych, usługi Azure storage blob, tabel i plików.      |
| Wersje maszyny DSVM obsługiwane      | Windows      |
| Typowe zastosowania      | Kopiowanie plików do magazynu obiektów blob, kopiowanie obiektów blob między kontami.      |
|  Jak używać / ją uruchomić?    |   Otwórz wiersz polecenia, a następnie wpisz `azcopy` Aby uzyskać pomoc.    |
| Zawiera linki do przykładów      | [Narzędzie AzCopy w systemie Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| Pokrewne narzędzia na maszyny DSVM      | Narzędzia AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Narzędzie do migracji danych usługi Cosmos DB platformy Azure

|    |           |
| ------------- | ------------- |
| Co to jest?   | Narzędzie do importowania danych z różnych źródeł, takich jak pliki w formacie JSON, pliki CSV, SQL, bazy danych MongoDB, magazynu tabel Azure, Amazon DynamoDB i interfejsu API SQL usługi Azure Cosmos DB kolekcji do usługi Azure Cosmos DB.      |
| Wersje maszyny DSVM obsługiwane      | Windows      |
| Typowe zastosowania      | Importowanie plików z maszyny Wirtualnej do bazy danych cosmos DB, importowanie danych z usługi Azure table storage do bazy danych cosmos DB lub importowania danych z bazy danych programu SQL Server do bazy danych cosmos DB.     |
|  Jak używać / ją uruchomić?    |   Aby użyć wiersza polecenia, a następnie wpisz wersję, otwórz wiersz polecenia `dt`. Za pomocą narzędzia z graficznym interfejsem użytkownika, otwórz wiersz polecenia, a następnie wpisz `dtui`.    |
| Zawiera linki do przykładów      | [Importowanie bazy danych cosmos DB danych](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| Pokrewne narzędzia na maszyny DSVM      | AzCopy, AdlCopy      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| Co to jest?   | Narzędzia programu SQL Server do skopiowania danych między programu SQL Server i plik danych.      |
| Wersje maszyny DSVM obsługiwane      | Windows      |
| Typowe zastosowania      | Importowanie pliku CSV do tabeli programu SQL Server, eksportowanie tabeli programu SQL Server do pliku.      |
|  Jak używać / ją uruchomić?    |   Otwórz wiersz polecenia, a następnie wpisz `bcp` Aby uzyskać pomoc.    |
| Zawiera linki do przykładów      | [Bulk Copy Utility](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| Pokrewne narzędzia na maszyny DSVM      | SQL Server, narzędzia sqlcmd      |

## <a name="blobfuse"></a>blobfuse

|    |           |
| ------------- | ------------- |
| Co to jest?   | Narzędzie do zamontowania kontenera obiektów blob platformy Azure w systemie plików systemu Linux.      |
| Wersje maszyny DSVM obsługiwane      | Linux      |
| Typowe zastosowania      | Odczytywanie i zapisywanie do obiektów blob w kontenerze      |
|  Jak używać / ją uruchomić?    |   Uruchom _blobfuse_ w terminalu.    |
| Zawiera linki do przykładów      | [blobfuse w witrynie GitHub](https://github.com/Azure/azure-storage-fuse)      |
| Pokrewne narzędzia na maszyny DSVM      | Wiersza polecenia platformy Azure      |


## <a name="microsoft-data-management-gateway"></a>Brama zarządzania danymi firmy Microsoft

|    |           |
| ------------- | ------------- |
| Co to jest?   | Narzędzie do łączenia z lokalnych źródeł danych do usługi do użycia w chmurze.      |
| Wersje maszyny DSVM obsługiwane      | Windows      |
| Typowe zastosowania      | Łączenie maszyny Wirtualnej ze źródłem danych lokalnych.      |
|  Jak używać / ją uruchomić?    |   Uruchom "Brama zarządzania danymi firmy Microsoft" z Start Menu.    |
| Zawiera linki do przykładów      | [Brama zarządzania danymi](https://msdn.microsoft.com/library/dn879362.aspx)      |
| Pokrewne narzędzia na maszyny DSVM      | AzCopy, AdlCopy, bcp    |

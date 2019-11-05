---
title: Obsługiwane magazyny danych w udziale danych platformy Azure
description: Dowiedz się więcej na temat magazynów danych, które są obsługiwane w przypadku używania udziału danych platformy Azure.
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 762cea6dce3e0c6be3f5e977c5f9de806ca0880e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516398"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Obsługiwane magazyny danych w udziale danych platformy Azure

Udział danych platformy Azure zapewnia otwarte i elastyczne udostępnianie danych, w tym możliwość udostępniania z i do różnych magazynów danych. Dostawcy danych mogą udostępniać dane z jednego typu magazynu danych, a ich konsumenci danych mogą wybrać magazyn danych, do którego mają być wysyłane dane. 

Ten artykuł zawiera informacje o rozbudowanym zestawie magazynów danych systemu Azure, które są obsługiwane w udziale danych platformy Azure. Możesz również znaleźć informacje dotyczące kombinacji magazynów danych, które mogą być wykorzystywane przez dostawców danych i użytkowników danych. 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Jakie magazyny danych są obsługiwane w udziale danych platformy Azure? 

Poniższa tabela zawiera szczegółowe informacje o obsługiwanych źródłach danych dla udziału danych platformy Azure. 

| Magazyn danych | Udostępnianie oparte na migawce | Udostępnianie w miejscu 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ | |
| Usługa Azure Data Lake Storage 1. generacji |✓ | |
| Usługa Azure Data Lake Storage 2. generacji |✓ ||
| Azure SQL Database |Publiczna wersja zapoznawcza | |
| Azure SQL Data Warehouse |Publiczna wersja zapoznawcza | |
| Azure Data Explorer | |[Ograniczona wersja zapoznawcza](https://aka.ms/azuredatasharepreviewsignup) |

## <a name="data-store-support-matrix"></a>Macierz obsługi magazynu danych

Udział danych platformy Azure oferuje klientom danych elastyczność podczas wybierania magazynu danych w celu akceptowania danych do programu. Na przykład dane udostępniane przez Azure SQL Database można odbierać do Azure Data Lake Store Gen2, Azure SQL Database lub Azure SQL Data Warehouse. Klienci mogą wybrać format, w którym będą wysyłane dane podczas konfigurowania otrzymanego udziału danych. 

Poniższa tabela zawiera szczegółowe informacje o różnych kombinacjach i wyborach, które użytkownicy danych mają podczas akceptowania i konfigurowania udziału danych. Aby uzyskać więcej informacji na temat konfigurowania mapowań zestawu danych, zobacz [jak skonfigurować mapowania zestawu danych](how-to-configure-mapping.md).

|  | Azure Blob Storage | Usługa Azure SQL Data Lake Gen1 | Usługa Azure SQL Data Lake Gen2 | Azure SQL Database | Azure SQL Data Warehouse 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ ||✓|
| Usługa Azure Data Lake Storage 1. generacji |✓ | |✓|
| Usługa Azure Data Lake Storage 2. generacji |✓ | |✓|
| Azure SQL Database |✓ | |✓|✓|✓|
| Azure SQL Data Warehouse |✓ | |✓|✓|✓|

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zacząć udostępniać dane, przejdź do samouczka [udostępnianie danych](share-your-data.md) .

---
title: Obsługiwane magazyny danych w usłudze Azure Data Share
description: Dowiedz się więcej o magazynach danych, które są obsługiwane w celu korzystania z usługi Azure Data Share.
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 624bb45de3e2ff184326949611d437f71f3e2def
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501805"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Obsługiwane magazyny danych w usłudze Azure Data Share

Usługa Azure Data Share zapewnia otwarte i elastyczne udostępnianie danych, w tym możliwość udostępniania z i do różnych magazynów danych. Dostawcy danych mogą udostępniać dane z jednego typu magazynu danych, a ich konsumenci mogą wybrać magazyn danych, do którego mają być odbierane dane. 

W tym artykule dowiesz się o bogatym zestawie magazynów danych platformy Azure, które są obsługiwane w usłudze Azure Data Share. Można również znaleźć informacje na temat kombinacji magazynów danych, które mogą być wykorzystywane przez dostawców danych i konsumentów danych. 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Jakie magazyny danych są obsługiwane w usłudze Azure Data Share? 

W poniższej tabeli opisano obsługiwane źródła danych dla usługi Azure Data Share. 

| Magazyn danych | Udostępnianie oparte na migawkach | Udostępnianie w miejscu 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ | |
| Usługa Azure Data Lake Storage 1. generacji |✓ | |
| Usługa Azure Data Lake Storage 2. generacji |✓ ||
| Azure SQL Database |Publiczna wersja zapoznawcza | |
| Usługa Azure Synapse Analytics (dawniej Azure SQL DW) |Publiczna wersja zapoznawcza | |
| Azure Data Explorer | |Publiczna wersja zapoznawcza |

## <a name="data-store-support-matrix"></a>Macierz obsługi magazynu danych

Usługa Azure Data Share oferuje konsumentom danych elastyczność przy podejmowaniu decyzji o magazynie danych, aby zaakceptować dane. Na przykład dane udostępniane z usługi Azure SQL Database mogą być odbierane do usługi Azure Data Lake Store Gen2, azure SQL Database lub Usługi Azure Synapse Analytics. Klienci mogą wybrać format, w którym mają być odbierane dane podczas konfigurowania odebranego udziału danych. 

Poniższa tabela zawiera szczegółowe informacje o różnych kombinacjach i opcjach, które konsumenci mają przy akceptowaniu i konfigurowaniu udziału danych. Aby uzyskać więcej informacji na temat konfigurowania mapowań zestawów danych, zobacz [jak skonfigurować mapowania zestawów danych](how-to-configure-mapping.md).

|  | Azure Blob Storage | Usługa Azure Data Lake Storage 1. generacji | Usługa Azure Data Lake Storage 2. generacji | Azure SQL Database | Azure Synapse Analytics 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage | ✓ || ✓|
| Usługa Azure Data Lake Storage 1. generacji | ✓ | | ✓|
| Usługa Azure Data Lake Storage 2. generacji | ✓ | | ✓|
| Azure SQL Database | ✓ | | ✓| ✓| ✓|
| Usługa Azure Synapse Analytics (dawniej Azure SQL DW) | ✓ | | ✓| ✓| ✓|

## <a name="share-from-a-storage-account"></a>Udostępnianie z konta magazynu
Usługa Azure Data Share obsługuje udostępnianie plików, folderów i systemów plików z usługi Azure Data Lake Gen1 i Azure Data Lake Gen2. Obsługuje również udostępnianie obiektów blob, folderów i kontenerów z usługi Azure Blob Storage. Tylko blok obiektu blob jest obecnie obsługiwany. Gdy foldery są współużytkowane w udostępnianiu opartym na migawkach, konsument danych może wybrać opcję wykonania pełnej kopii danych udziału lub wykorzystać możliwość przyrostowej migawki do kopiowania tylko nowych lub zaktualizowanych plików. Istniejące pliki o tej samej nazwie zostaną zastąpione.

## <a name="share-from-a-sql-based-source"></a>Udostępnianie ze źródła opartego na języku SQL
Usługa Azure Data Share obsługuje udostępnianie tabel lub widoków z usługi Azure SQL Database i usługi Azure Synapse Analytics (dawniej Azure SQL DW). Konsumenci danych mogą zaakceptować dane w usłudze Azure Data Lake Store Gen2 lub usłudze Azure Blob Storage jako pliku csv lub parquet. Należy zauważyć, że domyślnie formaty plików są csv. Konsument danych może zdecydować się na odbiór danych w formacie parkietu w razie potrzeby. Można to zrobić w ustawieniach mapowania zestawu danych podczas odbierania danych. 

Podczas akceptowania danych w usłudze Azure Data Lake Store Gen2 lub usługi Azure Blob Storage pełne migawki zastępują zawartość pliku docelowego. 

Konsument danych może wybrać odbiór danych do wybranej przez siebie tabeli. W tym scenariuszu jeśli tabela docelowa jeszcze nie istnieje, usługa Azure Data Share tworzy tabelę SQL ze schematem źródłowym. Jeśli tabela docelowa już istnieje o tej samej nazwie, zostanie upuszczona i zastąpiona najnowszą pełną migawką. Podczas mapowania tabeli docelowej można określić alternatywny schemat i nazwę tabeli. Migawki przyrostowe nie są obecnie obsługiwane. 

Udostępnianie ze źródeł opartych na języku SQL ma wymagania wstępne związane z regułami zapory i uprawnieniami. Szczegółowe informacje można znaleźć w sekcji wymagań wstępnych w samouczku [udostępniania danych.](share-your-data.md)

## <a name="share-from-azure-data-explorer"></a>Udostępnianie z Eksploratora danych platformy Azure
Usługa Azure Data Share obsługuje możliwość udostępniania baz danych w miejscu z klastrów usługi Azure Data Explorer. Dostawca danych może udostępniać na poziomie bazy danych lub klastra. Po udostępnieniu na poziomie bazy danych konsument danych będzie mógł uzyskać dostęp tylko do określonych baz danych udostępnionych przez dostawcę danych. Po udostępnieniu na poziomie klastra konsument danych może uzyskać dostęp do wszystkich baz danych z klastra dostawcy, w tym do wszystkich przyszłych baz danych utworzonych przez dostawcę danych.

Aby uzyskać dostęp do udostępnionych baz danych, konsument danych musi mieć własny klaster usługi Azure Data Explorer. Klaster eksploratora danych azure dla odbiorców danych musi zlokalizować się w tym samym centrum danych platformy Azure, co klaster usługi Azure Explorer dostawcy danych. Po nawiązaniu relacji udostępniania usługa Azure Data Share tworzy łącze symboliczne między dostawcą a klastrami usługi AzureExer explorer dla konsumenta.

Usługa Azure Data Explorer obsługuje dwa tryby pozyskiwania danych: wsadowe i przesyłania strumieniowego. Dane odebrane z partii w udostępnionej bazie danych będą wyświetlane od kilku sekund do kilku minut po stronie konsumenta danych. Dane odebrane ze strony przesyłania strumieniowego mogą potrwać do 24 godzin, aby pojawić się po stronie konsumenta danych. 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak rozpocząć udostępnianie danych, przejdź do [samouczka udostępniania danych.](share-your-data.md)

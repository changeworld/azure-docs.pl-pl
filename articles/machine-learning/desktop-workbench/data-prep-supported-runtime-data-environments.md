---
title: Obsługiwane kombinacje środowiska wykonywania i danych dla usługi Azure Machine Learning danych przygotowań | Dokumentacja firmy Microsoft
description: Ten dokument zawiera pełną listę obsługiwanych kombinacji różnych środowisk uruchomieniowych i źródła danych dla usługi Azure Machine Learning danych przygotowań
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 9168ac1d26432ca3eee5a59b63aa0cec3ae72856
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989060"
---
# <a name="supported-matrix-for-this-release"></a>Obsługiwanych macierzy w tej wersji 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Gdy kod ładuje dane za pomocą źródeł danych programu Azure Machine Learning lub Azure Machine Learning danych czynności przygotowawcze, wprowadzenie albo Pandas lub Spark dataframe następujących kombinacji eksperymentu obliczenia środowisk i lokalizacje danych są obsługiwane:

|     |Pliki lokalne  |Azure Blob Storage  |Baza danych programu SQL Server ***  |
|---------|---------|---------|---------|---------|
|Lokalne języka Python    |     Obsługiwane    |Nieobsługiwane         | Nieobsługiwane        |         |
|Python platformy docker (maszyny Wirtualnej systemu Linux)     |Obsługiwane w plikach projektu tylko *         | Nieobsługiwane        |        Nieobsługiwane |         |
|PySpark docker (maszyny Wirtualnej systemu Linux)     |Obsługiwane w plikach projektu tylko *     |Obsługiwane         | Obsługiwane**        |         |
|Python maszyny wirtualnej do nauki o danych platformy Azure     |Obsługiwane w plikach projektu tylko *         |Nieobsługiwane         |Nieobsługiwane         |         |
|PySPark maszyny wirtualnej do nauki o danych platformy Azure     | Obsługiwane w plikach projektu tylko *        |Nieobsługiwane         |Nieobsługiwane         |         |
|Azure HDInsight PySpark     | Nieobsługiwane        |Obsługiwane         |Obsługiwane**         |         |
|Azure HDInsight dla języka Python     | Nieobsługiwane        | Nieobsługiwane        | Nieobsługiwane        |         |

Azure Data Lake Store nie jest obecnie obsługiwane dla dowolnego obliczeniowego elementu docelowego.

* Gdy używane są ścieżki pliku lokalnego, pliki w projekcie są kopiowane do środowiska obliczeniowego, a następnie zapoznaj się dostępne. Pliki poza projektem nie są kopiowane i ścieżki nie zostaną rozpoznane w środowiska obliczeniowego. Należy rozważyć użycie podstawienia źródła danych, dzięki czemu kod może użyć lokalnego pliku podczas uruchamiania lokalnego. Następnie przełącz się do obiektu blob usługi Azure Storage dla różnych konfiguracji uruchamiania. Również służy próbkowania pomocy technicznej do źródeł danych do zarządzania uruchamiany dużych ilości danych tylko w niektórych konfiguracjach wykonywania.

** Korzysta z narzędzia Maven JDBC programu SQL Server, sterownik 6.2.1. Należy się upewnić, że ten pakiet (lub jeden zgodny) znajduje się w pliku spark_dependencies.yml dla środowiska obliczeniowego.

Obsługa usługi Azure SQL Database lub SQL Server pod warunkiem, że baza danych jest osiągalna z poziomu środowiska obliczeniowego. 

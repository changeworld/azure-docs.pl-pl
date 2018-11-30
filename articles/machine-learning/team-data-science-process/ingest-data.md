---
title: Ładowanie danych do środowiska usługi Azure storage analytics | Dokumentacja firmy Microsoft
description: Przenoszenie danych do oraz z usługi Azure Blob Storage
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: f73488947b85c97d00257faf8907055227570cb6
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441728"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Ładowanie danych w środowiskach magazynowania do celów analizy

Proces analizy danych zespołu wymaga, aby dane można pozyskane lub ładowany w różnych środowiskach innego magazynu należy przetworzyć lub analizowany w najlepszy sposób na każdym etapie procesu. Często używane do przetwarzania miejscami docelowymi danych obejmują usługi Azure Blob Storage, baz danych SQL Azure, programu SQL Server na maszynie Wirtualnej platformy Azure HDInsight (Hadoop) i Azure Machine Learning. 

Następujące artykuły zawierają instrukcje dotyczące pozyskiwania danych w różnych środowiskach docelowych, gdzie dane są przechowywane i przetwarzane.

* Do/z [usługi Azure Blob Storage](move-azure-blob.md)
* Aby [programu SQL Server na maszynie Wirtualnej platformy Azure](move-sql-server-virtual-machine.md)
* Aby [bazy danych Azure SQL](move-sql-azure.md)
* Aby [tabel programu Hive](move-hive-tables.md)
* Aby [SQL partycjonowane tabele](parallel-load-sql-partitioned-tables.md)
* Z [środowiska lokalnego programu SQL Server](move-sql-azure-adf.md)

Sformatuj technicznych i potrzeb biznesowych, a także początkową lokalizację i rozmiaru danych określi środowiskach docelowych, w których dane muszą być pozyskiwane w celu osiągnięcia celów analizy. Nie jest niczym niezwykłym scenariusza wymagają dane można przenosić między kilka środowisk, aby osiągnąć różnych zadań wymaganych do konstruowania modelu predykcyjnego. Ta sekwencja zadań może zawierać na przykład eksplorację danych, przetwarzanie wstępne, czyszczenia, próbkowania w dół i szkoleń modelowych.

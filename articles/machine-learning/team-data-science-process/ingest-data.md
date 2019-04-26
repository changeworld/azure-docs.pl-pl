---
title: Ładowanie danych do usługi Azure storage środowisk, danych zespołu dla celów naukowych
description: Przenoszenie danych do oraz z usługi Azure Blob Storage
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 56c45bf6db79ded64574a44399712951e82c1c3e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60303591"
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

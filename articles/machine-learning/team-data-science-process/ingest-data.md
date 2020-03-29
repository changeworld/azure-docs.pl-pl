---
title: Ładowanie danych do środowisk usługi Azure Storage — proces nauki o danych zespołu
description: Dowiedz się, jak pozyskiwania danych w różnych środowiskach docelowych, w których dane są przechowywane i przetwarzane.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f8eab59d810fb825dbebf80d01d8efd2dd0a9841
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720541"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Ładowanie danych w środowiskach magazynowania do celów analizy

Proces nauki o danych zespołu wymaga, aby dane były pojmowane lub ładowane w najbardziej odpowiedni sposób na każdym etapie. Miejsca docelowe danych mogą obejmować usługę Azure Blob Storage, bazy danych SQL Azure, program SQL Server na maszynie Wirtualnej platformy Azure, usługę HDInsight (Hadoop), analizę synapse i usługę Azure Machine Learning. 

W poniższych artykułach opisano sposób pozyskiwania danych do różnych środowisk docelowych, w których dane są przechowywane i przetwarzane.

* Do/Z [usługi Azure Blob Storage](move-azure-blob.md)
* Do [programu SQL Server na maszynie Wirtualnej platformy Azure](move-sql-server-virtual-machine.md)
* Do [bazy danych SQL platformy Azure](move-sql-azure.md)
* Do [tabel ula](move-hive-tables.md)
* Do [tabel podzielonych na partycje SQL](parallel-load-sql-partitioned-tables.md)
* Z [lokalnego programu SQL Server](move-sql-azure-adf.md)

Potrzeby techniczne i biznesowe, a także początkowa lokalizacja, format i rozmiar danych określą najlepszy plan pozyskiwania danych. Nie jest rzadkością, aby najlepszy plan miał kilka kroków. Ta sekwencja zadań może obejmować, na przykład, eksploracji danych, przetwarzania wstępnego, czyszczenia, pobierania próbek w dół i szkolenia modelu.  Usługa Azure Data Factory to zalecany zasób platformy Azure do organizowania przenoszenia i przekształcania danych.

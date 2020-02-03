---
title: Ładowanie danych do środowisk usługi Azure Storage — proces nauki danych zespołu
description: Dowiedz się więcej o sposobie pozyskiwania danych w różnych środowiskach docelowych, w których dane są przechowywane i przetwarzane.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720541"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Ładowanie danych w środowiskach magazynowania do celów analizy

Proces nauki danych zespołu wymaga pozyskania danych lub załadowania ich do najbardziej odpowiedniego sposobu na każdym etapie. Miejsca docelowe danych mogą obejmować Blob Storage Azure, bazy danych SQL Azure, SQL Server na maszynach wirtualnych platformy Azure, usłudze HDInsight (Hadoop), Synapse Analytics i Azure Machine Learning. 

Następujące artykuły zawierają instrukcje dotyczące pozyskiwania danych w różnych środowiskach docelowych, gdzie dane są przechowywane i przetwarzane.

* Do/z [usługi Azure Blob Storage](move-azure-blob.md)
* Aby [SQL Server na maszynie wirtualnej platformy Azure](move-sql-server-virtual-machine.md)
* Aby [Azure SQL Database](move-sql-azure.md)
* Do [tabel programu Hive](move-hive-tables.md)
* Na [partycjonowane tabele SQL](parallel-load-sql-partitioned-tables.md)
* Z [SQL Server lokalnych](move-sql-azure-adf.md)

Wymagania techniczne i biznesowe, a także początkowa lokalizacja, format i rozmiar danych określają najlepszy plan pozyskiwania danych. Najlepszym planem jest wykonanie kilku kroków. Ta sekwencja zadań może zawierać na przykład eksplorację danych, przetwarzanie wstępne, czyszczenia, próbkowania w dół i szkoleń modelowych.  Azure Data Factory to zalecany zasób platformy Azure służący do organizowania przenoszenia i przekształcania danych.

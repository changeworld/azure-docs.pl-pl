---
title: Ładowanie danych do środowisk usługi Azure Storage — proces nauki danych zespołu
description: Dowiedz się więcej o sposobie pozyskiwania danych w różnych środowiskach docelowych, w których dane są przechowywane i przetwarzane.
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
ms.openlocfilehash: 515decfafe46ad0c1b5b90743688abc26a975903
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053262"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Ładowanie danych w środowiskach magazynowania do celów analizy

Proces nauki danych zespołu wymaga pozyskania lub załadowania danych do różnych środowisk magazynu, które mają być przetwarzane lub analizowane w najbardziej odpowiednim przypadku na każdym etapie procesu. Miejsca docelowe danych często używane do przetwarzania obejmują Blob Storage Azure, bazy danych SQL Azure, SQL Server na maszynach wirtualnych platformy Azure, usłudze HDInsight (Hadoop) i Azure Machine Learning. 

W poniższych artykułach opisano sposób pozyskiwania danych w różnych środowiskach docelowych, w których dane są przechowywane i przetwarzane.

* Do/z [usługi Azure Blob Storage](move-azure-blob.md)
* Aby [SQL Server na maszynie wirtualnej platformy Azure](move-sql-server-virtual-machine.md)
* Do [usługi Azure SQL Database](move-sql-azure.md)
* Do [tabel programu Hive](move-hive-tables.md)
* Na [partycjonowane tabele SQL](parallel-load-sql-partitioned-tables.md)
* Z [SQL Server lokalnych](move-sql-azure-adf.md)

Wymagania techniczne i biznesowe, a także początkowa lokalizacja, format i rozmiar danych określają środowiska docelowe, do których dane muszą zostać pozyskane w celu osiągnięcia celów analizy. Nie jest to typowy scenariusz, aby wymagać przenoszenia danych między kilkoma środowiskami w celu osiągnięcia różnorodnych zadań wymaganych do skonstruowania modelu predykcyjnego. Ta sekwencja zadań może obejmować na przykład eksplorację danych, wstępne przetwarzanie, czyszczenie, próbkowanie w dół i uczenie modeli.

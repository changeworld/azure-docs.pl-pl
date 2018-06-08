---
title: Przykładowe dane w kontenerów obiektów blob platformy Azure, programu SQL Server i program Hive tabel | Dokumentacja firmy Microsoft
description: Jak eksplorować dane przechowywane w różnych enviromnents platformy Azure.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 80a9dfae-e3a6-4cfb-aecc-5701cfc7e39d
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 0efd754936b67611a747c6c5756de92443a937e4
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838003"
---
# <a name="heading"></a>Przykładowe dane w kontenerów obiektów blob platformy Azure, programu SQL Server i program Hive tabel
Ten dokument łącza do artykułów, które opisano, jak przykładowe dane, które są przechowywane w jednym z trzech różnych miejscach Azure:

* **Dane w kontenerze obiektów blob platformy Azure** jest próbkowany przez programowo ją pobrać i następnie pobierania próbek z przykładowy kod języka Python.
* **Dane programu SQL Server** jest próbkować za pomocą programy SQL i język programowania Python. 
* **Dane tabeli hive** jest próbkować za pomocą zapytań programu Hive.

Następujące **menu** linki do tematów opisujących sposób próbkowania danych z każdej z tych środowisk magazynu Azure. 

[!INCLUDE [cap-sample-data-selector](../../../includes/cap-sample-data-selector.md)]

To zadanie próbkowania jest krokiem w [zespołu danych nauki procesu (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

**Dlaczego przykładowe dane?**

Jeśli zestaw danych, które mają być analizowanie jest duży, zazwyczaj jest dobrym rozwiązaniem w dół przykładowych danych, aby zmniejszyć jego rozmiar mniejsze, ale reprezentatywny i łatwiejsze w zarządzaniu. To ułatwia zrozumienie danych, badanie i inżynieria funkcji. Swoją rolę w procesie Analytics Cortana jest umożliwienie szybkiego prototypy funkcji przetwarzania danych i modeli uczenia maszynowego.


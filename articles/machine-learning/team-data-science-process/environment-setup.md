---
title: Konfigurowanie środowisk nauki danych na platformie Azure | Dokumentacja firmy Microsoft
description: Ustawienia danych środowisk nauki na platformie Azure do użycia w procesie nauki danych zespołu.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 481cfa6a-7ea3-46ac-b0f9-2e3982c37153
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: deguhath
ms.openlocfilehash: 46dd8b84202e6f534456afa4c92a8decced895e8
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836991"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Konfigurowanie środowisk analizy danych do użytku w zespołowym przetwarzaniu danych dla celów naukowych
Proces nauki danych zespołu używa różnych środowiskach nauki danych do przechowywania, przetwarzania i analizy danych. Obejmują one magazynu obiektów Blob Azure, maszyny wirtualne platformy Azure, klastry usługi HDInsight (Hadoop) i obszarów roboczych usługi Azure Machine Learning kilku typów. Decyzja o poszczególnych środowisk do użycia zależy od typu i ilość danych, aby być modelowane i docelowej dla tych danych w chmurze. 

* Aby uzyskać wskazówki dotyczące pytań, na które należy wziąć pod uwagę podczas tworzenia tej decyzji, zobacz [planowanie Your Azure Learning danych nauki środowiska maszyny](plan-your-environment.md). 
* Dla katalogu niektóre scenariusze mogą wystąpić podczas wykonywania zaawansowane metody analizy, zobacz [scenariusze dotyczące procesu nauki danych zespołu](plan-sample-scenarios.md)

To menu łącza do tematów opisujących sposób konfigurowania różnych środowiskach nauki danych używanych przez proces nauki danych Team.

[!INCLUDE [data-science-environment-setup](../../../includes/cap-setup-environments.md)]

**Microsoft danych nauki maszyny wirtualnej (DSVM)** jest również dostępny jako obraz maszyny wirtualnej platformy Azure (VM). Ta maszyna wirtualna jest wstępnie zainstalowane i skonfigurowane z kilku popularnych narzędzi, które są często używane do analizy danych i uczenia maszynowego. DSVM jest dostępna w systemach Windows i Linux. Aby uzyskać więcej informacji, zobacz [wprowadzenie do opartej na chmurze danych nauki maszyny wirtualnej systemu Linux i Windows](../data-science-virtual-machine/overview.md).


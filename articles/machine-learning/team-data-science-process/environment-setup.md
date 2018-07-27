---
title: Konfigurowanie środowiska nauki o danych na platformie Azure | Dokumentacja firmy Microsoft
description: Ustawienia danych środowisk analizy na platformie Azure do użycia w procesie nauki o danych zespołu.
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
ms.openlocfilehash: 6c28a64830afeb19c6a9264888b296c3b99990d1
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2018
ms.locfileid: "39262569"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Konfigurowanie środowisk analizy danych do użytku w zespołowym przetwarzaniu danych dla celów naukowych
Proces analizy danych zespołu używa różnych środowiska nauki o danych do przechowywania, przetwarzania i analizy danych. Obejmują one kilka typów maszyn wirtualnych platformy Azure, klastry HDInsight (Hadoop) i obszary robocze usługi Azure Machine Learning, Azure Blob Storage. Decyzja o środowisko, które do użycia zależy od typu i ilość danych, aby modelować i docelowej dla tych danych w chmurze. 

* Aby uzyskać wskazówki dotyczące pytań, które należy wziąć pod uwagę podczas wprowadzania tej decyzji, zobacz [planu Your Azure maszyny danych nauki wykładowych](plan-your-environment.md). 
* Katalog Niektóre scenariusze mogą wystąpić podczas wykonywania zaawansowanych analiz, można zobaczyć [scenariusze dla zespołu danych dla celów naukowych](plan-sample-scenarios.md)

Tego łącza menu do tematów opisujących sposób konfigurowania różnych środowiska nauki o danych używany przez proces analizy danych zespołu.

[!INCLUDE [data-science-environment-setup](../../../includes/cap-setup-environments.md)]

**Microsoft Data Science maszyny wirtualnej znajdujący** jest również dostępny jako obraz maszyny wirtualnej (VM) platformy Azure. Ta maszyna wirtualna jest wstępnie zainstalowany i skonfigurowany za pomocą kilku popularnych narzędzi, które są często używane do analizy danych i uczenia maszynowego. Maszyny DSVM jest dostępna w systemach Windows i Linux. Aby uzyskać więcej informacji, zobacz [wprowadzenie do opartych na chmurze maszyny wirtualnej analizy danych dla systemów Linux i Windows](../data-science-virtual-machine/overview.md).

Dowiedz się, jak utworzyć:

- [Windows DSVM](../data-science-virtual-machine/provision-vm.md)
- [Ubuntu DSVM](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [CentOS nauki](../data-science-virtual-machine/linux-dsvm-intro.md)
- [Maszyna wirtualna do uczenia głębokiego](../data-science-virtual-machine/provision-deep-learning-dsvm.md)

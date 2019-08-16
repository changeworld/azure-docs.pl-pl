---
title: Konfigurowanie danych środowiska nauki o platformie Azure — zespołu danych dla celów naukowych
description: Ustawienia danych środowisk analizy na platformie Azure do użycia w procesie nauki o danych zespołu.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/29/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 028f289092a0183072ab0ba0be29f9d1f79781bc
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534322"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Konfigurowanie środowisk analizy danych do użytku w zespołowym przetwarzaniu danych dla celów naukowych
Proces analizy danych zespołu używa różnych środowiska nauki o danych do przechowywania, przetwarzania i analizy danych. Obejmują one kilka typów maszyn wirtualnych platformy Azure, klastry HDInsight (Hadoop) i obszary robocze usługi Azure Machine Learning, Azure Blob Storage. Decyzja o środowisko, które do użycia zależy od typu i ilość danych, aby modelować i docelowej dla tych danych w chmurze. 

* Aby uzyskać wskazówki dotyczące pytań, które należy wziąć pod uwagę podczas wprowadzania tej decyzji, zobacz [planu Your Azure maszyny danych nauki wykładowych](plan-your-environment.md). 
* Katalog Niektóre scenariusze mogą wystąpić podczas wykonywania zaawansowanych analiz, można zobaczyć [scenariusze dla zespołu danych dla celów naukowych](plan-sample-scenarios.md)

Następujące artykuły zawierają instrukcje dotyczące konfigurowania różnych środowiska nauki o danych używany przez proces analizy danych zespołu.

* [Konto usługi Azure storage](../../storage/common/storage-quickstart-create-account.md)
* [Klaster HDInsight (Hadoop)](customize-hadoop-cluster.md)
* [Usługa Azure Machine Learning Studio obszaru roboczego](../studio/create-workspace.md)

**Microsoft Data Science maszyny wirtualnej znajdujący** jest również dostępny jako obraz maszyny wirtualnej (VM) platformy Azure. Ta maszyna wirtualna jest wstępnie zainstalowany i skonfigurowany za pomocą kilku popularnych narzędzi, które są często używane do analizy danych i uczenia maszynowego. Maszyny DSVM jest dostępna w systemach Windows i Linux. Aby uzyskać więcej informacji, zobacz [wprowadzenie do opartych na chmurze maszyny wirtualnej analizy danych dla systemów Linux i Windows](../data-science-virtual-machine/overview.md).

Dowiedz się, jak utworzyć:

- [Windows DSVM](../data-science-virtual-machine/provision-vm.md)
- [Ubuntu DSVM](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [CentOS nauki](../data-science-virtual-machine/linux-dsvm-intro.md)

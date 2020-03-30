---
title: Konfigurowanie środowisk do nauki o danych na platformie Azure — proces nauki o danych zespołu
description: Skonfiguruj środowiska nauki o danych na platformie Azure do użycia w procesie nauki o danych zespołu.
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
ms.openlocfilehash: 6d1185e13397b3e466bd1e8d609a46f2f68bf390
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063937"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Konfigurowanie środowisk analizy danych do użytku w zespołowym przetwarzaniu danych dla celów naukowych
Proces nauki o danych zespołu używa różnych środowisk do nauki o danych do przechowywania, przetwarzania i analizy danych. Obejmują one usługę Azure Blob Storage, kilka typów maszyn wirtualnych platformy Azure, klastry usługi HDInsight (Hadoop) i obszary robocze usługi Azure Machine Learning. Decyzja o tym, które środowisko użyć zależy od typu i ilości danych do modelowania i docelowego miejsca docelowego dla tych danych w chmurze. 

* Aby uzyskać wskazówki dotyczące pytań, które należy wziąć pod uwagę podczas podejmowania tej decyzji, zobacz [Planowanie środowiska nauki o danych usługi Azure Machine Learning.](plan-your-environment.md) 
* Aby uzyskać katalog niektórych scenariuszy, które mogą wystąpić podczas wykonywania zaawansowanej analizy, zobacz [Scenariusze procesu nauki o danych zespołu](plan-sample-scenarios.md)

W poniższych artykułach opisano sposób konfigurowania różnych środowisk nauki o danych używanych przez proces nauki o danych zespołu.

* [Konto magazynu platformy Azure](../../storage/common/storage-account-create.md)
* [Klaster HDInsight (Hadoop)](customize-hadoop-cluster.md)
* [Obszar roboczy usługi Azure Machine Learning Studio (klasyczny)](../studio/create-workspace.md)

**Maszyna wirtualna nauki o danych firmy Microsoft (DSVM)** jest również dostępna jako obraz maszyny wirtualnej platformy Azure (VM). Ta maszyna wirtualna jest wstępnie zainstalowana i skonfigurowana za pomocą kilku popularnych narzędzi, które są powszechnie używane do analizy danych i uczenia maszynowego. DSVM jest dostępny zarówno w systemie Windows, jak i Linux. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do opartej na chmurze maszyny wirtualnej do nauki o danych dla systemów Linux i Windows](../data-science-virtual-machine/overview.md).

Dowiedz się, jak utworzyć:

- [Maszyna DSVM z systemem Windows](../data-science-virtual-machine/provision-vm.md)
- [Maszyna DSVM z systemem Ubuntu](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [Maszyna DSVM z systemem CentOS](../data-science-virtual-machine/linux-dsvm-intro.md)

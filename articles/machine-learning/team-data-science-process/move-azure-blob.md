---
title: Przenoszenie danych do i z magazynu obiektów Blob platformy Azure | Dokumentacja firmy Microsoft
description: Przenoszenie danych do oraz z usługi Azure Blob Storage
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: d6681e30-ab45-45ea-a9fb-ac8acefe544d
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 717fdd2053cae28234458e197f8211ef25cf7f9d
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394395"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Przenoszenie danych do i z usługi Azure Blob Storage
[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

<!-- just in case, adding this to separate these two include references -->

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Jakiej metody jest dla Ciebie najlepsza, zależy od danego scenariusza. [Scenariusze zaawansowanej analizy w usłudze Azure Machine Learning](plan-sample-scenarios.md) artykuł pomaga określić zasoby, których potrzebujesz różnych przepływy pracy do nauki o danych używane w procesie zaawansowanej analizy.

> [!NOTE]
> Pełne wprowadzenie do usługi Azure blob storage, zapoznaj się [podstawowe informacje o usłudze Azure Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) i [usługi Azure Blob Service](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

Alternatywnie, można użyć [usługi Azure Data Factory](https://azure.microsoft.com/services/data-factory/) do: 

* Tworzenie i zaplanować potok, który pobiera dane z usługi Azure blob storage 
* Przekaż go do opublikowanej usługi sieci web Azure Machine Learning 
* wyniki analizy predykcyjnej i 
* Przekaż wyniki do magazynu. 

Aby uzyskać więcej informacji, zobacz [tworzyć potoki predykcyjne przy użyciu usługi Azure Data Factory i Azure Machine Learning](../../data-factory/transform-data-using-machine-learning.md).

## <a name="prerequisites"></a>Wymagania wstępne
W tym dokumencie przyjęto założenie, że masz subskrypcję platformy Azure, konta magazynu i odpowiedniego klucza magazynu dla tego konta. Przed Trwa przekazywanie/pobieranie danych, musisz wiedzieć, usługa Azure storage konta nazwy i klucza konta usługi.

* Aby skonfigurować subskrypcję platformy Azure, zobacz [bezpłatnej miesięcznej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* Aby uzyskać instrukcje dotyczące tworzenia konta magazynu oraz w celu uzyskania konta i informacje o kluczu, zobacz [kontach magazynu Azure o](../../storage/common/storage-create-storage-account.md).


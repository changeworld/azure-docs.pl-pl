---
title: Przenoszenie danych do i z usługi Azure Blob storage | Dokumentacja firmy Microsoft
description: Przenoszenie danych do i z usługi Azure Blob storage
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: fa3fd5dfdd0fbdb8200b0c5c8df512caedbe735c
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441846"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Przenoszenie danych do i z usługi Azure Blob storage

Proces analizy danych zespołu wymaga, aby dane można pozyskane lub ładowany w różnych środowiskach innego magazynu należy przetworzyć lub analizowany w najlepszy sposób na każdym etapie procesu.
Następujące artykuły zawierają instrukcje dotyczące przenoszenia danych do i z usługi Azure Blob storage przy użyciu różnych technologii.

* [— Eksplorator usługi Azure Storage](move-data-to-azure-blob-using-azure-storage-explorer.md)
* [Narzędzie AzCopy](move-data-to-azure-blob-using-azcopy.md)
* [Python](move-data-to-azure-blob-using-python.md)
* [SSIS](move-data-to-azure-blob-using-ssis.md)

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


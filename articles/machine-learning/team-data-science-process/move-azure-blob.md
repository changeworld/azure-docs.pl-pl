---
title: Przenoszenie danych do i z usługi Azure Blob storage — zespołu danych dla celów naukowych
description: Przenoszenie danych do i z usługi Azure Blob Storage za pomocą Eksplorator usługi Azure Storage, AzCopy, Python i SSIS.
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
ms.openlocfilehash: fc58651bcb3b266b981fb953fd7341427d47fb2c
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717574"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Przenoszenie danych do i z usługi Azure Blob storage

Proces analizy danych zespołu wymaga, aby dane można pozyskane lub ładowany w różnych środowiskach innego magazynu należy przetworzyć lub analizowany w najlepszy sposób na każdym etapie procesu.

## <a name="different-technologies-for-moving-data"></a>Różne technologie przenoszenia danych

Następujące artykuły zawierają instrukcje dotyczące przenoszenia danych do i z usługi Azure Blob storage przy użyciu różnych technologii.

* [— Eksplorator usługi Azure Storage](move-data-to-azure-blob-using-azure-storage-explorer.md)
* [Narzędzie AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)
* [Python](move-data-to-azure-blob-using-python.md)
* [SSIS](move-data-to-azure-blob-using-ssis.md)

Jakiej metody jest dla Ciebie najlepsza, zależy od danego scenariusza. [Scenariusze zaawansowanej analizy w usłudze Azure Machine Learning](plan-sample-scenarios.md) artykuł pomaga określić zasoby, których potrzebujesz różnych przepływy pracy do nauki o danych używane w procesie zaawansowanej analizy.

> [!NOTE]
> Pełne wprowadzenie do usługi Azure blob storage, zapoznaj się [podstawowe informacje o usłudze Azure Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) i [usługi Azure Blob Service](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="using-azure-data-factory"></a>Używanie usługi Azure Data Factory

Alternatywnie, można użyć [usługi Azure Data Factory](https://azure.microsoft.com/services/data-factory/) do: 

* Tworzenie i zaplanować potok, który pobiera dane z usługi Azure blob storage 
* Przekaż go do opublikowanej usługi sieci web Azure Machine Learning 
* wyniki analizy predykcyjnej i 
* Przekaż wyniki do magazynu. 

Aby uzyskać więcej informacji, zobacz [tworzyć potoki predykcyjne przy użyciu usługi Azure Data Factory i Azure Machine Learning](../../data-factory/transform-data-using-machine-learning.md).

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że masz subskrypcję platformy Azure, konta magazynu i odpowiedniego klucza magazynu dla tego konta. Przed przekazaniem/pobraniem danych należy znać nazwę konta i klucz konta usługi Azure Storage.

* Aby skonfigurować subskrypcję platformy Azure, zobacz [bezpłatnej miesięcznej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* Instrukcje dotyczące tworzenia konta magazynu i uzyskiwania informacji o kontach i kluczach znajdują się w temacie [Informacje o kontach usługi Azure Storage](../../storage/common/storage-create-storage-account.md).


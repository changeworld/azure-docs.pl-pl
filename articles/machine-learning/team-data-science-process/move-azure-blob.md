---
title: Przenoszenie danych do i z magazynu obiektów Blob platformy Azure — proces nauki o danych zespołu
description: Przenoszenie danych do i z magazynu obiektów Blob platformy Azure przy użyciu Eksploratora usługi Azure Storage, AzCopy, Python i SSIS.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76717574"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Przenoszenie danych do i z magazynu obiektów Blob platformy Azure

Proces nauki o danych zespołu wymaga, aby dane były pojmowane lub ładowane do różnych środowisk magazynu, które mają być przetwarzane lub analizowane w najbardziej odpowiedni sposób na każdym etapie procesu.

## <a name="different-technologies-for-moving-data"></a>Różne technologie przenoszenia danych

W poniższych artykułach opisano sposób przenoszenia danych do i z magazynu obiektów Blob platformy Azure przy użyciu różnych technologii.

* [Eksplorator usługi Azure Storage](move-data-to-azure-blob-using-azure-storage-explorer.md)
* [AzCopy (Polski)](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)
* [Python](move-data-to-azure-blob-using-python.md)
* [SSIS](move-data-to-azure-blob-using-ssis.md)

Która metoda jest dla Ciebie najlepsza, zależy od scenariusza. Scenariusze [zaawansowanej analizy w usłudze Azure Machine Learning](plan-sample-scenarios.md) artykuł pomaga określić zasoby potrzebne do różnych przepływów pracy nauki o danych używanych w procesie zaawansowanej analizy.

> [!NOTE]
> Aby uzyskać pełne wprowadzenie do magazynu obiektów blob platformy Azure, zobacz [Podstawowe usługi Azure Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) i [usługi Azure Blob.](https://msdn.microsoft.com/library/azure/dd179376.aspx)
> 
> 

## <a name="using-azure-data-factory"></a>Używanie usługi Azure Data Factory

Alternatywnie można użyć [usługi Azure Data Factory,](https://azure.microsoft.com/services/data-factory/) aby: 

* tworzenie i planowanie potoku, który pobiera dane z magazynu obiektów blob platformy Azure, 
* przekazać go do opublikowanej usługi sieci web usługi Azure Machine Learning, 
* otrzymują wyniki analizy predykcyjnej, a także 
* przesłać wyniki do magazynu. 

Aby uzyskać więcej informacji, zobacz [Tworzenie potoków predykcyjnych przy użyciu usługi Azure Data Factory i usługi Azure Machine Learning.](../../data-factory/transform-data-using-machine-learning.md)

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że masz subskrypcję platformy Azure, konto magazynu i odpowiedni klucz magazynu dla tego konta. Przed przekazaniem/pobraniem danych musisz znać nazwę konta usługi Azure Storage i klucz konta.

* Aby skonfigurować subskrypcję platformy Azure, zobacz [Bezpłatna miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).
* Aby uzyskać instrukcje dotyczące tworzenia konta magazynu oraz uzyskiwania informacji o koncie i kluczach, zobacz [Informacje o kontach usługi Azure Storage](../../storage/common/storage-create-storage-account.md).


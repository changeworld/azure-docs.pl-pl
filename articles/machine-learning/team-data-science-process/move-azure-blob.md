---
title: Przenoszenie danych do i z magazynu obiektów Blob platformy Azure | Dokumentacja firmy Microsoft
description: Przenoszenie danych do oraz z usługi Azure Blob Storage
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: d6681e30-ab45-45ea-a9fb-ac8acefe544d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 424216226ca4b92f17cec78dadbb0643defa8d0c
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Przenoszenie danych do i z magazynu obiektów Blob platformy Azure
[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

<!-- just in case, adding this to separate these two include references -->

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Która metoda jest najlepsze dla Ciebie zależy od danego scenariusza. [Scenariusze zaawansowana analityka w usłudze Azure Machine Learning](plan-sample-scenarios.md) artykuł pomaga określić zasoby potrzebne dla różnych przepływów pracy nauki danych używany w procesie zaawansowana analityka.

> [!NOTE]
> Pełne wprowadzenie do magazynu obiektów blob platformy Azure, można znaleźć w temacie [podstawy obiektów Blob Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) i [usługi obiektów Blob Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

Alternatywnie, można użyć [fabryki danych Azure](https://azure.microsoft.com/services/data-factory/) do: 

* Tworzenie i planowanie potok, który pobiera dane z magazynu obiektów blob platformy Azure 
* Przekaż go do opublikowanych usługi sieci web uczenie maszynowe Azure 
* wyniki analizy predykcyjnej i 
* Przekaż wyniki do magazynu. 

Aby uzyskać więcej informacji, zobacz [tworzenie potoków predykcyjnej przy użyciu fabryki danych Azure i usługi Azure Machine Learning](../../data-factory/v1/data-factory-azure-ml-batch-execution-activity.md).

## <a name="prerequisites"></a>Wymagania wstępne
Tym dokumencie przyjęto założenie, że masz subskrypcję platformy Azure, konta magazynu i odpowiedniego klucza magazynu dla tego konta. Przed przekazaniem/pobieranie danych, musisz znać Azure klucz konta magazynu nazwy i konta.

* Aby skonfigurować subskrypcję platformy Azure, zobacz [bezpłatna miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).
* Aby uzyskać instrukcje dotyczące tworzenia konta magazynu oraz uzyskiwanie konta i informacje o kluczu, zobacz [kont magazynu Azure o](../../storage/common/storage-create-storage-account.md).


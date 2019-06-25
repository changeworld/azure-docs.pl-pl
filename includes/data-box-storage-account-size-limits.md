---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 0975df8ee9da4a239dbd72e468b967c88ab3feed
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67277392"
---
Poniżej przedstawiono limity rozmiaru danych, które są kopiowane do konta magazynu. Upewnij się, że dane, które zostaną przesłane odpowiada tych limitów. Aby uzyskać najbardziej aktualne informacje o tych limitach, przejdź do [obiekty docelowe skalowania magazynu obiektów blob platformy Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) i [usługi Azure Files skalowanie elementów docelowych](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Rozmiar danych skopiowane do konta magazynu platformy Azure                      | Limit domyślny          |
|---------------------------------------------------------------------|------------------------|
| Blokowe obiekty blob i stronicowych obiektów blob                                            | 2 PB dla Stanów Zjednoczonych i Europy.<br>500 TB dla wszystkich innych regionów, w tym Wielkiej Brytanii.  <br> Obejmuje to dane ze wszystkich źródeł, w tym urządzenia Data Box.|
| Azure Files                                                          | 5 TB na jedną akcję.<br> Wszystkie foldery w *StorageAccount_AzureFiles* należy wykonać ten limit.       |

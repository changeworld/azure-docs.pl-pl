---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 6a9c31eb46a457433834d5940b3f7e190ebe1476
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75467942"
---
Oto limity rozmiaru danych, które są kopiowane do konta magazynu. Upewnij się, że przesyłane dane są zgodne z tymi limitami. Aby uzyskać najbardziej aktualne informacje na temat tych limitów, zobacz [Cele skalowalności i wydajności dla przestrzeni magazynowej obiektów Blob](../articles/storage/blobs/scalability-targets.md) oraz [cele dotyczące skalowalności i wydajności usług Azure Files.](../articles/storage/files/storage-files-scale-targets.md)

| Rozmiar danych skopiowanych do konta magazynu platformy Azure                      | Limit domyślny          |
|---------------------------------------------------------------------|------------------------|
| Blokuj obiekt blob i obiekt blob strony                                            | 2 PB dla USA i Europy.<br>500 TB dla wszystkich innych regionów, w tym Wielkiej Brytanii.  <br> Obejmuje to dane ze wszystkich źródeł, w tym Data Box.|
| Azure Files                                                          | 5 TB na akcję.<br> Wszystkie foldery *w obszarze StorageAccount_AzureFiles* muszą być zgodne z tym limitem.       |

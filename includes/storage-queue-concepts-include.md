---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.custom: seo-python-october2019
ms.openlocfilehash: 92f638666d9ac832ee5e6a7d4dccf9a9e669f908
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72427971"
---
## <a name="what-is-queue-storage"></a>Co to jest magazyn kolejek?

Azure Queue Storage to usługa do przechowywania dużej liczby komunikatów, do której można uzyskać dostęp z dowolnego miejsca na świecie za pośrednictwem uwierzytelnionego połączenia za pomocą protokołu HTTP lub HTTPS. Pojedynczy komunikat z kolejki nie może przekraczać 64 KB, a kolejka może zawierać miliony komunikatów — maksymalnie liczbę nieprzekraczającą całkowitego limitu pojemności konta magazynu. Magazyn kolejek jest często używany do tworzenia zaległości prac do przetwarzania asynchronicznego.

## <a name="queue-service-concepts"></a>Koncepcje usługa kolejki

Usługa kolejki platformy Azure zawiera następujące składniki:

![Składniki usługi Azure usługa kolejki](./media/storage-queue-concepts-include/azure-queue-service-components.png)

* **Format adresu URL:** adresy URL kolejek mają następujący format:   
    http://`<storage account>`.queue.core.windows.net/`<queue>` 
  
    Następujący adres URL dotyczy kolejki w schemacie:  
  
    `http://myaccount.queue.core.windows.net/images-to-download`

* **Konto magazynu:** cały dostęp do usługi Azure Storage odbywa się przez konto magazynu. Aby uzyskać szczegółowe informacje na temat pojemności konta magazynu, zobacz temat [Cele dotyczące skalowalności i wydajności usługi Azure Storage](../articles/storage/common/storage-scalability-targets.md).
* **Kolejka:** kolejka zawiera zestaw komunikatów. Wszystkie komunikaty muszą być w kolejce. Pamiętaj, że nazwa kolejki może zawierać tylko małe litery. Informacje dotyczące nazewnictwa kolejek można znaleźć w temacie [Naming Queues and Metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx) (Nazewnictwo kolejek i metadanych).
* **Komunikat**: komunikat w dowolnym formacie, o maksymalnym rozmiarze 64 KB. Maksymalny czas pozostawania komunikatu w kolejce wynosi 7 dni.


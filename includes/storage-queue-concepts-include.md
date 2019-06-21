---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 8a596293a5c1572b30ea6101dad16328c8db2634
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183131"
---
## <a name="what-is-queue-storage"></a>Co to jest usługa Queue Storage?
Azure Queue Storage to usługa do przechowywania dużej liczby komunikatów, do której można uzyskać dostęp z dowolnego miejsca na świecie za pośrednictwem uwierzytelnionego połączenia za pomocą protokołu HTTP lub HTTPS. Pojedynczy komunikat z kolejki nie może przekraczać 64 KB, a kolejka może zawierać miliony komunikatów — maksymalnie liczbę nieprzekraczającą całkowitego limitu pojemności konta magazynu.

Najczęstsze zastosowania usługi Queue Storage obejmują:

* Tworzenie zaległości pracy do przetwarzania asynchronicznego
* Przekazywanie komunikatów z roli sieci Web platformy Azure do roli procesu roboczego platformy Azure

## <a name="queue-service-concepts"></a>Pojęcia dotyczące usługi kolejki
Usługa kolejki zawiera następujące składniki:

![Queue1](./media/storage-queue-concepts-include/queue1.png)

* **Format adresu URL:** Kolejki są adresy, przy użyciu następującego formatu adresu URL:   
    http://`<storage account>`.queue.core.windows.net/`<queue>` 
  
    Następujący adres URL dotyczy kolejki w schemacie:  
  
    `http://myaccount.queue.core.windows.net/images-to-download`

* **Konto magazynu:** Dostęp do usługi Azure Storage odbywa się za pośrednictwem konta magazynu. Aby uzyskać szczegółowe informacje na temat pojemności konta magazynu, zobacz temat [Cele dotyczące skalowalności i wydajności usługi Azure Storage](../articles/storage/common/storage-scalability-targets.md).
* **Kolejka:** Kolejka zawiera zestaw komunikatów. Wszystkie komunikaty muszą być w kolejce. Pamiętaj, że nazwa kolejki może zawierać tylko małe litery. Informacje dotyczące nazewnictwa kolejek można znaleźć w temacie [Naming Queues and Metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx) (Nazewnictwo kolejek i metadanych).
* **Komunikat:** Komunikat w dowolnym formacie, maksymalnie 64 KB. Maksymalny czas pozostawania komunikatu w kolejce wynosi 7 dni.


---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.custom: seo-python-october2019
ms.openlocfilehash: 23b93f507ef6abe19a0202b28afa31d28490b2b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80151341"
---
## <a name="what-is-queue-storage"></a>Co to jest magazyn kolejek?

Azure Queue Storage to usługa do przechowywania dużej liczby komunikatów, do której można uzyskać dostęp z dowolnego miejsca na świecie za pośrednictwem uwierzytelnionego połączenia za pomocą protokołu HTTP lub HTTPS. Pojedynczy komunikat z kolejki nie może przekraczać 64 KB, a kolejka może zawierać miliony komunikatów — maksymalnie liczbę nieprzekraczającą całkowitego limitu pojemności konta magazynu. Magazyn kolejki jest często używany do tworzenia zaległości pracy do przetwarzania asynchronicznie.

## <a name="queue-service-concepts"></a>Pojęcia dotyczące usług kolejek

Usługa Kolejka platformy Azure zawiera następujące składniki:

![Składniki usługi Kolejki platformy Azure](./media/storage-queue-concepts-include/azure-queue-service-components.png)

* **Format adresu URL:** Kolejki można adresować przy użyciu następującego formatu adresu URL: http://`<storage account>`.queue.core.windows.net/`<queue>`
  
    Następujący adres URL dotyczy kolejki w schemacie:  
  
    `http://myaccount.queue.core.windows.net/incoming-orders`

* **Konto magazynu:** cały dostęp do usługi Azure Storage odbywa się przez konto magazynu. Aby uzyskać więcej informacji na temat kont magazynu, zobacz [Omówienie konta magazynu](../articles/storage/common/storage-account-overview.md).
* **Kolejka:** kolejka zawiera zestaw komunikatów. Wszystkie komunikaty muszą być w kolejce. Pamiętaj, że nazwa kolejki może zawierać tylko małe litery. Informacje dotyczące nazewnictwa kolejek można znaleźć w temacie [Naming Queues and Metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx) (Nazewnictwo kolejek i metadanych).
* **Komunikat**: komunikat w dowolnym formacie, o maksymalnym rozmiarze 64 KB. Maksymalny czas pozostawania komunikatu w kolejce wynosi 7 dni. W przypadku wersji 2017-07-29 lub nowszej maksymalny czas wygaśnięcia może być dowolną liczbą dodatnią lub -1 wskazującą, że wiadomość nie wygaśnie. Jeśli ten parametr zostanie pominięty, domyślny czas do żywo wynosi siedem dni.


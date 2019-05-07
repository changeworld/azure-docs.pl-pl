---
title: Wprowadzenie do kolejek platformy Azure — usługa Azure Storage
description: Wprowadzenie do kolejek platformy Azure
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: overview
ms.date: 02/06/2019
ms.author: mhopkins
ms.reviewer: cbrooks
ms.subservice: queues
ms.openlocfilehash: 544ff9d2c624ef62bf8041afd818153c1c4bfcc8
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65142497"
---
# <a name="what-are-azure-queues"></a>Co to jest Azure Queues?

Azure Queue Storage to usługa do przechowywania dużej liczby komunikatów, do której można uzyskać dostęp z dowolnego miejsca na świecie za pośrednictwem uwierzytelnionego połączenia za pomocą protokołu HTTP lub HTTPS. Pojedynczy komunikat z kolejki nie może przekraczać 64 KB, a kolejka może zawierać miliony komunikatów — maksymalnie liczbę nieprzekraczającą całkowitego limitu pojemności konta magazynu.

## <a name="common-uses"></a>Najczęstsze zastosowania

Najczęstsze zastosowania usługi Queue Storage obejmują:

* Tworzenie zaległości pracy do przetwarzania asynchronicznego
* Przekazywanie komunikatów z roli sieci Web platformy Azure do roli procesu roboczego platformy Azure

## <a name="queue-service-concepts"></a>Pojęcia dotyczące usługi kolejki

Usługa kolejki zawiera następujące składniki:

![Pojęcia dotyczące kolejki](./media/storage-queues-introduction/queue1.png)

* **Format adresu URL:** Kolejki są adresy, przy użyciu następującego formatu adresu URL:   
    https://`<storage account>`.queue.core.windows.net/`<queue>` 
  
    Następujący adres URL dotyczy kolejki w schemacie:  
  
    `https://myaccount.queue.core.windows.net/images-to-download`

* **Konto magazynu:** Dostęp do usługi Azure Storage odbywa się za pośrednictwem konta magazynu. Aby uzyskać szczegółowe informacje na temat pojemności konta magazynu, zobacz temat [Cele dotyczące skalowalności i wydajności usługi Azure Storage](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

* **Kolejka:** Kolejka zawiera zestaw komunikatów. Wszystkie komunikaty muszą być w kolejce. Pamiętaj, że nazwa kolejki może zawierać tylko małe litery. Informacje dotyczące nazewnictwa kolejek można znaleźć w temacie [Naming Queues and Metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx) (Nazewnictwo kolejek i metadanych).

* **Komunikat:** Komunikat w dowolnym formacie, maksymalnie 64 KB. Maksymalny czas, który komunikat może pozostawać w kolejce wynosi siedem dni.

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie konta magazynu](../storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Rozpoczynanie pracy z kolejkami, przy użyciu platformy .NET](storage-dotnet-how-to-use-queues.md)

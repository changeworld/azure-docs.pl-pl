---
title: Wprowadzenie do kolejek platformy Azure — usługa Azure Storage
description: Wprowadzenie do kolejek platformy Azure
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: overview
ms.date: 06/07/2019
ms.author: mhopkins
ms.reviewer: cbrooks
ms.subservice: queues
ms.openlocfilehash: bc3045e3d3b6977555818fcdb3dcaf3246ebd200
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754810"
---
# <a name="what-are-azure-queues"></a>Co to jest Azure Queues?

Usługa Azure Queue storage jest usługą służącą do przechowywania dużej liczby komunikatów. Możesz uzyskać dostęp do wiadomości z dowolnego miejsca na świecie za pośrednictwem uwierzytelnionych połączeń HTTP lub HTTPS. Komunikatu w kolejce może być maksymalnie 64 KB. Kolejka może zawierać miliony komunikatów — maksymalnie nieprzekraczającą całkowitego limitu pojemności konta magazynu.

## <a name="common-uses"></a>Najczęstsze zastosowania

Najczęstsze zastosowania usługi Queue Storage obejmują:

* Tworzenie zaległości pracy do przetwarzania asynchronicznego
* Przekazywanie komunikatów z roli sieci Web platformy Azure do roli procesu roboczego platformy Azure

## <a name="queue-service-concepts"></a>Pojęcia dotyczące usługi kolejki

Usługa kolejki zawiera następujące składniki:

![Pojęcia dotyczące kolejki](./media/storage-queues-introduction/queue1.png)

* **Format adresu URL:** Kolejki są adresy, przy użyciu następującego formatu adresu URL:

    `https://<storage account>.queue.core.windows.net/<queue>`
  
    Następujący adres URL dotyczy kolejki w schemacie:  
  
    `https://myaccount.queue.core.windows.net/images-to-download`

* **Konto magazynu:** Dostęp do usługi Azure Storage odbywa się za pośrednictwem konta magazynu. Aby uzyskać szczegółowe informacje na temat pojemności konta magazynu, zobacz temat [Cele dotyczące skalowalności i wydajności usługi Azure Storage](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

* **Kolejka:** Kolejka zawiera zestaw komunikatów. Nazwa kolejki **musi** zawierać tylko małe litery. Informacje dotyczące nazewnictwa kolejek można znaleźć w temacie [Naming Queues and Metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx) (Nazewnictwo kolejek i metadanych).

* **Komunikat:** Komunikat w dowolnym formacie, maksymalnie 64 KB. Przed wersją 2017-07-29 dozwoloną czas wygaśnięcia wynosi siedem dni. W wersji 2017-07-29 lub nowszym, maksymalny czas wygaśnięcia może być dowolna liczba dodatnia, lub wartość -1, wskazujący, że komunikat nie wygasa. Jeśli ten parametr zostanie pominięty, domyślny czas wygaśnięcia wynosi siedem dni.

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie konta magazynu](../storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Rozpoczynanie pracy z kolejkami, przy użyciu platformy .NET](storage-dotnet-how-to-use-queues.md)

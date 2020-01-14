---
title: Wprowadzenie do kolejek platformy Azure — Azure Storage
description: Wprowadzenie do kolejek platformy Azure
author: mhopkins-msft
ms.author: mhopkins
ms.date: 06/07/2019
ms.service: storage
ms.subservice: queues
ms.topic: overview
ms.reviewer: cbrooks
ms.openlocfilehash: 0e8bac8344bec06b58a22b8c9162cd8bd22ee700
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750433"
---
# <a name="what-are-azure-queues"></a>Czym są kolejki platformy Azure?

Azure Queue Storage to usługa służąca do przechowywania dużej liczby komunikatów. Użytkownik uzyskuje dostęp do komunikatów z dowolnego miejsca na świecie za pośrednictwem uwierzytelnionych połączeń przy użyciu protokołu HTTP lub HTTPS. Komunikat w kolejce może mieć rozmiar do 64 KB. Kolejka może zawierać miliony komunikatów, do łącznego limitu pojemności konta magazynu. Kolejki są często używane do tworzenia zaległości prac do przetwarzania asynchronicznego.

## <a name="queue-service-concepts"></a>Koncepcje usługa kolejki

Usługa kolejki zawiera następujące składniki:

![Koncepcje kolejki](./media/storage-queues-introduction/queue1.png)

* **Format adresu URL:** adresy URL kolejek mają następujący format:

    `https://<storage account>.queue.core.windows.net/<queue>`
  
    Następujący adres URL dotyczy kolejki w schemacie:  
  
    `https://myaccount.queue.core.windows.net/images-to-download`

* **Konto magazynu:** Cały dostęp do usługi Azure Storage odbywa się za pomocą konta magazynu. Aby uzyskać informacje na temat pojemności konta magazynu, zobacz [cele skalowalności i wydajności dla kont magazynu w warstwie Standardowa](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

* **Kolejka:** kolejka zawiera zestaw komunikatów. Nazwa kolejki **musi** zawierać tylko małe litery. Informacje dotyczące nazewnictwa kolejek można znaleźć w temacie [Naming Queues and Metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx) (Nazewnictwo kolejek i metadanych).

* **Komunikat**: komunikat w dowolnym formacie, o maksymalnym rozmiarze 64 KB. Przed wersjami 2017-07-29 maksymalny dozwolony czas wygaśnięcia wynosi siedem dni. W wersji 2017-07-29 lub nowszej maksymalny czas wygaśnięcia może być dowolną liczbą dodatnią lub-1 oznacza, że komunikat nie wygasa. Jeśli ten parametr zostanie pominięty, domyślny czas wygaśnięcia wynosi siedem dni.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie konta magazynu](../storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Wprowadzenie do kolejek przy użyciu platformy .NET](storage-dotnet-how-to-use-queues.md)

---
title: Wprowadzenie do kolejek platformy Azure — usługa Azure Storage
description: Wprowadzenie do kolejek platformy Azure
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/18/2020
ms.service: storage
ms.subservice: queues
ms.topic: overview
ms.reviewer: cbrooks
ms.openlocfilehash: 4a2bea77578282d68d86bc1a8cea765aa2cbd555
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80060841"
---
# <a name="what-are-azure-queues"></a>Czym są kolejki platformy Azure?

Azure Queue Storage to usługa służąca do przechowywania dużej liczby komunikatów. Dostęp do wiadomości z dowolnego miejsca na świecie można uzyskać za pośrednictwem uwierzytelnionych połączeń przy użyciu protokołu HTTP lub HTTPS. Komunikat kolejki może mieć rozmiar do 64 KB. Kolejka może zawierać miliony wiadomości, do całkowitego limitu pojemności konta magazynu. Kolejki są często używane do tworzenia zaległości pracy do przetwarzania asynchronicznie.

## <a name="queue-service-concepts"></a>Pojęcia dotyczące usług kolejek

Usługa kolejki zawiera następujące składniki:

![Diagram przedstawiający relację między kontem magazynu, kolejkami i wiadomościami](./media/storage-queues-introduction/queue1.png)

* **Format adresu URL:** adresy URL kolejek mają następujący format:

    `https://<storage account>.queue.core.windows.net/<queue>`
  
    Następujący adres URL dotyczy kolejki w schemacie:  
  
    `https://myaccount.queue.core.windows.net/images-to-download`

* **Konto magazynu:** Cały dostęp do usługi Azure Storage odbywa się za pośrednictwem konta magazynu. Aby uzyskać informacje o pojemności konta magazynu, zobacz [Skalowalność i cele wydajności dla standardowych kont magazynu](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

* **Kolejka:** kolejka zawiera zestaw komunikatów. Nazwa kolejki **musi** być mała litera. Informacje dotyczące nazewnictwa kolejek można znaleźć w temacie [Naming Queues and Metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx) (Nazewnictwo kolejek i metadanych).

* **Komunikat**: komunikat w dowolnym formacie, o maksymalnym rozmiarze 64 KB. Przed wersją 2017-07-29 maksymalny dozwolony czas na żywo wynosi siedem dni. W przypadku wersji 2017-07-29 lub nowszej maksymalny czas wygaśnięcia może być dowolną liczbą dodatnią lub -1 wskazującą, że wiadomość nie wygaśnie. Jeśli ten parametr zostanie pominięty, domyślny czas do żywo wynosi siedem dni.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie konta magazynu](../storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Wprowadzenie do kolejek przy użyciu platformy .NET](storage-dotnet-how-to-use-queues.md)

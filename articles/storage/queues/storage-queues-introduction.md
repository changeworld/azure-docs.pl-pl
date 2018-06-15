---
title: Wprowadzenie do magazynu kolejek Azure | Dokumentacja firmy Microsoft
description: Wprowadzenie do magazynu kolejek Azure
services: storage
documentationcenter: ''
author: tamram
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: tamram
ms.openlocfilehash: 792e8c7efb2a627fbc1abde1389015949d5931e7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873828"
---
# <a name="introduction-to-queues"></a>Wprowadzenie do kolejek

Azure Queue Storage to usługa do przechowywania dużej liczby komunikatów, do której można uzyskać dostęp z dowolnego miejsca na świecie za pośrednictwem uwierzytelnionego połączenia za pomocą protokołu HTTP lub HTTPS. Pojedynczy komunikat z kolejki nie może przekraczać 64 KB, a kolejka może zawierać miliony komunikatów — maksymalnie liczbę nieprzekraczającą całkowitego limitu pojemności konta magazynu.

## <a name="common-uses"></a>Najczęstsze zastosowania

Najczęstsze zastosowania usługi Queue Storage obejmują:

* Tworzenie zaległości pracy do przetwarzania asynchronicznego
* Przekazywanie komunikatów z roli sieci Web platformy Azure do roli procesu roboczego platformy Azure

## <a name="queue-service-concepts"></a>Pojęcia dotyczące usługi kolejki

Usługa kolejki zawiera następujące składniki:

![Pojęcia dotyczące kolejki](./media/storage-queues-introduction/queue1.png)

* **Format adresu URL:** adresy URL kolejek mają następujący format:   
    http://`<storage account>`.queue.core.windows.net/`<queue>` 
  
    Następujący adres URL dotyczy kolejki w schemacie:  
  
    `http://myaccount.queue.core.windows.net/images-to-download`

* **Konto magazynu:** dostęp do usługi Azure Storage odbywa się za pośrednictwem konta magazynu. Aby uzyskać szczegółowe informacje na temat pojemności konta magazynu, zobacz temat [Cele dotyczące skalowalności i wydajności usługi Azure Storage](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

* **Kolejka:** kolejka zawiera zestaw komunikatów. Wszystkie komunikaty muszą być w kolejce. Pamiętaj, że nazwa kolejki może zawierać tylko małe litery. Informacje dotyczące nazewnictwa kolejek można znaleźć w temacie [Naming Queues and Metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx) (Nazewnictwo kolejek i metadanych).

* **Komunikat**: komunikat w dowolnym formacie, o maksymalnym rozmiarze 64 KB. Maksymalny czas, który komunikatu może pozostawać w kolejce wynosi siedem dni.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie konta magazynu](../storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Wprowadzenie do korzystania z kolejki przy użyciu platformy .NET](storage-dotnet-how-to-use-queues.md)

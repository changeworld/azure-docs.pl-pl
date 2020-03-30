---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 04/22/2019
ms.author: tarcher
ms.openlocfilehash: eb96027351cf244e9cd4404f702544411130db5e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67183921"
---
[Usługa Azure Service Bus](/azure/service-bus-messaging/service-bus-messaging-overview) jest brokerem [komunikatów integracji](https://azure.microsoft.com/product-categories/integration/) przedsiębiorstwa. Magistrala usług obsługuje dwa typy komunikacji: kolejki i tematy. 

Kolejki obsługują komunikację asynchronizacjową między aplikacjami. Aplikacja wysyła wiadomości do kolejki, która przechowuje wiadomości. Aplikacja odbierająca następnie łączy się i odczytuje wiadomości z kolejki.

Tematy obsługują wzorzec publikowania i subskrybowania, który umożliwia relację jeden do wielu między inicjatorem wiadomości a odbiornikami messager.
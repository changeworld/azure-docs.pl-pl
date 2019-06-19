---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 04/22/2019
ms.author: tarcher
ms.openlocfilehash: eb96027351cf244e9cd4404f702544411130db5e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183921"
---
[Usługa Azure Service Bus](/azure/service-bus-messaging/service-bus-messaging-overview) jest przedsiębiorstwem [integracji](https://azure.microsoft.com/product-categories/integration/) brokera komunikatów. Usługa Service bus obsługuje dwa rodzaje komunikacji: kolejki i tematy. 

Kolejki obsługują komunikacji asynchronicznej między aplikacjami. Aplikacja wysyła komunikaty do kolejki, która przechowuje komunikaty. Aplikacja odbierająca następnie łączy się i odczytuje komunikaty z kolejki.

Tematy obsługują Publikuj — Subskrybuj wzorzec, który umożliwia relacji jeden do wielu między inicjatorem wiadomości i receiver(s) przesyłania wiadomości.
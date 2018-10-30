---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8ae22ec7f75b9cd0d7958977dfd97169706c9389
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2018
ms.locfileid: "50226786"
---
Po utworzeniu odbiornika grupy dostępności, może być konieczne dostosowanie parametrów klastra RegisterAllProvidersIP i HostRecordTTL dla zasobu odbiornika. Te parametry można skrócić czas ponownego łączenia po przejściu w tryb failover, które mogą uniemożliwić przekroczeń limitu czasu połączenia. Aby uzyskać więcej informacji na temat tych parametrów, a także przykładowy kod, zobacz [Tworzenie lub Konfigurowanie odbiornika grupy dostępności](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover).


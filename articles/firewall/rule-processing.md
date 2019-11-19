---
title: Logika przetwarzania reguł usługi Azure Firewall
description: Zapora platformy Azure ma reguły NAT, reguły sieci i reguły aplikacji. Reguły są przetwarzane zgodnie z typem reguły.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2018
ms.author: victorh
ms.openlocfilehash: 0fc11221e0ff79d6e17b93282403792fc135c2a4
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74166781"
---
# <a name="azure-firewall-rule-processing-logic"></a>Logika przetwarzania reguł usługi Azure Firewall
Zapora platformy Azure ma reguły NAT, reguły sieci i reguły aplikacji. Reguły są przetwarzane zgodnie z typem reguły.


## <a name="network-rules-and-applications-rules"></a>Reguły sieci i zasady dotyczące aplikacji 
Reguły dotyczące sieci są stosowane najpierw, a następnie reguły aplikacji. Reguły są przerywane. Dlatego jeśli w regułach sieciowych zostanie znalezione dopasowanie, reguły aplikacji nie są przetwarzane.  Jeśli nie zostanie dopasowana reguła sieci i jeśli protokół pakietu to HTTP/HTTPS, pakiet zostanie oceniony przez reguły aplikacji. Jeśli nadal nie zostanie znalezione dopasowanie, pakiet jest oceniany względem kolekcji reguł infrastruktury. Jeśli wciąż nie zostanie znalezione dopasowanie, pakiet zostanie domyślnie odrzucony.

## <a name="nat-rules"></a>Reguły translatora adresów sieciowych
Połączenie przychodzące można włączyć przez skonfigurowanie translacji docelowego adresu sieciowego (DNAT) zgodnie z opisem w [samouczku: filtrowanie ruchu przychodzącego za pomocą usługi Azure firewall DNAT przy użyciu Azure Portal](tutorial-firewall-dnat.md). Reguły DNAT są stosowane jako pierwsze. Jeśli zostanie znalezione dopasowanie, niejawna odpowiadająca reguła sieci umożliwia dodanie przetłumaczonego ruchu. Aby przesłonić to zachowanie, jawnie dodaj kolekcję reguł sieci z regułami odmowy zgodnymi z przetłumaczonym ruchem. Dla tych połączeń nie są stosowane żadne reguły aplikacji.


## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak wdrożyć i skonfigurować zaporę platformy Azure](tutorial-firewall-deploy-portal.md).
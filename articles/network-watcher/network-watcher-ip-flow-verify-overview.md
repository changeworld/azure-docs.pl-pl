---
title: Wprowadzenie do przepływu IP Zweryfikuj obserwatora sieciowego Azure | Dokumentacja firmy Microsoft
description: Ta strona zawiera omówienie sieci IP obserwatora przepływu Sprawdź możliwości
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: d352fb2d-4b4f-4ac4-9c2e-1cfccf0e7e03
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 8a59047a586f3d7ad7c1f29b218396bd688caafd
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="introduction-to-ip-flow-verify-in-azure-network-watcher"></a>Wprowadzenie do przepływu IP Zweryfikuj obserwatora sieciowego Azure

Przepływ IP Sprawdź kontroli, jeśli pakiet jest dozwolony lub odrzucany do lub z maszyny wirtualnej. Zawiera kierunek, protokół, lokalny adres IP, zdalny adres IP, portu lokalnego i portu zdalnego. Jeśli pakiet jest zabroniony przez grupę zabezpieczeń, zwracana jest nazwa reguły, z którego pakiet. Podczas gdy można wybrać dowolny źródłowy lub docelowy adres IP, przepływ IP Sprawdź pomaga administratorom szybkie diagnozowanie problemów z łącznością z lub do Internetu i z lub do środowiska lokalnego.

Sprawdź przepływ IP jest przeznaczony dla interfejsu sieciowego maszyny wirtualnej. Przepływ ruchu jest następnie weryfikowany na podstawie ustawień skonfigurowanych do lub z tym interfejs sieciowy. Sprawdź przepływ IP jest przydatny do potwierdzenia, czy reguły do grupy zabezpieczeń sieci blokuje ruch przychodzący lub wychodzący z maszyny wirtualnej lub.

Sprawdź wystąpienia obserwatora sieciowego musi być utworzony we wszystkich regionach, które zamierzasz uruchomić przepływ IP. Obserwatora sieciowego jest usługą regionalnych i może być przeprowadzony tylko na zasobów w tym samym regionie. Wystąpienie używane nie ma wpływu na wyniki IP przepływu sprawdzić, jak trasy skojarzona z kartą Sieciową nadal jest zwracany.

![1][1]

## <a name="next-steps"></a>Kolejne kroki

Można znaleźć w artykule, aby dowiedzieć się, jeśli pakiet jest dozwolony lub niedozwolony dla określonej maszyny wirtualnej za pośrednictwem portalu. [Sprawdź, czy ruch jest dozwolony na maszynie Wirtualnej z przepływem Sprawdź IP przy użyciu portalu](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-ip-flow-verify-overview/figure1.png













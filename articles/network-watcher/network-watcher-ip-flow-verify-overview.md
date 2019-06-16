---
title: Wprowadzenie do przepływu dla adresu IP, sprawdź w usłudze Azure Network Watcher | Dokumentacja firmy Microsoft
description: Ta strona zawiera omówienie sieci IP obserwatora możliwość weryfikowania przepływu
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: d352fb2d-4b4f-4ac4-9c2e-1cfccf0e7e03
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2017
ms.author: kumud
ms.openlocfilehash: 5c34fd2b6d354f594ed153647c1bed700566fad6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64709595"
---
# <a name="introduction-to-ip-flow-verify-in-azure-network-watcher"></a>Wprowadzenie do przepływu dla adresu IP, sprawdź w usłudze Azure Network Watcher

Weryfikowanie przepływu protokołu IP kontroli, jeśli pakiet może wchodzić lub wychodzić do / z maszyny wirtualnej. Zawiera kierunek, protokół, lokalny adres IP zdalnego adresu IP, port lokalny i port zdalny. Jeśli pakiet zostanie odrzucona przez grupę zabezpieczeń, zwracana jest nazwa reguły, która odrzuciła pakiet. Chociaż można wybrać dowolny źródłowy lub docelowy adres IP, przepływu dla adresu IP, sprawdź pomaga administratorom szybkie diagnozowanie problemów z łącznością z lub z Internetem i z lub do środowiska lokalnego.

Weryfikowanie przepływu protokołu IP prezentuje reguły dla wszystkich sieciowych grup zabezpieczeń (NSG) stosowane do interfejsu sieciowego, takie jak podsieci lub maszyny wirtualnej karty sieciowej. Przepływ ruchu jest następnie weryfikowany na podstawie ustawień skonfigurowanych do lub z tym interfejsem sieciowym. Weryfikowanie przepływu protokołu IP jest przydatne w potwierdzenie, czy reguła w sieciowej grupie zabezpieczeń blokuje ruch przychodzący lub wychodzący ruch do lub z maszyny wirtualnej.

Sprawdź wystąpienie usługi Network Watcher musi zostać utworzone we wszystkich regionach, w których planuje się uruchamianie przepływu dla adresu IP. Usługa Network Watcher to regionalna usługa i może być został uruchomiony tylko względem zasobów, w tym samym regionie. Wystąpienie używane nie ma wpływu na wyniki adresu IP weryfikowanie przepływu, każda trasa skojarzone z kart Sieciowych lub podsieci nadal jest zwracana.

![1][1]

## <a name="next-steps"></a>Kolejne kroki

Można znaleźć w artykule, aby dowiedzieć się, jeśli pakiet jest dozwolony lub blokowany dla konkretnej maszyny wirtualnej za pośrednictwem portalu. [Sprawdź, czy jest dozwolony ruch na maszynie Wirtualnej za pomocą adresu IP przepływu Sprawdź przy użyciu portalu](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-ip-flow-verify-overview/figure1.png


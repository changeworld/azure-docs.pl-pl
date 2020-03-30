---
title: Wprowadzenie do weryfikacji przepływu ip w usłudze Azure Network Watcher | Dokumenty firmy Microsoft
description: Ta strona zawiera omówienie możliwości weryfikacji przepływu IP obserwatora sieci
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2017
ms.author: damendo
ms.openlocfilehash: 69aca5e0901a0da8aa98fe310ac220898bf650b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845010"
---
# <a name="introduction-to-ip-flow-verify-in-azure-network-watcher"></a>Wprowadzenie do weryfikacji przepływu ip w usłudze Azure Network Watcher

Weryfikacja przepływu IP sprawdza, czy pakiet jest dozwolony lub odrzucony do lub z maszyny wirtualnej. Informacje składają się z kierunku, protokołu, lokalnego adresu IP, zdalnego adresu IP, portu lokalnego i portu zdalnego. Jeśli pakiet zostanie odrzucony przez grupę zabezpieczeń, zwracana jest nazwa reguły, która odrzuciła pakiet. Podczas gdy można wybrać dowolny źródłowy lub docelowy adres IP, weryfikacja przepływu IP pomaga administratorom szybko diagnozować problemy z łącznością z Internetem lub z Internetu oraz ze środowiska lokalnego lub do środowiska lokalnego.

Weryfikacja przepływu IP analizuje reguły dla wszystkich grup zabezpieczeń sieciowych (NSG) stosowanych do interfejsu sieciowego, takich jak karta sieciowa podsieci lub maszyny wirtualnej. Przepływ ruchu jest następnie weryfikowany na podstawie skonfigurowanych ustawień do lub z tego interfejsu sieciowego. Weryfikacja przepływu IP jest przydatna do potwierdzenia, czy reguła w sieciowej grupie zabezpieczeń blokuje ruch przychodzący lub wychodzący do maszyny wirtualnej lub z maszyny wirtualnej.

Wystąpienie obserwatora sieci musi zostać utworzone we wszystkich regionach, które mają zostać uruchomione weryfikowanie przepływu IP. Network Watcher jest usługą regionalną i można ją ować tylko z zasobami w tym samym regionie. Użyte wystąpienie nie wpływa na wyniki weryfikacji przepływu IP, ponieważ każda trasa skojarzona z kartą sieciową lub podsiecią jest nadal zwracana.

![1][1]

## <a name="next-steps"></a>Następne kroki

Odwiedź poniższy artykuł, aby dowiedzieć się, czy pakiet jest dozwolony lub odrzucony dla określonej maszyny wirtualnej za pośrednictwem portalu. [Sprawdzanie, czy ruch na maszynie wirtualnej z weryfikacją przepływu IP za pomocą portalu](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-ip-flow-verify-overview/figure1.png


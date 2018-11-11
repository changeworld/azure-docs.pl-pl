---
title: Inspekcji usługi Azure Stack urządzenia fizycznego
description: Dowiedz się, jak zintegrować inspekcji dostępu do urządzenia fizycznego w usłudze Azure Stack
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 11/05/2018
ms.author: patricka
ms.reviewer: fiseraci
keywords: ''
ms.openlocfilehash: 83177363803c8f5c505c083614000dfa8f0df60d
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2018
ms.locfileid: "51036907"
---
# <a name="azure-stack-datacenter-integration---physical-device-auditing"></a>Integracja centrum danych usługi Azure Stack — inspekcja urządzenia fizycznego

Wszystkie urządzenia fizycznego w usłudze Azure Stack, takimi jak kontrolery zarządzania płytą główną (BMC) i przełączniki sieciowe emitowania dzienników inspekcji. Całe rozwiązanie inspekcji można zintegrować dzienniki inspekcji. Ponieważ urządzenia różnią się w wielu różnych dostawców sprzętu usługi Azure Stack OEM, skontaktuj się z dostawcą dokumentację inspekcji integracji.
Poniższe sekcje zawierają ogólne informacje dla urządzenia fizycznego inspekcji w usłudze Azure Stack.  

## <a name="physical-device-access-auditing"></a>Przeprowadzanie inspekcji dostępu do urządzenia fizycznego

Wszystkie fizyczne urządzenia w usłudze Azure Stack obsługuje TACACS lub serwera RADIUS. Obsługa obejmuje dostęp do kontrolera zarządzania płytą główną (BMC) i przełącznikami sieciowymi.

Rozwiązania platformy Azure Stack nie są dostarczane za pomocą usługi RADIUS lub TACACS wbudowane. Jednak te rozwiązania zostały zweryfikowane aby obsługiwały korzystanie z istniejących rozwiązań usługi RADIUS lub TACACS dostępnych na rynku.

Promień tylko MSCHAPv2 została zweryfikowana. Reprezentuje implementację najbardziej bezpieczne korzystanie z usługi RADIUS.
Zapoznaj się z dostawcą sprzętu producenta OEM w celu włączenia TACAS ani usługi RADIUS na urządzeniach dołączonych do rozwiązania usługi Azure Stack.

## <a name="syslog-forwarding-for-network-devices"></a>Przekazywania usługi SYSLOG dotyczących urządzeń sieciowych

Wszystkie fizyczne urządzenia sieciowe w usłudze Azure Stack obsługuje komunikaty dziennika systemowego. Rozwiązania platformy Azure Stack nie są dostarczane z serwera syslog. Jednak urządzenia zostały zweryfikowane umożliwiają wysyłanie wiadomości do istniejących rozwiązań usługi syslog na rynku.

Adres docelowy syslog jest opcjonalny parametr zbierane na potrzeby wdrożenia, ale mogą być również dodawane po wdrożeniu. Zapoznaj się z dostawcą sprzętu OEM, aby skonfigurować dziennik systemowy przekazywania na urządzeniach sieciowych.

## <a name="next-steps"></a>Kolejne kroki

[Obsługa zasad](azure-stack-servicing-policy.md)

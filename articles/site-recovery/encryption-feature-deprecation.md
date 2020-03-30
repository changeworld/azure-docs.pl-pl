---
title: Funkcja szyfrowania danych usługi Azure Site Recovery | Dokumenty firmy Microsoft
description: Szczegóły funkcji szyfrowania danych usługi Azure Site Recovery
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/15/2019
ms.author: rajanaki
ms.openlocfilehash: 5e74466891a5926d8ae8feb3c1c48348ecf3cfe6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74134998"
---
# <a name="deprecation-of-site-recovery-data-encryption-feature"></a>Wycofanie funkcji szyfrowania danych odzyskiwania witryny

W tym dokumencie opisano szczegóły wycofania i akcję korygującą, którą należy wykonać, jeśli używasz funkcji szyfrowania danych odzyskiwania witryny podczas konfigurowania odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V na platformie Azure. 

## <a name="deprecation-information"></a>Informacje o deprecjacji


Funkcja szyfrowania danych odzyskiwania witryny była dostępna dla klientów chroniących maszyny wirtualne funkcji Hyper-V, aby zapewnić, że replikowane dane są chronione przed zagrożeniami bezpieczeństwa. ta funkcja zostanie przestarzała do **30 grudnia 2019 r.** Jest on zastępowany przez bardziej zaawansowaną funkcję [Szyfrowanie w spoczynku,](https://azure.microsoft.com/blog/azure-site-recovery-encryption-at-rest/) która używa [szyfrowania usługi storage service](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) (SSE). Za pomocą usługi SSE dane są szyfrowane przed utrwalaniem do magazynu i odszyfrowywane podczas pobierania, a po przejściu w błąd na platformie Azure maszyny wirtualne będą uruchamiane z zaszyfrowanych kont magazynu, co pozwala na lepszy cel czasu odzyskiwania (RTO).

Należy pamiętać, że jeśli jesteś istniejącym klientem korzystającym z tej funkcji, otrzymałbyś komunikację ze szczegółami wycofania i krokami korygowania. 


## <a name="what-are-the-implications"></a>Jakie są tego konsekwencje?

Po **30 grudnia 2019**r. wszystkie maszyny wirtualne, które nadal korzystają z funkcji szyfrowania wycofane, nie będą mogły wykonywać pracy awaryjnej. 

## <a name="required-action"></a>Wymagana akcja
Aby kontynuować pomyślne operacje pracy awaryjnej i replikacji, wykonaj kroki opisane poniżej:

Wykonaj następujące kroki dla każdej maszyny Wirtualnej: 
1.  [Wyłącz replikację](https://docs.microsoft.com/azure/site-recovery/site-recovery-manage-registration-and-protection#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).
2.  [Utwórz nową zasadę replikacji](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-a-replication-policy).
3.  [Włącz replikację](https://docs.microsoft.com/azure/site-recovery/hyper-v-vmm-azure-tutorial#enable-replication) i wybierz konto magazynu z włączoną funkcją SSE.

Po zakończeniu replikacji początkowej do kont magazynu z włączoną SSE maszyny wirtualne będą używać szyfrowania w spoczynku z usługą Azure Site Recovery.


## <a name="next-steps"></a>Następne kroki
Zaplanuj wykonanie kroków korygowania i wykonaj je najwcześniej. W przypadku jakichkolwiek pytań dotyczących tego wycofania, skontaktuj się z pomocą techniczną firmy Microsoft. Aby dowiedzieć się więcej o scenariuszu funkcji Hyper-V na platformie Azure, zapoznaj się [z tym polem.](hyper-v-vmm-architecture.md)


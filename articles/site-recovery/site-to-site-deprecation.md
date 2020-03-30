---
title: Wycofanie odzyskiwania po awarii między lokacjami zarządzanymi przez klienta (z programem VMM) przy użyciu usługi Azure Site Recovery | Dokumenty firmy Microsoft
description: Szczegółowe informacje na temat nadchodzącego wycofania odzyskiwania po awarii między witrynami należącymi do klienta przy użyciu funkcji Hyper-V oraz między lokacjami zarządzanymi przez SCVMM na platformie Azure i opcjami alternatywnymi
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/25/2020
ms.author: rajanaki
ms.openlocfilehash: 208177d10e9002fafe2495710da229541a11a43e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77661674"
---
# <a name="deprecation-of-disaster-recovery-between-customer-managed-sites-with-vmm-using-azure-site-recovery"></a>Wycofanie odzyskiwania po awarii między lokacjami zarządzanymi przez klienta (z programem VMM) przy użyciu usługi Azure Site Recovery

W tym artykule opisano nadchodzący plan wycofania, odpowiednie implikacje i alternatywne opcje dostępne dla klientów w następującym scenariuszu:

Odzyskiwania danych między witrynami należącymi do klienta zarządzanymi przez Menedżera maszyn wirtualnych systemu System Center (SCVMM) przy użyciu funkcji Odzysk lokacji

> [!IMPORTANT]
> Klientom zaleca się, aby mogli podjąć kroki korygowania najwcześniej, aby uniknąć zakłóceń w ich środowisku. 

## <a name="what-changes-should-you-expect"></a>Jakich zmian należy się spodziewać?

- Od marca 2020 r. będziesz otrzymywać powiadomienia portalu Azure & komunikacji e-mail z nadchodzącym wycofaniem replikacji maszyn wirtualnych z funkcji lokacja-lokacja. Wycofanie planowane jest na marzec 2023 r.

- Jeśli masz istniejącą konfigurację, nie będzie miało wpływu na konfigurację.

- Gdy scenariusze są przestarzałe, chyba że klient postępuje zgodnie z alternatywnymi podejściami, istniejące replikacje mogą zostać zakłócone. Klienci nie będą mogli wyświetlać, zarządzać ani wykonywać żadnych operacji związanych z odzyskiwania po awarii za pośrednictwem usługi Azure Site Recovery w witrynie Azure portal.
 
## <a name="alternatives"></a>Alternatywy 

Poniżej znajdują się alternatywy, które klient może wybrać, aby upewnić się, że ich strategia odzyskiwania po awarii nie ma wpływu, gdy scenariusz jest przestarzały. 

- Opcja 1 (zalecane): wybierz, aby [rozpocząć korzystanie z platformy Azure jako docelowego odzyskiwania po awarii](hyper-v-vmm-azure-tutorial.md).


- Opcja 2: Wybierz, aby kontynuować replikację lokacji lokacji przy użyciu podstawowego [rozwiązania repliki funkcji Hyper-V,](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica)ale nie będzie można zarządzać konfiguracjami odzyskiwania po awarii przy użyciu usługi Azure Site Recovery w witrynie Azure portal. 


## <a name="remediation-steps"></a>Kroki korygowania

Jeśli zdecydujesz się przejść z opcją 1, wykonaj następujące czynności:

1. [Wyłącz ochronę wszystkich maszyn wirtualnych skojarzonych z maszynami wirtualnymi](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario). Użyj opcji **Wyłącz replikację i usuń** lub uruchom skrypty wymienione, aby upewnić się, że ustawienia replikacji w środowisku lokalnym są czyszczone. 

2. [Wyrejestruj wszystkie serwery programu VMM](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server) z konfiguracji replikacji lokacja-lokacja.

3. [Przygotowanie zasobów platformy Azure](tutorial-prepare-azure-for-hyperv.md) do włączania replikacji maszyn wirtualnych.
4. [Przygotowywanie lokalnych serwerów funkcji Hyper-V](hyper-v-prepare-on-premises-tutorial.md)
5. [Konfigurowanie replikacji maszyn wirtualnych w chmurze programu VMM](hyper-v-vmm-azure-tutorial.md)
6. Opcjonalnie, ale zalecane: [Uruchom wiertło odzyskiwania po awarii](tutorial-dr-drill-azure.md)

Jeśli zdecydujesz się przejść z opcją 2 przy użyciu repliki funkcji Hyper-V, wykonaj następujące czynności:

1. W **obszarze Elementy replikowane chronione** > **kliknij**prawym przyciskiem myszy komputer > **Wyłącz replikację**.
2. W **obszarze Wyłącz replikację**wybierz pozycję **Usuń**.

    Spowoduje to usunięcie zdrekonowanego elementu z usługi Azure Site Recovery (rozliczenia są zatrzymane). Konfiguracja replikacji na lokalnej maszynie wirtualnej **nie zostanie** wyczyszczona. 

## <a name="next-steps"></a>Następne kroki
Zaplanuj wycofanie i wybierz alternatywną opcję, która najlepiej nadaje się do twojej infrastruktury i firmy. W przypadku jakichkolwiek pytań dotyczących tej kwestii, skontaktuj się z pomocą techniczną firmy Microsoft


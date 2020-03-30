---
title: Przydziały i limity usług — usługa Azure Batch | Dokumenty firmy Microsoft
description: Dowiedz się więcej o domyślnych przydziałach, limitach i ograniczeniach usługi Azure Batch oraz o tym, jak żądać zwiększenia przydziału
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2019
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: c8a78acfa1f3e7332cee337ae1a82ee365fe356a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248231"
---
# <a name="batch-service-quotas-and-limits"></a>Limity przydziału i limity usługi Batch

Podobnie jak w przypadku innych usług platformy Azure, istnieją ograniczenia dotyczące niektórych zasobów skojarzonych z usługą Batch. Wiele z tych limitów to przydziały domyślne stosowane przez platformę Azure na poziomie subskrypcji lub konta. W tym artykule omówiono te wartości domyślne i sposób żądania zwiększenia przydziału.

Należy pamiętać o tych przydziałach podczas projektowania i skalowania obciążeń usługi Batch. Na przykład jeśli pula nie osiągnie docelowej liczby węzłów obliczeniowych, które zostały określone, może mieć osiągnięty limit przydziału podstawowego dla konta usługi Batch.

Można uruchomić wiele obciążeń usługi Batch na jednym koncie usługi Batch lub rozdzielić obciążenia pomiędzy konta tej usługi znajdujące się w jednej subskrypcji, ale różnych regionach świadczenia usługi Azure.

Jeśli planujesz uruchomić obciążenia produkcyjne w ułowieniu partii, może być konieczne zwiększenie jednego lub więcej przydziałów powyżej wartości domyślnej. Jeśli chcesz podnieść przydział, możesz bezpłatnie otworzyć [żądanie obsługi klienta](#increase-a-quota) online.

## <a name="resource-quotas"></a>Limity przydziałów zasobów

Przydział jest limitem kredytowym, a nie gwarancją zdolności produkcyjnych. Jeśli masz potrzeby w zakresie pojemności na dużą skalę, skontaktuj się z pomocą techniczną platformy Azure.

Należy również zauważyć, że przydziały nie są wartościami gwarantowanymi. Przydziały mogą się różnić w zależności od zmian z usługi wsadowej lub żądania użytkownika, aby zmienić wartość przydziału.

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

### <a name="cores-quotas-in-user-subscription-mode"></a>Przydziały rdzeni w trybie subskrypcji użytkownika

Jeśli utworzono konto usługi Batch z trybem alokacji puli ustawionym na **subskrypcję użytkownika,** przydziały są stosowane inaczej. W tym trybie maszyny wirtualne partii i inne zasoby są tworzone bezpośrednio w ramach subskrypcji podczas tworzenia puli. Przydziały rdzeni usługi Azure Batch nie mają zastosowania do konta utworzonego w tym trybie. Zamiast tego są stosowane przydziały w subskrypcji dla regionalnych rdzeni obliczeniowych i innych zasobów. Dowiedz się więcej o tych przydziałach w [limitach subskrypcji i usług platformy Azure, przydziałach i ograniczeniach](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="pool-size-limits"></a>Limity rozmiaru basenu

Limity rozmiaru puli są ustawiane przez usługę Batch. W przeciwieństwie do [przydziałów zasobów](#resource-quotas)nie można zmienić tych wartości. Tylko pule z komunikacją między węzłami i obrazami niestandardowymi mają ograniczenia inne niż przydział standardowy.

| **Zasobów** | **Limit maksymalny** |
| --- | --- |
| **Węzły obliczeniowe w [puli włączonej komunikacji między węzłami](batch-mpi.md)**  ||
| Tryb alokacji puli usług wsadowych | 100 |
| Tryb alokacji puli subskrypcji wsadowej | 80 |
| **Węzły obliczeniowe w [puli utworzonej za pomocą zasobu zarządzanego obrazu](batch-custom-images.md)**<sup>1</sup> ||
| Węzły dedykowane | 2000 |
| Węzły o niskim priorytecie | 1000 |

<sup>1</sup> Dla pul, które nie są włączone komunikacji między węzłami.

## <a name="other-limits"></a>Inne limity

Dodatkowe limity ustawione przez usługę Batch. W przeciwieństwie do [przydziałów zasobów](#resource-quotas)nie można zmienić tych wartości.

| **Zasobów** | **Limit maksymalny** |
| --- | --- |
| [Równoczesne zadania](batch-parallel-node-tasks.md) na węzeł obliczeniowy | 4 x liczba rdzeni węzła |
| [Aplikacje](batch-application-packages.md) na konto usługi Batch | 20 |
| Pakiety aplikacji dla aplikacji | 40 |
| Pakiety aplikacji na pulę | 10 |
| Maksymalny okres istnienia zadania | 180 dni<sup>1</sup> |
| [Instaluje](virtual-file-mount.md) na węzeł obliczeniowy | 10 |

<sup>1</sup> Maksymalny okres istnienia zadania, od momentu dodania do zadania do momentu jego zakończenia, wynosi 180 dni. Ukończone zadania utrzymują się przez siedem dni; dane dla zadań, które nie zostały ukończone w maksymalnym okresie istnienia, nie są dostępne.

## <a name="view-batch-quotas"></a>Wyświetlanie przydziałów wsadowych

Wyświetlanie przydziałów kont usługi Batch w [witrynie Azure portal][portal].

1. Wybierz **pozycję Konta usługi Batch** w portalu, a następnie wybierz interesujące Cię konto usługi Batch.
1. Wybierz **przydziały** w menu konta usługi Batch.
1. Wyświetlanie przydziałów aktualnie stosowanych do konta usługi Batch

    ![Przydziały kont wsadowych][account_quotas]

## <a name="increase-a-quota"></a>Zwiększanie przydziału

Wykonaj następujące kroki, aby zażądać zwiększenia przydziału dla konta usługi Batch lub subskrypcji za pomocą [witryny Azure portal][portal]. Typ zwiększenia przydziału zależy od trybu alokacji puli konta usługi Batch. Aby zażądać zwiększenia przydziału, należy dołączyć serię maszyn wirtualnych, dla której chcesz zwiększyć przydział. Po zastosowaniu zwiększenia przydziału jest on stosowany do wszystkich serii maszyn wirtualnych.

### <a name="increase-cores-quota-in-batch"></a>Zwiększanie przydziału rdzeni w uł.o. 

1. Wybierz kafelek **Pomoc + obsługa** na pulpicie nawigacyjnym portalu lub znak zapytania (**?**) w prawym górnym rogu portalu.
1. Wybierz **pozycję Nowe żądanie** > pomocy technicznej **— podstawy**.
1. W **podstawach**:
   
    a. **Usługa typu** > problemu**i limity subskrypcji (przydziały)**
   
    b. Wybierz subskrypcję.
   
    d. **Typ przydziału** > **Partia**
      
    Wybierz **pozycję Dalej**.
    
1. W obszarze **Szczegóły**:
      
    a. W **obszarze Podaj szczegóły**określ lokalizację, typ przydziału i konto usługi Batch.
    
    ![Zwiększenie przydziału partii][quota_increase]

    Typy przydziałów obejmują:

    * **Konto na partię**  
        Wartości specyficzne dla jednego konta usługi Batch, w tym dedykowane i o niskim priorytecie rdzenie oraz liczba zadań i pul.
        
    * **Na region**  
        Wartości, które mają zastosowanie do wszystkich kont usługi Batch w regionie i obejmują liczbę kont partii na region na subskrypcję.

    Przydział o niskim priorytecie jest pojedynczą wartością we wszystkich seriach maszyn wirtualnych. Jeśli potrzebujesz jednostek SKU o ograniczonym priorytecie, należy wybrać **rdzenie o niskim priorytecie** i uwzględnić rodziny maszyn wirtualnych, aby zażądać.

    b. Wybierz **ważność** zgodnie z [wpływem firmy][support_sev].

    Wybierz **pozycję Dalej**.

1. W **informacjach kontaktowych:**
   
    a. Wybierz **preferowaną metodę kontaktu**.
   
    b. Sprawdź i wprowadź wymagane dane kontaktowe.
   
    Wybierz **pozycję Utwórz,** aby przesłać żądanie pomocy technicznej.

Po przesłaniu żądania pomocy technicznej pomoc techniczna platformy Azure skontaktuje się z Tobą. Żądania przydziału mogą zostać wypełnione w ciągu kilku minut lub do dwóch dni roboczych.

## <a name="related-quotas-for-vm-pools"></a>Powiązane przydziały dla pul maszyn wirtualnych

Pule wsadowe w konfiguracji maszyny wirtualnej wdrożone w sieci wirtualnej platformy Azure automatycznie przydzielają dodatkowe zasoby sieci platformy Azure. Następujące zasoby są potrzebne dla każdego węzła puli 50 w sieci wirtualnej:

* Jedna [grupa zabezpieczeń sieci](../virtual-network/security-overview.md#network-security-groups)
* Jeden [publiczny adres IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* Jeden [moduł równoważenia obciążenia](../load-balancer/load-balancer-overview.md)

Te zasoby są przydzielane w subskrypcji, która zawiera sieć wirtualną dostarczoną podczas tworzenia puli usługi Batch. Te zasoby są ograniczone przez [limity zasobów](../azure-resource-manager/management/azure-subscription-service-limits.md) subskrypcji. Jeśli planujesz wdrożenia dużej puli w sieci wirtualnej, sprawdź przydziały subskrypcji dla tych zasobów. W razie potrzeby poproś o zwiększenie w witrynie Azure portal, wybierając **pomoc + pomoc techniczna**.


## <a name="related-topics"></a>Powiązane tematy
* [Tworzenie konta usługi Azure Batch w witrynie Azure Portal](batch-account-create-portal.md)
* [Omówienie funkcji usługi Azure Batch](batch-api-basics.md)
* [Azure subscription and service limits, quotas, and constraints (Limity subskrypcji i usług, limity przydziału oraz ograniczenia platformy Azure)](../azure-resource-manager/management/azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: https://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
[quota_increase]: ./media/batch-quota-limit/quota-increase.png

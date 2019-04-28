---
title: Usługi, limity przydziału i limity — Azure Batch | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o domyślne usługi Azure Batch przydziały, limity i ograniczenia i zwiększa sposobu wysyłania żądania limitu przydziału
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: e33d014bd2dddf0c7310727229f8137c9f181325
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776388"
---
# <a name="batch-service-quotas-and-limits"></a>Limity przydziału i limity usługi Batch

Zgodnie z innymi usługami platformy Azure istnieją limity określonych zasobów skojarzonych z usługą Batch. Wiele z tych limitów to domyślne limity przydziału stosowane przez platformę Azure na poziomie konta lub subskrypcji. W tym artykule omówiono te wartości domyślne i jak mogą żądać przydziału zwiększa.

Należy pamiętać tych limitów przydziału podczas projektowania i skalowania obciążeń usługi Batch. Na przykład jeśli pula nie dociera do docelowej liczby węzłów obliczeniowych, który określiłeś, może osiągnięto limit przydziału rdzeni dla konta usługi Batch.

Można uruchomić wiele obciążeń usługi Batch na jednym koncie usługi Batch lub rozdzielić obciążenia między konta tej usługi znajdujące się w jednej subskrypcji, ale w różnych regionach usługi Azure.

Jeśli planujesz uruchamianie obciążeń produkcyjnych w usłudze Batch, może być konieczne zwiększenie co najmniej jeden z limitów przydziału ponad wartość domyślną. Jeśli chcesz zwiększyć limit przydziału, możesz otworzyć online [żądanie obsługi klienta](#increase-a-quota) bez dodatkowych opłat.

> [!NOTE]
> Przydział to limit środków, nie gwarancję możliwości obliczeniowych. Jeśli masz zapotrzebowanie na pojemność, skontaktuj się z pomocą techniczną platformy Azure.
> 
> 

## <a name="resource-quotas"></a>Limity przydziałów zasobów
[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]


### <a name="cores-quotas-in-user-subscription-mode"></a>Limity przydziałów rdzeni w trybie subskrypcji użytkownika

Jeśli zostało utworzone konto usługi Batch z trybem alokacji puli równa **subskrypcja użytkownika**, przydziały są stosowane w różny sposób. W tym trybie maszyn wirtualnych partii i inne zasoby są tworzone bezpośrednio w Twojej subskrypcji po utworzeniu puli. Limity przydziałów rdzeni usługi Azure Batch nie dotyczą kont utworzonych w tym trybie. Zamiast tego należy przydziały w ramach subskrypcji dla regionalne rdzeni obliczeniowych i innych zasobów są stosowane. Dowiedz się więcej o tych limitów przydziału w [subskrypcji platformy Azure i limity, przydziały i ograniczenia](../azure-subscription-service-limits.md).

## <a name="pool-size-limits"></a>Limity rozmiaru puli

| **Zasób** | **Limit maksymalny** |
| --- | --- |
| **Węzłów obliczeniowych w [puli komunikacji między węzłami włączone](batch-mpi.md)**  ||
| Tryb alokacji puli usługa Batch | 100 |
| Tryb alokacji puli subskrypcja usługi Batch | 80 |
| **Węzłów obliczeniowych w [pula została utworzona przy użyciu niestandardowego obrazu maszyny Wirtualnej](batch-custom-images.md)**<sup>1</sup> ||
| Węzły dedykowane | 2000 |
| Węzły o niskim priorytecie | 1000 |

<sup>1</sup> dla puli, które nie są włączone komunikacji między węzłami.

## <a name="other-limits"></a>Inne limity

| **Zasób** | **Limit maksymalny** |
| --- | --- |
| [Współbieżnych zadań](batch-parallel-node-tasks.md) na węzeł obliczeniowy | 4 x liczba rdzeni węzła |
| [Aplikacje](batch-application-packages.md) na konto usługi Batch | 20 |
| Pakiety aplikacji na aplikację | 40 |
| Pakiety aplikacji na pulę | 10 |
| Okres istnienia zadania maksymalna | 180 dni<sup>1</sup> |

<sup>1</sup> maksymalny okres istnienia zadania, od momentu dodania go do zadania do jego ukończenia wynosi 180 dni. Ukończone zadania podrzędne są utrwalone przez 7 dni. Dane dla zadań podrzędnych nieukończonych w ciągu maksymalnego okresu istnienia nie są dostępne.

## <a name="view-batch-quotas"></a>Wyświetl przydziały usługi Batch

Wyświetl limity przydziału konta usługi Batch w [witryny Azure portal][portal].

1. Wybierz **konta usługi Batch** w portalu, a następnie wybierz konto usługi Batch interesuje Cię.
1. Wybierz **przydziały** menu konta usługi Batch.
1. Wyświetl limity przydziału, obecnie stosowany do konta usługi Batch

    ![Limity przydziału konta usługi Batch][account_quotas]

## <a name="increase-a-quota"></a>Zwiększ limit przydziału

Wykonaj następujące kroki, aby zażądać przydziału zwiększyć dla konta usługi Batch lub subskrypcją za pomocą [witryny Azure portal][portal]. Typ zwiększenia limitu przydziału, zależy od trybu alokacji puli konta usługi Batch. Aby zażądać zwiększenia limitu przydziału, musi zawierać serię maszyn wirtualnych, które chcesz zwiększyć limit przydziału. Po zastosowaniu zwiększenia limitu przydziału jest stosowana do wszystkich maszyn wirtualnych serii.

### <a name="increase-a-batch-cores-quota"></a>Zwiększ limit przydziału rdzeni usługi Batch 

1. Wybierz **Pomoc i obsługa techniczna** kafelków na pulpicie nawigacyjnym portalu lub znak zapytania (**?**) w prawym górnym rogu portalu.
1. Wybierz **nowe żądanie obsługi** > **podstawy**.
1. W **podstawy**:
   
    a. **Typ problemu** > **limitu przydziału**
   
    b. Wybierz subskrypcję.
   
    c. **Typ limitu przydziału** > **usługi Batch**
   
    d. **Plan pomocy technicznej** > **Obsługa limitu przydziału — włączone**
   
    Kliknij przycisk **Dalej**.
1. W **Problem**:
   
    a. Wybierz **ważność** zgodnie z opisem w swojej [wpływ na działalność][support_sev].
   
    b. W **szczegóły**, określ każdy przydział, które chcesz zmienić, nazwa konta usługi Batch i nowy limit.
   
    Kliknij przycisk **Dalej**.
1. W **informacje kontaktowe**:
   
    a. Wybierz **preferowana metoda kontaktu**.
   
    b. Sprawdź, a następnie wprowadź wymagane szczegóły dotyczące kontaktu.
   
    Kliknij przycisk **Utwórz**, aby przesłać żądanie pomocy technicznej.

Po przesłaniu żądania pomocy technicznej, pomocy technicznej platformy Azure skontaktuje. Należy pamiętać, że ukończenie żądania może potrwać do 2 dni roboczych.

## <a name="related-quotas-for-vm-pools"></a>Powiązane przydziały dla pul maszyn wirtualnych

Pule usługi Batch w konfiguracji maszyny wirtualnej wdrożony w sieci wirtualnej platformy Azure automatycznie przydzielić dodatkowe zasoby sieciowe platformy Azure. Dla poszczególnych węzłów 50 puli w sieci wirtualnej, potrzebne są następujące zasoby:

* 1 [sieciowej grupy zabezpieczeń](../virtual-network/security-overview.md#network-security-groups)
* 1 [publiczny adres IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* 1 [moduł równoważenia obciążenia](../load-balancer/load-balancer-overview.md)

Te zasoby są przydzielane w subskrypcji, która zawiera sieć wirtualną, podane podczas tworzenia puli usługi Batch. Te zasoby są ograniczone przez [limity zasobów](../azure-subscription-service-limits.md) subskrypcji. Jeśli planujesz dużą pulę wdrożeń w sieci wirtualnej, sprawdź przydziały subskrypcji dla tych zasobów. Jeśli to konieczne, należy zażądać zwiększenia w witrynie Azure portal, wybierając **Pomoc i obsługa techniczna**.


## <a name="related-topics"></a>Powiązane tematy
* [Tworzenie konta usługi Azure Batch przy użyciu witryny Azure portal](batch-account-create-portal.md)
* [Omówienie funkcji usługi Azure Batch](batch-api-basics.md)
* [Azure subscription and service limits, quotas, and constraints (Limity subskrypcji i usług, limity przydziału oraz ograniczenia platformy Azure)](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: https://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png

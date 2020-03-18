---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 03/04/2020
ms.author: labrenne
ms.custom: include file
ms.openlocfilehash: e9460108499ca76d1b149b61cebe3d3081bf6544
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79086252"
---
### <a name="general-requirements"></a>Wymagania ogólne

* Sieć wirtualna musi znajdować się w tej samej subskrypcji i w tym samym regionie co konto usługi Batch użyte do utworzenia puli.

* Pula używająca sieci wirtualnej może mieć maksymalnie 4096 węzłów.

* Podsieć określona dla puli musi mieć wystarczającą liczbę nieprzypisanych adresów IP do obsługi maszyn wirtualnych przeznaczony dla puli, czyli sumę właściwości puli `targetDedicatedNodes` i `targetLowPriorityNodes`. Jeśli podsieć nie ma wystarczającej liczby nieprzypisanych adresów IP, pula częściowo przydzieli węzły obliczeniowe, a następnie wystąpi błąd dotyczący zmiany rozmiaru.

* Punkt końcowy usługi Azure Storage musi być rozpoznawany przez dowolne niestandardowe serwery DNS, które obsługują sieć wirtualną. W szczególności rozpoznawalne powinny być adresy URL formularzy `<account>.table.core.windows.net`, `<account>.queue.core.windows.net` i `<account>.blob.core.windows.net`.

Dodatkowe wymagania dotyczące sieci wirtualnej różnią się w zależności od tego, czy pula usługi Batch znajduje się w konfiguracji maszyny wirtualnej, czy w konfiguracji usługi Cloud Services. W przypadku nowych wdrożeń puli w sieci wirtualnej zalecana jest konfiguracja maszyny wirtualnej.

### <a name="pools-in-the-virtual-machine-configuration"></a>Pule w konfiguracji maszyny wirtualnej

**Obsługiwane sieci wirtualne** — tylko sieci wirtualne oparte na usłudze Azure Resource Manager

**Identyfikator podsieci** — w przypadku określenia podsieci za pomocą interfejsów API usługi Batch użyj *identyfikatora zasobu* podsieci. Identyfikator podsieci ma postać:

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.Network/virtualNetworks/{network}/subnets/{subnet}
  ```

**Uprawnienia** — sprawdź, czy zasady zabezpieczeń lub blokady dla subskrypcji lub grupy zasobów sieci wirtualnej ograniczają uprawnienia użytkownika do zarządzania siecią wirtualną.

**Dodatkowe zasoby sieciowe** — usługa Batch automatycznie przydziela dodatkowe zasoby sieciowe w grupie zasobów zawierającej sieć wirtualną.

> [!IMPORTANT]
>Dla każdych 50 dedykowanych węzłów (lub 20 węzłów o niskim priorytecie) usługa Batch przydziela: jedną sieciową grupę zabezpieczeń (NSG), jeden publiczny adres IP i jeden moduł równoważenia obciążenia. Te zasoby są ograniczone przez [limity zasobów](../articles/azure-resource-manager/management/azure-subscription-service-limits.md) subskrypcji. W przypadku dużych pul może być konieczne zażądanie zwiększenia limitu przydziału dla co najmniej jednego z tych zasobów.

#### <a name="network-security-groups-batch-default"></a>Sieciowe grupy zabezpieczeń: Wartość domyślna usługi Batch

Podsieć musi zezwalać na komunikację przychodzącą z usługi Batch, co umożliwia planowanie zadań w węzłach obliczeniowych, i na komunikację wychodzącą na potrzeby komunikacji z usługą Azure Storage lub innymi zasobami zgodnie z potrzebami obciążenia. W przypadku pul w konfiguracji maszyny wirtualnej usługa Batch dodaje sieciowe grupy zabezpieczeń na poziomie interfejsów sieciowych (NIC) dołączonych do węzłów obliczeniowych. Te sieciowe grupy zabezpieczeń są konfigurowane przy użyciu następujących dodatkowych reguł:

* Ruch przychodzący protokołu TCP na porty 29876 i 29877 z adresów IP usługi Batch, które odpowiadają tagowi usługi `BatchNodeManagement`.
* Ruch przychodzący protokołu TCP na port 22 (węzły z systemem Linux) lub port 3389 (węzły z systemem Windows), aby zezwolić na dostęp zdalny. W przypadku niektórych typów zadań z wieloma wystąpieniami w systemie Linux (na przykład MPI) należy również zezwolić na ruch z portu 22 SSH dla adresów IP w podsieci zawierającej węzły obliczeniowe usługi Batch. Można ją blokować według reguł sieciowych grup zabezpieczeń na poziomie podsieci (patrz poniżej).
* Ruch wychodzący na dowolny port do sieci wirtualnej. Można ją zmieniać według reguł sieciowych grup zabezpieczeń na poziomie podsieci (patrz poniżej).
* Ruch wychodzący na dowolny port do Internetu. Można ją zmieniać według reguł sieciowych grup zabezpieczeń na poziomie podsieci (patrz poniżej).

> [!IMPORTANT]
> Zachowaj ostrożność przy modyfikowaniu lub dodawaniu reguły ruchu przychodzącego lub wychodzącego w sieciowych grupach zabezpieczeń skonfigurowanych za pomocą usługi Batch. Jeśli komunikacja z węzłami obliczeniowymi w określonej podsieci zostanie odrzucona przez sieciową grupę zabezpieczeń, usługa Batch ustawi stan węzłów obliczeniowych na **nienadające się do użytku**. Ponadto nie należy stosować blokad zasobów do żadnych zasobów utworzonych w usłudze Batch. W przeciwnym razie może to uniemożliwić oczyszczenie zasobów w wyniku akcji inicjowanych przez użytkownika, takich jak usuwanie puli.

#### <a name="network-security-groups-specifying-subnet-level-rules"></a>Sieciowe grupy zabezpieczeń: Określanie reguł na poziomie podsieci

Nie trzeba określać sieciowych grup zabezpieczeń na poziomie podsieci sieci wirtualnej, ponieważ usługa Batch konfiguruje własne sieciowe grupy zabezpieczeń (patrz powyżej). Jeśli masz sieciową grupę zabezpieczeń skojarzoną z podsiecią, w której wdrożone są węzły obliczeniowe usługi Batch, lub chcesz zastosować niestandardowe reguły sieciowej grupy zabezpieczeń w celu zastąpienia zastosowanych ustawień domyślnych, należy skonfigurować tę sieciową grupę zabezpieczeń przy użyciu co najmniej reguł zabezpieczeń dla ruchu przychodzącego i wychodzącego, jak pokazano w poniższych tabelach.

Skonfiguruj ruch przychodzący na port 3389 (Windows) lub 22 (Linux) tylko wtedy, gdy musisz zezwolić na zdalny dostęp do węzłów obliczeniowych ze źródeł zewnętrznych. Może być konieczne włączenie reguł portu 22 w systemie Linux, jeśli wymagana jest obsługa zadań z wieloma wystąpieniami z pewnymi środowiskami uruchomieniowymi MPI. Zezwalanie na ruch na tych portach nie jest wymagane do korzystania z węzłów obliczeniowych puli.

**Reguły zabezpieczeń dla ruchu przychodzącego**

| Źródłowe adresy IP | Tag usługi źródłowej | Porty źródłowe | Element docelowy | Porty docelowe | Protokół | Akcja |
| --- | --- | --- | --- | --- | --- | --- |
| Nie dotyczy | `BatchNodeManagement` [Tag usługi](../articles/virtual-network/security-overview.md#service-tags) (w przypadku używania wariantu regionalnego w tym samym regionie co konto usługi Batch) | * | Dowolne | 29876-29877 | TCP | Zezwalaj |
| Źródłowe adresy IP użytkowników umożliwiające zdalne uzyskiwanie dostępu do węzłów obliczeniowych i/lub podsieci węzłów obliczeniowych dla zadań z wieloma wystąpieniami systemu Linux, jeśli jest to wymagane. | Nie dotyczy | * | Dowolne | 3389 (Windows), 22 (Linux) | TCP | Zezwalaj |

**Reguły zabezpieczeń dla ruchu wychodzącego**

| Element źródłowy | Porty źródłowe | Element docelowy | Docelowy tag usługi | Porty docelowe | Protokół | Akcja |
| --- | --- | --- | --- | --- | --- | --- |
| Dowolne | * | [Tag usługi](../articles/virtual-network/security-overview.md#service-tags) | `Storage` (w przypadku używania wariantu regionalnego w tym samym regionie co konto usługi Batch) | 443 | TCP | Zezwalaj |

### <a name="pools-in-the-cloud-services-configuration"></a>Pule w konfiguracji usługi Cloud Services

**Obsługiwane sieci wirtualne** — tylko klasyczne sieci wirtualne

**Identyfikator podsieci** — w przypadku określenia podsieci za pomocą interfejsów API usługi Batch użyj *identyfikatora zasobu* podsieci. Identyfikator podsieci ma postać:

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicNetwork /virtualNetworks/{network}/subnets/{subnet}
  ```

**Uprawnienia** — jednostka usługi `Microsoft Azure Batch` musi mieć rolę `Classic Virtual Machine Contributor` w ramach kontroli dostępu opartej na rolach (RBAC) dla określonej sieci wirtualnej.

#### <a name="network-security-groups"></a>Grupy zabezpieczeń sieci

Podsieć musi zezwalać na komunikację przychodzącą z usługi Batch, co umożliwia planowanie zadań w węzłach obliczeniowych, i na komunikację wychodzącą na potrzeby komunikacji z usługą Azure Storage lub innymi zasobami.

Nie trzeba określać sieciowej grupy zabezpieczeń, ponieważ usługa Batch konfiguruje komunikację przychodzącą tylko z adresów IP usługi Batch do węzłów puli. Jednak jeśli określona sieć wirtualna ma skojarzone sieciowe grupy zabezpieczeń i/lub zaporę, skonfiguruj reguły zabezpieczeń ruchu przychodzącego i wychodzącego tak, jak pokazano w poniższych tabelach. Jeśli komunikacja z węzłami obliczeniowymi w określonej podsieci zostanie odrzucona przez sieciową grupę zabezpieczeń, usługa Batch ustawi stan węzłów obliczeniowych na **nienadające się do użytku**.

Skonfiguruj ruch przychodzący na port 3389 (Windows), gdy musisz zezwolić na dostęp RDP do węzłów puli. Węzły puli nie muszą być użyteczne.

**Reguły zabezpieczeń dla ruchu przychodzącego**

| Źródłowe adresy IP | Porty źródłowe | Element docelowy | Porty docelowe | Protokół | Akcja |
| --- | --- | --- | --- | --- | --- |
Dowolne <br /><br />Mimo że w praktyce wymaga to zezwolenia na cały ruch, usługa Batch stosuje regułę listy ACL filtrującą wszystkie adresy IP nienależące do usługi Batch na poziomie każdego węzła. | * | Dowolne | 10100, 20100, 30100 | TCP | Zezwalaj |
| Opcjonalne, aby umożliwić dostęp RDP do węzłów obliczeniowych. | * | Dowolne | 3389 | TCP | Zezwalaj |

**Reguły zabezpieczeń dla ruchu wychodzącego**

| Element źródłowy | Porty źródłowe | Element docelowy | Porty docelowe | Protokół | Akcja |
| --- | --- | --- | --- | --- | --- |
| Dowolne | * | Dowolne | 443  | Dowolne | Zezwalaj |

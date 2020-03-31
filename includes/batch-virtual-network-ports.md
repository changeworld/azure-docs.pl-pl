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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
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
>Dla każdego 50 dedykowanych węzłów (lub każdego 20 węzłów o niskim priorytecie) usługa Batch przydziela: jedną sieciową grupę zabezpieczeń (NSG), jeden publiczny adres IP i jeden moduł równoważenia obciążenia. Te zasoby są ograniczone przez [limity zasobów](../articles/azure-resource-manager/management/azure-subscription-service-limits.md) subskrypcji. W przypadku dużych pul może być konieczne żądanie zwiększenia przydziału dla co najmniej jednego z tych zasobów.

#### <a name="network-security-groups-batch-default"></a>Sieciowe grupy zabezpieczeń: domyślna partia

Podsieć musi zezwalać na komunikację przychodzącą z usługi Batch, aby móc planować zadania w węzłach obliczeniowych, a komunikacja wychodząca do komunikowania się z usługą Azure Storage lub innymi zasobami zgodnie z potrzebami obciążenia. W przypadku pul w konfiguracji maszyny wirtualnej usługa Batch dodaje sieciowe grupy zabezpieczeń na poziomie interfejsów sieciowych (KART SIECIOWYCH) dołączonych do węzłów obliczeniowych. Te sieciowe sieciowe sieciowe są skonfigurowane z następującymi dodatkowymi regułami:

* Przychodzący ruch TCP na portach 29876 i 29877 `BatchNodeManagement` z adresów IP usługi wsadowej, które odpowiadają tagowi usługi.
* Ruch przychodzący protokołu TCP na port 22 (węzły z systemem Linux) lub port 3389 (węzły z systemem Windows), aby zezwolić na dostęp zdalny. W przypadku niektórych typów zadań z wieloma wystąpieniami w systemie Linux (takich jak MPI) należy również zezwolić na ruch portu SSH 22 dla serwerów IP w podsieci zawierającej węzły obliczeniowe batch. Może to być zablokowane zgodnie z regułami sieciowej sieciowej na poziomie podsieci (patrz poniżej).
* Ruch wychodzący na dowolny port do sieci wirtualnej. Można to zmienić w przepisach dotyczących sieciowej sieciowej na poziomie podsieci (patrz poniżej).
* Ruch wychodzący na dowolnym porcie do Internetu. Można to zmienić w przepisach dotyczących sieciowej sieciowej na poziomie podsieci (patrz poniżej).

> [!IMPORTANT]
> Zachowaj ostrożność przy modyfikowaniu lub dodawaniu reguły ruchu przychodzącego lub wychodzącego w sieciowych grupach zabezpieczeń skonfigurowanych za pomocą usługi Batch. Jeśli komunikacja z węzłami obliczeniowymi w określonej podsieci zostanie odrzucona przez sieciową grupę zabezpieczeń, usługa Batch ustawi stan węzłów obliczeniowych na **nienadające się do użytku**. Ponadto żadne blokady zasobów nie powinny być stosowane do dowolnego zasobu utworzonego przez partię, w przeciwnym razie może to spowodować zapobieganie oczyszczaniu zasobów w wyniku akcji inicjowanych przez użytkownika, takich jak usuwanie puli.

#### <a name="network-security-groups-specifying-subnet-level-rules"></a>Sieciowe grupy zabezpieczeń: określanie reguł na poziomie podsieci

Nie jest wymagane określenie sieciowych grup zabezpieczeń na poziomie podsieci sieci wirtualnej, ponieważ usługa Batch konfiguruje własne sieciowe sieciowe (patrz wyżej). Jeśli z podsiecią skojarzoną jest grupa zabezpieczeń sieciowych, w której wdrożono węzły obliczeniowe usługi Batch lub chcesz zastosować niestandardowe reguły sieciowej grupy zabezpieczeń w celu zastąpienia zastosowanych wartości domyślnych, należy skonfigurować tę grupę zabezpieczeń z co najmniej przychodzącymi i wychodzącymi regułami zabezpieczeń, jak pokazano na poniższej stronie Tabel.

Skonfiguruj ruch przychodzący na porcie 3389 (Windows) lub 22 (Linux) tylko wtedy, gdy musisz zezwolić na zdalny dostęp do węzłów obliczeniowych ze źródeł zewnętrznych. Może być konieczne włączenie reguł portu 22 w systemie Linux, jeśli wymagana jest obsługa zadań wielu wystąpień z określonymi środowiskami uruchomieniowymi MPI. Zezwalanie na ruch na tych portach nie jest ściśle wymagane dla węzłów obliczeniowych puli, które mają być użyteczne.

**Reguły zabezpieczeń dla ruchu przychodzącego**

| Źródłowe adresy IP | Tag usługi źródłowej | Porty źródłowe | Element docelowy | Porty docelowe | Protocol (Protokół) | Akcja |
| --- | --- | --- | --- | --- | --- | --- |
| Nie dotyczy | `BatchNodeManagement`[Tag usługi](../articles/virtual-network/security-overview.md#service-tags) (jeśli używa się wariantu regionalnego, w tym samym regionie co konto usługi Batch) | * | Dowolne | 29876-29877 | TCP | Zezwalaj |
| W razie potrzeby serwery IP źródła użytkownika do zdalnego uzyskiwania dostępu do węzłów obliczeniowych i/lub podsieci węzłów obliczeniowych dla zadań wielu wystąpień systemu Linux. | Nie dotyczy | * | Dowolne | 3389 (Windows), 22 (Linux) | TCP | Zezwalaj |

**Reguły zabezpieczeń dla ruchu wychodzącego**

| Element źródłowy | Porty źródłowe | Element docelowy | Docelowy tag usługi | Porty docelowe | Protocol (Protokół) | Akcja |
| --- | --- | --- | --- | --- | --- | --- |
| Dowolne | * | [Tag usługi](../articles/virtual-network/security-overview.md#service-tags) | `Storage`(jeśli używasz wariantu regionalnego, w tym samym regionie co konto usługi Batch) | 443 | TCP | Zezwalaj |

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

Skonfiguruj ruch przychodzący na porcie 3389 dla systemu Windows, jeśli chcesz zezwolić na dostęp rdp do węzłów puli. Węzły puli nie muszą być użyteczne.

**Reguły zabezpieczeń dla ruchu przychodzącego**

| Źródłowe adresy IP | Porty źródłowe | Element docelowy | Porty docelowe | Protocol (Protokół) | Akcja |
| --- | --- | --- | --- | --- | --- |
Dowolne <br /><br />Mimo że w praktyce wymaga to zezwolenia na cały ruch, usługa Batch stosuje regułę listy ACL filtrującą wszystkie adresy IP nienależące do usługi Batch na poziomie każdego węzła. | * | Dowolne | 10100, 20100, 30100 | TCP | Zezwalaj |
| Opcjonalnie, aby umożliwić dostęp rdp do węzłów obliczeniowych. | * | Dowolne | 3389 | TCP | Zezwalaj |

**Reguły zabezpieczeń dla ruchu wychodzącego**

| Element źródłowy | Porty źródłowe | Element docelowy | Porty docelowe | Protocol (Protokół) | Akcja |
| --- | --- | --- | --- | --- | --- |
| Dowolne | * | Dowolne | 443  | Dowolne | Zezwalaj |

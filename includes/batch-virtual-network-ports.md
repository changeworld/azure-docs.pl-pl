---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 04/10/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 711b662c35b5f8fec96f1edee765696bc1028bf8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60550209"
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

**Dodatkowe zasoby sieciowe** — usługa Batch automatycznie przydziela dodatkowe zasoby sieciowe w grupie zasobów zawierającej sieć wirtualną. Dla każdego 50 węzły dedykowane (lub każdy z 20 węzłów o niskim priorytecie) przydziela partii: Moduł równoważenia obciążenia 1 sieciowej grupy zabezpieczeń (NSG), 1 publiczny adres IP i 1. Te zasoby są ograniczone przez [limity zasobów](../articles/azure-subscription-service-limits.md) subskrypcji. W przypadku dużych pul może być konieczne zażądanie zwiększenia limitu przydziału dla co najmniej jednego z tych zasobów.

#### <a name="network-security-groups"></a>Grupy zabezpieczeń sieci

Podsieć musi zezwalać na komunikację przychodzącą z usługi Batch, co umożliwia planowanie zadań w węzłach obliczeniowych, i na komunikację wychodzącą na potrzeby komunikacji z usługą Azure Storage lub innymi zasobami. W przypadku pul w konfiguracji maszyny wirtualnej usługa Batch dodaje sieciowe grupy zabezpieczeń na poziomie interfejsów sieciowych (NIC) dołączonych do maszyn wirtualnych. Te sieciowe grupy zabezpieczeń automatycznie konfigurują reguły ruchu przychodzącego i wychodzącego, aby zezwolić na następujący ruch:

* Ruch przychodzący protokołu TCP na porty 29876 i 29877 z adresów IP roli usługi Batch. 
* Ruch przychodzący protokołu TCP na port 22 (węzły z systemem Linux) lub port 3389 (węzły z systemem Windows), aby zezwolić na dostęp zdalny. W przypadku niektórych typów zadania obejmujące wiele wystąpień w systemie Linux (np. MPI), musisz również zezwolić na ruch na porcie 22 protokołu SSH dla adresów IP w podsieci zawierającej węzły obliczeniowe usługi Batch.
* Ruch wychodzący na dowolny port do sieci wirtualnej.
* Ruch wychodzący na dowolny port do Internetu.

> [!IMPORTANT]
> Zachowaj ostrożność przy modyfikowaniu lub dodawaniu reguły ruchu przychodzącego lub wychodzącego w sieciowych grupach zabezpieczeń skonfigurowanych za pomocą usługi Batch. Jeśli komunikacja z węzłami obliczeniowymi w określonej podsieci zostanie odrzucona przez sieciową grupę zabezpieczeń, usługa Batch ustawi stan węzłów obliczeniowych na **nienadające się do użytku**.

Nie trzeba określać sieciowych grup zabezpieczeń na poziomie podsieci, ponieważ usługa Batch konfiguruje własne sieciowe grupy zabezpieczeń. Jednak jeśli określona podsieć ma skojarzone sieciowe grupy zabezpieczeń i/lub zaporę, skonfiguruj reguły zabezpieczeń ruchu przychodzącego i wychodzącego tak, jak pokazano w poniższych tabelach. Skonfiguruj ruch przychodzący na porcie 3389 (Windows) lub 22 (Linux), tylko wtedy, gdy musisz zezwolić na zdalny dostęp do puli maszyn wirtualnych ze źródeł zewnętrznych. Maszyny wirtualne puli nie muszą być użyteczne. Należy pamiętać, że konieczne będzie włączenie ruchu w podsieci sieci wirtualnej na porcie 22 dla systemu Linux przy użyciu niektórych rodzajów zadania obejmujące wiele wystąpień, takie jak MPI.

**Reguły zabezpieczeń dla ruchu przychodzącego**

| Źródłowe adresy IP | Tag usługi źródłowej | Porty źródłowe | Element docelowy | Porty docelowe | Protokół | Akcja |
| --- | --- | --- | --- | --- | --- | --- |
| ND | `BatchNodeManagement` [Tag usługi](../articles/virtual-network/security-overview.md#service-tags) | * | Dowolne | 29876-29877 | TCP | Zezwalaj |
| Użytkownik źródłowe adresy IP do zdalnego uzyskiwania dostępu do węzłów obliczeniowych i/lub podsieci węzła obliczeń dla zadania obejmujące wiele wystąpień systemu Linux, jeśli jest to wymagane. | ND | * | Dowolne | 3389 (Windows), 22 (Linux) | TCP | Zezwalaj |

**Reguły zabezpieczeń dla ruchu wychodzącego**

| Element źródłowy | Porty źródłowe | Element docelowy | Docelowy tag usługi | Protokół | Akcja |
| --- | --- | --- | --- | --- | --- |
| Dowolne | 443 | [Tag usługi](../articles/virtual-network/security-overview.md#service-tags) | `Storage` (w tym samym regionie jako konta usługi Batch i sieci wirtualnej)  | Dowolne | Zezwalaj |

### <a name="pools-in-the-cloud-services-configuration"></a>Pule w konfiguracji usługi Cloud Services

**Obsługiwane sieci wirtualne** — tylko klasyczne sieci wirtualne

**Identyfikator podsieci** — w przypadku określenia podsieci za pomocą interfejsów API usługi Batch użyj *identyfikatora zasobu* podsieci. Identyfikator podsieci ma postać:

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicVirtualNetwork /virtualNetworks/{network}/subnets/{subnet}
  ```

**Uprawnienia** — jednostka usługi `MicrosoftAzureBatch` musi mieć rolę `Classic Virtual Machine Contributor` w ramach kontroli dostępu opartej na rolach (RBAC) dla określonej sieci wirtualnej.

#### <a name="network-security-groups"></a>Grupy zabezpieczeń sieci

Podsieć musi zezwalać na komunikację przychodzącą z usługi Batch, co umożliwia planowanie zadań w węzłach obliczeniowych, i na komunikację wychodzącą na potrzeby komunikacji z usługą Azure Storage lub innymi zasobami.

Nie trzeba określać sieciowej grupy zabezpieczeń, ponieważ usługa Batch konfiguruje komunikację przychodzącą tylko z adresów IP usługi Batch do węzłów puli. Jednak jeśli określona sieć wirtualna ma skojarzone sieciowe grupy zabezpieczeń i/lub zaporę, skonfiguruj reguły zabezpieczeń ruchu przychodzącego i wychodzącego tak, jak pokazano w poniższych tabelach. Jeśli komunikacja z węzłami obliczeniowymi w określonej podsieci zostanie odrzucona przez sieciową grupę zabezpieczeń, usługa Batch ustawi stan węzłów obliczeniowych na **nienadające się do użytku**.

Skonfiguruj ruch przychodzący na porcie 3389 dla Windows Jeśli chcesz zezwolić na dostęp RDP dla węzłów puli. Węzły puli nie muszą być użyteczne.

**Reguły zabezpieczeń dla ruchu przychodzącego**

| Źródłowe adresy IP | Porty źródłowe | Element docelowy | Porty docelowe | Protokół | Akcja |
| --- | --- | --- | --- | --- | --- |
Dowolne <br /><br />Mimo że w praktyce wymaga to zezwolenia na cały ruch, usługa Batch stosuje regułę listy ACL filtrującą wszystkie adresy IP nienależące do usługi Batch na poziomie każdego węzła. | * | Dowolne | 10100, 20100, 30100 | TCP | Zezwalaj |
| Opcjonalnie umożliwić dostęp RDP do węzłów obliczeniowych. | * | Dowolne | 3389 | TCP | Zezwalaj |

**Reguły zabezpieczeń dla ruchu wychodzącego**

| Element źródłowy | Porty źródłowe | Element docelowy | Porty docelowe | Protokół | Akcja |
| --- | --- | --- | --- | --- | --- |
| Dowolne | * | Dowolne | 443  | Dowolne | Zezwalaj |

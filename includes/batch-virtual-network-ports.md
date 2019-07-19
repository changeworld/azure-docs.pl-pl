---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 07/16/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: c8b25858556538835d6a84bf0d6699f9906f1438
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68322654"
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

**Dodatkowe zasoby sieciowe** — usługa Batch automatycznie przydziela dodatkowe zasoby sieciowe w grupie zasobów zawierającej sieć wirtualną. W przypadku każdego dedykowanego węzła 50 (lub każdego 20 węzłów o niskim priorytecie) usługi Batch przydziela: 1 sieciowa Grupa zabezpieczeń (sieciowej grupy zabezpieczeń), 1 publiczny adres IP i 1 moduł równoważenia obciążenia. Te zasoby są ograniczone przez [limity zasobów](../articles/azure-subscription-service-limits.md) subskrypcji. W przypadku dużych pul może być konieczne zażądanie zwiększenia limitu przydziału dla co najmniej jednego z tych zasobów.

#### <a name="network-security-groups"></a>Grupy zabezpieczeń sieci

Podsieć musi zezwalać na komunikację przychodzącą z usługi Batch, co umożliwia planowanie zadań w węzłach obliczeniowych, i na komunikację wychodzącą na potrzeby komunikacji z usługą Azure Storage lub innymi zasobami. W przypadku pul w konfiguracji maszyny wirtualnej usługa Batch dodaje sieciowe grupy zabezpieczeń na poziomie interfejsów sieciowych (NIC) dołączonych do maszyn wirtualnych. Te sieciowe grupy zabezpieczeń automatycznie konfigurują reguły ruchu przychodzącego i wychodzącego, aby zezwolić na następujący ruch:

* Ruch przychodzący protokołu TCP na porty 29876 i 29877 z adresów IP roli usługi Batch. 
* Ruch przychodzący protokołu TCP na port 22 (węzły z systemem Linux) lub port 3389 (węzły z systemem Windows), aby zezwolić na dostęp zdalny. W przypadku niektórych typów zadań z wieloma wystąpieniami w systemie Linux (na przykład MPI) należy również zezwolić na ruch z portu SSH 22 dla adresów IP w podsieci zawierającej węzły obliczeniowe usługi Batch.
* Ruch wychodzący na dowolny port do sieci wirtualnej.
* Ruch wychodzący na dowolny port do Internetu.

> [!IMPORTANT]
> Zachowaj ostrożność przy modyfikowaniu lub dodawaniu reguły ruchu przychodzącego lub wychodzącego w sieciowych grupach zabezpieczeń skonfigurowanych za pomocą usługi Batch. Jeśli komunikacja z węzłami obliczeniowymi w określonej podsieci zostanie odrzucona przez sieciową grupę zabezpieczeń, usługa Batch ustawi stan węzłów obliczeniowych na **nienadające się do użytku**.

Nie trzeba określać sieciowych grup zabezpieczeń na poziomie podsieci, ponieważ usługa Batch konfiguruje własne sieciowe grupy zabezpieczeń. Jednak jeśli określona podsieć ma skojarzone sieciowe grupy zabezpieczeń i/lub zaporę, skonfiguruj reguły zabezpieczeń ruchu przychodzącego i wychodzącego tak, jak pokazano w poniższych tabelach. Skonfiguruj ruch przychodzący na porcie 3389 (Windows) lub 22 (Linux) tylko wtedy, gdy musisz zezwolić na dostęp zdalny do maszyn wirtualnych puli ze źródeł zewnętrznych. Maszyny wirtualne puli nie muszą być użyteczne. Należy pamiętać, że w przypadku korzystania z określonych rodzajów zadań z wieloma wystąpieniami, takich jak MPI, należy włączyć ruch podsieci sieci wirtualnej na porcie 22 dla systemu Linux.

**Reguły zabezpieczeń dla ruchu przychodzącego**

| Źródłowe adresy IP | Tag usługi źródłowej | Porty źródłowe | Miejsce docelowe | Porty docelowe | Protocol | Action |
| --- | --- | --- | --- | --- | --- | --- |
| ND | `BatchNodeManagement`[Tag usługi](../articles/virtual-network/security-overview.md#service-tags) | * | Any | 29876-29877 | TCP | Allow |
| Adresy IP źródeł użytkowników umożliwiające zdalne uzyskiwanie dostępu do węzłów obliczeniowych i/lub podsieć węzłów obliczeniowych dla zadań z wielowystąpieniami systemu Linux, jeśli jest to wymagane. | ND | * | Any | 3389 (Windows), 22 (Linux) | TCP | Allow |

**Reguły zabezpieczeń dla ruchu wychodzącego**

| Source | Porty źródłowe | Miejsce docelowe | Docelowy tag usługi | Porty docelowe | Protocol | Action |
| --- | --- | --- | --- | --- | --- | --- |
| Any | * | [Tag usługi](../articles/virtual-network/security-overview.md#service-tags) | `Storage`(w tym samym regionie, w którym znajduje się konto w usłudze Batch i Sieć wirtualna) | 443 | TCP | Allow |

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

Skonfiguruj ruch przychodzący na porcie 3389 dla systemu Windows, jeśli chcesz zezwolić na dostęp RDP do węzłów puli. Węzły puli nie muszą być użyteczne.

**Reguły zabezpieczeń dla ruchu przychodzącego**

| Źródłowe adresy IP | Porty źródłowe | Miejsce docelowe | Porty docelowe | Protocol | Action |
| --- | --- | --- | --- | --- | --- |
Any <br /><br />Mimo że w praktyce wymaga to zezwolenia na cały ruch, usługa Batch stosuje regułę listy ACL filtrującą wszystkie adresy IP nienależące do usługi Batch na poziomie każdego węzła. | * | Any | 10100, 20100, 30100 | TCP | Allow |
| Opcjonalne, aby umożliwić dostęp RDP do węzłów obliczeniowych. | * | Any | 3389 | TCP | Allow |

**Reguły zabezpieczeń dla ruchu wychodzącego**

| Source | Porty źródłowe | Miejsce docelowe | Porty docelowe | Protocol | Action |
| --- | --- | --- | --- | --- | --- |
| Any | * | Any | 443  | Any | Allow |

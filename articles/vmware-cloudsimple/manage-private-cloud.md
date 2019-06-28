---
title: Zarządzanie rozwiązanie VMware na platformie Azure, Chmura prywatna CloudSimple
description: W tym artykule opisano możliwości, które są dostępne do zarządzania zasobami w chmurze prywatnej CloudSimple i działania
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 05a2fb451b3acce1011c1d5f4cf17f0a865d57d0
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332667"
---
# <a name="manage-private-cloud-resources-and-activity"></a>Zarządzanie zasobami chmury prywatnej i działania

Chmury prywatne są zarządzane z CloudSimple portalu.  Sprawdź stan, dostępnych zasobów, działanie chmury prywatnej i inne ustawienia, z poziomu portalu CloudSimple.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Uzyskiwanie dostępu do portalu CloudSimple

Dostęp do [CloudSimple portal](access-cloudsimple-portal.md).

## <a name="view-the-list-of-private-clouds"></a>Wyświetlanie listy chmur prywatnych

**Chmurami prywatnymi** karcie **zasobów** strona zawiera listę wszystkich chmur prywatnych w ramach subskrypcji. Informacje obejmują nazwę, numer wiersza polecenia vsphere klastrów, lokalizacji, bieżący stan informacje prywatne chmury i zasobów.

![Chmura prywatna strony](media/manage-private-cloud.png)

Wybierz chmurę prywatną, aby uzyskać dodatkowe informacje i akcje.

## <a name="private-cloud-summary"></a>Podsumowanie chmury prywatnej

Wyświetl podsumowanie kompleksowe wybranej chmury prywatnej.  Strona podsumowania zawiera serwery DNS, wdrożyć w chmurze prywatnej.  Możesz skonfigurować DNS przekazywania z lokalnymi serwerami DNS, serwery DNS chmury prywatnej.  Aby uzyskać więcej informacji na temat przekazywania DNS, zobacz [Konfigurowanie serwera DNS do rozpoznawania nazw dla chmury prywatnej vCenter ze środowiska lokalnego](https://docs.azure.cloudsimple.com/on-premises-dns-setup/).

![Podsumowanie chmury prywatnej](media/private-cloud-summary.png)

### <a name="available-actions"></a>Dostępne akcje

* [Uruchamianie klienta programu vSphere](https://docs.azure.cloudsimple.com/vsphere-access/). Dla tej chmury prywatnej, należy uzyskać dostęp do programu vCenter.
* [Kup węzłów](create-nodes.md). Dodawanie węzłów do tej chmury prywatnej.
* [Rozwiń](expand-private-cloud.md). Dodawanie węzłów do tej chmury prywatnej.
* **Odśwież**. Zaktualizuj informacje na tej stronie.
* **Usuń**. Możesz usunąć chmurę prywatną, w dowolnym momencie. **Przed usunięciem upewnij się, że utworzono kopię zapasową wszystkich systemów i danych.** Usunięcie Chmura prywatna powoduje usunięcie wszystkich maszyn wirtualnych, vCenter konfiguracji i danych. Kliknij przycisk **Usuń** w sekcji podsumowania dla wybranej chmury prywatnej. Po usunięciu wszystkich danych w chmurze prywatnej są usuwane w ramach procesu bezpiecznego, wysoce zgodna wymazywania.
* [Zmień uprawnienia vSphere](escalate-private-cloud-privileges.md).  Eskaluj Twoje uprawnienia w tej chmurze prywatnej.

## <a name="private-cloud-vlanssubnets"></a>Private Cloud VLANS/subnets

Wyświetl listę podsieci/sieci VLAN zdefiniowanych dla wybranej chmury prywatnej.  Lista zawiera zarządzanie utworzonymi w podsieci/sieci VLAN do Chmura prywatna została utworzona.

![Chmura prywatna — podsieci/sieci VLAN](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Dostępne akcje

* [Dodawanie podsieci/sieci VLAN](https://docs.azure.cloudsimple.com/create-vlan-subnet/). Do tej chmury prywatnej, należy dodać sieci VLAN/podzbiór.

Wybierz sieć VLAN/podsieć dla następujących akcji
* [Dołącz tabelę zapory](https://docs.azure.cloudsimple.com/firewall/). Tabela zapory należy dołączyć do tej chmury prywatnej.
* **Edytowanie**
* **Usuń** (tylko użytkownika podsieci/sieci VLAN)

## <a name="private-cloud-activity"></a>Aktywność w chmurze prywatnej

Wyświetl następujące informacje dla wybranej chmury prywatnej.  Informacje dotyczące aktywności jest filtrowana lista wszystkich działań dla wybranej chmury prywatnej.  Ta strona zawiera maksymalnie 25 ostatnie działania.

* Ostatnie alerty
* Ostatnie zdarzenia
* Ostatnie zadania
* Ostatnie inspekcji

![Chmura prywatna — działanie](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Stojaki chmury

Stojaki chmury są blokami konstrukcyjnymi usługi chmury prywatnej. Każdy stojak zawiera jednostki pojemności. CloudSimple automatycznie konfiguruje stojakami chmury na podstawie dokonanego wyboru podczas tworzenia lub rozszerzania chmury prywatnej.  Wyświetl pełną listę stojakami chmury, łącznie z chmury prywatnej, których każda jest przypisany do.

![Chmura prywatna — stojaki chmury](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>vSphere Management Network

Lista zasobów zarządzania VMware i maszyn wirtualnych, które są aktualnie skonfigurowane w chmurze prywatnej. Informacje dotyczące wersji oprogramowania, w pełni kwalifikowaną nazwę domeny (FQDN) i adres IP zasobów.

![Chmura prywatna — vSphere sieć zarządzania](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>Kolejne kroki

* [Używanie maszyn wirtualnych VMware na platformie Azure](quickstart-create-vmware-virtual-machine.md)
* Dowiedz się więcej o [Chmurami prywatnymi](cloudsimple-private-cloud.md)
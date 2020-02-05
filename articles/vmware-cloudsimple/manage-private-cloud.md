---
title: Zarządzaj chmurą prywatną Azure VMware Solutions (Automatyczna synchronizacja)
description: W tym artykule opisano możliwości dostępne w celu zarządzania programem automatycznej synchronizacji zasobów i aktywności chmury prywatnej
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 47bf2251f71204b99245c1a9d55ef87157c41dd8
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014831"
---
# <a name="manage-avs-private-cloud-resources-and-activities"></a>Zarządzanie automatyczna synchronizacja zasobów i działań w chmurze prywatnej

W portalu do automatycznej synchronizacji są zarządzane chmury prywatne. Sprawdź stan, dostępne zasoby, aktywność w chmurze prywatnej automatycznej synchronizacji oraz inne ustawienia w portalu automatycznej synchronizacji.

## <a name="sign-in-to-azure"></a>Zaloguj się w usłudze Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-avs-portal"></a>Dostęp do portalu automatycznej synchronizacji

Uzyskaj dostęp do [portalu automatycznej synchronizacji](access-cloudsimple-portal.md).

## <a name="view-the-list-of-avs-private-clouds"></a>Wyświetl listę chmur prywatnych w wersji zapoznawczej

Karta **prywatne chmury do automatycznej synchronizacji** na stronie **zasoby** zawiera listę wszystkich chmur prywatnych w Twojej subskrypcji. Informacje obejmują nazwę, liczbę klastrów vSphere, lokalizację, bieżący stan chmury prywatnej do automatycznej synchronizacji i informacje o zasobach.

![Automatyczna synchronizacja strony chmury prywatnej](media/manage-private-cloud.png)

Wybierz chmurę prywatną do automatycznej synchronizacji, aby uzyskać dodatkowe informacje i akcje.

## <a name="avs-private-cloud-summary"></a>Podsumowanie dotyczące automatycznej synchronizacji chmury prywatnej

Wyświetl kompleksowe podsumowanie wybranej chmury prywatnej automatycznej wersji zapoznawczej. Strona podsumowania zawiera serwery DNS wdrożone w chmurze prywatnej automatycznej synchronizacji. Można skonfigurować przesyłanie dalej DNS z lokalnych serwerów DNS do programu w celu automatycznej synchronizacji serwerów DNS w chmurze prywatnej. Aby uzyskać więcej informacji na temat przekazywania DNS, zobacz [Konfigurowanie systemu DNS do rozpoznawania nazw w celu automatycznej synchronizacji chmury prywatnej vCenter](https://docs.azure.cloudsimple.com/on-premises-dns-setup/)w środowisku lokalnym.

![Podsumowanie dotyczące automatycznej synchronizacji chmury prywatnej](media/private-cloud-summary.png)

### <a name="available-actions"></a>Dostępne akcje

* [Uruchom klienta vSphere](https://docs.azure.cloudsimple.com/vsphere-access/). Uzyskaj dostęp do programu vCenter w tej chmurze prywatnej automatycznej wersji.
* [Kup węzły](create-nodes.md). Dodaj węzły do tej chmury prywatnej automatycznej wersji próbnej.
* [Rozwiń węzeł](expand-private-cloud.md). Dodaj węzły do tej chmury prywatnej automatycznej wersji próbnej.
* **Odświeżanie**. Zaktualizuj informacje na tej stronie.
* **Usuń**. Chmurę prywatną można usunąć w dowolnym momencie. **Przed usunięciem upewnij się, że utworzono kopię zapasową wszystkich systemów i danych.** Usunięcie chmury prywatnej automatycznej synchronizacji usuwa wszystkie maszyny wirtualne, konfigurację programu vCenter i dane. Kliknij pozycję **Usuń** w sekcji Podsumowanie dla wybranej chmury prywatnej automatycznej synchronizacji. Po usunięciu wszystkie dane w chmurze prywatnej automatycznej synchronizacji są usuwane w bezpiecznym, wysoce zgodnym procesie wymazywania.
* [Zmień uprawnienia vSphere](escalate-private-cloud-privileges.md). Eskalować swoje uprawnienia do tej chmury prywatnej.

## <a name="avs-private-cloud-vlanssubnets"></a>Automatyczna synchronizacja sieci VLAN/podsieci w chmurze prywatnej

Wyświetl listę zdefiniowanych sieci VLAN/podsieci dla wybranej chmury prywatnej automatycznej wersji zapoznawczej. Lista zawiera sieci VLAN i podsieci zarządzania utworzone podczas tworzenia chmury prywatnej automatycznej synchronizacji.

![Automatyczna synchronizacja chmur prywatnych — sieci VLAN/podsieci](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Dostępne akcje

* [Dodawanie sieci VLAN/podsieci](https://docs.azure.cloudsimple.com/create-vlan-subnet/). Dodaj sieć VLAN/podzestaw do tej chmury prywatnej.

Wybierz sieć VLAN/podsieć dla następujących akcji
* [Dołącz tabelę zapory](https://docs.azure.cloudsimple.com/firewall/). Dołącz tabelę zapory do tej chmury prywatnej automatycznej wersji zaszeregowej.
* **Edytowanie**
* **Usuń** (tylko zdefiniowane przez użytkownika sieci VLAN/podsieci)

## <a name="avs-private-cloud-activity"></a>Automatyczna synchronizacja działań w chmurze prywatnej

Wyświetl następujące informacje dotyczące wybranej chmury prywatnej automatycznej wersji zapoznawczej. Informacje o działaniu to filtrowana lista wszystkich działań dla wybranej chmury prywatnej automatycznej synchronizacji. Ta strona zawiera maksymalnie 25 ostatnich działań.

* Ostatnie alerty
* Ostatnie zdarzenia
* Ostatnie zadania
* Ostatnia Inspekcja

![Automatyczna synchronizacja z chmurą prywatną — aktywność](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Stojaki w chmurze

Stojaki w chmurze to bloki konstrukcyjne chmury prywatnej automatycznej synchronizacji. Każdy stojak zapewnia jednostkę pojemności. Automatyczna synchronizacja automatycznie konfiguruje Stojaki w chmurze na podstawie wybranych opcji podczas tworzenia lub rozszerzania chmury prywatnej do automatycznej synchronizacji. Zapoznaj się z pełną listą stojaków w chmurze, w tym chmurę prywatną do automatycznej synchronizacji, do której każdy jest przypisany.

![Automatyczna synchronizacja chmur prywatnych — Stojaki w chmurze](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>Sieć zarządzania vSphere

Lista zasobów zarządzania VMware i maszyn wirtualnych, które są aktualnie skonfigurowane w chmurze prywatnej automatycznej synchronizacji. Informacje obejmują wersję oprogramowania, w pełni kwalifikowaną nazwę domeny (FQDN) i adres IP zasobów.

![Automatyczna synchronizacja chmury prywatnej — vSphere Management Network](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>Następne kroki

* [Korzystanie z maszyn wirtualnych VMware na platformie Azure](quickstart-create-vmware-virtual-machine.md)
* Dowiedz się więcej o [automatycznej synchronizacji chmur prywatnych](cloudsimple-private-cloud.md)
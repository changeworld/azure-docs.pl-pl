---
title: Azure VMware Solutions (Automatyczna synchronizacja) — dostęp do klienta vSphere
description: Opisuje, jak uzyskać dostęp do programu vCenter z chmury prywatnej automatycznej synchronizacji.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ad018ea89b194d42ab1867a0569725c4c3680f7d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022668"
---
# <a name="access-your-avs-private-cloud-vcenter-portal"></a>Dostęp do Twojego portalu vCenter w chmurze prywatnej chmury

Możesz uruchomić swoją aplikację do automatycznej synchronizacji w portalu vCenter w chmurze w usłudze Azure Portal lub w wersji zaautomatycznej. Portal vCenter umożliwia zarządzanie infrastrukturą VMware w chmurze prywatnej automatycznej synchronizacji.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Należy nawiązać połączenie sieciowe, a rozpoznawanie nazw DNS musi być włączone, aby można było uzyskać dostęp do portalu vCenter. Możesz nawiązać połączenie sieciowe z chmurą prywatną do automatycznej synchronizacji przy użyciu dowolnej z poniższych opcji.

* [Łączenie się z lokalnego programu w celu automatycznej synchronizacji przy użyciu ExpressRoute](on-premises-connection.md)
* [Skonfiguruj połączenie sieci VPN z chmurą prywatną do automatycznej synchronizacji](set-up-vpn.md)

Aby skonfigurować rozpoznawanie nazw DNS składników infrastruktury programu VMware w chmurze prywatnej, zobacz [Konfigurowanie systemu DNS na potrzeby rozpoznawania nazw w celu uzyskania automatycznej synchronizacji z lokalnymi stacjami roboczymi w chmurze](on-premises-dns-setup.md) .

## <a name="sign-in-to-azure"></a>Zaloguj się w usłudze Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="access-vcenter-from-azure-portal"></a>Dostęp do programu vCenter z Azure Portal

Portal usługi vCenter w chmurze prywatnej automatycznej synchronizacji można uruchomić z poziomu Azure Portal.

1. Wybierz pozycję **Wszystkie usługi**.

2. Wyszukaj **usługi automatycznej synchronizacji**.

3. Wybierz usługę automatycznej synchronizacji w chmurze prywatnej automatycznej synchronizacji, z którą chcesz nawiązać połączenie.

4. Na stronie **Przegląd** kliknij pozycję **Obejrzyj chmurę prywatną programu VMware.**

    ![Przegląd usługi automatycznej synchronizacji](media/cloudsimple-service-overview.png)

5. Wybierz chmurę prywatną automatycznej synchronizacji z listy chmur prywatnych, a następnie kliknij przycisk **Uruchom VSphere klienta**.

    ![Uruchom klienta vSphere](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-avs-portal"></a>Dostęp do programu vCenter z portalu automatycznej synchronizacji

Portal usługi vCenter w chmurze prywatnej automatycznej synchronizacji można uruchomić z poziomu portalu automatycznej synchronizacji.

1. Uzyskaj dostęp do [portalu automatycznej synchronizacji](access-cloudsimple-portal.md).

2. Z **zasobów** Wybierz chmurę prywatną do automatycznej synchronizacji, do której chcesz uzyskać dostęp, a następnie kliknij pozycję **Uruchom klienta vSphere**.

    ![Uruchom vSphere klienta-zasoby](media/cloudsimple-portal-resources-launch-vcenter.png)

3. Możesz również uruchomić Portal vCenter z poziomu ekranu podsumowania chmury prywatnej.

    ![Uruchom vSphere klienta — podsumowanie](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>Następne kroki

* [Tworzenie i zarządzanie sieciami VLAN/podsieciami dla chmur prywatnych o automatycznej synchronizacji](create-vlan-subnet.md)
* [Automatyczna synchronizacja modelu uprawnień chmury prywatnej programu VMware vCenter](learn-private-cloud-permissions.md)
---
title: Rozwiązanie Azure VMware według cloudSimple — klient usługi Access vSphere
description: W tym artykule opisano, jak uzyskać dostęp do centrum wirtualnego chmury prywatnej.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 18d9463bc512257034860e1188372879524924f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022668"
---
# <a name="access-your-private-cloud-vcenter-portal"></a>Dostęp do portalu private cloud vCenter

Portal vCenter usługi Private Cloud można uruchomić z witryny Azure portal lub portalu CloudSimple.  Portal vCenter umożliwia zarządzanie infrastrukturą VMware w chmurze prywatnej.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Należy ustanowić połączenie sieciowe i włączyć rozpoznawanie nazw DNS w celu uzyskania dostępu do portalu vCenter.  Połączenie sieciowe z chmurą prywatną można nawiązać, korzystając z dowolnej z poniższych opcji.

* [Łączenie się z lokalnego do CloudSimple przy użyciu usługi ExpressRoute](on-premises-connection.md)
* [Konfigurowanie połączenia sieci VPN z chmurą prywatną CloudSimple](set-up-vpn.md)

Aby skonfigurować rozpoznawanie nazw DNS składników infrastruktury VMware w chmurze prywatnej, zobacz [Konfigurowanie rozpoznawania nazw systemu DNS dla dostępu do centrum wirtualnego usługi Private Cloud z lokalnych stacji roboczych](on-premises-dns-setup.md)

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure portal w [https://portal.azure.com](https://portal.azure.com).

## <a name="access-vcenter-from-azure-portal"></a>Uzyskiwanie dostępu do centrum wirtualnego z witryny Azure portal

Portal vCenter usługi Private Cloud można uruchomić z witryny Azure portal.

1. Wybierz pozycję **Wszystkie usługi**.

2. Wyszukaj **usługi CloudSimple**.

3. Wybierz usługę CloudSimple chmury prywatnej, z którą chcesz się połączyć.

4. Na stronie **Przegląd** kliknij pozycję **Wyświetl chmury prywatne VMware**

    ![Omówienie usługi CloudSimple](media/cloudsimple-service-overview.png)

5. Wybierz chmurę prywatną z listy chmur prywatnych i kliknij przycisk **Uruchom klienta vSphere**.

    ![Uruchom klienta vSphere](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-cloudsimple-portal"></a>Dostęp do centrum vCenter z portalu CloudSimple

Portal vCenter usługi Private Cloud można uruchomić z portalu CloudSimple.

1. Uzyskaj dostęp do [portalu CloudSimple](access-cloudsimple-portal.md).

2. Z **zasobów** wybierz Private Cloud, do którego chcesz uzyskać dostęp i kliknij **uruchom klienta vSphere**.

    ![Uruchom klienta vSphere - Zasoby](media/cloudsimple-portal-resources-launch-vcenter.png)

3. Portal vCenter można również uruchomić na ekranie podsumowania chmury prywatnej.

    ![Uruchomienie klienta vSphere - Podsumowanie](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>Następne kroki

* [Tworzenie sieci/podsieci sieci VLAN i zarządzanie nimi dla chmur prywatnych](create-vlan-subnet.md)
* [CloudSimple Private Cloud model uprawnień VMware vCenter](learn-private-cloud-permissions.md)
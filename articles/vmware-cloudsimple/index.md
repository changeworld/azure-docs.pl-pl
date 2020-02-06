---
title: Azure VMware Solutions (AVS)
description: Portal dokumentacji dla usługi Azure VMware Solutions (AVS).
author: sharaths-cs
ms.author: b-mashar
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d81ea6778f3ba31d72c34334b1439994b076647c
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025218"
---
# <a name="azure-vmware-solution-by-avs"></a>Rozwiązanie VMware na platformie Azure obsługiwane przez usługę AVS

Witamy w portalu, w którym scentralizowano zasoby pomocy dotyczące rozwiązania VMware na platformie Azure obsługiwanego przez usługę AVS.
W tej witrynie dokumentacji można zapoznać się z następującymi tematami:

## <a name="overview"></a>Omówienie

Dowiedz się więcej o usłudze Azure VMware Solutions (AVS)

* Informacje na temat funkcji, korzyści i scenariuszy użycia: [Co to jest rozwiązanie VMware na platformie Azure obsługiwane przez usługę AVS](cloudsimple-vmware-solutions-overview.md)
* Zapoznaj się z [kluczowymi pojęciami dotyczącymi administracji](key-concepts.md)

## <a name="quickstart"></a>Szybki start

Dowiedz się, jak zacząć korzystać z rozwiązania

* Opis [inicjowania usługi i pojemności zakupu](quickstart-create-cloudsimple-service.md)
* Dowiedz się, jak utworzyć nowe środowisko VMware: [Konfigurowanie środowiska chmury prywatnej AVS](quickstart-create-private-cloud.md)
* Dowiedz się, jak ujednolicić zarządzanie w środowisku VMware i Azure — artykuł [Używanie maszyn wirtualnych VMware na platformie Azure](quickstart-create-vmware-virtual-machine.md)

## <a name="concepts"></a>Pojęcia

Poznaj następujące pojęcia

* [Usługa AVS](cloudsimple-service.md) znana również jako usługa Azure VMware Solutions (AVS). Ten zasób należy utworzyć raz w każdym regionie.
* Kupowanie pojemności dla środowiska przez utworzenie jednego lub większej liczby zasobów [węzła AVS](cloudsimple-node.md). Te zasoby są również nazywane węzłem rozwiązania VMware na platformie Azure obsługiwanego przez usługę AVS.
* Inicjowanie i konfigurowanie środowiska VMware przy użyciu [chmur prywatnych AVS](cloudsimple-private-cloud.md).
* Ujednolicanie zarządzania przy użyciu[maszyn wirtualnych AVS](cloudsimple-virtual-machines.md) nazywanych również maszynami wirtualnymi rozwiązania VMware na platformie Azure obsługiwanego przez usługę AVS.
* Projektowanie sieci bazowej [przy użyciu podsieci/VLAN](cloudsimple-vlans-subnets.md).
* Segmentowanie i zabezpieczanie sieci bazowej za pomocą zasobu [Tabela zapory](cloudsimple-firewall-tables.md).
* Uzyskiwanie bezpiecznego dostępu do środowisk VMware za pośrednictwem sieci WAN przy użyciu[bram VPN Gateway](cloudsimple-vpn-gateways.md).
* Zapewnianie publicznego dostępu do obciążeń przy użyciu [publicznego adresu IP](cloudsimple-public-ip-address.md).
* Ustanawianie łączności z sieciami wirtualnymi platformy Azure i sieciami lokalnymi przy użyciu [połączenia sieciowego platformy Azure](cloudsimple-azure-network-connection.md).
* Konfigurowanie elementów docelowych poczty e-mail dla alertów za pomocą obszaru [Zarządzanie kontami](cloudsimple-account.md).
* Wyświetlanie dzienników aktywności użytkowników i systemu przy użyciu ekranów [Zarządzanie działaniami](cloudsimple-activity.md).
* Przegląd różnych [składników oprogramowania VMware](vmware-components.md).

## <a name="tutorials"></a>Samouczki

Dowiedz się, jak wykonywać typowe zadania, takie jak:

* [Tworzenie usługi AVS](create-cloudsimple-service.md), raz w każdym regionie, w którym mają zostać wdrożone środowiska VMware.
* Zarządzanie podstawową funkcjonalnością usługi w [portalu AVS](access-cloudsimple-portal.md).
* Zapewnianie pojemności i optymalizowanie rozliczeń dla danej infrastruktury przez [zakup węzłów AVS](create-nodes.md).
* Zarządzanie konfiguracjami środowiska VMware przy użyciu chmur prywatnych AVS. Chmury prywatne AVS można [tworzyć](create-private-cloud.md), [rozszerzać](expand-private-cloud.md) i [zmniejszać](shrink-private-cloud.md) oraz [zarządzać](manage-private-cloud.md) nimi.
* Włączanie ujednoliconego zarządzania przez [zamapowanie subskrypcji platformy Azure](azure-subscription-mapping.md).
* Monitorowanie aktywności użytkowników i systemu przy użyciu ekranów [stron działań](monitor-activity.md).
* Konfigurowanie sieci dla danych środowisk przez [utworzenie podsieci i zarządzanie nimi](create-vlan-subnet.md).
* Segmentowanie i zabezpieczanie środowiska [przy użyciu reguł i tabel zapory](firewall.md).
* Zapewnianie dostępu przychodzącego przez Internet dla obciążeń przez [przydzielenie publicznych adresów IP](public-ips.md).
* Zapewnienie łączności z sieci wewnętrznych lub klienckich stacji roboczych przez [skonfigurowanie sieci VPN](vpn-gateway.md).
* Zapewnianie komunikacji ze [środowisk lokalnych](on-premises-connection.md), a także z [sieciami wirtualnymi platformy Azure](virtual-network-connection.md).
* Konfigurowanie elementów docelowych alertów i wyświetlanie całej zakupionej pojemności w [podsumowaniu konta](account.md).
* Wyświetlanie [użytkowników](users.md), którzy uzyskali dostęp do portalu AVS.
* Zarządzanie maszynami wirtualnymi oprogramowania VMware w witrynie Azure Portal:
    * [Tworzenie maszyn wirtualnych](azure-create-vm.md) w witrynie Azure Portal.
    * [Zarządzanie utworzonymi maszynami wirtualnymi](azure-manage-vm.md).

## <a name="how-to-guides"></a>Przewodniki z instrukcjami

W tych przewodnikach opisano rozwiązania realizujące następujące cele:

* [Zabezpieczanie środowiska](private-cloud-secure.md)
* Instalowanie narzędzi innych producentów, włączanie dodatkowych użytkowników i zewnętrznego źródła uwierzytelniania w programie vSphere przy użyciu [eskalacji uprawnień](escalate-privileges.md).
* Konfigurowanie dostępu do różnych usług VMware przez [skonfigurowanie lokalnego systemu DNS](on-premises-dns-setup.md).
* Włączanie przydzielania nazw i adresów dla obciążeń przez [skonfigurowanie dla tych obciążeń systemów DNS i DHCP](dns-dhcp-setup.md).
* Omówienie sposobu, w jaki usługa zapewnia bezpieczeństwo i funkcjonalność na platformie klienta za pomocą [aktualizacji i uaktualnień](vmware-components.md#updates-and-upgrades) usługi.
* Zmniejszanie całkowitego kosztu posiadania związanego z kopiami zapasowymi dzięki utworzeniu przykładowej architektury tworzenia kopii zapasowych za pomocą [oprogramowania do tworzenia kopii zapasowych innego producenta, takiego jak Veeam](backup-workloads-veeam.md).
* Tworzenie bezpiecznego środowiska przez włączenie szyfrowania danych w spoczynku przy użyciu [oprogramowania do szyfrowania KMS innego producenta](vsan-encryption.md).
* Rozszerzanie zarządzania za pomocą usługi Azure Active Directory (Azure AD) na środowisko VMware przez skonfigurowanie [źródła tożsamości usługi Azure AD](azure-ad.md).

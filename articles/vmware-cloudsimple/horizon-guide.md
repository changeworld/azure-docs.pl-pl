---
title: Rozwiązanie Azure VMware by CloudSimple — hostowanie infrastruktury pulpitu wirtualnego przy użyciu programu VMware Horizon za pomocą witryny private cloud
description: W tym artykule opisano, jak za pomocą witryny CloudSimple Private Cloud hostować infrastrukturę pulpitu wirtualnego przy użyciu programu VMware Horizon
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 723821a78ecae308443c93567402e3b232c036f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025252"
---
# <a name="use-cloudsimple-private-cloud-site-to-host-a-virtual-desktop-infrastructure-using-vmware-horizon"></a>Korzystanie z witryny CloudSimple Private Cloud do hosta infrastruktury pulpitu wirtualnego przy użyciu programu VMware Horizon

Za pomocą witryny CloudSimple Private Cloud można hostować infrastrukturę pulpitu wirtualnego (VDI) przy użyciu programu VMware Horizon 7.x. Na poniższej ilustracji przedstawiono architekturę rozwiązania logicznego dla VDI.

![Wdrażanie programu Horizon](media/horizon-deployment.png)

Dzięki temu rozwiązaniu masz pełną kontrolę nad Horizon View Manager i App Volume. Znane interfejsy interfejsu użytkownika, interfejsu API i interfejsu wiersza polecenia umożliwiają korzystanie z istniejących skryptów i narzędzi.

Rozwiązanie CloudSimple wymaga wykonania następujących czynności:

* Zainstaluj, skonfiguruj i zarządzaj programem VMware Horizon 7.x w chmurze prywatnej.
* Zapewnij własne licencje Horizon.

## <a name="deploy-the-solution"></a>Wdrażanie rozwiązania

W poniższych sekcjach opisano sposób wdrażania rozwiązania VDI przy użyciu programu Horizon w chmurze prywatnej.

1. [Sprawdź, czy wersje produktów VMware są zgodne](#verify-that-vmware-product-versions-are-compatible)
2. [Oszacuj rozmiar środowiska graficznego](#estimate-the-size-of-your-desktop-environment)
3. [Tworzenie chmury prywatnej dla swojego środowiska](#create-a-private-cloud-for-your-environment)
4. [Instalowanie programu VMware Horizon w chmurze prywatnej](#install-vmware-horizon-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Sprawdź, czy wersje produktów VMware są zgodne

* Sprawdź, czy bieżące i planowane wersje programu Horizon, App Volumes, Unified Access Gateway i User Environment Manager są ze sobą zgodne oraz z vCenter i PSC w chmurze prywatnej. Aby uzyskać informacje o zgodności, zobacz [Macierz zgodności oprogramowania VMware dla programu Horizon 7.5](https://www.vmware.com/resources/compatibility/sim/interop_matrix.php#interop&260=2877&0=).
* Aby dowiedzieć się aktualnych wersji vCenter i PSC w chmurze prywatnej, przejdź do **zasobów** w [portalu CloudSimple](access-cloudsimple-portal.md), wybierz chmurę prywatną i kliknij kartę **vSphere Management Network.**

![Wersje vCenter i PSC](media/private-cloud-vsphere-versions.png)

### <a name="estimate-the-size-of-your-desktop-environment"></a>Oszacuj rozmiar środowiska graficznego

* Sprawdź, czy zidentyfikowana konfiguracja mieści się w granicach operacyjnych VMware.
* Oszacuj zasoby potrzebne dla wszystkich komputerów stacjonarnych i składników zarządzania programem Horizon.

### <a name="create-a-private-cloud-for-your-environment"></a>Tworzenie chmury prywatnej dla swojego środowiska

1. Utwórz chmurę prywatną z portalu CloudSimple, postępując zgodnie z instrukcjami w [temacie Konfigurowanie środowiska chmury prywatnej.](quickstart-create-private-cloud.md)  CloudSimple tworzy domyślnego użytkownika vCenter o nazwie "cloudowner" w każdej nowo utworzonej chmury prywatnej. Aby uzyskać szczegółowe informacje na temat domyślnego modelu użytkowników i uprawnień w chmurze [prywatnej, zobacz Poznaj model uprawnień chmury prywatnej](learn-private-cloud-permissions.md).
2. Utwórz sieć VLAN w chmurze prywatnej dla płaszczyzny zarządzania horyzontem i przypisz jej podsieć CIDR. Aby uzyskać instrukcje, zobacz [Tworzenie sieci/podsieci i zarządzanie nimi](create-vlan-subnet.md). Jest to sieć, w której zostaną zainstalowane wszystkie składniki rozwiązania (ujednolicona brama dostępu, serwer połączeń, serwer woluminów aplikacji i serwery Menedżera środowiska użytkownika).
3. Zdecyduj, czy chcesz używać zewnętrznego dostawcy tożsamości z centrum vCenter private cloud. Jeśli tak, wybierz jedną z następujących opcji:
    * Użyj lokalnej usługi Active Directory jako zewnętrznego dostawcy tożsamości. Aby uzyskać instrukcje, zobacz [vCenter Identity Sources](set-vcenter-identity.md).
    * Skonfiguruj serwer usługi Active Directory w sieci VLAN w chmurze prywatnej w ramach usługi Horizon, aby był używany jako zewnętrzny dostawca tożsamości. Aby uzyskać instrukcje, zobacz [vCenter Identity Sources](set-vcenter-identity.md).
    * Skonfiguruj serwer DHCP i DNS w sieci VLAN płaszczyzny zarządzania horyzontu w chmurze prywatnej. Aby uzyskać instrukcje, zobacz [Konfigurowanie aplikacji i obciążeń DNS i DHCP w chmurze CloudSimple Private Cloud](dns-dhcp-setup.md).
4. Skonfiguruj przekazywanie dns na serwerze DNS zainstalowanym w chmurze prywatnej. Aby uzyskać instrukcje, zobacz [Tworzenie warunkowego przesyłania dalej](on-premises-dns-setup.md#create-a-conditional-forwarder).

### <a name="install-vmware-horizon-in-your-private-cloud"></a>Instalowanie programu VMware Horizon w chmurze prywatnej

Poniższy diagram wdrażania przedstawia rozwiązanie horizon wdrożone w chmurze prywatnej. Ujednolicona brama dostępu, ad/dc, widok i serwer woluminów aplikacji są instalowane w utworzonej przez użytkownika sieci VLAN 234. Brama ujednoliconego dostępu ma przypisany publiczny adres IP, do który można uzyskać dostęp z Internetu. Maszyny wirtualne z puli komputerów stacjonarnych horizon są wdrażane w sieci VLAN 235, aby zapewnić dodatkową izolację i bezpieczeństwo.

![Wdrożenie programu Horizon w chmurze prywatnej](media/horizon-private-cloud.png)

W poniższych sekcjach opisano instrukcje konfigurowania wdrożenia podobnego do przedstawionego na rysunku. Przed rozpoczęciem sprawdź, czy masz następujące elementy:

* Private Cloud utworzony przy użyciu portalu CloudSimple z wystarczającą pojemnością do uruchamiania pul pulpitu.
* Wystarczająca przepustowość między środowiskiem lokalnym a środowiskiem chmury prywatnej do obsługi ruchu sieciowego dla komputerów stacjonarnych.
* Tunel sieci VPN między lokalnym centrum danych a chmurą prywatną jest skonfigurowany między lokalnym centrum danych a chmurą prywatną.
* Osiągalność adresów IP z podsieci użytkowników końcowych w środowisku lokalnym do podsieci CloudSimple Private Cloud.
* Ad/DHCP/DNS zainstalowany dla chmury prywatnej.

#### <a name="cloudsimple-portal-create-a-dedicated-vlansubnet-for-desktop-pools"></a>Portal CloudSimple: tworzenie dedykowanej sieci VLAN/podsieci dla pul komputerów stacjonarnych

Utwórz sieć VLAN dla pul pulpitu Horizon i przypisz jej cidr podsieci. Aby uzyskać instrukcje, zobacz [Tworzenie sieci/podsieci i zarządzanie nimi](create-vlan-subnet.md). Jest to sieć, w której będą uruchamiane wszystkie maszyny wirtualne pulpitu.

Postępuj zgodnie ze standardowymi rozwiązaniami w zakresie zabezpieczeń, aby zabezpieczyć wdrożenie programu Horizon:

* Zezwalaj tylko na ruch RDP dla komputerów stacjonarnych / ruch SSH na komputerach wirtualnych.
* Zezwalaj tylko na zarządzanie ruchem między siecią VLAN płaszczyzny zarządzania horizon a siecią VLAN z pulą komputerów stacjonarnych.
* Zezwalaj tylko na zarządzanie ruchem z sieci lokalnej.

Te najlepsze rozwiązania można wymusić, konfigurując [reguły zapory](firewall.md) z portalu CloudSimple.

#### <a name="cloudsimple-portal-configure-firewall-rules-to-secure-horizon-management-plane"></a>Portal CloudSimple: konfigurowanie reguł zapory w celu zabezpieczenia płaszczyzny zarządzania horizon

Skonfiguruj następujące reguły w portalu CloudSimple. Aby uzyskać instrukcje, zobacz [Konfigurowanie tabel i reguł zapory](firewall.md).

1. Skonfiguruj reguły zapory w zaporze CloudSimple N-S, aby umożliwić komunikację między podsieciami lokalnymi a siecią VLAN zarządzania horyzontem, tak aby dozwolone były tylko porty sieciowe wymienione na [liście portów programu Horizon](https://docs.vmware.com/en/VMware-Horizon-7/7.1/com.vmware.horizon-client-agent.security.doc/GUID-52807839-6BB0-4727-A9C7-EA73DE61ADAB.html) dokumentu VMware.

2. Tworzenie reguł zapory E-W między siecią VLAN zarządzania horyzontem a siecią VLAN w chmurze prywatnej.

#### <a name="cloudsimple-portal-create-a-public-ip-address-for-unified-access-gateway"></a>Portal CloudSimple: tworzenie publicznego adresu IP dla bramy ujednoliconego dostępu

Utwórz publiczny adres IP urządzenia Unified Access Gateway, aby włączyć połączenia klientów stacjonarnych z Internetu. Aby uzyskać instrukcje, zobacz [Przydzielanie publicznych adresów IP](public-ips.md).

Po zakończeniu instalacji publiczny adres IP jest przypisywany i wyświetlany na stronie Publiczne adresy IP.

#### <a name="cloudsimple-portal-escalate-privileges"></a>CloudSimple portal: eskalacji uprawnień

Domyślny użytkownik "cloudowner" nie ma wystarczających uprawnień w private cloud vCenter do zainstalowania programu Horizon, więc uprawnienia vCenter użytkownika muszą zostać eskalowane. Aby uzyskać więcej informacji, zobacz [Eskalacja uprawnień](escalate-private-cloud-privileges.md).

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-horizon-installation"></a>Interfejs użytkownika vCenter: tworzenie użytkownika w instalacji private cloud dla programu Horizon

1. Zaloguj się do vCenter przy użyciu poświadczeń użytkownika "cloudowner".
2. Utwórz nowego użytkownika , "horizon-soln-admin", w vCenter i dodaj użytkownika do grupy administratorów w vCenter.
3. Wyloguj się z vCenter jako użytkownik "cloudowner" i zaloguj się jako użytkownik "horizon-soln-admin".

#### <a name="vcenter-ui-install-vmware-horizon"></a>Interfejs vCenter: Instalowanie programu VMware Horizon

Jak wspomniano we wcześniejszej sekcji architektury logicznej, rozwiązanie Horizon ma następujące składniki:

* Widok Horyzont VMware
* Brama ujednoliconego dostępu VMware
* Menedżer woluminów aplikacji VMware
* Menedżer środowiska użytkownika VMware

Zainstalować komponenty w następujący sposób:

1. Zainstaluj i skonfiguruj Bramę Ujednoliconego Dostępu, postępując zgodnie z instrukcjami zawartymi w dokumencie VMware [Wdrażanie i konfigurowanie bramy ujednoliconego dostępu VMware](https://docs.vmware.com/en/Unified-Access-Gateway/3.3.1/com.vmware.uag-331-deploy-config.doc/GUID-F5CE0D5E-BE85-4FA5-BBCF-0F86C9AB8A70.html).

2. Zainstaluj program Horizon View w chmurze prywatnej, postępując zgodnie z instrukcjami zawartymi w [Przewodniku po instalacji widoku](https://docs.vmware.com/en/VMware-Horizon-7/7.4/horizon-installation/GUID-37D39B4F-5870-4188-8B11-B6C41AE9133C.html).

3. Zainstaluj Menedżera woluminów aplikacji, postępując zgodnie z instrukcjami zawartymi w [temacie Instalowanie i konfigurowanie woluminów aplikacji VMware](https://docs.vmware.com/en/VMware-App-Volumes/2.10/com.vmware.appvolumes.user.doc/GUID-5E8BAF8C-F5A6-412C-9424-266BA7109BA4.html).

4. Zainstaluj i skonfiguruj Menedżera środowiska użytkownika, postępując zgodnie z instrukcjami dotyczącymi [instalowania i konfigurowania menedżera środowiska użytkownika VMware](https://docs.vmware.com/en/VMware-User-Environment-Manager/9.4/com.vmware.user.environment.manager-install-config/GUID-DBBC82E4-483F-4B28-9D49-4D28E08715BC.html).

#### <a name="file-a-support-request-to-upload-vmware-horizon-pre-packaged-app-volumes"></a>Złóż żądanie pomocy technicznej w celu przesłania wstępnie spakowanych woluminów aplikacji VMware Horizon

W ramach procesu instalacji Menedżer woluminów aplikacji używa wstępnie spakowanych woluminów do aprowizowania stosów aplikacji i zapisywalnych woluminów. Woluminy te służą jako szablony stosów aplikacji i zapisywalnych woluminów.

Przekazywanie woluminów do magazynu danych private cloud wymaga hasła głównego ESXi. Aby uzyskać pomoc, prześlij [prośbę o pomoc](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Dołącz pakiet instalatora AppVolumes, aby personel pomocy technicznej CloudSimple mógł przesyłać szablony do środowiska chmury prywatnej.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>CloudSimple portal: De-eskalacji uprawnień

Teraz można [de-eskalacji uprawnień](escalate-private-cloud-privileges.md#de-escalate-privileges) użytkownika "cloudowner".

## <a name="ongoing-management-of-your-horizon-solution"></a>Bieżące zarządzanie rozwiązaniem Horizon

Masz pełną kontrolę nad oprogramowaniem Horizon i App Volume Manager w środowisku private cloud i oczekuje się, że wykona niezbędne zarządzanie cyklem życia oprogramowania. Przed aktualizacją lub uaktualnieniem programu Horizon lub App Volume upewnij się, że wszystkie nowe wersje oprogramowania są zgodne z usługami Private Cloud vCenter i PSC.

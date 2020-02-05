---
title: Azure VMware Solutions (Automatyczna synchronizacja) — Użyj funkcji automatycznej synchronizacji w chmurze prywatnej do hostowania infrastruktury pulpitów wirtualnych przy użyciu usługi VMware Horizon
description: Opisuje, jak można użyć witryny chmury prywatnej do automatycznej synchronizacji w celu hostowania infrastruktury pulpitu wirtualnego przy użyciu usługi VMware Horizon
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f909ce297344e80ed6004631d5218d6a7fcd5085
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025252"
---
# <a name="use-avs-private-cloud-site-to-host-a-virtual-desktop-infrastructure-using-vmware-horizon"></a>Używanie funkcji automatycznej synchronizacji w chmurze prywatnej w celu hostowania infrastruktury pulpitu wirtualnego przy użyciu usługi VMware Horizon

Do hostowania infrastruktury pulpitów wirtualnych (VDI) przy użyciu programu VMware Horizon 7. x możesz użyć witryny chmury prywatnej do automatycznej synchronizacji. Na poniższej ilustracji przedstawiono architekturę rozwiązania logicznego infrastruktury VDI.

![Wdrożenie horyzontu](media/horizon-deployment.png)

W tym rozwiązaniu masz pełną kontrolę nad menedżerem widoku horyzontu i woluminem aplikacji. Znane interfejs użytkownika, interfejs API i interfejsy interfejsu wiersza polecenia umożliwiają korzystanie z istniejących skryptów i narzędzi.

Rozwiązanie do automatycznej synchronizacji wymaga wykonania następujących czynności:

* Instalowanie, Konfigurowanie i zarządzanie programem VMware Horizon 7. x w chmurze prywatnej automatycznej synchronizacji.
* Udostępnianie własnych licencji dla zakresu.

## <a name="deploy-the-solution"></a>Wdrażanie rozwiązania

W poniższych sekcjach opisano sposób wdrażania rozwiązania VDI przy użyciu horyzontu w chmurze prywatnej automatycznej synchronizacji.

1. [Sprawdź, czy wersje produktów VMware są zgodne](#verify-that-vmware-product-versions-are-compatible)
2. [Oszacowanie rozmiaru środowiska pulpitu](#estimate-the-size-of-your-desktop-environment)
3. [Tworzenie chmury prywatnej do automatycznej synchronizacji dla środowiska](#create-an-avs-private-cloud-for-your-environment)
4. [Zainstaluj horyzont oprogramowania VMware w chmurze prywatnej automatycznej synchronizacji](#install-vmware-horizon-in-your-avs-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Sprawdź, czy wersje produktów VMware są zgodne

* Sprawdź, czy bieżące i planowane wersje horyzontu, woluminów aplikacji, ujednoliconej bramy dostępu i Menedżera środowiska użytkownika są zgodne ze sobą oraz z usługami vCenter i PSC w chmurze prywatnej automatycznej synchronizacji. Aby uzyskać informacje o zgodności, zobacz [macierz zgodności programu VMware dla zakresu 7,5](https://www.vmware.com/resources/compatibility/sim/interop_matrix.php#interop&260=2877&0=).
* Aby dowiedzieć się, jakie są bieżące wersje programu vCenter i PSC w chmurze prywatnej automatycznej synchronizacji, przejdź do obszaru **zasoby** w [portalu automatycznej synchronizacji](access-cloudsimple-portal.md), wybierz chmurę prywatną do automatycznej synchronizacji i kliknij kartę **vSphere Management Network** .

![wersje vCenter i PSC](media/private-cloud-vsphere-versions.png)

### <a name="estimate-the-size-of-your-desktop-environment"></a>Oszacowanie rozmiaru środowiska pulpitu

* Sprawdź, czy zidentyfikowana konfiguracja mieści się w ramach limitów operacyjnych programu VMware.
* Oszacuj zasoby, które są zbędne dla wszystkich pulpitów i składników zarządzania horyzontem.

### <a name="create-an-avs-private-cloud-for-your-environment"></a>Tworzenie chmury prywatnej do automatycznej synchronizacji dla środowiska

1. Utwórz chmurę prywatną w wersji zaciągającej za pomocą portalu automatycznej konfiguracji, postępując zgodnie z instrukcjami w temacie [Konfigurowanie środowiska chmury prywatnej do automatycznej synchronizacji](quickstart-create-private-cloud.md). Funkcja automatycznej synchronizacji tworzy domyślnego użytkownika programu vCenter o nazwie "cloudowner" we wszystkich nowo utworzonych chmurach prywatnych. Aby uzyskać szczegółowe informacje na temat domyślnej automatycznej synchronizacji użytkownika i modelu uprawnień chmury prywatnej, zobacz [Omówienie modelu uprawnień do automatycznej synchronizacji w chmurze](learn-private-cloud-permissions.md).
2. Utwórz sieć VLAN w chmurze prywatnej automatycznej synchronizacji na potrzeby płaszczyzny zarządzania horyzontem i przypisz ją do podsieci CIDR. Aby uzyskać instrukcje, zobacz [Tworzenie sieci VLAN i podsieci oraz zarządzanie nimi](create-vlan-subnet.md). Jest to sieć, w której zostaną zainstalowane wszystkie składniki rozwiązania (ujednolicona Brama dostępu, serwer połączenia, serwer woluminu aplikacji i serwery Menedżera środowiska użytkownika).
3. Zdecyduj, czy chcesz użyć zewnętrznego dostawcy tożsamości w ramach automatycznej synchronizacji chmury prywatnej programu vCenter. Jeśli tak, wybierz jedną z następujących opcji:
    * Użyj Active Directory lokalnego jako zewnętrzny dostawca tożsamości. Aby uzyskać instrukcje, zobacz [źródła tożsamości vCenter](set-vcenter-identity.md).
    * Skonfiguruj serwer Active Directory w chmurze prywatnej do automatycznej synchronizacji w płaszczyźnie zarządzania horyzontem VLAN, która ma być używana jako zewnętrzny dostawca tożsamości. Aby uzyskać instrukcje, zobacz [źródła tożsamości vCenter](set-vcenter-identity.md).
    * Skonfiguruj serwer DHCP i DNS w płaszczyźnie zarządzania horyzontem sieci VLAN w chmurze prywatnej automatycznej synchronizacji. Aby uzyskać instrukcje, zobacz [Konfigurowanie aplikacji DNS i DHCP oraz obciążeń w chmurze prywatnej automatycznej synchronizacji](dns-dhcp-setup.md).
4. Skonfiguruj przekazywanie DNS na serwerze DNS zainstalowanym w chmurze prywatnej automatycznej synchronizacji. Aby uzyskać instrukcje, zobacz [Tworzenie usługi przesyłania dalej warunkowej](on-premises-dns-setup.md#create-a-conditional-forwarder).

### <a name="install-vmware-horizon-in-your-avs-private-cloud"></a>Zainstaluj horyzont oprogramowania VMware w chmurze prywatnej automatycznej synchronizacji

Na poniższym diagramie wdrożenia przedstawiono rozwiązanie horyzontu wdrożone w chmurze prywatnej automatycznej wersji. Ujednolicona Brama dostępu, usługi AD/DC, widok i serwer woluminu aplikacji są instalowane w sieci VLAN utworzonej przez użytkownika 234. Ujednolicona Brama dostępu ma przypisany publiczny adres IP, który jest dostępny z Internetu. Maszyny wirtualne puli pulpitów są wdrażane w sieci VLAN 235, aby zapewnić dodatkową izolację i bezpieczeństwo.

![Wdrożenie horyzontu w chmurze prywatnej automatycznej synchronizacji](media/horizon-private-cloud.png)

Poniższe sekcje zawierają instrukcje dotyczące konfigurowania wdrożenia podobnego do przedstawionego na rysunku. Przed rozpoczęciem upewnij się, że masz następujące elementy:

* Chmura prywatna o automatycznej synchronizacji utworzona przy użyciu portalu automatycznej synchronizacji z wystarczającą pojemnością do uruchamiania pul pulpitów.
* Wystarczająca przepustowość między środowiskiem lokalnym i środowiskiem chmury prywatnej do automatycznej synchronizacji w celu obsługi ruchu sieciowego dla komputerów stacjonarnych.
* Tunel sieci VPN typu lokacja-lokacja jest skonfigurowany między lokalnym centrum danych a chmurą prywatną do automatycznej synchronizacji.
* Dostępność adresów IP z podsieci użytkowników końcowych w środowisku lokalnym w podsieciach chmury prywatnej automatycznej synchronizacji.
* Usługi AD/DHCP/DNS zainstalowane w chmurze prywatnej synchronizacji.

#### <a name="avs-portal-create-a-dedicated-vlansubnet-for-desktop-pools"></a>Portal automatycznej synchronizacji: Tworzenie dedykowanej sieci VLAN/podsieci dla pul pulpitów

Utwórz sieć VLAN dla pul stacjonarnych horyzontów i przypisz ją do podsieci CIDR. Aby uzyskać instrukcje, zobacz [Tworzenie sieci VLAN i podsieci oraz zarządzanie nimi](create-vlan-subnet.md). Jest to sieć, w której będą uruchamiane wszystkie maszyny wirtualne.

Przestrzegaj standardowych najlepszych rozwiązań w zakresie zabezpieczeń w celu zabezpieczenia wdrożenia w ramach planu:

* Zezwalaj tylko na ruch RDP na pulpicie/ruch SSH do maszyn wirtualnych.
* Zezwalaj tylko na ruch związany z zarządzaniem między płaszczyzną zarządzania horyzontem VLAN a siecią VLAN puli pulpitów.
* Zezwalaj tylko na ruch związany z zarządzaniem z sieci lokalnej.

Te najlepsze rozwiązania można wymusić, konfigurując [reguły zapory](firewall.md) w portalu automatycznej konfiguracji.

#### <a name="avs-portal-configure-firewall-rules-to-secure-horizon-management-plane"></a>Portal automatycznej synchronizacji: Konfigurowanie reguł zapory na potrzeby ochrony płaszczyzny zarządzania

Skonfiguruj poniższe reguły w portalu automatycznej konfiguracji. Aby uzyskać instrukcje, zobacz [Konfigurowanie tabel i reguł zapory](firewall.md).

1. Skonfiguruj reguły zapory w zaporze "Automatyczna synchronizacja N-S", aby umożliwić komunikację między podsieciami lokalnymi i siecią VLAN zarządzania horyzontem, dzięki czemu dozwolone są tylko porty sieciowe wymienione na [liście portów horyzontu](https://docs.vmware.com/en/VMware-Horizon-7/7.1/com.vmware.horizon-client-agent.security.doc/GUID-52807839-6BB0-4727-A9C7-EA73DE61ADAB.html) dokumentów programu VMware.

2. Utwórz reguły zapory elektronicznej między siecią VLAN zarządzania horyzontem a siecią VLAN w chmurze prywatnej automatycznej synchronizacji.

#### <a name="avs-portal-create-a-public-ip-address-for-unified-access-gateway"></a>Portal automatycznej synchronizacji: Tworzenie publicznego adresu IP dla ujednoliconej bramy dostępu

Utwórz publiczny adres IP dla urządzenia bramy dostępu ujednoliconego, aby włączyć połączenia klientów klasycznych z Internetu. Aby uzyskać instrukcje, zobacz [przydzielanie publicznych adresów IP](public-ips.md).

Po zakończeniu instalacji, publiczny adres IP jest przypisywany i wyświetlany na liście na stronie publiczne adresy IPv4.

#### <a name="avs-portal-escalate-privileges"></a>Portal automatycznej synchronizacji: eskalacja uprawnień

Domyślny użytkownik "cloudowner" nie ma wystarczających uprawnień w chmurze prywatnej chmury programu vCenter w celu zainstalowania horyzontu, więc uprawnienia użytkownika vCenter muszą zostać przeeskalacjne. Aby uzyskać więcej informacji, zobacz temat [Eskalacja uprawnień](escalate-private-cloud-privileges.md).

#### <a name="vcenter-ui-create-a-user-in-avs-private-cloud-for-horizon-installation"></a>Interfejs użytkownika programu vCenter: Tworzenie użytkownika w ramach automatycznej synchronizacji w chmurze prywatnej na potrzeby instalacji z zakresu

1. Zaloguj się do programu vCenter przy użyciu poświadczeń użytkownika "cloudowner".
2. Utwórz nowego użytkownika "Horyzont-Soln-admin" w programie vCenter i Dodaj użytkownika do grupy Administratorzy w programie vCenter.
3. Wyloguj się z programu vCenter jako użytkownika "cloudowner" i zaloguj się jako użytkownik "Horizon-Soln-admin".

#### <a name="vcenter-ui-install-vmware-horizon"></a>Interfejs użytkownika vCenter: Instalowanie zakresu VMware

Jak wspomniano we wcześniejszej części architektury logicznej, rozwiązanie ma następujące składniki:

* Widok horyzontu VMware
* Ujednolicona Brama dostępu VMware
* Menedżer woluminów aplikacji VMware
* Menedżer środowiska użytkownika programu VMware

Zainstaluj składniki w następujący sposób:

1. Zainstaluj i skonfiguruj ujednoliconą bramę dostępu, postępując zgodnie z instrukcjami zawartymi w dokumencie VMware [wdrażanie i Konfigurowanie bramy VMware Unified Access Gateway](https://docs.vmware.com/en/Unified-Access-Gateway/3.3.1/com.vmware.uag-331-deploy-config.doc/GUID-F5CE0D5E-BE85-4FA5-BBCF-0F86C9AB8A70.html).

2. Zainstaluj widok horyzontów w chmurze prywatnej automatycznej wersji zapoznawczej, postępując zgodnie z instrukcjami wyświetlanymi w [przewodniku instalacji](https://docs.vmware.com/en/VMware-Horizon-7/7.4/horizon-installation/GUID-37D39B4F-5870-4188-8B11-B6C41AE9133C.html).

3. Zainstaluj program App Volume Manager, postępując zgodnie z instrukcjami w temacie [Instalowanie i Konfigurowanie woluminów aplikacji VMware](https://docs.vmware.com/en/VMware-App-Volumes/2.10/com.vmware.appvolumes.user.doc/GUID-5E8BAF8C-F5A6-412C-9424-266BA7109BA4.html).

4. Zainstaluj i skonfiguruj Menedżera środowiska użytkownika, postępując zgodnie z instrukcjami w [temacie Informacje o instalowaniu i konfigurowaniu Menedżera środowiska użytkownika programu VMware](https://docs.vmware.com/en/VMware-User-Environment-Manager/9.4/com.vmware.user.environment.manager-install-config/GUID-DBBC82E4-483F-4B28-9D49-4D28E08715BC.html).

#### <a name="file-a-support-request-to-upload-vmware-horizon-pre-packaged-app-volumes"></a>Prześlij żądanie pomocy technicznej w celu przeładowania wstępnie spakowanych woluminów aplikacji programu VMware Horizon

W ramach procesu instalacji program App Volume Manager używa wstępnie spakowanych woluminów do udostępniania stosów aplikacji i zapisywalnych woluminów. Te woluminy służą jako szablony dla stosów aplikacji i zapisywalnych woluminów.

Przekazywanie woluminów do magazynu danych chmury prywatnej w chmurze automatycznej wymaga hasła głównego ESXi. Aby uzyskać pomoc, Prześlij [żądanie pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Dołącz pakiet Instalatora AppVolumes, dzięki czemu personel pomocy technicznej automatycznej synchronizacji może przekazać szablony do środowiska chmury prywatnej do automatycznej synchronizacji.

#### <a name="avs-portal-de-escalate-privileges"></a>Portal automatycznej synchronizacji: anulowanie eskalacji uprawnień

Teraz można [cofnąć eskalację uprawnień](escalate-private-cloud-privileges.md#de-escalate-privileges) użytkownika "cloudowner".

## <a name="ongoing-management-of-your-horizon-solution"></a>Ciągłe zarządzanie rozwiązaniem horyzontu

Masz pełną kontrolę nad oprogramowaniem usługi Volume and App Manager w środowisku chmury prywatnej do automatycznej synchronizacji i oczekujesz, że wymagane jest zarządzanie cyklem życia oprogramowania. Upewnij się, że wszystkie nowe wersje oprogramowania są zgodne z funkcją automatycznej synchronizacji chmurowej, vCenter i PSC przed aktualizacją lub aktualizacją zakresu lub woluminu aplikacji.

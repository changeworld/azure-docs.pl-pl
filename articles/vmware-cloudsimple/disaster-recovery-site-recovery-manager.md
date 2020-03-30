---
title: Rozwiązanie Azure VMware firmy CloudSimple — konfigurowanie chmury prywatnej jako lokacji odzyskiwania po awarii przy użyciu menedżera odzyskiwania witryn VMware
description: W tym artykule opisano, jak skonfigurować chmurę CloudSimple Private Cloud jako lokację odzyskiwania po awarii dla lokalnych obciążeń VMware
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fa8b2da683d68a337df38e13726f22c5af43540a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565931"
---
# <a name="set-up-private-cloud-as-a-disaster-recovery-target-with-vmware-site-recovery-manager"></a>Konfigurowanie chmury prywatnej jako celu odzyskiwania po awarii za pomocą menedżera odzyskiwania witryn VMware

CloudSimple Private Cloud można używać jako lokacji odzyskiwania po awarii (DR) dla lokalnych obciążeń VMware.

Rozwiązanie odzyskiwania po awarii jest oparte na vSphere Replication i VMware Site Recovery Manager (SRM). Podobne podejście można stosować, aby włączyć private cloud jako lokację główną, która jest chroniona przez lokalną lokację odzyskiwania.

Rozwiązanie CloudSimple:

* Eliminuje konieczność konfigurowania centrum danych specjalnie dla odzyskiwania po awarii.
* Umożliwia korzystanie z lokalizacji platformy Azure, w których cloudsimple jest wdrażany dla odporności geograficznej na całym świecie.
* Umożliwia obniżenie kosztów wdrożenia i całkowity koszt posiadania w celu ustanowienia odzyskiwania po awarii.

Rozwiązanie CloudSimple wymaga wykonania następujących czynności:

* Zainstaluj, skonfiguruj i zarządzaj replikacją vSphere i srm w chmurze prywatnej.
* Podaj własne licencje na srm, gdy private cloud jest witryną chronioną. Nie potrzebujesz żadnych dodatkowych licencji SRM dla witryny CloudSimple, gdy jest używana jako lokacja odzyskiwania.

Dzięki temu rozwiązaniu masz pełną kontrolę nad replikacją vSphere i SRM. Znane interfejsy interfejsu użytkownika, interfejsu API i interfejsu wiersza polecenia umożliwiają korzystanie z istniejących skryptów i narzędzi.

![Wdrożenie menedżera odzyskiwania lokacji](media/srm-deployment.png)

Można użyć dowolnej wersji vRA i SRM, które są zgodne z usługą Private Cloud i środowiskami lokalnymi. Przykłady w tym przewodniku użyć vRA 6.5 i SRM 6.5. Te wersje są kompatybilne z vSphere 6.5, który jest obsługiwany przez CloudSimple.

## <a name="deploy-the-solution"></a>Wdrażanie rozwiązania

W poniższych sekcjach opisano sposób wdrażania rozwiązania odzyskiwania po awarii przy użyciu usługi SRM w chmurze prywatnej.

1. [Sprawdź, czy wersje produktów VMware są zgodne](#verify-that-vmware-product-versions-are-compatible)
2. [Oszacuj rozmiar środowiska odzyskiwania po awarii](#estimate-the-size-of-your-dr-environment)
3. [Tworzenie chmury prywatnej dla swojego środowiska](#create-a-private-cloud-for-your-environment)
4. [Konfigurowanie sieci w chmurze prywatnej dla rozwiązania SRM](#set-up-private-cloud-networking-for-the-srm-solution)
5. [Konfigurowanie połączenia sieci VPN między lokacją a siecią lokalną a chmurą prywatną oraz otwieranie wymaganych portów](#set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports)
6. [Konfigurowanie usług infrastrukturalnych w chmurze prywatnej](#set-up-infrastructure-services-in-your-private-cloud)
7. [Instalowanie urządzenia vSphere Replication w środowisku lokalnym](#install-vsphere-replication-appliance-in-your-on-premises-environment)
8. [Instalowanie urządzenia vSphere Replication w środowisku chmury prywatnej](#install-vsphere-replication-appliance-in-your-private-cloud-environment)
9. [Instalowanie serwera SRM w środowisku lokalnym](#install-srm-server-in-your-on-premises-environment)
10. [Instalowanie serwera SRM w chmurze prywatnej](#install-srm-server-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Sprawdź, czy wersje produktów VMware są zgodne

Konfiguracje w tym przewodniku podlegają następującym wymaganiom zgodności:

* Ta sama wersja usługi SRM musi zostać wdrożona w chmurze prywatnej i w środowisku lokalnym.
* Ta sama wersja replikacji vSphere musi być wdrożona w chmurze prywatnej i w środowisku lokalnym.
* Wersje kontrolera usług platformy (PSC) w chmurze prywatnej i środowisku lokalnym muszą być zgodne.
* Wersje vCenter w chmurze prywatnej i środowisku lokalnym muszą być zgodne.
* Wersje replikacji SRM i vSphere muszą być kompatybilne ze sobą oraz z wersjami PSC i vCenter.

Aby uzyskać łącza do odpowiedniej dokumentacji vmware i informacji o zgodności, przejdź do dokumentacji [programu VMware Site Recovery Manager.](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)

Aby dowiedzieć się, wersje vCenter i PSC w chmurze prywatnej, otwórz portal CloudSimple. Przejdź do **aplikacji Zasoby**, wybierz chmurę prywatną i kliknij kartę **vSphere Management Network.**

![vCenter & wersji PSC w chmurze prywatnej](media/srm-resources.png)

### <a name="estimate-the-size-of-your-dr-environment"></a>Oszacuj rozmiar środowiska odzyskiwania po awarii

1. Sprawdź, czy zidentyfikowana konfiguracja lokalna mieści się w obsługiwanych granicach. W przypadku SRM 6.5 limity są udokumentowane w artykule bazy wiedzy VMware na temat [limitów operacyjnych dla programu Site Recovery Manager 6.5](https://kb.vmware.com/s/article/2147110).
2. Upewnij się, że masz wystarczającą przepustowość sieci, aby spełnić wymagania dotyczące rozmiaru obciążenia i celu administracyjnego. Artykuł z bazy wiedzy VMware dotyczący [obliczania wymagań dotyczących przepustowości dla replikacji vSphere](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html) zawiera wskazówki dotyczące limitów przepustowości.
3. Użyj narzędzia CloudSimple sizer do oszacowania zasobów, które są potrzebne w witrynie odzyskiwania po awarii, aby chronić środowisko lokalne.

### <a name="create-a-private-cloud-for-your-environment"></a>Tworzenie chmury prywatnej dla swojego środowiska

Utwórz chmurę prywatną z portalu CloudSimple, postępując zgodnie z instrukcjami i zaleceniami dotyczącymi rozmiaru w obszarze [Tworzenie chmury prywatnej.](create-private-cloud.md)

### <a name="set-up-private-cloud-networking-for-the-srm-solution"></a>Konfigurowanie sieci w chmurze prywatnej dla rozwiązania SRM

Uzyskaj dostęp do portalu CloudSimple, aby skonfigurować sieć private cloud dla rozwiązania SRM.

Utwórz sieć VLAN dla sieci rozwiązań SRM i przypisz jej cidr podsieci. Aby uzyskać instrukcje, zobacz [Tworzenie sieci/podsieci i zarządzanie nimi](create-vlan-subnet.md).

### <a name="set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports"></a>Konfigurowanie połączenia sieci VPN między lokacją a siecią lokalną a chmurą prywatną oraz otwieranie wymaganych portów

Aby skonfigurować łączność między lokacją między siecią lokalną a chmurą prywatną. Aby uzyskać instrukcje, zobacz [Konfigurowanie połączenia sieci VPN z chmurą cloudsimple private cloud](set-up-vpn.md).

### <a name="set-up-infrastructure-services-in-your-private-cloud"></a>Konfigurowanie usług infrastrukturalnych w chmurze prywatnej

Skonfiguruj usługi infrastruktury w chmurze prywatnej, aby ułatwić zarządzanie obciążeniami i narzędziami.

Możesz dodać zewnętrznego dostawcę tożsamości zgodnie z opisem w [użyj usługi Azure AD jako dostawcy tożsamości dla vCenter w chmurze prywatnej CloudSimple,](azure-ad.md) jeśli chcesz wykonać dowolną z następujących czynności:

* Identyfikowanie użytkowników z lokalnej usługi Active Directory (AD) w chmurze prywatnej.
* Skonfiguruj usługę AD w chmurze prywatnej dla wszystkich użytkowników.
* Użyj usługi Azure AD.

Aby zapewnić usługi wyszukiwania adresów IP, zarządzania adresami IP i rozpoznawania nazw dla obciążeń w chmurze prywatnej, skonfiguruj serwer DHCP i DNS zgodnie z opisem w [aplikacji i obciążeniach DNS i DHCP w chmurze CloudSimple Private Cloud](dns-dhcp-setup.md).

Domena *.cloudsimple.io jest używana przez maszyny wirtualne do zarządzania i hosty w chmurze prywatnej. Aby rozpoznać żądania do tej domeny, należy skonfigurować przekazywanie dns na serwerze DNS zgodnie z opisem w [temacie Tworzenie usługi przesyłania dalej warunkowej](on-premises-dns-setup.md#create-a-conditional-forwarder).

### <a name="install-vsphere-replication-appliance-in-your-on-premises-environment"></a>Instalowanie urządzenia replikacji vSphere w środowisku lokalnym

Zainstaluj vSphere Replication Appliance (vRA) w środowisku lokalnym, postępującym zgodnie z dokumentacją VMware. Instalacja składa się z tych kroków wysokiego poziomu:

1. Przygotuj środowisko lokalne do instalacji vRA.

2. Wdrażanie vRA w środowisku lokalnym przy użyciu OVF w środowisku VR ISO od vmware.com. Dla vRA 6.5, [ten blog VMware](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) ma istotne informacje.

3. Zarejestruj lokalną umowę vRA za pomocą logowania pojedynczego vCenter w lokacji lokalnej. Szczegółowe instrukcje dotyczące replikacji vSphere 6.5 można znaleźć w dokumencie [VMware VMware vSphere Replication 6.5 Instalacja i konfiguracja](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

## <a name="install-vsphere-replication-appliance-in-your-private-cloud-environment"></a>Instalowanie urządzenia vSphere Replication w środowisku chmury prywatnej

Przed rozpoczęciem sprawdź, czy masz następujące elementy:

* Osiągalność adresów IP z podsieci w środowisku lokalnym do podsieci zarządzania w chmurze prywatnej
* Osiągalność adresu IP z podsieci replikacji w lokalnym środowisku vSphere do podsieci rozwiązania SRM w chmurze prywatnej

Aby uzyskać instrukcje, zobacz [Konfigurowanie połączenia sieci VPN z chmurą cloudsimple private cloud](set-up-vpn.md). Kroki są podobne do tych dla instalacji lokalnej.

CloudSimple zaleca używanie sieci FQDN zamiast adresów IP podczas instalacji vRA i SRM. Aby dowiedzieć się, że sieć FQDN vCenter i PSC znajduje się w chmurze prywatnej, otwórz portal CloudSimple. Przejdź do **aplikacji Zasoby**, wybierz chmurę prywatną i kliknij kartę **vSphere Management Network.**

![Znajdowanie FQDN vCenter/PSC w chmurze prywatnej](media/srm-resources.png)

CloudSimple wymaga, aby nie instalować vRA i SRM przy użyciu domyślnego użytkownika "cloudowner", ale zamiast tego utworzyć nowego użytkownika. Ma to na celu zapewnienie wysokiej dostępności i dostępności środowiska private cloud vCenter. Jednak domyślny użytkownik cloudowner w private cloud vCenter nie ma wystarczających uprawnień do tworzenia nowego użytkownika z uprawnieniami administracyjnymi.

Przed zainstalowaniem vRA i SRM należy eskalować uprawnienia vCenter użytkownika cloudowner, a następnie utworzyć użytkownika z uprawnieniami administracyjnymi w domenie SSO vCenter. Aby uzyskać szczegółowe informacje na temat domyślnego modelu uprawnień użytkownika i uprawnienia private cloud, zobacz [Poznaj model uprawnień private cloud](learn-private-cloud-permissions.md).

Instalacja składa się z tych kroków wysokiego poziomu:

1. [Eskalacja uprawnień](escalate-private-cloud-privileges.md).
2. Utwórz użytkownika w chmurze prywatnej dla replikacji vSphere i instalacji SRM. Wyjaśniono poniżej w [interfejsie użytkownika vCenter: Tworzenie użytkownika w chmurze prywatnej dla instalacji SRM & vRA](#vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation).
3. Przygotuj swoje środowisko private cloud do instalacji vRA.
4. Wdrażaj vRA w chmurze prywatnej, korzystając z OVF w środowisku VR ISO od vmware.com. Dla vRA 6.5, [ten blog VMware](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) ma istotne informacje.
5. Konfigurowanie reguł zapory dla vRA. Wyjaśniono poniżej w [portalu CloudSimple: Konfigurowanie reguł zapory dla vRA](#cloudsimple-portal-configure-firewall-rules-for-vra).
6. Zarejestruj private cloud vRA za pomocą vCenter single sign-on w witrynie private cloud.
7. Skonfiguruj połączenia replikacji vSphere między tymi dwoma urządzeniami. Upewnij się, że wymagane porty są otwarte w zapory. Zobacz [ten artykuł bazy wiedzy VMware,](https://kb.vmware.com/s/article/2087769) aby uzyskać listę numerów portów, które muszą być otwarte dla replikacji vSphere 6.5.

Szczegółowe instrukcje instalacji replikacji vSphere 6.5 można znaleźć w dokumencie [VMware vSphere Replication 6.5 Instalacja i konfiguracja](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation"></a>Interfejs użytkownika vCenter: Tworzenie użytkownika w chmurze prywatnej dla instalacji vRA i SRM

Zaloguj się do vCenter przy użyciu poświadczeń użytkownika cloudowner po eskalacji uprawnień z portalu CloudSimple.

Utwórz nowego `srm-soln-admin`użytkownika w vCenter i dodaj go do grupy administratorów w vCenter.
Wyloguj się z vCenter jako użytkownik cloudowner i zaloguj się jako użytkownik *srm-soln-admin.*

#### <a name="cloudsimple-portal-configure-firewall-rules-for-vra"></a>Portal CloudSimple: konfigurowanie reguł zapory dla platformy vRA

Skonfiguruj reguły zapory zgodnie z opisem w [temacie Konfigurowanie tabel i reguł zapory](firewall.md) w celu otwierania portów w celu umożliwienia komunikacji między:

* vRA w sieci rozwiązań SRM oraz vCenter i ESXi hostuje w sieci zarządzania.
* urządzeń vRA w obu lokalizacjach.

Zobacz ten [artykuł bazy wiedzy VMware,](https://kb.vmware.com/s/article/2087769) aby uzyskać listę numerów portów, które muszą być otwarte dla replikacji vSphere 6.5.

### <a name="install-srm-server-in-your-on-premises-environment"></a>Instalowanie serwera SRM w środowisku lokalnym

Przed rozpoczęciem sprawdź następujące kwestie:

* Urządzenie replikacji vSphere jest instalowane w środowiskach lokalnych i prywatnych w chmurze.
* Urządzenia replikacji vSphere w obu lokacjach są ze sobą połączone.
* Zapoznałeś się z informacjami vmware na temat wymagań wstępnych i najlepszych rozwiązań. W przypadku usługi SRM 6.5 można zapoznać się z wymaganiami wstępnymi i najlepszymi praktykami dotyczących dokumentu VMware dla [usługi SRM 6.5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html).

Postępuj zgodnie z dokumentacją VMware, aby wykonać instalację serwera SRM w modelu wdrażania "Topologia dwóch lokacji z jednym wystąpieniem vCenter na kontroler usług platformy", jak opisano w tym [dokumencie VMWare](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). Instrukcje instalacji srm 6.5 są dostępne w dokumencie VMware [Instalowanie Menedżera odzyskiwania witryny](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

### <a name="install-srm-server-in-your-private-cloud"></a>Instalowanie serwera SRM w chmurze prywatnej

Przed rozpoczęciem sprawdź następujące kwestie:

* Urządzenie replikacji vSphere jest instalowane w środowiskach lokalnych i prywatnych w chmurze.
* Urządzenia replikacji vSphere w obu lokacjach są ze sobą połączone.
* Zapoznałeś się z informacjami vmware na temat wymagań wstępnych i najlepszych rozwiązań. W przypadku usługi SRM 6.5 można zapoznać się [z wymaganiami wstępnymi i najlepszymi praktykami dotyczących instalacji serwera Programu Site Recovery Manager 6.5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html).

W poniższych krokach opisano instalację SRM usługi Private Cloud.

1. [Interfejs użytkownika vCenter: Zainstaluj usługę SRM](#vcenter-ui-install-srm)
2. [Portal CloudSimple: konfigurowanie reguł zapory dla usługi SRM](#cloudsimple-portal-configure-firewall-rules-for-srm)
3. [Interfejs użytkownika vCenter: Konfigurowanie srm](#vcenter-ui-configure-srm)
4. [CloudSimple portal: de-eskalacji uprawnień](#cloudsimple-portal-de-escalate-privileges)

#### <a name="vcenter-ui-install-srm"></a>Interfejs użytkownika vCenter: Zainstaluj usługę SRM

Po zalogowaniu się do vCenter przy użyciu poświadczeń srm-soln-admin, postępuj zgodnie z dokumentacją VMware, aby wykonać instalację serwera SRM w modelu wdrażania "Topologia dwóch lokacji z jednym wystąpieniem vCenter na kontroler usług platformy", jak opisano w tym [dokumencie VMWare](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). Instrukcje instalacji srm 6.5 są dostępne w dokumencie VMware [Instalowanie Menedżera odzyskiwania witryny](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

#### <a name="cloudsimple-portal-configure-firewall-rules-for-srm"></a>Portal CloudSimple: konfigurowanie reguł zapory dla usługi SRM

Skonfiguruj reguły zapory zgodnie z opisem w [temacie Konfigurowanie tabel i reguł zapory,](firewall.md) aby umożliwić komunikację między:

Serwer SRM i vCenter / PSC w chmurze prywatnej.
Serwery SRM w obu lokacjach

Zobacz [ten artykuł bazy wiedzy VMware,](https://kb.vmware.com/s/article/2087769) aby uzyskać listę numerów portów, które muszą być otwarte dla replikacji vSphere 6.5.

#### <a name="vcenter-ui-configure-srm"></a>Interfejs użytkownika vCenter: Konfigurowanie srm

Po zainstalowaniu srm w chmurze prywatnej, wykonaj następujące zadania, jak opisano w sekcjach VMware Site Recovery Manager Instalacji i konfiguracji Przewodnik. W przypadku usługi SRM 6.5 instrukcje są dostępne w dokumencie VMware [Instalowanie Menedżera odzyskiwania witryny](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

1. Łączenie wystąpień serwera Menedżera odzyskiwania witryn w witrynach chronionych i odzyskiwania.
2. Ustanawianie połączenia klienta z wystąpieniem serwera Menedżera odzyskiwania lokacji zdalnej.
3. Zainstaluj klucz licencyjny Menedżera odzyskiwania witryny.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>CloudSimple portal: De-eskalacji uprawnień

Aby deeskalować uprawnienia, zobacz [De-eskalacji uprawnień](escalate-private-cloud-privileges.md#de-escalate-privileges).

## <a name="ongoing-management-of-your-srm-solution"></a>Bieżące zarządzanie rozwiązaniem SRM

Masz pełną kontrolę nad oprogramowaniem vSphere Replication i SRM w środowisku private cloud i oczekuje się, że wykona niezbędne zarządzanie cyklem życia oprogramowania. Przed aktualizacją lub uaktualnieniem usługi vSphere Replication lub SRM upewnij się, że każda nowa wersja oprogramowania jest zgodna z programem Private Cloud vCenter i PSC.

> [!NOTE]
> CloudSimple obecnie bada opcje oferowania zarządzanej usługi ODZYSKIWANIA PO. 

## <a name="multiple-replication-configuration"></a>Konfiguracja wielu replikacji

 [Zarówno oparte na tablicy technologii replikacji i vSphere mogą być używane razem z SRM](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication) w tym samym czasie. Jednak muszą być stosowane do oddzielnego zestawu maszyn wirtualnych (dana maszyna wirtualna może być chroniona przez replikację opartą na tablicy lub replikację vSphere, ale nie obie). Ponadto witrynę CloudSimple można skonfigurować jako lokację odzyskiwania dla wielu chronionych witryn. Informacje na temat konfiguracji wielu lokacji można znaleźć w witrynie [SRM Multi-Site Options.](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite/)

## <a name="references"></a>Dokumentacja

* [Dokumentacja Menedżera odzyskiwania witryny VMware](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
* [Limity operacyjne dla programu Site Recovery Manager 6.5](https://kb.vmware.com/s/article/2147110)
* [Obliczanie wymagań dotyczących przepustowości dla replikacji vSphere](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)
* [Wybór OVF podczas wdrażania replikacji vSphere 6.5](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices/)
* [VMware vSphere Replication 6.5 Instalacja i konfiguracja](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)
* [Wymagania wstępne i najważniejsze wskazówki dotyczące SRM 6.5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
* [Menedżer odzyskiwania lokacji w topologii dwustanowiskowej z jednym wystąpieniem serwera vCenter na kontroler usług platformy](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)
* [Przewodnik instalacji i konfiguracji programu VMware Site Recovery Manager 6.5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)
* [Blog VMware na SRM z replikacją opartą na macierzy a replikacją vSphere](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)
* [Blog VMware na temat opcji SRM dla wielu witryn](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite)
* [Numery portów, które muszą być otwarte dla replikacji vSphere 5.8.x, 6.x i 8](https://kb.vmware.com/s/article/2147112)

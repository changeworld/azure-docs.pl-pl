---
title: Sap HANA skalowanie w poziomie ze stałą zgodą z usługą Azure NetApp Files na RHEL| Dokumenty firmy Microsoft
description: Przewodnik po wysokiej dostępności dla sap NetWeaver w systemie Red Hat Enterprise Linux z plikami NetApp azure dla aplikacji SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/06/2020
ms.author: radeltch
ms.openlocfilehash: 78f60cbd20e7801f4af26372884275d7d6782777
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754152"
---
# <a name="deploy-a-sap-hana-scale-out-system-with-standby-node-on-azure-vms-by-using-azure-netapp-files-on-red-hat-enterprise-linux"></a>Wdrażanie systemu skalowania w poziomie SAP HANA z węzłem wstrzymania na maszynach wirtualnych platformy Azure przy użyciu plików NetApp azure w systemie Red Hat Enterprise Linux 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]:https://launchpad.support.sap.com/#/notes/1900823
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2593824]:https://launchpad.support.sap.com/#/notes/2455582
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


W tym artykule opisano sposób wdrażania systemu SAP HANA o wysokiej dostępności w konfiguracji skalowawczej w poziomie ze stanem wstrzymania na maszynach wirtualnych (VMs) usługi Azure Red Hat Enterprise Enterprise, przy użyciu [plików NetApp usługi Azure](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/) dla udostępnionych woluminów magazynu.  

W przykładowych konfiguracjach, poleceniach instalacji i tak dalej, wystąpienie HANA wynosi **03,** a identyfikator systemu HANA to **HN1**. Przykłady są oparte na HANA 2.0 SP4 i Red Hat Enterprise Linux dla SAP 7.6. 

Przed rozpoczęciem należy zapoznać się z następującymi uwagami i dokumentami SAP:

* [Dokumentacja plików netapp platformy Azure][anf-azure-doc] 
* Uwaga SAP [1928533] zawiera:  
  * Lista rozmiarów maszyn wirtualnych platformy Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP
  * Ważne informacje o pojemności dla rozmiarów maszyn wirtualnych platformy Azure
  * Obsługiwane oprogramowanie SAP oraz system operacyjny (OS) i kombinacje baz danych
  * Wymagana wersja jądra SAP dla systemów Windows i Linux na platformie Microsoft Azure
* Uwaga SAP [2015553:]Wyświetla listę wymagań wstępnych dla wdrożeń oprogramowania SAP obsługiwanych przez SAP na platformie Azure
* SAP Note [2002167] zaleca ustawienia systemu operacyjnego dla systemu Red Hat Enterprise Linux
* SAP Note [2009879] ma wytyczne SAP HANA dla Red Hat Enterprise Linux
* Uwaga SAP [2178632]: Zawiera szczegółowe informacje na temat wszystkich metryk monitorowania zgłoszonych dla sap na platformie Azure
* Uwaga SAP [2191498]: Zawiera wymaganą wersję agenta hosta SAP dla systemu Linux na platformie Azure
* Uwaga SAP [2243692]: Zawiera informacje o licencjonowaniu SAP na linuksie na platformie Azure
* Uwaga SAP [1999351]: Zawiera dodatkowe informacje dotyczące rozwiązywania problemów z rozszerzeniem rozszerzonego monitorowania platformy Azure dla systemu SAP
* Uwaga SAP [1900823]: Zawiera informacje o wymaganiach dotyczących pamięci masowej SAP HANA
* [SAP Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Zawiera wszystkie wymagane notatki SAP dla Linuksa
* [Planowanie i implementacja maszyn wirtualnych platformy Azure dla systemu SAP w systemie Linux][planning-guide]
* [Wdrożenie maszyn wirtualnych platformy Azure dla systemu SAP w systemie Linux][deployment-guide]
* [Wdrożenie usługi DBMS maszyn wirtualnych platformy Azure dla systemu SAP w systemie Linux][dbms-guide]
* Ogólna dokumentacja RHEL
  * [Omówienie dodatku o wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administracja dodatkami o wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Dokumentacja dodatku o wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Red Hat Enterprise Linux Networking Przewodnik](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide)
* Dokumentacja RHEL specyficzna dla platformy Azure:
  * [Instalowanie systemu SAP HANA w systemie Red Hat Enterprise Linux do użytku na platformie Microsoft Azure](https://access.redhat.com/solutions/3193782)
* [Aplikacje SAP NetApp na platformie Microsoft Azure przy użyciu plików NetApp platformy Azure][anf-sap-applications-azure]


## <a name="overview"></a>Omówienie

Jedną z metod osiągnięcia wysokiej dostępności HANA jest skonfigurowanie automatycznego trybu failover hosta. Aby skonfigurować automatyczną funkcję pracy awaryjnej hosta, należy dodać jedną lub więcej maszyn wirtualnych do systemu HANA i skonfigurować je jako węzły wstrzymania. Gdy aktywny węzeł ulegnie awarii, węzeł rezerwowy automatycznie przejmuje. W przedstawionej konfiguracji z maszynami wirtualnymi platformy Azure można automatycznie przewinąć w tryb failover przy użyciu [systemu plików NFS w programie Azure NetApp Files.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)  

> [!NOTE]
> Węzeł wstrzymania potrzebuje dostępu do wszystkich woluminów bazy danych. Woluminy HANA muszą być zamontowane jako woluminy NFSv4. Ulepszony mechanizm blokowania oparty na dzierżawie plików w protokole NFSv4 jest używany do `I/O` ogrodzenia. 

> [!IMPORTANT]
> Aby utworzyć obsługiwana konfigurację, należy wdrożyć woluminy danych i dzienników HANA jako woluminy NFSv4.1 i zainstalować je przy użyciu protokołu NFSv4.1. Konfiguracja automatycznego trybu failover hosta hana z węzłem wstrzymania nie jest obsługiwana przez nfsv3.

![Omówienie wysokiej dostępności sap NetWeaver](./media/sap-hana-high-availability-rhel/sap-hana-scale-out-standby-netapp-files-rhel.png)

Na poprzednim diagramie, który jest zgodny z zaleceniami sieci SAP HANA, trzy podsieci są reprezentowane w jednej sieci wirtualnej platformy Azure: 
* Do komunikacji z klientem
* Do komunikacji z systemem pamięci masowej
* Do wewnętrznej komunikacji międzywęzłowej HANA

Woluminy NetApp platformy Azure znajdują się w osobnej podsieci, [delegowanej do plików NetApp platformy Azure.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)  

W tym przykładzie konfiguracji podsieci są:  

  - `client`10.9.1.0/26  
  - `storage`10.9.3.0/26  
  - `hana`10.9.2.0/26  
  - `anf`10.9.0.0/26 (podsieć delegowana do plików NetApp usługi Azure)

## <a name="set-up-the-azure-netapp-files-infrastructure"></a>Konfigurowanie infrastruktury plików NetApp platformy Azure 

Przed przystąpieniem do konfigurowania infrastruktury usługi Azure NetApp Files należy zapoznać się z [dokumentacją usługi Azure NetApp Files.][anf-azure-doc] 

Usługa Azure NetApp Files jest dostępna w kilku [regionach platformy Azure.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp) Sprawdź, czy wybrany region platformy Azure oferuje usługi Azure NetApp Files.  

Aby uzyskać informacje na temat dostępności plików NetApp usługi Azure w regionie platformy Azure, zobacz [Dostępność plików NetApp platformy Azure przez region Azure][anf-avail-matrix].  

Przed wdrożeniem plików NetApp platformy Azure należy zażądać dołączania do plików NetApp platformy Azure, przechodząc do [rejestrowania się w celu uzyskania instrukcji dotyczących plików NetApp platformy Azure.][anf-register] 

### <a name="deploy-azure-netapp-files-resources"></a>Wdrażanie zasobów plików NetApp platformy Azure  

W poniższych instrukcjach założono, że już wdrożono [sieć wirtualną platformy Azure.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) Zasoby i maszyny wirtualne usługi Azure NetApp Files, w których zostaną zainstalowane zasoby usługi Azure NetApp Files, muszą zostać wdrożone w tej samej sieci wirtualnej platformy Azure lub w równorzędnych sieciach wirtualnych platformy Azure.  

1. Jeśli zasoby nie zostały jeszcze wdrożone, poproś o [dołączanie do usługi Azure NetApp Files.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)  

2. Utwórz konto NetApp w wybranym regionie platformy Azure, postępując zgodnie z instrukcjami w [obszarze Tworzenie konta NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).  

3. Skonfiguruj pulę pojemności plików NetApp platformy Azure, postępując zgodnie z instrukcjami zawartymi w [obszarze Konfigurowanie puli pojemności plików NetApp platformy Azure](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  

   Architektura HANA przedstawiona w tym artykule używa jednej puli pojemności plików NetApp platformy Azure na poziomie *usługi Ultra.* W przypadku obciążeń HANA na platformie Azure zalecamy korzystanie z [poziomu usługi](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)Azure NetApp Files *Ultra* lub *Premium.*  

4. Delegować podsieć do plików NetApp platformy Azure, zgodnie z opisem w instrukcjach w [aplikacji Delegowanie podsieci do plików NetApp platformy Azure.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)  

5. Wdrażanie woluminów plików NetApp platformy Azure, postępując zgodnie z instrukcjami w [obszarze Tworzenie woluminu NFS dla plików NetApp platformy Azure.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)  

   Podczas wdrażania woluminów należy wybrać wersję systemu **NFSv4.1.** Wdrażanie woluminów w wyznaczonej [podsieci](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)Plików NetApp platformy Azure . 
   
   Należy pamiętać, że zasoby usługi Azure NetApp Files i maszyny wirtualne platformy Azure muszą znajdować się w tej samej sieci wirtualnej platformy Azure lub w równorzędnych sieciach wirtualnych platformy Azure. Na przykład **HN1**-data-mnt00001, **HN1**-log-mnt00001 i tak dalej, są nazwy woluminów i nfs://10.9.0.4/**HN1**-data-mnt00001, nfs://10.9.0.4/**HN1**-log-mnt00001 i tak dalej, są ścieżki plików dla woluminów plików NetApp Azure.  

   * głośność **HN1**-data-mnt00001 (nfs://10.9.0.4/**HN1**-data-mnt00001)
   * głośność **HN1**-data-mnt00002 (nfs://10.9.0.4/**HN1**-data-mnt00002)
   * głośność **HN1**-log-mnt00001 (nfs://10.9.0.4/**HN1**-log-mnt00001)
   * objętość **HN1**-log-mnt00002 (nfs://10.9.0.4/**HN1**-log-mnt00002)
   * wolumin **HN1**-udostępniony (nfs://10.9.0.4/**HN1**-udostępniony)
   
   W tym przykładzie użyliśmy oddzielnego woluminu usługi Azure NetApp Files dla każdego woluminu danych i dzienników HANA. W przypadku konfiguracji bardziej zoptymalizowanej pod względem kosztów w mniejszych lub nieproduktywnych systemach można umieścić wszystkie instalacje danych na jednym woluminie, a wszystkie dzienniki są montowane na innym woluminie.  

### <a name="important-considerations"></a>Istotne zagadnienia

Podczas tworzenia usługi Azure NetApp Files for SAP HANA skalowaw w poziomie ze scenariuszem stand by nodes należy pamiętać o następujących ważnych zagadnieniach:

- Minimalna pula pojemności wynosi 4 tebibajty (TiB).  
- Minimalny rozmiar woluminu to 100 gibibajtów (GiB).
- Usługi Azure NetApp Files i wszystkie maszyny wirtualne, na których będą zainstalowane woluminy usługi Azure NetApp Files, muszą znajdować się w tej samej sieci wirtualnej platformy Azure lub w [sieciach wirtualnych równorzędnych](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) w tym samym regionie.  
- Wybrana sieć wirtualna musi mieć podsieć, która jest delegowana do usługi Azure NetApp Files.
- Przepływność woluminu usługi Azure NetApp Files jest funkcją przydziału woluminu i poziomu usług, co jest udokumentowane w [poziomie usługi dla plików NetApp Platformy Azure](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). Podczas doboru rozmiaru woluminów HANA Azure NetApp, upewnij się, że wynikowa przepływność spełnia wymagania systemowe HANA.  
- Za pomocą zasad [eksportu](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)plików Usługi Azure NetApp można kontrolować dozwolonych klientów, typ dostępu (odczyt i zapis, tylko do odczytu i tak dalej). 
- Funkcja Usługi Azure NetApp Files nie jest jeszcze obsługująca strefę. Obecnie funkcja nie jest wdrażana we wszystkich strefach dostępności w regionie platformy Azure. Należy pamiętać o potencjalnych implikacje opóźnienia w niektórych regionach platformy Azure.  

> [!IMPORTANT]
> W przypadku obciążeń SAP HANA małe opóźnienia mają kluczowe znaczenie. Współpracuj z przedstawicielem firmy Microsoft, aby upewnić się, że maszyny wirtualne i woluminy plików NetApp platformy Azure są wdrażane w bliskiej odległości.  

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Rozmiar bazy danych HANA w plikach NetApp usługi Azure

Przepływność woluminu usługi Azure NetApp Files jest funkcją rozmiaru woluminu i poziomu usług, co jest udokumentowane w [poziomie usługi dla plików NetApp Platformy Azure](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). 

Podczas projektowania infrastruktury dla sap na platformie Azure, należy pamiętać o niektórych minimalnych wymagań dotyczących magazynu przez SAP, które przekładają się na minimalne właściwości przepływności:

- Odczyt i zapis w /hana/log 250 megabajtów na sekundę (MB/s) z rozmiarami We/Wy 1 MB.  
- Odczyt aktywności co najmniej 400 MB/s dla /hana/data dla rozmiarów 16 MB i 64 MB we/wy.  
- Aktywność zapisu co najmniej 250 MB/s dla /hana/data z rozmiarami 16 MB i 64 MB we/wy. 

[Limity przepływności plików NetApp platformy Azure](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) na 1 wartość limitu woluminu to:
- Warstwa magazynu premium — 64 MiB/s  
- Poziom ultra pamięci masowej — 128 MiB/s  

Aby spełnić minimalne wymagania SAP dotyczące przepływności danych i dziennika oraz wskazówki dotyczące /hana/shared, zalecane rozmiary będą następujące:

| Wolumin | Wielkość<br>Warstwa magazynu w warstwie Premium Storage | Wielkość<br>Warstwa Ultra Storage | Obsługiwany protokół NFS |
| --- | --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | wersja 4.1 |
| /hana/dane | 6.3 TiB | 3.2 TiB | wersja 4.1 |
| /hana/udostępniony | 1xRAM na 4 węzły procesu roboczego | 1xRAM na 4 węzły procesu roboczego | wersja 3 lub v4.1 |

Konfiguracja SAP HANA dla układu przedstawionego w tym artykule przy użyciu warstwy Azure NetApp Files Ultra Storage będzie:

| Wolumin | Wielkość<br>Warstwa Ultra Storage | Obsługiwany protokół NFS |
| --- | --- | --- |
| /hana/log/mnt00001 | 2 TiB | wersja 4.1 |
| /hana/log/mnt00002 | 2 TiB | wersja 4.1 |
| /hana/data/mnt00001 | 3.2 TiB | wersja 4.1 |
| /hana/data/mnt00002 | 3.2 TiB | wersja 4.1 |
| /hana/udostępniony | 2 TiB | wersja 3 lub v4.1 |

> [!NOTE]
> Zalecenia dotyczące rozmiaru plików NetApp platformy Azure dostępne w tym miejscu są ukierunkowane na spełnienie minimalnych wymagań zalecanych przez firmę SAP dla dostawców infrastruktury. W rzeczywistych scenariuszach wdrożeń klientów i obciążenia te rozmiary mogą nie być wystarczające. Użyj tych zaleceń jako punktu wyjścia i dostosować, w oparciu o wymagania określonego obciążenia.  

> [!TIP]
> Można dynamicznie zmienić rozmiar woluminów usługi Azure NetApp Files, bez konieczności *odinstalowywania* woluminów, zatrzymywania maszyn wirtualnych lub zatrzymywania sap HANA. Takie podejście umożliwia elastyczność, aby spełnić zarówno oczekiwane i nieprzewidziane wymagania przepływności aplikacji.

## <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Wdrażanie maszyn wirtualnych systemu Linux za pośrednictwem witryny Azure portal

Najpierw musisz utworzyć woluminy plików NetApp platformy Azure. Następnie wykonaj następujące czynności:
1. Utwórz [podsieci sieci wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) w sieci [wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). 
1. Wdrażanie maszyn wirtualnych. 
1. Utwórz dodatkowe interfejsy sieciowe i dołącz interfejsy sieciowe do odpowiednich maszyn wirtualnych.  

   Każda maszyna wirtualna ma trzy interfejsy sieciowe, które odpowiadają`client` `storage` trzem `hana`podsieciom sieci wirtualnej platformy Azure ( , i ). 

   Aby uzyskać więcej informacji, zobacz [Tworzenie maszyny wirtualnej systemu Linux na platformie Azure przy użyciu wielu kart interfejsu sieciowego](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics).  

> [!IMPORTANT]
> W przypadku obciążeń SAP HANA małe opóźnienia mają kluczowe znaczenie. Aby osiągnąć małe opóźnienia, należy współpracować z przedstawicielem firmy Microsoft, aby upewnić się, że maszyny wirtualne i woluminy plików NetApp platformy Azure są wdrażane w bliskiej odległości. Podczas [dołączania nowego systemu SAP HANA,](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) który używa SAP HANA Azure NetApp Files, prześlij niezbędne informacje. 
 
W następnych instrukcjach przyjęto założenie, że grupa zasobów została już utworzona, sieć wirtualna `storage` `hana`platformy Azure i trzy podsieci sieci wirtualnej platformy Azure: `client`i . Podczas wdrażania maszyn wirtualnych wybierz podsieć klienta, tak aby interfejs sieciowy klienta był interfejsem podstawowym na maszynach wirtualnych. Należy również skonfigurować jawną trasę do podsieci delegowanej plików Usługi Azure NetApp za pośrednictwem bramy podsieci magazynu. 

> [!IMPORTANT]
> Upewnij się, że wybrany system operacyjny jest certyfikowany przez SAP HANA dla określonych typów maszyn wirtualnych, których używasz. Aby uzyskać listę typów maszyn wirtualnych z certyfikatem SAP HANA i wersji systemu operacyjnego dla tych typów, przejdź do [witryny platform IaaS z certyfikatem SAP HANA.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Kliknij szczegóły wymienionego typu maszyny Wirtualnej, aby uzyskać pełną listę wersji systemu operacyjnego obsługiwanych przez SAP HANA dla tego typu.  

1. Utwórz zestaw dostępności dla sap hana. Upewnij się, że ustawisz domenę aktualizacji max.  

2. Utwórz trzy maszyny wirtualne (**hanadb1**, **hanadb2**, **hanadb3**) wykonując następujące kroki:  

   a. Użyj obrazu Red Hat Enterprise Linux w galerii platformy Azure, która jest obsługiwana dla sap HANA. W tym przykładzie użyliśmy obrazu RHEL-SAP-HA 7.6.  

   b. Wybierz zestaw dostępności utworzony wcześniej dla sap HANA.  

   d. Wybierz podsieć sieci wirtualnej platformy Azure klienta. Wybierz [opcję Sieć przyspieszona](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).  

   Podczas wdrażania maszyn wirtualnych nazwa interfejsu sieciowego jest generowana automatycznie. W tych instrukcjach dla uproszczenia będziemy odwoływać się do automatycznie generowanych interfejsów sieciowych, które są dołączone do podsieci sieci wirtualnej platformy Azure klienta, jako **hanadb1-client**, **hanadb2-client**i **hanadb3-client**. 

3. Utwórz trzy interfejsy sieciowe, po `storage` jednym dla każdej maszyny wirtualnej, dla podsieci sieci wirtualnej (w tym przykładzie **hanadb1-storage**, **hanadb2-storage**i **hanadb3-storage).**  

4. Utwórz trzy interfejsy sieciowe, po `hana` jednym dla każdej maszyny wirtualnej, dla podsieci sieci wirtualnej (w tym przykładzie **hanadb1-hana**, **hanadb2-hana**i **hanadb3-hana**).  

5. Dołącz nowo utworzone interfejsy sieci wirtualnej do odpowiednich maszyn wirtualnych, wykonując następujące kroki:  

    a. Przejdź do maszyny wirtualnej w [witrynie Azure portal](https://portal.azure.com/#home).  

    b. W lewym okienku wybierz pozycję **Maszyny wirtualne**. Filtruj nazwę maszyny wirtualnej (na przykład **hanadb1),** a następnie wybierz maszynę wirtualną.  

    d. W **okienku Przegląd** wybierz pozycję **Zatrzymaj,** aby zdążyć przydzielenie maszyny wirtualnej.  

    d. Wybierz **pozycję Sieć**, a następnie podłącz interfejs sieciowy. Z listy rozwijanej **Dołącz interfejs sieciowy** wybierz już utworzone `storage` `hana` interfejsy sieciowe dla podsieci i podsieci.  
    
    e. Wybierz **pozycję Zapisz**. 
 
    f. Powtórz kroki od b do e dla pozostałych maszyn wirtualnych (w naszym przykładzie **hanadb2** i **hanadb3).**
 
    g. Na razie pozostaw maszyny wirtualne w stanie zatrzymania. Następnie włączymy [przyspieszoną sieć](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) dla wszystkich nowo podłączonych interfejsów sieciowych.  

6. Włącz przyspieszoną sieć dla dodatkowych interfejsów `storage` `hana` sieciowych dla i podsieci, wykonując następujące kroki:  

    a. Otwórz [usługę Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) w [witrynie Azure portal](https://portal.azure.com/#home).  

    b. Wykonaj następujące polecenia, aby włączyć przyspieszoną sieć dla dodatkowych interfejsów sieciowych, które są podłączone do `storage` i `hana` podsieci.  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-storage</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true

    </code></pre>

7. Uruchom maszyny wirtualne, wykonując następujące kroki:  

    a. W lewym okienku wybierz pozycję **Maszyny wirtualne**. Filtruj nazwę maszyny wirtualnej (na przykład **hanadb1),** a następnie wybierz ją.  

    b. W okienku **Przegląd** wybierz pozycję **Start**.  

## <a name="operating-system-configuration-and-preparation"></a>Konfiguracja i przygotowanie systemu operacyjnego

Instrukcje w następnych sekcjach są poprzedzone jedną z następujących czynności:
* **[A]**: Dotyczy wszystkich węzłów
* **[1]**: Dotyczy tylko węzła 1
* **[2]**: Dotyczy tylko węzła 2
* **[3]**: Dotyczy tylko węzła 3

Skonfiguruj i przygotuj system operacyjny, wykonując następujące czynności:

1. **[A]** Obsługa plików hosta na maszynach wirtualnych. Uwzględnij wpisy dla wszystkich podsieci. Następujące wpisy zostały `/etc/hosts` dodane do tego przykładu.  

    <pre><code>
    # Storage
    10.9.3.4   hanadb1-storage
    10.9.3.5   hanadb2-storage
    10.9.3.6   hanadb3-storage
    # Client
    10.9.1.5   hanadb1
    10.9.1.6   hanadb2
    10.9.1.7   hanadb3
    # Hana
    10.9.2.4   hanadb1-hana
    10.9.2.5   hanadb2-hana
    10.9.2.6   hanadb3-hana
    </code></pre>

3. **[A]** Dodaj trasę sieciową, aby komunikacja z plikami NetApp platformy Azure przebiegła za pośrednictwem interfejsu sieciowego magazynu.  

   W tym przykładzie użyje `Networkmanager` do skonfigurowania dodatkowej trasy sieciowej. W poniższych instrukcjach przyjęto `eth1`założenie, że interfejs sieciowy magazynu jest .  
   Najpierw określ nazwę połączenia `eth1`urządzenia . W tym przykładzie nazwa `eth1` `Wired connection 1`połączenia urządzenia to .  

    <pre><code>
    # Execute as root
    nmcli connection
    # Result
    #NAME                UUID                                  TYPE      DEVICE
    #System eth0         5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03  ethernet  eth0
    #Wired connection 1  4b0789d1-6146-32eb-83a1-94d61f8d60a7  ethernet  eth1
    </code></pre>

   Następnie skonfiguruj dodatkową trasę do sieci `eth1`delegowanej plików Usługi Azure NetApp za pośrednictwem programu .  

    <pre><code>
    # Add the following route 
    # ANFDelegatedSubnet/cidr via StorageSubnetGW dev StorageNetworkInterfaceDevice
    nmcli connection modify <b>"Wired connection 1"</b> +ipv4.routes <b>"10.9.0.0/26 10.9.3.1"</b>
    </code></pre>

    Uruchom ponownie maszynę wirtualną, aby aktywować zmiany.  

3. **[A]** Zainstaluj pakiet klienta systemu plików NFS.  

    <pre><code>
    yum install nfs-utils
    </code></pre>

3. **[A]** Przygotuj system operacyjny do uruchomienia sap HANA na platformie Azure NetApp z systemem NFS, zgodnie z opisem w [aplikacji NetApp SAP na platformie Microsoft Azure przy użyciu plików NetApp platformy Azure.][anf-sap-applications-azure] Utwórz plik konfiguracyjny */etc/sysctl.d/netapp-hana.conf* dla ustawień konfiguracji NetApp.  

    <pre><code>
    vi /etc/sysctl.d/netapp-hana.conf
    # Add the following entries in the configuration file
    net.core.rmem_max = 16777216
    net.core.wmem_max = 16777216
    net.core.rmem_default = 16777216
    net.core.wmem_default = 16777216
    net.core.optmem_max = 16777216
    net.ipv4.tcp_rmem = 65536 16777216 16777216
    net.ipv4.tcp_wmem = 65536 16777216 16777216
    net.core.netdev_max_backlog = 300000 net.ipv4.tcp_slow_start_after_idle=0 net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **[A]** Utwórz plik konfiguracyjny */etc/sysctl.d/ms-az.conf* z dodatkowymi ustawieniami optymalizacji.  

    <pre><code>
    vi /etc/sysctl.d/ms-az.conf
    # Add the following entries in the configuration file
    ipv6.conf.all.disable_ipv6 = 1
    net.ipv4.tcp_max_syn_backlog = 16348
    net.ipv4.ip_local_port_range = 40000 65300
    net.ipv4.conf.all.rp_filter = 0
    sunrpc.tcp_slot_table_entries = 128
    vm.swappiness=10
    </code></pre>

5. **[A]** Dostosuj ustawienia sunrpc, zgodnie z zaleceniami aplikacji [NetApp SAP na platformie Microsoft Azure przy użyciu plików NetApp platformy Azure][anf-sap-applications-azure].  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

6. **[A]** Red Hat dla konfiguracji HANA.

    Skonfiguruj RHEL zgodnie z opisem w SAP Note [2292690], [2455582], [2593824] i <https://access.redhat.com/solutions/2447641>.

    > [!NOTE]
    > W przypadku instalacji dodatku HANA 2.0 SP04 konieczne będzie zainstalowanie pakietu `compat-sap-c++-7` zgodnie z opisem w notatce SAP [2593824], zanim będzie można zainstalować sap HANA. 

## <a name="mount-the-azure-netapp-files-volumes"></a>Instalowanie woluminów plików NetApp platformy Azure

1. **[A]** Tworzenie punktów instalacji dla woluminów bazy danych HANA.  

    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** Tworzenie katalogów specyficznych dla węzła dla /usr/sap w **HN1**-shared.  

    <pre><code>
    # Create a temporary directory to mount <b>HN1</b>-shared
    mkdir /mnt/tmp
    mount <b>10.9.0.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** Sprawdź ustawienie domeny nfs. Upewnij się, że domena jest skonfigurowana jako domyślna domena plików NetApp, **`defaultv4iddomain.com`** a mapowanie jest ustawione na **nikogo.**  

    > [!IMPORTANT]
    > Upewnij się, że domena `/etc/idmapd.conf` systemu plików NFS jest zgodna z domyślną **`defaultv4iddomain.com`** konfiguracją domeny w plikach NetApp usługi Azure: . Jeśli występuje niezgodność między konfiguracją domeny na kliencie systemu plików NFS (tj. maszyną wirtualną) a serwerem NFS, czyli konfiguracją usługi Azure NetApp, uprawnienia dla plików na `nobody`woluminach NetApp platformy Azure, które są zainstalowane na maszynach wirtualnych, będą wyświetlane jako .  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** `nfs4_disable_idmapping`Sprawdź . Należy ustawić na **Y**. Aby utworzyć strukturę `nfs4_disable_idmapping` katalogu, w której się znajduje, wykonaj polecenie mount. Nie będzie można ręcznie utworzyć katalogu w obszarze /sys/modules, ponieważ dostęp jest zarezerwowany dla jądra / sterowników.  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.9.0.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

   Aby uzyskać więcej informacji `nfs4_disable_idmapping` na https://access.redhat.com/solutions/1749883temat zmiany parametru, zobacz .

6. **[A]** Zainstaluj udostępnione woluminy plików NetApp platformy Azure.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-data-mnt00001 /hana/data/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-data-mnt00002 /hana/data/<b>HN1</b>/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-log-mnt00001 /hana/log/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    </code></pre>

7. **[1]** Zamontuj woluminy specyficzne dla węzła na **hanadb1**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** Zamontuj woluminy specyficzne dla węzła na **hanadb2**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** Zamontuj woluminy specyficzne dla węzła na **hanadb3**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** Sprawdź, czy wszystkie woluminy HANA są zainstalowane w wersji protokołu **NFSv4**.  

    <pre><code>
    sudo nfsstat -m
    # Verify that flag vers is set to <b>4.1</b> 
    # Example from <b>hanadb1</b>
    /hana/data/<b>HN1</b>/mnt00001 from 10.9.0.4:/<b>HN1</b>-data-mnt00001
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/log/<b>HN1</b>/mnt00002 from 10.9.0.4:/<b>HN1</b>-log-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/data/<b>HN1</b>/mnt00002 from 10.9.0.4:/<b>HN1</b>-data-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/log/<b>HN1</b>/mnt00001 from 10.9.0.4:/<b>HN1</b>-log-mnt00001
    Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /usr/sap/<b>HN1</b> from 10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b>
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/shared from 10.9.0.4:/<b>HN1</b>-shared/shared
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    </code></pre>

## <a name="installation"></a>Instalacja  

W tym przykładzie wdrażania sap HANA w konfiguracji skalowania w poziomie z węzłem wstrzymania z platformą Azure, użyliśmy DODATKU SP4 hana 2.0.  

### <a name="prepare-for-hana-installation"></a>Przygotowanie do instalacji HANA

1. **[A]** Przed instalacją HANA ustaw hasło główne. Hasło główne można wyłączyć po zakończeniu instalacji. Wykonaj `root` jako `passwd`polecenie .  

2. **[1]** Sprawdź, czy można zalogować się za pośrednictwem SSH do **hanadb2** i **hanadb3**, bez monitowania o hasło.  

    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** Zainstaluj dodatkowe pakiety, które są wymagane dla dodatku SP4 dla systemu HANA 2.0. Aby uzyskać więcej informacji, zobacz UWAGA SAP [2593824](https://launchpad.support.sap.com/#/notes/2593824). 

    <pre><code>
    yum install libgcc_s1 libstdc++6 compat-sap-c++-7 libatomic1 
    </code></pre>

4. **[2], [3]** Zmień własność SAP `data` HANA i `log` katalogów na **hn1**adm.   

    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

5. **[A]** Tymczasowo wyłącz zaporę, aby nie kolidowała z instalacją HANA. Można go ponownie włączyć, po zakończeniu instalacji HANA. 
   <pre><code>
    # Execute as root
    systemctl stop firewalld
    systemctl disable firewalld
   </code></pre>

### <a name="hana-installation"></a>Instalacja HANA

1. **[1]** Zainstaluj sap HANA, postępując zgodnie z instrukcjami zawartymi w [przewodniku instalacji i aktualizacji SAP HANA 2.0](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html). W tym przykładzie instalujemy sap HANA skalowaw w poziomie z wzorcem, jeden pracownik i jeden węzeł gotowości.  

   a. Uruchom program **hdblcm** z katalogu oprogramowania instalacyjnego HANA. Użyj `internal_network` parametru i przekaż przestrzeń adresową dla podsieci, która jest używana do wewnętrznej komunikacji między węzłami HANA.  

    <pre><code>
    ./hdblcm --internal_network=10.9.2.0/26
    </code></pre>

   b. W wierszu polecenia wprowadź następujące wartości:

     * Dla **Wybierz akcję**: wpisz **1** (dla instalacji)
     * Dodatkowe **elementy do montażu:** wprowadź **2, 3**
     * Dla ścieżki instalacji: naciśnij enter (domyślnie /hana/shared)
     * W polu **Nazwa hosta lokalnego**: naciśnij klawisz Enter, aby zaakceptować wartość domyślną
     * W obszarze **Czy chcesz dodać hosty do systemu?**: **wpisać y**
     * Aby dodać **nazwy hostów oddzielonych przecinkami:** wprowadź **hanadb2, hanadb3**
     * W polu **Nazwa użytkownika głównego** [root]: naciśnij klawisz Enter, aby zaakceptować wartość domyślną
     * Dla ról dla hosta hanadb2: wprowadź **1** (dla pracownika)
     * Dla **grupy trybu failover hosta** dla hosta hanadb2 [domyślnie]: naciśnij klawisz Enter, aby zaakceptować wartość domyślną
     * W przypadku **numeru partycji magazynu** dla hosta hanadb2 [<<assign automatically>>]: naciśnij klawisz Enter, aby zaakceptować wartość domyślną
     * Dla **grupy pracowników** dla hosta hanadb2 [domyślnie]: naciśnij klawisz Enter, aby zaakceptować wartość domyślną
     * W przypadku **wybierz role** dla hosta hanadb3: wprowadź **2** (dla gotowości)
     * Dla **grupy trybu failover hosta** dla hosta hanadb3 [domyślnie]: naciśnij klawisz Enter, aby zaakceptować wartość domyślną
     * Dla **grupy pracowników** dla hosta hanadb3 [domyślnie]: naciśnij klawisz Enter, aby zaakceptować wartość domyślną
     * Dla **SAP HANA System ID**: wprowadź **HN1**
     * Dla **numeru instancji** [00]: wprowadź **03**
     * W przypadku **lokalnej grupy pracowników hosta** [domyślnie]: naciśnij klawisz Enter, aby zaakceptować wartość domyślną
     * Dla **wybierz użycie systemu / Wprowadź indeks [4]**: wprowadź **4** (dla niestandardowych)
     * W polu **Lokalizacja woluminów danych** [/hana/data/HN1]: naciśnij klawisz Enter, aby zaakceptować wartość domyślną
     * Aby **dojechać do lokalizacji woluminów dziennika** [/hana/log/HN1]: naciśnij klawisz Enter, aby zaakceptować wartość domyślną
     * Aby **ograniczyć maksymalną alokację pamięci?** [n]: wprowadź **n**
     * W przypadku **nazwy hosta certyfikatu dla hosta hanadb1** [hanadb1]: naciśnij klawisz Enter, aby zaakceptować wartość domyślną
     * W przypadku **nazwy hosta certyfikatu dla hosta hanadb2** [hanadb2]: naciśnij klawisz Enter, aby zaakceptować wartość domyślną
     * W przypadku **nazwy hosta certyfikatu dla hosta hanadb3** [hanadb3]: naciśnij klawisz Enter, aby zaakceptować wartość domyślną
     * Dla **administratora systemu (hn1adm) Hasło:** wprowadź hasło
     * Dla **użytkownika (systemowego) hasła bazy danych**systemu : wprowadź hasło systemu
     * Aby **potwierdzić hasło użytkownika (systemu) bazy danych**systemu: wprowadź hasło systemu
     * Dla **restartu systemu po ponownym uruchomieniu komputera?** [n]: wprowadź **n** 
     * Dla **Czy chcesz kontynuować (y / n)**: sprawdź podsumowanie i jeśli wszystko wygląda dobrze, wprowadź **y**


2. **[1]** Sprawdź global.ini  

   Wyświetl global.ini i upewnij się, że konfiguracja wewnętrznej komunikacji między węzłami SAP HANA jest na miejscu. Sprawdź sekcję **komunikacji.** Powinien mieć przestrzeń adresową `hana` dla podsieci `listeninterface` `.internal`i powinien być ustawiony na . Sprawdź sekcję **internal_hostname_resolution.** Powinien mieć adresy IP dla maszyn wirtualnych `hana` HANA, które należą do podsieci.  

   <pre><code>
    sudo cat /usr/sap/<b>HN1</b>/SYS/global/hdb/custom/config/global.ini
    # Example 
    #global.ini last modified 2019-09-10 00:12:45.192808 by hdbnameserve
    [communication]
    internal_network = <b>10.9.2.0/26</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.9.2.4</b> = <b>hanadb1</b>
    <b>10.9.2.5</b> = <b>hanadb2</b>
    <b>10.9.2.6</b> = <b>hanadb3</b>
   </code></pre>

3. **[1]** Dodaj mapowanie hosta, aby upewnić się, że adresy IP klienta są używane do komunikacji z klientem. Dodaj `public_host_resolution`sekcję i dodaj odpowiednie adresy IP z podsieci klienta.  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.9.1.5</b>
    map_<b>hanadb2</b> = <b>10.9.1.6</b>
    map_<b>hanadb3</b> = <b>10.9.1.7</b>
   </code></pre>

4. **[1]** Uruchom ponownie sap HANA, aby aktywować zmiany.  

   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** Sprawdź, czy interfejs klienta będzie używał `client` adresów IP z podsieci do komunikacji.  

   <pre><code>
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.9.1.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.9.1.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.9.1.5</b>"
   </code></pre>

   Aby uzyskać informacje dotyczące sposobu weryfikacji konfiguracji, zobacz UWAGA SAP [2183363 — Konfiguracja sieci wewnętrznej SAP HANA](https://launchpad.support.sap.com/#/notes/2183363).  

5. **[A]** Włącz ponownie zaporę.  
   - Zatrzymaj HANA
       <pre><code>
        sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
       </code></pre>
   - Ponowne włączanie zapory
       <pre><code>
        # Execute as root
        systemctl start firewalld
        systemctl enable firewalld
       </code></pre>

   - Otwieranie niezbędnych portów zapory

       > [!IMPORTANT]
       > Tworzenie reguł zapory, aby umożliwić komunikację między węzłami HANA i ruch klienta. Wymagane porty są wyświetlane w [portach TCP/IP wszystkich produktów SAP](https://help.sap.com/viewer/ports). Następujące polecenia są tylko przykładem. W tym scenariuszu z używanym numerem systemu 03.

       <pre><code>
        # Execute as root
        sudo firewall-cmd --zone=public --add-port=30301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30301/tcp
        sudo firewall-cmd --zone=public --add-port=30303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30303/tcp
        sudo firewall-cmd --zone=public --add-port=30306/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30306/tcp
        sudo firewall-cmd --zone=public --add-port=30307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30307/tcp
        sudo firewall-cmd --zone=public --add-port=30313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30313/tcp
        sudo firewall-cmd --zone=public --add-port=30315/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30315/tcp
        sudo firewall-cmd --zone=public --add-port=30317/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30317/tcp
        sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30340/tcp
        sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30341/tcp
        sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30342/tcp
        sudo firewall-cmd --zone=public --add-port=1128/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1128/tcp
        sudo firewall-cmd --zone=public --add-port=1129/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1129/tcp
        sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40302/tcp
        sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40301/tcp
        sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40307/tcp
        sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40303/tcp
        sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40340/tcp
        sudo firewall-cmd --zone=public --add-port=50313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50313/tcp
        sudo firewall-cmd --zone=public --add-port=50314/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50314/tcp
        sudo firewall-cmd --zone=public --add-port=30310/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30310/tcp
        sudo firewall-cmd --zone=public --add-port=30302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30302/tcp
       </code></pre>

   - Uruchom HANA
       <pre><code>
        sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
       </code></pre>

6. Aby zoptymalizować sap HANA dla podstawowej usługi Azure NetApp Files storage, ustaw następujące parametry SAP HANA:

   - `max_parallel_io_requests`**128**
   - `async_read_submit` **on**
   - `async_write_submit_active` **on**
   - `async_write_submit_blocks`**wszystkie**

   Aby uzyskać więcej informacji, zobacz [NetApp SAP Applications na platformie Microsoft Azure przy użyciu plików NetApp platformy Azure][anf-sap-applications-azure]. 

   Począwszy od systemów SAP HANA 2.0, `global.ini`można ustawić parametry w . Aby uzyskać więcej informacji, zobacz UWAGA SAP [1999930](https://launchpad.support.sap.com/#/notes/1999930).  
   
   W przypadku systemów SAP HANA 1.0 wersje SPS12 i starsze parametry te można ustawić podczas instalacji, jak opisano w SAP Note [2267798](https://launchpad.support.sap.com/#/notes/2267798).  

7. Magazyn używany przez usługę Azure NetApp Files ma ograniczenie rozmiaru pliku o 16 terabajtów (TB). SAP HANA nie jest niejawnie świadomy ograniczenia magazynu i nie będzie automatycznie tworzyć nowy plik danych po osiągnięciu limitu rozmiaru pliku 16 TB. Jak SAP HANA próbuje zwiększyć plik powyżej 16 TB, że próba spowoduje błędy i, ostatecznie, w awarii serwera indeksu. 

   > [!IMPORTANT]
   > Aby zapobiec próbom powiększania plików danych przez SAP HANA powyżej [limitu 16](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-resource-limits) TB `global.ini`podsystemu magazynu, ustaw następujące parametry w pliku .  
   > - datavolume_striping = prawda
   > - datavolume_striping_size_gb = 15000 Aby uzyskać więcej informacji, zobacz UWAGA SAP [2400005](https://launchpad.support.sap.com/#/notes/2400005).
   > Należy pamiętać o SAP Note [2631285](https://launchpad.support.sap.com/#/notes/2631285). 

## <a name="test-sap-hana-failover"></a>Testowanie pracy awaryjnej SAP HANA 

1. Symulowanie awarii węzła w węźle procesu roboczego SAP HANA. Wykonaj następujące czynności: 

   a. Przed symulacją awarii węzła uruchom następujące polecenia jako **hn1**adm, aby uchwycić stan środowiska:  

   <pre><code>
    # Check the landscape status
    python /usr/sap/HN1/HDB<b>03</b>/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |

    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
   </code></pre>

   b. Aby symulować awarię węzła, uruchom następujące polecenie jako katalog główny w węźle procesu roboczego, które w tym przypadku jest **hanadb2:**  
   
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   d. Monitorowanie systemu pod kątem zakończenia pracy awaryjnej. Po zakończeniu pracy awaryjnej przechwytuj stan, który powinien wyglądać następująco:  

    <pre><code>
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | no     | info   |          |        |         2 |         0 | default  | default  | master 2   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb3 | yes    | info   |          |        |         0 |         2 | default  | default  | master 3   | slave      | standby     | slave       | standby | worker  | default | default |
   </code></pre>

   > [!IMPORTANT]
   > Gdy węzeł wystąpi błąd jądra, należy unikać opóźnień `kernel.panic` z sap HANA pracy awaryjnej, ustawiając na 20 sekund na *wszystkich* maszynach wirtualnych HANA. Konfiguracja odbywa `/etc/sysctl`się w programie . Uruchom ponownie maszyny wirtualne, aby aktywować zmianę. Jeśli ta zmiana nie zostanie wykonana, praca awaryjna może potrwać 10 lub więcej minut, gdy węzeł występuje wpadka jądra.  

2. Zabij serwer nazw, wykonując następujące czynności:

   a. Przed testem sprawdź stan środowiska, uruchamiając następujące polecenia jako **hn1**adm:  

   <pre><code>
    #Landscape status 
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
   </code></pre>

   b. Uruchom następujące polecenia jako **hn1**adm w aktywnym węźle głównym, który jest **hanadb1** w tym przypadku:  

    <pre><code>
        hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
    </code></pre>
    
    Węzeł rezerwowy **hanadb3** przejmie jako węzeł główny. Oto stan zasobu po zakończeniu testu pracy awaryjnej:  

    <pre><code>
     # Check the instance status
     sapcontrol -nr 03  -function GetSystemInstanceList
     GetSystemInstanceList
     OK
     hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
     hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
     hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
     hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
     # Check the landscape status
     python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
     | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
     |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
     |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
     | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
     | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
     | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
     | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
    </code></pre>

   d. Uruchom ponownie wystąpienie HANA na **hanadb1** (czyli na tej samej maszynie wirtualnej, gdzie serwer nazw został zabity). Węzeł **hanadb1** dołączy ponownie do środowiska i zachowa swoją rolę w trybie gotowości.  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Po uruchomieniu sap HANA na **hanadb1,** spodziewać się następującego stanu:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   d. Ponownie zabić serwer nazw na aktualnie aktywnym węźle głównym (czyli w węźle **hanadb3**).  
   
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   Węzeł **hanadb1** wznowi rolę węzła głównego. Po zakończeniu testu pracy awaryjnej stan będzie wyglądał następująco:

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

   e. Uruchom SAP HANA na **hanadb3**, który będzie gotowy do obsługi jako węzeł gotowości.  

   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Po uruchomieniu sap HANA na **hanadb3,** stan wygląda następująco:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

## <a name="next-steps"></a>Następne kroki

* [Planowanie i implementacja maszyn wirtualnych platformy Azure dla systemu SAP][planning-guide]
* [Wdrożenie maszyn wirtualnych platformy Azure dla systemu SAP][deployment-guide]
* [Wdrożenie usługi DBMS maszyn wirtualnych platformy Azure dla systemu SAP][dbms-guide]
* Aby dowiedzieć się, jak ustalić wysoką dostępność i zaplanować odzyskiwanie po awarii usługi SAP HANA na maszynach wirtualnych platformy Azure, zobacz [Wysoka dostępność sap HANA na maszynach wirtualnych platformy Azure (VM)][sap-hana-ha].

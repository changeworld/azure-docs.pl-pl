---
title: SAP HANA skalowanie w poziomie z węzłem gotowości na maszynach wirtualnych platformy Azure z Azure NetApp Files na SUSE Linux Enterprise Server | Microsoft Docs
description: Przewodnik wysokiej dostępności dla oprogramowania SAP NetWeaver na SUSE Linux Enterprise Server z Azure NetApp Files dla aplikacji SAP
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
ms.date: 04/30/2019
ms.author: radeltch
ms.openlocfilehash: 76369c1a4beb792de03cf0ccae5c86825812f103
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934178"
---
# <a name="sap-hana-scale-out-with-standby-node-on-azure-vms-with-azure-netapp-files-on-suse-linux-enterprise-server"></a>SAP HANA skalowanie w poziomie z węzłem gotowości na maszynach wirtualnych platformy Azure z Azure NetApp Files na SUSE Linux Enterprise Server 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]: https://launchpad.support.sap.com/#/notes/2205917
[1944799]: https://launchpad.support.sap.com/#/notes/1944799
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]: https://launchpad.support.sap.com/#/notes/1900823

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


W tym artykule opisano sposób wdrażania systemu HANA o wysokiej dostępności w konfiguracji skalowania w poziomie przy użyciu funkcji wstrzymywania w usłudze Azure Virtual Machines z [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/) dla woluminów magazynu udostępnionego.  
W przykładowych konfiguracjach, poleceniach instalacji i tak dalej wystąpienie platformy HANA ma wartość **03** , a identyfikator systemu Hana to **HN1**. Przykłady są oparte na platformie HANA 2,0 SP4 i SUSE Linux Enterprise Server dla oprogramowania SAP 12 SP4. 

Przeczytaj najpierw następujące informacje i dokumenty SAP:

* [Dokumentacja Azure NetApp Files][anf-azure-doc] 
* Nota SAP [1928533], która ma:  
  * Lista rozmiarów maszyn wirtualnych platformy Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP
  * Ważne informacje o pojemności dla rozmiarów maszyn wirtualnych platformy Azure
  * Obsługiwane oprogramowanie SAP oraz kombinacje systemu operacyjnego i bazy danych
  * Wymagana wersja jądra SAP dla systemu Windows i Linux w systemie Microsoft Azure
* Uwaga dotycząca protokołu SAP [2015553] zawiera listę wymagań wstępnych dotyczących wdrożeń oprogramowania SAP obsługiwanych przez oprogramowanie SAP na platformie Azure.
* W przypadku programu SAP Uwaga [2205917] zalecane ustawienia systemu operacyjnego dla SUSE Linux Enterprise Server aplikacji SAP
* Uwaga SAP [1944799] ma wytyczne sap dla SUSE Linux Enterprise Server aplikacji SAP
* Uwaga dotycząca oprogramowania SAP [2178632] zawiera szczegółowe informacje o wszystkich metrykach monitorowania raportowanych dla oprogramowania SAP na platformie Azure.
* Uwaga dotycząca programu SAP [2191498] ma wymaganą wersję agenta hosta SAP dla systemu Linux na platformie Azure.
* Uwaga dotycząca oprogramowania SAP [2243692] zawiera informacje na temat licencjonowania SAP w systemie Linux na platformie Azure.
* Uwaga dotycząca protokołu SAP [1984787] zawiera ogólne informacje o SUSE Linux Enterprise Server 12.
* Uwaga dotycząca programu SAP [1999351] zawiera dodatkowe informacje dotyczące rozwiązywania problemów z rozszerzoną funkcją monitorowania platformy Azure dla oprogramowania SAP.
* Uwaga dotycząca oprogramowania SAP [1900823] zawiera informacje o wymaganiach dotyczących magazynu SAP HANA
* [Społeczność systemu SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) na stronie wiki ma wszystkie wymagane uwagi SAP dla systemu Linux.
* [Planowanie i wdrażanie usługi Azure Virtual Machines dla oprogramowania SAP w systemie Linux][planning-guide]
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP w systemie Linux][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP w systemie Linux][dbms-guide]
* [Przewodniki dla systemu SUSE SAP ha Best Practices][suse-ha-guide] Przewodniki zawierają wszystkie informacje wymagane do skonfigurowania NetWeaver HA i replikacji systemu SAP HANA w środowisku lokalnym. Użyj tych przewodników jako ogólnego planu bazowego. Zapewniają one wiele bardziej szczegółowych informacji.
* [Informacje o wersji w programie SUSE High Availability Extension 12 SP3][suse-ha-12sp3-relnotes]
* [NetApp aplikacje SAP na Microsoft Azure przy użyciu Azure NetApp Files][anf-sap-applications-azure]
* [SAP HANA w systemach NetApp z systemem plików NFS](https://www.netapp.com/us/media/tr-4435.pdf). Przewodnik konfiguracji zawiera informacje dotyczące sposobu konfigurowania SAP HANA przy użyciu systemu plików NFS udostępnianego przez Azure NetApp Files.


## <a name="overview"></a>Przegląd

Jedną z metod uzyskiwania wysokiej dostępności platformy HANA jest przełączenie do trybu failover hosta. Aby skonfigurować funkcję autoawarii hosta, co najmniej jedna maszyna wirtualna jest dodawana do systemu HANA i skonfigurowana jako węzły w stanie wstrzymania. Gdy aktywny węzeł ulegnie awarii, węzeł w stanie wstrzymania zostanie automatycznie przełączone. W prezentowanej konfiguracji z maszynami wirtualnymi platformy Azure, które są wykonywane przez system [plików NFS w Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/).  

Węzeł w stanie wstrzymania musi mieć dostęp do wszystkich woluminów bazy danych. Woluminy HANA muszą być zainstalowane jako woluminy NFSv4. Ulepszony mechanizm blokowania oparty na dzierżawach plików w protokole NFSv4 jest używany do `I/O` ogrodzenia. 

> [!IMPORTANT]
> Należy koniecznie wdrożyć dane platformy HANA i woluminy dzienników jako woluminy NFSv 4.1 i zainstalować je przy użyciu protokołu NFSv 4.1 w celu zapewnienia obsługiwanej konfiguracji. Konfiguracja automatycznej pracy w trybie failover hosta HANA z węzłem wstrzymywania nie jest obsługiwana w przypadku NFSv3.

![Omówienie wysokiej dostępności SAP NetWeaver](./media/high-availability-guide-suse-anf/sap-hana-scale-out-standby-netapp-files-suse.png)

Postępując zgodnie z zaleceniami SAP HANA sieci, trzy podsieci zostały utworzone w ramach jednej sieci wirtualnej platformy Azure: w celu komunikacji z systemem magazynu dla wewnętrznej komunikacji między węzłami HANA i komunikacji z klientem. Woluminy usługi Azure NetApp znajdują się w osobnej podsieci [delegowanej do Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

W tej przykładowej konfiguracji podsieci są następujące:  

  - `storage` 10.23.2.0/24  
  - `hana` 10.23.3.0/24  
  - `client` 10.23.0.0/24  
  - `anf` 10.23.1.0/26  

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Konfigurowanie infrastruktury Azure NetApp Files 

Przed przystąpieniem do konfigurowania infrastruktury usługi Azure NetApp Files zapoznaj się z [dokumentacją Azure NetApp Files][anf-azure-doc]. Pliki usługi Azure NetApp są dostępne w kilku [regionach świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Sprawdź, czy wybrany region platformy Azure oferuje Azure NetApp Files.  

Poniższy link przedstawia dostępność Azure NetApp Files według regionu platformy Azure: [Azure NetApp Files dostępność według regionu platformy Azure][anf-avail-matrix].  
Przed wdrożeniem Azure NetApp Files należy zażądać dołączenia do Azure NetApp Files, postępując zgodnie z instrukcjami dotyczącymi [usługi Azure NetApp Files][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Wdrażanie zasobów Azure NetApp Files  

W poniższych krokach przyjęto założenie, że wdrożono już [Virtual Network platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Zasoby Azure NetApp Files i maszyny wirtualne, w których zostaną zainstalowane zasoby Azure NetApp Files, muszą być wdrożone w tej samej Virtual Network platformy Azure lub w równorzędnych sieciach wirtualnych platformy Azure.  

1. Jeśli jeszcze tego nie zrobiono, zażądaj dołączenia [do Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register).  

2. Utwórz konto NetApp w wybranym regionie świadczenia usługi Azure, postępując zgodnie z [instrukcjami dotyczącymi tworzenia konta NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).  

3. Skonfiguruj pulę pojemności Azure NetApp Files, postępując zgodnie z [instrukcjami dotyczącymi konfigurowania Azure NetApp Files puli pojemności](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  
Architektura HANA przedstawiona w tym artykule używa Azure NetApp Files puli pojemności bardzo usługi. Zalecamy Azure NetApp Files [poziomu usługi](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) Ultra lub Premium dla obciążeń Hana na platformie Azure.  

4. Delegowanie podsieci do plików NetApp platformy Azure zgodnie z opisem w [instrukcji delegowania podsieci do Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

5. Wdróż woluminy Azure NetApp Files, postępując zgodnie z [instrukcjami, aby utworzyć wolumin dla Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes).  Pamiętaj o wybraniu wersji **nfsv 4.1** w przypadku wdrażania woluminów. Obecnie dostęp do NFSv 4.1 wymaga dodatkowych listy dozwolonych. Wdróż woluminy w wyznaczeniej [podsieci](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)Azure NetApp Files. Należy pamiętać, że zasoby Azure NetApp Files i maszyny wirtualne platformy Azure muszą znajdować się w tym samym Virtual Network platformy Azure lub w równorzędnych sieciach wirtualnych platformy Azure. Na przykład <b>HN1</b>-Data-Mnt00001, <b>HN1</b>-log-mnt00001 itp. są nazwami woluminów oraz NFS://10.23.1.5/<b>HN1</b>-Data-mnt00001, NFS://10.23.1.4/<b>HN1</b>-log-mnt00001 itp. są ścieżkami plików dla woluminów Azure NetApp Files.  

   1. Volume <b>HN1</b>-Data-mnt00001 (NFS://10.23.1.5/<b>HN1</b>-Data-mnt00001)
   2. Volume <b>HN1</b>-Data-mnt00002 (NFS://10.23.1.6/<b>HN1</b>-Data-mnt00002)
   3. Volume <b>HN1</b>-log-mnt00001 (NFS://10.23.1.4/<b>HN1</b>-log-mnt00001)
   4. Volume <b>HN1</b>-log-mnt00002 (NFS://10.23.1.6/<b>HN1</b>-log-mnt00002)
   5. Volume <b>HN1</b>-shared (NFS://10.23.1.4/<b>HN1</b>-Shared)
   
   W tym przykładzie użyto oddzielnego Azure NetApp Files dla każdego woluminu danych i dzienników platformy HANA. Aby uzyskać bardziej zoptymalizowane pod kątem kosztów konfigurację w mniejszych lub nieproduktywnych systemach, można umieścić wszystkie instalacje danych i wszystkie dzienniki instalacji na pojedynczym woluminie.  

### <a name="important-considerations"></a>Ważne zagadnienia

Rozważając Azure NetApp Files dla architektury SAP NetWeaver w systemie SUSE wysokiej dostępności, weź pod uwagę następujące ważne zagadnienia:

- Minimalna Pula pojemności to 4 TiB.  
- Minimalny rozmiar woluminu to 100 GiB
- Azure NetApp Files i wszystkie maszyny wirtualne, w których zostaną zainstalowane woluminy Azure NetApp Files, muszą znajdować się w tej samej Virtual Network platformy Azure lub w [równorzędnych sieciach wirtualnych](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) w tym samym regionie.  
- Wybrana Sieć wirtualna musi mieć podsieć delegowaną do Azure NetApp Files.
- Przepływność woluminu NetApp Azure to funkcja limitu przydziału woluminu i poziomu usługi, zgodnie z opisem w obszarze [poziom usług dla Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). Podczas ustalania wielkości woluminów platformy Azure NetApp, upewnij się, że przepływność spełnia wymagania systemowe HANA.  
- Azure NetApp Files oferuje [zasady eksportowania](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): można kontrolować dozwolonych klientów, typ dostępu (odczyt & zapis, tylko do odczytu itp.). 
- Funkcja Azure NetApp Files nie jest jeszcze dostępna dla strefy. Obecnie Azure NetApp Files funkcja nie jest wdrażana we wszystkich strefach dostępności w regionie świadczenia usługi Azure. Weź pod uwagę potencjalne konsekwencje opóźnienia w niektórych regionach świadczenia usługi Azure.  
- Ważne jest, aby maszyny wirtualne wdrożone w pobliżu usługi Azure NetApp Storage w małych opóźnieniach. W przypadku obciążeń SAP HANA małe opóźnienia ma krytyczne znaczenie. Skontaktuj się z przedstawicielem firmy Microsoft, aby upewnić się, że maszyny wirtualne i woluminy Azure NetApp Files są wdrożone w bliskiej bliskości.  
- Identyfikator użytkownika dla identyfikatora <b>SID</b>adm i identyfikator grupy dla `sapsys` na maszynach wirtualnych muszą być zgodne z konfiguracją w Azure NetApp Files. 

> [!IMPORTANT]
> W przypadku obciążeń SAP HANA małe opóźnienia ma krytyczne znaczenie. Skontaktuj się z przedstawicielem firmy Microsoft, aby upewnić się, że maszyny wirtualne i woluminy Azure NetApp Files są wdrożone w bliskiej bliskości.  

> [!IMPORTANT]
> Jeśli istnieje niezgodność między IDENTYFIKATORem użytkownika dla identyfikatora <b>SID</b>adm a identyfikatorem grupy dla `sapsys` między maszyną wirtualną a konfiguracją usługi Azure NetApp, uprawnienia do plików na woluminach NetApp platformy Azure, zainstalowane na maszynach wirtualnych, będą wyświetlane jako `nobody`. Upewnij się, że określisz prawidłowy identyfikator użytkownika dla <b>identyfikatora SID</b>adm i identyfikator grupy dla `sapsys`, gdy [zostanie on dołączony do nowego systemu](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) Azure NetApp Files.

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Ustalanie wielkości dla bazy danych HANA na Azure NetApp Files

Przepływność woluminu NetApp Azure to funkcja rozmiaru woluminu i poziomu usługi, zgodnie z opisem w obszarze [poziom usług dla Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). 

Podczas projektowania infrastruktury dla oprogramowania SAP na platformie Azure należy pamiętać o minimalnych wymaganiach dotyczących magazynu przez oprogramowanie SAP, które przekładają się na minimalne charakterystyki przepływności:

- Włącz odczyt/zapis na/Hana/log z 250 MB/s z 1 MB we/wy  
- Włącz działanie odczytu przez co najmniej 400 MB/s dla/Hana/Data dla 16 MB i 64 MB we/wy  
- Włącz działanie zapisu przez co najmniej 250 MB/s dla/Hana/Data z 16 MB i 64 MB we/wy  

[Limity przepływności Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) na 1 TIB przydziału woluminu są następujące:
- Warstwa Premium Storage-64 MiB/s  
- Warstwa Ultra Storage — 128 MiB/s  

W celu spełnienia minimalnych wymagań dotyczących przepływności SAP dla danych i dziennika oraz zgodnie z wytycznymi dla `/hana/shared` zalecane rozmiary będą wyglądać następująco:

| Wolumin | Rozmiar<br /> Warstwa Premium Storage | Rozmiar<br /> Warstwa Ultra Storage | Obsługiwany protokół NFS |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v 4.1 |
| /hana/data | 6,3 TiB | 3,2 TiB | v 4.1 |
| /hana/shared | Maks. (512 GB, 1xRAM) na 4 węzły procesu roboczego | Maks. (512 GB, 1xRAM) na 4 węzły procesu roboczego | v3 lub v 4.1 |

SAP HANA konfiguracja układu przedstawionego w tym artykule przy użyciu warstwy Azure NetApp Files Ultra Storage będzie wyglądać następująco:

| Wolumin | Rozmiar<br /> Warstwa Ultra Storage | Obsługiwany protokół NFS |
| --- | --- |
| /hana/log/mnt00001 | 2 TiB | v 4.1 |
| /hana/log/mnt00002 | 2 TiB | v 4.1 |
| /hana/data/mnt00001 | 3,2 TiB | v 4.1 |
| /hana/data/mnt00002 | 3,2 TiB | v 4.1 |
| /hana/shared | 2 TiB | v3 lub v 4.1 |

> [!NOTE]
> Zalecenia dotyczące ustalania wielkości Azure NetApp Files podane w tym miejscu są celem spełnienia minimalnych wymagań SAP Express do dostawców infrastruktury. W rzeczywistych scenariuszach wdrażania klientów i obciążeń, które mogą nie być wystarczające. Użyj tych zaleceń jako punktu wyjścia i adaptacji na podstawie wymagań określonego obciążenia.  

> [!TIP]
> Woluminy Azure NetApp Files można zmienić dynamicznie, bez konieczności `unmount` woluminów, Zatrzymaj maszyny wirtualne lub Zatrzymaj SAP HANA. Dzięki temu można elastycznie spełnić wymagania dotyczące przepływności aplikacji zarówno w oczekiwany sposób, jak i nieprzewidziane.

## <a name="deploy-linux-virtual-machines-via-azure-portal"></a>Wdrażanie maszyn wirtualnych z systemem Linux za pomocą Azure Portal

Najpierw należy utworzyć woluminy Azure NetApp Files. Utwórz [podsieci sieci wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) w [usłudze Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Wdróż maszyny wirtualne. Utwórz dodatkowe interfejsy sieciowe i Dołącz interfejsy sieciowe do odpowiednich maszyn wirtualnych. Każda maszyna wirtualna ma trzy interfejsy sieciowe odpowiadające trzem podsieciom sieci wirtualnej platformy Azure (`storage`, `hana` i `client`).  Zobacz, [jak utworzyć maszynę wirtualną z systemem Linux na platformie Azure przy użyciu wielu kart interfejsu sieciowego](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics).  

> [!IMPORTANT]
> W przypadku obciążeń SAP HANA małe opóźnienia ma krytyczne znaczenie. Skontaktuj się z przedstawicielem firmy Microsoft, aby upewnić się, że maszyny wirtualne i woluminy Azure NetApp Files są wdrożone w bliskiej bliskości w celu osiągnięcia małych opóźnień. Prześlij niezbędne informacje w przypadku [nowego systemu SAP HANA](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u), który korzysta z SAP HANA Azure NetApp Files.  
> 
W następnych krokach przyjęto założenie, że utworzono już grupę zasobów, sieć wirtualną platformy Azure oraz trzy podsieci Virtual Network platformy Azure: `storage`, `hana` i `client`.  Podczas wdrażania maszyn wirtualnych wybierz podsieć magazynu, aby interfejs sieciowy magazynu był podstawowym interfejsem na maszynach wirtualnych. Jeśli nie jest to możliwe, należy skonfigurować jawną trasę do delegowanej podsieci usługi Azure NetApp za pośrednictwem bramy podsieci magazynu. 

> [!IMPORTANT]
> Upewnij się, że wybrany system operacyjny to SAP Certified dla SAP HANA na określonych typach maszyn wirtualnych, których używasz. Listę SAP HANA certyfikowanych typów maszyn wirtualnych i wydań systemu operacyjnego dla tych systemów można wyszukać w [SAP HANA certyfikowanych platformach IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Pamiętaj, aby kliknąć na liście szczegóły typu maszyny wirtualnej, aby uzyskać pełną listę SAP HANA obsługiwanych wersji systemu operacyjnego dla określonego typu maszyny wirtualnej.  

1. Utwórz zestaw dostępności dla SAP HANA. Upewnij się, że ustawiono maksymalną domenę aktualizacji.  

2. Utwórz trzy Virtual Machines (**hanadb1**, **hanadb2**, **hanadb3**)  
   - Użyj obrazu SLES4SAP w galerii platformy Azure, który jest obsługiwany dla SAP HANA. W tym przykładzie użyto obrazu SLES4SAP 12 SP4.  
   - Wybierz pozycję zestaw dostępności utworzony wcześniej dla SAP HANA.  
   - Wybierz podsieć Azure Virtual Network. Wybierz opcję [przyspieszone sieci](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).  
Podczas wdrażania maszyn wirtualnych nazwa interfejsu sieciowego jest generowana automatycznie. Odwołujemy się do interfejsów sieciowych dołączonych do podsieci magazynu Virtual Network platformy Azure jako **hanadb1-Storage**, **hanadb2-** Storage i **hanadb3-Storage**. 
3. Utwórz trzy interfejsy sieciowe — jeden dla każdej maszyny wirtualnej dla podsieci sieci wirtualnej `hana`. W tym przykładzie: **hanadb1-Hana**, **hanadb2-Hana**i **hanadb3-Hana**.  
4. Utwórz trzy interfejsy sieciowe — jeden dla każdej maszyny wirtualnej dla podsieci sieci wirtualnej **klienta** . W tym przykładzie: **hanadb1-Client**, **hanadb2-Client**i **hanadb3-Client**.  
5. Dołącz nowo utworzone interfejsy sieci wirtualnej do odpowiednich maszyn wirtualnych  

    1. Przejdź do maszyny wirtualnej w [Azure Portal](https://portal.azure.com/#home).  
    2. Wybierz pozycję Virtual Machines w okienku nawigacji po lewej stronie. Odfiltruj nazwę maszyny wirtualnej, na przykład **hanadb1**. Kliknij maszynę wirtualną.  
    3. W obszarze przegląd wybierz pozycję Zatrzymaj, aby cofnąć przydział maszyny wirtualnej.  
    4. Wybierz pozycję Sieć, a następnie Dołącz interfejs sieciowy. Wybierz z listy rozwijanej w obszarze "Dołącz interfejs sieciowy" już utworzone interfejsy sieciowe dla **`hana`** i podsieci **klienta** .  Zapisz zmiany. 
    5. Powtórz te czynności dla pozostałych maszyn wirtualnych. W naszym przykładzie — **hanadb2** i **hanadb3**.
    6. Pozostaw teraz maszyny wirtualne w stanie zatrzymania. Następnie włączysz [przyspieszoną sieć](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) dla wszystkich nowo dołączonych interfejsów sieciowych.  

6. Włącz przyspieszone sieci dla dodatkowych interfejsów sieciowych dla **`hana`** i **`client`** podsieci.  

    1. Otwórz [powłokę Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) w [Azure Portal](https://portal.azure.com/#home)  
    2. Wykonaj następujące polecenia, aby włączyć przyspieszone sieci dla dodatkowych interfejsów sieciowych dołączonych do **`hana`** i **`client`** podsieci.  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-client</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-client</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-client</b> --accelerated-networking true
    </code></pre>
7. Uruchom maszyny wirtualne  

    1. Wybierz pozycję Virtual Machines w okienku nawigacji po lewej stronie. Odfiltruj nazwę maszyny wirtualnej, na przykład **hanadb1**. Kliknij maszynę wirtualną.  
    2. W obszarze przegląd wybierz pozycję Rozpocznij.  

## <a name="operating-system-configuration-and-preparation"></a>Konfiguracja i przygotowanie systemu operacyjnego

Następujące elementy są poprzedzone znakiem **[A]** -odpowiednim dla wszystkich węzłów, **[1]** — dotyczy tylko węzła 1, **[2]** — dotyczy tylko węzła 2 lub **[3]** — dotyczy tylko węzła 3.

1. **[A]** Zachowaj pliki hostów na maszynach wirtualnych. Uwzględnij wpisy dla wszystkich podsieci. Następujące wpisy zostały dodane do `/etc/hosts` na potrzeby tego przykładu.  
    <pre><code>
    # Storage
    10.23.2.4   hanadb1
    10.23.2.5   hanadb2
    10.23.2.6   hanadb3
    # Client
    10.23.0.5   hanadb1-client
    10.23.0.6   hanadb2-client
    10.23.0.7   hanadb3-client
    # Hana
    10.23.3.4   hanadb1-hana
    10.23.3.5   hanadb2-hana
    10.23.3.6   hanadb3-hana
    </code></pre>

2. **[A]** Zmień ustawienia konfiguracji DHCP i chmury, aby uniknąć niezamierzonych zmian nazwy hosta.  
    <pre><code>
    vi /etc/sysconfig/network/dhcp
    #Change the following DHCP setting to "no"
    DHCLIENT_SET_HOSTNAME="no"
    vi /etc/sysconfig/network/ifcfg-eth0
    # Edit ifcfg-eth0 
    #Change CLOUD_NETCONFIG_MANAGE='yes' to "no"
    CLOUD_NETCONFIG_MANAGE='no'
    </code></pre>

3. **[A]** Przygotuj system operacyjny do uruchamiania SAP HANA w systemach NetApp z systemem plików NFS, zgodnie z opisem w [SAP HANA w systemach NetApp AFF Systems with NFS Configuration Guide](https://www.netapp.com/us/media/tr-4435.pdf). Utwórz plik konfiguracji dla ustawień konfiguracji NetApp: `/etc/sysctl.d/netapp-hana.conf`.  

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
    net.core.netdev_max_backlog = 300000
    net.ipv4.tcp_slow_start_after_idle=0
    net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **[A]** Utwórz plik konfiguracji przy użyciu ustawień konfiguracji firmy Microsoft dla platformy Azure: `/etc/sysctl.d/ms-az.conf`.  

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

4. **[A]** Dostosuj ustawienia sunrpc zgodnie z zaleceniami w [SAP HANA w systemie NetApp AFF Systems with NFS Configuration Guide](https://www.netapp.com/us/media/tr-4435.pdf).  
    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

## <a name="mount-the-azure-netapp-files-volumes"></a>Zainstaluj woluminy Azure NetApp Files

1. **[A]** Utwórz punkty instalacji dla woluminów bazy danych Hana.  
    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** utwórz katalogi specyficzne dla węzła dla `/usr/sap` w **HN1**— udostępnione.  

    <pre><code>
    # Create a temporary directory to mount  <b>HN1</b>-shared
    mkdir /mnt/tmp
    mount <b>10.23.1.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** Sprawdź ustawienie domeny NFS. Upewnij się, że domena jest skonfigurowana jako **`localdomain`** i że mapowanie jest ustawione na wartość **nikt**.  
    <pre><code>
    sudo cat  /etc/idmapd.conf
    # Example
    [General]
    Verbosity = 0
    Pipefs-Directory = /var/lib/nfs/rpc_pipefs
    Domain = <b>localdomain</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** wyłącz mapowanie identyfikatorów NFSv4. Wykonaj polecenie Mount, aby utworzyć strukturę katalogów, w której znajduje się `nfs4_disable_idmapping`.  Nie będzie można ręcznie utworzyć katalogu w obszarze/sys/modules, ponieważ dostęp jest zarezerwowany dla jądra/sterowników.  

    <pre><code>
    
    mkdir /mnt/tmp
    mount 10.23.1.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    # Disable NFSv4 idmapping. 
    echo "N" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    </code></pre>

5. **[A]** ręcznie utwórz grupę SAP HANA i użytkownika. Identyfikatory dla grup sapsys i User **hn1**ADM muszą być ustawione na te same identyfikatory, które są udostępniane podczas dołączania. W tym przykładzie identyfikatory są ustawione na **1001**. W przeciwnym razie nie będzie możliwe uzyskanie dostępu do woluminów.  Identyfikatory grup sapsys i kont użytkowników **hn1**adm i sapadm muszą być takie same na wszystkich maszynach wirtualnych.  

    <pre><code>
    # Create user group 
    sudo groupadd -g 1001 sapsys
    # Create  users 
    sudo useradd <b>hn1</b>adm -u 1001 -g 1001 -d /usr/sap/<b>HN1</b>/home -c "SAP HANA Database System" -s /bin/sh
    sudo useradd sapadm -u 1002 -g 1001 -d /home/sapadm -c "SAP Local Administrator" -s /bin/sh
    # Set the password  for both user ids
    sudo passwd hn1adm
    sudo passwd sapadm
    </code></pre>

6. **[A]** Zainstaluj udostępnione woluminy Azure NetApp Files.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.5:/<b>HN1</b>-data-mnt00001 /hana/data/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-data-mnt00002 /hana/data/<b>HN1</b>/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-log-mnt00001 /hana/log/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    </code></pre>

7. **[1]** Zainstaluj woluminy specyficzne dla węzła w **hanadb1**.  
    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** Zainstaluj woluminy specyficzne dla węzła w **hanadb2**.  
    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** Zainstaluj woluminy specyficzne dla węzła w **hanadb3**.  
    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** Sprawdź, czy wszystkie woluminy Hana są zainstalowane przy użyciu protokołu NFS w wersji **NFSv4**.  
    <pre><code>
    sudo nfsstat -m
    # Verify that flag vers is set to <b>4.1</b> 
    # Example from <b>hanadb1</b>
    /hana/data/<b>HN1</b>/mnt00001 from 10.23.1.5:/<b>HN1</b>-data-mnt00001
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.5
    /hana/log/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-log-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/data/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-data-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/log/<b>HN1</b>/mnt00001 from 10.23.1.4:/<b>HN1</b>-log-mnt00001
    Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /usr/sap/<b>HN1</b> from 10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b>
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /hana/shared from 10.23.1.4:/<b>HN1</b>-shared/shared
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    </code></pre>

## <a name="installation"></a>Instalacja  

W tym przykładzie w celu wdrożenia SAP HANA w konfiguracji skalowania w poziomie z aktywnym węzłem w systemie Azure użyto platformy HANA 2,0 SP4.  

### <a name="prepare-for-hana-installation"></a>Przygotowanie do instalacji platformy HANA

1. **[A]** Ustaw hasło główne przed instalacją platformy Hana (można wyłączyć hasło główne po zakończeniu instalacji). `passwd`polecenia EXECUTE AS `root`.  

2. **[1]** Sprawdź, czy można przeprowadzić protokół SSH do **hanadb2** i **hanadb3**bez monitowania o podanie hasła.  
    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** zainstaluj dodatkowe pakiety wymagane dla platformy Hana 2,0 SP4. Aby uzyskać szczegółowe informacje, zobacz temat SAP Note [2593824](https://launchpad.support.sap.com/#/notes/2593824) . 
    <pre><code>
    sudo zypper install libgcc_s1 libstdc++6 libatomic1 
    </code></pre>

4. **[2, 3]** Zmień własność katalogów SAP HANA `data` i `log` na **hn1**adm.   
    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

### <a name="hana-installation"></a>Instalacja platformy HANA

1. **[1]** Zainstaluj SAP HANA, postępując zgodnie z instrukcjami w temacie [SAP HANA 2,0 instalacji i aktualizacji przewodnika](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html). W tym przykładzie instalujemy SAP HANA skalowanie w poziomie za pomocą wzorca, jednego procesu roboczego i jednego węzła w stanie wstrzymania.  
   Uruchom program **hdblcm** z katalogu oprogramowania instalacyjnego platformy Hana. Użyj parametru `internal_network` i przekaż przestrzeń adresową dla podsieci, która jest używana w wewnętrznej komunikacji między węzłami HANA.  

    <pre><code>./hdblcm --internal_network=10.23.3.0/24
    </code></pre>

   Wprowadź następujące wartości w wierszu polecenia.

     * Wybierz akcję: wprowadź **1** (na potrzeby instalacji)
     * Wybierz dodatkowe składniki do instalacji: wprowadź **2, 3**
     * Wprowadź ścieżkę instalacji: naciśnij klawisz ENTER (wartość domyślna to/Hana/Shared)
     * Wprowadź nazwę hosta lokalnego: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * Czy chcesz dodać hosty do systemu? Wprowadź **y**
     * Wprowadź nazwy hostów rozdzielone Coma do dodania: **hanadb2, hanadb3**
     * Wprowadź nazwę użytkownika głównego [root]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * Wprowadź hasło użytkownika root: wprowadź hasło do konta głównego
     * Wybierz role do hanadb2 hosta: wprowadź **1** (dla procesu roboczego)
     * Wprowadź grupę trybu failover hosta dla hanadb2 hosta [domyślnie]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * Wprowadź numer partycji magazynu dla hanadb2 hosta [<<assign automatically>>]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * Wprowadź grupę procesów roboczych dla hosta hanadb2 [domyślnie]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * Wybierz role dla hosta hanadb3: wprowadź **2** (na potrzeby wstrzymania)
     * Wprowadź grupę trybu failover hosta dla hanadb3 hosta [domyślnie]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * Wprowadź grupę procesów roboczych dla hosta hanadb3 [domyślnie]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * Wprowadź identyfikator systemu SAP HANA: wprowadź **HN1**
     * Wprowadź numer wystąpienia [00]: wprowadź **03**
     * Wprowadź grupę lokalnego procesu roboczego hosta [domyślnie]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * Wybieranie opcji użycie systemu/wprowadzanie indeksu [4]: wprowadź **4** (dla niestandardowych)
     * Wprowadź lokalizację woluminów danych [/hana/data/HN1]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * Wprowadź lokalizację woluminów dziennika [/hana/log/HN1]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * Ograniczyć maksymalną alokację pamięci? [n]: wprowadź **n**
     * Wprowadź nazwę hosta certyfikatu dla hosta hanadb1 [hanadb1]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * Wprowadź nazwę hosta certyfikatu dla hosta hanadb2 [hanadb2]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * Wprowadź nazwę hosta certyfikatu dla hosta hanadb3 [hanadb3]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * Wprowadź hasło administratora systemu (hn1adm): wprowadź hasło
     * Wprowadź hasło użytkownika systemowej bazy danych (SYSTEM): wprowadź hasło systemu
     * Potwierdź hasło użytkownika systemowej bazy danych (SYSTEM): wprowadź hasło systemu
     * Czy uruchomić ponownie system po ponownym uruchomieniu komputera? [n]: wprowadź **n** 
     * Czy chcesz kontynuować (t/n): Sprawdź poprawność podsumowania i czy wszystko wygląda dobrze .


2. **[1]** Sprawdź plik Global. ini  

   Wyświetl plik Global. ini i upewnij się, że jest wykonywana konfiguracja wewnętrznego SAP HANA komunikacji między węzłami. Sprawdź **komunikację**sekcji. Powinna mieć przestrzeń adresową dla podsieci **`hana`** i `listeninterface` powinna być ustawiona na `.internal`. Sprawdź sekcję **internal_hostname_resolution**. Powinny mieć adresy IP dla maszyn wirtualnych HANA należących do podsieci **`hana`** .  

   <pre><code>
    sudo cat /usr/sap/<b>HN1</b>/SYS/global/hdb/custom/config/global.ini
    # Example 
    #global.ini last modified 2019-09-10 00:12:45.192808 by hdbnameserve
    [communication]
    internal_network = <b>10.23.3/24</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.23.3.4</b> = <b>hanadb1</b>
    <b>10.23.3.5</b> = <b>hanadb2</b>
    <b>10.23.3.6</b> = <b>hanadb3</b>
   </code></pre>

3. **[1]** Dodaj mapowanie hosta, aby upewnić się, że adresy IP klientów są używane do komunikacji z klientem. Dodaj sekcję `public_host_resolution` i Dodaj odpowiednie adresy IP z podsieci klienta.  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.23.0.5</b>
    map_<b>hanadb2</b> = <b>10.23.0.6</b>
    map_<b>hanadb3</b> = <b>10.23.0.7</b>
   </code></pre>

4. **[1]** ponownie uruchom SAP HANA, aby aktywować zmiany.  
   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** Sprawdź, czy interfejs klienta będzie używać adresów IP z podsieci **klienta** na potrzeby komunikacji.  
   <pre><code>
    sudo -u hn1adm /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.23.0.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.23.0.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.23.0.5</b>"
   </code></pre>

   Aby uzyskać szczegółowe informacje na temat weryfikowania konfiguracji, zobacz temat SAP Uwaga [2183363-konfiguracja sieci wewnętrznej SAP HANA](https://launchpad.support.sap.com/#/notes/2183363) .  

6. Aby zoptymalizować SAP HANA dla bazowego magazynu plików NetApp platformy Azure, ustaw następujące parametry SAP HANA:

   - `max_parallel_io_requests` **128**
   - `async_read_submit` **na**
   - `async_write_submit_active` **na**
   - `async_write_submit_blocks` **wszystkie**

   Aby uzyskać szczegółowe informacje, zobacz [SAP HANA w systemach NETAPP AFFs with NFS Configuration Guide](https://www.netapp.com/us/media/tr-4435.pdf). 

   Począwszy od systemów SAP HANA 2,0 można ustawić parametry w `global.ini`. Zobacz uwagi dotyczące oprogramowania SAP [1999930](https://launchpad.support.sap.com/#/notes/1999930).  
   W przypadku systemów SAP HANA 1,0 w wersjach do SPS12 te parametry można ustawić podczas instalacji, zgodnie z opisem w artykule SAP Uwaga [2267798](https://launchpad.support.sap.com/#/notes/2267798).  

7. Magazyn używany przez Azure NetApp Files ma ograniczenie rozmiaru pliku lub 16 TB. SAP HANA nie jest niejawnie świadomi ograniczenia magazynu i nie będzie automatycznie tworzyć nowego pliku danych, gdy zostanie osiągnięty limit rozmiaru pliku wynoszący 16 TB. Ponieważ SAP HANA próbuje zwiększyć plik poza 16 TB, co spowoduje błędy i ostatecznie indeksuje awarię serwera. 

   > [!IMPORTANT]
   > Aby zapobiec SAP HANA próbować zwiększyć ilość plików danych przekracza [Limit 16TB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-resource-limits) magazynu sybsystem, ustaw następujące parametry w `global.ini`.  
   > -  datavolume_striping = true
   > - datavolume_striping_size_gb = 15000 Aby uzyskać szczegółowe informacje, zobacz temat SAP Uwaga [2400005](https://launchpad.support.sap.com/#/notes/2400005).
   > Uwaga dotycząca oprogramowania SAP Note [2631285](https://launchpad.support.sap.com/#/notes/2631285). 

## <a name="test-sap-hana-failover"></a>Testowanie pracy w trybie failover SAP HANA 

1. Symulowanie awarii węzła w węźle procesu roboczego SAP HANA  

   Uruchom następujące polecenia jako **hn1**adm, aby przechwycić stan środowiska, przed symulacją awarii węzła.  

   <pre><code>
    # Check the landscape status
    python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/landscapeHostConfiguration.py
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

   Uruchom następujące polecenie jako element główny w węźle procesu roboczego, który jest w tym przypadku **hanadb2** , aby symulować awarię węzła.  
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   Monitoruj system do ukończenia pracy w trybie failover. Po zakończeniu przechwycenia w trybie failover stan powinien wyglądać podobnie do przedstawionego poniżej.  
   <pre><code>
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    # Check the landscape status
    /usr/sap/HN1/HDB03/exe/python_support> python landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | no     | info   |          |        |         2 |         0 | default  | default  | master 2   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb3 | yes    | info   |          |        |         0 |         2 | default  | default  | master 3   | slave      | standby     | slave       | standby | worker  | default | default |
   </code></pre>

   > [!IMPORTANT]
   > Aby uniknąć opóźnień SAP HANA trybu failover, gdy węzeł awaryjnego jądra, ustaw `kernel.panic` na 20 sekund na **wszystkich** maszynach wirtualnych Hana. Konfiguracja odbywa się w `/etc/sysctl`. Uruchom ponownie maszyny wirtualne, aby aktywować zmianę. Tryb failover, gdy w węźle występuje awaryjnego jądra, może upłynąć 10 lub więcej minut, jeśli ta zmiana nie zostanie wykonana.  

2. Kasuj serwer nazw  
   Uruchom następujące polecenia jako **hn1**adm, aby sprawdzić stan środowiska, przed rozpoczęciem testu:  

   <pre><code>
    #Landscape status 
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
   </code></pre>

   Uruchom następujące polecenia jako **hn1**adm w aktywnym węźle głównym, który jest w tym przypadku **hanadb1**.  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   **Hanadb3** węzła w stanie wstrzymania przejdzie jako węzeł główny. Stan zasobu po zakończeniu testu trybu failover:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03 -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
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

   Uruchom ponownie wystąpienie platformy HANA w systemie **hanadb1**, czyli na tej samej maszynie wirtualnej, na której serwer nazw został zamknięty. Węzeł **hanadb1** ponownie przywróci środowisko i zachowa jego rolę w stanie wstrzymania.  
   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Po rozpoczęciu SAP HANA na **hanadb1**wystąpi następujący stan:  
   <pre><code>
    # Check the instance status
    sapcontrol -nr 03 -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   Teraz należy ponownie skasować serwer nazw na aktualnie aktywnym węźle głównym, czyli w hanadb3.  
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   Węzeł **hanadb1** wznowi rolę węzła głównego. Stan po zakończeniu testu trybu failover będzie wyglądać następująco:
   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
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

   Rozpocznij SAP HANA na **hanadb3** — będzie on gotowy do pełnienia rolę węzła w stanie gotowości.  
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Stan po rozpoczęciu SAP HANA **hanadb3**.  
   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
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

* [Planowanie i wdrażanie Virtual Machines platformy Azure dla oprogramowania SAP][planning-guide]
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP][dbms-guide]
* Aby dowiedzieć się, jak ustanowić wysoką dostępność i plan odzyskiwania po awarii dla oprogramowania SAP 
* HANA na platformie Azure (duże wystąpienia), zobacz [SAP HANA (duże wystąpienia) wysoka dostępność i odzyskiwanie po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md).
* Aby dowiedzieć się, jak zapewnić wysoką dostępność i zaplanować odzyskiwanie po awarii SAP HANA na maszynach wirtualnych platformy Azure, zobacz [wysoka dostępność SAP HANA na platformie azure Virtual Machines (maszyny wirtualne)][sap-hana-ha]

---
title: Wysoka dostępność maszyn wirtualnych platformy Azure dla sap NetWeaver w sles | Dokumenty firmy Microsoft
description: Przewodnik po wysokiej dostępności dla SAP NetWeaver na SUSE Linux Enterprise Server dla aplikacji SAP
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
ms.date: 03/26/2020
ms.author: radeltch
ms.openlocfilehash: 05effb7d2e64c5f27acabad4b086ba27d6849cc8
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348821"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-for-sap-applications"></a>Wysoka dostępność sap NetWeaver na maszynach wirtualnych platformy Azure na suse Linux Enterprise Server dla aplikacji SAP

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

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

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

W tym artykule opisano sposób wdrażania maszyn wirtualnych, konfigurowania maszyn wirtualnych, instalowania struktury klastra i instalowania wysoce dostępnego systemu SAP NetWeaver 7.50.
W przykładowych konfiguracjach, poleceniach instalacji itp. Używany jest numer wystąpienia ASCS 00, numer wystąpienia ERS 02 i identyfikator systemu SAP NW1. Nazwy zasobów (na przykład maszyny wirtualne, sieci wirtualne) w przykładzie zakładają, że użyto [szablonu konwergentnego][template-converged] z sap system ID NW1 do utworzenia zasobów.

Najpierw przeczytaj poniższe uwagi i dokumenty SAP

* Uwaga SAP [1928533][1928533], która ma:
  * Lista rozmiarów maszyn wirtualnych platformy Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP
  * Ważne informacje o pojemności dla rozmiarów maszyn wirtualnych platformy Azure
  * Obsługiwane oprogramowanie SAP oraz system operacyjny (OS) i kombinacje baz danych
  * Wymagana wersja jądra SAP dla systemu Windows i Linux na platformie Microsoft Azure

* Uwaga SAP [2015553][2015553] zawiera listę wymagań wstępnych dla wdrożeń oprogramowania SAP obsługiwanych przez SAP na platformie Azure.
* Sap Note [2205917][2205917] ma zalecane ustawienia systemu operacyjnego dla SUSE Linux Enterprise Server dla aplikacji SAP
* Sap Note [1944799][1944799] ma wytyczne SAP HANA dla SUSE Linux Enterprise Server dla aplikacji SAP
* Uwaga SAP [2178632][2178632] zawiera szczegółowe informacje na temat wszystkich metryk monitorowania zgłoszonych dla sap na platformie Azure.
* Sap Note [2191498][2191498] ma wymaganą wersję agenta hosta SAP dla systemu Linux na platformie Azure.
* Uwaga SAP [2243692][2243692] ma informacje o licencjonowaniu SAP w systemie Linux na platformie Azure.
* Sap Note [1984787][1984787] ma ogólne informacje na temat SUSE Linux Enterprise Server 12.
* Uwaga SAP [1999351][1999351] zawiera dodatkowe informacje dotyczące rozwiązywania problemów z rozszerzeniem rozszerzonego monitorowania platformy Azure dla systemu SAP.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) ma wszystkie wymagane notatki SAP dla Linuksa.
* [Planowanie i implementacja maszyn wirtualnych platformy Azure dla systemu SAP w systemie Linux][planning-guide]
* [Wdrożenie maszyn wirtualnych platformy Azure dla systemu SAP w systemie Linux][deployment-guide]
* [Wdrożenie usługi DBMS maszyn wirtualnych platformy Azure dla systemu SAP w systemie Linux][dbms-guide]
* [Przewodniki po najlepszych praktykach SUSE SAP HA][suse-ha-guide] Przewodniki zawierają wszystkie wymagane informacje do skonfigurowania netweaver HA i SAP HANA System Replication lokalnie. Użyj tych przewodników jako ogólnej linii bazowej. Dostarczają one znacznie bardziej szczegółowych informacji.
* [Informacje o wersji SUSE High Availability Extension 12 SP3][suse-ha-12sp3-relnotes]

## <a name="overview"></a>Omówienie

Aby osiągnąć wysoką dostępność, SAP NetWeaver wymaga serwera NFS. Serwer NFS jest skonfigurowany w oddzielnym klastrze i może być używany przez wiele systemów SAP.

![Omówienie wysokiej dostępności sap NetWeaver](./media/high-availability-guide-suse/ha-suse.png)

Serwer NFS, SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS i baza danych SAP HANA używają wirtualnej nazwy hosta i wirtualnych adresów IP. Na platformie Azure moduł równoważenia obciążenia jest wymagany do używania wirtualnego adresu IP. Zalecamy użycie [standardowego modułu równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal). Na poniższej liście przedstawiono konfigurację (A)SCS i ERS load balancer.

### <a name="ascs"></a>1lit. Scs

* Konfiguracja frontu
  * Adres IP 10.0.0.7
* Port sondy
  * Port 620<strong>&lt;nr&gt;</strong>
* Reguły równoważenia obciążenia
  * W przypadku korzystania ze standardowego modułu równoważenia obciążenia wybierz **porty wysokiej jakości**
  * W przypadku korzystania z podstawowego modułu równoważenia obciążenia należy utworzyć reguły równoważenia obciążenia dla następujących portów
    * 32<strong>&lt;&gt; nr</strong> TCP
    * 36<strong>&lt;&gt; nr</strong> TCP
    * 39<strong>&lt;&gt; nr</strong> TCP
    * 81<strong>&lt;&gt; nr</strong> TCP
    * 5<strong>&lt;&gt;nr</strong>13 TCP
    * 5<strong>&lt;&gt;nr</strong>14 TCP
    * 5<strong>&lt;&gt;nr</strong>16 TCP

### <a name="ers"></a>Ers

* Konfiguracja frontu
  * Adres IP 10.0.0.8
* Port sondy
  * Port 621<strong>&lt;nr&gt;</strong>
* Reguły równoważenia obciążenia
  * W przypadku korzystania ze standardowego modułu równoważenia obciążenia wybierz **porty wysokiej jakości**
  * W przypadku korzystania z podstawowego modułu równoważenia obciążenia należy utworzyć reguły równoważenia obciążenia dla następujących portów
    * 32<strong>&lt;&gt; nr</strong> TCP
    * 33<strong>&lt;&gt; nr</strong> TCP
    * 5<strong>&lt;&gt;nr</strong>13 TCP
    * 5<strong>&lt;&gt;nr</strong>14 TCP
    * 5<strong>&lt;&gt;nr</strong>16 TCP

* Konfiguracja wewnętrznej bazy danych
  * Połączone z podstawowymi interfejsami sieciowymi wszystkich maszyn wirtualnych, które powinny być częścią klastra (A)SCS/ERS


## <a name="setting-up-a-highly-available-nfs-server"></a>Konfigurowanie serwera systemu plików NFS o wysokiej dostępności

SAP NetWeaver wymaga udostępnionego magazynu dla katalogu transportu i profilu. Przeczytaj [wysoką dostępność systemu plików NFS na maszynach wirtualnych platformy Azure na serwerze SUSE Linux Enterprise Server,][nfs-ha] aby dowiedzieć się, jak skonfigurować serwer NFS dla sap NetWeaver.

## <a name="setting-up-ascs"></a>Konfigurowanie (A)SCS

Szablon platformy Azure z usługi GitHub można użyć do wdrożenia wszystkich wymaganych zasobów platformy Azure, w tym maszyn wirtualnych, zestawu dostępności i modułu równoważenia obciążenia lub można ręcznie wdrożyć zasoby.

### <a name="deploy-linux-via-azure-template"></a>Wdrażanie systemu Linux za pośrednictwem szablonu platformy Azure

Portal Azure Marketplace zawiera obraz dla systemu SUSE Linux Enterprise Server for SAP Applications 12, którego można użyć do wdrożenia nowych maszyn wirtualnych. Obraz portalu marketplace zawiera agenta zasobów sap NetWeaver.

Można użyć jednego z szablonów szybkiego startu w usłudze GitHub, aby wdrożyć wszystkie wymagane zasoby. Szablon wdraża maszyny wirtualne, moduł równoważenia obciążenia, zestaw dostępności itp. Aby wdrożyć szablon, wykonaj następujące czynności:

1. Otwórz [szablon ASCS/SCS Multi SID][template-multisid-xscs] lub [szablon konwergentny][template-converged] w witrynie Azure portal. 
   Szablon ASCS/SCS tworzy tylko reguły równoważenia obciążenia dla wystąpień SAP NetWeaver ASCS/SCS i ERS (tylko linux), podczas gdy szablon konwergentny tworzy również reguły równoważenia obciążenia dla bazy danych (na przykład Microsoft SQL Server lub SAP HANA). Jeśli planujesz zainstalować system oparty na SAP NetWeaver i chcesz również zainstalować bazę danych na tych samych komputerach, użyj [szablonu konwergentnego][template-converged].
1. Wprowadź następujące parametry
   1. Prefiks zasobu (tylko szablon ASCS/SCS Multi SID)  
      Wprowadź prefiks, którego chcesz użyć. Wartość jest używana jako prefiks dla zasobów, które są wdrażane.
   3. Sap System ID (tylko szablon konwergentny)  
      Wprowadź identyfikator systemu SAP systemu SAP, który chcesz zainstalować. Identyfikator jest używany jako prefiks dla zasobów, które są wdrażane.
   4. Typ stosu  
      Wybierz typ stosu SAP NetWeaver
   5. Typ systemu operacyjnego  
      Wybierz jedną z dystrybucji Linuksa. W tym przykładzie wybierz SLES 12 BYOS
   6. Typ bazy danych  
      Wybierz HANA
   7. Rozmiar systemu SAP.  
      Ilość SAPS nowy system zapewnia. Jeśli nie masz pewności, ile saps wymaga systemu, zapytaj swojego partnera sap technology lub integratora systemów
   8. Dostępność systemu  
      Wybierz HA
   9. Nazwa użytkownika i hasło administratora  
      Tworzony jest nowy użytkownik, który może służyć do logowania się do komputera.
   10. Identyfikator podsieci  
   Jeśli chcesz wdrożyć maszynę wirtualną w istniejącej sieci wirtualnej, w której zdefiniowano podsieć, do której powinna być przypisana maszyna wirtualna, nazwij identyfikator tej określonej podsieci. Identyfikator zwykle wygląda jak /subskrypcje/**&lt;&gt;identyfikator subskrypcji**/resourceGroups/**&lt;nazwa&gt;grupy zasobów**/providers/Microsoft.Network/virtualNetworks/**&lt;nazwa&gt;sieci wirtualnej**/podsieci/**&lt;nazwa&gt; podsieci**

### <a name="deploy-linux-manually-via-azure-portal"></a>Ręczne wdrażanie systemu Linux za pośrednictwem witryny Azure portal

Najpierw należy utworzyć maszyny wirtualne dla tego klastra NFS. Następnie należy utworzyć moduł równoważenia obciążenia i używać maszyn wirtualnych w puli wewnętrznej bazy danych.

1. Tworzenie grupy zasobów
1. Tworzenie sieci wirtualnej
1. Tworzenie zestawu dostępności  
   Ustawianie domeny aktualizacji maksymalnej
1. Tworzenie maszyny wirtualnej 1  
   Użyj co najmniej SLES4SAP 12 SP1, w tym przykładzie obraz SLES4SAP 12 z zw.https://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   SLES Dla aplikacji SAP 12 SP1 jest używany  
   Wybierz zestaw dostępności utworzony wcześniej  
1. Tworzenie maszyny wirtualnej 2  
   Użyj co najmniej SLES4SAP 12 SP1, w tym przykładzie obraz SLES4SAP 12 z zw.https://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   SLES Dla aplikacji SAP 12 SP1 jest używany  
   Wybierz zestaw dostępności utworzony wcześniej  
1. Dodawanie co najmniej jednego dysku danych do obu maszyn wirtualnych  
   Dyski danych są używane dla katalogu /usr/sap/`<SAPSID`>
1. Tworzenie modułu równoważenia obciążenia (wewnętrznego, standardowego):  
   1. Tworzenie adresów IP frontendu
      1. Adres IP 10.0.0.7 dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pulę adresów IP frontu i kliknij przycisk Dodaj
         1. Wprowadź nazwę nowej puli IP frontendu (na przykład **nw1-ascs-frontend)**
         1. Ustaw przypisanie na Statyczne i wprowadź adres IP (na przykład **10.0.0.7**)
         1. Kliknij przycisk OK
      1. Adres IP 10.0.0.8 dla ASCS ERS
         * Powtórz powyższe kroki, aby utworzyć adres IP dla ERS (na przykład **10.0.0.8** i **nw1-aers-backend)**
   1. Tworzenie puli zaplecza
      1. Otwórz moduł równoważenia obciążenia, wybierz pule wewnętrznej bazy danych i kliknij przycisk Dodaj
      1. Wprowadź nazwę nowej puli wewnętrznej bazy danych (na przykład **nw1-backend)**
      1. Kliknij pozycję Dodaj maszynę wirtualną.
      1. Wybierz maszynę wirtualną
      1. Wybierz maszyny wirtualne klastra (A)SCS i ich adresy IP.
      1. Kliknij pozycję Dodaj.
   1. Tworzenie sond kondycji
      1. Port 620**00** dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz sondy kondycji i kliknij przycisk Dodaj
         1. Wprowadź nazwę nowej sondy kondycji (na przykład **nw1-ascs-hp**)
         1. Wybierz protokół TCP jako protokół, port 620**00**, zachowaj interwał 5 i próg w złej kondycji 2
         1. Kliknij przycisk OK
      1. Port 621**02** dla ASCS ERS
         * Powtórz powyższe kroki, aby utworzyć sondę kondycji dla ERS (na przykład 621**02** i **nw1-aers-hp)**
   1. Reguły równoważenia obciążenia
      1. Reguły równoważenia obciążenia dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz reguły równoważenia obciążenia i kliknij przycisk Dodaj
         1. Wprowadź nazwę nowej reguły równoważenia obciążenia (na przykład **nw1-lb-ascs)**
         1. Wybierz adres IP frontendu, pulę wewnętrznej bazy danych i sondę kondycji utworzoną wcześniej (na przykład **nw1-ascs-frontend,** **nw1-backend** i **nw1-ascs-hp**)
         1. Wybieranie **portów wysokiej haw**
         1. Wydłużenie limitu czasu bezczynnego do 30 minut
         1. **Upewnij się, że włączysz pływający adres IP**
         1. Kliknij przycisk OK
         * Powtórz powyższe kroki, aby utworzyć reguły równoważenia obciążenia dla ERS (na przykład **nw1-lb-ers**)
1. Alternatywnie, jeśli scenariusz wymaga podstawowego modułu równoważenia obciążenia (wewnętrznego), wykonaj następujące kroki:  
   1. Tworzenie adresów IP frontendu
      1. Adres IP 10.0.0.7 dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pulę adresów IP frontu i kliknij przycisk Dodaj
         1. Wprowadź nazwę nowej puli IP frontendu (na przykład **nw1-ascs-frontend)**
         1. Ustaw przypisanie na Statyczne i wprowadź adres IP (na przykład **10.0.0.7**)
         1. Kliknij przycisk OK
      1. Adres IP 10.0.0.8 dla ASCS ERS
         * Powtórz powyższe kroki, aby utworzyć adres IP dla ERS (na przykład **10.0.0.8** i **nw1-aers-frontend)**
   1. Tworzenie puli zaplecza
      1. Otwórz moduł równoważenia obciążenia, wybierz pule wewnętrznej bazy danych i kliknij przycisk Dodaj
      1. Wprowadź nazwę nowej puli wewnętrznej bazy danych (na przykład **nw1-backend)**
      1. Kliknij pozycję Dodaj maszynę wirtualną.
      1. Wybierz utworzony wcześniej zestaw dostępności
      1. Wybieranie maszyn wirtualnych klastra (A)SCS
      1. Kliknij przycisk OK
   1. Tworzenie sond kondycji
      1. Port 620**00** dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz sondy kondycji i kliknij przycisk Dodaj
         1. Wprowadź nazwę nowej sondy kondycji (na przykład **nw1-ascs-hp**)
         1. Wybierz protokół TCP jako protokół, port 620**00**, zachowaj interwał 5 i próg w złej kondycji 2
         1. Kliknij przycisk OK
      1. Port 621**02** dla ASCS ERS
         * Powtórz powyższe kroki, aby utworzyć sondę kondycji dla ERS (na przykład 621**02** i **nw1-aers-hp)**
   1. Reguły równoważenia obciążenia
      1. 32**00** TCP dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz reguły równoważenia obciążenia i kliknij przycisk Dodaj
         1. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład **nw1-lb-3200**)
         1. Wybierz adres IP frontendu, pulę wewnętrznej bazy danych i sondę kondycji utworzoną wcześniej (na przykład **nw1-ascs-frontend)**
         1. Zachowaj protokół **TCP**, wprowadź port **3200**
         1. Wydłużenie limitu czasu bezczynnego do 30 minut
         1. **Upewnij się, że włączysz pływający adres IP**
         1. Kliknij przycisk OK
      1. Dodatkowe porty dla ASCS
         * Powtórz powyższe kroki dla portów 36**00,** 39**00,** 81**00,** 5**00**13, 5**00**14, 5**00**16 i TCP dla ASCS
      1. Dodatkowe porty dla ASCS ERS
         * Powtórz powyższe kroki dla portów 33**02**,**5 02**13, 5**02**14, 5**02**16 i TCP dla ASCS ERS

> [!Note]
> Gdy maszyny wirtualne bez publicznych adresów IP są umieszczane w puli wewnętrznej bazy danych wewnętrznego (bez publicznego adresu IP) Standardowy moduł równoważenia obciążenia platformy Azure, nie będzie żadnych wychodzących połączeń z Internetem, chyba że zostanie wykonana dodatkowa konfiguracja, aby umożliwić routing do publicznych punktów końcowych. Aby uzyskać szczegółowe informacje na temat sposobu osiągnięcia łączności wychodzącej, zobacz [Łączność publiczna dla maszyn końcowych przy użyciu standardowego modułu równoważenia obciążenia platformy Azure w scenariuszach wysokiej dostępności SAP.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)  

> [!IMPORTANT]
> Nie należy włączać sygnatur czasowych TCP na maszynach wirtualnych platformy Azure umieszczonych za modułem równoważenia obciążenia platformy Azure. Włączenie sygnatur czasowych TCP spowoduje niepowodzenie sond kondycji. Ustaw parametr **net.ipv4.tcp_timestamps** na **0**. Aby uzyskać szczegółowe informacje, zobacz [Sondy kondycji modułu równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

### <a name="create-pacemaker-cluster"></a>Tworzenie klastra rozrusznika serca

Wykonaj kroki opisane w [sekcji Konfigurowanie rozrusznika serca na suse linux enterprise server na platformie Azure,](high-availability-guide-suse-pacemaker.md) aby utworzyć podstawowy klaster rozrusznika serca dla tego (A)serwera SCS.

### <a name="installation"></a>Instalacja

Następujące elementy są poprzedzone **[A]** - ma zastosowanie do wszystkich węzłów, **[1]** - dotyczy tylko węzła 1 lub **[2]** - dotyczy tylko węzła 2.

1. **[A]** Instalowanie złącza SUSE

   <pre><code>sudo zypper install sap-suse-cluster-connector
   </code></pre>

   > [!NOTE]
   > Znany problem z używaniem myślnika w nazwach hostów został rozwiązany w wersji **3.1.1** pakietu **sap-suse-cluster-connector**. Upewnij się, że używasz co najmniej wersji 3.1.1 pakietu sap-suse-cluster-connector, jeśli używasz węzłów klastra z kreską w nazwie hosta. W przeciwnym razie klaster nie będzie działać. 

   Upewnij się, że zainstalowano nową wersję łącznika klastra SAP SUSE. Stary został nazwany sap_suse_cluster_connector, a nowy nazywa **się sap-suse-cluster-connector**.

   ```
   sudo zypper info sap-suse-cluster-connector
   
   Information for package sap-suse-cluster-connector:
   ---------------------------------------------------
   Repository     : SLE-12-SP3-SAP-Updates
   Name           : sap-suse-cluster-connector
   <b>Version        : 3.0.0-2.2</b>
   Arch           : noarch
   Vendor         : SUSE LLC <https://www.suse.com/>
   Support Level  : Level 3
   Installed Size : 41.6 KiB
   <b>Installed      : Yes</b>
   Status         : up-to-date
   Source package : sap-suse-cluster-connector-3.0.0-2.2.src
   Summary        : SUSE High Availability Setup for SAP Products
   ```

1. **[A]** Aktualizowanie agentów zasobów SAP  
   
   Poprawka dla pakietu agentów zasobów jest wymagana do użycia nowej konfiguracji, która jest opisana w tym artykule. Można sprawdzić, czy poprawka jest już zainstalowana za pomocą następującego polecenia

   <pre><code>sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   Wydajność powinna być podobna do

   <pre><code>&lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   Jeśli polecenie grep nie znajduje parametru IS_ERS, należy zainstalować poprawkę wymienioną na [stronie pobierania SUSE](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents)

   <pre><code># example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

1. **[A]** Rozpoznawanie nazw hostów instalatora

   Można użyć serwera DNS lub zmodyfikować /etc/hosts we wszystkich węzłach. W tym przykładzie pokazano, jak używać pliku /etc/hosts.
   Zastąp adres IP i nazwa hosta w następujących poleceniach

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Wstaw następujące wiersze do /etc/hosts. Zmienianie adresu IP i nazwy hosta w celu dopasowania go do środowiska   

   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>Przygotowanie do instalacji SAP NetWeaver

1. **[A]** Tworzenie katalogów udostępnionych

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>NW1</b>/SYS
   sudo mkdir -p /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>NW1</b>/ERS<b>02</b>
   
   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>NW1</b>/SYS
   sudo chattr +i /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[A]** Konfigurowanie autofs

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Tworzenie pliku za pomocą

   <pre><code>sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   /usr/sap/<b>NW1</b>/SYS -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sidsys
   </code></pre>

   Uruchom ponownie autofs, aby zamontować nowe udziały

   <pre><code>sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[A]** Konfigurowanie pliku SWAP

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Uruchom ponownie agenta, aby aktywować zmianę

   <pre><code>sudo service waagent restart
   </code></pre>


### <a name="installing-sap-netweaver-ascsers"></a>Instalacja SAP NetWeaver ASCS/ERS

1. **[1]** Tworzenie wirtualnego zasobu IP i sondy kondycji dla wystąpienia ASCS

   > [!IMPORTANT]
   > Ostatnie testy ujawniły sytuacje, w których netcat przestaje odpowiadać na żądania z powodu zaległości i ograniczenia obsługi tylko jednego połączenia. Zasób netcat przestaje nasłuchiwać żądań modułu równoważenia obciążenia platformy Azure, a zmienny adres IP staje się niedostępny.  
   > W przypadku istniejących klastrów rozruszników zalecamy w przeszłości zastąpienie netcata socatem. Obecnie zalecamy użycie agenta zasobów azure-lb, który jest częścią agentów zasobów pakietu, z następującymi wymaganiami dotyczącymi wersji pakietu:
   > - W przypadku SLES 12 SP4/SP5 wersja musi być co najmniej resource-agents-4.3.018.a7fb5035-3.30.1.  
   > - Dla SLES 15/15 SP1 wersja musi być co najmniej resource-agents-4.3.0184.6ee15eb2-4.13.1.  
   >
   > Należy pamiętać, że zmiana będzie wymagać krótkiego przestoju.  
   > W przypadku istniejących klastrów stymulatora, jeśli konfiguracja została już zmieniona, aby używać socat zgodnie z opisem w [umacnianiu wykrywania równoważenia obciążenia azure,](https://www.suse.com/support/kb/doc/?id=7024128)nie ma wymogu natychmiastowego przejścia na agenta zasobów azure-lb.

   <pre><code>sudo crm node standby <b>nw1-cl-1</b>
   
   sudo crm configure primitive fs_<b>NW1</b>_ASCS Filesystem device='<b>nw1-nfs</b>:/<b>NW1</b>/ASCS' directory='/usr/sap/<b>NW1</b>/ASCS<b>00</b>' fstype='nfs4' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>NW1</b>_ASCS IPaddr2 \
     params ip=<b>10.0.0.7</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ASCS azure-lb port=620<b>00</b>
   
   sudo crm configure group g-<b>NW1</b>_ASCS fs_<b>NW1</b>_ASCS nc_<b>NW1</b>_ASCS vip_<b>NW1</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   Upewnij się, że stan klastra jest w porządku i że wszystkie zasoby są uruchamiane. Nie jest ważne, w którym węźle są uruchomione zasoby.

   <pre><code>sudo crm_mon -r
   
   # Node nw1-cl-1: standby
   # <b>Online: [ nw1-cl-0 ]</b>
   # 
   # Full list of resources:
   # 
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-0</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-0</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   </code></pre>

1. **[1]** Zainstaluj SAP NetWeaver ASCS  

   Zainstaluj SAP NetWeaver ASCS jako root w pierwszym węźle przy użyciu wirtualnej nazwy hosta, która mapuje adres IP konfiguracji frontendu modułu równoważenia obciążenia dla ASCS, na przykład <b>nw1-ascs</b>, <b>10.0.0.7</b> i numer wystąpienia użyty do sondy modułu równoważenia obciążenia, na przykład <b>00</b>.

   Parametr sapinst można użyć SAPINST_REMOTE_ACCESS_USER, aby umożliwić użytkownikowi niebędącemu rootem łączenie się z sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Jeśli instalacja nie może utworzyć podfolderu w /usr/sap/**NW1**/ASCS**00**, spróbuj skonfigurować właściciela i grupę folderu ASCS**00** i ponowić próbę.

   <pre><code>chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]** Tworzenie wirtualnego zasobu IP i sondy kondycji dla wystąpienia ERS

   <pre><code>sudo crm node online <b>nw1-cl-1</b>
   sudo crm node standby <b>nw1-cl-0</b>
   
   sudo crm configure primitive fs_<b>NW1</b>_ERS Filesystem device='<b>nw1-nfs</b>:/<b>NW1</b>/ASCSERS' directory='/usr/sap/<b>NW1</b>/ERS<b>02</b>' fstype='nfs4' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>NW1</b>_ERS IPaddr2 \
     params ip=<b>10.0.0.8</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ERS azure-lb port=621<b>02</b>
   
   sudo crm configure group g-<b>NW1</b>_ERS fs_<b>NW1</b>_ERS nc_<b>NW1</b>_ERS vip_<b>NW1</b>_ERS
   </code></pre>

   Upewnij się, że stan klastra jest w porządku i że wszystkie zasoby są uruchamiane. Nie jest ważne, w którym węźle są uruchomione zasoby.

   <pre><code>sudo crm_mon -r
   
   # Node <b>nw1-cl-0: standby</b>
   # <b>Online: [ nw1-cl-1 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      fs_NW1_ERS (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ERS (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   </code></pre>

1. **[2]** Zainstaluj SAP NetWeaver ERS

   Zainstaluj SAP NetWeaver ERS jako root w drugim węźle przy użyciu wirtualnej nazwy hosta, która mapuje adres IP konfiguracji frontendu modułu równoważenia obciążenia dla ERS, na przykład <b>nw1-aers</b>, <b>10.0.0.8</b> i numer wystąpienia użyty do sondy modułu równoważenia obciążenia, na przykład <b>02</b>.

   Parametr sapinst można użyć SAPINST_REMOTE_ACCESS_USER, aby umożliwić użytkownikowi niebędącemu rootem łączenie się z sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   > [!NOTE]
   > Użyj SWPM SP 20 PL 05 lub nowszego. Niższe wersje nie ustawiają poprawnie uprawnień, a instalacja zakończy się niepowodzeniem.

   Jeśli instalacja nie może utworzyć podfolderu w /usr/sap/**NW1**/ERS**02**, spróbuj skonfigurować właściciela i grupę folderu ERS**02** i ponowić próbę.

   <pre><code>chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>


1. **[1]** Dostosowywanie profili wystąpień ASCS/SCS i ERS
 
   * Profil ASCS/SCS

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   </code></pre>

   * Profil ERS

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>

1. **[A]** Konfigurowanie keep alive

   Komunikacja między serwerem aplikacji SAP NetWeaver a ASCS/SCS jest routowana za pośrednictwem modułu równoważenia obciążenia oprogramowania. Moduł równoważenia obciążenia rozłącza nieaktywne połączenia po konfigurowalnym przesunieniu czasu. Aby temu zapobiec, należy ustawić parametr w profilu SAP NetWeaver ASCS/SCS i zmienić ustawienia systemu Linux. Więcej informacji można znaleźć [w notatce SAP 1410736.][1410736]

   Parametr profilu ASCS/SCS enque/encni/set_so_keepalive został już dodany w ostatnim kroku.

   <pre><code># Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

1. **[A]** Konfigurowanie użytkowników SAP po instalacji

   <pre><code># Add sidadm to the haclient group
   sudo usermod -aG haclient <b>nw1</b>adm
   </code></pre>

1. **[1]** Dodawanie usług ASCS i ERS SAP do pliku sapservice

   Dodaj wpis usługi ASCS do drugiego węzła i skopiuj wpis usługi ERS do pierwszego węzła.

   <pre><code>cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>nw1-cl-1</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>nw1-cl-1</b> "cat /usr/sap/sapservices" | grep ERS<b>02</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

1. **[1]** Tworzenie zasobów klastra SAP

W przypadku korzystania z architektury serwera 1 (ENSA1) należy zdefiniować zasoby w następujący sposób:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ERS<b>02</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000
   
   sudo crm configure modgroup g-<b>NW1</b>_ASCS add rsc_sap_<b>NW1</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>NW1</b>_ERS add rsc_sap_<b>NW1</b>_ERS<b>02</b>
   
   sudo crm configure colocation col_sap_<b>NW1</b>_no_both -5000: g-<b>NW1</b>_ERS g-<b>NW1</b>_ASCS
   sudo crm configure location loc_sap_<b>NW1</b>_failover_to_ers rsc_sap_<b>NW1</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>NW1</b> eq 1
   sudo crm configure order ord_sap_<b>NW1</b>_first_start_ascs Optional: rsc_sap_<b>NW1</b>_ASCS<b>00</b>:start rsc_sap_<b>NW1</b>_ERS<b>02</b>:stop symmetrical=false
   
   sudo crm node online <b>nw1-cl-0</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

  Firma SAP wprowadziła obsługę serwera 2, w tym replikacji, od momentu wprowadzenia sap NW 7.52. Począwszy od platformy ABAP 1809, serwer 2 jest instalowany domyślnie. Zobacz SAP note [2630416,](https://launchpad.support.sap.com/#/notes/2630416) aby uzyskać pomoc techniczną serwera 2.
W przypadku korzystania z architektury serwera 2 w kolejce[(ENSA2)](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)należy zdefiniować zasoby w następujący sposób:

<pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ERS<b>02</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>" AUTOMATIC_RECOVER=false IS_ERS=true 
   
   sudo crm configure modgroup g-<b>NW1</b>_ASCS add rsc_sap_<b>NW1</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>NW1</b>_ERS add rsc_sap_<b>NW1</b>_ERS<b>02</b>
   
   sudo crm configure colocation col_sap_<b>NW1</b>_no_both -5000: g-<b>NW1</b>_ERS g-<b>NW1</b>_ASCS
   sudo crm configure order ord_sap_<b>NW1</b>_first_start_ascs Optional: rsc_sap_<b>NW1</b>_ASCS<b>00</b>:start rsc_sap_<b>NW1</b>_ERS<b>02</b>:stop symmetrical=false
   
   sudo crm node online <b>nw1-cl-0</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

  Jeśli uaktualniasz ze starszej wersji i przełączasz się na serwer 2 w kolejce, zobacz UWAGA SAP [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   Upewnij się, że stan klastra jest w porządku i że wszystkie zasoby są uruchamiane. Nie jest ważne, w którym węźle są uruchomione zasoby.


   <pre><code>sudo crm_mon -r
   
   # Online: <b>[ nw1-cl-0 nw1-cl-1 ]</b>
   #
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #      rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      fs_NW1_ERS (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-0</b>
   #      nc_NW1_ERS (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   #      rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-0</b>
   </code></pre>

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Przygotowanie serwera aplikacji SAP NetWeaver

Niektóre bazy danych wymagają, aby instalacja wystąpienia bazy danych była wykonywana na serwerze aplikacji. Przygotuj maszyny wirtualne serwera aplikacji, aby móc ich używać w takich przypadkach.

Kroki poniżej założono, że zainstalować serwer aplikacji na serwerze innym niż ASCS/SCS i HANA serwerów. W przeciwnym razie niektóre z poniższych kroków (takich jak konfigurowanie rozpoznawania nazw hostów) nie są potrzebne.

1. Konfigurowanie systemu operacyjnego

   Zmniejsz rozmiar brudnej pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [Niska wydajność zapisu na serwerach SLES 11/12 z dużą pamięcią RAM](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. Rozpoznawanie nazw hostów instalatora

   Można użyć serwera DNS lub zmodyfikować /etc/hosts we wszystkich węzłach. W tym przykładzie pokazano, jak używać pliku /etc/hosts.
   Zastąp adres IP i nazwa hosta w następujących poleceniach

   ```bash
   sudo vi /etc/hosts
   ```

   Wstaw następujące wiersze do /etc/hosts. Zmienianie adresu IP i nazwy hosta w celu dopasowania go do środowiska

   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   # IP address of all application servers
   <b>10.0.0.20 nw1-di-0</b>
   <b>10.0.0.21 nw1-di-1</b>
   </code></pre>

1. Tworzenie katalogu sapmnt

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. Konfigurowanie autofs

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Tworzenie nowego pliku za pomocą

   <pre><code>sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   </code></pre>

   Uruchom ponownie autofs, aby zamontować nowe udziały

   <pre><code>sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. Konfigurowanie pliku SWAP

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Uruchom ponownie agenta, aby aktywować zmianę

   <pre><code>sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Instalowanie bazy danych

W tym przykładzie SAP NetWeaver jest zainstalowany na SAP HANA. Do tej instalacji można użyć każdej obsługiwanej bazy danych. Aby uzyskać więcej informacji na temat instalowania systemu SAP HANA na platformie Azure, zobacz Wysoka dostępność sap HANA na maszynach wirtualnych platformy Azure .For more information on how to install SAP HANA in Azure, see [High Availability of SAP HANA on Azure Virtual Machines (VMs)][sap-hana-ha]. Aby uzyskać listę obsługiwanych baz danych, zobacz [UWAGA SAP 1928533][1928533].

1. Uruchamianie instalacji wystąpienia bazy danych SAP

   Zainstaluj wystąpienie bazy danych SAP NetWeaver jako katalog główny przy użyciu wirtualnej nazwy hosta, która jest mapowana na adres IP konfiguracji frontendu modułu równoważenia obciążenia dla bazy danych, na przykład <b>nw1-db</b> i <b>10.0.0.13</b>.

   Parametr sapinst można użyć SAPINST_REMOTE_ACCESS_USER, aby umożliwić użytkownikowi niebędącemu rootem łączenie się z sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Instalacja serwera aplikacji SAP NetWeaver

Wykonaj następujące kroki, aby zainstalować serwer aplikacji SAP.

1. Przygotowanie serwera aplikacji

   Wykonaj kroki opisane w rozdziale [Przygotowanie serwera aplikacji SAP NetWeaver](high-availability-guide-suse.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) powyżej, aby przygotować serwer aplikacji.

1. Instalowanie serwera aplikacji SAP NetWeaver

   Zainstaluj podstawowy lub dodatkowy serwer aplikacji SAP NetWeaver.

   Parametr sapinst można użyć SAPINST_REMOTE_ACCESS_USER, aby umożliwić użytkownikowi niebędącemu rootem łączenie się z sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. Aktualizacja bezpiecznego magazynu SAP HANA

   Zaktualizuj bezpieczny magazyn SAP HANA, aby wskazywał wirtualną nazwę konfiguracji replikacji systemu SAP HANA.

   Uruchom następujące polecenie, aby wyświetlić listę wpisów
   <pre><code>hdbuserstore List
   </code></pre>

   Powinno to wymieniać wszystkie wpisy i wyglądać podobnie do
   <pre><code>DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>HN1</b>
   </code></pre>

   Dane wyjściowe pokazują, że adres IP wpisu domyślnego wskazuje maszynę wirtualną, a nie adres IP modułu równoważenia obciążenia. Ten wpis musi zostać zmieniony, aby wskazać wirtualną nazwę hosta modułu równoważenia obciążenia. Upewnij się, że używasz tego samego portu **(30313** w powyższym wyjściu) i nazwy bazy danych **(HN1** w powyższym wyjściu)!

   <pre><code>su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db:30313@HN1</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>Testowanie konfiguracji klastra

Następujące testy są kopią przypadków testowych w przewodnikach najlepszych rozwiązań SUSE. Są one kopiowane dla Twojej wygody. Zawsze czytaj również najlepsze przewodniki i wykonaj wszystkie dodatkowe testy, które mogły zostać dodane.

1. Test HAGetFailoverConfig, HACheckConfig i HACheckFailoverConfig

   Uruchom następujące polecenia \<jako sapsid>adm w węźle, w którym jest aktualnie uruchomione wystąpienie ASCS. Jeśli polecenia nie powiodą się z FAIL: Niewystarczające pamięci, może to być spowodowane przez myślniki w nazwa hosta. Jest to znany problem i zostanie rozwiązany przez SUSE w pakiecie sap-suse-cluster-connector.

   <pre><code>nw1-cl-0:nw1adm 54> sapcontrol -nr <b>00</b> -function HAGetFailoverConfig
   
   # 15.08.2018 13:50:36
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: Toolchain Module
   # HASAPInterfaceVersion: Toolchain Module (sap_suse_cluster_connector 3.0.1)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode:
   # HANodes: nw1-cl-0, nw1-cl-1
   
   nw1-cl-0:nw1adm 55> sapcontrol -nr 00 -function HACheckConfig
   
   # 15.08.2018 14:00:04
   # HACheckConfig
   # OK
   # state, category, description, comment
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP instance configuration, 2 ABAP instances detected
   # SUCCESS, SAP CONFIGURATION, Redundant Java instance configuration, 0 Java instances detected
   # SUCCESS, SAP CONFIGURATION, Enqueue separation, All Enqueue server separated from application server
   # SUCCESS, SAP CONFIGURATION, MessageServer separation, All MessageServer separated from application server
   # SUCCESS, SAP CONFIGURATION, ABAP instances on multiple hosts, ABAP instances on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP SPOOL service configuration, 2 ABAP instances with SPOOL service detected
   # SUCCESS, SAP STATE, Redundant ABAP SPOOL service state, 2 ABAP instances with active SPOOL service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP SPOOL service on multiple hosts, ABAP instances with active ABAP SPOOL service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP BATCH service configuration, 2 ABAP instances with BATCH service detected
   # SUCCESS, SAP STATE, Redundant ABAP BATCH service state, 2 ABAP instances with active BATCH service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP BATCH service on multiple hosts, ABAP instances with active ABAP BATCH service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP DIALOG service configuration, 2 ABAP instances with DIALOG service detected
   # SUCCESS, SAP STATE, Redundant ABAP DIALOG service state, 2 ABAP instances with active DIALOG service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP DIALOG service on multiple hosts, ABAP instances with active ABAP DIALOG service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP UPDATE service configuration, 2 ABAP instances with UPDATE service detected
   # SUCCESS, SAP STATE, Redundant ABAP UPDATE service state, 2 ABAP instances with active UPDATE service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP UPDATE service on multiple hosts, ABAP instances with active ABAP UPDATE service on multiple hosts detected
   # SUCCESS, SAP STATE, SCS instance running, SCS instance status ok
   # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version (nw1-ascs_NW1_00), SAPInstance includes is-ers patch
   # SUCCESS, SAP CONFIGURATION, Enqueue replication (nw1-ascs_NW1_00), Enqueue replication enabled
   # SUCCESS, SAP STATE, Enqueue replication state (nw1-ascs_NW1_00), Enqueue replication active
   
   nw1-cl-0:nw1adm 56> sapcontrol -nr 00 -function HACheckFailoverConfig
   
   # 15.08.2018 14:04:08
   # HACheckFailoverConfig
   # OK
   # state, category, description, comment
   # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   </code></pre>

1. Ręczna migracja wystąpienia ASCS

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Uruchom następujące polecenia jako katalog główny, aby przeprowadzić migrację wystąpienia ASCS.

   <pre><code>nw1-cl-0:~ # crm resource migrate rsc_sap_NW1_ASCS00 force
   # INFO: Move constraint created for rsc_sap_NW1_ASCS00
   
   nw1-cl-0:~ # crm resource unmigrate rsc_sap_NW1_ASCS00
   # INFO: Removed migration constraints for rsc_sap_NW1_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stan zasobu po teście:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Test HAFailoverToNode

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Uruchom następujące polecenia \<jako sapsid>adm, aby przeprowadzić migrację instancji ASCS.

   <pre><code>nw1-cl-0:nw1adm 55> sapcontrol -nr 00 -host nw1-ascs -user nw1adm &lt;password&gt; -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   # Remove migration constraints
   nw1-cl-0:~ # crm resource clear rsc_sap_NW1_ASCS00
   #INFO: Removed migration constraints for rsc_sap_NW1_ASCS00
   </code></pre>

   Stan zasobu po teście:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Symulowanie awarii węzła

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Uruchom następujące polecenie jako katalog główny w węźle, w którym działa wystąpienie ASCS

   <pre><code>nw1-cl-0:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Jeśli używasz SBD, rozrusznik serca nie powinien automatycznie uruchamiać się na zabitym węźle. Stan po ponownym uruchomieniu węzła powinien wyglądać następująco.

   <pre><code>Online: [ nw1-cl-1 ]
   OFFLINE: [ nw1-cl-0 ]
   
   Full list of resources:
   
   stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   
   Failed Actions:
   * rsc_sap_NW1_ERS02_monitor_11000 on nw1-cl-1 'not running' (7): call=219, status=complete, exitreason='none',
       last-rc-change='Wed Aug 15 14:38:38 2018', queued=0ms, exec=0ms
   </code></pre>

   Użyj następujących poleceń, aby uruchomić rozrusznik serca w zabitym węźle, wyczyścić komunikaty SBD i wyczyścić nieudane zasoby.

   <pre><code># run as root
   # list the SBD device(s)
   nw1-cl-0:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   nw1-cl-0:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message nw1-cl-0 clear
   
   nw1-cl-0:~ # systemctl start pacemaker
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stan zasobu po teście:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Test ręcznego ponownego uruchomienia wystąpienia ASCS

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Utwórz blokadę enqueue przez, na przykład edytować użytkownika w transakcji su01. Uruchom następujące polecenia \<jako sapsid>adm w węźle, w którym działa wystąpienie ASCS. Polecenia zatrzyma wystąpienie ASCS i uruchomią go ponownie. Jeśli używasz architektury serwera 1 w kolejce, oczekuje się, że blokada enqueue zostanie utracona w tym teście. Jeśli używasz architektury serwera 2 w kolejce, ujmuje się w kolejce. 

   <pre><code>nw1-cl-1:nw1adm 54> sapcontrol -nr 00 -function StopWait 600 2
   </code></pre>

   Wystąpienie ASCS powinno być teraz wyłączone w rozruszniku serca

   <pre><code>rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Stopped (disabled)
   </code></pre>

   Uruchom wystąpienie ASCS ponownie w tym samym węźle.

   <pre><code>nw1-cl-1:nw1adm 54> sapcontrol -nr 00 -function StartWait 600 2
   </code></pre>

   Blokada enqueue transakcji su01 powinny zostać utracone i back-end powinien zostać zresetowany. Stan zasobu po teście:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Proces serwera wiadomości zabicia

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Uruchom następujące polecenia jako katalog główny, aby zidentyfikować proces serwera wiadomości i zabić go.

   <pre><code>nw1-cl-1:~ # pgrep ms.sapNW1 | xargs kill -9
   </code></pre>

   Jeśli tylko raz zabijesz serwer wiadomości, zostanie on ponownie uruchomiony przez sapstart. Jeśli zabijesz go wystarczająco często, rozrusznik serca ostatecznie przeniesie wystąpienie ASCS do innego węzła. Uruchom następujące polecenia jako katalog główny, aby oczyścić stan zasobu wystąpienia ASCS i ERS po teście.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stan zasobu po teście:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Zabij proces serwera w kolejce

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Uruchom następujące polecenia jako katalog główny w węźle, w którym jest uruchomione wystąpienie ASCS w celu zabicia serwera w kolejce.

   <pre><code>nw1-cl-0:~ # pgrep en.sapNW1 | xargs kill -9
   </code></pre>

   Wystąpienie ASCS należy natychmiast awaryjnie do innego węzła. Wystąpienie ERS należy również awaryjnie po uruchomieniu wystąpienia ASCS. Uruchom następujące polecenia jako katalog główny, aby oczyścić stan zasobu wystąpienia ASCS i ERS po teście.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stan zasobu po teście:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Zabij proces serwera replikacji w kolejce

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Uruchom następujące polecenie jako katalog główny w węźle, w którym jest uruchomione wystąpienie ERS, aby zabić proces serwera replikacji w kolejce.

   <pre><code>nw1-cl-0:~ # pgrep er.sapNW1 | xargs kill -9
   </code></pre>

   Jeśli polecenie zostanie uruchomione tylko raz, sapstart uruchomi proces ponownie. Jeśli uruchomisz go wystarczająco często, sapstart nie uruchomi ponownie procesu i zasób będzie w stanie zatrzymania. Uruchom następujące polecenia jako katalog główny, aby oczyścić stan zasobu wystąpienia ERS po teście.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stan zasobu po teście:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Zabij proces sapstartsrv w kolejce

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Uruchom następujące polecenia jako katalog główny w węźle, w którym jest uruchomiony system ASCS.

   <pre><code>nw1-cl-1:~ # pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   nw1-cl-1:~ # kill -9 59545
   </code></pre>

   Proces sapstartsrv powinien być zawsze ponownie uruchomiony przez agenta zasobów rozrusznika. Stan zasobu po teście:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

## <a name="next-steps"></a>Następne kroki

* [Ha dla SAP NW na maszynach wirtualnych platformy Azure na SLES dla aplikacji SAP — przewodnik po wielu identyfikatorach SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)
* [Planowanie i implementacja maszyn wirtualnych platformy Azure dla systemu SAP][planning-guide]
* [Wdrożenie maszyn wirtualnych platformy Azure dla systemu SAP][deployment-guide]
* [Wdrożenie usługi DBMS maszyn wirtualnych platformy Azure dla systemu SAP][dbms-guide]
* Aby dowiedzieć się, jak ustalić wysoką dostępność i plan odzyskiwania po awarii sap HANA na maszynach wirtualnych platformy Azure, zobacz [Wysoka dostępność SAP HANA na maszynach wirtualnych platformy Azure (VM)][sap-hana-ha]

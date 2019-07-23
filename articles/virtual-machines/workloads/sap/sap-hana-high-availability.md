---
title: Wysoka dostępność programu SAP HANA na maszynach wirtualnych platformy Azure w systemie SUSE Linux Enterprise Server | Dokumentacja firmy Microsoft
description: Wysoka dostępność programu SAP HANA na maszynach wirtualnych platformy Azure w systemie SUSE Linux Enterprise Server
services: virtual-machines-linux
documentationcenter: ''
author: MSSedusch
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/15/2019
ms.author: sedusch
ms.openlocfilehash: 78d14add09a89b7ec4d4844a12ffa0434d714b3a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709094"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-suse-linux-enterprise-server"></a>Wysoka dostępność programu SAP HANA na maszynach wirtualnych platformy Azure w systemie SUSE Linux Enterprise Server

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]: https://launchpad.support.sap.com/#/notes/2205917
[1944799]: https://launchpad.support.sap.com/#/notes/1944799
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2388694]:https://launchpad.support.sap.com/#/notes/2388694
[401162]: https://launchpad.support.sap.com/#/notes/401162

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json

Do tworzenia aplikacji w środowisku lokalnym możesz użyć albo replikacji systemu HANA lub korzystać z udostępnionego magazynu do ustalenia wysokiej dostępności dla oprogramowania SAP HANA.
Na maszynach wirtualnych Azure (maszyny wirtualne) replikacji systemu HANA na platformie Azure jest obecnie obsługiwana jest tylko funkcji wysokiej dostępności. SAP HANA replikacji składa się z jednego węzła podstawowego i co najmniej jeden węzeł pomocniczy. Zmiany danych w węźle podstawowym są replikowane do węzła pomocniczego, synchronicznie lub asynchronicznie.

W tym artykule opisano sposób wdrażania i konfigurowania maszyn wirtualnych, zainstalować framework klaster i zainstalować i skonfigurować replikacji systemu SAP HANA.
W przykładowych konfiguracji polecenia instalacji numer wystąpienia **03**oraz identyfikator systemu HANA **HN1** są używane.

Najpierw przeczytaj następujące uwagi SAP i dokumenty:

* Uwaga SAP [1928533], która zawiera:
  * Lista rozmiarów maszyn wirtualnych platformy Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP.
  * Pojemność ważne informacje dotyczące rozmiarów maszyn wirtualnych platformy Azure.
  * Obsługiwane oprogramowanie SAP i kombinacji systemu operacyjnego (OS) i bazy danych.
  * Wymagana wersja jądra SAP dla Windows i Linux w systemie Microsoft Azure.
* Uwaga SAP [2015553] wymieniono wymagania wstępne dotyczące wdrażania oprogramowania SAP obsługiwane przez oprogramowanie SAP na platformie Azure.
* Uwaga SAP [2205917] zawiera zalecane ustawienia systemu operacyjnego SUSE Linux Enterprise Server dla aplikacji SAP.
* Uwaga SAP [1944799] zawiera wskazówki dotyczące programu SAP HANA dla SUSE Linux Enterprise Server dla aplikacji SAP.
* Uwaga SAP [2178632] zawiera szczegółowe informacje na temat wszystkich metryk monitorowania, które są zgłaszane dla rozwiązania SAP na platformie Azure.
* Uwaga SAP [2191498] ma wymaganą wersję agenta hosta SAP dla systemu Linux na platformie Azure.
* Uwaga SAP [2243692] zawiera informacje o licencjonowaniu SAP, w systemie Linux na platformie Azure.
* Uwaga SAP [1984787] zawiera ogólne informacje o systemie SUSE Linux Enterprise Server 12.
* Uwaga SAP [1999351] zawiera dodatkowe informacje dotyczące rozwiązywania problemów rozszerzenia platformy Azure Enhanced Monitoring dla rozwiązania SAP.
* Uwaga SAP [401162] zawiera informacje na temat sposobu uniknięcia "adres już w użyciu" podczas konfigurowania replikacji systemu HANA.
* [WIKI społeczności SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) ma wszystkie wymagane informacje o SAP dla systemu Linux.
* [Oprogramowanie SAP HANA certyfikowane platform IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
* [Azure maszyny wirtualne, planowania i implementacji dla rozwiązania SAP w systemie Linux][planning-guide] przewodnik.
* [Wdrażania maszyn wirtualnych platformy Azure dla rozwiązania SAP w systemie Linux][deployment-guide] (w tym artykule).
* [Wdrażania systemu DBMS na maszynach wirtualnych platformy Azure dla rozwiązania SAP w systemie Linux][dbms-guide] przewodnik.
* [SUSE Linux Enterprise Server dla SAP aplikacji 12 z dodatkiem SP3 najlepsze praktyki prowadnic][sles-for-sap-bp]
  * Konfigurowanie SAP HANA SR zoptymalizowane pod kątem infrastruktury wydajności (z systemem SLES for SAP aplikacji 12 z dodatkiem SP1). Przewodnik zawiera wszystkie informacje wymagane do konfigurowania replikacji systemu SAP HANA do tworzenia aplikacji w środowisku lokalnym. Użyj tego przewodnika jako punkt odniesienia.
  * Konfigurowanie SAP HANA SR koszt zoptymalizowane pod kątem infrastruktury (z systemem SLES for SAP aplikacji 12 z dodatkiem SP1)

## <a name="overview"></a>Omówienie

Aby osiągnąć wysoką dostępność, SAP HANA jest zainstalowany na dwie maszyny wirtualne. Dane są replikowane przy użyciu replikacji systemu HANA.

![Omówienie wysoka dostępność oprogramowania SAP HANA](./media/sap-hana-high-availability/ha-suse-hana.png)

Używa ustawień replikacji systemu SAP HANA dedykowanej wirtualnej nazwy hosta i wirtualne adresy IP. Na platformie Azure modułu równoważenia obciążenia jest wymagany do użycia wirtualnego adresu IP. Na poniższej liście przedstawiono konfigurację modułu równoważenia obciążenia:

* Konfiguracja frontonu: Adres IP 10.0.0.13 hn1-db
* Konfiguracja zaplecza: Podłączone do podstawowych interfejsów sieciowych wszystkich maszyn wirtualnych, które powinny być częścią replikacji systemu HANA
* Port sondy: Port 62503
* Reguły równoważenia obciążenia: 30313 TCP, 30315 TCP, 30317 TCP

## <a name="deploy-for-linux"></a>Wdrażanie w systemie Linux

Agent zasobów dla oprogramowania SAP HANA znajduje się w systemie SUSE Linux Enterprise Server dla aplikacji SAP.
W portalu Azure Marketplace zawiera obraz dla SUSE Linux Enterprise Server 12 aplikacje SAP, który służy do wdrażania nowych maszyn wirtualnych.

### <a name="deploy-with-a-template"></a>Wdrażanie przy użyciu szablonu

Można użyć jednego z szablonów szybkiego startu, które znajdują się w usłudze GitHub w celu wdrożenia wszystkich wymaganych zasobów. Ten szablon służy do wdrażania maszyn wirtualnych, moduł równoważenia obciążenia, zestaw dostępności i tak dalej.
Aby wdrożyć szablon, wykonaj następujące kroki:

1. Otwórz [szablonu bazy danych][template-multisid-db] or the [converged template][template-converged] on the Azure portal. 
    The database template creates the load-balancing rules for a database only. The converged template also creates the load-balancing rules for an ASCS/SCS and ERS (Linux only) instance. If you plan to install an SAP NetWeaver-based system and you want to install the ASCS/SCS instance on the same machines, use the [converged template][template-converged].

1. Wprowadź następujące parametry:
    - **Identyfikator systemu SAP**: Wprowadź identyfikator systemu SAP systemu SAP, w którym chcesz zainstalować. Ten identyfikator jest używany jako prefiks dla zasobów, które są wdrażane.
    - **Stos typu**: (Ten parametr dotyczy tylko wtedy, gdy używasz szablonu konwergentnej.) Wybierz typ stos oprogramowania SAP NetWeaver.
    - **Typ systemu operacyjnego**: Wybierz jeden z dystrybucje systemu Linux. W tym przykładzie wybierz **systemu SLES 12**.
    - **Typ bazy danych**: Wybierz **HANA**.
    - **Rozmiar systemu SAP**: Wprowadź liczbę protokoły SAP, których zamierza zapewnić nowego systemu. Jeśli nie masz pewności ile protokoły SAP wymaga systemu, zapytaj partnerów technologicznych SAP lub integratora systemów.
    - **Dostępność systemu**: Wybierz **HA**.
    - **Nazwa użytkownika administratora i hasło administratora**: Tworzony jest nowy użytkownik, który może służyć do logowania się na tym komputerze.
    - **Nowej lub istniejącej podsieci**: Określa, czy należy utworzyć nową sieć wirtualną i podsieć lub użyć istniejącej podsieci. Jeśli masz już sieć wirtualną, która jest połączona z siecią lokalną, wybierz opcję **istniejące**.
    - **Identyfikator podsieci**: Jeśli chcesz wdrożyć maszynę Wirtualną w istniejącej sieci wirtualnej, w którym masz zdefiniowanej podsieci maszyny Wirtualnej powinien być przypisany do nazwy identyfikator odpowiednią podsieć. Identyfikator zwykle wygląda **/subscriptions/\<identyfikator subskrypcji > /resourceGroups/\<nazwy grupy zasobów > /providers/Microsoft.Network/virtualNetworks/\<nazwa sieci wirtualnej > /subnets/ \<Nazwa podsieci >** .

### <a name="manual-deployment"></a>Ręczne wdrażanie

> [!IMPORTANT]
> Upewnij się, że system operacyjny, należy wybrać w określonych typów maszyn wirtualnych, którego używasz z certyfikatem platformy SAP Hana SAP. Lista oprogramowania SAP HANA certyfikowane typy maszyn wirtualnych i system operacyjny w wersji dla tych można wyszukiwać [platform certyfikowane IaaS programu SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Upewnij się, że kliknij przycisk Szczegóły typu maszyny Wirtualnej, aby uzyskać pełną listę oprogramowania SAP HANA na liście obsługiwanych wersji systemu operacyjnego dla określonego typu maszyny Wirtualnej
>  

1. Utwórz grupę zasobów.
1. Utwórz sieć wirtualną.
1. Tworzenie zestawu dostępności.
   - Ustaw domena aktualizacji max.
1. Utwórz moduł równoważenia obciążenia (wewnętrzny).
   - Wybierz sieć wirtualną, utworzony w kroku 2.
1. Tworzenie maszyny wirtualnej 1.
   - Używanie obrazu SLES4SAP w galerii systemu Azure, która jest obsługiwana w przypadku oprogramowania SAP HANA na wybranego typu maszyny Wirtualnej.
   - Wybierz zestaw dostępności utworzonego w kroku 3.
1. Tworzenie maszyny wirtualnej 2.
   - Używanie obrazu SLES4SAP w galerii systemu Azure, która jest obsługiwana w przypadku oprogramowania SAP HANA na wybranego typu maszyny Wirtualnej.
   - Wybierz zestaw dostępności utworzonego w kroku 3. 
1. Dodawanie dysków danych.
1. Konfigurowanie modułu równoważenia obciążenia. Najpierw Utwórz pulę adresów IP frontonu:

   1. Otwórz moduł równoważenia obciążenia, wybierz opcję **puli adresów IP frontonu**i wybierz **Dodaj**.
   1. Wprowadź nazwę nowej puli adresów IP frontonu (na przykład **frontonu hana**).
   1. Ustaw **przypisania** do **statyczne** i wpisz adres IP (na przykład **10.0.0.13**).
   1. Kliknij przycisk **OK**.
   1. Po utworzeniu nowej puli adresów IP frontonu, należy pamiętać, puli adresów IP.

1. Następnie należy utworzyć pulę zaplecza:

   1. Otwórz moduł równoważenia obciążenia, wybierz opcję **pule zaplecza**i wybierz **Dodaj**.
   1. Wprowadź nazwę nowej puli zaplecza (na przykład **zaplecza platformy hana**).
   1. Wybierz **Dodaj maszynę wirtualną**.
   1. Wybierz zestaw dostępności utworzonego w kroku 3.
   1. Wybierz maszyny wirtualne klastra platformy SAP HANA.
   1. Kliknij przycisk **OK**.

1. Następnie utwórz sondę kondycji:

   1. Otwórz moduł równoważenia obciążenia, wybierz opcję **sond kondycji**i wybierz **Dodaj**.
   1. Wprowadź nazwę nowego sondy kondycji (na przykład **hana hp**).
   1. Wybierz **TCP** jako protokół i port 625**03**. Zachowaj **interwał** wartość 5, a **próg złej kondycji** wartość równa 2.
   1. Kliknij przycisk **OK**.

1. SAP HANA 1.0 tworzenie reguł równoważenia obciążenia:

   1. Otwórz moduł równoważenia obciążenia, wybierz opcję **reguły równoważenia obciążenia**i wybierz **Dodaj**.
   1. Wprowadź nazwę nowej reguły równoważenia obciążenia (na przykład hana-lb-3**03**15).
   1. Wybierz adres IP frontonu, puli zaplecza i sondy kondycji, który został utworzony wcześniej (na przykład **frontonu hana**).
   1. Zachowaj **protokołu** równa **TCP**, a następnie wprowadź port 3**03**15.
   1. Zwiększ **limitu czasu bezczynności** do 30 minut.
   1. Upewnij się, że **włączyć pływającego adresu IP**.
   1. Kliknij przycisk **OK**.
   1. Powtórz te czynności dla portu 3**03**17.

1. Dla wersji 2.0 programu SAP HANA tworzenie reguł równoważenia obciążenia w przypadku systemowej bazy danych:

   1. Otwórz moduł równoważenia obciążenia, wybierz opcję **reguły równoważenia obciążenia**i wybierz **Dodaj**.
   1. Wprowadź nazwę nowej reguły równoważenia obciążenia (na przykład hana-lb-3**03**13).
   1. Wybierz adres IP frontonu, puli zaplecza i sondy kondycji, który został utworzony wcześniej (na przykład **frontonu hana**).
   1. Zachowaj **protokołu** równa **TCP**, a następnie wprowadź port 3**03**13.
   1. Zwiększ **limitu czasu bezczynności** do 30 minut.
   1. Upewnij się, że **włączyć pływającego adresu IP**.
   1. Kliknij przycisk **OK**.
   1. Powtórz te czynności dla portu 3**03**14.

1. Dla wersji 2.0 programu SAP HANA należy najpierw utworzyć reguły równoważenia obciążenia dla bazy danych dzierżawy:

   1. Otwórz moduł równoważenia obciążenia, wybierz opcję **reguły równoważenia obciążenia**i wybierz **Dodaj**.
   1. Wprowadź nazwę nowej reguły równoważenia obciążenia (na przykład hana-lb-3**03**40).
   1. Wybierz adres IP frontonu, puli zaplecza i sondy kondycji utworzonego wcześniej (na przykład **frontonu hana**).
   1. Zachowaj **protokołu** równa **TCP**, a następnie wprowadź port 3**03**40.
   1. Zwiększ **limitu czasu bezczynności** do 30 minut.
   1. Upewnij się, że **włączyć pływającego adresu IP**.
   1. Kliknij przycisk **OK**.
   1. Powtórz te czynności dla portów 3**03**41 i 3**03**42.

Więcej informacji na temat wymagane porty dla oprogramowania SAP HANA na ten temat można znaleźć w rozdziale [połączenia baz danych dzierżaw](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) w [baz danych SAP HANA dzierżawy](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) przewodnika lub [2388694 Uwaga SAP][2388694].

> [!IMPORTANT]
> Nie należy włączać czasowe TCP na maszynach wirtualnych Azure umieszczonych za modułem równoważenia obciążenia platformy Azure. Włączenie protokołu TCP sygnatur czasowych spowoduje, że sondy kondycji nie powiedzie się. Ustaw parametr **net.ipv4.tcp_timestamps** do **0**. Aby uzyskać szczegółowe informacje, zobacz [sondy kondycji modułu równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).
> Zobacz też SAP Uwaga [2382421](https://launchpad.support.sap.com/#/notes/2382421). 

## <a name="create-a-pacemaker-cluster"></a>Tworzenie klastra program Pacemaker

Postępuj zgodnie z instrukcjami w [konfigurowania program Pacemaker w systemie SUSE Linux Enterprise Server na platformie Azure](high-availability-guide-suse-pacemaker.md) do tworzenia klastra program Pacemaker podstawowe dla tego serwera platformy HANA. SAP HANA i SAP NetWeaver (A) SCS, można użyć tego samego klastra program Pacemaker.

## <a name="install-sap-hana"></a>Instalowanie platformy SAP HANA

Kroki opisane w tej sekcji należy użyć następujących prefiksów:
- **[A]** : Ten krok ma zastosowanie do wszystkich węzłów.
- **[1]** : Ten krok dotyczy tylko węzeł 1.
- **[2]** : Ten krok dotyczy tylko klastra program Pacemaker w węźle 2.

1. **[A]**  Konfigurowanie układ dysku: **Menedżer woluminów logicznych (LVM)** .

   Zalecamy użycie LVM dla woluminów, które przechowują dane i pliki dziennika. W poniższym przykładzie założono, że maszyny wirtualne mają cztery dyski danych dołączone, które są używane do tworzenia dwa woluminy.

   Wyświetlić listę wszystkich dostępnych dysków:

   <pre><code>ls /dev/disk/azure/scsi1/lun*
   </code></pre>

   Przykładowe dane wyjściowe:

   <pre><code>
   /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
   </code></pre>

   Utwórz woluminy fizyczne dla wszystkich dysków, które chcesz użyć:

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0
   sudo pvcreate /dev/disk/azure/scsi1/lun1
   sudo pvcreate /dev/disk/azure/scsi1/lun2
   sudo pvcreate /dev/disk/azure/scsi1/lun3
   </code></pre>

   Utwórz grupę woluminu dla plików danych. Stosowanie jednej grupy woluminu dla plików dziennika i jeden do udostępnionego katalogu platformy SAP HANA:

   <pre><code>sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
   sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
   sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
   </code></pre>

   Tworzenie woluminów logicznych. Liniowy wolumin został utworzony, gdy używasz `lvcreate` bez `-i` przełącznika. Sugerujemy utworzenie woluminu rozłożonego lepszą wydajność operacji We/Wy, gdzie `-i` argument powinna być równa liczbie podstawowy wolumin fizyczny. W tym dokumencie dwa woluminy fizyczne są używane dla woluminu danych, więc `-i` przełącznika argument ma wartość **2**. Jeden wolumin fizyczny służy do woluminu dziennika, dzięki czemu nie `-i` jawnego użycia przełącznika. Użyj `-i` przełącznika i ustaw ją na liczbę podstawowy wolumin fizyczny, gdy używasz więcej niż jednego woluminu fizycznej dla poszczególnych danych dziennika lub udostępnione woluminy.

   <pre><code>sudo lvcreate <b>-i 2</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
   sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
   sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   </code></pre>

   Utwórz katalog instalacji, a następnie skopiuj identyfikator UUID wszystkich woluminów logicznych:

   <pre><code>sudo mkdir -p /hana/data/<b>HN1</b>
   sudo mkdir -p /hana/log/<b>HN1</b>
   sudo mkdir -p /hana/shared/<b>HN1</b>
   # Write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log, and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   sudo blkid
   </code></pre>

   Utwórz `fstab` wpisy dla trzech woluminów logicznych:       

   <pre><code>sudo vi /etc/fstab
   </code></pre>

   Wstaw następujący wiersz w `/etc/fstab` pliku:      

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
   </code></pre>

   Instalowanie nowych woluminów:

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]**  Konfigurowanie układ dysku: **Zwykłe dyski**.

   Pokaz systemów można umieścić HANA plików danych i dziennika na jednym dysku. Tworzenie partycji na /dev/disk/azure/scsi1/lun0 i sformatuj go przy użyciu xfs:

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
   
   # Write down the ID of /dev/disk/azure/scsi1/lun0-part1
   sudo /sbin/blkid
   sudo vi /etc/fstab
   </code></pre>

   Wstaw ten wiersz w pliku/etc/fstab:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
   </code></pre>

   Utwórz katalog docelowy, a następnie zainstalować dysk:

   <pre><code>sudo mkdir /hana
   sudo mount -a
   </code></pre>

1. **[A]**  Konfigurowanie rozpoznawania nazw hostów dla wszystkich hostów.

   Można użyć serwera DNS lub zmodyfikuj plik/etc/hosts na wszystkich węzłach. W tym przykładzie pokazano, jak używać pliku/etc/hosts.
   Zastąp adres IP i nazwy hosta w następujących poleceń:

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Wstaw następujące wiersze w pliku/etc/hosts. Zmienianie adresu IP i nazwy hosta do danego środowiska:

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]**  Zainstalować pakiety wysoka dostępność oprogramowania SAP HANA:

   <pre><code>sudo zypper install SAPHanaSR
   </code></pre>

Aby zainstalować replikacji systemu SAP HANA, wykonaj rozdziału 4 [przewodnik scenariusz SAP HANA SR wydajności zoptymalizowane pod kątem](https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/).

1. **[A]**  Uruchom **hdblcm** program z dysku DVD platformy HANA. Wprowadź następujące wartości w wierszu:
   * Wybierz opcję instalacji: Wprowadź **1**.
   * Wybierz dodatkowe składniki do instalacji: Wprowadź **1**.
   * Wprowadź ścieżkę instalacji [/ hana/udostępnione]: Wybierz opcję Wprowadź.
   * Wprowadź nazwę hosta lokalnego [.]: Wybierz opcję Wprowadź.
   * Czy chcesz dodać kolejne hosty systemu? (t/n) [n]: Wybierz opcję Wprowadź.
   * Wprowadź identyfikator systemu HANA SAP: Wprowadź identyfikator SID HANA, na przykład: **HN1**.
   * Wprowadź numer wystąpienia [00]: Wprowadź numer wystąpienie oprogramowania HANA. Wprowadź **03** możesz użyć szablonu platformy Azure lub po wdrożeniu ręcznym części tego artykułu.
   * Wybierz tryb bazy danych / wprowadź indeks [1]: Wybierz opcję Wprowadź.
   * Użycie systemu wybierz / Wprowadź indeks [4]: Wybierz wartość obciążenie systemu.
   * Wprowadź lokalizację woluminów danych [/ hana/data/HN1]: Wybierz opcję Wprowadź.
   * Wprowadź lokalizację woluminy dziennika [/ hana/log/HN1]: Wybierz opcję Wprowadź.
   * Ogranicza maksymalny przydział pamięci? [n]: Wybierz opcję Wprowadź.
   * Wprowadź nazwę hosta certyfikat dla hosta "..." [...]: Wybierz opcję Wprowadź.
   * Wprowadź użytkownika agenta hosta SAP (sapadm) hasło: Wprowadź hasło użytkownika agenta hosta.
   * Upewnij się, SAP hosta agenta użytkownika (sapadm) hasło: Wprowadź hasło użytkownika agenta hosta ponownie, aby potwierdzić.
   * Wprowadź administratora systemu (hdbadm) hasło: Wprowadź hasło administratora systemu.
   * Upewnij się, Administrator systemu (hdbadm) hasło: Wprowadź hasło administratora systemu, ponownie, aby potwierdzić.
   * Wprowadź katalog macierzysty Administrator systemu [/ usr/sap/HN1/głównej]: Wybierz opcję Wprowadź.
   * Wprowadź powłoki logowania administratora systemu [/ bin/sh]: Wybierz opcję Wprowadź.
   * Wprowadź identyfikator użytkownika administratora systemu [1001]: Wybierz opcję Wprowadź.
   * Wprowadź identyfikator dla grupy użytkowników (sapsys) [79]: Wybierz opcję Wprowadź.
   * Wprowadź hasło użytkownika (SYSTEM) bazy danych: Wprowadź hasło użytkownika bazy danych.
   * Potwierdź hasło użytkownika (SYSTEM) bazy danych: Wprowadź hasło użytkownika bazy danych ponownie, aby potwierdzić.
   * Uruchom ponownie system po ponownym uruchomieniu komputera? [n]: Wybierz opcję Wprowadź.
   * Czy chcesz kontynuować? (y/n): Sprawdź poprawność podsumowania. Wprowadź **y** aby kontynuować.

1. **[A]**  Uaktualnienia tego agenta hosta SAP.

   Pobierz najnowsze archiwum agenta hosta SAP z [Centrum oprogramowania SAP][sap-swcenter] i uruchom następujące polecenie, aby uaktualnić agenta. Zastąp ścieżkę do archiwum, aby wskazywał pobranego pliku:

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>Konfigurowanie replikacji 2.0 systemu SAP HANA

Kroki opisane w tej sekcji należy użyć następujących prefiksów:

* **[A]** : Ten krok ma zastosowanie do wszystkich węzłów.
* **[1]** : Ten krok dotyczy tylko węzeł 1.
* **[2]** : Ten krok dotyczy tylko klastra program Pacemaker w węźle 2.

1. **[1]**  Tworzenie bazy danych dzierżaw.

   Jeśli używasz programu SAP HANA w wersji 2.0 lub MDC utworzyć bazę danych dzierżawy w systemie SAP NetWeaver. Zastąp **NW1** z identyfikatorem SID systemu SAP.

   Wykonaj następujące polecenie jako < hanasid\>adm:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]**  Konfigurowanie replikacji systemu na pierwszym węźle:

   Tworzenie kopii zapasowych baz danych jako < hanasid\>adm:

   <pre><code>hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Skopiuj pliki infrastruktury kluczy publicznych systemu do lokacji dodatkowej:

   <pre><code>scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT   <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY  <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   Utwórz lokację główną:

   <pre><code>hdbnsutil -sr_enable --name=<b>SITE1</b>
   </code></pre>

1. **[2]**  Konfigurowanie replikacji systemu na drugim węźle:
    
   Zarejestruj drugi węzeł, który rozpoczyna się replikacja systemu. Uruchom następujące polecenie jako < hanasid\>adm:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Konfigurowanie replikacji 1.0 systemu SAP HANA

Kroki opisane w tej sekcji należy użyć następujących prefiksów:

* **[A]** : Ten krok ma zastosowanie do wszystkich węzłów.
* **[1]** : Ten krok dotyczy tylko węzeł 1.
* **[2]** : Ten krok dotyczy tylko klastra program Pacemaker w węźle 2.

1. **[1]**  Tworzenie wymaganych użytkowników.

   Uruchom następujące polecenie, jako katalog główny. Upewnij się zastąpić ciągi pogrubienia (identyfikator systemu HANA **HN1** i numer wystąpienia **03**) z wartościami instalację oprogramowania SAP HANA:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]**  Tworzenie wpisu magazynu kluczy.

   Jako użytkownik główny, aby utworzyć nowy wpis magazynu kluczy, uruchom następujące polecenie:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1]**  Utwórz kopię zapasową bazy danych.

   Tworzenie kopii zapasowej bazy danych jako użytkownik główny:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Jeśli używasz instalacji wielu dzierżawców, również należy wykonać kopię zapasową bazy danych dzierżaw:

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]**  Konfigurowanie replikacji systemu na pierwszym węźle.

   Utwórz lokację główną jako < hanasid\>adm:

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]**  Konfigurowanie replikacji systemu, w węźle pomocniczym.

   Zarejestruj lokacji dodatkowej jako < hanasid\>adm:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="create-sap-hana-cluster-resources"></a>Tworzenie zasobów klastra platformy SAP HANA

Najpierw utwórz topologii platformy HANA. Uruchom następujące polecenia w jednym z węzłów klastra program Pacemaker:

<pre><code>sudo crm configure property maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID

sudo crm configure primitive rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
  operations \$id="rsc_sap2_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10" timeout="600" \
  op start interval="0" timeout="600" \
  op stop interval="0" timeout="300" \
  params SID="<b>HN1</b>" InstanceNumber="<b>03</b>"

sudo crm configure clone cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  meta is-managed="true" clone-node-max="1" target-role="Started" interleave="true"
</code></pre>

Następnie należy utworzyć zasoby platformy HANA:

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer. 

sudo crm configure primitive rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHana \
  operations \$id="rsc_sap_<b>HN1</b>_HDB<b>03</b>-operations" \
  op start interval="0" timeout="3600" \
  op stop interval="0" timeout="3600" \
  op promote interval="0" timeout="3600" \
  op monitor interval="60" role="Master" timeout="700" \
  op monitor interval="61" role="Slave" timeout="700" \
  params SID="<b>HN1</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
  DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

sudo crm configure ms msl_SAPHana_<b>HN1</b>_HDB<b>03</b> rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> \
  meta is-managed="true" notify="true" clone-max="2" clone-node-max="1" \
  target-role="Started" interleave="true"

sudo crm configure primitive rsc_ip_<b>HN1</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \
  meta target-role="Started" is-managed="true" \
  operations \$id="rsc_ip_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10s" timeout="20s" \
  params ip="<b>10.0.0.13</b>"

sudo crm configure primitive rsc_nc_<b>HN1</b>_HDB<b>03</b> anything \
  params binfile="/usr/bin/nc" cmdline_options="-l -k 625<b>03</b>" \
  op monitor timeout=20s interval=10 depth=0

sudo crm configure group g_ip_<b>HN1</b>_HDB<b>03</b> rsc_ip_<b>HN1</b>_HDB<b>03</b> rsc_nc_<b>HN1</b>_HDB<b>03</b>

sudo crm configure colocation col_saphana_ip_<b>HN1</b>_HDB<b>03</b> 4000: g_ip_<b>HN1</b>_HDB<b>03</b>:Started \
  msl_SAPHana_<b>HN1</b>_HDB<b>03</b>:Master  

sudo crm configure order ord_SAPHana_<b>HN1</b>_HDB<b>03</b> Optional: cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  msl_SAPHana_<b>HN1</b>_HDB<b>03</b>

# Clean up the HANA resources. The HANA resources might have failed because of a known issue.
sudo crm resource cleanup rsc_SAPHana_<b>HN1</b>_HDB<b>03</b>

sudo crm configure property maintenance-mode=false
sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

Upewnij się, że kondycja klastra jest ok i wszystkie zasoby są uruchamiane. Nie jest to ważne w węźle, które zasoby są uruchomione.

<pre><code>sudo crm_mon -r

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# stonith-sbd     (stonith:external/sbd): Started hn1-db-0
# rsc_st_azure    (stonith:fence_azure_arm):      Started hn1-db-1
# Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
#     Started: [ hn1-db-0 hn1-db-1 ]
# Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
#     Masters: [ hn1-db-0 ]
#     Slaves: [ hn1-db-1 ]
# Resource Group: g_ip_HN1_HDB03
#     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
#     rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
</code></pre>

## <a name="test-the-cluster-setup"></a>Testuj ustawienia klastra

W tej sekcji opisano, jak można przetestować konfigurację. Każde badanie przyjęto założenie, że są głównymi i wzorzec platformy SAP HANA jest uruchomiona na **hn1-db-0** maszyny wirtualnej.

### <a name="test-the-migration"></a>Migracja testowa

Przed rozpoczęciem testu, upewnij się, że program Pacemaker nie ma żadnych akcji nie powiodło się (za pośrednictwem crm_mon - r), istnieją bez ograniczeń nieoczekiwany lokalizacji (na przykład resztek testu migracji), i czy HANA jest stan synchronizacji, na przykład za pomocą SAPHanaSR showAttr:

<pre><code>hn1-db-0:~ # SAPHanaSR-showAttr

Global cib-time
--------------------------------
global Mon Aug 13 11:26:04 2018

Hosts    clone_state lpa_hn1_lpt node_state op_mode   remoteHost    roles                            score site  srmode sync_state version                vhost
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
hn1-db-0 PROMOTED    1534159564  online     logreplay nws-hana-vm-1 4:P:master1:master:worker:master 150   SITE1 sync   PRIM       2.00.030.00.1522209842 nws-hana-vm-0
hn1-db-1 DEMOTED     30          online     logreplay nws-hana-vm-0 4:S:master1:master:worker:master 100   SITE2 sync   SOK        2.00.030.00.1522209842 nws-hana-vm-1
</code></pre>

Można migrować węzła głównego platformy SAP HANA, wykonując następujące polecenie:

<pre><code>crm resource migrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
</code></pre>

Jeśli ustawisz `AUTOMATED_REGISTER="false"`, ta sekwencja poleceń należy zmigrować węzła głównego platformy SAP HANA i grupę, która zawiera wirtualny adres IP, który hn1-db-1.

Po zakończeniu migracji danych wyjściowych - r crm_mon wygląda następująco

<pre><code>Online: [ hn1-db-0 hn1-db-1 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started hn1-db-1
 Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hn1-db-0 hn1-db-1 ]
 Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     Masters: [ hn1-db-1 ]
     Stopped: [ hn1-db-0 ]
 Resource Group: g_ip_HN1_HDB03
     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
     rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1

Failed Actions:
* rsc_SAPHana_HN1_HDB03_start_0 on hn1-db-0 'not running' (7): call=84, status=complete, exitreason='none',
    last-rc-change='Mon Aug 13 11:31:37 2018', queued=0ms, exec=2095ms
</code></pre>

Zasób platformy SAP HANA na hn1-db-0 nie można uruchomić jako pomocniczą. W takim przypadku należy skonfigurować wystąpienie oprogramowania HANA jako pomocniczy, przez wykonanie tego polecenia:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>
</code></pre>

Proces migracji tworzy ograniczenia lokalizacji, które muszą zostać usunięte ponownie:

<pre><code># Switch back to root and clean up the failed state
exit
hn1-db-0:~ # crm resource unmigrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
</code></pre>

Należy również wyczyścić stanu zasobu węzła pomocniczego:

<pre><code>hn1-db-0:~ # crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

Monitoruj stan zasobu HANA przy użyciu crm_mon - r. Gdy HANA jest uruchomiona na hn1-db-0, dane wyjściowe powinny wyglądać następująco

<pre><code>Online: [ hn1-db-0 hn1-db-1 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started hn1-db-1
 Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hn1-db-0 hn1-db-1 ]
 Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
 Resource Group: g_ip_HN1_HDB03
     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
     rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent-not-sbd"></a>Test agent Azure preferowane (nie interwencja)

Możesz przetestować instalację agenta programu Azure preferowane, wyłączając interfejsu sieciowego w węźle hn1-db-0:

<pre><code>sudo ifdown eth0
</code></pre>

Maszyna wirtualna powinna teraz ponownie uruchom lub Zatrzymaj w zależności od konfiguracji klastra.
Jeśli ustawisz `stonith-action` ustawienie wartości off, maszyna wirtualna zostaje zatrzymana i zasoby są migrowane do uruchomionej maszyny wirtualnej.

Po ponownym uruchomieniu maszyny wirtualnej zasobów platformy SAP HANA nie została uruchomiona jako pomocniczą, jeśli ustawisz `AUTOMATED_REGISTER="false"`. W takim przypadku należy skonfigurować wystąpienie oprogramowania HANA jako pomocniczy, przez wykonanie tego polecenia:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="test-sbd-fencing"></a>Preferowane interwencja testu

Aby przetestować instalację interwencja, zabijanie procesu inquisitor.

<pre><code>hn1-db-0:~ # ps aux | grep sbd
root       1912  0.0  0.0  85420 11740 ?        SL   12:25   0:00 sbd: inquisitor
root       1929  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-360014056f268462316e4681b704a9f73 - slot: 0 - uuid: 7b862dba-e7f7-4800-92ed-f76a4e3978c8
root       1930  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-360014059bc9ea4e4bac4b18808299aaf - slot: 0 - uuid: 5813ee04-b75c-482e-805e-3b1e22ba16cd
root       1931  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-36001405b8dddd44eb3647908def6621c - slot: 0 - uuid: 986ed8f8-947d-4396-8aec-b933b75e904c
root       1932  0.0  0.0  90524 16656 ?        SL   12:25   0:00 sbd: watcher: Pacemaker
root       1933  0.0  0.0 102708 28260 ?        SL   12:25   0:00 sbd: watcher: Cluster
root      13877  0.0  0.0   9292  1572 pts/0    S+   12:27   0:00 grep sbd

hn1-db-0:~ # kill -9 1912
</code></pre>

Powinien zostać przeprowadzony ponowny rozruch węzła klastra hn1-db-0. Program Pacemaker usługa może nie Rozpocznij później. Upewnij się uruchomić go ponownie.

### <a name="test-a-manual-failover"></a>Testowanie ręcznej pracy awaryjnej

Możesz przetestować ręcznej pracy awaryjnej, zatrzymując `pacemaker` usługi w węźle hn1-db-0:

<pre><code>service pacemaker stop
</code></pre>

Po włączeniu trybu failover należy ponownie uruchomić usługę. Jeśli ustawisz `AUTOMATED_REGISTER="false"`, zasobów platformy SAP HANA w węźle hn1-db-0 nie można uruchomić jako pomocniczą. W takim przypadku należy skonfigurować wystąpienie oprogramowania HANA jako pomocniczy, przez wykonanie tego polecenia:

<pre><code>service pacemaker start
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="suse-tests"></a>Testy SUSE

> [!IMPORTANT]
> Upewnij się, że system operacyjny, należy wybrać w określonych typów maszyn wirtualnych, którego używasz z certyfikatem platformy SAP Hana SAP. Lista oprogramowania SAP HANA certyfikowane typy maszyn wirtualnych i system operacyjny w wersji dla tych można wyszukiwać [platform certyfikowane IaaS programu SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Upewnij się, że kliknij przycisk Szczegóły typu maszyny Wirtualnej, aby uzyskać pełną listę oprogramowania SAP HANA na liście obsługiwanych wersji systemu operacyjnego dla określonego typu maszyny Wirtualnej

Uruchom wszystkie przypadki testowe, które są wymienione w przewodniku zoptymalizowane pod kątem scenariuszy SAP HANA SR wydajności lub SAP HANA SR koszt zoptymalizowane pod kątem scenariusza, w zależności od danego przypadku użycia. Przewodniki programu można znaleźć na [SLES for SAP najlepszych praktyk strony][sles-for-sap-bp].

Następujące testy są kopię opisy testu SAP HANA SR wydajności zoptymalizowane pod kątem scenariusza SUSE Linux Enterprise Server przewodnik SAP aplikacji 12 z dodatkiem SP1. Dla zaktualizowanej wersji zawsze także zapoznaj się z przewodnikiem, sam. Zawsze upewnij się, HANA jest synchronizowany przed rozpoczęciem badania, a także upewnij się, że konfiguracja program Pacemaker jest prawidłowa.

W następujących opisach testu przyjęto założenie, że PREFER_SITE_TAKEOVER = "true" i AUTOMATED_REGISTER = "false".
UWAGA: Następujące testy są przeznaczone do można uruchomić w sekwencji i zależą od stanu zakończenia poprzednich testów.

1. TEST 1: ZATRZYMAJ PODSTAWOWEJ BAZY DANYCH W WĘŹLE 1

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Uruchom następujące polecenia jako < hanasid\>adm w węźle hn1-db-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Program pacemaker powinny wykrywać, zatrzymane wystąpienie oprogramowania HANA i tryb failover do innego węzła. Po zakończeniu pracy w trybie failover, wystąpienie oprogramowania HANA w węźle hn1-db-0 jest zatrzymana, ponieważ program Pacemaker nie rejestruje automatycznie węzeł jako pomocniczy platformy HANA.

   Uruchom następujące polecenia, aby zarejestrować węzeł hn1-db-0 jako pomocniczego i czyszczenie zasobów nie powiodło się.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Stan zasobu po przeprowadzeniu testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

1. TEST 2: ZATRZYMAJ PODSTAWOWEJ BAZY DANYCH W WĘŹLE 2

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

   Uruchom następujące polecenia jako < hanasid\>adm w węźle hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Program pacemaker powinny wykrywać, zatrzymane wystąpienie oprogramowania HANA i tryb failover do innego węzła. Po zakończeniu pracy w trybie failover, wystąpienie oprogramowania HANA w węźle hn1-db-1 jest zatrzymana, ponieważ program Pacemaker nie rejestruje automatycznie węzeł jako pomocniczy platformy HANA.

   Uruchom następujące polecenia, aby zarejestrować węzeł hn1-db-1 jako pomocniczego i czyszczenie zasobów nie powiodło się.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Stan zasobu po przeprowadzeniu testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. TEST 3: AWARII PODSTAWOWEJ BAZY DANYCH W WĘŹLE

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Uruchom następujące polecenia jako < hanasid\>adm w węźle hn1-db-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>
   
   Program pacemaker powinna wykryć zabitych wystąpienie oprogramowania HANA i tryb failover do innego węzła. Po zakończeniu pracy w trybie failover, wystąpienie oprogramowania HANA w węźle hn1-db-0 jest zatrzymana, ponieważ program Pacemaker nie rejestruje automatycznie węzeł jako pomocniczy platformy HANA.

   Uruchom następujące polecenia, aby zarejestrować węzeł hn1-db-0 jako pomocniczego i czyszczenie zasobów nie powiodło się.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Stan zasobu po przeprowadzeniu testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

1. TEST 4: AWARII PODSTAWOWEJ BAZY DANYCH W WĘŹLE 2

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

   Uruchom następujące polecenia jako < hanasid\>adm w węźle hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Program pacemaker powinna wykryć zabitych wystąpienie oprogramowania HANA i tryb failover do innego węzła. Po zakończeniu pracy w trybie failover, wystąpienie oprogramowania HANA w węźle hn1-db-1 jest zatrzymana, ponieważ program Pacemaker nie rejestruje automatycznie węzeł jako pomocniczy platformy HANA.

   Uruchom następujące polecenia, aby zarejestrować węzeł hn1-db-1 jako pomocniczego i czyszczenie zasobów nie powiodło się.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Stan zasobu po przeprowadzeniu testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. TEST 5: LOKACJA GŁÓWNA AWARII WĘZŁA (WĘZEŁ 1)

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Uruchom następujące polecenia jako głównego w węźle hn1-db-0:

   <pre><code>hn1-db-0:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Program pacemaker powinny wykrywać węzła klastra zabitych i ogrodzenia węzła. Gdy węzeł zostanie ogrodzonych, program Pacemaker wyzwoli przejęcia wystąpienia platformy HANA. Po ponownym uruchomieniu węzła ogrodzonych program Pacemaker nie rozpocznie się automatycznie.

   Uruchom następujące polecenia, aby uruchomić program Pacemaker, czyszczenie komunikatów interwencja dla węzła hn1-db-0 zarejestrować węzeł hn1-db-0 jako pomocnicze i czyszczenie zasobów nie powiodło się.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-0:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-0:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-0 clear
   
   hn1-db-0:~ # systemctl start pacemaker
   
   # run as &lt;hanasid&gt;adm
   hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Stan zasobu po przeprowadzeniu testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

1. TESTOWANIE 6: LOKACJA DODATKOWA AWARII WĘZŁA (WĘZEŁ 2)

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

   Uruchom następujące polecenia jako głównego w węźle hn1-db-1:

   <pre><code>hn1-db-1:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Program pacemaker powinny wykrywać węzła klastra zabitych i ogrodzenia węzła. Gdy węzeł zostanie ogrodzonych, program Pacemaker wyzwoli przejęcia wystąpienia platformy HANA. Po ponownym uruchomieniu węzła ogrodzonych program Pacemaker nie rozpocznie się automatycznie.

   Uruchom następujące polecenia, aby uruchomić program Pacemaker, czyszczenie komunikaty interwencja węzła hn1-db-1, zarejestruj węzeł hn1-db-1 jako pomocniczego i czyszczenie zasobów nie powiodło się.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker
   
   # run as &lt;hanasid&gt;adm
   hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Stan zasobu po przeprowadzeniu testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. TESTOWANIE 7: ZATRZYMAJ POMOCNICZEJ BAZY DANYCH W WĘŹLE 2

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Uruchom następujące polecenia jako < hanasid\>adm w węźle hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Program pacemaker wykryje zatrzymane wystąpienie oprogramowania HANA i oznacz zasób jako węzeł hn1-db-1 nie powiodło się. Program pacemaker automatycznie ponownie uruchomić wystąpienie oprogramowania HANA. Uruchom następujące polecenie, aby wyczyścić stanu nie powiodło się.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Stan zasobu po przeprowadzeniu testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. TESTOWANIE 8: AWARII POMOCNICZEJ BAZY DANYCH W WĘŹLE 2

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Uruchom następujące polecenia jako < hanasid\>adm w węźle hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Program pacemaker wykryje zabitych wystąpienie oprogramowania HANA i oznacz zasób jako węzeł hn1-db-1 nie powiodło się. Uruchom następujące polecenie, aby wyczyścić stanu nie powiodło się. Program pacemaker należy następnie automatycznie ponownie uruchomić wystąpienie oprogramowania HANA.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Stan zasobu po przeprowadzeniu testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. TESTOWANIE 9: AWARII BAZY DANYCH HANA DODATKOWEJ URUCHOMIONY WĘZEŁ (WĘZEŁ 2) LOKACJI DODATKOWEJ

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Uruchom następujące polecenia jako głównego w węźle hn1-db-1:

   <pre><code>hn1-db-1:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Program pacemaker powinny wykrywać węzła klastra zabitych i ogrodzenia węzła. Po ponownym uruchomieniu węzła ogrodzonych program Pacemaker nie rozpocznie się automatycznie.

   Uruchom następujące polecenia, aby uruchomić program Pacemaker, czyszczenie komunikatów interwencja dla węzła hn1-db-1 i czyszczenie zasobów nie powiodło się.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker  
   
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Stan zasobu po przeprowadzeniu testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

## <a name="next-steps"></a>Kolejne kroki

* [Azure maszyny wirtualne, planowania i implementacji dla rozwiązania SAP][planning-guide]
* [Wdrażania maszyn wirtualnych platformy Azure dla rozwiązania SAP][deployment-guide]
* [Wdrażania systemu DBMS na maszynach wirtualnych platformy Azure dla rozwiązania SAP][dbms-guide]
* Aby dowiedzieć się, jak zadbać o wysokiej dostępności i plan odzyskiwania po awarii oprogramowania SAP Hana na platformie Azure (duże wystąpienia), zobacz [platformy SAP HANA (duże wystąpienia) o wysokiej dostępności i odzyskiwania po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md)

---
title: Konfigurowanie replikacji systemu SAP HANA na maszynach wirtualnych platformy Azure (VMs) | Dokumentacja firmy Microsoft
description: Ustanów wysoką dostępność środowiska SAP HANA na maszynach wirtualnych Azure (maszyny wirtualne).
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
ms.openlocfilehash: 66e1e4603602835d6ed5be9af58eb09a24b00b63
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709115"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-red-hat-enterprise-linux"></a>Wysoka dostępność programu SAP HANA na maszynach wirtualnych platformy Azure w systemie Red Hat Enterprise Linux

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2388694]:https://launchpad.support.sap.com/#/notes/2388694
[2292690]: https://launchpad.support.sap.com/#/notes/2292690
[2455582]: https://launchpad.support.sap.com/#/notes/2455582
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2009879]: https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json

Do tworzenia aplikacji w środowisku lokalnym możesz użyć albo replikacji systemu HANA lub korzystać z udostępnionego magazynu do ustalenia wysokiej dostępności dla oprogramowania SAP HANA.
Na maszynach wirtualnych Azure (maszyny wirtualne) replikacji systemu HANA na platformie Azure jest obecnie obsługiwana jest tylko funkcji wysokiej dostępności.
SAP HANA replikacji składa się z jednego węzła podstawowego i co najmniej jeden węzeł pomocniczy. Zmiany danych w węźle podstawowym są replikowane do węzła pomocniczego, synchronicznie lub asynchronicznie.

W tym artykule opisano sposób wdrażania i konfigurowania maszyn wirtualnych, zainstalować framework klaster i zainstalować i skonfigurować replikacji systemu SAP HANA.
W przykładowych konfiguracji polecenia instalacji numer wystąpienia **03**oraz identyfikator systemu HANA **HN1** są używane.

Najpierw przeczytaj następujące uwagi SAP i dokumenty:

* Uwaga SAP [1928533], która zawiera:
  * Lista rozmiarów maszyn wirtualnych platformy Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP.
  * Pojemność ważne informacje dotyczące rozmiarów maszyn wirtualnych platformy Azure.
  * Obsługiwane oprogramowanie SAP i kombinacji systemu operacyjnego (OS) i bazy danych.
  * Wymagana wersja jądra SAP dla Windows i Linux w systemie Microsoft Azure.
* Uwaga SAP [2015553] wymieniono wymagania wstępne dotyczące wdrażania oprogramowania SAP obsługiwane przez oprogramowanie SAP na platformie Azure.
* Uwaga SAP [2002167] zawiera zalecane ustawienia systemu operacyjnego Red Hat Enterprise Linux
* SAP Note [2009879] has SAP HANA Guidelines for Red Hat Enterprise Linux
* Uwaga SAP [2178632] zawiera szczegółowe informacje o metrykach wszystkie funkcje monitorowania zgłoszone dla rozwiązania SAP na platformie Azure.
* Uwaga SAP [2191498] ma wymaganą wersję agenta hosta SAP dla systemu Linux na platformie Azure.
* Uwaga SAP [2243692] zawiera informacje o licencjonowaniu SAP, w systemie Linux na platformie Azure.
* Uwaga SAP [1999351] zawiera dodatkowe informacje dotyczące rozwiązywania problemów rozszerzenia platformy Azure Enhanced Monitoring dla rozwiązania SAP.
* [WIKI społeczności SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) ma wszystkie wymagane informacje o SAP dla systemu Linux.
* [Azure maszyny wirtualne, planowania i implementacji dla rozwiązania SAP w systemie Linux][planning-guide]
* [Wdrażania maszyn wirtualnych platformy Azure dla rozwiązania SAP w systemie Linux (w tym artykule)][deployment-guide]
* [Wdrażania systemu DBMS na maszynach wirtualnych platformy Azure dla rozwiązania SAP w systemie Linux][dbms-guide]
* [Replikacja systemu SAP HANA w klastrze program pacemaker](https://access.redhat.com/articles/3004101)
* Dokumentacja systemu RHEL ogólne
  * [Omówienie dodatek wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administracja dodatek wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Dokumentacja dodatek wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Usługa Azure RHEL dokumentacji:
  * [Zasady pomocy technicznej w przypadku klastrów o wysokiej dostępności systemu RHEL — maszyny wirtualne platformy Microsoft Azure jako elementów członkowskich klastra](https://access.redhat.com/articles/3131341)
  * [Instalowanie i konfigurowanie Red Hat Enterprise Linux 7.4 (lub nowszy) o wysokiej dostępności klastra w systemie Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [Instalowanie platformy SAP HANA w systemie Red Hat Enterprise Linux do użycia w systemie Microsoft Azure](https://access.redhat.com/solutions/3193782)

## <a name="overview"></a>Przegląd

Aby osiągnąć wysoką dostępność, SAP HANA jest zainstalowany na dwie maszyny wirtualne. Dane są replikowane przy użyciu replikacji systemu HANA.

![Omówienie wysoka dostępność oprogramowania SAP HANA](./media/sap-hana-high-availability-rhel/ha-hana.png)

Używa ustawień replikacji systemu SAP HANA dedykowanej wirtualnej nazwy hosta i wirtualne adresy IP. Na platformie Azure modułu równoważenia obciążenia jest wymagany do użycia wirtualnego adresu IP. Na poniższej liście przedstawiono konfigurację modułu równoważenia obciążenia:

* Konfiguracja frontonu: Adres IP 10.0.0.13 hn1-db
* Konfiguracja zaplecza: Podłączone do podstawowych interfejsów sieciowych wszystkich maszyn wirtualnych, które powinny być częścią replikacji systemu HANA
* Port sondy: Port 62503
* Reguły równoważenia obciążenia: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP

## <a name="deploy-for-linux"></a>Wdrażanie w systemie Linux

W portalu Azure Marketplace zawiera obraz systemu Red Hat Enterprise Linux 7.4 platformy SAP Hana, który służy do wdrażania nowych maszyn wirtualnych.

### <a name="deploy-with-a-template"></a>Wdrażanie przy użyciu szablonu

Można użyć jednego z szablonów szybkiego startu, które znajdują się w usłudze GitHub w celu wdrożenia wszystkich wymaganych zasobów. Ten szablon służy do wdrażania maszyn wirtualnych, moduł równoważenia obciążenia, zestaw dostępności i tak dalej.
Aby wdrożyć szablon, wykonaj następujące kroki:

1. Otwórz [szablonu bazy danych][template-multisid-db] w witrynie Azure portal.
1. Wprowadź następujące parametry:
    * **Identyfikator systemu SAP**: Wprowadź identyfikator systemu SAP systemu SAP, w którym chcesz zainstalować. Ten identyfikator jest używany jako prefiks dla zasobów, które są wdrażane.
    * **Typ systemu operacyjnego**: Wybierz jeden z dystrybucje systemu Linux. W tym przykładzie wybierz **RHEL 7**.
    * **Typ bazy danych**: Wybierz **HANA**.
    * **Rozmiar systemu SAP**: Wprowadź liczbę protokoły SAP, których zamierza zapewnić nowego systemu. Jeśli nie masz pewności ile protokoły SAP wymaga systemu, zapytaj partnerów technologicznych SAP lub integratora systemów.
    * **Dostępność systemu**: Wybierz **HA**.
    * **Nazwa użytkownika administratora, hasło administratora lub SSH key**: Tworzony jest nowy użytkownik, który może służyć do logowania się na tym komputerze.
    * **Identyfikator podsieci**: Jeśli chcesz wdrożyć maszynę Wirtualną w istniejącej sieci wirtualnej, w którym masz zdefiniowanej podsieci maszyny Wirtualnej powinien być przypisany do nazwy identyfikator odpowiednią podsieć. Identyfikator zwykle wygląda **/subscriptions/\<identyfikator subskrypcji > /resourceGroups/\<nazwy grupy zasobów > /providers/Microsoft.Network/virtualNetworks/\<nazwa sieci wirtualnej > /subnets/ \<Nazwa podsieci >** . Pozostaw puste, jeśli chcesz utworzyć nową sieć wirtualną

### <a name="manual-deployment"></a>Ręczne wdrażanie

1. Utwórz grupę zasobów.
1. Utwórz sieć wirtualną.
1. Tworzenie zestawu dostępności.  
   Ustaw domena aktualizacji max.
1. Utwórz moduł równoważenia obciążenia (wewnętrzny).
   * Wybierz sieć wirtualną, utworzony w kroku 2.
1. Tworzenie maszyny wirtualnej 1.  
   W przypadku oprogramowania SAP HANA, należy użyć co najmniej w systemie Red Hat Enterprise Linux 7.4. W tym przykładzie użyto Red Hat Enterprise Linux 7.4 obrazu platformy SAP HANA <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> wybierz zestaw utworzony w kroku 3 dostępności.
1. Tworzenie maszyny wirtualnej 2.  
   W przypadku oprogramowania SAP HANA, należy użyć co najmniej w systemie Red Hat Enterprise Linux 7.4. W tym przykładzie użyto Red Hat Enterprise Linux 7.4 obrazu platformy SAP HANA <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> wybierz zestaw utworzony w kroku 3 dostępności.
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

## <a name="install-sap-hana"></a>Instalowanie platformy SAP HANA

Kroki opisane w tej sekcji należy użyć następujących prefiksów:

* **[A]** : Ten krok ma zastosowanie do wszystkich węzłów.
* **[1]** : Ten krok dotyczy tylko węzeł 1.
* **[2]** : Ten krok dotyczy tylko klastra program Pacemaker w węźle 2.

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

1. **[A]**  RHEL HANA konfiguracji

   Konfigurowanie systemu RHEL, zgodnie z opisem w Uwaga SAP [2292690] i [2455582] i <https://access.redhat.com/solutions/2447641>.

1. **[A]**  Instalowanie platformy SAP HANA

   Aby zainstalować replikacji systemu SAP HANA, postępuj zgodnie z <https://access.redhat.com/articles/3004101>.

   * Uruchom **hdblcm** program z dysku DVD platformy HANA. Wprowadź następujące wartości w wierszu:
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

1. **[A]**  Konfigurowanie zapory

   Utwórz regułę zapory dla portu sondy modułu równoważenia obciążenia platformy Azure.

   <pre><code>sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp
   sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp --permanent
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>Konfigurowanie replikacji 2.0 systemu SAP HANA

Kroki opisane w tej sekcji należy użyć następujących prefiksów:

* **[A]** : Ten krok ma zastosowanie do wszystkich węzłów.
* **[1]** : Ten krok dotyczy tylko węzeł 1.
* **[2]** : Ten krok dotyczy tylko klastra program Pacemaker w węźle 2.

1. **[A]**  Konfigurowanie zapory

   Tworzenie reguły zapory zezwalające na ruch replikacji systemu HANA i klienta. Wymagane porty są wymienione na [porty TCP/IP wszystkich produktów SAP](https://help.sap.com/viewer/ports). Następujące polecenia są tylko przykładowe zezwalająca na ruch replikacji systemu w wersji 2.0 platformy HANA i klienta w bazie danych SYSTEMDB, HN1 i NW1.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40301/tcp
   sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40307/tcp
   sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40303/tcp
   sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40340/tcp
   sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30340/tcp
   sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30341/tcp
   sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30342/tcp
   </code></pre>

1. **[1]**  Tworzenie bazy danych dzierżaw.

   Jeśli używasz programu SAP HANA w wersji 2.0 lub MDC utworzyć bazę danych dzierżawy w systemie SAP NetWeaver. Zastąp **NW1** z identyfikatorem SID systemu SAP.

   Wykonaj jako < hanasid\>adm następujące polecenie:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]**  Konfigurowanie replikacji systemu na pierwszym węźle:

   Wykonaj kopię zapasową baz danych jako < hanasid\>adm:

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

1. **[1]**  Sprawdź stan replikacji

   Sprawdź stan replikacji i zaczekaj, aż wszystkie bazy danych są zsynchronizowane. Jeśli stan jest nieznany, sprawdź ustawienia zapory.

   <pre><code>sudo su - <b>hn1</b>adm -c "python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/systemReplicationStatus.py"
   # | Database | Host     | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
   # |          |          |       |              |           |         |           | Host      | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
   # | -------- | -------- | ----- | ------------ | --------- | ------- | --------- | --------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
   # | SYSTEMDB | <b>hn1-db-0</b> | 30301 | nameserver   |         1 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30301 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30307 | xsengine     |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30307 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>NW1</b>      | <b>hn1-db-0</b> | 30340 | indexserver  |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30340 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30303 | indexserver  |         3 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30303 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   #
   # status system replication site "2": ACTIVE
   # overall system replication status: ACTIVE
   #
   # Local System Replication State
   # ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   #
   # mode: PRIMARY
   # site id: 1
   # site name: <b>SITE1</b>
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Konfigurowanie replikacji 1.0 systemu SAP HANA

Kroki opisane w tej sekcji należy użyć następujących prefiksów:

* **[A]** : Ten krok ma zastosowanie do wszystkich węzłów.
* **[1]** : Ten krok dotyczy tylko węzeł 1.
* **[2]** : Ten krok dotyczy tylko klastra program Pacemaker w węźle 2.

1. **[A]**  Konfigurowanie zapory

   Tworzenie reguły zapory zezwalające na ruch replikacji systemu HANA i klienta. Wymagane porty są wymienione na [porty TCP/IP wszystkich produktów SAP](https://help.sap.com/viewer/ports). Następujące polecenia są tylko przykładowe umożliwia replikację systemu w wersji 2.0 platformy HANA. Dostosuj go do instalacji programu SAP HANA w wersji 1.0.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   </code></pre>

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

   <pre><code>HDB stop
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   HDB start
   </code></pre>

## <a name="create-a-pacemaker-cluster"></a>Tworzenie klastra program Pacemaker

Postępuj zgodnie z instrukcjami w [konfigurowania program Pacemaker w systemie Red Hat Enterprise Linux na platformie Azure](high-availability-guide-rhel-pacemaker.md) do tworzenia klastra program Pacemaker podstawowe dla tego serwera platformy HANA.

## <a name="create-sap-hana-cluster-resources"></a>Tworzenie zasobów klastra platformy SAP HANA

Zainstaluj agentów zasobów platformy SAP HANA na **wszystkie węzły**. Upewnij się umożliwić repozytorium, który zawiera pakiet.

<pre><code># Enable repository that contains SAP HANA resource agents
sudo subscription-manager repos --enable="rhel-sap-hana-for-rhel-7-server-rpms"
   
sudo yum install -y resource-agents-sap-hana
</code></pre>

Następnie należy utworzyć topologii platformy HANA. Uruchom następujące polecenia w jednym z węzłów klastra program Pacemaker:

<pre><code>sudo pcs property set maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID
sudo pcs resource create SAPHanaTopology_<b>HN1</b>_<b>03</b> SAPHanaTopology SID=<b>HN1</b> InstanceNumber=<b>03</b> --clone clone-max=2 clone-node-max=1 interleave=true
</code></pre>

Następnie należy utworzyć zasoby platformy HANA:

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.

sudo pcs resource create SAPHana_<b>HN1</b>_<b>03</b> SAPHana SID=<b>HN1</b> InstanceNumber=<b>03</b> PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false master notify=true clone-max=2 clone-node-max=1 interleave=true

sudo pcs resource create vip_<b>HN1</b>_<b>03</b> IPaddr2 ip="<b>10.0.0.13</b>"

sudo pcs resource create nc_<b>HN1</b>_<b>03</b> azure-lb port=625<b>03</b>

sudo pcs resource group add g_ip_<b>HN1</b>_<b>03</b> nc_<b>HN1</b>_<b>03</b> vip_<b>HN1</b>_<b>03</b>

sudo pcs constraint order SAPHanaTopology_<b>HN1</b>_<b>03</b>-clone then SAPHana_<b>HN1</b>_<b>03</b>-master symmetrical=false

sudo pcs constraint colocation add g_ip_<b>HN1</b>_<b>03</b> with master SAPHana_<b>HN1</b>_<b>03</b>-master 4000

sudo pcs property set maintenance-mode=false
</code></pre>

Upewnij się, że kondycja klastra jest ok i wszystkie zasoby są uruchamiane. Nie jest to ważne w węźle, które zasoby są uruchomione.

<pre><code>sudo pcs status

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# azure_fence     (stonith:fence_azure_arm):      Started hn1-db-0
#  Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
#      Started: [ hn1-db-0 hn1-db-1 ]
#  Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
#      Masters: [ hn1-db-0 ]
#      Slaves: [ hn1-db-1 ]
#  Resource Group: g_ip_HN1_03
#      nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
#      vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

## <a name="test-the-cluster-setup"></a>Testuj ustawienia klastra

W tej sekcji opisano, jak można przetestować konfigurację. Przed rozpoczęciem testu, upewnij się, że program Pacemaker nie ma żadnych akcji nie powiodło się (za pośrednictwem stan komputerów), istnieją bez ograniczeń nieoczekiwany lokalizacji (na przykład resztek testu migracji), i czy HANA jest stan synchronizacji, na przykład za pomocą systemReplicationStatus:

<pre><code>[root@hn1-db-0 ~]# sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
</code></pre>

### <a name="test-the-migration"></a>Migracja testowa

Stan zasobu przed rozpoczęciem testu:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

Można migrować węzła głównego platformy SAP HANA, wykonując następujące polecenie:

<pre><code>[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-master
</code></pre>

Jeśli ustawisz `AUTOMATED_REGISTER="false"`, to polecenie należy zmigrować węzła głównego platformy SAP HANA i grupę, która zawiera wirtualny adres IP, który hn1-db-1.

Po zakończeniu migracji "status" sudo"komputerów" dane wyjściowe wyglądają następująco

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Stopped: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

Zasób platformy SAP HANA na hn1-db-0 jest zatrzymana. W takim przypadku należy skonfigurować wystąpienie oprogramowania HANA jako pomocniczy, przez wykonanie tego polecenia:

<pre><code>[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMod
e=sync --name=SITE1
</code></pre>

Proces migracji tworzy ograniczenia lokalizacji, które muszą zostać usunięte ponownie:

<pre><code># Switch back to root
exit
[root@hn1-db-0 ~]# pcs resource clear SAPHana_HN1_03-master
</code></pre>

Monitoruj stan zasobu HANA przy użyciu "komputerów status". Gdy HANA jest uruchomiona na hn1-db-0, dane wyjściowe powinny wyglądać następująco

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent"></a>Agent platformy Azure preferowane testowy

Stan zasobu przed rozpoczęciem testu:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

Możesz przetestować instalację agenta programu Azure preferowane przez wyłączenie interfejsu sieciowego w węźle, w którym platformy SAP HANA jest uruchomiona jako wzorzec.
Zobacz [artykuł bazy wiedzy systemu Red Hat 79523](https://access.redhat.com/solutions/79523) opis o sposobie symulacji awarii sieci. W tym przykładzie używamy skryptu net_breaker do całkowite blokowanie dostępu do sieci.

<pre><code>[root@hn1-db-1 ~]# sh ./net_breaker.sh BreakCommCmd 10.0.0.6
</code></pre>

Maszyna wirtualna powinna teraz ponownie uruchom lub Zatrzymaj w zależności od konfiguracji klastra.
Jeśli ustawisz `stonith-action` ustawienie wartości off, maszyna wirtualna zostaje zatrzymana i zasoby są migrowane do uruchomionej maszyny wirtualnej.

> [!NOTE]
> Może upłynąć do 15 minut, aż maszyny wirtualnej jest w trybie online ponownie.

Po ponownym uruchomieniu maszyny wirtualnej zasobów platformy SAP HANA nie została uruchomiona jako pomocniczą, jeśli ustawisz `AUTOMATED_REGISTER="false"`. W takim przypadku należy skonfigurować wystąpienie oprogramowania HANA jako pomocniczy, przez wykonanie tego polecenia:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>

# Switch back to root and clean up the failed state
exit
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
</code></pre>

Stan zasobu po przeprowadzeniu testu:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

### <a name="test-a-manual-failover"></a>Testowanie ręcznej pracy awaryjnej

Stan zasobu przed rozpoczęciem testu:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

Aby przetestować ręcznej pracy awaryjnej, zatrzymywanie klastra w węźle hn1-db-0:

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>

Po włączeniu trybu failover można ponownie uruchomić klaster. Jeśli ustawisz `AUTOMATED_REGISTER="false"`, zasobów platformy SAP HANA w węźle hn1-db-0 nie można uruchomić jako pomocniczą. W takim przypadku należy skonfigurować wystąpienie oprogramowania HANA jako pomocniczy, przez wykonanie tego polecenia:

<pre><code>[root@hn1-db-0 ~]# pcs cluster start
[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> exit
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
</code></pre>

Stan zasobu po przeprowadzeniu testu:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

## <a name="next-steps"></a>Kolejne kroki

* [Azure maszyny wirtualne, planowania i implementacji dla rozwiązania SAP][planning-guide]
* [Wdrażania maszyn wirtualnych platformy Azure dla rozwiązania SAP][deployment-guide]
* [Wdrażania systemu DBMS na maszynach wirtualnych platformy Azure dla rozwiązania SAP][dbms-guide]
* Aby dowiedzieć się, jak zadbać o wysokiej dostępności i plan odzyskiwania po awarii oprogramowania SAP Hana na platformie Azure (duże wystąpienia), zobacz [platformy SAP HANA (duże wystąpienia) o wysokiej dostępności i odzyskiwania po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md)

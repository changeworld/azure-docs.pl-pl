---
title: Wysoka dostępność maszyn wirtualnych SAP HANA na platformie Azure w sles | Dokumenty firmy Microsoft
description: Wysoka dostępność sap HANA na maszynach wirtualnych platformy Azure na serwerze SUSE Linux Enterprise Server
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/06/2020
ms.author: radeltch
ms.openlocfilehash: 69dcf91957263cea36f8ff6db6a7af14588998ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927225"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-suse-linux-enterprise-server"></a>Wysoka dostępność sap HANA na maszynach wirtualnych platformy Azure na serwerze SUSE Linux Enterprise Server

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
[2388694]:https://launchpad.support.sap.com/#/notes/2388694
[401162]:https://launchpad.support.sap.com/#/notes/401162

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json

W przypadku lokalnego programowania można użyć replikacji systemu HANA lub użyć udostępnionego magazynu w celu ustalenia wysokiej dostępności dla systemu SAP HANA.
Na maszynach wirtualnych platformy Azure (maszyny wirtualne) replikacja systemu HANA na platformie Azure jest obecnie jedyną obsługiwana funkcją wysokiej dostępności. Sap HANA Replikacja składa się z jednego węzła podstawowego i co najmniej jednego węzła pomocniczego. Zmiany danych w węźle podstawowym są replikowane do węzła pomocniczego synchronicznie lub asynchronicznie.

W tym artykule opisano sposób wdrażania i konfigurowania maszyn wirtualnych, instalowania struktury klastra oraz instalowania i konfigurowania replikacji systemu SAP HANA.
W przykładowych konfiguracjach używane są polecenia instalacji, numer wystąpienia **03**i identyfikator systemu HANA **HN1.**

Najpierw przeczytaj następujące uwagi i dokumenty SAP:

* Uwaga SAP [1928533], która ma:
  * Lista rozmiarów maszyn wirtualnych platformy Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP.
  * Ważne informacje o pojemności dla rozmiarów maszyn wirtualnych platformy Azure.
  * Obsługiwane oprogramowanie SAP oraz system operacyjny (OS) i kombinacje baz danych.
  * Wymagana wersja jądra SAP dla systemów Windows i Linux na platformie Microsoft Azure.
* Uwaga SAP [2015553] zawiera listę wymagań wstępnych dla wdrożeń oprogramowania SAP obsługiwanych przez SAP na platformie Azure.
* Sap Note [2205917] ma zalecane ustawienia systemu operacyjnego dla SUSE Linux Enterprise Server dla aplikacji SAP.
* Sap Note [1944799] ma wytyczne SAP HANA dla SUSE Linux Enterprise Server dla aplikacji SAP.
* Uwaga SAP [2178632] zawiera szczegółowe informacje na temat wszystkich metryk monitorowania, które są zgłaszane dla sap na platformie Azure.
* Sap Note [2191498] ma wymaganą wersję agenta hosta SAP dla systemu Linux na platformie Azure.
* Uwaga SAP [2243692] ma informacje o licencjonowaniu SAP w systemie Linux na platformie Azure.
* Sap Note [1984787] ma ogólne informacje na temat SUSE Linux Enterprise Server 12.
* Uwaga SAP [1999351] zawiera dodatkowe informacje dotyczące rozwiązywania problemów z rozszerzeniem rozszerzonego monitorowania platformy Azure dla systemu SAP.
* Uwaga SAP [401162] ma informacje na temat unikania "adresu już używanego" podczas konfigurowania replikacji systemu HANA.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) ma wszystkie wymagane notatki SAP dla Linuksa.
* [Platformy IaaS z certyfikatem SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
* [Azure Virtual Machines planowania i implementacji dla SAP w systemie Linux][planning-guide] przewodnik.
* [Wdrożenie maszyn wirtualnych platformy Azure dla systemu SAP w systemie Linux][deployment-guide] (w tym artykule).
* [Wdrożenie usługi Azure Virtual Machines DBMS dla sap w][dbms-guide] systemie Linux przewodnik.
* [SUSE Linux Enterprise Server for SAP Applications 12 SP3 — najważniejsze wskazówki — przewodniki po najlepszych praktykach][sles-for-sap-bp]
  * Konfigurowanie infrastruktury zoptymalizowanej pod kątem wydajności SAP HANA SR (SLES dla aplikacji SAP 12 z zm.). Przewodnik zawiera wszystkie informacje wymagane do skonfigurowania replikacji systemu SAP HANA dla lokalnego rozwoju. Użyj tego przewodnika jako linii bazowej.
  * Konfigurowanie infrastruktury zoptymalizowanej pod kątem kosztów SAP HANA SR (SLES dla aplikacji SAP 12 zw.)

## <a name="overview"></a>Omówienie

Aby osiągnąć wysoką dostępność, SAP HANA jest zainstalowany na dwóch maszynach wirtualnych. Dane są replikowane przy użyciu replikacji systemu HANA.

![Omówienie wysokiej dostępności sap hana](./media/sap-hana-high-availability/ha-suse-hana.png)

Konfiguracja replikacji systemu SAP HANA używa dedykowanej wirtualnej nazwy hosta i wirtualnych adresów IP. Na platformie Azure moduł równoważenia obciążenia jest wymagany do używania wirtualnego adresu IP. Na poniższej liście przedstawiono konfigurację modułu równoważenia obciążenia:

* Konfiguracja front-endu: adres IP 10.0.0.13 dla hn1-db
* Konfiguracja zaplecza: Połączenie z podstawowymi interfejsami sieciowymi wszystkich maszyn wirtualnych, które powinny być częścią replikacji systemu HANA
* Port sondy: Port 62503
* Reguły równoważenia obciążenia: 30313 TCP, 30315 TCP, 30317 TCP

## <a name="deploy-for-linux"></a>Wdrażanie dla systemu Linux

Agent zasobów dla SAP HANA znajduje się w SUSE Linux Enterprise Server dla aplikacji SAP.
Portal Azure Marketplace zawiera obraz dla systemu SUSE Linux Enterprise Server for SAP Applications 12, którego można użyć do wdrożenia nowych maszyn wirtualnych.

### <a name="deploy-with-a-template"></a>Wdrażanie za pomocą szablonu

Można użyć jednego z szablonów szybkiego startu, które znajdują się w usłudze GitHub, aby wdrożyć wszystkie wymagane zasoby. Szablon wdraża maszyny wirtualne, moduł równoważenia obciążenia, zestaw dostępności i tak dalej.
Aby wdrożyć szablon, wykonaj następujące kroki:

1. Otwórz [szablon bazy danych][template-multisid-db] lub [szablon konwergentny][template-converged] w witrynie Azure portal. 
    Szablon bazy danych tworzy reguły równoważenia obciążenia tylko dla bazy danych. Szablon konwergentny tworzy również reguły równoważenia obciążenia dla wystąpienia ASCS/SCS i ERS (tylko linux). Jeśli planujesz zainstalować system oparty na SAP NetWeaver i chcesz zainstalować wystąpienie ASCS/SCS na tych samych komputerach, użyj [szablonu konwergentnego][template-converged].

1. Wprowadź następujące parametry:
    - **Sap System ID**: Wprowadź identyfikator systemu SAP systemu SAP, który chcesz zainstalować. Identyfikator jest używany jako prefiks dla zasobów, które są wdrażane.
    - **Typ stosu**: (Ten parametr ma zastosowanie tylko w przypadku użycia szablonu konwergentnego). Wybierz typ stosu SAP NetWeaver.
    - **Typ systemu operacyjnego:** Wybierz jedną z dystrybucji Linuksa. W tym przykładzie wybierz **SLES 12**.
    - **Typ bazy:** Wybierz **HANA**.
    - **Rozmiar systemu SAP:** Wprowadź liczbę SAPS, które nowy system zamierza zapewnić. Jeśli nie masz pewności, ile saps system wymaga, zapytaj partnera technologii SAP lub integratora systemu.
    - **Dostępność systemu**: Wybierz **HA**.
    - **Nazwa użytkownika administratora i hasło administratora:** Tworzony jest nowy użytkownik, którego można użyć do zalogowania się na komputerze.
    - **Nowa lub istniejąca podsieć**: Określa, czy należy utworzyć nową sieć wirtualną i podsieć, czy też istniejącą podsieć. Jeśli masz już sieć wirtualną połączoną z siecią lokalną, wybierz pozycję **Istniejąca**.
    - **Identyfikator**podsieci: Jeśli chcesz wdrożyć maszynę wirtualną w istniejącej sieci wirtualnej, do której zdefiniowano podsieć, do której powinna być przypisana maszyna wirtualna, nazwij identyfikator tej określonej podsieci. Identyfikator zwykle wygląda jak **/subskrypcje/\<identyfikator subskrypcji>/resourceGroups/\<nazwa grupy zasobów>/providers/Microsoft.Network/virtualNetworks/nazwa\<sieci wirtualnej>/podsieci/\<nazwa podsieci>**.

### <a name="manual-deployment"></a>Wdrożenie ręczne

> [!IMPORTANT]
> Upewnij się, że wybrany system operacyjny jest certyfikowany przez SAP dla sap HANA na określonych typach maszyn wirtualnych, których używasz. Listę typów maszyn wirtualnych z certyfikatem SAP HANA i wersji systemu operacyjnego dla tych można sprawdzić w [platformach IaaS z certyfikatem SAP HANA.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Pamiętaj, aby kliknąć na szczegóły typu maszyny Wirtualnej na liście, aby uzyskać pełną listę wersji systemu operacyjnego obsługiwanych przez SAP HANA dla określonego typu maszyny Wirtualnej
>  

1. Utwórz grupę zasobów.
1. Utwórz sieć wirtualną.
1. Utwórz zestaw dostępności.
   - Ustaw domenę aktualizacji max.
1. Tworzenie modułu równoważenia obciążenia (wewnętrznego). Zalecamy [standardowy moduł równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).
   - Wybierz sieć wirtualną utworzoną w kroku 2.
1. Utwórz maszynę wirtualną 1.
   - Użyj obrazu SLES4SAP w galerii platformy Azure, który jest obsługiwany dla SAP HANA na typ maszyny Wirtualnej, który został wybrany.
   - Wybierz zestaw dostępności utworzony w kroku 3.
1. Utwórz maszynę wirtualną 2.
   - Użyj obrazu SLES4SAP w galerii platformy Azure, który jest obsługiwany dla SAP HANA na typ maszyny Wirtualnej, który został wybrany.
   - Wybierz zestaw dostępności utworzony w kroku 3. 
1. Dodawanie dysków z danymi.
1. W przypadku korzystania ze standardowego modułu równoważenia obciążenia wykonaj następujące czynności konfiguracyjne:
   1. Najpierw utwórz pulę adresów IP front-end:
   
      1. Otwórz moduł równoważenia obciążenia, wybierz **pulę adresów IP frontu**i wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej puli adresów IP front-endu (na przykład **hana-frontend).**
      1. Ustaw **przypisanie** na **Statyczne** i wprowadź adres IP (na przykład **10.0.0.13**).
      1. Kliknij przycisk **OK**.
      1. Po utworzeniu nowej puli adresów IP front-end należy zwrócić uwagę na adres IP puli.
   
   1. Następnie utwórz pulę zaplecza:
   
      1. Otwórz moduł równoważenia obciążenia, wybierz **pule wewnętrznej bazy**danych i wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej puli zaplecza (na przykład **hana-backend).**
      1. Wybierz **sieć wirtualną**.
      1. Wybierz **pozycję Dodaj maszynę wirtualną**.
      1. Wybierz ** Maszynę wirtualną**.
      1. Wybierz maszyny wirtualne klastra SAP HANA i ich adresy IP.
      1. Wybierz pozycję **Dodaj**.
   
   1. Następnie utwórz sondę kondycji:
   
      1. Otwórz moduł równoważenia obciążenia, wybierz **sondy kondycji**i wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej sondy kondycji (na przykład **hana-hp**).
      1. Wybierz **TCP** jako protokół i port 625**03**. Zachowaj wartość **Interwał** ustawioną na 5, a wartość **progu złej** kondycji ustawiona na 2.
      1. Kliknij przycisk **OK**.
   
   1. Następnie utwórz reguły równoważenia obciążenia:
   
      1. Otwórz moduł równoważenia obciążenia, wybierz **reguły równoważenia obciążenia**i wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład **hana-lb**).
      1. Wybierz adres IP front-endu, pulę zaplecza i sondę kondycji utworzoną wcześniej (na przykład **hana-frontend**, **hana-backend** i **hana-hp**).
      1. Wybierz **porty wysokiej haw**.
      1. Wydłuż **limit czasu bezczynnego** do 30 minut.
      1. Upewnij się, że **włączysz pływający adres IP**.
      1. Kliknij przycisk **OK**.

   > [!Note]
   > Gdy maszyny wirtualne bez publicznych adresów IP są umieszczane w puli wewnętrznej bazy danych wewnętrznego (bez publicznego adresu IP) Standardowy moduł równoważenia obciążenia platformy Azure, nie będzie żadnych wychodzących połączeń z Internetem, chyba że zostanie wykonana dodatkowa konfiguracja, aby umożliwić routing do publicznych punktów końcowych. Aby uzyskać szczegółowe informacje na temat sposobu osiągnięcia łączności wychodzącej, zobacz [Łączność publiczna dla maszyn końcowych przy użyciu standardowego modułu równoważenia obciążenia platformy Azure w scenariuszach wysokiej dostępności SAP.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)  

1. Alternatywnie, jeśli scenariusz nakazuje użycie podstawowego modułu równoważenia obciążenia, wykonaj następujące kroki konfiguracji:
   1. Najpierw utwórz pulę adresów IP front-end:
   
      1. Otwórz moduł równoważenia obciążenia, wybierz **pulę adresów IP frontu**i wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej puli adresów IP front-endu (na przykład **hana-frontend).**
      1. Ustaw **przypisanie** na **Statyczne** i wprowadź adres IP (na przykład **10.0.0.13**).
      1. Kliknij przycisk **OK**.
      1. Po utworzeniu nowej puli adresów IP front-end należy zwrócić uwagę na adres IP puli.
   
   1. Następnie utwórz pulę zaplecza:
   
      1. Otwórz moduł równoważenia obciążenia, wybierz **pule wewnętrznej bazy**danych i wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej puli zaplecza (na przykład **hana-backend).**
      1. Wybierz **pozycję Dodaj maszynę wirtualną**.
      1. Wybierz zestaw dostępności utworzony w kroku 3.
      1. Wybierz maszyny wirtualne klastra SAP HANA.
      1. Kliknij przycisk **OK**.
   
   1. Następnie utwórz sondę kondycji:
   
      1. Otwórz moduł równoważenia obciążenia, wybierz **sondy kondycji**i wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej sondy kondycji (na przykład **hana-hp**).
      1. Wybierz **TCP** jako protokół i port 625**03**. Zachowaj wartość **Interwał** ustawioną na 5, a wartość **progu złej** kondycji ustawiona na 2.
      1. Kliknij przycisk **OK**.
   
   1. W przypadku systemu SAP HANA 1.0 należy utworzyć reguły równoważenia obciążenia:
   
      1. Otwórz moduł równoważenia obciążenia, wybierz **reguły równoważenia obciążenia**i wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład hana-lb-3**03**15).
      1. Wybierz adres IP front-endu, pulę zaplecza i sondę kondycji utworzoną wcześniej (na przykład **hana-frontend).**
      1. Zachowaj **protokół** ustawiony na **TCP**i wprowadź port 3**03**15.
      1. Wydłuż **limit czasu bezczynnego** do 30 minut.
      1. Upewnij się, że **włączysz pływający adres IP**.
      1. Kliknij przycisk **OK**.
      1. Powtórz te kroki dla portu 3**03**17.
   
   1. W przypadku systemu SAP HANA 2.0 należy utworzyć reguły równoważenia obciążenia dla systemowej bazy danych:
   
      1. Otwórz moduł równoważenia obciążenia, wybierz **reguły równoważenia obciążenia**i wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład hana-lb-3**03**13).
      1. Wybierz adres IP front-endu, pulę zaplecza i sondę kondycji utworzoną wcześniej (na przykład **hana-frontend).**
      1. Zachowaj **protokół** ustawiony na **TCP**i wprowadź port 3**03**13.
      1. Wydłuż **limit czasu bezczynnego** do 30 minut.
      1. Upewnij się, że **włączysz pływający adres IP**.
      1. Kliknij przycisk **OK**.
      1. Powtórz te kroki dla portu 3**03**14.
   
   1. W przypadku sap HANA 2.0 najpierw utwórz reguły równoważenia obciążenia dla bazy danych dzierżawy:
   
      1. Otwórz moduł równoważenia obciążenia, wybierz **reguły równoważenia obciążenia**i wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład hana-lb-3**03**40).
      1. Wybierz adres IP wewnętrznej bazy danych, pulę wewnętrznej bazy danych i sondę kondycji utworzoną wcześniej (na przykład **hana-frontend).**
      1. Zachowaj **protokół** ustawiony na **TCP**i wprowadź port 3**03**40.
      1. Wydłuż **limit czasu bezczynnego** do 30 minut.
      1. Upewnij się, że **włączysz pływający adres IP**.
      1. Kliknij przycisk **OK**.
      1. Powtórz te kroki dla portów 3**03**41 i 3**03**42.

   Aby uzyskać więcej informacji na temat wymaganych portów dla sap HANA, przeczytaj rozdział [Połączenia z bazami danych dzierżawców](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) w [przewodniku SAP HANA Tenant Databases](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) lub [SAP Note 2388694][2388694].

> [!IMPORTANT]
> Nie należy włączać sygnatur czasowych TCP na maszynach wirtualnych platformy Azure umieszczonych za modułem równoważenia obciążenia platformy Azure. Włączenie sygnatur czasowych TCP spowoduje niepowodzenie sond kondycji. Ustaw parametr **net.ipv4.tcp_timestamps** na **0**. Aby uzyskać szczegółowe informacje, zobacz [Sondy kondycji modułu równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).
> Zobacz też: UWAGA SAP [2382421](https://launchpad.support.sap.com/#/notes/2382421). 

## <a name="create-a-pacemaker-cluster"></a>Tworzenie klastra rozrusznika serca

Wykonaj kroki opisane w [sekcji Konfigurowanie rozrusznika serca na suse linux enterprise server na platformie Azure,](high-availability-guide-suse-pacemaker.md) aby utworzyć podstawowy klaster rozrusznika serca dla tego serwera HANA. Można użyć tego samego klastra rozrusznika dla SAP HANA i SAP NetWeaver (A)SCS.

## <a name="install-sap-hana"></a>Instalowanie platformy SAP HANA

W tych sekcjach należy wykonać następujące prefiksy:
- **[A]**: Krok dotyczy wszystkich węzłów.
- **[1]**: Krok dotyczy tylko węzła 1.
- **[2]**: Krok dotyczy tylko węzła 2 klastra rozrusznika serca.

1. **[A]** Konfigurowanie układu dysku: **Menedżer woluminów logicznych (LVM)**.

   Zaleca się używanie lvm dla woluminów, które przechowują dane i pliki dziennika. W poniższym przykładzie przyjęto założenie, że maszyny wirtualne mają cztery dyski danych dołączone, które są używane do tworzenia dwóch woluminów.

   Wyświetl listę wszystkich dostępnych dysków:

   <pre><code>ls /dev/disk/azure/scsi1/lun*
   </code></pre>

   Przykładowe dane wyjściowe:

   <pre><code>
   /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
   </code></pre>

   Tworzenie woluminów fizycznych dla wszystkich dysków, których chcesz użyć:

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0
   sudo pvcreate /dev/disk/azure/scsi1/lun1
   sudo pvcreate /dev/disk/azure/scsi1/lun2
   sudo pvcreate /dev/disk/azure/scsi1/lun3
   </code></pre>

   Utwórz grupę woluminów dla plików danych. Użyj jednej grupy woluminów dla plików dziennika i jednej dla udostępnionego katalogu SAP HANA:

   <pre><code>sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
   sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
   sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
   </code></pre>

   Tworzenie woluminów logicznych. Wolumin liniowy jest tworzony `lvcreate` podczas `-i` używania bez przełącznika. Sugerujemy utworzenie woluminu rozłożonego w celu uzyskania lepszej `-i` wydajności we/wy, gdzie argumentem powinna być liczba podstawowej objętości fizycznej. W tym dokumencie dla woluminu danych są używane `-i` dwa woluminy fizyczne, więc argument przełącznika jest ustawiony na **2**. Jeden wolumin fizyczny jest używany dla `-i` woluminu dziennika, więc nie przełącznik jest jawnie używany. Użyj `-i` przełącznika i ustaw go na liczbę podstawowej woluminu fizycznego, gdy używasz więcej niż jednego woluminu fizycznego dla każdego woluminu danych, dziennika lub udostępnionego woluminu.

   <pre><code>sudo lvcreate <b>-i 2</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
   sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
   sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   </code></pre>

   Utwórz katalogi instalacji i skopiuj UUID wszystkich woluminów logicznych:

   <pre><code>sudo mkdir -p /hana/data/<b>HN1</b>
   sudo mkdir -p /hana/log/<b>HN1</b>
   sudo mkdir -p /hana/shared/<b>HN1</b>
   # Write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log, and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   sudo blkid
   </code></pre>

   Tworzenie `fstab` wpisów dla trzech woluminów logicznych:       

   <pre><code>sudo vi /etc/fstab
   </code></pre>

   Wstaw następujący wiersz `/etc/fstab` do pliku:      

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
   </code></pre>

   Zamontuj nowe woluminy:

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** Konfigurowanie układu dysku: **Dyski zwykłe**.

   W przypadku systemów demonstracyjnych można umieścić dane HANA i pliki dziennika na jednym dysku. Utwórz partycję na /dev/disk/azure/scsi1/lun0 i sformatuj ją za pomocą xfs:

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
   
   # Write down the ID of /dev/disk/azure/scsi1/lun0-part1
   sudo /sbin/blkid
   sudo vi /etc/fstab
   </code></pre>

   Wstaw ten wiersz w pliku /etc/fstab:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
   </code></pre>

   Utwórz katalog docelowy i zainstaluj dysk:

   <pre><code>sudo mkdir /hana
   sudo mount -a
   </code></pre>

1. **[A]** Konfigurowanie rozpoznawania nazw hostów dla wszystkich hostów.

   Można użyć serwera DNS lub zmodyfikować plik /etc/hosts we wszystkich węzłach. W tym przykładzie pokazano, jak używać pliku /etc/hosts.
   Zastąp adres IP i nazwa hosta w następujących poleceniach:

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Wstaw następujące wiersze w pliku /etc/hosts. Zmień adres IP i nazwa hosta, aby dopasować go do środowiska:

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** Zainstaluj pakiety wysokiej dostępności SAP HANA:

   <pre><code>sudo zypper install SAPHanaSR
   </code></pre>

Aby zainstalować replikację systemu SAP HANA, należy postępować zgodnie z rozdziałem 4 [przewodnika po scenariuszach zoptymalizowanych pod kątem wydajności SAP HANA SR](https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/).

1. **[A]** Uruchom program **hdblcm** z DYSKU HANA. Wprowadź następujące wartości w wierszu polecenia:
   * Wybierz instalację: Wprowadź **1**.
   * Wybierz dodatkowe komponenty do instalacji: Wprowadź **1**.
   * Wprowadź ścieżkę instalacji [/hana/shared]: Wybierz enter.
   * Wprowadź nazwę hosta lokalnego [..]: wybierz enter.
   * Czy chcesz dodać dodatkowe hosty do systemu? (r/n) [n]: Wybierz enter.
   * Wprowadź identyfikator systemu SAP HANA: Wprowadź identyfikator SID HANA, na przykład: **HN1**.
   * Wprowadź numer wystąpienia [00]: Wprowadź numer wystąpienia HANA. Wprowadź **03,** jeśli użyto szablonu platformy Azure lub po sekcji ręcznego wdrażania tego artykułu.
   * Wybierz tryb bazy danych / Wprowadź indeks [1]: Wybierz Enter.
   * Wybierz użycie systemu / Wprowadź indeks [4]: Wybierz wartość użycia systemu.
   * Wprowadź lokalizację woluminów danych [/hana/data/HN1]: wybierz enter.
   * Wprowadź lokalizację woluminów dziennika [/hana/log/HN1]: wybierz enter.
   * Czy ograniczyć maksymalną alokację pamięci? [n]: Wybierz enter.
   * Wprowadź nazwę hosta certyfikatu dla hosta '..."' [...]: Wybierz enter.
   * Wprowadź hasło użytkownika (sapadm) agenta hosta SAP: wprowadź hasło użytkownika agenta hosta.
   * Potwierdź hasło użytkownika (sapadm) agenta hosta SAP: wprowadź ponownie hasło użytkownika agenta hosta, aby potwierdzić.
   * Wprowadź administratora systemu (hdbadm) Hasło: Wprowadź hasło administratora systemu.
   * Potwierdź hasło administratora systemu (hdbadm): Wprowadź hasło administratora systemu ponownie, aby potwierdzić.
   * Wprowadź katalog macierzysty administratora systemu [/usr/sap/HN1/home]: wybierz enter.
   * Wprowadź powłokę logowania administratora systemu [/bin/sh]: wybierz enter.
   * Wprowadź identyfikator użytkownika administratora systemu [1001]: wybierz enter.
   * Wprowadź identyfikator grupy użytkowników (sapsys) [79]: Wybierz enter.
   * Wprowadź hasło użytkownika bazy danych (SYSTEM): Wprowadź hasło użytkownika bazy danych.
   * Potwierdź hasło użytkownika bazy danych (SYSTEM): Wprowadź hasło użytkownika bazy danych ponownie, aby potwierdzić.
   * Uruchom ponownie system po ponownym uruchomieniu komputera? [n]: Wybierz enter.
   * Czy chcesz kontynuować? (y/n): Sprawdź poprawność podsumowania. Wprowadź **y,** aby kontynuować.

1. **[A]** Uaktualnij agenta hosta SAP.

   Pobierz najnowsze archiwum agenta hosta SAP z [centrum oprogramowania SAP][sap-swcenter] i uruchom następujące polecenie, aby uaktualnić agenta. Zastąp ścieżkę do archiwum, aby wskazać pobrany plik:

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>Konfigurowanie replikacji systemu SAP HANA 2.0

W tych sekcjach należy wykonać następujące prefiksy:

* **[A]**: Krok dotyczy wszystkich węzłów.
* **[1]**: Krok dotyczy tylko węzła 1.
* **[2]**: Krok dotyczy tylko węzła 2 klastra rozrusznika serca.

1. **[1]** Tworzenie bazy danych dzierżawy.

   Jeśli używasz SAP HANA 2.0 lub MDC, utwórz bazę danych dzierżawy dla systemu SAP NetWeaver. Wymień **NW1** na identyfikator SID systemu SAP.

   Wykonaj następujące polecenie jako <hanasid\>adm:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** Konfigurowanie replikacji systemu w pierwszym węźle:

   Tworzenie kopii zapasowych baz danych jako <\>hanasid adm:

   <pre><code>hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Skopiuj systemowe pliki PKI do lokacji dodatkowej:

   <pre><code>scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT   <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY  <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   Utwórz lokację główną:

   <pre><code>hdbnsutil -sr_enable --name=<b>SITE1</b>
   </code></pre>

1. **[2]** Konfigurowanie replikacji systemu w drugim węźle:
    
   Zarejestruj drugi węzeł, aby uruchomić replikację systemu. Uruchom następujące polecenie jako <hanasid\>adm :

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Konfigurowanie replikacji systemu SAP HANA 1.0

W tych sekcjach należy wykonać następujące prefiksy:

* **[A]**: Krok dotyczy wszystkich węzłów.
* **[1]**: Krok dotyczy tylko węzła 1.
* **[2]**: Krok dotyczy tylko węzła 2 klastra rozrusznika serca.

1. **[1]** Utwórz wymaganych użytkowników.

   Uruchom następujące polecenie jako katalog główny. Pamiętaj, aby zastąpić pogrubione ciągi (HANA System ID **HN1** i numer wystąpienia **03)** wartościami instalacji SAP HANA:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]** Utwórz wpis magazynu kluczy.

   Uruchom następujące polecenie jako katalog główny, aby utworzyć nowy wpis magazynu kluczy:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1]** Tworzenie kopii zapasowej bazy danych.

   Tworzenie kopii zapasowych baz danych jako katalogu głównego:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Jeśli używasz instalacji wielodostępnych, również kopii zapasowej bazy danych dzierżawy:

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]** Konfigurowanie replikacji systemu w pierwszym węźle.

   Utwórz lokację główną jako <\>hanasid adm :

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** Konfigurowanie replikacji systemu w węźle pomocniczym.

   Zarejestruj witrynę dodatkową jako <hanasid\>adm:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="create-sap-hana-cluster-resources"></a>Tworzenie zasobów klastra SAP HANA

Najpierw utwórz topologię HANA. Uruchom następujące polecenia w jednym z węzłów klastra rozrusznika serca:

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

Następnie utwórz zasoby HANA:

> [!IMPORTANT]
> Ostatnie testy ujawniły sytuacje, w których netcat przestaje odpowiadać na żądania z powodu zaległości i ograniczenia obsługi tylko jednego połączenia. Zasób netcat przestaje nasłuchiwać żądań modułu równoważenia obciążenia platformy Azure, a zmienny adres IP staje się niedostępny.  
> W przypadku istniejących klastrów rozruszników zalecamy w przeszłości zastąpienie netcata socatem. Obecnie zalecamy użycie agenta zasobów azure-lb, który jest częścią agentów zasobów pakietu, z następującymi wymaganiami dotyczącymi wersji pakietu:
> - W przypadku SLES 12 SP4/SP5 wersja musi być co najmniej resource-agents-4.3.018.a7fb5035-3.30.1.  
> - Dla SLES 15/15 SP1 wersja musi być co najmniej resource-agents-4.3.0184.6ee15eb2-4.13.1.  
>
> Należy pamiętać, że zmiana będzie wymagać krótkiego przestoju.  
> W przypadku istniejących klastrów stymulatora, jeśli konfiguracja została już zmieniona, aby używać socat zgodnie z opisem w [umacnianiu wykrywania równoważenia obciążenia azure,](https://www.suse.com/support/kb/doc/?id=7024128)nie ma wymogu natychmiastowego przejścia na agenta zasobów azure-lb.

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

sudo crm configure primitive rsc_nc_<b>HN1</b>_HDB<b>03</b> azure-lb port=625<b>03</b>

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

Upewnij się, że stan klastra jest ok i że wszystkie zasoby są uruchamiane. Nie jest ważne, w którym węźle są uruchomione zasoby.

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
#     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
</code></pre>

## <a name="test-the-cluster-setup"></a>Testowanie konfiguracji klastra

W tej sekcji opisano, jak można przetestować konfigurację. Każdy test zakłada, że jesteś root i wzorzec SAP HANA jest uruchomiony na maszynie wirtualnej **hn1-db-0.**

### <a name="test-the-migration"></a>Testowanie migracji

Przed rozpoczęciem testu upewnij się, że rozrusznik serca nie ma żadnych nieudanych działań (za pośrednictwem crm_mon -r), nie ma nieoczekiwanych ograniczeń lokalizacji (na przykład resztki testu migracji) i że HANA jest stan synchronizacji, na przykład z SAPHanaSR-showAttr:

<pre><code>hn1-db-0:~ # SAPHanaSR-showAttr

Global cib-time
--------------------------------
global Mon Aug 13 11:26:04 2018

Hosts    clone_state lpa_hn1_lpt node_state op_mode   remoteHost    roles                            score site  srmode sync_state version                vhost
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
hn1-db-0 PROMOTED    1534159564  online     logreplay nws-hana-vm-1 4:P:master1:master:worker:master 150   SITE1 sync   PRIM       2.00.030.00.1522209842 nws-hana-vm-0
hn1-db-1 DEMOTED     30          online     logreplay nws-hana-vm-0 4:S:master1:master:worker:master 100   SITE2 sync   SOK        2.00.030.00.1522209842 nws-hana-vm-1
</code></pre>

Węzeł główny SAP HANA można przeprowadzić, wykonując następujące polecenie:

<pre><code>crm resource migrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
</code></pre>

Jeśli ustawisz `AUTOMATED_REGISTER="false"`, ta sekwencja poleceń powinna migrować węzeł główny SAP HANA i grupę zawierającą wirtualny adres IP do hn1-db-1.

Po zakończeniu migracji crm_mon -r dane wyjściowe wyglądają tak

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
     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1

Failed Actions:
* rsc_SAPHana_HN1_HDB03_start_0 on hn1-db-0 'not running' (7): call=84, status=complete, exitreason='none',
    last-rc-change='Mon Aug 13 11:31:37 2018', queued=0ms, exec=2095ms
</code></pre>

Zasób SAP HANA na hn1-db-0 nie uruchamia się jako pomocniczy. W takim przypadku skonfiguruj wystąpienie HANA jako pomocnicze, wykonując to polecenie:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>
</code></pre>

Migracja tworzy ograniczenia lokalizacji, które należy ponownie usunąć:

<pre><code># Switch back to root and clean up the failed state
exit
hn1-db-0:~ # crm resource unmigrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
</code></pre>

Należy również oczyścić stan zasobu węzła pomocniczego:

<pre><code>hn1-db-0:~ # crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

Monitorowanie stanu zasobu HANA przy użyciu crm_mon -r. Po uruchomieniu HANA na hn1-db-0, wyjście powinno wyglądać tak

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
     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent-not-sbd"></a>Testowanie agenta ogrodzenia platformy Azure (nie SBD)

Można przetestować konfigurację agenta ogrodzenia platformy Azure, wyłączając interfejs sieciowy w węźle hn1-db-0:

<pre><code>sudo ifdown eth0
</code></pre>

Maszyna wirtualna powinna teraz zostać ponownie uruchomiona lub zatrzymana w zależności od konfiguracji klastra.
Jeśli `stonith-action` ustawienie zostanie wyłączone, maszyna wirtualna zostanie zatrzymana, a zasoby zostaną przeniesione do uruchomionej maszyny wirtualnej.

Po ponownym uruchomieniu maszyny wirtualnej zasób SAP HANA nie `AUTOMATED_REGISTER="false"`uruchamia się jako pomocniczy, jeśli ustawisz . W takim przypadku skonfiguruj wystąpienie HANA jako pomocnicze, wykonując to polecenie:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="test-sbd-fencing"></a>Test ogrodzenia SBD

Można przetestować konfigurację SBD zabijając proces inkwizytora.

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

Węzeł klastra hn1-db-0 powinien zostać ponownie uruchomiony. Usługa rozrusznika serca może nie rozpocząć się później. Pamiętaj, aby uruchomić go ponownie.

### <a name="test-a-manual-failover"></a>Testowanie ręcznego trybu failover

Ręczne przejście awaryjne można `pacemaker` przetestować, zatrzymując usługę w węźle hn1-db-0:

<pre><code>service pacemaker stop
</code></pre>

Po przewijaniu awaryjnym można ponownie uruchomić usługę. Jeśli ustawisz `AUTOMATED_REGISTER="false"`, zasób SAP HANA w węźle hn1-db-0 nie może uruchomić jako pomocniczy. W takim przypadku skonfiguruj wystąpienie HANA jako pomocnicze, wykonując to polecenie:

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
> Upewnij się, że wybrany system operacyjny jest certyfikowany przez SAP dla sap HANA na określonych typach maszyn wirtualnych, których używasz. Listę typów maszyn wirtualnych z certyfikatem SAP HANA i wersji systemu operacyjnego dla tych można sprawdzić w [platformach IaaS z certyfikatem SAP HANA.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Pamiętaj, aby kliknąć na szczegóły typu maszyny Wirtualnej na liście, aby uzyskać pełną listę wersji systemu operacyjnego obsługiwanych przez SAP HANA dla określonego typu maszyny Wirtualnej

Uruchom wszystkie przypadki testowe, które są wymienione w SAP HANA SR Performance Optimized Scenario lub SAP HANA SR Cost Optimized Scenario guide, w zależności od przypadku użycia. Przewodniki można znaleźć na [stronie Sprawdzone metody dotyczące rozwiązania SLES for SAP][sles-for-sap-bp].

Poniższe testy są kopią opisów testów przewodnika SAP HANA SR Performance Optimized Scenario SUSE Linux Enterprise Server for SAP Applications 12 SP1. Aby uzyskać aktualną wersję, zawsze przeczytaj również sam przewodnik. Zawsze upewnij się, że hana jest zsynchronizowany przed rozpoczęciem testu, a także upewnij się, że konfiguracja rozrusznika jest poprawna.

W poniższych opisach testów przyjmujemy PREFER_SITE_TAKEOVER="true" i AUTOMATED_REGISTER="false".
UWAGA: Następujące testy są przeznaczone do uruchamiania w sekwencji i zależą od stanu zakończenia poprzednich testów.

1. TEST 1: ZATRZYMAJ PODSTAWOWĄ BAZĘ DANYCH W WĘŹLE 1

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Uruchom następujące polecenia jako <hanasid\>adm w węźle hn1-db-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Rozrusznik serca należy wykryć zatrzymane wystąpienie HANA i pracy awaryjnej do drugiego węzła. Po zakończeniu pracy awaryjnej wystąpienie HANA w węźle hn1-db-0 jest zatrzymane, ponieważ rozrusznik serca nie rejestruje automatycznie węzła jako pomocniczego HANA.

   Uruchom następujące polecenia, aby zarejestrować węzeł hn1-db-0 jako pomocniczy i oczyścić nieudany zasób.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Stan zasobu po teście:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TEST 2: ZATRZYMAJ PODSTAWOWĄ BAZĘ DANYCH W WĘŹLE 2

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Uruchom następujące polecenia jako <hanasid\>adm w węźle hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Rozrusznik serca należy wykryć zatrzymane wystąpienie HANA i pracy awaryjnej do drugiego węzła. Po zakończeniu pracy awaryjnej wystąpienie HANA w węźle hn1-db-1 jest zatrzymane, ponieważ rozrusznik serca nie rejestruje automatycznie węzła jako pomocniczego HANA.

   Uruchom następujące polecenia, aby zarejestrować węzeł hn1-db-1 jako pomocniczy i oczyścić nieudany zasób.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Stan zasobu po teście:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 3: AWARIA PODSTAWOWEJ BAZY DANYCH W WĘŹLE

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Uruchom następujące polecenia jako <hanasid\>adm w węźle hn1-db-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>
   
   Rozrusznik serca należy wykryć zabitych hana wystąpienia i pracy awaryjnej do drugiego węzła. Po zakończeniu pracy awaryjnej wystąpienie HANA w węźle hn1-db-0 jest zatrzymane, ponieważ rozrusznik serca nie rejestruje automatycznie węzła jako pomocniczego HANA.

   Uruchom następujące polecenia, aby zarejestrować węzeł hn1-db-0 jako pomocniczy i oczyścić nieudany zasób.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Stan zasobu po teście:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TEST 4: AWARIA PODSTAWOWEJ BAZY DANYCH W WĘŹLE 2

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Uruchom następujące polecenia jako <hanasid\>adm w węźle hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Rozrusznik serca należy wykryć zabitych hana wystąpienia i pracy awaryjnej do drugiego węzła. Po zakończeniu pracy awaryjnej wystąpienie HANA w węźle hn1-db-1 jest zatrzymane, ponieważ rozrusznik serca nie rejestruje automatycznie węzła jako pomocniczego HANA.

   Uruchom następujące polecenia, aby zarejestrować węzeł hn1-db-1 jako pomocniczy i oczyścić nieudany zasób.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Stan zasobu po teście:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 5: AWARIA WĘZŁA LOKACJI GŁÓWNEJ (WĘZEŁ 1)

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Uruchom następujące polecenia jako katalog główny w węźle hn1-db-0:

   <pre><code>hn1-db-0:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Rozrusznik serca powinien wykryć zabity węzeł klastra i ogrodzić węzeł. Gdy węzeł jest ogrodzony, rozrusznik spowoduje przejęcie wystąpienia HANA. Po ponownym uruchomieniu ogrodzonego węzła rozrusznik nie uruchomi się automatycznie.

   Uruchom następujące polecenia, aby uruchomić program Pacemaker, wyczyścić komunikaty SBD dla węzła hn1-db-0, zarejestrować węzeł hn1-db-0 jako pomocniczy i oczyścić nieudany zasób.

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

   Stan zasobu po teście:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TEST 6: WĘZEŁ LOKACJI DODATKOWEJ AWARII (WĘZEŁ 2)

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Uruchom następujące polecenia jako katalog główny w węźle hn1-db-1:

   <pre><code>hn1-db-1:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Rozrusznik serca powinien wykryć zabity węzeł klastra i ogrodzić węzeł. Gdy węzeł jest ogrodzony, rozrusznik spowoduje przejęcie wystąpienia HANA. Po ponownym uruchomieniu ogrodzonego węzła rozrusznik nie uruchomi się automatycznie.

   Uruchom następujące polecenia, aby uruchomić program Pacemaker, wyczyścić komunikaty SBD dla węzła hn1-db-1, zarejestrować węzeł hn1-db-1 jako pomocniczy i oczyścić nieudany zasób.

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

   Stan zasobu po teście:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 7: ZATRZYMAJ POMOCNICZĄ BAZĘ DANYCH W WĘŹLE 2

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Uruchom następujące polecenia jako <hanasid\>adm w węźle hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Rozrusznik serca wykryje zatrzymane wystąpienie HANA i oznaczy zasób jako nie powiódł się w węźle hn1-db-1. Rozrusznik serca należy automatycznie ponownie uruchomić wystąpienie HANA. Uruchom następujące polecenie, aby oczyścić stan awarii.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Stan zasobu po teście:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 8: AWARIA POMOCNICZEJ BAZY DANYCH W WĘŹLE 2

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Uruchom następujące polecenia jako <hanasid\>adm w węźle hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Rozrusznik serca wykryje zabite wystąpienie HANA i oznaczy zasób jako nie powiódł się w węźle hn1-db-1. Uruchom następujące polecenie, aby oczyścić stan awarii. Rozrusznik serca należy następnie automatycznie ponownie uruchomić wystąpienie HANA.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Stan zasobu po teście:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 9: WĘZEŁ LOKACJI DODATKOWEJ AWARII (WĘZEŁ 2) Z URUCHOMIE POMOCNICZĄ BAZĄ DANYCH HANA

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Uruchom następujące polecenia jako katalog główny w węźle hn1-db-1:

   <pre><code>hn1-db-1:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Rozrusznik serca powinien wykryć zabity węzeł klastra i ogrodzić węzeł. Po ponownym uruchomieniu ogrodzonego węzła rozrusznik nie uruchomi się automatycznie.

   Uruchom następujące polecenia, aby uruchomić program Pacemaker, wyczyścić komunikaty SBD dla węzła hn1-db-1 i oczyścić nieudany zasób.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker  
   
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Stan zasobu po teście:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

## <a name="next-steps"></a>Następne kroki

* [Planowanie i implementacja maszyn wirtualnych platformy Azure dla systemu SAP][planning-guide]
* [Wdrożenie maszyn wirtualnych platformy Azure dla systemu SAP][deployment-guide]
* [Wdrożenie usługi DBMS maszyn wirtualnych platformy Azure dla systemu SAP][dbms-guide]


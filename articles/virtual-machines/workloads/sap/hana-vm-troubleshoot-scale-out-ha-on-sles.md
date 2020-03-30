---
title: Sap HANA skalowanie w poziomie HSR-Pacemaker z SLES na maszynach wirtualnych platformy Azure rozwiązywania problemów| Dokumenty firmy Microsoft
description: Przewodnik do sprawdzania i rozwiązywania problemów ze złożoną konfiguracją sap HANA o wysokiej dostępności w poziomie w oparciu o replikację systemu SAP HANA (HSR) i rozrusznik serca na SLES 12 SP3 uruchomionym na maszynach wirtualnych platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/24/2018
ms.author: hermannd
ms.openlocfilehash: e93b3412785817050ac53030be9ff2172a678c06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617122"
---
# <a name="verify-and-troubleshoot-sap-hana-scale-out-high-availability-setup-on-sles-12-sp3"></a>Weryfikowanie i rozwiązywanie problemów z konfiguracją wysokiej dostępności SAP HANA o wysokiej dostępności w dodatku SP3 dla systemu SLES 12 

[sles-pacemaker-ha-guide]:high-availability-guide-suse-pacemaker.md
[sles-hana-scale-out-ha-paper]:https://www.suse.com/documentation/suse-best-practices/singlehtml/SLES4SAP-hana-scaleOut-PerfOpt-12/SLES4SAP-hana-scaleOut-PerfOpt-12.html
[sap-hana-iaas-list]:https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html
[suse-pacemaker-support-log-files]:https://www.suse.com/support/kb/doc/?id=7022702
[azure-linux-multiple-nics]:https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics
[suse-cloud-netconfig]:https://www.suse.com/c/multi-nic-cloud-netconfig-ec2-azure/
[sap-list-port-numbers]:https://help.sap.com/viewer/ports
[sles-12-ha-paper]:https://www.suse.com/documentation/sle-ha-12/pdfdoc/book_sleha/book_sleha.pdf
[sles-zero-downtime-paper]:https://www.suse.com/media/presentation/TUT90846_towards_zero_downtime%20_how_to_maintain_sap_hana_system_replication_clusters.pdf
[sap-nw-ha-guide-sles]:high-availability-guide-suse.md
[sles-12-for-sap]:https://www.suse.com/media/white-paper/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf


Ten artykuł ułatwia sprawdzenie konfiguracji klastra rozrusznika dla środowiska SAP HANA skalowawczej w poziomie, która jest uruchamiana na maszynach wirtualnych platformy Azure (maszyny wirtualne). Konfiguracja klastra została wykonana w połączeniu z sap hana replikacji systemu (HSR) i pakiet SUSE RPM SAPHanaSR-ScaleOut. Wszystkie testy zostały wykonane tylko na SUSE SLES 12 SP3. Sekcje artykułu obejmują różne obszary i zawierają przykładowe polecenia i fragmenty plików konfiguracyjnych. Zaleca się te przykłady jako metodę weryfikacji i sprawdzania całej konfiguracji klastra.



## <a name="important-notes"></a>Ważne uwagi

Wszystkie testy skalowania w poziomie SAP HANA w połączeniu z replikacją systemu SAP HANA i rozrusznikiem serca zostały wykonane tylko z sap HANA 2.0. Wersja systemu operacyjnego była SUSE Linux Enterprise Server 12 SP3 dla aplikacji SAP. Najnowszy pakiet RPM, SAPHanaSR-ScaleOut firmy SUSE, został użyty do skonfigurowania klastra rozrusznika serca.
SUSE [opublikowałszczegieł opis tej konfiguracji zoptymalizowanej pod kątem wydajności.][sles-hana-scale-out-ha-paper]

W przypadku typów maszyn wirtualnych obsługiwanych w skali w poziomie SAP HANA sprawdź [katalog IaaS z certyfikatem SAP HANA][sap-hana-iaas-list].

Wystąpił problem techniczny ze skalą SAP HANA w poziomie w połączeniu z wieloma podsieciami i vNIC i konfigurowaniem HSR. Jest to obowiązkowe, aby użyć najnowszych poprawek SAP HANA 2.0, gdzie ten problem został rozwiązany. Obsługiwane są następujące wersje SAP HANA: 

* rev2.00.024.04 lub wyższy 
* rev2.00.032 lub wyższy

Jeśli potrzebujesz pomocy ze strony SUSE, postępuj zgodnie z tym [przewodnikiem][suse-pacemaker-support-log-files]. Zbierz wszystkie informacje o klastrze wysokiej dostępności SAP HANA (HA), zgodnie z opisem w artykule. Obsługa SUSE potrzebuje tych informacji do dalszej analizy.

Podczas testowania wewnętrznego konfiguracja klastra została pomylona przez normalne zamknięcie maszyny wirtualnej w sposób wdzięczny za pośrednictwem witryny Azure portal. Dlatego zaleca się przetestowanie klastra pracy awaryjnej za pomocą innych metod. Użyj metod, takich jak wymuszenie paniki jądra, zamknięcie sieci lub migracja zasobu **msl.** Zobacz szczegóły w poniższych sekcjach. Założenie jest takie, że standardowe zamknięcie odbywa się z zamiarem. Najlepszym przykładem zamierzonego zamknięcia jest konserwacja. Zobacz szczegóły w [temat Planowana konserwacja](#planned-maintenance).

Ponadto podczas testowania wewnętrznego konfiguracja klastra została pomylona po ręcznym przejęciu systemu SAP HANA, gdy klaster był w trybie konserwacji. Zaleca się ponowne ponowne przełączenie go ręcznie przed zakończeniem trybu konserwacji klastra. Inną opcją jest wyzwolenie pracy awaryjnej przed przełączeniem klastra w tryb konserwacji. Aby uzyskać więcej informacji, zobacz [Planowana konserwacja](#planned-maintenance). W dokumentacji firmy SUSE opisano, jak można zresetować klaster w ten sposób za pomocą polecenia **crm.** Ale podejście wspomniane wcześniej było solidne podczas testów wewnętrznych i nigdy nie wykazało żadnych nieoczekiwanych skutków ubocznych.

Korzystając z polecenia **migracji crm,** upewnij się, że wyczyścisz konfigurację klastra. Dodaje ograniczenia lokalizacji, które mogą nie być świadome. Te ograniczenia mają wpływ na zachowanie klastra. Zobacz więcej szczegółów w [temat Planowana konserwacja](#planned-maintenance).



## <a name="test-system-description"></a>Opis systemu testowego

 W przypadku weryfikacji i certyfikacji ha skalowanej w poziomie SAP HANA użyto konfiguracji. Składał się z dwóch systemów z trzema węzłami SAP HANA każdy: jeden master i dwóch pracowników. W poniższej tabeli wymieniono nazwy maszyn wirtualnych i wewnętrzne adresy IP. Wszystkie próbki weryfikacji, które następują zostały wykonane na tych maszynach wirtualnych. Korzystając z tych nazw maszyn wirtualnych i adresów IP w przykładach poleceń, można lepiej zrozumieć polecenia i ich dane wyjściowe:


| Typ węzła | Nazwa maszyny wirtualnej | Adres IP |
| --- | --- | --- |
| Węzeł główny na miejscu 1 | hso-hana-vm-s1-0 | 10.0.0.30 |
| Węzeł pracownika 1 na miejscu 1 | hso-hana-vm-s1-1 | 10.0.0.31 |
| Węzeł pracownika 2 na miejscu 1 | hso-hana-vm-s1-2 | 10.0.0.32 |
| | | |
| Węzeł główny na miejscu 2 | hso-hana-vm-s2-0 | 10.0.0.40 |
| Węzeł pracownika 1 na miejscu 2 | hso-hana-vm-s2-1 | 10.0.0.41 |
| Węzeł pracownika 2 na miejscu 2 | hso-hana-vm-s2-2  | 10.0.0.42 |
| | | |
| Węzeł producenta większościowego | hso-hana-dm | 10.0.0.13 |
| Serwer urządzeń SBD | hso-hana-sbd | 10.0.0.19 |
| | | |
| Serwer NFS 1 | hso-nfs-vm-0 | 10.0.0.15 |
| Serwer NFS 2 | hso-nfs-vm-1 | 10.0.0.14 |



## <a name="multiple-subnets-and-vnics"></a>Wiele podsieci i maszyn wirtualnych

Zgodnie z zaleceniami sieci SAP HANA utworzono trzy podsieci w ramach jednej sieci wirtualnej platformy Azure. Sap HANA skalowania w poziomie na platformie Azure musi być zainstalowany w trybie nieudostępnionym. Oznacza to, że każdy węzeł używa woluminów dysków lokalnych dla **/hana/data** i **/hana/log**. Ponieważ węzły używają tylko woluminów dysków lokalnych, nie jest konieczne zdefiniowanie oddzielnej podsieci do przechowywania:

- 10.0.2.0/24 dla komunikacji międzyodowej SAP HANA
- 10.0.1.0/24 dla replikacji systemu SAP HANA (HSR)
- 10.0.0.0/24 dla wszystkiego innego

Aby uzyskać informacje na temat konfiguracji SAP HANA związanej z korzystaniem z wielu sieci, zobacz [SAP HANA global.ini](#sap-hana-globalini).

Każda maszyna wirtualna w klastrze ma trzy vNVC, które odpowiadają liczbie podsieci. [Jak utworzyć maszynę wirtualną systemu Linux na platformie Azure z wieloma kartami interfejsu sieciowego][azure-linux-multiple-nics] opisuje potencjalny problem routingu na platformie Azure podczas wdrażania maszyny wirtualnej z systemem Linux. Ten konkretny artykuł routingu ma zastosowanie tylko do korzystania z wielu vNICs. Problem został rozwiązany przez SUSE na domyślnie w SLES 12 SP3. Aby uzyskać więcej informacji, zobacz [Wiele kart sieciowych z siecią chmurową w EC2 i na platformie Azure][suse-cloud-netconfig].


Aby sprawdzić, czy sap HANA jest poprawnie skonfigurowany do korzystania z wielu sieci, uruchom następujące polecenia. Najpierw sprawdź na poziomie systemu operacyjnego, czy wszystkie trzy wewnętrzne adresy IP dla wszystkich trzech podsieci są aktywne. Jeśli zdefiniowano podsieci z różnymi zakresami adresów IP, należy dostosować polecenia:

<pre><code>
ifconfig | grep "inet addr:10\."
</code></pre>

Poniższe dane wyjściowe próbki pochodzi z drugiego węzła procesu roboczego w lokacji 2. Możesz zobaczyć trzy różne wewnętrzne adresy IP z eth0, eth1 i eth2:

<pre><code>
inet addr:10.0.0.42  Bcast:10.0.0.255  Mask:255.255.255.0
inet addr:10.0.1.42  Bcast:10.0.1.255  Mask:255.255.255.0
inet addr:10.0.2.42  Bcast:10.0.2.255  Mask:255.255.255.0
</code></pre>


Następnie sprawdź porty SAP HANA dla serwera nazw i HSR. SAP HANA powinien nasłuchiwają w odpowiednich podsieciach. W zależności od numeru wystąpienia SAP HANA należy dostosować polecenia. W przypadku systemu testowego numer wystąpienia wynosił **00**. Istnieją różne sposoby, aby dowiedzieć się, które porty są używane. 

Następująca instrukcja SQL zwraca identyfikator wystąpienia, numer wystąpienia i inne informacje:

<pre><code>
select * from "SYS"."M_SYSTEM_OVERVIEW"
</code></pre>

Aby znaleźć poprawne numery portów, można wyszukać, na przykład, w HANA Studio w obszarze **Konfiguracja** lub za pomocą instrukcji SQL:

<pre><code>
select * from M_INIFILE_CONTENTS WHERE KEY LIKE 'listen%'
</code></pre>

Aby znaleźć każdy port używany w stosie oprogramowania SAP, w tym SAP HANA, wyszukaj [porty TCP/IP wszystkich produktów SAP.][sap-list-port-numbers]

Biorąc pod uwagę numer wystąpienia **00** w systemie testowym SAP HANA 2.0, numer portu serwera nazw wynosi **30001**. Numer portu dla komunikacji metadanych HSR to **40002**. Jedną z opcji jest zalogowanie się do węzła procesu roboczego, a następnie sprawdzenie usług węzła głównego. W tym artykule sprawdziliśmy węzeł roboczy 2 na stronie 2, próbując połączyć się z węzłem głównym w lokacji 2.

Sprawdź port serwera nazw:

<pre><code>
nc -vz 10.0.0.40 30001
nc -vz 10.0.1.40 30001
nc -vz 10.0.2.40 30001
</code></pre>

Aby udowodnić, że komunikacja między węzłami używa podsieci **10.0.2.0/24,** wynik powinien wyglądać następująco.
Tylko połączenie za pośrednictwem podsieci **10.0.2.0/24** powinno zakończyć się pomyślnie:

<pre><code>
nc: connect to 10.0.0.40 port 30001 (tcp) failed: Connection refused
nc: connect to 10.0.1.40 port 30001 (tcp) failed: Connection refused
Connection to 10.0.2.40 30001 port [tcp/pago-services1] succeeded!
</code></pre>

Teraz sprawdź, czy port HSR **40002**:

<pre><code>
nc -vz 10.0.0.40 40002
nc -vz 10.0.1.40 40002
nc -vz 10.0.2.40 40002
</code></pre>

Aby udowodnić, że komunikacja HSR używa podsieci **10.0.1.0/24,** wynik powinien wyglądać następująco.
Tylko połączenie za pośrednictwem podsieci **10.0.1.0/24** powinno zakończyć się pomyślnie:

<pre><code>
nc: connect to 10.0.0.40 port 40002 (tcp) failed: Connection refused
Connection to 10.0.1.40 40002 port [tcp/*] succeeded!
nc: connect to 10.0.2.40 port 40002 (tcp) failed: Connection refused
</code></pre>



## <a name="corosync"></a>Corosync ( corosync )


Plik konfiguracyjny **corosync** musi być poprawny w każdym węźle w klastrze, w tym w węźle producenta większościowego. Jeśli sprzężenie klastra węzła nie działa zgodnie z oczekiwaniami, utwórz lub skopiuj **/etc/corosync/corosync.conf** ręcznie do wszystkich węzłów i uruchom ponownie usługę. 

Przykładem jest zawartość **corosync.conf** z systemu testowego.

Pierwsza sekcja to **totem**, jak opisano w [instalacji klastra](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#cluster-installation), krok 11. Można zignorować wartość **mcastaddr**. Wystarczy zachować istniejący wpis. Wpisy **tokenu** i **konsensusu** muszą być ustawione zgodnie z [dokumentacją systemu Microsoft Azure SAP HANA][sles-pacemaker-ha-guide].

<pre><code>
totem {
    version:    2
    secauth:    on
    crypto_hash:    sha1
    crypto_cipher:  aes256
    cluster_name:   hacluster
    clear_node_high_bit: yes

    token:      30000
    token_retransmits_before_loss_const: 10
    join:       60
    consensus:  36000
    max_messages:   20

    interface {
        ringnumber:     0
        bindnetaddr: 10.0.0.0
        mcastaddr:  239.170.19.232
        mcastport:  5405

        ttl:        1
    }
    transport:      udpu

}
</code></pre>

Druga sekcja, **rejestrowanie,** nie została zmieniona z podanych ustawień domyślnych:

<pre><code>
logging {
    fileline:   off
    to_stderr:  no
    to_logfile:     no
    logfile:    /var/log/cluster/corosync.log
    to_syslog:  yes
    debug:      off
    timestamp:  on
    logger_subsys {
        subsys:     QUORUM
        debug:  off
    }
}
</code></pre>

Trzecia sekcja pokazuje **listę węzłów**. Wszystkie węzły klastra muszą być wyświetlane z **ich nodeid:**

<pre><code>
nodelist {
  node {
   ring0_addr:hso-hana-vm-s1-0
   nodeid: 1
   }
  node {
   ring0_addr:hso-hana-vm-s1-1
   nodeid: 2
   }
  node {
   ring0_addr:hso-hana-vm-s1-2
   nodeid: 3
   }
  node {
   ring0_addr:hso-hana-vm-s2-0
   nodeid: 4
   }
  node {
   ring0_addr:hso-hana-vm-s2-1
   nodeid: 5
   }
  node {
   ring0_addr:hso-hana-vm-s2-2
   nodeid: 6
   }
  node {
   ring0_addr:hso-hana-dm
   nodeid: 7
   }
}
</code></pre>

W ostatniej sekcji **kworum**, ważne jest, aby ustawić wartość **dla expected_votes** poprawnie. Musi to być liczba węzłów, w tym węzeł twórcy większości. A wartość **dla two_node** musi wynosić **0**. Nie usuwaj wpisu całkowicie. Wystarczy ustawić wartość na **0**.

<pre><code>
quorum {
    # Enable and configure quorum subsystem (default: off)
    # see also corosync.conf.5 and votequorum.5
    provider: corosync_votequorum
    expected_votes: 7
    two_node: 0
}
</code></pre>


Uruchom ponownie usługę za pośrednictwem **systemctl:**

<pre><code>
systemctl restart corosync
</code></pre>




## <a name="sbd-device"></a>Urządzenie SBD

Jak skonfigurować urządzenie SBD na maszynie Wirtualnej platformy Azure jest opisane w [SBD ogrodzenia](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#sbd-fencing).

Najpierw sprawdź na maszynie wirtualnej serwera SBD, czy istnieją wpisy listy ACL dla każdego węzła w klastrze. Uruchom następujące polecenie na maszynie wirtualnej serwera SBD:


<pre><code>
targetcli ls
</code></pre>


W systemie testowym dane wyjściowe polecenia wyglądają jak następująca próbka. Nazwy listy ACL, takie jak **iqn.2006-04.hso-db-0.local:hso-db-0,** należy wprowadzić jako odpowiednie nazwy inicjatora na maszynach wirtualnych. Każda maszyna wirtualna potrzebuje innej maszyny Wirtualnej.

<pre><code>
 | | o- sbddbhso ................................................................... [/sbd/sbddbhso (50.0MiB) write-thru activated]
  | |   o- alua ................................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ....................................................................... [ALUA state: Active/optimized]
  | o- pscsi .................................................................................................. [Storage Objects: 0]
  | o- ramdisk ................................................................................................ [Storage Objects: 0]
  o- iscsi ............................................................................................................ [Targets: 1]
  | o- iqn.2006-04.dbhso.local:dbhso ..................................................................................... [TPGs: 1]
  |   o- tpg1 ............................................................................................... [no-gen-acls, no-auth]
  |     o- acls .......................................................................................................... [ACLs: 7]
  |     | o- iqn.2006-04.hso-db-0.local:hso-db-0 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-1.local:hso-db-1 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-2.local:hso-db-2 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-3.local:hso-db-3 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-4.local:hso-db-4 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-5.local:hso-db-5 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-6.local:hso-db-6 .................................................................. [Mapped LUNs: 1]
</code></pre>

Następnie sprawdź, czy nazwy inicjatora na wszystkich maszynach wirtualnych są różne i odpowiadają wcześniej pokazanym wpisom. W tym przykładzie jest z węzła roboczego 1 w lokacji 1:

<pre><code>
cat /etc/iscsi/initiatorname.iscsi
</code></pre>

Dane wyjściowe wyglądają następująco:

<pre><code>
##
## /etc/iscsi/iscsi.initiatorname
##
## Default iSCSI Initiatorname.
##
## DO NOT EDIT OR REMOVE THIS FILE!
## If you remove this file, the iSCSI daemon will not start.
## If you change the InitiatorName, existing access control lists
## may reject this initiator.  The InitiatorName must be unique
## for each iSCSI initiator.  Do NOT duplicate iSCSI InitiatorNames.
InitiatorName=iqn.2006-04.hso-db-1.local:hso-db-1
</code></pre>

Następnie sprawdź, czy **odnajdowanie** działa poprawnie. Uruchom następujące polecenie w każdym węźle klastra przy użyciu adresu IP maszyny wirtualnej serwera SBD:

<pre><code>
iscsiadm -m discovery --type=st --portal=10.0.0.19:3260
</code></pre>

Dane wyjściowe powinny wyglądać następująco:

<pre><code>
10.0.0.19:3260,1 iqn.2006-04.dbhso.local:dbhso
</code></pre>

Następnym punktem dowodowym jest sprawdzenie, czy węzeł widzi urządzenie SDB. Sprawdź to na każdym węźle, w tym w węźle większościowego producenta:

<pre><code>
lsscsi | grep dbhso
</code></pre>

Dane wyjściowe powinny wyglądać następująco. Jednak nazwy mogą się różnić. Nazwa urządzenia może również ulec zmianie po ponownym uruchomieniu maszyny Wirtualnej:

<pre><code>
[6:0:0:0]    disk    LIO-ORG  sbddbhso         4.0   /dev/sdm
</code></pre>

W zależności od stanu systemu czasami pomaga ponownie uruchomić usługi iSCSI w celu rozwiązania problemów. Następnie uruchom następujące polecenia:

<pre><code>
systemctl restart iscsi
systemctl restart iscsid
</code></pre>


Z dowolnego węzła można sprawdzić, czy wszystkie węzły są **jasne**. Upewnij się, że używasz poprawnej nazwy urządzenia w określonym węźle:

<pre><code>
sbd -d /dev/sdm list
</code></pre>

Dane wyjściowe powinny być **jasne** dla każdego węzła w klastrze:

<pre><code>
0       hso-hana-vm-s1-0        clear
1       hso-hana-vm-s2-2        clear
2       hso-hana-vm-s2-1        clear
3       hso-hana-dm     clear
4       hso-hana-vm-s1-1        clear
5       hso-hana-vm-s2-0        clear
6       hso-hana-vm-s1-2        clear
</code></pre>


Innym sprawdzenieM SBD jest opcja **zrzutu** polecenia **sbd.** W tym przykładowym poleceniu i wyjściu z węzła większościowego producenta nazwa urządzenia została **sdd**, a nie **sdm:**

<pre><code>
sbd -d /dev/sdd dump
</code></pre>

Dane wyjściowe, oprócz nazwy urządzenia, powinny wyglądać tak samo we wszystkich węzłach:

<pre><code>
==Dumping header on disk /dev/sdd
Header version     : 2.1
UUID               : 9fa6cc49-c294-4f1e-9527-c973f4d5a5b0
Number of slots    : 255
Sector size        : 512
Timeout (watchdog) : 60
Timeout (allocate) : 2
Timeout (loop)     : 1
Timeout (msgwait)  : 120
==Header on disk /dev/sdd is dumped
</code></pre>

Jeszcze jedno sprawdzenie dla SBD jest możliwość wysłania wiadomości do innego węzła. Aby wysłać wiadomość do węzła roboczego 2 w lokacji 2, uruchom następujące polecenie w węźle roboczym 1 w lokacji 2:

<pre><code>
sbd -d /dev/sdm message hso-hana-vm-s2-2 test
</code></pre>

Po stronie docelowej maszyny Wirtualnej **hso-hana-vm-s2-2** w tym przykładzie można znaleźć następujący wpis w **/var/log/messages:**

<pre><code>
/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68:   notice: servant: Received command test from hso-hana-vm-s2-1 on disk /dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68
</code></pre>

Sprawdź, czy wpisy w **/etc/sysconfig/sbd** odpowiadają opisowi w [pozycji Konfigurowanie rozrusznika serca na suse linux enterprise server na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#sbd-fencing). Sprawdź, czy ustawienie uruchamiania w **/etc/iscsi/iscsid.conf** jest ustawione na automatyczne.

Poniższe wpisy są ważne w **/etc/sysconfig/sbd**. W razie potrzeby dostosuj wartość **doodmową:**

<pre><code>
SBD_DEVICE="/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68;"
SBD_PACEMAKER=yes
SBD_STARTMODE=always
SBD_WATCHDOG=yes
</code></pre>


Sprawdź ustawienie uruchamiania w **/etc/iscsi/iscsid.conf**. Wymagane ustawienie powinno mieć miejsce za pomocą następującego polecenia **iscsiadm,** opisanego w dokumentacji. Sprawdź i dostosuj go ręcznie za pomocą **vi,** jeśli jest inny.

To polecenie ustawia zachowanie uruchamiania:

<pre><code>
iscsiadm -m node --op=update --name=node.startup --value=automatic
</code></pre>

Dokonaj tego wpisu w **/etc/iscsi/iscsid.conf**:

<pre><code>
node.startup = automatic
</code></pre>

Podczas testowania i weryfikacji, po ponownym uruchomieniu maszyny Wirtualnej, urządzenie SBD nie było już widoczne w niektórych przypadkach. Wystąpiła rozbieżność między ustawieniem uruchamiania a tym, co pokazał YaST2. Aby sprawdzić ustawienia, wykonaj następujące czynności:

1. Uruchom YaST2.
2. Wybierz **pozycję Usługi sieciowe** po lewej stronie.
3. Przewiń w dół po prawej stronie do **inicjatora iSCSI** i wybierz go.
4. Na następnym ekranie na karcie **Usługa** zostanie wyświetlena unikatowa nazwa inicjatora węzła.
5. Nad nazwą inicjatora upewnij się, że wartość **Start usługi** jest **ustawiona na Podczas uruchamiania**.
6. Jeśli tak nie jest, a następnie ustawić go **podczas uruchamiania** zamiast ręcznie **.**
7. Następnie przełącz górną kartę na **Połączone cele**.
8. Na ekranie **Połączone cele** powinien zostać wyświetlony wpis dla urządzenia SBD, taki jak ten przykład: **10.0.0.19:3260 iqn.2006-04.dbhso.local:dbhso**.
9. Sprawdź, czy wartość **startowa** jest ustawiona **na przy starcie**.
10. Jeśli nie, wybierz pozycję **Edytuj** i zmień ją.
11. Zapisz zmiany i zamknij YaST2.



## <a name="pacemaker"></a>Stymulatora

Po prawidłowym skonfigurowaniu wszystkiego można uruchomić następujące polecenie w każdym węźle, aby sprawdzić stan usługi Rozrusznik:

<pre><code>
systemctl status pacemaker
</code></pre>

Górna część danych wyjściowych powinna wyglądać następująco. Ważne jest, aby stan po **active** był wyświetlany jako **załadowany** i **aktywny (uruchomiony)**. Stan po **załadowaniu** musi być wyświetlany jako **włączony**.

<pre><code>
  pacemaker.service - Pacemaker High Availability Cluster Manager
   Loaded: loaded (/usr/lib/systemd/system/pacemaker.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2018-09-07 05:56:27 UTC; 4 days ago
     Docs: man:pacemakerd
           http://clusterlabs.org/doc/en-US/Pacemaker/1.1-pcs/html/Pacemaker_Explained/index.html
 Main PID: 4496 (pacemakerd)
    Tasks: 7 (limit: 4915)
   CGroup: /system.slice/pacemaker.service
           ├─4496 /usr/sbin/pacemakerd -f
           ├─4499 /usr/lib/pacemaker/cib
           ├─4500 /usr/lib/pacemaker/stonithd
           ├─4501 /usr/lib/pacemaker/lrmd
           ├─4502 /usr/lib/pacemaker/attrd
           ├─4503 /usr/lib/pacemaker/pengine
           └─4504 /usr/lib/pacemaker/crmd
</code></pre>

Jeśli ustawienie jest nadal **wyłączone,** uruchom następujące polecenie:

<pre><code>
systemctl enable pacemaker
</code></pre>

Aby wyświetlić wszystkie skonfigurowane zasoby w rozruszniku serca, uruchom następujące polecenie:

<pre><code>
crm status
</code></pre>

Dane wyjściowe powinny wyglądać następująco. Dobrze, że zasoby **cln** i **msl** są wyświetlane jako zatrzymane na większościowej maszynie VM, **hso-hana-dm**. Nie ma instalacji SAP HANA w węźle producenta większościowego. Więc **cln** i **msl** zasoby są wyświetlane jako zatrzymane. Ważne jest, aby wyświetlała prawidłową całkowitą liczbę maszyn wirtualnych, **7**. Wszystkie maszyny wirtualne, które są częścią klastra, muszą być wymienione ze stanem **Online**. Bieżący podstawowy węzeł główny musi być poprawnie rozpoznany. W tym przykładzie jest **hso-hana-vm-s1-0**:

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Tue Sep 11 15:56:40 2018
Last change: Tue Sep 11 15:56:23 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0
</code></pre>

Ważną cechą rozrusznika jest tryb konserwacji. W tym trybie można wprowadzać modyfikacje bez wywoływania natychmiastowej akcji klastra. Przykładem jest ponowne uruchomienie maszyny Wirtualnej. Typowym przypadkiem użycia będzie planowana konserwacja systemu operacyjnego lub infrastruktury platformy Azure. Zobacz [Planowana konserwacja](#planned-maintenance). Użyj następującego polecenia, aby włączyć rozrusznik serca w tryb konserwacji:

<pre><code>
crm configure property maintenance-mode=true
</code></pre>

Podczas sprawdzania **stanu crm,** można zauważyć w danych wyjściowych, że wszystkie zasoby są oznaczone jako **niezarządzane**. W tym stanie klastra nie reaguje na wszelkie zmiany, takie jak uruchamianie lub zatrzymywanie SAP HANA.
W poniższym przykładzie przedstawiono dane wyjściowe polecenia **stanu crm,** gdy klaster jest w trybie konserwacji:

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Wed Sep 12 07:48:10 2018
Last change: Wed Sep 12 07:46:54 2018 by root via cibadmin on hso-hana-vm-s2-1

7 nodes configured
17 resources configured

              *** Resource management is DISABLED ***
  The cluster will not attempt to start, stop or recover services

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm (unmanaged)
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00] (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-0 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00] (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Master hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm hso-hana-vm-s1-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s2-0 (unmanaged)
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s2-0 (unmanaged)
</code></pre>


W tym przykładzie polecenia pokazano, jak zakończyć tryb konserwacji klastra:

<pre><code>
crm configure property maintenance-mode=false
</code></pre>


Inne polecenie **crm** pobiera pełną konfigurację klastra do edytora, dzięki czemu można ją edytować. Po zapisaniu zmian klaster uruchamia odpowiednie akcje:

<pre><code>
crm configure edit
</code></pre>

Aby przyjrzeć się pełnej konfiguracji klastra, użyj opcji **crm show:**

<pre><code>
crm configure show
</code></pre>



Po awarii zasobów klastra polecenie **stanu crm** pokazuje listę **akcji nieudanych**. Zobacz następującą próbkę tego wyjścia:


<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Thu Sep 13 07:30:44 2018
Last change: Thu Sep 13 07:30:20 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm hso-hana-vm-s2-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0

Failed Actions:
* rsc_SAPHanaCon_HSO_HDB00_monitor_60000 on hso-hana-vm-s2-0 'unknown error' (1): call=86, status=complete, exitreason='none',
    last-rc-change='Wed Sep 12 17:01:28 2018', queued=0ms, exec=277663ms
</code></pre>

Jest to konieczne, aby wykonać oczyszczanie klastra po awarii. Ponownie użyj polecenia **crm** i użyj opcji polecenia **oczyszczania,** aby pozbyć się tych nieudanych wpisów akcji. Nazwij odpowiedni zasób klastra w następujący sposób:

<pre><code>
crm resource cleanup rsc_SAPHanaCon_HSO_HDB00
</code></pre>

Polecenie powinno zwracać dane wyjściowe, takie jak następująca próbka:

<pre><code>
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-dm
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-2
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-2
Waiting for 7 replies from the CRMd....... OK
</code></pre>



## <a name="failover-or-takeover"></a>Awaryjne lub przejęcie

Jak wspomniano w [ważne uwagi,](#important-notes)nie należy używać standardowego wdzięku zamknięcia do testowania klastra pracy awaryjnej lub przejęcia SAP HANA HSR. Zamiast tego zaleca się wyzwolenie błędu jądra, wymuszenie migracji zasobów lub ewentualnie zamknięcie wszystkich sieci na poziomie systemu operacyjnego maszyny Wirtualnej. Inną metodą jest polecenie **czuwania węzła \<\> crm.** Zobacz [dokument SUSE][sles-12-ha-paper]. 

Następujące trzy przykładowe polecenia mogą wymusić przemiennie pracy awaryjnej klastra:

<pre><code>
echo c &gt /proc/sysrq-trigger

crm resource migrate msl_SAPHanaCon_HSO_HDB00 hso-hana-vm-s2-0 force

wicked ifdown eth0
wicked ifdown eth1
wicked ifdown eth2
......
wicked ifdown eth&ltn&gt
</code></pre>

Zgodnie z opisem w [planowanej konserwacji,](#planned-maintenance)dobrym sposobem monitorowania działań klastra jest uruchomienie **SAPHanaSR-showAttr** za pomocą polecenia **watch:**

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

Pomaga również przyjrzeć się statusowi krajobrazu SAP HANA pochodzącego ze skryptu SAP Python. Instalator klastra szuka tej wartości stanu. Staje się jasne, gdy myślisz o awarii węzła procesu roboczego. Jeśli węzeł procesu roboczego ulegnie ulegnie upadkowi, sap HANA nie zwraca natychmiast błąd kondycji całego systemu skalowania w poziomie. 

Istnieją pewne ponownych prób, aby uniknąć niepotrzebnych pracy awaryjnej. Klaster reaguje tylko wtedy, gdy stan zmieni się z **Ok**, zwraca wartość **4**, do **błędu**, zwraca wartość **1**. Więc to prawda, jeśli dane wyjściowe z **SAPHanaSR-showAttr** pokazuje maszynę wirtualną ze stanem **w trybie offline**. Ale nie ma jeszcze żadnej aktywności, aby przełączyć podstawowe i pomocnicze. Żadne działanie klastra nie zostanie wyzwolone, o ile sap HANA nie zwraca błędu.

Stan stanu stanu stanu krajobrazu SAP HANA można monitorować jako identyfikator ** \<SID\>użytkownika HANA,** wywołując skrypt SAP Python w następujący sposób. Może być trzeba dostosować ścieżkę:

<pre><code>
watch python /hana/shared/HSO/exe/linuxx86_64/HDB_2.00.032.00.1533114046_eeaf4723ec52ed3935ae0dc9769c9411ed73fec5/python_support/landscapeHostConfiguration.py
</code></pre>

Dane wyjściowe tego polecenia powinny wyglądać następująco. Kolumna **Stan hosta** i **ogólny stan hosta** są ważne. Rzeczywiste dane wyjściowe są szersze, z dodatkowymi kolumnami.
Aby tabela danych wyjściowych była bardziej czytelna w tym dokumencie, większość kolumn po prawej stronie została usunięta:

<pre><code>
| Host             | Host   | Host   | Failover | Remove | 
|                  | Active | Status | Status   | Status | 
|                  |        |        |          |        | 
| ---------------- | ------ | ------ | -------- | ------ |    .......
| hso-hana-vm-s2-0 | yes    | ok     |          |        |        
| hso-hana-vm-s2-1 | yes    | ok     |          |        |         
| hso-hana-vm-s2-2 | yes    | ok     |          |        |        

overall host status: ok
</code></pre>


Istnieje inne polecenie, aby sprawdzić bieżące działania klastra. Zobacz następujące polecenie i ogon wyjściowy po zabiciu węzła głównego lokacji głównej. Możesz zobaczyć listę akcji przejścia, takich jak **promowanie** byłego pomocniczego węzła głównego, **hso-hana-vm-s2-0**, jako nowy główny wzorzec. Jeśli wszystko jest w porządku i wszystkie działania są zakończone, ta lista **podsumowania przejścia** musi być pusta.

<pre><code>
 crm_simulate -Ls

...........

Transition Summary:
 * Fence hso-hana-vm-s1-0
 * Stop    rsc_SAPHanaTop_HSO_HDB00:1   (hso-hana-vm-s1-0)
 * Demote  rsc_SAPHanaCon_HSO_HDB00:1   (Master -> Stopped hso-hana-vm-s1-0)
 * Promote rsc_SAPHanaCon_HSO_HDB00:5   (Slave -> Master hso-hana-vm-s2-0)
 * Move    rsc_ip_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
 * Move    rsc_nc_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
</code></pre>



## <a name="planned-maintenance"></a>Planowana konserwacja 

Istnieją różne przypadki użycia, jeśli chodzi o planowaną konserwację. Jedno pytanie brzmi, czy to tylko konserwacja infrastruktury, takich jak zmiany na poziomie systemu operacyjnego i konfiguracji dysku lub uaktualnienia HANA.
Dodatkowe informacje można znaleźć w dokumentach z SUSE, takich jak [Towards Zero Przestoje][sles-zero-downtime-paper] lub [SAP HANA SR Performance Optimized Scenario][sles-12-for-sap]. Dokumenty te zawierają również przykłady, które pokazują, jak ręcznie migrować podstawowy.

Przeprowadzono intensywne testy wewnętrzne w celu sprawdzenia przypadku użycia konserwacji infrastruktury. Aby uniknąć problemów związanych z migracją podstawowego, zdecydowaliśmy się zawsze migrować podstawowy przed wprowadzeniem klastra w tryb konserwacji. W ten sposób nie jest konieczne, aby klaster zapomniał o dawnej sytuacji: która strona była podstawowa, a która drugorzędna.

Istnieją dwie różne sytuacje w tym względzie:

- **Planowana konserwacja bieżącego wtórnego**. W takim przypadku można po prostu umieścić klaster w trybie konserwacji i wykonać pracę na pomocniczym bez wpływu na klaster.

- **Planowana konserwacja bieżącej podstawowej**. Aby użytkownicy mogli kontynuować pracę podczas konserwacji, należy wymusić pracę awaryjną. Dzięki takiemu podejściu należy wyzwolić klaster pracy awaryjnej przez rozrusznik serca, a nie tylko na poziomie SAP HANA HSR. Konfiguracja rozrusznika automatycznie wyzwala przejęcie SAP HANA. Należy również wykonać pracy awaryjnej przed umieszczeniem klastra w trybie konserwacji.

Procedura konserwacji w bieżącym miejscu wtórnym jest następująca:

1. Przełoż klaster w tryb konserwacji.
2. Wykonanie pracy w lokacji dodatkowej. 
3. Zakończ tryb konserwacji klastra.

Procedura konserwacji w bieżącej lokacji głównej jest bardziej złożona:

1. Ręcznie wyzwolić pracy awaryjnej lub sap HANA przejęcia za pośrednictwem migracji zasobów rozrusznika. Zobacz szczegóły, które następują.
2. Sap HANA w byłej lokacji głównej zostanie zamknięty przez konfigurację klastra.
3. Przełoż klaster w tryb konserwacji.
4. Po zakończeniu prac konserwacyjnych zarejestruj poprzednią podstawową jako nową lokację dodatkową.
5. Wyczyść konfigurację klastra. Zobacz szczegóły, które następują.
6. Zakończ tryb konserwacji klastra.


Migrowanie zasobu powoduje dodanie wpisu do konfiguracji klastra. Przykładem jest wymuszanie pracy awaryjnej. Należy oczyścić te wpisy przed zakończeniem trybu konserwacji. Zobacz poniższą próbkę.

Najpierw wymuś przerórzenie w stan failover klastra przez migrację zasobu **msl** do bieżącego pomocniczego węzła głównego. To polecenie daje ostrzeżenie, że zostało utworzone **ograniczenie przenoszenia:**

<pre><code>
crm resource migrate msl_SAPHanaCon_HSO_HDB00 force

INFO: Move constraint created for msl_SAPHanaCon_HSO_HDB00
</code></pre>


Sprawdź proces pracy awaryjnej za pomocą polecenia **SAPHanaSR-showAttr**. Aby monitorować stan klastra, otwórz dedykowane okno powłoki i uruchom polecenie za pomocą **zegarka:**

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

Dane wyjściowe powinny być wyświetlane ręczne pracy awaryjnej. Były pomocniczy węzeł główny został **awansowany**, w tej próbce, **hso-hana-vm-s2-0**. Dawna lokacja główna została zatrzymana, wartość **lss** **1** dla byłego głównego węzła głównego **hso-hana-vm-s1-0**: 

<pre><code>
Global cib-time                 prim  sec srHook sync_state
------------------------------------------------------------
global Wed Sep 12 07:40:02 2018 HSOS2 -   SFAIL  SFAIL


Sites lpt        lss mns              srr
------------------------------------------
HSOS1 10         1   hso-hana-vm-s1-0 P
HSOS2 1536738002 4   hso-hana-vm-s2-0 P


Hosts            clone_state node_state roles                        score  site
----------------------------------------------------------------------------------
hso-hana-dm                  online
hso-hana-vm-s1-0 UNDEFINED   online     master1::worker:             150    HSOS1
hso-hana-vm-s1-1 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s1-2 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s2-0 PROMOTED    online     master1:master:worker:master 150    HSOS2
hso-hana-vm-s2-1 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
hso-hana-vm-s2-2 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
</code></pre>

Po przemijce awaryjnej klastra i przejęciu sap HANA, przełóż klaster w tryb konserwacji, jak opisano w [pacemaker](#pacemaker).

Polecenia **SAPHanaSR-showAttr** i **stan crm** nie wskazują nic na temat ograniczeń utworzonych przez migrację zasobów. Jedną z opcji uwidocznienia tych ograniczeń jest pokazanie pełnej konfiguracji zasobu klastra za pomocą następującego polecenia:

<pre><code>
crm configure show
</code></pre>

W konfiguracji klastra można znaleźć nowe ograniczenie lokalizacji spowodowane przez poprzednią ręczną migrację zasobów. Ten przykładowy wpis zaczyna się od **cli lokalizacji-**:

<pre><code>
location cli-ban-msl_SAPHanaCon_HSO_HDB00-on-hso-hana-vm-s1-0 msl_SAPHanaCon_HSO_HDB00 role=Started -inf: hso-hana-vm-s1-0
</code></pre>

Niestety takie ograniczenia mogą mieć wpływ na ogólne zachowanie klastra. Dlatego obowiązkowe jest ich ponowne usunięcie przed przywróceniem całego systemu. Za pomocą polecenia **unmigrate** można wyczyścić ograniczenia lokalizacji, które zostały utworzone wcześniej. Nazewnictwo może być nieco mylące. Nie próbuje przeprowadzić migracji zasobu z powrotem do oryginalnej maszyny Wirtualnej, z której został zmigrowany. Po prostu usuwa ograniczenia lokalizacji, a także zwraca odpowiednie informacje po uruchomieniu polecenia:


<pre><code>
crm resource unmigrate msl_SAPHanaCon_HSO_HDB00

INFO: Removed migration constraints for msl_SAPHanaCon_HSO_HDB00
</code></pre>

Po zakończeniu prac konserwacyjnych należy zatrzymać tryb konserwacji klastra, jak pokazano w [części Rozrusznik](#pacemaker).



## <a name="hb_report-to-collect-log-files"></a>hb_report do zbierania plików dziennika

Aby przeanalizować problemy z klastrem rozrusznika, jest to pomocne, a także wymagane przez obsługę SUSE, aby uruchomić **narzędzie hb_report.** Zbiera wszystkie ważne pliki dziennika, które są potrzebne do analizy, co się stało. To przykładowe wywołanie używa czasu rozpoczęcia i zakończenia, w którym wystąpiło określone zdarzenie. Zobacz także [Ważne uwagi:](#important-notes)

<pre><code>
hb_report -f "2018/09/13 07:36" -t "2018/09/13 08:00" /tmp/hb_report_log
</code></pre>

Polecenie informuje, gdzie umieścić skompresowane pliki dziennika:

<pre><code>
The report is saved in /tmp/hb_report_log.tar.bz2
Report timespan: 09/13/18 07:36:00 - 09/13/18 08:00:00
</code></pre>

Następnie można wyodrębnić poszczególne pliki za pomocą standardowego polecenia **tar:**

<pre><code>
tar -xvf hb_report_log.tar.bz2
</code></pre>

Gdy spojrzysz na wyodrębnione pliki, znajdziesz wszystkie pliki dziennika. Większość z nich została umieszczona w oddzielnych katalogach dla każdego węzła w klastrze:

<pre><code>
-rw-r--r-- 1 root root  13655 Sep 13 09:01 analysis.txt
-rw-r--r-- 1 root root  14422 Sep 13 09:01 description.txt
-rw-r--r-- 1 root root      0 Sep 13 09:01 events.txt
-rw-r--r-- 1 root root 275560 Sep 13 09:00 ha-log.txt
-rw-r--r-- 1 root root     26 Sep 13 09:00 ha-log.txt.info
drwxr-xr-x 4 root root   4096 Sep 13 09:01 hso-hana-dm
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-2
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-2
-rw-r--r-- 1 root root 264726 Sep 13 09:00 journal.log
</code></pre>


W określonym zakresie czasowym został zabity bieżący węzeł główny **hso-hana-vm-s1-0.** Wpisy związane z tym zdarzeniem można znaleźć w **pliku journal.log:**

<pre><code>
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: (to hsoadm) root on none
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session opened for user hsoadm by (uid=0)
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 systemd[1]: Started Session c44290 of user hsoadm.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] A new membership (10.0.0.13:120996) was formed. Members left: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] Failed to receive the leave message. failed: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Removing all hso-hana-vm-s1-0 attributes for peer loss
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [QUORUM] Members[6]: 7 2 3 4 5 6
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [MAIN  ] Completed service synchronization, ready to provide service.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 crmd[28315]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 pacemakerd[28308]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:03+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session closed for user hsoadm
</code></pre>

Innym przykładem jest plik dziennika rozrusznika na pomocniczym wzorcu, który stał się nowym wzorcem podstawowym. Ten fragment pokazuje, że stan zabitego węzła głównego został ustawiony na **offline:**

<pre><code>
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 3 still member of group stonith-ng (peer=hso-hana-vm-s1-2, counter=5.1)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 4 still member of group stonith-ng (peer=hso-hana-vm-s2-0, counter=5.2)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 5 still member of group stonith-ng (peer=hso-hana-vm-s2-1, counter=5.3)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 6 still member of group stonith-ng (peer=hso-hana-vm-s2-2, counter=5.4)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 7 still member of group stonith-ng (peer=hso-hana-dm, counter=5.5)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 1 left group crmd (peer=hso-hana-vm-s1-0, counter=5.0)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: crm_update_peer_proc:     pcmk_cpg_membership: Node hso-hana-vm-s1-0[1] - corosync-cpg is now offline
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: peer_update_callback:     Client hso-hana-vm-s1-0/peer now has status [offline] (DC=hso-hana-dm, changed=4000000)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 2 still member of group crmd (peer=hso-hana-vm-s1-1, counter=5.0)
</code></pre>





## <a name="sap-hana-globalini"></a>SAP HANA global.ini


Poniższe fragmenty pochodzą z pliku SAP HANA **global.ini** w witrynie klastra 2. W tym przykładzie przedstawiono wpisy rozpoznawania nazwy hosta do korzystania z różnych sieci dla komunikacji międzyodejowej SAP HANA i HSR:

<pre><code>
[communication]
tcp_keepalive_interval = 20
internal_network = 10.0.2/24
listeninterface = .internal
</code></pre>


<pre><code>
[internal_hostname_resolution]
10.0.2.40 = hso-hana-vm-s2-0
10.0.2.42 = hso-hana-vm-s2-2
10.0.2.41 = hso-hana-vm-s2-1
</code></pre>

<pre><code>
[ha_dr_provider_SAPHanaSR]
provider = SAPHanaSR
path = /hana/shared/myHooks
execution_order = 1
</code></pre>

<pre><code>
[system_replication_communication]
listeninterface = .internal

[system_replication_hostname_resolution]
10.0.1.30 = hso-hana-vm-s1-0
10.0.1.31 = hso-hana-vm-s1-1
10.0.1.32 = hso-hana-vm-s1-2
10.0.1.40 = hso-hana-vm-s2-0
10.0.1.41 = hso-hana-vm-s2-1
10.0.1.42 = hso-hana-vm-s2-2
</code></pre>



## <a name="hawk"></a>Hawk

Rozwiązanie klastra zapewnia interfejs przeglądarki, który oferuje gui dla użytkowników, którzy wolą menu i grafiki do posiadania wszystkich poleceń na poziomie powłoki.
Aby użyć interfejsu przeglądarki, zastąp ** \<węzeł\> ** rzeczywistym węzłem SAP HANA w następującym adresie URL. Następnie wprowadź poświadczenia klastra **(klastra**użytkownika):

<pre><code>
https://&ltnode&gt:7630
</code></pre>

Ten zrzut ekranu przedstawia pulpit nawigacyjny klastra:


![Pulpit nawigacyjny klastra Hawk](media/hana-vm-scale-out-HA-troubleshooting/hawk-1.png)


W tym przykładzie przedstawiono ograniczenia lokalizacji spowodowane migracją zasobów klastra, jak wyjaśniono w [sprawie Planowana konserwacja:](#planned-maintenance)


![Ograniczenia listy Jastrzębia](media/hana-vm-scale-out-HA-troubleshooting/hawk-2.png)


Możesz również przesłać **hb_report** wyjście w Obszarze Hawk w obszarze **Historia**, pokazane w następujący sposób. Zobacz hb_report, aby zbierać pliki dziennika: 

![Hawk przesłać hb_report wyjście](media/hana-vm-scale-out-HA-troubleshooting/hawk-3.png)

Za pomocą **Eksploratora historii**można przejść przez wszystkie przejścia klastra zawarte w **hb_report** danych wyjściowych:

![Przejścia jastrzębia w produkcji hb_report](media/hana-vm-scale-out-HA-troubleshooting/hawk-4.png)

Ten końcowy zrzut ekranu przedstawia sekcję **Szczegóły** pojedynczego przejścia. Klaster zareagował na awarię węzła głównego, węzeł **hso-hana-vm-s1-0**. Teraz promuje węzeł pomocniczy jako nowy wzorzec, **hso-hana-vm-s2-0**:

![Jastrzębie pojedyncze przejście](media/hana-vm-scale-out-HA-troubleshooting/hawk-5.png)


## <a name="next-steps"></a>Następne kroki

W tym przewodniku dotyczącym rozwiązywania problemów opisano wysoką dostępność systemu SAP HANA w konfiguracji skalowającej w poziomie. Oprócz bazy danych innym ważnym składnikiem w krajobrazie SAP jest stos SAP NetWeaver. Dowiedz się więcej o [wysokiej dostępności systemu SAP NetWeaver na maszynach wirtualnych platformy Azure korzystających z serwera SUSE Enterprise Linux Server][sap-nw-ha-guide-sles].


---
title: Rozwiązywanie problemów z SAP HANA w wersji 2.0 instalacją program Pacemaker HSR skalowalnego w poziomie z systemem SLES 12 z dodatkiem SP3 na maszynach wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Przewodnik dotyczący sprawdzanie i rozwiązywanie problemów z konfiguracji o wysokiej dostępności złożonych skalowalnego w poziomie oprogramowania SAP HANA na podstawie SAP HANA System replikacji (HSR) i program Pacemaker w systemie SLES 12 z dodatkiem SP3 uruchamianych na maszynach wirtualnych platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermannd
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/24/2018
ms.author: hermannd
ms.openlocfilehash: b794b045efa4be20a63e9996425d69f0212ae0d7
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707237"
---
# <a name="verify-and-troubleshoot-sap-hana-scale-out-high-availability-setup-on-sles-12-sp3"></a>Sprawdź i rozwiązywanie problemów z instalacją w wysokiej dostępności skalowalnego w poziomie oprogramowania SAP HANA w systemie SLES 12 z dodatkiem SP3 

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


Ten artykuł pomoże Ci Sprawdź konfigurację klastra program Pacemaker dla oprogramowania SAP HANA skalowalnego w poziomie uruchamianego na maszynach wirtualnych platformy Azure (maszyny wirtualne). Konfiguracja klastra było wykonywane w połączeniu z replikacji systemu SAP HANA (HSR) i SUSE RPM pakietu SAPHanaSR skalowania. Wszystkie testy zostały wykonane tylko w systemie SUSE SLES 12 z dodatkiem SP3. Sekcje tego artykułu obejmuje różne obszary i obejmują przykładowych poleceniach i fragmenty z plików konfiguracji. Firma Microsoft zaleca te przykłady jako metodę, aby zweryfikować i sprawdzić ustawienia całego klastra.



## <a name="important-notes"></a>Ważne uwagi

Wszystkie testowanie pod kątem oprogramowania SAP HANA skalowalnego w poziomie w połączeniu z replikacji systemu SAP HANA i program Pacemaker zostało wykonane z SAP HANA w wersji 2.0 tylko. Wersja systemu operacyjnego została systemu SUSE Linux Enterprise Server 12 z dodatkiem SP3 dla aplikacji SAP. Najnowszy pakiet RPM ze skalowaniem SAPHanaSR od firmy SUSE, został użyty do skonfigurowania klastra program Pacemaker.
SUSE opublikowane [szczegółowy opis tej konfiguracji pod kątem wydajności][sles-hana-scale-out-ha-paper].

Dla typów maszyn wirtualnych, które są obsługiwane w przypadku oprogramowania SAP HANA skalowalnego w poziomie, sprawdź [katalogu IaaS z certyfikatem SAP HANA][sap-hana-iaas-list].

Wystąpił problem techniczny dotyczący oprogramowania SAP HANA skalowalnego w poziomie w połączeniu z wieloma podsieciami i kart sieciowych i Konfigurowanie replikacji systemu HANA. Jest to konieczne, aby używać najnowszych poprawek oprogramowanie SAP HANA w wersji 2.0, w którym ten problem został rozwiązany. Obsługiwane są następujące wersje oprogramowania SAP HANA: 

* rev2.00.024.04 lub nowszej 
* rev2.00.032 lub nowszej

Jeśli potrzebujesz pomocy technicznej od firmy SUSE, postępuj zgodnie z tym [przewodnik][suse-pacemaker-support-log-files]. Zebranie wszystkich informacji o klastrze platformy SAP HANA wysokiej dostępności (HA), zgodnie z opisem w artykule. Obsługa SUSE wymaga tych informacji do dalszej analizy.

Podczas testowania wewnętrznego Konfiguracja klastra stało się zastanawia normalne łagodne zamykanie maszyny Wirtualnej w witrynie Azure portal. Dlatego zaleca się przetestowanie klastra pracy awaryjnej przy użyciu innych metod. Użyj metod, takich jak wymuszanie jądra, lub zamknij sieci lub Migrowanie **msl** zasobów. Zobacz szczegółowe informacje w poniższych sekcjach. Zakłada się, czy standardowa zamknięcia się dzieje z zamiarem. Najlepszym przykładem zamierzone zamknięcie jest z powodu konserwacji. Szczegółowe informacje w [planowanej konserwacji](#planned-maintenance).

Ponadto podczas testowania wewnętrznego Konfiguracja klastra stało się mylić po ręcznej przejęcia platformy SAP HANA podczas klastra w trybie konserwacji. Zaleca się, czy możesz przełączenia go ponownie ręcznie zakończyć tryb konserwacji klastra. Innym rozwiązaniem jest wyzwalania trybu failover przed umieszczeniem klastra w tryb konserwacji. Aby uzyskać więcej informacji, zobacz [planowanej konserwacji](#planned-maintenance). Dokumentacja od firmy SUSE w tym artykule opisano sposób klastra w ten sposób można zresetować za pomocą **crm** polecenia. Ale podejście, o których wspomniano wcześniej był niezawodny podczas testowania wewnętrznego i nigdy nie wykazało, że wszelkie nieoczekiwane działania niepożądane.

Kiedy używasz **migracji crm** polecenia, upewnij się wyczyścić konfiguracji klastra. Dodaje ograniczenia lokalizacji, które mogą nie być świadomy. Te ograniczenia wpływu na działanie klastra. Zobacz więcej szczegółów w [planowanej konserwacji](#planned-maintenance).



## <a name="test-system-description"></a>Opis systemu testowego

 Weryfikacja wysokiej dostępności skalowalnego w poziomie oprogramowania SAP HANA i certyfikacji Konfiguracja był używany. Składa się z dwóch systemów w przypadku użycia trzech węzłów platformy SAP HANA, każdy: jednego serwera głównego i dwóch pracowników. Następujące nazwy maszyny Wirtualnej listy tabel i adresów IP. Wszystkie przykłady weryfikacji, które należy wykonać zostały wykonane na tych maszynach wirtualnych. Korzystając z tych nazw maszyn wirtualnych i adresy IP w przykładach poleceń, można lepiej zrozumieć poleceń i ich dane wyjściowe:


| Typ węzła | Nazwa maszyny wirtualnej | Adres IP |
| --- | --- | --- |
| Węzeł główny w witrynie 1 | hso-hana-vm-s1-0 | 10.0.0.30 |
| Węzeł procesu roboczego 1 w witrynie 1 | hso-hana-vm-s1-1 | 10.0.0.31 |
| Węzeł procesu roboczego 2 w witrynie 1 | hso-hana-vm-s1-2 | 10.0.0.32 |
| | | |
| Węzłem głównym w witrynie 2 | hso-hana-vm-s2-0 | 10.0.0.40 |
| Węzeł procesu roboczego 1 na 2 lokacji | hso-hana-vm-s2-1 | 10.0.0.41 |
| Węzeł procesu roboczego 2 w witrynie 2 | hso-hana-vm-s2-2  | 10.0.0.42 |
| | | |
| Twórca większościowym | hso-hana-dm | 10.0.0.13 |
| Interwencja urządzenia serwera | hso-hana-sbd | 10.0.0.19 |
| | | |
| Serwer systemu plików NFS 1 | hso-nfs-vm-0 | 10.0.0.15 |
| Serwer systemu plików NFS 2 | hso-nfs-vm-1 | 10.0.0.14 |



## <a name="multiple-subnets-and-vnics"></a>Wiele podsieci i kart sieciowych

Następujące oprogramowanie SAP HANA zalecenia dotyczące sieci trzy podsieci zostały utworzone w ramach jednej sieci wirtualnej platformy Azure. Skalować platformy SAP HANA na platformie Azure musi być zainstalowany w trybie nieudostępnionych. Oznacza to, że każdy węzeł będzie używał woluminów dysku lokalnego na potrzeby **/hana/danych** i **/hana/log**. Węzły używają tylko woluminy z dysku lokalnego, nie jest konieczne zdefiniować oddzielną podsieć dla magazynu:

- 10.0.2.0/24 do komunikacji między węzłami platformy SAP HANA
- 10.0.1.0/24 dla replikacji systemu SAP HANA (HSR)
- 10.0.0.0/24 dla wszystkich innych

Aby uzyskać informacje o konfiguracji oprogramowania SAP HANA, powiązane z użyciem wielu sieci, zobacz [global.ini platformy SAP HANA](#sap-hana-globalini).

Każda maszyna wirtualna w klastrze ma trzy kart sieciowych, które odpowiadają liczba podsieci. [Jak utworzyć maszynę wirtualną systemu Linux na platformie Azure z sieci wielu kart interfejsu][azure-linux-multiple-nics] describes a potential routing issue on Azure when deploying a Linux VM. This specific routing article applies only for use of multiple vNICs. The problem is solved by SUSE per default in SLES 12 SP3. For more information, see [Multi-NIC with cloud-netconfig in EC2 and Azure][suse-cloud-netconfig].


Aby sprawdzić, czy oprogramowanie SAP HANA jest prawidłowo skonfigurowany do używania wielu sieci, uruchom następujące polecenia. Sprawdź najpierw na poziomie systemu operacyjnego czy wszystkich trzech adresów IP dla wszystkich trzech podsieci są aktywne. Zdefiniowanej podsieci z różnych zakresów adresów IP, należy dostosować polecenia:

<pre><code>
ifconfig | grep "inet addr:10\."
</code></pre>

Następujące przykładowe dane wyjściowe pochodzą od drugiego węzła procesu roboczego w witrynie 2. Możesz wyświetlić trzy różne adresy IP z eth0, eth1 i eth2:

<pre><code>
inet addr:10.0.0.42  Bcast:10.0.0.255  Mask:255.255.255.0
inet addr:10.0.1.42  Bcast:10.0.1.255  Mask:255.255.255.0
inet addr:10.0.2.42  Bcast:10.0.2.255  Mask:255.255.255.0
</code></pre>


Następnie sprawdź porty platformy SAP HANA dla nazwy serwera i replikacji systemu HANA. SAP HANA będzie nasłuchiwał odpowiednie podsieci. W zależności od liczby wystąpień oprogramowania SAP HANA należy dostosować polecenia. Dla systemu testowego był liczby wystąpień **00**. Istnieją różne sposoby, aby dowiedzieć się, które porty są używane. 

Identyfikator wystąpienia, numer wystąpienia i inne informacje, funkcja zwraca następującą instrukcję SQL:

<pre><code>
select * from "SYS"."M_SYSTEM_OVERVIEW"
</code></pre>

Aby znaleźć numery poprawnego portu, można przeglądać, na przykład, HANA Studio w obszarze **konfiguracji** lub za pomocą instrukcji języka SQL:

<pre><code>
select * from M_INIFILE_CONTENTS WHERE KEY LIKE 'listen%'
</code></pre>

Aby odnaleźć każdy port, który jest używany w stosie oprogramowania SAP, łącznie z platformą SAP HANA, przeszukaj [porty TCP/IP wszystkich produktów SAP][sap-list-port-numbers].

Podany numer wystąpienia **00** system testowy SAP HANA w wersji 2.0, numer portu serwera nazw to **30001**. Numer portu do komunikacji metadanych replikacji systemu HANA jest **40002**. Jedną z opcji jest Zaloguj się w węźle procesu roboczego, a następnie sprawdź usług węzła głównego. W tym artykule będziemy sprawdzane pod węzłem procesu roboczego 2 w witrynie 2 próby połączenia do węzła głównego w witrynie 2.

Sprawdź nazwę portu serwera:

<pre><code>
nc -vz 10.0.0.40 30001
nc -vz 10.0.1.40 30001
nc -vz 10.0.2.40 30001
</code></pre>

Aby potwierdzić, że komunikacji międzywęzłowej używa podsieci **10.0.2.0/24**, wynik powinien wyglądać następujące przykładowe dane wyjściowe.
Tylko połączenie za pośrednictwem podsieci **10.0.2.0/24** ma być pomyślnie wykonane:

<pre><code>
nc: connect to 10.0.0.40 port 30001 (tcp) failed: Connection refused
nc: connect to 10.0.1.40 port 30001 (tcp) failed: Connection refused
Connection to 10.0.2.40 30001 port [tcp/pago-services1] succeeded!
</code></pre>

Sprawdzać dostępność portu HSR **40002**:

<pre><code>
nc -vz 10.0.0.40 40002
nc -vz 10.0.1.40 40002
nc -vz 10.0.2.40 40002
</code></pre>

Aby potwierdzić, że komunikacja HSR używa podsieci **10.0.1.0/24**, wynik powinien wyglądać następujące przykładowe dane wyjściowe.
Tylko połączenie za pośrednictwem podsieci **10.0.1.0/24** ma być pomyślnie wykonane:

<pre><code>
nc: connect to 10.0.0.40 port 40002 (tcp) failed: Connection refused
Connection to 10.0.1.40 40002 port [tcp/*] succeeded!
nc: connect to 10.0.2.40 port 40002 (tcp) failed: Connection refused
</code></pre>



## <a name="corosync"></a>Corosync


**Corosync** plik konfiguracji musi być prawidłowe w każdym węźle w klastrze, w tym większościowy węzeł producenta. Jeśli przyłączenia klastra węzeł nie działa zgodnie z oczekiwaniami, utworzyć lub skopiować **/etc/corosync/corosync.conf** ręcznie na wszystkie węzły, a następnie uruchom ponownie usługę. 

Zawartość **corosync.conf** z testu systemu jest przykładem.

Pierwsza sekcja jest **totem**, zgodnie z opisem w [klastra instalacji](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#cluster-installation), krok 11. Można zignorować wartość **mcastaddr**. Po prostu Zachowuj istniejący wpis. Wpisy dla **tokenu** i **consensus** należy ustawić zgodnie z opisem w [dokumentacji platformy Microsoft Azure SAP HANA][sles-pacemaker-ha-guide].

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

Druga sekcja **rejestrowania**, nie został zmieniony z danej wartości domyślnych:

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

Trzeci przedstawia sekcję **wstawienia**. Wszystkie węzły klastra mają pojawienie się przy użyciu ich **nodeid**:

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

W ostatniej sekcji **kworum**, należy ustawić wartość **expected_votes** poprawnie. Musi ona liczbę węzłów, w tym większościowy węzeł producenta. I wartości dla **two_node** musi być **0**. Nie należy usunąć wpis całkowicie. Ustaw wartość na **0**.

<pre><code>
quorum {
    # Enable and configure quorum subsystem (default: off)
    # see also corosync.conf.5 and votequorum.5
    provider: corosync_votequorum
    expected_votes: 7
    two_node: 0
}
</code></pre>


Uruchom ponownie usługę za pośrednictwem **systemctl**:

<pre><code>
systemctl restart corosync
</code></pre>




## <a name="sbd-device"></a>Interwencja urządzenia

Jak skonfigurować urządzenie interwencja w Maszynie wirtualnej platformy Azure jest opisana w [preferowane interwencja](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#sbd-fencing).

Najpierw czy na serwerze interwencja maszyny Wirtualnej znajdują się wpisy listy ACL dla każdego węzła w klastrze. Uruchom następujące polecenie na serwerze interwencja maszyny Wirtualnej:


<pre><code>
targetcli ls
</code></pre>


W systemie testowym dane wyjściowe polecenia będzie wyglądać jak w następującym przykładzie. Listy ACL nazwy, takie jak **iqn.2006-04.hso-db-0.local:hso-db-0** muszą zostać wprowadzone jako odpowiadających im nazw inicjatora na maszynach wirtualnych. Każda maszyna wirtualna musi mieć inną.

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

Sprawdź, czy nazw inicjatora na wszystkich maszynach wirtualnych są różne i odnoszą się do wcześniej pokazano wpisów. W tym przykładzie pochodzi z węzłem procesu roboczego 1 w witrynie 1:

<pre><code>
cat /etc/iscsi/initiatorname.iscsi
</code></pre>

Dane wyjściowe wyglądają jak w następującym przykładzie:

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

Następnie upewnij się, że **odnajdywania** działa prawidłowo. Uruchom następujące polecenie w każdym węźle klastra przy użyciu adresu IP serwera interwencja maszyny Wirtualnej:

<pre><code>
iscsiadm -m discovery --type=st --portal=10.0.0.19:3260
</code></pre>

Dane wyjściowe powinny wyglądać jak w następującym przykładzie:

<pre><code>
10.0.0.19:3260,1 iqn.2006-04.dbhso.local:dbhso
</code></pre>

Następny punkt dowód jest aby zweryfikować, że węzeł widzi urządzenia SDB. Sprawdź go w każdym węźle, w tym większościowy węzeł producenta:

<pre><code>
lsscsi | grep dbhso
</code></pre>

Dane wyjściowe powinny wyglądać jak w następującym przykładzie. Jednak nazwy mogą się różnić. Również może zmienić nazwę urządzenia, po ponownym uruchomieniu maszyny Wirtualnej:

<pre><code>
[6:0:0:0]    disk    LIO-ORG  sbddbhso         4.0   /dev/sdm
</code></pre>

W zależności od stanu systemu czasami warto ponowne uruchomienie usługi iSCSI, aby rozwiązać problemy. Następnie uruchom następujące polecenia:

<pre><code>
systemctl restart iscsi
systemctl restart iscsid
</code></pre>


Z poziomu każdego węzła można sprawdzić, czy wszystkie węzły są **wyczyść**. Upewnij się, że używasz poprawna nazwa urządzenia w określonym węźle:

<pre><code>
sbd -d /dev/sdm list
</code></pre>

Dane wyjściowe powinny być widoczne **wyczyść** dla każdego węzła w klastrze:

<pre><code>
0       hso-hana-vm-s1-0        clear
1       hso-hana-vm-s2-2        clear
2       hso-hana-vm-s2-1        clear
3       hso-hana-dm     clear
4       hso-hana-vm-s1-1        clear
5       hso-hana-vm-s2-0        clear
6       hso-hana-vm-s1-2        clear
</code></pre>


Sprawdź innego interwencja **zrzutu** opcji **interwencja** polecenia. W tym przykładowe polecenie i danych wyjściowych z większości węzłów producenta, nazwa urządzenia został **sdd**, a nie **sdm**:

<pre><code>
sbd -d /dev/sdd dump
</code></pre>

Dane wyjściowe, oprócz nazwa urządzenia powinna wyglądać takie same we wszystkich węzłach:

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

Co więcej Wyszukaj interwencja jest możliwość do wysyłania komunikatu do innego węzła. Aby wysłać komunikat do węzła procesu roboczego 2 w lokacji 2, uruchom następujące polecenie w węźle procesu roboczego 1 na 2 lokacji:

<pre><code>
sbd -d /dev/sdm message hso-hana-vm-s2-2 test
</code></pre>

Na stronie maszyny Wirtualnej, obiektu docelowego **hso-hana-vm-s2-2** w tym przykładzie można znaleźć następującego wpisu w **/var/log/messages**:

<pre><code>
/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68:   notice: servant: Received command test from hso-hana-vm-s2-1 on disk /dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68
</code></pre>

Sprawdź, czy wpisy w **/etc/sysconfig/sbd** odnoszą się do opisu w [konfigurowania program Pacemaker w systemie SUSE Linux Enterprise Server na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#sbd-fencing). Upewnij się, że ustawienie uruchamiania **/etc/iscsi/iscsid.conf** jest ustawiony na automatyczny.

Następujące wpisy są ważne w **/etc/sysconfig/sbd**. Dostosowanie **identyfikator** wartość, jeśli to konieczne:

<pre><code>
SBD_DEVICE="/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68;"
SBD_PACEMAKER=yes
SBD_STARTMODE=always
SBD_WATCHDOG=yes
</code></pre>


Sprawdź ustawienie uruchamiania w **/etc/iscsi/iscsid.conf**. Wymagane ustawienie powinno się to zdarzyć z następującymi **iscsiadm** polecenie opisane w dokumentacji. Sprawdź i zaadaptować ją ręcznie za pomocą **vi** Jeśli jest inny.

To polecenie ustawia zachowanie podczas uruchamiania:

<pre><code>
iscsiadm -m node --op=update --name=node.startup --value=automatic
</code></pre>

Wprowadź ten wpis w **/etc/iscsi/iscsid.conf**:

<pre><code>
node.startup = automatic
</code></pre>

Podczas testowania i weryfikacji po ponownym uruchomieniu maszyny wirtualnej na urządzeniu interwencja nie były widoczne już w niektórych przypadkach. Było rozbieżności między ustawienie uruchamiania i YaST2 pokazano. Aby sprawdzić ustawienia, należy wykonać następujące czynności:

1. Start YaST2.
2. Wybierz **usług sieciowych** po lewej stronie.
3. Przewiń w dół po prawej stronie, aby **inicjatora iSCSI** i wybierz ją.
4. Na następnym ekranie, w obszarze **usługi** karcie Nazwa inicjatora unikatowy dla węzła.
5. Upewnij się, że powyżej Nazwa inicjatora **uruchomić usługi** wartość jest równa **podczas rozruchu**.
6. Jeśli nie jest, wówczas ustaw ją na **podczas rozruchu** zamiast **ręcznie**.
7. Następnie przełącz górnej karty do **połączone obiekty docelowe**.
8. Na **połączone obiekty docelowe** ekranu, powinien zostać wyświetlony wpis dla urządzenia interwencja, np. w tym przykładzie: **10.0.0.19:3260 iqn.2006-04.dbhso.local:dbhso**.
9. Sprawdź, czy **uruchamiania** wartość jest równa **podczas rozruchu**.
10. Jeśli nie, wybierz **Edytuj** i zmień je.
11. Zapisz zmiany i zamknij YaST2.



## <a name="pacemaker"></a>Program pacemaker

Po wszystko jest prawidłowo skonfigurowane, można uruchomić następujące polecenie, w każdym węźle, aby sprawdzić stan usługi program Pacemaker:

<pre><code>
systemctl status pacemaker
</code></pre>

Górnej części danych wyjściowych powinien wyglądać jak w następującym przykładzie. Ważne jest, stan po **Active** jest przedstawiana w postaci **załadowane** i **aktywny (uruchomione)** . Stan po **Loaded** musi zostać pokazany jako **włączone**.

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

Jeśli ustawienie jest nadal na **wyłączone**, uruchom następujące polecenie:

<pre><code>
systemctl enable pacemaker
</code></pre>

Aby wyświetlić wszystkie zasoby skonfigurowany program Pacemaker, uruchom następujące polecenie:

<pre><code>
crm status
</code></pre>

Dane wyjściowe powinny wyglądać jak w następującym przykładzie. Ma ona poprawnie, **cln** i **msl** zasoby są pokazane jako zatrzymana na większości twórca maszyny Wirtualnej, **hso-hana-dm**. Nie ma żadnych instalacji oprogramowania SAP HANA, w większości węzłów producenta. Więc **cln** i **msl** zasoby są pokazane jako zatrzymana. Jest ważne, pokazuje poprawne łączna liczba maszyn wirtualnych, **7**. Wszystkie maszyny wirtualne, które są częścią klastra musi być wymieniona ze stanem **Online**. Musi być poprawnie rozpoznawana bieżącego podstawowego węzła głównego. W tym przykładzie przedstawiono w nim **hso-hana-vm-s1-0**:

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

Ważną funkcją program Pacemaker jest tryb konserwacji. W tym trybie może wprowadzać zmiany bez zdolnemu akcję natychmiastowego klastra. Przykładem jest ponowny rozruch maszyny Wirtualnej. Typowym przypadkiem użycia jest planowana konserwacja infrastruktury systemu operacyjnego lub na platformie Azure. Zobacz [planowanej konserwacji](#planned-maintenance). Użyj następującego polecenia, aby umieścić program Pacemaker w trybie konserwacji:

<pre><code>
crm configure property maintenance-mode=true
</code></pre>

Sprawdzenie za pomocą **stan crm**, można zauważyć, że w danych wyjściowych, że wszystkie zasoby są oznaczone jako **niezarządzanych**. W tym stanie klastra nie reagują na wszelkie zmiany, takie jak uruchamianie lub zatrzymywanie platformy SAP HANA.
Poniższy przykład pokazuje dane wyjściowe **stan crm** polecenia, gdy klaster jest w trybie konserwacji:

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


W tym przykładzie polecenie pokazuje, jak do zakończenia trybu konserwacji klastra:

<pre><code>
crm configure property maintenance-mode=false
</code></pre>


Inny **crm** polecenie pobiera kompletna Konfiguracja klastra do edytora, dzięki czemu można go edytować. Po zapisuje zmiany, klaster rozpoczyna się odpowiednie działania:

<pre><code>
crm configure edit
</code></pre>

Aby zobaczyć kompletna Konfiguracja klastra, należy użyć **Pokaż crm** opcji:

<pre><code>
crm configure show
</code></pre>



Po awarii zasobów klastra **stan crm** polecenie wyświetla listę **akcji nie powiodło się**. Zobacz poniższy przykład te dane wyjściowe:


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

Jest wykonanie oczyszczania klastra po awarii. Użyj **crm** ponownie polecenie, a następnie użyć opcji polecenia **oczyszczania** pozbyć się tych nie powiodło się wpisy akcji. Nadaj nazwę odpowiedniego zasobu klastra w następujący sposób:

<pre><code>
crm resource cleanup rsc_SAPHanaCon_HSO_HDB00
</code></pre>

Polecenie powinny zostać zwrócone dane wyjściowe podobne do poniższego przykładu:

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



## <a name="failover-or-takeover"></a>W trybie Failover lub przejęcia

Zgodnie z opisem w [ważne uwagi](#important-notes), nie używaj standardowych łagodne zamykanie do testowania klastra pracy awaryjnej lub przejęcia replikacji systemu HANA SAP HANA. Zamiast tego zaleca się wyzwolić jądra, wymusić migracji zasobów lub prawdopodobnie Zamknij wszystkie sieci na poziomie systemu operacyjnego maszyny wirtualnej. Inną metodą jest **crm \<węzła\> wstrzymania** polecenia. Zobacz [dokumentu SUSE][sles-12-ha-paper]. 

Następujące przykładowe trzy polecenia można wymusić tryb failover klastra:

<pre><code>
echo c &gt /proc/sysrq-trigger

crm resource migrate msl_SAPHanaCon_HSO_HDB00 hso-hana-vm-s2-0 force

wicked ifdown eth0
wicked ifdown eth1
wicked ifdown eth2
......
wicked ifdown eth&ltn&gt
</code></pre>

Zgodnie z opisem w [planowanej konserwacji](#planned-maintenance), dobrym sposobem na monitorowanie działania klastra jest uruchomienie **SAPHanaSR showAttr** z **Obejrzyj** polecenia:

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

Pomaga również zobaczyć stan pozioma platformy SAP HANA, pochodzące ze skryptu języka Python SAP. Konfiguracja klastra szuka to wartość stanu. Staną się oczywiste, rozważając awarii węzła procesu roboczego. Jeśli węzeł procesu roboczego ulegnie awarii, SAP HANA nie natychmiast zwróci błąd kondycji systemu zupełnie skalowalnego w poziomie. 

Istnieją pewne ponownych prób, aby uniknąć niepotrzebnych przejścia w tryb failover. Klaster reaguje tylko wtedy, gdy stan zmieni się z **Ok**, wartość zwracana **4**, **błąd**, wartość zwracana **1**. Dlatego jest on poprawny Jeśli dane wyjściowe z **SAPHanaSR showAttr** zawiera Maszynę wirtualną ze stanem **w trybie offline**. Ale nie ma żadnych działań do przełącznika podstawowego i pomocniczego. Brak działania klastra pobiera wyzwalane, tak długo, jak oprogramowanie SAP HANA nie zwraca błąd.

Możesz monitorować stan kondycji pozioma platformy SAP HANA jako użytkownik  **\<HANA SID\>adm** wywołując skrypt w języku Python SAP w następujący sposób. Może być konieczne dostosowanie ścieżki:

<pre><code>
watch python /hana/shared/HSO/exe/linuxx86_64/HDB_2.00.032.00.1533114046_eeaf4723ec52ed3935ae0dc9769c9411ed73fec5/python_support/landscapeHostConfiguration.py
</code></pre>

Dane wyjściowe tego polecenia powinien wyglądać jak w następującym przykładzie. **Stan hosta** kolumny i **ogólny stan hosta** są ważne. Rzeczywiste dane wyjściowe są szersze, za pomocą dodatkowych kolumn.
Aby tabela danych wyjściowych był bardziej czytelny, w tym dokumencie, większość kolumny po prawej stronie zostały usunięte:

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


Ma innego polecenia, aby sprawdzić bieżące działania klastra. Zobacz poniższe polecenie i zakończeniem danych wyjściowych po został skasowany węzła głównego w lokacji głównej. Można wyświetlić listę akcji przejścia, takich jak **podwyższania poziomu** byłych pomocniczego węzła głównego, **hso-hana-vm-s2-0**, jako nowego wzorca podstawowego. Jeśli wszystko jest w dobrym stanie, a wszystkie czynności są zakończone, to **podsumowanie przejścia** listy musi być pusty.

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

Istnieją różnych przypadków użycia, jeśli chodzi o planowanej konserwacji. Jedno pytanie jest, czy jest to po prostu konieczność konserwacji infrastruktury sposób zmiany na poziomie systemu operacyjnego i konfiguracji dysku lub uaktualnienia platformy HANA.
Dodatkowe informacje można znaleźć w dokumentach, od firmy SUSE, takich jak [kierunku żadnych przestojów][sles-zero-downtime-paper] or [SAP HANA SR Performance Optimized Scenario][sles-12-for-sap]. Te dokumenty również zawierać przykłady pokazujące, jak ręcznie migrować podstawowego.

Intensywny wewnętrzne testy została wykonana w celu sprawdź przypadek użycia konserwacji infrastruktury. Aby uniknąć problemów związanych z migracją podstawowego, podjęliśmy decyzję o zawsze migracji główną przed przełączeniem klastra w tryb konserwacji. W ten sposób nie jest to niezbędne do zapewnienia klastra zapomnij o sytuacji w poprzedniej wersji portalu: której stronie był podstawowego i który został dodatkowej.

W związku z tym są dwa różne sytuacje:

- **Planowana konserwacja na bieżącej maszynie pomocniczej**. W takim przypadku wystarczy umieścić klastra w tryb konserwacji i wykonują pracę w pomocniczym, bez wywierania wpływu na klastrze.

- **Planowanej konserwacji na serwerze podstawowym bieżącego**. Tak, aby użytkownicy mogą nadal działać podczas konserwacji, konieczne jest wymuszenie przejścia w tryb failover. W przypadku tej metody możesz wyzwolić tryb failover klastra przez program Pacemaker i nie tylko na poziomie replikacji systemu HANA SAP HANA. Instalator program Pacemaker automatyczne wyzwolenie przejęcia platformy SAP HANA. Należy również wykonać pracę awaryjną przed umieszczeniem klastra w tryb konserwacji.

Procedury w celu przeprowadzenia konserwacji w bieżącej lokacji dodatkowej jest następująca:

1. Umieść klastra w tryb konserwacji.
2. Wykonać pracę w lokacji dodatkowej. 
3. Koniec tryb konserwacji klastra.

Procedura obsługi w bieżącej lokacji głównej jest bardziej złożonych:

1. Ręcznie wyzwolić tryb failover lub przejęcia platformy SAP HANA przy użyciu migracji zasobów program Pacemaker. Zobacz szczegółowe informacje, które należy wykonać.
2. Oprogramowanie SAP HANA na wcześniejsze lokacji głównej pobiera zamknięta przez Konfiguracja klastra.
3. Umieść klastra w tryb konserwacji.
4. Po zakończeniu prac konserwacyjnych, należy zarejestrować byłych podstawowy jako nową lokację dodatkową.
5. Wyczyść konfigurację klastra. Zobacz szczegółowe informacje, które należy wykonać.
6. Koniec tryb konserwacji klastra.


Migrowanie zasobów dodaje wpis w konfiguracji klastra. Przykładem jest wymuszenie przejścia w tryb failover. Należy wyczyścić te wpisy przed zakończenia trybu konserwacji. Zobacz poniższy przykład.

Najpierw należy wymusić klastra pracy awaryjnej przy użyciu funkcji migracji **msl** zasobów do dodatkowej bieżącego węzła głównego. To polecenie wyświetla ostrzeżenie, że **Przenieś ograniczenie** został utworzony:

<pre><code>
crm resource migrate msl_SAPHanaCon_HSO_HDB00 force

INFO: Move constraint created for msl_SAPHanaCon_HSO_HDB00
</code></pre>


Sprawdzanie procesu pracy awaryjnej za pomocą polecenia **SAPHanaSR showAttr**. Aby monitorować stan klastra, Otwórz okno dedykowanych shell i uruchom polecenie **Obejrzyj**:

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

Dane wyjściowe powinny pokazywać ręcznej pracy awaryjnej. Stało się wcześniejsze pomocniczego węzła głównego **promowane**, w tym przykładzie **hso-hana-vm-s2-0**. Wcześniejsze lokacji głównej zostało zatrzymane, **lss** wartość **1** dla poprzedniej wersji portalu z podstawowym węzłem głównym **hso-hana-vm-s1-0**: 

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

Po pracy awaryjnej i przejęcia platformy SAP HANA, umieść klastra w tryb konserwacji, zgodnie z opisem w [program Pacemaker](#pacemaker).

Polecenia **SAPHanaSR showAttr** i **stan crm** nie wskazują nic o ograniczenia utworzone przez migrację zasobów. Jedną z opcji aby uwidocznić te ograniczenia jest pokazanie konfiguracji zasobów całego klastra za pomocą następującego polecenia:

<pre><code>
crm configure show
</code></pre>

W konfiguracji klastra możesz znaleźć nowe ograniczenie lokalizacji spowodowane migracji przypisywane ręcznie zasobów. Ten przykładowy wpis, który rozpoczyna się od **lokalizacji interfejsu wiersza polecenia —** :

<pre><code>
location cli-ban-msl_SAPHanaCon_HSO_HDB00-on-hso-hana-vm-s1-0 msl_SAPHanaCon_HSO_HDB00 role=Started -inf: hso-hana-vm-s1-0
</code></pre>

Niestety takie ograniczenia mogą mieć wpływ na ogólną działanie klastra. Dlatego jest to konieczne, aby usunąć je ponownie, przed przełączeniem w tryb całego systemu kopii zapasowych. Za pomocą **unmigrate** polecenia, można wyczyścić ograniczenia lokalizacji, które zostały utworzone przed. Nazewnictwo może być nieco mylące. Go nie spróbuj przeprowadzić migrację zasobów do oryginalnej maszyny Wirtualnej, z którego została poddana migracji. Po prostu ograniczenia lokalizacji jest usuwany, a także zwraca odpowiednie informacje, po uruchomieniu polecenia:


<pre><code>
crm resource unmigrate msl_SAPHanaCon_HSO_HDB00

INFO: Removed migration constraints for msl_SAPHanaCon_HSO_HDB00
</code></pre>

Na końcu prac konserwacyjnych, możesz zatrzymać tryb konserwacji klastra w pokazany na [program Pacemaker](#pacemaker).



## <a name="hbreport-to-collect-log-files"></a>hb_report, aby zbierać pliki dziennika

Aby analizować problemy z klastra program Pacemaker, jest przydatne i również żądany przez SUSE dział pomocy technicznej do uruchamiania **hb_report** narzędzia. Zbiera wszystkie pliki ważne dziennika, które trzeba sprawdzić, co się stało. To przykładowe wywołanie używa godzina rozpoczęcia i zakończenia, miejsce wystąpienia określonego zdarzenia. Zobacz też [ważne uwagi](#important-notes):

<pre><code>
hb_report -f "2018/09/13 07:36" -t "2018/09/13 08:00" /tmp/hb_report_log
</code></pre>

Polecenie poinformuje, gdzie je umieścić pliki skompresowane dziennika:

<pre><code>
The report is saved in /tmp/hb_report_log.tar.bz2
Report timespan: 09/13/18 07:36:00 - 09/13/18 08:00:00
</code></pre>

Następnie można wyodrębnić poszczególnych plików za pomocą standardowej **tar** polecenia:

<pre><code>
tar -xvf hb_report_log.tar.bz2
</code></pre>

Jeśli przyjrzymy się wyodrębnione pliki, możesz znaleźć wszystkie pliki dziennika. Większość z nich były umieszczane w osobnych katalogów dla każdego węzła w klastrze:

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


Zakres czasu został określony, bieżący węzeł główny **hso-hana-vm-s1-0** został zatrzymany. Znajdują się wpisy związane z tym zdarzeniem w **journal.log**:

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

Innym przykładem jest plik dziennika program Pacemaker we wzorcu dodatkowej stało się nowego wzorca podstawowego. Ten fragment prezentuje, czy stan zabitych podstawowego węzła głównego został ustawiony **offline**:

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


Następujące fragmenty są z platformy SAP HANA **global.ini** pliku 2 w witrynie klastra. Ten przykład przedstawia nazwę hosta wpisów rozpoznawania korzystać z różnych sieci na potrzeby komunikacji między węzłami platformy SAP HANA i replikacji systemu HANA:

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

Rozwiązanie klastrowe udostępnia interfejs przeglądarki, która oferuje graficzny interfejs użytkownika dla użytkowników, którzy wolą menu i aplikacji graficznych o wszystkie polecenia z poziomu powłoki.
Aby użyć interfejsu przeglądarki, Zastąp **\<węzła\>** z rzeczywistych węzłem platformy SAP HANA następujący adres URL. Następnie wprowadź poświadczenia klastra (użytkownik **klastra**):

<pre><code>
https://&ltnode&gt:7630
</code></pre>

Ten zrzut ekranu przedstawia pulpit nawigacyjny klastra:


![Pulpit nawigacyjny klastra hawk](media/hana-vm-scale-out-HA-troubleshooting/hawk-1.png)


W tym przykładzie przedstawiono ograniczenia lokalizacji spowodowane migracji zasobów klastra, jak wyjaśniono w [planowanej konserwacji](#planned-maintenance):


![Hawk lista ograniczeń](media/hana-vm-scale-out-HA-troubleshooting/hawk-2.png)


Możesz również przekazać **hb_report** dane wyjściowe w Hawk w obszarze **historii**, jak pokazano w następujący sposób. Zobacz hb_report, aby zbierać pliki dziennika: 

![Dane wyjściowe hb_report przekazywania hawk](media/hana-vm-scale-out-HA-troubleshooting/hawk-3.png)

Za pomocą **Explorer historii**, następnie można przejść przez wszystkie przejścia klastra w **hb_report** dane wyjściowe:

![Hawk przejścia w danych wyjściowych hb_report](media/hana-vm-scale-out-HA-troubleshooting/hawk-4.png)

Ten końcowy zrzut ekranu przedstawia **szczegóły** części jednej przejścia. Klaster reakcja na awarii podstawowego węzła głównego, węzła **hso-hana-vm-s1-0**. Jest teraz podwyższanie poziomu pomocniczego węzła jako nowy wzorzec **hso-hana-vm-s2-0**:

![Hawk jednego przejścia](media/hana-vm-scale-out-HA-troubleshooting/hawk-5.png)


## <a name="next-steps"></a>Następne kroki

Ten przewodnik rozwiązywania problemów opisano wysokiej dostępności dla oprogramowania SAP HANA w konfiguracji skalowania w poziomie. Innym ważnym elementem w środowisko SAP jest stos oprogramowania SAP NetWeaver. Dowiedz się więcej o [wysokiej dostępności dla oprogramowania SAP NetWeaver na maszynach wirtualnych Azure, które używają systemu SUSE Enterprise Linux Server][sap-nw-ha-guide-sles].


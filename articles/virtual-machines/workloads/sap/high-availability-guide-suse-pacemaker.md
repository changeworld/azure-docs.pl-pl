---
title: Konfigurowanie program Pacemaker w systemie SUSE Linux Enterprise Server na platformie Azure | Dokumentacja firmy Microsoft
description: Konfigurowanie program Pacemaker w systemie SUSE Linux Enterprise Server na platformie Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/16/2018
ms.author: sedusch
ms.openlocfilehash: f1b993cf45d987cb51f64359b331f3862d054774
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549903"
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>Konfigurowanie program Pacemaker w systemie SUSE Linux Enterprise Server na platformie Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[virtual-machines-linux-maintenance]:../../linux/maintenance-and-updates.md#maintenance-not-requiring-a-reboot
[virtual-machines-windows-maintenance]:../../windows/maintenance-and-updates.md#maintenance-not-requiring-a-reboot
[sles-nfs-guide]:high-availability-guide-suse-nfs.md
[sles-guide]:high-availability-guide-suse.md

Dostępne są dwie opcje do skonfigurowania klastra program Pacemaker w systemie Azure. Możesz użyć agenta preferowane dba o ponowne uruchomienie węzła nie powiodło się za pośrednictwem interfejsów API platformy Azure, lub można użyć urządzenia interwencja.

Urządzenie interwencja wymaga co najmniej jedną maszynę wirtualną dodatkowe, działa jako serwer obiektów docelowych iSCSI, która zawiera urządzenia z systemem interwencja. Te serwery obiektów docelowych iSCSI może jednak być współużytkowane z innymi klastrami program Pacemaker. Zaletą używania urządzenia z systemem interwencja jest krótszy czas pracy awaryjnej i, jeśli używasz interwencja urządzeniami lokalnymi, nie wymaga żadnych zmian na sposób działania klastra program pacemaker. Aby zezwolić na urządzeniu z systemem interwencja stają się niedostępne, na przykład podczas stosowania poprawek systemu operacyjnego serwera docelowego iSCSI, można użyć maksymalnie trzech urządzeń interwencja klastra program Pacemaker. Jeśli chcesz użyć więcej niż jedno urządzenie interwencja na program Pacemaker, pamiętaj wdrożyć wiele serwerów obiektów docelowych iSCSI i połączyć interwencja jednego z każdego serwera docelowego iSCSI. Zalecamy używanie jednego urządzenia interwencja lub trzy. Program pacemaker nie będzie automatycznie ogrodzenia węzła klastra, jeśli tylko skonfigurować dwa urządzenia interwencja i jeden z nich nie jest dostępny. Jeśli chcesz można było ogrodzenia, gdy jeden serwer obiektów docelowych iSCSI nie działa, należy korzystać z trzech urządzeń interwencja i w związku z tym trzech serwerów obiektów docelowych iSCSI.

Jeśli nie chcesz inwestować w dodatkowych maszyn wirtualnych, można również użyć agent Odgradzający Azure. Minusem jest to, że przejścia w tryb failover może potrwać od 10 do 15 minut, jeśli stop zasobów ulegnie awarii lub węzłów klastra nie może komunikować się które sobie nawzajem już.

![Program pacemaker w systemie SLES — omówienie](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

>[!IMPORTANT]
> Przy planowaniu i wdrażaniu program Pacemaker w systemie Linux w klastrze węzłów i interwencja urządzeń jest niezbędne dla ogólną niezawodność kompletna Konfiguracja klastra zaangażowane routingu między maszynami wirtualnymi i maszyn wirtualnych, obsługi urządzeń interwencja nie przechodzi przez inne urządzenia, takie jak [urządzeń WUS](https://azure.microsoft.com/solutions/network-appliances/). W przeciwnym razie problemy i obsługi zdarzeń za pomocą urządzenia NVA może mieć negatywny wpływ na stabilności i niezawodności ogólnej konfiguracji klastra. Aby uniknąć takich przeszkód, nie definiują reguły routingu urządzeń WUS lub [reguł routingu zdefiniowane użytkownika](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) tego kierowanie ruchu między węzłami klastra i interwencja za pośrednictwem urządzenia WUS i podobne urządzenia podczas planowania i wdrażania systemu Linux Program pacemaker klastrowane węzły i interwencja urządzeń. 
>

## <a name="sbd-fencing"></a>Preferowane interwencja

Wykonaj następujące kroki, aby urządzenia z systemem interwencja na użytek preferowane.

### <a name="set-up-iscsi-target-servers"></a>Konfigurowanie serwerów obiektów docelowych iSCSI

Najpierw należy utworzyć iSCSI docelowych maszyn wirtualnych. serwery obiektów docelowych iSCSI może być udostępniane wielu klastrów program Pacemaker.

1. Wdrażanie nowego systemu SLES 12 z dodatkiem SP1 lub nowszej maszyn wirtualnych i połączyć się z nimi za pośrednictwem protokołu ssh. Maszyny nie trzeba dużych. Rozmiar maszyny wirtualnej, takie jak Standard_E2s_v3 lub Standard_D2s_v3 jest wystarczająca. Upewnij się, że używasz usługi Premium storage dysku systemu operacyjnego.

Uruchom następujące polecenia na wszystkich **iSCSI docelowych maszyn wirtualnych**.

1. Aktualizacja w systemie SLES

   <pre><code>sudo zypper update
   </code></pre>

1. Usuwanie pakietów

   Aby uniknąć znany problem z targetcli i SLES 12 z dodatkiem SP3, należy odinstalować następujące pakiety. Można zignorować błędy dotyczące pakietów, których nie można znaleźć

   <pre><code>sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>

1. Instalowanie pakietów docelowych iSCSI

   <pre><code>sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. Włącz usługę obiektu docelowego iSCSI

   <pre><code>sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>Utwórz urządzenie iSCSI na serwerze docelowym iSCSI

Uruchom następujące polecenia na wszystkich **iSCSI docelowych maszyn wirtualnych** do tworzenia dysków iSCSI w przypadku klastrów używane przez systemy SAP. W poniższym przykładzie są tworzone interwencja urządzeń dla wielu klastrów. Przedstawia on sposób użyje jednego serwera obiektów docelowych iSCSI dla wielu klastrów. Urządzenia interwencja są umieszczane na dysku systemu operacyjnego. Upewnij się, że masz wystarczająco dużo miejsca.

**` nfs`** Służy do identyfikowania klastra systemu plików NFS **ascsnw1** służy do identyfikowania klastra ASCS **NW1**, **dbnw1** służy do identyfikowania bazy danych klastra **NW1** , **0 systemu plików nfs** i **1 systemu plików nfs** są nazwy hostów węzłów klastra systemu plików NFS **nw1 xscs 0** i **nw1 xscs 1**są nazwy hostów programu **NW1** węzły, klastra ASCS i **nw1-db-0** i **nw1-db-1** są nazwy hostów bazy danych z węzłami klastra. Zamień na ich nazw hostów węzły klastra i identyfikatora SID systemu SAP.

<pre><code># Create the root folder for all SBD devices
sudo mkdir /sbd

# Create the SBD device for the NFS server
sudo targetcli backstores/fileio create sbdnfs /sbd/sbdnfs 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.nfs.local:nfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/luns/ create /backstores/fileio/sbdnfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-0.local:nfs-0</b>
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-1.local:nfs-1</b>

# Create the SBD device for the ASCS server of SAP System NW1
sudo targetcli backstores/fileio create sbdascs<b>nw1</b> /sbd/sbdascs<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbdascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-0.local:nw1-xscs-0</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-1.local:nw1-xscs-1</b>

# Create the SBD device for the database cluster of SAP System NW1
sudo targetcli backstores/fileio create sbddb<b>nw1</b> /sbd/sbddb<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbddb<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-0.local:nw1-db-0</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-1.local:nw1-db-1</b>

# save the targetcli changes
sudo targetcli saveconfig
</code></pre>

Możesz sprawdzić, jeśli wszystko zostało poprawnie skonfigurowane za pomocą

<pre><code>sudo targetcli ls

o- / .......................................................................................................... [...]
  o- backstores ............................................................................................... [...]
  | o- block ................................................................................... [Storage Objects: 0]
  | o- fileio .................................................................................. [Storage Objects: 3]
  | | o- <b>sbdascsnw1</b> ................................................ [/sbd/sbdascsnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbddbnw1</b> .................................................... [/sbd/sbddbnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbdnfs</b> ........................................................ [/sbd/sbdnfs (50.0MiB) write-thru activated]
  | |   o- alua .................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | o- pscsi ................................................................................... [Storage Objects: 0]
  | o- ramdisk ................................................................................. [Storage Objects: 0]
  o- iscsi ............................................................................................. [Targets: 3]
  | o- <b>iqn.2006-04.ascsnw1.local:ascsnw1</b> .................................................................. [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-xscs-0.local:nw1-xscs-0</b> ............................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-xscs-1.local:nw1-xscs-1</b> ............................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .......................................... [fileio/sbdascsnw1 (/sbd/sbdascsnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.dbnw1.local:dbnw1</b> ...................................................................... [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-db-0.local:nw1-db-0</b> ................................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-db-1.local:nw1-db-1</b> ................................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .............................................. [fileio/sbddbnw1 (/sbd/sbddbnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.nfs.local:nfs</b> .......................................................................... [TPGs: 1]
  |   o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  |     o- acls ........................................................................................... [ACLs: 2]
  |     | o- <b>iqn.2006-04.nfs-0.local:nfs-0</b> ......................................................... [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     | o- <b>iqn.2006-04.nfs-1.local:nfs-1</b> ......................................................... [Mapped LUNs: 1]
  |     |   o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     o- luns ........................................................................................... [LUNs: 1]
  |     | o- lun0 .................................................. [fileio/sbdnfs (/sbd/sbdnfs) (default_tg_pt_gp)]
  |     o- portals ..................................................................................... [Portals: 1]
  |       o- 0.0.0.0:3260 ...................................................................................... [OK]
  o- loopback .......................................................................................... [Targets: 0]
  o- vhost ............................................................................................. [Targets: 0]
  o- xen-pvscsi ........................................................................................ [Targets: 0]
</code></pre>

### <a name="set-up-sbd-device"></a>Konfigurowanie urządzenia interwencja

Łączenie z urządzeniem iSCSI, który został utworzony w poprzednim kroku z klastra.
Uruchom następujące polecenia na węzłach nowego klastra, który chcesz utworzyć.
Następujące elementy mają prefiks albo **[A]** — mające zastosowanie do wszystkich węzłów, **[1]** — dotyczy to tylko węzeł 1 lub **[2]** — dotyczy to tylko węzeł 2.

1. **[A]**  Nawiązywanie połączenia z urządzeniami iSCSI

   Najpierw należy włączyć iSCSI i interwencja usług.

   <pre><code>sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]**  Zmienić nazwę inicjatora w pierwszym węźle

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Zmień zawartość pliku na zgodną z listy ACL, używane podczas tworzenia urządzenia iSCSI na serwerze docelowym iSCSI, na przykład w przypadku serwera NFS.

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-0.local:nfs-0</b>
   </code></pre>

1. **[2]**  Zmienić nazwę inicjatora drugiego węzła

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Zmień zawartość pliku na zgodną z listy kontroli dostępu, którego użyto podczas tworzenia urządzenia iSCSI na serwerze docelowym iSCSI

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-1.local:nfs-1</b>
   </code></pre>

1. **[A]**  Ponowne uruchomienie usługi iSCSI

   Teraz ponownie uruchomić usługę iSCSI, aby zastosować zmiany

   <pre><code>sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   Podłącz urządzenia iSCSI. W poniższym przykładzie 10.0.0.17 jest adres IP serwera docelowego iSCSI i 3260 jest domyślnym portem. <b>IQN.2006 04.nfs.local:nfs</b> jest jedną z nazw docelowych, które znajduje się po uruchomieniu pierwszego polecenia poniżej (iscsiadm -m discovery).

   <pre><code>sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the second iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.18:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.18:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.18:3260</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the third iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.19:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.19:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.19:3260</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   Upewnij się, że urządzenia iSCSI są dostępne i zanotuj nazwy urządzenia (w poniższym przykładzie/dev/sde)

   <pre><code>lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdd</b>
   # <b>[7:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sde</b>
   # <b>[8:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdf</b>
   </code></pre>

   Teraz można pobrać identyfikatorów urządzeń iSCSI.

   <pre><code>ls -l /dev/disk/by-id/scsi-* | grep <b>sdd</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03 -> ../../sdd</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sde</b>
   
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-1LIO-ORG_cl1:3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   # <b>lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df -> ../../sde</b>
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-SLIO-ORG_cl1_3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sdf</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf -> ../../sdf</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   </code></pre>

   Polecenie listę trzy identyfikatory urządzeń dla każdego urządzenia interwencja. Zalecane jest używanie Identyfikatora, który rozpoczyna się od scsi-3 w przykładzie powyżej to jest

   * **/dev/Disk/by-ID/SCSI-36001405afb0ba8d3a3c413b8cc2cca03**
   * **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**
   * **/dev/Disk/by-ID/SCSI-36001405f88f30e7c9684678bc87fe7bf**

1. **[1]**  Utwórz urządzenie interwencja

   Identyfikator urządzenia urządzeń iSCSI umożliwia utworzenie nowych urządzeń interwencja w pierwszym węźle klastra.

   <pre><code>sudo sbd -d <b>/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03</b> -1 60 -4 120 create

   # Also create the second and third SBD devices if you want to use more than one.
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 60 -4 120 create
   sudo sbd -d <b>/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf</b> -1 60 -4 120 create
   </code></pre>

1. **[A]**  Dostosowanie konfiguracji interwencja

   Otwórz plik konfiguracji interwencja

   <pre><code>sudo vi /etc/sysconfig/sbd
   </code></pre>

   Zmiana własności urządzenia interwencja, Włącz integrację program pacemaker i zmienić tryb startowy danej interwencja.

   <pre><code>[...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   [...]
   <b>SBD_WATCHDOG="yes"</b>
   </code></pre>

   Utwórz ` softdog` pliku konfiguracji

   <pre><code>echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   Teraz załadować moduł

   <pre><code>sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>Instalacja klastra

Następujące elementy mają prefiks albo **[A]** — mające zastosowanie do wszystkich węzłów, **[1]** — dotyczy to tylko węzeł 1 lub **[2]** — dotyczy to tylko węzeł 2.

1. **[A]**  Aktualizacji w systemie SLES

   <pre><code>sudo zypper update
   </code></pre>

1. **[A]**  Konfiguracji systemu operacyjnego

   W niektórych przypadkach program Pacemaker tworzy wiele procesów, a tym samym przekroczy dozwolona liczba procesów. W takim przypadku pulsu między węzłami klastra może zakończyć się niepowodzeniem i prowadzić do trybu failover zasobów. Zalecamy zwiększenie maksymalna liczba dozwolonych procesów, ustawiając następujący parametr.

   <pre><code># Edit the configuration file
   sudo vi /etc/systemd/system.conf
   
   # Change the DefaultTasksMax
   #DefaultTasksMax=512
   DefaultTasksMax=4096
   
   #and to activate this setting
   sudo systemctl daemon-reload
   
   # test if the change was successful
   sudo systemctl --no-pager show | grep DefaultTasksMax
   </code></pre>

   Zmniejsz rozmiar zmieniony pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [niska wydajność zapisu w systemie SLES 11/12 serwery z dużą ilość pamięci RAM](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]**  Konfigurowanie chmury platformy azure netconfig zaświadczanie o kondycji klastra

   Zmień plik konfiguracji interfejsu sieciowego, jak pokazano poniżej, aby uniemożliwić usunięcie wirtualnego adresu IP (program Pacemaker kontrolować przypisania adresów VIP) wtyczki sieci chmury. Aby uzyskać więcej informacji, zobacz [SUSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633). 

   <pre><code># Edit the configuration file
   sudo vi /etc/sysconfig/network/ifcfg-eth0 
   
   # Change CLOUD_NETCONFIG_MANAGE
   # CLOUD_NETCONFIG_MANAGE="yes"
   CLOUD_NETCONFIG_MANAGE="no"
   </code></pre>

1. **[1]**  Dostęp ssh

   <pre><code>sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[2]**  Dostęp ssh

   <pre><code># insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys
   
   sudo ssh-keygen

   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[1]**  Dostęp ssh

   <pre><code># insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]**  Horyzont instalacji agentów
   
   <pre><code>sudo zypper install fence-agents
   </code></pre>

1. **[A]**  Konfigurowanie rozpoznawania nazw hostów

   Można użyć serwera DNS lub zmodyfikować/etc/hosts na wszystkich węzłach. W tym przykładzie pokazano, jak przy użyciu pliku/etc/hosts.
   Zastąp adres IP i nazwy hosta w poniższych poleceniach. Zaletą używania/etc/hosts będzie niezależnie od systemu DNS, co może być zbyt pojedynczy punkt awarii klastra.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Wstaw następujące wiersze do/etc/hosts. Zmienianie adresu IP i nazwy hosta do danego środowiska   

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]**  Zainstaluj klaster

   <pre><code>sudo ha-cluster-init
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Network address to bind to (e.g.: 192.168.1.0) [10.0.0.0] <b>Press ENTER</b>
   # Multicast address (e.g.: 239.x.x.x) [239.232.97.43] <b>Press ENTER</b>
   # Multicast port [5405] <b>Press ENTER</b>
   # SBD is already configured to use /dev/disk/by-id/scsi-36001405639245768818458b930abdf69;/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf - overwrite (y/n)? <b>n</b>
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

1. **[2]**  Dodaj węzeł do klastra

   <pre><code>sudo ha-cluster-join
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # IP address or hostname of existing node (e.g.: 192.168.1.1) []<b>10.0.0.6</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   </code></pre>

1. **[A]**  Zmień hacluster hasło do tego samego hasła

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]**  Skonfigurować corosync Użyj innego transportu i dodać wstawienia. Klaster nie działa, w przeciwnym razie.

   <pre><code>sudo vi /etc/corosync/corosync.conf
   </code></pre>

   Dodaj następującą zawartość bold do pliku, jeśli wartości nie są tam lub innej. Upewnij się zmienić token 30000 umożliwia zachowywanie konserwacji pamięci. Aby uzyskać więcej informacji, zobacz [ten artykuł dla systemu Linux] [ virtual-machines-linux-maintenance] lub [Windows][virtual-machines-windows-maintenance]. Upewnij się również usunąć mcastaddr parametru.

   <pre><code>[...]
     <b>token:          30000
     token_retransmits_before_loss_const: 10
     join:           60
     consensus:      36000
     max_messages:   20</b>
     
     interface { 
        [...] 
     }
     <b>transport:      udpu</b>
     # remove parameter mcastaddr
     <b># mcastaddr: IP</b>
   } 
   <b>nodelist {
     node {
      # IP address of <b>prod-cl1-0</b>
      ring0_addr:10.0.0.6
     }
     node {
      # IP address of <b>prod-cl1-1</b>
      ring0_addr:10.0.0.7
     } 
   }</b>
   logging {
     [...]
   }
   quorum {
        # Enable and configure quorum subsystem (default: off)
        # see also corosync.conf.5 and votequorum.5
        provider: corosync_votequorum
        <b>expected_votes: 2</b>
        <b>two_node: 1</b>
   }
   </code></pre>

   Następnie uruchom ponownie usługę corosync

   <pre><code>sudo service corosync restart
   </code></pre>

## <a name="create-azure-fence-agent-stonith-device"></a>Utwórz urządzenie pomocą metody STONITH agenta Odgradzania platformy Azure

Urządzenie pomocą metody STONITH używa nazwy głównej usługi, do autoryzacji dla Microsoft Azure. Wykonaj następujące kroki, aby utworzyć jednostkę usługi.

1. Przejdź do [https://portal.azure.com](https://portal.azure.com)
1. Otwórz blok usługi Azure Active Directory  
   Przejdź do właściwości i zanotuj nazwę katalogu. Jest to **identyfikator dzierżawy**.
1. Kliknij przycisk rejestracje aplikacji
1. Kliknij pozycję Dodaj.
1. Wprowadź nazwę, wybierz typ aplikacji "Aplikacja/interfejsu API sieci Web", wprowadź adres URL logowania (na przykład http\://localhost) i kliknij przycisk Utwórz
1. Adres URL logowania nie jest używany i może być dowolny prawidłowy adres URL
1. Wybierz nową aplikację, a następnie kliknij przycisk kluczy na karcie Ustawienia
1. Wprowadź opis nowego klucza, wybierz pozycję "Nigdy nie wygasa" i kliknij przycisk Zapisz
1. Zanotuj wartość. Jest ona używana jako **hasło** jednostki usługi
1. Zanotuj identyfikator aplikacji. Jest ona używana jako nazwa użytkownika (**Identyfikatora logowania** w poniższych krokach) jednostki usługi

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]**  Utworzyć rolę niestandardową dla agenta odgradzania

Nazwa główna usługi nie ma uprawnień do dostępu do zasobów platformy Azure, domyślnie. Musisz nadać uprawnień jednostki usługi, uruchamianie i zatrzymywanie (Cofnij ich przydział) wszystkich maszyn wirtualnych klastra. Jeśli nie utworzono jeszcze niestandardowej roli, można utworzyć za pomocą [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell) lub [wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)

Użyj zawartości dla pliku wejściowego. Należy dostosować zawartość dla Twojej subskrypcji, Zastąp c276fc76-9cd4-44c9-99a7-4fd71546436e i e91d47c4-76f3-4271-a796-21b4ecfe3624 identyfikatory subskrypcji. Jeśli masz tylko jedną subskrypcję, należy usunąć drugi wpis w AssignableScopes.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to deallocate and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/deallocate/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]**  Przypisać rolę niestandardową nazwę główną usługi

Przypisz rolę niestandardową "Linux horyzont agenta rolę" utworzonego w rozdziale ostatniego jednostki usługi. Nie używaj roli właściciel już!

1. Przejdź do [https://portal.azure.com](https://portal.azure.com)
1. Otwieranie bloku wszystkie zasoby
1. Wybierz maszynę wirtualną, w pierwszym węźle klastra
1. Kliknij przycisk kontroli dostępu (IAM)
1. Kliknij przycisk Dodaj przypisanie roli
1. Wybierz rolę "Rolę agenta Odgradzania Linux"
1. Wprowadź nazwę aplikacji, które zostały utworzone powyżej
1. Klikanie pozycji Zapisz.

Powtórz powyższe kroki dla drugiego węzła klastra.

### <a name="1-create-the-stonith-devices"></a>**[1]**  Tworzenie urządzeń pomocą metody STONITH

Po edycji uprawnień dla maszyn wirtualnych można skonfigurować urządzenia pomocą metody STONITH w klastrze.

<pre><code># replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

sudo crm configure property stonith-timeout=900
sudo crm configure property stonith-enabled=true
</code></pre>

## <a name="default-pacemaker-configuration-for-sbd"></a>Domyślna konfiguracja program Pacemaker interwencja

1. **[1]**  Korzystanie z urządzenia pomocą metody STONITH i ustawić opóźnienie ogrodzenia

<pre><code>sudo crm configure property stonith-timeout=144
sudo crm configure property stonith-enabled=true

# List the resources to find the name of the SBD device
sudo crm resource list
sudo crm resource stop stonith-sbd
sudo crm configure delete <b>stonith-sbd</b>
sudo crm configure primitive <b>stonith-sbd</b> stonith:external/sbd \
   params pcmk_delay_max="15" \
   op monitor interval="15" timeout="15"
</code></pre>

## <a name="pacemaker-configuration-for-azure-scheduled-events"></a>Program pacemaker konfiguracji dla platformy Azure zaplanowane zdarzenia

Platforma Azure oferuje [zaplanowane zdarzenia](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/scheduled-events). Zaplanowane zdarzenia są dostarczane za pośrednictwem usługi metadanych i poczekanie, aż aplikacji przygotować się do zdarzeń, takich jak zamknięcie maszyny Wirtualnej, ponowne wdrożenie maszyny Wirtualnej itd. Zasób agenta **[zdarzeń azure](https://github.com/ClusterLabs/resource-agents/pull/1161)** monitorów zaplanowanych zdarzeń platformy Azure. W przypadku wykrycia zdarzenia agent spróbuje zatrzymać wszystkie zasoby objęte wpływem maszyny wirtualnej i przenieść je do innego węzła w klastrze. Aby osiągnąć tego dodatkowe zasoby program Pacemaker musi być skonfigurowany. 

1. **[A]**  Zainstalować **zdarzeń azure** agenta. 

<pre><code>sudo zypper install resource-agents
</code></pre>

2. **[1]**  Konfigurowanie zasobów w program Pacemaker. 

<pre><code>
#Place the cluster in maintenance mode
sudo crm configure property maintenance-mode=true

#Create Pacemaker resources for the Azure agent
sudo crm configure primitive rsc_azure-events ocf:heartbeat:azure-events op monitor interval=10s
sudo crm configure clone cln_azure-events rsc_azure-events

#Take the cluster out of maintenance mode
sudo crm configure property maintenance-mode=false
</code></pre>

   > [!NOTE]
   > Po skonfigurowaniu zasobów program Pacemaker dla agenta zdarzenia platformy azure, po umieszczeniu klastra na lub z trybu konserwacji, może nastąpić komunikaty ostrzegawcze, takich jak:  
     Ostrzeżenie: w cib opcje ładowania początkowego: nieznany atrybut "hostName_  <strong>hostname</strong>"  
     Ostrzeżenie: w cib opcje ładowania początkowego: nieznany atrybut "azure-events_globalPullState"  
     Ostrzeżenie: w cib opcje ładowania początkowego: nieznany atrybut "hostName_ <strong>hostname</strong>"  
   > Można zignorować te ostrzeżenia.

## <a name="next-steps"></a>Kolejne kroki

* [Azure maszyny wirtualne, planowania i implementacji dla rozwiązania SAP][planning-guide]
* [Wdrażania maszyn wirtualnych platformy Azure dla rozwiązania SAP][deployment-guide]
* [Wdrażania systemu DBMS na maszynach wirtualnych platformy Azure dla rozwiązania SAP][dbms-guide]
* [Wysoka dostępność systemu NFS na maszynach wirtualnych platformy Azure w systemie SUSE Linux Enterprise Server][sles-nfs-guide]
* [Wysoka dostępność środowiska SAP NetWeaver na maszynach wirtualnych platformy Azure w systemie SUSE Linux Enterprise Server dla aplikacji SAP][sles-guide]
* Aby dowiedzieć się, jak zadbać o wysokiej dostępności i plan odzyskiwania po awarii oprogramowania SAP Hana na maszynach wirtualnych platformy Azure, zobacz [wysokiej dostępności dla oprogramowania SAP HANA w usłudze Azure Virtual Machines (VMs)][sap-hana-ha]

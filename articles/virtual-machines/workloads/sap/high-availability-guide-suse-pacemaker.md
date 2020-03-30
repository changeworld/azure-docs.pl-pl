---
title: Konfigurowanie rozrusznika serca na SLES na platformie Azure | Dokumenty firmy Microsoft
description: Konfigurowanie rozrusznika serca na serwerze SUSE Linux Enterprise Server na platformie Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/17/2020
ms.author: radeltch
ms.openlocfilehash: 9d3d0ddbd1282827f17cd82228fcf0f3fba3a60f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471986"
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>Konfigurowanie rozrusznika serca na serwerze SUSE Linux Enterprise Server na platformie Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[virtual-machines-windows-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[sles-nfs-guide]:high-availability-guide-suse-nfs.md
[sles-guide]:high-availability-guide-suse.md

Istnieją dwie opcje konfigurowania klastra rozrusznika serca na platformie Azure. Można użyć agenta ogrodzenia, który zajmuje się ponownym uruchomieniem węzła nie powiodło się za pośrednictwem interfejsów API platformy Azure lub można użyć urządzenia SBD.

Urządzenie SBD wymaga co najmniej jednej dodatkowej maszyny wirtualnej, która działa jako serwer docelowy iSCSI i zapewnia urządzenie SBD. Te serwery docelowe iSCSI mogą być jednak współużytkowane innym klastrom rozrusznika serca. Zaletą korzystania z urządzenia SBD jest krótszy czas pracy awaryjnej, a w przypadku korzystania z urządzeń SBD lokalnie, nie wymaga żadnych zmian w sposobie obsługi klastra rozrusznika serca. Można użyć maksymalnie trzech urządzeń SBD dla klastra rozrusznika serca, aby umożliwić urządzenie SBD stają się niedostępne, na przykład podczas instalowania poprawek serwera docelowego iSCSI. Jeśli chcesz używać więcej niż jednego urządzenia SBD na rozrusznik serca, należy wdrożyć wiele serwerów docelowych iSCSI i podłączyć jeden identyfikator SBD z każdego serwera docelowego iSCSI. Zalecamy użycie jednego urządzenia SBD lub trzech. Rozrusznik serca nie będzie mógł automatycznie ogrodzić węzła klastra, jeśli skonfigurujesz tylko dwa urządzenia SBD, a jedno z nich nie jest dostępne. Jeśli chcesz być w stanie ogrodzić, gdy jeden serwer docelowy iSCSI jest w dół, musisz użyć trzech urządzeń SBD, a zatem trzy serwery docelowe iSCSI.

Jeśli nie chcesz inwestować w jedną dodatkową maszynę wirtualną, możesz również użyć agenta usługi Azure Fence. Wadą jest to, że przewija się w stan failover może potrwać od 10 do 15 minut, jeśli zatrzymanie zasobu nie powiedzie się lub węzły klastra nie mogą komunikować się, które siebie nawzajem.

![Pacemaker na przeglądzie SLES](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

>[!IMPORTANT]
> Podczas planowania i wdrażania węzłów klastrowych i urządzeń SBD z systemem Linux Pacemaker istotne jest, aby ogólna niezawodność pełnej konfiguracji klastra powodowała, że routing między zaangażowanymi maszynami wirtualnymi a maszynami wirtualnymi obsługującymi urządzenia SBD nie przechodzi przez żadne inne urządzenia, takie jak [urządzenia NVA.](https://azure.microsoft.com/solutions/network-appliances/) W przeciwnym razie problemy i zdarzenia konserwacji z urządzenia WUS może mieć negatywny wpływ na stabilność i niezawodność ogólnej konfiguracji klastra. Aby uniknąć takich przeszkód, nie należy definiować reguł routingu nas lub [reguł routingu zdefiniowanego przez użytkownika,](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) które kierują ruch między węzłami klastrowanymi i urządzeniami SBD za pośrednictwem urządzeń NVA i podobnych urządzeń podczas planowania i wdrażania węzłów klastrowych i urządzeń SBD z systemem Linux Pacemaker. 
>

## <a name="sbd-fencing"></a>Ogrodzenia SBD

Wykonaj następujące kroki, jeśli chcesz używać urządzenia SBD do szermierki.

### <a name="set-up-iscsi-target-servers"></a>Konfigurowanie serwerów docelowych iSCSI

Najpierw należy utworzyć docelowe maszyny wirtualne iSCSI. Serwery docelowe iSCSI mogą być współużytkowane z wieloma klastrami stymulatorów.

1. Wdrażanie nowych maszyn wirtualnych SLES 12 SP1 lub nowszych i łączenie się z nimi za pośrednictwem ssh. Maszyny nie muszą być duże. Rozmiar maszyny wirtualnej, takich jak Standard_E2s_v3 lub Standard_D2s_v3 jest wystarczająca. Upewnij się, że dysk systemu operacyjnego w wersji premium jest używany.

Uruchom następujące polecenia na wszystkich **maszynach wirtualnych docelowych iSCSI**.

1. Aktualizacja SLES

   <pre><code>sudo zypper update
   </code></pre>

   > [!NOTE]
   > Może być konieczne ponowne uruchomienie systemu operacyjnego po uaktualnieniu lub zaktualizowaniu systemu operacyjnego. 

1. Usuwanie pakietów

   Aby uniknąć znanego problemu z targetcli i SLES 12 SP3, odinstaluj następujące pakiety. Można zignorować błędy dotyczące pakietów, których nie można odnaleźć

   <pre><code>sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>

1. Instalowanie pakietów docelowych iSCSI

   <pre><code>sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. Włączanie usługi docelowej iSCSI

   <pre><code>sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>Tworzenie urządzenia iSCSI na serwerze docelowym iSCSI

Uruchom następujące polecenia na wszystkich **docelowych maszynach wirtualnych iSCSI,** aby utworzyć dyski iSCSI dla klastrów używanych przez systemy SAP. W poniższym przykładzie tworzone są urządzenia SBD dla wielu klastrów. Pokazuje, jak można użyć jednego serwera docelowego iSCSI dla wielu klastrów. Urządzenia SBD są umieszczane na dysku systemu operacyjnego. Upewnij się, że masz wystarczająco dużo miejsca.

**`nfs`** służy do identyfikacji klastra NFS, **ascsnw1** służy do identyfikacji klastra ASCS **NW1**, **dbnw1** służy do identyfikacji klastra bazy danych **NW1**, **nfs-0** i **nfs-1** są nazwami hostów węzłów klastra NFS, **nw1-xscs-0** i **nw1-xscs-1** są nazwami hostów węzłów klastra **NW1** ASCS, a **nw1-db-0** i **nw1-db-1** są nazwami hostów węzłów klastra bazy danych. Wymień je na nazwy hostów węzłów klastra i identyfikator SID systemu SAP.

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

Możesz sprawdzić, czy wszystko zostało prawidłowo skonfigurowane

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

### <a name="set-up-sbd-device"></a>Konfigurowanie urządzenia SBD

Połącz się z urządzeniem iSCSI utworzonym w ostatnim kroku z klastra.
Uruchom następujące polecenia w węzłach nowego klastra, który chcesz utworzyć.
Następujące elementy są poprzedzone **[A]** - ma zastosowanie do wszystkich węzłów, **[1]** - dotyczy tylko węzła 1 lub **[2]** - dotyczy tylko węzła 2.

1. **[A]** Łączenie się z urządzeniami iSCSI

   Najpierw włącz usługi iSCSI i SBD.

   <pre><code>sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]** Zmiana nazwy inicjatora w pierwszym węźle

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Zmień zawartość pliku, aby była zgodna z listami ACL używanymi podczas tworzenia urządzenia iSCSI na serwerze docelowym iSCSI, na przykład dla serwera NFS.

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-0.local:nfs-0</b>
   </code></pre>

1. **[2]** Zmiana nazwy inicjatora w drugim węźle

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Zmienianie zawartości pliku w celu dopasowania list ACL użytych podczas tworzenia urządzenia iSCSI na serwerze docelowym iSCSI

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-1.local:nfs-1</b>
   </code></pre>

1. **[A]** Ponowne uruchamianie usługi iSCSI

   Teraz uruchom ponownie usługę iSCSI, aby zastosować zmianę

   <pre><code>sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   Podłącz urządzenia iSCSI. W poniższym przykładzie 10.0.0.17 jest adresem IP serwera docelowego iSCSI, a 3260 jest portem domyślnym. <b>iqn.2006-04.nfs.local:nfs</b> jest jedną z nazw docelowych, która jest wymieniona po uruchomieniu pierwszego polecenia poniżej (iscsiadm -m discovery).

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

   Upewnij się, że urządzenia iSCSI są dostępne i zanotuj nazwę urządzenia (w poniższym przykładzie /dev/sde)

   <pre><code>lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdd</b>
   # <b>[7:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sde</b>
   # <b>[8:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdf</b>
   </code></pre>

   Teraz pobierz identyfikatory urządzeń iSCSI.

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

   Lista poleceń trzech identyfikatorów urządzeń dla każdego urządzenia SBD. Zalecamy użycie identyfikatora, który zaczyna się od scsi-3, w powyższym przykładzie jest to

   * **/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03**
   * **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**
   * **/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf**

1. **[1]** Tworzenie urządzenia SBD

   Użyj identyfikatora urządzenia iSCSI, aby utworzyć nowe urządzenia SBD w pierwszym węźle klastra.

   <pre><code>sudo sbd -d <b>/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03</b> -1 60 -4 120 create

   # Also create the second and third SBD devices if you want to use more than one.
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 60 -4 120 create
   sudo sbd -d <b>/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf</b> -1 60 -4 120 create
   </code></pre>

1. **[A]** Dostosowanie konfiguracji SBD

   Otwieranie pliku konfiguracyjnego SBD

   <pre><code>sudo vi /etc/sysconfig/sbd
   </code></pre>

   Zmień właściwość urządzenia SBD, włącz integrację rozrusznika serca i zmień tryb uruchamiania SBD.

   <pre><code>[...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   [...]
   </code></pre>

   Tworzenie `softdog` pliku konfiguracyjnego

   <pre><code>echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   Teraz załaduj moduł

   <pre><code>sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>Instalacja klastra

Następujące elementy są poprzedzone **[A]** - ma zastosowanie do wszystkich węzłów, **[1]** - dotyczy tylko węzła 1 lub **[2]** - dotyczy tylko węzła 2.

1. **[A]** Aktualizacja SLES

   <pre><code>sudo zypper update
   </code></pre>

1. **[A]** Zainstaluj składnik, potrzebny do zasobów klastra

   <pre><code>sudo zypper in socat
   </code></pre>

1. **[A]** Instalowanie składnika azure-lb, potrzebnego do zasobów klastra

   <pre><code>sudo zypper in resource-agents
   </code></pre>

   > [!NOTE]
   > Sprawdź wersję agentów zasobów pakietu i upewnij się, że są spełnione minimalne wymagania dotyczące wersji:  
   > - W przypadku SLES 12 SP4/SP5 wersja musi być co najmniej resource-agents-4.3.018.a7fb5035-3.30.1.  
   > - Dla SLES 15/15 SP1 wersja musi być co najmniej resource-agents-4.3.0184.6ee15eb2-4.13.1.  

1. **[A]** Konfigurowanie systemu operacyjnego

   W niektórych przypadkach rozrusznik tworzy wiele procesów, a tym samym wyczerpuje dozwoloną liczbę procesów. W takim przypadku puls między węzłami klastra może zakończyć się niepowodzeniem i prowadzić do pracy awaryjnej zasobów. Zaleca się zwiększenie maksymalnej dozwolonej liczby procesów, ustawiając następujący parametr.

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

   Zmniejsz rozmiar brudnej pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [Niska wydajność zapisu na serwerach SLES 11/12 z dużą pamięcią RAM](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]** Konfigurowanie klastra usługi ha w chmurze

   Zmień plik konfiguracyjny interfejsu sieciowego, jak pokazano poniżej, aby zapobiec usunięciu wirtualnego adresu IP przez wtyczkę sieci w chmurze (rozrusznik serca musi kontrolować przypisanie VIP). Aby uzyskać więcej informacji, zobacz [SUSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633). 

   <pre><code># Edit the configuration file
   sudo vi /etc/sysconfig/network/ifcfg-eth0 
   
   # Change CLOUD_NETCONFIG_MANAGE
   # CLOUD_NETCONFIG_MANAGE="yes"
   CLOUD_NETCONFIG_MANAGE="no"
   </code></pre>

1. **[1]** Włącz dostęp do ssh

   <pre><code>sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[2]** Włącz dostęp do ssh

   <pre><code>
   sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys   
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[1]** Włącz dostęp do ssh

   <pre><code># insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]** Instalowanie agentów ogrodzenia
   
   <pre><code>sudo zypper install fence-agents
   </code></pre>

   >[!IMPORTANT]
   > Jeśli używasz Suse Linux Enterprise Server dla SAP 15, należy pamiętać, że należy aktywować dodatkowy moduł i zainstalować dodatkowy składnik, który jest warunkiem wstępnym do korzystania z usługi Azure Fence Agent. Aby dowiedzieć się więcej o modułach i rozszerzeniach SUSE, zobacz [Moduły i rozszerzenia wyjaśnione](https://www.suse.com/documentation/sles-15/singlehtml/art_modules/art_modules.html). Postępuj zgodnie z instrukcjami poniżej, aby zainstalować zestaw SDK języka Azure Python. 

   Poniższe instrukcje dotyczące instalowania pakietu Azure Python SDK mają zastosowanie tylko do suse Enterprise Server dla SAP **15.**  

    - Jeśli korzystasz z subskrypcji Bring-Your-Own-Subscription, postępuj zgodnie z tymi instrukcjami  

    <pre><code>
    #Activate module PackageHub/15/x86_64
    sudo SUSEConnect -p PackageHub/15/x86_64
    #Install Azure Python SDK
    sudo zypper in python3-azure-sdk
    </code></pre>

     - Jeśli korzystasz z subskrypcji płatności zgodnie z rzeczywistym użyciem, postępuj zgodnie z tymi instrukcjami  

    <pre><code>#Activate module PackageHub/15/x86_64
    zypper ar https://download.opensuse.org/repositories/openSUSE:/Backports:/SLE-15/standard/ SLE15-PackageHub
    #Install Azure Python SDK
    sudo zypper in python3-azure-sdk
    </code></pre>

1. **[A]** Rozpoznawanie nazw hostów instalatora

   Można użyć serwera DNS lub zmodyfikować /etc/hosts we wszystkich węzłach. W tym przykładzie pokazano, jak używać pliku /etc/hosts.
   Zastąp adres IP i nazwa hosta w następujących poleceniach. Zaletą korzystania /etc/hosts jest to, że klaster staje się niezależny od DNS, który może być pojedynczym punktem awarii.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Wstaw następujące wiersze do /etc/hosts. Zmienianie adresu IP i nazwy hosta w celu dopasowania go do środowiska   

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]** Instalowanie klastra

   <pre><code>sudo ha-cluster-init -u
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Address for ring0 [10.0.0.6] <b>Press ENTER</b>
   # Port for ring0 [5405] <b>Press ENTER</b>
   # SBD is already configured to use /dev/disk/by-id/scsi-36001405639245768818458b930abdf69;/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf - overwrite (y/n)? <b>n</b>
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

1. **[2]** Dodawanie węzła do klastra

   <pre><code>sudo ha-cluster-join
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # IP address or hostname of existing node (e.g.: 192.168.1.1) []<b>10.0.0.6</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   </code></pre>

1. **[A]** Zmień hasło hacluster na to samo hasło

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** Dostosuj ustawienia corosync.  

   <pre><code>sudo vi /etc/corosync/corosync.conf
   </code></pre>

   Dodaj następującą pogrubienie zawartości do pliku, jeśli wartości nie istnieją lub są różne. Upewnij się, aby zmienić token na 30000, aby umożliwić zachowanie obsługi pamięci. Aby uzyskać więcej informacji, zobacz [ten artykuł dla systemu Linux][virtual-machines-linux-maintenance] lub [Windows][virtual-machines-windows-maintenance].

   <pre><code>[...]
     <b>token:          30000
     token_retransmits_before_loss_const: 10
     join:           60
     consensus:      36000
     max_messages:   20</b>
     
     interface { 
        [...] 
     }
     transport:      udpu
   } 
   nodelist {
     node {
      ring0_addr:10.0.0.6
     }
     node {
      ring0_addr:10.0.0.7
     } 
   }
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

## <a name="create-azure-fence-agent-stonith-device"></a>Tworzenie urządzenia STONITH agenta ogrodzenia platformy Azure

Urządzenie STONITH używa jednostki usługi do autoryzacji na platformie Microsoft Azure. Wykonaj następujące kroki, aby utworzyć jednostkę usługi.

1. Przejdź do strony <https://portal.azure.com>
1. Otwieranie bloku usługi Azure Active Directory  
   Przejdź do właściwości i zapisz identyfikator katalogu. Jest to **identyfikator dzierżawy**.
1. Kliknij pozycję Rejestracje aplikacji
1. Kliknij pozycję Nowa rejestracja
1. Wprowadź nazwę, wybierz opcję "Konta tylko w tym katalogu organizacji" 
2. Wybierz typ aplikacji "Web", wprowadź adres URL logowania\/(na przykład http: /localhost) i kliknij przycisk Dodaj  
   Adres URL logowania nie jest używany i może być dowolnym prawidłowym adresem URL
1. Wybierz pozycję Certyfikaty i wpisy tajne, a następnie kliknij pozycję Nowy klucz tajny klienta
1. Wprowadź opis nowego klucza, wybierz "Nigdy nie wygasa" i kliknij przycisk Dodaj
1. Zapisz wartość. Jest on używany jako **hasło** dla jednostki usługi
1. Wybierz pozycję Przegląd. Zapisz identyfikator aplikacji. Jest on używany jako nazwa użytkownika **(identyfikator logowania** w poniższych krokach) jednostki usługi

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** Tworzenie niestandardowej roli agenta ogrodzenia

Podmiot usługi nie ma uprawnień do uzyskiwania dostępu do zasobów platformy Azure domyślnie. Należy nadać service principal uprawnienia do uruchamiania i zatrzymywania (cofnięto przydział) wszystkie maszyny wirtualne klastra. Jeśli rola niestandardowa nie została jeszcze utworzona, można ją utworzyć przy użyciu [programu PowerShell](https://docs.microsoft.com/azure/role-based-access-control/custom-roles-powershell#create-a-custom-role) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles-cli)

Użyj następującej zawartości dla pliku wejściowego. Musisz dostosować zawartość do subskrypcji, czyli zastąpić c276fc76-9cd4-44c9-99a7-4fd71546436e i e91d47c4-76f3-4271-a796-21b4ecfe3624 z identyfikatorami subskrypcji. Jeśli masz tylko jedną subskrypcję, usuń drugi wpis w AssignableScopes.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to deallocate and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/deallocate/action",
    "Microsoft.Compute/virtualMachines/start/action", 
    "Microsoft.Compute/virtualMachines/powerOff/action" 
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** Przypisz rolę niestandardową do jednostki usługi

Przypisz rolę niestandardową "Rola agenta ogrodzenia linuksa", która została utworzona w ostatnim rozdziale do jednostki usługi. Nie używaj już roli Właściciel!

1. Przejdź do[https://portal.azure.com](https://portal.azure.com)
1. Otwórz ostrze Wszystkie zasoby
1. Wybieranie maszyny wirtualnej pierwszego węzła klastra
1. Kliknij pozycję Kontrola dostępu (IAM)
1. Kliknij pozycję Dodaj przypisanie roli
1. Wybierz rolę "Rola agenta ogrodzenia linuksa"
1. Wprowadź nazwę aplikacji utworzonej powyżej
1. Klikanie pozycji Zapisz.

Powtórz powyższe kroki dla drugiego węzła klastra.

### <a name="1-create-the-stonith-devices"></a>**[1]** Tworzenie urządzeń STONITH

Po edycji uprawnień dla maszyn wirtualnych można skonfigurować urządzenia STONITH w klastrze.

<pre><code># replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

sudo crm configure property stonith-timeout=900
sudo crm configure property stonith-enabled=true
</code></pre>

## <a name="default-pacemaker-configuration-for-sbd"></a>Domyślna konfiguracja rozrusznika serca dla SBD

1. **[1]** Włącz użycie urządzenia STONITH i ustaw opóźnienie ogrodzenia

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

## <a name="pacemaker-configuration-for-azure-scheduled-events"></a>Konfiguracja rozrusznika dla zaplanowanych zdarzeń platformy Azure

Platforma Azure oferuje [zaplanowane zdarzenia](https://docs.microsoft.com/azure/virtual-machines/linux/scheduled-events). Zaplanowane zdarzenia są dostarczane za pośrednictwem usługi metadanych i umożliwiają aplikacji przygotowanie się do zdarzeń, takich jak zamknięcie maszyny Wirtualnej, ponowne wdrożenie maszyny Wirtualnej itp. Agent zasobów **[azure-events](https://github.com/ClusterLabs/resource-agents/pull/1161)** monitoruje zaplanowane zdarzenia platformy Azure. Jeśli zostaną wykryte zdarzenia, agent spróbuje zatrzymać wszystkie zasoby na dotkniętej maszynie wirtualnej i przenieść je do innego węzła w klastrze. Aby osiągnąć ten dodatkowy rozrusznik zasobów musi być skonfigurowany. 

1. **[A]** Upewnij się, że pakiet dla **agenta zdarzeń platformy Azure** jest już zainstalowany i aktualny. 

<pre><code>sudo zypper info resource-agents
</code></pre>

2. **[1]** Skonfiguruj zasoby w rozruszniku serca. 

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
   > Po skonfigurowaniu zasobów rozrusznika dla agenta zdarzeń platformy Azure, po umieszczeniu klastra w trybie konserwacji lub poza to mogą być otrzymywać komunikaty ostrzegawcze, takie jak:  
     OSTRZEŻENIE: cib-bootstrap-options: nieznany atrybut 'hostName_ <strong>nazwa hosta</strong>'  
     OSTRZEŻENIE: cib-bootstrap-options: nieznany atrybut 'azure-events_globalPullState'  
     OSTRZEŻENIE: cib-bootstrap-options: nieznany atrybut 'hostName_ <strong>nazwa hosta</strong>'  
   > Te komunikaty ostrzegawcze mogą być ignorowane.

## <a name="next-steps"></a>Następne kroki

* [Planowanie i implementacja maszyn wirtualnych platformy Azure dla systemu SAP][planning-guide]
* [Wdrożenie maszyn wirtualnych platformy Azure dla systemu SAP][deployment-guide]
* [Wdrożenie usługi DBMS maszyn wirtualnych platformy Azure dla systemu SAP][dbms-guide]
* [Wysoka dostępność systemu plików NFS na maszynach wirtualnych platformy Azure na serwerze SUSE Linux Enterprise Server][sles-nfs-guide]
* [Wysoka dostępność sap NetWeaver na maszynach wirtualnych platformy Azure na suse Linux Enterprise Server dla aplikacji SAP][sles-guide]
* Aby dowiedzieć się, jak ustalić wysoką dostępność i plan odzyskiwania po awarii sap HANA na maszynach wirtualnych platformy Azure, zobacz [Wysoka dostępność SAP HANA na maszynach wirtualnych platformy Azure (VM)][sap-hana-ha]

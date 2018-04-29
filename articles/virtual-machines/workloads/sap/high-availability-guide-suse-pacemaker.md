---
title: Konfigurowanie rozrusznik w systemie SUSE Linux Enterprise Server na platformie Azure | Dokumentacja firmy Microsoft
description: Konfigurowanie rozrusznik w systemie SUSE Linux Enterprise Server na platformie Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/20/2018
ms.author: sedusch
ms.openlocfilehash: c82380c20c9ec631d9fea338404a25f167277701
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>Konfigurowanie rozrusznik w systemie SUSE Linux Enterprise Server na platformie Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md

Dostępne są dwie opcje do skonfigurowania klastra rozrusznik na platformie Azure. Możesz użyć agenta ogrodzenia, która zajmuje się ponownym uruchomieniu węzła nie powiodło się za pośrednictwem interfejsów API usługi Azure lub urządzenia interwencja można użyć.

Urządzenie interwencja wymaga jednego dodatkowe maszyny wirtualnej, która pełni funkcję serwera obiektów docelowych iSCSI i udostępnia interwencja urządzenia. Ten serwer obiektów docelowych iSCSI może jednak być współużytkowane z innymi klastrami rozrusznik. Zaletą korzystania z urządzenia interwencja jest krótszy czas pracy awaryjnej i, jeśli używasz interwencja urządzeniami lokalnymi, nie wymaga żadnych zmian w sposób działania klastra rozrusznik. Ogrodzenia interwencja można nadal używać agent odgradzający Azure jako kopia zapasowa ogrodzeń mechanizmu w przypadku, gdy serwer obiektów docelowych iSCSI nie jest dostępna.

Jeśli nie chcesz zainwestować w dodatkowe maszyny wirtualne, również służy agent Odgradzający Azure. Wadą podwyższonego jest, że trybu failover może zająć od 10 do 15 minut, jeśli stop zasobu nie powiedzie się lub węzłów klastra nie może komunikować się który wzajemnie już.

![Rozrusznik na SLES — omówienie](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

## <a name="sbd-fencing"></a>Interwencja ogrodzenia

Jeśli chcesz użyć urządzenia interwencja dla ogrodzenia, wykonaj następujące kroki.

### <a name="set-up-an-iscsi-target-server"></a>Konfigurowanie serwera obiektów docelowych iSCSI

Najpierw należy utworzyć iSCSI docelowej maszyny wirtualnej, jeśli nie masz już. serwery obiektów docelowych iSCSI można udostępniać wielu klastrów rozrusznik.

1. Wdróż nowe SLES 12 z dodatkiem SP1 lub nowszej maszyny wirtualnej i połączyć się z komputerem za pośrednictwem ssh. Maszyny nie musi być duży. Rozmiar maszyny wirtualnej, takie jak Standard_E2s_v3 lub Standard_D2s_v3 jest wystarczająca.

1. SLES aktualizacji

   <pre><code>
   sudo zypper update
   </code></pre>

1. Usuwanie pakietów

   Aby uniknąć znany problem z targetcli i SLES 12 z dodatkiem SP3, należy odinstalować następujące pakiety. Można zignorować błędy dotyczące pakietów, których nie można znaleźć
   
   <pre><code>
   sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>
   
1. Zainstaluj pakiety docelowego iSCSI

   <pre><code>
   sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. Włącz usługę obiektu docelowego iSCSI

   <pre><code>   
   sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>Utwórz urządzenie iSCSI na serwerze docelowym iSCSI

Dołączanie nowego dysku danych do docelowej maszyny wirtualnej iSCSI może służyć do tego klastra. Dysk z danymi mogą być jak 1 GB i muszą znajdować się na konto magazynu w warstwie Premium lub dysku zarządzane Premium, aby korzystać z [umowy SLA dla maszyny Wirtualnej z jednym](https://azure.microsoft.com/support/legal/sla/virtual-machines).

Uruchom następujące polecenie **obiektów docelowych iSCSI maszyny Wirtualnej** można utworzyć dysku iSCSI dla nowego klastra. W poniższym przykładzie **cl1** służy do identyfikowania nowego klastra i **produkcyjną cl1 0** i **produkcyjną cl1 1** są nazwy hostów z węzłów klastra. Zamień je nazwy hostów węzły klastra.

<pre><code>
# List all data disks with the following command
sudo ls -al /dev/disk/azure/scsi1/

# total 0
# drwxr-xr-x 2 root root  80 Mar 26 14:42 .
# drwxr-xr-x 3 root root 160 Mar 26 14:42 ..
# lrwxrwxrwx 1 root root  12 Mar 26 14:42 lun0 -> ../../../<b>sdc</b>
# lrwxrwxrwx 1 root root  12 Mar 26 14:42 lun1 -> ../../../sdd

# Then use the disk name to list the disk id
sudo ls -l /dev/disk/by-id/scsi-* | grep sdc

# lrwxrwxrwx 1 root root  9 Mar 26 14:42 /dev/disk/by-id/scsi-14d53465420202020a50923c92babda40974bef49ae8828f0 -> ../../sdc
# lrwxrwxrwx 1 root root  9 Mar 26 14:42 <b>/dev/disk/by-id/scsi-360022480a50923c92babef49ae8828f0 -> ../../sdc</b>

# Use the data disk that you attached for this cluster to create a new backstore
sudo targetcli backstores/block create <b>cl1</b> <b>/dev/disk/by-id/scsi-360022480a50923c92babef49ae8828f0</b>

sudo targetcli iscsi/ create iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/luns/ create /backstores/block/<b>cl1</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/acls/ create iqn.2006-04.<b>prod-cl1-0.local:prod-cl1-0</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/acls/ create iqn.2006-04.<b>prod-cl1-1.local:prod-cl1-1</b>

# save the targetcli changes
sudo targetcli saveconfig
</code></pre>

### <a name="set-up-sbd-device"></a>Konfigurowanie urządzenia interwencja

Nawiąż połączenie z urządzeniem iSCSI, który został utworzony w ostatnim kroku z klastra.
Uruchom następujące polecenia w węzłach nowego klastra, który chcesz utworzyć.
Następujące elementy są poprzedzane prefiksem albo **[A]** — mające zastosowanie do wszystkich węzłów, **[1]** — dotyczy to tylko węzła 1 lub **[2]** — dotyczy to tylko węzeł 2.

1. **[A]**  Połącz z urządzenia iSCSI

   Najpierw włączyć usługi interwencja i iSCSI.

   <pre><code>
   sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]**  Zmienić nazwę inicjatora w pierwszym węźle

   <pre><code>
   sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Zmienić zawartość pliku do dopasowania listy ACL zostały użyte podczas tworzenia urządzenia iSCSI na serwerze docelowym iSCSI

   <pre><code>   
   InitiatorName=<b>iqn.2006-04.prod-cl1-0.local:prod-cl1-0</b>
   </code></pre>

1. **[2]**  Zmień nazwę inicjatora na drugi węzeł

   <pre><code>
   sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Zmienić zawartość pliku do dopasowania listy ACL zostały użyte podczas tworzenia urządzenia iSCSI na serwerze docelowym iSCSI

   <pre><code>
   InitiatorName=<b>iqn.2006-04.prod-cl1-1.local:prod-cl1-1</b>
   </code></pre>

1. **[A]**  Ponowne uruchomienie usługi iSCSI

   Teraz ponownie uruchomić usługę iSCSI, aby zastosować zmiany
   
   <pre><code>
   sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   Podłącz urządzenia iSCSI. W poniższym przykładzie 10.0.0.17 to adres IP serwera docelowego iSCSI i 3260 jest domyślnym portem. <b>IQN.2006 04.cl1.local:cl1</b> jest nazwa docelowej, która znajduje się po uruchomieniu polecenia pierwszy.

   <pre><code>
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>
   
   sudo iscsiadm -m node -T <b>iqn.2006-04.cl1.local:cl1</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   Upewnij się, że urządzenia iSCSI jest dostępny i zanotuj gotowe nazwa urządzenia (w następujący przykład/dev/które integrują usługi)

   <pre><code>
   lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  cl1              4.0   /dev/sde</b>
   </code></pre>

   Teraz pobrać identyfikator urządzenia iSCSI.

   <pre><code>
   ls -l /dev/disk/by-id/scsi-* | grep <b>sde</b>
   
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-1LIO-ORG_cl1:3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   # <b>lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df -> ../../sde</b>
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-SLIO-ORG_cl1_3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   </code></pre>

   Polecenie listy trzy identyfikatory urządzeń. Firma Microsoft zaleca się, że za pomocą Identyfikatora, który rozpoczyna się od scsi-3, w tym przykładzie powyżej tego jest
   
   **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**

1. **[1]**  Utworzenia urządzenia interwencja

   Identyfikator urządzenia urządzenia iSCSI umożliwia utworzenie nowego urządzenia interwencja na pierwszym węźle klastra.

   <pre><code>
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 10 -4 20 create
   </code></pre>

1. **[A]**  Dostosowanie konfiguracji interwencja

   Otwórz plik konfiguracji interwencja

   <pre><code>
   sudo vi /etc/sysconfig/sbd
   </code></pre>

   Zmień wartość właściwości urządzenia interwencja, Włącz integrację rozrusznik i zmienić trybu uruchamiania interwencja.

   <pre><code>
   [...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   </code></pre>

   Utwórz plik konfiguracji softdog

   <pre><code>
   echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   Teraz załadować modułu

   <pre><code>
   sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>Instalacji klastra

Następujące elementy są poprzedzane prefiksem albo **[A]** — mające zastosowanie do wszystkich węzłów, **[1]** — dotyczy to tylko węzła 1 lub **[2]** — dotyczy to tylko węzeł 2.

1. **[A]**  Zaktualizować SLES

   <pre><code>
   sudo zypper update
   </code></pre>

1. **[1]**  Dostęp ssh

   <pre><code>
   sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

2. **[2]**  Dostęp ssh

   <pre><code>
   sudo ssh-keygen
   
   # insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key   
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[1]**  Dostęp ssh

   <pre><code>
   # insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]**  Zainstalować HA rozszerzenia
   
   <pre><code>
   sudo zypper install sle-ha-release fence-agents
   </code></pre>

1. **[A]**  Instalatora rozpoznawania nazwy hosta   

   Można użyć serwera DNS lub zmodyfikować/etc/hosts na wszystkich węzłach. Ten przykład przedstawia sposób użycia pliku/etc/hosts.
   Zastąp adres IP i nazwy hosta w następujących poleceń. Zaletą używania/etc/hosts jest niezależna od DNS, który może być zbyt pojedynczego punktu awarii klastra.

   <pre><code>
   sudo vi /etc/hosts
   </code></pre>
   
   Wstaw następujące wiersze do/etc/hosts. Zmienianie adresu IP i nazwy hosta do danego środowiska   
   
   <pre><code>
   # IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]**  Instalacji klastra
   
   <pre><code>
   sudo ha-cluster-init
   
   # Do you want to continue anyway? [y/N] -> y
   # Network address to bind to (for example: 192.168.1.0) [10.79.227.0] -> Press ENTER
   # Multicast address (for example: 239.x.x.x) [239.174.218.125] -> Press ENTER
   # Multicast port [5405] -> Press ENTER
   # Do you wish to configure an administration IP? [y/N] -> N
   </code></pre>

1. **[2]**  Dodaj węzeł do klastra
   
   <pre><code> 
   sudo ha-cluster-join
   
   # Do you want to continue anyway? [y/N] -> y
   # IP address or hostname of existing node (for example: 192.168.1.1) [] -> IP address of node 1 for example 10.0.0.14
   # /root/.ssh/id_rsa already exists - overwrite? [y/N] N
   </code></pre>

1. **[A]**  Zmień hacluster hasło do tego samego hasła

   <pre><code> 
   sudo passwd hacluster
   </code></pre>

1. **[A]**  Skonfigurować corosync Użyj innego transportu, a następnie dodaj wstawienia. Klaster nie działa, w przeciwnym razie wartość.
   
   <pre><code> 
   sudo vi /etc/corosync/corosync.conf   
   </code></pre>

   Dodaj następującą zawartość bold do pliku, jeśli wartości nie są występują lub innych.
   
   <pre><code> 
   [...]
     <b>token:          5000
     token_retransmits_before_loss_const: 10
     join:           60
     consensus:      6000
     max_messages:   20</b>
     
     interface { 
        [...] 
     }
     <b>transport:      udpu</b>
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

   <pre><code>
   sudo service corosync restart
   </code></pre>

1. **[1]**  Zmiany ustawień domyślnych rozrusznik

   <pre><code>
   sudo crm configure rsc_defaults resource-stickiness="1"   
   </code></pre>

## <a name="create-stonith-device"></a>Utwórz urządzenie STONITH

Urządzenie STONITH używa nazwy głównej usługi do autoryzacji przed Microsoft Azure. Wykonaj następujące kroki, aby utworzyć nazwy głównej usługi.

1. Przejdź do strony <https://portal.azure.com>
1. Otwarcie bloku usługi Azure Active Directory  
   Przejdź do właściwości i zanotuj nazwę katalogu. Jest to **Identyfikatorem dzierżawy**.
1. Kliknij przycisk rejestracji aplikacji
1. Kliknij pozycję Dodaj.
1. Wprowadź nazwę, wybierz typ aplikacji "Aplikacja/interfejs API sieci Web", wprowadź adres URL logowania (na przykład http://localhost) i kliknij przycisk Utwórz
1. Adres URL logowania nie jest używany i może być dowolny prawidłowy adres URL
1. Wybierz nową aplikację i kliknij na karcie Ustawienia
1. Wprowadź opis nowego klucza, wybierz pozycję "Nigdy nie wygasa" i kliknij przycisk Zapisz
1. Zanotuj wartość. Jest on używany jako **hasło** główną usługi
1. Zanotuj identyfikator aplikacji. Jest on używany jako nazwa użytkownika (**Identyfikatora** w poniższych krokach) główną usługi

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]**  Utworzyć niestandardową rolę agenta odgradzania

Nazwy głównej usługi nie ma uprawnień do domyślnie dostęp do zasobów platformy Azure. Należy podać nazwę główną usługi uprawnień do uruchamiania i zatrzymywania (deallocate) wszystkich maszyn wirtualnych klastra. Jeśli rola niestandardowa nie został jeszcze utworzony, można utworzyć za pomocą [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell#create-a-custom-role) lub [wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli#create-a-custom-role)

Użyj zawartości pliku wejściowego. Należy dostosować zawartość do subskrypcji, Zastąp c276fc76-9cd4-44c9-99a7-4fd71546436e i e91d47c4-76f3-4271-a796-21b4ecfe3624 identyfikatorów subskrypcji. Jeśli masz tylko jedną subskrypcję, usuń drugi wpis w AssignableScopes.

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

### <a name="1-assign-the-custom-role-to-the-service-principal"></a>**[1]**  Przypisanie roli niestandardowych do nazwy głównej usługi

Przypisz rolę niestandardowe "Linux ogranicznika agenta roli" utworzonego w rozdziale ostatniego główną usługi. Nie używaj rolę właściciela już!

1. Przejdź do strony https://portal.azure.com
1. Otwórz wszystkie bloku zasobów
1. Wybierz maszynę wirtualną w pierwszym węźle klastra
1. Kliknij przycisk kontroli dostępu (IAM)
1. Kliknij pozycję Dodaj.
1. Wybierz rolę "Rola agenta Odgradzania Linux"
1. Wprowadź nazwę aplikacji, utworzoną wcześniej
1. Kliknij przycisk OK

Powtórz powyższe kroki dla drugiego węzła klastra.

### <a name="1-create-the-stonith-devices"></a>**[1]**  Utworzyć urządzenia STONITH

Po można edytowane uprawnienia dla maszyn wirtualnych, możesz skonfigurować urządzenia STONITH w klastrze.

<pre><code>
# replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure property stonith-timeout=900

sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

</code></pre>

### <a name="1-create-fence-topology-for-sbd-fencing"></a>**[1]**  Tworzenie topologii ogranicznika interwencja ogrodzenia

Jeśli chcesz użyć urządzenia interwencja nadal zaleca się używanie agenta odgradzania Azure jako zapasowy, w przypadku, gdy serwer obiektów docelowych iSCSI nie jest dostępna.

<pre><code>
sudo crm configure fencing_topology \
  stonith-sbd rsc_st_azure

</code></pre>
### **[1] ** Korzystanie z urządzenia STONITH

<pre><code>
sudo crm configure property stonith-enabled=true 
</code></pre>


## <a name="next-steps"></a>Kolejne kroki
* [Azure maszyn wirtualnych, planowania i wdrażania dla programu SAP][planning-guide]
* [Maszyny wirtualne Azure wdrożenia SAP][deployment-guide]
* [Wdrożenia usługi Azure DBMS maszyny wirtualnej dla programu SAP][dbms-guide]
* Aby dowiedzieć się, jak nawiązać wysokiej dostępności i planu odzyskiwania po awarii programu SAP HANA na maszynach wirtualnych Azure, zobacz [dostępności wysokiej programu SAP HANA na maszynach wirtualnych Azure (VM)][sap-hana-ha]

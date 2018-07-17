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
ms.date: 07/13/2018
ms.author: sedusch
ms.openlocfilehash: 1fa69cc09772b9f90e6de05820c823f0409d926e
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39070347"
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>Konfigurowanie program Pacemaker w systemie SUSE Linux Enterprise Server na platformie Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[virtual-machines-linux-maintenance]:../../linux/maintenance-and-updates.md#memory-preserving-maintenance
[virtual-machines-windows-maintenance]:../../windows/maintenance-and-updates.md#memory-preserving-maintenance

Dostępne są dwie opcje do skonfigurowania klastra program Pacemaker w systemie Azure. Możesz użyć agenta preferowane dba o ponowne uruchomienie węzła nie powiodło się za pośrednictwem interfejsów API platformy Azure, lub można użyć urządzenia interwencja.

Urządzenie interwencja wymaga dodatkowych maszyn wirtualnych działa jako serwer obiektów docelowych iSCSI, która zawiera urządzenia z systemem interwencja. Ten serwer obiektów docelowych iSCSI może jednak być współużytkowane z innymi klastrami program Pacemaker. Zaletą używania urządzenia z systemem interwencja jest krótszy czas pracy awaryjnej i, jeśli używasz interwencja urządzeniami lokalnymi, nie wymaga żadnych zmian na sposób działania klastra program pacemaker. Preferowane interwencja można nadal używać agenta odgradzania platformy Azure do przechowywania kopii zapasowych odgradzania mechanizmu, w przypadku, gdy serwer obiektów docelowych iSCSI nie jest dostępna.

Jeśli nie chcesz inwestować w dodatkowych maszyn wirtualnych, można również użyć agent Odgradzający Azure. Minusem jest to, że przejścia w tryb failover może potrwać od 10 do 15 minut, jeśli stop zasobów ulegnie awarii lub węzłów klastra nie może komunikować się które sobie nawzajem już.

![Program pacemaker w systemie SLES — omówienie](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

>[!IMPORTANT]
> Za pomocą urządzenia interwencja klastra program Pacemaker, ma zasadnicze znaczenie dla ogólnej niezawodności całego klastra, który routingu między maszynami wirtualnymi związane i maszyn wirtualnych, obsługi urządzeń interwencja nie przechodzi przez wszystkie inne urządzenia, takie jak [urządzeń WUS](https://azure.microsoft.com/solutions/network-appliances/). W przeciwnym razie problemy związane z urządzenia WUS może mieć negatywny wpływ na stabilności i niezawodności ogólnej konfiguracji klastra. Aby uniknąć takich przeszkód, zbadaj reguł routingu urządzeń WUS i [reguł routingu zdefiniowane użytkownika](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) podczas planowania i wdrażania interwencja urządzeń.
>


## <a name="sbd-fencing"></a>Preferowane interwencja

Wykonaj następujące kroki, aby urządzenia z systemem interwencja na użytek preferowane.

### <a name="set-up-an-iscsi-target-server"></a>Konfigurowanie serwera obiektów docelowych iSCSI

Najpierw należy utworzyć iSCSI docelowej maszyny wirtualnej, jeśli nie masz jeszcze takiego. serwery obiektów docelowych iSCSI może być udostępniane wielu klastrów program Pacemaker.

1. Wdrażanie nowego systemu SLES 12 z dodatkiem SP1 lub nowszej maszynę wirtualną i nawiązać połączenie z maszyną za pośrednictwem protokołu ssh. Maszyny nie musi być duże. Rozmiar maszyny wirtualnej, takie jak Standard_E2s_v3 lub Standard_D2s_v3 jest wystarczająca.

1. Aktualizacja w systemie SLES

   <pre><code>
   sudo zypper update
   </code></pre>

1. Usuwanie pakietów

   Aby uniknąć znany problem z targetcli i SLES 12 z dodatkiem SP3, należy odinstalować następujące pakiety. Można zignorować błędy dotyczące pakietów, których nie można znaleźć
   
   <pre><code>
   sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>
   
1. Instalowanie pakietów docelowych iSCSI

   <pre><code>
   sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. Włącz usługę obiektu docelowego iSCSI

   <pre><code>   
   sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>Utwórz urządzenie iSCSI na serwerze docelowym iSCSI

Dołącz nowy dysk danych do docelowej maszyny wirtualnej iSCSI może służyć do tego klastra. Dysk danych może być tak małej, jak 1 GB i muszą znajdować się na konto magazynu Premium Storage lub dysku zarządzanego w warstwie Premium, aby korzystać z [pojedynczy umowę SLA maszyn wirtualnych](https://azure.microsoft.com/support/legal/sla/virtual-machines).

Uruchom następujące polecenie **obiektu docelowego iSCSI maszyny Wirtualnej** do utworzenia dysku iSCSI dla nowego klastra. W poniższym przykładzie **cl1** służy do identyfikowania nowego klastra i **0-prod-cl1** i **prod cl1 1** są nazwy hostów węzłów klastra. Zastąp je nazwy hostów węzły klastra.

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

Łączenie z urządzeniem iSCSI, który został utworzony w poprzednim kroku z klastra.
Uruchom następujące polecenia na węzłach nowego klastra, który chcesz utworzyć.
Następujące elementy mają prefiks albo **[A]** — mające zastosowanie do wszystkich węzłów, **[1]** — dotyczy to tylko węzeł 1 lub **[2]** — dotyczy to tylko węzeł 2.

1. **[A]**  Nawiązywanie połączenia z urządzeniami iSCSI

   Najpierw należy włączyć iSCSI i interwencja usług.

   <pre><code>
   sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]**  Zmienić nazwę inicjatora w pierwszym węźle

   <pre><code>
   sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Zmień zawartość pliku na zgodną z listy kontroli dostępu, którego użyto podczas tworzenia urządzenia iSCSI na serwerze docelowym iSCSI

   <pre><code>   
   InitiatorName=<b>iqn.2006-04.prod-cl1-0.local:prod-cl1-0</b>
   </code></pre>

1. **[2]**  Zmienić nazwę inicjatora drugiego węzła

   <pre><code>
   sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Zmień zawartość pliku na zgodną z listy kontroli dostępu, którego użyto podczas tworzenia urządzenia iSCSI na serwerze docelowym iSCSI

   <pre><code>
   InitiatorName=<b>iqn.2006-04.prod-cl1-1.local:prod-cl1-1</b>
   </code></pre>

1. **[A]**  Ponowne uruchomienie usługi iSCSI

   Teraz ponownie uruchomić usługę iSCSI, aby zastosować zmiany
   
   <pre><code>
   sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   Podłącz urządzenia iSCSI. W poniższym przykładzie 10.0.0.17 jest adres IP serwera docelowego iSCSI i 3260 jest domyślnym portem. <b>IQN.2006 04.cl1.local:cl1</b> jest nazwa docelowego, który znajduje się po uruchomieniu pierwszego polecenia.

   <pre><code>
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>
   
   sudo iscsiadm -m node -T <b>iqn.2006-04.cl1.local:cl1</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   Upewnij się, że urządzenie iSCSI jest dostępny i zwróć uwagę, gotowe nazwy urządzenia (w poniższym przykładzie/dev/sde)

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

   Polecenie listę trzy identyfikatory urządzeń. Zalecane jest używanie Identyfikatora, który rozpoczyna się od scsi-3 w przykładzie powyżej to jest
   
   **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**

1. **[1]**  Utwórz urządzenie interwencja

   Użyj Identyfikatora urządzenia urządzenie iSCSI, aby utworzyć nowe urządzenie interwencja na pierwszym węźle klastra.

   <pre><code>
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 10 -4 20 create
   </code></pre>

1. **[A]**  Dostosowanie konfiguracji interwencja

   Otwórz plik konfiguracji interwencja

   <pre><code>
   sudo vi /etc/sysconfig/sbd
   </code></pre>

   Zmiana własności urządzenia interwencja, Włącz integrację program pacemaker i zmienić tryb startowy danej interwencja.

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

   Teraz załadować moduł

   <pre><code>
   sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>Instalacja klastra

Następujące elementy mają prefiks albo **[A]** — mające zastosowanie do wszystkich węzłów, **[1]** — dotyczy to tylko węzeł 1 lub **[2]** — dotyczy to tylko węzeł 2.

1. **[A]**  Aktualizacji w systemie SLES

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

1. **[A]**  Horyzont instalacji agentów
   
   <pre><code>
   sudo zypper install fence-agents
   </code></pre>

1. **[A]**  Konfigurowanie rozpoznawania nazw hostów   

   Można użyć serwera DNS lub zmodyfikować/etc/hosts na wszystkich węzłach. W tym przykładzie pokazano, jak przy użyciu pliku/etc/hosts.
   Zastąp adres IP i nazwy hosta w poniższych poleceniach. Zaletą używania/etc/hosts to, że klaster stają się niezależnie od systemu DNS, który może być pojedynczym punktem awarii za.

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

1. **[1]**  Zainstaluj klaster
   
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

1. **[A]**  Skonfigurować corosync Użyj innego transportu i dodać wstawienia. Klaster nie działa, w przeciwnym razie.
   
   <pre><code> 
   sudo vi /etc/corosync/corosync.conf   
   </code></pre>

   Dodaj następującą zawartość bold do pliku, jeśli wartości nie są tam lub innej. Upewnij się zmienić token 30000 umożliwia zachowywanie konserwacji pamięci. Zobacz [ten artykuł dla systemu Linux] [ virtual-machines-linux-maintenance] lub [Windows] [ virtual-machines-windows-maintenance] Aby uzyskać więcej informacji.
   
   <pre><code> 
   [...]
     <b>token:          30000
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

1. **[1]**  Zmiany ustawień domyślnych program pacemaker

   <pre><code>
   sudo crm configure rsc_defaults resource-stickiness="1"   
   </code></pre>

## <a name="create-stonith-device"></a>Utwórz urządzenie pomocą metody STONITH

Urządzenie pomocą metody STONITH używa nazwy głównej usługi, do autoryzacji dla Microsoft Azure. Wykonaj następujące kroki, aby utworzyć jednostkę usługi.

1. Przejdź do strony <https://portal.azure.com>
1. Otwórz blok usługi Azure Active Directory  
   Przejdź do właściwości i zanotuj nazwę katalogu. Jest to **identyfikator dzierżawy**.
1. Kliknij przycisk rejestracje aplikacji
1. Kliknij pozycję Dodaj.
1. Wprowadź nazwę, wybierz typ aplikacji "Aplikacja/interfejsu API sieci Web", wprowadź adres URL logowania (na przykład http://localhost) i kliknij przycisk Utwórz
1. Adres URL logowania nie jest używany i może być dowolny prawidłowy adres URL
1. Wybierz nową aplikację, a następnie kliknij przycisk kluczy na karcie Ustawienia
1. Wprowadź opis nowego klucza, wybierz pozycję "Nigdy nie wygasa" i kliknij przycisk Zapisz
1. Zanotuj wartość. Jest ona używana jako **hasło** jednostki usługi
1. Zanotuj identyfikator aplikacji. Jest ona używana jako nazwa użytkownika (**Identyfikatora logowania** w poniższych krokach) jednostki usługi

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]**  Utworzyć rolę niestandardową dla agenta odgradzania

Nazwa główna usługi nie ma uprawnień do dostępu do zasobów platformy Azure, domyślnie. Musisz nadać uprawnień jednostki usługi, uruchamianie i zatrzymywanie (Cofnij ich przydział) wszystkich maszyn wirtualnych klastra. Jeśli nie utworzono jeszcze niestandardowej roli, można utworzyć za pomocą [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell#create-a-custom-role) lub [wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli#create-a-custom-role)

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

### <a name="1-assign-the-custom-role-to-the-service-principal"></a>**[1]**  Przypisać rolę niestandardową nazwę główną usługi

Przypisz rolę niestandardową "Linux horyzont agenta rolę" utworzonego w rozdziale ostatniego jednostki usługi. Nie używaj roli właściciel już!

1. Przejdź do strony https://portal.azure.com
1. Otwieranie bloku wszystkie zasoby
1. Wybierz maszynę wirtualną, w pierwszym węźle klastra
1. Kliknij przycisk kontroli dostępu (IAM)
1. Kliknij pozycję Dodaj.
1. Wybierz rolę "Rolę agenta Odgradzania Linux"
1. Wprowadź nazwę aplikacji, które zostały utworzone powyżej
1. Kliknij przycisk OK

Powtórz powyższe kroki dla drugiego węzła klastra.

### <a name="1-create-the-stonith-devices"></a>**[1]**  Tworzenie urządzeń pomocą metody STONITH

Po edycji uprawnień dla maszyn wirtualnych można skonfigurować urządzenia pomocą metody STONITH w klastrze.

<pre><code>
# replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure property stonith-timeout=900

sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

</code></pre>

### <a name="1-create-fence-topology-for-sbd-fencing"></a>**[1]**  Tworzyć ogrodzenia topologii preferowane interwencja

Jeśli chcesz użyć urządzenia interwencja nadal zaleca się przy użyciu agenta odgradzania platformy Azure do przechowywania kopii zapasowych w przypadku, gdy serwer obiektów docelowych iSCSI nie jest dostępna.

<pre><code>
sudo crm configure fencing_topology \
  stonith-sbd rsc_st_azure

</code></pre>
### **[1] ** Korzystanie z urządzenia pomocą metody STONITH

<pre><code>
sudo crm configure property stonith-enabled=true 
</code></pre>


## <a name="next-steps"></a>Kolejne kroki
* [Azure maszyny wirtualne, planowania i implementacji dla rozwiązania SAP][planning-guide]
* [Wdrażania maszyn wirtualnych platformy Azure dla rozwiązania SAP][deployment-guide]
* [Wdrażania systemu DBMS na maszynach wirtualnych platformy Azure dla rozwiązania SAP][dbms-guide]
* Aby dowiedzieć się, jak zadbać o wysokiej dostępności i plan odzyskiwania po awarii oprogramowania SAP Hana na maszynach wirtualnych platformy Azure, zobacz [wysokiej dostępności dla oprogramowania SAP HANA w usłudze Azure Virtual Machines (VMs)][sap-hana-ha]

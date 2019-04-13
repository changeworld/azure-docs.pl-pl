---
title: Konfigurowanie program Pacemaker w systemie Red Hat Enterprise Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Konfigurowanie program Pacemaker w systemie Red Hat Enterprise Linux na platformie Azure
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
ms.date: 08/17/2018
ms.author: sedusch
ms.openlocfilehash: b844c93a1f3e83d682b51db6f9854f11b24d82e7
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2019
ms.locfileid: "59543752"
---
# <a name="setting-up-pacemaker-on-red-hat-enterprise-linux-in-azure"></a>Konfigurowanie program Pacemaker w systemie Red Hat Enterprise Linux na platformie Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351

[virtual-machines-linux-maintenance]:../../linux/maintenance-and-updates.md#maintenance-not-requiring-a-reboot

> [!NOTE]
> Program pacemaker w systemie Red Hat Enterprise Linux używa agenta Odgradzania platformy Azure do ogrodzenia węzła klastra, jeśli jest to wymagane. Przejścia w tryb failover może potrwać do 15 minut, jeśli stop zasobów ulegnie awarii lub węzłów klastra nie może komunikować się które sobie nawzajem już. Aby uzyskać więcej informacji, przeczytaj [uruchamianie jako członek klastra systemu RHEL wysokiej dostępności maszyny Wirtualnej platformy Azure potrwać bardzo długo się ogrodzonych lub nie powiedzie się preferowane / limit czasu przed zamknięciem maszyny Wirtualnej](https://access.redhat.com/solutions/3408711)

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

## <a name="cluster-installation"></a>Instalacja klastra

![Program pacemaker w RHEL — omówienie](./media/high-availability-guide-rhel-pacemaker/pacemaker-rhel.png)

Następujące elementy mają prefiks albo **[A]** — mające zastosowanie do wszystkich węzłów, **[1]** — dotyczy to tylko węzeł 1 lub **[2]** — dotyczy to tylko węzeł 2.

1. **[A]**  Zarejestrować

   Rejestrowanie maszyn wirtualnych i dołączyć go do puli, która zawiera repozytoriów RHEL 7.

   <pre><code>sudo subscription-manager register
   # List the available pools
   sudo subscription-manager list --available --matches '*SAP*'
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

   Należy zauważyć, że przez dołączenie puli do obrazu systemu RHEL PAYG Marketplace usługi Azure, będzie skutecznie rozliczane podwójnej precyzji zrozumiała dla Ciebie RHEL: jeden raz dla obrazu PAYG i raz uprawnienie RHEL w puli, możesz dołączyć. Aby temu zaradzić, Azure udostępnia teraz BYOS RHEL obrazów. Więcej informacji znajduje się [tutaj](https://aka.ms/rhel-byos).

1. **[A]**  Włączyć RHEL for SAP repozytoriów

   Aby zainstalować wymagane pakiety, należy włączyć następujące repozytoriów.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable="rhel-sap-for-rhel-7-server-rpms"
   </code></pre>

1. **[A]**  Zainstalować dodatek wysokiej dostępności systemu RHEL

   <pre><code>sudo yum install -y pcs pacemaker fence-agents-azure-arm nmap-ncat
   </code></pre>

1. **[A]**  Konfigurowanie rozpoznawania nazw hostów

   Można użyć serwera DNS lub zmodyfikować/etc/hosts na wszystkich węzłach. W tym przykładzie pokazano, jak przy użyciu pliku/etc/hosts.
   Zastąp adres IP i nazwy hosta w poniższych poleceniach. Zaletą używania/etc/hosts to, że klaster stają się niezależnie od systemu DNS, który może być pojedynczym punktem awarii za.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Wstaw następujące wiersze do/etc/hosts. Zmienianie adresu IP i nazwy hosta do danego środowiska

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[A]**  Zmień hacluster hasło do tego samego hasła

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]**  Dodać reguły zapory dla program pacemaker

   Dodaj następujące reguły zapory, aby cała komunikacja klastra między węzłami klastra.

   <pre><code>sudo firewall-cmd --add-service=high-availability --permanent
   sudo firewall-cmd --add-service=high-availability
   </code></pre>

1. **[A]**  Włączenie usług podstawowy klaster

   Uruchom następujące polecenia, aby włączyć usługę program Pacemaker i należy ją uruchomić.

   <pre><code>sudo systemctl start pcsd.service
   sudo systemctl enable pcsd.service
   </code></pre>

1. **[1]**  Klastra Utwórz program Pacemaker

   Uruchom następujące polecenia, aby uwierzytelniać węzły i utworzyć klaster. Ustaw token 30000 umożliwia zachowywanie konserwacji pamięci. Aby uzyskać więcej informacji, zobacz [ten artykuł dla systemu Linux][virtual-machines-linux-maintenance].

   <pre><code>sudo pcs cluster auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup --name <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> --token 30000
   sudo pcs cluster start --all

   # Run the following command until the status of both nodes is online
   sudo pcs status

   # Cluster name: nw1-azr
   # WARNING: no stonith devices and stonith-enabled is not false
   # Stack: corosync
   # Current DC: <b>prod-cl1-1</b> (version 1.1.18-11.el7_5.3-2b07d5c5a9) - partition with quorum
   # Last updated: Fri Aug 17 09:18:24 2018
   # Last change: Fri Aug 17 09:17:46 2018 by hacluster via crmd on <b>prod-cl1-1</b>
   #
   # 2 nodes configured
   # 0 resources configured
   #
   # Online: [ <b>prod-cl1-0</b> <b>prod-cl1-1</b> ]
   #
   # No resources
   #
   #
   # Daemon Status:
   #   corosync: active/disabled
   #   pacemaker: active/disabled
   #   pcsd: active/enabled
   </code></pre>

1. **[A]**  Ustaw oczekiwanego głosów

   <pre><code>sudo pcs quorum expected-votes 2
   </code></pre>

## <a name="create-stonith-device"></a>Utwórz urządzenie pomocą metody STONITH

Urządzenie pomocą metody STONITH używa nazwy głównej usługi, do autoryzacji dla Microsoft Azure. Wykonaj następujące kroki, aby utworzyć jednostkę usługi.

1. Przejdź do strony <https://portal.azure.com>
1. Otwórz blok usługi Azure Active Directory, przejdź do właściwości i zanotuj nazwę katalogu. Jest to **identyfikator dzierżawy**.
1. Kliknij przycisk rejestracje aplikacji
1. Kliknij pozycję Dodaj.
1. Wprowadź nazwę, wybierz typ aplikacji "Aplikacja/interfejsu API sieci Web", wprowadź adres URL logowania (na przykład http:\//localhost) i kliknij przycisk Utwórz
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

1. Przejdź do strony https://portal.azure.com
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

<pre><code>
sudo pcs property set stonith-timeout=900
</code></pre>

Użyj następującego polecenia, aby skonfigurować urządzenie ogranicznika.

> [!NOTE]
> Opcja "pcmk_host_map" jest wymagana tylko w poleceniu, jeśli nazwy hosta systemu RHEL i nazwy węzłów platformy Azure nie są identyczne. Zapoznaj się z sekcją pogrubienia, w poleceniu.

<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm login="<b>login ID</b>" passwd="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:10.0.0.6;prod-cl1-1:10.0.0.7"</b> power_timeout=240 pcmk_reboot_timeout=900</code></pre>

### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]**  Korzystanie z urządzenia pomocą metody STONITH

<pre><code>sudo pcs property set stonith-enabled=true
</code></pre>

## <a name="next-steps"></a>Kolejne kroki

* [Azure maszyny wirtualne, planowania i implementacji dla rozwiązania SAP][planning-guide]
* [Wdrażania maszyn wirtualnych platformy Azure dla rozwiązania SAP][deployment-guide]
* [Wdrażania systemu DBMS na maszynach wirtualnych platformy Azure dla rozwiązania SAP][dbms-guide]
* Aby dowiedzieć się, jak zadbać o wysokiej dostępności i plan odzyskiwania po awarii oprogramowania SAP Hana na maszynach wirtualnych platformy Azure, zobacz [wysokiej dostępności dla oprogramowania SAP HANA w usłudze Azure Virtual Machines (VMs)][sap-hana-ha]

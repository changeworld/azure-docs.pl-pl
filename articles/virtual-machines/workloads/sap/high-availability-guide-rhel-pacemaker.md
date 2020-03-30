---
title: Konfigurowanie rozrusznika serca na platformie RHEL na platformie Azure | Dokumenty firmy Microsoft
description: Konfigurowanie rozrusznika serca w systemie Red Hat Enterprise Linux na platformie Azure
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
ms.date: 08/17/2018
ms.author: radeltch
ms.openlocfilehash: 21c551721815847eea4cb1435298ea6f7bf37966
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264481"
---
# <a name="setting-up-pacemaker-on-red-hat-enterprise-linux-in-azure"></a>Konfigurowanie rozrusznika serca w systemie Red Hat Enterprise Linux na platformie Azure

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

[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot


Najpierw przeczytaj następujące uwagi i dokumenty SAP:

* Uwaga SAP [1928533], która ma:
  * Lista rozmiarów maszyn wirtualnych platformy Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP.
  * Ważne informacje o pojemności dla rozmiarów maszyn wirtualnych platformy Azure.
  * Obsługiwane oprogramowanie SAP oraz system operacyjny (OS) i kombinacje baz danych.
  * Wymagana wersja jądra SAP dla systemów Windows i Linux na platformie Microsoft Azure.
* Uwaga SAP [2015553] zawiera listę wymagań wstępnych dla wdrożeń oprogramowania SAP obsługiwanych przez SAP na platformie Azure.
* SAP Note [2002167] ma zalecane ustawienia systemu operacyjnego dla systemu Red Hat Enterprise Linux
* SAP Note [2009879] ma wytyczne SAP HANA dla Red Hat Enterprise Linux
* Uwaga SAP [2178632] zawiera szczegółowe informacje na temat wszystkich metryk monitorowania zgłoszonych dla sap na platformie Azure.
* Sap Note [2191498] ma wymaganą wersję agenta hosta SAP dla systemu Linux na platformie Azure.
* Uwaga SAP [2243692] ma informacje o licencjonowaniu SAP w systemie Linux na platformie Azure.
* Uwaga SAP [1999351] zawiera dodatkowe informacje dotyczące rozwiązywania problemów z rozszerzeniem rozszerzonego monitorowania platformy Azure dla systemu SAP.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) ma wszystkie wymagane notatki SAP dla Linuksa.
* [Planowanie i implementacja maszyn wirtualnych platformy Azure dla systemu SAP w systemie Linux][planning-guide]
* [Wdrożenie maszyn wirtualnych platformy Azure dla systemu SAP w systemie Linux (w tym artykule)][deployment-guide]
* [Wdrożenie usługi DBMS maszyn wirtualnych platformy Azure dla systemu SAP w systemie Linux][dbms-guide]
* [Replikacja systemu SAP HANA w klastrze rozruszników serca](https://access.redhat.com/articles/3004101)
* Ogólna dokumentacja RHEL
  * [Omówienie dodatku o wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administracja dodatkami o wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Dokumentacja dodatku o wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Zasady pomocy technicznej dla klastrów wysokiej dostępności RHEL — sbd i fence_sbd](https://access.redhat.com/articles/2800691)
* Dokumentacja RHEL specyficzna dla platformy Azure:
  * [Zasady pomocy technicznej dotyczące klastrów wysokiej dostępności RHEL — maszyny wirtualne platformy Microsoft Azure jako elementy członkowskie klastra](https://access.redhat.com/articles/3131341)
  * [Instalowanie i konfigurowanie klastra o wysokiej dostępności w systemie Microsoft Azure dla systemu Red Hat Enterprise 7.4 (lub nowszych)](https://access.redhat.com/articles/3252491)
  * [Konfigurowanie SAP S/4HANA ASCS/ERS z autonomicznym serwerem Enqueue Server 2 (ENSA2) w rozruszniku serca na RHEL 7.6](https://access.redhat.com/articles/3974941)

## <a name="cluster-installation"></a>Instalacja klastra

![Pacemaker na przeglądzie RHEL](./media/high-availability-guide-rhel-pacemaker/pacemaker-rhel.png)

> [!NOTE]
> Red Hat nie obsługuje naśladowanego programowo watchdoga. Red Hat nie obsługuje SBD na platformach w chmurze. Aby uzyskać szczegółowe informacje, zobacz [Zasady pomocy technicznej dla klastrów wysokiej dostępności RHEL — sbd i fence_sbd](https://access.redhat.com/articles/2800691).
> Jedynym obsługiwanym mechanizmem szermierki dla klastrów Systemu Linux Red Hat Enterprise na platformie Azure jest agent ogrodzenia platformy Azure.  

Następujące elementy są poprzedzone **[A]** - ma zastosowanie do wszystkich węzłów, **[1]** - dotyczy tylko węzła 1 lub **[2]** - dotyczy tylko węzła 2.

1. **[A]** Zarejestruj się

   Zarejestruj swoje maszyny wirtualne i dołącz ją do puli zawierającej repozytoria dla RHEL 7.

   <pre><code>sudo subscription-manager register
   # List the available pools
   sudo subscription-manager list --available --matches '*SAP*'
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

   Należy zauważyć, że dołączając pulę do obrazu RHEL payg usługi Azure Marketplace, będzie skutecznie dwukrotnie rozliczane dla użycia RHEL: raz dla obrazu PAYG i raz dla uprawnienia RHEL w puli, którą dołączasz. Aby to złagodzić, platforma Azure udostępnia teraz obrazy BYOS RHEL. Więcej informacji można znaleźć [tutaj](../redhat/byos.md).

1. **[A]** Włącz RHEL dla repozytoriów SAP

   Aby zainstalować wymagane pakiety, należy włączyć następujące repozytoria.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-sap-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-eus-rpms
   </code></pre>

1. **[A]** Zainstaluj dodatek RHEL HA

   <pre><code>sudo yum install -y pcs pacemaker fence-agents-azure-arm nmap-ncat
   </code></pre>

   > [!IMPORTANT]
   > Firma Microsoft zaleca następujące wersje agenta usługi Azure Fence (lub nowszego) dla klientów, aby mogli korzystać z szybszego czasu pracy awaryjnej, jeśli zatrzymanie zasobu nie powiedzie się lub węzły klastra nie mogą komunikować się, które są już sobą nawzajem:  
   > RHEL 7.6: ogrodzenia-agentów-4.2.1-11.el7_6.8  
   > RHEL 7.5: ogrodzenia agentów-4.0.11-86.el7_5.8  
   > RHEL 7.4: ogrodnicy-4.0.11-66.el7_4.12  
   > Aby uzyskać więcej informacji, zobacz [Maszyna wirtualna platformy Azure uruchomiona jako członek klastra O wysokiej dostępności RHEL zajmuje bardzo dużo czasu, aby być ogrodzonym lub ogrodzenie kończy się niepowodzeniem / limit czasu przed zamknięciem maszyny Wirtualnej.](https://access.redhat.com/solutions/3408711)

   Sprawdź wersję agenta ogrodzenia platformy Azure. W razie potrzeby zaktualizuj go do wersji równej lub późniejszej niż wymienione powyżej.

   <pre><code># Check the version of the Azure Fence Agent
    sudo yum info fence-agents-azure-arm
   </code></pre>

   > [!IMPORTANT]
   > Jeśli chcesz zaktualizować agenta usługi Azure Fence i jeśli używasz roli niestandardowej, upewnij się, że zaktualizowano rolę niestandardową, aby uwzględnić akcję **powerOff**. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie roli niestandardowej dla agenta ogrodzenia](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker#1-create-a-custom-role-for-the-fence-agent).  

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

1. **[A]** Zmień hasło hacluster na to samo hasło

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** Dodawanie reguł zapory dla rozrusznika serca

   Dodaj następujące reguły zapory do całej komunikacji klastra między węzłami klastra.

   <pre><code>sudo firewall-cmd --add-service=high-availability --permanent
   sudo firewall-cmd --add-service=high-availability
   </code></pre>

1. **[A]** Włączanie podstawowych usług klastrowania

   Uruchom następujące polecenia, aby włączyć usługę rozrusznika serca i uruchomić ją.

   <pre><code>sudo systemctl start pcsd.service
   sudo systemctl enable pcsd.service
   </code></pre>

1. **[1]** Tworzenie klastra rozruszników serca

   Uruchom następujące polecenia, aby uwierzytelnić węzły i utworzyć klaster. Ustaw token na 30000, aby umożliwić zachowanie pamięci konserwacji. Aby uzyskać więcej informacji, zobacz [ten artykuł dla systemu Linux][virtual-machines-linux-maintenance].

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

1. **[A]** Zestaw oczekiwanych głosów

   <pre><code>sudo pcs quorum expected-votes 2
   </code></pre>

## <a name="create-stonith-device"></a>Tworzenie urządzenia STONITH

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

Podmiot zabezpieczeń usługi nie ma uprawnień do uzyskiwania dostępu do zasobów platformy Azure domyślnie. Należy nadać service principal uprawnienia do uruchamiania i zatrzymywania (wyłączanie) wszystkie maszyny wirtualne klastra. Jeśli rola niestandardowa nie została jeszcze utworzona, można ją utworzyć przy użyciu [programu PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)

Użyj następującej zawartości dla pliku wejściowego. Musisz dostosować zawartość do subskrypcji, czyli zastąpić c276fc76-9cd4-44c9-99a7-4fd71546436e i e91d47c4-76f3-4271-a796-21b4ecfe3624 z identyfikatorami subskrypcji. Jeśli masz tylko jedną subskrypcję, usuń drugi wpis w AssignableScopes.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to power-off and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/powerOff/action",
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

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** Przypisz rolę niestandardową do jednostki usługi

Przypisz rolę niestandardową "Rola agenta ogrodzenia linuksa", która została utworzona w ostatnim rozdziale do jednostki usługi. Nie należy już używać roli Właściciel!

1. Przejdź do strony https://portal.azure.com
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

<pre><code>
sudo pcs property set stonith-timeout=900
</code></pre>

Użyj następującego polecenia, aby skonfigurować urządzenie ogrodzeniowe.

> [!NOTE]
> Opcja "pcmk_host_map" jest tylko wymagane w poleceniu, jeśli nazwy hosta RHEL i nazwy węzłów platformy Azure NIE są identyczne. Zapoznaj się z sekcją pogrubioną w poleceniu.

<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm login="<b>login ID</b>" passwd="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:10.0.0.6;prod-cl1-1:10.0.0.7"</b> power_timeout=240 pcmk_reboot_timeout=900</code></pre>

### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]** Umożliwianie korzystania z urządzenia STONITH

<pre><code>sudo pcs property set stonith-enabled=true
</code></pre>

## <a name="next-steps"></a>Następne kroki

* [Planowanie i implementacja maszyn wirtualnych platformy Azure dla systemu SAP][planning-guide]
* [Wdrożenie maszyn wirtualnych platformy Azure dla systemu SAP][deployment-guide]
* [Wdrożenie usługi DBMS maszyn wirtualnych platformy Azure dla systemu SAP][dbms-guide]
* Aby dowiedzieć się, jak ustalić wysoką dostępność i plan odzyskiwania po awarii sap HANA na maszynach wirtualnych platformy Azure, zobacz [Wysoka dostępność SAP HANA na maszynach wirtualnych platformy Azure (VM)][sap-hana-ha]

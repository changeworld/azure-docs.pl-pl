---
title: Konfigurowanie grup dostępności dla programu SQL Server na maszynach wirtualnych RHEL na platformie Azure — maszyny wirtualne systemu Linux | Dokumenty firmy Microsoft
description: Dowiedz się więcej o konfigurowaniu wysokiej dostępności w środowisku klastra RHEL i konfigurowaniu stonith
ms.service: virtual-machines-linux
ms.subservice: ''
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 02/27/2020
ms.openlocfilehash: 40c91f67231fb6a9d01191ee5215eae8d4dc045b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79096698"
---
# <a name="tutorial-configure-availability-groups-for-sql-server-on-rhel-virtual-machines-in-azure"></a>Samouczek: Konfigurowanie grup dostępności dla programu SQL Server na maszynach wirtualnych RHEL na platformie Azure 

> [!NOTE]
> Przedstawiony samouczek znajduje się w **publicznej wersji zapoznawczej**. 
>
> Używamy programu SQL Server 2017 z RHEL 7.6 w tym samouczku, ale jest możliwe użycie programu SQL Server 2019 w RHEL 7 lub RHEL 8 do skonfigurowania ha. Polecenia do konfigurowania zasobów grupy dostępności uległy zmianie w programie RHEL 8 i warto przyjrzeć się temu artykułowi [Utwórz zasoby grupy dostępności](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) i Zasoby RHEL 8, aby uzyskać więcej informacji na temat prawidłowych poleceń.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Tworzenie nowej grupy zasobów, zestawu dostępności i maszyn wirtualnych systemu Azure Linux (VM)
> - Włącz wysoką dostępność (HA)
> - Tworzenie klastra rozrusznika serca
> - Konfigurowanie agenta ogrodzeniowego przez utworzenie urządzenia STONITH
> - Instalowanie programu SQL Server i mssql-tools w programie RHEL
> - Konfigurowanie grupy dostępności programu SQL Server always on
> - Konfigurowanie zasobów grupy dostępności (AG) w klastrze rozrusznika serca
> - Przetestuj czynnik awaryjny i środek szermierczy

W tym samouczku użyje interfejsu wiersza polecenia platformy Azure (CLI) do wdrażania zasobów na platformie Azure.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Jeśli wolisz zainstalować i używać interfejsu wiersza polecenia lokalnie, ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Jeśli masz więcej niż jedną [subskrypcję, ustaw subskrypcję, w](/cli/azure/manage-azure-subscriptions-azure-cli) której chcesz wdrożyć te zasoby.

Użyj następującego polecenia, aby `<resourceGroupName>` utworzyć grupę zasobów w regionie. Zamień `<resourceGroupName>` na wybraną nazwę. Używamy `East US 2` w tym samouczku. Aby uzyskać więcej informacji, zobacz następujące [przewodnika Szybki start](../quick-create-cli.md).

```azurecli-interactive
az group create --name <resourceGroupName> --location eastus2
```

## <a name="create-an-availability-set"></a>Tworzenie zestawu dostępności

Następnym krokiem jest utworzenie zestawu dostępności. Uruchom następujące polecenie w usłudze `<resourceGroupName>` Azure Cloud Shell i zastąp nazwą grupy zasobów. Wybierz nazwę `<availabilitySetName>`dla pliku .

```azurecli-interactive
az vm availability-set create \
    --resource-group <resourceGroupName> \
    --name <availabilitySetName> \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

Po zakończeniu polecenia należy uzyskać następujące wyniki:

```output
{
  "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/availabilitySets/<availabilitySetName>",
  "location": "eastus2",
  "name": "<availabilitySetName>",
  "platformFaultDomainCount": 2,
  "platformUpdateDomainCount": 2,
  "proximityPlacementGroup": null,
  "resourceGroup": "<resourceGroupName>",
  "sku": {
    "capacity": null,
    "name": "Aligned",
    "tier": null
  },
  "statuses": null,
  "tags": {},
  "type": "Microsoft.Compute/availabilitySets",
  "virtualMachines": []
}
```

## <a name="create-rhel-vms-inside-the-availability-set"></a>Tworzenie maszyn wirtualnych RHEL wewnątrz zestawu dostępności

> [!WARNING]
> Jeśli wybierzesz obraz RHEL zgodnie z rzeczywistymi oczekiwaniami (PayG) i skonfigurujesz wysokiej dostępności (HA), może być konieczne zarejestrowanie subskrypcji. Może to spowodować, że zapłacisz dwa razy za subskrypcję, ponieważ zostanie naliczona opłata za subskrypcję RHEL platformy Microsoft Azure dla maszyny Wirtualnej i subskrypcję red hat. Aby uzyskać więcej informacji, zobacz https://access.redhat.com/solutions/2458541.
>
> Aby uniknąć "podwójnie rozliczane", należy użyć obrazu RHEL HA podczas tworzenia maszyny Wirtualnej platformy Azure. Obrazy oferowane jako obrazy RHEL-HA są również obrazy PAYG z repozytorium HA wstępnie włączone.

1. Pobierz listę obrazów maszyny wirtualnej (VM), które oferują RHEL z ha:

    ```azurecli-interactive
    az vm image list --all --offer "RHEL-HA"
    ```

    Powinny zostać wyświetlone następujące wyniki:

    ```output
    [
            {
              "offer": "RHEL-HA",
              "publisher": "RedHat",
              "sku": "7.4",
              "urn": "RedHat:RHEL-HA:7.4:7.4.2019062021",
              "version": "7.4.2019062021"
            },
            {
              "offer": "RHEL-HA",
              "publisher": "RedHat",
              "sku": "7.5",
              "urn": "RedHat:RHEL-HA:7.5:7.5.2019062021",
              "version": "7.5.2019062021"
            },
            {
              "offer": "RHEL-HA",
              "publisher": "RedHat",
              "sku": "7.6",
              "urn": "RedHat:RHEL-HA:7.6:7.6.2019062019",
              "version": "7.6.2019062019"
            }
    ]
    ```

    W tym samouczku wybieramy `RedHat:RHEL-HA:7.6:7.6.2019062019`obraz .

    > [!IMPORTANT]
    > Nazwy komputerów muszą być mniejsze niż 15 znaków, aby skonfigurować grupę dostępności. Nazwa użytkownika nie może zawierać wielkich liter, a hasła muszą zawierać więcej niż 12 znaków.

1. Chcemy utworzyć 3 maszyny wirtualne w zestawie dostępności. Zastąp w poniższym poleceniu:

    - `<resourceGroupName>`
    - `<VM-basename>`
    - `<availabilitySetName>`
    - `<VM-Size>`- Przykładem może być "Standard_D16_v3"
    - `<username>`
    - `<adminPassword>`

    ```azurecli-interactive
    for i in `seq 1 3`; do
           az vm create \
             --resource-group <resourceGroupName> \
             --name <VM-basename>$i \
             --availability-set <availabilitySetName> \
             --size "<VM-Size>"  \
             --image "RedHat:RHEL-HA:7.6:7.6.2019062019" \
             --admin-username "<username>" \
             --admin-password "<adminPassword>" \
             --authentication-type all \
             --generate-ssh-keys
    done
    ```

Powyższe polecenie tworzy maszyny wirtualne i tworzy domyślną sieć wirtualną dla tych maszyn wirtualnych. Aby uzyskać więcej informacji na temat różnych konfiguracji, zobacz [az vm create](https://docs.microsoft.com/cli/azure/vm) article.

Wyniki powinny być podobne do następujących po zakończeniu polecenia dla każdej maszyny Wirtualnej:

```output
{
  "fqdns": "",
  "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<VM1>",
  "location": "eastus2",
  "macAddress": "<Some MAC address>",
  "powerState": "VM running",
  "privateIpAddress": "<IP1>",
  "publicIpAddress": "",
  "resourceGroup": "<resourceGroupName>",
  "zones": ""
}
```

> [!IMPORTANT]
> Domyślny obraz utworzony za pomocą powyższego polecenia domyślnie tworzy dysk systemu operacyjnego o wartości 32 GB. W tej instalacji domyślnej może zabraknąć miejsca. Do utworzenia dysku systemu operacyjnego o wartości 128 GB można użyć następującego parametru dodanego do powyższego `az vm create` polecenia: `--os-disk-size-gb 128`.
>
> Następnie można [skonfigurować Menedżera woluminów logicznych (LVM),](../../../virtual-machines/linux/configure-lvm.md) jeśli konieczne jest rozszerzenie odpowiednich woluminów folderów, aby pomieścić instalację.

### <a name="test-connection-to-the-created-vms"></a>Testowanie połączenia z utworzonymi maszynami wirtualnymi

Połącz się z maszynami wirtualnymi1 lub innymi maszynami wirtualnymi przy użyciu następującego polecenia w usłudze Azure Cloud Shell. Jeśli nie możesz znaleźć swoich usług WIRTUALNYCH, wykonaj ten [przewodnik Szybki start w usłudze Azure Cloud Shell.](../../../cloud-shell/quickstart.md#ssh-into-your-linux-vm)

```azurecli-interactive
ssh <username>@publicipaddress
```

Jeśli połączenie zakończy się pomyślnie, powinien zostać wyświetlony następujący wynik reprezentujący terminal Linux:

```output
[<username>@<VM1> ~]$
```

Wpisz, `exit` aby opuścić sesję SSH.

## <a name="enable-high-availability"></a>Włącz wysoką dostępność

> [!IMPORTANT]
> Aby ukończyć tę część samouczka, musisz mieć subskrypcję dla RHEL i dodatek o wysokiej dostępności. Jeśli używasz obrazu zalecanego w poprzedniej sekcji, nie musisz rejestrować innej subskrypcji.
 
Połącz się z każdym węzłem maszyny Wirtualnej i postępuj zgodnie z poniższym przewodnikiem, aby włączyć wysokiej wartości. Aby uzyskać więcej informacji, zobacz [włączanie subskrypcji wysokiej dostępności dla RHEL](/sql/linux/sql-server-linux-availability-group-cluster-rhel#enable-the-high-availability-subscription-for-rhel).

> [!TIP]
> Będzie to łatwiejsze, jeśli otworzysz sesję SSH do każdej z maszyn wirtualnych jednocześnie, ponieważ te same polecenia będą musiały być uruchamiane na każdej maszynie wirtualnej w całym artykule.
>
> Jeśli kopiujesz i wklejasz wiele `sudo` poleceń i zostanie wyświetlony monit o podanie hasła, dodatkowe polecenia nie zostaną uruchomione. Uruchom każde polecenie oddzielnie.


1. Uruchom następujące polecenia na każdej maszynie wirtualnej, aby otworzyć porty zapory rozrusznika serca:

    ```bash
    sudo firewall-cmd --permanent --add-service=high-availability
    sudo firewall-cmd --reload
    ```

1. Aktualizowanie i instalowanie pakietów rozrusznika we wszystkich węzłach przy użyciu następujących poleceń:

    > [!NOTE]
    > **nmap** jest instalowany jako część tego bloku poleceń jako narzędzie do znajdowania dostępnych adresów IP w sieci. Nie musisz instalować **nmapa**, ale będzie to przydatne w dalszej części tego samouczka.

    ```bash
    sudo yum update -y
    sudo yum install -y pacemaker pcs fence-agents-all resource-agents fence-agents-azure-arm nmap
    sudo reboot
    ```

1. Ustaw hasło dla domyślnego użytkownika, które jest tworzone podczas instalowania pakietów rozrusznika. Użyj tego samego hasła we wszystkich węzłach.

    ```bash
    sudo passwd hacluster
    ```

1. Użyj następującego polecenia, aby otworzyć plik hosts i skonfigurować rozpoznawanie nazw hostów. Aby uzyskać więcej informacji, zobacz [Konfigurowanie ag](/sql/linux/sql-server-linux-availability-group-configure-ha#prerequisites) na konfigurowanie pliku hosts.

    ```
    sudo vi /etc/hosts
    ```

    W **vi** edytorze vi `i` wprowadź, aby wstawić tekst, a w pustym wierszu dodaj **prywatny adres IP** odpowiedniej maszyny Wirtualnej. Następnie dodaj nazwę maszyny Wirtualnej po spacji obok adresu IP. Każdy wiersz powinien mieć osobny wpis.

    ```output
    <IP1> <VM1>
    <IP2> <VM2>
    <IP3> <VM3>
    ```

    > [!IMPORTANT]
    > Zalecamy użycie prywatnego adresu **IP** powyżej. Użycie publicznego adresu IP w tej konfiguracji spowoduje niepowodzenie instalacji i nie zaleca się narażania maszyny Wirtualnej na sieci zewnętrzne.

    Aby zamknąć edytor **vi,** najpierw naciśnij klawisz **Esc,** a następnie wprowadź polecenie, `:wq` aby napisać plik i zamknąć.

## <a name="create-the-pacemaker-cluster"></a>Tworzenie klastra rozrusznika serca

W tej sekcji włączymy i uruchomimy usługę pcsd, a następnie skonfigurujemy klaster. W przypadku programu SQL Server w systemie Linux zasoby klastra nie są tworzone automatycznie. Musimy ręcznie włączyć i utworzyć zasoby rozrusznika serca. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [konfigurowania wystąpienia klastra trybu failover dla RHEL](/sql/linux/sql-server-linux-shared-disk-cluster-red-hat-7-configure#install-and-configure-pacemaker-on-each-cluster-node)

### <a name="enable-and-start-pcsd-service-and-pacemaker"></a>Włączanie i uruchamianie usługi pcsd i rozrusznika serca

1. Uruchom polecenia we wszystkich węzłach. Te polecenia umożliwiają węzłom ponowne dołączenie do klastra po ponownym uruchomieniu.

    ```bash
    sudo systemctl enable pcsd
    sudo systemctl start pcsd
    sudo systemctl enable pacemaker
    ``` 

1. Usuń istniejącą konfigurację klastra ze wszystkich węzłów. Uruchom następujące polecenie:

    ```bash
    sudo pcs cluster destroy 
    sudo systemctl enable pacemaker 
    ```

1. W węźle podstawowym uruchom następujące polecenia, aby skonfigurować klaster.

    - Podczas uruchamiania `pcs cluster auth` polecenia uwierzytelniania węzłów klastra zostanie wyświetlony monit o podanie hasła. Wprowadź hasło użytkownika **hacluster** utworzonego wcześniej.

    ```bash
    sudo pcs cluster auth <VM1> <VM2> <VM3> -u hacluster
    sudo pcs cluster setup --name az-hacluster <VM1> <VM2> <VM3> --token 30000
    sudo pcs cluster start --all
    sudo pcs cluster enable --all
    ```

1. Uruchom następujące polecenie, aby sprawdzić, czy wszystkie węzły są w trybie online.

    ```bash
    sudo pcs status
    ```

    Jeśli wszystkie węzły są w trybie online, zobaczysz dane wyjściowe podobne do następujących:

    ```output
    Cluster name: az-hacluster
     
    WARNINGS:
    No stonith devices and stonith-enabled is not false
     
    Stack: corosync
    Current DC: <VM2> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
    Last updated: Fri Aug 23 18:27:57 2019
    Last change: Fri Aug 23 18:27:56 2019 by hacluster via crmd on <VM2>
     
    3 nodes configured
    0 resources configured
     
    Online: [ <VM1> <VM2> <VM3> ]
     
    No resources
     
     
    Daemon Status:
          corosync: active/enabled
          pacemaker: active/enabled
          pcsd: active/enabled
    ```

1. Ustaw oczekiwane głosy w klastrze na żywo do 3. To polecenie ma wpływ tylko na klaster na żywo i nie zmienia plików konfiguracyjnych.

    We wszystkich węzłach ustaw oczekiwane głosy za pomocą następującego polecenia:

    ```bash
    sudo pcs quorum expected-votes 3
    ```

## <a name="configure-the-fencing-agent"></a>Konfigurowanie środka ogrodzeniowego

Urządzenie STONITH zapewnia środek ogrodzeniowy. Poniższe instrukcje są modyfikowane dla tego samouczka. Aby uzyskać więcej informacji, zobacz [tworzenie urządzenia STONITH](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#create-stonith-device).
 
[Sprawdź wersję agenta ogrodzenia platformy Azure, aby upewnić się, że jest on zaktualizowany.](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#cluster-installation) Użyj następującego polecenia:

```bash
sudo yum info fence-agents-azure-arm
```

Powinien zostać wyświetlony podobny wynik do poniższego przykładu.

```output
Loaded plugins: langpacks, product-id, search-disabled-repos, subscription-manager
Installed Packages
Name        : fence-agents-azure-arm
Arch        : x86_64
Version     : 4.2.1
Release     : 11.el7_6.8
Size        : 28 k
Repo        : installed
From repo   : rhel-ha-for-rhel-7-server-eus-rhui-rpms
Summary     : Fence agent for Azure Resource Manager
URL         : https://github.com/ClusterLabs/fence-agents
License     : GPLv2+ and LGPLv2+
Description : The fence-agents-azure-arm package contains a fence agent for Azure instances.
```

### <a name="register-a-new-application-in-azure-active-directory"></a>Rejestrowanie nowej aplikacji w usłudze Azure Active Directory
 
 1. Przejdź do strony https://portal.azure.com
 2. Otwórz [blok usługi Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties). Przejdź do właściwości i zapisz identyfikator katalogu. Jest to`tenant ID`
 3. Kliknij [ **pozycję Rejestracje aplikacji**](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)
 4. Kliknij **pozycję Nowa rejestracja**
 5. Wprowadź **nazwę** `<resourceGroupName>-app`podobną , wybierz **pozycję Konta tylko w tym katalogu organizacji**
 6. Wybierz pozycję Typ aplikacji **w sieci Web** http://localhost) , wprowadź adres URL logowania (na przykład i kliknij przycisk Dodaj. Adres URL logowania nie jest używany i może być dowolnym prawidłowym adresem URL. Po zakończeniu kliknij **zarejestruj się**
 7. Wybierz **certyfikaty i wpisy tajne** dla nowej rejestracji aplikacji, a następnie kliknij pozycję **Nowy klucz tajny klienta**
 8. Wprowadź opis nowego klucza (klucz tajny klienta), wybierz pozycję **Nigdy nie wygasa** i kliknij przycisk **Dodaj**
 9. Zapisz wartość klucza tajnego. Jest on używany jako hasło dla jednostki usługi
10. Wybierz pozycję **Przegląd**. Zapisz identyfikator aplikacji. Jest on używany jako nazwa użytkownika (identyfikator logowania w poniższych krokach)
 
### <a name="create-a-custom-role-for-the-fence-agent"></a>Tworzenie roli niestandardowej dla agenta ogrodzenia

Postępuj zgodnie z [samouczkiem, aby utworzyć rolę niestandardową dla zasobów platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](../../../role-based-access-control/tutorial-custom-role-cli.md#create-a-custom-role).

Plik json powinien wyglądać podobnie do następującego:

- Zastąp ciąg `<username>` wybraną nazwą. Ma to na celu uniknięcie powielania podczas tworzenia tej definicji roli.
- Zamień `<subscriptionId>` na swój identyfikator subskrypcji platformy Azure.

```json
{
  "Name": "Linux Fence Agent Role-<username>",
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
    "/subscriptions/<subscriptionId>"
  ]
}
```

Aby dodać rolę, uruchom następujące polecenie:

- Zamień `<filename>` na nazwę pliku.
- Jeśli polecenie jest wykonywane ze ścieżki innej niż folder, w której plik jest zapisywany, dołącz ścieżkę folderu pliku do polecenia.

```bash
az role definition create --role-definition "<filename>.json"
```

Powinny zostać wyświetlone następujące dane wyjściowe:

```output
{
  "assignableScopes": [
    "/subscriptions/<subscriptionId>"
  ],
  "description": "Allows to power-off and start virtual machines",
  "id": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<roleNameId>",
  "name": "<roleNameId>",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/virtualMachines/powerOff/action",
        "Microsoft.Compute/virtualMachines/start/action"
      ],
      "dataActions": [],
      "notActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Linux Fence Agent Role-<username>",
  "roleType": "CustomRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="assign-the-custom-role-to-the-service-principal"></a>Przypisywanie roli niestandardowej do jednostki usługi

Przypisz rolę `Linux Fence Agent Role-<username>` niestandardową, która została utworzona w ostatnim kroku do jednostki usługi. Nie należy już używać roli Właściciel!
 
1. Przejdź do strony https://portal.azure.com
2. Otwórz [ostrze Wszystkie zasoby](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAll)
3. Wybieranie maszyny wirtualnej pierwszego węzła klastra
4. Kliknij **pozycję Kontrola dostępu (IAM)**
5. Kliknij **pozycję Dodaj przypisanie roli**
6. Wybierz rolę `Linux Fence Agent Role-<username>` z listy **Rola**
7. Na liście **Wybierz** wprowadź nazwę aplikacji utworzonej powyżej,`<resourceGroupName>-app`
8. Kliknij **przycisk Zapisz**
9. Powtórz powyższe kroki dla całego węzła klastra.

### <a name="create-the-stonith-devices"></a>Tworzenie urządzeń STONITH

Uruchom następujące polecenia w węźle 1:

- Zastąp `<ApplicationID>` wartość identyfikatora z rejestracji aplikacji.
- Zastąp `<servicePrincipalPassword>` wartość z klucza tajnego klienta.
- Zastąp grupę `<resourceGroupName>` zasobów z subskrypcji używanej w tym samouczku.
- `<tenantID>` Zastąp `<subscriptionId>` i z subskrypcji platformy Azure.

```bash
sudo pcs property set stonith-timeout=900
sudo pcs stonith create rsc_st_azure fence_azure_arm login="<ApplicationID>" passwd="<servicePrincipalPassword>" resourceGroup="<resourceGroupName>" tenantId="<tenantID>" subscriptionId="<subscriptionId>" power_timeout=240 pcmk_reboot_timeout=900
```

Ponieważ dodaliśmy już regułę do naszej zapory, aby zezwolić na usługę wysokiej klasy (`--add-service=high-availability`), nie ma potrzeby otwierania następujących portów zapory we wszystkich węzłach: 2224, 3121, 21064, 5405. Jeśli jednak występują problemy z połączeniem typu z usługą ha, użyj następującego polecenia, aby otworzyć te porty, które są skojarzone z usługą ha.

> [!TIP]
> Opcjonalnie można dodać wszystkie porty w tym samouczku na raz, aby zaoszczędzić trochę czasu. Porty, które należy otworzyć, są wyjaśnione w ich sekcjach względnych poniżej. Jeśli chcesz teraz dodać wszystkie porty, dodaj dodatkowe porty: 1433 i 5022.

```bash
sudo firewall-cmd --zone=public --add-port=2224/tcp --add-port=3121/tcp --add-port=21064/tcp --add-port=5405/tcp --permanent
sudo firewall-cmd --reload
```

## <a name="install-sql-server-and-mssql-tools"></a>Instalowanie programu SQL Server i narzędzi mssql
 
Użyj poniższej sekcji, aby zainstalować SQL Server i mssql-tools na maszynach wirtualnych. Wykonaj każdą z tych akcji na wszystkich węzłach. Aby uzyskać więcej informacji, zobacz [instalowanie programu SQL Server a Red Hat VM](/sql/linux/quickstart-install-connect-red-hat).

### <a name="installing-sql-server-on-the-vms"></a>Instalowanie programu SQL Server na maszynach wirtualnych

Do zainstalowania programu SQL Server używane są następujące polecenia:

```bash
sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/7/mssql-server-2017.repo
sudo yum install -y mssql-server
sudo /opt/mssql/bin/mssql-conf setup
sudo yum install mssql-server-ha
```

### <a name="open-firewall-port-1433-for-remote-connections"></a>Otwórz port zapory 1433 dla połączeń zdalnych

Aby połączyć się zdalnie, musisz otworzyć port 1433 na maszynie wirtualnej. Aby otworzyć port 1433 w zaporze każdej maszyny Wirtualnej, użyj następujących poleceń:

```bash
sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
sudo firewall-cmd --reload
```

### <a name="installing-sql-server-command-line-tools"></a>Instalowanie narzędzi wiersza polecenia programu SQL Server

Następujące polecenia są używane do instalowania narzędzi wiersza polecenia programu SQL Server. Aby uzyskać więcej informacji, zobacz [instalowanie narzędzi wiersza polecenia programu SQL Server](/sql/linux/quickstart-install-connect-red-hat#tools).

```bash
sudo curl -o /etc/yum.repos.d/msprod.repo https://packages.microsoft.com/config/rhel/7/prod.repo
sudo yum install -y mssql-tools unixODBC-devel
```
 
> [!NOTE] 
> Dla wygody należy dodać /opt/mssql-tools/bin/ do zmiennej środowiskowej PATH. Dzięki temu można uruchomić narzędzia bez określania pełnej ścieżki. Uruchom następujące polecenia, aby zmodyfikować zmienną PATH zarówno dla sesji logowania, jak i sesji interaktywnych/bez logowania:</br></br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile`</br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc`</br>
`source ~/.bashrc`


### <a name="check-the-status-of-the-sql-server"></a>Sprawdzanie stanu programu SQL Server

Po zakończeniu pracy z konfiguracją można sprawdzić stan programu SQL Server i sprawdzić, czy jest on uruchomiony:

```bash
systemctl status mssql-server --no-pager
```

Powinny zostać wyświetlone następujące dane wyjściowe:

```output
● mssql-server.service - Microsoft SQL Server Database Engine
   Loaded: loaded (/usr/lib/systemd/system/mssql-server.service; enabled; vendor preset: disabled)
   Active: active (running) since Thu 2019-12-05 17:30:55 UTC; 20min ago
     Docs: https://docs.microsoft.com/en-us/sql/linux
 Main PID: 11612 (sqlservr)
   CGroup: /system.slice/mssql-server.service
           ├─11612 /opt/mssql/bin/sqlservr
           └─11640 /opt/mssql/bin/sqlservr
```

## <a name="configure-sql-server-always-on-availability-group"></a>Konfigurowanie grupy dostępności programu SQL Server always on

Aby skonfigurować grupę dostępności programu SQL Server Always On Availability Group dla maszyn wirtualnych, należy wykonać następujące kroki. Aby uzyskać więcej informacji, zobacz [konfigurowanie grupy dostępności programu SQL Server Always On Availability Group, aby uzyskać wysoką dostępność w systemie Linux](/sql/linux/sql-server-linux-availability-group-configure-ha)

### <a name="enable-alwayson-availability-groups-and-restart-mssql-server"></a>Włącz grupy dostępności AlwaysOn i uruchom ponownie mssql-server

Włącz grupy dostępności AlwaysOn w każdym węźle obsługującym wystąpienie programu SQL Server. Następnie uruchom ponownie mssql-server. Uruchom następujący skrypt:

```
sudo /opt/mssql/bin/mssql-conf set hadr.hadrenabled 1
sudo systemctl restart mssql-server
```

### <a name="create-a-certificate"></a>Tworzenie certyfikatu

Obecnie nie obsługujemy uwierzytelniania usługi AD do punktu końcowego AG. W związku z tym musimy użyć certyfikatu dla szyfrowania punktów końcowych AG.

1. Połącz się **ze wszystkimi węzłami** przy użyciu programu SQL Server Management Studio (SSMS) lub SQL CMD. Uruchom następujące polecenia, aby włączyć AlwaysOn_health sesji i utworzyć klucz główny:

    > [!IMPORTANT]
    > Jeśli łączysz się zdalnie z wystąpieniem programu SQL Server, musisz mieć otwarty port 1433 na zaporze. Należy również zezwolić na połączenia przychodzące do portu 1433 w sieci nsg dla każdej maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Tworzenie reguły zabezpieczeń](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) do tworzenia reguły zabezpieczeń przychodzących.

    - Wymień je `<Master_Key_Password>` własnym hasłem.


    ```sql
    ALTER EVENT SESSION  AlwaysOn_health ON SERVER WITH (STARTUP_STATE=ON);
    GO
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Master_Key_Password>';
    ```

 
1. Połącz się z repliką podstawową przy użyciu usługi SSMS lub CMD SQL. Poniższe polecenia utworzą certyfikat `/var/opt/mssql/data/dbm_certificate.cer` w podstawowej `var/opt/mssql/data/dbm_certificate.pvk` replice programu SQL Server i klucz prywatny:

    - Wymień je `<Private_Key_Password>` własnym hasłem.

```sql
CREATE CERTIFICATE dbm_certificate WITH SUBJECT = 'dbm';
GO

BACKUP CERTIFICATE dbm_certificate
   TO FILE = '/var/opt/mssql/data/dbm_certificate.cer'
   WITH PRIVATE KEY (
           FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
           ENCRYPTION BY PASSWORD = '<Private_Key_Password>'
       );
GO
```

Zamknij sesję CMD SQL, `exit` uruchamiając polecenie i wróć do sesji SSH.
 
### <a name="copy-the-certificate-to-the-secondary-replicas-and-create-the-certificates-on-the-server"></a>Kopiowanie certyfikatu do replik pomocniczych i tworzenie certyfikatów na serwerze

1. Skopiuj dwa pliki utworzone w tej samej lokalizacji na wszystkich serwerach, na których będą hostować repliki dostępności.
 
    Na serwerze podstawowym uruchom `scp` następujące polecenie, aby skopiować certyfikat na serwery docelowe:

    - Zastąp `<username>` i `<VM2>` nazwę użytkownika i docelową nazwę maszyny Wirtualnej, której używasz.
    - Uruchom to polecenie dla wszystkich replik pomocniczych.

    > [!NOTE]
    > Nie musisz uruchamiać `sudo -i`, co daje środowisko główne. Można po prostu `sudo` uruchomić polecenie przed każdym poleceniem, jak poprzednio w tym samouczku.

    ```bash
    # The below command allows you to run commands in the root environment
    sudo -i
    ```

    ```bash
    scp /var/opt/mssql/data/dbm_certificate.* <username>@<VM2>:/home/<username>
    ```

1. Na serwerze docelowym uruchom następujące polecenie:

    - Zamień `<username>` na nazwę użytkownika.
    - Polecenie `mv` przenosi pliki lub katalog z jednego miejsca do drugiego.
    - Polecenie `chown` służy do zmiany właściciela i grupy plików, katalogów lub łączy.
    - Uruchom te polecenia dla wszystkich replik pomocniczych.

    ```bash
    sudo -i
    mv /home/<username>/dbm_certificate.* /var/opt/mssql/data/
    cd /var/opt/mssql/data
    chown mssql:mssql dbm_certificate.*
    ```

1. Poniższy skrypt Transact-SQL tworzy certyfikat z kopii zapasowej utworzonej w podstawowej replice programu SQL Server. Zaktualizuj skrypt za pomocą silnych haseł. Hasło odszyfrowywania jest tym samym hasłem, które zostało użyte do utworzenia pliku pvk w poprzednim kroku. Aby utworzyć certyfikat, uruchom następujący skrypt przy użyciu cmd SQL lub SSMS na wszystkich serwerach pomocniczych:

    ```sql
    CREATE CERTIFICATE dbm_certificate
        FROM FILE = '/var/opt/mssql/data/dbm_certificate.cer'
        WITH PRIVATE KEY (
        FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
        DECRYPTION BY PASSWORD = '<Private_Key_Password>'
                );
    GO
    ```

### <a name="create-the-database-mirroring-endpoints-on-all-replicas"></a>Tworzenie dublowania punktów końcowych bazy danych we wszystkich replikach

Uruchom następujący skrypt we wszystkich wystąpieniach SQL przy użyciu cmd SQL lub SSMS:

```sql
CREATE ENDPOINT [Hadr_endpoint]
    AS TCP (LISTENER_PORT = 5022)
    FOR DATABASE_MIRRORING (
    ROLE = ALL,
    AUTHENTICATION = CERTIFICATE dbm_certificate,
ENCRYPTION = REQUIRED ALGORITHM AES
);
GO

ALTER ENDPOINT [Hadr_endpoint] STATE = STARTED;
GO
```

### <a name="create-the-availability-group"></a>Tworzenie grupy dostępności

Połącz się z wystąpieniem programu SQL Server, w którym znajduje się replika podstawowa przy użyciu cmd SQL lub SSMS. Uruchom następujące polecenie, aby utworzyć grupę dostępności:

- Zamień `ag1` żądaną nazwę grupy dostępności.
- Zastąp `<VM1>`, `<VM2>`i `<VM3>` wartości z nazwami wystąpień programu SQL Server, które hostują repliki.

```sql
CREATE AVAILABILITY GROUP [ag1]
     WITH (DB_FAILOVER = ON, CLUSTER_TYPE = EXTERNAL)
     FOR REPLICA ON
         N'<VM1>'
          WITH (
             ENDPOINT_URL = N'tcp://<VM1>:5022',
             AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
             FAILOVER_MODE = EXTERNAL,
             SEEDING_MODE = AUTOMATIC
             ),
         N'<VM2>'
          WITH (
             ENDPOINT_URL = N'tcp://<VM2>:5022',
             AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
             FAILOVER_MODE = EXTERNAL,
             SEEDING_MODE = AUTOMATIC
             ),
         N'<VM3>'
         WITH(
            ENDPOINT_URL = N'tcp://<VM3>:5022',
            AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
            FAILOVER_MODE = EXTERNAL,
            SEEDING_MODE = AUTOMATIC
            );
GO

ALTER AVAILABILITY GROUP [ag1] GRANT CREATE ANY DATABASE;
GO
```

### <a name="create-a-sql-server-login-for-pacemaker"></a>Tworzenie logowania do programu SQL Server dla rozrusznika serca

Na wszystkich serwerach SQL utwórz identyfikator SQL dla rozrusznika serca. Następujące Transact-SQL tworzy login.

- Wymień `<password>` własne złożone hasło.

```sql
USE [master]
GO

CREATE LOGIN [pacemakerLogin] with PASSWORD= N'<password>';
GO

ALTER SERVER ROLE [sysadmin] ADD MEMBER [pacemakerLogin];
GO
```

Na wszystkich serwerach SQL zapisz poświadczenia używane do logowania programu SQL Server. 

1. Utwórz plik:

    ```bash
    sudo vi /var/opt/mssql/secrets/passwd
    ```

1. Dodaj do pliku następujące 2 wiersze:

    ```bash
    pacemakerLogin
    <password>
    ```

    Aby zamknąć edytor **vi,** najpierw naciśnij klawisz **Esc,** a następnie wprowadź polecenie, `:wq` aby napisać plik i zamknąć.

1. Spraw, aby plik był czytelny tylko przez katalog główny:

    ```bash
    sudo chown root:root /var/opt/mssql/secrets/passwd
    sudo chmod 400 /var/opt/mssql/secrets/passwd
    ```

### <a name="join-secondary-replicas-to-the-availability-group"></a>Dołączanie replik pomocniczych do grupy dostępności

1. Aby dołączyć do replik pomocniczych do ag, musisz otworzyć port 5022 na zaporze dla wszystkich serwerów. Uruchom następujące polecenie w sesji SSH:

    ```bash
    sudo firewall-cmd --zone=public --add-port=5022/tcp --permanent
    sudo firewall-cmd --reload
    ```

1. W replikach pomocniczych uruchom następujące polecenia, aby dołączyć je do ag:

    ```sql
    ALTER AVAILABILITY GROUP [ag1] JOIN WITH (CLUSTER_TYPE = EXTERNAL);
    GO

    ALTER AVAILABILITY GROUP [ag1] GRANT CREATE ANY DATABASE;
    GO
    ```

1. Uruchom następujący skrypt Transact-SQL w replice podstawowej i każdej replice pomocniczej:

    ```sql
    GRANT ALTER, CONTROL, VIEW DEFINITION ON AVAILABILITY GROUP::ag1 TO pacemakerLogin;
    GO
    
    GRANT VIEW SERVER STATE TO pacemakerLogin;
    GO
    ```

1. Po dołączeniu replik pomocniczych można je wyświetlić w Eksploratorze obiektów SSMS, rozwijając węzeł **Zawsze przy wysokiej dostępności:**

    ![dostępność-group-joined.png](media/sql-server-linux-rhel-ha-stonith-tutorial/availability-group-joined.png)

### <a name="add-a-database-to-the-availability-group"></a>Dodawanie bazy danych do grupy dostępności

Będziemy śledzić [artykuł grupy konfiguracji dostępności na temat dodawania bazy danych](/sql/linux/sql-server-linux-availability-group-configure-ha#add-a-database-to-the-availability-group).

W tym kroku są używane następujące polecenia Transact-SQL. Uruchom następujące polecenia w replice podstawowej:

```sql
CREATE DATABASE [db1]; -- creates a database named db1
GO

ALTER DATABASE [db1] SET RECOVERY FULL; -- set the database in full recovery mode
GO

BACKUP DATABASE [db1] -- backs up the database to disk
   TO DISK = N'/var/opt/mssql/data/db1.bak';
GO

ALTER AVAILABILITY GROUP [ag1] ADD DATABASE [db1]; -- adds the database db1 to the AG
GO
```

### <a name="verify-that-the-database-is-created-on-the-secondary-servers"></a>Sprawdzanie, czy baza danych jest tworzona na serwerach pomocniczych

W każdej pomocniczej replice programu SQL Server uruchom następującą kwerendę, aby sprawdzić, czy baza danych db1 została utworzona i jest w stanie SYNCHRONIZED:

```
SELECT * FROM sys.databases WHERE name = 'db1';
GO
SELECT DB_NAME(database_id) AS 'database', synchronization_state_desc FROM sys.dm_hadr_database_replica_states;
```

Jeśli `synchronization_state_desc` lista zsynchronizowane dla `db1`, oznacza to, że repliki są synchronizowane. Pomocnicze są `db1` wyświetlane w replice podstawowej.

## <a name="create-availability-group-resources-in-the-pacemaker-cluster"></a>Tworzenie zasobów grupy dostępności w klastrze rozrusznika serca

Będziemy kierować się przewodnikiem, aby [utworzyć zasoby grupy dostępności w klastrze rozrusznika serca](/sql/linux/sql-server-linux-create-availability-group#create-the-availability-group-resources-in-the-pacemaker-cluster-external-only).

### <a name="create-the-ag-cluster-resource"></a>Tworzenie zasobu klastra AG

1. Użyj następującego polecenia, `ag_cluster` aby utworzyć zasób w grupie `ag1`dostępności .

    ```bash
    sudo pcs resource create ag_cluster ocf:mssql:ag ag_name=ag1 meta failure-timeout=30s master notify=true
    ```

1. Sprawdź swój zasób i upewnij się, że są one w trybie online przed kontynuowaniem przy użyciu następującego polecenia:

    ```bash
    sudo pcs resource
    ```

    Powinny zostać wyświetlone następujące dane wyjściowe:

    ```output
    [<username>@VM1 ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
    Masters: [ <VM1> ]
    Slaves: [ <VM2> <VM3> ]
    ```

### <a name="create-a-virtual-ip-resource"></a>Tworzenie wirtualnego zasobu IP

1. Użyj dostępnego statycznego adresu IP z sieci, aby utworzyć wirtualny zasób IP. Można go znaleźć za `nmap`pomocą narzędzia poleceń .

    ```bash
    nmap -sP <IPRange>
    # For example: nmap -sP 10.0.0.*
    # The above will scan for all IP addresses that are already occupied in the 10.0.0.x space.
    ```

1. Ustawianie właściwości **z włączoną funkcją stonith** na false

    ```bash
    sudo pcs property set stonith-enabled=false
    ```

1. Utwórz wirtualny zasób IP za pomocą następującego polecenia:

    - Zastąp poniższą `<availableIP>` wartość nieużytym adresem IP.

    ```bash
    sudo pcs resource create virtualip ocf:heartbeat:IPaddr2 ip=<availableIP>
    ```

### <a name="add-constraints"></a>Dodawanie ograniczeń

1. Aby upewnić się, że adres IP i zasób AG są uruchomione w tym samym węźle, należy skonfigurować ograniczenie kolokacji. Uruchom następujące polecenie:

    ```bash
    sudo pcs constraint colocation add virtualip ag_cluster-master INFINITY with-rsc-role=Master
    ```

1. Utwórz ograniczenie zamawiania, aby upewnić się, że zasób AG jest uruchomiony przed adresem IP. Podczas gdy ograniczenie kolokacji implikuje ograniczenie porządkowania, wymusza je.

    ```bash
    sudo pcs constraint order promote ag_cluster-master then start virtualip
    ```

1. Aby zweryfikować ograniczenia, uruchom następujące polecenie:

    ```bash
    sudo pcs constraint list --full
    ```

    Powinny zostać wyświetlone następujące dane wyjściowe:

    ```
    Location Constraints:
    Ordering Constraints:
          promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
    Colocation Constraints:
          virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

### <a name="re-enable-stonith"></a>Ponownie włącz stonith

Jesteśmy gotowi do testów. Ponownie włącz stonith w klastrze, uruchamiając następujące polecenie w węźle 1:

```bash
sudo pcs property set stonith-enabled=true
```

### <a name="check-cluster-status"></a>Sprawdzanie stanu klastra

Stan zasobów klastra można sprawdzić za pomocą następującego polecenia:

```output
[<username>@VM1 ~]$ sudo pcs status
Cluster name: az-hacluster
Stack: corosync
Current DC: <VM3> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
Last updated: Sat Dec  7 00:18:38 2019
Last change: Sat Dec  7 00:18:02 2019 by root via cibadmin on VM1

3 nodes configured
5 resources configured

Online: [ <VM1> <VM2> <VM3> ]

Full list of resources:

 Master/Slave Set: ag_cluster-master [ag_cluster]
     Masters: [ <VM2> ]
     Slaves: [ <VM1> <VM3> ]
 virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
 rsc_st_azure   (stonith:fence_azure_arm):      Started <VM1>

Daemon Status:
  corosync: active/enabled
  pacemaker: active/enabled
  pcsd: active/enabled
```

## <a name="test-failover"></a>Testowanie pracy w trybie failover

Aby upewnić się, że konfiguracja powiodła się do tej pory, przetestujemy tryb failover. Aby uzyskać więcej informacji, zobacz [Praca awaryjna grupy Zawsze na dostępności w systemie Linux](/sql/linux/sql-server-linux-availability-group-failover-ha).

1. Uruchom następujące polecenie, aby ręcznie przemorować replikę podstawową do `<VM2>`. Zamień `<VM2>` na wartość nazwy serwera.

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

1. Jeśli ponownie sprawdzisz swoje ograniczenia, zobaczysz, że inne ograniczenie zostało dodane z powodu ręcznego trybu failover:

    ```output
    [<username>@VM1 ~]$ sudo pcs constraint list --full
    Location Constraints:
          Resource: ag_cluster-master
            Enabled on: VM2 (score:INFINITY) (role: Master) (id:cli-prefer-ag_cluster-master)
    Ordering Constraints:
            promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
    Colocation Constraints:
            virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

1. Usuń ograniczenie z `cli-prefer-ag_cluster-master` identyfikatorem za pomocą następującego polecenia:

    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

1. Sprawdź zasoby klastra `sudo pcs resource`za pomocą polecenia i powinieneś `<VM2>`zobaczyć, że wystąpienie podstawowe jest teraz .

    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
         ag_cluster (ocf::mssql:ag):        FAILED <VM1> (Monitoring)
         Masters: [ <VM2> ]
         Slaves: [ <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
         Masters: [ <VM2> ]
         Slaves: [ <VM1> <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
    ```

## <a name="test-fencing"></a>Ogrodzenia testowe

Stonith można przetestować, uruchamiając następujące polecenie. Spróbuj uruchomić poniższe `<VM1>` `<VM3>`polecenie z for .

```bash
sudo pcs stonith fence <VM3> --debug
```

> [!NOTE]
> Domyślnie akcja ogrodzenia powoduje wyłączenie i wyłączenie węzła. Jeśli chcesz tylko przesunąć węzeł `--off` w tryb offline, użyj tej opcji w poleceniu.

Powinieneś uzyskać następujące dane wyjściowe:

```output
[<username>@<VM1> ~]$ sudo pcs stonith fence <VM3> --debug
Running: stonith_admin -B <VM3>
Return Value: 0
--Debug Output Start--
--Debug Output End--
 
Node: <VM3> fenced
```
Aby uzyskać więcej informacji na temat testowania urządzenia ogrodzeniowego, zobacz następujący artykuł [Red Hat.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/s1-stonithtest-haar)

## <a name="next-steps"></a>Następne kroki

Aby korzystać z odbiornika grupy dostępności dla serwerów SQL, należy utworzyć i skonfigurować moduł równoważenia obciążenia.

> [!div class="nextstepaction"]
> [Samouczek: Konfigurowanie detektora grupy dostępności dla programu SQL Server na maszynach wirtualnych RHEL na platformie Azure](sql-server-linux-rhel-ha-listener-tutorial.md)

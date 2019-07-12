---
title: Wdróż i Skonfiguruj zaporę platformy Azure przy użyciu wiersza polecenia platformy Azure
description: W tym artykule dowiesz się, jak wdrożyć i skonfigurować zaporę platformy Azure przy użyciu wiersza polecenia platformy Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.date: 7/10/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 24954eecde58c978fa3e14bb3a2d411d708687a3
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707157"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-cli"></a>Wdróż i Skonfiguruj zaporę platformy Azure przy użyciu wiersza polecenia platformy Azure

Kontrolowanie dostępu do sieciowego ruchu wychodzącego jest ważną częścią ogólnego planu zabezpieczeń sieci. Na przykład możesz ograniczyć dostęp do witryn sieci web. Lub możesz chcieć ograniczyć wychodzące adresy IP i portów, które mogą być udostępniane.

Jednym ze sposobów kontrolowania dostępu do sieciowego ruchu wychodzącego z podsieci platformy Azure jest użycie usługi Azure Firewall. Za pomocą usługi Azure Firewall można skonfigurować następujące reguły:

* Reguły aplikacji, które definiują w pełni kwalifikowane nazwy domen (FQDN), do których można uzyskać dostęp z podsieci. Nazwa FQDN może również [obejmują wystąpień SQL](sql-fqdn-filtering.md).
* Reguły sieci, które definiują adres źródłowy, protokół, port docelowy i adres docelowy.

Ruch sieciowy podlega skonfigurowanym regułom zapory podczas kierowania ruchu sieciowego do zapory jako bramy domyślnej podsieci.

W tym artykule uproszczone pojedynczej sieci wirtualnej jest tworzony z trzech podsieci w celu łatwiejszego wdrażania. W przypadku wdrożeń produkcyjnych [modelu topologi gwiaździstej](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) jest zalecane. Zapora jest we własnej sieci wirtualnej. Obciążenia są serwery w wirtualnych sieciach równorzędnych, w tym samym regionie przy użyciu co najmniej jednej podsieci.

* **AzureFirewallSubnet** — w tej podsieci znajduje się zapora.
* **Workload-SN** — w tej podsieci znajduje się serwer obciążeń. Ruch sieciowy tej podsieci przechodzi przez zaporę.
* **Jump-SN** — w tej podsieci znajduje się serwer przesiadkowy. Serwer przesiadkowy ma publiczny adres IP, z którym możesz nawiązać połączenie przy użyciu pulpitu zdalnego. Stamtąd możesz połączyć się (przy użyciu innego pulpitu zdalnego) z serwerem obciążeń.

![Infrastruktura sieci samouczka](media/tutorial-firewall-rules-portal/Tutorial_network.png)

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Konfigurowanie testowego środowiska sieciowego
> * Wdrażanie zapory
> * Tworzenie trasy domyślnej
> * Skonfiguruj reguły aplikacji, aby umożliwić dostęp do www.google.com
> * Konfigurowanie reguły sieci w celu umożliwienia dostępu do zewnętrznych serwerów DNS
> * Testowanie zapory

Jeśli wolisz, możesz wykonać tej procedury przy użyciu [witryny Azure portal](tutorial-firewall-deploy-portal.md) lub [programu Azure PowerShell](deploy-ps.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, uruchom interfejs wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Aby znaleźć wersję, uruchom polecenie **az --version**. Aby uzyskać informacje o instalowaniu lub uaktualnianiu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

Zainstaluj rozszerzenie zapory usługi Azure:

```azurecli-interactive
az extension add -n azure-firewall
```


## <a name="set-up-the-network"></a>Konfigurowanie sieci

Najpierw utwórz grupę zasobów zawierającą zasoby wymagane do wdrożenia zapory. Następnie utwórz sieć wirtualną, podsieci i serwery do obsługi testowania.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów zawiera wszystkie zasoby dla wdrożenia.

```azurecli-interactive
az group create --name Test-FW-RG --location eastus
```

### <a name="create-a-vnet"></a>Tworzenie sieci wirtualnej

Ta sieć wirtualna ma trzy podsieci.

> [!NOTE]
> Minimalny rozmiar podsieci AzureFirewallSubnet to /26.

```azurecli-interactive
az network vnet create \
  --name Test-FW-VN \
  --resource-group Test-FW-RG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name AzureFirewallSubnet \
  --subnet-prefix 10.0.1.0/24
az network vnet subnet create \
  --name Workload-SN \
  --resource-group Test-FW-RG \
  --vnet-name Test-FW-VN   \
  --address-prefix 10.0.2.0/24
az network vnet subnet create \
  --name Jump-SN \
  --resource-group Test-FW-RG \
  --vnet-name Test-FW-VN   \
  --address-prefix 10.0.3.0/24
```

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Teraz utwórz maszyny wirtualne przesiadkową i obciążeń, a następnie umieść je w odpowiednich podsieciach.
Po wyświetleniu monitu wpisz hasło dla maszyny wirtualnej.

Utwórz maszynę wirtualną szybkie Srv.

```azurecli-interactive
az vm create \
    --resource-group Test-FW-RG \
    --name Srv-Jump \
    --location eastus \
    --image win2016datacenter \
    --vnet-name Test-FW-VN \
    --subnet Jump-SN \
    --admin-username azureadmin
az vm open-port --port 3389 --resource-group Test-FW-RG --name Srv-Jump
```



Utwórz kartę Sieciową dla pracy Srv z określonych adresów IP serwerów DNS i publicznego adresu IP na potrzeby testów.

```azurecli-interactive
az network nic create \
    -g Test-FW-RG \
    -n Srv-Work-NIC \
   --vnet-name Test-FW-VN \
   --subnet Workload-SN \
   --public-ip-address "" \
   --dns-servers 209.244.0.3 209.244.0.4
```

Teraz można tworzyć obciążenia maszyny wirtualnej.
Po wyświetleniu monitu wpisz hasło dla maszyny wirtualnej.

```azurecli-interactive
az vm create \
    --resource-group Test-FW-RG \
    --name Srv-Work \
    --location eastus \
    --image win2016datacenter \
    --nics Srv-Work-NIC \
    --admin-username azureadmin
```

## <a name="deploy-the-firewall"></a>Wdrażanie zapory

Teraz można wdrożyć zaporę w sieci wirtualnej.

```azurecli-interactive
az network firewall create \
    --name Test-FW01 \
    --resource-group Test-FW-RG \
    --location eastus
az network public-ip create \
    --name fw-pip \
    --resource-group Test-FW-RG \
    --location eastus \
    --allocation-method static \
    --sku standard
az network firewall ip-config create \
    --firewall-name Test-FW01 \
    --name FW-config \
    --public-ip-address fw-pip \
    --resource-group Test-FW-RG \
    --vnet-name Test-FW-VN
az network firewall update \
    --name Test-FW01 \
    --resource-group Test-FW-RG 
az network public-ip show \
    --name fw-pip \
    --resource-group Test-FW-RG
fwprivaddr="$(az network firewall ip-config list -g Test-FW-RG -f Test-FW01 --query "[?name=='FW-config'].privateIpAddress" --output tsv)"
```

Zanotuj prywatny adres IP. Użyjesz go później podczas tworzenia trasy domyślnej.

## <a name="create-a-default-route"></a>Tworzenie trasy domyślnej

Utwórz tabelę, z wyłączyć propagację tras protokołu BGP

```azurecli-interactive
az network route-table create \
    --name Firewall-rt-table \
    --resource-group Test-FW-RG \
    --location eastus \
    --disable-bgp-route-propagation true
```

Utwórz trasę.

```azurecli-interactive
az network route-table route create \
  --resource-group Test-FW-RG \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $fwprivaddr
```

Kojarzenie tabeli tras do podsieci

```azurecli-interactive
az network vnet subnet update \
    -n Workload-SN \
    -g Test-FW-RG \
    --vnet-name Test-FW-VN \
    --address-prefixes 10.0.2.0/24 \
    --route-table Firewall-rt-table
```

## <a name="configure-an-application-rule"></a>Konfigurowanie reguły aplikacji

Zasada aplikacji umożliwia dostęp ruchu wychodzącego do www.google.com.

```azurecli-interactive
az network firewall application-rule create \
   --collection-name App-Coll01 \
   --firewall-name Test-FW01 \
   --name Allow-Google \
   --protocols Http=80 Https=443 \
   --resource-group Test-FW-RG \
   --target-fqdns www.google.com \
   --source-addresses 10.0.2.0/24 \
   --priority 200 \
   --action Allow
```

Usługa Azure Firewall zawiera wbudowaną kolekcję reguł dla nazw FQDN infrastruktury, które domyślnie są dozwolone. Te nazwy FQDN są specyficzne dla platformy i nie można ich używać do innych celów. Aby uzyskać więcej informacji, zobacz [Infrastrukturalne nazwy FQDN](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Konfigurowanie reguły sieci

Zasada sieci umożliwia dostęp ruchu wychodzącego do dwóch adresów IP na port 53 (DNS).

```azurecli-interactive
az network firewall network-rule create \
   --collection-name Net-Coll01 \
   --destination-addresses 209.244.0.3 209.244.0.4 \
   --destination-ports 53 \
   --firewall-name Test-FW01 \
   --name Allow-DNS \
   --protocols UDP \
   --resource-group Test-FW-RG \
   --priority 200 \
   --source-addresses 10.0.2.0/24 \
   --action Allow
```

## <a name="test-the-firewall"></a>Testowanie zapory

Teraz należy sprawdzić, zapory, aby upewnić się, że działa zgodnie z oczekiwaniami.

1. Należy pamiętać, prywatnego adresu IP dla **pracy Srv** maszyny wirtualnej:

   ```azurecli-interactive
   az vm list-ip-addresses \
   -g Test-FW-RG \
   -n Srv-Work
   ```

1. Pulpit zdalny, aby połączyć **szybkie Srv** maszynę wirtualną i zaloguj się. Z tego miejsca Otwórz Podłączanie pulpitu zdalnego z **pracy Srv** prywatny adres IP i logowania.

3. Na **pracy SRV**, Otwórz okno programu PowerShell i uruchom następujące polecenia:

   ```
   nslookup www.google.com
   nslookup www.microsoft.com
   ```

   Oba polecenia powinno zwrócić odpowiedzi, pokazujący, że zapytania DNS są przejściem przez zaporę.

1. Uruchom następujące polecenia:

   ```
   Invoke-WebRequest -Uri https://www.google.com
   Invoke-WebRequest -Uri https://www.google.com

   Invoke-WebRequest -Uri https://www.microsoft.com
   Invoke-WebRequest -Uri https://www.microsoft.com
   ```

   Żądania www.google.com ma być pomyślnie wykonane, a żądania www.microsoft.com powinna zakończyć się niepowodzeniem. Oznacza to, że reguły zapory działają zgodnie z oczekiwaniami.

Teraz gdy masz pewność, czy działają reguły zapory:

* Możesz rozpoznać nazwy DNS przy użyciu skonfigurowanego zewnętrznego serwera DNS.
* Możesz przejść do jednej z dozwolonych nazw FQDN, ale nie do innych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Możesz zachować swoje zasoby zapory do następnego samouczka, lub jeśli nie będą już potrzebne, Usuń **Test-PD-RG** grupę zasobów, aby usunąć wszystkie zasoby związane z zapory:

```azurecli-interactive
az group delete \
  -n Test-FW-RG
```

## <a name="next-steps"></a>Kolejne kroki

* [Samouczek: Monitorowanie dzienników usługi Azure Firewall](./tutorial-diagnostics.md)

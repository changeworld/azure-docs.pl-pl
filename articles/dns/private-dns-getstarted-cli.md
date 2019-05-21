---
title: Tworzenie strefy prywatnej usługi Azure DNS przy użyciu interfejsu wiersza polecenia platformy Azure
description: W tym samouczku utworzysz i przetestujesz strefę prywatną i rekord DNS w usłudze Azure DNS. W tym szczegółowym przewodniku pokazano, jak po raz pierwszy utworzyć strefę prywatną i rekord DNS przy użyciu interfejsu wiersza polecenia platformy Azure oraz zarządzać nimi.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: 2758817d58fdd2e80b302b5f833308dbde1a6b63
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "65916050"
---
# <a name="create-an-azure-dns-private-zone-using-the-azure-cli"></a>Tworzenie strefy prywatnej usługi Azure DNS przy użyciu interfejsu wiersza polecenia platformy Azure

W tym samouczku przedstawiono kroki umożliwiające utworzenie po raz pierwszy strefy prywatnej i rekordu DNS przy użyciu interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Strefa DNS jest używana do hostowania rekordów DNS dla określonej domeny. Aby rozpocząć hostowanie domeny w usłudze Azure DNS, musisz utworzyć strefę DNS dla tej nazwy domeny. Każdy rekord DNS domeny zostanie utworzony w tej strefie DNS. Aby opublikować prywatną strefę DNS w sieci wirtualnej, musisz określić listę sieci wirtualnych, które mogą rozpoznawać rekordy w strefie.  Są to tak zwane *sieci wirtualne rozpoznawania*. Możesz również określić sieć wirtualną, dla której usługa Azure DNS utrzymuje rekordy nazw hosta zawsze, gdy maszyna wirtualna jest tworzona, zmienia protokół internetowy lub jest usuwana.  Jest to tak zwana *sieć wirtualna rejestracji*.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie strefy prywatnej DNS
> * Tworzenie testowych maszyn wirtualnych
> * Tworzenie dodatkowego rekordu DNS
> * Testowanie strefy prywatnej

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Jeśli chcesz, możesz wykonać kroki tego samouczka przy użyciu [programu Azure PowerShell](private-dns-getstarted-powershell.md).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-group"></a>Tworzenie grupy zasobów

Najpierw utwórz grupę zasobów, która będzie zawierać strefę DNS: 

```azurecli
az group create --name MyAzureResourceGroup --location "East US"
```

## <a name="create-a-dns-private-zone"></a>Tworzenie strefy prywatnej DNS

Strefa DNS jest tworzona przy użyciu polecenia `az network dns zone create` i wartości *Private* parametru **ZoneType**. Poniższy przykład tworzy strefę DNS o nazwie **private.contoso.com** w grupie zasobów o nazwie **MyAzureResourceGroup** i udostępnia strefę DNS w sieci wirtualnej o nazwie  **MyAzureVnet**.

W przypadku pominięcia parametru **ZoneType** strefa zostanie utworzona jako publiczna, dlatego jest on wymagany do utworzenia strefy prywatnej.

```azurecli
az network vnet create \
  --name myAzureVNet \
  --resource-group MyAzureResourceGroup \
  --location eastus \
  --address-prefix 10.2.0.0/16 \
  --subnet-name backendSubnet \
  --subnet-prefixes 10.2.0.0/24

az network dns zone create -g MyAzureResourceGroup \
   -n private.contoso.com \
  --zone-type Private \
  --registration-vnets myAzureVNet
```

Jeśli chcesz utworzyć strefę tylko do rozpoznawania nazw (bez automatycznego tworzenia nazwy hosta), możesz użyć parametru *resolution-vnets* zamiast parametru *registration-vnets*.

> [!NOTE]
> Nie będzie można zobaczyć rekordów automatycznie tworzonych nazw hosta. Jednak później przeprowadzisz testowanie, aby sprawdzić, czy istnieją.

### <a name="list-dns-private-zones"></a>Wyświetlanie listy stref prywatnych DNS

Aby wyliczyć strefy DNS, należy użyć polecenia `az network dns zone list`. Aby uzyskać pomoc, zobacz `az network dns zone list --help`.

Określenie grupy zasobów powoduje wyświetlenie listy tylko następujących stref w grupie zasobów:

```azurecli
az network dns zone list \
  --resource-group MyAzureResourceGroup
```

Pominięcie grupy zasobów powoduje wyświetlenie listy wszystkich stref w subskrypcji:

```azurecli
az network dns zone list 
```

## <a name="create-the-test-virtual-machines"></a>Tworzenie testowych maszyn wirtualnych

Teraz utworzysz dwie maszyny wirtualne, aby umożliwić przetestowanie strefy prywatnej DNS:

```azurecli
az vm create \
 -n myVM01 \
 --admin-username test-user \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --image win2016datacenter

az vm create \
 -n myVM02 \
 --admin-username test-user \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --image win2016datacenter
```

Ukończenie tej operacji potrwa kilka minut.

## <a name="create-an-additional-dns-record"></a>Tworzenie dodatkowego rekordu DNS

Aby utworzyć rekord DNS, użyj polecenia `az network dns record-set [record type] add-record`. Aby uzyskać pomoc dotyczącą dodawania na przykład rekordów A, zobacz `azure network dns record-set A add-record --help`.

 Poniższy przykład tworzy rekord o nazwie względnej **db** w strefie DNS **private.contoso.com**, w grupie zasobów **MyAzureResourceGroup**. W pełni kwalifikowana nazwa zestawu rekordów to **db.private.contoso.com**. Typ rekordu to „A” z adresem IP „10.2.0.4”.

```azurecli
az network dns record-set a add-record \
  -g MyAzureResourceGroup \
  -z private.contoso.com \
  -n db \
  -a 10.2.0.4
```

### <a name="view-dns-records"></a>Wyświetlanie rekordów DNS

Aby wyświetlić listę rekordów DNS w strefie, uruchom polecenie:

```azurecli
az network dns record-set list \
  -g MyAzureResourceGroup \
  -z private.contoso.com
```
Pamiętaj, że automatycznie tworzone rekordy A dla dwóch testowych maszyn wirtualnych nie będą widoczne.

## <a name="test-the-private-zone"></a>Testowanie strefy prywatnej

Teraz możesz sprawdzić rozpoznawanie nazw dla swojej **private.contoso.com** stref prywatnych.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Konfigurowanie maszyn wirtualnych w celu zezwolenia na ruch przychodzący protokołu ICMP

Do przetestowania rozpoznawania nazw możesz użyć polecenia ping. W tym celu skonfiguruj zaporę na obydwu maszynach wirtualnych, aby zezwolić na przychodzące pakiety protokołu ICMP.

1. Połącz się z maszyną wirtualną myVM01 i otwórz okno programu Windows PowerShell, używając uprawnień administratora.
2. Uruchom następujące polecenie:

   ```powershell
   New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
   ```

Powtórz dla maszyny wirtualnej myVM02.

### <a name="ping-the-vms-by-name"></a>Wysyłanie polecenia ping do maszyn wirtualnych według nazwy

1. W wierszu polecenia programu Windows PowerShell maszyny wirtualnej myVM02 wyślij polecenie ping do maszyny wirtualnej myVM01 przy użyciu automatycznie zarejestrowanej nazwy hosta:
   ```
   ping myVM01.private.contoso.com
   ```
   Powinny pojawić się dane wyjściowe podobne do następujących:
   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```
2. Teraz wyślij polecenie ping do utworzonej wcześniej nazwy **db**:
   ```
   ping db.private.contoso.com
   ```
   Powinny pojawić się dane wyjściowe podobne do następujących:
   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>Usuwanie wszystkich zasobów

Jeśli zasoby utworzone w tym samouczku nie są już potrzebne, możesz je usunąć, usuwając grupę zasobów **MyAzureResourceGroup**.

```azurecli
az group delete --name MyAzureResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku wdrożono strefę prywatną DNS, utworzono rekord DNS oraz przetestowano strefę.
Następnie możesz dowiedzieć się więcej na temat stref prywatnych DNS.

> [!div class="nextstepaction"]
> [Używanie usługi Azure DNS na potrzeby domen prywatnych](private-dns-overview.md)

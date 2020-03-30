---
title: Diagnozowanie problemu z routingiem sieci maszyn wirtualnych — interfejs wiersza polecenia platformy Azure
titleSuffix: Azure Network Watcher
description: W tym artykule dowiesz się, jak zdiagnozować problem routingu sieci maszyny wirtualnej przy użyciu funkcji następnego przeskoku usługi Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: damendo
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: ae139ea7aca7c3896fcd7b0acf2bf6673490a2f4
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382906"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-cli"></a>Diagnozowanie problemu z routingiem sieci maszyny wirtualnej — interfejs wiersza polecenia platformy Azure

W tym artykule można wdrożyć maszynę wirtualną (VM), a następnie sprawdzić komunikację na adres IP i adres URL. Określisz przyczynę niepowodzenia komunikacji oraz sposób rozwiązania problemu.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i używać interfejsu wiersza polecenia platformy Azure lokalnie, ten artykuł wymaga, aby uruchomić interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja została zainstalowana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Po zweryfikowaniu wersji interfejsu `az login` wiersza polecenia platformy Azure uruchom, aby utworzyć połączenie z platformą Azure. Polecenia interfejsu wiersza polecenia platformy Azure w tym artykule są sformatowane do uruchamiania w powłoce Bash.

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Przed utworzeniem maszyny wirtualnej musisz utworzyć grupę zasobów, która będzie zawierała maszynę wirtualną. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w lokalizacji *eastus:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm#az-vm-create). Jeśli klucze SSH nie istnieją jeszcze w domyślnej lokalizacji kluczy, to polecenie je utworzy. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`. Poniższy przykład tworzy maszynę wirtualną o nazwie *myVm:*

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Nie kontynuuj z pozostałymi krokami, dopóki maszyna wirtualna nie zostanie utworzona, a zestaw wiersza polecenia platformy Azure zwróci dane wyjściowe.

## <a name="test-network-communication"></a>Testowanie komunikacji sieciowej

Aby przetestować komunikację sieciową z obserwatorem sieci, należy najpierw włączyć obserwatora sieci w regionie, w którego znajduje się maszyna wirtualna, w której chcesz przetestować, a następnie użyć funkcji następnego przeskoku obserwatora sieci, aby przetestować komunikację.

### <a name="enable-network-watcher"></a>Włączanie usługi Network Watcher

Jeśli masz już włączonego obserwatora sieci w regionie Wschodnie stany USA, przejdź do [pozycji Użyj następnego przeskoku](#use-next-hop). Użyj polecenia [az network watcher configure,](/cli/azure/network/watcher#az-network-watcher-configure) aby utworzyć obserwatora sieci w regionie Wschodnie stany USA:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-next-hop"></a>Korzystanie z funkcji Następny przeskok

Na platformie Azure są automatycznie tworzone trasy do domyślnych miejsc docelowych. Możesz tworzyć trasy niestandardowe zastępujące domyślne trasy. Czasami użycie tras niestandardowych może spowodować niepowodzenie komunikacji. Aby przetestować routing z maszyny Wirtualnej, użyj [az network watcher show-next-hop,](/cli/azure/network/watcher?view=azure-cli-latest#az-network-watcher-show-next-hop) aby określić następny przeskok routingu, gdy ruch jest przeznaczony dla określonego adresu.

Przetestuj komunikację wychodzącą z maszyny wirtualnej do jednego z adresów IP domeny www.bing.com:

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 13.107.21.200 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

Po kilku sekundach dane wyjściowe informują, że **nextHopType** to **Internet,** a **routeTableId** to **Trasa systemowa**. Ten wynik informuje, że istnieje prawidłowa trasa do miejsca docelowego.

Przetestuj komunikację wychodzącą z maszyny wirtualnej do adresu 172.31.0.100:

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 172.31.0.100 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

Zwrócone dane wyjściowe informuje, że **Brak** jest **nextHopType**, i że **routeTableId** jest również **trasa systemowa**. Ten wynik oznacza, że istnieje prawidłowa trasa systemowa do miejsca docelowego, ale nie ma następnego przeskoku umożliwiającego kierowanie ruchu do miejsca docelowego.

## <a name="view-details-of-a-route"></a>Wyświetlanie szczegółów trasy

Aby dokładniej przeanalizować routing, przejrzyj skuteczne trasy interfejsu sieciowego za pomocą polecenia [az network nic show-effective-route-table:](/cli/azure/network/nic#az-network-nic-show-effective-route-table)

```azurecli-interactive
az network nic show-effective-route-table \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

Następujący tekst jest zawarty w zwróconym wyjściu:

```
{
  "additionalProperties": {
    "disableBgpRoutePropagation": false
  },
  "addressPrefix": [
    "0.0.0.0/0"
  ],
  "name": null,
  "nextHopIpAddress": [],
  "nextHopType": "Internet",
  "source": "Default",
  "state": "Active"
},
```

Gdy użyto `az network watcher show-next-hop` polecenia do testowania komunikacji wychodzącej do 13.107.21.200 w [Użyj następnego przeskoku,](#use-next-hop)trasa o **adresiePrefix** 0.0.0.0/0** została użyta do kierowania ruchu na adres, ponieważ żadna inna trasa w danych wyjściowych nie zawiera adresu. Domyślnie wszystkie adresy, które nie zostały określone w prefiksie adresu innej trasy, są kierowane do Internetu.

Gdy użyto `az network watcher show-next-hop` polecenia do testowania komunikacji wychodzącej do 172.31.0.100 jednak wynik poinformował, że nie było następnego typu przeskoku. W zwróconym danych wyjściowych widoczny jest również następujący tekst:

```
{
  "additionalProperties": {
    "disableBgpRoutePropagation": false
      },
  "addressPrefix": [
    "172.16.0.0/12"
  ],
  "name": null,
  "nextHopIpAddress": [],
  "nextHopType": "None",
  "source": "Default",
  "state": "Active"
},
```

Jak widać na wyjściu `az network watcher nic show-effective-route-table` z polecenia, choć istnieje domyślna trasa do prefiksu 172.16.0.0/12, który zawiera adres 172.31.0.100, **nextHopType** jest **Brak**. Platforma Azure tworzy domyślną trasę dla zakresu adresów 172.16.0.0/12, ale nie określa typu następnego przeskoku, jeśli nie jest to wymagane. Jeśli na przykład dodano zakres adresów 172.16.0.0/12 do przestrzeni adresowej sieci wirtualnej, platforma Azure zmieni **nextHopType** na **sieć wirtualną** dla trasy. Sprawdzanie będzie następnie **wyświetlić sieci wirtualnej** jako **nextHopType**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzono maszynę wirtualną i zdiagnozowano routing sieci z maszyny Wirtualnej. Uzyskano informacje o tworzeniu tras domyślnych na platformie Azure i przetestowano routing do dwóch różnych miejsc docelowych. Uzyskaj więcej informacji na temat [routingu na platformie Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) i dowiedz się, jak [tworzyć trasy niestandardowe](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

W przypadku wychodzących połączeń maszyn wirtualnych można również określić opóźnienie oraz dozwolony i odrzucony ruch sieciowy między maszyną wirtualną a punktem końcowym przy użyciu funkcji [rozwiązywania problemów z połączeniem](network-watcher-connectivity-cli.md) obserwatora sieci. Komunikację między maszyną wirtualną a punktem końcowym, taką jak adres IP lub adres URL, można monitorować w czasie, korzystając z funkcji monitora połączenia obserwatora sieci. Aby dowiedzieć się, jak to zrobić, zobacz [Monitorowanie połączenia sieciowego](connection-monitor.md).

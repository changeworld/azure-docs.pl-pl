---
title: Diagnozowanie maszyny wirtualnej problemu z routingiem sieciowym — interfejs wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule dowiesz się, jak diagnozować maszyny wirtualnej problemu z routingiem sieciowym za pomocą następnego przeskoku możliwości usługi Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: KumudD
manager: twooley
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
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 968b7dd703ba40f46a068deb1d8b7d2b32e0de2b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64688213"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-cli"></a>Diagnozowanie maszyny wirtualnej problemu z routingiem sieciowym — interfejs wiersza polecenia platformy Azure

W tym artykule Wdróż maszynę wirtualną (VM), a następnie sprawdź komunikaty do adresu IP i adres URL. Określisz przyczynę niepowodzenia komunikacji oraz sposób rozwiązania problemu.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i korzystać z interfejsu wiersza polecenia lokalnie, ten artykuł wymaga, czy korzystasz z wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja została zainstalowana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Po zweryfikowaniu wersji interfejsu wiersza polecenia uruchom polecenie `az login`, aby utworzyć połączenie z platformą Azure. Polecenia interfejsu wiersza polecenia, w tym artykule są formatowane do uruchamiania w powłoce Bash.

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Przed utworzeniem maszyny wirtualnej musisz utworzyć grupę zasobów, która będzie zawierała maszynę wirtualną. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm#az-vm-create). Jeśli klucze SSH nie istnieją jeszcze w domyślnej lokalizacji kluczy, to polecenie je utworzy. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`. W poniższym przykładzie utworzono maszynę wirtualną o nazwie *myVM*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Wykonywanie pozostałych kroków możesz zacząć dopiero wtedy, gdy maszyna wirtualna zostanie utworzona, a interfejs wiersza polecenia zwróci dane wyjściowe.

## <a name="test-network-communication"></a>Testowanie komunikacji sieciowej

Aby przetestować komunikacji sieciowej przy użyciu usługi Network Watcher, należy najpierw włączyć usługi network watcher w regionie, w którym znajduje się w maszynie Wirtualnej, która ma zostać przetestowana i następnie za pomocą usługi Network Watcher następnego przeskoku możliwości testowania komunikacji.

### <a name="enable-network-watcher"></a>Włączanie usługi Network Watcher

Jeśli masz już usługę network watcher włączone w regionie wschodnie stany USA, przejdź do [użycia miejsca docelowego następnego skoku](#use-next-hop). Użyj [Konfigurowanie usługi network watcher az](/cli/azure/network/watcher#az-network-watcher-configure) polecenie, aby utworzyć usługę network watcher w regionie wschodnie stany USA:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-next-hop"></a>Korzystanie z funkcji Następny przeskok

Na platformie Azure są automatycznie tworzone trasy do domyślnych miejsc docelowych. Możesz tworzyć trasy niestandardowe zastępujące domyślne trasy. Czasami użycie tras niestandardowych może spowodować niepowodzenie komunikacji. Aby przetestować routing z maszyny Wirtualnej, użyj [az network watcher show-next-hop](/cli/azure/network/watcher?view=azure-cli-latest#az-network-watcher-show-next-hop) do określenia następnego przeskoku routingu, gdy ruch jest przeznaczony dla określonego adresu.

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

Po kilku sekundach, dane wyjściowe informujący o tym, **Typ następnego przeskoku** jest **Internet**oraz że **routeTableId** jest **trasa systemowa**. Ten wynik poinformuje Cię o tym, że jest prawidłową trasę do lokalizacji docelowej.

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

Dane wyjściowe zwracane informujący o tym, **Brak** jest **Typ następnego przeskoku**oraz że **routeTableId** jest również **trasa systemowa**. Ten wynik oznacza, że istnieje prawidłowa trasa systemowa do miejsca docelowego, ale nie ma następnego przeskoku umożliwiającego kierowanie ruchu do miejsca docelowego.

## <a name="view-details-of-a-route"></a>Wyświetlanie szczegółów trasy

Aby analizować dalsze routingu, zapoznaj się z obowiązujące trasy dla interfejsu sieciowego z [az network nic show obowiązywać route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table) polecenia:

```azurecli-interactive
az network nic show-effective-route-table \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

Poniższy tekst znajduje się w dane wyjściowe:

```azurecli
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

Kiedy użyć `az network watcher show-next-hop` polecenie, aby przetestować komunikacja wychodząca 13.107.21.200 w [użycia miejsca docelowego następnego skoku](#use-next-hop), trasa o **addressPrefix** 0.0.0.0/0** został użyty do kierowania ruchu do adresu, ponieważ nie innej trasy w danych wyjściowych zawiera adres. Domyślnie wszystkie adresy, które nie zostały określone w prefiksie adresu innej trasy, są kierowane do Internetu.

Kiedy użyć `az network watcher show-next-hop` polecenia do przetestowania komunikacja wychodząca 172.31.0.100 jednak, wynik informować, że nie ma żadnych typu następnego przeskoku. W dane wyjściowe zostanie również wyświetlony następujący tekst:

```azurecli
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

Jak widać w danych wyjściowych `az network watcher nic show-effective-route-table` polecenia, chociaż istnieje trasa domyślna, prefiks 172.16.0.0/12, która zawiera 172.31.0.100 adres **Typ następnego przeskoku** jest **Brak**. Platforma Azure tworzy domyślną trasę dla zakresu adresów 172.16.0.0/12, ale nie określa typu następnego przeskoku, jeśli nie jest to wymagane. Jeśli na przykład dodano 172.16.0.0/12 zakresu adresów przestrzeni adresowej sieci wirtualnej, zmieni się Azure **Typ następnego przeskoku** do **sieć wirtualna** dla danej trasy. Następnie pokazywałaby sprawdzenie **sieć wirtualna** jako **Typ następnego przeskoku**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule utworzono Maszynę wirtualną i zdiagnozować, routing w sieci z maszyny Wirtualnej. Uzyskano informacje o tworzeniu tras domyślnych na platformie Azure i przetestowano routing do dwóch różnych miejsc docelowych. Uzyskaj więcej informacji na temat [routingu na platformie Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) i dowiedz się, jak [tworzyć trasy niestandardowe](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

Dla połączeń wychodzących maszyny Wirtualnej, można również określić opóźnienie i dozwolonych i niedozwolonych ruch sieciowy między maszyną Wirtualną i punkt końcowy korzystający z usługi Network Watcher [Rozwiązywanie problemów z połączeniami](network-watcher-connectivity-cli.md) możliwości. Można monitorować komunikację między maszyny Wirtualnej i punktu końcowego, takie jak adres IP lub adres URL, wraz z upływem czasu przy użyciu funkcji monitor połączeń usługi Network Watcher. Aby dowiedzieć się więcej, zobacz temat [monitorowanie połączenia sieciowego](connection-monitor.md).
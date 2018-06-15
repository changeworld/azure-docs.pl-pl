---
title: Diagnozowanie maszyny wirtualnej routingu problem z siecią - wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule Dowiedz się jak zdiagnozować problem routingu sieci maszyny wirtualnej przy użyciu funkcji w następnym przeskoku obserwatora sieci platformy Azure.
services: network-watcher
documentationcenter: network-watcher
author: jimdial
manager: jeconnoc
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
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: fcb7ec2e40b5c0e8794d2f4d70395dcbecca019c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32182500"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-cli"></a>Diagnozowanie maszyny wirtualnej routingu problem z siecią - wiersza polecenia platformy Azure

W tym artykule należy wdrożyć maszynę wirtualną (VM), a następnie sprawdź komunikacji z adresu IP i adres URL. Należy określić przyczynę awarii łączności i jak można go usunąć.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli do zainstalowania i używania interfejsu wiersza polecenia lokalnie, w tym artykule, wymaga używasz interfejsu wiersza polecenia Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja została zainstalowana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli). Po zweryfikowaniu wersji interfejsu wiersza polecenia, uruchom `az login` można utworzyć połączenia z platformą Azure. Polecenia interfejsu wiersza polecenia, w tym artykule są sformatowane do uruchamiania w powłoki Bash.

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Przed utworzeniem maszyny Wirtualnej, należy utworzyć grupę zasobów, aby zawierała maszyny Wirtualnej. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm#az_vm_create). Jeśli klucze SSH nie istnieją jeszcze w domyślnej lokalizacji kluczy, to polecenie je utworzy. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`. Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVm*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Nie Kontynuuj pozostałe kroki dopiero po utworzeniu maszyny Wirtualnej i interfejsu wiersza polecenia zwraca dane wyjściowe.

## <a name="test-network-communication"></a>Test łączności sieciowej

Aby przetestować komunikacji sieciowej z obserwatora sieciowego, należy najpierw włączyć obserwatora sieciowego, w regionie, w którym znajduje się w maszynie Wirtualnej, która ma zostać przetestowana, a następnie użyć obserwatora sieciowego następnego przeskoku możliwości do testowania łączności.

### <a name="enable-network-watcher"></a>Włącz obserwatora sieciowego

Jeśli masz już obserwatora sieciowego, włączona w regionie wschodnie stany USA, przejdź do [Użyj następnego przeskoku](#use-next-hop). Użyj [skonfigurować obserwatora sieciowego az](/cli/azure/network/watcher#az-network-watcher-configure) polecenie, aby utworzyć obserwatora sieciowego w regionie wschodnie stany USA:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-next-hop"></a>Użyj następnego skoku

Azure automatycznie tworzy trasy do domyślnej lokalizacji docelowych. Można utworzyć trasy niestandardowe, które zastępują trasy domyślnej. Czasami trasy niestandardowe może spowodować, że komunikacja się nie powieść. Aby przetestować routingu z maszyny Wirtualnej, użyj [az sieci obserwatora Pokaż następnego przeskoku](/cli/azure/network/watcher?view=azure-cli-latest#az-network-watcher-show-next-hop) ustalenie następnego przeskoku routingu ruchu jest przeznaczony dla określonego adresu.

Test komunikacji wychodzącej z maszyny Wirtualnej do jednego z adresów IP dla www.bing.com:

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 13.107.21.200 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

Po kilku sekundach, dane wyjściowe informuje o który **Typ następnego przeskoku** jest **Internet**oraz że **routeTableId** jest **trasa systemowa**. Wynik tego informuje o tym, czy jest prawidłową trasę do miejsca docelowego.

Przetestuj komunikacja wychodząca z maszyny Wirtualnej 172.31.0.100:

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 172.31.0.100 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

Dane wyjściowe zwrócone informuje o który **Brak** jest **Typ następnego przeskoku**oraz że **routeTableId** jest również **trasa systemowa**. Wynik tego informuje o tym, gdy istnieje trasa prawidłowego systemu docelowego, brak nie następnego przeskoku do kierowania ruchem do miejsca docelowego.

## <a name="view-details-of-a-route"></a>Wyświetl szczegóły trasy

Do analizy, dalsze routingu, przejrzyj skuteczne trasy dla interfejsu sieciowego z [az sieci karty sieciowej Pokaż obowiązującej--tabeli tras](/cli/azure/network/nic#az-network-nic-show-effective-route-table) polecenia:

```azurecli-interactive
az network nic show-effective-route-table \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

Następujący tekst jest uwzględniany w dane wyjściowe:

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

Kiedy używać `az network watcher show-next-hop` polecenia komunikacja wychodząca 13.107.21.200 w [Użyj następnego przeskoku](#use-next-hop), trasa o **prefiks adresu** 0.0.0.0/0** była używana do kierowania ruchem na adres, ponieważ nie innych tras w danych wyjściowych zawiera adres. Domyślnie wszystkie adresy, które nie zostały określone w prefiksu adresu innej trasy są kierowane do Internetu.

Kiedy używać `az network watcher show-next-hop` polecenia do przetestowania komunikacja wychodząca 172.31.0.100 jednak, wynik poinformowana, że nie ma żadnych Typ następnego przeskoku. Dane wyjściowe można również zawiera następujący tekst:

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

Jak widać w danych wyjściowych z `az network watcher nic show-effective-route-table` polecenia, ale istnieje trasa domyślna do prefiksu 172.16.0.0/12, który zawiera 172.31.0.100 adres **Typ następnego przeskoku** jest **Brak**. Azure tworzy trasę domyślną elementom 172.16.0.0/12, ale nie określono typu następnego przeskoku, dopóki ma powodu do. Jeśli na przykład zakres adresów 172.16.0.0/12 dodano do przestrzeni adresowej sieci wirtualnej, Azure zmiany **Typ następnego przeskoku** do **sieci wirtualnej** dla danej trasy. Sprawdzenie czy następnie następuje wyświetlenie **sieci wirtualnej** jako **Typ następnego przeskoku**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule utworzyć Maszynę wirtualną i zdiagnozować, routingu sieciowego z maszyny Wirtualnej. Wiesz, że Azure tworzy kilka tras domyślnych i przetestowane routingu do dwóch różnych miejsc docelowych. Dowiedz się więcej o [routing na platformie Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) oraz sposób [tworzenia niestandardowych tras](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

Dla połączeń wychodzących maszyny Wirtualnej, można również określić opóźnienie i dozwolonych i zabronionych ruch sieciowy między maszyny Wirtualnej i punktu końcowego za pomocą Monitora sieci [Rozwiązywanie problemów z połączenia](network-watcher-connectivity-cli.md) możliwości. Można monitorować komunikację między maszyny Wirtualnej i punktu końcowego, takie jak adres IP lub adres URL, w czasie przy użyciu możliwości monitor połączenia obserwator sieci. Aby dowiedzieć się więcej, zobacz temat [monitorować połączenia sieciowego](connection-monitor.md).
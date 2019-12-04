---
title: Korzystanie z interfejsu wiersza polecenia do wdrażania maszyn wirtualnych usługi Azure Spot (wersja zapoznawcza)
description: Dowiedz się, jak za pomocą interfejsu wiersza polecenia wdrożyć maszyny wirtualne Azure spot w celu oszczędności kosztów.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2019
ms.author: cynthn
ms.openlocfilehash: 0635be14937a3688792f65208dcb9d482b9e6d44
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74781997"
---
# <a name="preview-deploy-spot-vms-using-the-azure-cli"></a>Wersja zapoznawcza: wdrażanie maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure

Korzystanie z [maszyn wirtualnych platformy Azure](spot-vms.md) umożliwia korzystanie z nieużywanej pojemności przy znaczącym obciążeniu kosztów. W dowolnym momencie, gdy platforma Azure wymaga przywrócenia pojemności, infrastruktura platformy Azure wyłączy maszyny wirtualne. W związku z tym maszyny wirtualne są doskonałe dla obciążeń, które mogą obsłużyć przerwy, takie jak zadania przetwarzania wsadowego, środowiska deweloperskie/testowe, duże obciążenia obliczeniowe i inne.

Ceny maszyn wirtualnych na miejscu są zmienne, na podstawie regionu i jednostki SKU. Aby uzyskać więcej informacji, zobacz cennik maszyn wirtualnych dla [systemów](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) i Windows. 

Dla maszyny wirtualnej można ustawić maksymalną cenę, która ma być płacona za godzinę. Maksymalna cena maszyny wirtualnej na miejscu może być ustawiona w dolarach amerykańskich (USD) przy użyciu maksymalnie 5 miejsc dziesiętnych. Na przykład wartość `0.98765`będzie cena maksymalna $0,98765 USD za godzinę. Jeśli ustawisz maksymalną wartość `-1`, maszyna wirtualna nie zostanie wykluczona na podstawie ceny. Cena maszyny wirtualnej to aktualna cena za ilość miejsca lub cena standardowej maszyny wirtualnej, która kiedykolwiek jest mniejsza, o ile jest dostępna pojemność i przydział. Aby uzyskać więcej informacji na temat ustawiania ceny maksymalnej, zobacz [punkt maszyny wirtualne — Cennik](spot-vms.md#pricing).

Proces tworzenia maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure jest taki sam jak szczegółowy w [artykule szybki start](/azure/virtual-machines/linux/quick-create-cli). Po prostu Dodaj parametr "--Priority" i podaj maksymalną cenę lub `-1`.

> [!IMPORTANT]
> Wystąpienia punktowe są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> W przypadku wczesnej części publicznej wersji zapoznawczej wystąpienia usługi będą miały ustaloną cenę, więc nie będzie żadnych wykluczeń opartych na cenach.


## <a name="install-azure-cli"></a>Instalowanie interfejsu wiersza polecenia platformy Azure

Aby tworzyć maszyny wirtualne, musisz uruchomić interfejs wiersza polecenia platformy Azure w wersji 2.0.74 lub nowszej. Aby odnaleźć wersję, uruchom polecenie **az --version**. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

Zaloguj się do platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index#az-login).

```azurecli
az login
```

## <a name="create-a-spot-vm"></a>Tworzenie doaktywnej maszyny wirtualnej

Ten przykład pokazuje, jak wdrożyć maszynę wirtualną z systemem Linux, która nie zostanie wykluczona na podstawie ceny. 

```azurecli
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1
```

Po utworzeniu maszyny wirtualnej można wykonać zapytanie, aby zobaczyć maksymalną cenę rozliczeniową dla wszystkich maszyn wirtualnych w grupie zasobów.

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

**Następne kroki**

Możesz również utworzyć maszynę wirtualną na miejscu przy użyciu [Azure PowerShell](../windows/spot-powershell.md) lub [szablonu](spot-template.md).

Jeśli wystąpi błąd, zobacz [kody błędów](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

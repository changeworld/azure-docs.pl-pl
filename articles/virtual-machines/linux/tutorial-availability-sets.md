---
title: Samouczek dotyczący zestawów dostępności dla maszyn wirtualnych z systemem Linux na platformie Azure | Microsoft Docs
description: Uzyskaj informacje na temat zestawów dostępności dla maszyn wirtualnych z systemem Linux na platformie Azure.
documentationcenter: ''
services: virtual-machines-linux
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: tutorial
ms.date: 10/05/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: d317ec8136ad7a36381239593c3a53c40f897845
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-use-availability-sets"></a>Jak używać zestawów dostępności


W tym samouczku dowiesz się, jak zwiększyć dostępność i niezawodność rozwiązań korzystających z maszyn wirtualnych na platformie Azure przy użyciu funkcji zestawów dostępności. Zestawy dostępności zapewniają rozproszenie maszyn wirtualnych wdrożonych na platformie Azure pomiędzy wieloma izolowanymi klastrami sprzętowymi. Dzięki temu ewentualne awarie sprzętowe lub błędy oprogramowania na platformie Azure będą miały wpływ tylko na część maszyn wirtualnych, a całe rozwiązanie nadal będzie dostępne i funkcjonalne.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zestawu dostępności
> * Tworzenie maszyny wirtualnej w zestawie dostępności
> * Sprawdzanie dostępnych rozmiarów maszyn wirtualnych


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="availability-set-overview"></a>Zestaw dostępności — omówienie

Zestaw dostępności to dostępna na platformie Azure funkcja grupowania logicznego, zapewniająca izolację zawartych w tej grupie maszyn wirtualnych wdrożonych w centrum danych platformy Azure. Maszyny wirtualne platformy Azure umieszczone w zestawie dostępności korzystają z wielu serwerów fizycznych, regałów obliczeniowych, jednostek magazynowych i przełączników sieciowych. Ewentualne awarie sprzętowe lub błędy oprogramowania na platformie Azure będą miały wpływ tylko na część maszyn wirtualnych, a cała aplikacja nadal będzie działała i pozostanie dostępna dla klientów. Zestawy dostępności stanowią niezbędną funkcję podczas tworzenia niezawodnych rozwiązań w chmurze.

Rozważmy typowe rozwiązanie z użyciem maszyn wirtualnych, obejmujące cztery serwery internetowe frontonu oraz dwie maszyny wirtualne zaplecza, na których jest hostowana baza danych. Przed wdrożeniem maszyn wirtualnych na platformie Azure należałoby w takim przypadku zdefiniować dwa zestawy dostępności: jeden dla warstwy „Internet”, a drugi dla warstwy „baza danych”. Podczas tworzenia nowej maszyny wirtualnej można określić zestaw dostępności jako parametr polecenia az vm create, a platforma Azure automatycznie zapewni izolację maszyn wirtualnych tworzonych w ramach tego zestawu dostępności na wielu fizycznych zasobach sprzętowych. W przypadku problemu ze sprzętem fizycznym, na którym jest uruchomiona jedna z maszyn wirtualnych serwera internetowego lub serwera bazy danych, masz pewność, że pozostałe wystąpienia maszyn wirtualnych serwera internetowego i bazy danych będą nadal działać, ponieważ korzystają z innego sprzętu.

Zestawy dostępności umożliwiają wdrażanie niezawodnych rozwiązań z użyciem maszyn wirtualnych na platformie Azure.


## <a name="create-an-availability-set"></a>Tworzenie zestawu dostępności

Aby utworzyć zestaw dostępności, użyj polecenia [az vm availability-set create](/cli/azure/vm/availability-set#az_vm_availability_set_create). W tym przykładzie ustawimy zarówno liczbę domen aktualizacji, jak i domen błędów na *2* dla zestawu dostępności o nazwie *myAvailabilitySet* w grupie zasobów *myResourceGroupAvailability*.

Utwórz grupę zasobów.

```azurecli-interactive 
az group create --name myResourceGroupAvailability --location eastus
```


```azurecli-interactive 
az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

Zestawy dostępności pozwalają izolować zasoby w domenach błędów i aktualizować domeny. **Domeny błędów** reprezentują izolowaną kolekcję zasobów serwer + sieć + magazyn. W poprzednim przykładzie wskazaliśmy, że chcemy, aby nasz zestaw dostępności był rozproszony w co najmniej dwóch domenach błędów, gdy są wdrażane nasze maszyny wirtualne. Wskazaliśmy również, że chcemy, aby nasz zestaw dostępności był rozproszony w dwóch **domenach aktualizacji**.  Dwie domeny aktualizacji zapewniają, że w przypadku, gdy platforma Azure przeprowadza aktualizacje oprogramowania, zasoby naszych maszyn wirtualnych są izolowane, zapobiegając jednoczesnej aktualizacji całego oprogramowania działającego poniżej naszej maszyny wirtualnej.


## <a name="create-vms-inside-an-availability-set"></a>Tworzenie maszyn wirtualnych w zestawie dostępności

Aby zapewnić właściwe rozproszenie maszyn wirtualnych na sprzęcie, należy utworzyć je w ramach zestawu dostępności. Nie można dodać istniejącej, wcześniej utworzonej maszyny wirtualnej do zestawu dostępności. 

Podczas tworzenia maszyny wirtualnej przy użyciu polecenia [az vm create](/cli/azure/vm#az_vm_create) określasz zestaw dostępności za pomocą parametru `--availability-set`, aby określić nazwę zestawu dostępności.

```azurecli-interactive 
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --size Standard_DS1_v2  \
     --image Canonical:UbuntuServer:14.04.4-LTS:latest \
     --admin-username azureuser \
     --generate-ssh-keys \
     --no-wait
done 
```

Mamy teraz dwie maszyny wirtualne w naszym nowo utworzonym zestawie dostępności. Ponieważ są one w tym samym zestawie dostępności, platforma Azure zapewnia, że maszyny wirtualne i ich zasoby (w tym dyski z danymi) są rozmieszczone na izolowanym sprzęcie fizycznym. Takie rozmieszczenie zapewnia znacznie wyższą dostępność naszego ogólnego rozwiązania maszyny wirtualnej.

Możesz wyświetlić zestaw dostępności w portalu, przechodząc do pozycji Grupy zasobów > myResourceGroupAvailability > myAvailabilitySet, aby zobaczyć, w jaki sposób maszyny wirtualne są rozmieszczone w dwóch domenach aktualizacji i błędów.

![Zestaw dostępności w portalu](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Sprawdzanie dostępnych rozmiarów maszyn wirtualnych 

Możesz później dodać więcej maszyn wirtualnych do zestawu dostępności, ale potrzebujesz do tego informacji o rozmiarach maszyn wirtualnych udostępnianych przez sprzęt.  Użyj polecenia [az vm availability-set list-sizes](/cli/azure/availability-set#az_availability_set_list_sizes), aby wyświetlić listę wszystkich rozmiarów dostępnych w klastrze sprzętowym zestawu dostępności.

```azurecli-interactive 
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table  
```

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zestawu dostępności
> * Tworzenie maszyny wirtualnej w zestawie dostępności
> * Sprawdzanie dostępnych rozmiarów maszyn wirtualnych

Przejdź do następnego samouczka, aby poznać zestawy skalowania maszyn wirtualnych.

> [!div class="nextstepaction"]
> [Tworzenie zestawu skalowania maszyn wirtualnych](tutorial-create-vmss.md)


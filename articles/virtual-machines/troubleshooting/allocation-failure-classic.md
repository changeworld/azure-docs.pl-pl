---
title: Rozwiązywanie problemów z błędami alokacji maszyn wirtualnych platformy Azure w klasycznym modelu wdrażania| Dokumenty firmy Microsoft
description: Rozwiązywanie problemów z błędami alokacji podczas tworzenia, ponownego uruchamiania lub ponownego rozmiaru klasycznej maszyny Wirtualnej na platformie Azure
services: azure-service-management
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: bb939e23-77fc-4948-96f7-5037761c30e8
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 20e64e5225987a8045e406a0e8fcae098c580c61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77913382"
---
# <a name="troubleshooting-steps-specific-to-allocation-failure-scenarios-in-the-classic-deployment-model"></a>Procedury rozwiązywania problemów dotyczących scenariuszy awarii alokacji w klasycznym modelu wdrażania

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Poniżej przedstawiono typowe scenariusze alokacji, które powodują przypięte żądanie alokacji. W dalszej części tego artykułu przyjrzymy się każdemu scenariuszowi.

- Zmiana rozmiaru maszyny wirtualnej lub dodawania maszyn wirtualnych lub wystąpień roli do istniejącej usługi w chmurze
- Ponowne uruchomienie częściowo zatrzymanych maszyn wirtualnych (z cofniętą alokacją)
- Ponowne uruchomienie w pełni zatrzymanych maszyn wirtualnych (z cofniętą alokacją)
- Wdrożenia przejściowe i produkcyjne (tylko platforma jako usługa)
- Grupa koligacji (maszyna wirtualna lub bliskość usługi)
- Sieć wirtualna oparta na koligacji opartej na grupach

Po wyświetleniu błędu alokacji sprawdź, czy którykolwiek z wymienionych scenariuszy dotyczy błędu. Użyj błędu alokacji, który jest zwracany przez platformę Azure, aby zidentyfikować odpowiedni scenariusz. Jeśli żądanie jest przypięte, usuń niektóre ograniczenia przypinania, aby otworzyć żądanie do większej liczby klastrów, zwiększając w ten sposób szansę na sukces alokacji.
Ogólnie rzecz biorąc, jeśli błąd nie stwierdza, że "żądany rozmiar maszyny Wirtualnej nie jest obsługiwany", zawsze można ponowić próbę w późniejszym czasie. Jest tak, ponieważ w klastrze może zostać zwolniona wystarczająca ilość zasobów, aby uwzględnić żądanie. Jeśli problem polega na tym, że żądany rozmiar maszyny Wirtualnej nie jest obsługiwany, spróbuj użyć innego rozmiaru maszyny Wirtualnej. W przeciwnym razie jedyną opcją jest usunięcie ograniczenia przypinania.

Dwa typowe scenariusze awarii są związane z grupami koligacji. W przeszłości grupa koligacji była używana do zapewnienia bliskiej odległości od maszyn wirtualnych i wystąpień usługi lub została użyta do umożliwienia tworzenia sieci wirtualnej. Wraz z wprowadzeniem regionalnych sieci wirtualnych grupy koligacji nie są już wymagane do tworzenia sieci wirtualnej. Wraz ze zmniejszeniem opóźnienia sieci w infrastrukturze platformy Azure zmieniono zalecenie użycia grup koligacji dla maszyn wirtualnych lub bliskości usługi.

Na poniższym diagramie przedstawiono taksonomię (przypiętych) scenariuszy alokacji. 

![Przypięta taksonomia alokacji](./media/virtual-machines-common-allocation-failure/Allocation3.png)

## <a name="resize-a-vm-or-add-vms-or-role-instances-to-an-existing-cloud-service"></a>Zmiana rozmiaru maszyny wirtualnej lub dodawania maszyn wirtualnych lub wystąpień roli do istniejącej usługi w chmurze
**Błąd**

Upgrade_VMSizeNotSupported lub GeneralError

**Przyczyna przypinania klastra**

Żądanie, aby zmienić rozmiar maszyny Wirtualnej lub dodać maszynę wirtualną lub wystąpienie roli do istniejącej usługi w chmurze, należy podjąć próbę w oryginalnym klastrze obsługującym istniejącą usługę w chmurze. Utworzenie nowej usługi w chmurze umożliwia platformie Platformy Azure znalezienie innego klastra, który ma wolne zasoby lub obsługuje żądany rozmiar maszyny Wirtualnej.

**Obejście**

Jeśli błąd jest Upgrade_VMSizeNotSupported*, spróbuj użyć innego rozmiaru maszyny Wirtualnej. Jeśli przy użyciu innego rozmiaru maszyny Wirtualnej nie jest opcją, ale jeśli jest dopuszczalne użycie innego wirtualnego adresu IP (VIP), należy utworzyć nową usługę w chmurze do obsługi nowej maszyny Wirtualnej i dodać nową usługę w chmurze do regionalnej sieci wirtualnej, w której są uruchomione istniejące maszyny wirtualne. Jeśli istniejąca usługa w chmurze nie korzysta z regionalnej sieci wirtualnej, nadal można utworzyć nową sieć wirtualną dla nowej usługi w chmurze, a następnie podłączyć [istniejącą sieć wirtualną do nowej sieci wirtualnej](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Zobacz więcej o [regionalnych sieciach wirtualnych](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Jeśli błąd jest GeneralError *, jest prawdopodobne, że typ zasobu (na przykład rozmiar określonej maszyny Wirtualnej) jest obsługiwany przez klaster, ale klaster nie ma wolnych zasobów w tej chwili. Podobnie jak w powyższym scenariuszu, należy dodać żądany zasób obliczeniowy poprzez utworzenie nowej usługi w chmurze (należy pamiętać, że nowa usługa w chmurze musi używać innego adresu VIP) i użyć regionalnej sieci wirtualnej do łączenia usług w chmurze.

## <a name="restart-partially-stopped-deallocated-vms"></a>Ponowne uruchomienie częściowo zatrzymanych maszyn wirtualnych (z cofniętą alokacją)
**Błąd**

GeneralError*

**Przyczyna przypinania klastra**

Częściowa alokacja oznacza, że zatrzymano (cofnięto przydział) jeden lub więcej, ale nie wszystkie maszyny wirtualne w usłudze w chmurze. Po zatrzymaniu (cofnięto alokację) maszyny Wirtualnej skojarzone zasoby są zwalniane. Ponowne uruchomienie, które zatrzymało (cofnięto przydział) maszyny Wirtualnej jest zatem nowym żądaniem alokacji. Ponowne uruchamianie maszyn wirtualnych w częściowo cofniętej usłudze w chmurze jest równoznaczne z dodawaniem maszyn wirtualnych do istniejącej usługi w chmurze. Żądanie alokacji musi być podjęta w oryginalnym klastrze, który obsługuje istniejącą usługę w chmurze. Utworzenie innej usługi w chmurze umożliwia platformie Platformy Azure znalezienie innego klastra, który ma wolny zasób lub obsługuje żądany rozmiar maszyny Wirtualnej.

**Obejście**

Jeśli dopuszczalne jest użycie innego adresu VIP, usuń zatrzymane (cofnięte) maszyny wirtualne (ale zachowaj skojarzone dyski) i dodaj maszyny wirtualne z powrotem za pośrednictwem innej usługi w chmurze. Użyj regionalnej sieci wirtualnej, aby połączyć usługi w chmurze:

* Jeśli istniejąca usługa w chmurze korzysta z regionalnej sieci wirtualnej, po prostu dodaj nową usługę w chmurze do tej samej sieci wirtualnej.
* Jeśli istniejąca usługa w chmurze nie korzysta z regionalnej sieci wirtualnej, utwórz nową sieć wirtualną dla nowej usługi w chmurze, a następnie [podłącz istniejącą sieć wirtualną do nowej sieci wirtualnej.](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/) Zobacz więcej o [regionalnych sieciach wirtualnych](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

## <a name="restart-fully-stopped-deallocated-vms"></a>Ponowne uruchomienie w pełni zatrzymanych maszyn wirtualnych (z cofniętą alokacją)
**Błąd**

GeneralError*

**Przyczyna przypinania klastra**

Pełna alokacja oznacza, że zatrzymano (cofnięto przydział) wszystkie maszyny wirtualne z usługi w chmurze. Żądania alokacji do ponownego uruchomienia tych maszyn wirtualnych muszą być podejmowane w oryginalnym klastrze, który obsługuje usługę w chmurze. Utworzenie nowej usługi w chmurze umożliwia platformie Platformy Azure znalezienie innego klastra, który ma wolne zasoby lub obsługuje żądany rozmiar maszyny Wirtualnej.

**Obejście**

Jeśli dopuszczalne jest użycie innego adresu VIP, usuń oryginalne zatrzymane (cofnięto przydział) maszyny wirtualne (ale zachowaj skojarzone dyski) i usuń odpowiednią usługę w chmurze (skojarzone zasoby obliczeniowe zostały już zwolnione po zatrzymaniu (cofnięto przydział) maszyn wirtualnych). Utwórz nową usługę w chmurze, aby dodać maszyny wirtualne z powrotem.

## <a name="stagingproduction-deployments-platform-as-a-service-only"></a>Wdrożenia przejściowe/produkcyjne (tylko platforma jako usługa)
**Błąd**

New_General* lub New_VMSizeNotSupported*

**Przyczyna przypinania klastra**

Wdrożenie przejściowe i wdrożenie produkcyjne usługi w chmurze są hostowane w tym samym klastrze. Po dodaniu drugiego wdrożenia, odpowiednie żądanie alokacji zostanie podjęta w tym samym klastrze, który obsługuje pierwsze wdrożenie.

**Obejście**

Usuń pierwsze wdrożenie i oryginalną usługę w chmurze i ponownie wdrożyć usługę w chmurze. Ta akcja może wylądować pierwsze wdrożenie w klastrze, który ma wystarczająco dużo wolnych zasobów, aby dopasować zarówno wdrożeń lub w klastrze, który obsługuje rozmiary maszyn wirtualnych, które zostały żądane.

## <a name="affinity-group-vmservice-proximity"></a>Grupa koligacji (VM/bliskość usługi)
**Błąd**

New_General* lub New_VMSizeNotSupported*

**Przyczyna przypinania klastra**

Każdy zasób obliczeniowy przypisany do grupy koligacji jest powiązany z jednym klastrem. Nowe żądania zasobów obliczeniowych w tej grupie koligacji są podejmowane w tym samym klastrze, w którym znajdują się istniejące zasoby. Dotyczy to tego, czy nowe zasoby są tworzone za pośrednictwem nowej usługi w chmurze, czy za pośrednictwem istniejącej usługi w chmurze.

**Obejście**

Jeśli grupa koligacji nie jest konieczna, nie należy używać grupy koligacji ani grupować zasobów obliczeniowych w wiele grup koligacji.

## <a name="affinity-group-based-virtual-network"></a>Sieć wirtualna oparta na grupie koligacji
**Błąd**

New_General* lub New_VMSizeNotSupported*

**Przyczyna przypinania klastra**

Przed wprowadzeniem regionalnych sieci wirtualnych konieczne było skojarzenie sieci wirtualnej z grupą koligacji. W rezultacie zasoby obliczeniowe umieszczone w grupie koligacji są powiązane tymi samymi ograniczeniami, jak opisano w sekcji "Scenariusz alokacji: grupa koligacji (vm/lokalizacja usługi)" powyżej. Zasoby obliczeniowe są powiązane z jednym klastrem.

**Obejście**

Jeśli nie potrzebujesz grupy koligacji, utwórz nową regionalną sieć wirtualną dla nowych dodania zasobów, a następnie [podłącz istniejącą sieć wirtualną do nowej sieci wirtualnej](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Zobacz więcej o [regionalnych sieciach wirtualnych](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Alternatywnie można [przeprowadzić migrację sieci wirtualnej opartej na grupie koligacji do regionalnej sieci wirtualnej,](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)a następnie ponownie dodać żądane zasoby.



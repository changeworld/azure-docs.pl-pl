---
title: Rozwiązywanie problemów z błędami alokacji maszyny Wirtualnej platformy Azure w klasycznym modelu wdrażania | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z błędami alokacji występującymi podczas tworzenia lub ponownego uruchomienia lub zmienić rozmiar klasycznej maszyny Wirtualnej na platformie Azure
services: azure-service-management
documentationcenter: ''
author: genlin
manager: willchen
editor: ''
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: bb939e23-77fc-4948-96f7-5037761c30e8
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 7cd7897e3a0b940bbc636b2fbc3dbbc13b7cf540
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60505571"
---
# <a name="troubleshooting-steps-specific-to-allocation-failure-scenarios-in-the-classic-deployment-model"></a>Rozwiązywanie problemów w określonych scenariuszy awarii alokacji w klasycznym modelu wdrażania

Poniżej przedstawiono typowe scenariusze alokacji, które powodują wysłanie żądania alokacji do można przypiąć. Będziesz przejdziemy do każdego scenariusza w dalszej części tego artykułu.

- Zmień rozmiar maszyny Wirtualnej lub dodawanie maszyn wirtualnych lub wystąpień roli do istniejącej usługi w chmurze
- Ponowne uruchomienie częściowo zatrzymanych maszyn wirtualnych (z cofniętą alokacją)
- Ponowne uruchomienie w pełni zatrzymanych maszyn wirtualnych (z cofniętą alokacją)
- Wdrożenia środowisk przejściowych i produkcyjnych (platforma jako usługa tylko)
- Grupa koligacji (maszyna wirtualna lub usługa odległości między elementami)
- Koligacja — na podstawie grupy sieci wirtualnej

Gdy zostanie wyświetlony błąd alokacji, sprawdź, czy dowolny z wymienionych scenariuszy dotyczy błędu. Użyj błąd alokacji, który jest zwracany przez platformę Azure, aby zidentyfikować odpowiedni scenariusz. Jeśli Twoje żądanie jest przypięty, usuń niektóre ograniczenia przypinania, aby otworzyć żądanie do klastrów więcej, co zwiększa prawdopodobieństwo powodzenia alokacji.
Ogólnie rzecz biorąc Jeśli błąd stanu, że "żądany rozmiar maszyny Wirtualnej nie jest obsługiwany", możesz zawsze ponowić próbę w późniejszym czasie. Jest to spowodowane za mało zasobów zostały zwolnione w klastrze, aby obsłużyć Twojego żądania. Jeśli problem polega na tym, że żądany rozmiar maszyny Wirtualnej nie jest obsługiwane, należy spróbować innego rozmiaru maszyny Wirtualnej. W przeciwnym razie jedyną opcją jest usunięcie przypinania ograniczenia.

Dwa typowych scenariuszy awarii odnoszą się do grupy koligacji. W przeszłości grupy koligacji został użyty do zapewnienia bliskim sąsiedztwie do maszyn wirtualnych i wystąpień usługi lub został on użyty w celu umożliwienia utworzenia sieci wirtualnej. Wraz z wprowadzeniem regionalnych sieci wirtualnych grup koligacji nie są już wymagane do utworzenia sieci wirtualnej. Zmniejszeniu opóźnienia sieci w infrastrukturze platformy Azure zmienił się zalecenie, aby używać grup koligacji dla maszyn wirtualnych lub usługi zbliżeniowe.

Na poniższym diagramie przedstawiono taksonomii scenariuszy alokacji (przypiętych). 

![Taksonomia przypiętych alokacji](./media/virtual-machines-common-allocation-failure/Allocation3.png)

## <a name="resize-a-vm-or-add-vms-or-role-instances-to-an-existing-cloud-service"></a>Zmień rozmiar maszyny Wirtualnej lub dodawanie maszyn wirtualnych lub wystąpień roli do istniejącej usługi w chmurze
**Error**

Upgrade_VMSizeNotSupported lub GeneralError

**Przyczyny przypinanie klastra**

Żądanie, aby zmienić rozmiar maszyny Wirtualnej lub Dodaj Maszynę wirtualną lub wystąpieniem roli do istniejącej usługi w chmurze musi nastąpić w oryginalnego klastra, który jest hostem istniejącej usługi w chmurze. Tworzenie nowej usługi w chmurze umożliwia platformy Azure, aby znaleźć innego klastra, który bezpłatnymi zasobami lub obsługuje rozmiar maszyny Wirtualnej, którego zażądano.

**Obejście problemu**

Jeśli ten błąd jest Upgrade_VMSizeNotSupported *, należy spróbować innego rozmiaru maszyny Wirtualnej. Jeśli przy użyciu innego rozmiaru maszyny Wirtualnej nie jest opcjonalnym, ale można użyć różnych wirtualny adres IP (VIP), należy utworzyć nową usługę w chmurze do obsługi nowej maszyny Wirtualnej, a następnie dodaj nową usługę w chmurze do regionalnej sieci wirtualnej, w którym działają istniejących maszyn wirtualnych. Użycie istniejącej usługi w chmurze nie regionalnej sieci wirtualnej, możesz nadal tworzyć nową sieć wirtualną dla nowej usługi w chmurze, a następnie połącz swoje [istniejącą sieć wirtualną do nowej sieci wirtualnej](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Zobacz więcej informacji o [regionalnych sieci wirtualnych](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Jeśli ten błąd jest GeneralError *, jest typ zasobu (np. określonego rozmiaru maszyny Wirtualnej) jest obsługiwana przez klaster, że klaster ma bezpłatne zasoby w tej chwili. Podobnie jak w powyższym scenariuszu Usługa Dodaj zasób obliczeniowy żądany przez proces tworzenia nowej usługi w chmurze (należy zauważyć, że nową usługę w chmurze ma używać innego adresu VIP) i użyć regionalnej sieci wirtualnej do połączenia usługi w chmurze.

## <a name="restart-partially-stopped-deallocated-vms"></a>Ponowne uruchomienie częściowo zatrzymanych maszyn wirtualnych (z cofniętą alokacją)
**Error**

GeneralError *

**Przyczyny przypinanie klastra**

Częściowe dezalokacji oznacza, że zatrzymana (przydział zostanie cofnięty) jeden lub więcej, ale nie wszystkich maszyn wirtualnych w usłudze w chmurze. W chwili zatrzymania (Cofnij ich przydział) na maszynie Wirtualnej, są zwalniane, skojarzonych zasobów. Ponowne uruchomienie tej zatrzymano (cofnięto przydział) maszyny Wirtualnej jest zatem nowe żądanie alokacji. Ponowne uruchamianie maszyn wirtualnych w usłudze w chmurze częściowo cofnięcia przydziału jest równoważna do dodawania maszyn wirtualnych do istniejącej usługi w chmurze. Żądanie alokacji musi nastąpić w oryginalnego klastra, który jest hostem istniejącej usługi w chmurze. Tworzenie innej usługi w chmurze umożliwia platformy Azure, aby znaleźć innego klastra, który obsługuje rozmiar maszyny Wirtualnej, którego zażądano bezpłatny zasób lub.

**Obejście problemu**

Jeśli jest dopuszczalne, aby użyć innego adresu VIP, Usuń zatrzymano (cofnięto przydział) maszyn wirtualnych (zachowując skojarzone dyski) i Dodaj maszyny wirtualne z powrotem przez inną usługę w chmurze. Umożliwia łączenie usług w chmurze regionalnej sieci wirtualnej:

* Jeśli korzysta z istniejącej usługi w chmurze regionalnej sieci wirtualnej, po prostu Dodaj nową usługę w chmurze do tej samej sieci wirtualnej.
* Użycie istniejącej usługi w chmurze nie regionalnej sieci wirtualnej, Utwórz nową sieć wirtualną dla nowej usługi w chmurze, a następnie [połączyć z istniejącej sieci wirtualnej do nowej sieci wirtualnej](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Zobacz więcej informacji o [regionalnych sieci wirtualnych](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

## <a name="restart-fully-stopped-deallocated-vms"></a>Ponowne uruchomienie w pełni zatrzymanych maszyn wirtualnych (z cofniętą alokacją)
**Error**

GeneralError *

**Przyczyny przypinanie klastra**

Oznacza pełną dezalokacji zostanie zatrzymana (cofnięty przydział) wszystkich maszyn wirtualnych z usługi w chmurze. Żądania alokacji, aby ponownie uruchomić te maszyny wirtualne muszą nastąpić w oryginalnym klastrze, który hostuje usługę w chmurze. Tworzenie nowej usługi w chmurze umożliwia platformy Azure, aby znaleźć innego klastra, który bezpłatnymi zasobami lub obsługuje rozmiar maszyny Wirtualnej, którego zażądano.

**Obejście problemu**

Jeśli jest dopuszczalne, aby użyć innego adresu VIP, Usuń oryginalne zatrzymania (przydział zostanie cofnięty) maszyn wirtualnych (zachowując skojarzone dyski) i usuń odpowiednie usługi w chmurze (zasoby obliczeniowe skojarzone już zostały wydane po użytkownik zatrzymał (cofnął przydział) maszyn wirtualnych). Utwórz nową usługę w chmurze do dodawania maszyn wirtualnych z powrotem.

## <a name="stagingproduction-deployments-platform-as-a-service-only"></a>Wdrożenia przejściowego/produkcyjne (platforma jako usługa tylko)
**Error**

New_General * lub New_VMSizeNotSupported *

**Przyczyny przypinanie klastra**

Wdrażania przejściowego i wdrożenie produkcyjne usługi w chmurze znajdują się w tym samym klastrze. Podczas dodawania drugie wdrożenie odpowiedniego żądania alokacji zostanie podjęta w tym samym klastrze, który jest hostem pierwszym wdrożeniu.

**Obejście problemu**

Usuń pierwsze wdrożenie i oryginalną usługę w chmurze, a następnie ponownie wdrożyć usługę w chmurze. Ta akcja może być przejście pierwszym wdrożeniu w klastrze, który ma wystarczającą ilość wolnego zasobów, aby dopasować oba wdrożenia lub w klastrze, w którym obsługuje rozmiarów maszyn wirtualnych, które zostały wybrane.

## <a name="affinity-group-vmservice-proximity"></a>Grupa koligacji (maszyny Wirtualnej/usługi zbliżeniowe)
**Error**

New_General * lub New_VMSizeNotSupported *

**Przyczyny przypinanie klastra**

Obliczeń dowolnego zasobu przypisane do grupy koligacji jest powiązany z jednego klastra. Nowy zasób obliczeniowy żądań, w tym, że grupa koligacji są próby w tym samym klastrze, w którym znajdują się istniejące zasoby. Jest to istotne, czy nowe zasoby są tworzone za pomocą nowej usługi w chmurze lub istniejącej usługi w chmurze.

**Obejście problemu**

Jeśli grupy koligacji nie jest konieczne, nie używać grupy koligacji, lub grupować zasoby obliczeniowe w wielu grupach koligacji.

## <a name="affinity-group-based-virtual-network"></a>Sieci wirtualne oparte na grupach koligacji
**Error**

New_General * lub New_VMSizeNotSupported *

**Przyczyny przypinanie klastra**

Przed wprowadzeniem regionalnych sieci wirtualnych, trzeba skojarzyć sieć wirtualną z grupą koligacji. W wyniku obliczenia zasobów umieszczone w grupie koligacji są powiązane przez tego samego ograniczenia, zgodnie z opisem w temacie "alokacji scenariusza: Grupa koligacji (maszyny Wirtualnej/usługi zbliżeniowe) "powyższej sekcji. Zasoby obliczeniowe są powiązane z jednego klastra.

**Obejście problemu**

Jeśli grupy koligacji nie jest konieczne, Utwórz nowe, regionalnej sieci wirtualnej dla nowych zasobów, w przypadku dodawania, a następnie [połączyć z istniejącej sieci wirtualnej do nowej sieci wirtualnej](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Zobacz więcej informacji o [regionalnych sieci wirtualnych](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Możesz też [migrację sieci wirtualnej na podstawie grupy koligacji do regionalnej sieci wirtualnej](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/), a następnie ponownie dodaj żądanych zasobów.



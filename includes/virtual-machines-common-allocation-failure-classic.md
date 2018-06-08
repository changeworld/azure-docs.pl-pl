---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-windows, azure-resource-manager
author: genlin
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 04/14/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: f403e060859df6d1de96a3c0d478d57df2677eee
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "31531059"
---
Poniżej przedstawiono typowe scenariusze alokacji, które powodują żądanie alokacji przypięty. Firma Microsoft będzie Poznaj każdego scenariusza w dalszej części tego artykułu.

- Zmień rozmiar maszyny Wirtualnej lub Dodaj maszyny wirtualne lub wystąpień roli do istniejącej usługi w chmurze
- Uruchom ponownie częściowo zatrzymania maszyny wirtualnej (cofnięciu przydziału)
- Uruchom ponownie pełni zatrzymania maszyny wirtualnej (cofnięciu przydziału)
- Tymczasowych i produkcyjnych wdrożeń (platforma jako usługa tylko)
- Grupa koligacji (maszyna wirtualna lub usługa zbliżeniowe)
- Koligacja — na podstawie grupy sieci wirtualnej

Gdy zostanie wyświetlony błąd alokacji, sprawdź, czy dowolny z wymienionych scenariuszy dotyczy ten błąd. Użyj błąd alokacji, który jest zwracany przez platformę Azure, aby zidentyfikować odpowiedni scenariusz. Jeśli żądanie jest przypięty, usuń niektóre przypinania ograniczenia, aby otworzyć żądania do klastrów więcej, co zwiększa prawdopodobieństwo pomyślnego alokacji.
Ogólnie rzecz biorąc Jeśli błąd stanu, że "żądany rozmiar maszyny Wirtualnej nie jest obsługiwany", należy zawsze. Spróbuj ponownie później. Jest to spowodowane za mało zasobów został zwolniony w klastrze, aby obsłużyć żądanie. Jeśli problem nie zostanie żądany rozmiar maszyny Wirtualnej nie jest obsługiwana, spróbuj inny rozmiar maszyny Wirtualnej. W przeciwnym razie jedyną opcją jest usunięcie przypinania ograniczenia.

Dwie typowe scenariusze niepowodzenia są związane z grup koligacji. W przeszłości grupę koligacji zostało użyte do zapewnienia pobliżu maszyn wirtualnych i wystąpień usługi lub użyto w celu umożliwienia utworzenia sieci wirtualnej. Wraz z wprowadzeniem regionalnych sieci wirtualnych grup koligacji nie są wymagane do utworzenia sieci wirtualnej. Zmniejszeniu opóźnienia sieci w infrastrukturze Azure zalecenie, aby użyć grup koligacji dla maszyn wirtualnych lub zbliżeniowe usługi została zmieniona.

Na poniższym diagramie przedstawiono taksonomii w scenariuszach alokacji (przypiętych). 

![Taksonomii przypiętych alokacji](./media/virtual-machines-common-allocation-failure/Allocation3.png)

## <a name="resize-a-vm-or-add-vms-or-role-instances-to-an-existing-cloud-service"></a>Zmień rozmiar maszyny Wirtualnej lub Dodaj maszyny wirtualne lub wystąpień roli do istniejącej usługi w chmurze
**Error**

Upgrade_VMSizeNotSupported lub GeneralError

**Przyczyną przypinanie klastra**

Żądanie, aby zmienić rozmiar maszyny Wirtualnej lub dodać do istniejącej usługi w chmurze maszyny Wirtualnej lub wystąpienia roli musi podjąć w oryginalnym klastrze obsługującego istniejącą usługę w chmurze. Tworzenie nowej usługi w chmurze pozwala platformy Azure znaleźć innego klastra lub obsługuje rozmiar maszyny Wirtualnej, którego zażądano zwolnić zasoby.

**Obejście problemu**

Jeśli błąd Upgrade_VMSizeNotSupported *, spróbuj zmienić rozmiar maszyny Wirtualnej. Jeśli przy użyciu innego rozmiaru maszyny Wirtualnej nie ma możliwości użycia, ale można użyć różnych wirtualnego adresu IP (VIP), należy utworzyć nową usługę w chmurze do obsługi nowej maszyny Wirtualnej, a następnie dodaj nową usługę w chmurze do regionalną sieć wirtualną, której istniejące maszyny wirtualne są uruchomione. Użycie istniejącej usługi w chmurze nie regionalną sieć wirtualną, możesz utworzyć nową sieć wirtualną dla nowej usługi w chmurze, a następnie połącz z [istniejącej sieci wirtualnej do nowej sieci wirtualnej](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Zobacz więcej informacji [regionalnych sieci wirtualnych](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Jeśli błąd jest GeneralError *, jest prawdopodobne, że typ zasobu (np. dla określonego rozmiaru maszyny Wirtualnej) jest obsługiwana przez klaster, ale klaster nie ma wolnego zasobów w tej chwili. Podobny scenariusz powyżej, Dodaj zasób obliczeniowy żądany przez proces tworzenia nowej usługi w chmurze (Zauważ, że nowa usługa w chmurze ma używać innego adresu VIP) i połączenia usługi w chmurze przy użyciu regionalną sieć wirtualną.

## <a name="restart-partially-stopped-deallocated-vms"></a>Uruchom ponownie częściowo zatrzymania maszyny wirtualnej (cofnięciu przydziału)
**Error**

GeneralError *

**Przyczyną przypinanie klastra**

Częściowe dezalokacji oznacza, że zatrzymane (cofnięciu przydziału) jeden lub więcej, ale nie wszystkich maszyn wirtualnych w usłudze w chmurze. Po zatrzymaniu (deallocate) na maszynie Wirtualnej, są wydawane skojarzonych zasobów. Ponowne uruchomienie tego zatrzymanej maszyny Wirtualnej (cofnięciu przydziału) w związku z tym jest nowe żądanie alokacji. Ponowne uruchamianie maszyn wirtualnych w usłudze w chmurze częściowo deallocated jest odpowiednikiem Dodawanie maszyn wirtualnych do istniejącej usługi w chmurze. Żądanie alokacji musi podjąć w oryginalnym klastrze obsługującego istniejącą usługę w chmurze. Trwa tworzenie innej usługi chmury umożliwia platformy Azure znaleźć innego klastra, który bezpłatnego zasobu lub obsługuje rozmiar maszyny Wirtualnej, którego zażądano.

**Obejście problemu**

Jeśli można użyć różnych adresów VIP, Usuń zatrzymania maszyny wirtualnej (cofnięciu przydziału) (ale zachować skojarzone dyski) i dodać ponownie za pomocą innej usługi chmury maszyn wirtualnych. Użyj regionalną sieć wirtualną do połączenia usługi w chmurze:

* Jeśli istniejącą usługę w chmurze używa regionalną sieć wirtualną, po prostu Dodaj nową usługę w chmurze do tej samej sieci wirtualnej.
* Użycie istniejącej usługi w chmurze nie regionalną sieć wirtualną, Utwórz nową sieć wirtualną dla nowej usługi w chmurze, a następnie [połączyć istniejącej sieci wirtualnej do nowej sieci wirtualnej](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Zobacz więcej informacji [regionalnych sieci wirtualnych](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

## <a name="restart-fully-stopped-deallocated-vms"></a>Uruchom ponownie pełni zatrzymania maszyny wirtualnej (cofnięciu przydziału)
**Error**

GeneralError *

**Przyczyną przypinanie klastra**

Pełna dezalokacji oznacza, że został zatrzymany (cofnięciu przydziału) wszystkich maszyn wirtualnych z usługą w chmurze. Żądań alokacji, aby ponownie uruchomić te maszyny wirtualne muszą podjąć na oryginalnego klastra, który jest hostem usługi w chmurze. Tworzenie nowej usługi w chmurze pozwala platformy Azure znaleźć innego klastra lub obsługuje rozmiar maszyny Wirtualnej, którego zażądano zwolnić zasoby.

**Obejście problemu**

Jeśli można użyć różnych adresów VIP, usunąć oryginalny zatrzymania (cofnięciu przydziału) maszyn wirtualnych (ale zachować skojarzone dyski) i usuń odpowiednie usługi w chmurze (zasoby obliczeniowe skojarzone już zostały wydane, gdy zostanie zatrzymane (cofnięciu przydziału) maszyn wirtualnych). Utwórz nową usługę w chmurze można dodać z powrotem maszyn wirtualnych.

## <a name="stagingproduction-deployments-platform-as-a-service-only"></a>Wdrożeń przemieszczania/produkcyjnych (platforma jako usługa tylko)
**Error**

New_General * lub New_VMSizeNotSupported *

**Przyczyną przypinanie klastra**

Wdrażanie tymczasowej i wdrożenia produkcyjnego usługi w chmurze są obsługiwane w tym samym klastrze. Po dodaniu drugiego wdrożenia odpowiednie żądanie alokacji będą podejmowane w tym samym klastrze obsługującego ich pierwszym wdrożeniu.

**Obejście problemu**

Usuń ich pierwszym wdrożeniu i oryginalnego usługi w chmurze, a następnie ponownie wdrożyć usługę w chmurze. Ta akcja może grunt ich pierwszym wdrożeniu w klastrze, który ma wystarczającej ilości wolnych zasobów, aby dopasować oba wdrożenia lub w klastrze, który obsługuje rozmiarów maszyn wirtualnych, które zostały wybrane.

## <a name="affinity-group-vmservice-proximity"></a>Grupa koligacji (zbliżeniowe maszyny Wirtualnej lub usługi)
**Error**

New_General * lub New_VMSizeNotSupported *

**Przyczyną przypinanie klastra**

Wszelkie obliczeń zasobów przypisanych do grupy koligacji jest związany z jednego klastra. Nowy zasób obliczeniowy żądania w tej grupie koligacji są próby w tym samym klastrze, gdzie hostowane są istniejących zasobów. Jest to możliwe, czy nowe zasoby są tworzone przez nową usługę w chmurze albo przez istniejącą usługę w chmurze.

**Obejście problemu**

Grupy koligacji nie jest konieczne, nie używać grupy koligacji, czy grupa zasobów obliczeniowych do wielu grup koligacji.

## <a name="affinity-group-based-virtual-network"></a>Oparte na grupach koligacji sieci wirtualnej
**Error**

New_General * lub New_VMSizeNotSupported *

**Przyczyną przypinanie klastra**

Przed wprowadzeniem regionalnych sieci wirtualnych, wymagane było skojarzyć sieć wirtualną z grupą koligacji. W związku z tym obliczeniowe zasobów umieszczone w grupie koligacji są powiązane przez takich samych ograniczeń, zgodnie z opisem w "alokacji scenariusz: Grupa koligacji (zbliżeniowe maszyny Wirtualnej lub usługi)" powyższej sekcji. Zasoby obliczeniowe są powiązane z jednego klastra.

**Obejście problemu**

Nie należy do grupy koligacji, Utwórz nowy regionalną sieć wirtualną dla nowych zasobów w przypadku dodawania, a następnie [połączyć istniejącej sieci wirtualnej do nowej sieci wirtualnej](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Zobacz więcej informacji [regionalnych sieci wirtualnych](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Można też [migrację do regionalnej sieci wirtualnej sieci wirtualnej na podstawie grupy koligacji](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/), a następnie ponownie dodaj żądanych zasobów.

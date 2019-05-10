---
author: cynthn
ms.author: cynthn
ms.date: 04/30/2019
ms.topic: include
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: e1b3b5fe603072069cb3a19c7597fcc1872fefd7
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65416337"
---
Obrazy standardowych maszyn wirtualnych (VM) umożliwiają organizacjom migrację do chmury i zapewnia spójność we wdrożeniach. Obrazy obejmują zazwyczaj wstępnie zdefiniowanych ustawień zabezpieczeń i konfiguracji oraz niezbędne oprogramowanie. Konfigurowanie potoku przetwarzania obrazów wymaga czasu, infrastruktury i konfiguracji, ale za pomocą Kreatora obrazów maszyn wirtualnych platformy Azure, po prostu podać prostej konfiguracji opisujące obrazu, Prześlij go do usługi i wbudowany obraz i rozproszone.
 
Kreator obrazów maszyn wirtualnych platformy Azure (Kreatora obrazów platformy Azure) umożliwia rozpoczynać Windows lub obraz opartych na systemie Linux z portalu Azure Marketplace, istniejących obrazów niestandardowych lub Red Hat Enterprise Linux (RHEL) ISO i zacząć dodawać własne dostosowania. Ponieważ Kreator obrazów jest oparta na [HashiCorp Packer](https://packer.io/), można również zaimportować istniejące skrypty administracyjnej powłoki usługi Packer. Można również określić, gdzie chcesz obrazów hostowanych w współdzielona obraz w galerii platformy Azure (virtual-machines-common-shared-image-galleries.md) jako obrazu zarządzanego lub wirtualnego dysku twardego.

> [!IMPORTANT]
> Kreator obrazów platformy Azure jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Funkcje wersji zapoznawczej

Dla wersji zapoznawczej te funkcje są obsługiwane:

- Tworzenie obrazów złoty plan bazowy, który zawiera minimalne bezpieczeństwo i konfiguracje firmowe i umożliwiają działów zgodnie z własnymi dalsze odpowiadające ich potrzebom.
- Stosowanie poprawek dla istniejących obrazów, Kreator obrazów umożliwi stale stosowanie poprawek do istniejących obrazów niestandardowych.
- Dzięki integracji z galerii obrazów platformy Azure udostępnione, można do dystrybucji i wersji, i skalowania obrazy globalnie i pozwala na to system zarządzania obrazu.
- Integracja z istniejącym obrazie tworzyć potoki, po prostu wywołać Kreatora obrazów z potokiem lub korzystając z prostych (wersja zapoznawcza) obraz konstruktora Azure DevOps zadania.
- Migrowanie istniejących potoku dostosowywania obrazu na platformę Azure. Dostosowywanie obrazów przy użyciu istniejących skryptów, poleceń i procesów.
- Korzystać z pomocy technicznej Red Hat przenieść Twojej własnej subskrypcji. Tworzenie obrazów Red Hat Enterprise, do użytku z swoje subskrypcje Red Hat uprawnionych, nieużywane.
- Tworzenie obrazów w formacie VHD.
 

## <a name="regions"></a>Regiony
Usługa Konstruktora obrazów platformy Azure będzie dostępna w wersji zapoznawczej w tych regionach. Obrazy mogą być dystrybuowane spoza tych regionów.
- Wschodnie stany USA
- Wschodnie stany USA 2
- Zachodnio-środkowe stany USA
- Zachodnie stany USA
- Zachodnie stany USA 2

## <a name="os-support"></a>Obsługa systemu operacyjnego
AIB będzie obsługiwać portalu Azure Marketplace podstawowe obrazy systemu operacyjnego:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6
- CentOS 7.6
- Windows 2016
- Windows 2019


## <a name="how-it-works"></a>Jak to działa


![Rysunek koncepcyjny przedstawiający kreatora obrazów platformy Azure](./media/virtual-machines-image-builder-overview/image-builder.png)

Kreator obrazów platformy Azure jest w pełni zarządzana usługa platformy Azure, który jest dostępny przez dostawcę zasobów platformy Azure. Procesu Kreatora obrazów platformy Azure ma trzy główne części: źródła, dostosowywanie i dystrybucji, są one reprezentowane w szablonie. Na poniższym diagramie przedstawiono składniki, wraz z ich właściwości. 
 


**Procesu Kreatora obrazów** 

![Rysunek koncepcyjny przedstawiający procesu Kreatora obrazów platformy Azure](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. Utwórz szablon obrazu w formacie JSON. Ten plik JSON zawiera informacje dotyczące źródła obrazu, dostosowania i dystrybucji. Istnieje wiele przykładów w [repozytorium GitHub konstruktora obraz Azure](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).
1. Prześlij go do usługi, spowoduje to utworzenie artefakt obrazu szablonu w grupie zasobów, które określisz. W tle Kreator obrazów pobierze obraz źródłowy lub obrazu ISO i skrypty, zgodnie z potrzebami. Są one przechowywane w oddzielnej grupie zasobów, jest automatycznie tworzony w ramach subskrypcji, w formacie: IT_<DestinationResourceGroup>_<TemplateName>. 
1. Po utworzeniu szablonu obrazu może następnie utworzyć obraz. Kreator obrazów w tle używa szablon i pliki źródłowe do tworzenia maszyn wirtualnych, sieci i magazynu w IT_<DestinationResourceGroup>_<TemplateName> grupy zasobów.
1. W ramach tworzenia obrazów, Kreator obrazów dystrybuuje obraz zgodnie z szablonem, następnie usuwa dodatkowe zasoby w IT_<DestinationResourceGroup>_<TemplateName> grupy zasobów, który został utworzony dla procesu.


## <a name="permissions"></a>Uprawnienia

Aby umożliwić kreatora obrazów maszyn wirtualnych platformy Azure do dystrybucji obrazów zarządzanych obrazów lub do galerii obrazów udostępnione, konieczne będzie zapewnienie uprawnienia "Współautor" usługi "Kreatora obrazów maszyn wirtualnych platformy Azure" (identyfikator aplikacji: cf32a0cc-373c-47c9-9156-0db11f6a6dfc ) na temat grup zasobów. 

Jeśli używasz istniejącego niestandardowego obrazu zarządzanego lub wersję obrazu Kreatora obrazów platformy Azure będzie musiało przypadać "Reader" dostęp do tych grup zasobów.

Możesz przypisywać dostęp przy użyciu wiersza polecenia platformy Azure:

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName>
```

Jeśli konto usługi nie zostanie znaleziony, który oznacza, że subskrypcji, w którym dodajesz przypisania roli nie ma jeszcze zarejestrowana dla dostawcy zasobów.


## <a name="costs"></a>Koszty
Zostaną naliczone niektórych obliczeń, sieci i koszty magazynowania podczas tworzenia, kompilowania i przechowywanie obrazów za pomocą Kreatora obrazów platformy Azure. Te koszty są podobne do kosztów poniesionych przez ręcznego tworzenia obrazów niestandardowych. Dla zasobów opłata wyniesie stawki platformy Azure. 

W trakcie procesu tworzenia obrazu pliki są pobierane i przechowywane w `IT_<DestinationResourceGroup>_<TemplateName>` grupę zasobów, co spowoduje naliczenie opłaty za koszty magazynu małe. f, czy chcesz zachować te, usunąć szablon obrazu po kompilacji obrazu.
 
Kreator obrazów maszyna wirtualna jest tworzona przy użyciu rozmiaru D1v2 maszyny Wirtualnej i magazynu i sieci wymagane dla maszyny Wirtualnej. Te zasoby będą trwać przez czas trwania procesu kompilacji, a także zostaną usunięte po zakończeniu Kreatora obrazów podczas tworzenia obrazu. 
 
Kreator obrazów platformy Azure będzie dystrybuować obraz wybrany regiony, które mogą zostać naliczone opłaty za ruch wychodzący w sieci.
 
## <a name="next-steps"></a>Kolejne kroki 
 
Aby wypróbować kreatora obrazów platformy Azure, zobacz artykuły dotyczące tworzenia [Linux](../articles/virtual-machines/linux/image-builder.md) lub [Windows](../articles/virtual-machines/windows/image-builder.md) obrazów.
 
 

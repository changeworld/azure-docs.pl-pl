---
title: Pojemność arkusz kalkulacyjny planowania dla usługi Azure Stack | Dokumentacja firmy Microsoft
description: Więcej informacji o pojemności Planowanie arkusza kalkulacyjnego w przypadku wdrożeń usługi Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2018
ms.author: jeffgilb
ms.reviewer: prchint
ms.openlocfilehash: a37575e6e6e8902dc2dd89a26011031c2e1419c4
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2018
ms.locfileid: "53557261"
---
# <a name="azure-stack-capacity-planner"></a>Planista wydajności usługi Azure Stack
Planisty wydajności usługi Azure Stack to arkusz kalkulacyjny, używany do planowania pojemności zasobu usługi Azure Stack. Capacity planner zapewnia możliwość projektowania różne przydziały zasobów obliczeniowych i zobacz, jak te zmieszczą się na wybór ofert sprzętu. Poniżej znajdują się szczegółowe instrukcje dotyczące użycia usługi Azure Stack kalkulatora.

## <a name="worksheet-descriptions"></a>Opis arkusza
Poniżej przedstawiono krótkie podsumowanie arkusze zawarte w arkuszu kalkulacyjnym Planisty wydajności usługi Azure Stack, który można pobrać z [ http://aka.ms/azstackcapacityplanner ](https://aka.ms/azstackcapacityplanner):

|Nazwa karty|Opis|
|-----|-----|
|Zastrzeżenie dotyczące wersji|Krótki przegląd zastosowań Kalkulator, numer wersji i Data wydania.|
|Instrukcje|Zawiera szczegółowe instrukcje dotyczące Korzystanie z Planisty wydajności usługi Azure Stack.|
|DefinedSolutionSKUs|Tabelę zawierającą maksymalnie pięć definicji sprzętu. Wpisy w tym arkuszu są przykłady. Celem jest, że użytkownik zmieni się szczegółowe informacje, aby odpowiadały konfiguracji systemu, które są traktowane jako do użycia lub zakupu.|
|DefineByVMFootprint|Trwa tworzenie kolekcji różnych rozmiarów i liczby maszyn wirtualnych, aby znaleźć odpowiedni sprzęt jednostki SKU.|
|DefineByWorkloadFootprint|Trwa tworzenie kolekcji obciążeń usługi Azure Stack, aby znaleźć odpowiedni sprzęt jednostki SKU.|
|  |  |

## <a name="definedsolutionskus-instructions"></a>Instrukcje DefinedSolutionSKUs
Ten arkusz zawiera do pięciu przykłady definicji sprzętu. Zmień szczegóły, aby dopasować konfiguracji systemu, są traktowane jako do użycia lub zakupu.

### <a name="hardware-selections-provided-by-authorized-hardware-partners"></a>Opcje sprzętu oferowanych przez partnerów sprzętowych autoryzacji
Usługa Azure Stack jest dostarczana jako system zintegrowany z oprogramowaniem instalowanym przez partnerów rozwiązania. Ci partnerzy rozwiązania mają swoje własne, autorytatywny wersje usługi Azure Stack pojemności, narzędzia planowania i narzędzia te powinny być używane do finalize dyskusje rozwiązanie pojemność.

### <a name="multiple-ways-to-model-computing-resources"></a>Wiele sposobów, aby modelować, zasobów obliczeniowych
Podstawowe bloki konstrukcyjne, używane do modelowania w ramach planowania usługi Azure Stack resource są różnych rozmiarów maszyn wirtualnych usługi Azure Stack (VM). Zakres tych maszyn wirtualnych, rozmiaru, od najmniejszego, "0 Basic", do bieżącego największą maszynę Wirtualną, "Standard_Fsv2." Zgodnie z potrzebami, różne ilości różnych maszyn wirtualnych może służyć do tworzenia obliczeń alokacji zasobów za pomocą tego narzędzia na dwa różne sposoby.

1. Użytkownik planista wybiera ofertę sprzętu i widzi kombinacje różnych zasobów mieszczą się w tym zasobów sprzętowych. 

2. Użytkownik planista utworzy określonej kombinacji alokacji maszyny Wirtualnej, a następnie umożliwia pokazuje kalkulatora zasobów platformy Azure, dla których jednostek SKU sprzętu mogą obsłużyć tę konfigurację maszyny Wirtualnej.

To narzędzie udostępnia dwie metody przydzielania zasobów maszyny Wirtualnej. lub jako jedną kolekcję pojedynczego alokacji zasobów maszyny Wirtualnej jako kolekcję do sześciu różnych konfiguracjach obciążenia. Każdej konfiguracji obciążenia mogą zawierać różne przydziału dostępnych zasobów maszyny Wirtualnej. Szczegółowe informacje dotyczące tworzenia i używania każdego z tych modeli alokacji znajduje się poniżej. Tylko wartości znajdujących się w tle bez cieniowania komórki lub w ramach jednostki SKU rozwijanego listach tego arkusza powinien być modyfikowany. Zmiany wprowadzone w ramach cieniowane komórki mogą przestać działać obliczenia zasobów.


## <a name="definebyvmfootprint-instructions"></a>Instrukcje DefineByVMFootprint
Aby utworzyć model przy użyciu jednej kolekcji różnych rozmiarów i liczby maszyn wirtualnych, wybierz kartę "DefineByVMFootprint" i wykonaj tej sekwencji kroki.

1. W prawym górnym rogu tego arkusza użyj formantów okna dostarczonej listy rozwijane, aby wybrać początkowa liczba serwerów (zakresu od 4 do 16) mają zostać zainstalowane w każdym systemie sprzętu (SKU). Ta liczba serwerów, może być modyfikowany w dowolnym momencie podczas procesu modelowania, aby zobaczyć, jak ma to wpływ na ogólną dostępne zasoby dotyczące modelu alokacji zasobów.
2. Aby modelować różne alokacji zasobów maszyny Wirtualnej względem jedną konkretną konfiguracją sprzętu, można znaleźć listy rozwijane pole bezpośrednio pod etykietą "Bieżąca jednostka SKU" w prawym górnym rogu strony. Ściągnąć tego pola listy, a następnie wybierz żądany sprzętu jednostki SKU.
3. Teraz można przystąpić do rozpoczęcia, dodając różnych wielkości maszyny wirtualne do modelu. Aby dołączyć określonego typu maszyny Wirtualnej, należy wprowadzić wartość ilość w niebieskim polu Schemat z lewej strony tego wpisu maszyny Wirtualnej.

  > [!NOTE]
  > Całkowita ilość miejsca do maszyny Wirtualnej, który odwołuje się do całkowitej pojemności dysku danych maszyny wirtualnej (liczbę dysków obsługiwanych * maksymalną pojemność jednego dysku (1 TB)). Na podstawie wskaźników, konfiguracji możemy wypełnili tabeli dostępnych konfiguracji magazynu, w którym może wybrać żądany poziom zasobów usługi storage dla każdej maszyny Wirtualnej stosu usługi Azure. Jest jednak należy pamiętać, że możesz dodawać i Zmień tabelę dostępnych konfiguracji magazynu, zgodnie z potrzebami.<br><br>Każda maszyna wirtualna rozpoczyna się od wstępnie przypisanych lokalny magazyn tymczasowy. Aby uwzględnić alokowanie magazynu tymczasowego można zmienić numer lokalny temp miejscem, w tym ilości maksymalny dopuszczalny rozmiar magazynu tymczasowego menu rozwijanego.

4. W miarę dodawania maszyn wirtualnych zostaną wyświetlone wykresy pokazujące dostępnych zasobów jednostki SKU zmiany. Dzięki temu można zobaczyć efekty Dodawanie różnych rozmiarów i liczby maszyn wirtualnych w procesie modelowania. Innym sposobem, aby wyświetlić wpływ zmian jest Obejrzyj numery zużyto i nadal dostępne, bezpośrednio poniżej listy dostępnych maszyn wirtualnych. Te liczby odzwierciedlają szacowane wartości oparte na sprzęcie aktualnie wybranej jednostki SKU.
5. Po utworzeniu zestawu maszyn wirtualnych można znaleźć sugerowane sprzętu jednostki SKU, klikając przycisk "Sugerowane jednostki SKU" w prawym górnym rogu strony, bezpośrednio pod etykietą "Bieżąca jednostka SKU". Za pomocą tego przycisku, można zmodyfikować konfiguracje maszyny Wirtualnej i zobacz, jaki sprzęt obsługuje każdej konfiguracji.


## <a name="definebyworkloadfootprint-instructions"></a>Instrukcje DefineByWorkloadFootprint
Aby utworzyć model przy użyciu kolekcji usługi Azure Stack obciążeń, wybierz kartę "DefineByWorkloadFootprint" i wykonaj tej sekwencji kroki. Obciążeń platformy Azure Stack są tworzone za pomocą dostępnych zasobów maszyny Wirtualnej.   

> [!TIP]
> Aby zmienić rozmiar magazynu podana dla maszyny Wirtualnej platformy Azure Stack, zobacz uwagi w kroku 3 w poprzedniej sekcji.

1. W prawym górnym rogu tej strony użyj formantów okna dostarczonej listy rozwijane, aby wybrać początkowa liczba serwerów (zakresu od 4 do 16) mają zostać zainstalowane w każdym systemie sprzętu (SKU).
2. Aby modelować różne alokacji zasobów maszyny Wirtualnej względem jedną konkretną konfiguracją sprzętu, można znaleźć listy rozwijane pole bezpośrednio pod etykietą "Bieżąca jednostka SKU" w prawym górnym rogu strony. Ściągnąć tego pola listy, a następnie wybierz żądany sprzętu jednostki SKU.
3. Wybierz odpowiedni rozmiar dla każdego żądanego maszyn wirtualnych stosu platformy Azure, na stronie DefineByVMFootprint, zgodnie z powyższym opisem w kroku 3 instrukcji DefineByVMFootprint. Rozmiar magazynu dla maszyny Wirtualnej jest zdefiniowany w arkuszu DefineByVMFootprint.
4. Począwszy od lewego górnego rogu strony DefineByWorkloadFootprint, Utwórz konfiguracje dla maksymalnie sześć różnych typów obciążenia pracą, wprowadzając ilości każdego typu maszyn wirtualnych znajdujących się w ramach tego obciążenia. Odbywa się przez umieszczenie wartości liczbowe w kolumnie bezpośrednio pod nazwą tego obciążenia. Nazwy obciążenie może być modyfikowany odzwierciedlające typ obciążenia, które będą obsługiwane przez tę konfigurację określonego.
5. Możesz dołączyć określonej ilości każdego typu obciążenia, wprowadzając wartość w dolnej części tej kolumny bezpośrednio pod etykietą "Quantity".
6. Po utworzeniu typów obciążenia pracą i ilości, klikając pozycję "Sugerowane jednostki SKU" znajdujący się w prawym górnym rogu strony, bezpośrednio poniżej etykietę "Bieżąca jednostka SKU" spowoduje, że najmniejsza jednostka SKU z wystarczającą liczbą zasobów do obsługi tego ogólny Konfiguracja obciążeń, które mają być wyświetlane.
7. Dodatkowo modelowania, może być realizowane przez zmodyfikowanie Liczba wybranych serwerów na potrzeby sprzętu jednostki SKU, lub zmianę alokacji maszyny Wirtualnej lub ilości, w ramach konfiguracji obciążenia. Wykresy skojarzone zostanie wyświetlona natychmiast uzyskuje opinie pokazujący, jak zmiany wpływają na ogólny zużycia zasobów.
8. Po przejściu zmiany jednostki SKU sugerowanych dla nowej konfiguracji zostanie wyświetlona ponowne kliknięcie przycisku "Sugerowane jednostki SKU".


## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [integrację centrum danych informacje dotyczące usługi Azure Stack](azure-stack-datacenter-integration.md)

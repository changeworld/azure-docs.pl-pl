---
title: Podręcznik administratora Azure Lab Services | Microsoft Docs
description: Ten przewodnik pomaga administratorom, którzy tworzą konta laboratorium i zarządzają nimi przy użyciu Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2019
ms.author: spelluru
ms.openlocfilehash: 638a90615d248b3c2829770432dd6a08eb4bb2fb
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771738"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure Lab Services — Podręcznik administratora
Administratorzy IT, którzy zarządzają zasobami w chmurze organizacji, są również zwykle odpowiedzialni za Konfigurowanie konta laboratorium dla swojej organizacji. Administratorzy lub wykładowcy tworzą laboratoria zajęć na koncie laboratorium. Ten artykuł zawiera ogólne omówienie związanych zasobów platformy Azure oraz wskazówki dotyczące ich tworzenia.

![Widok wysokiego poziomu zasobów platformy Azure na koncie laboratorium](../media/administrator-guide/high-level-view.png)

- Laboratoria zajęć są hostowane w ramach subskrypcji platformy Azure należącej do Azure Lab Services
- Konta laboratorium, udostępnione galerii obrazów i wersje obrazów są hostowane w ramach subskrypcji
- W tej samej grupie zasobów można korzystać z konta Lab i galerii obrazów fragmentu. Na tym diagramie znajdują się one w różnych grupach zasobów. 

## <a name="subscription"></a>Subskrypcja
Twoja organizacja ma co najmniej jedną subskrypcję platformy Azure. Subskrypcja służy do zarządzania rozliczeniami i zabezpieczeniami dla wszystkich resources\services platformy Azure, które są używane w ramach tego konta, w tym kont laboratorium.

Relacja między kontem laboratorium a jego subskrypcją jest ważna, ponieważ:

- Rozliczenia są raportowane w ramach subskrypcji zawierającej konto laboratorium.
- Można przyznać użytkownikom w dzierżawie usługi Azure Active Directory (AD) skojarzonej z subskrypcją dostępną do Azure Lab Services. Możesz dodać użytkownika jako konto laboratorium owner\contributor lub jako twórcę laboratorium zajęć.

Usługi zajęć i ich maszyny wirtualne są zarządzane w całości dla Ciebie. Aby były określone, są one hostowane w ramach dedykowanej subskrypcji należącej do Azure Lab Services.

## <a name="resource-group"></a>Grupa zasobów
Subskrypcja zawiera co najmniej jedną grupę zasobów. Grupy zasobów służą do tworzenia grup logicznych zasobów platformy Azure, które są używane razem w ramach tego samego rozwiązania.  

Podczas tworzenia konta laboratorium należy skonfigurować grupę zasobów, która zawiera konto laboratorium. 

Grupa zasobów jest również wymagana podczas tworzenia [galerii obrazów udostępnionych](#shared-image-gallery). Możesz wybrać opcję umieszczenia konta laboratorium i udostępnionej galerii obrazów w dwóch oddzielnych grupach zasobów, która jest typowa, jeśli planujesz udostępnienie galerii obrazów w różnych rozwiązaniach. Można też zdecydować się na umieszczenie ich w tej samej grupie zasobów.

Podczas tworzenia konta laboratorium i automatycznego tworzenia i dołączania udostępnionej galerii obrazów konto laboratorium i Galeria obrazów udostępnionych są domyślnie tworzone w oddzielnych grupach zasobów. To zachowanie zostanie wyświetlone w przypadku korzystania z kroków opisanych w tym samouczku: [Konfigurowanie galerii obrazów udostępnionych w momencie tworzenia konta laboratorium](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation). Obraz w górnej części tego artykułu używa również tej konfiguracji. 

Firma Microsoft zaleca, aby przed zaplanowaniem struktury grup zasobów zaplanował swoją strukturę, ponieważ nie można zmienić grupy zasobów konta laboratorium lub udostępnionej galerii obrazów po jej utworzeniu. Jeśli musisz zmienić grupę zasobów dla tych zasobów, musisz usunąć i utworzyć ponownie konto laboratorium and\or udostępnionej galerii obrazów.

## <a name="lab-account"></a>Konto laboratorium
Konto laboratorium służy jako kontener dla jednej lub większej liczby laboratoriów zajęć. Gdy rozpoczynasz pracę z Azure Lab Services, często ma ono tylko jedno konto laboratorium. W miarę skalowania użycia laboratorium możesz później utworzyć więcej kont laboratorium.

Na poniższej liście przedstawiono scenariusze, w których może być korzystne więcej niż jedno konto laboratorium:

- **Zarządzanie różnymi wymaganiami dotyczącymi zasad w laboratoriach zajęć** 

    Podczas konfigurowania konta laboratorium należy ustawić zasady, które mają zastosowanie do wszystkich laboratoriów klasy na koncie laboratorium, takich jak:
    - Sieć wirtualna platformy Azure z zasobami udostępnionymi, do których może uzyskać dostęp laboratorium klasy. Na przykład może istnieć zestaw klas laboratoriów, które wymagają dostępu do udostępnionego zestawu danych w ramach sieci wirtualnej.
    - Obrazy maszyn wirtualnych, które mogą być używane przez laboratorium zajęć do tworzenia maszyn wirtualnych. Na przykład może istnieć zestaw klas laboratoriów, które wymagają dostępu do obrazu portalu Marketplace w witrynie [Data Science VM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu) .  

    Jeśli posiadasz laboratoria, które mają unikatowe wymagania dotyczące zasad ze sobą, może być korzystne utworzenie oddzielnych kont laboratorium do oddzielnego zarządzania tymi laboratoriami.
- **Ogranicz dostęp twórców laboratorium do określonych laboratoriów zajęć**  

    Gdy użytkownik zostanie dodany jako twórca laboratorium, uzyskuje dostęp do wszystkich laboratoriów stacjonarnych w ramach konta laboratorium, w tym laboratoriów, które są tworzone przez innych twórców laboratorium. Aby ograniczyć twórców laboratorium do zarządzania określonymi laboratoriami, można utworzyć oddzielne konta laboratorium w celu ograniczenia zakresu dostępu. Na przykład możesz utworzyć osobne konto laboratorium dla każdego działu na Uniwersytecie. Na przykład: jedno konto laboratorium dla działu nauki i drugi dla działu matematycznego itd.   
- **Oddzielanie budżetu według konta laboratorium**

    Zamiast poinformowania o wszystkich kosztach laboratorium dla jednego konta laboratoryjnego, może być konieczne wyraźne oddzielenie budżetu. Kontynuując przykład powyższego punktora, można utworzyć konto laboratorium dla każdego departamentu University, aby odpowiednio oddzielić budżet. Za pomocą Azure Cost Management można wyświetlić koszt poszczególnych poszczególnych kont laboratorium.
- **Izolowanie laboratoriów pilotażowych od aktywnych laboratoriów**

    Mogą wystąpić sytuacje, w których chcesz, aby zmiany zasad pilotażowych zostały wprowadzone do konta laboratorium bez wpływu na aktywną laboratorium. W tym scenariuszu tworzenie oddzielnego konta laboratorium do celów pilotażowych pozwala na odizolowanie zmian. 

## <a name="classroom-lab"></a>Laboratorium zajęć
Laboratorium zajęć zawiera co najmniej jedną maszynę wirtualną, która jest przypisana do konkretnego ucznia. Ogólnie rzecz biorąc, można oczekiwać:

- Ma jedno laboratorium klasy dla każdej klasy.
- Utwórz nowy zestaw klas Labs dla każdego semestru (lub dla każdego przedziału czasowego, który jest oferowany dla klasy). Zwykle w przypadku klas, które mają ten sam obraz, można użyć [udostępnionej galerii obrazów](#shared-image-gallery) do udostępniania obrazów w laboratoriach i semestrach.

Podczas określania sposobu tworzenia struktury laboratoriów zajęć należy wziąć pod uwagę następujące kwestie:

- **Wszystkie maszyny wirtualne w laboratorium zajęć są wdrażane przy użyciu tego samego obrazu, który został opublikowany**. W związku z tym, jeśli dysponujesz klasą, która wymaga publikacji różnych obrazów laboratoryjnych, należy utworzyć osobne laboratorium dla każdej z nich.
- **Przydział użycia jest ustawiany na poziomie laboratorium i ma zastosowanie do wszystkich użytkowników w laboratorium**. Na przykład może istnieć zestaw wykładowców, którzy potrzebują dostępu do maszyn wirtualnych klasy w celu przygotowania do nauczania, ale osoby przekazujące wymagają tylko 10 godzin, a Studenci zarejestrowani w klasie wymagają przydziału 40 godzin. Aby ustawić różne przydziały dla użytkowników, należy utworzyć osobne laboratoria klasy. Jednak po ustawieniu limitu przydziału można dodać więcej godzin do określonego użytkownika.
- **Harmonogram uruchamiania lub zamykania jest ustawiany na poziomie laboratorium i ma zastosowanie do wszystkich maszyn wirtualnych w środowisku laboratoryjnym**. Podobnie jak w poprzednim punkcie, jeśli trzeba ustawić różne harmonogramy dla użytkowników, należy utworzyć osobne laboratoria zajęć. 

## <a name="shared-image-gallery"></a>Galeria obrazów udostępnionych
Udostępniona Galeria obrazów jest dołączona do konta laboratorium i służy jako centralne repozytorium do przechowywania obrazów. Obraz jest zapisywany w galerii, gdy nauczycieli wybierze opcję zapisu z maszyny wirtualnej szablonu laboratorium klasowego. Za każdym razem, gdy nauczycieli wprowadza zmiany w szablonie maszyny wirtualnej i zapisuje, nowe wersje obrazu są zapisywane podczas zachowywania poprzednich wersji.

Instruktorzy mogą publikować wersję obrazu z galerii udostępnionych obrazów podczas tworzenia nowego laboratorium zajęć. Mimo że Galeria może przechowywać wiele wersji obrazu, wykładowcy mogą wybrać tylko najnowszą wersję podczas tworzenia laboratorium.

Udostępniona Galeria obrazów jest opcjonalnym zasobem, który nie jest potrzebny do natychmiastowego rozpoczęcia pracy z kilkoma pracowniami. Jednak używanie galerii obrazów udostępnionych ma wiele korzyści, które są przydatne podczas skalowania do większej ilości laboratoriów:

- **Umożliwia zapisywanie wersji obrazu szablonu maszyny wirtualnej i zarządzanie nimi**.

    Jest to przydatne podczas tworzenia obrazu niestandardowego lub wprowadzania zmian (oprogramowania, konfiguracji itd.) do obrazu z galerii publicznej witryny Marketplace.  Na przykład typowe dla nauczycieli wymagają zainstalowania różnych software\tooling. Zamiast wymagać od uczniów ręcznego instalowania tych wymagań wstępnych, różne wersje obrazu maszyny wirtualnej mogą być zapisane w galerii obrazów udostępnionych. Te wersje obrazu mogą być następnie używane podczas tworzenia nowych laboratoriów zajęć.
- **Włącza sharing\reuse szablonów maszyn wirtualnych w laboratoriach zajęć**.

    Zapobiega to konieczności konfigurowania obrazu od podstaw za każdym razem, gdy tworzysz nowe laboratorium zajęć. Na przykład jeśli jest oferowanych wiele klas, które wymagają tego samego obrazu, ten obraz musi być utworzony tylko raz i zapisany w galerii obrazów udostępnionych, tak aby można go było udostępnić w laboratoriach z klasą.
- **Zapewnia dostępność obrazu za poorednictwem replikacji**.

    Podczas zapisywania do galerii obrazów udostępnionych z laboratorium zajęć, obraz jest automatycznie replikowany do innych regionów w tej samej lokalizacji geograficznej. W przypadku awarii dla regionu Publikowanie maszyny wirtualnej szablonu w laboratorium zajęć nie ma wpływu na replikę obrazu w innych regionach. Ponadto ułatwia to wydajność w scenariuszach publikowania wielu maszyn wirtualnych Dzięki rozproszeniu do korzystania z różnych replik.

Aby logicznie grupować obrazy udostępnione, masz kilka opcji:

- Utwórz wiele udostępnionych galerii obrazów. Każde konto laboratorium może łączyć się tylko z jedną udostępnioną galerią obrazów, dlatego ta opcja będzie wymagała również utworzenia wielu kont laboratorium.
- Można też użyć jednej udostępnionej galerii obrazów, która jest współużytkowana przez wiele kont laboratorium. W takim przypadku każde konto laboratorium może włączyć tylko te obrazy, które mają zastosowanie do laboratoriów klasy, które zawiera.

## <a name="naming"></a>Nazewnictwo
Po rozpoczęciu pracy z Azure Lab Services zalecamy ustanowienie konwencji nazewnictwa dla grup zasobów, kont laboratorium, laboratoriów zajęć i galerii obrazów udostępnionych. Chociaż ustanowione konwencje nazewnictwa będą unikatowe dla potrzeb organizacji, w poniższej tabeli przedstawiono ogólne wytyczne.

| Typ zasobu | Rola | Sugerowany wzorzec | Przykłady |
| ------------- | ---- | ----------------- | -------- | 
| Grupa zasobów | Zawiera jedno lub więcej kont laboratorium i jedną lub więcej udostępnionych galerii obrazów | \<krótka nazwa organizacji\>-\<Environment\>RG<ul><li>**Krótka nazwa organizacji** określa nazwę organizacji obsługiwaną przez grupę zasobów</li><li>**Środowisko** identyfikuje środowisko dla zasobu, np. test lub produkcja</li><li>**RG** oznacza typ zasobu: Grupa zasobów.</li></ul> | contosouniversitylabs — RG<br/>contosouniversitylabs-test-RG<br/>contosouniversitylabs — prod-RG |
| Konto laboratorium | Zawiera co najmniej jedną Labs | \<krótka nazwa organizacji\>-\<Environment\>-La<ul><li>**Krótka nazwa organizacji** określa nazwę organizacji obsługiwaną przez grupę zasobów</li><li>**Środowisko** identyfikuje środowisko dla zasobu, np. test lub produkcja</li><li>**La** oznacza typ zasobu: konto laboratorium.</li></ul> | contosouniversitylabs — La<br/>mathdeptlabs — La<br/>sciencedeptlabs-test-La<br/>sciencedeptlabs — prod-La |
| Laboratorium zajęć | Zawiera co najmniej jedną maszynę wirtualną |\<nazwę klasy\>-\<przedziału czasu\>-\<nauczycieli identyfikator\><ul><li>**Nazwa klasy** identyfikuje nazwę klasy obsługiwanej przez laboratorium.</li><li>**Przedział czasu** określa przedział czasu, w którym jest oferowana Klasa.</li>**Identyfikator edukacji** identyfikuje nauczycieli, który jest właścicielem laboratorium.</li></ul> | CS1234-fall2019-jankowalski<br/>CS1234-spring2019-jankowalski | 
| Galeria obrazów udostępnionych | Zawiera co najmniej jedną wersję obrazu maszyny wirtualnej | Galeria\>krótkiej nazwy organizacji \< | contosouniversitylabsgallery |

Aby uzyskać więcej informacji na temat nazewnictwa innych zasobów platformy Azure, zobacz [konwencje nazewnictwa dla zasobów platformy Azure](/azure/architecture/best-practices/naming-conventions).

## <a name="regions-or-locations"></a>Regiony lub lokalizacje
Podczas konfigurowania zasobów Azure Lab Services "wymagane jest podanie regionu lub lokalizacji centrum danych, które będzie hostować zasób. Poniżej znajdują się szczegółowe informacje o tym, jak region\location ma wpływ na każdy z następujących zasobów używanych w ramach wdrożenia usług laboratoryjnych:

- **Grupa zasobów**

    Region określa centrum danych, w którym są przechowywane informacje o grupie zasobów. Zasoby platformy Azure zawarte w grupie zasobów mogą znajdować się w różnych regionach z ich poziomu nadrzędnego.
- **Konto laboratorium lub laboratorium zajęć**

    Lokalizacja konta laboratorium wskazuje region dla tego zasobu. Pracownie klas utworzone w ramach konta laboratorium można wdrożyć w dowolnym regionie w obrębie tej samej lokalizacji geograficznej. Określony region, w którym są wdrażane maszyny wirtualne laboratorium, jest automatycznie wybierany w oparciu o pojemność dostępną w regionie w tym czasie.  
    Jeśli administrator zezwoli twórcom laboratorium na wybór lokalizacji laboratorium zajęć, lokalizacje dostępne do wyboru są oparte na dostępnej pojemności regionalnej podczas tworzenia laboratorium.

    Lokalizacja laboratorium klasowego określa również, które rozmiary maszyn wirtualnych są dostępne do wyboru. Niektóre rozmiary obliczeń są dostępne tylko w określonych lokalizacjach.
- **Galeria obrazów udostępnionych**

    Region wskazuje region źródłowy, w którym jest przechowywana pierwsza wersja obrazu przed automatyczną replikacją do regionów docelowych.
    
Ogólną zasadą jest ustawienie region\location zasobu, który jest najbliżej użytkowników. W przypadku laboratoriów stacjonarnych oznacza to utworzenie laboratorium klasy najbliżej uczniów. W przypadku kursów online, w których studenci znajdują się na całym świecie, należy użyć najlepszych orzeczeń do utworzenia laboratorium klasy, które znajduje się centralnie. Lub Podziel klasę na wiele laboratoriów o klasie w oparciu o region uczniów.

## <a name="vm-sizing"></a>Rozmiar maszyny wirtualnej
Gdy Administratorzy lub twórcy laboratorium tworzą laboratorium zajęć, mogą wybrać spośród następujących rozmiarów maszyn wirtualnych na podstawie potrzeb ich klasy. Należy pamiętać, że dostępne rozmiary obliczeń zależą od regionu, w którym znajduje się konto w laboratorium:

| Rozmiar | Specyfikacja | Sugerowane użycie |
| ---- | ----- | ------------- |
| Małe| <ul><li>2 rdzenie</li><li>3,5 GB PAMIĘCI RAM</li></ul> | Ten rozmiar najlepiej nadaje się w przypadku wiersza polecenia, otwierania przeglądarki sieci Web, serwerów sieci Web o małym ruchu, małych i średnich baz danych. |
| Średnie | <ul><li>4 rdzenie</li><li>7 GB PAMIĘCI RAM</li></ul> | Ten rozmiar jest najlepiej dostosowany do relacyjnych baz danych, buforowania w pamięci i analizy. |
| Średni (Wirtualizacja zagnieżdżona) | <ul><li>4 rdzenie</li><li>16 GB PAMIĘCI RAM</li></ul> | Ten rozmiar jest najlepiej dostosowany do relacyjnych baz danych, buforowania w pamięci i analizy.  Ten rozmiar obsługuje również wirtualizację zagnieżdżoną. |
| Duże | <ul><li>8 rdzeni</li><li>32 GB PAMIĘCI RAM</li></ul> | Ten rozmiar najlepiej nadaje się w przypadku aplikacji wymagających szybszych procesorów CPU, lepszej wydajności dysków lokalnych, dużych baz danych i dużych pamięci podręcznych pamięci.  Ten rozmiar obsługuje również wirtualizację zagnieżdżoną. |
| Mały procesor GPU (wizualizacja) | <ul><li>6 rdzeni</li><li>56 GB PAMIĘCI RAM</li> | Ten rozmiar najlepiej nadaje się do zdalnej wizualizacji, przesyłania strumieniowego, gier, kodowania przy użyciu struktur, takich jak OpenGL i DirectX. |
| Mały procesor GPU (obliczenia) | <ul><li>6 rdzeni</li><li>56 GB PAMIĘCI RAM</li></ul> |Ten rozmiar najlepiej nadaje się w przypadku aplikacji intensywnie korzystających z komputerów, takich jak sztuczna inteligencja i uczenie głębokie. |
| Średni procesor GPU (wizualizacja) | <ul><li>12 rdzeni</li><li>112 GB PAMIĘCI RAM</li></ul> | Ten rozmiar najlepiej nadaje się do zdalnej wizualizacji, przesyłania strumieniowego, gier, kodowania przy użyciu struktur, takich jak OpenGL i DirectX. |

## <a name="manage-identity"></a>Zarządzanie tożsamością
Istnieją dwa typy ról, które może mieć administrator konta laboratorium:

- **Właściciel**

    Administrator, który ma przypisaną rolę **właściciela** , ma pełny dostęp do konta laboratorium, w tym prawo do przyznawania innym użytkownikom dostępu do konta laboratorium i dodawania twórców laboratorium. Administrator, który domyślnie tworzy konto laboratorium, jest dodawany jako właściciel.
- **Współautor**

    Administrator, do którego przypisano rolę współautor, może zmieniać ustawienia konta laboratorium, ale nie mogą udzielić dostępu innym użytkownikom. Ponadto program może dodawać twórców laboratorium.

Po dołączeniu udostępnionej galerii obrazów do konta laboratorium, dostęp jest udzielany automatycznie dla twórców i administratorów laboratorium, aby mogli wyświetlać i zapisywać obrazy w galerii. 

## <a name="pricing"></a>Cennik

### <a name="azure-lab-services"></a>Azure Lab Services
Cennik Azure Lab Services został opisany w następującym artykule: [Azure Lab Services Cennik](https://azure.microsoft.com/pricing/details/lab-services/).

Należy również wziąć pod uwagę ceny udostępnionej galerii obrazów, jeśli planujesz jej używać do przechowywania wersji obrazów i zarządzania nimi. 

### <a name="shared-image-gallery"></a>Galeria obrazów udostępnionych
Tworzenie galerii obrazów udostępnionych i dołączanie jej do konta laboratorium jest bezpłatne. Koszty nie są naliczane, dopóki nie zapiszesz wersji obrazu w galerii. Zazwyczaj ceny za używanie udostępnionej galerii obrazów są dość nieznaczne, ale ważne jest, aby zrozumieć, jak jest ona obliczana, ponieważ nie jest uwzględniona w cenach Azure Lab Services.  

### <a name="storage-charges"></a>Opłaty za magazyn
Aby przechowywać wersje obrazów, w galerii obrazów udostępnionych są używane standardowe dyski twarde dysk twardy. Rozmiar używanego dysku zarządzanego przez dysk twardy zależy od rozmiaru przechowywanego obrazu. Zapoznaj się z następującym artykułem, aby wyświetlić Cennik: [Zarządzanie dyskami zarządzanymi](https://azure.microsoft.com/pricing/details/managed-disks/).


### <a name="replication-and-network-egress-charges"></a>Opłaty za replikację i ruch wychodzący z sieci
Po zapisaniu wersji obrazu przy użyciu maszyny wirtualnej szablonu laboratorium klasy (VM) usługi laboratoryjne najpierw przechowują ją w regionie źródłowym, a następnie automatycznie replikuje wersję obrazu źródłowego do co najmniej jednego regionu docelowego. Należy pamiętać, że Azure Lab Services automatycznie replikuje wersję obrazu źródłowego do wszystkich regionów docelowych w geolokalizacji, w której znajduje się laboratorium klasowe. Na przykład jeśli laboratorium zajęć znajduje się w lokalizacji geograficznej USA, wersja obrazu jest replikowana do każdego z ośmiu regionów istniejących w Stanach Zjednoczonych

Opłata za ruch wychodzący sieci występuje, gdy wersja obrazu jest replikowana z regionu źródłowego do dodatkowych regionów docelowych. Opłata jest naliczana na podstawie rozmiaru wersji obrazu, gdy dane obrazu są początkowo przenoszone z regionu źródłowego.  Aby uzyskać szczegółowe informacje o cenach, zapoznaj się z następującym artykułem: [szczegóły cennika dotyczącego przepustowości](https://azure.microsoft.com/pricing/details/bandwidth/).

Klienci [rozwiązań edukacyjnych](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) mogą zrezygnować z płacenia opłat wychodzących. Aby dowiedzieć się więcej, skontaktuj się z menedżerem konta.  Aby uzyskać więcej informacji, zobacz sekcję **często zadawanych pytań** w połączonym dokumencie, w tym pytanie "Jakie programy transferu danych istnieją dla klientów akademickich I jak mam zakwalifikować?".

### <a name="pricing-example"></a>Przykład cen
Aby podsumowanie opisane powyżej ceny, przyjrzyjmy się przykładowi zapisywania obrazu maszyny wirtualnej z szablonami w galerii obrazów udostępnionych. Założono następujące scenariusze:

- Masz jeden niestandardowy obraz maszyny wirtualnej.
- Zapisujesz dwie wersje obrazu.
- Twoje laboratorium znajduje się w Stanach Zjednoczonych, które zawierają łącznie osiem regionów.
- Każda wersja obrazu ma rozmiar 32 GB; w związku z tym cena dysku zarządzanego przez dysk twardy wynosi $1,54 miesięcznie.

Łączny koszt jest szacowany jako:

Liczba obrazów × liczba wersji × liczba replik × cena dysku zarządzanego

W tym przykładzie kosztem jest:

1 obraz niestandardowy (32 GB) x 2 wersje x 8 USA regiony x $1,54 = $24,64 miesięcznie

### <a name="cost-management"></a>Zarządzanie kosztami
Ważne jest, aby administrator konta laboratorium mógł zarządzać kosztami przez zwykłe usuwanie niepotrzebnych wersji obrazu z galerii. 

Nie należy usuwać replikacji do określonych regionów w celu zmniejszenia kosztów (Ta opcja istnieje w galerii obrazów udostępnionych). Zmiany replikacji mogą mieć niekorzystny wpływ na możliwość publikowania maszyn wirtualnych przez usługę Azure Lab z obrazów zapisanych w galerii obrazów udostępnionych.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z samouczkiem, aby uzyskać instrukcje krok po kroku dotyczące tworzenia konta laboratorium i laboratorium: [Samouczek: Konfigurowanie konta laboratorium](tutorial-setup-lab-account.md)

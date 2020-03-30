---
title: Usługi Azure Lab — przewodnik dla administratorów | Dokumenty firmy Microsoft
description: Ten przewodnik pomaga administratorom, którzy tworzą konta laboratoryjne i zarządzają nimi przy użyciu usług Azure Lab Services.
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
ms.openlocfilehash: 8608aaab7bb8b6d10e67f27678c17f20a6c243da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80370852"
---
# <a name="azure-lab-services---administrator-guide"></a>Usługi Azure Lab — przewodnik dla administratorów
Administratorzy technologii informatycznych (IT), którzy zarządzają zasobami chmury uniwersytetu, są zazwyczaj odpowiedzialni za skonfigurowanie konta laboratoryjnego dla swojej szkoły. Po skonfigurowaniu konta laboratorium administratorzy lub nauczyciele tworzą laboratoria w klasie, które znajdują się na koncie laboratorium. Ten artykuł zawiera omówienie wysokiego poziomu zaangażowanych zasobów platformy Azure i wskazówki dotyczące ich tworzenia.

![Widok wysokiego poziomu zasobów platformy Azure na koncie laboratorium](../media/administrator-guide/high-level-view.png)

- Laboratoria classroom są hostowane w ramach subskrypcji platformy Azure należącej do usługi Azure Lab Services.
- Konta laboratoryjne, galeria obrazów udostępnionych i wersje obrazów są hostowane w ramach subskrypcji.
- Konto laboratorium i galeria obrazów udostępnionych można mieć w tej samej grupie zasobów. Na tym diagramie znajdują się one w różnych grupach zasobów. 

## <a name="subscription"></a>Subskrypcja
Twoja uczelnia ma co najmniej jedną subskrypcję platformy Azure. Subskrypcja służy do zarządzania rozliczeniami i zabezpieczeniami dla wszystkich zasobów platformy Azure\usług, które są w niej używane, w tym kont laboratoryjnych.

Relacja między kontem laboratorium a jego subskrypcją jest ważna, ponieważ:

- Rozliczenia są zgłaszane za pośrednictwem subskrypcji, która zawiera konto laboratorium.
- Możesz zapewnić użytkownikom w dzierżawie usługi Azure Active Directory (AD) dostęp do usług Azure Lab Services. Możesz dodać użytkownika jako właściciela konta laboratorium\współautora, twórcę laboratorium w klasie lub właściciela laboratorium w klasie.

Laboratoria classroom i ich maszyny wirtualne (VM) są zarządzane i hostowane w ramach subskrypcji należącej do usługi Azure Lab Services.

## <a name="resource-group"></a>Grupa zasobów
Subskrypcja zawiera jedną lub więcej grup zasobów. Grupy zasobów są używane do tworzenia grup logicznych zasobów platformy Azure, które są używane razem w ramach tego samego rozwiązania.  

Podczas tworzenia konta laboratorium należy skonfigurować grupę zasobów zawierającą konto laboratorium. 

Podczas tworzenia [udostępnionej galerii obrazów](#shared-image-gallery)wymagana jest również grupa zasobów. Możesz umieścić swoje konto laboratorium i galerię obrazów udostępnionych w dwóch oddzielnych grupach zasobów, co jest typowe, jeśli planujesz udostępnić galerię obrazów w różnych rozwiązaniach. Można też umieścić je w tej samej grupie zasobów.

Podczas tworzenia konta laboratoryjnego można automatycznie utworzyć i dołączyć galerię obrazów udostępnionych w tym samym czasie.  Ta opcja powoduje utworzenie konta laboratorium i galerii obrazów udostępnionych w oddzielnych grupach zasobów. Zobaczysz to zachowanie podczas korzystania z kroków opisanych w tym samouczku: [Skonfiguruj galerię obrazów udostępnionych w momencie tworzenia konta w laboratorium](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation). Obraz w górnej części tego artykułu również używa tej konfiguracji. 

Zalecamy inwestowanie czasu z góry, aby zaplanować strukturę grup zasobów, ponieważ *nie* jest możliwe zmienić grupę zasobów konta laboratorium lub udostępnionej galerii obrazów po jej utworzeniu. Jeśli chcesz zmienić grupę zasobów dla tych zasobów, musisz usunąć i ponownie utworzyć konto laboratorium i\lub galerię obrazów udostępnionych.

## <a name="lab-account"></a>Konto laboratorium
Konto laboratorium służy jako kontener dla jednego lub więcej laboratoriów w klasie. Po rozpoczęciu pracy z usługami Azure Lab Services często ma tylko jedno konto laboratoryjne. W miarę skalowania użycia w laboratorium można później utworzyć więcej kont laboratoryjnych.

Poniższa lista zawiera wyróżnienia scenariuszy, w których więcej niż jedno konto laboratorium może być korzystne:

- **Zarządzanie różnymi wymaganiami dotyczącymi zasad w laboratoriach szkolnych** 
    
    Podczas konfigurowania konta laboratorium można ustawić zasady, które mają zastosowanie do *wszystkich* laboratoriów w klasie na koncie laboratorium, takich jak:
    - Sieć wirtualna platformy Azure z udostępnionymi zasobami, do których może uzyskać dostęp laboratorium w klasie. Na przykład może mieć zestaw laboratoriów w klasie, które wymagają dostępu do udostępnionego zestawu danych w sieci wirtualnej.
    - Obrazy maszyny wirtualnej (VM), których laboratoria klasy może używać do tworzenia maszyn wirtualnych. Na przykład może mieć zestaw laboratoriów w klasie, które wymagają dostępu do [maszyny Wirtualnej nauki o danych dla systemu Linux](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu) Marketplace obrazu. 
    
    Jeśli masz laboratoria szkolne, które mają unikalne wymagania polityczne od siebie, może być korzystne utworzenie oddzielnych kont laboratoryjnych do zarządzania tymi laboratoriami w klasie oddzielnie.

- **Oddzielne konto budżetu według laboratorium**
  
    Zamiast zgłaszać wszystkie koszty laboratorium w klasie za pośrednictwem jednego konta laboratoryjnego, może być potrzebny bardziej wyraźnie rozdzielny budżet. Na przykład można utworzyć konta laboratoryjne dla wydziału matematyki uniwersytetu, wydziału informatyki itd., aby rozdzielić budżet między działami.  Następnie można wyświetlić koszt dla każdego konta laboratorium przy użyciu [usługi Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview).
    
- **Izolowanie laboratoriów pilotażowych z aktywnych\laboratoriów produkcyjnych**
  
    Mogą występować przypadki, w których chcesz pilotować zmiany zasad dla konta laboratorium bez potencjalnego wpływu na aktywne\produkcyjne laboratoria. W tym typie scenariusza utworzenie osobnego konta laboratorium do celów pilotażowych umożliwia izolowanie zmian. 

## <a name="classroom-lab"></a>Laboratorium w klasie
Laboratorium w klasie zawiera maszyny wirtualne (VM), które są przypisane do jednego ucznia. Ogólnie rzecz biorąc, można oczekiwać, że:

- Mieć jedno laboratorium w klasie dla każdej klasy.
- Stwórz nowy zestaw laboratoriów szkolnych w każdym semestrze (lub dla każdego przedziału czasowego, w których klasa jest oferowana). Zazwyczaj dla klas, które mają te same potrzeby obrazu, należy użyć [galerii obrazów udostępnionych](#shared-image-gallery) do ponownego użycia obrazów w laboratoriach i semestry.

Przy określaniu sposobu tworzenia laboratoriów w klasie należy wziąć pod uwagę następujące kwestie:

- **Wszystkie maszyny wirtualne w laboratorium w klasie są wdrażane z tym samym obrazem, który jest publikowany**

    W rezultacie, jeśli masz klasę, która wymaga różnych obrazów laboratoryjnych są publikowane w tym samym czasie, oddzielne laboratoria klasy muszą być tworzone dla każdego z nich.
  
- **Przydział użycia jest ustawiony na poziomie laboratorium i dotyczy wszystkich użytkowników w laboratorium**
    
    Aby ustawić różne przydziały dla użytkowników, należy utworzyć oddzielne laboratoria w klasie. Istnieje jednak możliwość dodania więcej godzin do określonego użytkownika po ustawieniu przydziału.
  
- **Harmonogram uruchamiania lub zamykania jest ustawiony na poziomie laboratorium i ma zastosowanie do wszystkich maszyn wirtualnych w laboratorium**

    Podobnie jak w poprzednim punkcie, jeśli chcesz ustawić różne harmonogramy dla użytkowników, musisz utworzyć oddzielne laboratoria w klasie. 

## <a name="shared-image-gallery"></a>Galeria zdjęć udostępnionych
Galeria obrazów udostępnionych jest dołączana do konta laboratorium i służy jako centralne repozytorium do przechowywania obrazów. Obraz jest zapisywany w galerii, gdy nauczyciel zdecyduje się na eksport z maszyny wirtualnej szablonu laboratorium klasy(VM). Za każdym razem, gdy program nauczyciel wprowadza zmiany w szablonie maszyny Wirtualnej i eksportuje, nowe wersje obrazu są zapisywane przy zachowaniu poprzednich wersji.

Instruktorzy mogą publikować wersję obrazu z udostępnionej galerii obrazów podczas tworzenia nowego laboratorium w klasie. Chociaż galeria może przechowywać wiele wersji obrazu, nauczyciele mogą wybrać najnowszą wersję tylko podczas tworzenia laboratorium.

Galeria obrazów udostępnionych to opcjonalny zasób, którego nie potrzebujesz od razu, gdy zaczynasz tylko kilka laboratoriów w klasie. Jednak korzystanie z udostępnionej galerii obrazów ma wiele zalet, które są pomocne podczas skalowania do większej liczby laboratoriów w klasie:

- **Umożliwia zapisywanie wersji szablonu obrazu maszyny Wirtualnej i zarządzanie nimi**

    Warto utworzyć obraz niestandardowy lub wprowadzić zmiany (oprogramowanie, konfiguracja itd.) do obrazu z publicznej galerii marketplace.  Na przykład często nauczyciele wymagają zainstalowania innego oprogramowania\narzędzi. Zamiast wymagać od uczniów ręcznego instalowania tych wymagań wstępnych samodzielnie, różne wersje szablonu obrazu maszyny Wirtualnej można wyeksportować do udostępnionej galerii obrazów. Te wersje obrazów mogą być następnie używane podczas tworzenia nowych laboratoriów szkolnych.
- **Umożliwia udostępnianie\ponownego użycia szablonów obrazów maszyn wirtualnych w laboratoriach w klasie**

    Obraz można zapisywać i ponownie używać, dzięki czemu nie trzeba go konfigurować od podstaw za każdym razem, gdy tworzysz nowe laboratorium w klasie. Jeśli na przykład oferowanych jest wiele klas, które wymagają tego samego obrazu, ten obraz musi zostać utworzony tylko raz i wyeksportowany do udostępnionej galerii obrazów, aby można było go udostępniać w laboratoriach szkolnych.
- **Zapewnia dostępność obrazu poprzez replikację**

    Po zapisaniu w udostępnionej galerii obrazów z laboratorium w klasie obraz jest automatycznie replikowany do innych [regionów w tej samej lokalizacji geograficznej.](https://azure.microsoft.com/global-infrastructure/regions/) W przypadku awarii dla regionu, publikowanie obrazu w laboratorium klasy nie ma wpływu, ponieważ można użyć repliki obrazu z innego regionu.  Publikowanie maszyn wirtualnych z wielu replik może również pomóc w wydajności.

Aby logicznie grupować udostępnione obrazy, masz kilka opcji:

- Utwórz wiele udostępnionych galerii obrazów. Każde konto laboratorium może łączyć się tylko z jedną galerią obrazów udostępnionych, więc ta opcja będzie również wymagać utworzenia wielu kont laboratoryjnych.
- Można też użyć jednej udostępnionej galerii obrazów współużytkowanej przez wiele kont laboratoryjnych. W takim przypadku każde konto laboratorium może włączyć tylko te obrazy, które mają zastosowanie do laboratoriów klasy, które zawiera.

## <a name="naming"></a>Nazewnictwo
Po rozpoczęciu pracy z usługami Azure Lab Services zaleca się ustanowienie konwencji nazewnictwa dla grup zasobów, kont laboratoryjnych, laboratoriów w klasie i galerii obrazów udostępnionych. Podczas gdy konwencje nazewnictwa, które ustanawiasz będzie unikatowa dla potrzeb organizacji, w poniższej tabeli przedstawiono ogólne wytyczne.

| Typ zasobu | Rola | Sugerowany wzorzec | Przykłady |
| ------------- | ---- | ----------------- | -------- | 
| Grupa zasobów | Zawiera co najmniej jedno konto w laboratorium i jedną lub więcej udostępnionych galerii obrazów | \<organizacja krótka nazwa\>-\<środowiska\>-rg<ul><li>**Krótka nazwa organizacji** identyfikuje nazwę organizacji, którą obsługuje grupa zasobów</li><li>**Środowisko** identyfikuje środowisko dla zasobu, takie jak pilotaż lub produkcja</li><li>**Rg** oznacza typ zasobu: grupa zasobów.</li></ul> | contosouniversitylabs-rg<br/>contosouniversitylabs-pilot-rg<br/>contosouniversitylabs-prod-rg |
| Konto laboratorium | Zawiera jedno lub więcej laboratoriów | \<organizacja krótka nazwa\>-\<środowiska\>-la<ul><li>**Krótka nazwa organizacji** identyfikuje nazwę organizacji, którą obsługuje grupa zasobów</li><li>**Środowisko** identyfikuje środowisko dla zasobu, takie jak pilotaż lub produkcja</li><li>**La** oznacza typ zasobu: konto laboratorium.</li></ul> | contosouniversitylabs-la<br/>mathdeptlabs-la<br/>sciencedeptlabs-pilot-la<br/>sciencedeptlabs-prod-la |
| Laboratorium w klasie | Zawiera jedną lub więcej maszyn wirtualnych |\<identyfikator\>-\<nauczyciela\>-\<w czasie nazwy klasy\><ul><li>**Nazwa klasy** identyfikuje nazwę klasy, która obsługuje laboratorium.</li><li>**Ramy czasowe** określa ramy czasowe, w których klasa jest oferowana.</li>**Identyfikator edukacyjny** identyfikuje nauczyciela, który jest właścicielem laboratorium.</li></ul> | CS1234-jesień2019-johndoe<br/>CS1234-spring2019-johndoe | 
| Galeria zdjęć udostępnionych | Zawiera jedną lub więcej wersji obrazu maszyny Wirtualnej | \<galeria krótkich nazw organizacji\> | galeria contosouniversitylabs |

Aby uzyskać więcej informacji na temat nazywania innych zasobów platformy Azure, zobacz [Konwencje nazewnictwa zasobów platformy Azure.](/azure/architecture/best-practices/naming-conventions)

## <a name="regionslocations"></a>Regiony\lokalizacje

Podczas konfigurowania zasobów usługi Azure Lab Services, musisz podać region (lub lokalizację) centrum danych, które będzie obsługiwać zasób. Oto więcej szczegółów na temat wpływu regionu na każdy z zasobów zaangażowanych w konfigurowanie laboratorium.

### <a name="resource-group"></a>Grupa zasobów

Region określa centrum danych, w którym są przechowywane informacje o grupie zasobów. Zasoby platformy Azure zawarte w grupie zasobów mogą znajdować się w różnych regionach niż ich element nadrzędny.

### <a name="lab-account"></a>Konto laboratorium

Lokalizacja konta laboratorium wskazuje region, w którym istnieje ten zasób.  

### <a name="classroom-lab"></a>Laboratorium w klasie
    
Lokalizacja laboratorium w klasie różni się w zależności od następujących czynników:

  - **Konto laboratorium jest równorzędne z siecią wirtualną**
  
    Konto laboratorium można [równorzędnie z sieci wirtualnej,](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network) gdy znajdują się one w tym samym regionie.  Gdy konto laboratorium jest równorzędne z siecią wirtualną, laboratoria w klasie są automatycznie tworzone w tym samym regionie, co konto laboratorium i sieć wirtualna.

    > [!NOTE]
    > Gdy konto laboratorium jest równorzędne z siecią wirtualną, ustawienie **Zezwalaj twórcy laboratorium na wybieranie lokalizacji laboratorium** jest wyłączone. Dodatkowe informacje na temat tego ustawienia można znaleźć w artykule: [Zezwalaj twórcy laboratorium na wybieranie lokalizacji laboratorium.](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location)
    
  - **Żadna sieć wirtualna nie jest równorzędna, ***a*** twórcy laboratorium nie mogą wybrać lokalizacji laboratorium**
  
    Jeśli **nie ma sieci** wirtualnej równorzędnej z kontem *laboratorium, a* [twórcy laboratorium **nie** mogą wybrać lokalizacji laboratorium,](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location)laboratoria klasy są automatycznie tworzone w regionie, który ma dostępną pojemność maszyny Wirtualnej.  W szczególności usługa Azure Lab Services wyszukuje dostępność w [regionach, które znajdują się w tej samej lokalizacji geograficznej co konto laboratorium.](https://azure.microsoft.com/global-infrastructure/regions)

  - **Sieć wirtualna nie jest równorzędna, ***a*** twórcy laboratorium mogą wybrać lokalizację laboratorium**
       
    Gdy **nie ma** sieci wirtualnej w równowadze i [twórcy laboratorium mogą wybrać lokalizację laboratorium,](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location)lokalizacje, które mogą być wybrane przez twórcę laboratorium są oparte na dostępnej pojemności.

Ogólną regułą jest ustawienie regionu zasobu na taki, który jest najbliżej jego użytkowników. W przypadku laboratoriów szkolnych oznacza to utworzenie laboratorium w klasie najbliżej uczniów. W przypadku kursów online, na których uczniowie znajdują się na całym świecie, musisz użyć najlepszego osądu, aby stworzyć laboratorium w klasie, które znajduje się centralnie. Możesz też podzielić klasę na wiele laboratoriów w klasie na podstawie regionu uczniów.

### <a name="shared-image-gallery"></a>Galeria zdjęć udostępnionych

Region wskazuje region źródłowy, w którym jest przechowywana pierwsza wersja obrazu, zanim zostanie automatycznie zreplikowana do regionów docelowych.

## <a name="vm-sizing"></a>Rozmiar maszyny Wirtualnej
Gdy administratorzy lub twórcy laboratorium tworzą laboratorium w klasie, mogą wybierać spośród następujących rozmiarów maszyn wirtualnych na podstawie potrzeb ich klasy. Pamiętaj, że dostępne rozmiary obliczeń zależą od regionu, w których znajduje się twoje konto laboratoryjne:

| Rozmiar | Specyfikacje | Seria | Sugerowane zastosowanie |
| ---- | ----- | ------ | ------------- |
| Small| <ul><li>2 rdzenie</li><li>3,5 GB pamięci RAM</li> | [Standardowa_A2_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Ten rozmiar najlepiej nadaje się do wiersza polecenia, otwierania przeglądarki internetowej, serwerów internetowych o małym natężeniu ruchu, małych i średnich baz danych. |
| Medium | <ul><li>4 rdzenie</li><li>7 GB pamięci RAM</li> | [Standardowa_A4_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Ten rozmiar najlepiej nadaje się do relacyjnych baz danych, buforowania w pamięci i analizy. |
| Średni (wirtualizacja zagnieżdżona) | <ul><li>4 rdzenie</li><li>16 GB pamięci RAM</li></ul> | [Standard_DC4s_v2](https://docs.microsoft.com/azure/virtual-machines/dcv2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Ten rozmiar najlepiej nadaje się do relacyjnych baz danych, buforowania w pamięci i analizy.  Ten rozmiar obsługuje również zagnieżdżoną wirtualizację. |
| Large | <ul><li>8 rdzeni</li><li>32 GB pamięci RAM</li></ul>  | [Standard_DC8_v2](https://docs.microsoft.com/azure/virtual-machines/dcv2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Ten rozmiar najlepiej nadaje się do aplikacji, które potrzebują szybszych procesorów, lepszej wydajności dysku lokalnego, dużych baz danych, dużych pamięci podręcznych.  Ten rozmiar obsługuje również zagnieżdżoną wirtualizację. |
| Mały procesor graficzny (wizualizacja) | <ul><li>6 rdzeni</li><li>56 GB pamięci RAM</li>  | [Standardowa_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | Ten rozmiar najlepiej nadaje się do zdalnej wizualizacji, przesyłania strumieniowego, gier, kodowania przy użyciu takich struktur jak OpenGL i DirectX. |
| Mały procesor graficzny (compute) | <ul><li>6 rdzeni</li><li>56 GB pamięci RAM</li></ul>  | [Standardowa_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |Ten rozmiar najlepiej nadaje się do zastosowań intensywnie korzystających z komputera, takich jak sztuczna inteligencja i uczenie głębokie. |
| Średni procesor graficzny (wizualizacja) | <ul><li>12 rdzeni</li><li>112 GB pamięci RAM</li></ul>  | [Standardowa_NC12](https://docs.microsoft.com/azure/virtual-machines/nc-series) | Ten rozmiar najlepiej nadaje się do zdalnej wizualizacji, przesyłania strumieniowego, gier, kodowania przy użyciu takich struktur jak OpenGL i DirectX. |

## <a name="manage-identity"></a>Zarządzanie tożsamością
Korzystając z [kontroli dostępu opartej na rolach platformy Azure,](https://docs.microsoft.com/azure/role-based-access-control/overview)można przypisać następujące role, aby zapewnić dostęp do kont laboratoryjnych i laboratoriów w klasie:

- **Właściciel konta laboratorium**

    Administrator, który tworzy konto laboratorium jest automatycznie dodawany do roli **właściciela** konta laboratorium.  Administrator, który ma przypisaną rolę **Właściciela,** może:
     - Zmień ustawienia konta laboratorium.
     - Zapewnij innym administratorom dostęp do konta laboratorium jako właściciele lub współautorzy. 
     - Zapewnij nauczycielom dostęp do laboratoriów w klasie jako twórcy, właściciele lub współautorzy.
     - Tworzenie i zarządzanie wszystkimi laboratoriami w klasie na koncie laboratorium.

- **Współautor konta laboratorium**

    Administrator, który ma przypisaną rolę **współautora** może:
    - Zmień ustawienia konta laboratorium.
    - Tworzenie i zarządzanie wszystkimi laboratoriami w klasie na koncie laboratorium.
    
    Nie *mogą* jednak dać innym użytkownikom dostępu do kont laboratoryjnych lub laboratoriów szkolnych.

- **Twórca laboratorium w klasie**

    Aby utworzyć laboratoria w klasie na koncie laboratorium, nauczyciel musi być członkiem roli **Twórca laboratorium.**  Gdy nauczyciel tworzy laboratorium w klasie, są one automatycznie dodawane jako właściciel laboratorium.  Zapoznaj się z samouczkiem na temat [dodawania użytkownika do roli **Twórca laboratorium** ](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role). 

- **Właściciel laboratorium classroom\współpracownik**
  
    Nauczyciel może wyświetlać i zmieniać ustawienia laboratorium w klasie, gdy jest członkiem roli **właściciela** lub **współautora** laboratorium; muszą być również członkiem roli **czytelnika** konta laboratorium.

    Kluczową różnicą między rolami **właściciela** i **współautora** w laboratorium jest to, że współautor *nie może udzielić* innym użytkownikom dostępu do zarządzania laboratorium — tylko właściciele mogą przyznać innym użytkownikom dostęp do zarządzania laboratorium.
    
    Ponadto nauczyciel *nie może* tworzyć nowych laboratoriów w klasie, chyba że jest również członkiem roli **Twórcy laboratorium.**

- **Galeria zdjęć udostępnionych**
    
    Po dołączeniu udostępnionej galerii obrazów do konta laboratorium właściciele kont laboratorium\współautorzy i twórcy laboratorium\właściciele\współautorzy automatycznie mają dostęp do wyświetlania i zapisywania obrazów w galerii. 

Oto kilka wskazówek, które pomogą ci w przypisywaniu ról:
   - Zazwyczaj tylko administratorzy powinni być członkami ról **właściciela** lub **współautora** konta laboratorium; możesz mieć więcej niż jednego właściciela\współautora.

   - Aby dać nauczycielowi możliwość tworzenia nowych laboratoriów w klasie i zarządzania laboratoriami, które tworzą; wystarczy przypisać dostęp do roli **Twórca laboratorium.**
   
   - Aby dać nauczycielowi możliwość zarządzania określonymi laboratoriami w klasie, ale *nie* możliwość tworzenia nowych laboratoriów; należy przypisać dostęp do roli **Właściciel** lub **Współautor** dla każdego z laboratoriów w klasie, którymi będą zarządzać.  Na przykład możesz zezwolić zarówno profesorowi, jak i asystentowi nauczyciela na współwłasne laboratorium w klasie.  Zapoznaj się z przewodnikiem, jak [dodać użytkownika jako właściciela do laboratorium w klasie](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-add-user-lab-owner).

## <a name="pricing"></a>Cennik

### <a name="azure-lab-services"></a>Azure Lab Services
Ceny usług Azure Lab Services opisano w następującym artykule: [Ceny usług Azure Lab Services.](https://azure.microsoft.com/pricing/details/lab-services/)

Należy również wziąć pod uwagę ceny galerii obrazów udostępnionych, jeśli planujesz używać jej do przechowywania i zarządzania wersjami obrazów. 

### <a name="shared-image-gallery"></a>Galeria zdjęć udostępnionych
Tworzenie udostępnionej galerii zdjęć i dołączanie jej do konta laboratoryjnego jest bezpłatne. Koszty nie są ponoszone, dopóki nie zapiszesz wersji obrazu w galerii. Zazwyczaj ceny za korzystanie z galerii obrazów udostępnionych jest dość nieistotne, ale ważne jest, aby zrozumieć, jak jest obliczana, ponieważ nie jest uwzględniona w cenach usług Azure Lab Services.  

#### <a name="storage-charges"></a>Opłaty za przechowywanie
Do przechowywania wersji obrazów galeria obrazów udostępnionych używa standardowych dysków zarządzanych przez dyski twarde. Rozmiar używanego dysku zarządzanego przez dysk twardy zależy od rozmiaru przechowywanej wersji obrazu. Zobacz następujący artykuł, aby wyświetlić ceny: [Ceny dysków zarządzanych](https://azure.microsoft.com/pricing/details/managed-disks/).


#### <a name="replication-and-network-egress-charges"></a>Opłaty za replikację i wyjście sieciowe
Po zapisaniu wersji obrazu przy użyciu maszyny wirtualnej szablonu laboratorium klasy (VM), usługi Azure Lab Services najpierw przechowuje ją w regionie źródłowym, a następnie automatycznie replikuje wersję obrazu źródłowego do jednego lub więcej regionów docelowych. Należy pamiętać, że usługa Azure Lab Services automatycznie replikuje wersję obrazu źródłowego do wszystkich regionów docelowych [w obrębie lokalizacji geograficznej,](https://azure.microsoft.com/global-infrastructure/regions/) w której znajduje się laboratorium w klasie. Jeśli na przykład laboratorium w klasie znajduje się w lokalizacji geograficznej STANÓW Zjednoczonych, wersja obrazu jest replikowana do każdego z ośmiu regionów istniejących w Stanach Zjednoczonych.

Opłata za ruch wychodzący sieci występuje, gdy wersja obrazu jest replikowana z regionu źródłowego do dodatkowych regionów docelowych. Pobrana kwota jest zależna od rozmiaru wersji obrazu, gdy dane obrazu są początkowo przesyłane wychodząco z regionu źródłowego.  Szczegółowe informacje o cenach można znaleźć w następującym artykule: [Szczegóły cen przepustowości](https://azure.microsoft.com/pricing/details/bandwidth/).

[Klienci](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) mogą zrezygnować z płacenia opłat za wyjście. Porozmawiaj z menedżerem konta, aby dowiedzieć się więcej.  Aby uzyskać więcej informacji, **zobacz sekcję często zadawanych pytań** w połączonym dokumencie, w szczególności pytanie "Jakie programy transferu danych istnieją dla klientów akademickich i jak się zakwalifikować?".

#### <a name="pricing-example"></a>Przykład cen
Aby podsumować ceny opisane powyżej, przyjrzyjmy się przykładowi zapisania obrazu maszyny Wirtualnej szablonu w galerii obrazów udostępnionych. Załóżmy, że następujące scenariusze:

- Masz jeden niestandardowy obraz maszyny Wirtualnej.
- Zapisujesz dwie wersje obrazu.
- Twoje laboratorium znajduje się w Usa, które ma w sumie osiem regionów.
- Każda wersja obrazu ma rozmiar 32 GB; w rezultacie cena dysku zarządzanego przez dysk hdd wynosi $ 1.54 miesięcznie.

Całkowity koszt szacuje się na:

Liczba obrazów × liczba wersji × liczba replik × cena dysku zarządzanego

W tym przykładzie koszt wynosi:

1 obraz niestandardowy (32 GB) x 2 wersje x 8 regionów USA x $1.54 = $24.64 miesięcznie

#### <a name="cost-management"></a>Zarządzanie kosztami
Administrator konta laboratorium musi zarządzać kosztami, regularnie usuwając niepotrzebne wersje obrazów z galerii. 

Nie należy usuwać replikacji do określonych regionów jako sposobu na obniżenie kosztów (ta opcja istnieje w galerii obrazów udostępnionych). Zmiany replikacji mogą mieć niekorzystny wpływ na możliwość publikowania maszyn wirtualnych w usłudze Azure Lab Service z obrazów zapisanych w galerii obrazów udostępnionych.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z samouczkiem, aby uzyskać instrukcje krok po kroku dotyczące tworzenia konta w laboratorium i laboratorium: [Konfigurowanie przewodnika](tutorial-setup-lab-account.md)

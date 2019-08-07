---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/06/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: e43b8c951d8c7d5d60904fe49d8639efaba5c89f
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775908"
---
Udostępniona Galeria obrazów to usługa, która ułatwia tworzenie struktury i organizacji na całym zarządzanym obrazie. Udostępnione Galerie obrazów zapewniają następujące:

- Zarządza replikacją globalną obrazów.
- Przechowywanie wersji i grupowanie obrazów w celu łatwiejszego zarządzania.
- Obrazy o wysokiej dostępności z kontami magazynu Strefowo nadmiarowego (ZRS) w regionach, które obsługują Strefy dostępności. Magazyn strefowo nadmiarowy oferuje lepszą odporność na awarie dotyczące jednej strefy.
- Udostępnianie między subskrypcjami, a nawet między dzierżawami Active Directory (AD) przy użyciu RBAC.
- Skalowanie wdrożeń przy użyciu replik obrazów w każdym regionie.

Za pomocą udostępnionej galerii obrazów możesz udostępnić swoje obrazy innym użytkownikom, podmiotom usług lub grupom usługi AD w organizacji. Obrazy udostępnione mogą być replikowane do wielu regionów, co umożliwia szybsze skalowanie wdrożeń.

Obraz zarządzany to kopia pełnej maszyny wirtualnej (w tym dowolnych dołączonych dysków danych) lub tylko dysku systemu operacyjnego, w zależności od sposobu tworzenia obrazu. Podczas tworzenia maszyny wirtualnej na podstawie obrazu kopia dysków VHD w obrazie jest używana do tworzenia dysków dla nowej maszyny wirtualnej. Zarządzany obraz pozostaje w magazynie i może być ponownie używany do tworzenia nowych maszyn wirtualnych.

Jeśli masz dużą liczbę zarządzanych obrazów, które chcesz zachować i chcesz udostępnić je w całej firmie, możesz użyć udostępnionej galerii obrazów jako repozytorium, która ułatwia udostępnianie obrazów. 

Funkcja galerii obrazów udostępnionych ma wiele typów zasobów:

| Resource | Opis|
|----------|------------|
| **Obraz zarządzany** | Podstawowy obraz, który może być używany samodzielnie lub do tworzenia **wersji obrazu** w galerii obrazów. Obrazy zarządzane są tworzone na podstawie uogólnionych maszyn wirtualnych. Obraz zarządzany jest specjalnym typem dysku VHD, który może służyć do tworzenia wielu maszyn wirtualnych i może być teraz używany do utworzenia wersji obrazu udostępnionego. |
| **Galeria obrazów** | Podobnie jak w przypadku portalu Azure Marketplace, **Galeria obrazów** jest repozytorium do zarządzania i udostępniania obrazów, ale ty kontrolujesz, kto ma dostęp. |
| **Definicja obrazu** | Obrazy są zdefiniowane w galerii i zawierają informacje o obrazie i wymaganiach dotyczących używania go w organizacji. Można dołączać informacje takie jak obraz systemu Windows lub Linux, minimalne i maksymalne wymagania dotyczące pamięci oraz informacje o wersji. Jest to definicja typu obrazu. |
| **Wersja obrazu** | **Wersja obrazu** jest używana do tworzenia maszyny wirtualnej w przypadku korzystania z galerii. Dla danego środowiska można mieć wiele wersji obrazu. Podobnie jak w przypadku obrazu zarządzanego, w przypadku tworzenia maszyny wirtualnej przy użyciu **wersji obrazu** wersja obrazu jest używana do tworzenia nowych dysków dla maszyny wirtualnej. Wersje obrazów można wielokrotnie używać. |

<br>


![Ilustracja przedstawiająca sposób, w jaki można mieć wiele wersji obrazu w galerii](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Definicje obrazów

Definicje obrazów są logiczną grupą dla wersji obrazu. Definicja obrazu zawiera informacje na temat przyczyny utworzenia obrazu, jego systemu operacyjnego oraz informacji o korzystaniu z obrazu. Definicja obrazu jest taka sama jak w przypadku planu dla wszystkich szczegółów dotyczących tworzenia określonego obrazu. Nie można wdrożyć maszyny wirtualnej na podstawie definicji obrazu, ale z wersji obrazu utworzonej na podstawie definicji.


Istnieją trzy parametry dla każdej definicji obrazu, które są używane w połączeniu **wydawcy**, **oferty** i **jednostki SKU**. Są one używane do znajdowania konkretnej definicji obrazu. Możesz mieć wersje obrazów, które współdzielą jeden lub dwa, ale nie wszystkie trzy wartości.  Na przykład poniżej przedstawiono trzy definicje obrazów i ich wartości:

|Definicja obrazu|Wydawca|Oferta|Numer SKU|
|---|---|---|---|
|myImage1|Contoso|Finanse|Zaplecze|
|myImage2|Contoso|Finanse|Fronton|
|myImage3|Testowanie|Finanse|Fronton|

Wszystkie trzy z nich mają unikatowe zestawy wartości. Ten format jest podobny do tego, jak obecnie można określić wydawcy, oferty i jednostki SKU dla [obrazów portalu Azure Marketplace](../articles/virtual-machines/windows/cli-ps-findimage.md) w Azure PowerShell, aby uzyskać najnowszą wersję obrazu portalu Marketplace. Każda definicja obrazu musi mieć unikatowy zestaw tych wartości.

Poniżej znajdują się inne parametry, które można ustawić w definicji obrazu, dzięki czemu można łatwiej śledzić zasoby:

* Stan systemu operacyjnego — można ustawić uogólniony lub wyspecjalizowany stan systemu operacyjnego, ale tylko uogólnione jest obecnie obsługiwane. Obrazy muszą być tworzone na podstawie maszyn wirtualnych, które zostały uogólnione przy użyciu programu `waagent -deprovision` Sysprep dla systemu Windows lub Linux.
* System operacyjny — może to być system Windows lub Linux.
* Opis — Użyj opisu, aby uzyskać bardziej szczegółowe informacje na temat tego, dlaczego istnieje definicja obrazu. Na przykład może istnieć definicja obrazu dla serwera frontonu, na którym jest wstępnie zainstalowana aplikacja.
* EULA — może służyć do wskazywania umowy licencyjnej użytkownika końcowego dotyczącej definicji obrazu.
* Zasady zachowania poufności informacji i informacje o wersji — przechowywanie informacji o wersji i zasad zachowania poufności informacji w usłudze Azure Storage oraz dostarczanie identyfikatora URI w celu uzyskania dostępu do nich w ramach definicji obrazu.
* Data zakończenia okresu użytkowania — Dołącz datę zakończenia okresu istnienia do definicji obrazu, aby można było użyć automatyzacji do usunięcia starych definicji obrazu.
* Tag — możesz dodać tagi podczas tworzenia definicji obrazu. Aby uzyskać więcej informacji na temat tagów, zobacz [Używanie tagów do organizowania zasobów](../articles/azure-resource-manager/resource-group-using-tags.md)
* Minimalne i maksymalne zalecenia dotyczące vCPU i pamięci — Jeśli obraz ma zalecenia vCPU i pamięci, możesz dołączyć te informacje do definicji obrazu.
* Niedozwolone typy dysków — można podać informacje o wymaganiach dotyczących magazynu dla maszyny wirtualnej. Na przykład jeśli obraz nie jest odpowiedni dla standardowych dysków DYSKowych, należy dodać je do listy nie Zezwalaj.


## <a name="regional-support"></a>Obsługa regionalna

Regiony źródłowe są wymienione w poniższej tabeli. Wszystkie regiony publiczne mogą być regionami docelowymi, ale w celu replikowania do Australii Środkowej i Australii środkowej 2 trzeba mieć subskrypcję listy dozwolonych. Aby zażądać listy dozwolonych, przejdź do: https://azure.microsoft.com/en-au/global-infrastructure/australia/contact/


| Regiony źródłowe |
|---------------------|-----------------|------------------|-----------------|
| Australia Środkowa   | Central US EUAP | Korea Środkowa    | Środkowo-zachodnie stany USA |
| Australia Środkowa 2 | Azja Wschodnia       | Korea Południowa      | Europa Zachodnia     |
| Australia Wschodnia      | East US         | Środkowo-północne stany USA | Indie Zachodnie      |
| Australia Południowo-Wschodnia | Wschodnie stany USA 2       | Europa Północna     | Zachodnie stany USA         |
| Brazylia Południowa        | East US 2 EUAP  | Środkowo-południowe stany USA | Zachodnie stany USA 2       |
| Kanada Środkowa      | Francja Środkowa  | Indie Południowe      |                 |
| Kanada Wschodnia         | Francja Południowa    | Azja Południowo-Wschodnia   |                 |
| Indie Środkowe       | Japonia Wschodnia      | Południowe Zjednoczone Królestwo         |                 |
| Środkowe stany USA          | Japonia Zachodnia      | Zachodnie Zjednoczone Królestwo          |                 |



## <a name="limits"></a>Limity 

Istnieją limity dla każdej subskrypcji dotyczące wdrażania zasobów przy użyciu udostępnionych galerii obrazów:
- 100 udostępnione Galerie obrazów na subskrypcję na region
- 1 000 definicji obrazów na subskrypcję na region
- 10 000 wersje obrazów na subskrypcję na region

Aby uzyskać więcej informacji, zobacz [Sprawdzanie użycia zasobów względem limitów](https://docs.microsoft.com/azure/networking/check-usage-against-limits) na potrzeby przykładów sprawdzania bieżącego użycia.
 

## <a name="scaling"></a>Skalowanie
Udostępniona Galeria obrazów pozwala określić liczbę replik, które mają być przechowywane na platformie Azure. Pomaga to w scenariuszach wdrażania wielu maszyn wirtualnych, ponieważ wdrożenia maszyn wirtualnych można rozmieścić w różnych replikach, co zmniejsza prawdopodobieństwo ograniczenia przetwarzania wystąpienia z powodu przeciążenia pojedynczej repliki.


Korzystając z galerii obrazów udostępnionych, można teraz wdrożyć do 1 000 wystąpień maszyn wirtualnych w zestawie skalowania maszyn wirtualnych (do maksymalnie 600 z obrazami zarządzanymi). Repliki obrazów zapewniają lepszą wydajność, niezawodność i spójność wdrożenia.  Można ustawić inną liczbę replik w każdym regionie docelowym na podstawie potrzeb skali dla regionu. Ponieważ każda replika to głęboka kopia obrazu, to ułatwia skalowanie wdrożeń liniowo z każdą dodatkową repliką. Wiemy, że nie ma żadnych dwóch obrazów ani regionów, poniżej przedstawiono ogólne wytyczne dotyczące korzystania z replik w regionie:

- Dla każdego 20 maszyn wirtualnych, które tworzysz współbieżnie, zalecamy zachowanie jednej repliki. Na przykład jeśli tworzysz maszyny wirtualne 120 na bieżąco przy użyciu tego samego obrazu w regionie, sugerujemy zachowywanie co najmniej 6 replik obrazu. 
- W przypadku każdego wdrożenia zestawu skalowania z maksymalnie 600 wystąpieniami zalecamy zachowanie co najmniej jednej repliki. Na przykład, jeśli tworzysz 5 zestawów skalowania współbieżnie, z których każda korzysta z 600 wystąpień maszyn wirtualnych przy użyciu tego samego obrazu w jednym regionie, sugerujemy zachowanie co najmniej 5 replik obrazu. 

Zawsze zalecamy przeprowadzenie aprowizacji liczby replik ze względu na takie czynniki jak rozmiar obrazu, zawartość i typ systemu operacyjnego.


![Ilustracja przedstawiająca sposób skalowania obrazów](./media/shared-image-galleries/scaling.png)



## <a name="make-your-images-highly-available"></a>Zapewnianie wysokiej dostępności obrazów

[Magazyn strefowo nadmiarowy (ZRS) platformy Azure](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/) zapewnia odporność na awarię strefy dostępności w regionie. Korzystając z ogólnej dostępności galerii obrazów udostępnionych, można wybrać przechowywanie obrazów na kontach ZRS w regionach, w których Strefy dostępności. 

Możesz również wybrać typ konta dla każdego regionu docelowego. Domyślnym typem konta magazynu jest Standard_LRS, ale można wybrać Standard_ZRS dla regionów z Strefy dostępności. Sprawdź regionalną dostępność ZRS [tutaj](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs).

![Ilustracja przedstawiająca ZRS](./media/shared-image-galleries/zrs.png)


## <a name="replication"></a>Replikacja
Udostępniona Galeria obrazów umożliwia również automatyczne replikowanie obrazów do innych regionów platformy Azure. Każda udostępniona wersja obrazu może być replikowana do różnych regionów, w zależności od tego, co jest sensowne dla Twojej organizacji. Przykładem jest zawsze replikowanie najnowszego obrazu w wielu regionach, a wszystkie starsze wersje są dostępne tylko w 1 regionie. Może to pomóc w zapisaniu kosztów magazynu dla udostępnionych wersji obrazu. 

Regiony, w których jest replikowana wersja udostępnionego obrazu, mogą zostać zaktualizowane po upływie czasu utworzenia. Czas potrzebny na replikację do różnych regionów zależy od ilości kopiowanych danych i liczby regionów, do których jest replikowana wersja. W niektórych przypadkach może to potrwać kilka godzin. Podczas replikacji można wyświetlić stan replikacji na region. Po zakończeniu replikacji obrazu w regionie można wdrożyć maszynę wirtualną lub zestaw skalowania przy użyciu tej wersji obrazu w regionie.

![Ilustracja przedstawiająca sposób replikowania obrazów](./media/shared-image-galleries/replication.png)


## <a name="access"></a>Access

Jako Galeria obrazów udostępnionych, definicja obrazu i wersja obrazu są wszystkie zasoby, które można udostępniać przy użyciu wbudowanych kontrolek kontroli RBAC platformy Azure. Za pomocą RBAC można udostępniać te zasoby innym użytkownikom, podmiotom usługi i grupom. Możesz nawet udostępnić dostęp osobom spoza dzierżawy, w ramach której zostały utworzone. Gdy użytkownik ma dostęp do udostępnionej wersji obrazu, może wdrożyć maszynę wirtualną lub zestaw skalowania maszyn wirtualnych.  Oto macierz udostępniania, która pomaga zrozumieć, do czego użytkownik uzyskuje dostęp:

| Udostępnione użytkownikowi     | Galeria obrazów udostępnionych | Definicja obrazu | Wersja graficzna |
|----------------------|----------------------|--------------|----------------------|
| Galeria obrazów udostępnionych | Tak                  | Yes          | Tak                  |
| Definicja obrazu     | Nie                   | Yes          | Yes                  |

Zalecamy udostępnianie na poziomie galerii w celu uzyskania najlepszego środowiska. Nie zaleca się udostępniania poszczególnych wersji obrazu. Aby uzyskać więcej informacji na temat RBAC, zobacz [Zarządzanie dostępem do zasobów platformy Azure przy użyciu RBAC](../articles/role-based-access-control/role-assignments-portal.md).

Obrazy mogą być również udostępniane na dużą skalę, nawet między dzierżawcami przy użyciu rejestracji aplikacji wielodostępnej. Aby uzyskać więcej informacji o udostępnianiu obrazów między dzierżawcami, zobacz [udostępnianie obrazów maszyn wirtualnych z galerii w ramach dzierżawców platformy Azure](../articles/virtual-machines/linux/share-images-across-tenants.md).

## <a name="billing"></a>Rozliczenia
korzystanie z usługi galerii obrazów udostępnionych nie wiąże się z żadnymi dodatkowymi opłatami. Zostanie naliczona opłata za następujące zasoby:
- Koszty magazynowania przechowywania wersji obrazu udostępnionego. Koszt zależy od liczby replik wersji obrazu oraz liczby regionów, do których jest replikowana wersja. Na przykład jeśli masz 2 obrazy i obie są replikowane do 3 regionów, zostanie zmieniony dla 6 dysków zarządzanych na podstawie ich rozmiaru. Aby uzyskać więcej informacji, zobacz [Cennik usługi Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/).
- Opłaty za ruch wychodzący z sieci są naliczane za replikację pierwszej wersji obrazu z regionu źródłowego do replikowanych regionów. Kolejne repliki są obsługiwane w regionie, więc nie są naliczane żadne dodatkowe opłaty. 

## <a name="updating-resources"></a>Aktualizowanie zasobów

Po utworzeniu można wprowadzić pewne zmiany zasobów galerii obrazów. Są one ograniczone do:
 
Galeria obrazów udostępnionych:
- Opis

Definicja obrazu:
- Zalecane procesorów wirtualnych vCPU
- Zalecana pamięć
- Opis
- Data zakończenia okresu istnienia

Wersja obrazu:
- Liczba replik regionalnych
- Regiony docelowe
- Wyklucz z najnowszych
- Data zakończenia okresu istnienia


## <a name="sdk-support"></a>Obsługa zestawu SDK

Następujące zestawy SDK obsługują Tworzenie udostępnionych galerii obrazów:

- [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Node.js](https://docs.microsoft.com/javascript/api/azure-arm-compute/?view=azure-node-latest)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [Go](https://docs.microsoft.com/go/azure/)

## <a name="templates"></a>Szablony

Możesz utworzyć zasób udostępnionej galerii obrazów przy użyciu szablonów. Dostępnych jest kilka szablonów szybkiego startu platformy Azure: 

- [Tworzenie galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Tworzenie definicji obrazu w galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Tworzenie wersji obrazu w galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Tworzenie maszyny wirtualnej z wersji obrazu](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>Często zadawane pytania 

**PYTANIE** Jak można wyświetlić listę wszystkich udostępnionych zasobów galerii obrazów w ramach subskrypcji? 
 
 A. Aby wyświetlić listę wszystkich udostępnionych zasobów galerii obrazów między subskrypcjami, do których masz dostęp na Azure Portal, wykonaj następujące czynności:

1. Otwórz [portal Azure](https://portal.azure.com).
1. Przejdź do pozycji **wszystkie zasoby**.
1. Wybierz wszystkie subskrypcje, w ramach których chcesz wyświetlić listę wszystkich zasobów.
1. Wyszukaj zasoby typu **Galeria prywatna**.
 
   Aby wyświetlić definicje obrazu i wersje obrazu, należy również wybrać **Pokaż ukryte typy**.
 
   Aby wyświetlić listę wszystkich udostępnionych zasobów galerii obrazów między subskrypcjami, do których masz uprawnienia, użyj następującego polecenia w interfejsie wiersza polecenia platformy Azure:

   ```bash
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
   ```


**PYTANIE** Czy mogę przenieść istniejący obraz do galerii obrazów udostępnionych?
 
 A. Tak. Istnieją 3 scenariusze na podstawie typów obrazów, które mogą mieć.

 Scenariusz 1: Jeśli masz obraz zarządzany, możesz utworzyć definicję obrazu i wersję obrazu.

 Scenariusz 2: Jeśli masz niezarządzany obraz uogólniony, możesz utworzyć z niego obraz zarządzany, a następnie utworzyć definicję obrazu i wersję obrazu. 

 Scenariusz 3: Jeśli masz dysk VHD w lokalnym systemie plików, musisz przekazać dysk VHD, utworzyć obraz zarządzany, a następnie utworzyć definicję obrazu i wersję obrazu oraz z niego.
- Jeśli wirtualny dysk twardy jest maszyną wirtualną z systemem Windows, zobacz [przekazywanie uogólnionego wirtualnego dysku twardego](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed).
- Jeśli wirtualny dysk twardy jest przeznaczony dla maszyny wirtualnej z systemem Linux, zobacz [przekazywanie wirtualnego dysku twardego](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)


**PYTANIE** Czy mogę utworzyć wersję obrazu z wyspecjalizowanego dysku?

 A. Nie, obecnie nie obsługujemy wyspecjalizowanych dysków jako obrazów. Jeśli masz wyspecjalizowany dysk, musisz [utworzyć maszynę wirtualną na podstawie wirtualnego dysku twardego](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal#create-a-vm-from-a-disk) , dołączając wyspecjalizowany dysk do nowej maszyny wirtualnej. Gdy masz działającą maszynę wirtualną, musisz postępować zgodnie z instrukcjami, aby utworzyć obraz zarządzany na podstawie [maszyny wirtualnej](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-custom-images) lub [maszyny wirtualnej](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images)z systemem Linux. Gdy masz uogólniony zarządzany obraz, możesz rozpocząć proces, aby utworzyć udostępniony obraz opisu i wersję obrazu.

 
**PYTANIE** Czy po utworzeniu można przenieść zasób udostępnionej galerii obrazów do innej subskrypcji?

 A. Nie, nie można przenieść zasobu udostępnionej galerii obrazów do innej subskrypcji. Jednak będzie można replikować wersje obrazu w galerii do innych regionów zgodnie z wymaganiami.

**PYTANIE** Czy można replikować moje wersje obrazów między chmurami — Chiny firmy 21Vianet, platforma Azure (Niemcy) i usługa Azure Government Cloud? 

 A. Nie, nie można replikować wersji obrazu w chmurach.

**PYTANIE** Czy można replikować wersje obrazów między subskrypcjami? 

 A. Nie, możesz replikować wersje obrazów między regionami w ramach subskrypcji i używać ich w innych subskrypcjach za pośrednictwem RBAC.

**PYTANIE** Czy mogę udostępniać wersje obrazów w dzierżawach usługi Azure AD? 

 A. Tak, możesz użyć RBAC, aby udostępnić osobom w dzierżawach. Jednak aby udostępnić je na dużą skalę, zobacz "udostępnianie obrazów galerii w ramach dzierżawców platformy Azure" przy użyciu [programu PowerShell](../articles/virtual-machines/windows/share-images-across-tenants.md) lub [interfejsu wiersza polecenia](../articles/virtual-machines/linux/share-images-across-tenants.md).


**PYTANIE** Jak długo trwa replikowanie wersji obrazów w regionach docelowych?

 A. Czas replikacji wersji obrazu jest w całości zależny od rozmiaru obrazu i liczby regionów, do których jest replikowana. Jednak najlepszym rozwiązaniem jest zapewnienie małego obrazu, a regiony źródłowe i docelowe zamykają się w celu uzyskania najlepszych wyników. Stan replikacji można sprawdzić przy użyciu flagi-ReplicationStatus.


**PYTANIE** Jaka jest różnica między regionem źródłowym a regionem docelowym?

 A. Region źródłowy to region, w którym zostanie utworzona wersja obrazu, a regiony docelowe to regiony, w których będzie przechowywana kopia wersji obrazu. Dla każdej wersji obrazu można mieć tylko jeden region źródłowy. Upewnij się również, że lokalizacja regionu źródłowego została przekazana jako jeden z regionów docelowych podczas tworzenia wersji obrazu.  


**PYTANIE** Jak mogę określić regionu źródłowego podczas tworzenia wersji obrazu?

 A. Podczas tworzenia wersji obrazu można użyć znacznika **--Location** w interfejsie wiersza polecenia i tagu **lokalizacji** w programie PowerShell, aby określić region źródłowy. Upewnij się, że zarządzany obraz, którego używasz jako obrazu podstawowego, aby utworzyć wersję obrazu, znajduje się w tej samej lokalizacji co lokalizacja, w której ma zostać utworzona wersja obrazu. Upewnij się również, że lokalizacja regionu źródłowego została przekazana jako jeden z regionów docelowych podczas tworzenia wersji obrazu.  


**PYTANIE** Jak mogę określić liczbę replik wersji obrazu, które mają zostać utworzone w każdym regionie?

 A. Istnieją dwa sposoby, w których można określić liczbę replik wersji obrazu, które mają zostać utworzone w każdym regionie:
 
1. Liczba replik regionalnych, która określa liczbę replik, które chcesz utworzyć na region. 
2. Typowa liczba replik, która jest wartością domyślną dla poszczególnych regionów w przypadku, gdy nie określono liczby replik regionalnych. 

Aby określić liczbę replik regionalnych, Przekaż lokalizację wraz z liczbą replik, które chcesz utworzyć w tym regionie: "Południowo-środkowe stany USA = 2". 

Jeśli liczba replik regionalnych nie jest określona dla każdej lokalizacji, domyślną liczbą replik będzie określona liczba znanych replik. 

Aby określić wspólną liczbę replik w interfejsie wiersza `az sig image-version create` polecenia, użyj argumentu **--Replica-Count** w poleceniu.


**PYTANIE** Czy można utworzyć galerię obrazów udostępnionych w innej lokalizacji niż ta, w której chcę utworzyć definicję obrazu i wersję obrazu?

 A. Tak, jest to możliwe. Jednak najlepszym rozwiązaniem jest utrzymywanie w tej samej lokalizacji grupy zasobów, galerii obrazów udostępnionych, definicji obrazu i wersji obrazu.


**PYTANIE** Jakie są opłaty za korzystanie z galerii obrazów udostępnionych?

 A. Nie są naliczane opłaty za korzystanie z usługi Shared Image Gallery, z wyjątkiem opłat za magazyn do przechowywania wersji obrazów i opłat za wychodzące sieci na potrzeby replikowania wersji obrazu z regionu źródłowego do regionów docelowych.

**PYTANIE** Jakiej wersji interfejsu API należy użyć do tworzenia galerii obrazów udostępnionych, definicji obrazu, wersji obrazu i maszyny wirtualnej/VMSS z wersji obrazu?

 A. W przypadku wdrożeń maszyny wirtualnej i zestawu skalowania maszyn wirtualnych przy użyciu wersji obrazu zalecamy użycie interfejsu API w wersji 2018-04-01 lub nowszej. Aby współpracować z udostępnionymi galeriami obrazów, definicjami obrazów i wersjami obrazów, zalecamy korzystanie z interfejsu API w wersji 2018-06-01. Magazyn strefowo nadmiarowy (ZRS) wymaga wersji 2019-03-01 lub nowszej.

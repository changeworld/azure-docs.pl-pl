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
ms.openlocfilehash: 0fe1de9bb674c66d1b665de25ee579bc86e42c75
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65192387"
---
Galeria obrazów udostępnionych to usługa, która pomaga w tworzeniu struktury i organizacji wokół niestandardowych obrazów maszyn wirtualnych zarządzanych. Podaj udostępnionego galerie obrazów:

- Zarządzane globalnej replikacji obrazów.
- Przechowywanie wersji i grupowania obrazów w celu ułatwienia zarządzania.
- Obrazy usługi wysokiej dostępności przy użyciu kont magazyn Strefowo nadmiarowy (ZRS) w regionach, które obsługują strefy dostępności. Magazyn ZRS zapewnia lepszą odporność na awarie strefowych.
- Udostępnianie w subskrypcjach, a nawet między dzierżawami, korzystając z modelu RBAC.

Przy użyciu galerii obrazów współdzielona możesz udostępnić swoje obrazy do różnych użytkowników, nazw głównych usług lub grup usługi Active Directory w Twojej organizacji. Udostępnianych obrazów mogą być replikowane w wielu regionach, szybsze skalowanie wdrożeń.

Zarządzany obraz jest kopią albo pełnej maszyny wirtualnej (w tym wszelkich dołączonych dysków danych) lub po prostu dysku systemu operacyjnego, w zależności od tego, jak utworzyć obraz. Po utworzeniu maszyny Wirtualnej z obrazu kopiowania dysków VHD na ilustracji są używane do tworzenia dysków dla nowej maszyny Wirtualnej. Zarządzany obraz pozostaje w magazynie i można wielokrotnie tworzenie nowych maszyn wirtualnych.

Jeśli masz dużą liczbę zarządzanych obrazów, które trzeba utrzymywać i chcesz udostępnić je w całej firmie, można użyć galerii obrazów udostępnione jako repozytorium, które można łatwo udostępniać swoje obrazy. 

Funkcja galerii obrazów współdzielona ma wiele typów zasobów:

| Resource | Opis|
|----------|------------|
| **Zarządzany obraz** | Obraz podstawowy, który mogą być używane autonomicznie lub użyty do utworzenia **wersję obrazu** w galerii obrazów. Obrazy zarządzane są tworzone na podstawie uogólnionego maszyn wirtualnych. Zarządzany obraz to specjalny typ wirtualnego dysku twardego, który można utworzyć wiele maszyn wirtualnych i może teraz służyć do tworzenia wersji udostępnionego obrazu. |
| **Galeria obrazów** | W portalu Azure Marketplace, takich jak **galerii obrazów** to repozytorium do zarządzania i udostępniania obrazów, ale możesz kontrolować, kto ma dostęp. |
| **Definicję obrazu** | Obrazy w galerii są zdefiniowane i zawierają informacje dotyczące obrazu i wymagania dotyczące korzystania z niego w Twojej organizacji. Możesz uwzględnić informacje, takie jak tego, czy obraz jest Windows lub Linux, wymagania dotyczące minimalnej i maksymalnej pamięci i informacje o wersji. Jest definicja typu obrazu. |
| **Wersja obrazu** | **Wersję obrazu** , które jest używane do utworzenia maszyny Wirtualnej, podczas korzystania z galerii. Może mieć wiele wersji obrazu, zgodnie z potrzebami w danym środowisku. Gdy używasz, takie jak zarządzany obraz **wersję obrazu** tworzenie maszyny Wirtualnej, wersja obrazu jest używany do tworzenia nowych dysków dla maszyny Wirtualnej. Wersje obrazów można wielokrotnie. |

<br>


![Grafika przedstawiająca sposób może mieć wiele wersji obraz w galerii](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Definicje obrazów

Definicje obrazu to logiczne grupowanie wersji obrazu. Definicję obrazu przechowuje dowiedzieć się, dlaczego obraz został utworzony, jakie jest on przeznaczony dla systemu operacyjnego i informacji o korzystaniu z obrazu. Definicję obrazu przypomina plan wszystkie szczegółowe informacje dotyczące tworzenia określonego obrazu. Nie możesz wdrożyć Maszynę wirtualną z definicji interfejsu obrazu, ale z wersję obrazu utworzonego na podstawie definicji.


Istnieją trzy parametry definicję każdego obrazu, które są używane w połączeniu - **wydawcy**, **oferują** i **jednostki SKU**. Są one używane, aby znaleźć definicję określonego obrazu. Może mieć wersji obrazu, które współużytkują jeden lub dwa, ale nie wszystkie trzy wartości.  Na przykład poniżej przedstawiono trzy definicje, które obrazu i ich wartości:

|Definicja obrazu|Wydawca|Oferta|SKU|
|---|---|---|---|
|myImage1|Contoso|Finanse|Zaplecze|
|myImage2|Contoso|Finanse|Fronton|
|myImage3|Testowanie|Finanse|Fronton|

Wszystkie trzy z nich ma unikatowe zbiory wartości. Format jest podobny do sposobu możesz obecnie określić wydawcy, oferta i jednostka SKU dla [obrazów portalu Azure Marketplace](../articles/virtual-machines/windows/cli-ps-findimage.md) w programie Azure PowerShell, aby uzyskać najnowszą wersję obrazu z witryny Marketplace. Każda definicja obrazu musi mieć unikatowe zbiory tych wartości.

Poniżej przedstawiono inne parametry, które mogą być ustawione na danej definicji obrazu, dzięki czemu można łatwiej śledzić zasobów:

* Stan systemu operacyjnego — mogą ustawić stan systemu operacyjnego na, uogólnione lub wyspecjalizowany, ale tylko uogólniony jest obecnie obsługiwane. Obrazy muszą być tworzone z maszyn wirtualnych, które została uogólniona za pomocą programu Sysprep dla Windows lub `waagent -deprovision` dla systemu Linux.
* System operacyjny — może być Windows lub Linux.
* Opis — opisu użycia, aby zapewnić bardziej szczegółowych informacji na temat Dlaczego istnieje definicję obrazu. Na przykład Niewykluczone, że definicję obrazu na serwerze frontonu, na którym wcześniej zainstalowano aplikację.
* Umowa licencyjna EULA — może służyć do wskaż Umowa licencyjna użytkownika oprogramowania określonych w definicji obrazu.
* Zasady zachowania poufności informacji i wersji uwagi — przechowywanie wersji i zasady zachowania poufności informacji w usłudze Azure storage i podaj identyfikator URI do uzyskiwania dostępu do ich jako część definicji obrazu.
* Wycofanych z eksploatacji daty — dołączyć datę zakończenia eksploatacji definicję obrazu, aby można było usunąć stare definicje obrazu przy użyciu usługi automation.
* Tag — można dodać tagów podczas tworzenia definicji obrazu. Aby uzyskać więcej informacji na temat tagów, zobacz [organizowanie zasobów za pomocą tagów](../articles/azure-resource-manager/resource-group-using-tags.md)
* Minimalna i maksymalna procesorów vCPU i zalecenia pamięci — Jeśli obraz zawiera zalecenia dotyczące pamięci i procesorów wirtualnych, można dołączyć te informacje do swojej definicji obrazu.
* Niedozwolone typy dysków — może dostarczyć informacji na temat na potrzeby magazynu dla maszyny Wirtualnej. Na przykład jeśli obraz nie jest odpowiednie dla standardowych dysków HDD, dodasz je do listy nie Zezwalaj.


## <a name="regional-support"></a>Wsparcie regionalne

W poniższej tabeli wymieniono regionów źródłowych. We wszystkich regionach publicznych może być regiony docelowe, ale można replikować do Australia Środkowa i Australia Środkowa 2 musisz mieć Twojej subskrypcji, na liście dozwolonych. Aby zażądać umieszczania na białej liście, przejdź do: https://www.microsoft.com/en-au/central-regions-eligibility/


| Regionów źródłowych |
|---------------------|-----------------|------------------|-----------------|
| Australia Środkowa   | Central US EUAP | Korea Środkowa    | Południowe Zjednoczone Królestwo 2      |
| Australia Środkowa 2 | Azja Wschodnia       | Korea Południowa      | Zachodnie Zjednoczone Królestwo         |
| Australia Wschodnia      | Wschodnie stany USA         | Środkowo-północne stany USA | Środkowo-zachodnie stany USA |
| Australia Południowo-Wschodnia | Wschodnie stany USA 2       | Europa Północna     | Europa Zachodnia     |
| Brazylia Południowa        | East US 2 EUAP  | Środkowo-południowe stany USA | Indie Zachodnie      |
| Kanada Środkowa      | Francja Środkowa  | Indie Południowe      | Zachodnie stany USA         |
| Kanada Wschodnia         | Francja Południowa    | Azja Południowo-Wschodnia   | Zachodnie stany USA         |
| Indie Środkowe       | Japonia Wschodnia      | Północne Zjednoczone Królestwo         | Zachodnie stany USA 2       |
| Środkowe stany USA          | Japonia Zachodnia      | Południowe Zjednoczone Królestwo         |                 |



## <a name="limits"></a>Limits 

Istnieją limity subskrypcji, przez wdrażanie zasobów przy użyciu współdzielonego, galerie obrazów:
- 100 galerie udostępnionego obrazu na subskrypcję na region
- 1000 obrazów definicje na subskrypcję na region
- 10 000 wersje obrazów na subskrypcję na region

Aby uzyskać więcej informacji, zobacz [Sprawdź użycie zasobów limitów](https://docs.microsoft.com/azure/networking/check-usage-against-limits) przykładów na temat sprawdzić bieżące użycie.
 

## <a name="scaling"></a>Skalowanie
Galeria obrazów udostępnionych pozwala określić liczbę replik mają platformy Azure, aby przechowywać obrazy. Dzięki temu w scenariuszach wdrażania wielu maszyn wirtualnych, zgodnie z wdrożenia maszyn wirtualnych może zostać rozłożona do różnych replik, zmniejszając prawdopodobieństwo utworzenie wystąpienia przetwarzania jest ograniczona z powodu przeciążenia dla pojedynczej repliki.

![Grafika przedstawiająca sposób mogą skalować obrazów](./media/shared-image-galleries/scaling.png)


## <a name="replication"></a>Replikacja
Galeria obrazów udostępnionych umożliwia również obrazów do innych regionów platformy Azure są automatycznie replikowane. Każda wersja obrazu udostępnione mogą być replikowane do innych regionów, w zależności od tego, co ma sens dla swojej organizacji. Przykładem jest zawsze replikowanie najnowszego obrazu w wielu regionach, gdy wszystkie starsze wersje są dostępne tylko w regionie 1. Pozwoli to zapisać kosztów magazynowania wersje udostępnionych obrazów. 

Regiony, które wersji udostępnionych obraz jest replikowany do może zostać zaktualizowana po godzinie utworzenia. Czas potrzebny do replikowania do różnych regionów zależy od tego, ilości danych, w której są kopiowane i liczbie regionów, wersja jest replikowana do. W niektórych przypadkach może to potrwać kilka godzin. Podczas replikacji jest wykonywane, możesz wyświetlić stan replikacji na region. Po zakończeniu replikacji obrazu w regionie, następnie można wdrożyć maszyny Wirtualnej lub w zestawie skalowania, korzystając z wersji obrazu w regionie.

![Grafika pokazująca, jak można replikować obrazów](./media/shared-image-galleries/replication.png)


## <a name="access"></a>Dostęp

Galeria obrazów udostępnione, udostępnionego obrazu i wersję udostępnionego obrazu są wszystkie zasoby, mogą być współużytkowane przy użyciu wbudowanych, które kontroluje macierzystych RBAC platformy Azure. Przy użyciu funkcji RBAC można udostępniać te zasoby do innych użytkowników, nazw głównych usług i grup. Możesz nawet udostępniać dostęp osobom spoza dzierżawy, którym zostały utworzone w ciągu. Po użytkownik ma dostęp do wersji obrazu udostępnione, można wdrożyć na maszynie Wirtualnej lub zestawu skalowania maszyn wirtualnych.  Oto macierzy do udostępniania, która pomaga zrozumieć, jakie użytkownik uzyskuje dostęp do:

| Udostępnione użytkownikowi     | Galeria obrazów udostępnionych | Udostępnionego obrazu | Udostępniona wersja obrazu |
|----------------------|----------------------|--------------|----------------------|
| Galeria obrazów udostępnionych | Yes                  | Yes          | Yes                  |
| Udostępnionego obrazu         | Nie                   | Yes          | Yes                  |
| Udostępniona wersja obrazu | Nie                   | Nie           | Yes                  |

Zaleca się udostępniania na poziomie galerii, aby uzyskać najlepsze wyniki. Aby uzyskać więcej informacji o ROLACH, zobacz [zarządzanie dostępem do zasobów platformy Azure przy użyciu funkcji RBAC](../articles/role-based-access-control/role-assignments-portal.md).

Obrazy mogą również być współużytkowane, na dużą skalę, przez dzierżaw przy użyciu rejestracji aplikacji wielodostępnej. Aby uzyskać więcej informacji na temat udostępniania obrazów dla dzierżaw, zobacz [udostępnianie galerii obrazów maszyn wirtualnych w dzierżawach usługi Azure](../articles/virtual-machines/linux/share-images-across-tenants.md).

## <a name="billing"></a>Rozliczenia
Brak bez dodatkowych opłat za korzystanie z usługi Shared galerii obrazów. Opłata wyniesie dla następujących zasobów:
- Koszty magazynowania, przechowywania wersji udostępnionego obrazu. Koszt zależy od liczby replik wersję obrazu i liczbie regionów, wersja jest replikowana do. Na przykład jeśli oba są replikowane do 3 regiony mają 2 obrazów, następnie użytkownik zmieni się za 6 dyski zarządzane na podstawie jego rozmiaru. Aby uzyskać więcej informacji, zobacz [cennika usługi Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).
- Opłaty za ruch wychodzący sieci dla replikacji pierwszej wersji obrazu z regionu źródłowego w replikowanych regionach. Kolejne repliki są obsługiwane w regionie, dzięki czemu nie będą naliczane dodatkowe opłaty. 

## <a name="updating-resources"></a>Aktualizowanie zasobów

Po utworzeniu możesz wprowadzić pewne zmiany, aby zasoby galerii obrazów. Są one ograniczone do:
 
Galeria obrazów udostępnionych:
- Opis

definicję obrazu:
- Zalecane procesorów wirtualnych Vcpu
- Zalecana ilość pamięci
- Opis
- Data

Wersja obrazu:
- Liczba replik regionalne
- Regiony docelowe
- Wykluczenie z najnowsze
- Data


## <a name="sdk-support"></a>Obsługa zestawu SDK

Następujące zestawy SDK obsługują tworzenie udostępnione, galerie obrazów:

- [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Node.js](https://docs.microsoft.com/javascript/api/azure-arm-compute/?view=azure-node-latest)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [Go](https://docs.microsoft.com/go/azure/)

## <a name="templates"></a>Szablony

Można utworzyć zasobu galerii obrazów udostępnione za pomocą szablonów. Dostępnych jest kilka szablonów szybkiego startu platformy Azure: 

- [Tworzenie udostępnionego galerii obrazów](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Utwórz definicję obrazu w udostępnionym galerii obrazów](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Utwórz wersję obrazu w udostępnionym galerii obrazów](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Utwórz Maszynę wirtualną z obrazu wersji](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>Często zadawane pytania 

**PYTANIE** Jak listy wszystkie zasoby udostępnione Galeria obrazów w subskrypcjach 
 
 A. Aby wyświetlić listę wszystkich zasobów galerii obrazów współużytkowane między subskrypcjami, że masz dostęp do witryny Azure portal, wykonaj następujące czynności:

1. Otwórz [portal Azure](https://portal.azure.com).
1. Przejdź do **wszystkie zasoby**.
1. Wybierz wszystkie subskrypcje, w których chcesz wyświetlić listę wszystkich zasobów.
1. Wyszukaj zasoby typu **prywatną galerię**.
 
   Aby wyświetlić obraz definicje i wersje obrazów, należy również wybrać **Pokaż ukryte typy**.
 
   Aby wyświetlić listę wszystkich zasobów w galerii obrazów udostępnione w subskrypcjach, które mają uprawnienia do, użyj następującego polecenia w interfejsie wiersza polecenia platformy Azure:

   ```bash
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
   ```


**PYTANIE** Czy mogę przenieść Mój istniejący obraz do galerii obrazów udostępnionych?
 
 A. Tak. Istnieją 3 scenariusze, w oparciu o typy obrazów, które mogą wiązać Ciebie.

 Scenariusz 1: Jeśli masz obrazu zarządzanego, następnie można utworzyć definicję obrazu i wersję obrazu z niego.

 Scenariusz 2: W przypadku niezarządzanych uogólnionego obrazu utworzenie obrazu zarządzanego z niego, a następnie utworzyć definicję obrazu i wersję obrazu z niego. 

 Scenariusz 3: W przypadku wirtualnego dysku twardego w lokalnym systemie plików, musisz przekazać wirtualny dysk twardy, utworzenie obrazu zarządzanego, a następnie można tworzyć i obrazów, definicji i wersję obrazu z niego.
- Jeśli wirtualny dysk twardy maszyny Wirtualnej z systemem Windows, zobacz [przekazywanie uogólnionego wirtualnego dysku twardego](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed).
- Jeśli wirtualny dysk twardy dla maszyny Wirtualnej z systemem Linux, zobacz [przekazania dysku VHD](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)


**PYTANIE** Czy mogę utworzyć wersję obrazu z wyspecjalizowanego dysku

 A. Nie, obecnie obsługujemy specjalnych dysków jako obrazy. Jeśli masz wyspecjalizowanego dysku, musisz [Utwórz Maszynę wirtualną z wirtualnego dysku twardego](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal#create-a-vm-from-a-disk) , dołączając wyspecjalizowanego dysku do nowej maszyny Wirtualnej. Po utworzeniu uruchomionej maszyny Wirtualnej, należy wykonać instrukcje dotyczące tworzenia obrazu zarządzanego z [Windows VM](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-custom-images) lub [maszyny Wirtualnej systemu Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). Po utworzeniu obrazu zarządzanego uogólnionej, można uruchomić procesu tworzenia udostępnionego obrazu, opis i wersję obrazu.

 
**PYTANIE** Po utworzeniu można przenieść zasobu galerii obrazów udostępnione do innej subskrypcji?

 A. Nie, nie można przenieść zasobu galerii udostępnionego obrazu do innej subskrypcji. Jednak można replikować wersje obrazów w galerii do innych regionów, zgodnie z potrzebami.

**PYTANIE** Czy można replikować mojej wersji obrazu dla chmur — Azure China 21Vianet, Azure (Niemcy) i w chmurze Azure Government? 

 A. Nie, nie można replikować wersje obrazów w chmurach.

**PYTANIE** Czy można replikować mojej wersji obrazu, między subskrypcjami? 

 A. Nie może replikować wersje obrazów między regionami w ramach subskrypcji i używać go w innych subskrypcji przy użyciu RBAC.

**PYTANIE** Czy mogę udostępniać wersje obrazów dla dzierżaw usługi Azure AD? 

 A. Tak, można użyć funkcji RBAC udostępnianie osobom w dzierżawach. Ale udostępnianie na dużą skalę, zobacz "udział w galerii obrazów dla dzierżaw usługi Azure" przy użyciu [PowerShell](../articles/virtual-machines/windows/share-images-across-tenants.md) lub [interfejsu wiersza polecenia](../articles/virtual-machines/linux/share-images-across-tenants.md).


**PYTANIE** Jak długo trwa replikowanie wersje obrazów na regiony docelowe?

 A. Czas replikacji wersji obrazu jest całkowicie zależna od rozmiaru obrazu i liczbie regionów, które są replikowane do. Jednak najlepszym rozwiązaniem jest zalecane obraz zachowywanie małych, i zamknij regiony źródłowe i docelowe, aby uzyskać najlepsze wyniki. Można sprawdzić stan replikacji za pomocą flagi ma — wartość.


**PYTANIE** Jaka jest różnica między regionu źródłowego i docelowego regionu?

 A. Region źródłowy jest region, w którym używana wersja obrazu zostanie utworzony, a regiony docelowe są regionów, w których będą przechowywane kopię wersji obrazu. Dla każdej wersji obrazu może mieć tylko jeden region źródła. Ponadto upewnij się, Przekaż lokalizacji region źródła jako jeden z regionów docelowych, podczas tworzenia wersji obrazu.  


**PYTANIE** Jak określić region źródła podczas tworzenia wersji obrazu?

 A. Podczas tworzenia wersji obrazu, możesz użyć **— lokalizacja** tagu w interfejsie wiersza polecenia i **-lokalizacji** tagu w programie PowerShell, aby określić region źródła. Upewnij się, że obrazu zarządzanego, którego używasz jako obraz podstawowy utworzyć wersję obrazu znajduje się w tej samej lokalizacji co lokalizacja, w której chcesz utworzyć wersję obrazu. Ponadto upewnij się, Przekaż lokalizacji region źródła jako jeden z regionów docelowych, podczas tworzenia wersji obrazu.  


**PYTANIE** Jak określić liczbę replik wersji obrazu do utworzenia w każdym regionie?

 A. Istnieją dwa sposoby, można określić liczbę replik wersji obrazu do utworzenia w każdym regionie:
 
1. Liczba replik regionalnych, który określa liczbę replik ma zostać utworzony na region. 
2. Typowe liczba replik, co jest ustawieniem domyślnym, na liczba regionów, w przypadku, gdy nie jest określona liczba replik regionalne. 

Aby określić liczbę replik regionalnych, należy przekazać lokalizacji wraz z liczbą repliki, który chcesz utworzyć w tym regionie: "Południowo-środkowe stany USA = 2". 

Jeśli liczba replik regionalnych nie zostanie określony z każdej lokalizacji, domyślna liczba replik będzie wspólnej liczba replik, który określiłeś. 

Aby określić typowe liczba replik w interfejsie wiersza polecenia, użyj **— liczba replik** argument `az sig image-version create` polecenia.


**PYTANIE** Galeria obrazów udostępnionych można utworzyć w innej lokalizacji niż ta, gdzie chcesz utworzyć definicję obrazu i wersję obrazu?

 A. Tak, jest to możliwe. Jednak najlepszym rozwiązaniem jest firma Microsoft zachęca do zachowania w tej samej lokalizacji grupy zasobów, Galeria obrazów udostępnionych, definicję obrazu i wersję obrazu.


**PYTANIE** Jakie są opłaty za korzystanie z galerii obrazów współdzielona?

 A. Nie istnieją żadne opłaty za korzystanie z usługi Shared galerii obrazów, z wyjątkiem opłat za magazyn do przechowywania wersji obrazu i opłaty za ruch wychodzący w sieci na potrzeby replikacji wersji obrazu z regionu źródłowego do regionów docelowych.

**PYTANIE** Jakiej wersji interfejsu API używać do tworzenia galerii obrazów udostępnione, definicję obrazu, wersja obrazu i VM/VMSS poza wersję obrazu

 A. W przypadku wdrożeń maszyn wirtualnych i zestawu skalowania maszyn wirtualnych przy użyciu wersji obrazu, zalecane jest użycie interfejsu API w wersji 2018-04-01 lub nowszej. Chcesz pracować, galerie obrazów udostępnionych, definicje obrazu i wersje obrazów, zaleca się, że używasz wersji 2018-06-01 interfejsu API. 

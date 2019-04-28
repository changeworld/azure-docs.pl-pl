---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 01/09/2018
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 8c7da8d04b456642b158dda77d9c745891aa18e6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60620399"
---
Galeria obrazów udostępnionych to usługa, która pomaga w tworzeniu struktury i organizacji wokół niestandardowych obrazów maszyn wirtualnych zarządzanych. Przy użyciu galerii obrazów współdzielona możesz udostępnić swoje obrazy do różnych użytkowników, nazw głównych usług lub grup usługi Active Directory w Twojej organizacji. Udostępnianych obrazów mogą być replikowane w wielu regionach, szybsze skalowanie wdrożeń.

Zarządzany obraz jest kopią albo pełnej maszyny wirtualnej (w tym wszelkich dołączonych dysków danych) lub po prostu dysku systemu operacyjnego, w zależności od tego, jak utworzyć obraz. Po utworzeniu maszyny Wirtualnej z obrazu kopiowania dysków VHD na ilustracji są używane do tworzenia dysków dla nowej maszyny Wirtualnej. Zarządzany obraz pozostaje w magazynie i można wielokrotnie tworzenie nowych maszyn wirtualnych.

Jeśli masz dużą liczbę zarządzanych obrazów, które trzeba utrzymywać i chcesz udostępnić je w całej firmie, można użyć galerii obrazów udostępnione jako repozytorium, które można łatwo zaktualizować i udostępniać swoje obrazy. Opłaty za korzystanie z galerii obrazów współużytkowane są tylko kosztów miejsca używanego przez obrazów oraz wszelkich kosztów sieciowego ruchu wychodzącego do replikowania obrazów z regionu źródłowego do określonych regionów opublikowane.

Funkcja galerii obrazów współdzielona ma wiele typów zasobów:

| Resource | Opis|
|----------|------------|
| **Zarządzany obraz** | Jest to podstawowy obraz, które mogą być używane autonomicznie lub użyty do utworzenia **wersję obrazu** w galerii obrazów. Obrazy zarządzane są tworzone na podstawie uogólnionego maszyn wirtualnych. Zarządzany obraz to specjalny typ wirtualnego dysku twardego, który można utworzyć wiele maszyn wirtualnych i może teraz służyć do tworzenia wersji udostępnionego obrazu. |
| **Galeria obrazów** | W portalu Azure Marketplace, takich jak **galerii obrazów** to repozytorium do zarządzania i udostępniania obrazów, ale możesz kontrolować, kto ma dostęp. |
| **Definicję obrazu** | Obrazy w galerii są zdefiniowane i zawierają informacje dotyczące obrazu i wymagania dotyczące korzystania z niego wewnętrznie. Dotyczy to również, czy obraz jest Windows lub Linux, informacje o wersji i wymagań dotyczących minimalnej i maksymalnej pamięci. Jest definicja typu obrazu. |
| **Wersja obrazu** | **Wersję obrazu** , które jest używane do utworzenia maszyny Wirtualnej, podczas korzystania z galerii. Może mieć wiele wersji obrazu, zgodnie z potrzebami w danym środowisku. Gdy używasz, takie jak zarządzany obraz **wersję obrazu** tworzenie maszyny Wirtualnej, wersja obrazu jest używany do tworzenia nowych dysków dla maszyny Wirtualnej. Wersje obrazów można wielokrotnie. |

<br>


![Grafika przedstawiająca sposób może mieć wiele wersji obraz w galerii](./media/shared-image-galleries/shared-image-gallery.png)

### <a name="regional-support"></a>Wsparcie regionalne

Wsparcie regionalne dla galerii udostępnionego obrazu jest w ograniczonej wersji zapoznawczej, ale zostanie rozwinięte wraz z upływem czasu. Dla ograniczonej wersji zapoznawczej poniżej przedstawiono listę regionów, w której utworzono galerie i listę regionów, w którym można replikować obrazu z galerii: 

| Utwórz galerię w  | Replikowanie wersji |
|--------------------|----------------------|
| Środkowo-zachodnie stany USA    |We wszystkich regionach publicznych&#42;|
| Wschodnie stany USA 2          ||
| Środkowo-południowe stany USA   ||
| Azja Południowo-Wschodnia     ||
| Europa Zachodnia        ||
| Zachodnie stany USA            ||
| Wschodnie stany USA            ||
| Kanada Środkowa     ||
|                    ||



&#42;Można replikować do Australia Środkowa i Australia Środkowa 2, musisz mieć Twojej subskrypcji, na liście dozwolonych. Aby zażądać umieszczania na białej liście, przejdź do: https://www.microsoft.com/en-au/central-regions-eligibility/

## <a name="scaling"></a>Skalowanie
Galeria obrazów udostępnionych pozwala określić liczbę replik mają platformy Azure, aby przechowywać obrazy. Dzięki temu w scenariuszach wdrażania wielu maszyn wirtualnych, zgodnie z wdrożenia maszyn wirtualnych może zostać rozłożona do różnych replik, zmniejszając prawdopodobieństwo utworzenie wystąpienia przetwarzania jest ograniczona z powodu przeciążenia dla pojedynczej repliki.

![Grafika przedstawiająca sposób mogą skalować obrazów](./media/shared-image-galleries/scaling.png)


## <a name="replication"></a>Replikacja
Galeria obrazów udostępnionych umożliwia również obrazów do innych regionów platformy Azure są automatycznie replikowane. Każda wersja obrazu udostępnione mogą być replikowane do innych regionów, w zależności od tego, co ma sens dla swojej organizacji. Przykładem jest zawsze replikowanie najnowszego obrazu w wielu regionach, gdy wszystkie starsze wersje są dostępne tylko w regionie 1. Pozwoli to zapisać kosztów magazynowania wersje udostępnionych obrazów. 

Regiony, które wersji udostępnionych obraz jest replikowany do może zostać zaktualizowana po godzinie utworzenia. Czas potrzebny do replikowania do różnych regionów zależy od tego, ilości danych, w której są kopiowane i liczbie regionów, wersja jest replikowana do. W niektórych przypadkach może to potrwać kilka godzin. Podczas replikacji jest wykonywane, możesz wyświetlić stan replikacji na region. Po zakończeniu replikacji obrazu w regionie, następnie można wdrożyć maszyny Wirtualnej lub zestawu skalowania maszyn wirtualnych, korzystając z wersji obrazu w regionie.

![Grafika pokazująca, jak można replikować obrazów](./media/shared-image-galleries/replication.png)


## <a name="access"></a>Dostęp
Galeria obrazów udostępnione, udostępnionego obrazu i wersję udostępnionego obrazu są wszystkie zasoby, mogą być współużytkowane przy użyciu wbudowanych, które kontroluje macierzystych RBAC platformy Azure. Przy użyciu funkcji RBAC można udostępniać te zasoby do innych użytkowników, nazw głównych usług i grup w Twojej organizacji. Zakres do udostępniania tych zasobów jest w tej samej dzierżawie usługi Azure AD. Po użytkownik ma dostęp do wersji obrazu udostępnione, można wdrożyć na maszynie Wirtualnej lub zestawu skalowania maszyn wirtualnych w dowolnej subskrypcji, do których mają dostęp w tej samej usłudze Azure AD dzierżawy jako wersję udostępnionego obrazu.  Oto macierzy do udostępniania, która pomaga zrozumieć, jakie użytkownik uzyskuje dostęp do:

| Udostępnione użytkownikowi     | Galeria obrazów udostępnionych | Udostępnionego obrazu | Udostępniona wersja obrazu |
|----------------------|----------------------|--------------|----------------------|
| Galeria obrazów udostępnionych | Yes                  | Yes          | Yes                  |
| Udostępnionego obrazu         | Nie                   | Yes          | Yes                  |
| Udostępniona wersja obrazu | Nie                   | Nie           | Yes                  |



## <a name="billing"></a>Rozliczenia
Brak bez dodatkowych opłat za korzystanie z usługi Shared galerii obrazów. Opłata wyniesie dla następujących zasobów:
- Koszty magazynowania, przechowywania wersji udostępnionego obrazu. To zależy od liczby replik wersji i liczbie regionów, wersja są replikowane do.
- Opłaty za ruch wychodzący sieci replikacji w regionie źródłowym wersji do replikowanych regionach.

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

**PYTANIE** Jak utworzyć konto usługi udostępnione obraz galerii publicznej wersji zapoznawczej?
 
 A. Aby zarejestrować się w galerii obrazów współdzielona publicznej wersji zapoznawczej, należy zarejestrować dla funkcji, uruchamiając następujące polecenia z każdej subskrypcji, w których mają zostać utworzone w galerii obrazów udostępnione, definicję obrazu lub zasobów wersji obrazu, a również gdy zamierzasz wdrożyć maszyn wirtualnych przy użyciu wersji obrazu.

**INTERFEJS WIERSZA POLECENIA**: 

```bash 
az feature register --namespace Microsoft.Compute --name GalleryPreview
az provider register --name Microsoft.Compute
```

**PowerShell**: 

```powershell
Register-AzProviderFeature -FeatureName GalleryPreview -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

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


**PYTANIE** Jak udostępnić Moje obrazy między subskrypcjami?
 
 A. Obrazy można udostępniać między subskrypcjami przy użyciu kontroli dostępu na podstawie ról (RBAC). Każdy użytkownik, który ma uprawnienia odczytu do wersji obrazu, nawet w różnych subskrypcjach, będzie można wdrożyć maszynę wirtualną przy użyciu wersji obrazu.


**PYTANIE** Czy mogę przenieść Mój istniejący obraz do galerii obrazów udostępnionych?
 
 A. Tak. Istnieją 3 scenariusze, w oparciu o typy obrazów, które mogą wiązać Ciebie.

 Scenariusz 1: Jeśli masz obrazu zarządzanego, następnie można utworzyć definicję obrazu i wersję obrazu z niego.

 Scenariusz 2: W przypadku niezarządzanych uogólnionego obrazu utworzenie obrazu zarządzanego z niego, a następnie utworzyć definicję obrazu i wersję obrazu z niego. 

 Scenariusz 3: W przypadku wirtualnego dysku twardego w lokalnym systemie plików, musisz przekazać wirtualny dysk twardy, utworzenie obrazu zarządzanego, a następnie można tworzyć i obrazów, definicji i wersję obrazu z niego.
- Jeśli wirtualny dysk twardy maszyny Wirtualnej z systemem Windows, zobacz [przekazywanie uogólnionego wirtualnego dysku twardego](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed).
- Jeśli wirtualny dysk twardy dla maszyny Wirtualnej z systemem Linux, zobacz [przekazania dysku VHD](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)


**PYTANIE** Czy mogę utworzyć wersję obrazu z wyspecjalizowanego dysku

 A. Nie, obecnie obsługujemy specjalnych dysków jako obrazy. Jeśli masz wyspecjalizowanego dysku, musisz [Utwórz Maszynę wirtualną z wirtualnego dysku twardego](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal#create-a-vm-from-a-disk) , dołączając wyspecjalizowanego dysku do nowej maszyny Wirtualnej. Po utworzeniu uruchomionej maszyny Wirtualnej, należy wykonać instrukcje dotyczące tworzenia obrazu zarządzanego z [Windows VM](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-custom-images) lub [maszyny Wirtualnej systemu Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). Po utworzeniu obrazu zarządzanego uogólnionej, można uruchomić procesu tworzenia udostępnionego obrazu, opis i wersję obrazu.


**PYTANIE** Można utworzyć galerii obrazów udostępnionych, definicję obrazu i wersję obrazu za pomocą witryny Azure portal?

 A. Nie, obecnie nie obsługujemy tworzenie żadne zasoby z galerii obrazów udostępnione za pośrednictwem witryny Azure portal. Jednak firma Microsoft obsługuje tworzenie zasobów galerii obrazów udostępnione za pośrednictwem interfejsu wiersza polecenia, szablonów i zestawy SDK. Również programu PowerShell zostanie wkrótce wydana.

 
**PYTANIE** Po utworzeniu można aktualizować definicję obrazu lub wersję obrazu? Jakiego rodzaju szczegółowe informacje można zmodyfikować?

 A. Szczegółowe informacje, które mogą być aktualizowane na każdym z zasobów są wymienione poniżej:
 
Galeria obrazów udostępnionych:
- Opis

definicję obrazu:
- Zalecane procesorów wirtualnych Vcpu
- Memory (Pamięć)
- Opis
- Data

Wersja obrazu:
- Liczba replik regionalne
- Regiony docelowe
- Wykluczenie z najnowsze
- Data


**PYTANIE** Po utworzeniu można przenieść zasobu galerii obrazów udostępnione do innej subskrypcji?

 A. Nie, nie można przenieść zasobu galerii udostępnionego obrazu do innej subskrypcji. Jednak można replikować wersje obrazów w galerii do innych regionów, zgodnie z potrzebami.

**PYTANIE** Czy można replikować mojej wersji obrazu dla chmur — Azure China 21Vianet, Azure (Niemcy) i w chmurze Azure Government? 

 A. Nie, nie można replikować wersje obrazów w chmurach.

**PYTANIE** Czy można replikować mojej wersji obrazu, między subskrypcjami? 

 A. Nie może replikować wersje obrazów między regionami w ramach subskrypcji i używać go w innych subskrypcji przy użyciu RBAC.

**PYTANIE** Czy mogę udostępniać wersje obrazów dla dzierżaw usługi Azure AD? 

 A. Nie, Galeria obrazów obecnie udostępnionego nie obsługuje udostępniania wersji obrazu dzierżaw usługi Azure AD. Jednak mogą użyć funkcji oferty prywatne w portalu Azure Marketplace, aby to osiągnąć.


**PYTANIE** Jak długo trwa replikowanie wersje obrazów na regiony docelowe?

 A. Czas replikacji wersji obrazu jest całkowicie zależna od rozmiaru obrazu i liczbie regionów, które są replikowane do. Jednak najlepszym rozwiązaniem jest zalecane obraz zachowywanie małych, i zamknij regiony źródłowe i docelowe, aby uzyskać najlepsze wyniki. Można sprawdzić stan replikacji za pomocą flagi ma — wartość.


**PYTANIE** Ile galerie obrazów udostępnionych można utworzyć w ramach subskrypcji?

 A. Domyślny limit przydziału jest: 
- 10 galerie udostępnionego obrazu na subskrypcję na region
- 200 definicje obrazu, na subskrypcję na region
- wersje obrazów 2000, na subskrypcję na region


**PYTANIE** Jaka jest różnica między regionu źródłowego i docelowego regionu?

 A. Region źródłowy jest region, w którym używana wersja obrazu zostanie utworzony, a regiony docelowe są regionów, w których będą przechowywane kopię wersji obrazu. Dla każdej wersji obrazu może mieć tylko jeden region źródła. Ponadto upewnij się, Przekaż lokalizacji region źródła jako jeden z regionów docelowych, podczas tworzenia wersji obrazu.  


**PYTANIE** Jak określić region źródła podczas tworzenia wersji obrazu?

 A. Podczas tworzenia wersji obrazu, możesz użyć **— lokalizacja** tagu w interfejsie wiersza polecenia i **-lokalizacji** tagu w programie PowerShell, aby określić region źródła. Upewnij się, że obrazu zarządzanego, którego używasz jako obraz podstawowy utworzyć wersję obrazu znajduje się w tej samej lokalizacji co lokalizacja, w której chcesz utworzyć wersję obrazu. Ponadto upewnij się, Przekaż lokalizacji region źródła jako jeden z regionów docelowych, podczas tworzenia wersji obrazu.  


**PYTANIE** Jak określić liczbę replik wersji obrazu do utworzenia w każdym regionie?

 A. Istnieją dwa sposoby, można określić liczbę replik wersji obrazu do utworzenia w każdym regionie:
 
1. Liczba replik regionalnych, który określa liczbę replik ma zostać utworzony na region. 
2. Typowe liczba replik, co jest ustawieniem domyślnym, na liczba regionów, w przypadku, gdy nie jest określona liczba replik regionalne. 

Aby określić liczbę replik regionalnej, należy przekazać lokalizacji wraz z liczbą repliki, który chcesz utworzyć w danym regionie, w następujący sposób: "Południowo-środkowe stany USA = 2". 

Jeśli liczba replik regionalnych nie zostanie określony z każdej lokalizacji, domyślna liczba replik będzie wspólnej liczba replik, który określiłeś. 

Aby określić typowe liczba replik w interfejsie wiersza polecenia, użyj **— liczba replik** argument `az sig image-version create` polecenia.


**PYTANIE** Galeria obrazów udostępnionych można utworzyć w innej lokalizacji niż ta, gdzie chcesz utworzyć definicję obrazu i wersję obrazu?

 A. Tak, jest to możliwe. Jednak najlepszym rozwiązaniem jest firma Microsoft zachęca do zachowania w tej samej lokalizacji grupy zasobów, Galeria obrazów udostępnionych, definicję obrazu i wersję obrazu.


**PYTANIE** Jakie są opłaty za korzystanie z galerii obrazów współdzielona?

 A. Nie istnieją żadne opłaty za korzystanie z usługi Shared galerii obrazów, z wyjątkiem opłat za magazyn do przechowywania wersji obrazu i opłaty za ruch wychodzący w sieci na potrzeby replikacji wersji obrazu z regionu źródłowego do regionów docelowych.

**PYTANIE** Jakiej wersji interfejsu API używać do tworzenia galerii obrazów udostępnione, definicję obrazu, wersja obrazu i VM/VMSS poza wersję obrazu

 A. W przypadku wdrożeń maszyn wirtualnych i zestawu skalowania maszyn wirtualnych przy użyciu wersji obrazu, zalecane jest użycie interfejsu API w wersji 2018-04-01 lub nowszej. Chcesz pracować, galerie obrazów udostępnionych, definicje obrazu i wersje obrazów, zaleca się, że używasz wersji 2018-06-01 interfejsu API. 

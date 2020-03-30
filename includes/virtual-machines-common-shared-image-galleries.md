---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/06/2019
ms.author: akjosh
ms.custom: include file
ms.openlocfilehash: a477114bda7d138a6860d21f2fad75e27d968833
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80117000"
---
Shared Image Gallery to usługa, która pomaga budować strukturę i organizację wokół zarządzanych obrazów. Udostępnione galerie obrazów zapewniają:

- Zarządzana globalna replikacja obrazów.
- Przechowywanie wersji i grupowanie obrazów w celu łatwiejszego zarządzania.
- Obrazy o wysokiej dostępności z kontami Stref nadmiarowych magazynów (ZRS) w regionach obsługujących strefy dostępności. ZRS oferuje lepszą odporność na awarie strefowe.
- Udostępnianie między subskrypcjami, a nawet między dzierżawami usługi Active Directory (AD) przy użyciu funkcji RBAC.
- Skalowanie wdrożeń za pomocą replik obrazu w każdym regionie.

Za pomocą udostępnionej galerii obrazów można udostępniać obrazy różnym użytkownikom, jednostkom usług lub grupom usług AD w organizacji. Udostępnione obrazy mogą być replikowane do wielu regionów, aby przyspieszyć skalowanie wdrożeń.

Obraz zarządzany jest kopią pełnej maszyny Wirtualnej (w tym wszystkich dołączonych dysków z danymi) lub tylko dysku systemu operacyjnego, w zależności od sposobu tworzenia obrazu. Podczas tworzenia maszyny Wirtualnej z obrazu, kopia VHDs na obrazie są używane do tworzenia dysków dla nowej maszyny Wirtualnej. Obraz zarządzany pozostaje w magazynie i może być używany w kółko do tworzenia nowych maszyn wirtualnych.

Jeśli masz dużą liczbę obrazów zarządzanych, które chcesz zachować i chcesz je udostępnić w całej firmie, możesz użyć galerii obrazów udostępnionych jako repozytorium, które ułatwia udostępnianie obrazów. 

Funkcja Shared Image Gallery ma wiele typów zasobów:

| Zasób | Opis|
|----------|------------|
| **Obraz zarządzany** | Podstawowy obraz, który może być używany samodzielnie lub używany do tworzenia **wersji obrazu** w galerii obrazów. Obrazy zarządzane są tworzone na [uogólnionych](#generalized-and-specialized-images) maszynach wirtualnych. Obraz zarządzany jest specjalnym typem dysku VHD, który może służyć do tworzenia wielu maszyn wirtualnych i może być teraz używany do tworzenia wersji obrazów udostępnionych. |
| **Migawka** | Kopia dysku VHD, który może być użyty do tworzenia **wersji obrazu**. Migawki mogą być pobierane ze [specjalistycznej](#generalized-and-specialized-images) maszyny Wirtualnej (która nie została uogólniona), a następnie używane samodzielnie lub z migawkami dysków z danymi, aby utworzyć wyspecjalizowaną wersję obrazu.
| **Galeria zdjęć** | Podobnie jak w portalu Azure **Marketplace, galeria obrazów** jest repozytorium do zarządzania i udostępniania obrazów, ale można kontrolować, kto ma dostęp. |
| **Definicja obrazu** | Obrazy są definiowane w galerii i zawierają informacje o obrazie i wymaganiach dotyczących używania go w organizacji. Można dołączyć informacje, takie jak to, czy obraz jest uogólniony lub wyspecjalizowany, system operacyjny, minimalne i maksymalne wymagania dotyczące pamięci oraz informacje o wersji. Jest to definicja typu obrazu. |
| **Wersja obrazu** | **Wersja obrazu** jest to, czego używasz do tworzenia maszyny Wirtualnej podczas korzystania z galerii. W zależności od środowiska może być dostępnych wiele wersji obrazu. Podobnie jak w przypadku obrazu zarządzanego, podczas tworzenia maszyny Wirtualnej przy użyciu **wersji obrazu** wersja obrazu jest używana do tworzenia nowych dysków dla maszyny Wirtualnej. Wersje obrazów mogą być używane wiele razy. |

<br>

![Grafika przedstawiająca, jak można mieć wiele wersji obrazu w galerii](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Definicje obrazów

Definicje obrazów są logicznym grupowanie dla wersji obrazu. Definicja obrazu zawiera informacje o tym, dlaczego obraz został utworzony, do jakiego systemu operacyjnego jest przeznaczony, oraz informacje o używaniu obrazu. Definicja obrazu jest jak plan dla wszystkich szczegółów wokół tworzenia określonego obrazu. Maszyna wirtualna nie jest wdrażana z definicji obrazu, ale z wersji obrazu utworzonej na podstawie definicji.

Istnieją trzy parametry dla każdej definicji obrazu, które są używane w połączeniu - **Wydawca**, **Oferta** i **SKU**. Są one używane do znajdowania określonej definicji obrazu. Można mieć wersje obrazów, które mają jeden lub dwa, ale nie wszystkie trzy wartości.  Oto na przykład trzy definicje obrazów i ich wartości:

|Definicja obrazu|Wydawca|Oferta|SKU|
|---|---|---|---|
|myImage1|Contoso|Finanse|Zaplecze|
|myImage2|Contoso|Finanse|Fronton|
|myImage3|Testowanie|Finanse|Fronton|

Wszystkie trzy z nich mają unikatowe zestawy wartości. Format jest podobny do tego, jak obecnie można określić wydawcy, oferty i jednostki SKU dla [obrazów portalu Azure Marketplace](../articles/virtual-machines/windows/cli-ps-findimage.md) w programie Azure PowerShell, aby uzyskać najnowszą wersję obrazu w portalu Marketplace. Każda definicja obrazu musi mieć unikatowy zestaw tych wartości.

Poniżej przedstawiono inne parametry, które można ustawić w definicji obrazu, dzięki czemu można łatwiej śledzić zasoby:

* Stan systemu operacyjnego — stan systemu operacyjnego można ustawić na [uogólniony lub wyspecjalizowany](#generalized-and-specialized-images).
* System operacyjny - może to być system Windows lub Linux.
* Opis — użyj opisu, aby podać bardziej szczegółowe informacje o tym, dlaczego definicja obrazu istnieje. Na przykład może mieć definicję obrazu dla serwera front-end, który ma wstępnie zainstalowaną aplikację.
* Eula - może służyć do wskażenia umowy licencyjnej użytkownika końcowego specyficznej dla definicji obrazu.
* Zasady zachowania poufności informacji i informacje o wersji — informacje o wersji sklepu i zasady zachowania poufności informacji w usłudze Azure Storage i udostępniają identyfikator URI do uzyskiwania do nich dostępu w ramach definicji obrazu.
* Data końca eksploatacji — dołącz datę zakończenia eksploatacji do definicji obrazu, aby móc używać automatyzacji do usuwania starych definicji obrazów.
* Tag - możesz dodawać znaczniki podczas tworzenia definicji obrazu. Aby uzyskać więcej informacji o znacznikach, zobacz [Organizowanie zasobów za pomocą tagów](../articles/azure-resource-manager/management/tag-resources.md)
* Minimalne i maksymalne zalecenia dotyczące procesora i pamięci — jeśli obraz ma zalecenia dotyczące procesora vCPU i pamięci, można dołączyć te informacje do definicji obrazu.
* Niedozwolone typy dysków — można podać informacje o potrzebach magazynowania maszyny Wirtualnej. Jeśli na przykład obraz nie jest odpowiedni dla standardowych dysków twardych, należy dodać je do listy niechcący.

## <a name="generalized-and-specialized-images"></a>Uogólnione i wyspecjalizowane obrazy

Istnieją dwa stany systemu operacyjnego obsługiwane przez Galerię obrazów udostępnionych. Zazwyczaj obrazy wymagają, aby maszyna wirtualna użyta do utworzenia obrazu została uogólniona przed zrobieniem obrazu. Generalizacja to proces, który usuwa informacje o komputerze i użytkowniku z maszyny wirtualnej. W systemie Windows używany jest również program Sysprep. W przypadku linuksa można użyć `-deprovision+user` [waagenta](https://github.com/Azure/WALinuxAgent) `-deprovision` lub parametrów.

Wyspecjalizowane maszyny wirtualne nie zostały za pośrednictwem procesu usuwania informacji i kont specyficznych dla komputera. Ponadto maszyny wirtualne utworzone na podstawie `osProfile` obrazów specjalistycznych nie mają skojarzonego z nimi. Oznacza to, że wyspecjalizowane obrazy będą miały pewne ograniczenia.

- Konta, które mogą służyć do logowania się do maszyny Wirtualnej mogą być również używane na dowolnej maszynie wirtualnej utworzonej przy użyciu wyspecjalizowanego obrazu, który jest tworzony na tej maszynie wirtualnej.
- Maszyny wirtualne będą miały **nazwę komputera** maszyny Wirtualnej, z których pobrano obraz. Należy zmienić nazwę komputera, aby uniknąć kolizji.
- Jest `osProfile` to, jak niektóre poufne informacje są `secrets`przekazywane do maszyny Wirtualnej, za pomocą . Może to powodować problemy przy użyciu funkcji KeyVault, `secrets` WinRM i innych funkcji, które używa w `osProfile`. W niektórych przypadkach można użyć tożsamości usługi zarządzanej (MSI) do obejść te ograniczenia.

> [!IMPORTANT]
> Wyspecjalizowane obrazy są obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Znane ograniczenia podglądu** Maszyny wirtualne można tworzyć tylko z wyspecjalizowanych obrazów przy użyciu portalu lub interfejsu API. Obsługa interfejsu wiersza polecenia lub programu PowerShell dla wersji zapoznawczej nie jest obsługiwana przez program CLI.


## <a name="regional-support"></a>Wsparcie regionalne

Regiony źródłowe są wymienione w poniższej tabeli. Wszystkie regiony publiczne mogą być regionami docelowymi, ale aby replikować do australii środkowej i Australii Central 2, musisz mieć białą listę subskrypcji. Aby poprosić o umieszczanie na białej liście, przejdź do:https://azure.microsoft.com/global-infrastructure/australia/contact/


| Regiony źródłowe        |                   |                    |                    |
| --------------------- | ----------------- | ------------------ | ------------------ |
| Australia Środkowa     | Chiny Wschodnie        | Indie Południowe        | Europa Zachodnia        |
| Australia Środkowa 2   | Chiny Wschodnie 2      | Azja Południowo-Wschodnia     | Południowe Zjednoczone Królestwo           |
| Australia Wschodnia        | Chiny Północne       | Japonia Wschodnia         | Zachodnie Zjednoczone Królestwo            |
| Australia Południowo-Wschodnia   | Chiny Północne 2     | Japonia Zachodnia         | US DoD (region środkowy)     |
| Brazylia Południowa          | Azja Wschodnia         | Korea Środkowa      | US DoD (region wschodni)        |
| Kanada Środkowa        | Wschodnie stany USA           | Korea Południowa        | US Gov Arizona     |
| Kanada Wschodnia           | Wschodnie stany USA 2         | Północno-środkowe stany USA   | US Gov Teksas       |
| Indie Środkowe         | Wschodnie Stany Zjednoczone 2 EUAP    | Europa Północna       | US Gov Wirginia    |
| Środkowe stany USA            | Francja Środkowa    | Południowo-środkowe stany USA   | Indie Zachodnie         |
| Centralna amerykańska EUAP       | Francja Południowa      | Zachodnio-środkowe stany USA    | Zachodnie stany USA            |
|                       |                   |                    | Zachodnie stany USA 2          |



## <a name="limits"></a>Limity 

Istnieją limity dla subskrypcji, do wdrażania zasobów przy użyciu galerii obrazów udostępnionych:
- 100 udostępnionych galerii zdjęć, na subskrypcję, na region
- 1000 definicji obrazów na subskrypcję, na region
- 10 000 wersji obrazów na subskrypcję dla regionu
- Każdy dysk dołączony do obrazu musi mieć rozmiar mniejszy lub równy 1 TB

Aby uzyskać więcej informacji, zobacz [Sprawdzanie użycia zasobów względem limitów,](https://docs.microsoft.com/azure/networking/check-usage-against-limits) aby zapoznać się z przykładami sprawdzania bieżącego użycia.
 
## <a name="scaling"></a>Skalowanie
Galeria obrazów udostępnionych umożliwia określenie liczby replik, które mają być zachowane przez platformę Azure obrazów. Pomaga to w scenariuszach wdrażania wielu maszyn wirtualnych, ponieważ wdrożenia maszyn wirtualnych mogą być rozłożone na różne repliki, zmniejszając prawdopodobieństwo ograniczenia przetwarzania tworzenia wystąpienia z powodu przeciążenia pojedynczej repliki.

Dzięki galerii obrazów udostępnionych można teraz wdrożyć do 1000 wystąpień maszyn wirtualnych w zestawie skalowania maszyny wirtualnej (z 600 z zarządzanymi obrazami). Repliki obrazów zapewniają lepszą wydajność wdrażania, niezawodność i spójność. Można ustawić inną liczbę replik w każdym regionie docelowym, na podstawie potrzeb skalowania dla regionu. Ponieważ każda replika jest głęboką kopią obrazu, pomaga to skalować wdrożenia liniowo z każdą dodatkową repliką. Chociaż rozumiemy, że żadne dwa obrazy lub regiony nie są takie same, oto nasze ogólne wytyczne dotyczące używania replik w regionie:

- W przypadku wdrożeń zestawu skalowania maszyny niewirtualnej (VMSS) — dla każdego 20 maszyn wirtualnych, które tworzysz jednocześnie, zaleca się zachować jedną replikę. Na przykład jeśli tworzysz 120 maszyn wirtualnych jednocześnie przy użyciu tego samego obrazu w regionie, zalecamy zachować co najmniej 6 replik obrazu. 
- W przypadku wdrożeń zestawu skalowania maszyny wirtualnej (VMSS) — dla każdego wdrożenia zestawu skalowania z maksymalnie 600 wystąpieniami zaleca się przechowywanie co najmniej jednej repliki. Na przykład jeśli tworzysz 5 zestawów skalowania jednocześnie, każdy z 600 wystąpień maszyn wirtualnych przy użyciu tego samego obrazu w jednym regionie, zalecamy zachować co najmniej 5 replik obrazu. 

Zawsze zalecamy nadmierne przewidywanie liczby replik ze względu na takie czynniki, jak rozmiar obrazu, zawartość i typ systemu operacyjnego.

![Grafika przedstawiająca sposób skalowania obrazów](./media/shared-image-galleries/scaling.png)

## <a name="make-your-images-highly-available"></a>Udostępnianie obrazów na wysokim stopniu dostępności

[Usługa Azure Zone Redundant Storage (ZRS)](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/) zapewnia odporność na błąd strefy dostępności w regionie. Dzięki ogólnej dostępności galerii obrazów udostępnionych możesz zapisać obrazy na kontach ZRS w regionach ze strefami dostępności. 

Można również wybrać typ konta dla każdego z regionów docelowych. Domyślny typ konta magazynu jest Standard_LRS, ale można wybrać Standard_ZRS dla regionów ze strefami dostępności. Sprawdź regionalną dostępność ZRS [tutaj](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs).

![Grafika przedstawiająca ZRS](./media/shared-image-galleries/zrs.png)

## <a name="replication"></a>Replikacja
Galeria obrazów udostępnionych umożliwia również automatyczne replikowanie obrazów do innych regionów platformy Azure. Każda wersja obrazu udostępnionego może być replikowana do różnych regionów w zależności od tego, co ma sens dla twojej organizacji. Jednym z przykładów jest zawsze replikować najnowszy obraz w wielu regionach, podczas gdy wszystkie starsze wersje są dostępne tylko w 1 regionie. Może to pomóc zaoszczędzić na kosztach magazynowania w wersjach obrazów udostępnionych. 

Regiony, w które jest replikowana wersja obrazu udostępnionego, mogą być aktualizowane po czasie tworzenia. Czas potrzebny do replikacji do różnych regionów zależy od ilości kopiowanych danych i liczby regionów, do w które jest replikowana wersja. W niektórych przypadkach może to potrwać kilka godzin. Podczas replikacji dzieje, można wyświetlić stan replikacji na region. Po zakończeniu replikacji obrazu w regionie można następnie wdrożyć maszynę wirtualną lub zestaw skalowania przy użyciu tej wersji obrazu w regionie.

![Grafika przedstawiająca sposób replikowania obrazów](./media/shared-image-galleries/replication.png)

## <a name="access"></a>Dostęp

Ponieważ udostępnione galerii obrazów, definicja obrazu i wersja obrazu są wszystkie zasoby, mogą być udostępniane przy użyciu wbudowanych natywnych formantów RBAC platformy Azure. Za pomocą rbac można udostępnić te zasoby do innych użytkowników, podmiotów usługi i grup. Można nawet udostępnić dostęp do osób spoza dzierżawy, które zostały utworzone w ramach. Gdy użytkownik ma dostęp do wersji obrazu udostępnionego, może wdrożyć maszynę wirtualną lub zestaw skalowania maszyny wirtualnej.  Oto macierz udostępniania, która pomaga zrozumieć, do czego użytkownik uzyskuje dostęp:

| Udostępnione użytkownikowi     | Galeria obrazów udostępnionych | Definicja obrazu | Wersja obrazu |
|----------------------|----------------------|--------------|----------------------|
| Galeria obrazów udostępnionych | Tak                  | Tak          | Tak                  |
| Definicja obrazu     | Nie                   | Tak          | Tak                  |

Zalecamy udostępnianie na poziomie galerii, aby uzyskać najlepsze wrażenia. Nie zalecamy udostępniania poszczególnych wersji obrazów. Aby uzyskać więcej informacji na temat rbac, zobacz [Zarządzanie dostępem do zasobów platformy Azure przy użyciu funkcji RBAC](../articles/role-based-access-control/role-assignments-portal.md).

Obrazy mogą być również udostępniane na dużą skalę, nawet w dzierżawach przy użyciu rejestracji aplikacji z wieloma dzierżawami. Aby uzyskać więcej informacji na temat udostępniania obrazów między dzierżawami, zobacz [Udostępnianie obrazów maszyn wirtualnych galerii w dzierżawach platformy Azure.](../articles/virtual-machines/linux/share-images-across-tenants.md)

## <a name="billing"></a>Rozliczenia
Korzystanie z usługi Shared Image Gallery nie jest obciążone dodatkową opłatą. Opłata zostanie naliczona za następujące zasoby:
- Koszty magazynowania wersji obrazów udostępnionych. Koszt zależy od liczby replik wersji obrazu i liczby regionów, do których wersja jest replikowana. Na przykład jeśli masz 2 obrazy i oba są replikowane do 3 regionów, następnie zostanie naliczona opłata za 6 dysków zarządzanych na podstawie ich rozmiaru. Aby uzyskać więcej informacji, zobacz [Ceny dysków zarządzanych](https://azure.microsoft.com/pricing/details/managed-disks/).
- Opłaty za wyjście sieciowe dla replikacji pierwszej wersji obrazu z regionu źródłowego do regionów replikowanych. Kolejne repliki są obsługiwane w regionie, więc nie ma żadnych dodatkowych opłat. 

## <a name="updating-resources"></a>Aktualizowanie zasobów

Po utworzeniu można wprowadzić pewne zmiany w zasobach galerii obrazów. Są one ograniczone do:
 
Galeria zdjęć udostępnionych:
- Opis

Definicja obrazu:
- Zalecane procesory wirtualne
- Zalecana pamięć
- Opis
- Data końca daty daty

Wersja obrazu:
- Liczba replik regionalnych
- Regiony docelowe
- Wyklucz z najnowszych
- Data końca daty daty

## <a name="sdk-support"></a>Obsługa zestawu SDK

Następujące sdks obsługuje tworzenie galerii obrazów udostępnionych:

- [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Node.js](https://docs.microsoft.com/javascript/api/@azure/arm-compute)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [Przejdź](https://docs.microsoft.com/azure/go/)

## <a name="templates"></a>Szablony

Zasób Shared Image Gallery można tworzyć przy użyciu szablonów. Dostępnych jest kilka szablonów szybki start platformy Azure: 

- [Tworzenie galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Tworzenie definicji obrazu w galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Tworzenie wersji obrazu w galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Tworzenie maszyny Wirtualnej z wersji obrazu](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>Często zadawane pytania 

* [Jak wyświetlić listę wszystkich zasobów udostępnionej galerii obrazów w ramach subskrypcji?](#how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions) 
* [Czy mogę przenieść istniejący obraz do galerii obrazów udostępnionych?](#can-i-move-my-existing-image-to-the-shared-image-gallery)
* [Czy można utworzyć wersję obrazu z dysku specjalistycznego?](#can-i-create-an-image-version-from-a-specialized-disk)
* [Czy mogę przenieść zasób Udostępnionej Galerii obrazów do innej subskrypcji po jego utworzeniu?](#can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created)
* [Czy mogę replikować wersje obrazów w chmurach, takich jak Azure China 21Vianet lub Azure Germany lub Azure Government Cloud?](#can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud)
* [Czy mogę replikować wersje obrazów w ramach subskrypcji?](#can-i-replicate-my-image-versions-across-subscriptions)
* [Czy mogę udostępniać wersje obrazów w dzierżawach usługi Azure AD?](#can-i-share-image-versions-across-azure-ad-tenants)
* [Jak długo trwa replikowanie wersji obrazów w regionach docelowych?](#how-long-does-it-take-to-replicate-image-versions-across-the-target-regions)
* [Jaka jest różnica między regionem źródłowym a regionem docelowym?](#what-is-the-difference-between-source-region-and-target-region)
* [Jak określić region źródłowy podczas tworzenia wersji obrazu?](#how-do-i-specify-the-source-region-while-creating-the-image-version)
* [Jak określić liczbę replik wersji obrazu, które mają zostać utworzone w każdym regionie?](#how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region)
* [Czy mogę utworzyć galerię obrazów udostępnionych w innym miejscu niż w przypadku definicji obrazu i wersji obrazu?](#can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version)
* [Jakie są opłaty za korzystanie z Galerii obrazów udostępnionych?](#what-are-the-charges-for-using-the-shared-image-gallery)
* [Jakiej wersji interfejsu API należy użyć do utworzenia galerii obrazów udostępnionych i definicji obrazu oraz wersji obrazu?](#what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version)
* [Jakiej wersji interfejsu API należy użyć do utworzenia udostępnionej maszyny wirtualnej lub skalowania maszyny wirtualnej z wersji obrazu?](#what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version)

### <a name="how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions"></a>Jak wyświetlić listę wszystkich zasobów udostępnionej galerii obrazów w ramach subskrypcji?

Aby wyświetlić listę wszystkich zasobów galerii obrazów udostępnionych w ramach subskrypcji, do których masz dostęp w witrynie Azure portal, wykonaj poniższe czynności:

1. Otwórz witrynę [Azure Portal](https://portal.azure.com).
1. Przejdź do **pliku Wszystkie zasoby**.
1. Wybierz wszystkie subskrypcje, w ramach których chcesz wyświetlić listę wszystkich zasobów.
1. Poszukaj zasobów typu **Galeria prywatna**.
 
   Aby wyświetlić definicje obrazów i wersje obrazów, należy również wybrać **opcję Pokaż ukryte typy**.
 
   Aby wyświetlić listę wszystkich zasobów galerii obrazów udostępnionych w ramach subskrypcji, do których masz uprawnienia, użyj następującego polecenia w wierszu polecenia platformy Azure:

   ```azurecli
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
   ```

### <a name="can-i-move-my-existing-image-to-the-shared-image-gallery"></a>Czy mogę przenieść istniejący obraz do galerii obrazów udostępnionych?
 
Tak. Istnieją 3 scenariusze oparte na typach obrazów, które mogą mieć.

 Scenariusz 1: Jeśli masz zarządzany obraz w tej samej subskrypcji co SIG, możesz utworzyć z niego definicję obrazu i wersję obrazu.

 Scenariusz 2: Jeśli masz niezarządzany obraz w tej samej subskrypcji co SIG, możesz utworzyć z niego obraz zarządzany, a następnie utworzyć z niego definicję obrazu i wersję obrazu. 

 Scenariusz 3: Jeśli masz dysk WIRTUALNY w lokalnym systemie plików, musisz przekazać dysk VHD do zarządzanego obrazu, a następnie możesz utworzyć z niego definicję obrazu i wersję obrazu.

- Jeśli dysk VHD jest maszyną wirtualną systemu Windows, zobacz [Przekazywanie dysku VHD](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed).
- Jeśli dysk VHD jest przeznaczony dla maszyny Wirtualnej z systemem Linux, zobacz [Przekazywanie dysku VHD](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)

### <a name="can-i-create-an-image-version-from-a-specialized-disk"></a>Czy można utworzyć wersję obrazu z dysku specjalistycznego?

Tak, obsługa specjalistycznych dysków, ponieważ obrazy są wyświetlane w wersji zapoznawczej. Maszynę Wirtualną można utworzyć tylko z wyspecjalizowanego obrazu przy użyciu portalu[(Windows](../articles/virtual-machines/linux/shared-images-portal.md) lub [Linux)](../articles/virtual-machines/linux/shared-images-portal.md)i interfejsu API. Nie ma obsługi programu PowerShell dla wersji zapoznawczej.

### <a name="can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created"></a>Czy mogę przenieść zasób Udostępnionej Galerii obrazów do innej subskrypcji po jego utworzeniu?

Nie, nie można przenieść zasobu galerii obrazów udostępnionych do innej subskrypcji. Jednak w razie potrzeby będzie można replikować wersje obrazów w galerii do innych regionów.

### <a name="can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud"></a>Czy mogę replikować wersje obrazów w chmurach, takich jak Azure China 21Vianet lub Azure Germany lub Azure Government Cloud?

Nie, nie można replikować wersji obrazu w chmurach.

### <a name="can-i-replicate-my-image-versions-across-subscriptions"></a>Czy mogę replikować wersje obrazów w ramach subskrypcji?

Nie, można replikować wersje obrazu w różnych regionach w ramach subskrypcji i używać go w innych subskrypcjach za pośrednictwem funkcji RBAC.

### <a name="can-i-share-image-versions-across-azure-ad-tenants"></a>Czy mogę udostępniać wersje obrazów w dzierżawach usługi Azure AD? 

Tak, można użyć RBAC do udostępniania osobom w całej dzierżawy. Aby jednak udostępnić je na dużą skalę, zobacz "Udostępnianie obrazów galerii w dzierżawach platformy Azure" przy użyciu programu [PowerShell](../articles/virtual-machines/windows/share-images-across-tenants.md) lub [cli](../articles/virtual-machines/linux/share-images-across-tenants.md).

### <a name="how-long-does-it-take-to-replicate-image-versions-across-the-target-regions"></a>Jak długo trwa replikowanie wersji obrazów w regionach docelowych?

Czas replikacji wersji obrazu jest całkowicie zależny od rozmiaru obrazu i liczby regionów, do które jest replikowany. Jednak jako najlepsze rozwiązanie zaleca się, aby zachować mały obraz, a regiony źródłowe i docelowe blisko, aby uzyskać najlepsze wyniki. Stan replikacji można sprawdzić za pomocą flagi -ReplicationStatus.

### <a name="what-is-the-difference-between-source-region-and-target-region"></a>Jaka jest różnica między regionem źródłowym a regionem docelowym?

Region źródłowy to region, w którym zostanie utworzona wersja obrazu, a regiony docelowe to regiony, w których będzie przechowywana kopia wersji obrazu. Dla każdej wersji obrazu można mieć tylko jeden region źródłowy. Upewnij się również, że podczas tworzenia wersji obrazu należy przekazać lokalizację regionu źródłowego jako jeden z regionów docelowych.

### <a name="how-do-i-specify-the-source-region-while-creating-the-image-version"></a>Jak określić region źródłowy podczas tworzenia wersji obrazu?

Podczas tworzenia wersji obrazu można użyć tagu **--location** w wierszu polecenia i tagu **-Location** w programie PowerShell, aby określić region źródłowy. Upewnij się, że obraz zarządzany używany jako obraz podstawowy do utworzenia wersji obrazu znajduje się w tym samym miejscu co lokalizacja, w której zamierzasz utworzyć wersję obrazu. Upewnij się również, że podczas tworzenia wersji obrazu należy przekazać lokalizację regionu źródłowego jako jeden z regionów docelowych.  

### <a name="how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region"></a>Jak określić liczbę replik wersji obrazu, które mają zostać utworzone w każdym regionie?

Istnieją dwa sposoby określania liczby replik wersji obrazu, które mają zostać utworzone w każdym regionie:
 
1. Liczba replik regionalnych, która określa liczbę replik, które chcesz utworzyć na region. 
2. Wspólna liczba replik, która jest domyślną liczbą na region w przypadku, gdy nie określono regionalnej liczby replik. 

Aby określić liczbę replik regionalnych, przekaż lokalizację wraz z liczbą replik, które chcesz utworzyć w tym regionie: "Południowo-środkowe stany USA=2". 

Jeśli liczba replik regionalnych nie jest określona dla każdej lokalizacji, domyślną liczbą replik będzie określona wspólna liczba replik. 

Aby określić wspólną liczbę replik w wierszu polecenia, `az sig image-version create` użyj argumentu **--replica-count** w poleceniu.

### <a name="can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version"></a>Czy mogę utworzyć galerię obrazów udostępnionych w innym miejscu niż w przypadku definicji obrazu i wersji obrazu?

Tak, jest to możliwe. Jednak jako najlepszą praktykę zachęcamy do zachowania grupy zasobów, galerii obrazów udostępnionych, definicji obrazu i wersji obrazu w tej samej lokalizacji.

### <a name="what-are-the-charges-for-using-the-shared-image-gallery"></a>Jakie są opłaty za korzystanie z Galerii obrazów udostępnionych?

Nie ma żadnych opłat za korzystanie z usługi Shared Image Gallery, z wyjątkiem opłat za przechowywanie wersji obrazu i opłat wychodzących sieciowych za replikowanie wersji obrazu z regionu źródłowego do regionów docelowych.

### <a name="what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version"></a>Jakiej wersji interfejsu API należy użyć do utworzenia galerii obrazów udostępnionych i definicji obrazu oraz wersji obrazu?

Aby pracować z udostępnionymi galeriami obrazów, definicjami obrazów i wersjami obrazów, zalecamy użycie interfejsu API w wersji 2018-06-01. Strefa nadmiarowa pamięć masowa (ZRS) wymaga wersji 2019-03-01 lub nowszej.

### <a name="what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version"></a>Jakiej wersji interfejsu API należy użyć do utworzenia udostępnionej maszyny wirtualnej lub skalowania maszyny wirtualnej z wersji obrazu?

W przypadku wdrożeń zestawu skalowania maszyn wirtualnych i maszyn wirtualnych przy użyciu wersji obrazu zaleca się użycie interfejsu API w wersji 2018-04-01 lub nowszej.

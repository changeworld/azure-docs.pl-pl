---
title: Omówienie usługi Azure Policy | Microsoft Docs
description: Azure Policy to usługa platformy Azure, która umożliwia tworzenie i przypisywanie definicji zasad oraz zarządzanie nimi w środowisku platformy Azure.
services: azure-policy
keywords: ''
author: DCtheGeek
ms.author: dacoulte
ms.reviewer: nini
ms.date: 04/18/2018
ms.topic: overview
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 886026f8548cf3d7416b5034995399368de8c419
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="what-is-azure-policy"></a>Co to jest Azure Policy?

Zarządzanie IT tworzy jasne połączenie między celami biznesowymi i projektami IT. Dobre zarządzanie IT obejmuje planowanie inicjatyw i określanie priorytetów na poziomie strategicznym. Czy w firmie występuje znacząca liczba problemów związanych z IT, które wydają się nie do rozwiązania? Wdrożenie zasad umożliwia lepsze zarządzanie nimi i zapobieganie im. Usługa Azure Policy służy właśnie do wdrażania zasad.

Azure Policy to usługa platformy Azure, która umożliwia tworzenie i przypisywanie definicji zasad oraz zarządzanie nimi. Definicje zasad wymuszają różne reguły i działania dotyczące zasobów, dzięki czemu zasoby te pozostają zgodne ze standardami firmy i umowami dotyczącymi poziomu usług. Usługa Azure Policy przeprowadza ocenę zasobów, skanując je pod kątem elementów niezgodnych z określonymi definicjami zasad. Może na przykład występować zasada dopuszczająca tylko określony typ maszyn wirtualnych. Inna zasada może wymagać oznaczania wszystkich zasobów określonym tagiem. Zasady te są następnie oceniane podczas tworzenia i aktualizowania zasobów.

## <a name="how-is-it-different-from-rbac"></a>Czym się to różni od RBAC?

Istnieje kilka podstawowych różnic między zasadami i kontrolą dostępu opartą na rolach (RBAC). RBAC koncentruje się na działaniach użytkownika w różnych zakresach. Na przykład możesz zostać dodany do roli współautora w grupie zasobów w określonym zakresie. Rola pozwala wprowadzać zmiany w tej grupie zasobów. Zasady skupiają się na właściwościach zasobów podczas ich wdrażania i dla już istniejących zasobów. Zasady umożliwiają na przykład kontrolowanie typów zasobów, które mogą być udostępniane. Można również ograniczyć lokalizacje, w których zasoby mogą być udostępniane. W odróżnieniu od RBAC, zasady to system z domyślnym zezwalaniem i wyraźnym zabranianiem.

Korzystanie z zasad wymaga uwierzytelnienia przy użyciu RBAC. Konkretnie dane konto wymaga:

- Uprawnienia `Microsoft.Authorization/policydefinitions/write` do definiowania zasad.
- Uprawnienia `Microsoft.Authorization/policyassignments/write` do przypisywania zasad.
- Uprawnienie `Microsoft.Authorization/policySetDefinitions/write` do definiowania inicjatywy.
- Uprawnienie `Microsoft.Authorization/policyassignments/write` do przypisania inicjatywy.


Te uprawnienia nie są uwzględnione w roli **Współautor**.


## <a name="policy-definition"></a>Definicja zasad

Każda definicja zasad zawiera warunki, w jakich zasady są wymuszane. Zawiera także towarzyszące działanie, które następuje w przypadku spełnienia warunków.

Usługa Azure Policy oferuje wbudowane zasady, które są domyślnie dostępne. Na przykład:

- **Wymaga programu SQL Server 12.0**: ta definicja zasad zawiera warunki/reguły, które zapewniają, że wszystkie serwery SQL korzystają z wersji 12.0. Jej działaniem jest odrzucanie wszystkich serwerów, które nie spełniają tych kryteriów.
- **Dozwolone jednostki SKU konta magazynu**: ta definicja zasad zawiera zestaw warunków/reguł, które określają, czy wdrażane konto magazynu mieści się w zakresie rozmiarów SKU. Jej działaniem jest odrzucanie wszystkich serwerów, które nie są zgodne z zestawem zdefiniowanych rozmiarów SKU.
- **Dozwolony typ zasobu**: ta definicja zasad zawiera zestaw warunków/reguł, które określają typy zasobów, które organizacja może wdrażać. Jej działaniem jest odrzucanie wszystkich zasobów, które nie należą do tej zdefiniowanej listy.
- **Dozwolone lokalizacje**: ta zasada umożliwia ograniczenie lokalizacji, które organizacja może określić podczas wdrażania zasobów. Jej działanie jest używane do wymuszania wymagań dotyczących zgodności obszarów geograficznych.
- **Dozwolone jednostki SKU maszyny wirtualnej**: te zasady umożliwiają określenie zestawu jednostek SKU maszyn wirtualnych, które organizacja może wdrażać.
- **Zastosuj tag i jego wartość domyślną**: ta zasada stosuje wymagany tag i jego wartość domyślną, jeśli nie została określona przez użytkownika.
- **Wymuszaj tag i jego wartość**: ta zasada wymusza wymagany tag i jego wartość w zasobie.
- **Niedozwolone typy zasobów**: ta zasada umożliwia określanie typów zasobów, których organizacja nie może wdrażać.

Dowolną z tych zasad można przypisać za pośrednictwem witryny Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Po wprowadzeniu zmian w definicji zasad ponowna ocena zasad jest przeprowadzana mniej więcej co godzinę.

Aby dowiedzieć się więcej o strukturach definicji zasad, zapoznaj się z artykułem [Struktura definicji zasad](policy-definition.md).

## <a name="policy-assignment"></a>Przypisywanie zasad

Przypisywanie zasad to definicja zasad, która została przypisana do określonego zakresu. Zakresem tym może być zarówno grupa zarządzania, jak i grupa zasobów. Termin *zakres* odnosi się do wszystkich grup zasobów, subskrypcji i grup zarządzania, do których przypisano definicję zasad. Przypisania zasad są dziedziczone przez wszystkie zasoby podrzędne. Tak więc jeśli zasady stosują się do grupy zasobów, stosują się do wszystkich zasobów w tej grupie zasobów. Z przypisania zasad można jednak wyłączyć zakres podrzędny.

Na przykład przy zakresie subskrypcji można określić zasady, które zapobiegają tworzeniu zasobów sieciowych. Można jednak wyłączyć jedną grupę zasobów w ramach subskrypcji, która jest przeznaczona dla infrastruktury sieciowej. Dostęp do tej grupy zasobów sieciowych można przyznać użytkownikom, którym powierzono tworzenie zasobów sieciowych.

W innym przykładzie można przypisać zasady listy dozwolonych typów zasobów na poziomie grupy zarządzania. Następnie można przypisać mniej ograniczające zasady (zezwalające na większą liczbę typów zasobów) w podrzędnej grupie zarządzania lub nawet bezpośrednio w subskrypcji. Jednak ten przykład nie działa, ponieważ zasady to system z wyraźnym zabranianiem. Zamiast tego należy wykluczyć podrzędną grupę zarządzania lub subskrypcję z przypisania zasad na poziomie grupy zarządzania. Następnie można przypisać mniej ograniczające zasady na poziomie podrzędnej grupy zarządzania lub subskrypcji. Jeśli zatem w wyniku zasad następuje odmowa zasobu, jedynym sposobem na zezwolenie na zasób jest zmodyfikowanie zasad odmowy.

Dodatkowe informacje na temat tworzenia definicji zasad i przypisań zamieszczono w artykule [Tworzenie przypisania zasad w celu identyfikowania niezgodnych zasobów w środowisku platformy Azure](assign-policy-definition.md).

## <a name="policy-parameters"></a>Parametry zasad

Parametry zasad ułatwiają zarządzanie zasadami przez redukowanie liczby definicji zasad, które należy utworzyć. Podczas tworzenia definicji zasad można zdefiniować parametry, które czynią je bardziej ogólnymi. Następnie można użyć tej definicji zasad ponownie w różnych scenariuszach. Polega to na przekazaniu innych wartości podczas przypisywania definicji zasad. Można na przykład określić jeden zestaw lokalizacji dla subskrypcji.

Parametry są definiowane/tworzone podczas tworzenia definicji zasad. Jeśli parametr jest zdefiniowany, otrzymuje nazwę i opcjonalnie wartość. Na przykład można zdefiniować parametr dla zasad o nazwie *location*. Następnie podczas przypisywania zasad można przydzielić mu różne wartości, takie jak *EastUS* i *WestUS*.

<!--
Next link should point to new Concept page for Parameters
-->
Dodatkowe informacje na temat parametrów zasad zamieszczono w artykule [Przegląd zasad zasobów — parametry](policy-definition.md#parameters).

## <a name="initiative-definition"></a>Definicja inicjatywy
Definicja inicjatywy to kolekcja definicji zasad dostosowanych w celu osiągnięcia jednego ogólnego celu. Definicje inicjatyw upraszczają przypisywanie definicji zasad i zarządzanie nimi. Upraszczają działania przez grupowanie zestawu zasad w ramach pojedynczego elementu. Można na przykład utworzyć inicjatywę o nazwie **Włączanie monitorowania w Azure Security Center**, której celem jest monitorowanie wszystkich dostępnych zaleceń dotyczących zabezpieczeń w Azure Security Center.

W ramach tej inicjatywy mogą występować definicje zasad, takie jak:

1. **Monitorowanie nieszyfrowanej bazy danych SQL w Security Center**  — do monitorowania niezaszyfrowanych baz danych i serwerów SQL.
2. **Monitorowanie luk w zabezpieczeniach systemu operacyjnego w Security Center** — do monitorowania serwerów, które nie spełniają wymagań skonfigurowanej linii bazowej.
3. **Monitorowanie brakującej ochrony punktów końcowych w Security Center** — do monitorowania serwerów bez zainstalowanego agenta chroniącego punkty końcowe.

<!--
For more information about initiative definitions, see Initiative Definitions.+ (instead of linking to this, link out to Concept page on Initiative Definitions)
-->

## <a name="initiative-assignment"></a>Przypisanie inicjatywy
Podobnie jak przypisanie zasad, przypisanie inicjatywy to definicja inicjatywy przypisana do określonego zakresu. Przypisania inicjatyw ograniczają potrzebę tworzenia różnych definicji inicjatyw dla każdego zakresu. Zakresem tym również może być zarówno grupa zarządzania, jak i grupa zasobów.

W powyższym przykładzie inicjatywa **Włączanie monitorowania w Azure Security Center** może być przypisana do różnych zakresów. Można na przykład przypisać jedno przypisanie do subskrypcji **subscriptionA**. Kolejne można przypisać do subskrypcji **subscriptionB**.

## <a name="initiative-parameters"></a>Parametry inicjatywy
Podobnie jak parametry zasad, parametry inicjatywy upraszczają zarządzanie inicjatywą przez ograniczenie nadmiarowości. Parametry inicjatywy to zasadniczo lista parametrów używanych przez definicje zasad w ramach tej inicjatywy.

Rozważmy na przykład scenariusz, w którym występuje definicja inicjatywy — **initiativeC** z dwiema definicjami zasad. Każda definicja zasad ma jeden zdefiniowany parametr:

| Zasady | nazwa parametru |Typ parametru  |Uwaga |
|---|---|---|---|
| policyA | allowedLocations | tablica  |Ten parametr oczekuje listy ciągów dla wartości, ponieważ typ parametru został zdefiniowany jako tablica |
| policyB | allowedSingleLocation |ciąg |Ten parametr oczekuje jednego słowa dla wartości, ponieważ typ parametru został zdefiniowany jako ciąg |

W tym scenariuszu podczas definiowania parametrów inicjatywy **initiativeC** dostępne są trzy opcje:

1. Użycie parametrów definicji zasad w ramach tej inicjatywy: w tym przykładzie *allowedLocations* i *allowedSingleLocation* stają się parametrami inicjatywy dla **initiativeC**.
2. Przekaż wartości parametrom definicji zasad w ramach tej definicji inicjatywy. W tym przykładzie można podać listę lokalizacji **parametrowi policyA — allowedLocations** i **parametrowi policyB — allowedSingleLocation**.
Wartości można przekazać również podczas przypisywania tej inicjatywy.
3. Podaj listę opcji *value*, które mogą być używane podczas przypisywania tej inicjatywy. Podczas przypisywania tej inicjatywy odziedziczone parametry z definicji zasad w ramach tej inicjatywy mogą zawierać jedynie wartości z tej dostarczonej listy.

Na przykład można utworzyć listę opcji wartości w definicji inicjatywy, która zawiera *EastUS*, *WestUS*, *CentralUS* i *WestEurope*. W takim przypadku podczas przypisywania inicjatywy nie jest możliwe wprowadzenie innej wartości, takiej jak *Southeast Asia*, ponieważ nie występuje ona na liście.

## <a name="recommendations-for-managing-policies"></a>Zalecenia dotyczące zarządzania zasadami

Podczas tworzenia definicji i przypisań zasad oraz zarządzania nimi warto kierować się kilkoma wskazówkami zamieszczonymi poniżej:

- Jeśli tworzysz definicje zasad w swoim środowisku, zalecamy rozpoczęcie od efektu audytu, a nie od efektu odrzucenia, do śledzenia wpływu definicji zasad na zasoby w środowisku. Jeśli masz już skrypty umożliwiające automatyczne skalowanie swoich aplikacji, skonfigurowanie efektu odrzucenia może negatywnie wpłynąć na zdefiniowane wcześniej zadania automatyzacji.
- Podczas tworzenia definicji i przypisań ważne jest zachowanie hierarchii organizacyjnych. Zaleca się tworzenie definicji na wyższym poziomie, na przykład poziomie grupy zarządzania lub subskrypcji, oraz przypisywanie ich na następnym poziomie podrzędnym. Na przykład w przypadku tworzenia definicji zasad na poziomie grupy zarządzania przypisanie zasad tej definicji może zostać obniżone do poziomu subskrypcji w ramach tej grupy zarządzania.
- Zalecamy korzystanie ze standardowej warstwy cenowej, co umożliwia lepsze zrozumienie stanu zgodności środowiska. Dodatkowe informacje na temat naszych modeli cenowych i ich możliwości zamieszczono w artykule [Cennik](https://azure.microsoft.com/pricing/details/azure-policy).
- Zawsze zalecamy stosowanie definicji inicjatyw zamiast definicji zasad, nawet jeśli w danym przypadku chodzi tylko o jedną zasadę. Na przykład, jeśli dysponujesz definicją zasad — *policyDefA* utworzoną w ramach definicji inicjatywy — *initiativeDefC*, jeśli zdecydujesz się na utworzenie w późniejszym czasie kolejnej definicji zasad, *policyDefB* o celach podobnych do *policyDefA*, możesz dodać ją do definicji *initiativeDefC*, co ułatwia śledzenie jej.

   Należy pamiętać, że po utworzeniu przypisania inicjatywy z definicji inicjatywy, wszelkie nowe definicje zasad dodane do definicji inicjatywy automatycznie podlegają przypisaniom inicjatywy w ramach tej definicji inicjatywy. Jednak w przypadku wystąpienia nowego parametru wprowadzonego do nowej definicji zasad konieczna jest aktualizacja definicji i przypisań inicjatywy poprzez edycję definicji lub przypisania inicjatywy.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz już podstawowe informacje na temat usługi Azure Policy i wprowadzanych przez nas kluczowych pojęć, oto zalecane kolejne kroki:

- [Przypisywanie definicji zasad](./assign-policy-definition.md)
- [Przypisywanie definicji zasad przy użyciu interfejsu wiersza polecenia platformy Azure](./assign-policy-definition-cli.md)
- [Przypisywanie definicji zasad przy użyciu programu PowerShell](./assign-policy-definition-ps.md)

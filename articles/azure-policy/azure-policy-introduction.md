---
title: Omówienie zasad platformy Azure
description: Azure Policy to usługa platformy Azure, która umożliwia tworzenie i przypisywanie definicji zasad oraz zarządzanie nimi w środowisku platformy Azure.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/24/2018
ms.topic: overview
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 51fd0c625ad7e600d54999ddd86e5e49a7c4f14d
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2018
ms.locfileid: "39249872"
---
# <a name="what-is-azure-policy"></a>Co to jest Azure Policy?

Zarządzanie IT umożliwia organizacji pewne osiąganie celów dzięki wydajnemu użyciu infrastruktury IT. W tym celu jest tworzone jasne połączenie między celami biznesowymi i projektami IT.

Czy w firmie występuje znacząca liczba problemów związanych z IT, które wydają się nie do rozwiązania?
Dobre zarządzanie IT obejmuje planowanie inicjatyw i określanie priorytetów na poziomie strategicznym w celu ułatwienia zarządzania i rozwiązywania problemów. Do tego właśnie służy usługa Azure Policy.

Azure Policy to usługa platformy Azure, która umożliwia tworzenie i przypisywanie zasad oraz zarządzanie nimi. Te zasady wymuszają różne reguły i efekty dotyczące zasobów, dzięki czemu zasoby te pozostają zgodne ze standardami firmy i umowami dotyczącymi poziomu usług. Aby to osiągnąć, usługa Azure Policy oblicza zasoby i skanuje w poszukiwaniu zasobów, które nie są zgodne z utworzonymi przez Ciebie zasadami. Może na przykład występować zasada dopuszczająca tylko określony rozmiar jednostki SKU maszyn wirtualnych w środowisku. Gdy te zasady zostaną wdrożone, zostaną obliczone podczas tworzenia i aktualizowania zasobów oraz dla istniejących zasobów. W dalszej części tej dokumentacji bardziej szczegółowo omówiono tworzenie i implementowanie zasad za pomocą usługi Azure Policy.

> [!IMPORTANT]
> Ocena zgodności w usłudze Azure Policy jest teraz dostępna w przypadku wszystkich przypisań niezależnie od warstwy cenowej. Jeśli przydziały nie pokazują danych zgodności, upewnij się, że subskrypcja została zarejestrowana w obrębie dostawcy zasobów Microsoft.PolicyInsights.

## <a name="how-is-it-different-from-rbac"></a>Czym się to różni od RBAC?

Istnieje kilka podstawowych różnic między zasadami i kontrolą dostępu opartą na rolach (RBAC). RBAC koncentruje się na działaniach użytkownika w różnych zakresach. Na przykład możesz zostać dodany do roli współautora w grupie zasobów w określonym zakresie. Rola pozwala wprowadzać zmiany w tej grupie zasobów.
Zasady skupiają się na właściwościach zasobów podczas ich wdrażania i dla już istniejących zasobów. Zasady umożliwiają na przykład kontrolowanie typów zasobów, które mogą być udostępniane. Można również ograniczyć lokalizacje, w których zasoby mogą być udostępniane. W odróżnieniu od RBAC, zasady to system z domyślnym zezwalaniem i wyraźnym zabranianiem.

### <a name="rbac-permissions-in-azure-policy"></a>Uprawnienia RBAC w usłudze Azure Policy

Usługa Azure Policy ma uprawnienia reprezentowane jako operacje u dwóch różnych dostawców zasobów:

- [Microsoft.Authorization](../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsight](../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Niektóre z ról wbudowanych mają różne poziomy uprawnień do zasobów usługi Azure Policy. Należą do nich poziomy **Administrator zabezpieczeń**, który umożliwia zarządzanie przypisaniami i definicjami zasad, ale nie umożliwia wyświetlania informacji o zgodności, oraz **Czytelnik**, który umożliwia odczytywanie szczegółów przypisań i definicji zasad, ale nie umożliwia wprowadzania zmian ani wyświetlania informacji o zgodności. Gdy **właściciel** ma pełne prawa, **współautor** nie ma żadnych uprawnień usługi Azure Policy. Aby udzielić uprawnień do wyświetlania szczegółów zgodności zasad, utwórz [rolę niestandardową](../role-based-access-control/custom-roles.md).

## <a name="policy-definition"></a>Definicja zasad

Proces tworzenia i implementowania zasad w usłudze Azure Policy rozpoczyna się od utworzenia definicji zasad. Każda definicja zasad zawiera warunki, w jakich zasady są wymuszane. Zawiera także towarzyszący efekt, który występuje w przypadku spełnienia warunków.

Usługa Azure Policy oferuje wbudowane zasady, które są domyślnie dostępne. Na przykład:

- **Wymaga programu SQL Server 12.0**: ta definicja zasad zawiera warunki/reguły, które zapewniają, że wszystkie serwery SQL korzystają z wersji 12.0. Jej efektem jest odrzucanie wszystkich serwerów, które nie spełniają tych kryteriów.
- **Dozwolone jednostki SKU konta magazynu**: ta definicja zasad zawiera zestaw warunków/reguł, które określają, czy wdrażane konto magazynu mieści się w zakresie rozmiarów SKU. Jej efektem jest odrzucanie wszystkich kont magazynu, które nie są zgodne z zestawem zdefiniowanych rozmiarów SKU.
- **Dozwolony typ zasobu**: ta definicja zasad zawiera zestaw warunków/reguł, które określają typy zasobów, które organizacja może wdrażać. Jej efektem jest odrzucanie wszystkich zasobów, które nie należą do tej zdefiniowanej listy.
- **Dozwolone lokalizacje**: ta zasada umożliwia ograniczenie lokalizacji, które organizacja może określić podczas wdrażania zasobów. Jej efekt jest używany do wymuszania wymagań dotyczących zgodności obszarów geograficznych.
- **Dozwolone jednostki SKU maszyny wirtualnej**: te zasady umożliwiają określenie zestawu jednostek SKU maszyn wirtualnych, które organizacja może wdrażać.
- **Zastosuj tag i jego wartość domyślną**: ta zasada stosuje wymagany tag i jego wartość domyślną, jeśli nie została określona przez użytkownika.
- **Wymuszaj tag i jego wartość**: ta zasada wymusza wymagany tag i jego wartość w zasobie.
- **Niedozwolone typy zasobów**: ta zasada umożliwia określanie typów zasobów, których organizacja nie może wdrażać.

Aby móc zaimplementować te definicje zasad (wbudowane i niestandardowe), musisz je przypisać. Dowolną z tych zasad można przypisać za pośrednictwem witryny Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

Zasady są ponownie obliczane co godzinę, co oznacza, że w przypadku wprowadzenia zmian w definicji zasad po zaimplementowaniu zasad (utworzeniu przypisania zasad) zostaną one ponownie obliczone dla zasobów w ciągu godziny.

Aby dowiedzieć się więcej o strukturach definicji zasad, zapoznaj się z tematem [Policy Definition Structure](policy-definition.md) (Struktura definicji zasad).

## <a name="policy-assignment"></a>Przypisywanie zasad

Przypisywanie zasad to definicja zasad, która została przypisana do określonego zakresu. Zakresem tym może być zarówno [grupa zarządzania](../azure-resource-manager/management-groups-overview.md), jak i grupa zasobów. Termin *zakres* odnosi się do wszystkich grup zasobów, subskrypcji i grup zarządzania, do których przypisano definicję zasad. Przypisania zasad są dziedziczone przez wszystkie zasoby podrzędne. Oznacza to, że zastosowanie zasad do grupy zasobów powoduje zastosowanie ich do wszystkich zasobów w tej grupie zasobów. Z przypisania zasad można jednak wyłączyć zakres podrzędny.

Na przykład przy zakresie subskrypcji można określić zasady, które zapobiegają tworzeniu zasobów sieciowych. Można jednak wyłączyć jedną grupę zasobów w ramach subskrypcji, która jest przeznaczona dla infrastruktury sieciowej. Dostęp do tej grupy zasobów sieciowych można przyznać użytkownikom, którym powierzono tworzenie zasobów sieciowych.

W innym przykładzie można przypisać zasady listy dozwolonych typów zasobów na poziomie grupy zarządzania. Następnie można przypisać mniej ograniczające zasady (zezwalające na większą liczbę typów zasobów) w podrzędnej grupie zarządzania lub nawet bezpośrednio w subskrypcji. Jednak ten przykład nie działa, ponieważ zasady to system z wyraźnym zabranianiem. Zamiast tego należy wykluczyć podrzędną grupę zarządzania lub subskrypcję z przypisania zasad na poziomie grupy zarządzania. Następnie można przypisać mniej ograniczające zasady na poziomie podrzędnej grupy zarządzania lub subskrypcji. Jeśli zatem w wyniku zasad następuje odmowa zasobu, jedynym sposobem na zezwolenie na zasób jest zmodyfikowanie zasad odmowy.

Dodatkowe informacje na temat tworzenia definicji zasad i przypisań zamieszczono w artykule [Tworzenie przypisania zasad w celu identyfikowania niezgodnych zasobów w środowisku platformy Azure](assign-policy-definition.md).

## <a name="policy-parameters"></a>Parametry zasad

Parametry zasad ułatwiają zarządzanie zasadami przez redukowanie liczby definicji zasad, które należy utworzyć. Podczas tworzenia definicji zasad można zdefiniować parametry, które czynią je bardziej ogólnymi. Następnie można użyć tej definicji zasad ponownie w różnych scenariuszach. Polega to na przekazaniu innych wartości podczas przypisywania definicji zasad. Można na przykład określić jeden zestaw lokalizacji dla subskrypcji.

Parametry są definiowane/tworzone podczas tworzenia definicji zasad. Jeśli parametr jest zdefiniowany, otrzymuje nazwę i opcjonalnie wartość. Na przykład można zdefiniować parametr dla zasad o nazwie *location*. Następnie podczas przypisywania zasad można przydzielić mu różne wartości, takie jak *EastUS* i *WestUS*.

Dodatkowe informacje na temat parametrów zasad zamieszczono w artykule [Przegląd zasad zasobów — parametry](policy-definition.md#parameters).

## <a name="initiative-definition"></a>Definicja inicjatywy

Definicja inicjatywy to kolekcja definicji zasad dostosowanych w celu osiągnięcia jednego ogólnego celu. Definicje inicjatyw upraszczają przypisywanie definicji zasad i zarządzanie nimi. Upraszczają działania przez grupowanie zestawu zasad w ramach pojedynczego elementu. Można na przykład utworzyć inicjatywę o nazwie **Włączanie monitorowania w Azure Security Center**, której celem jest monitorowanie wszystkich dostępnych zaleceń dotyczących zabezpieczeń w Azure Security Center.

W ramach tej inicjatywy mogą występować definicje zasad, takie jak:

- **Monitorowanie nieszyfrowanej bazy danych SQL w Security Center**  — do monitorowania niezaszyfrowanych baz danych i serwerów SQL.
- **Monitorowanie luk w zabezpieczeniach systemu operacyjnego w Security Center** — do monitorowania serwerów, które nie spełniają wymagań skonfigurowanej linii bazowej.
- **Monitorowanie brakującej ochrony punktów końcowych w Security Center** — do monitorowania serwerów bez zainstalowanego agenta chroniącego punkty końcowe.

## <a name="initiative-assignment"></a>Przypisanie inicjatywy

Podobnie jak przypisanie zasad, przypisanie inicjatywy to definicja inicjatywy przypisana do określonego zakresu. Przypisania inicjatyw ograniczają potrzebę tworzenia różnych definicji inicjatyw dla każdego zakresu. Zakresem tym również może być zarówno grupa zarządzania, jak i grupa zasobów.

W powyższym przykładzie inicjatywa **Włączanie monitorowania w Azure Security Center** może być przypisana do różnych zakresów. Można na przykład przypisać jedno przypisanie do subskrypcji **subscriptionA**.
Kolejne można przypisać do subskrypcji **subscriptionB**.

## <a name="initiative-parameters"></a>Parametry inicjatywy

Podobnie jak parametry zasad, parametry inicjatywy upraszczają zarządzanie inicjatywą przez ograniczenie nadmiarowości. Parametry inicjatywy to zasadniczo lista parametrów używanych przez definicje zasad w ramach tej inicjatywy.

Na przykład masz definicję inicjatywy **initiativeC** oraz definicje zasad **policyA** i **policyB**. Każda z nich oczekuje innego typu parametru:

| Zasady | Nazwa parametru |Typ parametru  |Uwaga |
|---|---|---|---|
| policyA | allowedLocations | tablica  |Ten parametr oczekuje listy ciągów dla wartości, ponieważ typ parametru został zdefiniowany jako tablica |
| policyB | allowedSingleLocation |ciąg |Ten parametr oczekuje jednego słowa dla wartości, ponieważ typ parametru został zdefiniowany jako ciąg |

W tym scenariuszu podczas definiowania parametrów inicjatywy **initiativeC** dostępne są trzy opcje:

- Użycie parametrów definicji zasad w ramach tej inicjatywy: w tym przykładzie *allowedLocations* i *allowedSingleLocation* stają się parametrami inicjatywy dla **initiativeC**.
- Przekaż wartości parametrom definicji zasad w ramach tej definicji inicjatywy. W tym przykładzie można podać listę lokalizacji **parametrowi policyA — allowedLocations** i **parametrowi policyB — allowedSingleLocation**. Wartości można przekazać również podczas przypisywania tej inicjatywy.
- Podaj listę opcji *value*, które mogą być używane podczas przypisywania tej inicjatywy. Podczas przypisywania tej inicjatywy odziedziczone parametry z definicji zasad w ramach tej inicjatywy mogą zawierać jedynie wartości z tej dostarczonej listy.

Na przykład można utworzyć listę opcji wartości w definicji inicjatywy, która zawiera *EastUS*, *WestUS*, *CentralUS* i *WestEurope*. W takim przypadku podczas przypisywania inicjatywy nie jest możliwe wprowadzenie innej wartości, takiej jak *Southeast Asia*, ponieważ nie występuje ona na liście.

## <a name="maximum-count-of-policy-objects"></a>Maksymalna liczba obiektów zasad

Istnieje maksymalna liczba dla każdego typu obiektu usługi Azure Policy. Wpis _Zakres_ oznacza subskrypcję lub grupę zarządzania.

| Lokalizacja | Elementy | Maksymalna liczba |
|---|---|---|
| Zakres | Definicje zasad | 250 |
| Zakres | Definicje inicjatyw | 100 |
| Dzierżawa | Definicje inicjatyw | 1000 |
| Zakres | Przypisanie zasad | 100 |
| Definicja zasad | Parametry | 20 |
| Definicja inicjatywy | Zasady | 100 |
| Definicja inicjatywy | Parametry | 100 |
| Przypisanie zasad | Wykluczenia (notScopes) | 100 |
| Reguła zasad | Zagnieżdżone instrukcje warunkowe | 512 |

## <a name="recommendations-for-managing-policies"></a>Zalecenia dotyczące zarządzania zasadami

Podczas tworzenia definicji i przypisań zasad oraz zarządzania nimi warto kierować się kilkoma wskazówkami i poradami zamieszczonymi poniżej:

- Jeśli tworzysz definicje zasad w swoim środowisku, zalecamy rozpoczęcie od efektu audytu, a nie od efektu odrzucenia, do śledzenia wpływu definicji zasad na zasoby w środowisku. Jeśli masz już skrypty umożliwiające automatyczne skalowanie swoich aplikacji, skonfigurowanie efektu odrzucenia może negatywnie wpłynąć na zdefiniowane wcześniej zadania automatyzacji.
- Podczas tworzenia definicji i przypisań ważne jest zachowanie hierarchii organizacyjnych. Zaleca się tworzenie definicji na wyższym poziomie, na przykład poziomie grupy zarządzania lub subskrypcji, oraz przypisywanie ich na następnym poziomie podrzędnym. Na przykład w przypadku tworzenia definicji zasad na poziomie grupy zarządzania przypisanie zasad tej definicji może zostać obniżone do poziomu subskrypcji w ramach tej grupy zarządzania.
- Zawsze zalecamy stosowanie definicji inicjatyw zamiast definicji zasad, nawet jeśli w danym przypadku chodzi tylko o jedną zasadę. Na przykład, jeśli dysponujesz definicją zasad — *policyDefA* utworzoną w ramach definicji inicjatywy — *initiativeDefC*, jeśli zdecydujesz się na utworzenie w późniejszym czasie kolejnej definicji zasad, *policyDefB* o celach podobnych do *policyDefA*, możesz dodać ją do definicji *initiativeDefC*, co ułatwia śledzenie jej.
- Należy pamiętać, że po utworzeniu przypisania inicjatywy z definicji inicjatywy, wszelkie nowe definicje zasad dodane do definicji inicjatywy automatycznie podlegają przypisaniom inicjatywy w ramach tej definicji inicjatywy.
- Wyzwolenie przypisania inicjatywy spowoduje również wyzwolenie wszystkich zasad w ramach tej inicjatywy. Jeśli jednak trzeba wykonać zasady indywidualnie, lepszym rozwiązaniem jest nieuwzględnianie ich w inicjatywie.

## <a name="video-overview"></a>Omówienie wideo

Poniższe omówienie usługi Azure Policy dotyczy kompilacji 2018. Aby pobrać slajdy lub klip wideo, obejrzyj klip wideo [Govern your Azure environment through Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030) w witrynie Channel 9.

> [!VIDEO https://channel9.msdn.com/events/Build/2018/THR2030/player]

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz już podstawowe informacje na temat usługi Azure Policy i kluczowych pojęć, oto zalecane kolejne kroki:

- [Przypisywanie definicji zasad](assign-policy-definition.md)
- [Przypisywanie definicji zasad przy użyciu interfejsu wiersza polecenia platformy Azure](assign-policy-definition-cli.md)
- [Przypisywanie definicji zasad przy użyciu programu PowerShell](assign-policy-definition-ps.md)
- Sprawdzanie, co to jest grupa zarządzania, na stronie [Organize your resources with Azure management groups (Organizowanie zasobów za pomocą grup zarządzania platformy Azure)](../azure-resource-manager/management-groups-overview.md)
- Wyświetlanie klipu wideo [Govern your Azure environment through Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030) w witrynie Channel 9
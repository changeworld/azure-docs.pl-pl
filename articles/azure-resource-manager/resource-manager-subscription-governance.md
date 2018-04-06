---
title: Najlepsze rozwiązania dla przedsiębiorstw przenoszenie na platformę Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano szkieletu służącego do zapewnienia bezpiecznego i łatwą w obsłudze środowiska przedsiębiorstwa.
services: azure-resource-manager
documentationcenter: na
author: rdendtler
manager: timlt
editor: tysonn
ms.assetid: 8692f37e-4d33-4100-b472-a8da37ce628f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/31/2017
ms.author: rodend;karlku;tomfitz
ms.openlocfilehash: 02553bb59cb37bab21d53adafc42fddcf08795a7
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="azure-enterprise-scaffold---prescriptive-subscription-governance"></a>Szkieletu Azure enterprise — ładu przetestowanego subskrypcji
Przedsiębiorstwa są coraz bardziej przyjmowanie chmury publicznej, elastyczność i elastyczność. Są one przy użyciu sile z chmury do generowania przychodu lub optymalizacji zasobów w firmie. Microsoft Azure udostępnia wiele różnych usług czy przedsiębiorstwa można grupować jak bloków konstrukcyjnych w celu rozwiązania szerokiej gamy obciążeń i aplikacji. 

Jednak znajomość czego zacząć często jest trudne. Po podjęciu decyzji używać platformy Azure, często wystąpić na kilka pytań:

* "Jak spełniają naszych wymagania prawne suwerenności danych w niektórych krajach?"
* "Jak zapewnić, że ktoś nie mogą przypadkowo zmieniają krytyczne systemu?"
* "Jak sprawdzić, co każdy zasób obsługuje tak może I uwzględnić go i Utwórz kopię dokładnie rachunku?"

Perspektywa pusty subskrypcji z nie szyny guard jest czasochłonnym zadaniem. Ta puste miejsce mogą utrudniać przejście na platformie Azure.

Ten artykuł zawiera punkt początkowy dla specjalistów eliminują konieczność zarządzania i zrównoważenia go z konieczności elastyczność. Podaj pojęcie szkieletu przedsiębiorstwa, który przeprowadzi organizacji w wdrażanie i zarządzanie nim swoje subskrypcje platformy Azure. 

## <a name="need-for-governance"></a>Potrzebę ładu
Podczas przenoszenia do platformy Azure, należy zapoznać się temacie ładu wcześniej, aby zapewnić skuteczne użycie chmury w przedsiębiorstwie. Niestety czas i biurokracji tworzenia systemu kompleksowe zarządzanie oznacza, że niektórych grup biznesowych, przejdź bezpośrednio do dostawców nie angażując rozwiązaniami INFORMATYCZNYMI dla firm. Takie podejście może pozostać przedsiębiorstwa otwarte na luki w zabezpieczeniach Jeśli zasoby nie są właściwie zarządzane. Właściwości chmury publicznej elastyczności, elastyczność i wyceny opartej na zużycie — są ważne dla grupy biznesowej do szybkiego spełnienia wymagań klientów (wewnętrznych i zewnętrznych). Jednak przedsiębiorstwa IT wymaga w celu zapewnienia skutecznego ochrony danych i systemów.

W świecie rzeczywistym szkieletów służy do tworzenia podstawę struktury. Szkieletu przeprowadzi postać ogólną i zawiera punktów kontrolnych dla bardziej trwałego systemów, które ma zostać zainstalowany. Szkieletu enterprise jest taka sama: zestaw elastyczne opcje kontroli i funkcji platformy Azure, zapewniające strukturę środowiska i kotwice usług oparty na chmurze publicznej. Zapewnia konstruktorów (IT i grup biznesowych) foundation, aby utworzyć i dołączyć nowe usługi.

Szkieletu jest oparta na rozwiązania, które firma Microsoft zostały zebrane z wielu korzystają z klientami o różnych rozmiarach. Tych klientów w zakresie od małe firmy projektowania rozwiązań w chmurze i przedsiębiorstwom listy Fortune 500 i niezależnym dostawcom oprogramowania migracji i projektowania rozwiązań w chmurze. Szkieletu przedsiębiorstwa jest "dedykowanym" elastyczny do obsługi tradycyjnych obciążeń IT i elastyczne obciążenia; takie jak deweloperom tworzenie aplikacji oprogramowania — jako — usługa (SaaS) są oparte na funkcji platformy Azure.

Szkieletu przedsiębiorstwa ma być podstawą dla każdej nowej subskrypcji w obrębie platformy Azure. Umożliwia administratorom upewnij się, że obciążeń spełniają wymagania minimalnych ładu organizacji bez uniemożliwia grup biznesowych i deweloperom szybkie osiągnięcia własnych celów.

> [!IMPORTANT]
> Ładu ma podstawowe znaczenie dla sukcesu Azure. W tym artykule celem implementacji technicznej szkieletu przedsiębiorstwa, ale tylko dotyka na szersze procesu i relacje między składnikami. Zasady zarządzania wypływających z góry w dół i jest określany przez firmy chce osiągnąć. Oczywiście tworzenia modelu ładu dla platformy Azure obejmuje przedstawicieli IT, ale co ważniejsze powinien mieć silne reprezentacja z liderów grupy biznesowej oraz zabezpieczeń i zarządzanie ryzykiem. W końcu szkieletu przedsiębiorstwa jest o ograniczaniu ryzyka firm ułatwiające misji i celów organizacji.
> 
> 

Poniższa ilustracja opisano składniki szkieletu. Podstawę polega na stałe planu dla działów, konta i subskrypcji. Słupków składają się z zasad Menedżera zasobów i silne Standardy nazewnictwa. Pozostałe szkieletu funkcjach tej Włącz bezpieczne i łatwe w zarządzaniu środowisko i pochodzi z podstawowych funkcji platformy Azure.

![składniki szkieletu](./media/resource-manager-subscription-governance/components.png)

> [!NOTE]
> Azure szybko zwiększył się od czasu jego wprowadzenia 2008. Wzrostu wymagane przez firmę Microsoft zespoły inżynierów przemyślenia ich podejście do zarządzania i wdrażania usług. Model usługi Azure Resource Manager został wprowadzony w 2014 i zastępuje klasycznym modelu wdrażania. Menedżer zasobów umożliwia organizacjom łatwiej wdrażać, organizować i kontrolowania zasobów platformy Azure. Menedżer zasobów zawiera paralelizacja podczas tworzenia zasobów dla szybsze wdrażanie złożonych, współzależne rozwiązań. Zawiera także precyzyjną kontrolę dostępu oraz możliwość zasobów tag przy użyciu metadanych. Firma Microsoft zaleca utworzenie wszystkich zasobów za pomocą modelu Resource Manager. Szkieletu enterprise jawnie jest przeznaczona dla modelu Resource Manager.
> 
> 

## <a name="define-your-hierarchy"></a>Zdefiniuj hierarchii
Podstawę szkieletu jest rejestracja Enterprise Azure (i Enterprise Portal). Rejestracja enterprise definiuje kształt i korzystają z usług Azure w obrębie firmy i jest strukturę zarządu core. W ramach umowy enterprise agreement klienci mogą dodatkowo podzielić środowiska do działów, kont, a na końcu subskrypcji. Subskrypcji platformy Azure to podstawowa jednostka, w którym znajdują się wszystkie zasoby. Definiuje również kilka ograniczeń w obrębie platformy Azure, takich jak liczba rdzeni, zasoby itd.

![hierarchia](./media/resource-manager-subscription-governance/agreement.png)

Różni się co przedsiębiorstwa i umożliwia hierarchii na poprzedniej ilustracji dużą swobodę w sposób organizowania Azure w firmie. Przed wdrożeniem wskazówki zawarte w tym dokumencie, możesz modelu hierarchii i zrozumienie wpływu na rozliczenia, dostęp do zasobów i złożoności.

Są trzy wspólne wzorce dla rejestracji Azure:

* **Funkcjonalności** wzorca
  
    ![funkcjonalności](./media/resource-manager-subscription-governance/functional.png)
* **Jednostki biznesowej** wzorca 
  
    ![firmy](./media/resource-manager-subscription-governance/business.png)
* **Geograficzne** wzorca
  
    ![geograficzne](./media/resource-manager-subscription-governance/geographic.png)

Należy zastosować szkieletu na poziomie subskrypcji, aby rozszerzyć ładu wymagań przedsiębiorstwa do subskrypcji.

## <a name="naming-standards"></a>Standardy nazewnictwa
Pierwszy słupka z szkieletu jest nazewnictwa standardów. Dobrze zaprojektowanego Standardy nazewnictwa umożliwiają identyfikowanie zasobów w portalu na rachunek i w ramach skryptów. Prawdopodobnie już nazewnictwo standardy dotyczące infrastruktury lokalnej. Podczas dodawania Azure do środowiska, powinno obejmować standardami nazewnictwa zasobów platformy Azure. Standard nazewnictwa ułatwienia bardziej efektywne zarządzanie środowiskiem na wszystkich poziomach.

> [!TIP]
> Dla konwencji nazewnictwa:
> * Przejrzyj i przyjęcie w miarę możliwości [wskazówki Patterns and Practices](../guidance/guidance-naming-conventions.md). W tych wskazówkach ułatwia podejmowanie decyzji o znaczący standard nazewnictwa.
> * Użyj camelCasing dla nazw zasobów (takich jak myResourceGroup i vnetNetworkName). Uwaga: Dostępne są niektóre zasoby, takie jak kont magazynu, gdy tylko opcja polega na małe litery (i innych znaków specjalnych).
> * Należy wziąć pod uwagę, aby wymusić Standardy nazewnictwa przy użyciu zasad usługi Azure Resource Manager (opisany w następnej sekcji).
> 
> Poprzedniego wskazówki ułatwiające implementację spójnej konwencji nazewnictwa.

## <a name="policies-and-auditing"></a>Zasady i inspekcji
Drugi słupka z szkieletu obejmuje utworzenie [zasady Azure](../azure-policy/azure-policy-introduction.md) i [dziennik inspekcji](resource-group-audit.md). Zasady usługi Resource Manager umożliwiają zarządzanie ryzykiem na platformie Azure. Można zdefiniować zasady, zapewniając suwerenności danych przez ograniczenie, wymuszanie lub inspekcji pewne akcje. 

* Zasada jest domyślnie **Zezwalaj** systemu. Definiowanie i przypisywanie zasad z zasobami, które Odmów lub inspekcję akcji zasobów jest kontrolowanie akcji.
* Zasady są opisane w definicjach zasad w języku definicji zasad (warunki, jeżeli to).
* Możesz utworzyć zasady z JSON (Javascript Object Notation) sformatowany plików. Po zdefiniowaniu zasad, można przypisać do określonego zakresu: zasobu, grupy zasobów lub subskrypcji.

Zasady mają wiele akcji, które umożliwia szczegółowe podejścia do scenariuszy. Dostępne są następujące akcje:

* **Odmów**: blokuje żądania zasobów
* **Inspekcji**: zezwala na żądanie, ale dodaje linię do dziennika aktywności (która może być używane, aby zapewnić alerty lub do wyzwolenia elementów runbook)
* **Dołącz**: dodaje określony informacje do zasobu. Na przykład jeśli do zasobu nie jest tag "CostCenter", należy dodać tagu z wartością domyślną.

### <a name="common-uses-of-resource-manager-policies"></a>Najczęstsze zastosowania zasad Menedżera zasobów
Zasady usługi Azure Resource Manager są zaawansowane narzędzie w zestawie narzędzi programu Azure. Umożliwiają one uniknąć kosztów nieoczekiwany, aby zidentyfikować Centrum kosztów zasobów za pomocą znakowania i upewnij się, że spełniono wymagania dotyczące zgodności. Gdy zasady są połączone z wbudowanych funkcji inspekcji, możesz fashion złożone i elastycznych rozwiązań. Zezwalaj na zasady firmy do świadczenia formanty obciążeń "Tradycyjnych IT" i "Agile" obciążenia; takie jak tworzenie aplikacji klienta. Najbardziej typowe wzorce, które widzimy dla zasad są:

* **Suwerenności danych geograficznie zgodności** — platforma Azure udostępnia regionów całym świecie. Przedsiębiorstwa często chcesz kontrolować, gdy zasoby są tworzone (czy do zapewnienia suwerenności danych lub po prostu upewnij się, że zasoby są tworzone w pobliżu użytkowników końcowych zasobów).
* **Koszt zarządzania** -subskrypcji platformy Azure może obejmować zasoby wiele typów i skali. Firmy często chcą upewnij się, że standardowa subskrypcje unikać niepotrzebnie zasoby, które mogą kosztem setki kwoty miesiąc lub więcej.
* **Domyślna ładu za pomocą wymaganych tagów** -wymaganie tagów jest jedną z najbardziej typowe i wysoce żądanej funkcji. Za pomocą usługi Azure Resource Manager zasad przedsiębiorstwa będą mogli upewnij się, że zasób jest odpowiednio oznakowane. Najbardziej typowe tagi są: działu, właściciel zasobu i środowisko typu (na przykład - produkcji, testu, rozwoju)

**Przykłady**

"Tradycyjnych IT" subskrypcji dla aplikacji biznesowych z

* Właściciel znaczniki wszystkich zasobów i wymusić dział
* Ogranicz tworzenie zasobu w Ameryce Północnej regionie
* Ograniczyć możliwość tworzenia G serii maszyn wirtualnych i klastry usługi HDInsight

Środowisko "Agile" dla jednostki biznesowej, tworzenie aplikacji w chmurze

* Aby spełnić wymagania suwerenności danych, pozwala na tworzenie zasobów tylko w określonym regionie.
* Wymuś tag środowiska wszystkich zasobów. Jeśli zasób jest tworzona bez tagu, dołącz **środowiska: nieznany** tag do zasobu.
* Inspekcja, gdy zasoby są tworzone poza Ameryce Północnej, ale nie uniemożliwiają.
* Inspekcja podczas tworzenia zasobów wysokich kosztów.

> [!TIP]
> Najczęściej zasad Menedżera zasobów między organizacjami jest formantem *gdzie* zasoby mogą być tworzone i *co* typów zasobów, które mogą być tworzone. Oprócz formantów na *gdzie* i *co*, wiele przedsiębiorstw zapewnić zasoby mają odpowiednie metadane, aby rozliczyć wstecz do użycia przez przy użyciu zasad. Firma Microsoft zaleca stosowanie zasad na poziomie subskrypcji dla:
> 
> * Suwerenności danych geograficznie zgodności
> * Zarządzanie kosztami
> * Wymagane tagi (powodowane potrzeb biznesowych, takich jak adres rachunku, właściciel aplikacji)
> 
> Można zastosować dodatkowe zasady na niższych poziomach zakresu.
> 
> 

### <a name="audit---what-happened"></a>Inspekcji - co się stało?
Aby zobaczyć, jak działa środowisko, musisz inspekcji aktywności użytkownika. Większość typów zasobów w systemie Azure utworzyć dzienników diagnostycznych, które można analizować przy użyciu narzędzia dziennika lub w Analiza dzienników Azure. Możesz zbierać Dzienniki aktywności wielu subskrypcji, aby zapewnić działów lub widok przedsiębiorstwa. Rekordy inspekcji są zarówno ważne narzędzia diagnostycznego, jak i ważnych mechanizm zdarzenia wyzwalacza w środowisku platformy Azure.

Dzienniki aktywności z wdrożenia usługi Resource Manager umożliwiają określenie **operacji** które miało miejsce i kto wykonał. Dzienniki aktywności mogą być zbierane i zagregowane za pomocą narzędzi takich jak analizy dzienników.

## <a name="resource-tags"></a>Tagi zasobów
Jak użytkownicy w Twojej organizacji dodać zasoby do subskrypcji, staje się coraz bardziej ważne skojarzyć zasoby z odpowiedniego działu, klientów i środowisko. Możesz dołączyć metadanych do zasobów za pomocą [tagi](resource-group-using-tags.md). Tagi są używane do udostępniania informacji na temat zasobu lub właściciela. Znaczniki umożliwiają nie tylko agregacji i grupy zasobów na różne sposoby, ale użyć tych danych na potrzeby obciążeń zwrotnych. Można oznaczyć zasoby z maksymalnie 15 pary klucz: wartość. 

Tagi zasobów są elastyczne i musi być podłączona do najwięcej zasobów. Przykłady typowych tagi zasobów są:

* Adres rachunku
* Dział (lub jednostki biznesowej)
* Środowiska (Programowanie produkcji, etap)
* Warstwy (warstwa sieci Web, warstwy aplikacji)
* Właściciel aplikacji
* ProjectName

![tags](./media/resource-manager-subscription-governance/resource-group-tagging.png)

Więcej przykładów tagów, zobacz [konwencje nazewnictwa dla zasobów platformy Azure zalecane](../guidance/guidance-naming-conventions.md).

> [!TIP]
> Należy rozważyć zmianę zasad, które ma znakowanie dla:
> 
> * Grupy zasobów
> * Magazyn
> * Maszyny wirtualne
> * Serwery środowisk/sieci web usługi aplikacji
> 
> Ta strategia znakowania identyfikuje wszystkich subskrypcji, jakie metadanych jest wymagany dla firm, finansowych, zabezpieczeń, zarządzania ryzykiem i ogólne zarządzanie środowiska. 

## <a name="resource-group"></a>Grupa zasobów
Menedżer zasobów pozwala na poddane zasobów znaczący grup zarządzania, koligacji rozliczeń lub fizycznych. Jak wspomniano wcześniej, platforma Azure ma dwa modele wdrażania. W starszych klasycznego modelu podstawową jednostką zarządzania było subskrypcji. Było trudne do podziału zasobów w ramach subskrypcji, które doprowadziło do utworzenia dużą liczbę subskrypcji. Z modelu Resource Manager widzieliśmy wprowadzenie grup zasobów. Grupy zasobów to kontenery zasoby, które mają wspólne cykl lub udostępnić atrybut, taki jak "wszystkie serwery SQL" lub "Aplikacja A".

Grupy zasobów nie może być zawarty w sobie nawzajem i zasoby mogą należeć tylko do jednej grupy zasobów. Pewne akcje można stosować na wszystkie zasoby w grupie zasobów. Na przykład usunięcie grupy zasobów powoduje usunięcie wszystkich zasobów w grupie zasobów. Zazwyczaj umieszczeniu całej aplikacji lub systemu pokrewne w tej samej grupie zasobów. Na przykład trójwarstwowa aplikacja o nazwie aplikacji sieci Web firmy Contoso zawierałoby serwera sieci web, serwera aplikacji i programu SQL server w tej samej grupie zasobów.

> [!TIP]
> Sposób organizowania grup zasobów mogą się różnić od obciążeń "Tradycyjnych IT" w przypadku obciążeń "Agile IT":
> 
> * "Tradycyjnych IT" obciążeń najczęściej są pogrupowane według elementów w obrębie tego samego cyklu życia, takie jak aplikacja. Umożliwia grupowanie przez aplikację do zarządzania aplikacjami w poszczególnych.
> * "Agile IT" obciążeń często skupić się na aplikacji chmury zewnętrznej skierowane do klienta. Grupy zasobów powinien odzwierciedlać warstwy wdrażania (na przykład warstwa sieci Web warstwy aplikacji) i zarządzania.
> 
> Opis obciążenie pomocne w opracowywaniu strategii grupy zasobów.

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach
Użytkownik prawdopodobnie prośbę o sobie "kto ma dostęp do zasobów?" "jak kontrolować dostęp?" Stosowanie lub brak zezwolenia dostęp do portalu Azure, kontrolowania dostępu do zasobów w portalu odgrywa kluczową rolę. 

Gdy Azure pierwotnie został wydany, kontroli dostępu do subskrypcji zostały podstawowe: administratorem ani Współadministratorem. Dostęp do subskrypcji w klasycznym modelu niejawnego dostęp do wszystkich zasobów w portalu. Ten brak precyzyjną kontrolę, które doprowadziły do mnożenie subskrypcji do zapewnienia poziomu kontroli dostępu uzasadnione rejestracji platformy Azure.

Mnożenie tej subskrypcji nie jest już potrzebne. Przy użyciu kontroli dostępu opartej na rolach można przypisać użytkowników do ról standardowych (takich jak "" i "zapisu" typowych ról). Można również zdefiniować role niestandardowe.

> [!TIP]
> Aby wdrażanie kontroli dostępu opartej na rolach:
> * Połączyć z tożsamością firmową Sklepu (najczęściej Active Directory) do usługi Azure Active Directory przy użyciu narzędzia AD Connect.
> * Kontrola administracyjna/Współadministratora subskrypcji za pomocą tożsamości zarządzanych. **Nie** przypisać administratora/współadministratora do nowego właściciela subskrypcji. Zamiast tego użyj role RBAC zapewnienie **właściciela** praw do grupy lub osoby.
> * Dodawanie użytkowników platformy Azure do grupy (na przykład właściciele X aplikacji) w usłudze Active Directory. Użyj grupy zsynchronizowane, aby podać członków grupy odpowiednie uprawnienia do zarządzania grupę zasobów zawierającą aplikację.
> * Postępuj zgodnie z zasadą przyznawania **najniższych uprawnień** wymagane w oczekiwanym pracy. Na przykład:
>   * Grupa wdrożenia: Grupa, która jest tylko możliwość wdrażania zasobów.
>   * Zarządzanie maszynami wirtualnymi: Grupę, która umożliwia ponowne uruchomienie maszyn wirtualnych (dla operacji)
> 
> Te wskazówki ułatwiają zarządzanie dostępem użytkowników w Twojej subskrypcji.

## <a name="azure-resource-locks"></a>Blokad zasobów platformy Azure
Jak organizacji dodaje podstawowe usługi do subskrypcji, staje się coraz bardziej ważne upewnić się, że te usługi są dostępne w celu uniknięcia przerw w działaniu biznesowych. [Blokowania zasobów](resource-group-lock-resources.md) pozwalają ograniczyć operacje na zasobach wysokiej wartości, gdzie modyfikowania lub usuwania ich miałoby znaczący wpływ na aplikacje lub infrastruktury chmury. Blokady można stosować do subskrypcji, grupy zasobów lub zasobów. Zazwyczaj należy zastosować blokady do podstawowych zasobów, takich jak sieci wirtualnych, bramy i kont magazynu. 

Blokowania zasobów obsługuje obecnie dwie wartości: CanNotDelete i tylko do odczytu. CanNotDelete oznacza, że użytkownicy (z odpowiednich praw) mogą w dalszym ciągu odczytu lub zmodyfikuj zasobu, ale nie można go usunąć. Tylko do odczytu oznacza, że autoryzowani użytkownicy nie można usunąć ani zmodyfikować zasobu.

Aby utworzyć lub usunąć blokady zarządzania, musi mieć dostęp do `Microsoft.Authorization/*` lub `Microsoft.Authorization/locks/*` akcje.
Wbudowanych ról tylko właściciel i Administrator dostępu użytkowników są przyznawane te akcje.

> [!TIP]
> Opcje sieci podstawowej powinny być chronione przy użyciu blokad. Przypadkowego usunięcia bramy sieci VPN typu lokacja lokacja będą Fatalne z subskrypcją platformy Azure. Azure nie zezwala na usuwanie sieci wirtualnej, który jest używany, ale stosowanie więcej ograniczeń, jest przydatne środki ostrożności. 
> 
> * Sieć wirtualna: CanNotDelete
> * Sieciowa grupa zabezpieczeń: CanNotDelete
> * Zasady: CanNotDelete
> 
> Zasady również są kluczowe dla obsługi właściwych kontroli. Firma Microsoft zaleca stosowanie **CanNotDelete** blokady do zasady, które są używane.

## <a name="core-networking-resources"></a>Podstawowe zasoby sieciowe
Dostęp do zasobów można wewnętrznych (w ramach sieci) lub zewnętrznych (za pośrednictwem Internetu). To proste dla użytkowników w organizacji na przypadkowo umieść zasobów w miejscu niewłaściwy i potencjalnie otwórz je, aby nieautoryzowanym dostępem. Podobnie jak w przypadku urządzeń lokalnych, przedsiębiorstw, należy dodać kontrole w celu zapewnienia, że Azure użytkowników podejmować decyzje prawo. Zarządzanie subskrypcją nazywamy zasobów podstawowych, które zapewniają podstawowy kontrolę dostępu. Podstawowe zasoby obejmują:

* **Sieci wirtualne** obiektów kontenera podsieci. Chociaż nie niezbędne, często jest używana podczas łączenia z aplikacji do wewnętrznych zasobów firmy.
* **Sieciowe grupy zabezpieczeń** są podobne do zapory i podaj reguły jak zasobu można "rozmawiać" za pośrednictwem sieci. Udostępniają one szczegółową kontrolę nad jak / Jeśli podsieci (lub maszyny wirtualnej) można nawiązać połączenia z Internetem lub innych podsieci w tej samej sieci wirtualnej.

![Podstawowe operacje sieciowe](./media/resource-manager-subscription-governance/core-network.png)

> [!TIP]
> Praca w sieci:
> * Tworzenie sieci wirtualnych przeznaczonym do obciążeń dołączonej do Internetu i wewnętrzny uwzględniającym obciążeń. Takie podejście zmniejsza ryzyko przypadkowego umieszczania maszyn wirtualnych, które są przeznaczone dla wewnętrznych obciążeń w zewnętrznych połączonej przestrzeni.
> * Konfigurowanie grup zabezpieczeń sieci, aby ograniczyć dostęp. Co najmniej zablokowanie dostępu do Internetu z wewnętrzne sieci wirtualne i zablokowanie dostępu do sieci firmowej z zewnętrzne sieci wirtualne.
> 
> Te wskazówki ułatwiające implementację zabezpieczania zasobów sieciowych.

### <a name="automation"></a>Automatyzacja
Zarządzanie zasobami indywidualnie jest czasochłonne i potencjalnie podatna dla niektórych operacji. Platforma Azure oferuje różne możliwości automatyzacji, w tym usługi Automatyzacja Azure Logic Apps i usługi Azure Functions. [Automatyzacja Azure](../automation/automation-intro.md) umożliwia administratorom utworzenie i zdefiniowanie elementów runbook do obsługi typowych zadań zarządzania zasobami. Możesz utworzyć elementy runbook za pomocą edytora kodu programu PowerShell lub edytora graficznego. Służy do tworzenia złożonych wieloetapowym przepływów pracy. Automatyzacja Azure jest często używane do obsługi typowych zadań, takich jak zamykanie nieużywanych zasobów lub tworzenia zasobów w odpowiedzi na określone wyzwalacza bez udziału człowieka.

> [!TIP]
> Do automatyzacji:
> * Utwórz konto usługi Automatyzacja Azure i przejrzyj dostępne elementy runbook (zarówno graficznego i polecenia wiersza) dostępna w [galerię elementów Runbook](../automation/automation-runbook-gallery.md).
> * Importowanie i dostosować klucza elementów runbook na własny użytek.
> 
> Typowy scenariusz obejmuje możliwość rozpoczęcia/zamknięcie maszyny wirtualne zgodnie z harmonogramem. Brak przykładowe elementy runbook, które są dostępne w galerii zarówno obsługi tego scenariusza i omawiające sposób go rozwinąć.
> 
> 

## <a name="azure-security-center"></a>Azure Security Center
Możliwe, że jedną z największych okienek do chmury wdrażania została problemów za pośrednictwem zabezpieczeń. Ryzyko kierownicy działów zabezpieczeń należy i upewnij się, że zasobów platformy Azure są bezpieczne. 

[Centrum zabezpieczeń Azure](../security-center/security-center-intro.md) centralnej Wyświetla stan zabezpieczeń zasobów w subskrypcji i zawiera zalecenia, które ułatwiają ochronę zasobów ze złamanymi zabezpieczeniami. Bardziej szczegółowe zasady (na przykład stosowania zasad do określonych grup zasobów, które umożliwiają firmie dostosować ich postawie ryzyko, które są one adresowania) można ją włączyć. Wreszcie Centrum zabezpieczeń Azure jest otwartej platformie, która umożliwia partnerom firmy Microsoft i niezależnym dostawcom oprogramowania tworzenia oprogramowania, które podłącza się do Centrum zabezpieczeń Azure w celu zwiększenia jego możliwości. 

> [!TIP]
> Centrum zabezpieczeń Azure jest domyślnie włączone w każdej subskrypcji. Jednak należy włączyć zbieranie danych z maszyn wirtualnych, aby umożliwić Centrum zabezpieczeń Azure zainstalować jego agenta i rozpocząć zbieranie danych.
> 
> ![Zbieranie danych](./media/resource-manager-subscription-governance/data-collection.png)
> 
> 

## <a name="next-steps"></a>Kolejne kroki
* Teraz, kiedy znasz dotyczących ładu subskrypcji, jest czas, aby wyświetlić te zalecenia w praktyce. Zobacz [przykłady stosowania ładu subskrypcji platformy Azure](resource-manager-subscription-examples.md).


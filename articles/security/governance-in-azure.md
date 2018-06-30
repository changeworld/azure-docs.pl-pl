---
title: Zarządzanie na platformie Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat przetwarzania danych usług w chmurze, które można skalować w górę i w dół do wymagań aplikacji lub przedsiębiorstwa.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: TomSh
ms.openlocfilehash: 9c6509f25be7fe520a427e17ca1206e10f296fea
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110766"
---
# <a name="governance-in-azure"></a>Ład na platformie Azure

Azure udostępnia wiele opcji dotyczących zabezpieczeń oraz możliwość ich kontroli, dzięki czemu można spełnić unikatowe wymagania danej organizacji wdrożeń.

Chmury Azure ładu odwołuje się do procesów decyzyjnych kryteria i zasady objętego planowania, architektura, przejęcia, wdrażania, operacji i zarządzanie chmury obliczeniowej. Zarządzanie chmury Azure oferuje zintegrowane inspekcji i konsultingowe podejście do sprawdzenia i udzielanie porad organizacji na ich użycie platformy Azure. 

Aby utworzyć plan zarządzania w chmurze Azure, należy wykonać omówiono osób, procesów i technologii teraz w miejscu. Następnie można tworzyć platform, które ułatwiają IT do obsługi spójnie potrzeb biznesowych, jednocześnie zapewniając użytkownikom elastyczność możliwości korzystania z funkcji platformy Azure.

## <a name="implementation-of-policies-processes-and-standards"></a>Stosowania zasad, procesów i standardów 

Zarządzanie ustanowił role i obowiązki nadzorują implementacja zasad zabezpieczeń informacji i ciągłości obrębie platformy Azure. Zarządzania platformy Azure jest odpowiedzialny za nadzór nad zabezpieczeń i ciągłości rozwiązań w jego odpowiednich zespołów (w tym firmom). Ułatwia także zgodności z zasadami zabezpieczeń, procesów i standardów.

### <a name="account-provisioning"></a>Aprowizacja kont

Definiowanie hierarchii konta jest ważny krok i struktury usług platformy Azure w obrębie firmy. Jest strukturę zarządu core. Klienci, którzy mają Enterprise Agreement (EA) można podzielić środowiska do działów, konta i subskrypcji.

![Aprowizacja kont](./media/governance-in-azure/security-governance-in-azure-fig1.png)

Jeśli nie masz umowy Enterprise Agreement, rozważ użycie [Azure tagi](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) do definiowania hierarchii na poziomie subskrypcji. Subskrypcji platformy Azure to podstawowa jednostka, która zawiera wszystkie zasoby. Definiuje również kilka ograniczeń w obrębie platformy Azure, takich jak liczby rdzeni i zasobów. Subskrypcje mogą zawierać [grup zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), które zawierają zasoby. [Kontrola dostępu oparta na rolach (RBAC)](https://docs.microsoft.com/en-us/azure/role-based-access-control/overview) zasady stosowane na te trzy poziomy.

Różni się każdego przedsiębiorstwa. Dla firm innych niż przedsiębiorstwa hierarchii przy użyciu tagów Azure umożliwia elastyczność w sposób organizowania Azure. Przed wdrożeniem zasobami na platformie Azure, możesz hierarchii i zrozumienie wpływu na rozliczenia, dostęp do zasobów i złożoności.

### <a name="subscription-controls"></a>Formanty subskrypcji

Subskrypcje określają sposób zgłoszone i rozliczane użycia zasobów. Można skonfigurować subskrypcje oddzielnych rozliczeń i płatności. Jedno konto platformy Azure może mieć wiele subskrypcji. Subskrypcje można ustalić skalę użycia zasobów platformy Azure przez wielu działów w firmie.

Na przykład firma ma IT, HR, i działu marketingu i działów te działają różnych projektów. Firma może bazowy jego rozliczeń każdy dział wykorzystanie zasobów platformy Azure, takich jak maszyny wirtualne. Firma może wybrać, finansów każdego działu.

Subskrypcje platformy Azure nawiązać trzy parametry:

- Identyfikator unikatowy subskrybenta

- Lokalizacja rozliczeń

- Zbiór dostępnych zasobów

Dla poszczególnych te parametry obejmują jeden identyfikator konta Microsoft, numer karty kredytowej i pełny zestaw usług platformy Azure. Microsoft wymusza ograniczenia użycia, w zależności od typu subskrypcji.

Hierarchie Azure rejestracji zdefiniuj struktury usług w ramach umowy Enterprise Agreement. Umowa Enterprise portal umożliwia klientom dzielenia dostęp do zasobów platformy Azure skojarzone z umowy Enterprise Agreement, w oparciu o elastyczną hierarchii, które można dostosować do potrzeb organizacji. Wzorzec hierarchii powinna odpowiadać, zarządzania i geograficzne struktury konta skojarzone dostępu rozliczeń i zasobów organizacji.

Jednostka funkcjonalności, business, są trzy wzorce wysokiego poziomu hierarchii i geograficzne. Działów są konstrukcję administracyjne dla grupy kont. W ramach każdego działu kont można przypisać subskrypcji, których tworzenie silosów rozliczeń i kilka ograniczeń klucza na platformie Azure (na przykład liczba kont magazynu i maszyn wirtualnych).

![Formanty subskrypcji](./media/governance-in-azure/security-governance-in-azure-fig2.png)


W przypadku organizacji z umową Enterprise subskrypcji platformy Azure, wykonaj czterech poziomu hierarchii:

1. administrator przedsiębiorstwa rejestracji

2. administratorem działu

3. Właściciel konta

4. Administrator usługi

Ta hierarchia reguluje następujące czynności:

- Relacja rozliczeń.

- Administracja konta.

- Dostęp do zasobów za pomocą RBAC.

- Granice:

  - Użycie i rozliczenia (oparte na numery oferta karta szybkość)

  - Limity

  - Sieć wirtualna

- Dołączanie do usługi Azure Active Directory (Azure AD). Jedno wystąpienie usługi Azure AD może być skojarzony z wielu subskrypcji.

- Skojarzenie z konta organizacji rejestracji.

### <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

[RBAC](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) umożliwia zarządzanie dostępem szczegółowe zasobów na platformie Azure. Za pomocą RBAC, można udzielić tylko takiego dostępu użytkownicy muszą wykonać swoje zadania. Firmy powinny skupić się na zapewniając pracownikom dokładne uprawnienia, które są im niezbędne. Za dużo uprawnienia ujawnia konta na ataki. Za mało uprawnienia oznacza, że pracownicy nie można pobrać ich pracować wydajnie. 

Zamiast nadanie każdy nieograniczonych uprawnień w Twojej subskrypcji platformy Azure lub zasobów, można zezwolić tylko pewne akcje. Na przykład umożliwia RBAC let jednego pracownika zarządzania maszynami wirtualnymi w ramach subskrypcji, gdy pracownik zarządza baz danych w tej samej subskrypcji.

Aby udzielić dostępu, należy przypisać role do użytkowników, grup lub aplikacje w określonego zakresu. Zakres przypisania roli może być pojedynczego zasobu, grupy zasobów lub subskrypcji. Rola przypisana w zakresie nadrzędnej również udziela dostępu do podrzędnych w nim zawarte. Na przykład użytkownik z dostępem do grupy zasobów można zarządzać wszystkie zasoby, które zawiera, takie jak witryny sieci Web, maszyn wirtualnych i podsieci. W ramach każdej subskrypcji możesz utworzyć maksymalnie 2000 przypisań ról.

Rola to kolekcja uprawnień i RBAC ma kilka wbudowanych ról. Następujące role wbudowane dotyczą wszystkich typów zasobów:

- **Właściciel** ma pełny dostęp do wszystkich zasobów łącznie z prawem delegować dostęp do innych użytkowników.

- **Współautor** można tworzyć i zarządzania wszystkimi typami zasobów platformy Azure, ale nie może udzielić dostępu do innych użytkowników.

- **Czytnik** można wyświetlić wszystkich zasobów systemu Azure.

Pozostałe role wbudowane na platformie Azure Zezwalaj na zarządzanie określonych zasobów platformy Azure. Na przykład Rola współautora maszyny wirtualnej zezwala użytkownikowi na tworzenie i zarządzanie maszynami wirtualnymi. Listę wbudowanych ról i ich działania, zobacz [wbudowane role RBAC](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

RBAC obsługuje operacje zarządzania zasobami Azure w portalu Azure i interfejsów API usługi Azure Resource Manager. W większości przypadków RBAC nie można autoryzować poziom danych operacji dla zasobów platformy Azure. Informacje, jak RBAC zostanie rozszerzone na operacje na danych, zobacz [poznać definicje ról](https://docs.microsoft.com/en-us/azure/role-based-access-control/role-definitions).

Jeśli wbudowane role nie spełniają potrzeb określonym dostępu, możesz [utworzyć niestandardową rolę](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). Podobnie jak role wbudowane role niestandardowe można przypisać do użytkowników, grup i aplikacji w subskrypcji, grupy zasobów i zakres zasobów. Można tworzyć role niestandardowe przy użyciu [programu Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell), [interfejsu wiersza polecenia Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)i [interfejsu API REST](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest).

### <a name="resource-management"></a>Zarządzanie zasobami

Platforma Azure oferuje dwa modele wdrażania: [klasycznego](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) i Menedżera zasobów Azure.

W klasycznym modelu każdy zasób istnieje niezależnie. Nie istnieje sposób do grupowania powiązanych zasobów. Należy ręcznie śledzić zasobów, do których się rozwiązania lub aplikacji i pamiętaj, aby zarządzać nimi w skoordynowany sposób podejście. Podstawową jednostką zarządzania jest subskrypcji. Trudno jest podział zasobów w ramach subskrypcji, co prowadzi do powstania dużej liczby subskrypcji.

Model wdrażania usługi Resource Manager zawiera pojęcie [grupy zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). Grupa zasobów to kontener dla zasobów mających wspólny cykl życia. Może obejmować wszystkich zasobów dla rozwiązania lub tylko tych zasobów, które mają być zarządzane jako grupa. Użytkownik decyduje o sposobie przydziału zasobów do grup zasobów pod kątem tego, co jest najbardziej odpowiednie dla danej organizacji.

Model wdrażania przy użyciu usługi Resource Manager zapewnia kilka korzyści:

- Możliwość grupowego wdrożenia i monitorowania wszystkich usług w ramach rozwiązania oraz zarządzania nimi (zamiast obsługiwania usług pojedynczo).

- Można wielokrotnie wdrażać rozwiązania przez cały cykl życia i mieć pewność, że zasoby są wdrażane w spójnym stanie.

- Możliwość stosowania kontroli dostępu do wszystkich zasobów w grupie zasobów. Te zasady są stosowane automatycznie po dodaniu nowych zasobów do grupy zasobów.

- Możliwość dodawania tagów do zasobów w celu logicznego uporządkowania wszystkich zasobów w ramach subskrypcji.

- Możliwość definiowania infrastruktury rozwiązania za pomocą formatu JavaScript Object Notation (JSON). Plik JSON jest nazywany szablonem usługi Resource Manager.

- Możliwość definiowania zależności między zasobami, dlatego są one wdrożone w odpowiedniej kolejności.

![Resource Manager](./media/governance-in-azure/security-governance-in-azure-fig4.png)

Aby uzyskać zalecenia dotyczące szablonów, zobacz [Best practices for creating Azure Resource Manager templates](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) (Najlepsze rozwiązania dotyczące tworzenia szablonów usługi Azure Resource Manager).

Usługa Azure Resource Manager analizuje zależności, aby pomóc w zapewnieniu, że zasoby są tworzone w odpowiedniej kolejności. Jeśli jeden zasób opiera się na wartości z innym zasobem (na przykład maszynę wirtualną wymagające konta magazynu dla dysków), możesz [ustawić zależność](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies) w szablonie.

Szablonu można także używać w celu aktualizacji infrastruktury. Można na przykład dodać zasób do rozwiązania lub dodać reguły konfiguracji dla już wdrożonych zasobów. Jeśli szablon służy do tworzenia zasobu, ale ten zasób już istnieje, Resource Manager przeprowadzi aktualizację, zamiast tworzyć nowy zasób. Resource Manager zaktualizuje istniejący zasób stanu, tak jak nowego.

Resource Manager zapewnia rozszerzenia dla scenariuszy, jeśli potrzebujesz więcej operacji, takich jak Instalacja oprogramowania, które nie są uwzględnione w konfiguracji.

### <a name="resource-tracking"></a>Śledzenie zasobów

Jak użytkownicy w Twojej organizacji dodać zasoby do subskrypcji, staje się on ważniejsze do skojarzenia z odpowiedniego działu, klientów i środowisko zasobów. Możesz dołączyć metadanych do zasobów za pomocą [tagi](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags). Tagi są używane do udostępniania informacji na temat zasobu lub właściciela. Znaczniki umożliwiają nie tylko agregacji i grupy zasobów na kilka sposobów, ale również użyć tych danych na potrzeby obciążeń zwrotnych.

Użyj tagów w przypadku złożonych kolekcji grup zasobów i zasobów, a trzeba uporządkować w taki sposób jak najwygodniejszy do Ciebie. Na przykład można oznaczyć zasoby, które pełnią podobną rolę w organizacji lub należących do tego samego działu.

Bez użycia tagów użytkownicy w organizacji można utworzyć wiele zasobów, które mogą być trudne do później identyfikację i zarządzanie nimi. Na przykład możesz usunąć wszystkie zasoby w projekcie. Jeśli te zasoby nie są oznaczone tagami tego projektu, użytkownik musi je znaleźć ręcznie. Tagowanie może być istotnym sposobem na zredukowanie niepotrzebnych kosztów w ramach subskrypcji.

Zasoby nie muszą znajdować się w tej samej grupie zasobów, aby udostępnić tag. Możesz utworzyć własne taksonomii tag, aby upewnić się, że wszyscy użytkownicy w organizacji używać typowych tagów zamiast przypadkowo stosowanie nieco inne tagów (na przykład "Wydział" zamiast "dział").

Zasady zasobów umożliwiają tworzenie standardowych zasad dla Twojej organizacji. Można utworzyć zasady, aby upewnić się, że zasoby są oznaczane odpowiednie wartości.

Oznakowane zasoby można również wyświetlić za pośrednictwem witryny Azure Portal. [Raport użycia](https://docs.microsoft.com/azure/billing/billing-understand-your-bill) dla Twoja subskrypcja obejmuje tag nazwy i wartości, więc można przerwać limit koszty według znaczników.

Aby uzyskać więcej informacji na temat tagów, zobacz [rozliczeń znaczniki inicjatywy zasad](../azure-policy/scripts/billing-tags-policy-init.md).

Tagi mają następujące ograniczenia:

- Każdy zasób lub grupa zasobów może mieć co najwyżej 15 pary klucz wartość tagu. To ograniczenie dotyczy tylko tagów stosowanych bezpośrednio do grupy zasobów lub zasobu. Grupa zasobów może zawierać wiele zasobów, że mieć 15 pary klucz wartość tagu.

- Nazwa tagu jest ograniczona do 512 znaków.

- Wartość tagu jest ograniczona do 256 znaków.

- Tagi zastosowane do grupy zasobów nie są dziedziczone przez zasoby należące do tej grupy.

Jeśli masz więcej niż 15 wartości, które należy skojarzyć z zasobem, użyj ciągu JSON jako wartości tagu. Ciąg JSON może zawierać wiele wartości, które są stosowane do klucza jeden tag.

#### <a name="tags-for-billing"></a>Tagi dla rozliczeń

Znaczniki umożliwiają grupowania danych rozliczeń. Na przykład jeśli korzystasz z wieloma maszynami wirtualnymi w różnych organizacjach, za pomocą tagów do użycia grup Centrum kosztów. Umożliwia także tagi kategoryzowania koszty przez środowisko uruchomieniowe, takich jak rozliczeń użycia dla maszyn wirtualnych w środowisku produkcyjnym.

Można pobrać informacji na temat tagów za pomocą [użycia zasobów platformy Azure i interfejsów API RateCard](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview) lub użycia pliku wartości rozdzielanych przecinkami (CSV). Pobierz plik użycia z [portalu konta usługi Azure](https://account.windowsazure.com/) lub [EA portal](https://ea.azure.com/).

Aby uzyskać więcej informacji na temat programowy dostęp do informacji dotyczących rozliczeń, zobacz [uzyskać wgląd w Microsoft Azure użycia zasobów](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview). Dla operacji interfejsu API REST, zobacz [dokumentacja interfejsu API REST rozliczenia Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Po pobraniu użycia woluminów CSV dla usług, które obsługują tagów z rozliczeniami znaczniki są wyświetlane w kolumnie tagów.

### <a name="critical-resource-controls"></a>Formanty krytycznego zasobu

Jak organizacji dodaje podstawowe usługi do subskrypcji, staje się bardziej należy się upewnić, że te usługi są dostępne w celu uniknięcia przerw w działaniu biznesowych. [Blokowania zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) pozwalają ograniczyć operacje na zasobach wysokiej wartości, gdzie modyfikowania lub usuwania ich miałoby znaczący wpływ na aplikacje lub infrastruktury chmury. Blokady można stosować do subskrypcji, grupy zasobów lub zasobów. Zazwyczaj należy zastosować blokady do podstawowych zasobów, takich jak sieci wirtualnych, bramy i kont magazynu.

Blokowania zasobów obsługuje obecnie dwie wartości: **CanNotDelete** i **tylko do odczytu**. **CanNotDelete** oznacza, że użytkownikom (odpowiednie prawa) można nadal odczytywać lub zmodyfikuj zasobu, ale nie można go usunąć. **Tylko do odczytu** oznacza, że autoryzowani użytkownicy nie można usunąć ani zmodyfikować zasobu.

Blokowania zasobów mają zastosowanie tylko do operacji, które pojawiają się w płaszczyźnie zarządzania, która składa się z operacji wysyłane do <https://management.azure.com>. Blokad nie Ograniczaj, jak zasoby wykonywanie własnych funkcji. Zmiany zasobu jest ograniczony, ale operacje zasobów nie są ograniczone. Na przykład **tylko do odczytu** blokady w bazie danych SQL pozwala usuwanie i modyfikowanie bazy danych, ale nie ogranicza możliwość tworzenia, aktualizowania lub usuwania danych w bazie danych.

Stosowanie **tylko do odczytu** może prowadzić do nieoczekiwanych wyników, ponieważ niektóre operacje, które się wydawać odczytu operacje wymagają dodatkowych czynności. Na przykład wprowadzenie **tylko do odczytu** blokady na koncie magazynu uniemożliwia wyświetlanie kluczy wszystkich użytkowników. Operacja wyświetlanie kluczy jest obsługiwana za pomocą żądania POST klucze zwracane nie są dostępne dla operacji zapisu.

![Formanty krytycznego zasobu](./media/governance-in-azure/security-governance-in-azure-fig5.png)

Innym przykładem umieszczenie **tylko do odczytu** blokada na zasób usługi Azure App Service uniemożliwia wyświetlanie plików dla zasobu, ponieważ dostęp do zapisu wymaga interakcji Eksploratora serwera w usłudze Visual Studio.

W przeciwieństwie do kontroli dostępu opartej na rolach blokady zarządzania służy do stosowania ograniczenia we wszystkich użytkowników i ról. Aby dowiedzieć się więcej na temat ustawiania uprawnień, zobacz [zarządzanie dostępem przy użyciu RBAC i portalu Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

Po zastosowaniu blokady w zakresie nadrzędnym, wszystkie zasoby w ramach tego zakresu dziedziczą tego samego blokady. Nawet zasoby, które później zostaną dodane dziedziczą blokady z obiektu nadrzędnego. Najbardziej restrykcyjne blokady w dziedziczenia ma pierwszeństwo.

Aby utworzyć lub usunąć blokady zarządzania, musi mieć dostęp do Microsoft.Authorization/ lub Microsoft.Authorization/locks/ akcji. Wbudowanych ról tylko właściciel i Administrator dostępu użytkowników są przyznawane te akcje.

### <a name="api-access-to-billing-information"></a>Interfejs API dostęp do informacji dotyczących rozliczeń

Za pomocą interfejsów API usługi Azure rozliczeń pobierania danych użycia i zasobów do narzędziami analizy danych preferowany. API RateCard i użycia zasobów Azure może pomóc dokładnie przewidzieć i zarządzanie nimi kosztów. Interfejsy API są zaimplementowane jako dostawca zasobów i częścią rodziny interfejsach API udostępnianych przez usługi Azure Resource Manager.

#### <a name="resource-usage-api-preview"></a>Użycie zasobów interfejsu API (wersja zapoznawcza)

Użyj platformy Azure [API użycia zasobów](https://msdn.microsoft.com/library/azure/mt219003) Twoje dane szacowany wykorzystania platformy Azure. Interfejs API zawiera:

- **RBAC**: Skonfiguruj zasady dostępu na [portalu Azure](https://portal.azure.com/) lub za pomocą [poleceń cmdlet programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) Aby określić użytkowników lub aplikacji, które można uzyskać dostępu do danych użycia subskrypcji. Obiekty wywołujące należy użyć standardowego tokeny usługi Azure Active Directory do uwierzytelniania. Dodaj element wywołujący do albo czytnika rozliczeń, czytnika, właściciela lub współautora roli do uzyskania dostępu do danych użycia dla określonej subskrypcji platformy Azure.

- **Godzinową lub dzienną agregację**: wywołań można określić, czy chcą, aby ich danych użycia usługi Azure w przyrostach godzinowo lub dziennie. Wartość domyślna to codziennie.

- **Wystąpienie metadanych (w tym tagi zasobów)**: Pobierz szczegóły na poziomie wystąpienia takich jak pełny identyfikator URI zasobu (/subscriptions/ {identyfikator subskrypcji} /..), informacje o grupie zasobów i tagi zasobów. Te metadane pomaga w sposób niejednoznaczny i programowo przydzielenie użycia tagów przypadków użycia, takich jak między ładowania.

- **Metadane zasobu**: szczegóły zasobu, takie jak nazwa licznika, kategorii licznika, podkategorii miernika, jednostki i region zapewniają wywołującego lepiej zrozumieć co został wykorzystany. Również pracujemy, aby były wyrównane terminologii metadanych zasobów w portalu Azure, Azure użycia woluminów CSV, EA rozliczeń CSV i inne środowiska publicznych, aby pomóc korelowania danych środowiska.

- **Użycia dla wszystkich oferują typy**: danych użycia jest dostępna dla wszystkich oferują typów, w tym płatność za rzeczywiste użycie, MSDN, zobowiązania pieniężnego, środki pieniężne i atrybutów Rozszerzonych.

#### <a name="resource-ratecard-api"></a>RateCard zasobu interfejsu API

Użyj interfejsu API RateCard zasobów Azure, aby uzyskać listę dostępnych zasobów platformy Azure i szacowany informacje o cenach dla każdego. Interfejs API zawiera:

- **RBAC**: Konfigurowanie zasad dostępu w portalu Azure lub za pomocą poleceń cmdlet programu Azure PowerShell, aby określić użytkowników lub aplikacji, które można uzyskać dostępu do danych RateCard. Obiekty wywołujące należy użyć standardowego tokeny usługi Azure Active Directory do uwierzytelniania. Dodaj element wywołujący do roli czytnika, właścicielem lub współautorem uzyskać dostęp do danych użycia dla określonej subskrypcji platformy Azure.

- **Obsługa płatność za rzeczywiste użycie, MSDN, zobowiązań i środki pieniężne oferty (ale nie EA)**: ten interfejs API zawiera informacje o szybkości poziomu oferty Azure. Wywołujący ten interfejs API należy przekazać informacje oferty, aby uzyskać szczegóły dotyczące zasobów i szybkości. Administrator przedsiębiorstwa nie jest obecnie obsługiwane ponieważ EA oferty zostały dostosowane stawki dla rejestracji. 

#### <a name="scenarios"></a>Scenariusze

Kombinacja użycia i interfejsów API RateCard umożliwia tych scenariuszy:

- **Zrozumienie Azure spędzają w miesiącu**: użyj kombinacji użycia oraz RateCard interfejsów API, aby uzyskać lepszą wgląd w chmurze wydatków w miesiącu. Można analizować co godzinę i codzienne oszacowania użycia i opłat.

- **Konfigurowanie alertów**: umożliwia użycie i interfejsów API RateCard zużycie szacowany chmury i opłat i skonfigurować alerty oparte na zasobach lub pieniężne na podstawie.

- **Przewidywanie rachunku**: Get szacowane zużycie i w chmurze wydatków i zastosować algorytmów uczenia maszynowego na potrzeby prognozowania BOM będzie końca cyklu rozliczeniowego.

- **Wykonaj wstępne zużycia koszt analizy**: przewidywanie, ile rachunku byłby przez użycie oczekiwanego przenoszenia obciążeń na platformie Azure za pomocą interfejsu API RateCard. Jeśli masz istniejące obciążenia w innych chmur lub chmur prywatnych, użycie z platformy Azure można również mapować spędzają szybkości, aby uzyskać lepszą oszacowanie Azure. Ta Szacowana daje możliwość przestawnego na ofertę i porównania pomiędzy typami inną ofertę poza płatność za rzeczywiste użycie, takich jak zobowiązań i środki pieniężne.

- **Wykonywanie analizy warunkowej**: można określić, czy jest bardziej ekonomiczne rozwiązanie do uruchamiania obciążeń w innym regionie lub w innej konfiguracji zasobów platformy Azure. Koszty zasobów platformy Azure mogą się różnić w zależności na region platformy Azure, którego używasz. Można również określić, czy innego typu oferty Azure zapewnia większą szybkość na zasobów platformy Azure.

### <a name="networking-controls"></a>Formanty sieci

Dostęp do zasobów można wewnętrznych (w ramach sieci) lub zewnętrznych (za pośrednictwem Internetu). To proste dla użytkowników w organizacji na przypadkowo umieść zasobów w miejscu niewłaściwy i potencjalnie otwórz je, aby nieautoryzowanym dostępem. Podobnie jak w przypadku urządzeń lokalnych, przedsiębiorstw, należy dodać kontrole w celu zapewnienia, że Azure użytkowników podejmować decyzje prawo.

![Formanty sieci](./media/governance-in-azure/security-governance-in-azure-fig6.png)

Do zarządzania subskrypcją w następujących zasobach core udostępniono podstawowe kontroli dostępu.

#### <a name="network-connectivity"></a>Połączenie sieciowe

[Sieci wirtualne](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) obiektów kontenera podsieci. Chociaż nie niezbędne, sieci wirtualnej jest często używane do łączenia aplikacji do wewnętrznych zasobów firmy. Usługa Azure Virtual Network umożliwia bezpieczne łączenie zasobów platformy Azure z sobą z sieciami wirtualnymi.

Sieć wirtualna jest odzwierciedla w chmurze Twoją sieć. Sieć wirtualna jest logiczną izolacją chmury Azure w wersji dedykowanej do subskrypcji. Sieci wirtualne można również łączyć się z siecią lokalną.

Poniżej przedstawiono funkcje dla sieci wirtualnych platformy Azure:

- **Izolacja**: sieci wirtualne są odizolowane od siebie. Można utworzyć oddzielne sieci wirtualnych umożliwiające tworzenie, testowanie i produkcji, które używają tego samego bloków adresów CIDR. Z drugiej strony można utworzyć wiele sieci wirtualnych, które używają różnych bloków adresów CIDR i połączyć ze sobą sieci. Sieć wirtualną można podzielić na wiele podsieci. Platforma Azure udostępnia rozpoznawania nazw wewnętrznych dla maszyn wirtualnych i usług Azure Cloud Services wystąpienia roli, które są podłączone do sieci wirtualnej. Opcjonalnie można skonfigurować sieć wirtualną do użycia zamiast rozpoznawania nazw wewnętrznych Azure własne serwery DNS.

- **Połączenie z Internetem**: Azure wszystkich maszyn wirtualnych i wystąpień roli usługi w chmurze, które są podłączone do sieci wirtualnej mają dostęp do Internetu, domyślnie. Można również włączyć dostęp przychodzący do określonych zasobów, zgodnie z potrzebami.

- **Łączność zasobów platformy Azure**: zasobów platformy Azure, takich jak usługi w chmurze i maszyn wirtualnych, można połączyć do tej samej sieci wirtualnej. Zasoby można połączyć ze sobą za pośrednictwem prywatnych adresów IP, nawet jeśli znajdują się w różnych podsieciach. Platforma Azure udostępnia domyślny routing między podsieciami, sieci wirtualnych i sieci lokalnej, więc nie trzeba konfigurować i zarządzać nimi trasy.

- **Połączenie wirtualnej sieci**: sieci wirtualne można połączyć ze sobą. Zasoby, które są podłączone do żadnej sieci wirtualnej może następnie komunikować się z dowolnego zasobu w innych sieci wirtualnej.

- **Połączenie lokalne**: możesz nawiązać połączenie sieci wirtualnych sieci lokalnej za pośrednictwem sieci prywatnej połączeń między siecią a Azure lub za pośrednictwem połączenia lokacja lokacja wirtualnej sieci prywatnej (VPN) przez internet.

- **Filtrowanie ruchu**: można filtrować ruchu sieciowego (przychodzący i wychodzący) dla maszyn wirtualnych i usług w chmurze przez źródłowy adres IP i port docelowy adres IP i portu i protokołu.

- **Routing**: Opcjonalnie można zastąpić domyślne Azure routingu, konfigurując własnego trasy lub za pomocą trasy protokołu BGP za pośrednictwem bramy sieci.

#### <a name="network-access-controls"></a>Kontrolę dostępu do sieci

[Sieciowe grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG) są takie jak zapory i podaj reguły jak zasobu można "rozmawiać" za pośrednictwem sieci. Zapewniają kontrolę nad jak podsieć (lub maszyny wirtualnej) można łączyć z Internetem lub innych podsieci w tej samej sieci wirtualnej.

Grupa zabezpieczeń sieci zawiera listę reguł zabezpieczeń, które akceptować lub odrzucać ruch sieciowy do zasoby podłączone do sieci wirtualnych platformy Azure. Grupy NSG można kojarzyć z podsieci, do poszczególnych maszyn wirtualnych (klasyczne) lub do interfejsów sieciowych poszczególnych (NIC) dołączonych do maszyn wirtualnych (Resource Manager).

Grupa NSG jest skojarzona z podsiecią, reguły dotyczą wszystkie zasoby podłączone do podsieci. Można bardziej ograniczyć ruch przez skojarzenie grupy NSG do maszyny Wirtualnej lub karty sieciowej.

## <a name="security-and-compliance-with-organizational-standards"></a>Bezpieczeństwo i zgodność ze standardami w organizacji

Każda firma ma inne potrzeby i będzie czerpanie różne korzyści z rozwiązań w chmurze. Klienci wszelkiego rodzaju wątpliwości tego samego podstawowego przenoszeniu do chmury. Klienci szukają od dostawców w chmurze jest:

- **Zabezpieczanie danych**: liderów IT potwierdzić, że chmura może zapewniać bezpieczeństwo danych i kontrola administracyjna. Ale są one nadal danych, czy migracja do chmury pozostawi je bardziej narażony na hakerów niż ich bieżącego rozwiązania wewnętrznych.

- **Zachowania prywatności danych**: usługi w chmurze podnieść wyzwania unikatowy prywatności. Jak firm wyglądać chmury w celu zapisania na koszty infrastruktury i zwiększyć ich elastyczność, one również martwić się o utracie kontroli nad którym są przechowywane ich dane, kto uzyskuje dostęp do jego i jak jest używany.

- **Przekaż nam kontroli**: również w firmach skorzystać z chmury, aby wdrożyć więcej innowacyjne rozwiązania, są one danych o utracie kontroli nad ich danych. Najnowsze informacje agencji rządowych Stanów Zjednoczonych, dostęp do danych klienta za pośrednictwem czynniki prawne i pozaprawnych oznacza, należy niektórych dyrektorzy działu informatyki ostrożność przechowywanie danych w chmurze.

- **Podwyższ poziom przezroczystości**: decydentów firm zrozumieć znaczenie bezpieczeństwa, ochrony prywatności i kontroli. Ale chcą także możliwość niezależnie Sprawdź, jak ich przechowywanych danych, uzyskuje się dostęp i zabezpieczonych.

- **Obsługa zgodności**: jak firm rozwinąć ich korzystanie z technologii chmury, złożoność i zakres norm i przepisów nadal podlegać ewolucji. Firmy muszą znać spełnienia standardów ich zgodności.

## <a name="security-configuration-for-monitoring-logging-and-auditing"></a>Konfiguracja zabezpieczeń monitorowanie, rejestrowanie i inspekcja

Subskrybenci platformy Azure mogą zarządzać środowiskami chmury przy użyciu wielu urządzeń. Urządzenia te mogą obejmować stacjami roboczymi do zarządzania, komputerami deweloperów i urządzeń nawet uprzywilejowanych użytkowników końcowych, które mają uprawnienia specyficzne dla zadania. 

W niektórych przypadkach funkcje administracyjne są wykonywane za pośrednictwem konsoli opartych na sieci web, takich jak portalu Azure. W innych przypadkach mogą istnieć bezpośrednie połączenia do platformy Azure z systemów lokalnych za pośrednictwem sieci VPN, usług terminalowych, protokołów aplikacji klienckich lub (programowo) interfejsu API zarządzania usługi Azure (SMAPI). Ponadto punkty końcowe klienta może być albo domeny przyłączone lub odizolowane i niezarządzane, takich jak tablety lub smartfony.

To zróżnicowanie może dodać znaczące zagrożenie do wdrożenia w chmurze. Może być trudne do zarządzania, śledzenie i inspekcję czynności administracyjnych. To zróżnicowanie może również wprowadzać zagrożenia bezpieczeństwa związane z nieuregulowanym dostępem do punktów końcowych klienta, które są używane do zarządzania usługami w chmurze. Użycie ogólnych lub osobistych stacji roboczych do opracowywania infrastruktury i zarządzania nią powoduje, że zagrożenia mogą nadchodzić z nieprzewidywalnych kierunków, na przykład podczas przeglądania sieci Web (na przykład ataki za pośrednictwem używanych witryn) lub korzystania z poczty e-mail (na przykład techniki socjotechniczne i wyłudzanie informacji).

Monitorowanie, rejestrowanie i inspekcja stanowią podstawę dla śledzenia i zrozumienia czynności administracyjnych. Inspekcję wszystkich akcji szczegółowy może nie być możliwe ze względu na ilość generowanych danych. Jednak najlepszym rozwiązaniem jest inspekcja skuteczności zasad zarządzania.

Zarządzanie zabezpieczeń platformy Azure z obiektów zasad grupy usługi Azure Active Directory Domain Services (AD DS), mogą pomóc Ci kontrolować wszystkich administratorów interfejsów systemu Windows, takich jak udostępnianie plików. Dołącz stacjami roboczymi do zarządzania monitorowanie, rejestrowanie i inspekcji. Śledź dostęp i używanie przez wszystkich administratorów i deweloperów.

### <a name="azure-security-center"></a>Azure Security Center

[Centrum zabezpieczeń Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) centralnej Wyświetla stan zabezpieczeń zasobów w subskrypcji. Zawiera zalecenia, które ułatwiają ochronę zasobów ze złamanymi zabezpieczeniami. Można go włączyć bardziej szczegółowe zasady — na przykład stosowania zasad do określonych grup zasobów, które umożliwiają firmie dostosować ich postawie ryzyko, które są one adresowania.

![Azure Security Center](./media/governance-in-azure/security-governance-in-azure-fig7.png)

Centrum zabezpieczeń zapewnia zabezpieczenia zintegrowane monitorowanie i zarządzanie zasadami subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które mogłyby w przeciwnym razie pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań zabezpieczających. Po włączeniu [zasady zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-policies) dla zasobów subskrypcji Centrum zabezpieczeń analizuje zabezpieczenia zasobów, aby zidentyfikować potencjalne luki. Informacje o konfiguracji sieci są dostępne natychmiast.

Centrum zabezpieczeń Azure reprezentuje kombinację najlepszych rozwiązań analizy i zabezpieczeń Zarządzanie zasadami dla wszystkich zasobów w ramach subskrypcji platformy Azure. Umożliwia on zespoły zabezpieczeń i oficerowie ryzyka do zapobiegania, wykrywania i reagowania na zagrożenia bezpieczeństwa, ponieważ automatycznie zbiera i analizuje dane dotyczące zabezpieczeń z zasobów platformy Azure, sieci i rozwiązań partnerskich, takich jak programy ochrony przed złośliwym oprogramowaniem i zapory.

Ponadto Centrum zabezpieczeń Azure stosuje zaawansowane metody analizy, w tym uczenie maszynowe i analizę behawioralną. Używa globalnej analizy zagrożeń ze swoich produktów i usług firmy Microsoft cyfrowego ds. przestępstw jednostki (DCU) MSRC Microsoft Security Response Center (), a źródła zewnętrznego. Możesz zastosować [ładu zabezpieczeń](https://www.credera.com/blog/credera-site/azure-governance-part-4-other-tools-in-the-toolbox/) szeroko na poziomie subskrypcji. Alternatywnie można jej ograniczyć do określonych wymagań i zastosować je do pojedynczych zasobów za pośrednictwem definicji zasad.

Na koniec Centrum zabezpieczeń Azure analizuje kondycja zabezpieczeń zasobów na podstawie tych zasad i używa tych informacji w celu zapewnienia interesującego pulpitów nawigacyjnych i alerty dla zdarzenia, takie jak wykrywania złośliwego oprogramowania lub złośliwe połączenia IP prób. Aby uzyskać więcej informacji dotyczących sposobu stosowania zaleceń, zobacz [wdrażanie zaleceń dotyczących zabezpieczeń w Centrum zabezpieczeń Azure](https://docs.microsoft.com/azure/security-center/security-center-recommendations).

Centrum zabezpieczeń Azure monitorowanie następujących zasobów platformy Azure:

- Maszynach wirtualnych (VM) (w tym usługi w chmurze)

- Sieci wirtualne

- Bazy danych SQL

- Partnerskich rozwiązań zintegrowanych z subskrypcją platformy Azure, takich jak Zapora aplikacji sieci web na maszynach wirtualnych i na [środowiska usługi aplikacji](https://docs.microsoft.com/azure/app-service/app-service-app-service-environments-readme)

Jeśli najpierw przejść do Centrum zabezpieczeń zbieranie danych jest włączone na wszystkich maszynach wirtualnych w ramach subskrypcji. Firma Microsoft zaleca, aby zachować włączone zbieranie danych, ale możesz [ją wyłączyć](https://docs.microsoft.com/azure/security-center/security-center-faq) w zasadach Centrum zabezpieczeń.

### <a name="log-analytics"></a>Log Analytics

Analiza dzienników Azure oprogramowania rozwoju i usługi zespołu ochrony informacji i [program ładu](https://github.com/Microsoft/azure-docs/blob/master/articles/log-analytics/log-analytics-security.md) obsługuje jej wymagania biznesowe. Zgodne przepisom eksportowym obowiązującym zgodnie z opisem w [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) i [zgodności Centrum zaufania Microsoft](https://www.microsoft.com/TrustCenter/Compliance/default.aspx). Jak analizy dzienników ustanawia wymagania dotyczące zabezpieczeń, identyfikuje opcji zabezpieczeń i zarządza i monitory ryzyka opisana istnieje. Co rok zespół przegląda zasady, normy, procedur i wytycznych.

Każdego członka zespołu programowanie analizy dzienników odbiera szkolenia formalnego aplikacji w zakresie zabezpieczeń. System kontroli wersji pomaga w ochronie poszczególnych projektów rozwoju oprogramowania.

Firma Microsoft ma zabezpieczeń i zgodności zespołu nadzoruje i ocenia wszystkich usług firmy Microsoft. Zabezpieczenia informatyków tworzą zespołu, a nie są one powiązane z działów engineering, które opracowanie analizy dzienników. Biuro zabezpieczeń ma swoje własne łańcuch zarządzania. Prowadzą niezależnych ocen produktów i usług w celu zapewnienia bezpieczeństwa i zgodności.

Do podstawowych funkcji analizy dzienników są udostępniane przez zestaw usług, które działają na platformie Azure. Każda usługa udostępnia określoną funkcję zarządzania. Możesz łączyć usługi, aby realizować różne scenariusze zarządzania.

![Usługi platformy Azure do zarządzania](./media/governance-in-azure/security-governance-in-azure-fig9.JPG)

Te usługi zarządzania zostały zaprojektowane w chmurze. Są one całkowicie hostowane na platformie Azure, nie wymagają one wdrażania i zarządzania zasobami lokalnymi. Konfiguracja jest minimalny, i może być uruchomiona w ciągu kilku minut.

Umieść agenta na każdym komputerze z systemem Windows lub Linux, w centrum danych, a następnie wysyła dane do analizy dzienników. Istnieje można analizować oraz inne dane zbierane z usługi w chmurze lub lokalnie. Użyj usługi Kopia zapasowa Azure i usługi Azure Site Recovery mógł korzystać z chmury na potrzeby tworzenia kopii zapasowej i wysoka dostępność dla zasobów lokalnych.

![Usługi zarządzania na pulpicie nawigacyjnym platformy Azure](./media/governance-in-azure/security-governance-in-azure-fig8.png)

Elementy Runbook w chmurze nie można zwykle uzyskują dostęp do zasobów lokalnych, ale można zainstalować agenta na co najmniej jeden komputer, które będą obsługiwać elementów runbook w centrum danych. Po uruchomieniu elementu runbook, należy określić, czy go uruchomić w chmurze lub lokalne procesu roboczego.

Różnych rozwiązaniach dostępnych firmy Microsoft i partnerów, można dodać do subskrypcji platformy Azure, aby zwiększyć wartość inwestycji w usługę Analiza dzienników. Na przykład system Azure oferuje [rozwiązań do zarządzania](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions)--opakowane zestawów logiki implementujących scenariusz zarządzania przy użyciu co najmniej jednej usługi zarządzania.

![Galeria rozwiązań do zarządzania na platformie Azure](./media/governance-in-azure/security-governance-in-azure-fig10.png)

Przez partnera możesz utworzyć rozwiązań do obsługi aplikacji i usług i udostępniania ich użytkownikom za pośrednictwem portalu Azure Marketplace lub szybkiego startu szablonów.

## <a name="performance-alerting-and-monitoring"></a>Monitorowanie i alerty wydajności

### <a name="alerting"></a>Generowanie alertów

Alerty są metody monitorowania metryki zasobów platformy Azure, zdarzeń i dzienniki. Powiadom ich, należy po spełnieniu warunku, który został określony.

Alerty są dostępne w usługach, w tym:

- **Azure Application Insights**: alerty testu i metryki sieci web umożliwia. Aby uzyskać więcej informacji, zobacz [ustawić alertów w usłudze Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) i [monitorowanie dostępności i czas odpowiedzi dla wszystkich witryn sieci Web](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability).

- **Zaloguj się Analytics**: włącza routing aktywności i dzienników diagnostycznych do analizy dzienników. Umożliwia on metryki, dziennika i inne typy alertów. Aby uzyskać więcej informacji, zobacz [alertów w analizy dzienników](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts).

- **Azure Monitor**: włącza alerty oparte na wartości metryki i zdarzenia dziennika aktywności. Można użyć [interfejsu API REST Monitor Azure](https://msdn.microsoft.com/library/dn931943.aspx) do zarządzania alertami. Aby uzyskać więcej informacji, zobacz [przy użyciu portalu Azure, programu PowerShell lub interfejsu wiersza polecenia, aby tworzyć alerty](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-alerts-portal).

### <a name="monitoring"></a>Monitorowanie

Problemy z wydajnością w Twojej aplikacji w chmurze mogą wpłynąć na prowadzoną działalność. Z wielu powiązanych elementów i częste wersjach degradations może nastąpić w dowolnej chwili. I w przypadku tworzenia aplikacji, użytkowników, zazwyczaj odnajdywanie problemów, które nie udało się znaleźć podczas testowania. Należy od razu wiedzieć o tych problemów i narzędzia do diagnozowania i ich rozwiązywania.

Istnieje szereg narzędzia do monitorowania usługi i aplikacje platformy Azure. Niektóre z ich funkcji nakładają się. Jest to spowodowane częściowo rozmycia między rozwoju i działania aplikacji.

Poniżej przedstawiono główne narzędzia:

- **Azure Monitor** jest podstawowym narzędziem do monitorowania usługi działające na platformie Azure. Udostępnia poziomie infrastruktury danych o przepływności usługi i otaczającego środowiska. Jeśli zarządzasz wszystkich aplikacji w usłudze Azure i podjęcie decyzji o skalować w górę lub w dół zasobów Azure Monitor może pomóc start.

- **Usługa Application Insights** mogą być używane do tworzenia aplikacji oraz jako rozwiązanie monitorujące produkcji. Działa on instalując pakiet w aplikacji, więc zapewnia bardziej wewnętrzny widok co się dzieje. Jego danych obejmuje czas reakcji zależności i śladów wyjątek, migawki i wykonywania profilów debugowania. Zawiera narzędzia do analizowania tego telemetrii zarówno do debugowania aplikacji i pomagające zrozumieć, co robią użytkownicy z nim. Można określić, czy kolekcji czas odpowiedzi jest z powodu coś w aplikacji lub niektóre zewnętrznego problemem resourcing. Jeśli używasz programu Visual Studio i aplikacji jest uszkodzone, możesz przejść bezpośrednio do wiersza problem kodu, można go naprawić.

- **Zaloguj się Analytics** jest przeznaczony dla osób konieczne dostrojenie wydajności i planowanie konserwacji na aplikacje uruchomione w środowisku produkcyjnym. On zbiera i agregują dane z wielu źródeł z opóźnieniem 10 – 15 minut. Przewiduje całościowe rozwiązanie zarządzania Azure, lokalne i innych firm oparte na chmurze infrastruktury (na przykład usług Amazon Web Services). Udostępnia narzędzia do analizowania danych między źródłami, umożliwia złożonych zapytań przez wszystkie dzienniki i może aktywnie alert po wystąpieniu określonego warunku. Można nawet zbieranie danych niestandardowych w centralnym repozytorium, a następnie wykonasz zapytania i wizualizacji danych.

- **System Center Operations Manager** jest do zarządzania i monitorowania instalacji dużych chmury. Być może już znasz go jako narzędzia do zarządzania lokalnymi w systemie Windows Server i funkcji Hyper-V na podstawie chmury, ale można również zintegrować z i zarządzanie aplikacjami platformy Azure. Między innymi może instalować usługi Application Insights na istniejące aplikacje na żywo. Jeśli aplikacja przestanie działać, Operations Manager określa w sekundach.


## <a name="next-steps"></a>Kolejne kroki

- [Best practices for creating Azure Resource Manager templates (Najlepsze rozwiązania dotyczące tworzenia szablonów usługi Azure Resource Manager)](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices)

- [Przykłady stosowania ładu subskrypcji platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-subscription-examples)

- [Microsoft Azure dla instytucji rządowych](https://docs.microsoft.com/azure/azure-government/)

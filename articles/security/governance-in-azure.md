---
title: Zarządzanie na platformie Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat przetwarzania danych usług w chmurze zawierających szeroką gamę wystąpienia obliczeniowe i usług, które można skalować w górę i w dół automatycznie na potrzeby aplikacji lub przedsiębiorstwa.
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
ms.openlocfilehash: a5f323b98fa30d2c4c89fa8fe8e75c1d89089b6e
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895280"
---
# <a name="governance-in-azure"></a>Ład na platformie Azure

Wiemy, że zabezpieczenia są zadania, co w chmurze i jak ważne jest aby znaleźć dokładne i aktualne informacje na temat zabezpieczeń platformy Azure. Najważniejsze przyczyny na potrzeby aplikacji i usług Azure ma korzystać z jego szerokiej gamy narzędzi zabezpieczeń i możliwości. Te narzędzia i funkcje pomocy umożliwiają tworzenie bezpiecznych rozwiązań na bezpiecznej platformie Azure.

Aby lepiej zrozumieć tablicy ładu formanty implementowane w systemie Microsoft Azure z perspektywy programu Microsoft operations i klienta, w tym artykule "Zarządzanie w Azure" jest zapisywany na zapewnić kompleksowe przyjrzeć się ładu Funkcje dostępne w systemie Microsoft Azure.

## <a name="azure-platform"></a>Platforma Azure

Azure to platforma usługi chmury publicznej, która obsługuje szeroki wybór systemów operacyjnych, programowania języków, struktury, narzędzia, baz danych i urządzeń. Kontenery systemu Linux można uruchamiać z integracji dokerskie; Tworzenie aplikacji za pomocą języka JavaScript, Python, .NET, PHP, Java i Node.js; Kompilacja zapleczy dla systemu iOS, Android i Windows urządzeń. Usługi w chmurze publicznej Azure obsługuje te same technologie miliony deweloperów i informatyków już zależne i zaufania.

Podczas tworzenia lub migracji zasobów informatycznych do dostawcy usług chmury publicznej, są zależne możliwości Twojej organizacji do ochrony aplikacji i danych z usług i formanty zapewniają do zarządzania zabezpieczeniami elementów zawartości opartej na chmurze.

Infrastruktury platformy Azure umożliwiają z funkcji aplikacji do obsługi jednocześnie miliony klientów i zapewnia foundation godne zaufania, na którym firmy mogą spełnić ich wymagań dotyczących zabezpieczeń. Ponadto Azure udostępnia wiele opcji dotyczących zabezpieczeń oraz możliwość ich kontroli, dzięki czemu można dostosować zabezpieczeń, aby spełnić unikatowe wymagania danej organizacji wdrożeń.

Ten dokument pomaga zrozumieć, jak możliwości zarządzania Azure można spełnić te wymagania.

## <a name="abstract"></a>Abstrakcyjny

Zarządzanie chmury Microsoft Azure oferuje zintegrowane inspekcji i konsultingowe podejście do sprawdzenia i udzielanie porad organizacji na ich użycie platformy Azure. Zarządzanie chmury Microsoft Azure odwołuje się do procesów decyzyjnych, kryteria i zasady objętego planowania, architektura, przejęcia, wdrożenie i operacji zarządzania chmury obliczeniowej.

Aby utworzyć plan ładu chmury Microsoft Azure, musisz podjąć omówiono osób, procesów i technologii obecnie w miejscu, a następnie tworzą platform, które ułatwiają IT, aby spójnie obsługuje potrzeb biznesowych, zapewniając użytkownikom końcowym elastyczność korzystać z zaawansowanych funkcji programu Microsoft Azure.

W tym dokumencie opisano, jak można uzyskać z podwyższonym poziomem uprawnień poziom zarządzania zasobami IT na platformie Microsoft Azure. Ten dokument ułatwia zrozumienie zabezpieczeń i nadzór nad funkcji wbudowanych w Microsoft Azure.

Poniżej przedstawiono główne zagadnienia ładu omówione w tym dokumencie:

- Implementacja zasad, procesów i procedur zgodnie z harmonogramem cele organizacji.

- Bezpieczeństwo i ciągłe zgodności ze standardami w organizacji

- Monitorowanie i alerty

## <a name="implementation-of-policies-processes-and-procedures"></a>Stosowania zasad, procesów i procedur 

Zarządzanie ustanowił role i obowiązki nadzorują stosowania zasad zabezpieczeń informacji i ciągłości obrębie platformy Azure. Interfejs Microsoft Azure management jest odpowiedzialny za nadzór nad zabezpieczeń i ciągłości rozwiązań w ramach ich odpowiednich zespołów (takie jak stron trzecich) i ułatwienie przestrzegania zasad zabezpieczeń, procesów i standardów.

Poniżej przedstawiono czynniki ewoluował:

- Aprowizacja kont

- Formanty subskrypcji

- Kontroli dostępu na podstawie roli

- Zarządzanie zasobami

- Śledzenie zasobów

- Kontrola krytyczne zasobów

- Interfejs API dostęp do informacji rozliczeń

- Formanty sieci

## <a name="account-provisioning"></a>Aprowizacja kont

Definiowanie hierarchii konta jest ważny krok struktury platformy Azure i usług w obrębie firmy i jest strukturę zarządu core. W przypadku klientów z umowy enterprise agreement klientów można podzielić dodatkowe środowiska do działów, kont, a na końcu subskrypcji.

![Aprowizacja kont](./media/governance-in-azure/security-governance-in-azure-fig1.png)

Jeśli nie ma umowy enterprise agreement, rozważ użycie [Azure tagi](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) do definiowania hierarchii na poziomie subskrypcji. Subskrypcji platformy Azure to podstawowa jednostka, w którym znajdują się wszystkie zasoby. Definiuje również kilka ograniczeń w obrębie platformy Azure, takich jak liczba rdzeni, zasoby itd. Subskrypcje mogą zawierać [grup zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), które zawierają zasoby. [RBAC](https://docs.microsoft.com/azure/api-management/api-management-role-based-access-control) zasady stosowane na te trzy poziomy.

Różni się co przedsiębiorstwa i umożliwia hierarchii przy użyciu tagów Azure w przypadku klientów z systemem innym niż enterprise dużą swobodę w sposób organizowania Azure w ramach firmy. Przed wdrożeniem zasobów na platformie Microsoft Azure, możesz hierarchii i zrozumienie wpływu na rozliczenia, dostęp do zasobów i złożoności.

## <a name="subscription-controls"></a>Formanty subskrypcji

Subskrypcji kontroluje sposób zgłoszone i rozliczane użycia zasobów. Subskrypcje można skonfigurować oddzielne rozliczeń i płatności. Wymienione wcześniej w jednym konta Azure firma Microsoft może mieć wiele subskrypcji. Subskrypcje można ustalić skalę użycia zasobów platformy Azure przez wielu działów w firmie.

Na przykład, jeśli firma ma IT, działów HR i marketingu i działów te mają różnych projektów uruchomiona. Na podstawie użycia zasobów platformy Azure, takich jak maszyn wirtualnych przez każdy dział, one może zostać użyta, odpowiednio. To firma Microsoft może kontrolować Finanse każdego działu.

Subskrypcje platformy Azure nawiązać trzy parametry:

- Identyfikator unikatowy subskrybenta

- Lokalizacja rozliczeń

- Zbiór dostępnych zasobów

Dla poszczególnych Obejmowałoby to jeden identyfikator konta Microsoft, numer karty kredytowej i pełny zestaw usług platformy Azure — mimo że Microsoft wymusza ograniczenia użycia, w zależności od typu subskrypcji.

Hierarchie Azure rejestracji zdefiniuj struktury usług w ramach umowy Enterprise Agreement. Enterprise Portal umożliwia klientom dzielenia dostęp do zasobów platformy Azure skojarzone z umową Enterprise oparte na elastyczne hierarchiach dostosowania do potrzeb organizacji. Wzorzec hierarchii powinna odpowiadać zarządzania i geograficzne struktury organizacji tak, aby skojarzone dostępu rozliczeń i zasobów można dokładnie wyjaśnić.

Trzy wzorce wysokiego poziomu są jednostki funkcjonalności, biznesowych i działów geograficznego, za pomocą narzędzia jako konstrukcję administracyjne dla grupy kont. W ramach każdego działu kont można przypisać subskrypcji, których tworzenie silosów rozliczeń i kilka ograniczeń klucza na platformie Azure (np. liczbę maszyn wirtualnych, kont magazynu, itp.).

![Formanty subskrypcji](./media/governance-in-azure/security-governance-in-azure-fig2.png)


W przypadku organizacji z umową Enterprise subskrypcji platformy Azure, wykonaj czterech poziomu hierarchii:

- administrator przedsiębiorstwa rejestracji

- administratorem działu

- Właściciel konta

- Administrator usługi

Ta hierarchia reguluje następujące czynności:

- Relacja rozliczeń

- Administracja konta

- Rola dostępu na podstawie formantu (RBAC) do artefaktów

- Granice/limity

- Granice

  - Użycie i rozliczenia (oparte na numery oferta karta szybkość)

  - Limity

  - Virtual Network

- Dołączone do usługi AAD 1 (1 AAD być skojarzone z wielu subskrypcji)

- Skojarzone z kontem rejestracji enterprise

## <a name="role-based-access-controls"></a>Kontrola dostępu oparta na rolach

Gdy Azure pierwotnie został wydany, kontroli dostępu do subskrypcji zostały podstawowe: administratorem ani Współadministratorem. Dostęp do subskrypcji w klasycznym modelu niejawnego dostęp do wszystkich zasobów w portalu. Ten brak precyzyjną kontrolę, które doprowadziły do mnożenie subskrypcji do zapewnienia poziomu kontroli dostępu uzasadnione rejestracji platformy Azure.

![Kontrola dostępu oparta na rolach](./media/governance-in-azure/security-governance-in-azure-fig3.png)

Mnożenie tej subskrypcji nie jest już potrzebne. Przy użyciu kontroli dostępu opartej na rolach można przypisać użytkowników do ról standardowych (takich jak "" i "zapisu" typowych ról). Można również zdefiniować role niestandardowe.

[Azure opartej na rolach kontroli dostępu (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) umożliwia precyzyjne zarządzanie dostępem dla platformy Azure. Korzystając z modelu RBAC, można udzielić użytkownikom tylko takiego dostępu, jakiego potrzebują do wykonania swoich zadań. Nastawionych zabezpieczeń należy skoncentrować się na zapewniając pracownikom dokładne uprawnienia potrzebne. Za dużo uprawnienia ujawnia konta na ataki. Za mało uprawnienia oznacza, że pracownicy nie można pobrać ich pracować wydajnie. Azure opartej na rolach kontroli dostępu (RBAC) pomaga rozwiązać ten problem, oferując precyzyjne zarządzanie dostępem dla platformy Azure. RBAC ułatwia rozdzielenie obowiązków w obrębie organizacji i udzielić tylko takiego dostępu dla użytkowników, które są niezbędne do wykonywania swoich zadań. Zamiast nadanie każdy nieograniczonych uprawnień w Twojej subskrypcji platformy Azure lub zasobów, można zezwolić tylko pewne akcje.

Na przykład użycie funkcji RBAC, aby umożliwić jednego pracownika zarządzać maszyn wirtualnych w ramach subskrypcji, gdy inny można zarządzać baz danych w ramach tej samej subskrypcji.

Azure RBAC ma trzy podstawowe role, które są stosowane do wszystkich typów zasobów:

- **Właściciel** ma pełny dostęp do wszystkich zasobów łącznie z prawem delegować dostęp do innych użytkowników.

- **Współautor** można tworzyć i zarządzania wszystkimi typami zasobów platformy Azure, ale nie może udzielić dostępu do innych użytkowników.

- **Czytnik** można wyświetlić istniejących zasobów platformy Azure.

Pozostałe role RBAC na platformie Azure umożliwiają zarządzanie określonych zasobów platformy Azure. Na przykład Rola współautora maszyny wirtualnej zezwala użytkownikowi na tworzenie i zarządzanie maszynami wirtualnymi. Nie daje im dostępu do sieci wirtualnej lub podsieci, która łączy się z maszyny wirtualnej.

[Wbudowane role RBAC](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) Wyświetla listę ról, które są dostępne w systemie Azure. Określa operacje i zakresem, który każdego wbudowana rola przyznaje użytkownikom.

Udziel dostępu, przypisując odpowiednie role RBAC do użytkowników, grup i aplikacji w określonego zakresu. Zakres przypisania roli może być pojedynczego zasobu, grupy zasobów lub subskrypcji. Rola przypisana w zakresie nadrzędnej również udziela dostępu do podrzędnych w nim zawarte.

Na przykład użytkownik z dostępem do grupy zasobów można zarządzać wszystkie zasoby, które zawiera, takie jak witryny sieci Web, maszyn wirtualnych i podsieci.

Azure RBAC obsługuje tylko operacje zarządzania zasobami Azure w portalu Azure i interfejsów API usługi Azure Resource Manager. Nie można go autoryzacji wszystkie operacje poziomu danych zasobów platformy Azure. Na przykład ktoś może zarządzać kontami magazynu autoryzacji, ale nie do obiektów blob lub tabel w ramach konta magazynu nie. Podobnie bazy danych SQL mogą być zarządzane, ale nie tabele w niej.

Jeśli chcesz uzyskać więcej szczegółowych informacji na temat sposobu, w jaki RBAC ułatwia zarządzanie dostępem, zobacz [Co to jest kontrola dostępu oparta na rolach](https://docs.microsoft.com/azure/role-based-access-control/overview).

Możesz również [utworzyć niestandardową rolę](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) based kontroli dostępu (RBAC), jeśli żadna z wbudowanych ról nie spełnia określonych dostępu wymaga. Role niestandardowe można tworzyć przy użyciu [programu Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell), [Azure interfejsu wiersza polecenia (CLI)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)i [interfejsu API REST](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest). Podobnie jak role wbudowane role niestandardowe można przypisać do użytkowników, grup i aplikacji w subskrypcji, grupy zasobów i zakresy zasobów.

W ramach każdej subskrypcji można przyznać maksymalnie 2000 przypisań ról.

## <a name="resource-management"></a>Zarządzanie zasobami

Dostarczana przez platformę Azure pierwotnie tylko klasycznym modelu wdrażania. W tym modelu każdy zasób istniał niezależnie; nie było możliwości wykonania do grupowania powiązanych zasobów. Zamiast tego trzeba było ręcznie śledzić zasoby, które składają się rozwiązania lub aplikacji i pamiętaj, aby zarządzać nimi w skoordynowany sposób podejście.

Aby wdrożyć rozwiązanie, trzeba było utworzyć każdego zasobu indywidualnie za pośrednictwem portalu Azure lub utworzyć skrypt wdrożone wszystkie zasoby w odpowiedniej kolejności. Aby usunąć rozwiązania, trzeba było indywidualnie usunąć wszystkie zasoby. Nie można zastosować i zaktualizuj zasady kontroli dostępu dla powiązanych zasobów. Na koniec, nie możesz zastosować tagów do zasobów się z postanowieniami, które ułatwiają monitorowanie zasobów i Zarządzanie rozliczeniami.

W 2014 r. Azure wprowadzono Menedżera zasobów dodane pojęcie grupę zasobów. Grupa zasobów to kontener dla zasobów, które mają wspólne cyklu życia. Model wdrażania usługi Resource Manager zapewnia kilka korzyści:

- Można wdrożyć, zarządzanie i monitorowanie wszystkich usług do rozwiązania jako grupy, zamiast obsługi tych usług indywidualnie.

- Można wielokrotnie wdrażania rozwiązania przez cały cykl życia i mieć pewność, zasoby są wdrażane w spójnym stanie.

- Możliwość stosowania kontroli dostępu do wszystkich zasobów w grupie zasobów, a te zasady są stosowane automatycznie, gdy nowe zasoby są dodawane do grupy zasobów.

- Możliwość dodawania tagów do zasobów w celu logicznego uporządkowania wszystkich zasobów w ramach subskrypcji.

- JavaScript Object Notation (JSON) służy do definiowania infrastruktury dla rozwiązania. Plik JSON jest nazywany szablonem usługi Resource Manager.

- Możliwość definiowania zależności między zasobami, aby wdrażać je w odpowiedniej kolejności.

![Zarządzanie zasobami](./media/governance-in-azure/security-governance-in-azure-fig4.png)

Menedżer zasobów pozwala na poddane zasobów znaczący grup zarządzania, koligacji rozliczeń lub fizycznych. Jak wspomniano wcześniej, platforma Azure ma dwa modele wdrażania. W starszych [klasycznego modelu](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model), podstawową jednostką zarządzania został subskrypcji. Było trudne do podziału zasobów w ramach subskrypcji, które doprowadziło do utworzenia dużą liczbę subskrypcji. Z modelu Resource Manager widzieliśmy wprowadzenie grup zasobów.

Grupa zasobów to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. [Grupa zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) mogą zawierać wszystkich zasobów dla rozwiązania lub tylko tych zasobów, które mają być zarządzane jako grupa. Użytkownik decyduje o sposobie przydziału zasobów do grup zasobów pod kątem tego, co jest najbardziej odpowiednie dla danej organizacji.

Aby uzyskać zalecenia dotyczące szablonów, zobacz [Best practices for creating Azure Resource Manager templates](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) (Najlepsze rozwiązania dotyczące tworzenia szablonów usługi Azure Resource Manager).

Usługa Azure Resource Manager analizuje zależności i sprawdza, czy zasoby są tworzone we właściwej kolejności. Jeśli jeden zasób opiera się na wartości z innego zasobu (na przykład maszyna wirtualna wymagająca konta magazynu na potrzeby dysków), ustawiana jest zależność.

>[!Note]
>Aby uzyskać więcej informacji, zobacz [Defining dependencies in Azure Resource Manager templates](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies) (Definiowanie zależności w szablonach usługi Azure Resource Manager).

Szablonu można także używać w celu aktualizacji infrastruktury. Można na przykład dodać zasób do rozwiązania lub dodać reguły konfiguracji dla już wdrożonych zasobów. Jeśli szablon służy do utworzenia zasobu, ale ten zasób już istnieje, usługa Azure Resource Manager przeprowadzi aktualizację, zamiast tworzyć nowy element zawartości. Usługa Azure Resource Manager zaktualizuje istniejący zasób do stanu określonego dla nowego zasobu.

Resource Manager zapewnia rozszerzenia dla scenariuszy, gdy potrzebne są dodatkowe operacje, takie jak instalowanie oprogramowania, które nie są uwzględnione w konfiguracji.

## <a name="resource-tracking"></a>Śledzenie zasobów

Jak użytkownicy w Twojej organizacji dodać zasoby do subskrypcji, staje się coraz bardziej ważne skojarzyć zasoby z odpowiedniego działu, klientów i środowisko. Możesz dołączyć metadanych do zasobów za pomocą tagów. Możesz użyć [tagi](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) możesz podać informacje dotyczące zasobu lub właściciela. Znaczniki umożliwiają nie tylko agregacji i grupy zasobów na kilka sposobów, ale użyć tych danych na potrzeby obciążeń zwrotnych.

Tagi są przydatne w przypadku złożonych kolekcji grup zasobów i zasobów, które trzeba uporządkować wizualnie w możliwie logicznej formie. Można na przykład oznaczyć tagami zasoby, które pełnią podobną rolę w organizacji lub należą do tego samego działu.

Bez użycia tagów użytkownicy w organizacji mogą tworzyć wiele zasobów, które będą później bardzo trudne do znalezienia i zarządzania. Na przykład możesz usunąć wszystkie zasoby w projekcie. Jeśli te zasoby nie są oznaczone tagami tego projektu, użytkownik musi je znaleźć ręcznie. Tagowanie może być istotnym sposobem na zredukowanie niepotrzebnych kosztów w ramach subskrypcji.

Zasoby mogą być oznaczone tym samym tagiem, nawet jeśli nie znajdują się w tej samej grupie zasobów. Można utworzyć własną taksonomię tagów, aby mieć pewność, że wszyscy użytkownicy w organizacji używają tych samych tagów. Dzięki temu uniknie się pomyłek wynikających z użycia podobnych tagów (na przykład „wydział” zamiast „dział”).

Zasady zasobów umożliwiają tworzenie standardowych zasad dla Twojej organizacji. Można utworzyć zasad, które upewnij się, że zasoby są oznaczane odpowiednie wartości.

> [!Note]
> Aby uzyskać więcej informacji, zobacz [rozliczeń znaczniki inicjatywy zasad](../azure-policy/scripts/billing-tags-policy-init.md).

Oznakowane zasoby można również wyświetlić za pośrednictwem witryny Azure Portal.

[Raport użycia](https://docs.microsoft.com/azure/billing/billing-understand-your-bill) dla subskrypcji obejmuje nazwy i wartości tagów, co pozwala na rozbicie kosztów według tagów.

> [!Note]
> Aby uzyskać więcej informacji na temat tagów, zobacz [rozliczeń znaczniki inicjatywy zasad](../azure-policy/scripts/billing-tags-policy-init.md).

Tagi mają następujące ograniczenia:

- Każdy zasób lub grupa zasobów może mieć co najwyżej 15 pary klucz wartość tagu. Powyższe ograniczenie ma zastosowanie tylko do tagów stosowane bezpośrednio do grupy zasobów lub zasobu. Grupa zasobów może zawierać wiele zasobów, że mieć 15 pary klucz wartość tagu.

- Nazwa tagu jest ograniczona do 512 znaków.

- Wartość tagu jest ograniczona do 256 znaków.

- Tagi zastosowane do grupy zasobów nie są dziedziczone przez zasoby należące do tej grupy.

Jeśli masz więcej niż 15 wartości, które należy skojarzyć z zasobem, użyj ciągu JSON jako wartości tagu. Ciąg JSON może zawierać wiele wartości, które są stosowane do klucza jeden tag.

### <a name="tags-and-billing"></a>Znaczniki i rozliczeń

Znaczniki umożliwiają grupowania danych rozliczeń. Na przykład jeśli korzystasz z wieloma maszynami wirtualnymi w różnych organizacjach, za pomocą tagów do użycia grup Centrum kosztów. Umożliwia także tagi kategoryzowania koszty przez środowisko uruchomieniowe; przykład użycia rozliczeń dla maszyn wirtualnych w środowisku produkcyjnym.

Można pobrać informacji na temat tagów za pomocą [użycia zasobów platformy Azure i interfejsów API RateCard](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview) lub użycia pliku wartości rozdzielanych przecinkami (CSV). Pobierz plik użycia z [portalu konta usługi Azure](https://account.windowsazure.com/) lub [EA portal](https://ea.azure.com/).

>[!Note]
> Aby uzyskać więcej informacji na temat programowy dostęp do informacji dotyczących rozliczeń, zobacz [uzyskać wgląd w Microsoft Azure użycia zasobów](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview). Dla operacji interfejsu API REST, zobacz [dokumentacja interfejsu API REST rozliczenia Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Po pobraniu użycia woluminów CSV dla usług, które obsługują tagów z rozliczeniami znaczniki są wyświetlane w kolumnie tagów.

## <a name="critical-resource-controls"></a>Formanty krytycznego zasobu

Jak organizacji dodaje podstawowe usługi do subskrypcji, staje się coraz bardziej ważne upewnić się, że te usługi są dostępne w celu uniknięcia przerw w działaniu biznesowych. [Blokowania zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) pozwalają ograniczyć operacje na zasobach wysokiej wartości, gdzie modyfikowania lub usuwania ich miałoby znaczący wpływ na aplikacje lub infrastruktury chmury. Blokady można stosować do subskrypcji, grupy zasobów lub zasobów. Zazwyczaj należy zastosować blokady do podstawowych zasobów, takich jak sieci wirtualnych, bramy i kont magazynu.

Blokowania zasobów obsługuje obecnie dwie wartości: CanNotDelete i tylko do odczytu. CanNotDelete oznacza, że użytkownicy (z odpowiednich praw) mogą w dalszym ciągu odczytu lub zmodyfikuj zasobu, ale nie można go usunąć. Tylko do odczytu oznacza, że autoryzowani użytkownicy nie można usunąć ani zmodyfikować zasobu.

Menedżer zasobów blokad mają zastosowanie tylko do operacji, które pojawiają się w płaszczyźnie zarządzania, która składa się z operacji wysyłane do <https://management.azure.com>. Blokad nie ograniczają jak zasoby wykonywanie własnych funkcji. Zmiany zasobu jest ograniczony, ale operacje zasobów nie są ograniczone. Na przykład tylko do odczytu blokady na bazę danych SQL pozwala usuwanie i modyfikowanie bazy danych, ale go nie uniemożliwiają tworzenie, aktualizowanie lub usuwanie danych w bazie danych.

Stosowanie **tylko do odczytu** może prowadzić do nieoczekiwanych wyników, ponieważ niektóre operacje, które się wydawać odczytu operacje wymagają dodatkowych czynności. Na przykład wprowadzenie **tylko do odczytu** blokady na koncie magazynu uniemożliwia wyświetlanie kluczy wszystkich użytkowników. Listy kluczy operacji jest obsługiwany za pomocą żądania POST, ponieważ zwrócony klucze są dostępne do zapisu.

![Formanty krytycznego zasobu](./media/governance-in-azure/security-governance-in-azure-fig5.png)

Innym przykładem wprowadzenie blokady tylko do odczytu na zasób usługi aplikacji — uniemożliwia Eksploratora serwera w usłudze Visual Studio wyświetlanie plików dla zasobu, ponieważ interakcji wymaga dostępu do zapisu.

W przeciwieństwie do kontroli dostępu opartej na rolach blokady zarządzania służy do stosowania ograniczenia we wszystkich użytkowników i ról. Aby dowiedzieć się więcej o ustawianiu uprawnień dla użytkowników i ról, zobacz [kontroli dostępu opartej na roli Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

Po zastosowaniu blokady w zakresie nadrzędnym, wszystkie zasoby w ramach tego zakresu dziedziczą tego samego blokady. Nawet zasoby, które później zostaną dodane dziedziczą blokady z obiektu nadrzędnego. Najbardziej restrykcyjne blokady w dziedziczenia ma pierwszeństwo.

Aby utworzyć lub usunąć blokady zarządzania, musi mieć dostęp do Microsoft.Authorization/ _lub Microsoft.Authorization/locks/_ akcje. Wbudowanych ról, tylko **właściciela** i **Administrator dostępu użytkowników** otrzymują te akcje.

## <a name="api-access-to-billing-information"></a>Interfejs API dostęp do informacji dotyczących rozliczeń

Za pomocą interfejsów API usługi Azure rozliczeń pobierania danych użycia i zasobów do narzędziami analizy danych preferowany. API RateCard i użycia zasobów Azure może pomóc dokładnie przewidzieć i zarządzanie nimi kosztów. Interfejsy API są zaimplementowane jako dostawca zasobów i częścią rodziny interfejsach API udostępnianych przez Menedżera zasobów Azure.

### <a name="azure-resource-usage-api-preview"></a>Użycie zasobów platformy Azure, interfejsu API (wersja zapoznawcza)

Użyj platformy Azure [API użycia zasobów](https://msdn.microsoft.com/library/azure/mt219003) Twoje dane szacowany wykorzystania platformy Azure. Interfejs API zawiera:

- **Azure kontroli dostępu opartej na rolach** -skonfigurować zasady dostępu na [portalu Azure](https://portal.azure.com/) lub za pomocą [poleceń cmdlet programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) Aby określić użytkowników lub aplikacji, które mogą korzystać z dane użycia dla tej subskrypcji. Obiekty wywołujące należy użyć standardowego tokeny usługi Azure Active Directory do uwierzytelniania. Dodaj element wywołujący do albo czytnika rozliczeń, czytnika, właściciela lub współautora roli do uzyskania dostępu do danych użycia dla określonej subskrypcji platformy Azure.

- **Co godzinę lub agregacje codzienne** — wywołań można określić, czy chcą ich danych użycia usługi Azure co godzinę pakiety w ramach Agreement lub pakiety codziennie w ramach Agreement. Wartość domyślna to codziennie.

- **Wystąpienie metadanych (w tym tagi zasobów)** — Pobierz szczegóły na poziomie wystąpienia takich jak identyfikator uri zasobu w pełni kwalifikowana (/subscriptions/ {identyfikator subskrypcji} /..), informacje o grupie zasobów i tagi zasobów. Te metadane pomaga w sposób niejednoznaczny i programowo przydzielić użycia według znaczników, dla przypadków użycia, takich jak między ładowania.

- **Metadane zasobu** — szczegóły zasobów, takie jak nazwa miernika, kategorii licznika, podkategorii miernika, jednostki i region zapewniają wywołującego lepiej zrozumieć co został wykorzystany. Również pracujemy, aby były wyrównane terminologii metadanych zasobów w portalu Azure, Azure użycia woluminów CSV, EA rozliczeń CSV i inne środowiska publicznych pozwalają korelowania danych środowiska.

- **Użycia dla wszystkich oferują typy** — danych użycia jest dostępna dla wszystkich oferują typy jak płatność za rzeczywiste użycie, MSDN, zobowiązania pieniężnego, środki pieniężne i atrybutów Rozszerzonych.

**Zasobów platformy Azure RateCard interfejsu API (wersja zapoznawcza)**

Użyj interfejsu API RateCard zasobów Azure, aby uzyskać listę dostępnych zasobów platformy Azure i szacowany informacje o cenach dla każdego. Interfejs API zawiera:

- **Kontrola dostępu oparta na rolach Azure** — Konfigurowanie zasad dostępu w portalu Azure lub za pomocą poleceń cmdlet programu Azure PowerShell, aby określić, którzy użytkownicy lub aplikacji można uzyskać dostępu do danych RateCard. Obiekty wywołujące należy użyć standardowego tokeny usługi Azure Active Directory do uwierzytelniania. Dodaj element wywołujący do czytnika, właścicielem lub współautorem roli do uzyskania dostępu do danych użycia dla określonej subskrypcji platformy Azure.

- **(Nie obsługiwany EA) oferuje obsługę płatność za rzeczywiste użycie, MSDN, zobowiązań i środki pieniężne** — ten interfejs API zawiera informacje o szybkości poziomu oferty Azure. Wywołujący ten interfejs API należy przekazać informacje oferty, aby uzyskać szczegóły dotyczące zasobów i szybkości. Obecnie możemy udostępnić EA stawki, dlatego EA oferty zostały dostosowane stawki dla rejestracji. Oto niektóre scenariusze, które są możliwe z kombinacją użycia i interfejsów API RateCard:

- **Spędzają na platformie Azure w miesiącu** — za pomocą odpowiedniej kombinacji użycia i RateCard interfejsów API, aby uzyskać lepszą wgląd w chmurze spędzają w miesiącu. Można analizować zasobników co godzinę i dziennego użycia i opłat prognozy.

- **Konfigurowanie alertów** — umożliwia użycie i interfejsów API RateCard zużycie szacowany chmury i opłat i skonfigurować alerty oparte na zasobach lub pieniężne na podstawie.

- **Przewidywanie rachunku** — Get szacowane zużycie i w chmurze wydatków i zastosować algorytmów uczenia maszynowego na potrzeby prognozowania BOM będzie końca cyklu rozliczeniowego.

- **Wstępne zużycia koszt analizy** — za pomocą interfejsu API RateCard do prognozowania, ile rachunku byłby przez użycie oczekiwanego przenoszenia obciążeń w systemie Azure. Jeśli masz istniejące obciążenia w innych chmur lub chmur prywatnych, użycie z platformy Azure można również mapować spędzają szybkości, aby uzyskać lepszą oszacowanie Azure. Ta Szacowana daje możliwość przestawnego na ofertę i porównania pomiędzy typami inną ofertę poza płatność za rzeczywiste użycie, takich jak zobowiązań i środki pieniężne. Interfejs API również daje możliwość Zobacz różnice koszt według regionu i można wykonać analizy warunkowej kosztów podejmowanie decyzji dotyczących wdrożenia.

- **Analizy warunkowej** — można określić, czy jest bardziej ekonomiczne rozwiązanie do uruchamiania obciążeń w innym regionie lub w innej konfiguracji zasobów platformy Azure. Koszty zasobów platformy Azure mogą się różnić w zależności na region platformy Azure, którego używasz.

- Można również określić, czy innego typu oferty Azure zapewnia większą szybkość na zasobów platformy Azure.

## <a name="networking-controls"></a>Formanty sieci

Dostęp do zasobów można wewnętrznych (w ramach sieci) lub zewnętrznych (za pośrednictwem Internetu). To proste dla użytkowników w organizacji na przypadkowo umieść zasobów w miejscu niewłaściwy i potencjalnie otwórz je, aby nieautoryzowanym dostępem. Jak lokalnie / urządzeń, przedsiębiorstwa należy dodać kontrole w celu zapewnienia, że użytkownicy Azure podejmować decyzje prawym.

![Formanty sieci](./media/governance-in-azure/security-governance-in-azure-fig6.png)

Zarządzanie subskrypcją nazywamy zasobów podstawowych, które zapewniają podstawowy kontrolę dostępu. Podstawowe zasoby obejmują:

### <a name="network-connectivity"></a>Połączenie sieciowe

[Sieci wirtualne](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) obiektów kontenera podsieci. Chociaż nie niezbędne, często jest używana podczas łączenia z aplikacji do wewnętrznych zasobów firmy. Usługi sieci wirtualnej platformy Azure pozwala bezpiecznie łączyć z zasobami Azure ze sobą w sieciach wirtualnych (sieci wirtualne).

Sieci wirtualnej jest odzwierciedla w chmurze Twoją sieć. Sieci wirtualnej jest logiczną izolacją chmury Azure w wersji dedykowanej do subskrypcji. Sieci wirtualne mogą łączyć się z siecią lokalną.

Poniżej przedstawiono funkcje dla sieci wirtualnych Azure:

- **Izolacja**: sieci wirtualne są odizolowane od siebie. Można utworzyć oddzielne sieci wirtualnych do tworzenia, testowania i produkcji, korzystające z tego samego bloków adresów CIDR. Z drugiej strony możesz utworzyć wiele sieci wirtualnych, użyj innego bloków adresów CIDR i połączyć ze sobą sieci. Sieć wirtualną można podzielić na wiele podsieci. Platforma Azure udostępnia rozpoznawania nazw wewnętrznych dla maszyn wirtualnych i usług w chmurze wystąpień roli podłączone do sieci wirtualnej. Opcjonalnie można skonfigurować sieć wirtualną do użycia zamiast rozpoznawania nazw wewnętrznych Azure własne serwery DNS.

- **Połączenie z Internetem**: Wszystkie maszyny wirtualne Azure (VM) i usługi w chmurze wystąpień roli podłączone do sieci wirtualnej mają dostęp do Internetu, domyślnie. Można również włączyć dostęp przychodzący do określonych zasobów, zgodnie z potrzebami.

- **Łączność zasobów platformy Azure**: zasobów platformy Azure, takich jak usługi w chmurze i maszyn wirtualnych można podłączyć do tej samej sieci wirtualnej. Zasoby można połączyć ze sobą przy użyciu prywatnych adresów IP, nawet jeśli znajdują się w różnych podsieciach. Platforma Azure udostępnia domyślny routing między podsieciami, sieci wirtualnych i sieci lokalnej, więc nie trzeba konfigurować i zarządzać nimi trasy.

- **Łączność sieci wirtualnej**: sieci wirtualne mogą zostać połączone ze sobą, włączanie zasoby podłączone do żadnych sieci wirtualnej do komunikowania się z dowolnego zasobu w innych sieci wirtualnej.

- **Połączenie lokalne**: sieci wirtualnych można podłączyć do sieci lokalnej za pośrednictwem sieci prywatnej połączeń między siecią a Azure lub za pośrednictwem połączenia sieci VPN typu lokacja lokacja za pośrednictwem Internetu.

- **Filtrowanie ruchu**: ruchu sieciowego wystąpień roli maszyny Wirtualnej i usługi w chmurze można użyć do filtrowania ruchu przychodzącego i wychodzącego przez źródłowy adres IP i port docelowy adres IP i portu i protokołu.

- **Routing**: Opcjonalnie można zastąpić domyślne Azure routingu, konfigurowanie własnego trasy lub użyj trasy protokołu BGP za pośrednictwem bramy sieci.

## <a name="network-access-controls"></a>Kontrolę dostępu do sieci

[Sieciowe grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) są takie jak zapory i reguł dla jak zasobu można "rozmawiać" za pośrednictwem sieci. Udostępniają one szczegółową kontrolę nad jak / Jeśli podsieci (lub maszyny wirtualnej) można nawiązać połączenia z Internetem lub innych podsieci w tej samej sieci wirtualnej.

Sieciowa grupa zabezpieczeń (NSG, network security group) zawiera listę reguł zabezpieczeń, które blokują lub zezwalają na ruch sieciowy do zasobów połączonych z usługami Azure Virtual Network (VNet). Sieciowe grupy zabezpieczeń można skojarzyć z podsieciami, poszczególnymi maszynami wirtualnymi (model klasyczny) lub poszczególnymi interfejsami sieciowymi (NIC) dołączonymi do maszyn wirtualnych (model usługi Resource Manager).

Jeśli sieciowa grupa zabezpieczeń jest skojarzona z podsiecią, te reguły są stosowane do wszystkich zasobów połączonych z tą podsiecią. Ruch można ograniczyć jeszcze bardziej, kojarząc sieciową grupę zabezpieczeń również z maszyną wirtualną lub kartą sieciową.

## <a name="security-and-continuous-compliance-with-organizational-standards"></a>Bezpieczeństwo i ciągłe zgodności ze standardami w organizacji

Każda firma ma różnych potrzeb, a każda firma będzie czerpanie różne korzyści z rozwiązań w chmurze. Klienci wszelkiego rodzaju wątpliwości tego samego podstawowego przenoszeniu do chmury. Chcą oni zachować kontrolę nad ich danymi i mają te dane mają być przechowywane bezpieczeństwo i prywatność, wszystkie zachowując przezroczystość i zgodności.

Klienci szukają od dostawców w chmurze jest:

- **Zabezpieczanie danych** podczas potwierdzeniem, że chmura może zapewniać zwiększyć bezpieczeństwo danych i kontrolę administracyjną, liderów IT nadal dotyczy czy migracja do chmury pozostawi je bardziej narażony na hakerów niż bieżący wewnętrznych rozwiązania.

- **Zachowaj naszych danych** usługi w chmurze prywatnej podnieść wyzwania unikatowy prywatności dla firm. Jak firm wyglądać chmury w celu zapisania na koszty infrastruktury i zwiększyć ich elastyczność, one również martwić się o utracie kontroli nad którym są przechowywane ich dane, kto uzyskuje dostęp do jego i jak jest używany.

- **Przekaż nam kontroli** nawet mogą korzystać z chmury, aby wdrożyć więcej innowacyjne rozwiązania firmy są bardzo dane o utracie kontroli nad ich danych. Najnowsze informacje agencji rządowych dostęp do danych klienta za pośrednictwem oznacza, że zarówno prawne i bardzo prawnych, należy niektórych dyrektorzy działu informatyki ostrożność przechowywanie danych w chmurze.

- **Podwyższ poziom przezroczystości** zabezpieczeń, prywatności i kontroli są istotne dla firm decydentów, mają również możliwość niezależnie Sprawdź, jak ich przechowywanych danych, uzyskuje się dostęp i zabezpieczonych.

- **Obsługa zgodności** jako firm rozwinąć ich korzystanie z technologii chmury, złożoność i zakres norm i przepisów nadal podlegać ewolucji. Firmy muszą wiedzieć, że zostaną spełnione ich standardów zgodności tego zgodności rozpoczyna się jako przepisy dotyczące zmian w czasie.

## <a name="security-configuration-monitoring-and-alerting"></a>Konfiguracja zabezpieczeń, monitorowanie i alerty

Subskrybenci platformy Azure mogą zarządzać środowiskami chmury przy użyciu wielu urządzeń, łącznie ze stacjami roboczymi do zarządzania, komputerami deweloperów, a nawet urządzeniami uprzywilejowanych użytkowników końcowych, którzy mają uprawnienia specyficzne dla zadania. W niektórych przypadkach funkcje administracyjne są wykonywane za pośrednictwem konsoli opartych na sieci web, takich jak portalu Azure. W innych przypadkach mogą istnieć bezpośrednie połączenia z platformą Azure z systemów lokalnych za pośrednictwem wirtualnych sieci prywatnych (VPN), usług terminalowych, protokołów aplikacji klienckich lub (programowo) interfejsu API zarządzania usługami Azure (SMAPI, Service Management API). Ponadto punkty końcowe klienta mogą być przyłączone do domeny lub odizolowane i niezarządzane (np. tablety lub smartfony).

Liczne możliwości uzyskania dostępu i zarządzania zapewniają bogaty zestaw opcji, jednak mogą spowodować podwyższenie ryzyka związanego z wdrożeniem w chmurze. Może to utrudniać śledzenie i inspekcję czynności administracyjnych oraz zarządzanie nimi. To zróżnicowanie może również wprowadzać zagrożenia bezpieczeństwa związane z nieuregulowanym dostępem do punktów końcowych klienta używanych do zarządzania usługami w chmurze. Użycie ogólnych lub osobistych stacji roboczych do opracowywania infrastruktury i zarządzania nią powoduje, że zagrożenia mogą nadchodzić z nieprzewidywalnych kierunków, na przykład podczas przeglądania sieci Web (na przykład ataki za pośrednictwem używanych witryn) lub korzystania z poczty e-mail (na przykład techniki socjotechniczne i wyłudzanie informacji).

Monitorowanie, rejestrowanie i inspekcja stanowią podstawę dla śledzenia i zrozumienia czynności administracyjnych, ale nie zawsze można przeprowadzić szczegółową inspekcję wszystkich akcji ze względu na ilość generowanych danych. Inspekcja skuteczności zasad zarządzania jest jednak najlepszym rozwiązaniem.

Zabezpieczeń platformy Azure zarządzania z usługi AD DS obiektów zasad grupy do kontrolowania wszystkich administratorów Windows interfejsów, takich jak udostępnianie plików. Uwzględnij stacje robocze używane do zarządzania w procesach inspekcji, monitorowania i rejestrowania. Śledź dostęp i używanie przez wszystkich administratorów i deweloperów.

### <a name="azure-security-center"></a>Azure Security Center

[Centrum zabezpieczeń Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) centralnej Wyświetla stan zabezpieczeń zasobów w subskrypcji i zawiera zalecenia, które ułatwiają ochronę zasobów ze złamanymi zabezpieczeniami. Bardziej szczegółowe zasady (na przykład stosowania zasad do określonych grup zasobów, które umożliwiają firmie dostosować ich postawie ryzyko, które są one adresowania) można ją włączyć.

![Azure Security Center](./media/governance-in-azure/security-governance-in-azure-fig7.png)

Centrum zabezpieczeń zapewnia zabezpieczenia zintegrowane monitorowanie i zarządzanie zasadami subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które mogłyby w przeciwnym razie pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań zabezpieczających. Po włączeniu [zasady zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-policies) dla zasobów subskrypcji Centrum zabezpieczeń analizuje zabezpieczenia zasobów, aby zidentyfikować potencjalne luki. Informacje o konfiguracji sieci są dostępne natychmiast.

Centrum zabezpieczeń Azure reprezentuje kombinację najlepszych rozwiązań analizy i zabezpieczeń Zarządzanie zasadami dla wszystkich zasobów w ramach subskrypcji platformy Azure. To narzędzie wydajny i łatwy w użyciu umożliwia zespołów zabezpieczeń i ryzyka oficerowie zapobiegania, wykrywania i reagowania na zagrożenia bezpieczeństwa, ponieważ automatycznie zbiera i analizuje dane dotyczące zabezpieczeń z zasobów platformy Azure, sieci i rozwiązań partnerskich, takich jak przed złośliwym oprogramowaniem programy i zapory.

Ponadto Centrum zabezpieczeń Azure stosuje zaawansowane metody analizy, w tym uczenie maszynowe i analizę behawioralną podczas korzystania z globalnej analizy zagrożeń ze swoich produktów i usług Microsoft cyfrowego ds. przestępstw jednostki (DCU) firmy Microsoft Zabezpieczenia odpowiedzi Center (MSRC) i zewnętrznych źródeł danych. [Zarządzanie zabezpieczeń](https://www.credera.com/blog/credera-site/azure-governance-part-4-other-tools-in-the-toolbox/) szeroko stosowana na poziomie subskrypcji lub zawęzić do określonych, szczegółowe wymagania stosowane do pojedynczych zasobów za pośrednictwem definicji zasad.

Na koniec Centrum zabezpieczeń Azure analizuje kondycja zabezpieczeń zasobów na podstawie tych zasad i używa go do zapewnienia interesującego pulpitów nawigacyjnych i alerty dla zdarzenia, takie jak wykrywania złośliwego oprogramowania lub złośliwe połączenia IP prób.

>[!Note]
> Więcej informacji dotyczących stosowania zaleceń można znaleźć w temacie [Wdrażanie zaleceń dotyczących zabezpieczeń w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations).

Centrum zabezpieczeń zbiera dane z maszyn wirtualnych do oceny stanu zabezpieczeń, podaj zalecenia dotyczące zabezpieczeń i alertów na zagrożenia. Jeśli najpierw przejść do Centrum zabezpieczeń zbieranie danych jest włączone na wszystkich maszynach wirtualnych w ramach subskrypcji. Zbieranie danych jest zalecane, ale użytkownik może zrezygnować przez [wyłączanie zbierania danych](https://docs.microsoft.com/azure/security-center/security-center-faq) w zasadach Centrum zabezpieczeń.

Wreszcie Centrum zabezpieczeń Azure jest otwartej platformie, która umożliwia partnerom firmy Microsoft i niezależnym dostawcom oprogramowania tworzenia oprogramowania, które podłącza się do Centrum zabezpieczeń Azure w celu zwiększenia jego możliwości.

Centrum zabezpieczeń Azure monitorowanie następujących zasobów platformy Azure:

- Maszynach wirtualnych (VM) (w tym usługi w chmurze)

- Sieci wirtualne platformy Azure

- Usługi SQL Azure

- Partnerskich rozwiązań zintegrowanych z subskrypcją platformy Azure, takich jak Zapora aplikacji sieci web na maszynach wirtualnych i na [środowiska usługi aplikacji](https://docs.microsoft.com/azure/app-service/app-service-app-service-environments-readme).

### <a name="log-analytics"></a>Log Analytics

Bezpieczeństwo informacji analizy dzienników oprogramowania rozwoju i usługi zespołu i [program ładu](https://github.com/Microsoft/azure-docs/blob/master/articles/log-analytics/log-analytics-security.md) obsługuje jej wymagania biznesowe i stosuje przepisom eksportowym obowiązującym zgodnie z opisem w [zaufania usługi Microsoft Azure Centrum](https://azure.microsoft.com/support/trust-center/) i [zgodności Centrum zaufania Microsoft](https://www.microsoft.com/TrustCenter/Compliance/default.aspx). Jak analizy dzienników ustanowić wymagania dotyczące zabezpieczeń, identyfikuje kontroli zabezpieczeń zarządza i monitoruje ryzyka są także opisane istnieje. Co rok, możemy przeglądu zasady, normy, procedury i wskazówek.

Każdego członka zespołu programowanie analizy dzienników odbiera szkolenia formalnego aplikacji w zakresie zabezpieczeń. Wewnętrznie używamy system kontroli wersji dla rozwoju oprogramowania. Każdy projekt oprogramowania jest chroniona przez system kontroli wersji.

Firma Microsoft ma zabezpieczeń i zgodności zespołu nadzoruje i ocenia wszystkich usług firmy Microsoft. Zabezpieczenia informatyków uzupełnić zespołu i nie są one powiązane z działów engineering, które opracowanie analizy dzienników. Biuro zabezpieczeń ma swoje własne łańcuch zarządzania i przeprowadzanie niezależnych ocen produktów i usług w celu zapewnienia bezpieczeństwa i zgodności.

Platforma Azure udostępnia kolekcję usług zarządzania, które zostały zaprojektowane w chmurze od początku. Zamiast wdrażania i zarządzania nimi w lokalnych zasobach, te składniki znajdują się wyłącznie na platformie Azure. Konfiguracja jest ograniczona do minimalnego zakresu, a pracę można rozpocząć dosłownie w ciągu kilku minut.

![Program Operations Manager Suite](./media/governance-in-azure/security-governance-in-azure-fig8.png)

Wyłącznie z powodu analizy dzienników usługi uruchamiane w chmurze nie oznacza, że nie można efektywnie zarządzać w lokalnym środowisku.

Umieść agenta w dowolnym systemie Windows lub komputera z systemem Linux w centrum danych która będzie wysyłać dane do analizy dzienników, w którym można przeprowadzić analizę oraz innych danych zebranych z chmury, lub na lokalne usługi. Użyj Azure Backup i Azure Site Recovery, aby korzystać z chmury na potrzeby tworzenia kopii zapasowej i wysoka dostępność dla zasobów lokalnych.

Elementy runbook w chmurze zwykle nie mają dostępu do zasobów lokalnych, ale możesz też zainstalować na komputerach agenta, który będzie hostować elementy runbook w centrum danych. Po uruchomieniu elementu runbook wystarczy określić, czy ma on działać w chmurze czy w lokalnym procesie roboczym.

Do podstawowych funkcji analizy dzienników są udostępniane przez zestaw usług, które działają na platformie Azure. Każda usługa udostępnia określoną funkcję zarządzania. Możesz łączyć usługi, aby realizować różne scenariusze zarządzania.

![Program Operations Manager Suite](./media/governance-in-azure/security-governance-in-azure-fig9.JPG)

Azure Operations manager rozszerza jego funkcje zapewniając rozwiązania do zarządzania. [Rozwiązania do zarządzania](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions) opakowaniach jednostkowych zestawów logiki implementujących scenariusz zarządzania, wykorzystując co najmniej jednej usługi zarządzania.

![Zarządzanie operacji systemu Azure](./media/governance-in-azure/security-governance-in-azure-fig10.png)

Różnych rozwiązaniach dostępnych firmy Microsoft i partnerów, możesz łatwo można dodać do subskrypcji platformy Azure, aby zwiększyć wartość inwestycji w usługę Analiza dzienników.

Przez partnera możesz utworzyć rozwiązań do obsługi aplikacji i usług i udostępniania ich użytkownikom za pośrednictwem portalu Azure Marketplace lub Szybki Start szablonów.

## <a name="performance-alerting-and-monitoring"></a>Monitorowanie i alerty wydajności

### <a name="alerting"></a>Generowanie alertów

Metodą monitorowania metryki zasobów platformy Azure, zdarzeń i dzienniki są alerty i powiadomienia po określeniu warunek jest spełniony.

**Alerty w różnych usług platformy Azure**

Alerty są dostępne w różnych usługach, w tym:

- Usługi Application Insights: Włącza testu sieci web i metryki alerty.

>[!Note]
> Zobacz [ustawić alertów w usłudze Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) i [monitorowanie dostępności i czas odpowiedzi dla wszystkich witryn sieci Web](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability).

- Analiza dzienników: Włącza routing aktywności i dzienników diagnostycznych do analizy dzienników i umożliwia metryki, log oraz inne typy alertów.

>[!Note]
> Aby uzyskać więcej informacji, zobacz alerty w [analizy dzienników](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts).

- Azure Monitor: Włącza alerty oparte na wartości metryki i zdarzenia dziennika aktywności. Można użyć [interfejsu API REST Monitor Azure](https://msdn.microsoft.com/library/dn931943.aspx) do zarządzania alertami.

>[!Note]
> Aby uzyskać więcej informacji, zobacz [przy użyciu portalu Azure, programu PowerShell lub interfejsu wiersza polecenia, aby tworzyć alerty](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-alerts-portal).

### <a name="monitoring"></a>Monitorowanie

Problemy z wydajnością w Twojej aplikacji w chmurze może wpłynąć na Twojej firmy. Z wielu powiązanych elementów i częste wersjach degradations może nastąpić w dowolnej chwili. I w przypadku tworzenia aplikacji, użytkowników, zazwyczaj odnajdywanie problemy, które nie udało się znaleźć podczas testowania. Należy od razu wiedzieć o tych problemów i narzędzia do diagnozowania i rozwiązywania problemów. Microsoft Azure ma szeroką gamę narzędzi do identyfikacji tych problemów.

**Jak monitorować Moje aplikacje w chmurze Azure?**

Istnieje szereg narzędzia do monitorowania usługi i aplikacje platformy Azure. Niektóre z ich funkcji nakładają się. To jest częściowo ze względów historycznych i częściowo z powodu rozmycia między rozwoju i działania aplikacji.

Poniżej przedstawiono główne narzędzia:

- **Azure Monitor** jest podstawowym narzędziem do monitorowania usługi działające na platformie Azure. Udostępnia poziomie infrastruktury danych o przepływności usługi i otaczającego środowiska. W przypadku zarządzania aplikacjami w usłudze Azure podjęcie decyzji o skalować w górę lub w dół zasobów, Azure Monitor daje służy do zainicjowania.

- **Usługa Application Insights** mogą być używane do tworzenia aplikacji oraz jako rozwiązanie monitorujące produkcji. Działa, instalując pakiet w swojej aplikacji, a więc zapewnia bardziej wewnętrzny widok co się dzieje. Jego danych obejmuje czas reakcji zależności i śladów wyjątek, debugowanie migawki, profile wykonywania. Zapewnia zaawansowanych narzędzi inteligentne do analizowania tego telemetrii zarówno do debugowania aplikacji i pomagające zrozumieć, co robią użytkownicy z nim. Można określić, czy kolekcji czas odpowiedzi jest z powodu coś w aplikacji lub niektóre zewnętrznego problemem resourcing. Jeśli używasz programu Visual Studio i aplikacji jest uszkodzone, możesz można przenieść prawo do wierszy problem kodu, można go naprawić.

- **Zaloguj się Analytics** jest przeznaczony dla osób konieczne dostrojenie wydajności i planowanie konserwacji na aplikacje uruchomione w środowisku produkcyjnym. Jest on oparty na platformie Azure. On zbiera i agregują dane z wielu źródeł, chociaż z opóźnieniem 10 – 15 minut. Przewiduje całościowe rozwiązanie zarządzania Azure, lokalne i innych firm oparte na chmurze infrastruktury (na przykład usług Amazon Web Services). Zapewnia bardziej zaawansowane funkcje narzędzi do analizowania danych między źródłami więcej umożliwia złożonych zapytań przez wszystkie dzienniki i może aktywnie alert po wystąpieniu określonego warunku. Można nawet zebrać dane niestandardowe w jego centralnym repozytorium, dzięki czemu może zapytania i wizualizacji go.

- **System Center Operations Manager (SCOM)** jest do zarządzania i monitorowania instalacji dużych chmury. Może być już znasz go jako narzędzia do zarządzania lokalnymi chmury na podstawie funkcji Hyper-V i Windows Server, ale można również zintegrować z i zarządzanie aplikacjami platformy Azure. Między innymi może instalować usługi Application Insights na istniejące aplikacje na żywo. Jeśli aplikacja przestanie działać, określa w sekundach.


## <a name="next-steps"></a>Kolejne kroki

- [Najlepsze rozwiązania dotyczące tworzenia szablonów usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices).

- [Przykłady stosowania ładu subskrypcji platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-subscription-examples).

- [Microsoft Azure dla instytucji rządowych](https://docs.microsoft.com/azure/azure-government/).

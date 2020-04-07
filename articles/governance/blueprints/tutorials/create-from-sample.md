---
title: 'Samouczek: Próbka planu do nowego środowiska'
description: W tym samouczku można użyć przykład planu do utworzenia definicji planu, która konfiguruje dwie grupy zasobów i konfiguruje przypisanie roli dla każdego.
ms.date: 03/25/2020
ms.topic: tutorial
ms.openlocfilehash: 79928822b41dfe0583afbeaf2f5f1a4a87dd3202
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677087"
---
# <a name="tutorial-create-an-environment-from-a-blueprint-sample"></a>Samouczek: Tworzenie środowiska na podstawie przykładu planu

Przykładowe plany zawierają przykłady, co można zrobić za pomocą planów platformy Azure. Każdy z nich jest próbką o określonym zamiarze lub celu, ale nie tworzy kompletne środowisko przez siebie. Każdy z nich jest przeznaczony jako miejsce rozpoczęcia eksplorowania przy użyciu planów platformy Azure z różnymi kombinacjami dołączonych artefaktów, projektów i parametrów.

Poniższy samouczek używa grup zasobów z przykładem planu **RBAC** do zaprezentowania różnych aspektów usługi Azure Blueprints. Następujące kroki są objęte:

> [!div class="checklist"]
> - Tworzenie nowej definicji planu z przykładu
> - Oznaczanie kopii próbki jako **opublikowanej**
> - Przypisywanie kopii planu do istniejącej subskrypcji
> - Inspekcja wdrożonych zasobów dla przydziału
> - Co niesesignować plan, aby usunąć zamki

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, potrzebna jest subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="create-blueprint-definition-from-sample"></a>Tworzenie definicji planu z przykładu

Najpierw zaimplementuj przykład planu. Importowanie tworzy nowy plan w środowisku na podstawie próbki.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Na stronie **Wprowadzenie** po lewej stronie wybierz przycisk **Utwórz** w obszarze _Tworzenie planu_.

1. Znajdź **grupy zasobów z** próbką planu RBAC w obszarze _Inne przykłady_ i wybierz ją.

1. Wprowadź _podstawy_ przykładu planu:

   - **Nazwa planu:** Podaj nazwę kopii przykładu planu. W tym samouczku użyjemy nazwy _two-rgs-with-role-assignments_.
   - **Lokalizacja definicji:** Użyj wielokropek i wybierz grupę zarządzania lub subskrypcję, aby zapisać kopię próbki.

1. Wybierz kartę _Artefakty_ u góry strony lub **Dalej: Artefakty** u dołu strony.

1. Przejrzyj listę artefaktów, które tworzą przykład planu. W tym przykładzie zdefiniowano dwie grupy zasobów o wyświetlanych nazwach _ProdRG_ i _PreProdRG_. Ostateczna nazwa i lokalizacja każdej grupy zasobów są ustawiane podczas przypisywania planu. Grupie zasobów _ProdRG_ jest przypisana rola _Współautor,_ a grupie zasobów _PreProdRG_ są przypisywane role _Właściciel_ i _Czytelnicy._ Role przypisane w definicji są statyczne, ale użytkownik, aplikacja lub grupa, która jest przypisana do roli jest ustawiona podczas przypisywania planu.

1. Po zakończeniu przeglądania przykładu planu wybierz pozycję Zapisz wersja **robocza.**

Ten krok tworzy kopię przykładowej definicji planu w wybranej grupie zarządzania lub subskrypcji. Zapisana definicja planu jest zarządzana jak każdy plan utworzony od podstaw. Próbkę można zapisać w grupie zarządzania lub w subskrypcji tyle razy, ile potrzeba. Jednak każda kopia musi mieć unikatową nazwę.

Po **zapisaniu definicji planu powiodło się** powiadomienie portalu, przejdź do następnego kroku.

## <a name="publish-the-sample-copy"></a>Publikowanie przykładowej kopii

Kopia przykładu planu została utworzona w twoim środowisku. Jest tworzony w trybie **roboczym** i musi zostać **opublikowany,** zanim będzie można go przypisać i wdrożyć. Kopię przykładu planu można dostosować do środowiska i potrzeb. W tym samouczku nie będziemy wprowadzać żadnych zmian.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Definicje planu.** Użyj filtrów, aby znaleźć definicję planu _dwóch rgs-with-role-assignments,_ a następnie wybierz ją.

1. Wybierz **pozycję Publikuj plan** u góry strony. W nowym okienku po prawej stronie **podaj wersję** jako _1.0_ dla kopii przykładu planu. Ta właściwość jest przydatna, jeśli dokonasz modyfikacji później. Podaj **informacje o zmianach,** takie jak "Pierwsza wersja opublikowana z grup zasobów za pomocą przykładowego planu RBAC". Następnie wybierz **pozycję Publikuj** u dołu strony.

Ten krok umożliwia przypisanie planu do subskrypcji. Po opublikowaniu można nadal wszczynać zmiany. Dodatkowe zmiany wymagają publikowania z nową wartość **Version** do śledzenia różnic między różnymi wersjami tej samej definicji planu.

Po **opublikowaniu definicji planu publikowania powiadomienie** portalu powiodło się, przejdź do następnego kroku.

## <a name="assign-the-sample-copy"></a>Przypisywanie przykładowej kopii

Po **pomyślnym**opublikowaniu kopii przykładu planu można ją przypisać do subskrypcji w grupie zarządzania, do której została zapisana. W tym kroku są dostarczane parametry, aby każde wdrożenie kopii przykładu planu unikatowe.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Definicje planu.** Użyj filtrów, aby znaleźć definicję planu _dwóch rgs-with-role-assignments,_ a następnie wybierz ją.

1. Wybierz **pozycję Przypisz plan** u góry strony definicji planu.

1. Podaj wartości parametrów dla przypisania planu:

   - Podstawy

     - **Subskrypcje:** Wybierz jedną lub więcej subskrypcji, które znajdują się w grupie zarządzania, do której została zapisana kopia przykładu planu. Jeśli wybierzesz więcej niż jedną subskrypcję, dla każdej z nich zostanie utworzone przypisanie przy użyciu wprowadzonych parametrów.
     - **Nazwa przypisania:** Nazwa jest wstępnie wypełniona na podstawie nazwy definicji planu.
     - **Lokalizacja:** Wybierz region dla zarządzanej tożsamości, w którym ma zostać utworzona. Usługa Azure Blueprint używa tej tożsamości zarządzanej do wdrażania wszystkich artefaktów w przypisanej strategii. Aby dowiedzieć się więcej, zobacz [tożsamości zarządzane dla zasobów platformy Azure](../../../active-directory/managed-identities-azure-resources/overview.md).
       W tym samouczku wybierz _pozycję Wschodnie stany USA 2_.
     - **Wersja definicji planu:** Wybierz **opublikowaną** wersję _1.0_ kopii przykładowej definicji planu.

   - Przypisanie blokady

     Wybierz tryb blokady planu _tylko do odczytu._ Aby uzyskać więcej informacji, zobacz [blokowanie zasobów strategii](../concepts/resource-locking.md).

   - Tożsamość zarządzana

     Pozostaw domyślną przypisaną opcję _System._ Aby uzyskać więcej informacji, zobacz [tożsamości zarządzane](../../../active-directory/managed-identities-azure-resources/overview.md).

   - Parametry artefaktu

     Parametry zdefiniowane w tej sekcji dotyczą artefaktu, w którym jest zdefiniowany. Parametry te są [parametry dynamiczne,](../concepts/parameters.md#dynamic-parameters) ponieważ są one zdefiniowane podczas przypisywania planu. Dla każdego artefaktu ustaw wartość parametru na zdefiniowaną w kolumnie **Wartość.** Dla `{Your ID}`, wybierz swoje konto użytkownika platformy Azure.

     |Nazwa artefaktu|Typ artefaktu|Nazwa parametru|Wartość|Opis|
     |-|-|-|-|-|
     |Grupa zasobów ProdRG|Grupa zasobów|Nazwa|ProdukcjaRG|Definiuje nazwę pierwszej grupy zasobów.|
     |Grupa zasobów ProdRG|Grupa zasobów|Lokalizacja|Zachodnie stany USA 2|Ustawia lokalizację pierwszej grupy zasobów.|
     |Współautor|Przypisanie roli|Użytkownik lub grupa|{Twój identyfikator}|Określa, który użytkownik lub grupa ma udzielić przypisania roli _współautora_ w ramach pierwszej grupy zasobów.|
     |Grupa zasobów PreProdRG|Grupa zasobów|Nazwa|PreProductionRG|Definiuje nazwę drugiej grupy zasobów.|
     |Grupa zasobów PreProdRG|Grupa zasobów|Lokalizacja|Zachodnie stany USA|Ustawia lokalizację drugiej grupy zasobów.|
     |Właściciel|Przypisanie roli|Użytkownik lub grupa|{Twój identyfikator}|Określa, który użytkownik lub grupa ma udzielić przypisania roli _Właściciela_ w ramach drugiej grupy zasobów.|
     |Czytelnicy|Przypisanie roli|Użytkownik lub grupa|{Twój identyfikator}|Określa, który użytkownik lub grupa ma udzielić przypisania roli _Czytelnicy_ w ramach drugiej grupy zasobów.|

1. Po wprowadzeniu wszystkich parametrów wybierz **pozycję Przypisz** u dołu strony.

Ten krok wdraża zdefiniowane zasoby i konfiguruje wybrane **przypisanie blokady**. Zastosowanie blokad planu może potrwać do 30 minut.

Po **wyświetleniu powiadomienia portalu Przypisanie planu powiodło się,** przejdź do następnego kroku.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Inspekcja zasobów wdrożonych przez przydział

Przypisanie planu tworzy i śledzi artefakty zdefiniowane w definicji planu. Możemy zobaczyć stan zasobów ze strony przypisania planu i patrząc bezpośrednio na zasoby.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Przypisane plany.** Użyj filtrów, aby znaleźć przypisanie planu _przydziału dwa-rgs-z przypisaniami ról,_ a następnie zaznacz go.

   Na tej stronie możemy zobaczyć przypisanie powiodło się i listę utworzonych zasobów wraz z ich stan blokady planu. Jeśli przypisanie zostanie zaktualizowane, w części rozwijanej **Operacja przydziału** są wyświetlane szczegółowe informacje o wdrażaniu każdej wersji definicji. Każdy utworzony zasób można kliknąć i otwierać tę stronę właściwości zasobów.

1. Wybierz grupę zasobów **ProductionRG.**

   Widzimy, że nazwa grupy zasobów jest **ProductionRG,** a nie nazwa wyświetlana _artefaktu ProdRG_. Ta nazwa jest zgodna z wartością ustawioną podczas przypisywania planu.

1. Po lewej stronie wybierz stronę **Kontrola dostępu (IAM),** a następnie kartę **Przypisania ról.**

   W tym miejscu widzimy, że Twoje konto otrzymało rolę _Współautor w_ zakresie _tego zasobu_. Przypisanie planu _Przydział-dwa-rgs-z przypisaniami ról_ ma rolę _Właściciel,_ ponieważ została użyta do utworzenia grupy zasobów. Te uprawnienia są również używane do zarządzania zasobami za pomocą skonfigurowanych blokad planu.

1. W portalu Azure breadcrumb wybierz **przydział-dwa-rgs-z przypisaniami ról,** aby wrócić do jednej strony, a następnie wybierz grupę zasobów **PreProductionRG.**

1. Po lewej stronie wybierz stronę **Kontrola dostępu (IAM),** a następnie kartę **Przypisania ról.**

   Tutaj widzimy, że twoje konto zostało przyznane zarówno _właściciel_ i _czytelnik_ role, zarówno w zakresie _tego zasobu_. Przypisanie planu ma również rolę _Właściciela,_ taką jak pierwsza grupa zasobów.

1. Wybierz kartę **Odmów przydziałów.**

   Przydział planu utworzył [przypisanie odmowy](../../../role-based-access-control/deny-assignments.md) w wdrożonej grupie zasobów w celu wymuszenia trybu _blokady_ planu tylko do odczytu. Przypisanie odmowy uniemożliwia osobie z odpowiednimi prawami na karcie _Przypisania ról podejmowanie_ określonych akcji. Przypisanie odmowy dotyczy _wszystkich podmiotów._

1. Wybierz przypisanie odmowy, a następnie wybierz stronę **Odmowa uprawnień** po lewej stronie.

   Przypisanie odmowy uniemożliwia wszystkie **\*** operacje z konfiguracją i **akcja,** ale umożliwia dostęp do odczytu, wykluczając ** \*/odczyt** za pośrednictwem **NotActions**.

1. Z modułu naciągowego portalu Azure wybierz **opcję PreProductionRG — Kontrola dostępu (IAM)**. Następnie wybierz stronę **Przegląd** po lewej stronie, a następnie przycisk **Usuń grupę zasobów.** Wprowadź nazwę _PreProductionRG,_ aby potwierdzić usunięcie, a następnie wybierz **pozycję Usuń** u dołu okienka.

   Powiadomienie portalu **Usuń grupę zasobów PreProductionRG nie powiodło się.** Błąd stwierdza, że podczas gdy konto ma uprawnienia do usuwania grupy zasobów, dostęp jest odrzucany przez przypisanie planu. Pamiętaj, że wybraliśmy tryb blokady planu _tylko do odczytu_ podczas przypisywania planu. Blokada planu uniemożliwia kontu z uprawnieniami, nawet _właściciel,_ usunięcie zasobu. Aby uzyskać więcej informacji, zobacz [blokowanie zasobów strategii](../concepts/resource-locking.md).

Te kroki pokazują, że nasze zasoby zostały utworzone zgodnie z definicją, a blokady planu uniemożliwiły niechciane usunięcie, nawet z konta z uprawnieniami.

## <a name="unassign-the-blueprint"></a>Niepodpisaj planu

Ostatnim krokiem jest usunięcie przypisania planu i zasobów, które wdrożono.
Usunięcie przypisania nie powoduje usunięcia wdrożonych artefaktów.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Przypisane plany.** Użyj filtrów, aby znaleźć przypisanie planu _przydziału dwa-rgs-z przypisaniami ról,_ a następnie zaznacz go.

1. Wybierz przycisk **Cozbij plan** u góry strony. Przeczytaj ostrzeżenie w oknie dialogowym potwierdzenia, a następnie wybierz **przycisk OK**.

   Po usunięciu przypisania planu blokady planu są również usuwane. Utworzone zasoby można ponownie usunąć przez konto z uprawnieniami.

1. Wybierz **grupy zasobów** z menu Azure, a następnie wybierz polecenie **ProductionRG**.

1. Po lewej stronie wybierz stronę **Kontrola dostępu (IAM),** a następnie kartę **Przypisania ról.**

Zabezpieczenia dla każdej grupy zasobów nadal mają wdrożone przypisania ról, ale przypisanie planu nie ma już dostępu _właściciela._

Po usunięciu **przypisania planu powiodło się** powiadomienie portalu, przejdź do następnego kroku.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy w tym samouczku usuń następujące zasoby:

- Grupa zasobów _ProductionRG_
- Grupa zasobów _PreProductionRG_
- Definicja planu _dwa-rgs-z przypisaniami roli_

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak utworzyć nowy plan z przykładowej definicji. Aby dowiedzieć się więcej o planach platformy Azure, przejdź do artykułu cyklu życia planu.

> [!div class="nextstepaction"]
> [Dowiedz się więcej o cyklu życia planu](../concepts/lifecycle.md)
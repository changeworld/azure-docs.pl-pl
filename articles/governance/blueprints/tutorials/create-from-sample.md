---
title: Utwórz środowisko na podstawie próbki planu
description: Przykładowy plan umożliwia tworzenie definicji planu, który konfiguruje dwie grupy zasobów i konfiguruje przypisania roli dla każdego.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/05/2019
ms.topic: tutorial
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 4f400e45d8defc304cf58c4bd05fa19f16d0501b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59785977"
---
# <a name="create-an-environment-from-a-blueprint-sample"></a>Utwórz środowisko na podstawie próbki planu

Przykładowe schematy zawierają przykłady co można zrobić za pomocą planów usługi Azure. Każda znajduje się przykładowy z określonym celem lub przeznaczenia, ale nie tworzenie kompletnego środowiska samodzielnie. Każdy element jest przeznaczony jako punkt wyjścia do eksplorowania przy użyciu plany platformy Azure przy użyciu różnych kombinacji uwzględnione artefaktów, projekty i parametry.

Używa następującego samouczka **grup zasobów z RBAC** przykładowy plan do prezentowania różnych aspektów korzystania z usługi schematy. Omówione są następujące czynności:

> [!div class="checklist"]
> - Utwórz nową definicję planu z próbki
> - Oznacz kopii przykładu jako **opublikowano**
> - Przypisz swoją kopię planu do istniejącej subskrypcji
> - Zbadaj wdrożonych zasobów dla przypisania
> - Cofnij przypisanie planu, aby usunąć blokady

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka, jest potrzebna jest subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="create-blueprint-definition-from-sample"></a>Tworzenie definicji planu z próbki

Najpierw należy wdrożyć przykładowy plan. Importowanie powoduje utworzenie nowego planu w danym środowisku, w oparciu o przykład.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Z **wprowadzenie** strony po lewej stronie, wybierz opcję **Utwórz** przycisku w obszarze _Tworzenie planu_.

1. Znajdź **grup zasobów z RBAC** przykładowy plan w ramach _inne przykłady_ i wybierz **korzystać z tej próbki**.

1. Wprowadź _podstawy_ próbki planu:

   - **Nazwa planu**: Podaj nazwę dla tej kopii przykładu planu. W tym samouczku użyjemy nazwy _dwóch rgs roli przypisania z_.
   - **Lokalizacja definicji**: Wielokropek i wybierz grupę zarządzania lub subskrypcję, aby zapisać kopię próbki.

1. Wybierz _artefaktów_ kartę w górnej części strony lub **dalej: Artefakty** w dolnej części strony.

1. Przejrzyj listę artefaktów, które tworzą przykładowy plan. Ta przykładowa aplikacja definiuje dwie grupy zasobów, nazwą wyświetlaną _ProdRG_ i _PreProdRG_. Końcowe nazwę i lokalizację każdej grupy zasobów są ustawiane podczas przypisywania planu. _ProdRG_ grupy zasobów jest przypisany _Współautor_ roli i _PreProdRG_ grupy zasobów jest przypisany _właściciela_ i  _Czytelnicy_ ról. Role przypisane do definicji są statyczne, ale ustawiono użytkownika, aplikacji lub grupy, któremu przypisano rolę podczas przypisywania planu.

1. Wybierz **Zapisz wersję roboczą** po zakończeniu przeglądania przykładu planu.

W tym kroku tworzy kopię przykładowej definicji planu w wybrana grupa zarządzania lub subskrypcji. Definicji planu zapisane odbywa się podobnie jak wszelkie planu tworzyć od podstaw. Przykład może zapisać do grupy zarządzania lub subskrypcji dowolną liczbę razy. Jednak każda kopia należy podać unikatową nazwę.

Gdy **Zapisywanie definicji planu powiodło się** pojawi się powiadomienie portalu, przejść do następnego kroku.

## <a name="publish-the-sample-copy"></a>Kopiowanie przykładowych publikowania

Utworzono kopię przykładowej planu w danym środowisku. Jest tworzony w **projekt** tryb i musi być **opublikowano** przed mogą zostać przypisane i wdrożony. Kopię przykładowych planu można dostosować w taki sposób, aby Twoje środowisko i potrzeb. W tym samouczku firma Microsoft nie będzie wprowadzać żadnych zmian.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz **planu definicje** strony po lewej stronie. Użyj filtrów, aby znaleźć _dwóch rgs roli przypisania z_ planu definicji, a następnie wybierz ją.

1. Wybierz **publikowania planu** w górnej części strony. W okienku po prawej stronie, podaj **wersji** jako _1.0_ dla tej kopii przykładu planu. Ta właściwość jest przydatne w przypadku, jeśli wprowadzisz zmiany w dalszej części. Podaj **zmienić uwagi** takie jak "pierwsza wersja opublikowany z grup zasobów z przykładem planu RBAC." Następnie wybierz pozycję **Publikuj** w dolnej części strony.

Ten krok sprawia, że można przypisać planu do subskrypcji. Po opublikowaniu, nadal można wprowadzać zmiany. Dodatkowe zmiany wymagają publikowanie z nową **wersji** wartość, aby śledzić różnice między różnymi wersjami tego samego definicji planu.

Gdy **publikowania planu definicji powiodło się** pojawi się powiadomienie portalu, przejść do następnego kroku.

## <a name="assign-the-sample-copy"></a>Przypisania kopiowania próbki

Gdy kopii przykładu planu została pomyślnie **opublikowano**, może ona zostać przypisana do subskrypcji w obrębie grupy zarządzania został zapisany w. Ten krok jest, gdzie parametry są przekazywane do unikatowość każdego wdrożenia kopii przykładu planu.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz **planu definicje** strony po lewej stronie. Użyj filtrów, aby znaleźć _dwóch rgs roli przypisania z_ planu definicji, a następnie wybierz ją.

1. Wybierz **planu Przypisz** w górnej części strony definicji planu.

1. Podaj wartości parametrów dla przypisania planu:

   - Podstawy

     - **Subskrypcje**: Wybierz co najmniej jednej z subskrypcji, które znajdują się w grupie zarządzania należy zapisać swoją kopię przykładowy plan do. Jeśli wybierzesz więcej niż jedną subskrypcję, przypisania zostaną utworzone dla każdego przy użyciu podanych parametrów.
     - **Nazwa przypisania**: Nazwa jest wstępnie wypełniona automatycznie na podstawie nazwy definicji planu.
     - **Lokalizacja**: Wybierz region dla tożsamości zarządzanej, które zostały utworzone w. Usługa Azure Blueprint używa tej tożsamości zarządzanej do wdrażania wszystkich artefaktów w przypisanej strategii. Aby dowiedzieć się więcej, zobacz [Tożsamości zarządzane dla zasobów platformy Azure](../../../active-directory/managed-identities-azure-resources/overview.md).
       Na potrzeby tego samouczka wybierz _wschodnie stany USA 2_.
     - **Wersja definicji planu**: Wybierz **opublikowano** wersji _1.0_ kopii Przykładowa definicja planu.

   - Zablokuj przypisanie

     Wybierz _tylko do odczytu_ tryb blokady planu. Aby uzyskać więcej informacji, zobacz [blokowanie zasobów strategii](../concepts/resource-locking.md).

   - Tożsamość zarządzana

     Pozostaw wartość domyślną _przypisanej w systemie_ opcji. Aby uzyskać więcej informacji, zobacz [zarządzanych tożsamości](../../../active-directory/managed-identities-azure-resources/overview.md).

   - Parametry artefaktu

     Parametry zdefiniowane w tej sekcji dotyczą artefaktu w ramach której jest zdefiniowany. Te parametry są [parametrów dynamicznych](../concepts/parameters.md#dynamic-parameters) ponieważ są one definiowane podczas przypisywania planu. Dla każdego artefaktu, należy ustawić wartość tego parametru, co to jest zdefiniowany w **wartość** kolumny. Aby uzyskać `{Your ID}`, wybierz konto użytkownika usługi Azure.

     |Nazwa artefaktu|Typ artefaktu|Nazwa parametru|Wartość|Opis|
     |-|-|-|-|-|
     |Grupa zasobów ProdRG|Grupa zasobów|Name (Nazwa)|ProductionRG|Definiuje nazwę pierwszej grupy zasobów.|
     |Grupa zasobów ProdRG|Grupa zasobów|Lokalizacja|Zachodnie stany USA 2|Ustawia lokalizację pierwszej grupy zasobów.|
     |Współautor|Przypisanie roli|Użytkownik lub grupa|{ID}|Określa, który użytkownik lub grupa, aby udzielić _Współautor_ przypisanie roli w ramach pierwszej grupy zasobów.|
     |Grupa zasobów PreProdRG|Grupa zasobów|Name (Nazwa)|PreProductionRG|Definiuje nazwę drugiej grupy zasobów.|
     |Grupa zasobów PreProdRG|Grupa zasobów|Lokalizacja|Zachodnie stany USA|Ustawia lokalizację grupy zasobów, do drugiego.|
     |Właściciel|Przypisanie roli|Użytkownik lub grupa|{ID}|Określa, który użytkownik lub grupa, aby udzielić _właściciela_ przypisanie roli w drugiej grupie zasobów.|
     |Czytelnicy|Przypisanie roli|Użytkownik lub grupa|{ID}|Określa, który użytkownik lub grupa, aby udzielić _czytelnicy_ przypisanie roli w drugiej grupie zasobów.|

1. Po wprowadzeniu wszystkich parametrów, wybierz **przypisać** w dolnej części strony.

Ten krok umożliwia wdrażanie zdefiniowanych zasobów i konfiguruje wybrane **przypisania blokady**. Plan blokady może potrwać do 30 minut do zastosowania.

Gdy **przypisywanie definicji planu powiodło się** pojawi się powiadomienie portalu, przejść do następnego kroku.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Sprawdź zasoby wdrożone przez przypisanie

Przypisanie planu tworzy i śledzi artefakty zdefiniowane w definicji planu. Możemy zobaczyć stan zasobów ze strony przypisanie planu i sprawdzając zasobów bezpośrednio.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz **przypisane plany** strony po lewej stronie. Użyj filtrów, aby znaleźć _Assignment-two-rgs-with-role-assignments_ planu przypisania, a następnie wybierz ją.

   Na tej stronie widać, przypisanie powiodło się i listę utworzonych zasobów wraz z ich stan blokady planu. Jeśli przypisanie zostanie zaktualizowane, **operacji przypisania** listy rozwijanej przedstawia szczegółowe informacje dotyczące wdrażania dla każdej definicji wersji. Wszystkie wymienione zasoby, które utworzono można klikać i otwiera tej strony właściwości zasobów.

1. Wybierz **ProductionRG** grupy zasobów.

   Widzimy, że nazwa grupy zasobów jest **ProductionRG** , a nie nazwę wyświetlaną artefaktu _ProdRG_. Ta nazwa jest zgodna z wartością ustawioną podczas przypisywania planu.

1. Wybierz **kontrola dostępu (IAM)** strony po lewej stronie i następnie **przypisań ról** kartę.

   Widać, czy Twoje konto ma udzielone _Współautor_ rolę w zakresie _ten zasób_. _Assignment-two-rgs-with-role-assignments_ przypisaniu planu _właściciela_ roli, ponieważ został użyty do utworzenia grupy zasobów. Te uprawnienia są również używane do zarządzania zasobami przy użyciu blokady skonfigurowanych planu.

1. W portalu Azure obszarze nawigacji wybierz **Assignment-two-rgs-with-role-assignments** aby powrócić po jednej stronie, następnie wybierz pozycję **PreProductionRG** grupy zasobów.

1. Wybierz **kontrola dostępu (IAM)** strony po lewej stronie i następnie **przypisań ról** kartę.

   Widać, czy Twoje konto ma udzielone zarówno _właściciela_ i _czytnika_ ról, zarówno w zakresie _ten zasób_. Ma również przypisanie planu _właściciela_ ról, takich jak pierwszej grupy zasobów.

1. Wybierz **Odmów przypisania** kartę.

   Przypisanie planu utworzone [Odmów przypisania](../../../role-based-access-control/deny-assignments.md) w grupie zasobów wdrożonego, aby wymusić _tylko do odczytu_ tryb blokady planu. Przypisanie Odmów zapobiega osoba, która ma odpowiednie uprawnienia na _przypisań ról_ kartę możliwość podejmowania określonych działań. Wpływa na przypisanie Odmów _wszystkich podmiotów zabezpieczeń_.

1. Wybierz przypisanie Odmów, a następnie wybierz **odmowa uprawnień** strony po lewej stronie.

   Przypisanie Odmów uniemożliwia wszystkie operacje wykonywane **\*** i **akcji** konfiguracji, ale umożliwia dostęp do odczytu, z wyłączeniem  **\* /odczyt**za pośrednictwem **NotActions**.

1. W portalu Azure obszarze nawigacji wybierz **PreProductionRG — kontrola dostępu (IAM)**. Następnie wybierz pozycję **Przegląd** strony po lewej stronie i następnie **Usuń grupę zasobów** przycisku. Wprowadź nazwę _PreProductionRG_ o potwierdzenie usunięcia i wybierz pozycję **Usuń** w dolnej części okienka.

   Powiadomienia portalu **PreProductionRG nie powiodło się. Usuń grupę zasobów** jest wyświetlana. Przez przypisanie planu opisu błędu wynika, że gdy Twoje konto ma uprawnienia do usuwania grupy zasobów, jest odmowa dostępu. Należy pamiętać, że wybrano _tylko do odczytu_ planu tryb blokady podczas przypisywania planu. Plan zastosowana Blokada zapobiega konta z uprawnieniami, nawet _właściciela_, usunięcie tego zasobu. Aby uzyskać więcej informacji, zobacz [blokowanie zasobów strategii](../concepts/resource-locking.md).

Te kroki pokazują, że nasze zaawansowane zasoby zostały utworzone zgodnie z definicją i blokad planu uniemożliwił usuwanie niepożądanych, nawet z konta z uprawnieniami.

## <a name="unassign-the-blueprint"></a>Cofnij przypisanie planu

Ostatnim krokiem jest, aby usunąć przypisanie planu i zasobów, które je wdrożyć.
Usuwanie przypisania nie powoduje usunięcia wdrożonej artefaktów.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz **przypisane plany** strony po lewej stronie. Użyj filtrów, aby znaleźć _Assignment-two-rgs-with-role-assignments_ planu przypisania, a następnie wybierz ją.

1. Wybierz **Cofnij przypisanie planu** znajdujący się u góry strony. Przeczytaj ostrzeżenie w oknie dialogowym potwierdzenia, a następnie wybierz **OK**.

   Za pomocą przypisanie planu usunięte blokady planu są również usuwane. Ponownie można usunąć zasoby utworzone za pomocą konta z uprawnieniami.

1. Wybierz **grup zasobów** z menu platformy Azure, następnie wybierz pozycję **ProductionRG**.

1. Wybierz **kontrola dostępu (IAM)** strony po lewej stronie i następnie **przypisań ról** kartę.

Zabezpieczenia dla poszczególnych grup zasobów środki, nieopłacone przypisań ról wdrożonych, ale przypisanie planu nie ma już _właściciela_ dostępu.

Gdy **usuwanie przypisania planu powiodło się** pojawi się powiadomienie portalu, przejść do następnego kroku.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu tego samouczka, należy usunąć następujące zasoby:

- Grupa zasobów _ProductionRG_
- Grupa zasobów _PreProductionRG_
- Definicja planu _dwóch rgs roli przypisania z_

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej na temat [cyklu życia strategii](../concepts/lifecycle.md)
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../concepts/parameters.md)
- Dowiedz się, jak używać [blokowania zasobów strategii](../concepts/resource-locking.md)
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../concepts/sequencing-order.md)
- Dowiedz się, jak [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md)
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](../troubleshoot/general.md)
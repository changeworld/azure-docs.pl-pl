---
title: 'Samouczek: Konfigurowanie inicjowania obsługi administracyjnej przychodzącej successfactors w usłudze Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować aprowizacji przychodzącej z SuccessFactors do usługi Azure AD
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: fac4f61e-d942-4429-a298-9ba74db95077
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 09501a80d6ddcbbc9fa6cc08e36f47beb13d1663
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063226"
---
# <a name="tutorial-configure-sap-successfactors-to-azure-ad-user-provisioning-preview"></a>Samouczek: Konfigurowanie czynników sukcesu SAP do inicjowania obsługi administracyjnej użytkowników usługi Azure AD (wersja zapoznawcza)
Celem tego samouczka jest pokazanie kroków, które należy wykonać w celu aprowizowania danych procesu roboczego z centrum pracownika SuccessFactors do usługi Azure Active Directory z opcjonalnym odpisem adresu e-mail do SuccessFactors. Ta integracja jest w publicznej wersji zapoznawczej i obsługuje pobieranie ponad [70+ atrybutów użytkownika](../app-provisioning/sap-successfactors-attribute-reference.md) z SuccessFactors Employee Central. 

>[!NOTE]
>Użyj tego samouczka, jeśli użytkownicy, których chcesz udostępnić z SuccessFactors są tylko w chmurze użytkowników, którzy nie potrzebują lokalnego konta AD. Jeśli użytkownicy wymagają tylko lokalnego konta usługi AD lub zarówno konta usługi AD, jak i usługi Azure AD, zapoznaj się z samouczkiem [konfigurowania funkcji SAP SuccessFactors do](sap-successfactors-inbound-provisioning-tutorial.md#overview) inicjowania obsługi administracyjnej użytkownika usługi Active Directory. 

## <a name="overview"></a>Omówienie

[Usługa inicjowania obsługi administracyjnej użytkowników usługi Azure Active Directory](../app-provisioning/user-provisioning.md) integruje się z [centrum pracowników SuccessFactors](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) w celu zarządzania cyklem życia tożsamości użytkowników. 

Przepływy pracy inicjowania obsługi administracyjnej użytkowników SuccessFactors obsługiwane przez usługę inicjowania obsługi administracyjnej użytkowników usługi Azure AD umożliwiają automatyzację następujących scenariuszy zarządzania zasobami ludzkimi i cyklem życia tożsamości:

* **Zatrudnianie nowych pracowników** — gdy nowy pracownik jest dodawany do SuccessFactors, konto użytkownika jest automatycznie tworzone w usłudze Azure Active Directory i opcjonalnie usługi Office 365 i [innych aplikacji SaaS obsługiwanych przez usługę Azure AD,](../app-provisioning/user-provisioning.md)z odpisem adresu e-mail do SuccessFactors.

* **Aktualizacja atrybutu i profilu pracownika** — gdy rekord pracownika jest aktualizowany w aplikacjach SuccessFactors (takich jak nazwa, tytuł lub menedżer), ich konto użytkownika zostanie automatycznie zaktualizowane w usłudze Azure Active Directory i opcjonalnie usługi Office 365 i [innych aplikacji SaaS obsługiwanych przez usługę Azure AD.](../app-provisioning/user-provisioning.md)

* **Wypatrywanie pracowników** — po zakończeniu pracy pracownika w programach SuccessFactors jego konto użytkownika jest automatycznie wyłączane w usłudze Azure Active Directory i opcjonalnie w usłudze Office 365 i [innych aplikacjach SaaS obsługiwanych przez usługę Azure AD.](../app-provisioning/user-provisioning.md)

* **Pracownik ponownie pracuje** — gdy pracownik zostanie ponownie zatrudniony w successfactors, jego stare konto może zostać automatycznie ponownie aktywowane lub ponownie aprowizowane (w zależności od preferencji) do usługi Azure Active Directory i opcjonalnie usługi Office 365 i [innych aplikacji SaaS obsługiwanych przez usługę Azure AD.](../app-provisioning/user-provisioning.md)

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Do kogo najlepiej nadaje się to rozwiązanie do inicjowania obsługi administracyjnej użytkownika?

To rozwiązanie do inicjowania obsługi administracyjnej użytkowników usługi Azure Active Directory jest idealnie odpowiednie dla:

* Organizacje, które pragną wstępnie utworzonego rozwiązania opartego na chmurze dla inicjowania obsługi administracyjnej użytkowników SuccessFactors

* Organizacje, które wymagają bezpośredniego inicjowania obsługi administracyjnej od successfactors do usługi Azure Active Directory

* Organizacje, które wymagają inicjowania obsługi administracyjnej użytkowników przy użyciu danych uzyskanych z [centrum pracowników SuccessFactors (EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organizacje korzystające z usługi Office 365 do poczcie e-mail

## <a name="solution-architecture"></a>Architektura rozwiązania

W tej sekcji opisano architekturę rozwiązania inicjowania obsługi administracyjnej dla użytkowników tylko w chmurze. Istnieją dwa powiązane przepływy:

* **Autorytatywny przepływ danych hr — od SuccessFactors do Azure Active Directory:** W tym przepływie zdarzenia procesu roboczego (takie jak Nowi zatrudnienia, transfery, zakończenia) najpierw występują w centrum roboczym successfactors w chmurze, a następnie dane zdarzenia są przepływane do usługi Azure Active Directory. W zależności od zdarzenia może to prowadzić do tworzenia/aktualizacji/włączania/wyłączania operacji w usłudze Azure AD.
* **Przepływ storn zwrotności poczty e-mail — z lokalnej usługi Active Directory do SuccessFactors:** Po zakończeniu tworzenia konta w usłudze Azure Active Directory wartość atrybutu wiadomości e-mail lub nazwy UPN wygenerowanej w usłudze Azure AD można zapisać z powrotem do SuccessFactors.

  ![Omówienie](./media/sap-successfactors-inbound-provisioning/sf2aad-overview.png)

### <a name="end-to-end-user-data-flow"></a>Przepływ danych użytkownika końcowego

1. Zespół HR wykonuje transakcje pracowników (Stowarzyszania /Przeprowadzki/Urlopowicze lub Nowi pracownicy/transfery/rozwiązania) w Centrum Pracowników SuccessFactors
2. Usługa inicjowania obsługi administracyjnej usługi Azure AD uruchamia zaplanowane synchronizacje tożsamości z SuccessFactors EC i identyfikuje zmiany, które muszą być przetwarzane w celu synchronizacji z lokalną usługą Active Directory.
3. Usługa inicjowania obsługi administracyjnej usługi Azure AD określa zmianę i wywołuje operację create/update/enable/disable dla użytkownika w usłudze Azure AD.
4. Jeśli [aplikacja SuccessFactors Writeback](sap-successfactors-writeback-tutorial.md) jest skonfigurowana, adres e-mail użytkownika jest pobierany z usługi Azure AD. 
5. Usługa inicjowania obsługi administracyjnej usługi Azure AD zapisuje atrybut wiadomości e-mail do SuccessFactors, na podstawie pasującego atrybutu używane.

## <a name="planning-your-deployment"></a>Planowanie wdrożenia

Konfigurowanie obsługi administracyjnej użytkownika opartej na usłudze Cloud HR z funkcji SuccessFactors do usługi Azure AD wymaga znacznego planowania obejmującego różne aspekty, takie jak:

* Określanie pasującego identyfikatora 
* Mapowanie atrybutów
* Transformacja atrybutów 
* Filtry określania zakresu

Zapoznaj się z planem wdrażania zasobów ludzkich w [chmurze,](../app-provisioning/plan-cloud-hr-provision.md) aby uzyskać kompleksowe wskazówki dotyczące tych tematów. 

## <a name="configuring-successfactors-for-the-integration"></a>Konfigurowanie successfactors dla integracji

Typowym wymaganiem wszystkich łączników inicjowania obsługi administracyjnej SuccessFactors jest to, że wymagają one poświadczeń konta SuccessFactors z odpowiednimi uprawnieniami do wywoływania interfejsów API OData. W tej sekcji opisano kroki, aby utworzyć konto usługi w SuccessFactors i udzielić odpowiednich uprawnień. 

* [Tworzenie/identyfikowanie konta użytkownika interfejsu API w successfactors](#createidentify-api-user-account-in-successfactors)
* [Tworzenie roli uprawnień interfejsu API](#create-an-api-permissions-role)
* [Tworzenie grupy uprawnień dla użytkownika interfejsu API](#create-a-permission-group-for-the-api-user)
* [Udziel roli uprawnień grupie uprawnień](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>Tworzenie/identyfikowanie konta użytkownika interfejsu API w successfactors
Praca z zespołem administracyjnym SuccessFactors lub partnerem implementacji, aby utworzyć lub zidentyfikować konto użytkownika w SuccessFactors, które będą używane do wywoływania interfejsów API OData. Poświadczenia nazwy użytkownika i hasła tego konta będą wymagane podczas konfigurowania aplikacji inicjowania obsługi administracyjnej w usłudze Azure AD. 

### <a name="create-an-api-permissions-role"></a>Tworzenie roli uprawnień interfejsu API

* Zaloguj się do SAP SuccessFactors za pomocą konta użytkownika, które ma dostęp do Centrum administracyjnego.
* Wyszukaj *pozycję Zarządzaj rolami uprawnień,* a następnie wybierz pozycję **Zarządzaj rolami uprawnień** z wyników wyszukiwania.
  ![Zarządzanie rolami uprawnień](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* Na liście ról uprawnień kliknij pozycję **Utwórz nowy**.
  > [!div class="mx-imgBorder"]
  > ![Utwórz nową rolę uprawnień](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* Dodaj **nazwę roli** i **opis** dla nowej roli uprawnień. Nazwa i opis powinny wskazywać, że rola jest dla uprawnień użycia interfejsu API.
  > [!div class="mx-imgBorder"]
  > ![Szczegóły roli uprawnień](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* W obszarze Ustawienia uprawnień kliknij pozycję **Uprawnienie...**, a następnie przewiń listę uprawnień w dół i kliknij pozycję **Zarządzaj narzędziami integracji**. Zaznacz pole wyboru **Zezwalaj administratorowi na dostęp do interfejsu API OData za pomocą uwierzytelniania podstawowego**.
  > [!div class="mx-imgBorder"]
  > ![Zarządzanie narzędziami integracji](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* Przewiń w dół w tym samym polu i wybierz **pozycję Centralny interfejs API pracownika**. Dodaj uprawnienia, jak pokazano poniżej, aby czytać za pomocą interfejsu API ODATA i edytować za pomocą interfejsu API ODATA. Wybierz opcję edycji, jeśli planujesz użyć tego samego konta w scenariuszu Writeback to SuccessFactors. 
  > [!div class="mx-imgBorder"]
  > ![Odczytanie uprawnień do zapisu](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)
* Kliknij **gotowe**. Kliknij **pozycję Zapisz zmiany**.

### <a name="create-a-permission-group-for-the-api-user"></a>Tworzenie grupy uprawnień dla użytkownika interfejsu API

* W Centrum administracyjnym SuccessFactors wyszukaj pozycję *Zarządzaj grupami uprawnień*, a następnie wybierz pozycję **Zarządzaj grupami uprawnień** z wyników wyszukiwania.
  > [!div class="mx-imgBorder"]
  > ![Zarządzanie grupami uprawnień](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* W oknie Zarządzanie grupami uprawnień kliknij pozycję **Utwórz nowy**.
  > [!div class="mx-imgBorder"]
  > ![Dodawanie nowej grupy](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
* Dodaj nazwę grupy dla nowej grupy. Nazwa grupy powinna wskazywać, że grupa jest dla użytkowników interfejsu API.
  > [!div class="mx-imgBorder"]
  > ![Nazwa grupy uprawnień](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* Dodaj członków do grupy. Na przykład można wybrać **nazwę użytkownika** z menu rozwijanego Puli osób, a następnie wprowadź nazwę użytkownika konta interfejsu API, które będą używane do integracji. 
  > [!div class="mx-imgBorder"]
  > ![Dodawanie członków grupy](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
* Kliknij **przycisk Gotowe,** aby zakończyć tworzenie grupy uprawnień.

### <a name="grant-permission-role-to-the-permission-group"></a>Udziel roli uprawnień grupie uprawnień

* W Centrum administracyjnym SuccessFactors wyszukaj pozycję *Zarządzaj rolami uprawnień*, a następnie wybierz pozycję **Zarządzaj rolami uprawnień** z wyników wyszukiwania.
* Z **listy ról uprawnień**wybierz rolę utworzoną dla uprawnień użycia interfejsu API.
* W obszarze **Nadaj tę rolę do...** kliknij przycisk **Dodaj....**
* Wybierz **pozycję Grupa uprawnień...** z menu rozwijanego, a następnie kliknij polecenie **Wybierz...** aby otworzyć okno Grupy, aby wyszukać i wybrać grupę utworzoną powyżej. 
  > [!div class="mx-imgBorder"]
  > ![Dodawanie grupy uprawnień](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* Przejrzyj przyznanie roli uprawnień grupie uprawnień. 
  > [!div class="mx-imgBorder"]
  > ![Szczegóły roli i grupy uprawnień](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* Kliknij **pozycję Zapisz zmiany**.

## <a name="configuring-user-provisioning-from-successfactors-to-azure-ad"></a>Konfigurowanie inicjowania obsługi administracyjnej użytkowników z czynników successfactors do usługi Azure AD

Ta sekcja zawiera kroki dla obsługi administracyjnej konta użytkownika z SuccessFactors do usługi Azure AD.

* [Dodawanie aplikacji łącznika inicjowania obsługi administracyjnej i konfigurowanie łączności z czynnikami SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Konfigurowanie mapowań atrybutów](#part-2-configure-attribute-mappings)
* [Włączanie i uruchamianie inicjowania obsługi administracyjnej użytkowników](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Część 1: Dodaj aplikację łącznika inicjowania obsługi administracyjnej i skonfiguruj łączność z SuccessFactors

**Aby skonfigurować pliki SuccessFactors do inicjowania obsługi administracyjnej usługi Azure AD:**

1. Przejdź do strony <https://portal.azure.com>

2. Na lewym pasku nawigacyjnym wybierz pozycję **Azure Active Directory**

3. Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie **wszystkie aplikacje**.

4. Wybierz **pozycję Dodaj aplikację**i wybierz kategorię **Wszystkie.**

5. Wyszukaj **dane SuccessFactors w usłudze Azure Active Directory User Provisioning**i dodaj tę aplikację z galerii.

6. Po dodaniu aplikacji i wyświetleniu ekranu szczegółów aplikacji wybierz pozycję **Inicjowanie obsługi administracyjnej**

7. Zmienianie **trybu** **inicjowania obsługi administracyjnej** na **automatyczny**

8. Wypełnij sekcję **Poświadczenia administratora** w następujący sposób:

   * **Nazwa użytkownika administratora** — wprowadź nazwę użytkownika konta użytkownika interfejsu API SuccessFactors z dołączonym identyfikatorem firmy. Ma format: **nazwa\@użytkownika companyID**

   * **Hasło administratora –** Wprowadź hasło konta użytkownika interfejsu API SuccessFactors. 

   * **Adres URL dzierżawy —** Wprowadź nazwę punktu końcowego usług interfejsu API SuccessFactors. Wprowadź tylko nazwę hosta serwera bez http lub https. Ta wartość powinna wyglądać następująco: **api-server-name.successfactors.com**.

   * **Wiadomość e-mail z powiadomieniem –** Wprowadź swój adres e-mail i zaznacz pole wyboru "Wyślij wiadomość e-mail w przypadku wystąpienia błędu".
    > [!NOTE]
    > Usługa inicjowania obsługi administracyjnej usługi Azure AD wysyła powiadomienie e-mail, jeśli zadanie inicjowania obsługi administracyjnej przechodzi do stanu [kwarantanny.](/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

   * Kliknij przycisk **Testuj połączenie.** Jeśli test połączenia zakończy się pomyślnie, kliknij przycisk **Zapisz** u góry. Jeśli to się nie powiedzie, sprawdź, czy poświadczenia SuccessFactors i adres URL są prawidłowe.
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sf2aad-provisioning-creds.png)

   * Po pomyślnym zapisaniu poświadczeń w sekcji **Mapowania** zostanie wyświetlone domyślne mapowanie **Synchronizuj użytkowników błędów na usługę Azure Active Directory**

### <a name="part-2-configure-attribute-mappings"></a>Część 2: Konfigurowanie mapowań atrybutów

W tej sekcji skonfigurujesz sposób przepływu danych użytkownika z SuccessFactors do usługi Active Directory.

1. Na karcie Inicjowanie obsługi administracyjnej w obszarze **Mapowania**kliknij pozycję **Synchronizuj użytkowników successfactors z usługą Azure Active Directory**.

1. W polu **Zakres obiektu źródłowego** można wybrać zestawy użytkowników w SuccessFactors powinny być w zakresie inicjowania obsługi administracyjnej do usługi Azure AD, definiując zestaw filtrów opartych na atrybutach. Domyślnym zakresem jest "wszyscy użytkownicy w SuccessFactors". Przykładowe filtry:

   * Przykład: Zakres do użytkowników z personIdExternal między 1000000 i 2000000 (z wyłączeniem 20000000)

      * Atrybut: personIdExternal

      * Operator: REGEX Match

      * Wartość: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Przykład: Tylko pracownicy, a nie pracownicy warunkowi

      * Atrybut: EmployeeID

      * Operator: NIE MA WARTOŚCI NULL

   > [!TIP]
   > Podczas konfigurowania aplikacji inicjowania obsługi administracyjnej po raz pierwszy, należy przetestować i zweryfikować mapowania atrybutów i wyrażeń, aby upewnić się, że daje pożądany wynik. Firma Microsoft zaleca użycie filtrów zakresu w obszarze **Zakres obiektu źródłowego** do testowania mapowań z kilkoma użytkownikami testowymi z SuccessFactors. Po sprawdzeniu, że mapowania działają, można usunąć filtr lub stopniowo rozszerzać go, aby uwzględnić więcej użytkowników.

   > [!CAUTION] 
   > Domyślnym zachowaniem aparatu inicjowania obsługi administracyjnej jest wyłączenie/usunięcie użytkowników, którzy wychodzą poza zakres. Może to nie być pożądane w SuccessFactors do integracji usługi Azure AD. Aby zastąpić to domyślne zachowanie, zapoznaj się z [artykułem Pomiń usuwanie kont użytkowników, które wykraczają poza zakres](../app-provisioning/skip-out-of-scope-deletions.md)
  
1. W polu **Akcje obiektów docelowych** można globalnie filtrować, jakie akcje są wykonywane w usłudze Active Directory. **Tworzenie** i **aktualizowanie** są najczęściej.

1. W sekcji **Mapowania atrybutów** można zdefiniować sposób mapowania poszczególnych atrybutów SuccessFactors do atrybutów usługi Active Directory.

  >[!NOTE]
  >Pełna lista atrybutów SuccessFactors obsługiwanych przez aplikację znajduje się w [części SuccessFactors Attribute Reference](../app-provisioning/sap-successfactors-attribute-reference.md)


1. Kliknij istniejące mapowanie atrybutów, aby je zaktualizować, lub kliknij pozycję **Dodaj nowe mapowanie** u dołu ekranu, aby dodać nowe mapowania. Mapowanie poszczególnych atrybutów obsługuje następujące właściwości:

      * **Typ mapowania**

         * **Direct** — zapisuje wartość SuccessFactors atrybut do ad atrybut, bez zmian

         * **Stała** — zapis statycznej, stałej wartości ciągu do atrybutu AD

         * **Wyrażenie** — umożliwia zapisanie wartości niestandardowej do atrybutu AD, na podstawie co najmniej jednego atrybutu SuccessFactors. [Aby uzyskać więcej informacji, zobacz ten artykuł na temat wyrażeń](../app-provisioning/functions-for-customizing-application-data.md).

      * **Atrybut Source** — atrybut użytkownika z czynników SuccessFactors

      * **Wartość domyślna** — opcjonalnie. Jeśli atrybut źródłowy ma pustą wartość, mapowanie zapisze tę wartość.
            Najczęstszą konfiguracją jest pozostawienie tego pustego miejsca.

      * **Atrybut docelowy** — atrybut użytkownika w usłudze Active Directory.

      * **Dopasuj obiekty przy użyciu tego atrybutu** — czy to mapowanie powinno służyć do jednoznacznej identyfikacji użytkowników między SuccessFactors i Active Directory. Ta wartość jest zazwyczaj ustawiana w polu Identyfikator procesu roboczego dla successfactors, który jest zazwyczaj mapowany do jednego z atrybutów identyfikatora pracownika w usłudze Active Directory.

      * **Pierwszeństwo dopasowania** — można ustawić wiele pasujących atrybutów. Gdy istnieje wiele, są one oceniane w kolejności zdefiniowanej przez to pole. Jak tylko zostanie znaleziony dopasowania, nie dalsze pasujące atrybuty są oceniane.

      * **Zastosuj to mapowanie**

         * **Zawsze** — stosowanie tego mapowania zarówno w przypadku akcji tworzenia, jak i aktualizacji użytkownika

         * **Tylko podczas tworzenia** — zastosuj to mapowanie tylko do akcji tworzenia użytkownika

1. Aby zapisać mapowania, kliknij przycisk **Zapisz** u góry sekcji Mapowanie atrybutów.

Po zakończeniu konfiguracji mapowania atrybutów można teraz [włączyć i uruchomić usługę inicjowania obsługi administracyjnej użytkowników](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Włączanie i uruchamianie inicjowania obsługi administracyjnej użytkowników

Po zakończeniu konfiguracji aplikacji inicjowania obsługi administracyjnej SuccessFactors można włączyć usługę inicjowania obsługi administracyjnej w witrynie Azure portal.

> [!TIP]
> Domyślnie po włączeniu usługi inicjowania obsługi administracyjnej, rozpocznie operacje inicjowania obsługi administracyjnej dla wszystkich użytkowników w zakresie. Jeśli występują błędy w mapowaniu lub problemy z danymi workday, zadanie inicjowania obsługi administracyjnej może zakończyć się niepowodzeniem i przejść do stanu kwarantanny. Aby tego uniknąć, jako najlepszą praktykę, zaleca się skonfigurowanie **filtru zakresu obiektów źródłowych** i przetestowanie mapowań atrybutów z kilkoma użytkownikami testowymi przed uruchomieniem pełnej synchronizacji dla wszystkich użytkowników. Po sprawdzeniu, że mapowania działają i dają pożądane wyniki, można usunąć filtr lub stopniowo rozszerzać go, aby uwzględnić więcej użytkowników.

1. Na karcie **Inicjowanie obsługi administracyjnej** ustaw **stan inicjowania obsługi administracyjnej** **na Włączone**.

2. Kliknij przycisk **Zapisz**.

3. Ta operacja rozpocznie synchronizację początkową, która może zająć zmienną liczbę godzin w zależności od liczby użytkowników w dzierżawie SuccessFactors. Można sprawdzić pasek postępu, aby śledzić postęp cyklu synchronizacji. 

4. W dowolnym momencie sprawdź **inspekcję dzienników** kartę w witrynie Azure portal, aby zobaczyć, jakie akcje usługa inicjowania obsługi administracyjnej wykonała. Dzienniki inspekcji zawierają listę wszystkich zdarzeń synchronizacji wykonywanych przez usługę inicjowania obsługi administracyjnej, takich jak użytkownicy są odczytywane z programu Workday, a następnie dodawane lub aktualizowane do usługi Active Directory. 

5. Po zakończeniu synchronizacji początkowej zostanie ono napisać raport podsumowujący inspekcję na karcie **Inicjowanie obsługi administracyjnej,** jak pokazano poniżej.

   > [!div class="mx-imgBorder"]
   > ![Pasek postępu inicjowania obsługi administracyjnej](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o obsługiwanych atrybutach SuccessFactors dla inicjowania obsługi administracyjnej ruchu przychodzącego](../app-provisioning/sap-successfactors-attribute-reference.md)
* [Dowiedz się, jak skonfigurować zapisywanie zwrotne wiadomości e-mail do SuccessFactors](sap-successfactors-writeback-tutorial.md)
* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)
* [Dowiedz się, jak skonfigurować logowanie jednokrotne między successfactors a usługą Azure Active Directory](successfactors-tutorial.md)
* [Dowiedz się, jak zintegrować inne aplikacje SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Dowiedz się, jak eksportować i importować konfiguracje inicjowania obsługi administracyjnej](../app-provisioning/export-import-provisioning-configuration.md)



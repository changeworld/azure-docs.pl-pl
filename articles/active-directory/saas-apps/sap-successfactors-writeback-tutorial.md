---
title: 'Samouczek: Konfigurowanie storców successfactors w usłudze Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować przypis dot.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: ad255bd4-9e50-43a1-a92b-359215868b6b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 2de0cdd32428884170f549afacdbd52c3a10c93f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060052"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-sap-successfactors-preview"></a>Samouczek: Konfigurowanie storazy atrybutu z usługi Azure AD do sap SuccessFactors (Wersja zapoznawcza)
Celem tego samouczka jest pokazanie kroków, które należy wykonać, aby wykonać atrybuty stornuj z usługi Azure AD do SuccessFactors Employee Central. Jedynym atrybutem obecnie obsługiwanym dla storca zwrotnego jest atrybut e-mail. 

## <a name="overview"></a>Omówienie

Po skonfigurowaniu integracji inicjowania obsługi administracyjnej przy użyciu [funkcji SuccessFactors do lokalnej](sap-successfactors-inbound-provisioning-tutorial.md) aplikacji inicjowania obsługi administracyjnej usługi AD lub aplikacji [SuccessFactors do](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) aplikacji inicjowania obsługi administracyjnej usługi Azure AD można opcjonalnie skonfigurować aplikację SuccessFactors Writeback do zapisywania adresu e-mail z powrotem do SuccessFactors. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Do kogo najlepiej nadaje się to rozwiązanie do inicjowania obsługi administracyjnej użytkownika?

To rozwiązanie do inicjowania obsługi administracyjnej dla użytkowników SuccessFactors writeback jest idealnie odpowiednie dla:

* Organizacje korzystające z usługi Office 365, które pragną odpisywać autorytatywne atrybuty zarządzane przez IT (takie jak adres e-mail) z powrotem do SuccessFactors

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

## <a name="configuring-successfactors-writeback"></a>Konfigurowanie stortowania successfactors

W tej sekcji przedstawiono kroki 

* [Dodawanie aplikacji łącznika inicjowania obsługi administracyjnej i konfigurowanie łączności z czynnikami SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Konfigurowanie mapowań atrybutów](#part-2-configure-attribute-mappings)
* [Włączanie i uruchamianie inicjowania obsługi administracyjnej użytkowników](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Część 1: Dodaj aplikację łącznika inicjowania obsługi administracyjnej i skonfiguruj łączność z SuccessFactors

**Aby skonfigurować writeback SuccessFactors:**

1. Przejdź do strony <https://portal.azure.com>

2. Na lewym pasku nawigacyjnym wybierz pozycję **Azure Active Directory**

3. Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie **wszystkie aplikacje**.

4. Wybierz **pozycję Dodaj aplikację**i wybierz kategorię **Wszystkie.**

5. Wyszukaj **wpisanie z powrotem successfactors**i dodaj tę aplikację z galerii.

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
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * Po pomyślnym zapisaniu poświadczeń w sekcji **Mapowania** zostanie wyświetlone domyślne mapowanie **Synchronizuj użytkowników usługi Azure Active Directory na dane SuccessFactors**

### <a name="part-2-configure-attribute-mappings"></a>Część 2: Konfigurowanie mapowań atrybutów

W tej sekcji skonfigurujesz sposób przepływu danych użytkownika z SuccessFactors do usługi Active Directory.

1. Na karcie Inicjowanie obsługi administracyjnej w obszarze **Mapowania**kliknij pozycję **Synchronizuj użytkowników usługi Azure Active Directory z czynnikami sukcesu**.

1. W polu **Zakres obiektu źródłowego** można wybrać zestawy użytkowników w usłudze Azure AD, które powinny być brane pod uwagę w przypadku stornia zwrotnego, definiując zestaw filtrów opartych na atrybutach. Domyślnym zakresem jest "wszyscy użytkownicy w usłudze Azure AD". 
   > [!TIP]
   > Podczas konfigurowania aplikacji inicjowania obsługi administracyjnej po raz pierwszy, należy przetestować i zweryfikować mapowania atrybutów i wyrażeń, aby upewnić się, że daje pożądany wynik. Firma Microsoft zaleca użycie filtrów zakresu w obszarze **Zakres obiektu źródłowego** do testowania mapowań z kilkoma użytkownikami testowymi z usługi Azure AD. Po sprawdzeniu, że mapowania działają, można usunąć filtr lub stopniowo rozszerzać go, aby uwzględnić więcej użytkowników.

1. Pole **Akcje obiektu docelowego** obsługuje tylko operację **Aktualizuj.**

1. W sekcji **Mapowania atrybutów** można zmienić tylko pasujący identyfikator, który jest używany do łączenia profilu użytkownika SuccessFactors z użytkownikiem usługi Azure AD i który atrybut w usłudze Azure AD służy jako źródło wiadomości e-mail. 
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sfwb-attribute-mapping.png)

   >[!NOTE]
   >Writeback SuccessFactors obsługuje tylko atrybut e-mail. Nie należy **dodawać nowego mapowania** do dodawania nowych atrybutów. 

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

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)
* [Dowiedz się, jak skonfigurować logowanie jednokrotne między successfactors a usługą Azure Active Directory](successfactors-tutorial.md)
* [Dowiedz się, jak zintegrować inne aplikacje SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Dowiedz się, jak eksportować i importować konfiguracje inicjowania obsługi administracyjnej](../app-provisioning/export-import-provisioning-configuration.md)


---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą systemu Zendesk | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i systemu Zendesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: jeedes
ms.openlocfilehash: 37d20eabfc8fb883cda346dc8b55a17b8b959218
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268178"
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą systemu Zendesk

W tym samouczku dowiesz się, jak zintegrować systemu Zendesk w usłudze Azure Active Directory (Azure AD).

Integrowanie systemu Zendesk z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do systemu Zendesk.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do systemu Zendesk (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą systemu Zendesk, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Zendesk logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym.
Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie systemu Zendesk z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-zendesk-from-the-gallery"></a>Dodawanie systemu Zendesk z galerii

Aby skonfigurować integrację programu Zendesk w usłudze Azure AD, należy dodać systemu Zendesk z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać systemu Zendesk z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![image](./media/zendesk-tutorial/selectazuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![image](./media/zendesk-tutorial/a_select_app.png)
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![image](./media/zendesk-tutorial/a_new_app.png)

4. W polu wyszukiwania wpisz **systemu Zendesk**, wybierz opcję **Zendesk** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![image](./media/zendesk-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą systemu Zendesk w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w systemie Zendesk dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w usłudze Zendesk musi nawiązać.

W systemie Zendesk, przypisz wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą systemu Zendesk, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego systemu Zendesk](#create-a-zendesk-test-user)**  — aby odpowiednikiem Britta Simon w systemie Zendesk, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji systemu Zendesk.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne za pomocą systemu Zendesk, wykonaj następujące czynności:**

1. W [witryny Azure portal](https://portal.azure.com/)na **Zendesk** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![image](./media/zendesk-tutorial/b1_b2_select_sso.png)

2. Kliknij przycisk **pojedynczej zmiany trybu logowania jednokrotnego** na górze ekranu, aby wybrać **SAML** trybu.

      ![image](./media/zendesk-tutorial/b1_b2_saml_ssso.png)

3. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **wybierz** dla **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![image](./media/zendesk-tutorial/b1_b2_saml_sso.png)

4. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** przycisk, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![image](./media/zendesk-tutorial/b1-domains_and_urlsedit.png)

5. Na **podstawową konfigurację protokołu SAML** sekcji, wykonaj następujące czynności:

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<subdomain>.zendesk.com`.

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `<subdomain>.zendesk.com`.

    ![image](./media/zendesk-tutorial/b1-domains_and_urls.png)

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania jednokrotnego i identyfikator. Skontaktuj się z pomocą [zespół obsługi klienta systemu Zendesk](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) do uzyskania tych wartości.

6. Zendesk oczekuje twierdzenia SAML w określonym formacie. Nie obowiązkowego atrybutów SAML, ale Opcjonalnie można dodać atrybut z **atrybutów użytkownika** sekcji na stronie integracji aplikacji. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** przycisk, aby otworzyć **atrybutów użytkownika** okna dialogowego.

    ![image](./media/zendesk-tutorial/i4-attribute.png)

7. W **oświadczenia użytkownika** sekcji na **atrybutów użytkownika** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:

    a. Kliknij przycisk **Dodaj nowe oświadczenie** otworzyć **Zarządzanie oświadczenia użytkownika** okna dialogowego.

    ![image](./media/zendesk-tutorial/i2-attribute.png)

    ![image](./media/zendesk-tutorial/i3-attribute.png)
    
    b. W **nazwa** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.

    c. Pozostaw **Namespace** puste.

    d. Wybierz źródło jako **atrybutu**.
    
    e. Z **atrybut źródłowy** wpisz wartość atrybutu wyświetlanego dla tego wiersza.
    
    f. Kliknij przycisk **Ok**

    g. Kliknij pozycję **Zapisz**.

    > [!NOTE]
    > Atrybuty rozszerzenia umożliwiają dodawanie atrybutów, które nie są dostępne w usłudze Azure AD domyślnie. Kliknij przycisk [atrybutów użytkowników, które można ustawić w SAML](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) Aby uzyskać pełną listę SAML atrybuty, które **Zendesk** akceptuje.

8. W protokołu SAML sekcji certyfikatu podpisywania, w **certyfikat podpisywania SAML** sekcji, skopiuj **odcisk palca**i zapisz go na komputerze.

    ![image](./media/zendesk-tutorial/C3_certificate.png)

    a. Wybierz opcję odpowiednią dla **opcja podpisywania** w razie potrzeby.

    b. Wybierz opcję odpowiednią dla **algorytmu podpisywania** w razie potrzeby.

    c. Kliknij pozycję **Zapisz**

9. Na **Konfigurowanie systemu Zendesk** kliknij **wyświetlić instrukcje krok po kroku** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj poniższe adresy URL, z **krótki przewodnik po sekcji.**

    Należy zwrócić uwagę na to, czy adres url może wskazywać następujące czynności:

    a. Adres URL usługi rejestracji logowania jednokrotnego SAML

    b. Identyfikator jednostki

    c. Adres URL wylogowania

    ![image](./media/zendesk-tutorial/d1_saml.png) 

10. Istnieją dwa sposoby na których można skonfigurować Zendesk — automatyczne i ręczne.
  
11. Aby zautomatyzować konfigurację w ramach systemu Zendesk, musisz zainstalować **rozszerzenia przeglądarki do bezpiecznego Moje aplikacje logowania** , klikając **zainstalować rozszerzenie**.

    ![image](./media/zendesk-tutorial/install_extension.png)

12. Po dodaniu rozszerzenia do przeglądarki, kliknij pozycję **Instalatora systemu Zendesk** nastąpi bezpośrednie przekierowanie do aplikacji systemu Zendesk. W tym miejscu należy podać poświadczenia administratora do logowania się do systemu Zendesk. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzować kroku 13.

     ![image](./media/zendesk-tutorial/d2_saml.png) 

13. Jeśli chcesz ręcznie skonfigurować systemu Zendesk, Otwórz nowe okno przeglądarki sieci web i dziennika do witryny firmy systemu Zendesk z uprawnieniami administratora i wykonaj następujące czynności:

    * Kliknij przycisk **administratora**.

    * W okienku nawigacji po lewej stronie kliknij **ustawienia**, a następnie kliknij przycisk **zabezpieczeń**.

    * Na **zabezpieczeń** strony, wykonaj następujące czynności:

      ![Zabezpieczenia](././media/zendesk-tutorial/ic773089.png "zabezpieczeń")

      ![Logowanie jednokrotne](././media/zendesk-tutorial/ic773090.png "logowanie jednokrotne")

      a. Kliknij przycisk **Admin & agentów** kartę.

      b. Wybierz **logowanie jednokrotne (SSO) i SAML**, a następnie wybierz pozycję **SAML**.

      c. W **adres URL logowania jednokrotnego SAML** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowanej w witrynie Azure portal.

      d. W **zdalny adres URL wylogowania** pola tekstowego, Wklej wartość **adres URL wylogowania** skopiowanej w witrynie Azure portal.

      e. W **odcisk palca certyfikatu** pola tekstowego, Wklej **odcisk palca** wartości certyfikatów, które zostały skopiowane z witryny Azure portal.

      f. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.

    ![image](./media/zendesk-tutorial/d_users_and_groups.png)

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![image](./media/zendesk-tutorial/d_adduser.png)

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![image](./media/zendesk-tutorial/d_userproperties.png)

    a. W **nazwa** pola wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.

### <a name="create-a-zendesk-test-user"></a>Tworzenie użytkownika testowego systemu Zendesk

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w systemie Zendesk. Zendesk obsługuje automatyczna aprowizacja użytkowników, która jest domyślnie włączona. Więcej szczegółów można znaleźć [tutaj](Zendesk-provisioning-tutorial.md) dotyczące sposobu konfigurowania automatycznej aprowizacji użytkowników.

**Jeśli potrzebujesz ręcznie utworzyć użytkownika, wykonaj poniższe czynności:**

> [!NOTE]
> **Użytkownik końcowy** kont są automatycznie konfigurowani podczas logowania. **Agent** i **administratora** konta, które muszą zostać aprowizowane ręcznie w **Zendesk** przed zalogowaniem się.

1. Zaloguj się do Twojej **Zendesk** dzierżawy.

2. Wybierz **listy klientów** kartę.

3. Wybierz **użytkownika** kartę, a następnie kliknij przycisk **Dodaj**.

    ![Dodaj użytkownika](././media/zendesk-tutorial/ic773632.png "Dodaj użytkownika")
4. Typ **nazwa** i **E-mail** istniejącego konta usługi Azure AD do aprowizowania, a następnie kliknij przycisk **Zapisz**.

    ![Nowy użytkownik](././media/zendesk-tutorial/ic773633.png "nowego użytkownika")

> [!NOTE]
> Można użyć dowolnego innego systemu Zendesk użytkownika konta narzędzi do tworzenia lub interfejsów API dostarczonych przez systemu Zendesk do aprowizacji kont użytkowników usługi AAD.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do systemu Zendesk.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![image](./media/zendesk-tutorial/d_all_applications.png)

2. Na liście aplikacji wybierz **Zendesk**.

    ![image](./media/zendesk-tutorial/d_all_zendeskapplications.png)

3. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![image](./media/zendesk-tutorial/d_leftpaneusers.png)

4. Wybierz **Dodaj** przycisk, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![image](./media/zendesk-tutorial/d_assign_user.png)

4. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

5. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Zendesk w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji systemu Zendesk.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie Aprowizowania użytkowników](zendesk-provisioning-tutorial.md)

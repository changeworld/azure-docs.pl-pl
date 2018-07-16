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
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: e5ef74329e2adb6f3b8b60f547231a245a03b1fe
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39050599"
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

- Subskrypcję usługi Azure AD
- Zendesk logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie systemu Zendesk z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-zendesk-from-the-gallery"></a>Dodawanie systemu Zendesk z galerii
Aby skonfigurować integrację programu Zendesk w usłudze Azure AD, należy dodać systemu Zendesk z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać systemu Zendesk z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **systemu Zendesk**, wybierz opcję **Zendesk** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Systemu Zendesk, na liście wyników](./media/zendesk-tutorial/tutorial_zendesk_addfromgallery.png)

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

1. W witrynie Azure portal na **Zendesk** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/zendesk-tutorial/tutorial_zendesk_samlbase.png)

3. Na **systemu Zendesk, domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Domena systemu Zendesk i adresy URL pojedynczego logowania jednokrotnego informacji](./media/zendesk-tutorial/tutorial_zendesk_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<subdomain>.zendesk.com`

    b. W **identyfikator** polu tekstowym wpisz wartość, przy użyciu następującego wzorca: `<subdomain>.zendesk.com`

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania jednokrotnego i identyfikator. Skontaktuj się z pomocą [zespół obsługi klienta systemu Zendesk](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) do uzyskania tych wartości.

4. Na **certyfikat podpisywania SAML** sekcji, skopiuj **odcisk PALCA** wartość certyfikatu.

    ![Link pobierania certyfikatu](./media/zendesk-tutorial/tutorial_zendesk_certificate.png)

5. Zendesk oczekuje twierdzenia SAML w określonym formacie. Istnieją żadnych obowiązkowych atrybutów SAML, ale Opcjonalnie można dodać atrybut z **atrybutów użytkownika** sekcji, wykonując poniższe kroki: 

     ![Konfigurowanie logowania jednokrotnego](./media/zendesk-tutorial/tutorial_zendesk_attributes1.png)

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego Dodaj](./media/zendesk-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie logowania jednokrotnego addattb](./media/zendesk-tutorial/tutorial_attribute_05.png)

    b. W **nazwa** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.

    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.
    
    d. Kliknij przycisk **OK**.

    > [!NOTE]
    > Atrybuty rozszerzenia umożliwiają dodawanie atrybutów, które nie są dostępne w usłudze Azure AD domyślnie. Kliknij przycisk [atrybutów użytkowników, które można ustawić w SAML](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) Aby uzyskać pełną listę SAML atrybuty, które **Zendesk** akceptuje.

6. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/zendesk-tutorial/tutorial_general_400.png)

7. Na **konfiguracji systemu Zendesk** kliknij **konfigurowania systemu Zendesk** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja systemu Zendesk](./media/zendesk-tutorial/tutorial_zendesk_configure.png) 

8. W oknie przeglądarki internetowej innej Zaloguj się do witryny firmy systemu Zendesk, jako administrator.

9. Kliknij przycisk **administratora**.

10. W okienku nawigacji po lewej stronie kliknij **ustawienia**, a następnie kliknij przycisk **zabezpieczeń**.

11. Na **zabezpieczeń** strony, wykonaj następujące czynności: 

     ![Zabezpieczenia](./media/zendesk-tutorial/ic773089.png "zabezpieczeń")

    ![Logowanie jednokrotne](./media/zendesk-tutorial/ic773090.png "logowanie jednokrotne")

     a. Kliknij przycisk **Admin & agentów** kartę.

     b. Wybierz **logowanie jednokrotne (SSO) i SAML**, a następnie wybierz pozycję **SAML**.

     c. W **adres URL logowania jednokrotnego SAML** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowanej w witrynie Azure portal. 

     d. W **zdalny adres URL wylogowania** pola tekstowego, Wklej wartość **adres URL wylogowania** skopiowanej w witrynie Azure portal.

     e. W **odcisk palca certyfikatu** pola tekstowego, Wklej **odcisk palca** wartości certyfikatów, które zostały skopiowane z witryny Azure portal.

     f. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/zendesk-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/zendesk-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/zendesk-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/zendesk-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.

### <a name="create-a-zendesk-test-user"></a>Tworzenie użytkownika testowego systemu Zendesk

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w systemie Zendesk. Zendesk obsługuje automatyczna aprowizacja użytkowników, która jest domyślnie włączona. Więcej szczegółów można znaleźć [tutaj](zendesk-provisioning-tutorial.md) dotyczące sposobu konfigurowania automatycznej aprowizacji użytkowników.

**Jeśli potrzebujesz ręcznie utworzyć użytkownika, wykonaj poniższe czynności:**

> [!NOTE]
> **Użytkownik końcowy** kont są automatycznie konfigurowani podczas logowania. **Agent** i **administratora** konta, które muszą zostać aprowizowane ręcznie w **Zendesk** przed zalogowaniem się.

1. Zaloguj się do Twojej **Zendesk** dzierżawy.

2. Wybierz **listy klientów** kartę.

3. Wybierz **użytkownika** kartę, a następnie kliknij przycisk **Dodaj**.

    ![Dodaj użytkownika](./media/zendesk-tutorial/ic773632.png "Dodaj użytkownika")
4. Typ **nazwa** i **E-mail** istniejącego konta usługi Azure AD do aprowizowania, a następnie kliknij przycisk **Zapisz**.

    ![Nowy użytkownik](./media/zendesk-tutorial/ic773633.png "nowego użytkownika")

> [!NOTE]
> Można użyć dowolnego innego systemu Zendesk użytkownika konta narzędzi do tworzenia lub interfejsów API dostarczonych przez systemu Zendesk do aprowizacji kont użytkowników usługi AAD.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do systemu Zendesk.

![Przypisanie roli użytkownika][200]

**Aby przypisać Britta Simon systemu Zendesk, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **Zendesk**.

    ![Łącze systemu Zendesk, na liście aplikacji](./media/zendesk-tutorial/tutorial_zendesk_app.png)

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Zendesk w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji systemu Zendesk.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie Aprowizowania użytkowników](zendesk-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
[4]: ./media/zendesk-tutorial/tutorial_general_04.png

[100]: ./media/zendesk-tutorial/tutorial_general_100.png

[200]: ./media/zendesk-tutorial/tutorial_general_200.png
[201]: ./media/zendesk-tutorial/tutorial_general_201.png
[202]: ./media/zendesk-tutorial/tutorial_general_202.png
[203]: ./media/zendesk-tutorial/tutorial_general_203.png

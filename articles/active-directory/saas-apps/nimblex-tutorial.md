---
title: 'Samouczek: Integracja usługi Azure Active Directory z Nimblex | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Nimblex.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d3e165a5-f062-4b50-ac0b-b400838e99cd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2018
ms.author: jeedes
ms.openlocfilehash: 63b70e38f4c4f804c9be520480c7562834d42598
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37872324"
---
# <a name="tutorial-azure-active-directory-integration-with-nimblex"></a>Samouczek: Integracja usługi Azure Active Directory z Nimblex

W tym samouczku dowiesz się, jak zintegrować Nimblex w usłudze Azure Active Directory (Azure AD).

Integrowanie Nimblex z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Nimblex.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do Nimblex (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Nimblex, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Nimblex logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Nimblex z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-nimblex-from-the-gallery"></a>Dodawanie Nimblex z galerii
Aby skonfigurować integrację Nimblex w usłudze Azure AD, należy dodać Nimblex z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Nimblex z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Nimblex**, wybierz opcję **Nimblex** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Nimblex na liście wyników](./media/nimblex-tutorial/tutorial_nimblex_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Nimblex w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Nimblex do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Nimblex musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Nimblex, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Nimblex](#create-a-nimblex-test-user)**  — aby odpowiednikiem Britta Simon w Nimblex połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Nimblex.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Nimblex, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Nimblex** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/nimblex-tutorial/tutorial_nimblex_samlbase.png)

3. Na **Nimblex domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Nimblex domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/nimblex-tutorial/tutorial_nimblex_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<YOUR APPLICATION PATH>/Login.aspx`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<YOUR APPLICATION PATH>/`

    c. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<path-to-application>/SamlReply.aspx`

    > [!NOTE] 
    > Te wartości są prawdziwe. Rzeczywisty adres URL logowania, identyfikator i adres URL odpowiedzi, należy zaktualizować te wartości. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Nimblex](mailto:support@ebms.com.au) do uzyskania tych wartości.

4. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/nimblex-tutorial/tutorial_nimblex_certificate.png) 

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/nimblex-tutorial/tutorial_general_400.png)

6. Na **konfiguracji Nimblex** , kliknij przycisk **skonfigurować Nimblex** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja Nimblex](./media/nimblex-tutorial/tutorial_nimblex_configure.png) 

7. W oknie przeglądarki internetowej innej Zaloguj się do Nimblex jako Administrator zabezpieczeń.

9. W prawej górnej części strony, kliknij polecenie **ustawienia** logo.

    ![Ustawienia Nimblex](./media/nimblex-tutorial/tutorial_nimblex_settings.png)

10. Na **Panelu sterowania** w obszarze **zabezpieczeń** kliknij sekcję **logowania jednokrotnego**.

    ![Ustawienia Nimblex](./media/nimblex-tutorial/tutorial_nimblex_single.png)

11. Na **Zarządzanie logowania jednokrotnego** stronie, wybierz nazwę wystąpienia i kliknij przycisk **Edytuj**.

    ![Nimblex saml](./media/nimblex-tutorial/tutorial_nimblex_saml.png)

12. Na **Edytowanie dostawcy logowania jednokrotnego** strony, wykonaj następujące czynności:

    ![Nimblex saml](./media/nimblex-tutorial/tutorial_nimblex_sso.png)

    a. W **opis** polu tekstowym wpisz nazwę wystąpienia.

    b. W programie Notatnik, otwórz certyfikat zakodowany base-64, pobrany z witryny Azure portal, skopiuj jego zawartość i następnie wklej go do **certyfikatu** pole.

    c. W **adres Url docelowej logowania jednokrotnego dostawcy tożsamości** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL**, który skopiowano z witryny Azure portal.

    d. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/nimblex-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/nimblex-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/nimblex-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/nimblex-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-nimblex-test-user"></a>Tworzenie użytkownika testowego Nimblex

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w Nimblex. Nimblex obsługę just-in-time, który jest domyślnie włączona. Brak elementu akcji dla Ciebie w tej sekcji. Nowy użytkownik jest tworzony podczas próby dostępu Nimblex, jeśli go jeszcze nie istnieje.

>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej klienta Nimblex](mailto:support@ebms.com.au).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Nimblex.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Nimblex, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **Nimblex**.

    ![Link Nimblex na liście aplikacji](./media/nimblex-tutorial/tutorial_nimblex_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Nimblex w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Nimblex.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/nimblex-tutorial/tutorial_general_01.png
[2]: ./media/nimblex-tutorial/tutorial_general_02.png
[3]: ./media/nimblex-tutorial/tutorial_general_03.png
[4]: ./media/nimblex-tutorial/tutorial_general_04.png

[100]: ./media/nimblex-tutorial/tutorial_general_100.png

[200]: ./media/nimblex-tutorial/tutorial_general_200.png
[201]: ./media/nimblex-tutorial/tutorial_general_201.png
[202]: ./media/nimblex-tutorial/tutorial_general_202.png
[203]: ./media/nimblex-tutorial/tutorial_general_203.png


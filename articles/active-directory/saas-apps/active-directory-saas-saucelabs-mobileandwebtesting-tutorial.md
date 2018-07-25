---
title: 'Samouczek: Integracja usługi Azure Active Directory z sos Labs — Mobile i testowanie sieci Web | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i sos Labs — Mobile i testowanie sieci Web.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3142d947-70e5-4345-8a30-b92d8715fac9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: jeedes
ms.openlocfilehash: 2c7c0283178e35fb8065bca3c6e380445ab43ae5
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2018
ms.locfileid: "39239921"
---
# <a name="tutorial-azure-active-directory-integration-with-sauce-labs---mobile-and-web-testing"></a>Samouczek: Integracja usługi Azure Active Directory z sos Labs — Mobile i testowanie sieci Web

W tym samouczku dowiesz się, jak integrować laboratoria sos - Mobile i testowanie sieci Web w usłudze Azure Active Directory (Azure AD).

Integrowanie laboratoriów sos - Mobile i testowanie sieci Web z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do laboratoriów sos - Mobile i testowanie sieci Web.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do laboratoriów sos - Mobile i testowanie sieci Web (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do konfigurowania integracji z usługą Azure AD z sos Labs — Mobile i testowanie sieci Web, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Laboratoria sos - Mobile i testowanie logowania jednokrotnego w sieci Web włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie sos Labs — Mobile i testowanie sieci Web z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-sauce-labs---mobile-and-web-testing-from-the-gallery"></a>Dodawanie sos Labs — Mobile i testowanie sieci Web z galerii
Aby skonfigurować integrację sos Labs — Mobile i testowanie sieci Web w usłudze Azure AD, należy dodać sos Labs — Mobile i testowanie sieci Web z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać sos Labs — Mobile i testowanie sieci Web z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **sos Labs — Mobile i testowanie sieci Web**, wybierz opcję **sos Labs — Mobile i testowanie sieci Web** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Sos Labs — Mobile i testowanie sieci Web na liście wyników](./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą Labs sos — Mobile i testowanie sieci Web oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w laboratoriach sos - Mobile i testowanie sieci Web do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w laboratoriach sos - Mobile i testowanie sieci Web musi zostać nawiązane.

Do konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą Labs sos - Mobile i testowanie sieci Web, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie laboratoriów sos - Mobile i testowanie sieci Web test użytkownika](#create-a-sauce-labs---mobile-and-web-testing-test-user)**  — aby odpowiednikiem Britta Simon w sos Labs — Mobile i testowanie sieci Web jest połączony z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i skonfigurować logowanie jednokrotne w laboratoriach sos - mobilnych i testowania aplikacji sieci Web.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z sos Labs — Mobile i testowanie sieci Web, wykonaj następujące czynności:**

1. W witrynie Azure portal na **sos Labs — Mobile i testowanie sieci Web** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_samlbase.png)

3. Na **sos Labs — Mobile i domena testowania sieci Web i adresy URL** sekcji, użytkownik nie ma do wykonywania żadnych czynności, jak aplikacja już jest wstępnie zintegrowana z platformą Azure.

    ![Sos Labs — Mobile i domeny testowania sieci Web oraz adresy URL pojedynczego logowania jednokrotnego informacji](./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_url.png)

4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_certificate.png)

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/tutorial_general_400.png)

6. W oknie przeglądarki internetowej innej Zaloguj się do laboratoriów sos - mobilnych i sieci Web testowania witryny firmy jako administrator.

7. Kliknij pozycję **ikony użytkownika** i wybierz **zarządzanie zespołem** kartę.

    ![Konfigurowanie logowania jednokrotnego](./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/configure1.png)

8. Wprowadź swoje **nazwy domeny** w polu tekstowym.

    ![Konfigurowanie logowania jednokrotnego](./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/configure2.png)

9. Kliknij przycisk **Konfiguruj** kartę.

    ![Konfigurowanie logowania jednokrotnego](./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/configure3.png)

10. W **skonfigurować logowanie jednokrotne** sekcji, wykonaj następujące kroki.

    ![Konfigurowanie logowania jednokrotnego](./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/configure4.png)

    a. Kliknij przycisk **Przeglądaj** i przekaż plik metadanych pobrany z usługi Azure AD.

    b. Wybierz **Zezwalaj APROWIZACJI just in Time** pola wyboru.

    c. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
  
### <a name="create-a-sauce-labs---mobile-and-web-testing-test-user"></a>Tworzenie laboratoriów sos - Mobile i testowanie sieci Web test użytkownika

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w laboratoriach sos - Mobile i testowanie sieci Web. Sos Labs — Mobile i testowanie sieci Web obsługę just-in-time, który jest domyślnie włączona. Brak elementu akcji dla Ciebie w tej sekcji. Nowy użytkownik jest tworzony podczas próby dostępu sos Labs — Mobile i testowanie sieci Web, jeśli go jeszcze nie istnieje.
>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [sos Labs — Mobile i testowanie sieci Web zespołu pomocy technicznej](mailto:support@saucelabs.com).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do udzielania dostępu do laboratoriów sos - Mobile i testowanie sieci Web za pomocą platformy Azure logowania jednokrotnego.

![Przypisanie roli użytkownika][200]

**Aby przypisać Britta Simon do laboratoriów sos - Mobile i testowanie sieci Web, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **sos Labs — Mobile i testowanie sieci Web**.

    ![Laboratoria sos - Mobile i testowanie sieci Web Połącz się na liście aplikacji](./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu Labs sos — Mobile i testowanie sieci Web kafelka w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do Labs sos — Mobile i testowania aplikacji sieci Web.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/tutorial_general_203.png

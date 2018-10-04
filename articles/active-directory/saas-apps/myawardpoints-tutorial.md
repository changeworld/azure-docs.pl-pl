---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Mój zespół Sub/górną pierwszych punktów Award | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między Azure Active Directory a Mój zespół Sub/górną pierwszych punktów Award.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a7a08eed-7a6b-4a83-8f8e-0add6d2fb8cf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2018
ms.author: jeedes
ms.openlocfilehash: 479fcc0408021ff63dbcabe3734f60a4ad6d542f
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48247758"
---
# <a name="tutorial-azure-active-directory-integration-with-my-award-points-top-subtop-team"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Mój zespół Sub/górną pierwszych punktów Award

W tym samouczku dowiesz się, jak zintegrować Mój zespół Sub/górną pierwszych punktów Award za pomocą usługi Azure Active Directory (Azure AD).

Integrowanie Mój zespół Sub/górną pierwszych punktów Award z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Mój zespół Sub/górną pierwszych punktów Award.
- Użytkowników, aby automatycznie uzyskać zalogowanych do moje punkty górnej Sub/górną zespół Award (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Wymagania wstępne

Do konfigurowania integracji z usługą Azure AD za pomocą Mój zespół Sub/górną pierwszych punktów Award, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Mój zespół Sub/górną pierwszych punktów Award logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym.
Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Mój zespół Sub/górną pierwszych punktów Award z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-my-award-points-top-subtop-team-from-the-gallery"></a>Dodawanie Mój zespół Sub/górną pierwszych punktów Award z galerii

Aby skonfigurować integrację Mój zespół Sub/górną pierwszych punktów Award w usłudze Azure AD, należy dodać Mój zespół Sub/górną pierwszych punktów Award z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Mój zespół Sub/górną pierwszych punktów Award z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Mój zespół Sub/górną pierwszych punktów Award**, wybierz opcję **Mój zespół Sub/górną pierwszych punktów Award** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Mój zespół Sub/górną Award punktów góry na liście wyników](./media/myawardpoints-tutorial/tutorial_myawardpoints_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą My Award punktów górnej Sub/górną zespołu, na podstawie użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Mój zespół Sub/górną pierwszych punktów Award dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Mój zespół Sub/górną pierwszych punktów Award musi nawiązać.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Mój zespół Sub/górną pierwszych punktów Award, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Mój zespół Sub/górną pierwszych punktów Award](#create-a-my-award-points-top-subtop-team-test-user)**  — aby odpowiednikiem Britta Simon w Moje Award punktów górnej Sub/górną zespołu, który jest połączony z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Mój zespół Sub/górną pierwszych punktów Award.

**Aby skonfigurować usługi Azure AD logowanie jednokrotne z Mój zespół Sub/górną pierwszych punktów nagrody, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Mój zespół Sub/górną pierwszych punktów Award** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/myawardpoints-tutorial/tutorial_myawardpoints_samlbase.png)

3. Na **Moje Award punktów górnej Sub/górną zespołu domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Moje Award punktów górnej Sub/górną zespołu domena i adresy URL pojedynczego logowania jednokrotnego informacje](./media/myawardpoints-tutorial/tutorial_myawardpoints_url.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://microsoftrr.performnet.com/biwv1auth/Shibboleth.sso/Login?providerId=<SAMLENTITYID>`

    > [!NOTE]
    > Zostanie wyświetlony `<SAMLENTITYID>` wartości w kolejnych krokach w tym samouczku.

4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/myawardpoints-tutorial/tutorial_myawardpoints_certificate.png)

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/myawardpoints-tutorial/tutorial_general_400.png)

6. W **Mój Award punktów górnej Sub/górną zespołu Konfiguracja** zaznacz **Skonfiguruj moje punkty górnej Sub/górną zespół Award** aby otworzyć okno Konfigurowanie logowania jednokrotnego. Skopiuj identyfikator jednostki SAML z **krótki** sekcji, a następnie dołącz wartość Identyfikatora jednostki protokołu SAML ze znakiem w adresie URL zamiast z `<SAMLENTITYID>` w **Moje Award punktów górnej Sub/górną zespołu domena i adresy URL** sekcja w witrynie Azure portal.

7. Aby skonfigurować logowanie jednokrotne na **Mój zespół Sub/górną pierwszych punktów Award** stronie, musisz wysłać pobrany **XML metadanych** do [zespołem pomocy technicznej Mój zespół Sub/górną pierwszych punktów Award](mailto:myawardpoints@biworldwide.com). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/myawardpoints-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/myawardpoints-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/myawardpoints-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/myawardpoints-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.

### <a name="create-a-my-award-points-top-subtop-team-test-user"></a>Tworzenie użytkownika testowego Mój zespół Sub/górną pierwszych punktów Award

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Mój zespół Sub/górną pierwszych punktów Award. Praca z [zespołem pomocy technicznej Mój zespół Sub/górną pierwszych punktów Award](mailto:myawardpoints@biworldwide.com) Aby dodać użytkowników na platformie Mój zespół Sub/górną pierwszych punktów Award. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Mój zespół Sub/górną pierwszych punktów Award.

![Przypisanie roli użytkownika][200]

**Aby przypisać Britta Simon Mój zespół Sub/górną pierwszych punktów nagrody, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **Mój zespół Sub/górną pierwszych punktów Award**.

    ![Łącze Mój zespół Sub/górną pierwszych punktów Award na liście aplikacji](./media/myawardpoints-tutorial/tutorial_myawardpoints_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Mój zespół Sub/górną pierwszych punktów Award w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do aplikacji Mój zespół Sub/górną pierwszych punktów Award.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/myawardpoints-tutorial/tutorial_general_01.png
[2]: ./media/myawardpoints-tutorial/tutorial_general_02.png
[3]: ./media/myawardpoints-tutorial/tutorial_general_03.png
[4]: ./media/myawardpoints-tutorial/tutorial_general_04.png

[100]: ./media/myawardpoints-tutorial/tutorial_general_100.png

[200]: ./media/myawardpoints-tutorial/tutorial_general_200.png
[201]: ./media/myawardpoints-tutorial/tutorial_general_201.png
[202]: ./media/myawardpoints-tutorial/tutorial_general_202.png
[203]: ./media/myawardpoints-tutorial/tutorial_general_203.png
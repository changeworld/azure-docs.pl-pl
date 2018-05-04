---
title: 'Samouczek: Integracji Azure Active Directory z QPrism | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i QPrism.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 72ab75ba-132b-4f83-a34b-d28b81b6d7bc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: jeedes
ms.openlocfilehash: b924ea1df926518ba1d86909f8e6a78deabd5468
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-azure-active-directory-integration-with-qprism"></a>Samouczek: Integracji Azure Active Directory z QPrism

Z tego samouczka dowiesz się integrowanie QPrism z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD QPrism zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do QPrism.
- Można umożliwić użytkownikom automatycznie pobrać zalogowanego do QPrism (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji: portalu Azure.

Aby uzyskać więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z QPrism, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- QPrism logowanie jednokrotne włączone subskrypcji

Aby przetestować kroki opisane w tym samouczku, wykonaj te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie QPrism z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="add-qprism-from-the-gallery"></a>Dodaj QPrism z galerii
Aby skonfigurować integrację usługi Azure AD QPrism, należy dodać QPrism z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać QPrism z galerii:**

1. W [portalu Azure](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory**. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, w górnej części okna dialogowego, zaznacz **nowej aplikacji**.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **QPrism**i wybierz **QPrism** z panelu wyników. Następnie kliknij przycisk **Dodaj** można dodać aplikację.

    ![QPrism na liście wyników](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z QPrism na podstawie użytkownika testowego, nazywany "Britta Simona".

Do rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, komu użytkownika odpowiednika w QPrism jest użytkownikiem w usłudze Azure AD. Innymi słowy musi być połączone relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w QPrism.

Aby ustalić tę relację w QPrism, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username**.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z QPrism, wykonaj poniższe bloki konstrukcyjne:

1. [Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on) umożliwienie użytkownikom korzystać z tej funkcji.
2. [Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) do testowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. [Tworzenie użytkownika testowego QPrism](#create-a-qprism-test-user) w celu zapewnienia odpowiednikiem Simona Britta QPrism, który jest połączony z usługi Azure AD reprezentację użytkownika.
4. [Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) umożliwiające Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. [Test rejestracji jednokrotnej](#test-single-sign-on) Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji QPrism.

1. W portalu Azure na **QPrism** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_samlbase.png)

3. W **QPrism domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i domeny QPrism pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_url.png)

    a. W **adres URL logowania** wpisz adres URL, który korzysta z następującego wzorca: `https://<customer domain>.qmyzone.com/login`

    b. W **identyfikator** wpisz adres URL, który korzysta z następującego wzorca: `https://<customer domain>.qmyzone.com/metadata.php`
         
    > [!NOTE] 
    > Wartości te nie są prawdziwe. Zaktualizować te wartości z rzeczywistego identyfikatora i logowania jednokrotnego adresu URL. Skontaktuj się z [zespołem pomocy technicznej klienta QPrism](mailto:qsupport-ce@quatrro.com) uzyskać te wartości. 

4. Na **certyfikat podpisywania SAML** sekcji, kliknij przycisk Kopiuj, aby skopiować **adres Url metadanych Federacji aplikacji** i wklej go do Notatnika.

     ![Łącze pobierania certyfikatu](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_certificate.png)

5. Wybierz pozycję **Zapisz**.

    ![Konfigurowanie rejestracji jednokrotnej przycisk zapisywania](./media/active-directory-saas-qprism-tutorial/tutorial_general_400.png)
    
6. Aby skonfigurować logowanie jednokrotne w **QPrism** stronie, musisz wysłać **adres Url metadanych Federacji aplikacji** do [QPrism zespołem pomocy technicznej](mailto:qsupport-ce@quatrro.com). To ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML one wartość.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD:**

1. W portalu Azure, w lewym okienku wybierz **usługi Azure Active Directory**.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-qprism-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-qprism-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, w górnej części **wszyscy użytkownicy** okno dialogowe, wybierz opcję **Dodaj**.

    ![Przycisk Dodaj](./media/active-directory-saas-qprism-tutorial/create_aaduser_03.png)

4. W **użytkownika** okno dialogowe, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-qprism-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-a-qprism-test-user"></a>Tworzenie użytkownika testowego QPrism

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w QPrism. Praca z [zespołem pomocy technicznej QPrism](mailto:qsupport-ce@quatrro.com) do dodawania użytkowników na platformie QPrism. Użytkownicy muszą utworzyć i aktywowana, aby użyć rejestracji jednokrotnej. 

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu QPrism.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta do QPrism:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu. Przejdź do **aplikacje dla przedsiębiorstw**i wybierz **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **QPrism**.

    ![Łącze QPrism na liście aplikacji](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_app.png)  

3. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Wybierz pozycję **Dodaj**. Następnie w obszarze **Dodaj przydziału**, wybierz pozycję **użytkowników i grup**.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** w **użytkowników** listy.

6. Na **użytkowników i grup** okno dialogowe, wybierz opcję **wybierz**.

7. W obszarze **Dodaj przydziału**, wybierz pozycję **przypisać**.
    
### <a name="test-single-sign-on"></a>Test rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

W panelu dostępu po wybraniu kafelka QPrism użytkownik powinien pobrać automatycznie zalogować się przy QPrism aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS w usłudze Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_203.png


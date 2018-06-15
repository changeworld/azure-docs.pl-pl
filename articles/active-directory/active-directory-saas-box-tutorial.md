---
title: 'Samouczek: Integrowanie usługi Azure Active Directory z polem | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i pola.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3b565c8d-35e2-482a-b2f4-bf8fd7d8731f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/8/2017
ms.author: jeedes
ms.openlocfilehash: daad9104798dc02b479b4e022287c3630e4a67a0
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34336874"
---
# <a name="integrate-azure-active-directory-with-box"></a>Integrowanie usługi Azure Active Directory z polem

Z tego samouczka dowiesz Integrowanie usługi Azure Active Directory (Azure AD) z polem.

Integrowanie usługi Azure AD z pola, można uzyskać następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do pola.
- Można umożliwić użytkownikom uzyskać zalogowany automatycznie do pola (logowanie jednokrotne lub logowania jednokrotnego) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji, portalu Azure.

Aby dowiedzieć się więcej o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z polem, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Włączona usługa rejestracji Jednokrotnej w pole subskrypcji

> [!NOTE]
> Podczas testowania czynności w tym samouczku, zaleca się wykonanie *nie* za pomocą środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, wykonaj te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. 

Scenariusz, który jest opisane w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie pola z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="add-box-from-the-gallery"></a>Dodaj pole z galerii
Aby skonfigurować integrację usługi Azure AD z polem, Dodaj pole z galerii do listy zarządzane aplikacje SaaS w następujący sposób:

1. W [portalu Azure](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory**. 

    ![Przycisk usługi Azure Active Directory][1]

2. Wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    ![Okno "Aplikacje przedsiębiorstwa"][2]
    
3. Aby dodać nową aplikację, zaznacz **nowej aplikacji** u góry okna.

    !["Nowa aplikacja" przycisku][3]

4. W polu wyszukiwania wpisz **pole**, wybierz pozycję **pole** w lista wyników, a następnie wybierz **Dodaj**.

    ![Pole z listy wyników](./media/active-directory-saas-box-tutorial/tutorial_box_search.png)
### <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z polem, na podstawie użytkownika testowego, nazywany "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi zidentyfikować użytkowników pole i jego odpowiednik w usłudze Azure AD. Innymi słowy można ustanowić relacji łącza między użytkownika usługi Azure AD i tego samego użytkownika w polu.

Do ustanawiania relacji łącza, przypisz jako pole *Username* wartość *nazwy użytkownika* w usłudze Azure AD.

Aby konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej z polem, należy wykonać bloków konstrukcyjnych w następnych pięciu sekcjach.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

Włączanie usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji pola w następujący sposób:

1. W portalu Azure w **pole** okna integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Łącze "Logowanie jednokrotne"][4]

2. W **logowanie jednokrotne** okna w **tryb rejestracji jednokrotnej** wybierz opcję **na języku SAML logowania jednokrotnego**.
 
    ![Okno "Logowanie jednokrotne"](./media/active-directory-saas-box-tutorial/tutorial_box_samlbase.png)

3. W obszarze **pole domeny i adres URL**, wykonaj następujące czynności:

    !["Wprowadź adresy URL i domeny" single informacje logowania jednokrotnego](./media/active-directory-saas-box-tutorial/url3.png)

    a. W **adres URL logowania** wpisz adres URL w następującym formacie: *https://\<poddomena >. box.com*.

    b. W **identyfikator** pole tekstowe, typ **box.net**.
     
    > [!NOTE] 
    > Poprzednie wartości nie są prawdziwe. Zaktualizuj je z rzeczywisty adres URL logowania i identyfikator. Aby uzyskać wartości, skontaktuj się z [zespołem pomocy technicznej klienta pole](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire). 

4. W obszarze **certyfikat podpisywania SAML**, wybierz pozycję **XML metadanych**, a następnie zapisz plik metadanych na komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-box-tutorial/tutorial_box_certificate.png) 

5. Wybierz pozycję **Zapisz**.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-box-tutorial/tutorial_general_400.png)
    
6. Aby skonfigurować logowanie Jednokrotne dla aplikacji, postępuj zgodnie z procedurą w [Konfigurowanie logowania jednokrotnego na własnych](https://community.box.com/t5/How-to-Guides-for-Admins/Setting-Up-Single-Sign-On-SSO-for-your-Enterprise/ta-p/1263#ssoonyourown).

> [!NOTE] 
> Jeśli nie można włączyć ustawienia logowania jednokrotnego dla konta pola, konieczne może być skontaktuj się z [zespołem pomocy technicznej klienta pole](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire) i podaj pobrany plik XML.

> [!TIP]
> Jak podczas konfigurowania aplikacji, możesz przeczytać zwięzły wersji poprzednich instrukcji w [portalu Azure](https://portal.azure.com). Po dodaniu aplikacji w **usługi Active Directory** > **aplikacje dla przedsiębiorstw** zaznacz **rejestracji jednokrotnej** karcie, a następnie przejść osadzone w dokumentacji **konfiguracji** sekcji u dołu. Aby uzyskać więcej informacji o funkcji osadzonych dokumentacji, zobacz [usługi Azure AD osadzonych dokumentacji]( https://go.microsoft.com/fwlink/?linkid=845985).
>

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji możesz tworzyć użytkownika testowego Simona Britta w portalu Azure w następujący sposób:

![Tworzenie użytkownika testowego usługi Azure AD][100]

1. W portalu Azure, w lewym okienku wybierz **usługi Azure Active Directory**.

    ![Łącze usługi Azure Active Directory](./media/active-directory-saas-box-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę bieżących użytkowników, wybierz **użytkowników i grup** > **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-box-tutorial/create_aaduser_02.png)

3. W górnej części **wszyscy użytkownicy** wybierz **Dodaj**.

    ![Przycisk Dodaj](./media/active-directory-saas-box-tutorial/create_aaduser_03.png)

    **Użytkownika** zostanie otwarte okno.

4. W **użytkownika** okna, wykonaj następujące czynności:

    ![Okno użytkownika](./media/active-directory-saas-box-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-a-box-test-user"></a>Tworzenie użytkownika testowego pole

W tej sekcji utworzysz użytkownika testowego Simona Britta w polu. Pole obsługę w czasie, który jest domyślnie włączona. Jeśli użytkownik nie istnieje, nowy jest tworzony podczas próby dostępu do pola. Nie jest wymagane, aby utworzyć użytkownika żadna akcja.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji możesz umożliwić użytkownikowi Simona Britta do użycia usługi Azure logowania jednokrotnego za udzielanie dostępu do pola. Aby to zrobić, wykonaj następujące czynności:

![Przypisanie roli użytkownika][200]

1. W portalu Azure Otwórz **aplikacji** widok, przejdź do **katalogu** wyświetlić, a następnie wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    !["Aplikacje przedsiębiorstwa" i "Wszystkie aplikacje" łącza][201] 

2. W **aplikacji** listy, wybierz **pole**.

    ![Łącze pola](./media/active-directory-saas-box-tutorial/tutorial_box_app.png)  

3. W okienku po lewej stronie wybierz **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Wybierz **Dodaj** , a następnie w **Dodaj przydziału** okienku wybierz **użytkowników i grup**.

    ![W okienku Dodaj przydziału][203]

5. W **użytkowników i grup** okna w **użytkowników** listy, wybierz **Simona Britta**.

6. Wybierz **wybierz** przycisku.

7. W **Dodaj przydziału** wybierz **przypisać**.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po wybraniu **pole** kafelka w panelu dostępu po otwarciu strony logowania do logowania się na aplikacji pole.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczki dotyczące integracji aplikacji SaaS w usłudze Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
* [Skonfiguruj Inicjowanie obsługi użytkowników](active-directory-saas-box-userprovisioning-tutorial.md)



<!--Image references-->

[1]: ./media/active-directory-saas-box-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-box-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-box-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-box-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-box-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-box-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-box-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-box-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-box-tutorial/tutorial_general_203.png


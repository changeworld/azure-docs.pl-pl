---
title: 'Samouczek: Integracji Azure Active Directory z OpenAthens | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i OpenAthens.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2017
ms.author: jeedes
ms.openlocfilehash: 3b19f3c7ed84a63f48320a2c7af8d06a9cc5deb4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-openathens"></a>Samouczek: Integracji Azure Active Directory z OpenAthens

Z tego samouczka dowiesz się integrowanie OpenAthens z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD OpenAthens zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do OpenAthens.
- Umożliwia użytkownikom do automatycznego logowania do OpenAthens (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji--portalu Azure.

Aby uzyskać więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z OpenAthens, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- OpenAthens logowanie jednokrotne włączone subskrypcji

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [Pobierz bezpłatną wersję próbną miesięcznego](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie OpenAthens z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-openathens-from-the-gallery"></a>Dodawanie OpenAthens z galerii
Aby skonfigurować integrację usługi Azure AD OpenAthens, należy dodać OpenAthens z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać OpenAthens z galerii**

1. W [portalu Azure](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**, a następnie przejdź do **wszystkie aplikacje**.

    ![W okienku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, zaznacz **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **OpenAthens**, wybierz pozycję **OpenAthens** z panelu wyniki, a następnie wybierz **Dodaj** przycisku.

    ![OpenAthens na liście wyników](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z OpenAthens oparte na użytkownika testu o nazwie "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w OpenAthens jest dla użytkownika, w usłudze Azure AD. Innymi słowy należy ustanowić relację łącza między użytkownika usługi Azure AD i danemu użytkownikowi w OpenAthens.

W OpenAthens, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z OpenAthens, należy wykonać poniższe bloki konstrukcyjne:

1. [Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on), aby umożliwić użytkownikom korzystać z tej funkcji.
2. [Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user), aby przetestować usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. [Tworzenie użytkownika testowego OpenAthens](#create-a-openathens-test-user), w celu zapewnienia odpowiednikiem Simona Britta OpenAthens połączonego z usługi Azure AD reprezentację użytkownika.
4. [Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user), aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. [Test rejestracji jednokrotnej](#test-single-sign-on), aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji OpenAthens.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z OpenAthens**

1. W portalu Azure na **OpenAthens** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Konfigurowanie jednego łącza logowania jednokrotnego][4]

2. Aby włączyć logowanie jednokrotne, w **logowanie jednokrotne** okno dialogowe, wybierz opcję **na języku SAML logowania jednokrotnego** jako **tryb**.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_samlbase.png)

3. W **OpenAthens domeny i adres URL** wprowadź wartość `https://login.openathens.net/saml/2/metadata-sp` w **identyfikator** pola tekstowego.

    ![Adresy URL i domeny OpenAthens pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_url.png)

4. W **certyfikat podpisywania SAML** wybierz opcję **XML metadanych**, a następnie zapisz plik metadanych na komputerze.

    ![Certyfikat podpisywania AMSL pobrać łącza](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_certificate.png) 

5. Wybierz ikonę **Zapisz**.

    ![Logowanie jednokrotne przycisk zapisywania](./media/active-directory-saas-openathens-tutorial/tutorial_general_400.png)

6. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy OpenAthens.

7. Wybierz **połączeń** z listy w obszarze **zarządzania** kartę. 

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application1.png)

8. Wybierz **SAML 1.1/2.0**, a następnie wybierz **Konfiguruj** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application2.png)
    
9. Aby dodać konfigurację, zaznacz **Przeglądaj** przycisk, aby przekazać plik .xml metadanych, który został pobrany z portalu Azure, a następnie wybierz **Dodaj**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application3.png)

10. Wykonaj poniższe kroki w obszarze **szczegóły** kartę.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application4.png)

    a. W **mapowania nazwy wyświetlanej**, wybierz pozycję **Użyj atrybutu**.

    b. W **atrybutu nazwy wyświetlanej** tekst Wprowadź wartość `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    c. W **mapowanie użytkownika unikatowy**, wybierz pozycję **Użyj atrybutu**.

    d. W **użytkownika unikatowy atrybut** tekst Wprowadź wartość `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    e. W **stanu**, zaznacz wszystkie trzy pola wyboru.

    f. W **tworzenie kont lokalnych**, wybierz pozycję **automatycznie**.

    g. Wybierz **zapisać zmiany**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji. Po dodaniu tej aplikacji z **usługi Active Directory** > **aplikacje dla przedsiębiorstw** zaznacz **rejestracji jednokrotnej** karcie i dostępu do osadzonego Dokumentacja za pośrednictwem **konfiguracji** sekcji u dołu. Więcej informacji o funkcji osadzonych dokumentacji, zobacz [usługi Azure AD osadzonych dokumentacji](https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie "Britta Simona".

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD**

1. W portalu Azure, w lewym okienku wybierz **usługi Azure Active Directory**.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-openathens-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie wybierz **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-openathens-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, wybierz opcję **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-openathens-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-openathens-tutorial/create_aaduser_04.png)

    a. W **nazwa** polu tekstowym **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail dla Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pola tekstowego.

    d. Wybierz pozycję **Utwórz**.
  
### <a name="create-an-openathens-test-user"></a>Tworzenie użytkownika testowego OpenAthens

OpenAthens obsługę w czasie, a użytkownicy są tworzone automatycznie po pomyślnym uwierzytelnieniu. Nie trzeba wykonywać żadnych czynności w tej sekcji.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do udostępnienia jej OpenAthens za pomocą usługi Azure rejestracji jednokrotnej.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta OpenAthens**

1. W portalu Azure, otwórz wyświetlić aplikacje, przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw**, a następnie wybierz **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. W **aplikacji** listy, wybierz **OpenAthens**.

    ![Łącze OpenAthens na liście aplikacji](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_app.png)  

3. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Wybierz **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okienka.

    ![W okienku Dodaj przydziału][203]

5. W **użytkowników i grup** listy, wybierz **Simona Britta**.

6. Wybierz **wybierz** przycisk **użytkowników i grup** listy.

7. Wybierz **przypisać** przycisk **Dodaj przydziału** okienka.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po wybraniu **OpenAthens** kafelka w panelu dostępu użytkownik powinien automatycznie zalogować się przy OpenAthens aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* Lista samouczków dotyczących sposobów integracji aplikacji SaaS w usłudze Azure Active Directory, zobacz [samouczków integracji aplikacji SaaS do użycia z usługą Azure AD](active-directory-saas-tutorial-list.md).
* Aby uzyskać więcej informacji na temat dostępu do aplikacji i logowanie jednokrotne z usługą Azure Active Directory, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

<!--Image references-->

[1]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_203.png


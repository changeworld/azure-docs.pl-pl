---
title: 'Samouczek: Integracji Azure Active Directory z SciQuest spędzają na katalog | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i SciQuest spędzają na katalog.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 9fab641b-292e-4bef-91d1-8ccc4f3a0c1f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: jeedes
ms.openlocfilehash: 5b5e650dab3e45808ccbccab630509aa62a70c82
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35942635"
---
# <a name="tutorial-azure-active-directory-integration-with-sciquest-spend-director"></a>Samouczek: Integracji Azure Active Directory z SciQuest spędzają na katalog

Z tego samouczka dowiesz się integrowanie SciQuest spędzają na katalog w usłudze Azure Active Directory (Azure AD).

Integracja z usługą Azure AD SciQuest spędzają na katalog zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do SciQuest spędzają na katalog.
- Umożliwia użytkownikom automatycznie pobrać zalogowane SciQuest spędzają dyrektora (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z SciQuest spędzają na katalog, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Dyrektor spędzają SciQuest logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie SciQuest spędzają na katalog z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-sciquest-spend-director-from-the-gallery"></a>Dodawanie SciQuest spędzają na katalog z galerii
Aby skonfigurować integrację usługi Azure AD SciQuest spędzają na katalog, należy dodać SciQuest spędzają na katalog z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać SciQuest spędzają na katalog z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **SciQuest spędzają na katalog**, wybierz pozycję **SciQuest spędzają na katalog** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Dyrektor SciQuest spędzają na liście wyników](./media/sciquest-spend-director-tutorial/tutorial_sciquestspenddirector_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z SciQuest Dyrektor spędzają na podstawie użytkownika testowego, nazywany "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w SciQuest spędzają na katalog jest dla użytkownika, w usłudze Azure AD. Innymi słowy musi można ustanowić łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w SciQuest spędzają na katalog.

W SciQuest spędzają na katalog, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z SciQuest spędzają na katalog, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego SciQuest spędzają na katalog](#create-a-sciquest-spend-director-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta spędzają na katalog SciQuest, połączonej z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w SciQuest spędzają na katalog aplikacji.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z SciQuest spędzają na katalog, wykonaj następujące czynności:**

1. W portalu Azure na **SciQuest spędzają na katalog** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/sciquest-spend-director-tutorial/tutorial_sciquestspenddirector_samlbase.png)

3. Na **SciQuest spędzają na katalog domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i SciQuest spędzają na katalog domeny pojedynczy informacje logowania jednokrotnego](./media/sciquest-spend-director-tutorial/tutorial_sciquestspenddirector_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<companyname>.sciquest.com/apps/Router/SAMLAuth/<instancename>`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<companyname>.sciquest.com`

    c. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<companyname>.sciquest.com/apps/Router/ExternalAuth/Login/<instancename>`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania, identyfikator i adres URL odpowiedzi, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta Dyrektor spędzają SciQuest](https://www.jaggaer.com/contact-us/) uzyskać te wartości. 

4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/sciquest-spend-director-tutorial/tutorial_sciquestspenddirector_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/sciquest-spend-director-tutorial/tutorial_general_400.png)

6. Do konfigurowania rejestracji jednokrotnej na **SciQuest spędzają na katalog** stronie, musisz wysłać pobrany **XML metadanych** do [zespołem pomocy technicznej SciQuest spędzają na katalog](https://www.jaggaer.com/contact-us/).

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/sciquest-spend-director-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/sciquest-spend-director-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/sciquest-spend-director-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/sciquest-spend-director-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-sciquest-spend-director-test-user"></a>Tworzenie użytkownika testowego SciQuest spędzają na katalog

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w SciQuest spędzają na katalog.

Należy skontaktować się z [zespołem pomocy technicznej SciQuest spędzają na katalog](https://www.jaggaer.com/contact-us/) i dostarczać szczegółowe informacje o Twoim koncie testu, aby został utworzony.

Alternatywnie można też skorzystać w czasie inicjowania obsługi administracyjnej, pojedynczego logowania jednokrotnego funkcja, która jest obsługiwana przez SciQuest spędzają na katalog.  
Po włączeniu w czasie inicjowania obsługi użytkowników są tworzone automatycznie przez SciQuest spędzają na katalog podczas jednego próba logowania jednokrotnego, jeśli nie istnieją. Ta funkcja eliminuje potrzebę ręcznie utworzyć użytkowników odpowiednikiem rejestracji jednokrotnej.

Można uzyskać w czasie inicjowania obsługi administracyjnej włączone, należy skontaktować się z [zespołem pomocy technicznej SciQuest spędzają na katalog](https://www.jaggaer.com/contact-us/).

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu SciQuest spędzają na katalog.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta SciQuest spędzają na katalog, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **SciQuest spędzają na katalog**.

    ![Łącze SciQuest Dyrektor spędzają na liście aplikacji](./media/sciquest-spend-director-tutorial/tutorial_sciquestspenddirector_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka SciQuest spędzają na katalog, w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane SciQuest spędzają na katalog aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sciquest-spend-director-tutorial/tutorial_general_01.png
[2]: ./media/sciquest-spend-director-tutorial/tutorial_general_02.png
[3]: ./media/sciquest-spend-director-tutorial/tutorial_general_03.png
[4]: ./media/sciquest-spend-director-tutorial/tutorial_general_04.png

[100]: ./media/sciquest-spend-director-tutorial/tutorial_general_100.png

[200]: ./media/sciquest-spend-director-tutorial/tutorial_general_200.png
[201]: ./media/sciquest-spend-director-tutorial/tutorial_general_201.png
[202]: ./media/sciquest-spend-director-tutorial/tutorial_general_202.png
[203]: ./media/sciquest-spend-director-tutorial/tutorial_general_203.png


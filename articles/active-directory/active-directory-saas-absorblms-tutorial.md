---
title: 'Samouczek: Integracji Azure Active Directory z przyjęcia LMS | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i przyjęcia LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: jeedes
ms.openlocfilehash: f877d8fee4a94207fc01f4a5e0e7919f1286f2e4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34340669"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Samouczek: Integracji Azure Active Directory z przyjęcia LMS

Z tego samouczka dowiesz się integrowanie LMS przyjęcia w usłudze Azure Active Directory (Azure AD).

Integracja z usługą Azure AD przyjęcia LMS zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do przyjęcia LMS.
- Umożliwia użytkownikom automatycznie zaloguj się do przyjęcia LMS (za pośrednictwem rejestracji jednokrotnej) za pomocą ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji, portalu Azure.

Jeśli chcesz dowiedzieć się więcej na temat oprogramowania jako usługa (SaaS) integracji aplikacji z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z przyjęcia LMS, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Przyjęcia LMS logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Nie zalecamy korzystania do środowiska produkcyjnego w ramach tego samouczka.

Aby przetestować kroki opisane w tym samouczku, wykonaj te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

* Dodawanie przyjęcia LMS z galerii
* Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="add-absorb-lms-from-the-gallery"></a>Dodaj przyjęcia LMS z galerii
Aby skonfigurować integrację usługi Azure AD przyjęcia LMS, należy dodać przyjęcia LMS z galerii do listy zarządzanych aplikacji SaaS.

Aby dodać przyjęcia LMS z galerii, wykonaj następujące czynności:

1. W [portalu Azure](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    ![W okienku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać aplikację, wybierz **nowej aplikacji** przycisku.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **przyjęcia LMS**, wybierz pozycję **przyjęcia LMS** w wyniku panelu, a następnie wybierz **Dodaj** przycisku.

    ![Przyjęcia LMS na liście wyników](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z LMS przyjęcia w oparciu o nazwie Simona Britta użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, co to jest użytkownik odpowiednikiem LMS przyjęcia w usłudze Azure AD. Innymi słowy należy ustanowić relację łącza między użytkownikiem w usłudze Azure AD i odpowiedniego użytkownika w LMS przyjęcia.

Ustanawiania relacji to łącze, przypisując *nazwy użytkownika* wartość w usłudze Azure AD jako *Username* wartość LMS przyjęcia.

Aby konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej z przyjęcia LMS, należy wykonać bloków konstrukcyjnych w następnych pięciu sekcjach.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji LMS przyjęcia.

Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z przyjęcia LMS, wykonaj następujące czynności:

1. W portalu Azure na **przyjęcia LMS** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. W **logowanie jednokrotne** okna dialogowego, **tryb** wybierz opcję **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_samlbase.png)

3. W **adresy URL i przyjęcia domeny LMS** sekcji, wykonaj następujące czynności:

    ![Przyjęcia LMS domeny i adres URL pojedynczego logowania jednokrotnego informacji](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_url.png)

    a. W **identyfikator** wpisz adres URL, który używa następującej składni: `https://<subdomain>.myabsorb.com/Account/SAML`.

    b. W **adres URL odpowiedzi** wpisz adres URL, który używa następującej składni: `https://<subdomain>.myabsorb.com/Account/SAML`.
     
    > [!NOTE] 
    > Te adresy URL nie są wartości rzeczywistych. Zaktualizuj je z rzeczywistego identyfikatora i adresy URL odpowiedzi. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej klienta przyjęcia LMS](https://www.absorblms.com/support). 

4. W **certyfikat podpisywania SAML** sekcji w **Pobierz** kolumny wybierz **XML metadanych**, a następnie zapisz plik metadanych do komputera.

    ![Łącze pobierania certyfikatu podpisywania](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_certificate.png) 

5. Wybierz pozycję **Zapisz**.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-absorblms-tutorial/tutorial_general_400.png)
    
6. W **przyjęcia konfiguracji LMS** wybierz opcję **skonfigurować LMS przyjęcia** otworzyć **Konfigurowanie logowania jednokrotnego** okna, a następnie skopiuj **Sign-Out adresu URL** w **sekcji krótkimi opisami.**

    ![W okienku przyjęcia LMS konfiguracji](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_configure.png) 

7. W nowym oknie przeglądarki sieci web należy zalogować się jako administrator do przyjęcia LMS witryny firmy.

8. Wybierz **konta** u góry po prawej stronie. 

    ![Przycisk Konto](./media/active-directory-saas-absorblms-tutorial/1.png)

9. Wybierz w okienku konta **ustawienia portalu**.

    ![Łącze Ustawienia portalu](./media/active-directory-saas-absorblms-tutorial/2.png)
    
10. Wybierz kartę **Użytkownicy**.

    ![Na karcie Użytkownicy](./media/active-directory-saas-absorblms-tutorial/3.png)

11. Na stronie konfiguracji rejestracji jednokrotnej wykonaj następujące czynności:

    ![Na stronie konfiguracji rejestracji jednokrotnej](./media/active-directory-saas-absorblms-tutorial/4.png)

    a. W **tryb** wybierz opcję **zainicjował dostawcy tożsamości**.

    b. Otwórz Notatnik, certyfikatu, który został pobrany z portalu Azure. Usuń **---BEGIN CERTIFICATE---** i **---END CERTIFICATE---** tagów. Następnie w **klucza** Wklej pozostałą zawartość.
    
    c. W **właściwość identyfikatora** wybierz atrybut, który został skonfigurowany jako identyfikator użytkownika w usłudze Azure AD. Na przykład jeśli *userPrincipalName* jest wybrane w usłudze Azure AD, wybierz **Username**.

    d. W **adres URL logowania** Wklej **adres URL dostępu użytkownika** z poziomu aplikacji **właściwości** strony portalu Azure.

    e. W **adresu URL wylogowania**, Wklej **Sign-Out URL** wartości, które zostały skopiowane z **Konfigurowanie logowania jednokrotnego** okna portalu Azure.

12. Przełącz **tylko zezwalają na logowanie SSO** do **na**.

    ![Przełącz tylko Zezwalaj na logowanie SSO](./media/active-directory-saas-absorblms-tutorial/5.png)

13. Wybierz **zapisać.**

> [!TIP]
> Możesz przeczytać zwięzły wersji tych instrukcji w [portalu Azure](https://portal.azure.com) podczas konfigurowania aplikacji. Po dodaniu aplikacji z **usługi Active Directory** > **aplikacje dla przedsiębiorstw** zaznacz **rejestracji jednokrotnej** karcie i dostępu do osadzonego Dokumentacja za pośrednictwem **konfiguracji** sekcji u dołu. Aby uzyskać więcej informacji, zobacz [usługi Azure AD osadzonych dokumentacji]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego Simona Britta w portalu Azure.

![Tworzenie użytkownika testowego usługi Azure AD][100]

Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:

1. W portalu Azure, w lewym okienku wybierz **usługi Azure Active Directory**.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-absorblms-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, wybierz **użytkowników i grup** > **wszyscy użytkownicy**.
    
    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-absorblms-tutorial/create_aaduser_02.png) 

3. W górnej części okna dialogowego, wybierz **Dodaj**.
 
    ![Przycisk Dodaj](./media/active-directory-saas-absorblms-tutorial/create_aaduser_03.png) 

4. W **użytkownika** okno dialogowe, wykonaj następujące czynności:
 
    ![Okno dialogowe użytkownika](./media/active-directory-saas-absorblms-tutorial/create_aaduser_04.png) 

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** tekstowym, wpisz adres e-mail Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość w **hasło** pole.

    d. Wybierz pozycję **Utwórz**.

### <a name="create-an-absorb-lms-test-user"></a>Tworzenie użytkownika testowego przyjęcia LMS

Dla użytkowników usługi Azure AD zalogować się do przyjęcia LMS ich muszą zostać skonfigurowane w LMS przyjęcia.  

W przypadku przyjęcia LMS konfiguracja jest zadanie ręczne.

Aby skonfigurować konto użytkownika, wykonaj następujące czynności:

1. Zaloguj się do witryny firmy przyjęcia LMS jako administrator.

2. W okienku po lewej stronie wybierz **użytkowników**.

    ![Łącze przyjęcia użytkowników LMS](./media/active-directory-saas-absorblms-tutorial/absorblms_users.png)

3. W **użytkowników** okienku wybierz **użytkowników**.

    ![Łącze użytkowników](./media/active-directory-saas-absorblms-tutorial/absorblms_userssub.png)

4. W **Dodaj nowy** listy rozwijanej wybierz **użytkownika**.

    ![Dodaj nowy listy rozwijanej](./media/active-directory-saas-absorblms-tutorial/absorblms_createuser.png)

5. Na **Dodaj użytkownika** strony, wykonaj następujące czynności:

    ![Strona Dodawanie użytkownika](./media/active-directory-saas-absorblms-tutorial/user.png)

    a. W **imię** wpisz imię, takich jak **Britta**.

    b. W **nazwisko** wpisz nazwisko, takich jak **Simona**.
    
    c. W **Username** wpisz pełną nazwę, taką jak **Simona Britta**.

    d. W **hasło** wpisz hasło Simona Britta.

    e. W **Potwierdź hasło** pola, wpisz ponownie hasło.
    
    f. Ustaw **jest aktywny** Przełącz, aby **Active**.  

6. Wybierz **zapisać.**
 
### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji możesz umożliwić użytkownikowi Simona Britta do użycia usługi Azure logowania jednokrotnego za udzielanie dostępu do przyjęcia LMS.

![Przypisanie roli użytkownika][200]

Aby przypisać użytkownika Simona Britta do przyjęcia LMS, wykonaj następujące czynności:

1. W portalu Azure Otwórz widok aplikacji, przejdź do widoku katalogu, a następnie wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    ![Łącze "Wszystkie aplikacje"][201] 

2. W **aplikacji** listy, wybierz **przyjęcia LMS**.

    ![Łącze przyjęcia LMS na liście aplikacji](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_app.png) 

3. W okienku po lewej stronie wybierz **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202] 

4. Wybierz **Dodaj** , a następnie w **Dodaj przydziału** okienku wybierz **użytkowników i grup**.

    ![W okienku Dodaj przydziału][203]

5. W **użytkowników i grup** okna dialogowego, **użytkowników** listy, wybierz **Simona Britta**.

6. W **użytkowników i grup** okno dialogowe, wybierz opcję **wybierz** przycisku.

7. W **Dodaj przydziału** okno dialogowe, wybierz opcję **przypisać** przycisku.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

W panelu dostępu wybierając **przyjęcia LMS** kafelków automatycznie loguje się użytkownik do przyjęcia LMS aplikacji. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://msdn.microsoft.com/library/dn308586).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS w usłudze Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_203.png


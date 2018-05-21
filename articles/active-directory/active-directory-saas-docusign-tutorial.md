---
title: 'Samouczek: Integracji Azure Active Directory z DocuSign | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: jeedes
ms.openlocfilehash: 28c46e7071841552c5bcbf3df38d9d7771b0cfe6
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Samouczek: Integracji Azure Active Directory z DocuSign

Z tego samouczka dowiesz się integrowanie DocuSign z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD DocuSign zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do DocuSign
- Umożliwia użytkownikom automatycznie pobrać zalogowane do DocuSign (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z DocuSign, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- DocuSign logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie DocuSign z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-docusign-from-the-gallery"></a>Dodawanie DocuSign z galerii
Aby skonfigurować integrację usługi Azure AD DocuSign, należy dodać DocuSign z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać DocuSign z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **DocuSign**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_search.png)

5. W panelu wyników wybierz **DocuSign**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z DocuSign na podstawie użytkownika testowego, nazywany "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w DocuSign jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w DocuSign musi się.

Ta relacja łącza zostanie nawiązane, przypisując wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** w DocuSign.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z DocuSign, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego DocuSign](#creating-a-docusign-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta DocuSign połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji DocuSign.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z DocuSign, wykonaj następujące czynności:**

1. W portalu Azure na **DocuSign** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_samlbase.png)

3. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base 64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_certificate.png) 

4. Na **konfiguracji DocuSign** części portalu Azure, kliknij przycisk **skonfigurować DocuSign** można otworzyć Konfigurowanie logowania jednokrotnego okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_configure.png)

5. W oknie przeglądarki innej witryny sieci web, zaloguj się do Twojego **portalu administracyjnego DocuSign** jako administrator.

6. W menu nawigacji po lewej stronie kliknij **domen**.
   
    ![Konfigurowanie logowania jednokrotnego][51]

7. W okienku po prawej stronie, kliknij polecenie **oświadczenia domeny**.
   
    ![Konfigurowanie logowania jednokrotnego][52]

8. Na **oświadczenia domeny** okna dialogowego, w **nazwy domeny** pole tekstowe, wpisz domenę firmy, a następnie kliknij przycisk **oświadczeń**. Upewnij się, że weryfikowanie domeny i stan jest aktywny.
   
    ![Konfigurowanie logowania jednokrotnego][53]

9. W menu po lewej stronie kliknij **dostawców tożsamości**  
   
    ![Konfigurowanie logowania jednokrotnego][54]
10. W okienku po prawej stronie kliknij **Dodawanie dostawcy tożsamości**. 
   
    ![Konfigurowanie logowania jednokrotnego][55]

11. Na **ustawień dostawcy tożsamości** wykonaj następujące czynności:
   
    ![Konfigurowanie logowania jednokrotnego][56]

    a. W **nazwa** tekstowym, wpisz unikatową nazwę dla danej konfiguracji. Nie należy używać spacji.

    b. Wklej **identyfikator jednostki SAML** do **wystawcy dostawcy tożsamości** pola tekstowego.

    c. Wklej **SAML pojedynczy znak na adres URL usługi** do **adresu URL logowania do dostawcy tożsamości** pola tekstowego.

    d. Wklej **Sign-Out URL** do **adres URL wylogowania dostawcy tożsamości** pola tekstowego.

    e. Wybierz **podpisywania żądania uwierzytelniania**.

    f. Jako **AuthN wysyłanie żądania przez**, wybierz pozycję **POST**.

    g. Jako **Wyślij żądanie wylogowania**, wybierz pozycję **UZYSKAĆ**.

12. W **mapowanie atrybutu niestandardowego** sekcji, wybierz pole do mapowania oświadczenie, Azure AD. W tym przykładzie **emailaddress** oświadczenia jest zamapowana z wartością **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. Jest domyślną nazwę oświadczenia z usługą Azure AD, oświadczenie adresu e-mail. 
   
    > [!NOTE]
    > Użyj odpowiedniej **identyfikator użytkownika** do mapowania użytkownika z usługi Azure AD do mapowania użytkowników DocuSign. Zaznacz odpowiednie pola, a następnie wprowadź odpowiednią wartość, na podstawie własnych ustawień organizacji.
          
    ![Konfigurowanie logowania jednokrotnego][57]

13. W **certyfikat dostawcy tożsamości** , kliknij przycisk **Dodaj certyfikat**, a następnie przekaż certyfikat został pobrany z portalu usługi Azure AD.   
   
    ![Konfigurowanie logowania jednokrotnego][58]

14. Kliknij pozycję **Zapisz**.

15. W **dostawców tożsamości** kliknij **akcje**, a następnie kliknij przycisk **punkty końcowe**.   
   
    ![Konfigurowanie logowania jednokrotnego][59]
 
16. W **Wyświetl SAML 2.0 punkty końcowe** sekcji na **portalu administracyjnego DocuSign**, wykonaj następujące czynności:
   
    ![Konfigurowanie logowania jednokrotnego][60]
   
    a. Kopiuj **adres URL wystawcy dostawcy usługi**i wkleić do **identyfikator** textbox w **DocuSign domeny i adres URL** części portalu Azure, zgodnie ze wzorcem: `https://<subdomain>.docusign.com/organization/<uniqueID>/saml2/login/sp/<uniqueID>`.
   
    b. Kopiuj **adres URL logowania dostawcy usługi**i wkleić do **na adres URL logowania** textbox w **DocuSign domeny i adres URL** części portalu Azure, zgodnie ze wzorcem: `https://<subdomain>.docusign.com/organization/<uniqueID>/saml2/` .

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_url.png)
      
    c.  Kliknij przycisk **Zamknij**
    
17. W portalu Azure, kliknij polecenie **zapisać**.
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-docusign-tutorial/tutorial_general_400.png)

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-docusign-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-docusign-tutorial/create_aaduser_02.png) 

3. W górnej części okna dialogowego, kliknij przycisk **Dodaj** otworzyć **użytkownika** okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-docusign-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-docusign-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-docusign-test-user"></a>Tworzenie użytkownika testowego DocuSign

Aplikacja obsługuje **tylko w czasie Inicjowanie obsługi użytkowników** i po uwierzytelniania użytkowników są tworzone automatycznie w aplikacji.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do udostępnienia jej DocuSign za pomocą usługi Azure rejestracji jednokrotnej.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta DocuSign, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **DocuSign**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka DocuSign w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji DocuSign.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
* [Skonfiguruj Inicjowanie obsługi użytkowników](active-directory-saas-docusign-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_04.png
[51]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_29.png
[100]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_203.png


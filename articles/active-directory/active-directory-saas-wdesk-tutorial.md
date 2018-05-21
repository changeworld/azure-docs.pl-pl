---
title: 'Samouczek: Integracji Azure Active Directory z Wdesk | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Wdesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 06900a91-a326-4663-8ba6-69ae741a536e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.openlocfilehash: c55b9c32ccf6712da69c869a6f44c71e1c209627
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-wdesk"></a>Samouczek: Integracji Azure Active Directory z Wdesk

Z tego samouczka dowiesz się integrowanie Wdesk z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Wdesk zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Wdesk
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Wdesk (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz. [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Wdesk, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Wdesk jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Wdesk z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-wdesk-from-the-gallery"></a>Dodawanie Wdesk z galerii
Aby skonfigurować integrację usługi Azure AD Wdesk, należy dodać Wdesk z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Wdesk z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Wdesk**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_search.png)

5. W panelu wyników wybierz **Wdesk**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Wdesk na podstawie użytkownika testowego, nazywany "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Wdesk jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Wdesk musi się.

Ta relacja łącza zostanie nawiązane, przypisując wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** w Wdesk.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Wdesk, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Wdesk](#creating-a-wdesk-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Wdesk połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Wdesk.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Wdesk, wykonaj następujące czynności:**

1. W portalu Azure na **Wdesk** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_samlbase.png)

3. Na **Wdesk domeny i adres URL** sekcji, jeśli chcesz skonfigurować aplikację w **IDP** tryb inicjowane, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_url.png)

    a. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL**. Jeśli chcesz skonfigurować aplikację w **SP** inicjowane tryb, wykonaj następujące kroki:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_url1.png)

    W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`
     
    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Te wartości można uzyskać od WDesk portalu, po skonfigurowaniu logowania jednokrotnego. 
  
5. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_certificate.png) 

6. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-wdesk-tutorial/tutorial_general_400.png)
    
7. W oknie przeglądarki innej witryny sieci web, zaloguj się jako Administrator zabezpieczeń Wdesk.

8. W lewym dolnym rogu, kliknij przycisk **Admin** i wybierz polecenie **administrator konta**:
 
     ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

9. W Wdesk administratora, przejdź do **zabezpieczeń**, następnie **SAML** > **ustawienia SAML**:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

10. W obszarze **ustawienia ogólne**, sprawdź **Włącz SAML rejestrację jednokrotną**:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

11. W obszarze **szczegóły dostawcy usług**, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. Kopiuj **adres URL logowania** i wklej go w **adres Url logowania** textbox w portalu Azure.
   
      b. Kopiuj **adres Url metadanych** i wklej go w **identyfikator** textbox w portalu Azure.
       
      c. Kopiuj **adres url klienta** i wklej go w **adres Url odpowiedzi** textbox w portalu Azure.
   
      d. Kliknij przycisk **zapisać** w portalu Azure, aby zapisać zmiany.      

12. Kliknij przycisk **Konfigurowanie ustawień IdP** otworzyć **edytowanie ustawień dostawców tożsamości** okna dialogowego. Kliknij przycisk **wybierz plik** zlokalizować **Metadata.xml** następnie przekazać plik zapisany w portalu Azure.
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
13. Kliknij przycisk **zapisać zmiany**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-wdesk-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-wdesk-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-wdesk-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-wdesk-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-wdesk-test-user"></a>Tworzenie użytkownika testowego Wdesk

Aby umożliwić użytkownikom usługi Azure AD zalogować się do Wdesk, musi być przygotowana do Wdesk. W Wdesk Inicjowanie obsługi to zadanie ręczne.

**Aby udostępnić konta użytkownika, wykonaj następujące czynności:**

1. Zaloguj się jako Administrator zabezpieczeń Wdesk.
2. Przejdź do **Admin** > **konto administratora**.

     ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Kliknij przycisk **członków** w obszarze **osób**.

4. Teraz kliknij **dodać element członkowski** otworzyć **dodać element członkowski** okno dialogowe. 
   
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-wdesk-tutorial/createuser1.png)  

5. W **użytkownika** tekst Wprowadź nazwa użytkownika, takich jak **brittasimon@contoso.com** i kliknij przycisk **Kontynuuj** przycisku.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-wdesk-tutorial/createuser3.png)

6.  Wprowadź szczegóły, jak pokazano poniżej:
  
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-wdesk-tutorial/createuser4.png)
 
    a. W **E-mail** tekstowym wprowadź adres e-mail użytkownika, takich jak **brittasimon@contoso.com**.

    b. W **imię** tekst Wprowadź imię użytkownika, takich jak **Britta**.

    c. W **nazwisko** tekst Wprowadź nazwisko użytkownika, takich jak **Simona**.

7. Kliknij przycisk **zapisać element członkowski** przycisku.  

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-wdesk-tutorial/createuser5.png)

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Wdesk.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Wdesk, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Wdesk**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Wdesk w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Wdesk.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_203.png


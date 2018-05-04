---
title: 'Samouczek: Integracji Azure Active Directory z Netsuite | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Netsuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2018
ms.author: jeedes
ms.openlocfilehash: ecbadd45f964e4d4d36dcf67cf85cd878f012150
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>Samouczek: Integracji Azure Active Directory z Netsuite

Z tego samouczka dowiesz się integrowanie Netsuite z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Netsuite zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Netsuite
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Netsuite (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Netsuite, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Netsuite logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Netsuite z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-netsuite-from-the-gallery"></a>Dodawanie Netsuite z galerii
Aby skonfigurować integrację usługi Azure AD Netsuite, należy dodać Netsuite z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Netsuite z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Netsuite**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_search.png)

5. W panelu wyników wybierz **Netsuite**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Netsuite na podstawie użytkownika testowego, nazywany "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Netsuite jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Netsuite musi się.

Ta relacja łącza zostanie nawiązane, przypisując wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** w Netsuite.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Netsuite, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Netsuite](#creating-a-netsuite-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Netsuite połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Netsuite.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Netsuite, wykonaj następujące czynności:**

1. W portalu Azure na **Netsuite** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_samlbase.png)

3. Na **Netsuite domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_url.png)

    W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca:   `https://<tenant-name>.netsuite.com/saml2/acs` `https://<tenant-name>.na1.netsuite.com/saml2/acs` `https://<tenant-name>.na2.netsuite.com/saml2/acs` `https://<tenant-name>.sandbox.netsuite.com/saml2/acs` `https://<tenant-name>.na1.sandbox.netsuite.com/saml2/acs` `https://<tenant-name>.na2.sandbox.netsuite.com/saml2/acs`

    > [!NOTE] 
    > Nie są to rzeczywiste wartości. Rzeczywisty adres URL odpowiedzi, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej Netsuite](http://www.netsuite.com/portal/services/support.shtml) uzyskać te wartości.
 
4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik XML na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-netsuite-tutorial/tutorial_general_400.png)

6. Na **konfiguracji Netsuite** , kliknij przycisk **skonfigurować Netsuite** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_configure.png) 

7. Otwórz nową kartę w przeglądarce i zaloguj się do witryny firmy Netsuite jako administrator.

8. Na pasku narzędzi w górnej części strony kliknij **Instalator**, następnie kliknij przycisk **Menedżer instalacji**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-Netsuite-tutorial/ns-setup.png)

9. Z **zadań konfiguracyjnych** listy, wybierz **integracji**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-Netsuite-tutorial/ns-integration.png)

10. W **Zarządzanie uwierzytelniania** kliknij **SAML logowania jednokrotnego**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-Netsuite-tutorial/ns-saml.png)

11. Na **Instalatora SAML** wykonaj następujące czynności:
   
    a. Kopia **SAML pojedynczy znak na adres URL usługi** wartość z **krótkimi opisami** sekcji **Konfigurowanie logowania jednokrotnego** i wklej ją do **strony logowania dostawcy tożsamości** w Netsuite.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-netsuite-tutorial/ns-saml-setup.png)
  
    b. Netsuite, wybierz **podstawowej metody uwierzytelniania**.

    c. W polu z etykietą **metadanych dostawcy tożsamości SAMLV2**, wybierz pozycję **Przekaż plik metadanych IDP**. Następnie kliknij przycisk **Przeglądaj** można przekazać pliku metadanych, który został pobrany z portalu Azure.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-netsuite-tutorial/ns-sso-setup.png)

    d. Kliknij przycisk **przesłać**.

12. W usłudze Azure AD, kliknij **widoku i edytować wszystkie atrybuty użytkowników** pole wyboru i dodać atrybut.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-Netsuite-tutorial/ns-attributes.png)

13. Aby uzyskać **nazwa atrybutu** wpisz w `account`. Aby uzyskać **wartość atrybutu** wpisz w identyfikatora konta Netsuite Ta wartość jest stała i zmianę konta. Poniżej znajdują się instrukcje dotyczące sposobu Znajdź identyfikator konta:

      ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-Netsuite-tutorial/ns-add-attribute.png)

    a. W Netsuite, kliknij przycisk **Instalator** w menu górnym menu nawigacyjnym.

    b. Kliknięcie w obszarze **zadań konfiguracyjnych** części menu nawigacji po lewej stronie wybierz **integracji** sekcji, a następnie kliknij polecenie **preferencje usług sieci Web**.

    c. Identyfikator konta Netsuite skopiuj i wklej ją do **wartość atrybutu** pole w usłudze Azure AD.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-Netsuite-tutorial/ns-account-id.png)

14. Zanim użytkownicy mogą wykonywać logowania jednokrotnego do Netsuite, ich należy przypisać odpowiednie uprawnienia w Netsuite. Postępuj zgodnie z instrukcjami poniżej, aby przypisać te uprawnienia.

    a. W menu górnym menu nawigacyjnym kliknij **Instalator**, następnie kliknij przycisk **Menedżer instalacji**.
      
      ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-Netsuite-tutorial/ns-setup.png)

    b. W menu nawigacji po lewej stronie wybierz **użytkownicy i role**, następnie kliknij przycisk **Zarządzanie rolami**.
      
      ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-Netsuite-tutorial/ns-manage-roles.png)

    c. Kliknij przycisk **nową rolę**.

    d. Wpisz w **nazwa** dla roli nowego użytkownika.
      
      ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-Netsuite-tutorial/ns-new-role.png)

    e. Kliknij pozycję **Zapisz**.

    f. W menu u góry kliknij **uprawnienia**. Następnie kliknij przycisk **Instalatora**.
      
       ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-Netsuite-tutorial/ns-sso.png)

    g. Wybierz **ustawić zapasowej SAML logowania jednokrotnego**, a następnie kliknij przycisk **Dodaj**.

    h. Kliknij pozycję **Zapisz**.

    i. W menu górnym menu nawigacyjnym kliknij **Instalator**, następnie kliknij przycisk **Menedżer instalacji**.
      
       ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-Netsuite-tutorial/ns-setup.png)

    j. W menu nawigacji po lewej stronie wybierz **użytkownicy i role**, następnie kliknij przycisk **Zarządzanie użytkownikami**.
      
       ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-Netsuite-tutorial/ns-manage-users.png)

    k. Wybierz użytkownika testowego. Następnie kliknij przycisk **Edytuj**.
      
       ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-Netsuite-tutorial/ns-edit-user.png)

    l. W oknie dialogowym ról, wybierz rolę, którą utworzono i kliknij przycisk **Dodaj**.
      
       ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-Netsuite-tutorial/ns-add-role.png)

    m. Kliknij pozycję **Zapisz**.
    
### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-netsuite-tutorial/create_aaduser_01.png) 

2.  Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-netsuite-tutorial/create_aaduser_02.png) 

3. W górnej części okna dialogowego, kliknij przycisk **Dodaj** otworzyć **użytkownika** okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-netsuite-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-netsuite-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**. 

### <a name="creating-a-netsuite-test-user"></a>Tworzenie użytkownika testowego Netsuite

W tej sekcji użytkownika o nazwie Simona Britta jest tworzony w Netsuite. Netsuite obsługę w czasie, który jest domyślnie włączona.
Nie ma elementu akcji można w tej sekcji. Jeśli użytkownik nie istnieje w Netsuite, nowy jest tworzony podczas próby uzyskania dostępu Netsuite.


### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Netsuite.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Netsuite, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Netsuite**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Aby przetestować jednego ustawienia logowania jednokrotnego, otwórz Panel dostępu w [ https://myapps.microsoft.com ](https://myapps.microsoft.com/), zaloguj się do konta testowego i kliknij przycisk **Netsuite**.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Skonfiguruj Inicjowanie obsługi użytkowników](active-directory-saas-netsuite-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_203.png


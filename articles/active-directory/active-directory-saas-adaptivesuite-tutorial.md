---
title: 'Samouczek: Azure Active Directory integracji z pakietem adaptacyjną | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i adaptacyjną Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.openlocfilehash: 80067a82744498b273a99caa69e6c12f47100733
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-suite"></a>Samouczek: Integracji Azure Active Directory z adaptacyjną Suite

W tym samouczku Dowiedz się integrowanie adaptacyjną pakietu z usługi Azure Active Directory (Azure AD).

Integrowanie adaptacyjną pakiet z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, kto ma dostęp do zestawu adaptacyjną
- Umożliwia użytkownikom automatycznie pobrać zalogowane adaptacyjną pakietu (logowanie jednokrotne) z konta usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z adaptacyjną Suite, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Pakiet adaptacyjną jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie pakietu adaptacyjną z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-adaptive-suite-from-the-gallery"></a>Dodawanie pakietu adaptacyjną z galerii
Aby skonfigurować integrację usługi Azure AD adaptacyjną pakietu, należy dodać pakiet adaptacyjną z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać pakiet adaptacyjną z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Suite adaptacyjną**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_search.png)

5. W panelu wyników wybierz **Suite adaptacyjną**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z adaptacyjną pakietu na podstawie użytkownika testowego, nazywany "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednikiem pakietu adaptacyjną jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i danemu użytkownikowi pakietu adaptacyjną musi określone.

Adaptacyjną pakietu, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Do konfigurowania i testowania usługi Azure AD rejestracji jednokrotnej z adaptacyjną pakietu, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego adaptacyjną Suite](#creating-an-adaptive-suite-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta adaptacyjną pakiet, który jest połączony z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w adaptacyjną pakiet aplikacji.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z adaptacyjną pakiet, wykonaj następujące czynności:**

1. W portalu Azure na **Suite adaptacyjną** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_samlbase.png)

3. Na **adaptacyjną domeny Suite i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_url.png)

    W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    >[!NOTE]
    > Tę wartość można uzyskać z adaptacyjną zestawu **ustawienia logowania jednokrotnego SAML** strony.
    >
    
    Sprawdź **Pokaż zaawansowane ustawienia adresu URL** pole. W **identyfikator jednostki** tekstowym, wpisz ten sam adres URL wprowadzony w **adres URL odpowiedzi** pola tekstowego.
    
    >[!NOTE]
    > Identyfikator jednostki dla każdej domeny adaptacyjną Insights jest taki sam jak adres URL odpowiedzi.
    >

4. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_400.png)

6. Na **adaptacyjną konfiguracji pakietu** , kliknij przycisk **skonfigurować pakiet adaptacyjną** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_configure.png) 

7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy adaptacyjną Suite.

8. Przejdź do **Admin**.
   
    ![Administrator](./media/active-directory-saas-adaptivesuite-tutorial/IC805644.png "administratora")

9. W **użytkownikami i rolami** kliknij **Zarządzaj ustawieniami logowania jednokrotnego SAML**.
   
    ![Zarządzanie ustawieniami logowania jednokrotnego SAML](./media/active-directory-saas-adaptivesuite-tutorial/IC805645.png "Zarządzanie ustawieniami logowania jednokrotnego SAML")

10. Na **ustawienia logowania jednokrotnego SAML** wykonaj następujące czynności:
   
    ![Ustawienia logowania jednokrotnego SAML](./media/active-directory-saas-adaptivesuite-tutorial/IC805646.png "ustawienia logowania jednokrotnego SAML")

    a. W **Nazwa dostawcy tożsamości** tekstowym, wpisz nazwę dla danej konfiguracji.
    
    b. Wklej **identyfikator jednostki SAML** wartość skopiowany z portalu Azure do **dostawcy tożsamości identyfikator jednostki** pola tekstowego.
  
    c. Wklej **SAML pojedynczy znak na adres URL usługi** wartość skopiowany z portalu Azure do **dostawcy tożsamości adresu URL logowania jednokrotnego** pola tekstowego.
  
    d. Wklej **SAML pojedynczy znak na adres URL usługi** wartość skopiowany z portalu Azure do **niestandardowy adres URL wylogowania** pola tekstowego.
  
    e. Aby przekazać certyfikat pobrany, kliknij przycisk **wybierz plik**.
  
    f. Wykonaj następujące czynności, aby uzyskać:
    * **Identyfikator użytkownika SAML**, wybierz pozycję **nazwy użytkownika adaptacyjną Insights**.
    * **Lokalizacja identyfikator użytkownika SAML**, wybierz pozycję **identyfikatora użytkownika w NameID podmiotu**.
    * **Format SAML NameID**, wybierz pozycję **adres E-mail**.
    * **Włącz SAML**, wybierz pozycję **Zezwalaj logowania jednokrotnego SAML i bezpośrednie logowanie adaptacyjną Insights**.
    
    g. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-an-adaptive-suite-test-user"></a>Tworzenie użytkownika testowego adaptacyjną Suite

Aby umożliwić użytkownikom usługi Azure AD zalogować się do adaptacyjnego Suite, muszą mieć przydzielone do adaptacyjnego Suite.  

* W przypadku adaptacyjną Suite Inicjowanie obsługi to zadanie ręczne.

**Aby skonfigurować, inicjowanie obsługi użytkowników, wykonaj następujące czynności:** 

1. Zaloguj się do Twojego **Suite adaptacyjną** witryny firmy jako administrator.
2. Przejdź do **Admin**.
   
   ![Administrator](./media/active-directory-saas-adaptivesuite-tutorial/IC805644.png "administratora")
3. W **użytkownikami i rolami** kliknij **Dodaj użytkownika**.
   
   ![Dodaj użytkownika](./media/active-directory-saas-adaptivesuite-tutorial/IC805648.png "Dodaj użytkownika")
4. W **nowego użytkownika** sekcji, wykonaj następujące czynności:
   
   ![Przedstawia](./media/active-directory-saas-adaptivesuite-tutorial/IC805649.png "przesyłania")   

   a. Typ **nazwa**, **logowania**, **E-mail**, **hasło** z prawidłowym użytkownikiem usługi Azure Active Directory chcesz udostępnić w odnośnych pola tekstowe.
  
   b. Wybierz **roli**.
  
   c. Kliknij przycisk **przesłać**.

>[!NOTE]
>Możesz użyć innych adaptacyjną Suite użytkownika konta tworzenia narzędzi lub interfejsów API dostarczonych przez pakiet adaptacyjną do kont użytkowników usługi AAD.
>  

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do zestawu adaptacyjną.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta adaptacyjną pakietu, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Suite adaptacyjną**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

Celem tej sekcji służy do testowania usługi Microsoft Azure AD rejestracji jednokrotnej konfigurację za pomocą panelu dostępu.

Po kliknięciu kafelka adaptacyjną Suite w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane adaptacyjną pakiet aplikacji.


## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_203.png


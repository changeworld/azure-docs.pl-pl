---
title: 'Samouczek: Integracji Azure Active Directory z SuccessFactors | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i SuccessFactors.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 63ee7f7a08d16048eb3f0672cac4c2f41a3ed0f4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>Samouczek: Integracji Azure Active Directory z SuccessFactors

Z tego samouczka dowiesz się integrowanie SuccessFactors z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD SuccessFactors zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do SuccessFactors.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do SuccessFactors (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z SuccessFactors, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- SuccessFactors jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie SuccessFactors z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-successfactors-from-the-gallery"></a>Dodawanie SuccessFactors z galerii
Aby skonfigurować integrację usługi Azure AD SuccessFactors, należy dodać SuccessFactors z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać SuccessFactors z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **SuccessFactors**, wybierz pozycję **SuccessFactors** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![SuccessFactors na liście wyników](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z SuccessFactors w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w SuccessFactors jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w SuccessFactors musi się.

W SuccessFactors, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z SuccessFactors, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego SuccessFactors](#create-a-successfactors-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta SuccessFactors połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji SuccessFactors.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z SuccessFactors, wykonaj następujące czynności:**

1. W portalu Azure na **SuccessFactors** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_samlbase.png)

3. Na **SuccessFactors domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i domeny SuccessFactors pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca:
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca:
    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    c. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca:
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|
         
    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta SuccessFactors](https://www.successfactors.com/en_us/support.html) uzyskać te wartości. 

4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-successfactors-tutorial/tutorial_general_400.png)
    
6. Na **konfiguracji SuccessFactors** , kliknij przycisk **skonfigurować SuccessFactors** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_configure.png) 

7. W oknie przeglądarki innej witryny sieci web, zaloguj się do Twojego **portalu administracyjnego SuccessFactors** jako administrator.
    
8. Odwiedź stronę **zabezpieczeń aplikacji** i macierzysty **pojedynczy znak w funkcji**. 

9. Umieść wszystkie wartości w **zresetować tokenu** i kliknij przycisk **zapisać tokenu** do włączenia funkcji logowania jednokrotnego SAML.
   
    ![Konfigurowanie rejestracji jednokrotnej po stronie aplikacji][11]

    > [!NOTE] 
    > Ta wartość jest używana jako przełącznika wł. / wył. Po zapisaniu wartości logowania jednokrotnego SAML jest włączone. Po zapisaniu pustego logowania jednokrotnego SAML jest wyłączona.

10. Macierzysty poniżej zrzut ekranu i wykonaj następujące czynności:
   
    ![Konfigurowanie rejestracji jednokrotnej po stronie aplikacji][12]
   
    a. Wybierz **logowania jednokrotnego SAML v2** przycisk radiowy
   
    b. Ustaw **nazwa jednostki zostanie SAML**(na przykład wystawcy SAML + nazwa firmy).
   
    c. W **adres URL wystawcy** pole tekstowe, Wklej **identyfikator jednostki SAML** wartość, która została skopiowana z portalu Azure.
   
    d. Wybierz **odpowiedzi (odbiorcy wygenerowanych/IdP/AP)** jako **wymagają podpisu obowiązkowe**.
   
    e. Wybierz **włączone** jako **Włącz flagę SAML**.
   
    f. Wybierz **nr** jako **podpisu żądania logowania (rz wygenerowanych/SP/RP)**.
   
    g. Wybierz **profilu przeglądarki lub używanego po** jako **profilu SAML**.
   
    h. Wybierz **nr** jako **wymusić nieprawidłowy okres certyfikatu**.
   
    i. Skopiuj zawartość pliku certyfikatu pobrany z portalu Azure, a następnie wklej go do **certyfikatu weryfikacji SAML** pola tekstowego.

    > [!NOTE] 
    > Zawartość certyfikatu musi mieć rozpocząć certyfikatu i na końcu tagi certyfikatu.

11. Przejdź do SAML V2, a następnie wykonaj następujące czynności:
   
    ![Konfigurowanie rejestracji jednokrotnej po stronie aplikacji][13]
   
    a. Wybierz **tak** jako **inicjowane SP wylogowania globalnego obsługują**.
   
    b. W **globalne wylogowania adres URL usługi (LogoutRequest docelowy)** pole tekstowe, Wklej **Sign-Out URL** wartość, która została skopiowana tworzą portalu Azure.
   
    c. Wybierz **nr** jako **wymagają sp należy szyfrować wszystkie element NameID**.
   
    d. Wybierz **nieokreślony** jako **NameID Format**.
   
    e. Wybierz **tak** jako **Włącz sp zainicjował logowania (AuthnRequest)**.
   
    f. W **żądanie wysłania jako wystawca firmie** pole tekstowe, Wklej **SAML pojedynczy znak na adres URL usługi** wartość, która została skopiowana z portalu Azure.

12. Wykonaj następujące kroki, aby wprowadzić nazwy logowania użytkowników bez uwzględniania wielkości liter.
   
    ![Konfigurowanie rejestracji jednokrotnej][29]
    
    a. Odwiedź stronę **ustawienia firmy**(w dolnej).
   
    b. Zaznacz pole wyboru obok **włączyć Username Non-uwzględniana wielkość liter**.
   
    c.Click **zapisać**.
   
    > [!NOTE] 
    > Próba włączenia to system sprawdza, czy tworzy duplikat nazwy logowania SAML. Na przykład, jeśli klient ma nazw użytkowników Użytkownik1 i Użytkownik1. Zdjęciu uwzględniana wielkość liter powoduje, że te duplikaty. System umożliwia komunikat o błędzie i nie obsługuje tę funkcję. Klient musi Zmień jedną z nazw użytkowników, aby została wpisana inny.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-successfactors-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-successfactors-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-successfactors-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-successfactors-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-successfactors-test-user"></a>Tworzenie użytkownika testowego SuccessFactors

Aby umożliwić użytkownikom usługi Azure AD zalogować się do SuccessFactors, musi być przygotowana do SuccessFactors.  
W przypadku SuccessFactors Inicjowanie obsługi to zadanie ręczne.

Aby uzyskać użytkownicy utworzeni w SuccessFactors, należy skontaktować się [SuccessFactors obsługuje zespołu](https://www.successfactors.com/en_us/support.html).

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu SuccessFactors.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta SuccessFactors, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **SuccessFactors**.

    ![Łącze SuccessFactors na liście aplikacji](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka SuccessFactors w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji SuccessFactors.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_04.png

[11]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_09.png
[14]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_05.png
[15]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_06.png
[29]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_10.png

[100]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_203.png


---
title: 'Samouczek: Integracji Azure Active Directory z TeamSeer | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i TeamSeer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 6ec4806f-fe0f-4ed7-8cfa-32d1c840433f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: b4fd5b65bf0f5ce90aa93f89aad4617976b3a490
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34350852"
---
# <a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Samouczek: Integracji Azure Active Directory z TeamSeer

Z tego samouczka dowiesz się integrowanie TeamSeer z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD TeamSeer zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do TeamSeer
- Umożliwia użytkownikom automatycznie pobrać zalogowane do TeamSeer (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z TeamSeer, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- TeamSeer jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie TeamSeer z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-teamseer-from-the-gallery"></a>Dodawanie TeamSeer z galerii
Aby skonfigurować integrację TeamSeer w usłudze Azure AD, należy dodać TeamSeer z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać TeamSeer z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **TeamSeer**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_search.png)

5. W panelu wyników wybierz **TeamSeer**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z TeamSeer na podstawie użytkownika testowego, nazywany "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w TeamSeer jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w TeamSeer musi się.

W TeamSeer, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z TeamSeer, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego TeamSeer](#creating-a-teamseer-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta TeamSeer połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji TeamSeer.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z TeamSeer, wykonaj następujące czynności:**

1. W portalu Azure na **TeamSeer** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_samlbase.png)

3. Na **TeamSeer domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_url.png)

     W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://www.teamseer.com/<companyid>`

    > [!NOTE] 
    > Wartość nie jest prawdziwe. Zaktualizuj tę wartość z adresem URL logowania rzeczywistych. Skontaktuj się z [zespołem pomocy technicznej klienta TeamSeer](http://pages.theaccessgroup.com/solutions_business-suite_absence-management_contact.html) można uzyskać wartość. 
 
4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-teamseer-tutorial/tutorial_general_400.png)

6. Na **konfiguracji TeamSeer** , kliknij przycisk **skonfigurować TeamSeer** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_configure.png)

7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy TeamSeer.

8. Przejdź do **HR Admin**.
   
    ![Administrator HR](./media/active-directory-saas-teamseer-tutorial/ic789634.png "HR administratora")

9. Kliknij przycisk **Instalatora**.
   
    ![Instalator](./media/active-directory-saas-teamseer-tutorial/ic789635.png "Instalatora")

10. Kliknij przycisk **konfigurowanie szczegóły dostawcy SAML**.
   
    ![Ustawienia SAML](./media/active-directory-saas-teamseer-tutorial/ic789636.png "ustawienia SAML")

11. W sekcji szczegółów SAML dostawcy wykonaj następujące czynności:
   
    ![Ustawienia SAML](./media/active-directory-saas-teamseer-tutorial/ic789637.png "ustawienia SAML")   

    a. Wklej **pojedynczy znak na adres URL usługi** do wartości w **adres URL** pola tekstowego.
          
    b. Otwórz w Notatniku certyfikatu zakodowanego base-64, skopiuj zawartość go do Schowka, a następnie wklej go do **certyfikatu publicznego IdP** pola tekstowego.

12. Aby ukończyć konfigurację dostawcy SAML, wykonaj następujące czynności:
    
    ![Ustawienia SAML](./media/active-directory-saas-teamseer-tutorial/ic789638.png "ustawienia SAML") 

    a. W **Test adresy E-mail**, wpisz adres e-mail użytkownika testowego. 
  
    b. W **wystawcy** tekstowym, wpisz adres URL wystawcy usługodawcy. 
  
    c. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-teamseer-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-teamseer-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-teamseer-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-teamseer-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-teamseer-test-user"></a>Tworzenie użytkownika testowego TeamSeer

Aby umożliwić użytkownikom usługi Azure AD zalogować się do TeamSeer, ich należy udostępnić w celu ShiftPlanning. W przypadku TeamSeer Inicjowanie obsługi to zadanie ręczne.

**Aby udostępnić konta użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do Twojego **TeamSeer** witryny firmy jako administrator.

2. Wykonaj następujące czynności:
   
    ![Administrator HR](./media/active-directory-saas-teamseer-tutorial/ic789640.png "HR administratora")  
 
    a. Przejdź do **HR Admin \> użytkowników**.
  
    b. Kliknij przycisk **Uruchom Kreatora nowego użytkownika**.

3. W **szczegóły użytkownika** sekcji, wykonaj następujące czynności:
   
    ![Szczegóły użytkownika](./media/active-directory-saas-teamseer-tutorial/ic789641.png "szczegóły użytkownika")

    a. Typ **imię**, **nazwisko**, **nazwę użytkownika (adres E-mail)** poprawnego konta usługi AAD chcesz udostępnić w do powiązanych pól tekstowych.
  
    b. Kliknij przycisk **Dalej**.

4. Postępuj zgodnie z wyświetlanymi instrukcje dotyczące dodawania nowego użytkownika i kliknij przycisk **Zakończ**.

>[!NOTE]
>Możesz użyć innych TeamSeer użytkownika konta tworzenia narzędzi lub interfejsów API dostarczonych przez TeamSeer do udostępnienia konta użytkownika usługi Azure AD. 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu TeamSeer.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta TeamSeer, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **TeamSeer**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

Jeśli chcesz przetestować jednego ustawienia logowania jednokrotnego, otwórz Panel dostępu. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_203.png


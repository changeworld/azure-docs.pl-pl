---
title: 'Samouczek: Integracji Azure Active Directory z OpsGenie | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i OpsGenie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 41b59b22-a61d-4fe6-ab0d-6c3991d1375f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2018
ms.author: jeedes
ms.openlocfilehash: b0cd54c7750e85eb7b4e1ba6be309c585d5a7b4e
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="tutorial-azure-active-directory-integration-with-opsgenie"></a>Samouczek: Integracji Azure Active Directory z OpsGenie

Z tego samouczka dowiesz się integrowanie OpsGenie z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD OpsGenie zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do OpsGenie
- Umożliwia użytkownikom automatycznie pobrać zalogowane do OpsGenie (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z OpsGenie, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- OpsGenie logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie OpsGenie z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-opsgenie-from-the-gallery"></a>Dodawanie OpsGenie z galerii
Aby skonfigurować integrację usługi Azure AD OpsGenie, należy dodać OpsGenie z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać OpsGenie z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **OpsGenie**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_search.png)

5. W panelu wyników wybierz **OpsGenie**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z OpsGenie w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w OpsGenie jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w OpsGenie musi się.

W OpsGenie, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z OpsGenie, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego OpsGenie](#creating-a-opsgenie-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta OpsGenie połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji OpsGenie.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z OpsGenie, wykonaj następujące czynności:**

1. W portalu Azure na **OpsGenie** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_samlbase.png)

3. Na **OpsGenie domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_url.png)

    W **adres URL logowania** tekstowym, wpisz adres URL: `https://app.opsgenie.com/auth/login`

4. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-opsgenie-tutorial/tutorial_general_400.png)

5. Na **konfiguracji OpsGenie** , kliknij przycisk **skonfigurować OpsGenie** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczy znak na adres URL usługi** z sekcji krótkimi opisami.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_configure.png)

6. Aby wygenerować **adres URL metadanych**, wykonaj następujące czynności:

    a. Kliknij przycisk **rejestracji aplikacji**.
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_appregistrations.png)
   
    b. Kliknij przycisk **punkty końcowe** otworzyć **punkty końcowe** okno dialogowe.  
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_endpointicon.png)

    c. Kliknij przycisk Kopiuj, aby skopiować **dokument METADANYCH usług FEDERACYJNYCH** adresu url i wklej go do Notatnika.
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_endpoint.png)
     
    d. Teraz przejdź do strony właściwości **OpsGenie** i skopiuj **identyfikator aplikacji** przy użyciu **kopiowania** przycisk i wklej go do Notatnika.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_appid.png)

    e. Generowanie **adres URL metadanych** przy użyciu następującego wzorca: `<FEDERATION METADATA DOCUMENT url>?appid=<application id>`

7. Otwórz inne wystąpienie przeglądarki, a następnie zaloguj się do OpsGenie jako administrator.

8. Kliknij przycisk **ustawienia**, a następnie kliknij przycisk **rejestracji jednokrotnej** kartę.
   
    ![OpsGenie rejestracji jednokrotnej](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_06.png)

9. Aby włączyć logowanie Jednokrotne, zaznacz **włączone**.
   
    ![Ustawienia OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_07.png) 

10. W **dostawcy** kliknij **usługi Azure Active Directory** kartę.
   
    ![Ustawienia OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_08.png) 

11. Na stronie okna dialogowego usługi Azure Active Directory wykonaj następujące czynności:
   
    ![Ustawienia OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_09.png)
    
    a. W **SAML 2.0 Endpoint** pole tekstowe, Wklej **pojedynczy znak na adres URL usługi**wartość, która została skopiowana z portalu Azure.
    
    b. W **adres Url metadanych:** pole tekstowe, Wklej **adres URL metadanych**wartość, która została skopiowana z portalu Azure.
    
    c. Kliknij przycisk **zapisać zmiany**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-opsgenie-test-user"></a>Tworzenie użytkownika testowego OpsGenie

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w OpsGenie. 

1. W oknie przeglądarki sieci web Zaloguj się do dzierżawy OpsGenie jako administrator.

2. Przejdź do listy użytkowników, klikając **użytkownika** w lewym panelu.
   
   ![Ustawienia OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_10.png) 

3. Kliknij przycisk **dodać użytkownika**.

4. Na **Dodaj użytkownika** okna dialogowego, wykonaj następujące czynności:
   
   ![Ustawienia OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_11.png)
   
   a. W **E-mail** pole tekstowe, wpisz adres e-mail BrittaSimon rozwiązane w usłudze Azure Active Directory.
   
   b. W **imię i nazwisko** pole tekstowe, typ **Simona Britta**.
   
   c. Kliknij pozycję **Zapisz**. 

>[!NOTE]
>Britta pobiera wiadomość e-mail z instrukcjami konfigurowania jej profilu.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu OpsGenie.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta OpsGenie, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **OpsGenie**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

Celem tej sekcji służy do testowania konfigurację usługi Azure AD z logowania jednokrotnego za pomocą panelu dostępu.

Po kliknięciu kafelka OpsGenie w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji OpsGenie.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_203.png


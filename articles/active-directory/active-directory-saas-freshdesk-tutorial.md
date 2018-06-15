---
title: 'Samouczek: Integracji Azure Active Directory z FreshDesk | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i FreshDesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 76f91f865b2f2135e319d164a0823c57cda837c9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34345337"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Samouczek: Integracji Azure Active Directory z FreshDesk

Z tego samouczka dowiesz się integrowanie FreshDesk z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD FreshDesk zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do FreshDesk
- Umożliwia użytkownikom automatycznie pobrać zalogowane do FreshDesk (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu zarządzania Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z FreshDesk, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- FreshDesk jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie FreshDesk z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-freshdesk-from-the-gallery"></a>Dodawanie FreshDesk z galerii
Aby skonfigurować integrację usługi Azure AD FreshDesk, należy dodać FreshDesk z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać FreshDesk z galerii, wykonaj następujące czynności:**

1. W  **[portalu zarządzania Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Kliknij przycisk **Dodaj** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **FreshDesk**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_search.png)

5. W panelu wyników wybierz **FreshDesk**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z FreshDesk w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w FreshDesk jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w FreshDesk musi się.

Ta relacja łącza zostanie nawiązane, przypisując wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** w FreshDesk.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z FreshDesk, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego FreshDesk](#creating-a-freshdesk-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta FreshDesk połączonego z jej reprezentacji usługi Azure AD.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu zarządzania Azure i skonfigurować logowanie jednokrotne w aplikacji FreshDesk.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z FreshDesk, wykonaj następujące czynności:**

1. W portalu zarządzania Azure na **FreshDesk** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okna dialogowego, jako **tryb** wybierz **na języku SAML logowania jednokrotnego** Włącz funkcji logowania jednokrotnego.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_samlbase.png)

3. Na **FreshDesk domeny i adres URL** sekcji, wprowadź **adres URL logowania** jako: `https://<tenant-name>.freshdesk.com` lub wszelkie inne wartości Freshdesk ma sugerowane.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_url.png)

    > [!NOTE] 
    > Należy pamiętać, że nie jest rzeczywistą wartość. Należy zaktualizować wartości z adresem URL logowania rzeczywistych. Skontaktuj się z [zespołem pomocy technicznej klienta FreshDesk](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) aby zyskać tę wartość.  

4. Na **certyfikat podpisywania SAML** kliknij **certyfikatu** , a następnie Zapisz certyfikat na komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-freshdesk-tutorial/tutorial_general_400.png)

6. Na **konfiguracji FreshDesk** kliknij **skonfigurować FreshDesk** można otworzyć Konfigurowanie logowania jednokrotnego okna. Skopiuj SAML pojedynczy znak na adres URL usługi i adres URL Sign-Out z **krótkimi opisami** sekcji.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_configure.png)

7. W oknie przeglądarki innej witryny sieci web Zaloguj się do witryny firmy Freshdesk jako administrator.

8. W menu u góry kliknij **Admin**.
   
   ![Administrator](./media/active-directory-saas-freshdesk-tutorial/IC776768.png "administratora")

9. W **ustawienia ogólne** , kliknij pozycję **zabezpieczeń**.
   
   ![Zabezpieczenia](./media/active-directory-saas-freshdesk-tutorial/IC776769.png "zabezpieczeń")

10. W **zabezpieczeń** sekcji, wykonaj następujące czynności:
   
    ![Jednokrotne](./media/active-directory-saas-freshdesk-tutorial/IC776770.png "jednokrotne")
   
    a. Aby uzyskać **pojedynczy znak na rejestracji jednokrotnej (SSO)**, wybierz pozycję **na**.

    b. Wybierz **logowania jednokrotnego SAML**.

    c. Typ **SAML pojedynczy znak na adres URL usługi** skopiowany z portalu Azure do **adres URL logowania SAML** pola tekstowego.

    d. Typ **Sign-Out URL** skopiowany z portalu Azure do **adresu URL wylogowania** pola tekstowego.

    e. Kopiuj **odcisk palca** wartość z certyfikat pobrany z portalu Azure i wklej ją do **odcisk palca certyfikatu zabezpieczeń** pola tekstowego.  
 
    >[!TIP]
    >Aby uzyskać więcej informacji, zobacz [jak pobrać wartość odcisku palca certyfikatu](http://youtu.be/YKQF266SAxI). 
    
    f. Kliknij pozycję **Zapisz**.


### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu zarządzania Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu zarządzania Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_01.png) 

2. Przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy** do wyświetlenia na liście Użytkownicy.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_02.png) 

3. W górnej części okna dialogowego kliknij **Dodaj** otworzyć **użytkownika** okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-freshdesk-test-user"></a>Tworzenie użytkownika testowego FreshDesk

Aby włączyć użytkowników usługi Azure AD zalogować się do FreshDesk, musi być przygotowana do FreshDesk.  
W przypadku FreshDesk Inicjowanie obsługi to zadanie ręczne.

**Aby udostępnić konta użytkowników, wykonaj następujące czynności:**

1. Zaloguj się do Twojego **Freshdesk** dzierżawy.
2. W menu u góry kliknij **Admin**.
   
   ![Administrator](./media/active-directory-saas-freshdesk-tutorial/IC776772.png "administratora")

3. W **ustawienia ogólne** , kliknij pozycję **agentów**.
   
   ![Agenci](./media/active-directory-saas-freshdesk-tutorial/IC776773.png "agentów")

4. Kliknij przycisk **nowego agenta**.
   
    ![Nowy Agent](./media/active-directory-saas-freshdesk-tutorial/IC776774.png "nowego agenta")

5. W oknie dialogowym informacji o agencie wykonaj następujące czynności:
   
   ![Informacji o agencie](./media/active-directory-saas-freshdesk-tutorial/IC776775.png "informacji o agencie")
   
   a. W **imię i nazwisko** tekstowym, wpisz nazwę konta usługi Azure AD, aby udostępnić.

   b. W **E-mail** pole tekstowe, adres e-mail konta usługi Azure AD, aby udostępnić typu usługi Azure AD.

   c. W **tytuł** tekstowym, wpisz nazwę konta usługi Azure AD, aby udostępnić.

   d. Wybierz **roli agentów**, a następnie kliknij przycisk **przypisać**.
       
   e. Kliknij pozycję **Zapisz**.     
   
    >[!NOTE]
    >Właściciel konta usługi Azure AD otrzyma wiadomość e-mail zawierającą łącze do potwierdzenia konta, zanim zostanie aktywowany. 
    > 
    
    >[!NOTE]
    >Możesz użyć innych Freshdesk użytkownika konta tworzenia narzędzi lub interfejsów API dostarczonych przez Freshdesk do kont użytkowników usługi AAD. Aby FreshDesk.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do użycia usługi Azure logowania jednokrotnego za udostępnienie jej do pola.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta FreshDesk, wykonaj następujące czynności:**

1. Otwórz widok aplikacji w portalu zarządzania Azure, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **FreshDesk**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka FreshDesk w panelu dostępu, należy pobrać strony logowania, aby pobrać zalogowane FreshDesk aplikacji.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_203.png


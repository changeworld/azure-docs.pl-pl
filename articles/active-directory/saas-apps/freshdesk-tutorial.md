---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługi FreshDesk | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i usługi FreshDesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: d0fbed347805a581fb66e0218290993817277214
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39428336"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Samouczek: Integracja usługi Azure Active Directory z usługi FreshDesk

W tym samouczku dowiesz się, jak zintegrować usługi FreshDesk w usłudze Azure Active Directory (Azure AD).

Integrowanie usługi FreshDesk z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do usługi FreshDesk
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do usługi FreshDesk (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — portalu zarządzania platformy Azure

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługi FreshDesk, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Usługi FreshDesk logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie usługi FreshDesk z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-freshdesk-from-the-gallery"></a>Dodawanie usługi FreshDesk z galerii
Aby skonfigurować integrację z usługi FreshDesk w usłudze Azure AD, należy dodać usługi FreshDesk z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać usługi FreshDesk z galerii, wykonaj następujące czynności:**

1. W  **[portalu zarządzania systemu Azure](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Kliknij przycisk **Dodaj** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **usługi FreshDesk**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/freshdesk-tutorial/tutorial_freshdesk_search.png)

1. W panelu wyników wybierz **usługi FreshDesk**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/freshdesk-tutorial/tutorial_freshdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą usługi FreshDesk w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w usłudze FreshDesk z użytkownikiem w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w usłudze FreshDesk musi zostać ustanowione.

Ustanowieniu tej relacji łączy, przypisując wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** w usłudze FreshDesk.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego z usługi FreshDesk, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego usługi FreshDesk](#creating-a-freshdesk-test-user)**  — odpowiednikiem Britta Simon znajdują się w usłudze FreshDesk połączonego z usługi Azure AD reprezentacja jej.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowanie jednokrotne w portalu zarządzania platformy Azure i konfigurowanie logowania jednokrotnego w aplikacji usługi FreshDesk.

**Aby skonfigurować usługę Azure AD logowania jednokrotnego z usługi FreshDesk, wykonaj następujące czynności:**

1. W portalu zarządzania platformy Azure na **usługi FreshDesk** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, jako **tryb** wybierz **opartej na SAML logowania jednokrotnego** na włączanie logowania jednokrotnego.
 
    ![Konfigurowanie logowania jednokrotnego](./media/freshdesk-tutorial/tutorial_freshdesk_samlbase.png)

1. Na **usługi FreshDesk domena i adresy URL** sekcji, wprowadź **adres URL logowania** jako: `https://<tenant-name>.freshdesk.com` lub dowolna inna wartość proponuje usługi Freshdesk.

    ![Konfigurowanie logowania jednokrotnego](./media/freshdesk-tutorial/tutorial_freshdesk_url.png)

    > [!NOTE] 
    > Należy pamiętać, że nie jest rzeczywistą wartość. Należy zaktualizować wartość z adresem URL rzeczywistej logowania jednokrotnego. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta usługi FreshDesk](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) aby zyskać tę wartość.  

1. Na **certyfikat podpisywania SAML** kliknij **certyfikatu** , a następnie Zapisz certyfikat na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/freshdesk-tutorial/tutorial_freshdesk_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/freshdesk-tutorial/tutorial_general_400.png)

1. Na **konfiguracji usługi FreshDesk** kliknij **skonfigurować usługi FreshDesk** otworzyć Konfigurowanie logowania jednokrotnego okno. Skopiuj SAML pojedynczego logowania jednokrotnego adres URL usługi i adres URL wylogowania **krótki** sekcji.

    ![Konfigurowanie logowania jednokrotnego](./media/freshdesk-tutorial/tutorial_freshdesk_configure.png)

1. W oknie przeglądarki internetowej innej Zaloguj się do usługi Freshdesk firmowa witryna, jako administrator.

1. W menu u góry kliknij **administratora**.
   
   ![Administrator](./media/freshdesk-tutorial/IC776768.png "administratora")

1. W **ustawienia ogólne** kliknij pozycję **zabezpieczeń**.
   
   ![Zabezpieczenia](./media/freshdesk-tutorial/IC776769.png "zabezpieczeń")

1. W **zabezpieczeń** sekcji, wykonaj następujące czynności:
   
    ![Logowanie jednokrotne](./media/freshdesk-tutorial/IC776770.png "logowanie jednokrotne")
   
    a. Aby uzyskać **logowanie jednokrotne (SSO)**, wybierz opcję **na**.

    b. Wybierz **logowania jednokrotnego SAML**.

    c. Typ **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowaną z witryny Azure portal do **adres URL logowania języka SAML** pola tekstowego.

    d. Typ **adres URL wylogowania** skopiowaną z witryny Azure portal do **adres URL wylogowania** pola tekstowego.

    e. Kopiuj **odcisk palca** z pobranego certyfikatu z witryny Azure portal i wklej go do **odcisk palca certyfikatu zabezpieczeń** pola tekstowego.  
 
    >[!TIP]
    >Aby uzyskać więcej informacji, zobacz [jak pobrać wartość odcisku palca certyfikatu](http://youtu.be/YKQF266SAxI). 
    
    f. Kliknij pozycję **Zapisz**.


### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu zarządzania platformy Azure o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu zarządzania Azure**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/freshdesk-tutorial/create_aaduser_01.png) 

1. Przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy** do wyświetlania listy użytkowników.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/freshdesk-tutorial/create_aaduser_02.png) 

1. W górnej części okna dialogowego kliknij **Dodaj** otworzyć **użytkownika** okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/freshdesk-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/freshdesk-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-freshdesk-test-user"></a>Tworzenie użytkownika testowego usługi FreshDesk

Aby umożliwić użytkownikom usługi Azure AD zalogować się do usługi FreshDesk, musi być obsługiwana w usłudze FreshDesk.  
W przypadku usługi FreshDesk Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby udostępnić konta użytkowników, wykonaj następujące czynności:**

1. Zaloguj się do Twojej **usługi Freshdesk** dzierżawy.
1. W menu u góry kliknij **administratora**.
   
   ![Administrator](./media/freshdesk-tutorial/IC776772.png "administratora")

1. W **ustawienia ogólne** kliknij pozycję **agentów**.
   
   ![Agenci](./media/freshdesk-tutorial/IC776773.png "agentów")

1. Kliknij przycisk **nowy Agent**.
   
    ![Nowy Agent](./media/freshdesk-tutorial/IC776774.png "nowego agenta")

1. W oknie dialogowym informacji o agencie wykonaj następujące czynności:
   
   ![Informacji o agencie](./media/freshdesk-tutorial/IC776775.png "informacji o agencie")
   
   a. W **imię i nazwisko** polu tekstowym wpisz nazwę konta usługi Azure AD, do aprowizowania.

   b. W **E-mail** polu tekstowym wpisz usługi Azure AD adres e-mail konta usługi Azure AD, do aprowizowania.

   c. W **tytuł** polu tekstowym wpisz nazwę konta usługi Azure AD, do aprowizowania.

   d. Wybierz **roli agentów**, a następnie kliknij przycisk **przypisać**.
       
   e. Kliknij pozycję **Zapisz**.     
   
    >[!NOTE]
    >Właściciel konta usługi Azure AD otrzyma wiadomość e-mail zawierającą link do potwierdzenia konta, zanim zostanie aktywowany. 
    > 
    
    >[!NOTE]
    >Można użyć jakichkolwiek innych usługi Freshdesk użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez usługi Freshdesk do aprowizacji kont użytkowników usługi AAD. do usługi FreshDesk.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do użycia platformy Azure logowania jednokrotnego przez udostępnienie jej do pola.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon usługi FreshDesk, wykonaj następujące czynności:**

1. W portalu zarządzania platformy Azure powoduje ono otwarcie widoku aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **usługi FreshDesk**.

    ![Konfigurowanie logowania jednokrotnego](./media/freshdesk-tutorial/tutorial_freshdesk_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi FreshDesk w panelu dostępu, należy uzyskać strony logowania, aby pobrać zalogowanych do aplikacji usługi FreshDesk.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/freshdesk-tutorial/tutorial_general_01.png
[2]: ./media/freshdesk-tutorial/tutorial_general_02.png
[3]: ./media/freshdesk-tutorial/tutorial_general_03.png
[4]: ./media/freshdesk-tutorial/tutorial_general_04.png

[100]: ./media/freshdesk-tutorial/tutorial_general_100.png

[200]: ./media/freshdesk-tutorial/tutorial_general_200.png
[201]: ./media/freshdesk-tutorial/tutorial_general_201.png
[202]: ./media/freshdesk-tutorial/tutorial_general_202.png
[203]: ./media/freshdesk-tutorial/tutorial_general_203.png


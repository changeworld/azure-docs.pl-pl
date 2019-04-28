---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą TalentLMS | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i TalentLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: c903d20d-18e3-42b0-b997-6349c5412dde
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fa78ec2b5623dfd010a8fe5709916a47e221a9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60732334"
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą TalentLMS

W tym samouczku dowiesz się, jak zintegrować TalentLMS w usłudze Azure Active Directory (Azure AD).

Integrowanie TalentLMS z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do TalentLMS
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do TalentLMS (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą TalentLMS, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- TalentLMS logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej tutaj: [Wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie TalentLMS z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-talentlms-from-the-gallery"></a>Dodawanie TalentLMS z galerii
Aby skonfigurować integrację TalentLMS w usłudze Azure AD, należy dodać TalentLMS z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać TalentLMS z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **TalentLMS**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/talentlms-tutorial/tutorial_talentlms_search.png)

1. W panelu wyników wybierz **TalentLMS**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/talentlms-tutorial/tutorial_talentlms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą TalentLMS w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w TalentLMS do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w TalentLMS musi można ustanowić.

W TalentLMS, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą TalentLMS, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego TalentLMS](#creating-a-talentlms-test-user)**  — aby odpowiednikiem Britta Simon w TalentLMS połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji TalentLMS.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z TalentLMS, wykonaj następujące czynności:**

1. W witrynie Azure portal na **TalentLMS** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/talentlms-tutorial/tutorial_talentlms_samlbase.png)

1. Na **TalentLMS domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/talentlms-tutorial/tutorial_talentlms_url.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<tenant-name>.TalentLMSapp.com`

    b. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `http://<tenant-name>.talentlms.com`

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL i identyfikatora logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta TalentLMS](https://www.talentlms.com/contact) do uzyskania tych wartości. 
 
1. Na **certyfikat podpisywania SAML** sekcji, skopiuj **odcisk PALCA** wartość z certyfikatu.

    ![Konfigurowanie logowania jednokrotnego](./media/talentlms-tutorial/tutorial_talentlms_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/talentlms-tutorial/tutorial_general_400.png)

1. Na **konfiguracji TalentLMS** , kliknij przycisk **skonfigurować TalentLMS** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/talentlms-tutorial/tutorial_talentlms_configure.png)  

1. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy TalentLMS.

1. W **konta u & stawienia** kliknij **użytkowników** kartę.
   
    ![Ustawienia & konta](./media/talentlms-tutorial/IC777296.png "& Ustawienia konta")

1. Kliknij przycisk **logowanie jednokrotne (SSO)**,

1. W sekcji Single sign-on (Logowanie jednokrotne) wykonaj następujące kroki:
   
    ![Logowanie jednokrotne](./media/talentlms-tutorial/IC777297.png "Logowanie jednokrotne")   

    a. Z **typu integracja logowania jednokrotnego** listy wybierz **SAML 2.0**.

    b. W **dostawcy tożsamości (IDP)** pola tekstowego, Wklej wartość **SAML identyfikator jednostki**, które zostały skopiowane z witryny Azure portal.
 
    c. Wklej **odcisk palca** wartości z witryny Azure portal do **odcisk palca certyfikatu** pola tekstowego.    

    d.  W **zdalnego logowania adresu URL** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego adres URL usługi**, które zostały skopiowane z witryny Azure portal.
 
    e. W **zdalny adres URL wylogowania** pola tekstowego, Wklej wartość **adres URL wylogowania**, które zostały skopiowane z witryny Azure portal.

    f. Wypełnij następujące informacje: 

    * W **TargetedID** pole tekstowe, wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`
     
    * W **imię** pole tekstowe, wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`
    
    * W **nazwisko** pole tekstowe, wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`
    
    * W **E-mail** pole tekstowe, wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`
    
1. Kliknij pozycję **Zapisz**.
 
> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/talentlms-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/talentlms-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/talentlms-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/talentlms-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-talentlms-test-user"></a>Tworzenie użytkownika testowego TalentLMS

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do TalentLMS, musi być obsługiwana w TalentLMS. W przypadku TalentLMS Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do Twojej **TalentLMS** dzierżawy.

1. Kliknij przycisk **użytkowników**, a następnie kliknij przycisk **Dodaj użytkownika**.

1. Na **Dodaj użytkownika** okna dialogowego strony, wykonaj następujące czynności:
   
    ![Dodawanie użytkownika](./media/talentlms-tutorial/IC777299.png "Dodawanie użytkownika")  

    a. W polu tekstowym **First name (Imię)** wprowadź imię użytkownika, na przykład **Britta**.

    b. W polu tekstowym **Last name (Nazwisko)** wprowadź nazwisko użytkownika, na przykład **Simon**.
 
    c. W **adres E-mail** polu tekstowym wprowadź adres e-mail użytkownika, takich jak **brittasimon\@contoso.com**.

    d. Kliknij pozycję **Add User** (Dodaj użytkownika).

>[!NOTE]
>Można użyć jakichkolwiek innych TalentLMS użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez TalentLMS do aprowizacji kont użytkowników usługi AAD.
 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do TalentLMS.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon TalentLMS, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **TalentLMS**.

    ![Konfigurowanie logowania jednokrotnego](./media/talentlms-tutorial/tutorial_talentlms_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka TalentLMS w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji TalentLMS

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/talentlms-tutorial/tutorial_general_01.png
[2]: ./media/talentlms-tutorial/tutorial_general_02.png
[3]: ./media/talentlms-tutorial/tutorial_general_03.png
[4]: ./media/talentlms-tutorial/tutorial_general_04.png

[100]: ./media/talentlms-tutorial/tutorial_general_100.png

[200]: ./media/talentlms-tutorial/tutorial_general_200.png
[201]: ./media/talentlms-tutorial/tutorial_general_201.png
[202]: ./media/talentlms-tutorial/tutorial_general_202.png
[203]: ./media/talentlms-tutorial/tutorial_general_203.png


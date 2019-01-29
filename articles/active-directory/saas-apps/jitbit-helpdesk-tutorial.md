---
title: 'Samouczek: Integracja usługi Azure Active Directory z pomocą techniczną Jitbit | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Jitbit działu pomocy technicznej.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 15ce27d4-0621-4103-8a34-e72c98d72ec3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: e240efe98e04195947829b1821e0f6b6d92c2be6
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55173449"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Samouczek: Integracja usługi Azure Active Directory z Jitbit działu pomocy technicznej

W tym samouczku dowiesz się, jak zintegrować Jitbit działu pomocy technicznej za pomocą usługi Azure Active Directory (Azure AD).

Integrowanie pomocy Jitbit z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do pomocy technicznej Jitbit
- Użytkowników, aby automatycznie uzyskać zalogowanych do Jitbit działu pomocy technicznej (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z pomocą techniczną Jitbit, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Pomoc techniczna Jitbit logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie pomocy technicznej Jitbit z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-jitbit-helpdesk-from-the-gallery"></a>Dodawanie pomocy technicznej Jitbit z galerii
Aby skonfigurować integrację Jitbit pomoc techniczna do usługi Azure AD, należy dodać Jitbit działu pomocy technicznej z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Jitbit działu pomocy technicznej z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **pomocy Jitbit**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_search.png)

1. W panelu wyników wybierz **pomocy Jitbit**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji można skonfigurować, i test usługi Azure AD logowanie jednokrotne za pomocą techniczną Jitbit oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Jitbit pomocy dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w pomocy Jitbit musi można ustanowić.

W Jitbit działu pomocy technicznej, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Jitbit działu pomocy technicznej, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego pomocy Jitbit](#creating-a-jitbit-helpdesk-test-user)**  — aby odpowiednikiem Britta Simon w pomocy Jitbit, w którym jest połączony z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Jitbit Helpdesk.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Jitbit działu pomocy technicznej, wykonaj następujące czynności:**

1. W witrynie Azure portal na **pomocy Jitbit** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_samlbase.png)

1. Na **Jitbit pomocy technicznej domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_url.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: 
    | |     
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Ta wartość nie jest prawdziwe. Zaktualizuj tę wartość przy użyciu rzeczywisty adres URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta pomoc techniczna Jitbit](https://www.jitbit.com/support/) aby zyskać tę wartość. 
    
    b.  W **identyfikator** pole tekstowe, wpisz adres URL, jako pokazano poniżej: `https://www.jitbit.com/web-helpdesk/`

    
 


1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/jitbit-helpdesk-tutorial/tutorial_general_400.png)

1. Na **konfiguracji pomocy technicznej Jitbit** , kliknij przycisk **Konfigurowanie pomocy Jitbit** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_configure.png) 

1. W oknie przeglądarki internetowej innej Zaloguj się do witryny firmy Jitbit działu pomocy technicznej, jako administrator.

1. Na pasku narzędzi u góry kliknij **administracji**.
   
    ![Administracja](./media/jitbit-helpdesk-tutorial/ic777681.png "Administracja")

1. Kliknij przycisk **ustawienia ogólne**.
   
    ![Użytkownikom, firmom i uprawnienia](./media/jitbit-helpdesk-tutorial/ic777680.png "użytkownikom, firmom i uprawnienia")

1. W **ustawienia uwierzytelniania** konfiguracji sekcji, wykonaj następujące czynności:
   
    ![Ustawienia uwierzytelniania](./media/jitbit-helpdesk-tutorial/ic777683.png "ustawienia uwierzytelniania")
    
    a. Wybierz **Włącz SAML 2.0 pojedynczego logowania**, aby zalogować się przy użyciu pojedynczego logowania jednokrotnego (SSO) przy użyciu **OneLogin**.

    b. W **adresu URL punktu końcowego** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowanej w witrynie Azure portal.

    c. Otwórz swoje **base-64** zakodowane certyfikatów w programie Notatnik, skopiuj jego zawartość do Schowka, a następnie wklej go do **certyfikat X.509** textbox

    d. Kliknij przycisk **Zapisz zmiany**.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/jitbit-helpdesk-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/jitbit-helpdesk-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/jitbit-helpdesk-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/jitbit-helpdesk-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz nazwę jako **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-jitbit-helpdesk-test-user"></a>Tworzenie użytkownika testowego Jitbit działu pomocy technicznej

Aby umożliwić użytkownikom usługi Azure AD zalogować się do działu pomocy technicznej Jitbit, musi być obsługiwana w Jitbit działu pomocy technicznej.  W przypadku pomocy Jitbit aprowizacji to zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do Twojej **pomocy Jitbit** dzierżawy.

1. W menu u góry kliknij **administracji**.
   
    ![Administracja](./media/jitbit-helpdesk-tutorial/ic777681.png "Administracja")

1. Kliknij przycisk **użytkownikom, firmom i uprawnienia**.
   
    ![Użytkownikom, firmom i uprawnienia](./media/jitbit-helpdesk-tutorial/ic777682.png "użytkownikom, firmom i uprawnienia")

1. Kliknij pozycję **Dodaj użytkownika**.
   
    ![Dodawanie użytkownika](./media/jitbit-helpdesk-tutorial/ic777685.png "Dodawanie użytkownika")
   
1. W sekcji Tworzenie wpisz dane konta usługi Azure AD, który chcesz aprowizowania wykonaj następujące czynności:

    ![Tworzenie](./media/jitbit-helpdesk-tutorial/ic777686.png "tworzenie")
   
   a. W **Username** polu tekstowym wpisz **BrittaSimon**, nazwę użytkownika, tak jak w witrynie Azure portal.

   b. W **E-mail** polu tekstowym wpisz adres e-mail użytkownika, takie jak **BrittaSimon@contoso.com**.

   c. W **imię** polu tekstowym wpisz imię użytkownika, takich jak **Britta**.

   d. W **nazwisko** pole tekstowe, wpisz nazwisko użytkownika, takich jak **Simon**.
   
   e. Kliknij pozycję **Utwórz**.

>[!NOTE]
>Aprowizuj konta użytkownika usługi Azure AD, można użyć innych narzędzi do tworzenia konta użytkownika Jitbit działu pomocy technicznej lub interfejsów API dostarczonych przez Jitbit działu pomocy technicznej.
> 
        

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do udzielania dostępu do działu pomocy technicznej Jitbit za pomocą platformy Azure logowania jednokrotnego.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Jitbit działu pomocy technicznej, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **pomocy Jitbit**.

    ![Konfigurowanie logowania jednokrotnego](./media/jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Jitbit działu pomocy technicznej w panelu dostępu, należy uzyskać stronę logowania aplikacji Jitbit działu pomocy technicznej.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/jitbit-helpdesk-tutorial/tutorial_general_01.png
[2]: ./media/jitbit-helpdesk-tutorial/tutorial_general_02.png
[3]: ./media/jitbit-helpdesk-tutorial/tutorial_general_03.png
[4]: ./media/jitbit-helpdesk-tutorial/tutorial_general_04.png

[100]: ./media/jitbit-helpdesk-tutorial/tutorial_general_100.png

[200]: ./media/jitbit-helpdesk-tutorial/tutorial_general_200.png
[201]: ./media/jitbit-helpdesk-tutorial/tutorial_general_201.png
[202]: ./media/jitbit-helpdesk-tutorial/tutorial_general_202.png
[203]: ./media/jitbit-helpdesk-tutorial/tutorial_general_203.png


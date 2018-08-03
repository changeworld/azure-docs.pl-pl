---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą Mozy Enterprise | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Mozy Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: jeedes
ms.openlocfilehash: 7dc2f13979bb0ea919a78b750160119198ee7d6d
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39436611"
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Samouczek: Integracja usługi Azure Active Directory z usługą Mozy Enterprise

W tym samouczku dowiesz się, jak zintegrować Mozy przedsiębiorstwa za pomocą usługi Azure Active Directory (Azure AD).

Integracja przedsiębiorstwa Mozy z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Mozy przedsiębiorstwa
- Użytkowników, aby automatycznie uzyskać zalogowanych do przedsiębiorstwa Mozy (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą Mozy Enterprise, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Mozy Enterprise logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Mozy przedsiębiorstwa z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-mozy-enterprise-from-the-gallery"></a>Dodawanie Mozy przedsiębiorstwa z galerii
Aby skonfigurować integrację Mozy przedsiębiorstwa w usłudze Azure AD, należy dodać Mozy przedsiębiorstwa z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Mozy przedsiębiorstwa z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Mozy Enterprise**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_search.png)

1. W panelu wyników wybierz **Mozy Enterprise**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji można skonfigurować, i test usługi Azure AD logowanie jednokrotne z usługą Mozy Enterprise oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w przedsiębiorstwie Mozy do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w przedsiębiorstwie Mozy musi zostać ustanowione.

W przedsiębiorstwie Mozy przypisze się wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z usługą Mozy Enterprise, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Mozy Enterprise](#creating-a-mozy-enterprise-test-user)**  — aby odpowiednikiem Britta Simon w przedsiębiorstwie Mozy, które jest połączone z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji przedsiębiorstwa Mozy.

**Aby skonfigurować usługi Azure AD logowanie jednokrotne z usługą Mozy Enterprise, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Mozy Enterprise** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_samlbase.png)

1. Na **Mozy Enterprise domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_url.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<tenantname>.Mozyenterprise.com`

    > [!NOTE] 
    > Ta wartość nie jest prawdziwe. Zaktualizuj tę wartość przy użyciu rzeczywisty adres URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej Mozy Enterprise Client](http://support.mozy.com/) aby zyskać tę wartość.

1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/mozy-enterprise-tutorial/tutorial_general_400.png)

1. Na **konfiguracja dla przedsiębiorstw Mozy** , kliknij przycisk **skonfigurować Mozy Enterprise** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego adres URL usługi** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_configure.png) 

1. W oknie przeglądarki internetowej innej Zaloguj się do witryny firmy Mozy przedsiębiorstwa, jako administrator.

1. W **konfiguracji** kliknij **zasady uwierzytelniania**.
   
   ![Zasady uwierzytelniania](./media/mozy-enterprise-tutorial/ic777314.png "zasady uwierzytelniania")

1. Na **zasady uwierzytelniania** sekcji, wykonaj następujące czynności:
   
   ![Zasady uwierzytelniania](./media/mozy-enterprise-tutorial/ic777315.png "zasady uwierzytelniania")
   
   a. Wybierz **usługi katalogowej** jako **dostawcy**.
   
   b. Wybierz **użyć wypychania LDAP**.
   
   c. Kliknij przycisk **uwierzytelnianie SAML** kartę.
   
   d. Wklej **SAML pojedynczego logowania jednokrotnego adres URL usługi**, który skopiowano z witryny Azure portal do **adres URL uwierzytelniania** pole tekstowe.
   
   e. Wklej **identyfikator jednostki SAML**, skopiowanej w witrynie Azure portal do **punktem końcowym SAML** pola tekstowego.
   
   f. Otwórz pobrany certyfikat zakodowany base-64 w programie Notatnik, skopiuj jego zawartość do Schowka, a następnie wklej cały certyfikat do **certyfikat SAML** pola tekstowego.
   
   g. Wybierz **włączyć logowanie Jednokrotne dla grupy Administratorzy logowanie się przy użyciu swoich poświadczeń sieciowych**.
   
   h. Kliknij przycisk **Zapisz zmiany**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/mozy-enterprise-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/mozy-enterprise-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/mozy-enterprise-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/mozy-enterprise-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-mozy-enterprise-test-user"></a>Tworzenie użytkownika testowego Mozy przedsiębiorstwa

Aby umożliwić użytkownikom usługi Azure AD zalogować się do przedsiębiorstwa Mozy, musi być obsługiwana w przedsiębiorstwie Mozy. W przypadku przedsiębiorstw Mozy aprowizacji to zadanie ręczne.

>[!NOTE]
>Można użyć jakichkolwiek innych przedsiębiorstw Mozy użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez przedsiębiorstwa Mozy do aprowizacji kont użytkowników usługi AAD.

**Aby udostępnić konta użytkowników, wykonaj następujące czynności:**

1. Zaloguj się do Twojej **Mozy Enterprise** dzierżawy.

1. Kliknij przycisk **użytkowników**, a następnie kliknij przycisk **Dodaj nowego użytkownika**.
   
   ![Użytkownicy](./media/mozy-enterprise-tutorial/ic777317.png "użytkowników")
   
   >[!NOTE]
   >**Dodaj nowego użytkownika** tylko wtedy, gdy tylko jest wyświetlana opcja **Mozy** jest wybrany jako dostawcę, w obszarze **zasady uwierzytelniania**. Jeśli skonfigurowano uwierzytelnianie SAML, następnie użytkownicy są automatycznie dodawane podczas ich pierwszego logowania za pomocą logowania jednokrotnego na.
    
1. W oknie dialogowym Nowy użytkownik należy wykonać następujące czynności:
   
   ![Dodawanie użytkowników](./media/mozy-enterprise-tutorial/ic777318.png "Dodawanie użytkowników")
   
   a. Z **wybierz grupę** , wybierz grupę na liście.
   
   b. Z **jakiego rodzaju użytkownika** , wybierz typ na liście.
   
   c. W **Username** polu tekstowym wpisz nazwę użytkownika usługi Azure AD.
   
   d. W **E-mail** pole tekstowe, wpisz adres e-mail użytkownika usługi Azure AD.
   
   e. Wybierz **Wyślij wiadomość e-mail z instrukcją użytkownika**.
   
   f. Kliknij przycisk **Dodaj użytkowników**.

     >[!NOTE]
     > Po utworzeniu użytkownika zostanie wysłana wiadomość e-mail do użytkownika usługi Azure AD, która zawiera link do potwierdzenia konta, zanim stanie się aktywny.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Mozy przedsiębiorstwa.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Mozy przedsiębiorstwa, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Mozy Enterprise**.

    ![Konfigurowanie logowania jednokrotnego](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Mozy przedsiębiorstwa, w panelu dostępu, powinna pojawić się aplikacja dla przedsiębiorstw Mozy strony logowania.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/mozy-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/mozy-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/mozy-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/mozy-enterprise-tutorial/tutorial_general_04.png

[100]: ./media/mozy-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/mozy-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/mozy-enterprise-tutorial/tutorial_general_201.png
[202]: ./media/mozy-enterprise-tutorial/tutorial_general_202.png
[203]: ./media/mozy-enterprise-tutorial/tutorial_general_203.png


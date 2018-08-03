---
title: 'Samouczek: Integracja usługi Azure Active Directory z logowania jednokrotnego SAML insygnia | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i logowania jednokrotnego SAML insygnia.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 828c981c-c3dd-4eb2-8699-0f732baa43f6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2017
ms.author: jeedes
ms.openlocfilehash: 776ad8445c1968928a631ae1a401db4c65a6bb6d
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421245"
---
# <a name="tutorial-azure-active-directory-integration-with-insignia-saml-sso"></a>Samouczek: Integracja usługi Azure Active Directory z logowania jednokrotnego SAML insygnia

W tym samouczku dowiesz się, jak zintegrować logowania jednokrotnego SAML insygnia w usłudze Azure Active Directory (Azure AD).

Integracja logowania jednokrotnego SAML insygnia z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do logowania jednokrotnego SAML insygnia.
- Użytkowników, aby automatycznie uzyskać zalogowanych do logowania jednokrotnego SAML insygnia (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu logowania jednokrotnego SAML insygnia, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Logowania jednokrotnego SAML insygnia logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie logowania jednokrotnego SAML insygnia z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-insignia-saml-sso-from-the-gallery"></a>Dodawanie logowania jednokrotnego SAML insygnia z galerii
Aby skonfigurować integrację logowania jednokrotnego SAML insygnia w usłudze Azure AD, należy dodać logowania jednokrotnego SAML insygnia z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać logowania jednokrotnego SAML insygnia z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **logowania jednokrotnego SAML insygnia**, wybierz opcję **logowania jednokrotnego SAML insygnia** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Na liście wyników, logowania jednokrotnego SAML insygnia](./media/insigniasamlsso-tutorial/tutorial_insigniasamlsso_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą logowania jednokrotnego SAML insygnia oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w logowania jednokrotnego SAML insygnia do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w logowania jednokrotnego SAML insygnia musi zostać ustanowione.

W insygnia logowania jednokrotnego SAML, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego SAML insygnia, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego logowania jednokrotnego SAML insygnia](#create-an-insignia-saml-sso-test-user)**  — aby odpowiednikiem Britta Simon w insygnia logowania jednokrotnego SAML, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji logowania jednokrotnego SAML insygnia.

**Aby skonfigurować usługę Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego SAML insygnia, wykonaj następujące czynności:**

1. W witrynie Azure portal na **logowania jednokrotnego SAML insygnia** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/insigniasamlsso-tutorial/tutorial_insigniasamlsso_samlbase.png)

1. Na **insygnia SAML logowania jednokrotnego domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Domena logowania jednokrotnego SAML insygnia i adresy URL pojedynczego logowania jednokrotnego informacji](./media/insigniasamlsso-tutorial/tutorial_insigniasamlsso_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:
    | |
    |--|
    | `https://<customername>.insigniails.com/ils` |
    | `https://<customername>.insigniails.com/` |
    | `https://<customername>.insigniailsusa.com/ ` |

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<customername>.insigniailsusa.com/<uniqueid>`

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania jednokrotnego i identyfikator. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta logowania jednokrotnego SAML insygnia](http://www.insigniasoftware.com/insignia/Techsupport.aspx) do uzyskania tych wartości. 
 

1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/insigniasamlsso-tutorial/tutorial_insigniasamlsso_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/insigniasamlsso-tutorial/tutorial_general_400.png)

1. Na **konfiguracji logowania jednokrotnego SAML insygnia** kliknij **skonfigurować insygnia logowania jednokrotnego SAML** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja logowania jednokrotnego SAML insygnia](./media/insigniasamlsso-tutorial/tutorial_insigniasamlsso_configure.png) 

1. Aby skonfigurować logowanie jednokrotne na **logowania jednokrotnego SAML insygnia** stronie, musisz wysłać pobrany **certyfikat (Base64)**, **adres URL wylogowania i SAML pojedynczego logowania jednokrotnego usługi adresu URL** do [Zespołem pomocy technicznej logowania jednokrotnego SAML insygnia](http://www.insigniasoftware.com/insignia/Techsupport.aspx). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/insigniasamlsso-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/insigniasamlsso-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/insigniasamlsso-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/insigniasamlsso-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-an-insignia-saml-sso-test-user"></a>Tworzenie użytkownika testowego logowania jednokrotnego SAML insygnia

W tej sekcji utworzysz użytkownika o nazwie Britta Simon insygnia biblioteki systemu. Praca z [zespołem pomocy technicznej systemu biblioteki insygnia](http://www.insigniasoftware.com/insignia/Techsupport.aspx) Aby dodać użytkowników na platformie insygnia biblioteki systemu.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do logowania jednokrotnego SAML insygnia.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon do logowania jednokrotnego SAML insygnia, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **logowania jednokrotnego SAML insygnia**.

    ![Link logowania jednokrotnego SAML insygnia na liście aplikacji](./media/insigniasamlsso-tutorial/tutorial_insigniasamlsso_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka logowania jednokrotnego SAML insygnia w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do logowania jednokrotnego SAML insygnia aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/insigniasamlsso-tutorial/tutorial_general_01.png
[2]: ./media/insigniasamlsso-tutorial/tutorial_general_02.png
[3]: ./media/insigniasamlsso-tutorial/tutorial_general_03.png
[4]: ./media/insigniasamlsso-tutorial/tutorial_general_04.png

[100]: ./media/insigniasamlsso-tutorial/tutorial_general_100.png

[200]: ./media/insigniasamlsso-tutorial/tutorial_general_200.png
[201]: ./media/insigniasamlsso-tutorial/tutorial_general_201.png
[202]: ./media/insigniasamlsso-tutorial/tutorial_general_202.png
[203]: ./media/insigniasamlsso-tutorial/tutorial_general_203.png


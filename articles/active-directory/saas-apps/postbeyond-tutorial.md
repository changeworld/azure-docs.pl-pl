---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą PostBeyond | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i PostBeyond.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 09992f08-ec50-4472-997f-ccbe719039e8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0306b1e73ad6fbd123cb4f835068e92a49ea2189
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56177516"
---
# <a name="tutorial-azure-active-directory-integration-with-postbeyond"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą PostBeyond

W tym samouczku dowiesz się, jak zintegrować PostBeyond w usłudze Azure Active Directory (Azure AD).

Integrowanie PostBeyond z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do PostBeyond
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do PostBeyond (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą PostBeyond, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- PostBeyond logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie PostBeyond z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-postbeyond-from-the-gallery"></a>Dodawanie PostBeyond z galerii
Aby skonfigurować integrację PostBeyond w usłudze Azure AD, należy dodać PostBeyond z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać PostBeyond z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **PostBeyond**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/postbeyond-tutorial/tutorial_postbeyond_search.png)

1. W panelu wyników wybierz **PostBeyond**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/postbeyond-tutorial/tutorial_postbeyond_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą PostBeyond w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w PostBeyond do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w PostBeyond musi można ustanowić.

W PostBeyond, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą PostBeyond, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego PostBeyond](#creating-a-postbeyond-test-user)**  — aby odpowiednikiem Britta Simon w PostBeyond połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji PostBeyond.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z PostBeyond, wykonaj następujące czynności:**

1. W witrynie Azure portal na **PostBeyond** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/postbeyond-tutorial/tutorial_postbeyond_samlbase.png)

1. Na **PostBeyond domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/postbeyond-tutorial/tutorial_postbeyond_url.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.postbeyond.com`

    b. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.postbeyond.com`

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL i identyfikatora logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta PostBeyond](mailto:sso@postbeyond.com) do uzyskania tych wartości. 
 
1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/postbeyond-tutorial/tutorial_postbeyond_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/postbeyond-tutorial/tutorial_general_400.png)

1. Na **konfiguracji PostBeyond** , kliknij przycisk **skonfigurować PostBeyond** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/postbeyond-tutorial/tutorial_postbeyond_configure.png) 

1. Aby skonfigurować logowanie jednokrotne na **PostBeyond** stronie, musisz wysłać pobrany **Certificate(Base64)**, **identyfikator jednostki SAML**, **SAML logowania jednokrotnego Adres URL usługi** i **adres URL wylogowania** do [zespołem pomocy technicznej PostBeyond](mailto:sso@postbeyond.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/postbeyond-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/postbeyond-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/postbeyond-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/postbeyond-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-postbeyond-test-user"></a>Tworzenie użytkownika testowego PostBeyond

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w PostBeyond. Jeśli nie wiesz, jak dodać Britta Simon w PostBeyond, skontaktuj się z [zespołem pomocy technicznej PostBeyond](mailto:sso@postbeyond.com) do dodawania użytkownika testowego i włączyć logowanie Jednokrotne.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do PostBeyond.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon PostBeyond, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **PostBeyond**.

    ![Konfigurowanie logowania jednokrotnego](./media/postbeyond-tutorial/tutorial_postbeyond_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest test konfiguracji logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po kliknięciu kafelka PostBeyond w panelu dostępu, powinieneś PostBeyond stronie logowania. Kliknij pozycję **Zaloguj się przy użyciu usługi Office 365**, wprowadź swoje poświadczenia usługi Azure AD. Następnie użytkownik powinien być zalogowany do PostBeyond.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/postbeyond-tutorial/tutorial_general_01.png
[2]: ./media/postbeyond-tutorial/tutorial_general_02.png
[3]: ./media/postbeyond-tutorial/tutorial_general_03.png
[4]: ./media/postbeyond-tutorial/tutorial_general_04.png

[100]: ./media/postbeyond-tutorial/tutorial_general_100.png

[200]: ./media/postbeyond-tutorial/tutorial_general_200.png
[201]: ./media/postbeyond-tutorial/tutorial_general_201.png
[202]: ./media/postbeyond-tutorial/tutorial_general_202.png
[203]: ./media/postbeyond-tutorial/tutorial_general_203.png


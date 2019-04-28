---
title: 'Samouczek: Integracja usługi Azure Active Directory przy użyciu przewodników interaktywnych Yonyx | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i przewodników interaktywnych Yonyx.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 07db4e01-319b-4cb6-9b93-4577bffd3cbc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fce7653770d0fb3134f266137e167125b9161865
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60863115"
---
# <a name="tutorial-azure-active-directory-integration-with-yonyx-interactive-guides"></a>Samouczek: Integracja usługi Azure Active Directory przy użyciu przewodników interaktywnych Yonyx

W tym samouczku dowiesz się, jak zintegrować przewodników interaktywnych Yonyx za pomocą usługi Azure Active Directory (Azure AD).

Integrowanie przewodników interaktywnych Yonyx z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do przewodników interaktywnych Yonyx
- Użytkowników, aby automatycznie uzyskać zalogowanych do przewodników interaktywnych Yonyx (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu przewodników interaktywnych Yonyx, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Przewodniki interaktywne Yonyx logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie przewodników interaktywnych Yonyx z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-yonyx-interactive-guides-from-the-gallery"></a>Dodawanie przewodników interaktywnych Yonyx z galerii
Aby skonfigurować integrację Yonyx przewodniki interakcyjne w usłudze Azure AD, należy dodać przewodników interaktywnych Yonyx z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać przewodników interaktywnych Yonyx z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **przewodników interaktywnych Yonyx**, wybierz pozycję **przewodników interaktywnych Yonyx** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Przewodniki interaktywne Yonyx na liście wyników](./media/yonyx-tutorial/tutorial_yonyxinteractiveguides_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Yonyx przewodników interaktywnych oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w przewodnikach interaktywne Yonyx do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w przewodnikach interaktywne Yonyx musi można ustanowić.

W przewodnikach interaktywne Yonyx, przypisz wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu przewodników interaktywnych Yonyx, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **[Tworzenie użytkownika testowego przewodników interaktywnych Yonyx](#create-a-yonyx-interactive-guides-test-user)**  — aby odpowiednikiem Britta Simon w Yonyx przewodników interaktywnych, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji Włączanie usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Yonyx przewodników interaktywnych.

**Aby skonfigurować usługi Azure AD logowania jednokrotnego przy użyciu przewodników interaktywnych Yonyx, wykonaj następujące czynności:**

1. W witrynie Azure portal na **przewodników interaktywnych Yonyx** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/yonyx-tutorial/tutorial_yonyxinteractiveguides_samlbase.png)

1. Na **Yonyx interaktywne przewodniki domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Yonyx interaktywne przewodniki domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/yonyx-tutorial/tutorial_yonyxinteractiveguides_url.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<company name>.yonyx.com/y/conversation/?id=<guid number>`

    b. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<company name>.yonyx.com`

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL i identyfikatora logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej Yonyx interaktywne przewodników klienta](mailto:support@yonyx.com) do uzyskania tych wartości. 
 
1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link do pobierania certyfikatu](./media/yonyx-tutorial/tutorial_yonyxinteractiveguides_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/yonyx-tutorial/tutorial_general_400.png)

1. Na **Yonyx interaktywne przewodniki konfiguracji** , kliknij przycisk **skonfigurować przewodników interaktywnych Yonyx** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Yonyx interaktywne przewodniki konfiguracji](./media/yonyx-tutorial/tutorial_yonyxinteractiveguides_configure.png) 

1. Aby skonfigurować logowanie jednokrotne na **przewodników interaktywnych Yonyx** stronie, musisz wysłać pobrany **Certificate(Base64)**, **adres URL wylogowania**, **jednego języka SAML Adres URL logowania jednokrotnego usługi** **identyfikator jednostki SAML** do [zespołu pomocy technicznej przewodników interaktywnych Yonyx](mailto:support@yonyx.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

  ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](./media/yonyx-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](./media/yonyx-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Przycisk Dodaj](./media/yonyx-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Okno dialogowe Użytkownik](./media/yonyx-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-yonyx-interactive-guides-test-user"></a>Tworzenie użytkownika testowego przewodników interaktywnych Yonyx

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon Yonyx interaktywne przewodników. Przewodniki interaktywne Yonyx obsługę just-in-time, który jest domyślnie włączona.

W tej sekcji nie musisz niczego robić. Nowy użytkownik jest tworzony podczas próby dostępu Yonyx przewodników interaktywnych, jeśli go jeszcze nie istnieje.

> [!NOTE]
> Jeśli potrzebujesz ręcznie utworzyć użytkownika, musisz skontaktować się z zespołem pomocy technicznej przewodników interaktywnych Yonyx za pośrednictwem <mailto:support@yonyx.com>. 

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do przewodników interaktywnych Yonyx.

![Przypisanie roli użytkownika][200]

**Aby przypisać Britta Simon Yonyx przewodników interaktywnych, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **przewodników interaktywnych Yonyx**.

    ![Link przewodników interaktywnych Yonyx na liście aplikacji](./media/yonyx-tutorial/tutorial_yonyxinteractiveguides_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka przewodników interaktywnych Yonyx w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do Yonyx przewodników interaktywnych aplikacji.

Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/yonyx-tutorial/tutorial_general_01.png
[2]: ./media/yonyx-tutorial/tutorial_general_02.png
[3]: ./media/yonyx-tutorial/tutorial_general_03.png
[4]: ./media/yonyx-tutorial/tutorial_general_04.png

[100]: ./media/yonyx-tutorial/tutorial_general_100.png

[200]: ./media/yonyx-tutorial/tutorial_general_200.png
[201]: ./media/yonyx-tutorial/tutorial_general_201.png
[202]: ./media/yonyx-tutorial/tutorial_general_202.png
[203]: ./media/yonyx-tutorial/tutorial_general_203.png


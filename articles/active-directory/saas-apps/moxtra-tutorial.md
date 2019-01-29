---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Moxtra | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Moxtra.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 2aed2d4b-1dcd-4839-8fed-9419d107c61c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.openlocfilehash: e0674c0bd3e5244b76d35e05057aee3b75249703
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55197113"
---
# <a name="tutorial-azure-active-directory-integration-with-moxtra"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Moxtra

W tym samouczku dowiesz się, jak zintegrować Moxtra w usłudze Azure Active Directory (Azure AD).

Integrowanie Moxtra z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Moxtra
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Moxtra (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Moxtra, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Moxtra logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Moxtra z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-moxtra-from-the-gallery"></a>Dodawanie Moxtra z galerii
Aby skonfigurować integrację Moxtra w usłudze Azure AD, należy dodać Moxtra z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Moxtra z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Moxtra**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/moxtra-tutorial/tutorial_moxtra_search.png)

1. W panelu wyników wybierz **Moxtra**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/moxtra-tutorial/tutorial_moxtra_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Moxtra w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Moxtra do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Moxtra musi można ustanowić.

W Moxtra, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Moxtra, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Moxtra](#creating-a-moxtra-test-user)**  — aby odpowiednikiem Britta Simon w Moxtra połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Moxtra.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Moxtra, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Moxtra** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/moxtra-tutorial/tutorial_moxtra_samlbase.png)

1. Na **Moxtra domena i adresy URL** sekcji, wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/moxtra-tutorial/tutorial_moxtra_url.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL jako: `https://www.moxtra.com/service/#login`

1. Aplikacja Moxtra oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Możesz zarządzać wartości te atrybuty z "**atrybutów użytkownika**" sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład w przypadku tej konfiguracji. 

    ![Konfigurowanie logowania jednokrotnego](./media/moxtra-tutorial/tutorial_moxtra_attributes.png)
    
1. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | ------------------- | -------------------- |    
    | firstname | user.givenname |
    | lastname | user.surname |
    | idpid    | < Identyfikator jednostki SAML > 

    > [!Note]
    > Wartość **idpid** atrybut nie jest prawdziwe. Można uzyskać wartości rzeczywiste z **krótki** sekcji **konfiguracji Moxtra**.
    
    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](./media/moxtra-tutorial/tutorial_attribute_04.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    ![Konfigurowanie logowania jednokrotnego](./media/moxtra-tutorial/tutorial_attribute_05.png)

    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.

    d. Kliknij przycisk **OK**.
    
1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/moxtra-tutorial/tutorial_moxtra_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/moxtra-tutorial/tutorial_general_400.png)

1. Na **konfiguracji Moxtra** , kliknij przycisk **skonfigurować Moxtra** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego adres URL usługi** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/moxtra-tutorial/tutorial_moxtra_configure.png) 

1. W innym oknie przeglądarki Zaloguj się do witryny firmy Moxtra jako administrator.

1. Na pasku narzędzi po lewej stronie kliknij pozycję **konsoli administracyjnej > SAML logowania jednokrotnego**, a następnie kliknij przycisk **New**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/moxtra-tutorial/tutorial_moxtra_06.png) 

1. Na **SAML** strony, wykonaj następujące czynności:
   
    ![Konfigurowanie logowania jednokrotnego](./media/moxtra-tutorial/tutorial_moxtra_08.png)   
 
    a. W **nazwa** polu tekstowym wpisz nazwę dla danej konfiguracji (np.: *SAML*). 
  
    b. W **identyfikator jednostki tożsamości** pola tekstowego, Wklej wartość **identyfikator jednostki SAML** skopiowanej w witrynie Azure portal. 
 
    c. W **adres URL logowania** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowanej w witrynie Azure portal. 
 
    d. W **AuthnContextClassRef** polu tekstowym wpisz **urn: oasis: nazwy: tc: SAML:2.0:ac:classes:Password**. 
 
    e. W **Format identyfikatora NameID** polu tekstowym wpisz **urn: oasis: nazwy: tc: SAML:1.1:nameid — format: emailAddress**. 
 
    f. Otwórz certyfikat, który został pobrany z witryny Azure portal w programie Notatnik, skopiuj zawartość, a następnie wklej go do **certyfikatu** pola tekstowego.    
 
    g. W polu tekstowym domeny poczty e-mail protokołu SAML wpisz domeny poczty e-mail protokołu SAML.    
  
    >[!NOTE]
    >Aby zobaczyć kroki, aby zweryfikować domenę, kliknij przycisk "**i**" poniżej.

    h. Kliknij przycisk **Update** (Aktualizuj).

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/moxtra-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/moxtra-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/moxtra-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/moxtra-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-moxtra-test-user"></a>Tworzenie użytkownika testowego Moxtra

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w Moxtra.

**Aby utworzyć użytkownika o nazwie Britta Simon w Moxtra, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy Moxtra jako administrator.

1. Na pasku narzędzi po lewej stronie kliknij pozycję **konsoli administracyjnej > Zarządzanie użytkownikami**, a następnie **Dodaj użytkownika**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/moxtra-tutorial/tutorial_moxtra_10.png) 

1. Na **Dodaj użytkownika** okno dialogowe, należy wykonać następujące czynności:
  
    a. W **imię** polu tekstowym wpisz **Britta**.
  
    b. W **nazwisko** polu tekstowym wpisz **Simon**.
  
    c. W **E-mail** pole tekstowe, wpisz adres e-mail Britty takie same jak w witrynie Azure portal.
  
    d. W **dzielenia** polu tekstowym wpisz **Dev**.
  
    e. W **działu** polu tekstowym wpisz **IT**.
  
    f. Wybierz **administratora**.
  
    g. Kliknij pozycję **Add** (Dodaj).

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Moxtra.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Moxtra, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Moxtra**.

    ![Konfigurowanie logowania jednokrotnego](./media/moxtra-tutorial/tutorial_moxtra_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Moxtra w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Moxtra.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/moxtra-tutorial/tutorial_general_01.png
[2]: ./media/moxtra-tutorial/tutorial_general_02.png
[3]: ./media/moxtra-tutorial/tutorial_general_03.png
[4]: ./media/moxtra-tutorial/tutorial_general_04.png

[100]: ./media/moxtra-tutorial/tutorial_general_100.png

[200]: ./media/moxtra-tutorial/tutorial_general_200.png
[201]: ./media/moxtra-tutorial/tutorial_general_201.png
[202]: ./media/moxtra-tutorial/tutorial_general_202.png
[203]: ./media/moxtra-tutorial/tutorial_general_203.png


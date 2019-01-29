---
title: 'Samouczek: Integracja usługi Azure Active Directory przy użyciu przekazywania obrazów | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i przekazywania obrazów.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 65bb5990-07ef-4244-9f41-cd28fc2cb5a2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 4bc522915b302c7875ca77213a39fce52f0736a1
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55162246"
---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>Samouczek: Integracja usługi Azure Active Directory przy użyciu przekazywania obrazów

W tym samouczku dowiesz się, jak zintegrować przekazywania obrazu z usługi Azure Active Directory (Azure AD).

Integrowanie przekazywania obrazów z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do przekazywania obrazu
- Użytkowników, aby automatycznie uzyskać zalogowanych do przekazywania obrazu (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu przekazywania obrazów, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Obraz przekazywania logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie przekazywania obrazów z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-image-relay-from-the-gallery"></a>Dodawanie przekazywania obrazów z galerii
Aby skonfigurować integrację przekazywania obrazu do usługi Azure AD, należy dodać przekazywania obrazów z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać przekazywania obrazów z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **przekazywania obrazów**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/imagerelay-tutorial/tutorial_imagerelay_search.png)

1. W panelu wyników wybierz **przekazywania obrazów**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/imagerelay-tutorial/tutorial_imagerelay_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i testowanie usługi Azure AD logowania jednokrotnego dzięki usłudze Relay obraz w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w przekazywania obrazu do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w przekazywania obrazu musi można ustanowić.

W przekazywania obrazu, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu przekazywania obrazu, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego przekazywania obrazów](#creating-an-image-relay-test-user)**  — aby odpowiednikiem Britta Simon w przekaźnik obrazu, który jest połączony z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji przekazywania obrazów.

**Aby skonfigurować usługi Azure AD logowania jednokrotnego przy użyciu przekazywania obrazów, wykonaj następujące czynności:**

1. W witrynie Azure portal na **przekazywania obrazów** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/imagerelay-tutorial/tutorial_imagerelay_samlbase.png)

1. Na **obraz przekazywania domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/imagerelay-tutorial/tutorial_imagerelay_url.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.imagerelay.com/`

    b. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.imagerelay.com/sso/metadata`

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL i identyfikatora logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienckich przekaźnika obraz](http://support.imagerelay.com/) do uzyskania tych wartości. 
 


1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/imagerelay-tutorial/tutorial_imagerelay_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/imagerelay-tutorial/tutorial_general_400.png)

1. Na **Konfiguracja przekazywania obrazów** , kliknij przycisk **Konfigurowanie przekazywania obrazów** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adresu URL usługi wylogowania i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/imagerelay-tutorial/tutorial_imagerelay_configure.png) 

1. W innym oknie przeglądarki należy zalogować się jako administrator do witryny firmy przekazywania obrazów.

1. Na pasku narzędzi u góry kliknij **użytkowników i uprawnień** obciążenia.
   
    ![Konfigurowanie logowania jednokrotnego](./media/imagerelay-tutorial/tutorial_imagerelay_06.png) 

1. Kliknij przycisk **Utwórz nowe uprawnienie**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/imagerelay-tutorial/tutorial_imagerelay_08.png)

1. W **pojedynczy znak na ustawienia** obciążenia, wybierz opcję **tej grupie mogą tylko logowanie za pomocą logowania jednokrotnego** pole wyboru, a następnie kliknij przycisk **Zapisz**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/imagerelay-tutorial/tutorial_imagerelay_09.png) 

1. Przejdź do **ustawienia konta**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/imagerelay-tutorial/tutorial_imagerelay_10.png) 

1. Przejdź do **pojedynczy znak na ustawienia** obciążenia.
    
     ![Konfigurowanie logowania jednokrotnego](./media/imagerelay-tutorial/tutorial_imagerelay_11.png)

1. Na **ustawienia języka SAML** okno dialogowe, należy wykonać następujące czynności:
    
    ![Konfigurowanie logowania jednokrotnego](./media/imagerelay-tutorial/tutorial_imagerelay_12.png)
    
    a. W **adres URL logowania** pola tekstowego, Wklej wartość **pojedynczy znak na adres URL usługi** skopiowanej w witrynie Azure portal.

    b. W **adres URL wylogowania** pola tekstowego, Wklej wartość **adresu URL usługi wylogowania jednokrotnego** skopiowanej w witrynie Azure portal.

    c. Jako **Format identyfikatora nazwy**, wybierz opcję **urn: oasis: nazwy: tc: SAML:1.1:nameid — format: emailAddress**.

    d. Jako **powiązanie opcji w przypadku żądań od dostawcy usług (przekazywania obrazów)**, wybierz opcję **powiązanie WPIS**.

    e. W obszarze **x.509 certyfikatu**, kliknij przycisk **Aktualizuj certyfikat**.

    ![Konfigurowanie logowania jednokrotnego](./media/imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. Otwórz pobranego certyfikatu w programie Notatnik, skopiuj zawartość i wklej go w polu tekstowym certyfikatu x.509.

    ![Konfigurowanie logowania jednokrotnego](./media/imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. W **aprowizacji użytkowników just in Time** zaznacz **Włącz Aprowizację użytkowników just in Time**.

    ![Konfigurowanie logowania jednokrotnego](./media/imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Wybierz grupy uprawnień (na przykład **logowania jednokrotnego podstawowe**) który będzie mógł zalogować się tylko za pomocą logowania jednokrotnego.

    ![Konfigurowanie logowania jednokrotnego](./media/imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/imagerelay-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/imagerelay-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/imagerelay-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/imagerelay-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-an-image-relay-test-user"></a>Tworzenie użytkownika testowego przekazywania obrazów

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w przekazywania obrazów.

**Aby utworzyć użytkownika o nazwie Britta Simon w przekazywania obrazów, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy przekazywania obrazu jako administrator.

1. Przejdź do **użytkowników i uprawnień** i wybierz **Create User logowania jednokrotnego**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/imagerelay-tutorial/tutorial_imagerelay_21.png) 

1. Wprowadź **E-mail**, **imię**, **nazwisko**, i **firmy** użytkownika, o których chcesz aprowizować, a następnie wybierz grupy uprawnień (w przypadku przykład: podstawowe logowania jednokrotnego) czyli grupy, która może zalogować się tylko za pomocą logowania jednokrotnego.
   
    ![Konfigurowanie logowania jednokrotnego](./media/imagerelay-tutorial/tutorial_imagerelay_22.png) 

1. Kliknij pozycję **Utwórz**.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do przekazywania obrazów.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon do przekazywania obrazów, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **przekazywania obrazów**.

    ![Konfigurowanie logowania jednokrotnego](./media/imagerelay-tutorial/tutorial_imagerelay_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.    

Po kliknięciu kafelka przekazywania obrazów w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do przekazywania obrazu aplikacji.


## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/imagerelay-tutorial/tutorial_general_01.png
[2]: ./media/imagerelay-tutorial/tutorial_general_02.png
[3]: ./media/imagerelay-tutorial/tutorial_general_03.png
[4]: ./media/imagerelay-tutorial/tutorial_general_04.png


[100]: ./media/imagerelay-tutorial/tutorial_general_100.png

[200]: ./media/imagerelay-tutorial/tutorial_general_200.png
[201]: ./media/imagerelay-tutorial/tutorial_general_201.png
[202]: ./media/imagerelay-tutorial/tutorial_general_202.png
[203]: ./media/imagerelay-tutorial/tutorial_general_203.png


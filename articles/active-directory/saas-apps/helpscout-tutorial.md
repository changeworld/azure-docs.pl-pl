---
title: 'Samouczek: Integracja usługi Azure Active Directory z pomocy programu Scout | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i ułatwić programu Scout.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f999ff396a5573e6928fd8a25e1bb634f3615c0
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56179286"
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Samouczek: Integracja usługi Azure Active Directory z pomocy programu Scout

W tym samouczku dowiesz się, jak zintegrować Pomoc programu Scout za pomocą usługi Azure Active Directory (Azure AD).

Integrowanie pomocy programu Scout za pomocą usługi Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do pomocy programu Scout.
- Użytkowników, aby automatycznie uzyskać zalogowanych do pomocy programu Scout (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z pomocy programu Scout, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Pomoc programu Scout logowania jednokrotnego włączonych subskrypcji

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie pomocy programu Scout za pomocą galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-help-scout-from-the-gallery"></a>Dodawanie pomocy programu Scout za pomocą galerii
Aby skonfigurować integrację programu Scout pomocy w usłudze Azure AD, należy dodać Scout pomocy z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Scout pomocy z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **Pomoc programu Scout**, wybierz opcję **Pomoc programu Scout** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Pomoc programu Scout na liście wyników](./media/helpscout-tutorial/tutorial_helpscout_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą programu Scout pomocy, oparte na użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Pomocy programu Scout do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w Pomocy programu Scout musi zostać ustanowione.

Pomoc programu Scout używa adresów e-mail dotyczące logowania się, a więc do ustanawiania relacji łączy, takie same **adres e-mail** jako **nazwa_użytkownika** w usłudze Azure AD.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z pomocy programu Scout, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **[Tworzenie użytkownika testowego Pomoc programu Scout](#create-a-help-scout-test-user)**  — aby odpowiednikiem Britta Simon w Pomocy programu Scout, który jest połączony z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Pomoc programu Scout.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z pomocy programu Scout, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Pomoc programu Scout** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/helpscout-tutorial/tutorial_helpscout_samlbase.png)

1. Na **Pomoc programu Scout domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Informacje pomocy technicznej programu Scout domena i adresy URL pojedynczego logowania jednokrotnego](./media/helpscout-tutorial/tutorial_helpscout_url.png)

    a. **Identyfikator** jest **"Audience identyfikatora URI (identyfikator jednostki usługi dostawcy)"** z pomocy programu Scout zaczyna się od `urn:`

    b. **Adres URL odpowiedzi** jest **"Po wstecz adres URL (adres URL usługi Assertion klienta)"** z pomocy programu Scout zaczyna się od `https://` 

    > [!NOTE] 
    > Wartości tych adresów URL są tylko na potrzeby demonstracyjne. Musisz zaktualizować te wartości z rzeczywistych adres URL odpowiedzi i identyfikator. Pobierz te wartości z **logowania jednokrotnego** kartę w sekcji Uwierzytelnianie zostało wyjaśnione w dalszej części tego samouczka.

1. Jeśli chcesz skonfigurować aplikację w **SP** inicjowane trybu wyboru **Pokaż zaawansowane ustawienia adresu URL** i wykonaj następujące kroki:

    ![Informacje pomocy technicznej programu Scout domena i adresy URL pojedynczego logowania jednokrotnego](./media/helpscout-tutorial/tutorial_helpscout_url1.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL jako: `https://secure.helpscout.net/members/login/`
     
1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link do pobierania certyfikatu](./media/helpscout-tutorial/tutorial_helpscout_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/helpscout-tutorial/tutorial_general_400.png)


1. Na **pomocy Konfiguracja programu Scout** , kliknij przycisk **Konfigurowanie pomocy programu Scout** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji**.

    ![Konfigurowanie logowania jednokrotnego](./media/helpscout-tutorial/config.png) 

1. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy Pomoc programu Scout.

1. Po zalogowaniu kliknij **"Manage"** z górnego menu, a następnie wybierz pozycję **"Firma"** z menu rozwijanego.

    ![Konfigurowanie logowania jednokrotnego](./media/helpscout-tutorial/settings1.png) 
 
1. Wybierz **"Uwierzytelnianie"** z menu po lewej stronie. 

    ![Konfigurowanie logowania jednokrotnego](./media/helpscout-tutorial/settings2.png) 

1. To spowoduje przejście do sekcji Ustawienia protokołu SAML i wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/helpscout-tutorial/settings3.png) 
 
    a. Kopiuj **wstecz po wprowadzeniu adresu URL (adres URL usługi Assertion konsumenta)** wartość i Wklej wartość w **adres URL odpowiedzi** polu w witrynie Azure portal pod Scout pomocy **domena i adresy URL** sekcji.
    
    b. Kopiuj **identyfikator URI odbiorców (identyfikator jednostki usługi dostawcy)** wartość i Wklej wartość w **identyfikator** polu w witrynie Azure portal pod Scout pomocy **domena i adresy URL** sekcji.

1. Przełącz **Włącz SAML** na i wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/helpscout-tutorial/settings4.png) 
 
    a. W **pojedynczy adres URL logowania** pola tekstowego, Wklej wartość **pojedynczy znak na adres URL usługi**, które zostały skopiowane z witryny Azure portal.
    
    b. Kliknij przycisk **Przekaż certyfikat** do przekazania **Certificate(Base64)** pobranego z witryny Azure portal.

    c. Wprowadź w organizacji poczty e-mail domeny np. - `contoso.com` w **domen poczty E-mail** pola tekstowego. Wiele domen można oddzielić przecinkami. Dowolnym Pomoc programu Scout użytkownik lub Administrator, który wprowadzi tej konkretnej domeny na [strona logowania Pomoc programu Scout](https://secure.helpscout.net/members/login/) będą kierowane do dostawcy tożsamości do uwierzytelniania przy użyciu swoich poświadczeń.

    d. Ponadto można przełączać się **logowania jednokrotnego SAML życie** Jeśli użytkownicy mają tylko zalogować się do pomocy programu Scout za pośrednictwem za pośrednictwem tej metody. Jeśli chcesz nadal pozostaw tę opcję, aby zalogować się przy użyciu swoich poświadczeń w Pomocy programu Scout, możesz pozostawić wyłączony. Nawet jeśli ta opcja jest włączona, właściciel konta zawsze będzie można zalogować się do pomocy programu Scout za pomocą hasła do konta.

    e. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/helpscout-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/helpscout-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/helpscout-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/helpscout-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-help-scout-test-user"></a>Tworzenie użytkownika testowego Pomoc programu Scout

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w Pomocy programu Scout. Pomoc programu Scout obsługę just-in-time, który jest domyślnie włączona.

W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w Pomocy programu Scout, nowy katalog jest tworzony podczas próby uzyskania dostępu pomocy programu Scout.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon korzystać z platformy Azure logowania jednokrotnego przez udzielenie dostępu pomocy programu Scout.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon do pomocy programu Scout, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Pomoc programu Scout**.

    ![Łącze Pomoc programu Scout na liście aplikacji](./media/helpscout-tutorial/tutorial_helpscout_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Scout pomocy w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do aplikacji Pomoc programu Scout.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/helpscout-tutorial/tutorial_general_01.png
[2]: ./media/helpscout-tutorial/tutorial_general_02.png
[3]: ./media/helpscout-tutorial/tutorial_general_03.png
[4]: ./media/helpscout-tutorial/tutorial_general_04.png

[100]: ./media/helpscout-tutorial/tutorial_general_100.png

[200]: ./media/helpscout-tutorial/tutorial_general_200.png
[201]: ./media/helpscout-tutorial/tutorial_general_201.png
[202]: ./media/helpscout-tutorial/tutorial_general_202.png
[203]: ./media/helpscout-tutorial/tutorial_general_203.png


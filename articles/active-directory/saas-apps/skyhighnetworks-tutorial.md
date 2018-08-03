---
title: 'Samouczek: Integracja usługi Azure Active Directory przy użyciu sieci Skyhigh | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Skyhigh sieci.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 48d6ddd1-4d3e-4019-8234-5e5212684d9c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: jeedes
ms.openlocfilehash: 40237946adf0e9cf30367fd0464a6c32572c3aaf
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39423729"
---
# <a name="tutorial-azure-active-directory-integration-with-skyhigh-networks"></a>Samouczek: Integracja usługi Azure Active Directory przy użyciu sieci Skyhigh

W tym samouczku dowiesz się, jak zintegrować Skyhigh sieci z usługą Azure Active Directory (Azure AD).

Integracja sieci Skyhigh z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do sieci Skyhigh.
- Użytkowników, aby automatycznie uzyskać zalogowanych do sieci Skyhigh (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu sieci Skyhigh, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Sieci Skyhigh logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie sieci Skyhigh z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-skyhigh-networks-from-the-gallery"></a>Dodawanie sieci Skyhigh z galerii
Aby skonfigurować integrację Skyhigh sieci w usłudze Azure AD, musisz dodać Skyhigh sieci z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Skyhigh sieci z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **Skyhigh sieci**, wybierz opcję **sieci Skyhigh** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Skyhigh sieci na liście wyników](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą sieci Skyhigh w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w sieciach Skyhigh do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w sieciach Skyhigh musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu Skyhigh sieci, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego sieci Skyhigh](#create-a-skyhigh-networks-test-user)**  — aby odpowiednikiem Britta Simon w sieciach Skyhigh, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Skyhigh sieci.

**Aby skonfigurować usługi Azure AD logowania jednokrotnego przy użyciu sieci Skyhigh, wykonaj następujące czynności:**

1. W witrynie Azure portal na **sieci Skyhigh** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_samlbase.png)

1. Na **Skyhigh sieci domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Adresy URL i domen sieci Skyhigh pojedynczego logowania jednokrotnego informacji](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_url.png)

    a. W **identyfikator jednostki** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<ENV>.myshn.net/shndash/saml/Azure_SSO`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<ENV>.myshn.net/shndash/response/saml-postlogin`

1. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Adresy URL i domen sieci Skyhigh pojedynczego logowania jednokrotnego informacji](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_url1.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<ENV>.myshn.net/shndash/saml/Azure_SSO`
     
    > [!NOTE] 
    > Te wartości są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta sieci Skyhigh](mailto:support@skyhighnetworks.com) do uzyskania tych wartości. 

1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/skyhighnetworks-tutorial/tutorial_general_400.png)
    
1. Na **konfiguracji sieci Skyhigh** , kliknij przycisk **Konfigurowanie sieci Skyhigh** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja sieci Skyhigh](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_configure.png) 

1. Aby skonfigurować logowanie jednokrotne na **sieci Skyhigh** stronie, musisz wysłać pobrany **certyfikat (Base64), adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** do [ Zespołu pomocy technicznej sieci Skyhigh](mailto:support@skyhighnetworks.com). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/skyhighnetworks-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/skyhighnetworks-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/skyhighnetworks-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/skyhighnetworks-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-skyhigh-networks-test-user"></a>Tworzenie użytkownika testowego Skyhigh sieci

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w sieciach Skyhigh. Praca z [zespołu pomocy technicznej sieci Skyhigh](mailto:support@skyhighnetworks.com) Aby dodać użytkowników na platformie Skyhigh sieci. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do udzielania dostępu do sieci Skyhigh za pomocą platformy Azure logowania jednokrotnego.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Skyhigh sieci, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **sieci Skyhigh**.

    ![Link sieci Skyhigh na liście aplikacji](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Skyhigh sieci w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Skyhigh sieci.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/skyhighnetworks-tutorial/tutorial_general_01.png
[2]: ./media/skyhighnetworks-tutorial/tutorial_general_02.png
[3]: ./media/skyhighnetworks-tutorial/tutorial_general_03.png
[4]: ./media/skyhighnetworks-tutorial/tutorial_general_04.png

[100]: ./media/skyhighnetworks-tutorial/tutorial_general_100.png

[200]: ./media/skyhighnetworks-tutorial/tutorial_general_200.png
[201]: ./media/skyhighnetworks-tutorial/tutorial_general_201.png
[202]: ./media/skyhighnetworks-tutorial/tutorial_general_202.png
[203]: ./media/skyhighnetworks-tutorial/tutorial_general_203.png


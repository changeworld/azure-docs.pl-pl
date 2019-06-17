---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą klucza tajnego Server (lokalnie) | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i klucz tajny Server (lokalnie).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: be4ba84a-275d-4f71-afce-cb064edc713f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9167a5ed72e6fec2ca03cc97d1d41dd6cd4aaba6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62104579"
---
# <a name="tutorial-azure-active-directory-integration-with-secret-server-on-premises"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą klucza tajnego Server (lokalnie)

W tym samouczku dowiesz się, jak zintegrować wpis tajny serwera (lokalnie) z usługą Azure Active Directory (Azure AD).

Integrowanie wpis tajny serwera (lokalnie) z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do klucza tajnego Server (lokalnie).
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do wpisu tajnego Server (lokalnie) (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą klucza tajnego Server (lokalnie), potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Klucz tajny Server (lokalnie) logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie wpisu tajnego Server (lokalnie) z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-secret-server-on-premises-from-the-gallery"></a>Dodawanie wpisu tajnego Server (lokalnie) z galerii
Aby skonfigurować integrację serwera klucz tajny (lokalnie) w usłudze Azure AD, należy dodać wpis tajny serwera (lokalnie) z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać wpis tajny serwera (lokalnie) z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **wpis tajny serwera (lokalnie)** , wybierz opcję **wpis tajny serwera (lokalnie)** z panelu wynik następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tajne Server (lokalnie) na liście wyników](./media/secretserver-on-premises-tutorial/tutorial_secretserver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą klucza tajnego serwera (lokalnie) w oparciu o nazwie "Britta Simon" użytkownika testowego.

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednikiem wpisu tajnego Server (lokalnie) dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika na serwerze klucz tajny (lokalnie) musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą klucza tajnego Server (lokalnie), należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego wpis tajny serwera (lokalnie)](#create-a-secret-server-on-premises-test-user)**  — aby odpowiednikiem Britta Simon w serwera klucz tajny (lokalnie), który jest połączony z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji wpis tajny serwera (lokalnie).

**Aby skonfigurować usługę Azure AD logowanie jednokrotne za pomocą klucza tajnego Server (lokalnie), wykonaj następujące czynności:**

1. W witrynie Azure portal na **wpis tajny serwera (lokalnie)** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/secretserver-on-premises-tutorial/tutorial_secretserver_samlbase.png)

1. Na **wpis tajny serwera (lokalnie), domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Tajne Server (lokalnie) domena i adresy URL pojedynczy informacje logowania jednokrotnego](./media/secretserver-on-premises-tutorial/tutorial_secretserver_url.png)

    a. W **identyfikator** polu tekstowym wprowadź nazwę użytkownika, wybranych wartości, na przykład: `https://secretserveronpremises.azure`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<SecretServerURL>/SAML/AssertionConsumerService.aspx`

    > [!NOTE]
    > Identyfikator jednostki powyżej jest tylko przykładem i masz swobodę wyboru unikatową wartość, która identyfikuje Twoje wystąpienie serwera klucz tajny w usłudze Azure AD. Trzeba będzie wysłać ten identyfikator jednostki [zespołem pomocy technicznej klienta wpis tajny serwera (lokalnie)](https://thycotic.force.com/support/s/) i ich konfigurowanie na bok. Aby uzyskać więcej informacji, przeczytaj [w tym artykule](https://thycotic.force.com/support/s/article/Configuring-SAML-in-Secret-Server).

1. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Tajne Server (lokalnie) domena i adresy URL pojedynczy informacje logowania jednokrotnego](./media/secretserver-on-premises-tutorial/tutorial_secretserver_url1.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<SecretServerURL>/login.aspx`
     
    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta wpis tajny serwera (lokalnie)](https://thycotic.force.com/support/s/) do uzyskania tych wartości.

1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/secretserver-on-premises-tutorial/tutorial_secretserver_certificate.png)

1. Sprawdź **Pokaż zaawansowane ustawienia podpisywania certyfikatów** i wybierz **opcja podpisywania** jako **odpowiedź i potwierdzenie SAML logowania**.

    ![Opcje podpisywania](./media/secretserver-on-premises-tutorial/signing.png)

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/secretserver-on-premises-tutorial/tutorial_general_400.png)
    
1. Na **konfiguracji wpis tajny serwera (lokalnie)** , kliknij przycisk **skonfigurować klucz tajny Server (lokalnie)** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja serwera tajny (lokalnie)](./media/secretserver-on-premises-tutorial/tutorial_secretserver_configure.png)

1. Aby skonfigurować logowanie jednokrotne na **wpis tajny serwera (lokalnie)** stronie, musisz wysłać pobrany **Certificate(Base64), adres URL wylogowania protokołu SAML pojedynczego logowania jednokrotnego adres URL usługi**, i **jednostki SAML Identyfikator** do [wpis tajny serwera (lokalnie), zespół pomocy technicznej](https://thycotic.force.com/support/s/). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/secretserver-on-premises-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/secretserver-on-premises-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/secretserver-on-premises-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/secretserver-on-premises-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-secret-server-on-premises-test-user"></a>Tworzenie użytkownika testowego wpis tajny serwera (lokalnie)

W tej sekcji utworzysz użytkownika o nazwie Britta Simon klucz tajny Server (lokalnie). Praca z [wpis tajny serwera (lokalnie), zespół pomocy technicznej](https://thycotic.force.com/support/s/) Aby dodać użytkowników na platformie wpis tajny serwera (lokalnie). Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do klucza tajnego Server (lokalnie).

![Przypisanie roli użytkownika][200]

**Aby przypisać Britta Simon wpis tajny serwera (lokalnie), wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

1. Na liście aplikacji wybierz **wpis tajny serwera (lokalnie)** .

    ![Link wpisu tajnego Server (lokalnie) na liście aplikacji](./media/secretserver-on-premises-tutorial/tutorial_secretserver_app.png)

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka wpis tajny serwera (lokalnie), w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do aplikacji wpis tajny serwera (lokalnie).
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/secretserver-on-premises-tutorial/tutorial_general_01.png
[2]: ./media/secretserver-on-premises-tutorial/tutorial_general_02.png
[3]: ./media/secretserver-on-premises-tutorial/tutorial_general_03.png
[4]: ./media/secretserver-on-premises-tutorial/tutorial_general_04.png

[100]: ./media/secretserver-on-premises-tutorial/tutorial_general_100.png

[200]: ./media/secretserver-on-premises-tutorial/tutorial_general_200.png
[201]: ./media/secretserver-on-premises-tutorial/tutorial_general_201.png
[202]: ./media/secretserver-on-premises-tutorial/tutorial_general_202.png
[203]: ./media/secretserver-on-premises-tutorial/tutorial_general_203.png


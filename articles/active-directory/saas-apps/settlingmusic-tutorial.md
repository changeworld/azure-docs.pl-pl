---
title: 'Samouczek: Integracja usługi Azure Active Directory przy użyciu rozliczania utworów muzycznych | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i odtwarzaniu muzyki Settling.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6f86a8a2-4bd0-40cc-b1b4-752fce123328
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b7dee41b226cdacea1d9c7f1cf581d9f095977e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60865241"
---
# <a name="tutorial-azure-active-directory-integration-with-settling-music"></a>Samouczek: Integracja usługi Azure Active Directory przy użyciu rozliczania utworów muzycznych

W tym samouczku dowiesz się, jak zintegrować Settling muzykę z usługą Azure Active Directory (Azure AD).

Integrowania muzyczny Settling z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do rozliczania music.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do rozliczania music (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu rozliczania utworów muzycznych, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Settling utworów muzycznych logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodanie muzyki Settling z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-settling-music-from-the-gallery"></a>Dodanie muzyki Settling z galerii
Aby skonfigurować integrację Settling muzyki w usłudze Azure AD, należy dodać Settling muzykę z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Settling muzykę z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **rozliczania utworów muzycznych**, wybierz opcję **rozliczania utworów muzycznych** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Rozliczanie utworów muzycznych na liście wyników](./media/settlingmusic-tutorial/tutorial_settlingmusic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowania usługi Azure AD logowania jednokrotnego przy użyciu rozliczania utworów muzycznych, w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika muzyczny Settling jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika rozstrzygnąć utworów muzycznych musi nawiązać.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z muzyką Settling, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **[Tworzenie użytkownika testowego utworów muzycznych Settling](#create-a-settling-music-test-user)**  — aby odpowiednikiem Britta Simon rozstrzygnąć utworów muzycznych, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji utworów muzycznych Settling.

**Aby skonfigurować usługi Azure AD logowania jednokrotnego przy użyciu rozliczania utworów muzycznych, wykonaj następujące czynności:**

1. W witrynie Azure portal na **rozliczania utworów muzycznych** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/settlingmusic-tutorial/tutorial_settlingmusic_samlbase.png)

1. Na **rozliczania utworów muzycznych domen i adresów URL** sekcji, wykonaj następujące czynności:

    ![Rozliczanie utworów muzycznych domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/settlingmusic-tutorial/tutorial_settlingmusic_url.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.rakurakuseisan.jp/<USERACCOUNT>/`

    b. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.rakurakuseisan.jp/<USERACCOUNT>/`

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL i identyfikatora logowania. Skontaktuj się z pomocą [rozliczania zespołem pomocy technicznej klienta utworów muzycznych](https://rakurakuseisan.jp/) do uzyskania tych wartości. 
 
1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link do pobierania certyfikatu](./media/settlingmusic-tutorial/tutorial_settlingmusic_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/settlingmusic-tutorial/tutorial_general_400.png)

1. Na **rozliczania utworów muzycznych konfiguracji** , kliknij przycisk **skonfiguruj rozliczanie utworów muzycznych** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Rozliczanie utworów muzycznych konfiguracji](./media/settlingmusic-tutorial/tutorial_settlingmusic_configure.png) 

1. W oknie przeglądarki internetowej innej, zaloguj się do rozliczania utworów muzycznych jako Administrator zabezpieczeń.

1. Na górze strony, kliknij przycisk **zarządzania** kartę.

    ![Rozliczanie step1 utworów muzycznych](./media/settlingmusic-tutorial/tutorial_settlingmusic_step1.png)

1. Kliknij pozycję **ustawienia systemowego** kartę.

    ![Rozliczanie step2 utworów muzycznych](./media/settlingmusic-tutorial/tutorial_settlingmusic_step2.png)

1. Przełącz się do **zabezpieczeń** kartę.

    ![Rozliczanie step3 utworów muzycznych](./media/settlingmusic-tutorial/tutorial_settlingmusic_step3.png)

1. Na **Single sign-on ustawienie** sekcji, wykonaj następujące czynności:

    ![Rozliczanie step5 utworów muzycznych](./media/settlingmusic-tutorial/tutorial_settlingmusic_step4.png)

    a. Kliknij przycisk **umożliwiające**.

    b. W **adres URL logowania dostawcy identyfikator** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowanej w witrynie Azure portal.

    c. W **adres URL wylogowania dostawcy identyfikator** pola tekstowego, Wklej wartość **adres URL wylogowania** skopiowanej w witrynie Azure portal.

    d. Kliknij przycisk **wybierz plik** do przekazania **certyfikat (Base64)** który został pobrany tworzą witryny Azure portal.

    e. Kliknij przycisk **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/settlingmusic-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/settlingmusic-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/settlingmusic-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/settlingmusic-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-settling-music-test-user"></a>Tworzenie użytkownika testowego Settling utworów muzycznych

W tej sekcji utworzysz użytkownika o nazwie Britta Simon Settling muzyczny. Praca z [rozliczania zespołem pomocy technicznej klienta utworów muzycznych](https://rakurakuseisan.jp/) Aby dodać użytkowników na platformie utworów muzycznych Settling. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do rozliczania music.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Settling muzyki, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **rozliczania utworów muzycznych**.

    ![Link Settling utworów muzycznych na liście aplikacji](./media/settlingmusic-tutorial/tutorial_settlingmusic_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Settling utworów muzycznych, w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacja muzyka Settling.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/settlingmusic-tutorial/tutorial_general_01.png
[2]: ./media/settlingmusic-tutorial/tutorial_general_02.png
[3]: ./media/settlingmusic-tutorial/tutorial_general_03.png
[4]: ./media/settlingmusic-tutorial/tutorial_general_04.png

[100]: ./media/settlingmusic-tutorial/tutorial_general_100.png

[200]: ./media/settlingmusic-tutorial/tutorial_general_200.png
[201]: ./media/settlingmusic-tutorial/tutorial_general_201.png
[202]: ./media/settlingmusic-tutorial/tutorial_general_202.png
[203]: ./media/settlingmusic-tutorial/tutorial_general_203.png


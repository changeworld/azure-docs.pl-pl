---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą pałąka zabezpieczającego | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i pałąka zabezpieczającego.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 57537e54-9388-4272-a610-805ce45a451f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/04/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ebedddf5e62e41bd0ef2d4c426df448a81a1e2d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60349797"
---
# <a name="tutorial-azure-active-directory-integration-with-rollbar"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą pałąka zabezpieczającego

W tym samouczku dowiesz się, jak zintegrować pałąka zabezpieczającego w usłudze Azure Active Directory (Azure AD).

Integrowanie pałąka zabezpieczającego z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do pałąka zabezpieczającego.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do pałąka zabezpieczającego (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą pałąka zabezpieczającego, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Pałąka zabezpieczającego logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie pałąka zabezpieczającego z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-rollbar-from-the-gallery"></a>Dodawanie pałąka zabezpieczającego z galerii
Aby skonfigurować integrację pałąka zabezpieczającego w usłudze Azure AD, należy dodać pałąka zabezpieczającego z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać pałąka zabezpieczającego z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **pałąka zabezpieczającego**, wybierz opcję **pałąka zabezpieczającego** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Pałąka zabezpieczającego na liście wyników](./media/rollbar-tutorial/tutorial_rollbar_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą pałąka zabezpieczającego w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w pałąka zabezpieczającego dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w pałąka zabezpieczającego musi zostać ustanowione.

W pałąka zabezpieczającego, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą pałąka zabezpieczającego, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **[Tworzenie użytkownika testowego pałąka zabezpieczającego](#create-a-rollbar-test-user)**  — aby odpowiednikiem Britta Simon w pałąka zabezpieczającego, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji pałąka zabezpieczającego.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z pałąka zabezpieczającego, wykonaj następujące czynności:**

1. W witrynie Azure portal na **pałąka zabezpieczającego** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/rollbar-tutorial/tutorial_rollbar_samlbase.png)

1. Na **pałąka zabezpieczającego domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Pałąka zabezpieczającego domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/rollbar-tutorial/tutorial_rollbar_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL: `https://saml.rollbar.com`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://rollbar.com/<accountname>/saml/sso/azure/`

1. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Pałąka zabezpieczającego domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/rollbar-tutorial/tutorial_rollbar_url1.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://rollbar.com/<accountname>/saml/login/azure/`
     
    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta pałąka zabezpieczającego](mailto:support@rollbar.com) do uzyskania tych wartości. 

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/rollbar-tutorial/tutorial_rollbar_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/rollbar-tutorial/tutorial_general_400.png)
    
1. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy pałąka zabezpieczającego.

1. Kliknij pozycję **ustawienia profilu** w prawym górnym rogu, a następnie kliknij przycisk **ustawienia nazwy konta**.
    
    ![Konfigurowanie](./media/rollbar-tutorial/general.png)

1. Kliknij przycisk **dostawcy tożsamości** w obszarze zabezpieczeń.

    ![Konfigurowanie](./media/rollbar-tutorial/configure1.png)

1. W **dostawcy tożsamości SAML** sekcji, wykonaj następujące czynności:
    
    ![Konfigurowanie](./media/rollbar-tutorial/configure2.png)

    a. Wybierz **AZURE** z **dostawcy tożsamości SAML** listy rozwijanej.

    b. Otwórz plik metadanych w programie Notatnik, skopiuj jego zawartość do Schowka, a następnie wklej go do **metadanych SAML** pola tekstowego.

    c. Kliknij pozycję **Zapisz**.

1. Po kliknięciu przycisku Zapisz przycisku ekranu będą się następująco:
    
    ![Konfigurowanie](./media/rollbar-tutorial/configure3.png)
    > [!NOTE] 
    > Aby wykonać poniższe czynności, należy najpierw dodać siebie jako użytkownik pałąka zabezpieczającego aplikacji na platformie Azure.
    a. Jeśli chcesz wymagać od wszystkich użytkowników, aby uwierzytelnić się za pośrednictwem platformy Azure, a następnie kliknij przycisk **zalogowania się za pośrednictwem dostawcy tożsamości** konieczność ponownego uwierzytelnienia za pośrednictwem platformy Azure.  

    b.  Gdy następuje powrót do ekranu, wybierz **wymagają logowania za pomocą dostawcy tożsamości SAML** pola wyboru.

    b. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/rollbar-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/rollbar-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/rollbar-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/rollbar-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-rollbar-test-user"></a>Tworzenie użytkownika testowego pałąka zabezpieczającego

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do pałąka zabezpieczającego, musi być obsługiwana w pałąka zabezpieczającego. W przypadku pałąka zabezpieczającego Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do witryny firmy pałąka zabezpieczającego jako administrator.

1. Kliknij pozycję **ustawienia profilu** w prawym górnym rogu, a następnie kliknij przycisk **ustawienia nazwy konta**.

    ![Użytkownik](./media/rollbar-tutorial/general.png)

1. Kliknij pozycję **Użytkownicy**.
    
    ![Dodawanie pracownika](./media/rollbar-tutorial/user1.png)

1. Kliknij przycisk **Zaproś członków zespołu**.

    ![Zaproś inne osoby](./media/rollbar-tutorial/user2.png)

1. W polu tekstowym wprowadź nazwę użytkownika, takich jak **brittasimon\@contoso.com** i kliknięcie **Add/zaproszenia**.

    ![Zaproś inne osoby](./media/rollbar-tutorial/user3.png)

1. Użytkownik otrzymuje wiadomość z zaproszeniem i po zaakceptowaniu go on utworzony w systemie.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do pałąka zabezpieczającego.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon pałąka zabezpieczającego, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **pałąka zabezpieczającego**.

    ![Link pałąka zabezpieczającego na liście aplikacji](./media/rollbar-tutorial/tutorial_rollbar_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka pałąka zabezpieczającego w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji pałąka zabezpieczającego.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/rollbar-tutorial/tutorial_general_01.png
[2]: ./media/rollbar-tutorial/tutorial_general_02.png
[3]: ./media/rollbar-tutorial/tutorial_general_03.png
[4]: ./media/rollbar-tutorial/tutorial_general_04.png

[100]: ./media/rollbar-tutorial/tutorial_general_100.png

[200]: ./media/rollbar-tutorial/tutorial_general_200.png
[201]: ./media/rollbar-tutorial/tutorial_general_201.png
[202]: ./media/rollbar-tutorial/tutorial_general_202.png
[203]: ./media/rollbar-tutorial/tutorial_general_203.png


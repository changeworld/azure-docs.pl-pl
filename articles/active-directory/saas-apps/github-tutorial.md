---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą usługi GitHub | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i usługi GitHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeedes
ms.openlocfilehash: 41e8fb40f07a88cb6fa2108a38db7f973fccd2f9
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/19/2018
ms.locfileid: "53607802"
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą usługi GitHub

W tym samouczku dowiesz się, jak zintegrować usługi GitHub z usługi Azure Active Directory (Azure AD).

Integrowanie usługi GitHub z usługi Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do usługi GitHub.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do usługi GitHub (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą usługi GitHub, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- GitHub logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym.
Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie usługi GitHub za pomocą galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-github-from-the-gallery"></a>Dodawanie usługi GitHub za pomocą galerii
Aby skonfigurować integrację z usługi GitHub do usługi Azure AD, należy dodać GitHub z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać usługi GitHub z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![image](./media/github-tutorial/selectazuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![image](./media/github-tutorial/a_select_app.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![image](./media/github-tutorial/a_new_app.png)

4. W polu wyszukiwania wpisz **GitHub**, wybierz opcję **GitHub** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![image](./media/github-tutorial/tutorial_github_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą usługi GitHub, w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w usłudze GitHub dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w usłudze GitHub musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą usługi GitHub, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
3. **[Tworzenie użytkownika testowego GitHub](#create-a-github-test-user)**  — aby odpowiednikiem Britta Simon w GitHub, które jest połączone z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji usługi GitHub.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne za pomocą usługi GitHub, wykonaj następujące czynności:**

1. W [witryny Azure portal](https://portal.azure.com/)na **GitHub** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![image](./media/github-tutorial/b1_b2_select_sso.png)

2. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **wybierz** dla **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![image](./media/github-tutorial/b1_b2_saml_sso.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![image](./media/github-tutorial/b1-domains_and_urlsedit.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![image](./media/github-tutorial/tutorial_github_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://github.com/orgs/<entity-id>/sso`

    b. W **identyfikator jednostki** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > Należy pamiętać, że nie są rzeczywiste wartości. Musisz zaktualizować te wartości przy użyciu rzeczywistego konta dla adresu URL i identyfikator. W tym miejscu zalecamy przy użyciu unikatowej wartości ciągu w identyfikatorze. Przejdź do sekcji administratora usługi GitHub, aby pobrać te wartości.

5. GitHub aplikacja oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Kliknij przycisk **Edytuj** przycisk, aby otworzyć **atrybutów użytkownika** okna dialogowego.

    ![image](./media/github-tutorial/i3-attribute.png)

6. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej, i wykonaj następujące czynności:

    a. Kliknij przycisk **Edytuj** przycisk, aby otworzyć **Zarządzanie oświadczenia użytkownika** okna dialogowego.

    ![image](./media/github-tutorial/i2-attribute.png)

    ![image](./media/github-tutorial/i4-attribute.png)

    b. Z **atrybut źródłowy** , wybierz wartość atrybutu na liście.

    c. Kliknij pozycję **Zapisz**.

7. W **certyfikat podpisywania SAML** kliknij **Pobierz** można pobrać **certyfikat (Base64)** i zapisz go na komputerze.

    ![image](./media/github-tutorial/tutorial_github_certficate.png)

8. Na **skonfiguruj usługę GitHub** sekcji, skopiuj odpowiedni adres URL, zgodnie z wymaganiami.

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

    ![image](./media/github-tutorial/d1_samlsonfigure.png)

9. W oknie przeglądarki internetowej innej Zaloguj się do witryny organizacji GitHub, jako administrator.

10. Przejdź do **ustawienia** i kliknij przycisk **zabezpieczeń**

    ![Ustawienia](./media/github-tutorial/tutorial_github_config_github_03.png)

11. Sprawdź **uwierzytelnianie SAML Włącz** okno, odsłaniając pola konfiguracji logowania jednokrotnego. Następnie użyj pojedynczego logowania jednokrotnego wartość adresu URL, aby zaktualizować pojedynczy adres URL logowania w konfiguracji usługi Azure AD.

    ![Ustawienia](./media/github-tutorial/tutorial_github_config_github_13.png)

12. Określ następujące pola:

    ![Ustawienia](./media/github-tutorial/tutorial_github_config_github_051.png)

    a. W **adres URL logowania** pola tekstowego, Wklej **adres URL logowania** wartości, które zostały skopiowane z witryny Azure portal.

    b. W **wystawcy** pola tekstowego, Wklej **usługi Azure AD identyfikator** wartości, które zostały skopiowane z witryny Azure portal.

    c. Otwórz certyfikat pobrany z witryny Azure portal w programie Notatnik, Wklej zawartość **certyfikatu publicznego** pola tekstowego.

    d. Kliknij pozycję **Edytuj** ikona edycji **podpis metody** i **metoda szyfrowanego** z **RSA SHA1** i **SHA1**do **algorytm RSA-SHA256** i **SHA256** jak pokazano poniżej.

    ![image](./media/github-tutorial/tutorial_github_sha.png)

13. Kliknij pozycję **plik konfiguracji SAML testu** do upewnij się, że żadne błędy sprawdzania poprawności ani błędy podczas logowania jednokrotnego.

    ![Ustawienia](./media/github-tutorial/tutorial_github_config_github_06.png)

14. Kliknij pozycję **Zapisz**

> [!NOTE]
> Logowanie jednokrotne w usłudze GitHub jest uwierzytelniany w konkretnej organizacji w usłudze GitHub, a nie zastępuje uwierzytelnianie w usłudze GitHub. W związku z tym Jeśli sesja github.com wygasł, możesz może poproszona o uwierzytelnienie za pomocą usługi GitHub identyfikatorze/haśle w procesie pojedynczego logowania jednokrotnego.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![image](./media/github-tutorial/d_users_and_groups.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![image](./media/github-tutorial/d_adduser.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![image](./media/github-tutorial/d_userproperties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.

### <a name="create-a-github-test-user"></a>Tworzenie użytkownika testowego usługi GitHub

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w usłudze GitHub. Usługa GitHub obsługuje automatyczna aprowizacja użytkowników, która jest domyślnie włączona. Więcej szczegółów można znaleźć [tutaj](github-provisioning-tutorial.md) dotyczące sposobu konfigurowania automatycznej aprowizacji użytkowników.

**Jeśli potrzebujesz ręcznie utworzyć użytkownika, należy wykonać następujące kroki:**

1. Zaloguj się do witryny usługi GitHub firmy jako administrator.

2. Kliknij przycisk **osób**.

    ![Osoby](./media/github-tutorial/tutorial_github_config_github_08.png "osoby")

3. Kliknij przycisk **Członkowskie zaproszenia**.

    ![Zaproś użytkowników](./media/github-tutorial/tutorial_github_config_github_09.png "zaprosić użytkowników")

4. Na **Członkowskie zaproszenia** okna dialogowego strony, wykonaj następujące czynności:

    a. W **E-mail** pole tekstowe, wpisz adres e-mail konta Britta Simon.

    ![Zaproś inne osoby](./media/github-tutorial/tutorial_github_config_github_10.png "zapraszać inne osoby")

    b. Kliknij przycisk **wysłać zaproszenia**.

    ![Zaproś inne osoby](./media/github-tutorial/tutorial_github_config_github_11.png "zapraszać inne osoby")

    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory będzie otrzymywać wiadomości e-mail i kliknij link, aby potwierdzić swoje konto, zanim stanie się aktywny.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do usługi GitHub.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![image](./media/github-tutorial/d_all_applications.png)

2. Na liście aplikacji wybierz **GitHub**.

    ![image](./media/github-tutorial/tutorial_github_app.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![image](./media/github-tutorial/d_leftpaneusers.png)

4. Wybierz **Dodaj** przycisk, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![image](./media/github-tutorial/d_assign_user.png)

4. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

5. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi GitHub, w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikację usługi GitHub.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



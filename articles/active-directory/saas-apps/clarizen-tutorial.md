---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Clarizen | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Clarizen.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: jeedes
ms.openlocfilehash: 8c66d8eec6d3967026c17db9ce8d616b97d25931
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54807732"
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Clarizen

W tym samouczku nauczysz się integracji Azure Active Directory (Azure AD) przy użyciu Clarizen. Ta integracja zapewnia następujące korzyści:

- Można kontrolować, w usłudze Azure AD, kto ma dostęp do Clarizen.
- Aby umożliwić użytkownikom automatyczne logowanie do Clarizen (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji, witryny Azure portal.

Scenariusz, w tym samouczku obejmuje dwa główne zadania:

1. Dodaj Clarizen z galerii.
1. Konfigurowanie i testowanie usługi Azure AD logowania jednokrotnego.

Jeśli chcesz więcej szczegółów na temat oprogramowania jako usługi (SaaS) integracji aplikacji z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne
Aby skonfigurować integrację usługi Azure AD za pomocą Clarizen, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Subskrypcja Clarizen, w którym włączono obsługę logowania jednokrotnego

Aby przetestować kroki w tym samouczku, musisz mieć dostęp do następujących elementów:

- Testowanie usługi Azure AD logowania jednokrotnego w środowisku testowym. Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska testowego usługi Azure AD, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="add-clarizen-from-the-gallery"></a>Dodaj Clarizen z galerii
Aby skonfigurować integrację Clarizen w usłudze Azure AD, należy dodać Clarizen z galerii z listą zarządzanych aplikacji SaaS.

1. W [witryny Azure portal](https://portal.azure.com), w okienku po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Ikona usługi Azure Active Directory][1]

1. Kliknij przycisk **aplikacje dla przedsiębiorstw**. Następnie kliknij przycisk **wszystkie aplikacje**.

    ![Kliknięcie przycisku "aplikacje dla przedsiębiorstw" i "Wszystkie aplikacje"][2]

1. Kliknij przycisk **Dodaj** znajdujący się u góry okna dialogowego.

    ![Przycisk "Dodaj"][3]

1. W polu wyszukiwania wpisz **Clarizen**.

    ![Wpisz "Clarizen" w polu wyszukiwania](./media/clarizen-tutorial/tutorial_clarizen_000.png)

1. W okienku wyników wybierz **Clarizen**, a następnie kliknij przycisk **Dodaj** umożliwiające dodanie aplikacji.

    ![Wybieranie Clarizen w okienku wyników](./media/clarizen-tutorial/tutorial_clarizen_0001.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD
W poniższych sekcjach Skonfiguruj i testowanie usługi Azure AD logowanie jednokrotne za pomocą Clarizen na podstawie użytkownika testu Britta Simon.

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Clarizen do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Clarizen musi można ustanowić. Ustanowienia tej relacji łączy, przypisując wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** w Clarizen.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Clarizen, wykonaj poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  do testowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Clarizen](#create-a-clarizen-test-user)**  mieć odpowiednikiem Britta Simon Clarizen połączonego z jej reprezentacji usługi Azure AD.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  umożliwiające Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD
Włączanie usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Clarizen.

1. W witrynie Azure portal na **Clarizen** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Kliknięcie przycisku "Logowanie jednokrotne"][4]

1. W **logowanie jednokrotne** okno dialogowe dla **tryb**, wybierz opcję **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Wybierając pozycję "opartej na SAML logowania jednokrotnego"](./media/clarizen-tutorial/tutorial_clarizen_01.png)

1. W **Clarizen domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Pola Adres URL identyfikatora i odpowiedzi](./media/clarizen-tutorial/tutorial_clarizen_02.png)

    a. W **identyfikator** wpisz wartość jako: **Clarizen**

    b. W **adres URL odpowiedzi** wpisz adres URL przy użyciu następującego wzorca: **https://<company name>.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx**

    > [!NOTE]
    > Nie są to rzeczywiste wartości. Należy użyć rzeczywistego identyfikatora i adres URL odpowiedzi. Tutaj zaleca się, że używasz unikatowej wartości ciągu jako identyfikator. Aby uzyskać wartości rzeczywiste, skontaktuj się z [zespołem pomocy technicznej Clarizen](https://success.clarizen.com/hc/en-us/requests/new).

1. Na **certyfikat podpisywania SAML** kliknij **Utwórz nowy certyfikat**.

    ![Klikając przycisk "Utwórz nowy certyfikat"](./media/clarizen-tutorial/tutorial_clarizen_03.png)    

1. W **Utwórz nowy certyfikat** okna dialogowego pole, kliknij ikonę kalendarza i wybierz datę wygaśnięcia. Następnie kliknij przycisk **Save** (Zapisz).

    ![Wybieranie i zapisywanie datę wygaśnięcia](./media/clarizen-tutorial/tutorial_general_300.png)

1. W **certyfikat podpisywania SAML** zaznacz **Ustaw nowy certyfikat jako aktywny**, a następnie kliknij przycisk **Zapisz**.

    ![Zaznaczenie pola wyboru dla uaktywnienie nowego certyfikatu](./media/clarizen-tutorial/tutorial_clarizen_04.png)

1. W **certyfikat przerzucania** okno dialogowe, kliknij przycisk **OK**.

    ![Kliknięcie przycisku "OK", aby upewnić się, że chcesz uaktywnić certyfikat](./media/clarizen-tutorial/tutorial_general_400.png)

1. W **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Klikając pozycję "Certyfikat (Base64)" Aby rozpocząć pobieranie](./media/clarizen-tutorial/tutorial_clarizen_05.png)

1. W **konfiguracji Clarizen** , kliknij przycisk **skonfigurować Clarizen** otworzyć **Konfigurowanie logowania jednokrotnego** okna.

    ![Klikając pozycję "Konfiguruj Clarizen"](./media/clarizen-tutorial/tutorial_clarizen_06.png)

    ![Okno "Konfigurowanie logowania jednokrotnego", łącznie z plikami i adresów URL](./media/clarizen-tutorial/tutorial_clarizen_07.png)

1. W oknie przeglądarki internetowej innej Zaloguj się w witrynie firmy Clarizen jako administrator.

1. Kliknij swoją nazwę użytkownika, a następnie kliknij przycisk **ustawienia**.

    ![Klikając pozycję "Ustawienia" w obszarze swoją nazwę użytkownika](./media/clarizen-tutorial/tutorial_clarizen_001.png "ustawienia")

1. Kliknij przycisk **ustawienia globalne** kartę. Następnie obok pozycji **uwierzytelniania federacyjnego**, kliknij przycisk **Edytuj**.

    ![Kartę "Ustawienia globalne"](./media/clarizen-tutorial/tutorial_clarizen_002.png "ustawienia globalne")

1. W **uwierzytelniania federacyjnego** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe "Federacyjnego uwierzytelniania"](./media/clarizen-tutorial/tutorial_clarizen_003.png "uwierzytelniania federacyjnego")

    a. Wybierz **Włącz federacyjne uwierzytelniania**.

    b. Kliknij przycisk **przekazywanie** Aby przekazać certyfikat pobrany.

    c. W **adres URL logowania** wprowadź wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z okna konfiguracji aplikacji usługi Azure AD.

    d. W **adres URL wylogowania** wprowadź wartość **adres URL wylogowania** z okna konfiguracji aplikacji usługi Azure AD.

    e. Wybierz **Użyj WPIS**.

    f. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W witrynie Azure portal Utwórz użytkownika testowego o nazwie Britta Simon.

![Nazwa i adres e-mail użytkownika usługi Azure AD][100]

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Ikona usługi Azure Active Directory](./media/clarizen-tutorial/create_aaduser_01.png)

1. Kliknij przycisk **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy** do wyświetlania listy użytkowników.

    ![Klikając pozycję "Użytkownicy i grupy" i "Wszyscy użytkownicy"](./media/clarizen-tutorial/create_aaduser_02.png)

1. W górnej części okna dialogowego kliknij **Dodaj** otworzyć **użytkownika** okno dialogowe.

    ![Przycisk "Dodaj"](./media/clarizen-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe "User" z nazwę, adres e-mail i hasło, wypełnione](./media/clarizen-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail konta Britta Simon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.

### <a name="create-a-clarizen-test-user"></a>Tworzenie użytkownika testowego Clarizen

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w Clarizen.

**Jeśli potrzebujesz utworzyć użytkownika ręcznie, wykonaj następujące czynności:**

Aby umożliwić użytkownikom usługi Azure AD do logowania się Clarizen w, uaktywniać ich konta. W przypadku Clarizen Inicjowanie obsługi administracyjnej jest zadanie ręczne.

1. Zaloguj się do witryny firmy Clarizen jako administrator.

2. Kliknij kartę **People** (Osoby).

    ![Klikając pozycję "Osoby"](./media/clarizen-tutorial/create_aaduser_001.png "osoby")

3. Kliknij przycisk **zaprosić użytkownika**.

    ![Przycisk "Zaproś użytkownika"](./media/clarizen-tutorial/create_aaduser_002.png "zaprosić użytkowników")

1. W **zaprosić osób** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe "Zapraszać inne osoby"](./media/clarizen-tutorial/create_aaduser_003.png "zaprosić osoby")

    a. W **E-mail** wpisz adres e-mail konta Britta Simon.

    b. Kliknij przycisk **zaprosić**.

    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z linkiem, którego użyje w celu potwierdzenia konta, zanim stanie się ono aktywne.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD
Włącz Britta Simon korzystać z platformy Azure logowania jednokrotnego przez udostępnienie jej Clarizen.

![Testowanie przypisanego użytkownika][200]

1. W witrynie Azure portal, otwórz wyświetlić aplikacje, przejdź do widoku katalogu, kliknij przycisk **aplikacje dla przedsiębiorstw**, a następnie kliknij przycisk **wszystkie aplikacje**.

    ![Kliknięcie przycisku "aplikacje dla przedsiębiorstw" i "Wszystkie aplikacje"][201]

1. Na liście aplikacji wybierz **Clarizen**.

    ![Wybierając Clarizen na liście](./media/clarizen-tutorial/tutorial_clarizen_50.png)

1. W okienku po lewej stronie kliknij **użytkowników i grup**.

    ![Klikając pozycję "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj**. Następnie w **Dodaj przydziału** okno dialogowe, wybierz opcję **użytkowników i grup**.

    ![Przycisk "Dodaj" i "Dodawanie przypisania" — okno dialogowe][203]

1. W **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. W **użytkowników i grup** okno dialogowe, kliknij przycisk **wybierz** przycisku.

1. W **Dodaj przydziału** okno dialogowe, kliknij przycisk **przypisać** przycisku.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego
Testowanie konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Clarizen w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze aplikacji Clarizen.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/clarizen-tutorial/tutorial_general_01.png
[2]: ./media/clarizen-tutorial/tutorial_general_02.png
[3]: ./media/clarizen-tutorial/tutorial_general_03.png
[4]: ./media/clarizen-tutorial/tutorial_general_04.png

[100]: ./media/clarizen-tutorial/tutorial_general_100.png

[200]: ./media/clarizen-tutorial/tutorial_general_200.png
[201]: ./media/clarizen-tutorial/tutorial_general_201.png
[202]: ./media/clarizen-tutorial/tutorial_general_202.png
[203]: ./media/clarizen-tutorial/tutorial_general_203.png
---
title: 'Samouczek: Integracja usługi Azure Active Directory z Asana | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i usłudze Asana.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 837e38fe-8f55-475c-87f4-6394dc1fee2b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: jeedes
ms.openlocfilehash: f5ea7a330891d4befeb6388bbe7f37b2a4aa848f
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39438212"
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Samouczek: Integracja usługi Azure Active Directory z Asana

W tym samouczku dowiesz się, jak zintegrować Asana w usłudze Azure Active Directory (Azure AD).

Integrowanie Asana z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Asana
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Asana (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Asana, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Logowania jednokrotnego Asana włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym.
Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Asana z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-asana-from-the-gallery"></a>Dodawanie Asana z galerii
Aby skonfigurować integrację Asana w usłudze Azure AD, należy dodać Asana z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Asana z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **Asana**, wybierz opcję **Asana** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/asana-tutorial/tutorial_asana_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Asana, w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w usłudze Asana dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w usłudze Asana musi zostać ustanowione.

W usłudze Asana, przypisz wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD jednokrotne logowanie w usłudze Asana, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego w usłudze Asana](#create-an-asana-test-user)**  — aby odpowiednikiem Britta Simon w usłudze Asana, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Asana.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Asana, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Asana** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/asana-tutorial/tutorial_asana_samlbase.png)

1. Na **Asana, domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i domen w usłudze Asana pojedynczego logowania jednokrotnego informacji](./media/asana-tutorial/tutorial_asana_url.png)

    a. W **adres URL logowania** polu tekstowym wprowadź adres URL: `https://app.asana.com/`

    b. W **identyfikator** polu tekstowym wartość typu: `https://app.asana.com/`

1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/asana-tutorial/tutorial_asana_certificate.png)

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/asana-tutorial/tutorial_general_400.png)

1. Na **konfiguracji Asana** , kliknij przycisk **skonfigurować Asana** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja w usłudze Asana](./media/asana-tutorial/tutorial_asana_configure.png)

1. W oknie innej przeglądarki logowanie jednokrotne do aplikacji Asana. Aby skonfigurować logowanie Jednokrotne w usłudze Asana, dostęp do ustawienia obszaru roboczego, klikając nazwę obszaru roboczego w prawym górnym rogu ekranu. Następnie kliknij  **\<nazwy swojego obszaru roboczego\> ustawienia**.

    ![Ustawienia logowania jednokrotnego w usłudze Asana](./media/asana-tutorial/tutorial_asana_09.png)

1. Na **ustawień organizacji** okna, kliknij przycisk **administracji**. Następnie kliknij przycisk **członków musisz zalogować się za pośrednictwem protokołu SAML** umożliwiające konfigurację logowania jednokrotnego. Wykonaj następujące kroki:

    ![Konfigurowanie ustawień organizacji rejestracji jednokrotnej](./media/asana-tutorial/tutorial_asana_10.png)  

     a. W **adres URL logowania strony** pola tekstowego, Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL**.

     b. Kliknij prawym przyciskiem myszy certyfikat pobrany z witryny Azure portal, a następnie otwórz plik certyfikatu za pomocą Notatnika lub preferowanym edytorze tekstu. Kopiowanie zawartości między begin i end tytuł certyfikatu, a następnie wklej je **certyfikat X.509** pola tekstowego.

1. Kliknij pozycję **Zapisz**. Przejdź do [Asana przewodnik konfigurowania logowania jednokrotnego](https://asana.com/guide/help/premium/authentication#gl-saml) Jeśli potrzebujesz dodatkowej pomocy.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Przycisk usługi Azure Active Directory](./media/asana-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/asana-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/asana-tutorial/create_aaduser_03.png)

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:

    ![Przycisk Dodaj](./media/asana-tutorial/create_aaduser_04.png)

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.

### <a name="create-an-asana-test-user"></a>Tworzenie użytkownika testowego w usłudze Asana

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w usłudze Asana. Asana obsługuje automatyczna aprowizacja użytkowników, która jest domyślnie włączona. Więcej szczegółów można znaleźć [tutaj](asana-provisioning-tutorial.md) dotyczące sposobu konfigurowania automatycznej aprowizacji użytkowników.

**Jeśli potrzebujesz ręcznie utworzyć użytkownika, wykonaj poniższe czynności:**

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w usłudze Asana.

1. Na **Asana**, przejdź do **zespoły** części w panelu po lewej stronie. Kliknij przycisk ze znakiem plus.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/asana-tutorial/tutorial_asana_12.png)

1. Wpisz adres e-mail britta.simon@contoso.com w polu tekstowym, a następnie wybierz **zaprosić**.

1. Kliknij przycisk **Wyślij zaproszenie**. Nowy użytkownik otrzyma wiadomość e-mail do swojego konta poczty e-mail. Będzie potrzebuje do tworzenia i sprawdzania poprawności konta.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon korzystać z platformy Azure logowania jednokrotnego przez udzielenie dostępu w usłudze Asana.

![Przypisanie roli użytkownika][200]

**Aby przypisać Britta Simon Asana, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

1. Na liście aplikacji wybierz **Asana**.

    ![Link Asana, na liście aplikacji](./media/asana-tutorial/tutorial_asana_app.png)

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest testowanie usługi Azure AD logowania jednokrotnego.

Przejdź do strony logowania w usłudze Asana. W polu tekstowym adresu E-mail, Wstaw adres e-mail britta.simon@contoso.com. Pozostaw pole tekstowe z hasłem pusty, a następnie kliknij przycisk **logowanie**. Nastąpi przekierowanie do strony logowania usługi Azure AD. Ukończ poświadczeń usługi Azure AD. Teraz użytkownik jest zalogowany usłudze Asana.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie Aprowizowania użytkowników](asana-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/asana-tutorial/tutorial_general_01.png
[2]: ./media/asana-tutorial/tutorial_general_02.png
[3]: ./media/asana-tutorial/tutorial_general_03.png
[4]: ./media/asana-tutorial/tutorial_general_04.png

[100]: ./media/asana-tutorial/tutorial_general_100.png

[200]: ./media/asana-tutorial/tutorial_general_200.png
[201]: ./media/asana-tutorial/tutorial_general_201.png
[202]: ./media/asana-tutorial/tutorial_general_202.png
[203]: ./media/asana-tutorial/tutorial_general_203.png
[10]: ./media/asana-tutorial/tutorial_general_060.png
[11]: ./media/asana-tutorial/tutorial_general_070.png
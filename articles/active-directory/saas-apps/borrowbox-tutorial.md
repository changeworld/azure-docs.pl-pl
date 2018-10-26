---
title: 'Samouczek: Integracja usługi Azure Active Directory z BorrowBox | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i BorrowBox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: dd8e4178-9a63-492a-bd48-782e94e404af
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: jeedes
ms.openlocfilehash: 654af4a3512f1e2acab5b0edf9e1aa4620ea70b4
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093075"
---
# <a name="tutorial-azure-active-directory-integration-with-borrowbox"></a>Samouczek: Integracja usługi Azure Active Directory z BorrowBox

W tym samouczku dowiesz się, jak zintegrować BorrowBox w usłudze Azure Active Directory (Azure AD).

Integrowanie BorrowBox z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do BorrowBox.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do BorrowBox (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą BorrowBox, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- BorrowBox logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie BorrowBox z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-borrowbox-from-the-gallery"></a>Dodawanie BorrowBox z galerii
Aby skonfigurować integrację BorrowBox w usłudze Azure AD, należy dodać BorrowBox z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać BorrowBox z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![image](./common/selectazuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![image](./common/a_select_app.png)
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![image](./common/a_new_app.png)

4. W polu wyszukiwania wpisz **BorrowBox**, wybierz opcję **BorrowBox** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![image](./media/borrowbox-tutorial/tutorial_borrowbox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą BorrowBox w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w BorrowBox do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w BorrowBox musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą BorrowBox, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego BorrowBox](#create-a-borrowbox-test-user)**  — aby odpowiednikiem Britta Simon w BorrowBox połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji BorrowBox.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z BorrowBox, wykonaj następujące czynności:**

1. W [witryny Azure portal](https://portal.azure.com/)na **BorrowBox** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![image](./common/B1_B2_Select_SSO.png)

2. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, wybierz **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![image](./common/b1_b2_saml_sso.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** przycisk, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![image](./common/b1-domains_and_urlsedit.png)

4. Na **podstawową konfigurację protokołu SAML** sekcji użytkownik nie musiał wykonać każdy krok, ponieważ aplikacja już jest wstępnie zintegrowana z platformą Azure.

    ![image](./media/borrowbox-tutorial/tutorial_borrowbox_url.png)

    a. Kliknij przycisk **Ustaw dodatkowe adresy URL**.

    b. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://fe.bolindadigital.com/wldcs_bol_fo/b2i/mainPage.html?b2bSite=<ID>`

    ![image](./media/borrowbox-tutorial/tutorial_borrowbox_url1.png)

    > [!NOTE]
    > Wartość adres URL logowania nie jest prawdziwe. Zaktualizuj wartość za pomocą adresu URL logowania rzeczywistych. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta BorrowBox](mailto:borrowbox@bolinda.com) można uzyskać wartość.

5. Aplikacja BorrowBox oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Możesz zarządzać wartości te atrybuty z **atrybutów użytkowników i oświadczeń** sekcji na stronie integracji aplikacji. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** przycisk, aby otworzyć **atrybutów użytkowników i oświadczeń** okna dialogowego.

    ![image](./media/borrowbox-tutorial/i4-attribute.png)

6. W **oświadczenia użytkownika** sekcji na **atrybutów użytkowników i oświadczeń** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:
    
    a. Kliknij pozycję **ikonę edycji** otworzyć **Zarządzanie oświadczenia użytkownika** okna dialogowego.

    ![image](./media/borrowbox-tutorial/i2-attribute.png)

    ![image](./media/borrowbox-tutorial/i3-attribute.png)

    b. Z **atrybut źródłowy** listy wybierz **user.mail**.

    c. Kliknij pozycję **Zapisz**. 

7. Na **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML** strony w **certyfikat podpisywania SAML** , kliknij przycisk **Pobierz** można pobrać odpowiedni certyfikat zgodnie z wymagania i zapisz go na komputerze.

    ![image](./media/borrowbox-tutorial/tutorial_borrowbox_certificate.png) 

8. Aby skonfigurować logowanie jednokrotne na **BorrowBox** stronie, musisz wysłać certyfikatu/metadanych, który został pobrany z witryny Azure portal do [zespołem pomocy technicznej BorrowBox](mailto:borrowbox@bolinda.com). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.

    ![image](./common/d_users_and_groups.png)

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![image](./common/d_adduser.png)

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![image](./common/d_userproperties.png)

    a. W **nazwa** pola wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-a-borrowbox-test-user"></a>Tworzenie użytkownika testowego BorrowBox

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w BorrowBox. BorrowBox obsługę just-in-time, który jest domyślnie włączona. Brak elementu akcji dla Ciebie w tej sekcji. Nowy użytkownik jest tworzony podczas próby dostępu BorrowBox, jeśli go jeszcze nie istnieje.
>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej BorrowBox](mailto:borrowbox@bolinda.com).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do BorrowBox.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![image](./common/d_all_applications.png)

2. Na liście aplikacji wybierz **BorrowBox**.

    ![image](./media/borrowbox-tutorial/tutorial_borrowbox_app.png)

3. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![image](./common/d_leftpaneusers.png)

4. Wybierz **Dodaj** przycisk, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![image](./common/d_assign_user.png)

4. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

5. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka BorrowBox w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji BorrowBox.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

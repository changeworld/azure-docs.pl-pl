---
title: 'Samouczek: Integracja usługi Azure Active Directory z łącznikiem sieci Meta | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Meta sieci łącznika.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4ae5f30d-113b-4261-b474-47ffbac08bf7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2018
ms.author: jeedes
ms.openlocfilehash: a3f40624e51ef287d70bed547eba7ec9e0882b0e
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2018
ms.locfileid: "43669199"
---
# <a name="tutorial-azure-active-directory-integration-with-meta-networks-connector"></a>Samouczek: Integracja usługi Azure Active Directory z łącznikiem sieci Meta

W tym samouczku dowiesz się, jak zintegrować Meta sieci łącznika z usługą Azure Active Directory (Azure AD).

Integrowanie Meta sieci łącznika z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do gniazda sieci Meta.
- Użytkowników, aby automatycznie uzyskać zalogowanych do łącznika sieci Meta (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą łącznika sieci Meta, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Łącznik sieci Meta logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodanie metadanych sieci łącznika z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-meta-networks-connector-from-the-gallery"></a>Dodanie metadanych sieci łącznika z galerii
Aby skonfigurować integrację Meta sieci łącznika do usługi Azure AD, należy dodać Meta sieci łącznika z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Meta sieci łącznika z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 
    
    ![Przycisk usługi Azure Active Directory][1]
    
1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.
    
    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.
    
    ![Nowy przycisk aplikacji][3]
    
1. W polu wyszukiwania wpisz **Meta sieci łącznika**, wybierz opcję **Meta sieci łącznika** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.
    
    ![Meta łącznika sieci na liście wyników](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą łącznika sieci Meta oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w łączniku sieci Meta do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w łączniku sieci Meta musi nawiązać.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą łącznika sieci Meta, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego łącznika sieci Meta](#create-a-meta-networks-connector-test-user)**  — aby odpowiednikiem Britta Simon w Meta sieci łącznik, który jest połączony z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Meta sieci łącznika.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne za pomocą łącznika sieci Meta, wykonaj następujące czynności:**

1. W witrynie Azure portal na **łącznika sieci Meta** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_samlbase.png)

1. Na **Meta sieci łącznika domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Meta sieci łącznika domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_url.png)

    1. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/saml/metadata`
    
    1. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/sso/saml`
    
1. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Meta sieci łącznika domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_url1.png)

    1. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/login`
    
    1. W **tan przekaźnika** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/#/`
    
    > [!NOTE]
    > Te wartości są prawdziwe. Zaktualizuj te wartości identyfikatorem rzeczywisty adres URL odpowiedzi, a adres URL logowania są wyjaśnione w dalszej części tego samouczka.
    
1. Aplikacja łącznika sieci meta oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Możesz zarządzać wartości te atrybuty z **atrybutów użytkownika** sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego.
    
    ![Konfigurowanie logowania jednokrotnego](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_attribute.png)
    
1. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu | Przestrzeń nazw|
    | ---------------| --------------- | -------- |
    | Imię | user.givenname | |
    | nazwisko | user.surname | |
    | emailaddress| User.mail| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | name | user.userprincipalname| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | telefon | User.telephonenumber | |

    1. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

        ![Konfigurowanie logowania jednokrotnego](./media/metanetworksconnector-tutorial/tutorial_attribute_04.png)
    
        ![Konfigurowanie logowania jednokrotnego](./media/metanetworksconnector-tutorial/tutorial_attribute_05.png)   
    
    1. W **nazwa** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.
    
    1. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.
    
    1. W **Namespace** polu tekstowym wpisz wartość przestrzeni nazw wyświetlanego dla tego wiersza.
    
    1. Kliknij przycisk **Ok**
    
1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.
    
    ![Link pobierania certyfikatu](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_certificate.png)
    
1. Na **konfiguracji łącznika sieci Meta** , kliknij przycisk **skonfiguruj łącznik sieci Meta** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego adres URL usługi** z **krótki przewodnik po sekcji.**
    
    ![Konfigurowanie logowania jednokrotnego](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_configure.png)
    
1. Kliknij przycisk **Zapisz** przycisku.
    
    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/metanetworksconnector-tutorial/tutorial_general_400.png)
    
1. Otwórz nową kartę w przeglądarce i zaloguj się do konta administratora łącznika sieci Meta.
    
    > [!NOTE]
    > Łącznik sieci metadanych jest bezpieczny system. Dlatego przed uzyskaniem dostępu do ich portalu należy uzyskać swoje publicznego adresu IP adres na liście dozwolonych na bok. Aby uzyskać publicznego adresu IP, wykonaj Poniższy link określony [tutaj](https://whatismyipaddress.com/). Wyślij swój adres IP, aby [zespołem pomocy technicznej klienta łącznika sieci Meta](mailto:support@metanetworks.com) można pobrać z adresu IP, na liście dozwolonych.
    
1. Przejdź do **administratora** i wybierz **ustawienia**.
    
    ![Konfigurowanie logowania jednokrotnego](./media/metanetworksconnector-tutorial/configure3.png)
    
1. Upewnij się, że **dziennika ruchu internetowego** i **MFA VPN życie** są ustawione na off.
    
    ![Konfigurowanie logowania jednokrotnego](./media/metanetworksconnector-tutorial/configure1.png)
    
1. Przejdź do **administratora** i wybierz **SAML**.
    
    ![Konfigurowanie logowania jednokrotnego](./media/metanetworksconnector-tutorial/configure4.png)
    
1. Wykonaj następujące czynności na **szczegóły** strony:
    
    ![Konfigurowanie logowania jednokrotnego](./media/metanetworksconnector-tutorial/configure2.png)
    
    1. Kopiuj **adres URL logowania jednokrotnego** wartość i wklej go w **adres URL logowania** polu tekstowym w **Meta sieci łącznika domena i adresy URL** sekcji.
    
    1. Kopiuj **adres URL odbiorcy** wartość i wklej go w **adres URL odpowiedzi** polu tekstowym w **Meta sieci łącznika domena i adresy URL** sekcji.
    
    1. Kopiuj **identyfikator URI odbiorców (identyfikator jednostki SP)** wartość i wklej go w **identyfikator jednostki** polu tekstowym w **Meta sieci łącznika domena i adresy URL** sekcji.
    
    1. Włącz SAML
    
1. Na **ogólne** kartę, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/metanetworksconnector-tutorial/configure5.png)

    1. W **pojedynczy znak na adres URL dostawcy tożsamości**, Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL** wartości, które zostały skopiowane z witryny Azure portal.

    1. W **wystawca dostawcy tożsamości**, Wklej **identyfikator jednostki SAML** wartości, które zostały skopiowane z witryny Azure portal.

    1. Otwórz certyfikat pobrany z witryny Azure portal w programie Notatnik, wklej go do **certyfikat X.509** pola tekstowego.

    1. Włącz **aprowizacji Just-in-Time**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.
    
![Tworzenie użytkownika testowego usługi Azure AD][100]
    
**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.
    
    ![Przycisk usługi Azure Active Directory](./media/metanetworksconnector-tutorial/create_aaduser_01.png)
    
1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.
    
    !["Użytkownicy i grupy" i "All users" linki](./media/metanetworksconnector-tutorial/create_aaduser_02.png)
    
1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.
    
    ![Przycisk Dodaj](./media/metanetworksconnector-tutorial/create_aaduser_03.png)
    
1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/metanetworksconnector-tutorial/create_aaduser_04.png)
    
    1. W **nazwa** wpisz **BrittaSimon**.

    1. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.
    
    1. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.
    
    1. Kliknij pozycję **Utwórz**.
    
### <a name="create-a-meta-networks-connector-test-user"></a>Tworzenie użytkownika testowego Meta sieci łącznika

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w łączniku sieci Meta. Łącznik sieci meta obsługę just-in-time, który jest domyślnie włączona. Brak elementu akcji dla Ciebie w tej sekcji. Nowy użytkownik jest tworzony podczas próby dostępu Meta sieci Connector, jeśli go jeszcze nie istnieje.

>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej klienta łącznika sieci Meta](mailto:support@metanetworks.com).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do metadanych sieci łącznika.

![Przypisanie roli użytkownika][200]

**Aby przypisać Britta Simon Meta sieci łącznika, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.
    
    ![Przypisz użytkownika][201]
    
1. Na liście aplikacji wybierz **łącznika sieci Meta**.
    
    ![Link Meta sieci łącznika na liście aplikacji](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_app.png)  
    
1. W menu po lewej stronie kliknij **użytkowników i grup**.
    
    ![Link "Użytkownicy i grupy"][202]
    
1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.
    
    ![Okienko Dodawanie przypisania][203]
    
1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.
    
1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.
    
1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Meta sieci łącznika w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do Meta sieci łącznika aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/metanetworksconnector-tutorial/tutorial_general_01.png
[2]: ./media/metanetworksconnector-tutorial/tutorial_general_02.png
[3]: ./media/metanetworksconnector-tutorial/tutorial_general_03.png
[4]: ./media/metanetworksconnector-tutorial/tutorial_general_04.png

[100]: ./media/metanetworksconnector-tutorial/tutorial_general_100.png

[200]: ./media/metanetworksconnector-tutorial/tutorial_general_200.png
[201]: ./media/metanetworksconnector-tutorial/tutorial_general_201.png
[202]: ./media/metanetworksconnector-tutorial/tutorial_general_202.png
[203]: ./media/metanetworksconnector-tutorial/tutorial_general_203.png


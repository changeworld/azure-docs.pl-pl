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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c4d702a33742da5501be208154cbdd593fa0524
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56199718"
---
# <a name="tutorial-azure-active-directory-integration-with-meta-networks-connector"></a>Samouczek: Integracja usługi Azure Active Directory z łącznikiem sieci Meta

W tym samouczku dowiesz się, jak zintegrować Meta sieci łącznika z usługą Azure Active Directory (Azure AD).

Integrowanie Meta sieci łącznika z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do gniazda sieci Meta.
- Użytkowników, aby automatycznie uzyskać zalogowanych do łącznika sieci Meta (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą łącznika sieci Meta, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Łącznik sieci Meta logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
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
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.
    
    ![Przycisk Nowa aplikacja][3]
    
1. W polu wyszukiwania wpisz **Meta sieci łącznika**, wybierz opcję **Meta sieci łącznika** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.
    
    ![Meta łącznika sieci na liście wyników](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą łącznika sieci Meta oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w łączniku sieci Meta do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w łączniku sieci Meta musi nawiązać.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą łącznika sieci Meta, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **[Tworzenie użytkownika testowego łącznika sieci Meta](#create-a-meta-networks-connector-test-user)**  — aby odpowiednikiem Britta Simon w Meta sieci łącznik, który jest połączony z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Meta sieci łącznika.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne za pomocą łącznika sieci Meta, wykonaj następujące czynności:**

1. W witrynie Azure portal na **łącznika sieci Meta** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_samlbase.png)

1. Na **Meta sieci łącznika domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Meta sieci łącznika domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_url.png)

    1. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/saml/metadata`
    
    1. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/sso/saml`
    
1. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Meta sieci łącznika domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_url1.png)

    1. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/login`
    
    1. W **tan przekaźnika** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/#/`
    
    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości identyfikatorem rzeczywisty adres URL odpowiedzi, a adres URL logowania są wyjaśnione w dalszej części tego samouczka.
    
1. Aplikacja łącznika sieci meta oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego działania.
    
    ![Konfigurowanie logowania jednokrotnego](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_attribute.png)
    
1. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu | Przestrzeń nazw|
    | ---------------| --------------- | -------- |
    | firstname | user.givenname | |
    | lastname | user.surname | |
    | emailaddress| user.mail| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | name | user.userprincipalname| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | phone | user.telephonenumber | |

    1. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

        ![Konfigurowanie logowania jednokrotnego](./media/metanetworksconnector-tutorial/tutorial_attribute_04.png)
    
        ![Konfigurowanie logowania jednokrotnego](./media/metanetworksconnector-tutorial/tutorial_attribute_05.png)   
    
    1. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.
    
    1. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.
    
    1. W polu tekstowym **Przestrzeń nazw** wpisz wartość przestrzeni nazw pokazaną dla tego wiersza.
    
    1. Kliknij przycisk **OK**.
    
1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.
    
    ![Link do pobierania certyfikatu](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_certificate.png)
    
1. Na **konfiguracji łącznika sieci Meta** , kliknij przycisk **skonfiguruj łącznik sieci Meta** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego adres URL usługi** z **krótki przewodnik po sekcji.**
    
    ![Konfigurowanie logowania jednokrotnego](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_configure.png)
    
1. Kliknij przycisk **Save** (Zapisz).
    
    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/metanetworksconnector-tutorial/tutorial_general_400.png)
    
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

    ![Okno dialogowe Użytkownik](./media/metanetworksconnector-tutorial/create_aaduser_04.png)
    
    1. W **nazwa** wpisz **BrittaSimon**.

    1. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.
    
    1. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.
    
    1. Kliknij pozycję **Utwórz**.
    
### <a name="create-a-meta-networks-connector-test-user"></a>Tworzenie użytkownika testowego Meta sieci łącznika

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w łączniku sieci Meta. Łącznik sieci meta obsługę just-in-time, który jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Nowy użytkownik jest tworzony podczas próby dostępu Meta sieci Connector, jeśli go jeszcze nie istnieje.

>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej klienta łącznika sieci Meta](mailto:support@metanetworks.com).

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


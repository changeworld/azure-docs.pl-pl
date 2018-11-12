---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługi FreshDesk | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i usługi FreshDesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2018
ms.author: jeedes
ms.openlocfilehash: b5968b83fc9beb481e2ad2c0cd44d2c284747fa1
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2018
ms.locfileid: "51010817"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Samouczek: Integracja usługi Azure Active Directory z usługi FreshDesk

W tym samouczku dowiesz się, jak zintegrować usługi FreshDesk w usłudze Azure Active Directory (Azure AD).

Integrowanie usługi FreshDesk z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do usługi FreshDesk.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do usługi FreshDesk (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługi FreshDesk, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Usługi FreshDesk logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie usługi FreshDesk z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-freshdesk-from-the-gallery"></a>Dodawanie usługi FreshDesk z galerii

Aby skonfigurować integrację z usługi FreshDesk w usłudze Azure AD, należy dodać usługi FreshDesk z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać usługi FreshDesk z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **usługi FreshDesk**, wybierz opcję **usługi FreshDesk** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Usługi FreshDesk na liście wyników](./media/freshdesk-tutorial/tutorial_freshdesk_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą usługi FreshDesk w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w usłudze FreshDesk z użytkownikiem w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w usłudze FreshDesk musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego z usługi FreshDesk, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego usługi FreshDesk](#creating-a-freshdesk-test-user)**  — odpowiednikiem Britta Simon znajdują się w usłudze FreshDesk połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji usługi FreshDesk.

**Aby skonfigurować usługę Azure AD logowania jednokrotnego z usługi FreshDesk, wykonaj następujące czynności:**

1. W witrynie Azure portal na **usługi FreshDesk** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **wybierz** dla **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](common/tutorial_general_301.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** ikonę, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](common/editconfigure.png)

4. Na **podstawową konfigurację protokołu SAML** sekcji, wykonaj następujące czynności:

    ![Adresy URL i domen usługi FreshDesk pojedynczego logowania jednokrotnego informacji](./media/freshdesk-tutorial/tutorial_freshdesk_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<tenant-name>.freshdesk.com` lub dowolna inna wartość proponuje usługi Freshdesk.

    b. W **identyfikator jednostki** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<tenant-name>.freshdesk.com` lub dowolna inna wartość proponuje usługi Freshdesk.

    > [!NOTE]
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania jednokrotnego i identyfikator. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta usługi FreshDesk](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) do uzyskania tych wartości.

5. Aplikacja usługi FreshDesk oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Poniższy zrzut ekranu przedstawia przykład tego. Wartość domyślna **identyfikator użytkownika** jest **user.userprincipalname** , ale **usługi FreshDesk** oczekuje, że to mają być mapowane z adresem e-mail użytkownika. W przypadku którego można użyć **user.mail** atrybutu z listy lub użyj wartości odpowiedni atrybut, na podstawie konfiguracji organizacji.

    ![image](./media/freshdesk-tutorial/i4-attribute.png)

6. W **oświadczenia użytkownika** sekcji na **atrybutów użytkowników i oświadczeń** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:
    
    a. Kliknij pozycję **ikonę edycji** otworzyć **Zarządzanie oświadczenia użytkownika** okna dialogowego.

    ![image](./media/freshdesk-tutorial/i2-attribute.png)

    ![image](./media/freshdesk-tutorial/i3-attribute.png)

    b. Z **atrybut źródłowy** listy wybierz **user.mail**.

    c. Kliknij pozycję **Zapisz**.

7. Na **certyfikat podpisywania SAML** strony w **certyfikat podpisywania SAML** kliknij **Pobierz** można pobrać **certyfikat (Base64)**, a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/freshdesk-tutorial/tutorial_freshdesk_certificate.png)

8. Otwórz **polecenia** i uruchom następujące polecenia:

    a. Wprowadź `certutil.exe -dump FreshDesk.cer` wartości w wierszu polecenia.

    > [!NOTE]
    > W tym miejscu **FreshDesk.cer** to certyfikat, który został pobrany z witryny Azure portal.

    b. Kopiuj **Cert Hash(sha256)** wartość i wklej go w Notatniku. 

9. Na **Konfigurowanie usługi FreshDesk** sekcji, skopiuj odpowiedni adres URL, zgodnie z wymaganiami.

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

    ![Konfiguracja usługi FreshDesk](common/configuresection.png)

10. W oknie przeglądarki internetowej innej Zaloguj się do usługi Freshdesk firmowa witryna, jako administrator.

11. Wybierz **ikonę ustawienia** i **zabezpieczeń** sekcji, wykonaj następujące czynności:

    ![Logowanie jednokrotne](./media/freshdesk-tutorial/IC776770.png "logowanie jednokrotne")
  
    a. Aby uzyskać **logowanie jednokrotne (SSO)**, wybierz opcję **na**.

    b. Wybierz **logowania jednokrotnego SAML**.

    c. W **adres URL logowania języka SAML** pola tekstowego, Wklej **adres URL logowania** wartości, które zostały skopiowane z witryny Azure portal.

    d. W **adres URL wylogowania** pola tekstowego, Wklej **adres URL wylogowania** wartości, które zostały skopiowane z witryny Azure portal.

    e. W **odcisk palca certyfikatu zabezpieczeń** pola tekstowego, Wklej **Cert Hash(sha256)** wartość, która ma zostać uzyskany wcześniej.
  
    f. Kliknij pozycję **Zapisz**.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.

    ![Utwórz użytkownika usługi Azure AD][100]

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![Tworzenie użytkownika testowego usługi Azure AD](common/create_aaduser_01.png) 

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![Tworzenie użytkownika testowego usługi Azure AD](common/create_aaduser_02.png)

    a. W **nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.

### <a name="creating-a-freshdesk-test-user"></a>Tworzenie użytkownika testowego usługi FreshDesk

Aby umożliwić użytkownikom usługi Azure AD zalogować się do usługi FreshDesk, musi być obsługiwana w usłudze FreshDesk.  
W przypadku usługi FreshDesk Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby udostępnić konta użytkowników, wykonaj następujące czynności:**

1. Zaloguj się do Twojej **usługi Freshdesk** dzierżawy.

2. W menu u góry kliknij **administratora**.

    ![Administrator](./media/freshdesk-tutorial/IC776772.png "administratora")

3. W **ustawienia ogólne** kliknij pozycję **agentów**.
  
    ![Agenci](./media/freshdesk-tutorial/IC776773.png "agentów")

4. Kliknij przycisk **nowy Agent**.

    ![Nowy Agent](./media/freshdesk-tutorial/IC776774.png "nowego agenta")

5. W oknie dialogowym informacji o agencie wykonaj następujące czynności:

    ![Informacji o agencie](./media/freshdesk-tutorial/IC776775.png "informacji o agencie")

    a. W **E-mail** polu tekstowym wpisz usługi Azure AD adres e-mail konta usługi Azure AD, do aprowizowania.

    b. W **imię i nazwisko** polu tekstowym wpisz nazwę konta usługi Azure AD, do aprowizowania.

    c. W **tytuł** polu tekstowym wpisz nazwę konta usługi Azure AD, do aprowizowania.

    d. Kliknij pozycję **Zapisz**.

    >[!NOTE]
    >Właściciel konta usługi Azure AD otrzyma wiadomość e-mail zawierającą link do potwierdzenia konta, zanim zostanie aktywowany.
    >
    >[!NOTE]
    >Można użyć jakichkolwiek innych usługi Freshdesk użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez usługi Freshdesk do aprowizacji kont użytkowników usługi AAD do usługi FreshDesk.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do usługi FreshDesk.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **usługi FreshDesk**.

    ![Konfigurowanie logowania jednokrotnego](./media/freshdesk-tutorial/tutorial_freshdesk_app.png)

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

6. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.

### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi FreshDesk w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji usługi FreshDesk.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png

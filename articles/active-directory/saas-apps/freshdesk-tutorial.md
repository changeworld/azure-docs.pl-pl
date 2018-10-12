---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługi FreshDesk | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i usługi FreshDesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 3f640d729d002e89c4968442e8d8ab761f78d090
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2018
ms.locfileid: "49115168"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Samouczek: Integracja usługi Azure Active Directory z usługi FreshDesk

W tym samouczku dowiesz się, jak zintegrować usługi FreshDesk w usłudze Azure Active Directory (Azure AD).

Integrowanie usługi FreshDesk z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do usługi FreshDesk
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do usługi FreshDesk (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługi FreshDesk, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Usługi FreshDesk logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym.
Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie usługi FreshDesk z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-freshdesk-from-the-gallery"></a>Dodawanie usługi FreshDesk z galerii

Aby skonfigurować integrację z usługi FreshDesk w usłudze Azure AD, należy dodać usługi FreshDesk z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać usługi FreshDesk z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]

3. Kliknij przycisk **Dodaj** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **usługi FreshDesk**. Wybierz **usługi FreshDesk** z panel wyników, a następnie wybierz **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/freshdesk-tutorial/tutorial_freshdesk_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą usługi FreshDesk w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w usłudze FreshDesk z użytkownikiem w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w usłudze FreshDesk musi zostać ustanowione.

Ustanowieniu tej relacji łączy, przypisując wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **adres e-mail** w usłudze FreshDesk.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego z usługi FreshDesk, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego usługi FreshDesk](#creating-a-freshdesk-test-user)**  — odpowiednikiem Britta Simon znajdują się w usłudze FreshDesk połączonego z usługi Azure AD reprezentacja jej.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji usługi FreshDesk.

**Aby skonfigurować usługę Azure AD logowania jednokrotnego z usługi FreshDesk, wykonaj następujące czynności:**

1. W witrynie Azure portal na **usługi FreshDesk** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, jako **tryb** wybierz **opartej na SAML logowania jednokrotnego** na włączanie logowania jednokrotnego.

    ![Konfigurowanie logowania jednokrotnego](./media/freshdesk-tutorial/tutorial_freshdesk_samlbase.png)

3. Na **usługi FreshDesk domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/freshdesk-tutorial/tutorial_freshdesk_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<tenant-name>.freshdesk.com` lub dowolna inna wartość proponuje usługi Freshdesk.

    > [!NOTE]
    > Należy pamiętać, że nie jest rzeczywistą wartość. Należy zaktualizować wartość z adresem URL rzeczywistej logowania jednokrotnego. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta usługi FreshDesk](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) aby zyskać tę wartość.

4. Aplikacja oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Poniższy zrzut ekranu przedstawia przykład tego. Wartość domyślna **identyfikator użytkownika** jest **user.userprincipalname** , ale **usługi FreshDesk** oczekuje, że to mają być mapowane z adresem e-mail użytkownika. W przypadku którego można użyć **user.mail** atrybutu z listy lub użyj wartości odpowiedni atrybut, na podstawie konfiguracji organizacji.

    ![Konfigurowanie logowania jednokrotnego](./media/freshdesk-tutorial/tutorial_attribute.png)

5. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/freshdesk-tutorial/tutorial_freshdesk_certificate.png)

    > [!NOTE]
    > Jeśli masz problemy, zapoznaj się to [łącze](https://support.freshdesk.com/support/discussions/topics/317543).

6. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/freshdesk-tutorial/tutorial_general_400.png)

7. Zainstaluj **OpenSSL** w systemie, jeśli nie jest zainstalowany w systemie.

8. Otwórz **polecenia** i uruchom następujące polecenia:

    a. Wprowadź `openssl x509 -inform DER -in FreshDesk.cer -out certificate.crt` wartości w wierszu polecenia.

    > [!NOTE]
    > W tym miejscu **FreshDesk.cer** to certyfikat, który został pobrany z witryny Azure portal.

    b. Wprowadź `openssl x509 -noout -fingerprint -sha256 -inform pem -in certificate.crt` wartości w wierszu polecenia. 
    
    > [!NOTE]
    > W tym miejscu **certificate.crt** jest certyfikat wyjściowy, który jest generowany w poprzednim kroku.

    c. Kopiuj **odcisk palca** wartość i wklej go w Notatniku. Usuń dwukropki z odciskiem palca, a następnie Uzyskaj końcowa wartość odcisku palca.

9. Na **konfiguracji usługi FreshDesk** kliknij **skonfigurować usługi FreshDesk** otworzyć Konfigurowanie logowania jednokrotnego okno. Skopiuj SAML pojedynczego logowania jednokrotnego adres URL usługi i adres URL wylogowania **krótki** sekcji.

    ![Konfigurowanie logowania jednokrotnego](./media/freshdesk-tutorial/tutorial_freshdesk_configure.png)

10. W oknie przeglądarki internetowej innej Zaloguj się do usługi Freshdesk firmowa witryna, jako administrator.

11. Wybierz **ikonę ustawienia** i **zabezpieczeń** sekcji, wykonaj następujące czynności:

    ![Logowanie jednokrotne](./media/freshdesk-tutorial/IC776770.png "logowanie jednokrotne")
  
    a. Aby uzyskać **logowanie jednokrotne (SSO)**, wybierz opcję **na**.

    b. Wybierz **logowania jednokrotnego SAML**.

    c. W **adres URL logowania języka SAML** pola tekstowego, Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL** wartości, które zostały skopiowane z witryny Azure portal.

    d. W **adres URL wylogowania** pola tekstowego, Wklej **adres URL wylogowania** wartości, które zostały skopiowane z witryny Azure portal.

    e. W **odcisk palca certyfikatu zabezpieczeń** pola tekstowego, Wklej **odcisk palca** wartość, której uzyskano wcześniej, po usunięciu dwukropki.
  
    f. Kliknij pozycję **Zapisz**.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/freshdesk-tutorial/create_aaduser_01.png) 

2. Przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy** do wyświetlania listy użytkowników.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/freshdesk-tutorial/create_aaduser_02.png) 

3. W górnej części okna dialogowego kliknij **Dodaj** otworzyć **użytkownika** okna dialogowego.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/freshdesk-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/freshdesk-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.

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

W tej sekcji możesz włączyć Britta Simon do użycia platformy Azure logowania jednokrotnego przez udostępnienie jej do pola.

![Przypisz użytkownika][200]

**Aby przypisać Britta Simon usługi FreshDesk, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **usługi FreshDesk**.

    ![Konfigurowanie logowania jednokrotnego](./media/freshdesk-tutorial/tutorial_freshdesk_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.

### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi FreshDesk w panelu dostępu, należy uzyskać strony logowania, aby pobrać zalogowanych do aplikacji usługi FreshDesk.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/freshdesk-tutorial/tutorial_general_01.png
[2]: ./media/freshdesk-tutorial/tutorial_general_02.png
[3]: ./media/freshdesk-tutorial/tutorial_general_03.png
[4]: ./media/freshdesk-tutorial/tutorial_general_04.png

[100]: ./media/freshdesk-tutorial/tutorial_general_100.png

[200]: ./media/freshdesk-tutorial/tutorial_general_200.png
[201]: ./media/freshdesk-tutorial/tutorial_general_201.png
[202]: ./media/freshdesk-tutorial/tutorial_general_202.png
[203]: ./media/freshdesk-tutorial/tutorial_general_203.png
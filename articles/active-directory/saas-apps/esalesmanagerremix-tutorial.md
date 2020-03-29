---
title: 'Samouczek: Integracja usługi Azure Active Directory z programem E Sales Manager Remix | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a programem E Sales Manager Remix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 89b5022c-0d5b-4103-9877-ddd32b6e1c02
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 895fb0d83e383618818325263ac80c5919a0ee7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65406948"
---
# <a name="integrate-azure-active-directory-with-e-sales-manager-remix"></a>Integracja usługi Azure Active Directory z programem E Sales Manager Remix

W tym samouczku dowiesz się, jak zintegrować usługę Azure Active Directory (Azure AD) z programem E Sales Manager Remix.

Integrując usługę Azure AD z E Sales Manager Remix, można uzyskać następujące korzyści:

- W usłudze Azure AD można kontrolować, kto ma dostęp do programu E Sales Manager Remix.
- Możesz włączyć użytkownikom automatyczne logowanie do E Sales Manager Remix (logowanie jednokrotne lub logowanie jednokrotne) za pomocą ich kont usługi Azure AD.
- Możesz zarządzać kontami z jednej centralnej lokalizacji — witryny Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z programem E Sales Manager Remix, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Subskrypcja z obsługą programu E Sales Manager z obsługą SSO

> [!NOTE]
> Podczas testowania kroków w tym samouczku, zaleca się, aby *nie* używać środowiska produkcyjnego.

Aby przetestować kroki w tym samouczku, musisz mieć dostęp do następujących elementów:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska usługi Azure AD w wersji próbnej, możesz skorzystać z [miesięcznej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku testujesz logowanie jednokrotne usługi Azure AD w środowisku testowym. 

Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

* Dodawanie E Sales Manager Remix z galerii
* Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

## <a name="add-e-sales-manager-remix-from-the-gallery"></a>Dodaj E Sales Manager Remix z galerii
Aby skonfigurować integrację usługi Azure AD z E Sales Manager Remix, dodaj E Sales Manager Remix z galerii do listy zarządzanych aplikacji SaaS, wykonując następujące czynności:

1. W [witrynie Azure Portal](https://portal.azure.com) w okienku po lewej stronie wybierz pozycję **Azure Active Directory**. 

    ![Przycisk Azure Active Directory][1]

1. Wybierz **aplikacje** > enterprise**Wszystkie aplikacje**.

    ![Okno "Aplikacje dla przedsiębiorstw"][2]
    
1. Aby dodać nową aplikację, wybierz **pozycję Nowa aplikacja** w górnej części okna.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **E Sales Manager Remix**, wybierz **pozycję E Sales Manager Remix** na liście wyników, a następnie wybierz pozycję **Dodaj**.

    ![E Sales Manager Remix na liście wyników](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego za pomocą E Sales Manager Remix, na podstawie użytkownika testowego o nazwie "Britta Simon".

Aby usługa Azure AD działała z logiem pojedynczym, musi zidentyfikować użytkownika programu E Sales Manager Remix i jego odpowiednika w usłudze Azure AD. Innymi słowy należy ustanowić relację łącza między użytkownikiem usługi Azure AD a tym samym użytkownikiem w E Sales Manager Remix.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą E Sales Manager Remix, wykonaj bloki konstrukcyjne w następnych pięciu sekcjach:

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

Włącz logowanie jednokrotne usługi Azure AD w portalu Azure i skonfiguruj logowanie jednokrotne w aplikacji E Sales Manager Remix, wykonując następujące czynności:

1. W witrynie Azure portal na stronie Integracja aplikacji **E Sales Manager remix** wybierz pozycję **Logowanie jednokrotne**.

    ![Łącze "Logowanie jednokrotne"][4]

1. W oknie **Logowania jednokrotnego w** polu **Tryb logowania jednokrotnego** wybierz pozycję **Logowanie oparte na SAML**.
 
    ![Okno "Logowanie jednokrotne"](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

1. W obszarze **E Sales Manager Remix Domain and URL**wykonaj następujące czynności:

    ![E Menedżer sprzedaży Remix Domeny i adresy URL — informacje o logowanie jednokrotnym](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. W polu **Adres URL logowania** wpisz adres URL w następującym formacie: https:// *\<adres URL\<oparty na serwerze>/ poddomeny>/esales-pc*.

    b. W polu **Identyfikator** wpisz adres URL w następującym formacie: *https://\<adres URL oparty\<na serwerze>/ poddomeny>/*.

    d. Zanotuj wartość **identyfikatora** do późniejszego użycia w tym samouczku.
    
    > [!NOTE] 
    > Podane wyżej wartości nie są rzeczywiste. Zaktualizuj je za pomocą rzeczywistego adresu URL logowania i identyfikatora. Aby uzyskać wartości, skontaktuj się z [zespołem pomocy technicznej E Sales Manager Remix Client](mailto:esupport@softbrain.co.jp).

1. W obszarze **Certyfikat podpisywania SAML**wybierz **pozycję Certyfikat (Base64),** a następnie zapisz plik certyfikatu na komputerze.

    ![Link do pobrania certyfikatu (Base64)](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

1. Zaznacz pole wyboru **Wyświetl i edytuj wszystkie inne atrybuty użytkownika,** a następnie wybierz atrybut adresu **e-mail.**
    
    ![Okno Atrybuty użytkownika](./media/esalesmanagerremix-tutorial/configure1.png)

    Zostanie otwarte okno **Edytuj atrybut.**

1. Skopiuj wartości **Obszaru nazw** i **Nazwa.** Wygeneruj wartość w * \<wzorcu\<>/ Name>* i zapisz ją do późniejszego użycia w tym samouczku.

    ![Okno Edytowanie atrybutu](./media/esalesmanagerremix-tutorial/configure2.png)

1. W obszarze **Konfiguracja remiksu E Sales Managera**wybierz pozycję **Konfiguruj E Sales Manager Remix**.

    ![Konfiguracja remiksu e Sales Managera](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

    Zostanie otwarte okno **Konfigurowanie logowania.**

1. W sekcji **Podręczna dokumentacja** skopiuj adres URL wylogowania i adres URL usługi logowania jednokrotnego SAML.

1. Wybierz **pozycję Zapisz**.

    ![Przycisk Zapisz](./media/esalesmanagerremix-tutorial/tutorial_general_400.png)

1. Zaloguj się do aplikacji E Sales Manager Remix jako administrator.

1. W prawym górnym rogu wybierz polecenie **Do menu administratora**.

    ![Polecenie "Do menu administratora"](./media/esalesmanagerremix-tutorial/configure4.png)

1. W lewym okienku wybierz **pozycję Ustawienia systemowe** > **Współpraca z systemem zewnętrznym**.

    ![Linki "Ustawienia systemowe" i "Współpraca z systemem zewnętrznym"](./media/esalesmanagerremix-tutorial/configure5.png)
    
1. W oknie **Współpraca z systemem zewnętrznym** wybierz **SAML**.

    ![Okno "Współpraca z systemem zewnętrznym"](./media/esalesmanagerremix-tutorial/configure6.png)

1. W obszarze **Ustawienie uwierzytelniania SAML**wykonaj następujące czynności:

    ![Sekcja "Ustawienie uwierzytelniania SAML"](./media/esalesmanagerremix-tutorial/configure3.png)
    
    a. Zaznacz pole wyboru **Wersja pc.**
    
    b. W sekcji **Element współpracy** na liście rozwijanej wybierz pozycję **Poczta e-mail**.

    d. W **polu Element współpracy** wklej wartość oświadczenia skopiowaną wcześniej z witryny **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**Azure Portal (czyli ).

    d. W polu **Wystawca (identyfikator jednostki)** wklej wartość identyfikatora skopiowaną wcześniej z sekcji **E Sales Manager Remix Domain and URL** w witrynie Azure portal.

    e. Aby przekazać pobrany certyfikat z witryny Azure portal, wybierz **pozycję Wybór pliku**.

    f. W polu **adresu URL logowania dostawcy identyfikatora** wklej adres URL usługi logowania jednokrotnego SAML skopiowany wcześniej w witrynie Azure portal.

    g. W polu **Adresu URL wylogowania dostawcy tożsamości** wklej wartość adresu URL wylogowania skopiowaną wcześniej w witrynie Azure portal.

    h. Wybierz **pozycję Ustawienie zakończone**.

> [!TIP]
> Podczas konfigurowania aplikacji można przeczytać zwięzłą wersję poprzednich instrukcji w [witrynie Azure portal](https://portal.azure.com). Po dodaniu aplikacji w sekcji**Aplikacje przedsiębiorstwa** **usługi Active Directory** > wybierz kartę **Logowanie jednokrotne,** a następnie uzyskaj dostęp do osadzonej dokumentacji w sekcji **Konfiguracja** u dołu. Aby uzyskać więcej informacji na temat osadzonej funkcji dokumentacji, zobacz [Dokumentacja osadzona usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego Britta Simon w witrynie Azure portal, wykonując następujące czynności:

![Tworzenie użytkownika testowego usługi Azure AD][100]

1. W witrynie Azure portal w lewym okienku wybierz pozycję **Azure Active Directory**.

    ![Łącze usługi Azure Active Directory](./media/paloaltoadmin-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę bieżących użytkowników, wybierz **pozycję Użytkownicy i grupy** > **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](./media/paloaltoadmin-tutorial/create_aaduser_02.png)

1. U góry okna **Wszyscy użytkownicy** wybierz pozycję **Dodaj**.

    ![Przycisk Dodaj](./media/paloaltoadmin-tutorial/create_aaduser_03.png)
    
    Zostanie otwarte okno **Użytkownik.**

1. W oknie **Użytkownik** wykonaj następujące czynności:

    ![Okno Użytkownik](./media/paloaltoadmin-tutorial/create_aaduser_04.png)

    a. W polu **Nazwa** wpisz **BrittaSimon**.

    b. W polu **Nazwa użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    d. Zaznacz pole wyboru **Pokaż hasło,** a następnie zanotuj wartość wyświetlaną w polu **Hasło.**

    d. Wybierz **pozycję Utwórz**.
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>Tworzenie użytkownika testowego E Sales Manager

1. Zaloguj się do aplikacji E Sales Manager Remix jako administrator.

1. Z menu w prawym górnym rogu wybierz **polecenie Do menu administratora.**

    ![Konfiguracja remiksu e Sales Managera](./media/esalesmanagerremix-tutorial/configure4.png)

1. Wybierz **ustawienia** > firmy**Konserwacja działów i pracowników,** a następnie wybierz **pozycję Zarejestrowani pracownicy.**

    ![Karta "Zarejestrowani pracownicy"](./media/esalesmanagerremix-tutorial/user1.png)

1. W sekcji **Nowa rejestracja pracowników** wykonaj następujące czynności:
    
    ![Sekcja "Nowa rejestracja pracowników"](./media/esalesmanagerremix-tutorial/user2.png)

    a. W polu **Nazwa pracownika** wpisz nazwę użytkownika (na przykład **Britta**).

    b. Uzupełnij pozostałe wymagane pola.
    
    d. Jeśli włączysz SAML, administrator nie może zalogować się ze strony logowania. Udziel użytkownikowi uprawnień logowania administratora, zaznaczając pole wyboru **Logowanie administratora.**

    d. Wybierz **pozycję Rejestracja**.

1. W przyszłości, aby zalogować się jako administrator, zaloguj się jako użytkownik, który ma uprawnienia administratora, a następnie, w prawym górnym rogu, wybierz opcję **Do menu administratora**.

    ![Polecenie "Do menu administratora"](./media/esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć użytkownika Britta Simon do korzystania z usługi Azure logowania jednokrotnego, udzielając dostępu do E Sales Manager Remix. Aby to zrobić, wykonaj następujące czynności: 

![Przypisywanie roli użytkownika][200] 

1. W witrynie Azure portal otwórz widok **Aplikacje,** przejdź do widoku **Katalog,** a następnie wybierz **pozycję Aplikacje** > Enterprise**Wszystkie aplikacje**.

    ![Linki "Aplikacje dla przedsiębiorstw" i "Wszystkie aplikacje"][201] 

1. Na liście **Aplikacje** wybierz **pozycję E Sales Manager Remix**.

    ![Link do Remiksu E Sales Manager](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

1. W lewym okienku wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”][202]

1. Wybierz **pozycję Dodaj,** a następnie w okienku **Dodawanie przydziału** wybierz pozycję **Użytkownicy i grupy**.

    ![Okienko Dodawanie przypisania][203]

1. W oknie **Użytkownicy i grupy** na liście **Użytkownicy** wybierz pozycję **Britta Simon**.

1. Wybierz przycisk **Wybierz.**

1. W oknie **Dodaj przypisanie** wybierz pozycję **Przypisz**.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po wybraniu kafelka E Sales Manager Remix w Panelu dostępu należy zalogować się automatycznie do aplikacji E Sales Manager Remix.

Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/esalesmanagerremix-tutorial/tutorial_general_01.png
[2]: ./media/esalesmanagerremix-tutorial/tutorial_general_02.png
[3]: ./media/esalesmanagerremix-tutorial/tutorial_general_03.png
[4]: ./media/esalesmanagerremix-tutorial/tutorial_general_04.png

[100]: ./media/esalesmanagerremix-tutorial/tutorial_general_100.png

[200]: ./media/esalesmanagerremix-tutorial/tutorial_general_200.png
[201]: ./media/esalesmanagerremix-tutorial/tutorial_general_201.png
[202]: ./media/esalesmanagerremix-tutorial/tutorial_general_202.png
[203]: ./media/esalesmanagerremix-tutorial/tutorial_general_203.png


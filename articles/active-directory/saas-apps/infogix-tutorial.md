---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą określają Data3Sixty Infogix | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i określają Data3Sixty Infogix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: aa3109b8-bdbe-45ae-933a-2eb4dc03855c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: jeedes
ms.openlocfilehash: 5e9b805786346abd6dfe830c9ce6ae4cc341c9e7
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55194274"
---
# <a name="tutorial-azure-active-directory-integration-with-infogix-data3sixty-govern"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą określają Data3Sixty Infogix

W tym samouczku dowiesz się, jak zintegrować Zarządzanie Data3Sixty Infogix za pomocą usługi Azure Active Directory (Azure AD).

Integrowanie określają Data3Sixty Infogix z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do zarządzania Data3Sixty Infogix.
- Użytkowników, aby automatycznie uzyskać zalogowanych do Infogix Data3Sixty określają (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do konfigurowania integracji z usługą Azure AD z Infogix Data3Sixty określające, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Określają Data3Sixty Infogix logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Infogix Data3Sixty określają sposób za pomocą galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-infogix-data3sixty-govern-from-the-gallery"></a>Dodawanie Infogix Data3Sixty określają sposób za pomocą galerii
Aby skonfigurować integrację z określają Data3Sixty Infogix w usłudze Azure AD, należy dodać określają Data3Sixty Infogix z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać określają Data3Sixty Infogix z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **określają Data3Sixty Infogix**, wybierz opcję **określają Data3Sixty Infogix** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Infogix Data3Sixty określają sposób na liście wyników](./media/infogix-tutorial/tutorial_infogix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Infogix Data3Sixty określają sposób oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w określają Data3Sixty Infogix do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w określają Data3Sixty Infogix musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Infogix Data3Sixty określające, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **[Tworzenie użytkownika testowego określają Data3Sixty Infogix](#create-an-infogix-data3sixty-govern-test-user)**  — aby odpowiednikiem Britta Simon w Infogix Data3Sixty określają sposób połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji określają Data3Sixty Infogix.

**Aby skonfigurować usługi Azure AD logowanie jednokrotne za pomocą określają Data3Sixty Infogix, wykonaj następujące czynności:**

1. W witrynie Azure portal na **określają Data3Sixty Infogix** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/infogix-tutorial/tutorial_infogix_samlbase.png)

1. Na **Infogix Data3Sixty określają domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Infogix Data3Sixty określają domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/infogix-tutorial/tutorial_infogix_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL: `https://data3sixty.com/ui`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.data3sixty.com/sso/acs`

1. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Infogix Data3Sixty określają domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/infogix-tutorial/tutorial_infogix_url1.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.data3sixty.com`
     
    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta określające Data3Sixty Infogix](mailto:data3sixtysupport@infogix.com) do uzyskania tych wartości.

1. Określają Data3Sixty Infogix aplikacja oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego działania.
    
    ![Konfigurowanie logowania jednokrotnego attb](./media/infogix-tutorial/tutorial_infogix_attribute.png)
    
1. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | ------------------- | -------------------- |    
    | firstname           | user.givenname |
    | lastname        | user.surname |
    | nazwa użytkownika       | user.mail    |
    
    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego Dodaj](./media/infogix-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie Addattb rejestracji jednokrotnej](./media/infogix-tutorial/tutorial_attribute_05.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.
    
    e. Kliknij przycisk **OK**.

1. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Raw)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link do pobierania certyfikatu](./media/infogix-tutorial/tutorial_infogix_certificate.png)

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/infogix-tutorial/tutorial_general_400.png)
    
1. Na **Infogix Data3Sixty określają sposób konfiguracji** kliknij **skonfigurować Infogix Data3Sixty określają sposób** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Infogix Data3Sixty określają sposób konfiguracji](./media/infogix-tutorial/tutorial_infogix_configure.png) 

1. Aby skonfigurować logowanie jednokrotne na **określają Data3Sixty Infogix** stronie, musisz wysłać pobrany **certyfikatu (Raw), adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** do [ Określają Data3Sixty Infogix zespołem pomocy technicznej](mailto:data3sixtysupport@infogix.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/infogix-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/infogix-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/infogix-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/infogix-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-an-infogix-data3sixty-govern-test-user"></a>Tworzenie użytkownika testowego określają Data3Sixty Infogix


Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w określają Data3Sixty Infogix. Określają Data3Sixty Infogix obsługę just-in-time, który jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Nowy użytkownik jest tworzony podczas próby dostępu określają Data3Sixty Infogix, jeśli go jeszcze nie istnieje.

>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [Infogix Data3Sixty zarządzanie zespołem pomocy technicznej](mailto:data3sixtysupport@infogix.com).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do udzielania dostępu do zarządzania Data3Sixty Infogix za pomocą platformy Azure logowania jednokrotnego.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon, które pozwalają zarządzać sposobem Data3Sixty Infogix, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **określają Data3Sixty Infogix**.

    ![Łącze Infogix Data3Sixty określają, na liście aplikacji](./media/infogix-tutorial/tutorial_infogix_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Infogix Data3Sixty określające, w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji określają Data3Sixty Infogix.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/infogix-tutorial/tutorial_general_01.png
[2]: ./media/infogix-tutorial/tutorial_general_02.png
[3]: ./media/infogix-tutorial/tutorial_general_03.png
[4]: ./media/infogix-tutorial/tutorial_general_04.png

[100]: ./media/infogix-tutorial/tutorial_general_100.png

[200]: ./media/infogix-tutorial/tutorial_general_200.png
[201]: ./media/infogix-tutorial/tutorial_general_201.png
[202]: ./media/infogix-tutorial/tutorial_general_202.png
[203]: ./media/infogix-tutorial/tutorial_general_203.png


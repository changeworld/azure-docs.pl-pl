---
title: 'Samouczek: Integracja usługi Azure Active Directory z łuk publikowania — Usługa rejestracji Jednokrotnej | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i łuk publikowania — Usługa rejestracji Jednokrotnej.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ae609583-f875-4cb8-b68e-1b0b7938e9a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: jeedes
ms.openlocfilehash: 8c4922c817d9667b1a25846df53f9366e2018342
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55187118"
---
# <a name="tutorial-azure-active-directory-integration-with-arc-publishing---sso"></a>Samouczek: Integracja usługi Azure Active Directory z łuk publikowania — Usługa rejestracji Jednokrotnej

W tym samouczku dowiesz się, jak zintegrować łuk publikowania — logowanie Jednokrotne w usłudze Azure Active Directory (Azure AD).

Integrowanie łuk publikowania — logowanie Jednokrotne z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do łuk publikowania — Usługa rejestracji Jednokrotnej.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do łuk publikowania — SSO (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do konfigurowania integracji z usługą Azure AD z łuk publikowania — logowanie Jednokrotne, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Publikowanie łuk — logowanie Jednokrotne logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie publikowania łuk — Usługa rejestracji Jednokrotnej z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-arc-publishing---sso-from-the-gallery"></a>Dodawanie publikowania łuk — Usługa rejestracji Jednokrotnej z galerii
Aby skonfigurować integrację łuk publikowania — logowanie Jednokrotne w usłudze Azure AD należy dodać łuk publikowania — Usługa rejestracji Jednokrotnej z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać łuk publikowania — Usługa rejestracji Jednokrotnej z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **łuk publikowania — Usługa rejestracji Jednokrotnej**, wybierz opcję **łuk publikowania — Usługa rejestracji Jednokrotnej** z panelu wynik następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Publikowanie łuk — logowanie Jednokrotne na liście wyników](./media/arc-tutorial/tutorial_arc_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji konfigurowania i testowania usługi Azure AD logowania jednokrotnego przy użyciu publikowania łuk — Usługa rejestracji Jednokrotnej w oparciu o użytkownika testu o nazwie "Britta Simon".

Logowanie jednokrotne do pracy usługi Azure AD musi wiedzieć, jakie użytkownik odpowiednika w publikacji łuk — Usługa rejestracji Jednokrotnej jest dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w publikacji łuk — logowania jednokrotnego musi nawiązane.

Do konfigurowania i testowania usługi Azure AD logowania jednokrotnego przy użyciu publikowania łuk — logowanie Jednokrotne, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **[Tworzenie publikacji łuk — Usługa rejestracji Jednokrotnej testowe użytkownika](#create-an-arc-publishing---sso-test-user)**  — aby odpowiednikiem Britta Simon w łuk publikowania — logowanie Jednokrotne połączonej z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i skonfigurować logowanie jednokrotne w swojej łuk publikowania — Usługa rejestracji Jednokrotnej w aplikacji.

**Aby skonfigurować usługę Azure AD w logowanie jednokrotne za pomocą łuk publikowania — logowanie Jednokrotne, wykonaj następujące czynności:**

1. W witrynie Azure portal na **łuk publikowania — Usługa rejestracji Jednokrotnej** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/arc-tutorial/tutorial_arc_samlbase.png)

1. Na **łuk publikowania — Usługa rejestracji Jednokrotnej, domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Publikowanie łuk — domena logowania jednokrotnego i adresy URL pojedynczego logowania jednokrotnego informacji](./media/arc-tutorial/tutorial_arc_url.png)

    1. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://www.okta.com/saml2/service-provider/<Unique ID>`

    1. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>`

1. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Publikowanie łuk — domena logowania jednokrotnego i adresy URL pojedynczego logowania jednokrotnego informacji](./media/arc-tutorial/tutorial_arc_url1.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>`
     
    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z pomocą [łuk publikowania — zespół obsługi klienta logowania jednokrotnego](mailto:inf@washpost.com) do uzyskania tych wartości. 

1. Publikowanie łuk — Usługa rejestracji Jednokrotnej aplikacja oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego działania.
    
    ![Konfigurowanie logowania jednokrotnego](./media/arc-tutorial/tutorial_arc_attribute.png)

1. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | ---------------| --------------- |    
    | firstName | user.givenname |
    | lastName | user.surname |
    | email | user.mail |
    | grupy | user.assignedroles |

    1. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

     ![Konfigurowanie logowania jednokrotnego](./media/arc-tutorial/tutorial_attribute_04.png)

     ![Konfigurowanie logowania jednokrotnego](./media/arc-tutorial/tutorial_attribute_05.png)
    
    1. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.
    
    1. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.

    1. Pozostaw pole **Przestrzeń nazw** puste.
    
    1. Kliknij przycisk **OK**.

    > [!NOTE]
    > W tym miejscu **grup** atrybutu jest zamapowana z **user.assignedroles**. Są to role niestandardowe utworzone w usłudze Azure AD do mapowania nazwy grup w aplikacji. Można znaleźć więcej wskazówek [tutaj](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) na temat tworzenia niestandardowych ról w usłudze Azure AD. 

1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link do pobierania certyfikatu](./media/arc-tutorial/tutorial_arc_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/arc-tutorial/tutorial_general_400.png)
    
1. Na **łuk publikowania — Konfiguracja logowania jednokrotnego** , kliknij przycisk **skonfigurować łuk publikowania — Usługa rejestracji Jednokrotnej** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Publikowanie łuk - konfiguracji logowania jednokrotnego](./media/arc-tutorial/tutorial_arc_configure.png) 

1. Aby skonfigurować logowanie jednokrotne na **łuk publikowania — Usługa rejestracji Jednokrotnej** stronie, musisz wysłać pobrany **certyfikat (Base64), adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** do [łuk Publikowanie — Usługa rejestracji Jednokrotnej, zespół pomocy technicznej](mailto:inf@washpost.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/arc-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/arc-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/arc-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe Użytkownik](./media/arc-tutorial/create_aaduser_04.png)

    1. W **nazwa** wpisz **BrittaSimon**.

    1. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    1. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    1. Kliknij pozycję **Utwórz**.
 
### <a name="create-an-arc-publishing---sso-test-user"></a>Utwórz łuk publikowania — logowanie Jednokrotne użytkownika testowego

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w łuk publikowania — Usługa rejestracji Jednokrotnej. Publikowanie łuk — Usługa rejestracji Jednokrotnej obsługę just-in-time, który jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Nowy użytkownik jest tworzony podczas próby dostępu łuk publikowania — Usługa rejestracji Jednokrotnej, jeśli go jeszcze nie istnieje.

>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [łuk publikowania — Usługa rejestracji Jednokrotnej, zespół pomocy technicznej](mailto:inf@washpost.com).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do łuk publikowania — Usługa rejestracji Jednokrotnej.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon łuk publikowania — logowanie Jednokrotne, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **łuk publikowania — Usługa rejestracji Jednokrotnej**.

    ![Publikowanie łuk - linku logowania jednokrotnego na liście aplikacji](./media/arc-tutorial/tutorial_arc_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu łuk publikowania — Kafelek rejestracji Jednokrotnej w panelu dostępu, możesz powinien pobrać automatycznie zalogowanych do Twojej łuk publikowania — Usługa rejestracji Jednokrotnej w aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/arc-tutorial/tutorial_general_01.png
[2]: ./media/arc-tutorial/tutorial_general_02.png
[3]: ./media/arc-tutorial/tutorial_general_03.png
[4]: ./media/arc-tutorial/tutorial_general_04.png

[100]: ./media/arc-tutorial/tutorial_general_100.png

[200]: ./media/arc-tutorial/tutorial_general_200.png
[201]: ./media/arc-tutorial/tutorial_general_201.png
[202]: ./media/arc-tutorial/tutorial_general_202.png
[203]: ./media/arc-tutorial/tutorial_general_203.png


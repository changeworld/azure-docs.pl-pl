---
title: 'Samouczek: Integracja usługi Azure Active Directory z przodu | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i frontonu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 88270b6d-2571-434a-b139-b6ccc3a2b19f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: jeedes
ms.openlocfilehash: 95b6565885435aeb964dd37bd49ae5a05c06be94
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55175472"
---
# <a name="tutorial-azure-active-directory-integration-with-front"></a>Samouczek: Integracja usługi Azure Active Directory z przodu

W tym samouczku dowiesz się, jak zintegrować frontonu za pomocą usługi Azure Active Directory (Azure AD).

Integrowanie frontonu z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do przodu.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych na wierzch (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą frontonu, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Front logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie frontonu z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-front-from-the-gallery"></a>Dodawanie frontonu z galerii
Aby skonfigurować integrację z przodu w usłudze Azure AD, należy dodać frontonu z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać frontonu z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **frontonu**, wybierz opcję **frontonu** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Front na liście wyników](./media/front-tutorial/tutorial_front_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą frontonu, w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać odpowiednika użytkownika z przodu do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika z przodu musi można ustanowić.

Z przodu przypisze się wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą frontonu, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **[Tworzenie użytkownika testowego Front](#create-a-front-test-user)**  — aby odpowiednik obiektu Britta Simon z przodu połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji frontonu.

**Aby skonfigurować usługi Azure AD logowanie jednokrotne za pomocą frontonu, wykonaj następujące czynności:**

1. W witrynie Azure portal na **frontonu** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/front-tutorial/tutorial_front_samlbase.png)

1. Na **frontonu domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/front-tutorial/tutorial_front_url1.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.frontapp.com`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.frontapp.com/sso/saml/callback`
     
    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości z rzeczywistych identyfikatorem i adres URL odpowiedzi, które zostały wyjaśnione w dalszej części samouczka lub skontaktuj się z [zespołem pomocy technicznej klienta frontonu](mailto:support@frontapp.com) do uzyskania tych wartości. 

1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/front-tutorial/tutorial_front_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/front-tutorial/tutorial_general_400.png)
    
1. Na **konfiguracji frontonu** kliknij **skonfigurować frontonu** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/front-tutorial/tutorial_front_configure.png) 

1. Zaloguj się do przodu dzierżawy z uprawnieniami administratora.

1. Przejdź do **ustawienia (koło zębate ikonę u dołu lewego paska bocznego) > Preferencje**.
   
    ![Konfigurowanie pojedynczego logowania jednokrotnego w aplikacji po stronie](./media/front-tutorial/tutorial_front_000.png)

1. Kliknij przycisk **Single Sign On** łącza.
   
    ![Konfigurowanie pojedynczego logowania jednokrotnego w aplikacji po stronie](./media/front-tutorial/tutorial_front_001.png)

1. Wybierz **SAML** na liście rozwijanej **Single Sign On**.
   
    ![Konfigurowanie pojedynczego logowania jednokrotnego w aplikacji po stronie](./media/front-tutorial/tutorial_front_002.png)

1. W **punktu wejścia** textbox umieścić wartość elementu **pojedynczy znak na adres URL usługi** z Kreatora konfiguracji aplikacji usługi Azure AD.
    
    ![Konfigurowanie pojedynczego logowania jednokrotnego w aplikacji po stronie](./media/front-tutorial/tutorial_front_003.png)

1. Otwórz swoje pobrany **Certificate(Base64)** w Notatniku plik, skopiuj jego zawartość do Schowka, a następnie wklej go do **certyfikatu podpisywania** pola tekstowego.
    
    ![Konfigurowanie pojedynczego logowania jednokrotnego w aplikacji po stronie](./media/front-tutorial/tutorial_front_004.png)

1. Na **ustawień dostawcy usług** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie pojedynczego logowania jednokrotnego w aplikacji po stronie](./media/front-tutorial/tutorial_front_005.png)

    a. Skopiuj wartość **identyfikator jednostki** i wklej go w **identyfikator** polu tekstowym w **frontonu domena i adresy URL** sekcji w witrynie Azure portal.

    b. Skopiuj wartość **adres URL usługi ACS** i wklej go w **adres URL odpowiedzi** polu tekstowym w **frontonu domena i adresy URL** sekcji w witrynie Azure portal.
    
1. Kliknij przycisk **Save** (Zapisz).

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/front-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/front-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/front-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/front-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-front-test-user"></a>Tworzenie użytkownika testowego frontonu

W tej sekcji utworzysz użytkownika o nazwie Britta Simon z przodu. Praca z [zespołem pomocy technicznej klienta frontonu](mailto:support@frontapp.com) Aby dodać użytkowników do przodu platformy. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do przodu.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon do przodu, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **frontonu**.

    ![Link frontonu na liście aplikacji](./media/front-tutorial/tutorial_front_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest testowanie usługi Azure AD SSOconfiguration, za pomocą panelu dostępu.

Po kliknięciu kafelka frontonu w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji frontonu. 

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/front-tutorial/tutorial_general_01.png
[2]: ./media/front-tutorial/tutorial_general_02.png
[3]: ./media/front-tutorial/tutorial_general_03.png
[4]: ./media/front-tutorial/tutorial_general_04.png

[100]: ./media/front-tutorial/tutorial_general_100.png

[200]: ./media/front-tutorial/tutorial_general_200.png
[201]: ./media/front-tutorial/tutorial_general_201.png
[202]: ./media/front-tutorial/tutorial_general_202.png
[203]: ./media/front-tutorial/tutorial_general_203.png


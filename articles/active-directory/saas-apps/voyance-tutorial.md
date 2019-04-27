---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Voyance | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Voyance.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 539dc1f9-64c9-4dce-b259-2b0b49dcf857
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1771fe1d94eb64cf1e5227953bdc5defa488e85f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60639283"
---
# <a name="tutorial-azure-active-directory-integration-with-voyance"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Voyance

W tym samouczku dowiesz się, jak zintegrować Voyance w usłudze Azure Active Directory (Azure AD).

Integrowanie Voyance z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Voyance
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Voyance (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Voyance, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Voyance logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Voyance z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-voyance-from-the-gallery"></a>Dodawanie Voyance z galerii
Aby skonfigurować integrację Voyance w usłudze Azure AD, należy dodać Voyance z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Voyance z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **Voyance**, wybierz opcję **Voyance** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Voyance na liście wyników](./media/voyance-tutorial/tutorial_voyance_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Voyance w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Voyance do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Voyance musi można ustanowić.

W Voyance, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Voyance, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **[Tworzenie użytkownika testowego Voyance](#create-a-voyance-test-user)**  — aby odpowiednikiem Britta Simon w Voyance połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Voyance.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Voyance, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Voyance** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/voyance-tutorial/tutorial_voyance_samlbase.png)

1. Na **Voyance domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Voyance domena i adresy URL pojedynczy informacji logowania dla dostawcy tożsamości](./media/voyance-tutorial/tutorial_voyance_url1.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.nyansa.com`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.nyansa.com/saml/create/`

1. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Voyance domena i adresy URL pojedynczego logowania jednokrotnego informacje dotyczące SP](./media/voyance-tutorial/tutorial_voyance_url2.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.nyansa.com/`
     
    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Voyance](mailto:support@nyansa.com) do uzyskania tych wartości. 

1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link do pobierania certyfikatu](./media/voyance-tutorial/tutorial_voyance_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/voyance-tutorial/tutorial_general_400.png)
    
1. Na **konfiguracji Voyance** , kliknij przycisk **skonfigurować Voyance** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja Voyance](./media/voyance-tutorial/tutorial_voyance_configure.png) 

1. W oknie przeglądarki internetowej innej logowanie jednokrotne do swojej dzierżawy Voyance jako administrator.

1. Przejdź do prawym górnym rogu paska nawigacji i kliknij pozycję z listy rozwijanej informujący, że "**Acme University**".
    
    ![Konfigurowanie logowania jednokrotnego w aplikacji po stronie Acme University](./media/voyance-tutorial/tutorial_voyance_001.png) 

1. Kliknij przycisk "**ustawienia administratora**".

    ![Konfigurowanie logowania jednokrotnego na aplikacji po stronie ustawień administratora](./media/voyance-tutorial/tutorial_voyance_002.png)

1. Kliknij przycisk "**dostęp użytkownika**" kartę.

    ![Konfigurowanie logowania jednokrotnego na dostęp użytkownika po stronie aplikacji](./media/voyance-tutorial/tutorial_voyance_003.png)

1. Kliknij przycisk "**logowania jednokrotnego jest wyłączona**" przycisk, aby skonfigurować usługę Azure AD jako dostawcy tożsamości za pomocą języka SAML 2.0.

    ![Konfigurowanie pojedynczego logowania jednokrotnego w aplikacji po stronie logowania jednokrotnego jest wyłączona, przycisk](./media/voyance-tutorial/tutorial_voyance_004.png)

1. Przejdź do **SAML w wersji 2** sekcji, a następnie wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego w aplikacji po stronie SAML w wersji 2](./media/voyance-tutorial/tutorial_voyance_005.png)
    
    a. Wybierz **włączone**.
    
    b. Wklej **SAML pojedynczego logowania jednokrotnego adres URL usługi**, który skopiowano z witryny Azure portal do **adres URL logowania dostawcy tożsamości** pole tekstowe.

    c. Otwórz pobrany certyfikat zakodowany w formacie Base64 w programie Notatnik, skopiuj jego zawartość do Schowka, a następnie wklej go do **certyfikatu tożsamości** pola tekstowego.
    
    d. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](./media/voyance-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](./media/voyance-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Przycisk Dodaj](./media/voyance-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Okno dialogowe Użytkownik](./media/voyance-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-voyance-test-user"></a>Tworzenie użytkownika testowego Voyance

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w Voyance. Voyance obsługę just-in-time, który jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Nowy użytkownik jest tworzony podczas próby dostępu Voyance, jeśli go jeszcze nie istnieje.

>[!NOTE]
>Jeśli potrzebujesz ręcznie utworzyć użytkownika, musisz skontaktować się z [zespołem pomocy technicznej Voyance](maiLto:support@nyansa.com).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Voyance.

![Przypisanie roli użytkownika][200]

**Aby przypisać Britta Simon Voyance, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Voyance**.

    ![Link Voyance na liście aplikacji](./media/voyance-tutorial/tutorial_voyance_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Voyance w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Voyance.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/voyance-tutorial/tutorial_general_01.png
[2]: ./media/voyance-tutorial/tutorial_general_02.png
[3]: ./media/voyance-tutorial/tutorial_general_03.png
[4]: ./media/voyance-tutorial/tutorial_general_04.png

[100]: ./media/voyance-tutorial/tutorial_general_100.png

[200]: ./media/voyance-tutorial/tutorial_general_200.png
[201]: ./media/voyance-tutorial/tutorial_general_201.png
[202]: ./media/voyance-tutorial/tutorial_general_202.png
[203]: ./media/voyance-tutorial/tutorial_general_203.png


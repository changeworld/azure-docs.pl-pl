---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą iQualify LMS | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i iQualify LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 8a3caaff-dd8d-4afd-badf-a0fd60db3d2c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c8cd7a2cfc96af791e4b07ae4c6d35b645acba0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2019
ms.locfileid: "57835802"
---
# <a name="tutorial-azure-active-directory-integration-with-iqualify-lms"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą iQualify LMS

W tym samouczku dowiesz się, jak zintegrować iQualify LMS w usłudze Azure Active Directory (Azure AD).

Integrowanie iQualify LMS z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do iQualify LMS.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do iQualify LMS (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą iQualify LMS, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- IQualify LMS logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie iQualify LMS z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-iqualify-lms-from-the-gallery"></a>Dodawanie iQualify LMS z galerii
Aby skonfigurować integrację iQualify LMS w usłudze Azure AD, należy dodać iQualify LMS z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać iQualify LMS z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **iQualify LMS**, wybierz opcję **iQualify LMS** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![iQualify LMS na liście wyników](./media/iqualify-tutorial/tutorial_iqualify_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz skonfigurować i przetestować usługi Azure AD logowanie jednokrotne za pomocą iQualify LMS zależnie od użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi wiedzieć, jakie użytkownik odpowiednika w iQualify LMS jest dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w iQualify LMS musi nawiązane.

IQualify LMS przypisywanie wartości **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą iQualify LMS, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **[Tworzenie użytkownika testowego LMS iQualify](#create-an-iqualify-lms-test-user)**  — aby mają odpowiednika w pozycji Britta simon w iQualify LMS połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji systemu LMS iQualify.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z iQualify LMS, wykonaj następujące czynności:**

1. W witrynie Azure portal na **iQualify LMS** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/iqualify-tutorial/tutorial_iqualify_samlbase.png)

1. Na **iQualify LMS domena i adresy URL** sekcji, jeśli chcesz skonfigurować aplikację w trybie zainicjował dostawcy tożsamości należy wykonać następujące czynności:

    ![informacje o iQualify LMS domena i adresy URL logowania jednokrotnego](./media/iqualify-tutorial/tutorial_iqualify_url.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: 

    | |
    |--|--|
    | Środowisko produkcyjne: `https://<yourorg>.iqualify.com/`|
    | Środowisko testowe: `https://<yourorg>.iqualify.io`|
    
    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: 

    | |
    |--|--|
    | Środowisko produkcyjne: `https://<yourorg>.iqualify.com/auth/saml2/callback` |
    | Środowisko testowe: `https://<yourorg>.iqualify.io/auth/saml2/callback` |

1. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![informacje o iQualify LMS domena i adresy URL logowania jednokrotnego](./media/iqualify-tutorial/tutorial_iqualify_url1.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca:
    
    | |
    |--|--|
    | Środowisko produkcyjne: `https://<yourorg>.iqualify.com/login` |
    | Środowisko testowe: `https://<yourorg>.iqualify.io/login` |
     
    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z pomocą [zespół obsługi klienta systemu LMS iQualify](https://www.iqualify.com) do uzyskania tych wartości. 

1. Aplikacja systemu LMS iQualify oczekuje potwierdzeń zabezpieczeń Assertion Markup Language (SAML), które mają być wyświetlane w określonym formacie. Konfigurowanie oświadczenia i zarządzanie nimi wartości atrybutów w **atrybutów użytkownika** części strony iQualify integracji aplikacji, jak pokazano na poniższym zrzucie ekranu:
    
    ![Konfigurowanie logowania jednokrotnego](./media/iqualify-tutorial/atb.png)

1. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe wykonaj następujące kroki dla każdego wiersza, jak pokazano w tabeli poniżej:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | --- | --- |    
    | email | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | person_id | "atrybutu" | 

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](./media/iqualify-tutorial/atb2.png)

    ![Konfigurowanie logowania jednokrotnego](./media/iqualify-tutorial/atb3.png)
    
    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.
    
    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.
    
    d. Kliknij przycisk **OK**.

    e. Powtórz kroki od "a" do "d" dla następnego wierszy tabeli. 

    > [!Note]
    > Powtórzenie kroków "a" do "d" dla **person_id** atrybut jest **opcjonalne**

1. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base 64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link do pobierania certyfikatu](./media/iqualify-tutorial/tutorial_iqualify_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/iqualify-tutorial/tutorial_general_400.png)
    
1. Na **iQualify konfiguracji LMS** , kliknij przycisk **skonfigurować iQualify LMS** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![iQualify LMS konfiguracji](./media/iqualify-tutorial/tutorial_iqualify_configure.png) 

1.  Otwórz nowe okno przeglądarki, a następnie zaloguj się do środowiska iQualify jako administrator.

1. Po użytkownik jest zalogowany, kliknij awatara w prawym górnym rogu, a następnie kliknij pozycję **"Ustawienia konta".**

    ![Ustawienia konta](./media/iqualify-tutorial/setting1.png) 
1. W obszarze Ustawienia konta kliknij w menu po lewej stronie wstążki, a następnie kliknij **"INTEGRACJE."**
    
    ![INTEGRACJE](./media/iqualify-tutorial/setting2.png)

1. W obszarze integracji, kliknij **SAML** ikony.

    ![Ikona SAML](./media/iqualify-tutorial/setting3.png)

1. W **ustawienia uwierzytelniania SAML** okna dialogowego pole, wykonaj następujące czynności:

    ![Ustawienia uwierzytelniania SAML](./media/iqualify-tutorial/setting4.png)

    a. W **adresu URL protokołu SAML jednokrotnego logowania usługi** pole, Wklej **adres URL protokołu SAML pojedynczego Sign‑On usługi** wartość kopiowana z okna konfiguracji aplikacji usługi Azure AD.
    
    b. W **adres URL WYLOGOWANIA protokołu SAML** pole, Wklej **Sign‑Out URL** wartość kopiowana z okna konfiguracji aplikacji usługi Azure AD.
    
    c. Otwórz plik pobranego certyfikatu w programie Notatnik, skopiuj zawartość, a następnie wklej go w **certyfikatu publicznego** pole.
    
    d. W **Etykieta przycisku logowania** wprowadź nazwę dla przycisku, który będzie wyświetlany na stronie logowania.
    
    e. Kliknij przycisk **SAVE** (Zapisz).

    f. Kliknij przycisk **aktualizacji**.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/iqualify-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/iqualify-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/iqualify-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/iqualify-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-an-iqualify-lms-test-user"></a>Tworzenie użytkownika testowego LMS iQualify

W tej sekcji użytkownika o nazwie Britta Simon jest tworzony w iQualify. iQualify LMS obsługę just‑in‑time użytkownika, który jest domyślnie włączona.

W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w iQualify, nowy katalog jest tworzony podczas próby uzyskania dostępu iQualify LMS.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do iQualify LMS.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon iQualify LMS, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **iQualify LMS**.

    ![Link LMS iQualify na liście aplikacji](./media/iqualify-tutorial/tutorial_iqualify_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu iQualify, który LMS kafelka w panelu dostępu, powinna pojawić się strona logowania aplikacji LMS iQualify. 

   ![Strona logowania](./media/iqualify-tutorial/login.png) 

Kliknij przycisk **Zaloguj się przy użyciu usługi Azure AD** przycisku i należy pobrać automatycznie zalogowanych do aplikacji LMS iQualify.

Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/iqualify-tutorial/tutorial_general_01.png
[2]: ./media/iqualify-tutorial/tutorial_general_02.png
[3]: ./media/iqualify-tutorial/tutorial_general_03.png
[4]: ./media/iqualify-tutorial/tutorial_general_04.png

[100]: ./media/iqualify-tutorial/tutorial_general_100.png

[200]: ./media/iqualify-tutorial/tutorial_general_200.png
[201]: ./media/iqualify-tutorial/tutorial_general_201.png
[202]: ./media/iqualify-tutorial/tutorial_general_202.png
[203]: ./media/iqualify-tutorial/tutorial_general_203.png


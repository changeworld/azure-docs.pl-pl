---
title: 'Samouczek: Integracja usługi Azure Active Directory z TextMagic | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i TextMagic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 3e5b49d2-7096-46bc-a9ce-90e09177ba28
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: jeedes
ms.openlocfilehash: 1769f3d0d86fca784d8d4e7a221a7cf3bde16def
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39056100"
---
# <a name="tutorial-azure-active-directory-integration-with-textmagic"></a>Samouczek: Integracja usługi Azure Active Directory z TextMagic

W tym samouczku dowiesz się, jak zintegrować TextMagic w usłudze Azure Active Directory (Azure AD).

Integrowanie TextMagic z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do TextMagic.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do TextMagic (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą TextMagic, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- TextMagic logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie TextMagic z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-textmagic-from-the-gallery"></a>Dodawanie TextMagic z galerii
Aby skonfigurować integrację TextMagic w usłudze Azure AD, należy dodać TextMagic z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać TextMagic z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **TextMagic**, wybierz opcję **TextMagic** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![TextMagic na liście wyników](./media/textmagic-tutorial/tutorial_textmagic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą TextMagic w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w TextMagic do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w TextMagic musi można ustanowić.

W TextMagic, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą TextMagic, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego TextMagic](#create-a-textmagic-test-user)**  — aby odpowiednikiem Britta Simon w TextMagic połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji TextMagic.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z TextMagic, wykonaj następujące czynności:**

1. W witrynie Azure portal na **TextMagic** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/textmagic-tutorial/tutorial_textmagic_samlbase.png)

3. Na **TextMagic domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![TextMagic domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/textmagic-tutorial/tutorial_textmagic_url.png)

    W **identyfikator** pole tekstowe, wpisz adres URL: `https://my.textmagic.com/saml/metadata`

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![TextMagic domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/textmagic-tutorial/url1.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL: `https://my.textmagic.com/login/sso`


5. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/textmagic-tutorial/tutorial_textmagic_certificate.png) 

6. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/textmagic-tutorial/tutorial_general_400.png)
    
7. Na **konfiguracji TextMagic** , kliknij przycisk **skonfigurować TextMagic** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja TextMagic](./media/textmagic-tutorial/tutorial_textmagic_configure.png) 

8. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy TextMagic.

9. Wybierz **ustawienia konta** w obszarze nazwy użytkownika.

    ![Konfiguracja TextMagic](./media/textmagic-tutorial/config1.png) 
10. Kliknij kartę **"pojedynczego logowania jednokrotnego (SSO)"** i wypełnij następujące pola:  
    
    ![Konfiguracja TextMagic](./media/textmagic-tutorial/config2.png)

    a. W **dostawcy tożsamości identyfikator jednostki:** pola tekstowego, Wklej wartość **identyfikator jednostki SAML**, który skopiowano z witryny Azure portal.

    b. W **dostawcy tożsamości adres URL logowania jednokrotnego:** pola tekstowego, Wklej wartość **pojedynczy znak na adres URL usługi**, który skopiowano z witryny Azure portal.

    c. W **dostawcy tożsamości SLO URL:** pola tekstowego, Wklej wartość **adres URL wylogowania**, który skopiowano z witryny Azure portal.

    d. Otwórz swoje **certyfikat szyfrowany algorytmem base-64** w Notatniku pobranego z witryny Azure portal, skopiuj zawartość go do Schowka, a następnie wklej go do **certyfikatu publicznego x509:** pola tekstowego.

    e. Kliknij pozycję **Zapisz**.


> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/textmagic-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/textmagic-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/textmagic-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/textmagic-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-textmagic-test-user"></a>Tworzenie użytkownika testowego TextMagic

Aplikacja obsługuje tylko w czasie Inicjowanie obsługi użytkowników oraz uwierzytelniania użytkowników w aplikacji, automatycznie tworzony. Musisz podać informacje o jeden raz o pierwszego logowania do aktywowania konta podrzędnego do systemu.
Brak elementu akcji dla Ciebie w tej sekcji.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do TextMagic.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon TextMagic, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **TextMagic**.

    ![Link TextMagic na liście aplikacji](./media/textmagic-tutorial/tutorial_textmagic_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka TextMagic w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji TextMagic.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/textmagic-tutorial/tutorial_general_01.png
[2]: ./media/textmagic-tutorial/tutorial_general_02.png
[3]: ./media/textmagic-tutorial/tutorial_general_03.png
[4]: ./media/textmagic-tutorial/tutorial_general_04.png

[100]: ./media/textmagic-tutorial/tutorial_general_100.png

[200]: ./media/textmagic-tutorial/tutorial_general_200.png
[201]: ./media/textmagic-tutorial/tutorial_general_201.png
[202]: ./media/textmagic-tutorial/tutorial_general_202.png
[203]: ./media/textmagic-tutorial/tutorial_general_203.png


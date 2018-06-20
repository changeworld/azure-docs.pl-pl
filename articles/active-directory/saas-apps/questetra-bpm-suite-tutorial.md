---
title: 'Samouczek: Azure Active Directory integracji z pakietem BPM Questetra | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Questetra BPM Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: fb6d5b73-e491-4dd2-92d6-94e5aba21465
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2017
ms.author: jeedes
ms.openlocfilehash: e9a4ab5bb262b274f4c53741f7a9ea1ce3956848
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36220132"
---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Samouczek: Azure Active Directory integracji z pakietem BPM Questetra

Z tego samouczka dowiesz integrowanie Questetra BPM pakietu z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Questetra BPM Suite zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do zestawu BPM Questetra
- Umożliwia użytkownikom automatycznie pobrać zalogowane Questetra BPM pakietu (logowanie jednokrotne) z konta usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z pakietem BPM Questetra, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Pakiet BPM Questetra logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie zestawu BPM Questetra z galerii
2. Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

## <a name="add-questetra-bpm-suite-from-the-gallery"></a>Dodawanie zestawu BPM Questetra z galerii
Aby skonfigurować integrację usługi Azure AD Questetra BPM pakietu, należy dodać pakiet BPM Questetra z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać pakiet BPM Questetra z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Questetra BPM Suite**, wybierz pozycję **Questetra BPM Suite** z panelu wyników, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Dodaj z galerii](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z pakietem BPM Questetra w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednikiem pakietu BPM Questetra jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i danemu użytkownikowi pakietu BPM Questetra musi określone.

W zestawie BPM Questetra, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z pakietem BPM Questetra, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego zestawu BPM Questetra](#create-a-questetra-bpm-suite-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Questetra BPM pakiet, który jest połączony z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne Questetra BPM pakietu aplikacji.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z pakietem BPM Questetra, wykonaj następujące czynności:**

1. W portalu Azure na **Questetra BPM Suite** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Logowanie na podstawie protokołu SAML](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_samlbase.png)

3. Na **Questetra BPM Suite domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Sekcja Questetra BPM Suite domeny i adresy URL](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<subdomain>.questetra.net/saml/SSO/alias/bpm`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<subdomain>.questetra.net/`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Możesz uzyskać te wartości z **SP informacji** sekcji na Twojej **Questetra BPM pakiet** witryny firmy, która znajduje się w dalszej części samouczka lub skontaktuj się z [Obsługa Questetra BPM pakiet klienta zespół](https://www.questetra.com/contact/). 
 
4. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base 64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Sekcja certyfikat podpisywania SAML](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Przycisk Zapisz](./media/questetra-bpm-suite-tutorial/tutorial_general_400.png)

6. Na **Questetra BPM pakiet konfiguracji** kliknij **skonfigurować pakiet BPM Questetra** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Sekcja konfiguracji pakietu BPM Questetra](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_configure.png) 

7. W oknie przeglądarki innej witryny sieci web, zaloguj się do Twojego **Questetra BPM Suite** witryny firmy jako administrator.

8. W menu u góry kliknij **ustawienia systemu**. 
   
    ![Azure AD rejestracji jednokrotnej][10]

9. Aby otworzyć **SingleSignOnSAML** kliknij przycisk **logowania jednokrotnego (SAML)**. 
   
    ![Azure AD rejestracji jednokrotnej][11]

10. Na Twojej **Questetra BPM Suite** firmy lokacji, w **informacji SP** sekcji, wykonaj następujące czynności:

    a. Kopii **adres URL usługi ACS**, a następnie wklej go do **na adres URL logowania** textbox w **Questetra BPM Suite domeny i adres URL** sekcji z portalu Azure.
    
    b. Kopia **identyfikator jednostki**, a następnie wklej go do **identyfikator** textbox w **Questetra BPM Suite domeny i adres URL** sekcji z portalu Azure.

11. W Twojej **Questetra BPM Suite** firmy, witryny, należy wykonać następujące czynności: 
   
    ![Konfigurowanie rejestracji jednokrotnej][15]
   
    a. Wybierz **Włącz rejestrację jednokrotną**.
   
    b. W **identyfikator jednostki** pole tekstowe, Wklej wartość **identyfikator jednostki SAML** którego została skopiowana z portalu Azure.
    
    c. W **adres URL logowania strony** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi** którego została skopiowana z portalu Azure.
    
    d. W **adres URL strony wylogowania** pole tekstowe, Wklej wartość **Sign-Out URL** którego została skopiowana z portalu Azure.
    
    e. W **NameID format** pole tekstowe, typ `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    f. Otwórz z **Base-64** zakodowanego certyfikatu w programie Notatnik pobrany z portalu Azure, skopiuj zawartość go do Schowka, a następnie wklej go do **certyfikatu weryfikacji** pola tekstowego. 

    g. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/questetra-bpm-suite-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/questetra-bpm-suite-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/questetra-bpm-suite-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/questetra-bpm-suite-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-questetra-bpm-suite-test-user"></a>Tworzenie użytkownika testowego Questetra BPM Suite

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta Questetra BPM pakietu.

**Aby utworzyć użytkownika o nazwie Simona Britta pakietu BPM Questetra, wykonaj następujące kroki:**

1. Zalogować się do witryny Questetra BPM Suite firmy jako administrator.
2. Przejdź do **ustawienia systemu > listy użytkowników > Nowy użytkownik**. 
3. W oknie dialogowym Nowy użytkownik wykonaj następujące czynności: 
   
    ![Tworzenie użytkownika testowego][300] 
   
    a. W **nazwa** pole tekstowe, typ **nazwa** użytkownika **britta.simon@contoso.com**.
   
    b. W **E-mail** pole tekstowe, typ **e-mail** użytkownika **britta.simon@contoso.com**
   
    c. W **hasło** pola tekstowego, a typ **hasło** użytkownika.
    
    d. Kliknij przycisk **Dodaj nowego użytkownika**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do zestawu BPM Questetra.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Questetra BPM pakietu, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Questetra BPM Suite**.

    ![Pakiet BPM Questetra liście aplikacji](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Questetra BPM Suite w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane Questetra BPM pakiet aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/questetra-bpm-suite-tutorial/tutorial_general_01.png
[2]: ./media/questetra-bpm-suite-tutorial/tutorial_general_02.png
[3]: ./media/questetra-bpm-suite-tutorial/tutorial_general_03.png
[4]: ./media/questetra-bpm-suite-tutorial/tutorial_general_04.png
[10]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[15]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_08.png

[100]: ./media/questetra-bpm-suite-tutorial/tutorial_general_100.png

[200]: ./media/questetra-bpm-suite-tutorial/tutorial_general_200.png
[201]: ./media/questetra-bpm-suite-tutorial/tutorial_general_201.png
[202]: ./media/questetra-bpm-suite-tutorial/tutorial_general_202.png
[203]: ./media/questetra-bpm-suite-tutorial/tutorial_general_203.png
[300]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_11.png 


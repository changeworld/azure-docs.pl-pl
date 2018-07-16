---
title: 'Samouczek: Integracja usługi Azure Active Directory z 8 x 8 wirtualnych Office | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między platformy Azure Active Directory oraz 8 x 8 wirtualnego pakietu Office.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b34a6edf-e745-4aec-b0b2-7337473d64c5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 3a33f9ba0ca744709e21e9e55acc22b657c2adc2
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39048423"
---
# <a name="tutorial-azure-active-directory-integration-with-8x8-virtual-office"></a>Samouczek: Integracja usługi Azure Active Directory z pakietem Office wirtualnej 8 x 8

W tym samouczku dowiesz się, jak zintegrować 8 x 8 wirtualnych Office z usługą Azure Active Directory (Azure AD).

Integrowanie 8 x 8 wirtualnych pakietu Office z usługą Azure AD oferuje następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do pakietu Office wirtualnej 8 x 8
- Użytkowników, aby automatycznie uzyskać zalogowanych do 8 x 8 wirtualnych Office (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z 8 x 8 wirtualnych z pakietem Office, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- 8 x 8 wirtualnych Office logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie 8 x 8 wirtualnych Office za pomocą galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-8x8-virtual-office-from-the-gallery"></a>Dodawanie 8 x 8 wirtualnych Office za pomocą galerii
Aby skonfigurować integrację pakietu Office wirtualnej 8 x 8 w usłudze Azure AD, należy dodać 8 x 8 wirtualnych Office za pomocą galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać 8 x 8 wirtualnych Office z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **8 x 8 wirtualnych Office**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_search.png)

5. W panelu wyników wybierz **8 x 8 wirtualnych Office**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD logowanie jednokrotne z 8 x 8 wirtualnych pakietu Office na podstawie których użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi wiedzieć, jakie użytkownik odpowiednika w 8 x 8 wirtualnych Office jest dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w 8 x 8 wirtualnych Office musi można ustanowić.

W pakiecie Office wirtualnej 8 x 8, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z 8 x 8 wirtualnych z pakietem Office, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego wirtualnego Office 8 x 8](#creating-a-8x8-virtual-office-test-user)**  — aby odpowiednikiem Britta Simon w 8 x 8 wirtualnych biuro, które jest połączony z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i skonfigurować logowanie jednokrotne w 8 x 8 wirtualnych aplikacji pakietu Office.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z pakietem Office wirtualnej 8 x 8, wykonaj następujące czynności:**

1. W witrynie Azure portal na **8 x 8 wirtualnych Office** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_samlbase.png)

3. Na **8 x 8 wirtualnych Office domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:

    | `https://sso.8x8.com/<companyname>` |
    | `https://www.8x8.com/<companyname>` |
    | `https://sso.8x8pilot.com/<companyname>` |

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:

    | `https://<subdomain>.8x8.com/saml2` |
    | `https://<subdomain>.8x8pilot.com/saml2`|

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adres URL odpowiedzi. Skontaktuj się z pomocą [zespół pomocy technicznej usługi Office wirtualnego 8 x 8](https://www.8x8.com/about-us/contact-us) do uzyskania tych wartości.
 


4. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Raw)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_certificate.png) 

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/8x8virtualoffice-tutorial/tutorial_general_400.png)

6. Na **8 x 8 wirtualnych Office Konfiguracja** , kliknij przycisk **Konfiguruj 8 x 8 wirtualnych Office** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_configure.png) 

7. Logowanie jednokrotne do swojej dzierżawy Office wirtualnego 8 x 8 jako administrator.

8. Wybierz **Mgr konta Office wirtualnego** na panelu aplikacji.
   
    ![Konfigurowanie aplikacji po stronie](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

9. Wybierz **firm** konto, aby zarządzać, a następnie kliknij przycisk **Sign In** przycisku.
   
    ![Konfigurowanie aplikacji po stronie](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

10. Kliknij przycisk **kont** karty na liście menu.
   
    ![Konfigurowanie aplikacji po stronie](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

11. Kliknij przycisk **Single Sign On** się na liście kont.
   
    ![Konfigurowanie aplikacji po stronie](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

12. Wybierz **Single Sign On** metodę uwierzytelniania i kliknij przycisk **SAML**.
    
    ![Konfigurowanie aplikacji po stronie](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

13. Kopiuj **adres URL logowania jednokrotnego SAML**, **pojedynczy jedno adresu URL usługi wylogowania** i **adres URL wystawcy** z usługi Azure AD do **Zaloguj się w adresie URL**, **adres URL wylogowania**  i **adres URL wystawcy** 8 x 8 wirtualnych pakietu Office. 
    
    ![Konfigurowanie aplikacji po stronie](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)
    
14. Kliknij przycisk **przeglądarki** przycisk, aby przekazać certyfikat, który został pobrany z usługi Azure AD, a następnie kliknij przycisk **Zapisz** przycisku.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/8x8virtualoffice-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/8x8virtualoffice-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/8x8virtualoffice-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/8x8virtualoffice-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-8x8-virtual-office-test-user"></a>Tworzenie użytkownika testowego wirtualnego Office 8 x 8

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w pakiecie Office wirtualnej 8 x 8. 8 x 8 wirtualnych Office obsługę just-in-time, który jest domyślnie włączona.

Brak elementu akcji dla Ciebie w tej sekcji. Nowy użytkownik jest tworzony podczas próby dostępu 8 x 8 wirtualnych Office, jeśli go jeszcze nie istnieje. 

>[!NOTE]
>Jeśli potrzebujesz ręcznie utworzyć użytkownika, musisz skontaktować się z [zespół pomocy technicznej usługi Office wirtualnego 8 x 8](https://www.8x8.com/about-us/contact-us). 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do pakietu Office wirtualnej 8 x 8.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon 8 x 8 wirtualnych Office, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **8 x 8 wirtualnych Office**.

    ![Konfigurowanie logowania jednokrotnego](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Office wirtualnego 8 x 8 w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do 8 x 8 wirtualnych aplikacji pakietu Office.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/8x8virtualoffice-tutorial/tutorial_general_01.png
[2]: ./media/8x8virtualoffice-tutorial/tutorial_general_02.png
[3]: ./media/8x8virtualoffice-tutorial/tutorial_general_03.png
[4]: ./media/8x8virtualoffice-tutorial/tutorial_general_04.png

[100]: ./media/8x8virtualoffice-tutorial/tutorial_general_100.png

[200]: ./media/8x8virtualoffice-tutorial/tutorial_general_200.png
[201]: ./media/8x8virtualoffice-tutorial/tutorial_general_201.png
[202]: ./media/8x8virtualoffice-tutorial/tutorial_general_202.png
[203]: ./media/8x8virtualoffice-tutorial/tutorial_general_203.png


---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą IdeaScale | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i IdeaScale.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: e16dda6b-fdf9-43cc-9bbb-a523f085a8af
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: ca5cac1888fe7e126d6bdc8bd4a2e9bc192f4d41
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54810061"
---
# <a name="tutorial-azure-active-directory-integration-with-ideascale"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą IdeaScale

W tym samouczku dowiesz się, jak zintegrować IdeaScale w usłudze Azure Active Directory (Azure AD).

Integrowanie IdeaScale z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do IdeaScale
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do IdeaScale (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą IdeaScale, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Logowania jednokrotnego IdeaScale włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie IdeaScale z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-ideascale-from-the-gallery"></a>Dodawanie IdeaScale z galerii
Aby skonfigurować integrację IdeaScale w usłudze Azure AD, należy dodać IdeaScale z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać IdeaScale z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **IdeaScale**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/ideascale-tutorial/tutorial_ideascale_search.png)

1. W panelu wyników wybierz **IdeaScale**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/ideascale-tutorial/tutorial_ideascale_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą IdeaScale w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w IdeaScale do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w IdeaScale musi można ustanowić.

W IdeaScale, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą IdeaScale, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego IdeaScale](#creating-an-ideascale-test-user)**  — aby odpowiednikiem Britta Simon w IdeaScale połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji IdeaScale.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z IdeaScale, wykonaj następujące czynności:**

1. W witrynie Azure portal na **IdeaScale** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/ideascale-tutorial/tutorial_ideascale_samlbase.png)

1. Na **IdeaScale domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/ideascale-tutorial/tutorial_ideascale_url.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.ideascale.com`

    b. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca:
    | |
    |--|
    | `http://<companyname>.ideascale.com`  |
    | `https://<companyname>.ideascale.com` |

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL i identyfikatora logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta IdeaScale](https://support.ideascale.com/) do uzyskania tych wartości. 
 
1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/ideascale-tutorial/tutorial_ideascale_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/ideascale-tutorial/tutorial_general_400.png)

1. Na **konfiguracji IdeaScale** , kliknij przycisk **skonfigurować IdeaScale** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania i identyfikator jednostki SAML** z **krótki przewodnik po sekcji**.

    ![Konfigurowanie logowania jednokrotnego](./media/ideascale-tutorial/tutorial_ideascale_configure.png) 

1. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy IdeaScale.

1. Przejdź do **ustawienia społeczności**.
   
    ![Ustawienia społeczności](./media/ideascale-tutorial/ic790847.png "ustawienia społeczności")

1. Przejdź do **zabezpieczeń \> pojedynczego ustawienia logować**.
   
    ![Pojedynczy ustawienia logować](./media/ideascale-tutorial/ic790848.png "pojedynczy logować ustawienia")

1. Jako **typu jednokrotnego**, wybierz opcję **SAML 2.0**.
   
    ![Pojedynczy typ logować](./media/ideascale-tutorial/ic790849.png "pojedynczy typ logować")

1. Na **pojedynczego ustawienia logować** okno dialogowe, należy wykonać następujące czynności:
   
    ![Pojedynczy ustawienia logować](./media/ideascale-tutorial/ic790850.png "pojedynczy logować ustawienia")
   
    a. W **identyfikator jednostki dostawcy tożsamości SAML** pola tekstowego, Wklej wartość **identyfikator jednostki SAML** skopiowanej w witrynie Azure portal.

    b. Skopiuj zawartość pliku metadanych pobranych z witryny Azure portal, a następnie wklej go do **metadanych dostawcy tożsamości SAML** pola tekstowego.

    c. W **adres URL wylogowania Powodzenie** pola tekstowego, Wklej wartość **adres URL wylogowania** skopiowanej w witrynie Azure portal.

    d. Kliknij przycisk **Save Changes** (Zapisz zmiany).

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/ideascale-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/ideascale-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/ideascale-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/ideascale-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-an-ideascale-test-user"></a>Tworzenie użytkownika testowego IdeaScale

Aby umożliwić użytkownikom usługi Azure AD zalogować się do IdeaScale, ich musi być obsługiwana w celu IdeaScale. W przypadku IdeaScale Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby skonfigurować aprowizację użytkowników, wykonaj następujące czynności:**

1. Zaloguj się do Twojej **IdeaScale** witryny firmy jako administrator.

1. Przejdź do **ustawienia społeczności**.
   
    ![Ustawienia społeczności](./media/ideascale-tutorial/ic790847.png "ustawienia społeczności")

1. Przejdź do **podstawowych ustawień \> zarządzania elementu członkowskiego**.

1. Kliknij przycisk **Dodawanie elementu członkowskiego**.
   
    ![Element członkowski zarządzania](./media/ideascale-tutorial/ic790852.png "zarządzania elementu członkowskiego")

1. W sekcji Dodaj nowy element członkowski wykonaj następujące czynności:
   
    ![Dodaj nowy element członkowski](./media/ideascale-tutorial/ic790853.png "Dodaj nowy element członkowski")
   
    a. W **adresy E-mail** pole tekstowe, wpisz adres e-mail prawidłowego konta usługi AAD do aprowizowania.
   
    b. Kliknij przycisk **Save Changes** (Zapisz zmiany). 
   
    >[!NOTE]
    >Właściciel konta usługi Azure Active Directory otrzymuje wiadomość e-mail z linkiem do potwierdzenia konta, zanim stanie się aktywny.
      
>[!NOTE]
>Można użyć jakichkolwiek innych IdeaScale użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez IdeaScale do aprowizacji kont użytkowników usługi AAD.
 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do IdeaScale.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon IdeaScale, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **IdeaScale**.

    ![Konfigurowanie logowania jednokrotnego](./media/ideascale-tutorial/tutorial_ideascale_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego


Celem tej sekcji jest do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka IdeaScale w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji IdeaScale.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ideascale-tutorial/tutorial_general_01.png
[2]: ./media/ideascale-tutorial/tutorial_general_02.png
[3]: ./media/ideascale-tutorial/tutorial_general_03.png
[4]: ./media/ideascale-tutorial/tutorial_general_04.png

[100]: ./media/ideascale-tutorial/tutorial_general_100.png

[200]: ./media/ideascale-tutorial/tutorial_general_200.png
[201]: ./media/ideascale-tutorial/tutorial_general_201.png
[202]: ./media/ideascale-tutorial/tutorial_general_202.png
[203]: ./media/ideascale-tutorial/tutorial_general_203.png


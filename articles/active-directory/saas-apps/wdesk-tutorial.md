---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Wdesk | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Wdesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 06900a91-a326-4663-8ba6-69ae741a536e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.openlocfilehash: ee4e81d0af1648aa4ee61501c231585cf0b1cbfa
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54811778"
---
# <a name="tutorial-azure-active-directory-integration-with-wdesk"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Wdesk

W tym samouczku dowiesz się, jak zintegrować Wdesk w usłudze Azure Active Directory (Azure AD).

Integrowanie Wdesk z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Wdesk
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Wdesk (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz. [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Wdesk, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Wdesk logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Wdesk z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-wdesk-from-the-gallery"></a>Dodawanie Wdesk z galerii
Aby skonfigurować integrację Wdesk w usłudze Azure AD, należy dodać Wdesk z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Wdesk z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Wdesk**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/wdesk-tutorial/tutorial_wdesk_search.png)

1. W panelu wyników wybierz **Wdesk**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/wdesk-tutorial/tutorial_wdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Wdesk w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Wdesk do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Wdesk musi można ustanowić.

Ustanowieniu tej relacji łączy, przypisując wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** w Wdesk.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Wdesk, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Wdesk](#creating-a-wdesk-test-user)**  — aby odpowiednikiem Britta Simon w Wdesk połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Wdesk.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Wdesk, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Wdesk** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/wdesk-tutorial/tutorial_wdesk_samlbase.png)

1. Na **Wdesk domena i adresy URL** sekcji, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/wdesk-tutorial/tutorial_wdesk_url.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

1. Sprawdź **Pokaż zaawansowane ustawienia adresu URL**. Jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb, wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/wdesk-tutorial/tutorial_wdesk_url1.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`
     
    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Te wartości można uzyskać od WDesk portalu, podczas konfigurowania logowania jednokrotnego. 
  
1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/wdesk-tutorial/tutorial_wdesk_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/wdesk-tutorial/tutorial_general_400.png)
    
1. W oknie przeglądarki internetowej innej, zaloguj się do Wdesk jako Administrator zabezpieczeń.

1. W lewym dolnym rogu, kliknij przycisk **administratora** i wybierz polecenie **administrator konta**:
 
     ![Konfigurowanie logowania jednokrotnego](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

1. W Wdesk administratora, przejdź do **zabezpieczeń**, następnie **SAML** > **ustawienia języka SAML**:

    ![Konfigurowanie logowania jednokrotnego](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

1. W obszarze **ustawienia ogólne**, sprawdź **Włącz SAML logowania jednokrotnego**:

    ![Konfigurowanie logowania jednokrotnego](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

1. W obszarze **szczegóły dostawcy usługi**, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. Kopiuj **adres URL logowania** i wklej go w **adres Url logowania** pole tekstowe w witrynie Azure portal.
   
      b. Kopiuj **adres Url metadanych** i wklej go w **identyfikator** pole tekstowe w witrynie Azure portal.
       
      c. Kopiuj **adres url klienta** i wklej go w **adres Url odpowiedzi** pole tekstowe w witrynie Azure portal.
   
      d. Kliknij przycisk **Zapisz** w witrynie Azure portal, aby zapisać zmiany.      

1. Kliknij przycisk **Konfigurowanie ustawień protokołu IdP** otworzyć **edytowanie ustawień dostawców tożsamości** okna dialogowego. Kliknij przycisk **wybierz plik** zlokalizować **Metadata.xml** następnie przekaż plik został zapisany w witrynie Azure portal.
    
    ![Konfigurowanie logowania jednokrotnego](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
1. Kliknij przycisk **Zapisz zmiany**.

    ![Konfigurowanie logowania jednokrotnego](./media/wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/wdesk-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/wdesk-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/wdesk-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/wdesk-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-wdesk-test-user"></a>Tworzenie użytkownika testowego Wdesk

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do Wdesk, musi być obsługiwana w Wdesk. W Wdesk Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do Wdesk jako Administrator zabezpieczeń.
1. Przejdź do **administratora** > **konta administratora**.

     ![Konfigurowanie logowania jednokrotnego](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

1. Kliknij przycisk **członków** w obszarze **osób**.

1. Teraz klikaj polecenie **Dodawanie elementu członkowskiego** otworzyć **Dodawanie elementu członkowskiego** okno dialogowe. 
   
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/wdesk-tutorial/createuser1.png)  

1. W **użytkownika** tekstu wprowadź nazwę użytkownika, użytkownika, takich jak **brittasimon@contoso.com** i kliknij przycisk **Kontynuuj** przycisku.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/wdesk-tutorial/createuser3.png)

1.  Wprowadź szczegółowe informacje, jak pokazano poniżej:
  
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/wdesk-tutorial/createuser4.png)
 
    a. W **E-mail** tekstu wprowadź adres e-mail użytkownika, takich jak **brittasimon@contoso.com**.

    b. W polu tekstowym **First Name** (Imię) wprowadź imię użytkownika, na przykład **Britta**.

    c. W **nazwisko** tekstu wprowadź nazwisko użytkownika, takich jak **Simon**.

1. Kliknij przycisk **zapisać element członkowski** przycisku.  

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/wdesk-tutorial/createuser5.png)

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Wdesk.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Wdesk, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Wdesk**.

    ![Konfigurowanie logowania jednokrotnego](./media/wdesk-tutorial/tutorial_wdesk_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Wdesk w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Wdesk.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/wdesk-tutorial/tutorial_general_01.png
[2]: ./media/wdesk-tutorial/tutorial_general_02.png
[3]: ./media/wdesk-tutorial/tutorial_general_03.png
[4]: ./media/wdesk-tutorial/tutorial_general_04.png

[100]: ./media/wdesk-tutorial/tutorial_general_100.png

[200]: ./media/wdesk-tutorial/tutorial_general_200.png
[201]: ./media/wdesk-tutorial/tutorial_general_201.png
[202]: ./media/wdesk-tutorial/tutorial_general_202.png
[203]: ./media/wdesk-tutorial/tutorial_general_203.png


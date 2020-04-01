---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z usługą ADP | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a rozwiązaniem ADP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7be5331b-0481-48f7-9d6b-619dfec657e1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b031ded2022078c31bd8570c6a6317c398715480
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "70162661"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adp"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z usługą ADP

W tym samouczku dowiesz się, jak zintegrować usługę ADP z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi ADP z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do usługi ADP.
* Włącz użytkownikom automatyczne logowanie do usługi ADP za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną rejestracją jednokrotną (SSO) ADP.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Rozwiązanie ADP obsługuje logowanie jednokrotne inicjowane przez **dostawcę tożsamości**

> [!NOTE]
> Identyfikator tej aplikacji jest stałą wartością ciągu, więc tylko jedno wystąpienie można skonfigurować w jednej dzierżawie.

## <a name="adding-adp-from-the-gallery"></a>Dodawanie rozwiązania ADP z galerii

Aby skonfigurować integrację rozwiązania ADP z usługą Azure AD, należy z poziomu galerii dodać to rozwiązanie do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **ADP** w polu wyszukiwania.
1. Wybierz **ADP** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-adp"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla usługi ADP

Konfigurowanie i testowanie usługi Azure AD SSO przy użyciu protokołu ADP przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik syt/r działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem wsłudze ADP.

Aby skonfigurować i przetestować jednostkę SSO usługi Azure AD przy za pomocą usługi ADP, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
2. **[Skonfiguruj logowanie jednokrotne ADP](#configure-adp-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego ADP](#create-adp-test-user)** — aby mieć odpowiednik B.Simon w ADP, który jest połączony z reprezentacją użytkownika usługi Azure AD.
3. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W witrynie Azure Portal na stronie integracji aplikacji **ADP** kliknij kartę ** Właściwości**, a następnie wykonaj następujące kroki: 

    ![Właściwości logowania jednokrotnego](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. Ustaw pole **Możliwe logowanie użytkowników** na wartość **Tak**.

    b. Skopiuj wartość pola **Adres URL dostępu użytkownika** i wklej ją w **sekcji Adres URL logowania jednokrotnego**, co zostało opisane w dalszej części tego samouczka.

    d. Ustaw pole **Wymagane przypisanie użytkownika** na wartość **Tak**.

    d. Ustaw pole **Widoczne dla użytkowników** na wartość **Nie**.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **ADP** znajdź sekcję **Zarządzaj** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL:`https://fed.adp.com`

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź kod **XML metadanych federacji** i wybierz pozycję **Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie ADP** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B.Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do usługi ADP.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **ADP**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-adp-sso"></a>Konfigurowanie jednostki SSO ADP

Aby skonfigurować logowanie jednokrotne po stronie rozwiązania **ADP**, należy przekazać pobrany **plik XML metadanych** w [witrynie internetowej rozwiązania ADP](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Ten proces może potrwać kilka dni.

### <a name="configure-your-adp-services-for-federated-access"></a>Konfigurowanie usług ADP pod kątem dostępu federacyjnego

>[!Important]
> Twoi pracownicy, dla których wymagany jest dostęp federacyjny do usług ADP, muszą zostać przypisani do aplikacji usługi ADP, co wiąże się z koniecznością ponownego przypisania użytkowników do określonej usługi ADP.
Po otrzymaniu potwierdzenia od przedstawiciela firmy ADP skonfiguruj swoje usługi ADP, a następnie przypisz użytkowników lub zarządzaj nimi, aby kontrolować ich dostęp do określonej usługi ADP.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **ADP** w polu wyszukiwania.
1. Wybierz **ADP** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.
1. W witrynie Azure Portal na stronie integracji aplikacji **ADP** kliknij kartę ** Właściwości**, a następnie wykonaj następujące kroki:  

    ![Właściwości połączone z logami jednokrotnymi](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  Ustaw pole **Możliwe logowanie użytkowników** na wartość **Tak**.

    b.  Ustaw pole **Wymagane przypisanie użytkownika** na wartość **Tak**.

    d.  Ustaw pole **Widoczne dla użytkowników** na wartość **Tak**.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **ADP** znajdź sekcję **Zarządzaj** i wybierz pozycję **Logowanie jednokrotne**.

1. W oknie dialogowym **Wybierz metodę logowania jednokrotnego** w polu **Tryb** wybierz pozycję **Połączony**. w celu połączenia aplikacji z rozwiązaniem **ADP**.

    ![Połączone logowanie jednokrotne](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

1. Przejdź do sekcji **Konfigurowanie adresu URL logowania**, a następnie wykonaj następujące kroki:

    ![Właściwość logowania jednokrotnego](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)

    a. Wklej wartość pola **Adres URL dostępu użytkownika**, która została skopiowana z przedstawionej powyżej **karty właściwości** (z głównej aplikacji ADP).
                                                             
    b. Poniżej przedstawiono 5 aplikacji, które obsługują różne **adresy URL stanu przekaźnika**. Należy ręcznie dołączyć odpowiednią wartość **adresu URL stanu przekaźnika** dla określonej aplikacji w polu **Adres URL dostępu użytkownika**.
    
    * **ADP Workforce Now**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **ADP Workforce Now Enhanced Time**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **ADP Vantage HCM**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP Enterprise HR**

        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **Zapisz** zmiany.

10. Po otrzymaniu potwierdzenia od przedstawiciela firmy ADP rozpocznij testowanie przez jednego lub dwóch użytkowników.

    a. Przypisz kilku użytkowników do aplikacji usługi ADP, aby przetestować dostęp federacyjny.

    b. Test zostanie zakończony pomyślnie, gdy użytkownik uzyska dostęp do aplikacji usługi ADP w galerii i będzie mógł uzyskać dostęp do usługi ADP.
 
11. Jako potwierdzenie pomyślnego wykonania testu przypisz federacyjną usługę ADP do poszczególnych użytkowników lub grup użytkowników, co zostało wyjaśnione w dalszej części tego samouczka, i przekaż o tym informacje swoim pracownikom.

### <a name="create-adp-test-user"></a>Tworzenie użytkownika testowego rozwiązania ADP

Celem tej sekcji jest utworzenie użytkownika o nazwie B.Simon w ADP. Skontaktuj się z [zespołem pomocy technicznej rozwiązania ADP](https://www.adp.com/contact-us/overview.aspx) w celu dodania użytkowników w ramach konta rozwiązania ADP. 

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka ADP w panelu dostępu powinno nastąpić automatyczne zalogowanie do rozwiązania ADP, dla którego skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę ADP w usłudze Azure AD](https://aad.portal.azure.com)

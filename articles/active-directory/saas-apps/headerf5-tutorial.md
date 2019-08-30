---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) za pomocą klawisza F5 | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i F5.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 59a87abb-1ec1-4438-be07-5b115676115f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea4167f5a8f4e29641a999c72f57b368190a34e0
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70166093"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu klawisza F5

W tym samouczku dowiesz się, jak zintegrować F5 z Azure Active Directory (Azure AD). Po zintegrowaniu z usługą Azure AD za pomocą klawisza F5 można:

* Kontrolka w usłudze Azure AD, która ma dostęp do klawisza F5.
* Zezwól użytkownikom na automatyczne logowanie do programu F5 przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* F5 SSO można skonfigurować na trzy różne sposoby.

- [Konfigurowanie logowania jednokrotnego dla aplikacji na podstawie nagłówka](#configure-f5-single-sign-on-for-header-based-application)

- [Konfigurowanie logowania jednokrotnego dla aplikacji Kerberos](kerbf5-tutorial.md)

- [Konfigurowanie logowania jednokrotnego w przypadku zaawansowanej aplikacji Kerberos](advance-kerbf5-tutorial.md)

## <a name="adding-f5-from-the-gallery"></a>Dodawanie F5 z galerii

Aby skonfigurować integrację klawisza F5 z usługą Azure AD, musisz dodać F5 z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **F5** w polu wyszukiwania.
1. Wybierz pozycję **F5** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla F5

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą klawisza F5 przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w F5.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą klawisza F5, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj funkcję rejestracji jednokrotnej F5](#configure-f5-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego F5](#create-f5-test-user)** , aby dysponować odpowiednikiem B. Simon w F5, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj logowanie](#test-sso)** jednokrotne — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja z aplikacją **F5** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie**jednokrotne.
1. Na stronie **Wybierz metodę logowania** jednokrotnego wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<YourCustomFQDN>.f5.com/`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<YourCustomFQDN>.f5.com/`

1. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie F5** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do klawisza F5.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **F5**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-f5-sso"></a>Konfigurowanie rejestracji jednokrotnej F5

- [Konfigurowanie logowania jednokrotnego dla aplikacji Kerberos](kerbf5-tutorial.md)

- [Konfigurowanie logowania jednokrotnego w przypadku zaawansowanej aplikacji Kerberos](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-header-based-application"></a>Konfigurowanie logowania jednokrotnego dla aplikacji na podstawie nagłówka

1. Otwórz nowe okno przeglądarki sieci Web i zaloguj się do firmowej firmy jako administrator i wykonaj następujące czynności:

1. Należy zaimportować certyfikat metadanych do F5 (na podstawie nagłówka), który będzie używany później w procesie instalacji. Przejdź do pozycji **System > Certificate management > Zarządzanie certyfikatami ruchu > > listy certyfikatów SSL**. Kliknij pozycję **Importuj** w prawym górnym rogu

    ![Konfiguracja F5 (na podstawie nagłówka)](./media/headerf5-tutorial/configure12.png)
 
1. Ponadto potrzebny jest także **certyfikat SSL** dla nazwy hosta (`headerapp.superdemo.live`), w tym przykładzie użyto certyfikatu wieloznacznego.

    ![Konfiguracja F5 (na podstawie nagłówka)](./media/headerf5-tutorial/configure13.png)

1. Przejdź do – **F5 (nagłówek oparta na nagłówkach) Big-IP kliknij pozycję Access > konfiguracji z przewodnikiem > federacyjnej > dostawcy usług SAML**.

    ![Konfiguracja F5 (na podstawie nagłówka)](./media/headerf5-tutorial/configure01.png)

    ![Konfiguracja F5 (na podstawie nagłówka)](./media/headerf5-tutorial/configure02.png)
 
1. Określ **Identyfikator jednostki** (taki sam jak skonfigurowany w konfiguracji aplikacji usługi Azure AD)

    ![Konfiguracja F5 (na podstawie nagłówka)](./media/headerf5-tutorial/configure03.png) 

1. Utwórz nowy serwer wirtualny, określ **adres docelowy**, **port przekierowania** jest opcjonalny. Wybierz certyfikat z użyciem **certyfikatu** wieloznacznego (lub certyfikatu, który został przekazany do aplikacji), który został przekazany wcześniej, i **skojarzony klucz prywatny**.

    ![Konfiguracja F5 (na podstawie nagłówka)](./media/headerf5-tutorial/configure04.png) 

1. Przekaż **metadane** konfiguracji i określ nową **nazwę łącznika SAML dostawcy tożsamości** , a także należy określić certyfikat Federacji, który został przekazany wcześniej.

    ![Konfiguracja F5 (na podstawie nagłówka)](./media/headerf5-tutorial/configure05.png)
 
1. **Utwórz nowy** Pula aplikacji zaplecza, określ **adresy IP** serwerów aplikacji zaplecza.

    ![Konfiguracja F5 (na podstawie nagłówka)](./media/headerf5-tutorial/configure06.png)

1. W obszarze Logowanie jednokrotne wybierz opcję **oparty na nagłówku HTTP**. Możesz dodać inne nagłówki na podstawie swojej aplikacji. Zapoznaj się z listą zmiennych sesji SAML

    ![Konfiguracja F5 (na podstawie nagłówka)](./media/headerf5-tutorial/configure07.png) 

1. Kontakt z pomocą [techniczną (nagłówek oparta na nagłówku)](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) , aby uzyskać szczegółowe informacje dotyczące **Właściwości** .

    ![Konfiguracja F5 (na podstawie nagłówka)](./media/headerf5-tutorial/configure08.png)

1. Skontaktuj się z działem [obsługi klienta F5 (nagłówek based)](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) , aby uzyskać szczegółowe informacje o **właściwościach zarządzania sesją** .

    ![Konfiguracja F5 (na podstawie nagłówka)](./media/headerf5-tutorial/configure09.png)

1. **Przejrzyj podsumowanie** i kliknij pozycję **Wdróż**.

    ![Konfiguracja F5 (na podstawie nagłówka)](./media/headerf5-tutorial/configure10.png)

    ![Konfiguracja F5 (na podstawie nagłówka)](./media/headerf5-tutorial/configure11.png)

### <a name="create-f5-test-user"></a>Utwórz użytkownika testowego F5

W tej sekcji utworzysz użytkownika o nazwie B. Simon w F5. Pracuj z [zespołem pomocy technicznej dla klientów F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) , aby dodać użytkowników na platformie F5. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego. 

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka F5 w panelu dostępu należy automatycznie zalogować się do F5, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę Azure AD](https://aad.portal.azure.com/)

- [Konfigurowanie logowania jednokrotnego dla aplikacji Kerberos](kerbf5-tutorial.md)

- [Konfigurowanie logowania jednokrotnego w przypadku zaawansowanej aplikacji Kerberos](advance-kerbf5-tutorial.md)


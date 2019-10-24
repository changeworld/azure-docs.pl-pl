---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z pakietem servicesuite | Microsoft Docs'
description: Dowiedz się, jak można skonfigurować funkcję logowania jednokrotnego między usługą Azure Active Directory i aplikacją NetSuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d578b5d08fecde733bb7b257057e480fef83c4e
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754418"
---
# <a name="tutorial-integrate-azure-ad-single-sign-on-sso-with-netsuite"></a>Samouczek: integracja logowania jednokrotnego w usłudze Azure AD z pakietem

W tym samouczku dowiesz się, jak zintegrować pakiet z usługą Azure Active Directory (Azure AD). Podczas integrowania pakietu z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do pakietu.
* Zezwól użytkownikom na automatyczne logowanie do pakietu z użyciem kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji, Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja obsługująca Logowanie jednokrotne (SSO) dla pakietu.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. 

Pakiet servicesuite obsługuje:

* Zainicjowane przez dostawcy tożsamości Logowanie jednokrotne.
* Inicjowanie obsługi administracyjnej użytkowników w trybie JIT (just-in-Time).
* [Automatyczne Inicjowanie obsługi użytkowników](NetSuite-provisioning-tutorial.md).

> [!NOTE]
> Ponieważ identyfikator tej aplikacji jest stałą wartością ciągu, można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="add-netsuite-from-the-gallery"></a>Dodaj pakiet z galerii

Aby skonfigurować integrację pakietu w usłudze Azure AD, Dodaj pakiet z galerii do listy zarządzanych aplikacji SaaS, wykonując następujące czynności:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) za pomocą konta służbowego lub konto Microsoft prywatnego.
1. W okienku po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **pakiet** w polu wyszukiwania.
1. W okienku wyników wybierz pozycję **zestaw**, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netsuite"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla pakietu

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą pakietu z użyciem użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w pakiecie.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą pakietu, wykonaj następujące bloki konstrukcyjne:

1. [Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso) , aby umożliwić użytkownikom korzystanie z tej funkcji.

    a. [Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą użytkownika B. Simon.  
    b. [Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user) aby umożliwić użytkownikowi B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. [Skonfiguruj logowanie](#configure-netsuite-sso) jednokrotne w ramach pakietu, aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * [Utwórz użytkownika testowego pakietu](#create-the-netsuite-test-user) dla sieci, aby dysponować odpowiednikiem użytkownika B. Simon w pakiecie, który jest połączony z reprezentacją usługi Azure AD.
1. [Przetestuj Logowanie jednokrotne](#test-sso) , aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie **integracja aplikacji sieci** Web Znajdź sekcję **Zarządzanie** , a następnie wybierz pozycję **Logowanie jednokrotne**.
1. W okienku **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. W okienku **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** wybierz ikonę **Edytuj** ("ołówek") obok pozycji **Podstawowa konfiguracja SAML**.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** , w polu tekstowym **adres URL odpowiedzi** wpisz adres URL w jednym z następujących formatów:

    ```
    https://<tenant-name>.NetSuite.com/saml2/acs
    https://<tenant-name>.na1.NetSuite.com/saml2/acs
    https://<tenant-name>.na2.NetSuite.com/saml2/acs
    https://<tenant-name>.sandbox.NetSuite.com/saml2/acs
    https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs
    https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs
    ```

    > [!NOTE]
    > Wartości w poprzednich adresach URL nie są prawdziwe. Zaktualizuj je za pomocą adresu URL rzeczywistej odpowiedzi. Aby uzyskać wartość, skontaktuj się z [zespołem obsługi klienta dla pakietu](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml). Można również odnieść się do formatów przedstawionych w sekcji podstawowe informacje o **konfiguracji SAML** w Azure Portal.

    Aplikacja sieci oczekuje, że potwierdzenia SAML będą wyświetlane w określonym formacie. Musisz dodać niestandardowe mapowania atrybutów do konfiguracji atrybutów tokenu SAML. 
    
1. Aby otworzyć okienko **atrybuty użytkownika** , wybierz ikonę **Edytuj** ("ołówek"). W okienku zostanie wyświetlona lista atrybutów domyślnych, jak pokazano na poniższej ilustracji: 

    ![Okienko atrybuty użytkownika](common/edit-attribute.png)

    Oprócz tych atrybutów aplikacja dla pakietu może oczekiwać kilku atrybutów do przekazania z powrotem do odpowiedzi SAML. 

1. W okienku **atrybuty użytkownika** w obszarze **oświadczenia użytkownika**wykonaj następujące kroki, aby dodać atrybut tokenu SAML przedstawiony w poniższej tabeli:

    | Nazwa | Atrybut źródłowy | 
    | ---------------| --------------- |
    | account  | `account id` |

    a. Wybierz pozycję **Dodaj nowe oświadczenie** , aby otworzyć okienko **Zarządzanie oświadczeniami użytkowników** .

    b. W polu **Nazwa** wpisz nazwę atrybutu, który jest pokazywany dla tego wiersza.

    d. Pozostaw puste pole **obszar nazw** .

    d. Z listy rozwijanej **Źródło** wybierz pozycję **atrybut**.

    e. Na liście **atrybutów źródłowych** wprowadź wartość atrybutu, która jest wyświetlana dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Wybierz pozycję **Zapisz**.

    >[!NOTE]
    >Wartość atrybutu Account nie jest prawdziwa. Ta wartość zostanie zaktualizowana zgodnie z opisem w dalszej części tego samouczka.

1. W okienku **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź pozycję **XML metadanych Federacji**.

1. Wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie pakietu** , skopiuj odpowiedni adres URL lub adresy URL, w zależności od wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku Azure Portal wybierz pozycję **Azure Active Directory**  > **Użytkownicy**  > **Wszyscy użytkownicy**.

1. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

1. W okienku właściwości **użytkownika** wykonaj następujące kroki:

   a. W polu **Nazwa** wprowadź wartość **B. Simon**.  
   b. W polu **Nazwa użytkownika** wprowadź username@companydomain.extension (na przykład B.Simon@contoso.com).  
   d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.  
   d. Wybierz pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji zostanie włączona funkcja User B. Simon, która umożliwia logowanie jednokrotne na platformie Azure przez przyznanie dostępu do pakietu.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Z listy Aplikacje wybierz pozycję **zestaw**.
1. W okienku Przegląd poszukaj sekcji **Zarządzanie** , a następnie wybierz łącze **Użytkownicy i grupy** .

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika** , a następnie w okienku **Dodaj przypisanie** wybierz pozycję **Użytkownicy i grupy**.

    ![Przycisk "Dodaj użytkownika"](common/add-assign-user.png)

1. W okienku **Użytkownicy i grupy** , na liście rozwijanej **Użytkownicy** wybierz pozycję **B. Simon**, a następnie wybierz przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, wykonaj następujące czynności:

   a. W okienku **Wybierz rolę** z listy rozwijanej wybierz odpowiednią rolę dla użytkownika.  
   b. W dolnej części ekranu wybierz przycisk **Wybierz** .
1. W okienku **Dodaj przypisanie** wybierz przycisk **Przypisz** .

## <a name="configure-netsuite-sso"></a>Konfigurowanie logowania jednokrotnego dla pakietu

1. Otwórz nową kartę w przeglądarce i zaloguj się do swojej witryny firmowej jako administrator.

2. Na górnym pasku nawigacyjnym wybierz pozycję **Instalator**, a następnie wybierz pozycję **firma**  > **włączyć funkcje**.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-setupsaml.png)

3. Na pasku narzędzi w środku strony wybierz pozycję **SuiteCloud**.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-suitecloud.png)

4. W obszarze **Zarządzanie uwierzytelnianiem**zaznacz pole wyboru Protokół **SAML logowanie** jednokrotne, aby włączyć opcję logowania jednokrotnego protokołu SAML w zestawie.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-ticksaml.png)

5. Na górnym pasku nawigacyjnym wybierz pozycję **Konfiguracja**.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-setup.png)

6. Na liście **zadania Instalatora** wybierz pozycję **integracja**.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-integration.png)

7. W obszarze **Zarządzanie uwierzytelnianiem**wybierz pozycję **SAML logowanie**jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-saml.png)

8. W okienku **Instalatora SAML** w obszarze **Konfiguracja pakietu**, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Zaznacz pole wyboru **podstawowa metoda uwierzytelniania** .

    b. W obszarze **metadane dostawcy tożsamości SAMLV2**wybierz pozycję **Przekaż plik metadanych dostawcy tożsamości**, a następnie wybierz pozycję **Przeglądaj** , aby przekazać pobrany plik metadanych z Azure Portal.

    d. Wybierz pozycję **Prześlij**.

9. Na górnym pasku nawigacyjnym pakietu, wybierz pozycję **Instalator**, a następnie wybierz pozycję **firma**  > **Informacje o**firmie.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-com.png)

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-account-id.png)

    b. W okienku **Informacje o firmie** w prawej kolumnie skopiuj wartość **Identyfikator konta** .

    d. Wklej **Identyfikator konta** skopiowany z konta usługi w polu **wartość atrybutu** w usłudze Azure AD. 

10. Zanim użytkownicy będą mogli wykonywać logowanie jednokrotne do aplikacji NetSuite, należy im wcześniej przypisać odpowiednie uprawnienia w tej aplikacji. Aby przypisać te uprawnienia, wykonaj następujące czynności:

    a. Na górnym pasku nawigacyjnym wybierz pozycję **Konfiguracja**.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-setup.png)

    b. W lewym okienku wybierz pozycję **użytkownicy/role**, a następnie wybierz pozycję **Zarządzaj rolami**.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-manage-roles.png)

    d. Wybierz pozycję **Nowa rola**.

    d. Wprowadź **nazwę** nowej roli.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-new-role.png)

    e. Wybierz pozycję **Zapisz**.

    f. Na górnym pasku nawigacyjnym wybierz pozycję **uprawnienia**. Następnie wybierz pozycję **Konfiguracja**.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-sso.png)

    g. Wybierz pozycję **SAML logowanie**jednokrotne, a następnie wybierz pozycję **Dodaj**.

    h. Wybierz pozycję **Zapisz**.

    i. Na górnym pasku nawigacyjnym wybierz pozycję **Instalator**, a następnie wybierz pozycję **Menedżer instalacji**.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-setup.png)

    j. W lewym okienku wybierz pozycję **użytkownicy/role**, a następnie wybierz pozycję **Zarządzaj użytkownikami**.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Wybierz użytkownika testowego, wybierz pozycję **Edytuj**, a następnie wybierz kartę **dostęp** .

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-edit-user.png)

    l. W okienku **role** Przypisz odpowiednią rolę utworzoną przez użytkownika.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-add-role.png)

    m. Wybierz pozycję **Zapisz**.

### <a name="create-the-netsuite-test-user"></a>Tworzenie użytkownika testowego pakietu

W tej sekcji użytkownik o nazwie B. Simon został utworzony w pakiecie. Aplikacja NetSuite obsługuje aprowizację użytkowników just in time, która jest domyślnie włączona. W tej sekcji nie ma elementu Action. Jeśli użytkownik jeszcze nie istnieje w aplikacji NetSuite, zostanie utworzony po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po wybraniu kafelka pakietu w panelu dostępu należy automatycznie zalogować się do pakietu, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Wypróbuj pakiet z usługą Azure AD](https://aad.portal.azure.com/)


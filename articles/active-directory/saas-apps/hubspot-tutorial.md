---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą HubSpot | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i HubSpot.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 57343ccd-53ea-4e62-9e54-dee2a9562ed5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b2c765778fc2bdd8425cc3f375831c0d317e753
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67100890"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą HubSpot

W tym samouczku dowiesz się, jak zintegrować HubSpot w usłudze Azure Active Directory (Azure AD).

Integrowanie HubSpot z usługą Azure AD zapewnia następujące korzyści:

* Możesz użyć usługi Azure AD w celu kontrolowania, kto ma dostęp do HubSpot.
* Użytkownicy mogą być automatycznie zalogowany do HubSpot za pomocą kont usługi Azure AD (logowanie jednokrotne).
* Możesz zarządzać kontami z jednej centralnej lokalizacji — witryny Azure Portal.

Aby uzyskać więcej informacji na temat oprogramowania jako usługi (SaaS) integracji aplikacji z usługą Azure AD, zobacz [logowanie jednokrotne do aplikacji w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą HubSpot, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcję usługi Azure AD, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed przystąpieniem do wykonywania.
* HubSpot subskrypcji przy użyciu logowania jednokrotnego włączone.

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka konfigurowania i testowania usługi Azure AD logowanie jednokrotne w środowisku testowym, a integracja HubSpot z usługą Azure AD.

HubSpot obsługuje następujące funkcje:

* **Zainicjowane przez Dostawcę logowania jednokrotnego**
* **Logowanie jednokrotne inicjowane przez dostawcę tożsamości**

## <a name="add-hubspot-in-the-azure-portal"></a>Dodaj HubSpot w witrynie Azure portal

Aby zintegrować HubSpot z usługą Azure AD, należy dodać HubSpot z listą zarządzanych aplikacji SaaS.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W menu po lewej stronie wybierz **usługi Azure Active Directory**.

    ![Opcja usługi Azure Active Directory](common/select-azuread.png)

1. Wybierz pozycję **Aplikacje dla przedsiębiorstw** > **Wszystkie aplikacje**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Aby dodać aplikację, wybierz pozycję **nową aplikację**.

    ![Nowa opcja aplikacji](common/add-new-app.png)

1. W polu wyszukiwania wprowadź **HubSpot**. W wynikach wyszukiwania wybierz **HubSpot**, a następnie wybierz pozycję **Dodaj**.

    ![HubSpot na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą HubSpot, w oparciu o użytkownika testu o nazwie **Britta Simon**. Dla logowania jednokrotnego do pracy należy ustanowić połączone relację między użytkownikiem usługi Azure AD i powiązanych użytkowników w HubSpot.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą HubSpot, należy wykonać poniższe bloki konstrukcyjne:

| Zadanie | Opis |
| --- | --- |
| **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)** | Umożliwia użytkownikom korzystać z tej funkcji. |
| **[Konfigurowanie HubSpot logowania jednokrotnego](#configure-hubspot-single-sign-on)** | Konfiguruje pojedynczy ustawień logowania jednokrotnego w aplikacji. |
| **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** | Testy usługi Azure AD logowanie jednokrotne dla użytkownika o nazwie Britta Simon. |
| **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** | Umożliwia Britta Simon korzystać z usługi Azure AD logowania jednokrotnego. |
| **[Tworzenie użytkownika testowego HubSpot](#create-a-hubspot-test-user)** | Tworzy odpowiednikiem Britta Simon HubSpot, połączonego z usługi Azure AD reprezentacja użytkownika. |
| **[Testowanie logowania jednokrotnego](#test-single-sign-on)** | Sprawdza, czy konfiguracja działa. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz usługi Azure AD logowanie jednokrotne za pomocą HubSpot w witrynie Azure portal.

1. W [witryny Azure portal](https://portal.azure.com/)w **HubSpot** okienko integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Konfigurowanie opcji rejestracji jednokrotnej](common/select-sso.png)

1. W **wybierz jedną metodę logowania jednokrotnego** okienku wybierz **SAML** lub **SAML/WS-Fed** trybu, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

1. W **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML** okienku wybierz **Edytuj** (ikonę ołówka) aby otworzyć **podstawową konfigurację protokołu SAML** okienka.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W **podstawową konfigurację protokołu SAML** okienko, aby skonfigurować *inicjowane przez dostawcę tożsamości tryb*, wykonaj następujące czynności:

    1. W **identyfikator** wprowadź adres URL z następującym wzorcem: https:\//api.hubspot.com/login-api/v1/saml/login?portalId=\<identyfikator klienta\>.

    1. W **adres URL odpowiedzi** wprowadź adres URL z następującym wzorcem: https:\//api.hubspot.com/login-api/v1/saml/acs?portalId=\<identyfikator klienta\>.

    ![HubSpot domena i adresy URL pojedynczy informacje logowania jednokrotnego](common/idp-intiated.png)

    > [!NOTE]
    > Aby sformatować adresy URL, znajdują się w do wzorców wyświetlane w **podstawową konfigurację protokołu SAML** okienko w witrynie Azure portal.

1. Aby skonfigurować aplikację w *zainicjowanego przez dostawcę usług* trybu:

    1. Wybierz **Ustaw dodatkowe adresy URL**.

    1. W **adres URL logowania** wprowadź **https:\//app.hubspot.com/login**.

    ![Opcja Set dodatkowe adresy URL](common/metadata-upload-additional-signon.png)

1. W **Ustaw się logowanie jednokrotne z SAML** okienko w **certyfikat podpisywania SAML** zaznacz **Pobierz** obok **certyfikat (Base64)** . Wybierz opcję pobierania, w zależności od wymagań. Zapisz certyfikat na komputerze.

    ![Opcja pobierania certyfikat (Base64)](common/certificatebase64.png)

1. W **Konfigurowanie HubSpot** sekcji, skopiuj następujące adresy URL, zgodnie z wymaganiami:

    * Adres URL logowania
    * Identyfikator usługi Azure AD
    * Adres URL wylogowywania

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-hubspot-single-sign-on"></a>Konfigurowanie HubSpot logowania jednokrotnego

1. Otwórz nową kartę w przeglądarce i zaloguj się do konta administratora HubSpot.

1. Wybierz **ustawienia** ikonę w prawym górnym rogu strony.

    ![Ikona ustawień w HubSpot](./media/hubspot-tutorial/config1.png)

1. Wybierz **konto domyślne**.

    ![Opcja ustawienia domyślne konto w HubSpot](./media/hubspot-tutorial/config2.png)

1. Przewiń w dół do **zabezpieczeń** sekcji, a następnie wybierz **Konfigurowanie**.

    ![Konfigurowanie opcji HubSpot](./media/hubspot-tutorial/config3.png)

1. W **Konfigurowanie logowania jednokrotnego** sekcji, wykonaj następujące czynności:

    1. W **odbiorców adresu URl (identyfikator jednostki usługi dostawcy)** wybierz opcję **kopiowania** Aby skopiować wartość. W witrynie Azure portal w **podstawową konfigurację protokołu SAML** okienko, Wklej wartość w **identyfikator** pole.

    1. W **logowanie się na adres URl, ACS, odbiorca lub przekierowania** wybierz opcję **kopiowania** Aby skopiować wartość. W witrynie Azure portal w **podstawową konfigurację protokołu SAML** okienko, Wklej wartość w **adres URL odpowiedzi** pole.

    1. W HubSpot w **identyfikator dostawcy tożsamości lub adres URL wystawcy** pole, Wklej wartość **usługi Azure AD identyfikator** skopiowaną w witrynie Azure portal.

    1. W HubSpot w **pojedynczy znak na adres URL dostawcy tożsamości** pole, Wklej wartość **adres URL logowania** skopiowaną w witrynie Azure portal.

    1. Otwórz pobrany plik Certificate(Base64) w Notatniku Windows. Zaznacz i skopiuj zawartość pliku. Następnie w HubSpot, wklej go w **certyfikat X.509** pole.

    1. Wybierz **Sprawdź**.

        ![Konfigurowanie pojedynczego logowania jednokrotnego w temacie HubSpot](./media/hubspot-tutorial/config4.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w witrynie Azure Portal.

1. W witrynie Azure Portal wybierz kolejno pozycje **Azure Active Directory** > **Użytkownicy** > **Wszyscy użytkownicy**.

    ![Użytkownicy i wszystkie opcje użytkowników](common/users.png)

1. Wybierz przycisk **Nowy użytkownik**.

    ![Nowa opcja użytkownika](common/new-user.png)

1. W okienku **Użytkownik** wykonaj następujące czynności:

    1. W polu **Nazwa** wpisz **BrittaSimon**.
  
    1. W **nazwa_użytkownika** wprowadź **brittasimon\@\<Twojej domeny firmy >.\< rozszerzenie\>** . Na przykład **brittasimon\@contoso.com**.

    1. Wybierz **hasło Show** pole wyboru. Zanotuj wartość, która jest wyświetlana w **hasło** pole.

    1. Wybierz pozycję **Utwórz**.

    ![W okienku użytkownika](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz udzielić dostępu Britta Simon HubSpot, dzięki czemu korzystaniem Azure logowania jednokrotnego.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje** > **HubSpot**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Na liście aplikacji wybierz **HubSpot**.

    ![HubSpot na liście aplikacji](common/all-applications.png)

1. W menu wybierz pozycję **Użytkownicy i grupy**.

    ![Opcja użytkowników i grup](common/users-groups-blade.png)

1. Wybierz przycisk **Dodaj użytkownika**. Następnie w okienku **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

1. W **użytkowników i grup** okienku wybierz **Britta Simon** na liście Użytkownicy. Wybierz pozycję **Wybierz**.

1. Jeśli są oczekiwane wartości roli dla asercji SAML w **wybierz rolę** okienku zaznacz odpowiednie rolę dla użytkownika z listy. Wybierz pozycję **Wybierz**.

1. W **Dodaj przydziału** okienku wybierz **przypisać**.

### <a name="create-a-hubspot-test-user"></a>Tworzenie użytkownika testowego HubSpot

Aby włączyć usługi Azure AD użytkownika do logowania się na HubSpot, użytkownik musi być obsługiwana w HubSpot. W HubSpot Inicjowanie obsługi administracyjnej jest zadanie ręczne.

Aby aprowizować konto użytkownika w HubSpot:

1. Zaloguj się do witryny firmy HubSpot jako administrator.

1. Wybierz **ustawienia** ikonę w prawym górnym rogu strony.

    ![Ikona ustawień w HubSpot](./media/hubspot-tutorial/config1.png)

1. Wybierz **użytkownicy i zespoły**.

    ![Opcja użytkownicy i zespoły w HubSpot](./media/hubspot-tutorial/user1.png)

1. Wybierz **Utwórz użytkownika**.

    ![Opcja Utwórz użytkownika w HubSpot](./media/hubspot-tutorial/user2.png)

1. W **Dodaj adres e-mail addess(es)** wprowadź adres e-mail użytkownika w formacie brittasimon\@contoso.com, a następnie wybierz **dalej**.

    ![Dodaj adresy e-mail pola w sekcji Tworzenie użytkowników w HubSpot](./media/hubspot-tutorial/user3.png)

1. W **tworzenia użytkowników** wybierz każdą kartę. Na każdej karcie należy ustawić odpowiednich opcji i uprawnienia dla użytkownika. Następnie wybierz opcję **Dalej**.

    ![Karty w sekcji Tworzenie użytkowników w HubSpot](./media/hubspot-tutorial/user4.png)

1. Aby wysłać zaproszenia do użytkownika, zaznacz **wysyłania**.

    ![Opcja wysyłania w HubSpot](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > Użytkownik zostanie aktywowany po użytkownik akceptuje zaproszenie.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji możesz przetestować konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu portalu Moje aplikacje.

Po skonfigurowaniu rejestracji jednokrotnej, po wybraniu **HubSpot** w portalu Moje aplikacje są automatycznie zarejestrowaniu w usłudze HubSpot. Aby uzyskać więcej informacji na temat portalu Moje aplikacje, zobacz [dostępu i użycia aplikacji w portalu Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej, przejrzyj następujące artykuły:

- [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

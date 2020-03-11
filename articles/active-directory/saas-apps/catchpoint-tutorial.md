---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą punkt przechwytywania | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i punkt przechwytywania.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab3eead7-8eb2-4c12-bb3a-0e46ec899d37
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c56d34a331821ffbc3d0d4f2cf5e9b033f4011ff
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2020
ms.locfileid: "78968491"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-catchpoint"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą punkt przechwytywania

W tym samouczku dowiesz się, jak zintegrować usługę punkt przechwytywania z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi punkt przechwytywania z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do punkt przechwytywania.
* Zezwól użytkownikom na automatyczne logowanie się do usługi punkt przechwytywania przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) punkt przechwytywania.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Punkt przechwytywania obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne
* Punkt przechwytywania obsługuje Inicjowanie obsługi użytkowników **just in Time**
* Po skonfigurowaniu punkt przechwytywania można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-catchpoint-from-the-gallery"></a>Dodawanie punkt przechwytywania z galerii

Aby skonfigurować integrację programu punkt przechwytywania z usługą Azure AD, musisz dodać punkt przechwytywania z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **punkt przechwytywania** w polu wyszukiwania.
1. Wybierz pozycję **punkt przechwytywania** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-catchpoint"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla punkt przechwytywania

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą punkt przechwytywania przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w punkt przechwytywania.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą punkt przechwytywania, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-catchpoint-sso)** w usłudze punkt przechwytywania, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego punkt przechwytywania](#create-catchpoint-test-user)** , aby dysponować odpowiednikiem B. Simon w punkt przechwytywania, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **punkt przechwytywania** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania** jednokrotnego przy użyciu języka SAML kliknij ikonę Edytuj/pióro, aby **skonfigurować podstawową konfigurację SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL: `https://portal.catchpoint.com/SAML2`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL: `https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. Aplikacja punkt przechwytywania oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/default-attributes.png)

1. Oprócz powyższych, aplikacja punkt przechwytywania oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.

    | Name (Nazwa) | Atrybut źródłowy|
    | ------------ | --------- |
    | przestrzeń nazw | user.assignedrole |

    > [!NOTE]
    > w przypadku wystąpienia przestrzeni nazw należy zamapować nazwę konta. Nazwa tego konta powinna być skonfigurowana jako rola w usłudze Azure AD, która zostanie przeniesiona z powrotem w odpowiedzi SAML. Zapoznaj się z tym [artykułem](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) , aby dowiedzieć się, jak skonfigurować role

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie punkt przechwytywania** skopiuj odpowiednie adresy URL na podstawie wymagania.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi punkt przechwytywania.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **punkt przechwytywania**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-catchpoint-sso"></a>Konfigurowanie logowania jednokrotnego punkt przechwytywania

1. W innym oknie przeglądarki sieci Web Zaloguj się do aplikacji punkt przechwytywania jako administrator.

1. Kliknij ikonę **Ustawienia** , a następnie wybierz pozycję **dostawca tożsamości logowania jednokrotnego**.

    ![Konfiguracja punkt przechwytywania](./media/catchpoint-tutorial/configuration1.png)

1. Na stronie **Logowanie** jednokrotne wykonaj następujące czynności:

    ![Konfiguracja punkt przechwytywania](./media/catchpoint-tutorial/configuration2.png)

    1. W polu tekstowym **przestrzeń nazw** Wprowadź prawidłową wartość przestrzeni nazw.

    1. W polu tekstowym **Identity Provider Issuer** (Wystawca dostawcy tożsamości) wprowadź wartość pola **Identyfikator usługi Azure AD** skopiowaną z witryny Azure Portal.

    1. W polu tekstowym **adres URL** logowania jednokrotnego wprowadź wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    1. Otwórz plik pobranego **certyfikatu (base64)** w Notatniku, skopiuj zawartość pliku certyfikatu i wklej go do pola tekstowego **certyfikatu** .

    1. Możesz również przekazać **plik XML metadanych Federacji** , klikając opcję **Przekaż metadane** .

    1. Kliknij przycisk **Save** (Zapisz).

### <a name="create-catchpoint-test-user"></a>Utwórz użytkownika testowego punkt przechwytywania

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w punkt przechwytywania. Punkt przechwytywania obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w usłudze punkt przechwytywania, zostanie utworzony nowy po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka punkt przechwytywania w panelu dostępu należy automatycznie zalogować się do punkt przechwytywania, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

> [!NOTE]
> Gdy logujesz się do aplikacji punkt przechwytywania za pomocą strony logowania, po podaniu **poświadczeń punkt przechwytywania**Wprowadź prawidłową wartość **przestrzeni nazw** w polu tekstowym **poświadczenia firmy (SSO)** i kliknij przycisk **Zaloguj**.

![Konfiguracja punkt przechwytywania](./media/catchpoint-tutorial/loginimage.png)

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj punkt przechwytywania z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
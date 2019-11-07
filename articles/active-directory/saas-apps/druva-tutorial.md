---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Druva | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Druva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16b23ef246561d052935642c323c2d830e21cbe7
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "73570189"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-druva"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą Druva

W tym samouczku dowiesz się, jak zintegrować usługę Druva z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Druva z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Druva.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Druva przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Druva.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Druva obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-druva-from-the-gallery"></a>Dodawanie aplikacji Druva z galerii

Aby skonfigurować integrację aplikacji Druva z usługą Azure AD, należy dodać tę aplikację z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Druva** w polu wyszukiwania.
1. Wybierz pozycję **Druva** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-druva"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla Druva

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Druva przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Druva.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Druva, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-druva-sso)** w usłudze Druva, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego Druva](#create-druva-test-user)** , aby dysponować odpowiednikiem B. Simon w Druva, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Druva** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

1. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** nie musisz wykonywać żadnych kroków, ponieważ aplikacja jest już wstępnie zintegrowana z platformą Azure.

1. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://login.druva.com/api/commonlogin/samlconsume`

1. Kliknij pozycję **Zapisz**.

1. Aplikacja Druva oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/default-attributes.png)

1. Oprócz powyższych, aplikacja Druva oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy|
    | ------------------- | -------------------- |
    | emailAddress | User. email |
    | druva_auth_token | Token logowania jednokrotnego został wygenerowany z poziomu konsoli administratora o postaci DCP bez znaków cudzysłowu.  Na przykład: X-XXXXX-XXXX-S-A-M-P-L-E + TXOXKXEXNX =. Platforma Azure automatycznie dodaje znaki cudzysłowu wokół tokenu uwierzytelniania. |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie Druva** skopiuj odpowiednie adresy URL na podstawie wymagania.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Druva.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Druva**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-druva-sso"></a>Konfigurowanie logowania jednokrotnego Druva

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny Druva jako administrator.

1. Kliknij logo Druva w lewym górnym rogu, a następnie kliknij pozycję **Druva ustawienia chmury**.

    ![Ustawienia](./media/druva-tutorial/ic795091.png "Ustawienia")

1. Na karcie **Logowanie** jednokrotne kliknij pozycję **Edytuj**.

    ![Ustawienia logowania jednokrotnego](./media/druva-tutorial/ic795092.png "Ustawienia logowania jednokrotnego")

1. Na stronie **Edytuj ustawienia rejestracji** jednokrotnej wykonaj następujące czynności:

    ![Ustawienia logowania jednokrotnego](./media/druva-tutorial/ic795095.png "Ustawienia logowania jednokrotnego")

    1. W polu tekstowym **ID Provider Login URL** (Adres URL logowania dostawcy identyfikatorów) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    1. Otwórz certyfikat kodowany algorytmem base-64 w Notatniku, skopiuj jego zawartość do schowka, a następnie wklej ją w polu tekstowym **ID Provider Certificate** (Certyfikat dostawcy identyfikatorów).

       > [!NOTE]
       > Aby włączyć logowanie jednokrotne dla administratorów, wybierz pozycję **administratorzy Zaloguj się do chmury Druva za pomocą dostawcy rejestracji jednokrotnej** i **Zezwalaj na dostęp failsafe do usługi Druva (zalecane)** . Druva zaleca włączenie usługi **failsafe dla administratorów** , aby musieli uzyskać dostęp do konsoli programu DCP w przypadku wystąpienia błędów w dostawcy tożsamości. Umożliwia także administratorom używanie hasła SSO i DCP w celu uzyskania dostępu do konsoli programu.

    1. Kliknij pozycję **Zapisz**. Dzięki temu dostęp do platformy chmurowej Druva przy użyciu logowania jednokrotnego.

### <a name="create-druva-test-user"></a>Tworzenie użytkownika testowego w aplikacji Druva

W tej sekcji użytkownik o nazwie B. Simon został utworzony w Druva. Druva obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w usłudze Druva, zostanie utworzony nowy po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Druva w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Druva, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj Druva z usługą Azure AD](https://aad.portal.azure.com/)
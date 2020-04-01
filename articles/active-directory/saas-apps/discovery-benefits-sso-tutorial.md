---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z logiem jednokrotnym dotyczącymi korzyści odnajdywania | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a logowanie jednokrotne korzyści odnajdywania.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a788cd07-0eed-4067-b79d-19b840e8836d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64c4a6811ef5d7ed4f29c7dae89561616895a42a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72266143"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-discovery-benefits-sso"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z logiem jednokrotnym dotyczącymi korzyści odnajdywania

W tym samouczku dowiesz się, jak zintegrować samouszczącą usługę Discovery Benefits z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Discovery Benefits SSO z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do usługi Discovery Benefits SSO.
* Włącz użytkownikom automatyczne logowanie się do logowania logowania odnajdywania korzyści za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Discovery Benefits SSO jednokrotnie logowanie (Logowanie jednokrotne) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Odnajdowanie korzyści SSO obsługuje **IDP** zainicjowane SSO

> [!NOTE]
> Identyfikator tej aplikacji jest stałą wartością ciągu, więc tylko jedno wystąpienie można skonfigurować w jednej dzierżawie.

## <a name="adding-discovery-benefits-sso-from-the-gallery"></a>Dodawanie dodawania do usługi SSO korzyści odnajdowania z galerii

Aby skonfigurować integrację ujeżdnego wpisu typu "Odnajdźce" z usługą Azure AD, należy dodać ujednolicinieszka systemu odnajdowania korzyści odnajdowania z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **wpisać wpisanie wpisaowego wpisau dodatkowego dostawcy informacji dodatkowej korzyści odnajdywania** w polu wyszukiwania.
1. Wybierz **pozycję Discovery Benefits SSO** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-discovery-benefits-sso"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla logowania jednokrotnego korzyści odnajdywania

Konfigurowanie i testowanie usługi Azure AD SSO przy użyciu funkcji SYC korzystających z funkcji odnajdowania za pomocą użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w usłudze Odnajdowanie korzyści— naliczania łącza.

Aby skonfigurować i przetestować przychylić do usługi Azure AD sytą przy za pomocą Ś/e. korzyści odnajdywania, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne odnajdywania korzyści —](#configure-discovery-benefits-sso-sso)** aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz odnajdowanie korzyści użytkownika testowego](#create-discovery-benefits-sso-test-user)** — mieć odpowiednik B.Simon w discovery korzyści samouszeństwo, który jest połączony z reprezentacji usługi Azure AD użytkownika.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **logowania jednokrotnego korzyści odnajdywania** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja** SAML aplikacja jest wstępnie skonfigurowana w trybie inicjowanym **przez usługę IDP,** a niezbędne adresy URL są już wstępnie wypełnione platformą Azure. Użytkownik musi zapisać konfigurację, klikając przycisk **Zapisz.** 

1. Aplikacja samoso elementów samouszeńców odnajdywania oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe Atrybuty użytkownika.

    ![image](common/edit-attribute.png)

    a. Kliknij ikonę **Edytuj,** aby otworzyć okno dialogowe **Unikatowy identyfikator użytkownika (identyfikator nazwy).**

    ![Konfiguracja usługi SSO korzyści odnajdywania](./media/discovery-benefits-sso-tutorial/attribute01.png)

    ![Konfiguracja usługi SSO korzyści odnajdywania](./media/discovery-benefits-sso-tutorial/attribute02.png)

    b. Kliknij ikonę **Edytuj,** aby otworzyć okno dialogowe **Zarządzanie transformacją.**

    d. W polach **tekstowych Transformacja** wpisz **touppercase()** pokazane dla tego wiersza.

    d. W polu tekstowym **Parametr 1** `<Name Identifier value>`wpisz parametr w stylu .

    e. Kliknij przycisk **Dodaj**.

    > [!NOTE]
    > Identyfikator Y SSO korzyści odnajdywania wymaga stałej wartości ciągu, która ma być przekazywana w polu **Unikatowy identyfikator użytkownika (identyfikator nazwy),** aby ta integracja działała. Usługa Azure AD obecnie nie obsługuje tej funkcji, więc w ramach obejścia można użyć **toupper** lub **ToLower** przekształcenia NameID, aby ustawić stałą wartość ciągu, jak pokazano powyżej na zrzucie ekranu.

    f. Mamy automatycznie wypełnione dodatkowe oświadczenia, które są wymagane dla`SSOInstance` `SSOID`konfiguracji SSO (i ). Użyj **ikony Edytuj,** aby zamapować wartości zgodnie z organizacją.

    ![Konfiguracja usługi SSO korzyści odnajdywania](./media/discovery-benefits-sso-tutorial/attribute03.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie ujednawania ujednych wyników odnajdowania** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do logowania jednokrotnego korzyści odnajdywania.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Discovery Benefits SSO**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-discovery-benefits-sso-sso"></a>Konfigurowanie sytów YSO osuw odnajdywania

Aby skonfigurować logowanie jednokrotne po stronie **logowania odnajdywania korzyści,** należy wysłać **pobrany certyfikat (Base64)** i odpowiednie skopiowane adresy URL z witryny Azure portal do [zespołu pomocy technicznej discovery benefits SSO](mailto:Jsimpson@DiscoveryBenefits.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-discovery-benefits-sso-test-user"></a>Tworzenie użytkownika testowego SSO korzyści odnajdywania

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Discovery Korzyści SYT. Praca z [zespołem pomocy technicznej SSO korzyści odnajdywania,](mailto:Jsimpson@DiscoveryBenefits.com) aby dodać użytkowników na platformie SSO korzyści odnajdywania. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka logowania jednośliowego korzyści odnajdywania w Panelu dostępu należy automatycznie zalogować się do logowania jednośmiękowego odnajdywania korzyści, dla którego skonfigurowano logowanie jednośmiękowe. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę 3.00 z usługą Azure AD w zakresie korzyści odnajdowania](https://aad.portal.azure.com/)


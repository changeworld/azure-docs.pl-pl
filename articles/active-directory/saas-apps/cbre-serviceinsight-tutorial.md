---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z usługą CBRE ServiceInsight | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją CBRE ServiceInsight.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31d7cedf-2b70-4fa4-9b05-80066d2e8bfd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 462f44206b4f92629079eb5ae24099eae17291f8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "71121395"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cbre-serviceinsight"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z usługą CBRE ServiceInsight

W tym samouczku dowiesz się, jak zintegrować usługę CBRE ServiceInsight z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi CBRE ServiceInsight z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do usługi CBREInsight.
* Włącz użytkownikom automatyczne logowanie do usługi CBRE ServiceInsight za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego CBRE ServiceInsight.CBRE ServiceInsight single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Aplikacja CBRE ServiceInsight obsługuje logowanie jednokrotne inicjowane przez **dostawcę usługi (SP)**
* Aplikacja CBRE ServiceInsight obsługuje aprowizowanie użytkowników typu **Just In Time**

## <a name="adding-cbre-serviceinsight-from-the-gallery"></a>Dodawanie aplikacji CBRE ServiceInsight z galerii

Aby skonfigurować integrację aplikacji CBRE ServiceInsight z usługą Azure AD, należy dodać aplikację CBRE ServiceInsight z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **CBRE ServiceInsight** w polu wyszukiwania.
1. Wybierz **pozycję CBRE ServiceInsight** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cbre-serviceinsight"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla usługi CBRE ServiceInsight

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą usługi CBRE ServiceInsight przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w witrynie CBRE ServiceInsight.

Aby skonfigurować i przetestować usługę Azure AD SSO przy za pomocą usługi CBRE ServiceInsight, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj CBRE ServiceInsight SSO](#configure-cbre-serviceinsight-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego CBRE ServiceInsight](#create-cbre-serviceinsight-test-user)** — aby mieć odpowiednik B.Simon w CBRE ServiceInsight, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **CBRE ServiceInsight** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    W polu **tekstowym "Podpisywanie adresu URL"** wpisz adres URL:`https://adfs4.mainstreamsasp.com/adfs/ls/`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej klienta aplikacji CBRE ServiceInsight](mailto:SISupport@cbre.com). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiuj, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do usługi CBREInsight.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **CBRE ServiceInsight**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-cbre-serviceinsight-sso"></a>Konfigurowanie usługi CBRE ServiceInsight SSO

Aby skonfigurować logowanie jednokrotne po stronie aplikacji **CBRE ServiceInsight**, musisz wysłać **adres URL metadanych federacyjnych aplikacji** do [zespołu pomocy technicznej aplikacji CBRE ServiceInsight](mailto:SISupport@cbre.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-cbre-serviceinsight-test-user"></a>Tworzenie użytkownika testowego aplikacji CBRE ServiceInsight

W tej sekcji w aplikacji CBRE ServiceInsight jest tworzony użytkownik o nazwie Britta Simon. Aplikacja CBRE ServiceInsight obsługuje aprowizowanie typu just in time, które jest domyślnie włączone. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w aplikacji CBRE ServiceInsight, zostanie utworzony podczas próby uzyskania dostępu do aplikacji CBRE ServiceInsight.

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka CBRE ServiceInsight w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji CBRE ServiceInsight, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę CBRE ServiceInsight z usługą Azure AD](https://aad.portal.azure.com/)
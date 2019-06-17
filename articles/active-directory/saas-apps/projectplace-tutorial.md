---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą narzędzia Projectplace | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Projectplace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 298059ca-b652-4577-916a-c31393d53d7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 179721075484c35c5ebbb3d936b83bc407b75a8d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67093524"
---
# <a name="tutorial-integrate-projectplace-with-azure-active-directory"></a>Samouczek: Integracja narzędzia Projectplace przy użyciu usługi Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować Projectplace w usłudze Azure Active Directory (Azure AD). W ramach Projectplace integracji z usługą Azure AD, możesz wykonywać następujące czynności:

* Kontrolowanie w usłudze Azure AD, kto ma dostęp do narzędzia Projectplace.
* Umożliwianie użytkownikom można automatycznie zalogowany Projectplace przy użyciu konta usługi Azure AD.
* Zarządzanie Twoimi kontami w jednej centralnej lokalizacji — witryny Azure portal.
* Użytkownicy mogą być udostępniane Projectplace automatycznie.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/).
* Projectplace logowania jednokrotnego (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku, skonfiguruj i przetestuj logowania jednokrotnego usługi Azure AD w środowisku testowym. Obsługuje Projectplace **dodatkiem SP oraz dostawców tożsamości** zainicjowane logowania jednokrotnego i obsługuje **Just In Time** Inicjowanie obsługi użytkowników.

## <a name="adding-projectplace-from-the-gallery"></a>Dodawanie Projectplace z galerii

Aby skonfigurować integrację z narzędziem projectplace firmy w usłudze Azure AD, należy dodać Projectplace z galerii z listą zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** usługi.
1. Przejdź do **aplikacje dla przedsiębiorstw** , a następnie wybierz **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz **nową aplikację**.
1. W **Dodaj z galerii** sekcji, wpisz **Projectplace** w polu wyszukiwania.
1. Wybierz **Projectplace** z wyników panelu, a następnie dodać aplikację. Odczekaj kilka sekund, podczas gdy aplikacja zostanie dodany do Twojej dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Konfiguracja i testowanie logowania jednokrotnego usługi Azure AD za pomocą narzędzia Projectplace, za pomocą użytkownika testu o nazwie **B. Simon**. Logowanie Jednokrotne do pracy musisz ustanowić relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w Projectplace.

Aby skonfigurować i przetestować logowania jednokrotnego usługi Azure AD za pomocą narzędzia Projectplace, wykonaj poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-sso)**  aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie narzędzia Projectplace](#configure-projectplace)**  do konfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  do testowania usługi Azure AD logowanie jednokrotne za pomocą B. Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  umożliwiające Simon B. korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego Projectplace](#create-projectplace-test-user)**  taki odpowiednik B. Simon w narzędzia Projectplace, połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-sso)**  Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

Wykonaj następujące kroki, aby włączyć logowania jednokrotnego usługi Azure AD w witrynie Azure portal.

1. W [witryny Azure portal](https://portal.azure.com/)na **Projectplace** strona integracji aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **logowanie jednokrotne**.
1. Na **wybierz jedną metodę logowania jednokrotnego** wybierz **SAML**.
1. Na **Ustaw się logowanie jednokrotne z SAML** kliknij ikonę edycji/pióra **podstawową konfigurację protokołu SAML** edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na **podstawową konfigurację protokołu SAML** sekcji, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb, aplikacja jest wstępnie skonfigurowana i wymaganych adresów URL już są wstępnie wypełniane przy użyciu platformy Azure . Użytkownik musi zapisać konfigurację, klikając **Zapisz** przycisku.

1. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://service.projectplace.com`

1. Na **Ustaw się logowanie jednokrotne z SAML** strony w **certyfikat podpisywania SAML** sekcji, kliknij przycisk Kopiuj **ikonę** do skopiowania **adres Url metadanych Federacji aplikacji** , zgodnie z wymaganiami, a następnie zapisz go w Notatniku.

   ![Link pobierania certyfikatu](common/copy-metadataurl.png)

1. Na **Konfigurowanie Projectplace** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-projectplace"></a>Konfigurowanie narzędzia Projectplace

Aby skonfigurować logowanie jednokrotne na **Projectplace** stronie, musisz wysłać skopiowany **adres Url metadanych Federacji aplikacji** w witrynie Azure portal do [zespołem pomocy technicznej Projectplace](https://success.planview.com/Projectplace/Support) . Ten zespół gwarantuje, że połączenia logowania jednokrotnego SAML jest prawidłowo po obu stronach.

>[!NOTE]
>Jednej konfiguracji logowania jednokrotnego musi być wykonywane przez [zespołem pomocy technicznej Projectplace](https://success.planview.com/Projectplace/Support). Otrzymasz powiadomienie, zaraz po zakończeniu konfiguracji. 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzymy użytkownika testowego w witrynie Azure portal, o nazwie B. Simon.

1. W okienku po lewej stronie w witrynie Azure portal wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. W **użytkownika** właściwości, wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B. Simon`.  
   1. W **nazwa_użytkownika** wprowadź username@companydomain.extension. Na przykład `BrittaSimon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można udostępnić Simon B. do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do narzędzia Projectplace.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz **Projectplace**.
1. Na stronie Przegląd usługi aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **użytkowników i grup**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz **Dodaj użytkownika**, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W **użytkowników i grup** okno dialogowe, wybierz opcję **B. Simon** z listy użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. Jeśli oczekujesz wszelkie wartości roli dla asercji SAML w **wybierz rolę** okno dialogowe, wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-projectplace-test-user"></a>Tworzenie użytkownika testowego Projectplace

>[!NOTE]
>W przypadku inicjowania obsługi administracyjnej w Projectplace włączone, można pominąć ten krok. Możesz poprosić [zespołem pomocy technicznej Projectplace](https://success.planview.com/Projectplace/Support) umożliwia inicjowanie obsługi administracyjnej elementu, gdy użytkownicy gotowe, zostanie utworzony w Projectplace podczas pierwszego logowania.

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do narzędzia Projectplace, należy dodać je do narzędzia Projectplace. Należy je dodać ręcznie.

**Aby utworzyć konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do Twojej **Projectplace** witryny firmy jako administrator.

2. Przejdź do **osób**, a następnie wybierz pozycję **członków**:
   
    ![Przejdź do osób, a następnie wybierz elementy członkowskie](./media/projectplace-tutorial/ic790228.png "osoby")

3. Wybierz **Dodawanie elementu członkowskiego**:
   
    ![Wybierz opcję Dodaj element członkowski](./media/projectplace-tutorial/ic790232.png "dodawać członków")

4. W **Dodawanie elementu członkowskiego** sekcji, wykonaj następujące kroki.
   
    ![Dodawanie elementu członkowskiego sekcji](./media/projectplace-tutorial/ic790233.png "nowych elementów członkowskich")
   
    1. W **nowych elementów członkowskich** wprowadź adres e-mail Azure prawidłowe konto usługi AD, który chcesz dodać.
   
    1. Wybierz pozycję **Wyślij**.

   Wiadomość e-mail zawierającą łącze w celu potwierdzenia konta, zanim stanie się aktywny są wysyłane do właściciela konta usługi Azure AD.

>[!NOTE]
>Umożliwia także inne narzędzie do tworzenia konta użytkownika lub interfejsów API dostarczonych przez narzędzia Projectplace, aby dodać konta użytkowników usługi Azure AD.


### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka Projectplace w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze narzędzia Projectplace, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
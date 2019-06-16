---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą MyVR | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i MyVR.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b1b4bbc7-cdac-447c-83a3-bcb869159255
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10473c820cd0dbb7c7db3f33de203ca4d636414b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67096486"
---
# <a name="tutorial-integrate-myvr-with-azure-active-directory"></a>Samouczek: Integracja MyVR za pomocą usługi Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować MyVR w usłudze Azure Active Directory (Azure AD). W ramach MyVR integracji z usługą Azure AD, możesz wykonywać następujące czynności:

* Kontrolowanie w usłudze Azure AD, kto ma dostęp do MyVR.
* Umożliwianie użytkownikom można automatycznie zalogowany do MyVR za pomocą kont usługi Azure AD.
* Zarządzanie Twoimi kontami w jednej centralnej lokalizacji — witryny Azure portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz pobrać miesięcznej bezpłatnej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).
* MyVR logowania jednokrotnego (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku, skonfiguruj i przetestuj logowania jednokrotnego usługi Azure AD w środowisku testowym. 

* Obsługuje MyVR **dodatkiem SP oraz dostawców tożsamości** jednokrotne logowanie inicjowane przez.
* Obsługuje MyVR **Just In Time** Inicjowanie obsługi użytkowników.

## <a name="adding-myvr-from-the-gallery"></a>Dodawanie MyVR z galerii

Aby skonfigurować integrację MyVR w usłudze Azure AD, należy dodać MyVR z galerii z listą zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** usługi.
1. Przejdź do **aplikacje dla przedsiębiorstw** , a następnie wybierz **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz **nową aplikację**.
1. W **Dodaj z galerii** sekcji, wpisz **MyVR** w polu wyszukiwania.
1. Wybierz **MyVR** z wyników panelu, a następnie dodać aplikację. Odczekaj kilka sekund, podczas gdy aplikacja zostanie dodany do Twojej dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Konfiguracja i testowanie logowania jednokrotnego usługi Azure AD za pomocą MyVR za pomocą użytkownika testu o nazwie **B. Simon**. Logowanie Jednokrotne do pracy musisz ustanowić relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w MyVR.

Aby skonfigurować i przetestować logowania jednokrotnego usługi Azure AD za pomocą MyVR, wykonaj poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-sso)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Konfigurowanie logowania jednokrotnego MyVR](#configure-myvr-sso)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą B. Simon.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Simon B. korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Tworzenie użytkownika testowego MyVR](#create-myvr-test-user)**  — aby odpowiednikiem B. Simon w MyVR połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Testowanie logowania jednokrotnego](#test-sso)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

Wykonaj następujące kroki, aby włączyć logowania jednokrotnego usługi Azure AD w witrynie Azure portal.

1. W [witryny Azure portal](https://portal.azure.com/)na **MyVR** strona integracji aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **logowanie jednokrotne**.
1. Na **wybierz jedną metodę logowania jednokrotnego** wybierz **SAML**.
1. Na **Ustaw się logowanie jednokrotne z SAML** kliknij ikonę edycji/pióra **podstawową konfigurację protokołu SAML** edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na **podstawową konfigurację protokołu SAML** sekcji, jeśli użytkownik chce skonfigurować aplikację w **tożsamości** zainicjowano tryb, użytkownik nie będzie musiał wykonać każdy krok, ponieważ aplikacja jest już wstępnie zintegrowane z platformą Azure.

1. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

   W polu tekstowym **Adres URL logowania** wpisz następujący adres URL: `https://ess.virtualroster.net/ess/login.aspx`

1. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** edytuj oświadczenia, korzystając z **ikony edycji**, lub dodaj je za pomocą opcji **Dodaj nowe oświadczenie**, aby skonfigurować atrybut tokenu języka SAML, jak pokazano na ilustracji powyżej, a następnie wykonaj następujące czynności:

   | Name (Nazwa) | Atrybut źródłowy|
   | ---------------| --------------- |
   | Imię | user.givenname |
   | surname | user.surname |
   | emailaddress | user.mail |
   | EmployeeID | user.employeeid |

   1. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.
   1. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.
   1. Pozostaw pole **Przestrzeń nazw** puste.
   1. Dla opcji Źródło wybierz wartość **Atrybut**.
   1. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.
   1. Kliknij przycisk **OK**.
   1. Kliknij pozycję **Zapisz**.

1. Na **Ustaw się logowanie jednokrotne z SAML** strony w **certyfikat podpisywania SAML** sekcji, Znajdź **certyfikat (Base64)** i wybierz **Pobierz** do pobrania certyfikatu i zapisz go na komputerze.

   ![Link pobierania certyfikatu](common/certificatebase64.png)

1. Na **Konfigurowanie MyVR** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-myvr-sso"></a>Konfigurowanie logowania jednokrotnego MyVR

Aby skonfigurować logowanie jednokrotne na **MyVR** stronie, musisz wysłać pobrany **certyfikat (Base64)** i odpowiednie skopiowany adresy URL z portalu Azure, aby [MyVR zespołem pomocy technicznej](mailto:arno.vandenberg@Kronos.com). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzymy użytkownika testowego w witrynie Azure portal, o nazwie B. Simon.

1. W okienku po lewej stronie w witrynie Azure portal wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. W **użytkownika** właściwości, wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B. Simon`.  
   1. W **nazwa_użytkownika** wprowadź username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można udostępnić Simon B. do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do MyVR.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz **MyVR**.
1. Na stronie Przegląd usługi aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **użytkowników i grup**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz **Dodaj użytkownika**, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

   ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W **użytkowników i grup** okno dialogowe, wybierz opcję **B. Simon** z listy użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. Jeśli oczekujesz wszelkie wartości roli dla asercji SAML w **wybierz rolę** okno dialogowe, wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-myvr-test-user"></a>Tworzenie użytkownika testowego MyVR

W tej sekcji użytkownika o nazwie B. Simon jest tworzony w MyVR. MyVR obsługuje aprowizacji użytkowników w czasie, który jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w MyVR, nowy katalog jest tworzony po uwierzytelnieniu.

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka MyVR w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze MyVR, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

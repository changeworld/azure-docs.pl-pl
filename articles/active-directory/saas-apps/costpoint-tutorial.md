---
title: 'Samouczek: Azure Active Directory integrację z usługą Costpoint | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Costpoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9ecc5f58-4462-4ade-ab73-0a4f61027504
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c1a8b916feb2ad67623434f2b63468be72bf1aa
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879597"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>Samouczek: Integruj Costpoint z Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować usługę Costpoint z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Costpoint z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Costpoint.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Costpoint przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) usługi Costpoint.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. Usługa Costpoint obsługuje funkcję SSO zainicjowaną przez usługę **SP i dostawcy tożsamości** .

## <a name="adding-costpoint-from-the-gallery"></a>Dodawanie Costpoint z galerii

Aby skonfigurować integrację programu Costpoint z usługą Azure AD, musisz dodać Costpoint z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Costpoint** w polu wyszukiwania.
1. Wybierz pozycję **Costpoint** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Costpoint przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Costpoint.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Costpoint, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj Costpoint](#configure-costpoint)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Costpoint](#create-costpoint-test-user)** , aby miał odpowiednik B. Simon w Costpoint, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Przetestuj logowanie](#test-sso)** jednokrotne, aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Costpoint** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie**jednokrotne.
1. Na stronie **Wybierz metodę logowania** jednokrotnego wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML**, jeśli masz **plik metadanych dostawcy usługi**, wykonaj następujące kroki:

    > [!NOTE]
    > Plik metadanych dostawcy usług zostanie pobrany z sekcji **generowanie metadanych Costpoint** , która została omówiona w dalszej części tego samouczka.
 
    1. Kliknij przycisk **przekazywania pliku metadanych**.
    
    1. Kliknij pozycję **logo folderu** wybierz plik metadanych, a następnie kliknij przycisk **przekazywanie**.
    
    1. Po pomyślnym przekazaniu pliku metadanych wartości **identyfikatorów** i **adresów URL odpowiedzi** są automatycznie wypełniane w polu textCostpoint sekcji

        > [!Note]
        > Jeśli wartości **Identyfikator** i **Adres URL odpowiedzi** nie zostaną automatycznie wypełnione, wpisz te wartości ręcznie zgodnie z wymaganiami. Sprawdź, czy **Identyfikator (identyfikator jednostki)** i **adres URL odpowiedzi (adres URL usługi potwierdzenia odbiorcy)** są poprawnie ustawione i czy **adres URL ACS** jest prawidłowym adresem URL Costpoint kończącym się **/LoginServlet.CPS**.

    1. Kliknij pozycję **Ustaw dodatkowe adresy URL**.

    1. W polu tekstowym **stan przekaźnika** wpisz wartość przy użyciu następującego wzorca:`system=[your system], (for example, **system=DELTEKCP**)`

1. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę usług**, wykonaj następujący krok:
    
    W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://costpointteea.deltek.com/cpweb/cploginform.htm`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora, adresu URL odpowiedzi i stanu przekazywania. Skontaktuj się z [zespołem obsługi klienta Costpoint](https://www.deltek.com/about/contact-us) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** kliknij ikonę kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go w Notatniku.

   ![Link pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="generate-costpoint-metadata"></a>Generuj metadane Costpoint

Konfiguracja logowania jednokrotnego w usłudze Costpoint SAML została omówiona w przewodniku **DeltekCostpoint711Security. PDF** . W tym celu należy zapoznać się z tematem Konfiguracja logowania jednokrotnego **SAML — > skonfigurować Logowanie jednokrotne SAML między Costpoint i usługą Azure AD** . Postępuj zgodnie z instrukcjami i Generuj plik **XML metadanych Federacji COSTPOINT Sp** . Użyj tego elementu w **podstawowej konfiguracji SAML** w Azure Portal.

![Narzędzie konfiguracji Costpoint](./media/costpoint-tutorial/config02.png)

> [!NOTE]
> Przewodnik **DeltekCostpoint711Security. PDF** zostanie pobrany z [zespołu obsługi klienta Costpoint](https://www.deltek.com/about/contact-us). Jeśli nie masz tego pliku, skontaktuj się z nim, aby uzyskać ten plik.

### <a name="configure-costpoint"></a>Konfigurowanie Costpoint

Wróć do **Narzędzia konfiguracji Costpoint** i wklej **adres URL metadanych federacji aplikacji** do pola tekstowego **XML metadanych Federacji dostawcy tożsamości** i Kontynuuj instrukcje z przewodnika **DeltekCostpoint711Security. PDF** , aby zakończyć Costpoint konfigurację SAML. 

![Narzędzie konfiguracji Costpoint](./media/costpoint-tutorial/config01.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie usłudze B. Simon dostępu do usługi Costpoint.

1. W Azure Portal wybierz pozycję **aplikacje** > dla przedsiębiorstw**wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Costpoint**.
1. W sekcji **Zarządzanie** na stronie Przegląd aplikacji wybierz pozycję **Użytkownicy i grupy**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodaj przypisanie** kliknij przycisk **Przypisz** .

### <a name="create-costpoint-test-user"></a>Utwórz użytkownika testowego Costpoint

W tej sekcji utworzysz użytkownika w Costpoint. Załóżmy, że **Identyfikator użytkownika** to **b. Simon** i nazwa **B. Simon**. Współpracuj z [zespołem obsługi klienta Costpoint](https://www.deltek.com/about/contact-us) , aby dodać użytkownika na platformie Costpoint. Użytkownik musi zostać utworzony i aktywowany przed użyciem rejestracji jednokrotnej.
 
Po utworzeniu wybór **metody uwierzytelniania** użytkownika musi być **Active Directory**, należy zaznaczyć pole wyboru protokołu **SAML logowania** jednokrotnego, a nazwa użytkownika Azure Active Directory musi być **Active Directory lub identyfikatorem certyfikatu** . (jak pokazano poniżej).

![Costpoint użytkownika](./media/costpoint-tutorial/user01.png)

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

Po wybraniu kafelka Costpoint w panelu dostępu należy automatycznie zalogować się do Costpoint, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
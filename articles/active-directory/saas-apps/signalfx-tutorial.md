---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z SignalFx | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a SignalFx.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6d5ab4b0-29bc-4b20-8536-d64db7530f32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/24/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 149718dcd325ef6bd6a6754ba100ffdc34be0a07
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79136418"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-signalfx"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z signalfx

W tym samouczku dowiesz się, jak zintegrować SignalFx z usługą Azure Active Directory (Azure AD). Po zintegrowaniu SignalFx z usługą Azure AD można:

* Kontrola z usługi Azure AD, który ma dostęp do SignalFx;
* Włącz użytkownikom automatyczne logowanie do SignalFx za pomocą ich kont usługi Azure AD; I
* Zarządzaj kontami w jednej lokalizacji (witryny Azure Portal).

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Zanim zaczniesz, będziesz potrzebować:

* Subskrypcji usługi Azure AD
    * Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto tutaj](https://azure.microsoft.com/free/).
* Subskrypcja z obsługą logowania jednokrotnego (SSO) signalFx

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz samouszeńcę usługi Azure AD w środowisku testowym.

* SignalFx obsługuje zainicjowane przez protokół SSO protokołu **IDP**
* SignalFx obsługuje **inicjowanie** obsługi administracyjnej użytkowników just in time
* Po skonfigurowaniu SignalFx można wymusić kontrolę sesji, która chroni eksfiltracji i infiltracji poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="step-1-add-the-signalfx-application-in-azure"></a>Krok 1: Dodaj aplikację SignalFx na platformie Azure

Skorzystaj z tych instrukcji, aby dodać aplikację SignalFx do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
1. W oknie nawigacji po lewej stronie wybierz pozycję **Azure Active Directory**.
1. Wybierz **pozycję Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** w polu wyszukiwania wprowadź i wybierz pozycję **SignalFx**.
     * Może być konieczne odczekanie kilku minut na dodaniu aplikacji do dzierżawy.
1. Pozostaw witrynę Azure portal otwartą, a następnie otwórz nową kartę sieci Web.    

## <a name="step-2-begin-signalfx-sso-configuration"></a>Krok 2: Rozpocznij konfigurację SSO SignalFx

Użyj tych instrukcji, aby rozpocząć proces konfiguracji signalFx SSO.

1. W nowo otwartej karcie dostęp i logowanie do interfejsu użytkownika SignalFx. 
1. W górnym menu kliknij pozycję **Integracje**. 
1. W polu wyszukiwania wprowadź i wybierz pozycję **Azure Active Directory**.
1. Kliknij **pozycję Utwórz nową integrację**.
1. W **polu Nazwa**wprowadź łatwo rozpoznawalną nazwę, którą użytkownicy zrozumieją.
1. Oznacz **pokaż na stronie logowania**.
    * Ta funkcja wyświetli dostosowany przycisk na stronie logowania, który użytkownicy mogą kliknąć. 
    * Informacje wprowadzone w **nazwa** pojawią się na przycisku. W rezultacie wprowadź **nazwę,** którą użytkownicy rozpoznają. 
    * Ta opcja będzie działać tylko wtedy, gdy używasz niestandardowej poddomeny dla aplikacji SignalFx, takiej jak **yourcompanyname.signalfx.com**. Aby uzyskać niestandardową poddomenę, skontaktuj się z pomocą techniczną SignalFx. 
1. Skopiuj **identyfikator integracji**. Te informacje będą potrzebne w późniejszym kroku. 
1. Pozostaw interfejs signalfx otwarty. 

## <a name="step-3-configure-azure-ad-sso"></a>Krok 3: Konfigurowanie usługi Azure AD SSO

Użyj tych instrukcji, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. Wróć do [portalu Azure](https://portal.azure.com/)i na stronie integracji aplikacji **SignalFx** znajdź sekcję **Zarządzaj,** a następnie wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę pióra (edytuj) dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** wypełnij następujące pola: 

    a. W **identyfikatorze**wprowadź następujący `https://api.<realm>.signalfx.com/v1/saml/metadata` adres `<realm>` URL i zastąp sferą SignalFx. 

    b. W **adresie URL**odpowiedzi `https://api.<realm>.signalfx.com/v1/saml/acs/<integration ID>` wprowadź `<realm>` następujący adres URL i zastąp sferą SignalFx, a także `<integration ID>` **identyfikatorem integracji** skopiowanym wcześniej z interfejsu SignalFx.

1. SignalFx aplikacja oczekuje potwierdzeń SAML w określonym formacie, który wymaga, aby dodać niestandardowe mapowania atrybutów do konfiguracji atrybutów tokenu SAML. 
    
1. Przejrzyj i sprawdź, czy następujące oświadczenia są mapowane na atrybuty źródłowe, które są wypełniane w usłudze Active Directory. 

    | Nazwa |  Atrybut źródłowy|
    | ------------------- | -------------------- |
    | User.FirstName  | user.givenname |
    | User.email  | user.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | user.surname    |

    > [!NOTE]
    > Ten proces wymaga skonfigurowania usługi Active Directory z co najmniej jedną zweryfikowaną domeną niestandardową, a także dostępu do kont e-mail w tej domenie. Jeśli nie masz pewności lub potrzebujesz pomocy w tej konfiguracji, skontaktuj się z pomocą techniczną SignalFx.  

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź **pozycję Certyfikat (Base64),** a następnie wybierz pozycję **Pobierz**. Pobierz certyfikat i zapisz go na komputerze. Następnie skopiuj wartość **adresu URL metadanych federacji aplikacji;** te informacje będą potrzebne w późniejszym kroku w interfejsie signalfx. 

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie SignalFx** skopiuj wartość **identyfikatora usługi Azure AD.** Te informacje będą potrzebne w późniejszym kroku w interfejsie signalfx. 

## <a name="step-4-create-an-azure-ad-test-user"></a>Krok 4: Tworzenie użytkownika testowego usługi Azure AD

Skorzystaj z tych instrukcji, aby utworzyć użytkownika testowego w witrynie Azure o nazwie **B.Simon**.

1. W witrynie Azure portal w oknie nawigacji po lewej stronie wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. U góry strony wybierz pozycję **Nowy użytkownik**.
1. We właściwościach **Użytkownika:**
   1. W **pliku Nazwa użytkownika**wprowadź `username@companydomain.extension`, takie jak `b.simon@contoso.com`.
   1. W **nazwie** `B.Simon`wprowadź .
   1. Oznacz **polecenie Pokaż hasło**, a następnie skopiuj wyświetlaną wartość w polu **Hasło**. Te informacje będą potrzebne w późniejszym kroku, aby przetestować tę integrację. 
   1. Kliknij przycisk **Utwórz**.

## <a name="step-5-assign-the-azure-ad-test-user"></a>Krok 5: Przypisywanie użytkownika testowego usługi Azure AD

Skorzystaj z tych instrukcji, aby umożliwić użytkownikowi testowe używanie logowania jednokrotnego platformy Azure dla SignalFx.

1. W portalu Azure wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **signalfx**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzanie,** a następnie wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przydziału** wybierz pozycję **Użytkownicy i grupy**.

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** z listy **Użytkownicy** wybierz pozycję **B.Simon**, a następnie u dołu strony kliknij pozycję **Wybierz**.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę,** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu strony.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz**.

## <a name="step-6-complete-the-signalfx-sso-configuration"></a>Krok 6: Ukończ konfigurację SSO SignalFx 

1. Otwórz poprzednią kartę i wróć do interfejsu użytkownika SignalFx, aby wyświetlić bieżącą stronę integracji usługi Azure Active Directory. 
1. Obok **pozycji Certyfikat (Base64)** kliknij pozycję **Przekaż plik**, a następnie znajdź plik **certyfikatu zakodowany w usłudze Base64,** który został wcześniej pobrany z witryny Azure Portal.
1. Obok **identyfikatora usługi Azure AD wklej**wartość **identyfikatora usługi Azure AD,** która została skopiowana wcześniej z witryny Azure portal. 
1. Obok **adresu URL metadanych federacji**wklej wartość **adresu URL metadanych federacji aplikacji** skopiowaną wcześniej z witryny Azure portal. 
1. Kliknij przycisk **Zapisz**.

## <a name="step-7-test-sso"></a>Krok 7: Test SSO

Zapoznaj się z poniższymi informacjami dotyczącymi sposobu testowania logowania sygnowania, a także oczekiwań dotyczących logowania do SignalFx po raz pierwszy. 

### <a name="test-logins"></a>Testowanie loginów

* Aby przetestować login, należy użyć okna prywatnego / incognito lub można wylogować się z witryny Azure portal. Jeśli nie, pliki cookie dla użytkownika, który skonfigurował aplikację, będą zakłócać i zapobiegać pomyślnemu logowaniu z użytkownikiem testowym.

* Gdy nowy użytkownik testowy zaloguje się po raz pierwszy, platforma Azure wymusi zmianę hasła. W takim przypadku proces logowania do logowania logowania nie zostanie zakończony; użytkownik testowy zostanie przekierowany do witryny Azure portal. Aby rozwiązać problem, użytkownik testowy powinien zmienić swoje hasło i przejść do strony logowania SignalFx lub panelu dostępu i spróbuj ponownie.
    * Po kliknięciu kafelka SignalFx w Panelu dostępu należy automatycznie zalogować się do SignalFx. 
        * Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

* Dostęp do aplikacji SignalFx można uzyskać z Panelu dostępu lub za pośrednictwem niestandardowej strony logowania przypisanej do organizacji. Użytkownik testowy należy przetestować integracji, począwszy od jednej z tych lokalizacji.
    * Użytkownik testowy może używać poświadczeń **b.simon@contoso.com**utworzonych wcześniej w tym procesie dla programu .

### <a name="first-time-logins"></a>Logowanie po raz pierwszy

* Gdy użytkownik loguje się do SignalFx z logowania SSO SAML po raz pierwszy, użytkownik otrzyma wiadomość e-mail SignalFx z linkiem. Użytkownik musi kliknąć łącze do celów uwierzytelniania. To sprawdzanie poprawności wiadomości e-mail będzie miało miejsce tylko dla użytkowników po raz pierwszy. 

* SignalFx obsługuje tworzenie użytkownika **Just In Time,** co oznacza, że jeśli użytkownik nie istnieje w SignalFx, konto użytkownika zostanie utworzone przy pierwszej próbie logowania.

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Wypróbuj SignalFx z usługą Azure AD](https://aad.portal.azure.com/)
---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługami Amazon Web Services (AWS) | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i usługami Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f6640708905abc266b07b7b66f5da09aeb890f01
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68823866"
---
# <a name="tutorial-integrate-amazon-web-services-aws-with-azure-active-directory"></a>Samouczek: Integruj Amazon Web Services (AWS) z Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować usługę Amazon Web Services (AWS) z usługą Azure Active Directory (Azure AD). Podczas integrowania Amazon Web Services (AWS) z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Amazon Web Services (AWS).
* Zezwól użytkownikom na automatyczne logowanie do Amazon Web Services (AWS) przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

![Amazon Web Services (AWS)](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Dla wielu wystąpień można skonfigurować wiele identyfikatorów, tak jak pokazano poniżej.

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Korzystając z tych wartości, usługa Azure AD usunie wartość **#** i wyśle poprawną wartość `https://signin.aws.amazon.com/saml` jako adres URL odbiorcy w tokenie SAML.

**Zalecamy użycie tego podejścia z następujących względów:**

a. Każda aplikacja zapewni unikatowy certyfikat x509. Każde wystąpienie wystąpienia aplikacji AWS może następnie mieć inną datę wygaśnięcia certyfikatu, która może być zarządzana na poszczególnych kontach AWS. Ogólnie Przerzucanie certyfikatów będzie łatwiejsze w tym przypadku.

b. Istnieje możliwość włączenia aprowizowania użytkowników za pomocą aplikacji usług AWS w usłudze Azure AD. Nasza usługa pobierze wtedy wszystkie role z konta usług AWS. Nie trzeba ręcznie dodawać ani aktualizować ról usług AWS dla aplikacji.

c. Właściciela aplikacji można przypisać dla konkretnej aplikacji. Może on wtedy zarządzać tą aplikacją bezpośrednio w usłudze Azure AD.

> [!Note]
> Upewnij się, że używasz tylko aplikacji z galerii.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Amazon Web Services (AWS) subskrypcja z włączonym logowaniem jednokrotnym (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. Amazon Web Services (AWS) obsługuje zainicjowanie logowania jednokrotnego **z użyciem SP i dostawcy tożsamości** .

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Dodawanie usług Amazon Web Services (AWS) z galerii

Aby skonfigurować integrację usług Amazon Web Services (AWS) z usługą Azure AD, należy dodać usługi Amazon Web Services (AWS) z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Amazon Web Services (AWS)** w polu wyszukiwania.
1. Wybierz pozycję **Amazon Web Services (AWS)** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Amazon Web Services (AWS) przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Amazon Web Services (AWS).

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Amazon Web Services (AWS), wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj Amazon Web Services (AWS)](#configure-amazon-web-services-aws)** , aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Amazon Web Services (AWS)](#create-amazon-web-services-aws-test-user)** , aby miał odpowiednik B. Simon w Amazon Web Services (AWS), który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Przetestuj logowanie](#test-sso)** jednokrotne, aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. Na [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Amazon Web Services (AWS)** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie**jednokrotne.
1. Na stronie **Wybierz metodę logowania** jednokrotnego wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja języka SAML** aplikacja została wstępnie skonfigurowana i wymagane adresy URL są już wstępnie wypełnione na platformie Azure. Użytkownik musi zapisać konfigurację, klikając przycisk **Zapisz** .

5. W przypadku konfigurowania wielu wystąpień podaj wartość identyfikatora. Dla drugiego i kolejnych wystąpień podaj wartość identyfikatora w następującym formacie. Użyj znaku **#** , aby określić unikatową główną nazwę usługi.

    `https://signin.aws.amazon.com/saml#2`

6. Aplikacja Amazon Web Services (AWS) oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę  **Edytuj** , aby otworzyć okno dialogowe Atrybuty użytkownika.

    ![image](common/edit-attribute.png)

7. Oprócz powyższych, aplikacja Amazon Web Services (AWS) oczekuje kilku atrybutów do przekazania z powrotem do odpowiedzi SAML. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** wykonaj następujące czynności, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli:

    | Name (Nazwa)  | Atrybut źródłowy  | Przestrzeń nazw |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Role            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | „podaj wartość z zakresu od 900 sekund (15 minut) do 43200 sekund (12 godzin)” |  https://aws.amazon.com/SAML/Attributes |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    c. W polu tekstowym **Przestrzeń nazw** wpisz wartość przestrzeni nazw pokazaną dla tego wiersza.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij polecenie **Zapisz**.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

   ![Link pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **konfigurowanie Amazon Web Services (AWS)** skopiuj odpowiednie adresy URL na podstawie wymagań.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-amazon-web-services-aws"></a>Konfigurowanie Amazon Web Services (AWS)

1. W innym oknie przeglądarki zaloguj się do usług Amazon Web Services (AWS) jako administrator.

2. Kliknij pozycję **AWS Home** (Strona główna usług AWS).

    ![Konfigurowanie logowania jednokrotnego — strona główna][11]

3. Kliknij pozycję **Identity and Access Management** (Zarządzanie tożsamościami i dostępem).

    ![Konfigurowanie logowania jednokrotnego — tożsamość][12]

4. Kliknij pozycję **Identity Providers** (Dostawcy tożsamości), a następnie pozycję **Create Provider** (Utwórz dostawcę).

    ![Konfigurowanie logowania jednokrotnego — dostawca][13]

5. Na stronie okna dialogowego **Configure Provider** (Konfigurowanie dostawcy) wykonaj następujące kroki:

    ![Okno dialogowe Configure Single Sign-On (Konfigurowanie logowania jednokrotnego)][14]

    a. Dla pozycji **Provider Type** (Typ dostawcy) wybierz opcję **SAML**.

    b. W polu tekstowym **Provider Name** (Nazwa dostawcy) wpisz nazwę dostawcy (na przykład: *WAAD*).

    c. Aby przekazać **plik metadanych** pobrany z witryny Azure Portal, kliknij pozycję **Choose File** (Wybierz plik).

    d. Kliknij pozycję **Next Step** (Następny krok).

6. Na stronie okna dialogowego **Verify Provider Information** (Weryfikowanie informacji o dostawcy) kliknij pozycję **Create** (Utwórz).

    ![Konfigurowanie logowania jednokrotnego — weryfikowanie][15]

7. Kliknij pozycję **Roles** (Role), a następnie **Create role** (Utwórz rolę).

    ![Konfigurowanie logowania jednokrotnego — role][16]

8. Na stronie **Create role** (Tworzenie roli) wykonaj następujące kroki:  

    ![Konfigurowanie logowania jednokrotnego — zaufanie][19]

    a. Wybierz pozycję **SAML 2.0 federation** (Federacja SAML 2.0) w obszarze **Select type of trusted entity** (Wybierz typ zaufanej jednostki).

    b. W **sekcji Choose a SAML 2.0 Provider (Wybierz dostawcę SAML 2.0)** określ utworzonego wcześniej **dostawcę SAML** (na przykład: *WAAD*)

    c. Wybierz pozycję **Allow programmatic and AWS Management Console access** (Zezwalaj na dostęp programowy i za pomocą konsoli AWS Management Console).
  
    d. Kliknij pozycję **Next: Permissions** (Dalej: uprawnienia).

9. W oknie dialogowym **Attach Permissions Policies** (Dołączanie zasad uprawnień) dołącz zasady odpowiednie dla Twojej organizacji. Kliknij pozycję **Next: Review** (Dalej: przegląd).  

    ![Konfigurowanie logowania jednokrotnego — zasady][33]

10. W oknie dialogowym **Review** (Przegląd) wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego — przegląd][34]

    a. W polu tekstowym **Role name** (Nazwa roli) podaj nazwę roli.

    b. W polu tekstowym **Role description** (Opis roli) podaj opis.

    c. Kliknij pozycję **Create Role** (Utwórz rolę).

    d. Utwórz potrzebną liczbę ról i zamapuj je na dostawcę tożsamości.

11. Użyj poświadczeń konta usług AWS do pobrania ról z konta usług AWS na potrzeby aprowizacji użytkowników usługi Azure AD. W tym celu otwórz stronę główną konsoli usług AWS.

12. Kliknij pozycję **Services** -> **Security, Identity& Compliance** -> **IAM** (Usługi -> Zabezpieczenia, tożsamość i zgodność -> Zarządzanie dostępem i tożsamościami).

    ![pobieranie ról z konta usług AWS](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. Wybierz kartę **Policies (Zasady)** w sekcji IAM (Zarządzanie dostępem i tożsamościami).

    ![pobieranie ról z konta usług AWS](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Utwórz nowe zasady, klikając pozycję **Create policy** (Utwórz zasady) w celu pobrania ról z konta usług AWS na potrzeby aprowizacji użytkowników usługi Azure AD.

    ![Tworzenie nowych zasad](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Utwórz własne zasady, aby pobrać wszystkie role z kont usług AWS, wykonując następujące kroki:

    ![Tworzenie nowych zasad](./media/amazon-web-service-tutorial/policy1.png)

    a. W sekcji **„Create policy”** („Utwórz zasady”) kliknij kartę **„JSON”** .

    b. Dodaj poniższy kod JSON do dokumentu zasad.

    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                "iam:ListRoles"
                ],
                "Resource": "*"
            }
        ]
    }
    ```

    c. Kliknij **przycisk Review Policy (Przejrzyj zasady)** , aby zweryfikować zasady.

    ![Definiowanie nowych zasad](./media/amazon-web-service-tutorial/policy5.png)

16. Zdefiniuj **nowe zasady**, wykonując następujące kroki:

    ![Definiowanie nowych zasad](./media/amazon-web-service-tutorial/policy2.png)

    a. W polu **Policy Name** (Nazwa zasad) podaj wartość **AzureAD_SSOUserRole_Policy**.

    b. W polu **Description** (Opis) możesz wpisać opis: **Te zasady umożliwiają pobranie ról z kont usług AWS**.

    c. Kliknij przycisk **„Create Policy”** („Utwórz zasady”).

17. Utwórz nowe konto użytkownika w usłudze zarządzania dostępem i tożsamościami usług AWS, wykonując następujące kroki:

    a. Kliknij element nawigacji **Users** (Użytkownicy) w konsoli usługi zarządzania dostępem i tożsamościami usług AWS.

    ![Definiowanie nowych zasad](./media/amazon-web-service-tutorial/policy3.png)

    b. Kliknij przycisk **Add user** (Dodaj użytkownika), aby utworzyć nowego użytkownika.

    ![Dodaj użytkownika](./media/amazon-web-service-tutorial/policy4.png)

    c. W sekcji **Add user** (Dodaj użytkownika) wykonaj następujące kroki:

    ![Dodaj użytkownika](./media/amazon-web-service-tutorial/adduser1.png)

    * Podaj nazwę użytkownika: **AzureADRoleManager**.

    * W polu Access type (Typ dostępu) wybierz opcję **Programmatic access** (Dostęp programowy). Dzięki temu użytkownik może wywołać interfejsy API i pobrać role z konta usług AWS.

    * Kliknij przycisk **Next Permissions** (Dalej: uprawnienia) w prawym dolnym rogu.

18. Teraz utwórz nowe zasady dla tego użytkownika, wykonując następujące kroki:

    ![Dodaj użytkownika](./media/amazon-web-service-tutorial/adduser2.png)

    a. Kliknij przycisk **Attach existing policies directly** (Dołącz istniejące zasady bezpośrednio).

    b. Wyszukaj nowo utworzone zasady w sekcji **AzureAD_SSOUserRole_Policy** filtru.

    c. Wybierz pozycję **policy** (zasady), a następnie kliknij przycisk **Next: Review** (Dalej: przegląd).

19. Przejrzyj zasady dołączonego użytkownika, wykonując następujące kroki:

    ![Dodaj użytkownika](./media/amazon-web-service-tutorial/adduser3.png)

    a. Przejrzyj nazwę użytkownika, typ dostępu i zasady zamapowane do użytkownika.

    b. Kliknij przycisk **Create user** (Utwórz użytkownika) w prawym dolnym rogu, aby utworzyć użytkownika.

20. Pobierz poświadczenia użytkownika, wykonując następujące kroki:

    ![Dodaj użytkownika](./media/amazon-web-service-tutorial/adduser4.png)

    a. Skopiuj **identyfikator klucza dostępu** i **klucz dostępu wpisu tajnego**.

    b. Podaj te poświadczenia w sekcji aprowizacji użytkowników usługi Azure AD, aby pobrać role z konsoli usług AWS.

    c. Kliknij przycisk **Close** (Zamknij) znajdujący się u dołu.

21. Przejdź do sekcji **User Provisioning** (Aprowizacja użytkowników) dla aplikacji usług Amazon Web Services w portalu zarządzania usługi Azure AD.

    ![Dodaj użytkownika](./media/amazon-web-service-tutorial/provisioning.png)

22. Podaj **klucz dostępu** i **wpis tajny** odpowiednio w polach **Client Secret** (Wpis tajny klienta) i **Secret Token** (Token wpisu tajnego).

    ![Dodaj użytkownika](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Podaj klucz dostępu użytkownika usług AWS w polu **clientsecret**.

    b. Podaj wpis tajny użytkownika usług AWS w polu **Secret Token** (Token wpisu tajnego).

    c. Kliknij przycisk **Test Connection** (Testuj połączenie). Testowanie połączenia powinno zakończyć się pomyślnie.

    d. Zapisz ustawienie, klikając przycisk **Save** (Zapisz) znajdujący się u góry.

23. Teraz upewnij się, że aprowizacja jest **włączona**: w sekcji Settings (Ustawienia) ustaw przełącznik w pozycji On (Włączone), a następnie kliknij przycisk **Save** (Zapisz) znajdujący się u góry.

    ![Dodaj użytkownika](./media/amazon-web-service-tutorial/provisioning2.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do Amazon Web Services (AWS).

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Amazon Web Services (AWS)** .
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-amazon-web-services-aws-test-user"></a>Tworzenie użytkownika testowego usług Amazon Web Services (AWS)

Celem tej sekcji jest utworzenie użytkownika o nazwie B. Simon w Amazon Web Services (AWS). Usługi Amazon Web Services (AWS) nie wymagają utworzenia użytkownika w swoim systemie logowania jednokrotnego, więc w tym miejscu nie trzeba wykonywać żadnych działań.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

Po wybraniu kafelka Amazon Web Services (AWS) w panelu dostępu należy automatycznie zalogować się do Amazon Web Services (AWS), dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="known-issues"></a>Znane problemy

 * W sekcji **Aprowizacja** w podsekcji **Mapowania** będzie wyświetlany komunikat „Trwa ładowanie...” i nigdy nie będą wyświetlane mapowania atrybutów. Jedyny obsługiwany obecnie przepływ pracy aprowizacji to importowanie ról z usług AWS do usługi Azure AD w celu wyboru podczas przypisywania użytkownika/grupy. Mapowania atrybutów na te potrzeby są wstępnie zdefiniowane i nie można ich konfigurować.
 
 * Sekcja **Aprowizacja** obsługuje wprowadzanie tylko jednego zestawu poświadczeń jednocześnie dla jednej dzierżawy usług AWS. Wszystkie zaimportowane role są zapisywane w ramach właściwości appRoles [obiektu servicePrincipal](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) usługi Azure AD dla dzierżawy usług AWS. Z poziomu galerii do usługi Azure AD można dodać wiele dzierżaw usług AWS (reprezentowanych przez obiekty servicePrincipal) służących do aprowizacji, jednak istnieje znany problem związany z brakiem możliwości automatycznego zapisywania wszystkich zaimportowanych ról z wielu obiektów servicePrincipal usług AWS używanych do aprowizacji do pojedynczego obiektu servicePrincipal używanego do logowania jednokrotnego. Jako obejście można użyć [interfejsu API programu Microsoft Graph](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) do wyodrębnienia wszystkich zaimportowanych ról aplikacji do poszczególnych obiektów servicePrincipal usług AWS, dla których skonfigurowano aprowizację. Te ciągi ról można następnie dodawać do obiektu servicePrincipal usług AWS, dla którego skonfigurowano logowanie jednokrotne.

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png

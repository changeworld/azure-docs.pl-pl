---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z usługą Amazon Web Services (AWS) | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i usługami Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40fd8217285643aa7d706d194d7f78ba0634dd32
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048962"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-amazon-web-services-aws"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z usługami Amazon Web Services (AWS)

W tym samouczku dowiesz się, jak zintegrować usługi Amazon Web Services (AWS) z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usług Amazon Web Services (AWS) z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do usług Amazon Web Services (AWS).
* Włącz automatyczne logowanie użytkowników do usług Amazon Web Services (AWS) za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

![Diagram relacji usługi Azure AD i AWS](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Można skonfigurować wiele identyfikatorów dla wielu wystąpień. Przykład:

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Dzięki tym wartościom usługa Azure **#** AD usuwa wartość `https://signin.aws.amazon.com/saml` usługi , i wysyła poprawną wartość jako adres URL odbiorców w tokenie SAML.

Firma Microsoft zaleca takie podejście z następujących powodów:

- Każda aplikacja zapewnia unikatowy certyfikat X509. Każde wystąpienie wystąpienia aplikacji AWS może mieć inną datę wygaśnięcia certyfikatu, którą można zarządzać na podstawie indywidualnego konta AWS. Ogólne przerzucie certyfikatu jest łatwiejsze w tym przypadku.

- Możesz włączyć inicjowanie obsługi administracyjnej użytkowników za pomocą aplikacji AWS w usłudze Azure AD, a następnie nasza usługa pobiera wszystkie role z tego konta AWS. Nie trzeba ręcznie dodawać ani aktualizować ról AWS w aplikacji.

- Możesz przypisać właściciela aplikacji indywidualnie dla aplikacji. Ta osoba może zarządzać aplikacją bezpośrednio w usłudze Azure AD.

> [!Note]
> Upewnij się, że używasz tylko aplikacji galerii.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną rejestracją jednokrotną (SSO) usługi AWS.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Usługi Amazon Web Services (AWS) obsługują logowanie jednokrotne inicjowane przez **dostawcę usługi i dostawcę tożsamości**
* Po skonfigurowaniu amazon web services (AWS) można wymusić kontrolę sesji, które chronią eksfiltracji i infiltracji poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> Identyfikator tej aplikacji jest stałą wartością ciągu, więc tylko jedno wystąpienie można skonfigurować w jednej dzierżawie.

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Dodawanie usług Amazon Web Services (AWS) z galerii

Aby skonfigurować integrację usług Amazon Web Services (AWS) z usługą Azure AD, należy dodać usługi Amazon Web Services (AWS) z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego, konta szkolnego lub osobistego konta Microsoft.
1. W witrynie Azure portal wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W menu przegląd usługi Azure Active Directory wybierz polecenie **Aplikacje przedsiębiorstwa** > **Wszystkie aplikacje**.
1. Wybierz **pozycję Nowa aplikacja,** aby dodać aplikację.
1. W sekcji **Dodaj z galerii** wpisz **Amazon Web Services (AWS)** w polu wyszukiwania.
1. Wybierz **Amazon Web Services (AWS)** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla usług Amazon Web Services (AWS)

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą usług Amazon Web Services (AWS) przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sygnowania użytkownika działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w usługach Amazon Web Services (AWS).

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą usług Amazon Web Services (AWS), wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne amazon web services (AWS)](#configure-amazon-web-services-aws-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego Amazon Web Services (AWS)](#create-amazon-web-services-aws-test-user)** — aby mieć odpowiednik B.Simon w Amazon Web Services (AWS), który jest połączony z reprezentacją użytkownika usługi Azure AD.
    1. **[Jak skonfigurować inicjowanie obsługi administracyjnej ról w usługach Amazon Web Services (AWS)](#how-to-configure-role-provisioning-in-amazon-web-services-aws)**
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. Na stronie integracji aplikacji **Amazon Web Services (AWS)** znajdź [sekcję](https://portal.azure.com/) **Zarządzaj** i wybierz opcję **logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** aplikacja jest wstępnie skonfigurowana, a niezbędne adresy URL są już wstępnie wypełnione platformą Azure. Użytkownik musi zapisać konfigurację, wybierając pozycję **Zapisz**.

1. Podczas konfigurowania więcej niż jednego wystąpienia, podaj wartość identyfikatora. Począwszy od drugiego wystąpienia, użyj następującego **#** formatu, w tym znaku, aby określić unikatową wartość SPN.

    `https://signin.aws.amazon.com/saml#2`

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź kod **XML metadanych federacji** i wybierz pozycję **Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie usług Amazon Web Services (AWS)** skopiuj odpowiednie adresy URL na podstawie wymagań użytkownika.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B.Simon.

1. W witrynie Azure portal wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W menu przegląd usługi Azure Active Directory wybierz polecenie **Użytkownicy** > **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Amazon Web Services (AWS).

1. W witrynie Azure portal wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W menu przegląd usługi Azure Active Directory wybierz polecenie **Aplikacje przedsiębiorstwa** > **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Amazon Web Services (AWS)**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-amazon-web-services-aws-sso"></a>Konfigurowanie usługi Amazon Web Services (AWS) — sygatora internetowego

1. W innym oknie przeglądarki zaloguj się do witryny firmy AWS jako administrator.

2. Wybierz **AWS Home**.

    ![Zrzut ekranu przedstawiający witrynę firmy AWS z wyróżnioną ikoną AWS Home][11]

3. Wybierz **pozycję Zarządzanie tożsamościami i dostępem**.

    ![Zrzut ekranu przedstawiający stronę usług AWS z wyróżnioną pozycją IAM][12]

4. Wybierz **pozycję Dostawcy** > tożsamości**Utwórz dostawcę**.

    ![Zrzut ekranu przedstawiający stronę IAM z wyróżnioną wyróżnioną pozycją Dostawcy tożsamości i dostawcą tworzenia][13]

5. Na stronie **Konfigurowanie dostawcy** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawiający dostawcę konfiguracji][14]

    a. W przypadku **typu dostawcy**wybierz pozycję **SAML**.

    b. W przypadku **nazwy dostawcy**wpisz nazwę dostawcy (na przykład *WAAD).*

    d. Aby przesłać pobrany **plik metadanych** z witryny Azure portal, wybierz **pozycję Wybierz plik**.

    d. Wybierz **następny krok**.

6. Na stronie **Weryfikuj informacje o dostawcy** wybierz pozycję **Utwórz**.

    ![Zrzut ekranu przedstawiający weryfikowanie informacji o dostawcy z wyróżnioną pozycją Utwórz][15]

7. Wybierz **pozycję Rola** > **Utwórz rolę**.

    ![Zrzut ekranu strony Role][16]

8. Na stronie **Create role** (Tworzenie roli) wykonaj następujące kroki:  

    ![Zrzut ekranu przedstawiający stronę utwórz rolę][19]

    a. W obszarze **Wybierz typ zaufanej encji**wybierz pozycję **FEDERACJA SAML 2.0**.

    b. W obszarze **Wybierz dostawcę SAML 2.0**wybierz wcześniej utworzonego **dostawcę SAML** (na przykład: *WAAD*).

    d. Wybierz pozycję **Allow programmatic and AWS Management Console access** (Zezwalaj na dostęp programowy i za pomocą konsoli AWS Management Console).
  
    d. Wybierz **dalej: Uprawnienia**.

9. W oknie dialogowym **Dołączanie zasad uprawnień** dołącz odpowiednie zasady dla swojej organizacji. Następnie wybierz **przycisk Dalej: Przejrzyj**.  

    ![Zrzut ekranu przedstawiający okno dialogowe Dołączanie zasad uprawnień][33]

10. W oknie dialogowym **Przeglądanie** wykonaj następujące czynności:

    ![Zrzut ekranu z okna dialogowego Recenzja][34]

    a. W **nazwie roli**wprowadź nazwę roli.

    b. W **opisie roli**wprowadź opis.

    d. Wybierz **pozycję Utwórz rolę**.

    d. Utwórz dowolną liczbę ról i zamapuj je na dostawcę tożsamości.

11. Poświadczenia konta usługi AWS służy do pobierania ról z konta AWS w inicjowaniu obsługi administracyjnej użytkowników usługi Azure AD. W tym celu otwórz stronę główną konsoli usług AWS.

12. Wybierz pozycję **Usługi**. W obszarze **Zabezpieczenia, zgodność & tożsamości**wybierz pozycję **IAM**.

    ![Zrzut ekranu przedstawiający domową konsolę AWS z podświetlenymi usługami i iam](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. W sekcji IAM wybierz pozycję **Zasady**.

    ![Zrzut ekranu przedstawiający sekcję IAM z wyróżnioną wyróżnioną wyróżnioną pouniętą zasadami](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Utwórz nową zasadę, wybierając **pozycję Utwórz zasady** do pobierania ról z konta AWS w inicjowaniu obsługi administracyjnej użytkowników usługi Azure AD.

    ![Zrzut ekranu przedstawiający stronę Utwórz rolę z wyróżnioną pozycją Utwórz zasady](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Utwórz własne zasady, aby pobrać wszystkie role z kont AWS.

    ![Zrzut ekranu przedstawiający stronę Tworzenie zasad z wyróżnioną pozycją JSON](./media/amazon-web-service-tutorial/policy1.png)

    a. W **obszarze Tworzenie zasad**wybierz kartę **JSON.**

    b. W dokumencie zasad dodaj następujący JSON:

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

    d. Wybierz **pozycję Przejrzyj zasady,** aby zweryfikować zasady.

    ![Zrzut ekranu przedstawiający stronę Tworzenie zasad](./media/amazon-web-service-tutorial/policy5.png)

16. Zdefiniuj nową zasadę.

    ![Zrzut ekranu przedstawiający stronę Utwórz zasady z wyróżnionymi polami Nazwa i Opis](./media/amazon-web-service-tutorial/policy2.png)

    a. W **yjmij nazwę**wpisz **AzureAD_SSOUserRole_Policy**.

    b. W polu **Opis**wprowadź **pozycję Ta zasada umożliwia pobieranie ról z kont AWS**.

    d. Wybierz pozycję **Create policy** (Utwórz zasady).

17. Utwórz nowe konto użytkownika w usłudze AWS IAM.

    a. W konsoli AWS IAM wybierz pozycję **Użytkownicy**.

    ![Zrzut ekranu przedstawiający konsolę AWS IAM z wyróżnioną pojednaną pośniętą](./media/amazon-web-service-tutorial/policy3.png)

    b. Aby utworzyć nowego użytkownika, wybierz pozycję **Dodaj użytkownika**.

    ![Zrzut ekranu przedstawiający przycisk Dodaj użytkownika](./media/amazon-web-service-tutorial/policy4.png)

    d. W sekcji **Dodaj użytkownika:**

    ![Zrzut ekranu przedstawiający stronę Dodaj użytkownika z wyróżnioną nazwą użytkownika i typem dostępu](./media/amazon-web-service-tutorial/adduser1.png)

    * Podaj nazwę użytkownika: **AzureADRoleManager**.

    * Dla typu dostępu wybierz **opcję Dostęp programowy**. W ten sposób użytkownik może wywołać interfejsy API i pobrać role z konta AWS.

    * Wybierz **pozycję Następne uprawnienia**.

18. Utwórz nową zasadę dla tego użytkownika.

    ![Zrzut ekranu przedstawiający dodawanie użytkownika](./media/amazon-web-service-tutorial/adduser2.png)

    a. Wybierz **pozycję Dołącz istniejące zasady bezpośrednio**.

    b. Wyszukaj nowo utworzone zasady w sekcji **AzureAD_SSOUserRole_Policy** filtru.

    d. Wybierz zasadę, a następnie wybierz pozycję **Dalej: Przejrzyj**.

19. Przejrzyj zasady dołączonego użytkownika.

    ![Zrzut ekranu przedstawiający stronę Dodaj użytkownika z wyróżnioną pozycją Utwórz użytkownika](./media/amazon-web-service-tutorial/adduser3.png)

    a. Przejrzyj nazwę użytkownika, typ dostępu i zasady zamapowane do użytkownika.

    b. Wybierz **pozycję Utwórz użytkownika**.

20. Pobierz poświadczenia użytkownika.

    ![Zrzut ekranu przedstawiający dodawanie użytkownika](./media/amazon-web-service-tutorial/adduser4.png)

    a. Skopiuj **identyfikator klucza dostępu** i **klucz dostępu wpisu tajnego**.

    b. Wprowadź te poświadczenia w sekcji inicjowania obsługi administracyjnej użytkowników usługi Azure AD, aby pobrać role z konsoli AWS.

    d. Wybierz polecenie **Zamknij**.

### <a name="how-to-configure-role-provisioning-in-amazon-web-services-aws"></a>Jak skonfigurować inicjowanie obsługi administracyjnej ról w usługach Amazon Web Services (AWS)

1. W portalu zarządzania usługi Azure AD w aplikacji AWS przejdź do **obsługi administracyjnej**.

    ![Zrzut ekranu przedstawiający aplikację AWS z wyróżnioną wyróżnioną aprowisją](./media/amazon-web-service-tutorial/provisioning.png)

2. Wprowadź klucz dostępu i klucz tajny w **polach clientsecret** i **Secret Token,** odpowiednio.

    ![Zrzut ekranu przedstawiający okno dialogowe Poświadczenia administratora](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Podaj klucz dostępu użytkownika usług AWS w polu **clientsecret**.

    b. Podaj wpis tajny użytkownika usług AWS w polu **Secret Token** (Token wpisu tajnego).

    d. Wybierz **opcję Testuj połączenie**.

    d. Zapisz to ustawienie, wybierając pozycję **Zapisz**.

3. W sekcji **Ustawienia** dla pozycji **Stan inicjowania obsługi administracyjnej**wybierz pozycję **Włącz**. Następnie wybierz pozycję **Zapisz**.

    ![Zrzut ekranu przedstawiający sekcję Ustawienia z wyróżnioną wstawioną wzniechcenie](./media/amazon-web-service-tutorial/provisioning2.png)

> [!NOTE]
> Usługa inicjowania obsługi administracyjnej importuje role tylko z usługi AWS do usługi Azure AD. Usługa nie aprowizuje użytkowników i grup z usługi Azure AD do usługi AWS.

> [!NOTE]
> Po zapisaniu poświadczeń inicjowania obsługi administracyjnej, należy poczekać na uruchomienie początkowego cyklu synchronizacji. Synchronizacja trwa zwykle około 40 minut. Stan można zobaczyć u dołu strony **inicjowania obsługi administracyjnej** w obszarze **Bieżący stan**.

### <a name="create-amazon-web-services-aws-test-user"></a>Tworzenie użytkownika testowego usług Amazon Web Services (AWS)

Celem tej sekcji jest utworzenie użytkownika o nazwie B.Simon w Amazon Web Services (AWS). Usługi Amazon Web Services (AWS) nie wymagają utworzenia użytkownika w swoim systemie logowania jednokrotnego, więc w tym miejscu nie trzeba wykonywać żadnych działań.

## <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Amazon Web Services (AWS) na panelu dostępu powinno nastąpić automatyczne zalogowanie do usług Amazon Web Services (AWS), dla których skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="known-issues"></a>Znane problemy

 * W sekcji **Inicjowanie obsługi administracyjnej** podsekcja **Mapowania** pokazuje "Ładowanie..." i nigdy nie wyświetla mapowań atrybutów. Jedynym obsługiwanym obecnie przepływem pracy inicjowania obsługi administracyjnej jest importowanie ról z usługi AWS do usługi Azure AD w celu wyboru podczas przypisywania użytkownika lub grupy. Mapowania atrybutów dla tego są wstępnie określone i nie są konfigurowalne.

 * Sekcja **Aprowizacja** obsługuje wprowadzanie tylko jednego zestawu poświadczeń jednocześnie dla jednej dzierżawy usług AWS. Wszystkie zaimportowane role `appRoles` są zapisywane we właściwości [ `servicePrincipal` obiektu](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) usługi Azure AD dla dzierżawy usługi AWS.

   Wielu dzierżaw AWS (reprezentowanych przez) `servicePrincipals`można dodać do usługi Azure AD z galerii do inicjowania obsługi administracyjnej. Istnieje jednak znany problem, ponieważ nie można automatycznie zapisywać wszystkich zaimportowanych ról `servicePrincipals` z wielu usług `servicePrincipal` AWS używanych do inicjowania obsługi administracyjnej do singla używanego dla jednokrotnego.

   Aby obejść ten problem, można użyć [interfejsu API programu Microsoft Graph,](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) aby wyodrębnić wszystkie `appRoles` importowane do każdego usługi AWS, `servicePrincipal` w którym skonfigurowano inicjowanie obsługi administracyjnej. Następnie można dodać te ciągi ról `servicePrincipal` do usługi AWS, w której skonfigurowano syconą funkcję SSO.

* Role muszą spełniać następujące wymagania, aby kwalifikować się do importowania z usługi AWS do usługi Azure AD:

  * Role muszą mieć dokładnie jednego saml-provider zdefiniowane w AWS

  * Łączna długość roli ARN i saml-provider ARN dla importowanej roli musi wynosić 119 znaków lub mniej

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługi Amazon Web Services (AWS) z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić Amazon Web Services (AWS) dzięki zaawansowanej widoczności i kontroli](https://docs.microsoft.com/cloud-app-security/protect-aws)

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

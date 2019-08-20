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
ms.openlocfilehash: 672a3571202b92232bd45a42254a43019f6a9796
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617343"
---
# <a name="tutorial-integrate-amazon-web-services-aws-with-azure-active-directory"></a>Samouczek: Integruj Amazon Web Services (AWS) z Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować usługę Amazon Web Services (AWS) z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi AWS z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do AWS.
* Zezwól użytkownikom na automatyczne logowanie się do usługi AWS przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji, Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

![Diagram relacji usług Azure AD i AWS](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Można skonfigurować wiele identyfikatorów dla wielu wystąpień. Przykład:

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Za pomocą tych wartości usługa Azure AD usuwa wartość **#** i wysyła poprawną wartość `https://signin.aws.amazon.com/saml` jako adres URL odbiorców w tokenie SAML.

Zalecamy to podejście z następujących powodów:

- Każda aplikacja zapewnia unikatowy certyfikat x509. Każde wystąpienie wystąpienia aplikacji AWS może następnie mieć inną datę wygaśnięcia certyfikatu, która może być zarządzana na poszczególnych kontach AWS. Ogólnie Przerzucanie certyfikatów jest w tym przypadku łatwiejsze.

- Możesz włączyć Inicjowanie obsługi użytkowników przy użyciu aplikacji AWS w usłudze Azure AD, a następnie Nasza usługa pobiera wszystkie role z tego konta AWS. Nie trzeba ręcznie dodawać ani aktualizować ról usług AWS dla aplikacji.

- Właściciela aplikacji można przypisać osobno dla aplikacji. Ta osoba może zarządzać aplikacją bezpośrednio w usłudze Azure AD.

> [!Note]
> Upewnij się, że używasz tylko aplikacji w galerii.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) usługi AWS.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. Usługa AWS obsługuje funkcję SSO zainicjowaną przez usługę **SP i dostawcy tożsamości** .

## <a name="add-aws-from-the-gallery"></a>Dodaj AWS z galerii

Aby skonfigurować integrację programu AWS z usługą Azure AD, musisz dodać AWS z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Amazon Web Services (AWS)** w polu wyszukiwania.
1. Wybierz pozycję **Amazon Web Services (AWS)** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą AWS przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy nawiązać relację między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w AWS.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą AWS, wykonaj następujące bloki konstrukcyjne:

1. **Skonfiguruj Logowanie jednokrotne usługi Azure AD** , aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **Skonfiguruj AWS** , aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **Utwórz użytkownika testowego usługi Azure AD** w celu przetestowania logowania jednokrotnego usługi Azure AD za pomocą B. Simon.
4. **Przypisz użytkownika testowego usługi Azure AD,** aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
5. **Utwórz użytkownika testowego AWS** , aby miał odpowiednik B. Simon w AWS, który jest połączony z reprezentacją usługi Azure AD.
6. **Przetestuj logowanie** jednokrotne, aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Amazon Web Services (AWS)** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie**jednokrotne.
1. Na stronie **Wybierz metodę logowania** jednokrotnego wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** wybierz ikonę pióra dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Zrzut ekranu przedstawiający Konfigurowanie logowania jednokrotnego przy użyciu strony SAML z wyróżnioną ikoną pióra](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja SAML** aplikacja została wstępnie skonfigurowana, a wymagane adresy URL są już wstępnie wypełnione na platformie Azure. Użytkownik musi zapisać konfigurację, wybierając pozycję **Zapisz**.

5. W przypadku konfigurowania więcej niż jednego wystąpienia podaj wartość identyfikatora. W drugim wystąpieniu, użyj następującego formatu, łącznie **#** ze znakiem, aby określić unikatową wartość SPN.

    `https://signin.aws.amazon.com/saml#2`

6. Aplikacja AWS oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Wybierz ikonę pióra, aby otworzyć okno dialogowe atrybuty użytkownika.

    ![Zrzut ekranu przedstawiający okno dialogowe atrybuty użytkownika z wyróżnioną ikoną pióra](common/edit-attribute.png)

7. Oprócz powyższych atrybutów aplikacja AWS oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML. W sekcji **oświadczenia użytkownika** w oknie dialogowym **atrybuty użytkownika** wykonaj następujące kroki, aby dodać atrybut tokenu SAML.

    | Name  | Atrybut źródłowy  | Przestrzeń nazw |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Role            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | „podaj wartość z zakresu od 900 sekund (15 minut) do 43200 sekund (12 godzin)” |  https://aws.amazon.com/SAML/Attributes |

    a. Wybierz pozycję **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![Zrzut ekranu sekcji oświadczenia użytkowników z opcją Dodaj nowe oświadczenie i Zapisz wyróżniony](common/new-save-attribute.png)

    ![Zrzut ekranu przedstawiający okno dialogowe Zarządzanie oświadczeniami użytkowników](common/new-attribute-details.png)

    b. W polu **Nazwa**wpisz nazwę atrybutu wyświetlanego dla tego wiersza.

    c. W polu **przestrzeń nazw**wpisz wartość przestrzeni nazw wyświetlaną dla tego wiersza.

    d. W obszarze **Źródło**wybierz pozycję **atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Wybierz przycisk **OK**.

    g. Wybierz pozycję **Zapisz**.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji**. Wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

   ![Zrzut ekranu przedstawiający sekcję certyfikat podpisywania SAML z wyróżnioną pozycją Pobierz](common/metadataxml.png)

1. W sekcji **konfigurowanie Amazon Web Services (AWS)** skopiuj odpowiednie adresy URL na podstawie wymagań.

   ![Zrzut ekranu przedstawiający sekcję Konfigurowanie Amazon Web Services (AWS) z wyróżnionymi adresami URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-aws"></a>Konfigurowanie AWS

1. W innym oknie przeglądarki Zaloguj się do witryny firmy AWS jako administrator.

2. Wybierz pozycję **AWS Home**.

    ![Zrzut ekranu przedstawiający witrynę firmy AWS z wyróżnioną ikoną główną AWS][11]

3. Wybierz pozycję **Zarządzanie tożsamościami i dostępem**.

    ![Zrzut ekranu przedstawiający stronę usług AWS Services z wyróżnioną pozycją IAM][12]

4. Wybierz pozycję **dostawcy** > tożsamości**Utwórz dostawcę**.

    ![Zrzut ekranu przedstawiający stronę mapy][13]

5. Na stronie **Konfiguruj dostawcę** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawiający konfigurację dostawcy][14]

    a. W obszarze **Typ dostawcy**wybierz pozycję **SAML**.

    b. W obszarze **Nazwa dostawcy**wpisz nazwę dostawcy (na przykład: *WAAD*).

    c. Aby przekazać pobrany **plik metadanych** z Azure Portal, wybierz pozycję **Wybierz plik**.

    d. Wybierz **Następny krok**.

6. Na stronie **Weryfikowanie informacji o dostawcy** wybierz pozycję **Utwórz**.

    ![Zrzut ekranu przedstawiający Weryfikowanie informacji o dostawcy, z wyróżnioną pozycją Utwórz][15]

7. Wybierz pozycję **role** > **Utwórz rolę**.

    ![Zrzut ekranu strony ról][16]

8. Na stronie **Create role** (Tworzenie roli) wykonaj następujące kroki:  

    ![Zrzut ekranu przedstawiający stronę tworzenie roli][19]

    a. W obszarze **Wybierz typ zaufanej jednostki**wybierz pozycję **SAML 2,0 Federation**.

    b. W obszarze **Wybierz dostawcę saml 2,0**wybierz wcześniej utworzony **dostawca SAML** (na przykład: *WAAD*).

    c. Wybierz pozycję **Allow programmatic and AWS Management Console access** (Zezwalaj na dostęp programowy i za pomocą konsoli AWS Management Console).
  
    d. Wybierz opcję **Dalej: Permissions** (Dalej: uprawnienia).

9. W oknie dialogowym **Dołącz zasady uprawnień** Dołącz odpowiednie zasady dla swojej organizacji. Następnie wybierz **pozycję Dalej: Review** (Dalej: przegląd).  

    ![Zrzut ekranu przedstawiający okno dialogowe zasady dołączania uprawnień][33]

10. W oknie dialogowym **Przegląd** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawiający okno dialogowe przegląd][34]

    a. W polu **Nazwa roli**wprowadź nazwę roli.

    b. W polu **Opis roli**wprowadź opis.

    c. Wybierz pozycję **Utwórz rolę**.

    d. Utwórz dowolną liczbę ról zgodnie z potrzebami i zamapuj je na dostawcę tożsamości.

11. Użyj poświadczeń konta usługi AWS, aby pobrać role z konta AWS w ramach aprowizacji użytkowników usługi Azure AD. W tym celu otwórz stronę główną konsoli usług AWS.

12. Wybierz pozycję **usługi**. W obszarze **zabezpieczenia, tożsamość & zgodność**, wybierz pozycję **IAM**.

    ![Zrzut ekranu przedstawiający stronę główną konsoli programu AWS z wyróżnionymi usługami i usługą IAM](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. W sekcji IAM wybierz pozycję **zasady**.

    ![Zrzut ekranu sekcji IAM z wyróżnionymi zasadami](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Utwórz nowe zasady, wybierając pozycję **Utwórz zasady** , aby pobrać role z konta AWS w ramach aprowizacji użytkowników usługi Azure AD.

    ![Zrzut ekranu przedstawiający stronę tworzenie roli z wyróżnionymi zasadami tworzenia zasad](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Utwórz własne zasady, aby pobrać wszystkie role z kont AWS.

    ![Zrzut ekranu przedstawiający stronę Tworzenie zasad z wyróżnioną pozycją JSON](./media/amazon-web-service-tutorial/policy1.png)

    a. W obszarze **Tworzenie zasad**wybierz kartę **JSON** .

    b. W dokumencie zasad Dodaj następujący kod JSON:

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

    c. Wybierz pozycję **Przejrzyj zasady** , aby sprawdzić poprawność zasad.

    ![Zrzut ekranu przedstawiający stronę Tworzenie zasad](./media/amazon-web-service-tutorial/policy5.png)

16. Zdefiniuj nowe zasady.

    ![Zrzut ekranu przedstawiający stronę Tworzenie zasad z wyróżnionymi polami nazwa i opis](./media/amazon-web-service-tutorial/policy2.png)

    a. W obszarze **Nazwa**wprowadź **AzureAD_SSOUserRole_Policy**.

    b. W polu **Opis**wprowadź następujące zasady umożliwią **pobranie ról z kont usługi AWS**.

    c. Wybierz pozycję **Utwórz zasady**.

17. Utwórz nowe konto użytkownika w usłudze IAM AWS.

    a. W konsoli AWS IAM wybierz pozycję **Użytkownicy**.

    ![Zrzut ekranu przedstawiający konsolę AWS IAM z wyróżnionymi użytkownikami](./media/amazon-web-service-tutorial/policy3.png)

    b. Aby utworzyć nowego użytkownika, wybierz pozycję **Dodaj użytkownika**.

    ![Zrzut ekranu przedstawiający przycisk Dodaj użytkownika](./media/amazon-web-service-tutorial/policy4.png)

    c. W sekcji **Dodawanie użytkownika** :

    ![Zrzut ekranu przedstawiający stronę Dodawanie użytkownika z wyróżnioną nazwą użytkownika i typem dostępu](./media/amazon-web-service-tutorial/adduser1.png)

    * Podaj nazwę użytkownika: **AzureADRoleManager**.

    * W polu Typ dostępu wybierz pozycję **dostęp programistyczny**. Dzięki temu użytkownik może wywołać interfejsy API i pobrać role z konta AWS.

    * Wybierz pozycję **następne uprawnienia**.

18. Utwórz nowe zasady dla tego użytkownika.

    ![Zrzut ekranu przedstawiający Dodawanie użytkownika](./media/amazon-web-service-tutorial/adduser2.png)

    a. Wybierz pozycję **Dołącz istniejące zasady bezpośrednio**.

    b. Wyszukaj nowo utworzone zasady w sekcji **AzureAD_SSOUserRole_Policy** filtru.

    c. Wybierz zasady, a następnie wybierz pozycję **dalej: Review** (Dalej: przegląd).

19. Zapoznaj się z zasadami dla dołączonego użytkownika.

    ![Zrzut ekranu przedstawiający stronę Dodawanie użytkownika z wyróżnioną pozycją Utwórz użytkownika](./media/amazon-web-service-tutorial/adduser3.png)

    a. Przejrzyj nazwę użytkownika, typ dostępu i zasady zamapowane do użytkownika.

    b. Wybierz pozycję **Utwórz użytkownika**.

20. Pobierz poświadczenia użytkownika.

    ![Zrzut ekranu przedstawiający Dodawanie użytkownika](./media/amazon-web-service-tutorial/adduser4.png)

    a. Skopiuj **identyfikator klucza dostępu** i **klucz dostępu wpisu tajnego**.

    b. Wprowadź te poświadczenia w sekcji aprowizacji użytkowników usługi Azure AD, aby pobrać role z konsoli AWS.

    c. Wybierz polecenie **Zamknij**.

21. W portalu zarządzania usługi Azure AD w aplikacji AWS przejdź do obszaru **udostępniania**.

    ![Zrzut ekranu aplikacji AWS z wyróżnioną obsługą administracyjną](./media/amazon-web-service-tutorial/provisioning.png)

22. Wprowadź odpowiednio klucz dostępu i wpis tajny w polach **clientsecret** i **Secret** .

    ![Zrzut ekranu przedstawiający okno dialogowe poświadczenia administratora](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Podaj klucz dostępu użytkownika usług AWS w polu **clientsecret**.

    b. Podaj wpis tajny użytkownika usług AWS w polu **Secret Token** (Token wpisu tajnego).

    c. Wybierz pozycję **Testuj połączenie**.

    d. Zapisz ustawienie, wybierając pozycję **Zapisz**.

23. W sekcji **Ustawienia** w obszarze **stan aprowizacji**wybierz pozycję **włączone**. Następnie wybierz pozycję **Zapisz**.

    ![Zrzut ekranu przedstawiający sekcję ustawień z wyróżnioną pozycją](./media/amazon-web-service-tutorial/provisioning2.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego, B. Simon, w Azure Portal.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory** > **Użytkownicy** > **Wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   
   a. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   b. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension. Na przykład `B.Simon@contoso.com`.   
   c. Wybierz pozycję **Pokaż hasło**i Zapisz ją. Następnie wybierz przycisk **Create** (Utwórz).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji należy włączyć usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi AWS.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Amazon Web Services (AWS)** .
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Zrzut ekranu przedstawiający sekcję Zarządzanie z wyróżnionymi użytkownikami i grupami](common/users-groups-blade.png)

1. Wybierz przycisk **Dodaj użytkownika**. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.

    ![Zrzut ekranu przedstawiający Dodawanie użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon**. Następnie wybierz **pozycję Wybierz**.
1. Jeśli oczekujesz, że jakakolwiek wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz z listy odpowiednią rolę dla użytkownika. Następnie wybierz **pozycję Wybierz**.
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Po wybraniu kafelka AWS w panelu dostępu należy automatycznie zalogować się do AWS, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="known-issues"></a>Znane problemy

 * W sekcji dotyczącej **aprowizacji** w podsekcji **mapowania** jest wyświetlana wartość "ładowanie..." i nigdy nie wyświetla mapowań atrybutów. Jedyną obsługiwaną obsługą przepływu pracy jest importowanie ról z AWS do usługi Azure AD w celu wyboru podczas przypisywania użytkowników lub grup. Mapowania atrybutów dla tego elementu są wstępnie określone i nie można ich konfigurować.
 
 * Sekcja **Aprowizacja** obsługuje wprowadzanie tylko jednego zestawu poświadczeń jednocześnie dla jednej dzierżawy usług AWS. Wszystkie zaimportowane role są zapisywane `appRoles` we właściwości [ `servicePrincipal` obiektu](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) usługi Azure AD dla dzierżawy AWS. 
 
   Wiele dzierżawców AWS (reprezentowane przez `servicePrincipals`) można dodać do usługi Azure AD z galerii w celu aprowizacji. Istnieje jednak znany problem, ale nie jest możliwe automatyczne zapisanie wszystkich zaimportowanych ról z wielu AWS `servicePrincipals` używanych do aprowizacji w `servicePrincipal` ramach logowania jednokrotnego. 
   
   Jako obejście można użyć [interfejsu API Microsoft Graph](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) , aby wyodrębnić wszystkie `appRoles` zaimportowane do każdego AWSu `servicePrincipal` , w którym skonfigurowano Inicjowanie obsługi. Następnie można dodać te ciągi ról do AWS `servicePrincipal` , w którym skonfigurowano Logowanie jednokrotne.
 
* Role muszą spełniać następujące wymagania, aby można było zaimportować je z AWS do usługi Azure AD:

  * Role muszą mieć dokładnie jeden dostawca SAML zdefiniowany w AWS

  * Połączona długość roli ARN i SAML-Provider ARN dla importowanej roli nie może zawierać więcej niż 119 znaków

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Samouczków dotyczących integrowania aplikacji SaaS przy użyciu usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

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

---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Amazon Web Services (AWS) do łączenia z wieloma kontami | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure AD i wiele kont Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1961ed4114ac1aecb63dd4e56febbd5ab2a8d20d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56218044"
---
# <a name="tutorial-azure-active-directory-integration-with-multiple-amazon-web-services-aws-accounts"></a>Samouczek: Integracja usługi Azure Active Directory z wieloma kontami usług Amazon Web Services (AWS)

W tym samouczku dowiesz się, jak zintegrować usługi Azure Active Directory (Azure AD) z wieloma kontami Amazon Web Services (AWS).

Integracja usług Amazon Web Services (AWS) z usługą Azure AD daje następujące korzyści:

- Możliwość kontrolowania za pomocą usługi Azure AD kto ma dostęp do usług Amazon Web Services (AWS).
- Użytkowników, aby automatycznie uzyskać zalogowanych do Amazon Web Services (AWS) (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

![Usługi Amazon Web Services (AWS) na liście wyników](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

>[!NOTE]
>Należy pamiętać, że łączenia jednej aplikacji AWS do wszystkich kont platformy AWS nie jest naszym Zalecanym podejściem. Zamiast tego zaleca się używać [to](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial) podejście do skonfigurowania kilku wystąpień konta usług AWS do wielu wystąpień usługi AWS aplikacji w usłudze Azure AD.

**Należy pamiętać, że nie jest zalecane na potrzeby tego podejścia z następujących przyczyn:**

* Masz podejście Graph Explorer umożliwia stosowanie poprawek do wszystkich ról w aplikacji. Nie zaleca się przy użyciu podejścia pliku manifestu.

* Zaobserwowaliśmy klienci raportowania, po dodaniu ~ 1200 role aplikacji w ramach jednej aplikacji AWS, żadnych operacji na jej uruchomienia zgłaszanie błędów związanych z nimi do rozmiaru. Istnieje stały limit rozmiaru w obiekcie aplikacji.

* Musisz ręcznie zaktualizować roli, ponieważ role poproś o dodanie Cię we wszystkich kont, czyli Niestety Zastąp podejście, a nie dołączania. Jeśli Twoje konta są coraz następnie ten staje się również n x n relacji z klientami i rolami.

* Wszystkich kont platformy AWS będzie korzystać z tego samego pliku XML metadanych federacji z czym w czasie Przerzucanie certyfikatów dysku tego ćwiczenia dużych aktualizacji certyfikatu na wszystkich kont platformy AWS, w tym samym czasie

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługami Amazon Web Services (AWS), są potrzebne następujące elementy:

- Subskrypcję usługi Azure AD podstawowa lub premium
- Amazon Web Services (AWS) wielu logowanie jednokrotne włączone konta

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie usług Amazon Web Services (AWS) z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Dodawanie usług Amazon Web Services (AWS) z galerii
Aby skonfigurować integrację usług Amazon Web Services (AWS) z usługą Azure AD, należy dodać usługi Amazon Web Services (AWS) z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać usługi Amazon Web Services (AWS) z galerii, wykonaj następujące kroki:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![image](./media/aws-multi-accounts-tutorial/selectazuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![image](./media/aws-multi-accounts-tutorial/a_select_app.png)
    
3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![image](./media/aws-multi-accounts-tutorial/a_new_app.png)

4. W polu wyszukiwania wpisz **Amazon Web Services (AWS)**, wybierz pozycję **Amazon Web Services (AWS)** na panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![image](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

5. Po dodaniu aplikacji przejdź do obszaru **właściwości** strony i skopiuj **obiektu o identyfikatorze**.

    ![Usługi Amazon Web Services (AWS) na liście wyników](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_properties.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowania, w usłudze Azure AD logowanie jednokrotne, za pomocą Amazon Web Services (AWS) na podstawie użytkownika testowego, o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Amazon Web Services (AWS) do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w Amazon Web Services (AWS) musi zostać ustanowione.

W Amazon Web Services (AWS), przypisz wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD w usługach Amazon Web Services (AWS), należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji usług Amazon Web Services (AWS).

**Aby skonfigurować usługi Azure AD logowanie jednokrotne za pomocą usługi Amazon Web Services (AWS), wykonaj następujące czynności:**

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji usług **Amazon Web Services (AWS)** wybierz pozycję **Logowanie jednokrotne**.

    ![image](./media/aws-multi-accounts-tutorial/B1_B2_Select_SSO.png)

2. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, wybierz **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![image](./media/aws-multi-accounts-tutorial/b1_b2_saml_sso.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![image](./media/aws-multi-accounts-tutorial/b1-domains_and_urlsedit.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** użytkownik nie musi wykonywać żadnych kroków, ponieważ aplikacja jest już wstępnie zintegrowana z platformą Azure.

    ![image](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_url.png)

5. Aplikacja usług Amazon Web Services (AWS) oczekuje asercji SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty i oświadczenia użytkownika** na stronie integracji aplikacji. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** przycisk, aby otworzyć **atrybutów użytkowników i oświadczeń** okna dialogowego.

    ![image](./media/aws-multi-accounts-tutorial/i4-attribute.png)

6. W **oświadczenia użytkownika** sekcji na **atrybutów użytkowników i oświadczeń** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:
    
    | Name (Nazwa)  | Atrybut źródłowy  | Przestrzeń nazw |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Rola            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | „podaj wartość z zakresu od 900 sekund (15 minut) do 43200 sekund (12 godzin)” |  https://aws.amazon.com/SAML/Attributes |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](./media/aws-multi-accounts-tutorial/i2-attribute.png)

    ![image](./media/aws-multi-accounts-tutorial/i3-attribute.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    c. Wprowadź **Namespace** wartość.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij pozycję **Zapisz**.

7. Na **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML** strony w **certyfikat podpisywania SAML** kliknij **Pobierz** można pobrać **XML metadanych Federacji**  i zapisz go na komputerze.

    ![image](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

8. W innym oknie przeglądarki zaloguj się do usług Amazon Web Services (AWS) jako administrator.

9. Kliknij pozycję **AWS Home** (Strona główna usług AWS).

    ![Konfigurowanie logowania jednokrotnego — strona główna][11]

10. Kliknij pozycję **Identity and Access Management** (Zarządzanie tożsamościami i dostępem).

    ![Konfigurowanie logowania jednokrotnego — tożsamość][12]

11. Kliknij pozycję **Identity Providers** (Dostawcy tożsamości), a następnie pozycję **Create Provider** (Utwórz dostawcę).

    ![Konfigurowanie logowania jednokrotnego — dostawca][13]

12. Na stronie okna dialogowego **Configure Provider** (Konfigurowanie dostawcy) wykonaj następujące kroki:

    ![Okno dialogowe Configure Single Sign-On (Konfigurowanie logowania jednokrotnego)][14]

    a. Dla pozycji **Provider Type** (Typ dostawcy) wybierz opcję **SAML**.

    b. W polu tekstowym **Provider Name** (Nazwa dostawcy) wpisz nazwę dostawcy (na przykład: *WAAD*).

    c. Aby przekazać **plik metadanych** pobrany z witryny Azure Portal, kliknij pozycję **Choose File** (Wybierz plik).

    d. Kliknij pozycję **Next Step** (Następny krok).

13. Na stronie okna dialogowego **Verify Provider Information** (Weryfikowanie informacji o dostawcy) kliknij pozycję **Create** (Utwórz).

    ![Konfigurowanie logowania jednokrotnego — weryfikowanie][15]

14. Kliknij pozycję **Roles** (Role), a następnie **Create role** (Utwórz rolę).

    ![Konfigurowanie logowania jednokrotnego — role][16]

15. Na stronie **Create role** (Tworzenie roli) wykonaj następujące kroki:  

    ![Konfigurowanie logowania jednokrotnego — zaufanie][19]

    a. Wybierz pozycję **SAML 2.0 federation** (Federacja SAML 2.0) w obszarze **Select type of trusted entity** (Wybierz typ zaufanej jednostki).

    b. W **sekcji Choose a SAML 2.0 Provider (Wybierz dostawcę SAML 2.0)** określ utworzonego wcześniej **dostawcę SAML** (na przykład: *WAAD*)

    c. Wybierz pozycję **Allow programmatic and AWS Management Console access** (Zezwalaj na dostęp programowy i za pomocą konsoli AWS Management Console).
  
    d. Kliknij pozycję **Next: Permissions** (Dalej: uprawnienia).

16. Na **Dołącz zasady uprawnień** okno dialogowe, nie trzeba dołączać żadnych zasad. Kliknij pozycję **Next: Review** (Dalej: przegląd).  

    ![Konfigurowanie logowania jednokrotnego — zasady][33]

17. W oknie dialogowym **Review** (Przegląd) wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego — przegląd][34]

    a. W polu tekstowym **Role name** (Nazwa roli) podaj nazwę roli.

    b. W polu tekstowym **Role description** (Opis roli) podaj opis.

    c. Kliknij pozycję **Create Role** (Utwórz rolę).

    d. Utwórz potrzebną liczbę ról i zamapuj je na dostawcę tożsamości.

18. Wyloguj z bieżącego konta usługi AWS i zaloguj się przy użyciu innego konta, które chcesz skonfigurować funkcję logowania jednokrotnego na usłudze Azure AD.

19. Wykonaj krok – 9, aby krok-17, aby utworzyć wiele ról, które chcesz instalacji dla tego konta. Jeśli masz więcej niż dwóch kont, wykonaj te same czynności dla wszystkich kont do tworzenia ról dla nich.

20. Gdy wszystkie role są tworzone w ramach kont, są one wyświetlane w **role** listy dla tych kont.

    ![Ustawienia ról](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_listofroles.png)

21. Musimy przechwytywania ARN roli i zaufane jednostki dla wszystkich ról dla wszystkich kont, które musimy mapowanie ręczne za pomocą aplikacji usługi Azure AD. 

22. Kliknij na rolach, aby skopiować **ARN roli** i **zaufane jednostki** wartości. Te wartości będą potrzebne dla wszystkich ról, które należy utworzyć w usłudze Azure AD.

    ![Ustawienia ról](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_role_summary.png)

23. Wykonaj powyższy krok dla wszystkich ról we wszystkich kont i zapisz je wszystkie w formacie **ARN roli, zaufane jednostki** w Notatniku.

24. Otwórz [programu Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) w innym oknie.

    a. Zaloguj się do witryny Graph Explorer przy użyciu poświadczeń globalny administrator/współadministrator dla Twojej dzierżawy.

    b. Musisz mieć wystarczające uprawnienia do tworzenia ról. Kliknij pozycję **modyfikowania uprawnień** do uzyskania wymaganych uprawnień.

    ![Okno dialogowe Eksploratora programu Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Wybierz następujące uprawnienia z listy (jeśli jeszcze nie masz tych) i kliknij pozycję "Modyfikuj uprawnienia" 

    ![Okno dialogowe Eksploratora programu Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Zapyta zalogować się ponownie i zaakceptować zgody. Po zaakceptowaniu zezwolenia, użytkownik jest zalogowany w Eksploratorze programu Graph ponownie.

    e. Zmień listę rozwijaną wersji, aby **beta**. Do pobrania wszystkich jednostek usługi w dzierżawie, użyj następującego zapytania:

     `https://graph.microsoft.com/beta/servicePrincipals`

    Jeśli używasz wielu katalogów, wówczas można użyć następujących wzorzec ma w nim domeny głównej `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Okno dialogowe Eksploratora programu Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. Z listy pobrania nazwy główne usług Pobierz ten, który należy zmodyfikować. Ctrl + F umożliwia również wyszukiwanie aplikacji ze wszystkich wymienionych ServicePrincipals. Można użyć następujących zapytań, za pomocą **obiektu o identyfikatorze** skopiowanej ze strony właściwości usługi AD platformy Azure w celu uzyskania do odpowiedniej nazwy głównej usługi.

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Okno dialogowe Eksploratora programu Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. Wyodrębnij właściwości appRoles z obiektu jednostki usługi.

    ![Okno dialogowe Eksploratora programu Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. Teraz trzeba generować nowe role w aplikacji. 

    i. Poniżej JSON jest przykładem obiektu appRoles. Utworzyć podobne obiektu można dodać role, które mają dla swojej aplikacji.

    ```
    {
    "appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Auditors,WAAD",
            "displayName": "Auditors,WAAD",
            "id": "bcad6926-67ec-445a-80f8-578032504c09",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
        }    ]
    }
    ```

    > [!Note]
    > Możesz tylko dodawać nowe role po **msiam_access** potrzeby operacji patch. Ponadto można dodać dowolną liczbę ról dowolną na potrzeby Twojej organizacji. Usługa Azure AD będzie wysyłać **wartość** tych ról jako wartość oświadczenia w odpowiedzi SAML.

    j. Wróć do usługi Graph Explorer i zmień metodę z **UZYSKAĆ** do **PATCH**. Stosowanie poprawek do obiektu jednostki usługi mają żądanego ról, aktualizując właściwości appRoles podobny do przedstawionego powyżej w przykładzie. Kliknij przycisk **Uruchom kwerendę** do wykonywania operacji patch. Komunikat o powodzeniu potwierdza tworzenia ról dla aplikacji usług Amazon Web Services.

    ![Okno dialogowe Eksploratora programu Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

25. Po jednostki usługi są zainstalowane odpowiednie poprawki z większej liczby ról, można przypisać użytkowników i grup do odpowiednich ról. Można to zrobić, przechodząc do witryny portal i przejdź do aplikacji usług Amazon Web Services. Kliknij pozycję **użytkowników i grup** kartę w górnej części. 

26. Firma Microsoft zaleca tworzenie nowych grup dla każdej roli usług AWS, dzięki czemu można przypisać tego określoną rolę w tej grupie. Należy zauważyć, że to mapowanie jeden-do-jednego dla jednej grupy do jednej roli. Następnie można dodać członków, którzy należą do tej grupy.

27. Po utworzeniu grupy wybierz grupę i przypisz do aplikacji.

    ![Konfigurowanie logowania jednokrotnego Dodaj](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

> [!Note]
> Grupy zagnieżdżone nie są obsługiwane podczas przypisywania grup.

28. Aby przypisać rolę do grupy, wybierz rolę, a następnie kliknij pozycję **przypisać** przycisk w dolnej części strony.

    ![Konfigurowanie logowania jednokrotnego Dodaj](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

> [!Note]
> Należy pamiętać, trzeba odświeżyć sesję w witrynie Azure portal, aby zobaczyć nowe role.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi Amazon Web Services (AWS), w panelu dostępu, powinna pojawić się strona aplikacji usług Amazon Web Services (AWS) przy użyciu opcji, aby wybrać rolę.

![Konfigurowanie logowania jednokrotnego Dodaj](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_screen.png)

Można również sprawdzić odpowiedzi SAML, aby zobaczyć ról przekazywany jako oświadczenia.

![Konfigurowanie logowania jednokrotnego Dodaj](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_saml.png)

Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[11]: ./media/aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/ic7950253.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/

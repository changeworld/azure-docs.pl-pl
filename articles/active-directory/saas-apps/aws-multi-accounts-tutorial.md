---
title: 'Samouczek: integracja Azure Active Directory z usługą Amazon Web Services (AWS) w celu połączenia wielu kont | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między usługą Azure AD i wieloma kontami Amazon Web Services (AWS).
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
ms.date: 01/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb528d71b94449b282947a487e4fc79b343df778
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74195902"
---
# <a name="tutorial-azure-active-directory-integration-with-multiple-amazon-web-services-aws-accounts"></a>Samouczek: integracja Azure Active Directory z wieloma kontami Amazon Web Services (AWS)

W tym samouczku dowiesz się, jak zintegrować usługę Azure Active Directory (Azure AD) z wieloma kontami Amazon Web Services (AWS).

Integracja usług Amazon Web Services (AWS) z usługą Azure AD daje następujące korzyści:

- Możliwość kontrolowania za pomocą usługi Azure AD kto ma dostęp do usług Amazon Web Services (AWS).
- Możesz umożliwić użytkownikom automatyczne uzyskiwanie zalogowanych do Amazon Web Services (AWS) (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

![Usługi Amazon Web Services (AWS) na liście wyników](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

>[!NOTE]
>Należy pamiętać, że połączenie jednej aplikacji AWS ze wszystkimi kontami AWS nie jest rozwiązaniem zalecanym. Zamiast tego zalecamy użycie [tej](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial) metody w celu skonfigurowania wielu wystąpień konta AWS na wielu wystąpieniach aplikacji AWS w usłudze Azure AD.

**Należy pamiętać, że nie zaleca się używania tego podejścia z następujących powodów:**

* Musisz użyć podejścia do Eksploratora grafów, aby poprawić wszystkie role do aplikacji. Nie zalecamy użycia metody pliku manifestu.

* Widzimy, że po dodaniu ~ 1200 ról aplikacji dla pojedynczej aplikacji AWS każda operacja w aplikacji rozpoczęła generowanie błędów związanych z rozmiarem. Istnieje stały limit rozmiaru obiektu aplikacji.

* Musisz ręcznie zaktualizować rolę, ponieważ role zostaną dodane do dowolnego konta, które jest podejściem zastępującym i nie dołączaj niestety. Ponadto, jeśli Twoje konta są rosnące, to n x n relacji z kontami i rolami.

* Wszystkie konta usługi AWS będą używać tego samego pliku XML metadanych Federacji i w czasie przerzucania certyfikatów, które trzeba wykonać w celu uzyskania tego ogromnego ćwiczenia, aby zaktualizować certyfikat na wszystkich kontach AWS w tym samym czasie

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługami Amazon Web Services (AWS), są potrzebne następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja usług Amazon Web Services (AWS) z włączonym logowaniem jednokrotnym

> [!NOTE]
> Nie zalecamy używania środowiska produkcyjnego do testowania czynności opisanych w tym samouczku.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługi Amazon Web Services (AWS) obsługują logowanie jednokrotne inicjowane przez **dostawcę usługi i dostawcę tożsamości**

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Dodawanie usług Amazon Web Services (AWS) z galerii

Aby skonfigurować integrację usług Amazon Web Services (AWS) z usługą Azure AD, należy dodać usługi Amazon Web Services (AWS) z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać usługi Amazon Web Services (AWS) z galerii, wykonaj następujące kroki:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk usługi Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Amazon Web Services (AWS)** , wybierz pozycję **Amazon Web Services (AWS)** na panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Usługi Amazon Web Services (AWS) na liście wyników](common/search-new-app.png)

5. Po dodaniu aplikacji przejdź do strony **Właściwości** i skopiuj **Identyfikator obiektu**.

    ![Usługi Amazon Web Services (AWS) na liście wyników](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_properties.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą Amazon Web Services (AWS) na podstawie użytkownika testowego o nazwie "Britta Simon".

Aby logowanie jednokrotne działało, usługa Azure AD musi znać, co odpowiedni użytkownik w Amazon Web Services (AWS) należy do użytkownika w usłudze Azure AD. Innymi słowy, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Amazon Web Services (AWS).

W Amazon Web Services (AWS) Przypisz wartość **Nazwa użytkownika** w usłudze Azure AD jako wartość parametru **username** , aby określić relację łącza.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD w usługach Amazon Web Services (AWS), należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w usługach Amazon Web Services (AWS)](#configure-amazon-web-services-aws-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączasz Logowanie jednokrotne w usłudze Azure AD w Azure Portal i skonfiguruj Logowanie jednokrotne w aplikacji Amazon Web Services (AWS).

**Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą Amazon Web Services (AWS), wykonaj następujące czynności:**

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji usług **Amazon Web Services (AWS)** wybierz pozycję **Logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** użytkownik nie musi wykonywać żadnych kroków, ponieważ aplikacja jest już wstępnie zintegrowana z platformą Azure.

    ![image](common/preintegrated.png)

5. Aplikacja usług Amazon Web Services (AWS) oczekuje asercji SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty i oświadczenia użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij przycisk **Edytuj** , aby otworzyć okno dialogowe **atrybuty użytkownika & oświadczenia** .

    ![image](common/edit-attribute.png)

6. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej, i wykonaj następujące czynności:

    | Nazwa  | Atrybut źródłowy  | Przestrzeń nazw |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Rola            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | „podaj wartość z zakresu od 900 sekund (15 minut) do 43200 sekund (12 godzin)” |  https://aws.amazon.com/SAML/Attributes |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    c. W polu tekstowym **Przestrzeń nazw** wpisz wartość przestrzeni nazw pokazaną dla tego wiersza.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij pozycję **Zapisz**.

7. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** kliknij pozycję **Pobierz** , aby pobrać **plik XML metadanych Federacji** i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

### <a name="configure-amazon-web-services-aws-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usług Amazon Web Services (AWS)

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

    b. W polu tekstowym **Nazwa dostawcy** wpisz nazwę dostawcy (na przykład: *WAAD*).

    c. Aby przekazać **plik metadanych** pobrany z witryny Azure Portal, kliknij pozycję **Choose File** (Wybierz plik).

    d. Kliknij pozycję **Next Step** (Następny krok).

6. Na stronie okna dialogowego **Verify Provider Information** (Weryfikowanie informacji o dostawcy) kliknij pozycję **Create** (Utwórz).

    ![Konfigurowanie logowania jednokrotnego — weryfikowanie][15]

7. Kliknij pozycję **Roles** (Role), a następnie **Create role** (Utwórz rolę).

    ![Konfigurowanie logowania jednokrotnego — role][16]

8. Na stronie **Create role** (Tworzenie roli) wykonaj następujące kroki:  

    ![Konfigurowanie logowania jednokrotnego — zaufanie][19]

    a. Wybierz pozycję **SAML 2.0 federation** (Federacja SAML 2.0) w obszarze **Select type of trusted entity** (Wybierz typ zaufanej jednostki).

    b. W obszarze **Wybieranie dostawcy saml 2,0**wybierz wcześniej utworzony **dostawca SAML** (na przykład: *WAAD*)

    c. Wybierz pozycję **Allow programmatic and AWS Management Console access** (Zezwalaj na dostęp programowy i za pomocą konsoli AWS Management Console).
  
    d. Kliknij przycisk **Dalej: uprawnienia**.

9. W oknie dialogowym **Attach Permissions Policies** (Dołączanie zasad uprawnień) dołącz zasady odpowiednie dla Twojej organizacji. Kliknij przycisk **Dalej: przegląd**.  

    ![Konfigurowanie logowania jednokrotnego — zasady][33]

10. W oknie dialogowym **Review** (Przegląd) wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego — przegląd][34]

    a. W polu tekstowym **Role name** (Nazwa roli) podaj nazwę roli.

    b. W polu tekstowym **Role description** (Opis roli) podaj opis.

    c. Kliknij pozycję **Create Role** (Utwórz rolę).

    d. Utwórz potrzebną liczbę ról i zamapuj je na dostawcę tożsamości.

11. Wyloguj się z bieżącego konta AWS i zaloguj się przy użyciu innego konta, w którym chcesz skonfigurować Logowanie jednokrotne za pomocą usługi Azure AD.

12. Wykonaj krok od 2 do kroku-10, aby utworzyć wiele ról, które chcesz skonfigurować dla tego konta. Jeśli masz więcej niż dwa konta, wykonaj te same kroki dla wszystkich kont, aby utworzyć dla nich role.

13. Po utworzeniu wszystkich ról na kontach zostaną one wyświetlone na liście **role** dla tych kont.

    ![Konfiguracja ról](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_listofroles.png)

14. Musimy przechwycić wszystkie role ARN i Zaufane jednostki dla wszystkich ról na wszystkich kontach, które muszą być mapowane ręcznie przy użyciu aplikacji usługi Azure AD.

15. Kliknij role, aby skopiować wartości **ARN ról** i **zaufanych jednostek** . Te wartości są wymagane dla wszystkich ról, które należy utworzyć w usłudze Azure AD.

    ![Konfiguracja ról](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_role_summary.png)

16. Wykonaj powyższy krok dla wszystkich ról we wszystkich kontach i Zapisz wszystkie z nich w formacie **role ARN, Zaufane jednostki** w Notatniku.

17. Otwórz [Eksploratora grafów usługi Azure AD](https://developer.microsoft.com/graph/graph-explorer) w innym oknie.

    a. Zaloguj się do witryny programu Graph Explorer przy użyciu poświadczeń administratora globalnego/współadministratora dla Twojej dzierżawy.

    b. Musisz mieć wystarczające uprawnienia do tworzenia ról. Kliknij przycisk **Modyfikuj uprawnienia** , aby uzyskać wymagane uprawnienia.

    ![Eksplorator grafu — okno dialogowe](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Wybierz następujące uprawnienia z listy (jeśli nie masz tych jeszcze) i kliknij pozycję "Modyfikuj uprawnienia" 

    ![Eksplorator grafu — okno dialogowe](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Spowoduje to wyświetlenie żądania zalogowania się i zaakceptowanie zgody. Po zaakceptowaniu zgody użytkownik jest ponownie zalogowany do Eksploratora grafów.

    e. Zmień listę rozwijaną wersji na wersję **beta**. Aby pobrać wszystkie jednostki usługi z dzierżawy, należy użyć następującego zapytania:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Jeśli używasz wielu katalogów, możesz użyć następującego wzorca, który ma swoją domenę podstawową, `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Eksplorator grafu — okno dialogowe](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. Z listy pobranych jednostek usługi Pobierz tę, którą chcesz zmodyfikować. Możesz również użyć klawiszy Ctrl + F, aby przeszukać aplikację ze wszystkich obiektów serviceprincipals. Możesz użyć następującego zapytania, używając **identyfikatora obiektu** skopiowanego ze strony właściwości usługi Azure AD, aby uzyskać dostęp do odpowiedniej jednostki usługi.

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Eksplorator grafu — okno dialogowe](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. Wyodrębnij Właściwość appRoles z obiektu jednostki usługi.

    ![Eksplorator grafu — okno dialogowe](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. Teraz musisz wygenerować nowe role dla swojej aplikacji. 

    i. Poniższy kod JSON jest przykładem obiektu appRoles. Utwórz podobny obiekt, aby dodać role, które mają być używane w aplikacji.

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
    > Nowe role można dodawać tylko po **msiam_access** dla operacji patch. Ponadto możesz dodać dowolną liczbę ról zgodnie z potrzebami organizacji. Usługa Azure AD wyśle **wartość** tych ról jako wartość żądania w odpowiedzi SAML.

    j. Wróć do Eksploratora grafów i Zmień metodę z **Get** na **patch**. Poprawka obiektu jednostki usługi w celu uzyskania żądanych ról przez zaktualizowanie właściwości appRoles podobnej do przedstawionej powyżej w przykładzie. Kliknij przycisk **Uruchom zapytanie** , aby wykonać operację patch. Komunikat o powodzeniu potwierdza utworzenie roli dla aplikacji Amazon Web Services.

    ![Eksplorator grafu — okno dialogowe](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

18. Po zastosowaniu poprawki jednostki usługi z większą liczbą ról można przypisywać użytkowników/grupy do odpowiednich ról. Można to zrobić, przechodząc do portalu i przechodząc do aplikacji Amazon Web Services. Kliknij kartę **Użytkownicy i grupy** w górnej części strony.

19. Zalecamy utworzenie nowych grup dla każdej roli AWS, aby można było przypisać tę konkretną rolę w tej grupie. Należy pamiętać, że jest to jedno do jednego mapowania dla jednej grupy do jednej roli. Następnie można dodać członków należących do tej grupy.

20. Po utworzeniu grup wybierz grupę i przypisz ją do aplikacji.

    ![Skonfiguruj Dodawanie logowania jednokrotnego](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > Grupy zagnieżdżone nie są obsługiwane podczas przypisywania grup.

21. Aby przypisać rolę do grupy, wybierz rolę, a następnie kliknij przycisk **Przypisz** w dolnej części strony.

    ![Skonfiguruj Dodawanie logowania jednokrotnego](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > Należy pamiętać, że należy odświeżyć sesję w Azure Portal, aby zobaczyć nowe role.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Amazon Web Services (AWS) w panelu dostępu należy uzyskać stronę aplikacji Amazon Web Services (AWS) z opcją, aby wybrać rolę.

![Skonfiguruj Dodawanie logowania jednokrotnego](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_screen.png)

Możesz również zweryfikować odpowiedź SAML, aby zobaczyć role przekazywane jako oświadczenia.

![Skonfiguruj Dodawanie logowania jednokrotnego](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_saml.png)

Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Jak skonfigurować Inicjowanie obsługi przy użyciu interfejsów API programu MS Graph](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-configure-api)
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

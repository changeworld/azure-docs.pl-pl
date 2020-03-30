---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługami Amazon Web Services (AWS) w celu połączenia wielu kont | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure AD a wieloma kontami usług Amazon Web Services (AWS).
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
ms.openlocfilehash: edd54352b1328c95ae2c3e466003b64eaa0fcfde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368004"
---
# <a name="tutorial-azure-active-directory-integration-with-multiple-amazon-web-services-aws-accounts"></a>Samouczek: Integracja usługi Azure Active Directory z wieloma kontami amazon web services (AWS)

W tym samouczku dowiesz się, jak zintegrować usługę Azure Active Directory (Azure AD) z wieloma kontami amazon web services (AWS).

Integracja usług Amazon Web Services (AWS) z usługą Azure AD daje następujące korzyści:

- Możliwość kontrolowania za pomocą usługi Azure AD kto ma dostęp do usług Amazon Web Services (AWS).
- Można włączyć użytkowników do automatycznego logowania do Amazon Web Services (AWS) (logowanie jednokrotne) z ich kont usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej szczegółów na temat integracji aplikacji SaaS z usługą Azure AD, zobacz, [co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

![Usługi Amazon Web Services (AWS) na liście wyników](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

>[!NOTE]
>Należy pamiętać, łącząc jedną aplikację AWS do wszystkich kont AWS nie jest naszym zalecanym podejściem. Zamiast tego zaleca się użycie [tej](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial) metody do skonfigurowania wielu wystąpień konta AWS do wielu wystąpień aplikacji AWS w usłudze Azure AD.

**Należy pamiętać, że nie zaleca się stosowania tego podejścia z następujących powodów:**

* Musisz użyć podejścia Microsoft Graph Explorer, aby załatać wszystkie role do aplikacji. Nie zaleca się przy użyciu podejścia pliku manifestu.

* Widzieliśmy klientów zgłaszających, że po dodaniu ~1200 ról aplikacji dla jednej aplikacji AWS, każda operacja w aplikacji zaczęła rzucać błędy związane z rozmiarem. Istnieje twardy limit rozmiaru na obiekcie aplikacji.

* Musisz ręcznie zaktualizować rolę, jak role są dodawane w dowolnym z kont, który jest podejściem Zamień i nie dołącz niestety. Również jeśli twoje konta rosną, staje się to n x n relacji z kontami i rolami.

* Wszystkie konta AWS będą używać tego samego pliku XML metadanych federacji, a w momencie rolowania certyfikatu musisz prowadzić to ogromne ćwiczenie, aby zaktualizować certyfikat na wszystkich kontach AWS w tym samym czasie

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługami Amazon Web Services (AWS), są potrzebne następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja usług Amazon Web Services (AWS) z włączonym logowaniem jednokrotnym

> [!NOTE]
> Nie zalecamy używania środowiska produkcyjnego do testowania czynności opisanych w tym samouczku.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska usługi Azure AD w wersji próbnej, możesz skorzystać z [miesięcznej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługi Amazon Web Services (AWS) obsługują logowanie jednokrotne inicjowane przez **dostawcę usługi i dostawcę tożsamości**

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Dodawanie usług Amazon Web Services (AWS) z galerii

Aby skonfigurować integrację usług Amazon Web Services (AWS) z usługą Azure AD, należy dodać usługi Amazon Web Services (AWS) z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać usługi Amazon Web Services (AWS) z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Amazon Web Services (AWS)**, wybierz pozycję **Amazon Web Services (AWS)** na panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Usługi Amazon Web Services (AWS) na liście wyników](common/search-new-app.png)

5. Po dodaniu aplikacji przejdź do strony **Właściwości** i skopiuj **identyfikator obiektu**.

    ![Usługi Amazon Web Services (AWS) na liście wyników](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_properties.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego za pomocą Amazon Web Services (AWS) na podstawie użytkownika testowego o nazwie "Britta Simon".

Aby usługa Azure AD działała z logiem pojedynczym, musi wiedzieć, czym jest użytkownik w usługach Amazon Web Services (AWS) dla użytkownika usługi Azure AD. Innymi słowy należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w usługach Amazon Web Services (AWS).

W Amazon Web Services (AWS) przypisz wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **nazwy użytkownika** w celu ustanowienia relacji łącza.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD w usługach Amazon Web Services (AWS), należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w usługach Amazon Web Services (AWS)](#configure-amazon-web-services-aws-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure portal i konfigurujesz logowanie jednokrotne w aplikacji Amazon Web Services (AWS).

**Aby skonfigurować logowanie jednokrotne usługi Azure AD w usługach Amazon Web Services (AWS), wykonaj następujące kroki:**

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji usług **Amazon Web Services (AWS)** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** użytkownik nie musi wykonywać żadnych kroków, ponieważ aplikacja jest już wstępnie zintegrowana z platformą Azure.

    ![image](common/preintegrated.png)

5. Aplikacja usług Amazon Web Services (AWS) oczekuje asercji SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty i oświadczenia użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą narzędzia SAML** kliknij przycisk **Edytuj,** aby otworzyć okno dialogowe **Atrybuty & oświadczenia użytkownika.**

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

    d. W polu **tekstowym Obszar nazw** wpisz wartość obszaru nazw wyświetlaną dla tego wiersza.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij przycisk **Zapisz**.

7. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** kliknij pozycję **Pobierz,** aby pobrać **kod XML metadanych federacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

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

    b. W polach tekstowych **Nazwa dostawcy** wpisz nazwę dostawcy (na przykład: *WAAD*).

    d. Aby przekazać **plik metadanych** pobrany z witryny Azure Portal, kliknij pozycję **Choose File** (Wybierz plik).

    d. Kliknij pozycję **Next Step** (Następny krok).

6. Na stronie okna dialogowego **Verify Provider Information** (Weryfikowanie informacji o dostawcy) kliknij pozycję **Create** (Utwórz).

    ![Konfigurowanie logowania jednokrotnego — weryfikowanie][15]

7. Kliknij pozycję **Roles** (Role), a następnie **Create role** (Utwórz rolę).

    ![Konfigurowanie logowania jednokrotnego — role][16]

8. Na stronie **Create role** (Tworzenie roli) wykonaj następujące kroki:  

    ![Konfigurowanie logowania jednokrotnego — zaufanie][19]

    a. Wybierz pozycję **SAML 2.0 federation** (Federacja SAML 2.0) w obszarze **Select type of trusted entity** (Wybierz typ zaufanej jednostki).

    b. W **obszarze Wybierz sekcję Dostawca SAML 2.0**wybierz wcześniej utworzonego **dostawcę SAML** (na przykład: *WAAD*)

    d. Wybierz pozycję **Allow programmatic and AWS Management Console access** (Zezwalaj na dostęp programowy i za pomocą konsoli AWS Management Console).
  
    d. Kliknij **przycisk Dalej: Uprawnienia**.

9. W oknie dialogowym **Attach Permissions Policies** (Dołączanie zasad uprawnień) dołącz zasady odpowiednie dla Twojej organizacji. Kliknij **przycisk Dalej: Recenzja**.  

    ![Konfigurowanie logowania jednokrotnego — zasady][33]

10. W oknie dialogowym **Review** (Przegląd) wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego — przegląd][34]

    a. W polu tekstowym **Role name** (Nazwa roli) podaj nazwę roli.

    b. W polu tekstowym **Role description** (Opis roli) podaj opis.

    d. Kliknij pozycję **Create Role** (Utwórz rolę).

    d. Utwórz potrzebną liczbę ról i zamapuj je na dostawcę tożsamości.

11. Wyloguj się z bieżącego konta AWS i zaloguj się przy innym koncie, na którym chcesz skonfigurować logowanie jednokrotne za pomocą usługi Azure AD.

12. Wykonaj krok od 2 do kroku 10, aby utworzyć wiele ról, które chcesz skonfigurować dla tego konta. Jeśli masz więcej niż dwa konta, wykonaj te same kroki dla wszystkich kont, aby utworzyć dla nich role.

13. Po utworzeniu wszystkich ról na kontach są one wyświetlane na liście **Role** dla tych kont.

    ![Konfiguracja ról](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_listofroles.png)

14. Musimy przechwycić wszystkie arn roli i zaufanych jednostek dla wszystkich ról na wszystkich kontach, które musimy mapować ręcznie za pomocą aplikacji usługi Azure AD.

15. Kliknij role, aby skopiować wartości **ARN** roli i **zaufanych jednostek.** Te wartości są potrzebne dla wszystkich ról, które należy utworzyć w usłudze Azure AD.

    ![Konfiguracja ról](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_role_summary.png)

16. Wykonaj powyższy krok dla wszystkich ról na wszystkich kontach i przechowuj wszystkie z nich w formacie **Role ARN,Zaufane jednostki** w notatniku.

17. Otwórz [Eksploratora wykresów firmy Microsoft](https://developer.microsoft.com/graph/graph-explorer) w innym oknie.

    a. Zaloguj się do witryny Eksploratora wykresów przy użyciu poświadczeń administratora globalnego/współadministratora dla dzierżawy.

    b. Musisz mieć wystarczające uprawnienia do tworzenia ról. Kliknij na **zmodyfikowanie uprawnień,** aby uzyskać wymagane uprawnienia.

    ![Okno dialogowe Eksplorator wykresu](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    d. Wybierz następujące uprawnienia z listy (jeśli jeszcze ich nie masz) i kliknij "Modyfikuj uprawnienia" 

    ![Okno dialogowe Eksplorator wykresu](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Spowoduje to poproszenie o ponowne zalogowanie się i zaakceptowanie zgody. Po zaakceptowaniu zgody użytkownik jest ponownie zalogowany do Eksploratora wykresów.

    e. Zmień wersję rozwijanej na **wersję beta**. Aby pobrać wszystkie jednostki usługi z dzierżawy, należy użyć następującej kwerendy:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Jeśli używasz wielu katalogów, możesz użyć następującego wzorca, który ma swoją domenę podstawową`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Okno dialogowe Eksplorator wykresu](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. Z listy podmiotów usługi pobrane, pobierz ten, który należy zmodyfikować. Można również użyć Ctrl +F do wyszukiwania aplikacji ze wszystkich wymienionych ServicePrincipals. Następujące zapytanie można użyć przy użyciu **identyfikatora obiektu,** który został skopiowany z usługi Azure AD Właściwości strony, aby uzyskać do odpowiedniego jednostki usługi.

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Okno dialogowe Eksplorator wykresu](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. Wyodrębnij właściwość appRoles z obiektu jednostki usługi.

    ![Okno dialogowe Eksplorator wykresu](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. Teraz należy wygenerować nowe role dla aplikacji. 

    i. Poniżej JSON jest przykład appRoles obiektu. Utwórz podobny obiekt, aby dodać role, które chcesz dla aplikacji.

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
    > Nowe role można dodawać tylko po **msiam_access** dla operacji poprawki. Ponadto można dodać dowolną liczbę ról, ile chcesz, aby według potrzeb organizacji. Usługa Azure AD wyśle **wartość** tych ról jako wartość oświadczenia w odpowiedzi SAML.

    j. Wróć do Eksploratora grafu firmy Microsoft i zmień metodę z **GET** na **PATCH**. Popraw obiekt service principal, aby mieć żądane role, aktualizując właściwość appRoles podobną do pokazanej powyżej w przykładzie. Kliknij **przycisk Uruchom kwerendę,** aby wykonać operację poprawki. Komunikat o sukcesie potwierdza utworzenie roli aplikacji Amazon Web Services.

    ![Okno dialogowe Eksploratora programu Microsoft Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

18. Po załataniu jednostki usługi z większą liczą się ról, można przypisać użytkowników/grup do odpowiednich ról. Można to zrobić, przechodząc do portalu i przechodząc do aplikacji Amazon Web Services. Kliknij kartę **Użytkownicy i grupy** u góry.

19. Zalecamy utworzenie nowych grup dla każdej roli usługi AWS, aby można było przypisać tę konkretną rolę w tej grupie. Należy zauważyć, że jest to od jednego do jednego mapowania dla jednej grupy do jednej roli. Następnie można dodać członków, którzy należą do tej grupy.

20. Po utworzeniu grup wybierz grupę i przypisz ją do aplikacji.

    ![Konfigurowanie dodawania logowania jednokrotnego](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > Grupy zagnieżdżone nie są obsługiwane podczas przypisywania grup.

21. Aby przypisać rolę do grupy, wybierz rolę i kliknij przycisk **Przypisz** u dołu strony.

    ![Konfigurowanie dodawania logowania jednokrotnego](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > Należy pamiętać, że należy odświeżyć sesję w witrynie Azure portal, aby wyświetlić nowe role.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Amazon Web Services (AWS) w Panelu dostępu należy uzyskać stronę aplikacji Amazon Web Services (AWS) z opcją wyboru roli.

![Konfigurowanie dodawania logowania jednokrotnego](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_screen.png)

Można również sprawdzić odpowiedź SAML, aby wyświetlić role przekazywane jako oświadczenia.

![Konfigurowanie dodawania logowania jednokrotnego](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_saml.png)

Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Jak skonfigurować inicjowanie obsługi administracyjnej przy użyciu interfejsów API programu Microsoft Graph](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-configure-api)
* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

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

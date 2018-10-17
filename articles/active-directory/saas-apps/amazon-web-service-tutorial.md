---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą usługi Amazon Web Services (AWS) | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeedes
ms.openlocfilehash: 8e91fbf0befaef9088e9afaa6e69c0cb29ad4858
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49363763"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą usługi Amazon Web Services (AWS)

W tym samouczku dowiesz się, jak zintegrować Amazon Web Services (AWS) w usłudze Azure Active Directory (Azure AD).

Integrowanie usług Amazon Web Services (AWS) z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do usługi Amazon Web Services (AWS).
- Użytkowników, aby automatycznie uzyskać zalogowanych do Amazon Web Services (AWS) (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

![usługi Amazon Web Services (AWS)](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Można skonfigurować wiele identyfikatorów dla wielu wystąpień, tak jak pokazano poniżej. 

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Te wartości usługi Azure AD spowoduje usunięcie wartości **#** i wysłać poprawną wartość `https://signin.aws.amazon.com/saml` jako adres URL odbiorców w tokenu SAML.

**Firma Microsoft zaleca, aby użyć tego podejścia z następujących powodów:**

a. Każda aplikacja zapewnia możesz X509 unikatowy certyfikat, a więc każde wystąpienie może mieć datę wygaśnięcia innego certyfikatu i zarządzasz to na podstawie indywidualnych kont platformy AWS. Ogólna Przerzucanie certyfikatów będzie łatwe w tym przypadku.

b. Umożliwia inicjowanie obsługi użytkowników za pomocą usług AWS aplikacji w usłudze Azure AD, a następnie usługa powoduje pobranie wszystkich ról z tego konta usługi AWS. Nie trzeba ręcznie dodać lub zaktualizować ról usługi AWS dla aplikacji.

c. Możesz przypisać właściciela aplikacji dla aplikacji, który można zarządzać aplikacją bezpośrednio w usłudze Azure AD.

> [!Note]
> Upewnij się, że używasz tylko aplikacji z galerii

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą usługi Amazon Web Services (AWS), potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Amazon Web Services (AWS) logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

> [!Note]
> Jeśli chcesz integracja wielu kont platformy AWS do jednego konta platformy Azure do rejestracji jednokrotnej, zob. [to](https://docs.microsoft.com/azure/active-directory/active-directory-saas-aws-multi-accounts-tutorial) artykułu.

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie usług Amazon Web Services (AWS) z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Dodawanie usług Amazon Web Services (AWS) z galerii
Aby skonfigurować integrację z usługi Amazon Web Services (AWS) do usługi Azure AD, należy dodać Amazon Web Services (AWS) z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Amazon Web Services (AWS) z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![image](./media/amazon-web-service-tutorial/selectazuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![image](./media/amazon-web-service-tutorial/a_select_app.png)
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![image](./media/amazon-web-service-tutorial/a_new_app.png)

4. W polu wyszukiwania wpisz **Amazon Web Services (AWS)**, wybierz opcję **Amazon Web Services (AWS)** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowania, w usłudze Azure AD logowanie jednokrotne, za pomocą Amazon Web Services (AWS) na podstawie użytkownika testowego, o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Amazon Web Services (AWS) do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w Amazon Web Services (AWS) musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą usługi Amazon Web Services (AWS), należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Amazon Web Services (AWS)](#create-an-amazon-web-services-aws-test-user)**  — aby odpowiednikiem Britta Simon w Amazon Web Services (AWS) połączoną usługę Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji usług Amazon Web Services (AWS).

**Aby skonfigurować usługi Azure AD logowanie jednokrotne za pomocą usługi Amazon Web Services (AWS), wykonaj następujące czynności:**

1. W [witryny Azure portal](https://portal.azure.com/)na **Amazon Web Services (AWS)** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![image](./media/amazon-web-service-tutorial/B1_B2_Select_SSO.png)

2. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, wybierz **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![image](./media/amazon-web-service-tutorial/b1_b2_saml_sso.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** przycisk, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![image](./media/amazon-web-service-tutorial/b1-domains_and_urlsedit.png)

4. Na **podstawową konfigurację protokołu SAML** sekcji użytkownik nie musiał wykonać każdy krok, ponieważ aplikacja już jest wstępnie zintegrowana z platformą Azure.

    ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

5. Podczas konfigurowania więcej niż jedno wystąpienie, podaj wartość identyfikatora. Z drugiej instancji lub nowszy Podaj wartość identyfikatora w następującym formacie. Użyj **#** Zaloguj się określić unikatową wartość SPN. 

    `https://signin.aws.amazon.com/saml#2`

    ![Domena usługi Amazon Web Services (AWS) i adresy URL pojedynczego logowania jednokrotnego informacji](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_identifier.png)

6. Aplikacja usługi Amazon Web Services (AWS) oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Możesz zarządzać wartości te atrybuty z **atrybutów użytkowników i oświadczeń** sekcji na stronie integracji aplikacji. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** przycisk, aby otworzyć **atrybutów użytkowników i oświadczeń** okna dialogowego.

    ![image](./media/amazon-web-service-tutorial/i4-attribute.png)

7. W **oświadczenia użytkownika** sekcji na **atrybutów użytkowników i oświadczeń** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:
    
    | Name (Nazwa)  | Atrybut źródłowy  | Przestrzeń nazw |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Rola            | User.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | "Podaj wartość z zakresu od 900 sekund (15 minut) na 43200 sekund (12 godzin)" |  https://aws.amazon.com/SAML/Attributes |

    a. Kliknij przycisk **Dodaj nowe oświadczenie** otworzyć **Zarządzanie oświadczenia użytkownika** okna dialogowego.

    ![image](./media/amazon-web-service-tutorial/i2-attribute.png)

    ![image](./media/amazon-web-service-tutorial/i3-attribute.png)

    b. W **nazwa** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.

    c. Wprowadź **Namespace** wartość.

    d. Wybierz źródło jako **atrybutu**.

    e. Z **atrybut źródłowy** wpisz wartość atrybutu wyświetlanego dla tego wiersza.

    f. Kliknij pozycję **Zapisz**.

8. Na **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML** strony w **certyfikat podpisywania SAML** kliknij **Pobierz** można pobrać **XML metadanych Federacji**  i zapisz go na komputerze.

    ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

9. W oknie innej przeglądarki Zaloguj się do witryny firmy Amazon Web Services (AWS) jako administrator.

10. Kliknij przycisk **strona główna usług AWS**.

    ![Konfigurowanie logowania jednokrotnego głównej][11]

11. Kliknij przycisk **Zarządzanie tożsamościami i dostępem**.

    ![Konfigurowanie tożsamości rejestracji jednokrotnej][12]

12. Kliknij przycisk **dostawców tożsamości**, a następnie kliknij przycisk **Tworzenie dostawcy**.

    ![Konfigurowanie dostawcy rejestracji jednokrotnej][13]

13. Na **Konfigurowanie dostawcy** okna dialogowego strony, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego okna dialogowego][14]

    a. Jako **typ dostawcy**, wybierz opcję **SAML**.

    b. W **Nazwa dostawcy** polu tekstowym wpisz nazwę dostawcy (na przykład: *WAAD*).

    c. Można przekazać swoje pobrany **plik metadanych** z witryny Azure portal, kliknij przycisk **wybierz plik**.

    d. Kliknij przycisk **następny krok**.

14. Na **Sprawdź informacje o dostawcy** strony okna dialogowego kliknij **Utwórz**.

    ![Konfigurowanie logowania jednokrotnego Sprawdź][15]

15. Kliknij przycisk **role**, a następnie kliknij przycisk **tworzenia ról**.

    ![Konfigurowanie ról rejestracji jednokrotnej][16]

16. Na **tworzenia ról** strony, wykonaj następujące czynności:  

    ![Skonfigurować zaufanie rejestracji jednokrotnej][19]

    a. Wybierz **federation SAML 2.0** w obszarze **wybierz typ obiektu zaufanego**.

    b. W obszarze **wybierz sekcję SAML 2.0 Provider**, wybierz opcję **SAML dostawcy** utworzonego wcześniej (na przykład: *WAAD*)

    c. Wybierz **dozwolonych programowy i dostęp do konsoli zarządzania usług AWS**.
  
    d. Kliknij przycisk **dalej: uprawnienia**.

17. Na **Dołącz zasady uprawnień** okno dialogowe, nie trzeba dołączać żadnych zasad. Kliknij przycisk **dalej: Przejrzyj**.  

    ![Konfigurowanie zasad rejestracji jednokrotnej][33]

18. Na **przeglądu** okno dialogowe, należy wykonać następujące czynności:

    ![Konfigurowanie przeglądu rejestracji jednokrotnej][34]

    a. W **nazwy roli** polu tekstowym wprowadź nazwę roli.

    b. W **opis roli** polu tekstowym wprowadź opis.

    c. Kliknij przycisk **utworzyć rolę**.

    d. Utwórz tyle ról stosownie do potrzeb i mapować je do dostawcy tożsamości.

19. Użyj poświadczeń konta usługi AWS do pobierania ról z konta usługi AWS w aprowizacji użytkownika usługi Azure AD. W tym celu otwórz konsolę usług AWS macierzystego.

20. Kliknij pozycję **usług** -> **Security, Identity & Compliance** -> **IAM**.

    ![Pobieranie ról z konta usług AWS](./media/amazon-web-service-tutorial/fetchingrole1.png)

21. Wybierz **zasady** karty w sekcji zarządzania tożsamościami i Dostępem.

    ![Pobieranie ról z konta usług AWS](./media/amazon-web-service-tutorial/fetchingrole2.png)

22. Utwórz nowe zasady, klikając **Tworzenie zasad** pobierania ról z konta usługi AWS w aprowizacji użytkownika usługi Azure AD.

    ![Tworzenie nowych zasad](./media/amazon-web-service-tutorial/fetchingrole3.png)

23. Utwórz własne zasady można pobrać wszystkie role z kont platformy AWS, wykonując następujące czynności:

    ![Tworzenie nowych zasad](./media/amazon-web-service-tutorial/policy1.png)

    a. W **"Utwórz zasady"** kliknij sekcję **"JSON"** kartę.

    b. W dokumencie zasady, należy dodać poniżej JSON.

    ```

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

    c. Kliknij pozycję **przycisk zasady przeglądu** można zweryfikować zasad.

    ![Definiowanie nowych zasad](./media/amazon-web-service-tutorial/policy5.png)

24. Zdefiniuj **nowe zasady** , wykonując następujące czynności:

    ![Definiowanie nowych zasad](./media/amazon-web-service-tutorial/policy2.png)

    a. Podaj **Nazwa_zasad** jako **AzureAD_SSOUserRole_Policy**.

    b. Możesz podać **opis** zasad jako **umożliwi te zasady można pobrać ról z kont platformy AWS**.

    c. Kliknij pozycję **"Utwórz zasady"** przycisku.

25. Utwórz nowe konto użytkownika w usłudze zarządzania tożsamościami i Dostępem usług AWS, wykonując następujące czynności:

    a. Kliknij pozycję **użytkowników** nawigacji w konsoli usług AWS zarządzania tożsamościami i Dostępem.

    ![Definiowanie nowych zasad](./media/amazon-web-service-tutorial/policy3.png)

    b. Kliknij pozycję **Dodaj użytkownika** przycisk, aby utworzyć nowego użytkownika.

    ![Dodawanie użytkownika](./media/amazon-web-service-tutorial/policy4.png)

    c. W **Dodaj użytkownika** sekcji, wykonaj następujące czynności:

    ![Dodawanie użytkownika](./media/amazon-web-service-tutorial/adduser1.png)

    * Wprowadź nazwę użytkownika jako **AzureADRoleManager**.

    * Wybierz typ dostępu **dostęp programowy** opcji. W ten sposób użytkownik wywoływać interfejsy API i pobrać ról z konta usługi AWS.

    * Kliknij pozycję **dalej uprawnienia** przycisk w prawym dolnym rogu.

26. Teraz należy utworzyć nowe zasady dla tego użytkownika, wykonując następujące czynności:

    ![Dodawanie użytkownika](./media/amazon-web-service-tutorial/adduser2.png)

    a. Kliknij pozycję **Dołącz istniejące zasady bezpośrednio** przycisku.

    b. Wyszukaj zasady nowo utworzonego w sekcji filtru **AzureAD_SSOUserRole_Policy**.

    c. Wybierz **zasad** a następnie kliknij polecenie **dalej: Przejrzyj** przycisku.

27. Przejrzyj zasady dołączonych użytkowników, wykonując poniższe czynności:

    ![Dodawanie użytkownika](./media/amazon-web-service-tutorial/adduser3.png)

    a. Sprawdź nazwę użytkownika, typ dostępu i zasad zamapowany na użytkownika.

    b. Kliknij pozycję **Utwórz użytkownika** przycisk w prawym dolnym rogu, aby utworzyć użytkownika.

28. Pobieranie poświadczeń użytkownika, wykonując poniższe czynności:

    ![Dodawanie użytkownika](./media/amazon-web-service-tutorial/adduser4.png)

    a. Skopiuj użytkownika **dostępu Identyfikatora klucza** i **wpis tajny dostępu**.

    b. Wprowadź te poświadczenia do użytkownika usługi Azure AD, inicjowanie obsługi administracyjnej sekcji można pobrać ról z konsoli usług AWS.

    c. Kliknij pozycję **Zamknij** znajdujący się u dołu.

29. Przejdź do **aprowizacji użytkowników** części aplikacji usług Amazon Web Services w portalu zarządzania usługi Azure AD.

    ![Dodawanie użytkownika](./media/amazon-web-service-tutorial/provisioning.png)

30. Wprowadź **klucz dostępu** i **klucz tajny** w **klucz tajny klienta** i **klucz tajny tokenu** odpowiednio pola.

    ![Dodawanie użytkownika](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Wprowadź klucz dostępu do użytkownika usług AWS w **clientsecret** pola.

    b. Wprowadź hasło użytkownika usług AWS w **klucz tajny tokenu** pola.

    c. Kliknij pozycję **Testuj połączenie** przycisk, a powinien możesz pomyślnie przetestować tego połączenia.

    d. Zapisz ustawienia, klikając **Zapisz** znajdujący się u góry.

31. Teraz upewnij się, Włączyłeś stan inicjowania obsługi administracyjnej **na** w sekcji Ustawienia przez podjęcia w zakresie przełącznika, a następnie klikając **Zapisz** znajdujący się u góry.

    ![Dodawanie użytkownika](./media/amazon-web-service-tutorial/provisioning2.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.

    ![image](./media/amazon-web-service-tutorial/d_users_and_groups.png)

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![image](./media/amazon-web-service-tutorial/d_adduser.png)

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![image](./media/amazon-web-service-tutorial/d_userproperties.png)

    a. W **nazwa** pola wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-an-amazon-web-services-aws-test-user"></a>Tworzenie użytkownika testowego Amazon Web Services (AWS)

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w Amazon Web Services (AWS). Amazon Web Services (AWS) nie wymaga użytkownika do utworzenia w ich systemie dla logowania jednokrotnego, dzięki czemu nie trzeba wykonywać żadnych czynności w tym miejscu.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon korzystać z platformy Azure logowania jednokrotnego przez udzielenie dostępu do usługi Amazon Web Services (AWS).

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![image](./media/amazon-web-service-tutorial/d_all_applications.png)

2. Na liście aplikacji wybierz **Amazon Web Services (AWS)**.

    ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_app.png)

3. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![image](./media/amazon-web-service-tutorial/d_leftpaneusers.png)

4. Wybierz **Dodaj** przycisk, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![image](./media/amazon-web-service-tutorial/d_assign_user.png)

5. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

    ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_users.png)

6. W **wybierz rolę** okno dialogowe Wybieranie odpowiedniej roli użytkownika na liście, a następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

    ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_roles.png)

    >[!NOTE]
    >Po włączeniu aprowizacji przy użyciu aplikacji użytkowników, należy czekać na 30 minut na pobranie wszystkich ról z usługi Amazon Web Services (AWS), należy odświeżyć stronę, a następnie podczas przypisywania aplikacji do użytkowników i grup, zobacz ról do użycia r.

7. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.

    ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_assign.png)
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi Amazon Web Services (AWS), w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do aplikacji usług Amazon Web Services (AWS).
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

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

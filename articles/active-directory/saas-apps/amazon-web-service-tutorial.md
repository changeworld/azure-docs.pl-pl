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
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.openlocfilehash: 797be143284566efcefce5ed6c7ded822d5aa97f
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39438901"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą usługi Amazon Web Services (AWS)

W tym samouczku dowiesz się, jak zintegrować Amazon Web Services (AWS) w usłudze Azure Active Directory (Azure AD).

Integrowanie usług Amazon Web Services (AWS) z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do usługi Amazon Web Services (AWS).
- Użytkowników, aby automatycznie uzyskać zalogowanych do Amazon Web Services (AWS) (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą usługi Amazon Web Services (AWS), potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Amazon Web Services (AWS) logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie usług Amazon Web Services (AWS) z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Dodawanie usług Amazon Web Services (AWS) z galerii
Aby skonfigurować integrację z usługi Amazon Web Services (AWS) do usługi Azure AD, należy dodać Amazon Web Services (AWS) z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Amazon Web Services (AWS) z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **Amazon Web Services (AWS)**, wybierz opcję **Amazon Web Services (AWS)** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Amazon Web Services (AWS) na liście wyników](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowania, w usłudze Azure AD logowanie jednokrotne, za pomocą Amazon Web Services (AWS) na podstawie użytkownika testowego, o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Amazon Web Services (AWS) do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w Amazon Web Services (AWS) musi zostać ustanowione.

W Amazon Web Services (AWS), przypisz wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą usługi Amazon Web Services (AWS), należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Amazon Web Services (AWS)](#create-an-amazon-web-services-aws-test-user)**  — aby odpowiednikiem Britta Simon w Amazon Web Services (AWS) połączoną usługę Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji usług Amazon Web Services (AWS).

**Aby skonfigurować usługi Azure AD logowanie jednokrotne za pomocą usługi Amazon Web Services (AWS), wykonaj następujące czynności:**

1. W witrynie Azure portal na **Amazon Web Services (AWS)** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

1. Na **Amazon Web Services (AWS) domena i adresy URL** sekcji, użytkownik nie ma do wykonywania żadnych czynności, jak aplikacja już jest wstępnie zintegrowana z platformą Azure.

    ![Domena usługi Amazon Web Services (AWS) i adresy URL pojedynczego logowania jednokrotnego informacji](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

1. Aplikacja oprogramowania usług Amazon Web Services (AWS) oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Możesz zarządzać wartości te atrybuty z "**atrybutów użytkownika**" sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego.

    ![Konfigurowanie logowania jednokrotnego attb](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_attribute.png) 

1. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:
    
    | Nazwa atrybutu  | Wartość atrybutu | Przestrzeń nazw |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Rola            | User.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >Należy skonfigurować aprowizację użytkowników w usłudze Azure AD, aby pobrać wszystkie role z konsoli usług AWS. Zobacz poniższe kroki inicjowania obsługi administracyjnej.

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego Dodaj](./media/amazon-web-service-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie logowania jednokrotnego addattb](./media/amazon-web-service-tutorial/tutorial_attribute_05.png)

    b. W **nazwa** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.

    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.

    d. W **Namespace** polu tekstowym wpisz wartość przestrzeni nazw wyświetlanego dla tego wiersza.
    
    d. Kliknij przycisk **OK**.

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/amazon-web-service-tutorial/tutorial_general_400.png)

1. W oknie innej przeglądarki Zaloguj się do witryny firmy Amazon Web Services (AWS) jako administrator.

1. Kliknij przycisk **strona główna usług AWS**.
   
    ![Konfigurowanie logowania jednokrotnego głównej][11]

1. Kliknij przycisk **Zarządzanie tożsamościami i dostępem**. 
   
    ![Konfigurowanie tożsamości rejestracji jednokrotnej][12]

1. Kliknij przycisk **dostawców tożsamości**, a następnie kliknij przycisk **Tworzenie dostawcy**. 
   
    ![Konfigurowanie dostawcy rejestracji jednokrotnej][13]

1. Na **Konfigurowanie dostawcy** okna dialogowego strony, wykonaj następujące czynności: 
   
    ![Konfigurowanie logowania jednokrotnego okna dialogowego][14]
 
    a. Jako **typ dostawcy**, wybierz opcję **SAML**.

    b. W **Nazwa dostawcy** polu tekstowym wpisz nazwę dostawcy (na przykład: *WAAD*).

    c. Można przekazać swoje pobrany **plik metadanych** z witryny Azure portal, kliknij przycisk **wybierz plik**.

    d. Kliknij przycisk **następny krok**.

1. Na **Sprawdź informacje o dostawcy** strony okna dialogowego kliknij **Utwórz**. 
    
    ![Konfigurowanie logowania jednokrotnego Sprawdź][15]

1. Kliknij przycisk **role**, a następnie kliknij przycisk **tworzenia ról**. 
    
    ![Konfigurowanie ról rejestracji jednokrotnej][16]

1. Na **tworzenia ról** strony, wykonaj następujące czynności:  
    
    ![Skonfigurować zaufanie rejestracji jednokrotnej][19] 

    a. Wybierz **federation SAML 2.0** w obszarze **wybierz typ obiektu zaufanego**.

    b. W obszarze **wybierz sekcję SAML 2.0 Provider**, wybierz opcję **SAML dostawcy** utworzonego wcześniej (na przykład: *WAAD*)

    c. Wybierz **dozwolonych programowy i dostęp do konsoli zarządzania usług AWS**.
  
    d. Kliknij przycisk **dalej: uprawnienia**.

1. Na **Dołącz zasady uprawnień** okno dialogowe, nie trzeba dołączać żadnych zasad. Kliknij przycisk **dalej: Przejrzyj**.  
    
    ![Konfigurowanie zasad rejestracji jednokrotnej][33]

1. Na **przeglądu** okno dialogowe, należy wykonać następujące czynności:   
    
    ![Konfigurowanie przeglądu rejestracji jednokrotnej][34] 

    a. W **nazwy roli** polu tekstowym wprowadź nazwę roli.

    b. W **opis roli** polu tekstowym wprowadź opis.

    c. Kliknij przycisk **utworzyć rolę**.

    d. Utwórz tyle ról stosownie do potrzeb i mapować je do dostawcy tożsamości.

1. Użyj poświadczeń konta usługi AWS do pobierania ról z konta usługi AWS w aprowizacji użytkownika usługi Azure AD. W tym celu otwórz konsolę usług AWS macierzystego.

1. Kliknij pozycję **usług** -> **Security, Identity & Compliance** -> **IAM**.

    ![Pobieranie ról z konta usług AWS](./media/amazon-web-service-tutorial/fetchingrole1.png)

1. Wybierz **zasady** karty w sekcji zarządzania tożsamościami i Dostępem.

    ![Pobieranie ról z konta usług AWS](./media/amazon-web-service-tutorial/fetchingrole2.png)

1. Utwórz nowe zasady, klikając **Tworzenie zasad** pobierania ról z konta usługi AWS w aprowizacji użytkownika usługi Azure AD.

    ![Tworzenie nowych zasad](./media/amazon-web-service-tutorial/fetchingrole3.png)

1. Utwórz własne zasady można pobrać wszystkie role z kont platformy AWS, wykonując następujące czynności:

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

1. Zdefiniuj **nowe zasady** , wykonując następujące czynności:

    ![Definiowanie nowych zasad](./media/amazon-web-service-tutorial/policy2.png)

    a. Podaj **Nazwa_zasad** jako **AzureAD_SSOUserRole_Policy**.

    b. Możesz podać **opis** zasad jako **umożliwi te zasady można pobrać ról z kont platformy AWS**.
    
    c. Kliknij pozycję **"Utwórz zasady"** przycisku.

1.  Utwórz nowe konto użytkownika w usłudze zarządzania tożsamościami i Dostępem usług AWS, wykonując następujące czynności:

    a. Kliknij pozycję **użytkowników** nawigacji w konsoli usług AWS zarządzania tożsamościami i Dostępem.

    ![Definiowanie nowych zasad](./media/amazon-web-service-tutorial/policy3.png)
    
    b. Kliknij pozycję **Dodaj użytkownika** przycisk, aby utworzyć nowego użytkownika.

    ![Dodawanie użytkownika](./media/amazon-web-service-tutorial/policy4.png)

    c. W **Dodaj użytkownika** sekcji, wykonaj następujące czynności:
    
    ![Dodawanie użytkownika](./media/amazon-web-service-tutorial/adduser1.png)
    
    * Wprowadź nazwę użytkownika jako **AzureADRoleManager**.
    
    * Wybierz typ dostępu **dostęp programowy** opcji. W ten sposób użytkownik wywoływać interfejsy API i pobrać ról z konta usługi AWS.
    
    * Kliknij pozycję **dalej uprawnienia** przycisk w prawym dolnym rogu.

1. Teraz należy utworzyć nowe zasady dla tego użytkownika, wykonując następujące czynności:

    ![Dodawanie użytkownika](./media/amazon-web-service-tutorial/adduser2.png)
    
    a. Kliknij pozycję **Dołącz istniejące zasady bezpośrednio** przycisku.

    b. Wyszukaj zasady nowo utworzonego w sekcji filtru **AzureAD_SSOUserRole_Policy**.
    
    c. Wybierz **zasad** a następnie kliknij polecenie **dalej: Przejrzyj** przycisku.

1.  Przejrzyj zasady dołączonych użytkowników, wykonując poniższe czynności:

    ![Dodawanie użytkownika](./media/amazon-web-service-tutorial/adduser3.png)
    
    a. Sprawdź nazwę użytkownika, typ dostępu i zasad zamapowany na użytkownika.
    
    b. Kliknij pozycję **Utwórz użytkownika** przycisk w prawym dolnym rogu, aby utworzyć użytkownika.

1. Pobieranie poświadczeń użytkownika, wykonując poniższe czynności:

    ![Dodawanie użytkownika](./media/amazon-web-service-tutorial/adduser4.png)
    
    a. Skopiuj użytkownika **dostępu Identyfikatora klucza** i **wpis tajny dostępu**.
    
    b. Wprowadź te poświadczenia do użytkownika usługi Azure AD, inicjowanie obsługi administracyjnej sekcji można pobrać ról z konsoli usług AWS.
    
    c. Kliknij pozycję **Zamknij** znajdujący się u dołu.

1. Przejdź do **aprowizacji użytkowników** części aplikacji usług Amazon Web Services w portalu zarządzania usługi Azure AD.

    ![Dodawanie użytkownika](./media/amazon-web-service-tutorial/provisioning.png)

1. Wprowadź **klucz dostępu** i **klucz tajny** w **klucz tajny klienta** i **klucz tajny tokenu** odpowiednio pola.

    ![Dodawanie użytkownika](./media/amazon-web-service-tutorial/provisioning1.png)
    
    a. Wprowadź klucz dostępu do użytkownika usług AWS w **clientsecret** pola.
    
    b. Wprowadź hasło użytkownika usług AWS w **klucz tajny tokenu** pola.
    
    c. Kliknij pozycję **Testuj połączenie** przycisk, a powinien możesz pomyślnie przetestować tego połączenia.

    d. Zapisz ustawienia, klikając **Zapisz** znajdujący się u góry.
 
1. Teraz upewnij się, Włączyłeś stan inicjowania obsługi administracyjnej **na** w sekcji Ustawienia przez podjęcia w zakresie przełącznika, a następnie klikając **Zapisz** znajdujący się u góry.

    ![Dodawanie użytkownika](./media/amazon-web-service-tutorial/provisioning2.png)

> [!Note]
> Jeśli chcesz integracja wielu kont platformy AWS do jednego konta platformy Azure do rejestracji jednokrotnej, zob. [to](https://docs.microsoft.com/azure/active-directory/active-directory-saas-aws-multi-accounts-tutorial) artykułu. 


### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/amazon-web-service-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/amazon-web-service-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/amazon-web-service-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/amazon-web-service-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-an-amazon-web-services-aws-test-user"></a>Tworzenie użytkownika testowego Amazon Web Services (AWS)

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w Amazon Web Services (AWS). Amazon Web Services (AWS) nie wymaga użytkownika do utworzenia w ich systemie dla logowania jednokrotnego, dzięki czemu nie trzeba wykonywać żadnych czynności w tym miejscu.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon korzystać z platformy Azure logowania jednokrotnego przez udzielenie dostępu do usługi Amazon Web Services (AWS).

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon do usługi Amazon Web Services (AWS), wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Amazon Web Services (AWS)**.

    ![Link Amazon Web Services (AWS) na liście aplikacji](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi Amazon Web Services (AWS), w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do aplikacji usług Amazon Web Services (AWS). Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/amazon-web-service-tutorial/tutorial_general_01.png
[2]: ./media/amazon-web-service-tutorial/tutorial_general_02.png
[3]: ./media/amazon-web-service-tutorial/tutorial_general_03.png
[4]: ./media/amazon-web-service-tutorial/tutorial_general_04.png

[100]: ./media/amazon-web-service-tutorial/tutorial_general_100.png

[200]: ./media/amazon-web-service-tutorial/tutorial_general_200.png
[201]: ./media/amazon-web-service-tutorial/tutorial_general_201.png
[202]: ./media/amazon-web-service-tutorial/tutorial_general_202.png
[203]: ./media/amazon-web-service-tutorial/tutorial_general_203.png
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

---
title: 'Samouczek: Integracji Azure Active Directory z usług sieci Web firmy Amazon (AWS) | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i usług sieci Web firmy Amazon (AWS).
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
ms.date: 05/10/2018
ms.author: jeedes
ms.openlocfilehash: 1cf57d76e87fcc0b6628ac194f4d5c389bf684d4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Samouczek: Integracji Azure Active Directory z usług sieci Web firmy Amazon (AWS)

Z tego samouczka dowiesz się integrowanie Amazon Web Services (AWS) z usługi Azure Active Directory (Azure AD).

Integrowanie usługi Amazon Web Services (AWS) z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do usług sieci Web firmy Amazon (AWS).
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Amazon Web Services (AWS) (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Amazon Web Services (AWS), potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Amazon Web Services (AWS) logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Amazon Web Services (AWS) z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Dodawanie Amazon Web Services (AWS) z galerii
Aby skonfigurować integrację z usługami sieci Web Amazon (AWS) do usługi Azure AD, należy dodać Amazon Web Services (AWS) z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Amazon Web Services (AWS) z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Amazon Web Services (AWS)**, wybierz pozycję **Amazon Web Services (AWS)** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Amazon Web Services (AWS) na liście wyników](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Amazon Web usługi (AWS) w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Amazon Web Services (AWS) jest dla użytkownika, w usłudze Azure AD. Innymi słowy musi można ustanowić łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Amazon Web Services (AWS).

W Amazon Web Services (usług AWS), przypisz wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Amazon Web Services (AWS), należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Amazon Web Services (AWS)](#create-an-amazon-web-services-aws-test-user)**  — aby odpowiednikiem Simona Britta w Amazon usług sieci Web (AWS) połączonej z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji usługi sieci Web firmy Amazon (AWS).

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Amazon Web Services (AWS), wykonaj następujące czynności:**

1. W portalu Azure na **Amazon Web Services (AWS)** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

3. Na **Amazon Web Services (AWS) domeny i adres URL** sekcji, użytkownik nie trzeba wykonywać żadnych czynności, jak aplikacja już jest wstępna Integracja z usługą Azure.

    ![Domena Amazon Web Services (AWS) i adresy URL pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

4. Aplikacji Amazon Web Services (AWS) oczekuje potwierdzenia języka SAML w określonym formacie. Skonfiguruj następujące oświadczeń dla tej aplikacji. Możesz zarządzać wartości tych atrybutów z "**atrybuty użytkownika**" sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego.

    ![Konfigurowanie rejestracji jednokrotnej attb](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_attribute.png)   

5. W **atrybuty użytkownika** sekcji na **logowanie jednokrotne** okna dialogowego, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:
    
    | Nazwa atrybutu  | Wartość atrybutu | Przestrzeń nazw |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Rola            | User.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >Należy skonfigurować Inicjowanie obsługi użytkowników w usłudze Azure AD można pobrać za pomocą konsoli usług AWS wszystkich ról. Zobacz poniższe kroki inicjowania obsługi administracyjnej.

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie rejestracji jednokrotnej Dodaj](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie rejestracji jednokrotnej addattb](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_05.png)

    b. W **nazwa** tekstowym, wpisz nazwę atrybut wyświetlany dla danego wiersza.

    c. Z **wartość** listy, wpisz wartość atrybutu wyświetlany dla danego wiersza.

    d. W **Namespace** tekstowym, wpisz wartość przestrzeni nazw wyświetlany dla danego wiersza.
    
    d. Kliknij przycisk **OK**.

6. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

7. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_400.png)

8. W oknie innej przeglądarki logowania do witryny firmy Amazon Web Services (AWS) jako administrator.

9. Kliknij przycisk **głównej usług AWS**.
   
    ![Konfigurowanie rejestracji jednokrotnej głównej][11]

10. Kliknij przycisk **Zarządzanie tożsamościami i dostępem**. 
   
    ![Konfigurowanie tożsamości rejestracji jednokrotnej][12]

11. Kliknij przycisk **dostawców tożsamości**, a następnie kliknij przycisk **Tworzenie dostawcy**. 
   
    ![Konfigurowanie dostawcy rejestracji jednokrotnej][13]

12. Na **Konfigurowanie dostawcy** okna dialogowego strony, należy wykonać następujące czynności: 
   
    ![Konfigurowanie rejestracji jednokrotnej okna dialogowego][14]
 
    a. Jako **typ dostawcy**, wybierz pozycję **SAML**.

    b. W **Nazwa dostawcy** tekstowym, wpisz nazwę dostawcy (na przykład: *drewna*).

    c. Aby przekazać z pobranego **pliku metadanych** z portalu Azure, kliknij przycisk **wybierz plik**.

    d. Kliknij przycisk **następny krok**.

13. Na **Sprawdź informacje o dostawcy** strony okna dialogowego, kliknij przycisk **Utwórz**. 
    
    ![Konfigurowanie rejestracji jednokrotnej Sprawdź][15]

14. Kliknij przycisk **ról**, a następnie kliknij przycisk **Utwórz rolę**. 
    
    ![Konfigurowanie ról rejestracji jednokrotnej][16]

15. Na **Utwórz rolę** wykonaj następujące czynności:  
    
    ![Skonfigurować zaufanie rejestracji jednokrotnej][19] 

    a. Wybierz **federacyjnego SAML 2.0** w obszarze **wybierz typ zaufana jednostka**.

    b. W obszarze **Wybieranie sekcji SAML 2.0 dostawcy**, wybierz pozycję **SAML dostawcy** utworzonym wcześniej (na przykład: *drewna*)

    c. Wybierz **Zezwalaj programowych i dostępu do konsoli zarządzania usług AWS**.
  
    d. Kliknij przycisk **dalej: uprawnienia**.

16. Na **Dołącz zasady uprawnień** okna dialogowego, nie trzeba dołączać żadnych zasad. Kliknij przycisk **dalej: Przejrzyj**.  
    
    ![Konfigurowanie zasad rejestracji jednokrotnej][33]

17. Na **przeglądu** okna dialogowego, wykonaj następujące czynności:   
    
    ![Skonfiguruj przeglądu rejestracji jednokrotnej][34] 

    a. W **nazwy roli** pole tekstowe, wprowadź nazwę roli.

    b. W **opis roli** pole tekstowe, wprowadź opis.

    c. Kliknij przycisk **utworzyć rolę**.

    d. Tworzenie ról tyle zgodnie z potrzebami i zamapowania ich na dostawcy tożsamości.

18. Użyj poświadczeń konta usługi usług AWS pobierania ról z konta usług AWS w inicjowania obsługi usługi Azure AD użytkownika. W tym celu otwórz konsolę usług AWS macierzystego.

19. Polecenie **usług** -> **zabezpieczeń, tożsamości i zgodności** -> **IAM**.

    ![Pobieranie role z konta usług AWS](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole1.png)

20. Wybierz **zasady** kartę w sekcji IAM.

    ![Pobieranie role z konta usług AWS](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole2.png)

21. Utwórz nowe zasady, klikając **Tworzenie zasad** pobierania ról z konta usług AWS w inicjowania obsługi usługi Azure AD użytkownika.

    ![Tworzenie nowych zasad](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole3.png)

22. Tworzenie własnych zasad można pobrać wszystkich ról z kont usług AWS, wykonując następujące czynności:

    ![Tworzenie nowych zasad](./media/active-directory-saas-amazon-web-service-tutorial/policy1.png)

    a. W **"Utwórz zasady"** kliknij sekcję **"JSON"** kartę.

    b. W dokumencie zasady dodawania poniżej JSON.
    
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

    c. Polecenie **przycisk przeglądu zasad** do sprawdzania poprawności zasad.

    ![Definiowanie nowych zasad](./media/active-directory-saas-amazon-web-service-tutorial/policy5.png)

23. Zdefiniuj **nowe zasady** , wykonując następujące czynności:

    ![Definiowanie nowych zasad](./media/active-directory-saas-amazon-web-service-tutorial/policy2.png)

    a. Podaj **Nazwa zasady** jako **AzureAD_SSOUserRole_Policy**.

    b. Możesz podać **opis** do zasad jako **umożliwi tej zasady można pobrać ról z kont usług AWS**.
    
    c. Polecenie **"Utwórz zasady"** przycisku.

24. Utwórz nowe konto użytkownika w usłudze IAM usług AWS, wykonując następujące czynności:

    a. Polecenie **użytkowników** nawigacji w konsoli usług AWS IAM.

    ![Definiowanie nowych zasad](./media/active-directory-saas-amazon-web-service-tutorial/policy3.png)
    
    b. Polecenie **Dodaj użytkownika** przycisk, aby utworzyć nowego użytkownika.

    ![Dodawanie użytkownika](./media/active-directory-saas-amazon-web-service-tutorial/policy4.png)

    c. W **Dodaj użytkownika** sekcji, wykonaj następujące czynności:
    
    ![Dodawanie użytkownika](./media/active-directory-saas-amazon-web-service-tutorial/adduser1.png)
    
    * Wprowadź nazwę użytkownika jako **AzureADRoleManager**.
    
    * Wybierz typ dostępu **dostęp programistyczny** opcji. Dzięki temu użytkownik możesz wywoływać interfejsy API i pobrać role z konta usług AWS.
    
    * Polecenie **dalej uprawnienia** przycisk w prawym dolnym rogu.

25. Teraz należy utworzyć nowe zasady dla tego użytkownika, wykonując następujące czynności:

    ![Dodawanie użytkownika](./media/active-directory-saas-amazon-web-service-tutorial/adduser2.png)
    
    a. Polecenie **dołączyć istniejących zasad bezpośrednio** przycisku.

    b. Wyszukaj zasady nowo utworzony w sekcji Filtr **AzureAD_SSOUserRole_Policy**.
    
    c. Wybierz **zasad** , a następnie kliknij polecenie **dalej: Przejrzyj** przycisku.

26. Przejrzyj zasady użytkownika, wykonując następujące kroki:

    ![Dodawanie użytkownika](./media/active-directory-saas-amazon-web-service-tutorial/adduser3.png)
    
    a. Sprawdź nazwę użytkownika, typ dostępu i zasad zamapowany na użytkownika.
    
    b. Polecenie **tworzenia użytkownika** przycisk w prawym dolnym rogu do utworzenia użytkownika.

27. Pobieranie poświadczeń użytkownika, wykonując następujące kroki:

    ![Dodawanie użytkownika](./media/active-directory-saas-amazon-web-service-tutorial/adduser4.png)
    
    a. Skopiuj użytkownika **dostępu Identyfikatora klucza** i **klucza tajnego dostępu**.
    
    b. Wprowadź te poświadczenia do usługi Azure AD aprowizacji użytkowników sekcji można pobrać ról za pomocą konsoli usług AWS.
    
    c. Polecenie **Zamknij** znajdujący się u dołu.

28. Przejdź do **Inicjowanie obsługi użytkowników** części aplikacji usług Amazon Web Services w portalu zarządzania usługi Azure AD.

    ![Dodawanie użytkownika](./media/active-directory-saas-amazon-web-service-tutorial/provisioning.png)

29. Wprowadź **klucz dostępu** i **klucz tajny** w **klucz tajny klienta** i **klucz tajny tokenu** odpowiednio do pola.

    ![Dodawanie użytkownika](./media/active-directory-saas-amazon-web-service-tutorial/provisioning1.png)
    
    a. Wprowadź klucz dostępu użytkownika usług AWS w **clientsecret** pola.
    
    b. Wprowadź hasło użytkownika usług AWS w **klucz tajny tokenu** pola.
    
    c. Polecenie **Testuj połączenie** button powinien mógł pomyślnie przetestować tego połączenia.

    d. Zapisz ustawienia, klikając **zapisać** na górze.
 
30. Teraz upewnij się, że włączyć stan inicjowania obsługi administracyjnej **na** w sekcji Ustawienia wprowadzanie na przełącznik, a następnie klikając pozycję na **zapisać** na górze.

    ![Dodawanie użytkownika](./media/active-directory-saas-amazon-web-service-tutorial/provisioning2.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-an-amazon-web-services-aws-test-user"></a>Tworzenie użytkownika testowego Amazon Web Services (AWS)

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w Amazon Web Services (AWS). Amazon Web Services (AWS) nie wymaga od użytkownika mogą być tworzone w systemie dla logowania jednokrotnego, więc nie trzeba wykonywać żadnych czynności w tym miejscu.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji musisz włączyć Simona Britta do użycia usługi Azure logowania jednokrotnego za udzielanie dostępu do usług sieci Web firmy Amazon (AWS).

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta do Amazon Web Services (AWS), wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Amazon Web Services (AWS)**.

    ![Łącze Amazon Web Services (AWS) na liście aplikacji](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Amazon Web Services (AWS) w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji usługi sieci Web firmy Amazon (AWS).
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_203.png
[11]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795025.png
[32]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950252.png
[35]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950253.png
[36]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png


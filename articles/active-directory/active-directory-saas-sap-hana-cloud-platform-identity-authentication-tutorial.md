---
title: 'Samouczek: Integracji Azure Active Directory z uwierzytelnianie tożsamości platformy chmury SAP | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i uwierzytelniania tożsamości platformy SAP w chmurze.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: jeedes
ms.openlocfilehash: e7cc1df5e550dec62869c2a6f68cdc2a84167142
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>Samouczek: Integracji Azure Active Directory z uwierzytelniania tożsamości platformy SAP w chmurze

Z tego samouczka dowiesz się integrowanie uwierzytelniania tożsamości platformy SAP w chmurze z usługą Azure Active Directory (Azure AD). Uwierzytelnianie tożsamości platformy chmury SAP służy jako serwer proxy IdP do uzyskiwać dostęp do aplikacji przy użyciu usługi Azure AD jako główny IdP SAP.

Podczas uwierzytelniania tożsamości platformy SAP w chmurze są zintegrowane z usługą Azure AD, dostępne są następujące korzyści:

- Można kontrolować w usłudze Azure AD, kto ma dostęp do aplikacji SAP.
- Umożliwia użytkownikom automatycznie logować się do SAP aplikacji za pomocą ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji--portalu Azure.

Aby uzyskać więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz artykuł [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z uwierzytelnianiem tożsamość platformy chmury SAP, potrzebne są następujące elementy:

- Subskrypcja usługi Azure AD.
- Pojedynczy znak z włączoną subskrypcji uwierzytelniania tożsamości platformy SAP w chmurze.

> [!NOTE]
> Nie zaleca się używanie środowiska produkcyjnego do testowania czynności w tym samouczku.

Aby przetestować kroki opisane w tym samouczku, wykonaj te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, [Pobierz bezpłatną wersję próbną miesięcznego](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz, który jest opisane w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie uwierzytelniania tożsamości platformy SAP w chmurze z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

Przed Poznaj szczegółowe informacje techniczne, koniecznie zrozumienie pojęcia, które zamierzasz przyjrzeć się. Uwierzytelnianie tożsamości platformy chmury SAP i Active Directory Federation Services umożliwiają implementowanie logowania jednokrotnego w aplikacji lub usług, które są chronione przez usługę Azure AD (jako IdP) z aplikacjami SAP i usług, które są chronione przez chmurę SAP Uwierzytelnianie tożsamości platformy.

Obecnie uwierzytelnianie tożsamości platformy chmury SAP działa jako dostawca tożsamości serwera Proxy, aby aplikacje SAP. Usługi Azure Active Directory z kolei pełni rolę początku dostawcy tożsamości w tej instalacji. 

Na poniższym diagramie przedstawiono tę relację:

![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-sapcloudauth-tutorial/architecture-01.png)

W przypadku tej konfiguracji dzierżawy SAP Cloud Platform tożsamość uwierzytelniania jest skonfigurowana jako zaufanych aplikacji w usłudze Azure Active Directory. 

Wszystkie aplikacje SAP i usług, które mają być chronione w ten sposób następnie są konfigurowane w konsoli zarządzania uwierzytelniania tożsamości platformy SAP w chmurze.

W związku z tym autoryzacji za udzielanie dostępu do usługi i aplikacje SAP musi odbywać się w SAP Cloud Platform tożsamość uwierzytelniania (w przeciwieństwie do usługi Azure Active Directory).

Konfigurując uwierzytelnianie tożsamości platformy chmury SAP jako aplikację za pomocą usługi Azure Active Directory Marketplace, nie trzeba skonfigurować oddzielne oświadczenia lub twierdzeniom SAML.

>[!NOTE] 
>Obecnie tylko Jednokrotną w sieci Web był testowany obie strony. Przepływów, które są niezbędne dla aplikacji do interfejsu API lub interfejsu API do interfejsu API komunikacji powinny działać, ale nie została jeszcze przetestowana. Zostaną one przetestowane podczas kolejnych działań.
>

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Dodawanie uwierzytelniania tożsamości platformy SAP w chmurze z galerii
Aby skonfigurować integrację SAP Cloud Platform tożsamość uwierzytelniania w usłudze Azure AD, należy dodać uwierzytelnianie tożsamości platformy chmury SAP z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać uwierzytelnianie tożsamości platformy chmury SAP z galerii, należy wykonać następujące czynności:**

1. W [portalu Azure](https://portal.azure.com), na panelu nawigacyjnym po lewej stronie wybierz **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nowe nowej aplikacji, wybierz **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **uwierzytelnianie tożsamości platformy chmury SAP**. 

5. Wybierz **uwierzytelnianie tożsamości platformy chmury SAP** z panelu wyniki, a następnie wybierz **Dodaj** przycisku.

    ![Uwierzytelnianie tożsamości platformy chmury SAP na liście wyników](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji możesz Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej z uwierzytelniania tożsamości platformy SAP w chmurze. Skonfigurować i przetestować go z użytkownikiem testu o nazwie "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, będącego użytkownika odpowiednika w uwierzytelniania tożsamości platformy SAP w chmurze. Innymi słowy należy ustanowić powiązanie użytkownika usługi Azure AD i danemu użytkownikowi w uwierzytelniania tożsamości platformy SAP w chmurze.

W uwierzytelniania tożsamości platformy w chmurze SAP, należy podać wartość **Username** tę samą wartość jak **nazwy użytkownika** w usłudze Azure AD. Teraz ustanowieniu połączenia między dwóch użytkowników.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z uwierzytelnianiem tożsamość platformy chmury SAP, wykonaj poniższe bloki konstrukcyjne:

1. [Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on) umożliwienie użytkownikom korzystać z tej funkcji.
2. [Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) do testowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. [Tworzenie użytkownika testowego uwierzytelnianie tożsamości platformy chmury SAP](#create-an-sap-cloud-platform-identity-authentication-test-user) w celu zapewnienia odpowiednikiem Simona Britta SAP platformy tożsamość uwierzytelniania w chmurze połączonego z usługi Azure AD reprezentację użytkownika.
4. [Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) umożliwiające Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. [Test rejestracji jednokrotnej](#test-single-sign-on) Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne do aplikacji uwierzytelnianie tożsamości platformy chmury SAP.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej uwierzytelnianie tożsamości platformy chmury SAP, wykonaj następujące kroki:**

1. W portalu Azure na **uwierzytelnianie tożsamości platformy chmury SAP** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. W **logowanie jednokrotne** okna dialogowego, w obszarze **na języku SAML logowania jednokrotnego**, wybierz pozycję **tryb** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_samlbase.png)

3. Jeśli chcesz skonfigurować aplikację w **IDP** inicjowane w trybie **domeny uwierzytelniania tożsamości SAP Cloud Platform i adres URL** sekcji, wykonaj następujące czynności:  

    ![Adresy URL i SAP Cloud Platform tożsamości uwierzytelniania domeny pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_url.png)

    a. W **identyfikator** wpisz adres URL z następującego wzorca: `<IAS-tenant-id>.accounts.ondemand.com`

    b. W **adres URL odpowiedzi** wpisz adres URL z następującego wzorca: `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Wartości te nie są prawdziwe. Rzeczywisty identyfikator i adres URL odpowiedzi, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej SAP Cloud Platform tożsamość uwierzytelniania klient](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) uzyskać te wartości. Jeśli nie znasz wartość identyfikatora, zapoznaj się z dokumentacją uwierzytelnianie tożsamości platformy chmury SAP o [konfiguracji dzierżawy SAML 2.0](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

4. Jeśli chcesz skonfigurować aplikację w **SP** tryb inicjowane, wybierz opcję **Pokaż zaawansowane ustawienia adresu URL**.

    ![Adresy URL i SAP Cloud Platform tożsamości uwierzytelniania domeny pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_url1.png)

    W **na adres URL logowania** wpisz adres URL z następującego wzorca: `{YOUR BUSINESS APPLICATION URL}`.

    > [!NOTE]
    > Ta wartość nie jest prawdziwe. Zaktualizuj tę wartość przy rzeczywisty adres URL logowania. Użyj Twojej firmy jednokrotnej adres URL aplikacji. Skontaktuj się z [zespołem pomocy technicznej SAP Cloud Platform tożsamość uwierzytelniania klient](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) Jeśli masz wątpliwości.

5. W **certyfikat podpisywania SAML** zaznacz **XML metadanych**. Następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_certificate.png)

6. Aplikacja uwierzytelniania tożsamości w chmurze platformy SAP oczekuje potwierdzenia języka SAML w określonym formacie. Zarządzanie wartości tych atrybutów z **atrybuty użytkownika** na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład formatu. 

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-sapcloudauth-tutorial/attribute.png)

7. Jeśli aplikacja SAP oczekuje atrybutu, takich jak **imię**, Dodaj **imię** atrybutu w **atrybuty użytkownika** sekcji. Ta opcja jest dostępna w **logowanie jednokrotne** okna dialogowego **atrybuty tokenu SAML** okno dialogowe.

    a. Aby otworzyć **Dodawanie atrybutu** okno dialogowe, wybierz opcję **Dodaj atrybut**. 
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_attribute_05.png)
    
    b. W **nazwa** wpisz nazwę atrybutu **imię**.
    
    c. Z **wartość** , wybierz wartość atrybutu na liście **user.givenname**.
    
    d. Wybierz **Ok**.

8. Wybierz ikonę **Zapisz**.

    ![Konfigurowanie rejestracji jednokrotnej przycisk zapisywania](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_400.png)

9. W **konfiguracji uwierzytelniania tożsamości SAP chmury platformy** zaznacz **skonfigurować uwierzytelnianie tożsamości platformy chmury SAP** otworzyć **Konfigurowanie logowania jednokrotnego**okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfiguracja uwierzytelniania tożsamości platformy chmury SAP](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_configure.png) 

10. Aby uzyskać logowania jednokrotnego skonfigurowane dla aplikacji, przejdź do konsoli administracyjnej programu SAP Cloud Platform tożsamość uwierzytelniania. Adres URL ma następujący wzór: `https://<tenant-id>.accounts.ondemand.com/admin`. Następnie przeczytaj dokumentację dotyczącą uwierzytelnianie tożsamości platformy chmury SAP w [integracji z programem Microsoft Azure AD](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html). 

11. W portalu Azure wybierz **zapisać** przycisku.

12. Następujące należy kontynuować tylko wtedy, gdy chcesz dodać, a następnie włączenia funkcji logowania jednokrotnego dla innej aplikacji SAP. Powtórz kroki opisane w sekcji **Dodawanie SAP platformy tożsamość uwierzytelniania w chmurze z galerii**.

13. W portalu Azure na **uwierzytelnianie tożsamości platformy chmury SAP** strona integracji aplikacji, wybierz opcję **połączonej logowania jednokrotnego**.

    ![Skonfiguruj połączonego logowania jednokrotnego](./media/active-directory-saas-sapcloudauth-tutorial/linked_sign_on.png)

14. Zapisz konfigurację.

>[!NOTE] 
>Nowa aplikacja korzysta z jednego logowania jednokrotnego konfiguracji poprzedniej aplikacji SAP. Sprawdź, czy używać tego samego dostawcy tożsamości firmy w konsoli administracyjnej programu SAP Cloud Platform tożsamość uwierzytelniania.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com) podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory** > **aplikacje dla przedsiębiorstw** zaznacz **rejestracji jednokrotnej** karcie i dostępu do osadzonego Dokumentacja za pośrednictwem **konfiguracji** sekcji u dołu. Możesz przeczytać dodatkowe informacje o funkcji dokumentacji osadzony w [usługi Azure AD osadzonych dokumentacji]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, należy wykonać następujące czynności:**

1. W portalu Azure, w lewym okienku wybierz **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie wybierz **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, wybierz opcję **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego należy wykonać następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-an-sap-cloud-platform-identity-authentication-test-user"></a>Tworzenie użytkownika testowego uwierzytelniania tożsamości platformy SAP w chmurze

Nie trzeba utworzyć SAP Cloud Platform tożsamość uwierzytelniania użytkownika. Użytkownicy, którzy znajdują się w magazynie usługi Azure AD użytkownika może używać funkcji logowania jednokrotnego.

Uwierzytelnianie tożsamości platformy chmury SAP obsługuje opcję federacji tożsamości. Ta opcja umożliwia aplikacji sprawdzić, czy użytkownicy, którzy są uwierzytelniani przez dostawcę tożsamości firmy istnieje w magazynie z SAP chmury platformy tożsamości uwierzytelnianie użytkownika. 

Opcja federacji tożsamości jest domyślnie wyłączona. Włączenie federacji tożsamości użytkowników importowanych w uwierzytelnianie tożsamości platformy chmury SAP dostęp do aplikacji. 

Aby uzyskać więcej informacji o tym, jak włączyć lub wyłączyć federacji tożsamości z uwierzytelniania tożsamości platformy SAP w chmurze, zobacz "Włącz tożsamości federacyjnej z SAP platformy tożsamość uwierzytelniania w chmurze" w [Konfigurowanie federacji tożsamości z Uwierzytelnianie tożsamości użytkownika magazynu SAP chmury platformy](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html).

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do uwierzytelniania tożsamości platformy SAP w chmurze.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta do uwierzytelniania tożsamości platformy SAP w chmurze, należy wykonać następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu. Następnie należy przejść do **aplikacje dla przedsiębiorstw**, a następnie wybierz **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **uwierzytelnianie tożsamości platformy chmury SAP**.

    ![Uwierzytelnianie tożsamości platformy chmury SAP łącze na liście aplikacji](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_app.png)  

3. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Wybierz **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okno dialogowe.

    ![W okienku Dodaj przydziału][203]

5. W **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** przycisk **użytkowników i grup** okno dialogowe.

7. Wybierz **przypisać** przycisk **Dodaj przydziału** okno dialogowe.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po wybraniu kafelka uwierzytelnianie tożsamości platformy chmury SAP w panelu dostępu get automatycznym zalogowaniem się do aplikacji uwierzytelnianie tożsamości platformy chmury SAP.

Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS w usłudze Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_203.png

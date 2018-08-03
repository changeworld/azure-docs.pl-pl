---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą uwierzytelniania tożsamości platformy SAP w chmurze | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i uwierzytelniania tożsamości platformy SAP w chmurze.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: jeedes
ms.openlocfilehash: 266c9523f45294899e3cddbe782cbc54846eb119
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39422311"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą uwierzytelniania tożsamości platformy SAP w chmurze

W tym samouczku dowiesz się, jak zintegrować uwierzytelnianie tożsamości platformy SAP w chmurze z usługą Azure Active Directory (Azure AD). Uwierzytelnianie tożsamości platformy SAP w chmurze jest używany jako serwer proxy protokołu IdP dostępu do aplikacji SAP, korzystających z usługi Azure AD jako główny dostawca tożsamości.

Podczas uwierzytelniania tożsamości platformy SAP w chmurze można zintegrować z usługą Azure AD, możesz uzyskać następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do aplikacji SAP.
- Aby umożliwić użytkownikom automatyczne logowanie do aplikacji za pomocą swoich kont usługi Azure AD SAP.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Aby uzyskać więcej informacji na temat integracji aplikacji SaaS z usługą Azure AD, zobacz artykuł [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu uwierzytelniania tożsamości platformy SAP w chmurze, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD.
- Pojedynczy znak na włączona subskrypcji uwierzytelniania tożsamości platformy SAP w chmurze.

> [!NOTE]
> Nie zaleca się testowanie kroki opisane w tym samouczku przy użyciu środowiska produkcyjnego.

Aby przetestować czynności w ramach tego samouczka, wykonaj te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli masz środowisko wersji próbnej usługi Azure AD [bezpłatna wersja próbna miesięcznej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz, który jest opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie uwierzytelniania tożsamości platformy SAP w chmurze za pomocą galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

Przed wykonaniem instrukcji zawartych w szczegółowe informacje techniczne, koniecznie zapoznać się z pojęciami, które zamierzasz Przyjrzyj się. Uwierzytelnianie tożsamości platformy SAP w chmurze i usług federacyjnych Active Directory umożliwiają implementowanie logowania jednokrotnego w aplikacji lub usług, które są chronione przez usługę Azure AD (jako dostawcy tożsamości) z aplikacjami SAP i usług, które są chronione przez oprogramowanie SAP Cloud Uwierzytelnianie tożsamości platformy.

Obecnie uwierzytelnianie tożsamości platformy SAP w chmurze działa jako dostawca tożsamości serwera Proxy dla aplikacji SAP. Usługa Azure Active Directory z kolei działa jako czołowy dostawca tożsamości w tej konfiguracji. 

Na poniższym diagramie przedstawiono tę relację:

![Tworzenie użytkownika testowego usługi Azure AD](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

W przypadku takiej konfiguracji dzierżawy usługi uwierzytelniania tożsamości platformy SAP w chmurze jest skonfigurowany jako zaufanych aplikacji w usłudze Azure Active Directory. 

Wszystkie aplikacje SAP i usługi, które mają być chronione w ten sposób następnie są konfigurowane w konsoli zarządzania uwierzytelniania tożsamości platformy SAP w chmurze.

W związku z tym autoryzacja przyznawania dostępu do usług i aplikacji SAP musi odbywać się w uwierzytelniania tożsamości SAP Cloud Platform (w przeciwieństwie do usługi Azure Active Directory).

Konfigurowanie uwierzytelniania tożsamości platformy SAP w chmurze jako aplikację za pomocą usługi Azure Active Directory Marketplace, nie trzeba konfigurować poszczególnych oświadczeń lub twierdzenia SAML.

>[!NOTE] 
>Obecnie tylko usługa rejestracji Jednokrotnej w sieci Web został przetestowany przez obie strony. Przepływy, które są niezbędne dla aplikacji do interfejsu API lub interfejsu API do interfejsu API komunikacji powinny działać, ale nie został jeszcze przetestowany. Zostaną one przetestowane podczas kolejnych działań.
>

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Dodawanie uwierzytelniania tożsamości platformy SAP w chmurze za pomocą galerii
Aby skonfigurować integrację SAP Cloud Platform tożsamości uwierzytelniania w usłudze Azure AD, należy dodać uwierzytelnianie tożsamości platformy SAP w chmurze za pomocą galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać uwierzytelnianie tożsamości platformy SAP w chmurze z galerii, wykonaj następujące czynności:**

1. W [witryny Azure portal](https://portal.azure.com), w panelu nawigacyjnym po lewej stronie wybierz **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nowe nową aplikację, wybierz **nową aplikację** znajdujący się u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **uwierzytelniania tożsamości platformy SAP w chmurze**. 

1. Wybierz **uwierzytelniania tożsamości platformy SAP w chmurze** z panel wyników, a następnie wybierz **Dodaj** przycisku.

    ![Uwierzytelnianie tożsamości SAP Cloud Platform na liście wyników](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowania usługi Azure AD logowania jednokrotnego przy użyciu uwierzytelniania tożsamości platformy SAP w chmurze. Możesz skonfigurować i przetestować go za pomocą użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi wiedzieć, który jest odpowiednikiem użytkownika uwierzytelniania tożsamości platformy SAP w chmurze. Innymi słowy należy ustanowić łącze między użytkownika usługi Azure AD i powiązanych użytkowników w uwierzytelniania tożsamości platformy SAP w chmurze.

W przypadku uwierzytelniania tożsamości platformy SAP w chmurze, należy podać wartość **Username** taką samą wartość jak **nazwy użytkownika** w usłudze Azure AD. Łącze między dwóch użytkowników ma ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu uwierzytelniania tożsamości platformy SAP w chmurze, wykonaj poniższe bloki konstrukcyjne:

1. [Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on) aby umożliwić użytkownikom korzystać z tej funkcji.
1. [Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) do testowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. [Tworzenie użytkownika testowego uwierzytelniania tożsamości platformy SAP w chmurze](#create-an-sap-cloud-platform-identity-authentication-test-user) mieć odpowiednikiem Britta Simon SAP platforma tożsamości uwierzytelniania w chmurze połączonego z usługi Azure AD reprezentacja użytkownika.
1. [Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) umożliwiające Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. [Testowanie logowania jednokrotnego](#test-single-sign-on) można sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji uwierzytelniania tożsamości platformy SAP w chmurze.

**Aby skonfigurować usługę Azure AD logowania jednokrotnego przy użyciu uwierzytelniania tożsamości platformy SAP w chmurze, wykonaj następujące czynności:**

1. W witrynie Azure portal na **uwierzytelniania tożsamości platformy SAP w chmurze** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. W **logowanie jednokrotne** dialogowego **opartej na SAML logowania jednokrotnego**, wybierz opcję **tryb** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_samlbase.png)

1. Jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb, w **SAP Cloud Platform tożsamości uwierzytelniania domena i adresy URL** sekcji, wykonaj następujące czynności:  

    ![SAP Cloud Platform tożsamości uwierzytelniania domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_url.png)

    a. W **identyfikator** wpisz adres URL z następującym wzorcem: `<IAS-tenant-id>.accounts.ondemand.com`

    b. W **adres URL odpowiedzi** wpisz adres URL z następującym wzorcem: `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu identyfikatora rzeczywiste i adres URL odpowiedzi. Skontaktuj się z pomocą [zespołem pomocy technicznej SAP Cloud Platform tożsamości uwierzytelniania klienta](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) do uzyskania tych wartości. Jeśli nie rozumiesz wartość identyfikatora, zapoznaj się z dokumentacją uwierzytelniania tożsamości platformy SAP w chmurze o [konfiguracji dzierżawy SAML 2.0](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

1. Jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb, wybierz opcję **Pokaż zaawansowane ustawienia adresu URL**.

    ![SAP Cloud Platform tożsamości uwierzytelniania domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_url1.png)

    W **na adres URL logowania** wpisz adres URL z następującym wzorcem: `{YOUR BUSINESS APPLICATION URL}`.

    > [!NOTE]
    > Ta wartość nie jest prawdziwe. Zaktualizuj tę wartość przy użyciu rzeczywisty adres URL logowania. Użyj swojej firmy jednokrotnej adres URL aplikacji. Skontaktuj się z pomocą [zespołem pomocy technicznej SAP Cloud Platform tożsamości uwierzytelniania klienta](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) Jeśli masz jakiekolwiek wątpliwości.

1. W **certyfikat podpisywania SAML** zaznacz **XML metadanych**. Następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_certificate.png)

1. Aplikacja uwierzytelniania tożsamości w chmurze platformy SAP oczekuje twierdzenia SAML, w określonym formacie. Zarządzanie wartościami tych atrybutów z **atrybutów użytkownika** sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład formatu. 

    ![Konfigurowanie logowania jednokrotnego](./media/sap-hana-cloud-platform-identity-authentication-tutorial/attribute.png)

1. Jeśli aplikacja SAP oczekuje atrybutu, takich jak **firstName**, Dodaj **firstName** atrybutu w **atrybutów użytkownika** sekcji. Ta opcja jest dostępna w **logowanie jednokrotne** okna dialogowego **atrybuty tokenu języka SAML** okno dialogowe...

    a. Aby otworzyć **Dodawanie atrybutu** okno dialogowe, wybierz opcję **Dodaj atrybut**. 
    
    ![Konfigurowanie logowania jednokrotnego](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurowanie logowania jednokrotnego](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_attribute_05.png)
    
    b. W **nazwa** wpisz nazwę atrybutu **firstName**.
    
    c. Z **wartość** , wybierz wartość atrybutu na liście **user.givenname**.
    
    d. Wybierz przycisk **OK**.

1. Wybierz ikonę **Zapisz**.

    ![Konfigurowanie logowania jednokrotnego przycisk zapisywania](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_400.png)

1. W **konfiguracji uwierzytelniania tożsamości platformy w chmurze SAP** zaznacz **skonfigurować SAP platforma tożsamości uwierzytelniania w chmurze** otworzyć **skonfigurować logowanie jednokrotne**okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja uwierzytelniania dla tożsamości platformy SAP Cloud](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_configure.png) 

1. Aby uzyskać logowanie Jednokrotne skonfigurowane pod kątem swojej aplikacji, przejdź do konsoli administracyjnej uwierzytelniania tożsamości platformy SAP w chmurze. Adres URL zawiera następującego wzorca: `https://<tenant-id>.accounts.ondemand.com/admin`. Następnie zapoznaj się z dokumentacją dotyczącą uwierzytelniania tożsamości SAP Cloud Platform na [integracji z usługą Microsoft Azure AD](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html). 

1. W witrynie Azure portal wybierz **Zapisz** przycisku.

1. Wykonaj następujące czynności tylko wtedy, gdy chcesz dodać i włączyć logowanie Jednokrotne dla innej aplikacji SAP. Powtórz kroki opisane w sekcji **Dodawanie SAP platforma tożsamości uwierzytelniania w chmurze za pomocą galerii**.

1. W witrynie Azure portal na **uwierzytelniania tożsamości platformy SAP w chmurze** strona integracji aplikacji, wybierz opcję **połączone logowanie jednokrotne**.

    ![Konfigurowanie połączone logowanie](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

1. Zapisz konfigurację.

>[!NOTE] 
>Nowa aplikacja korzysta z jednej konfiguracji logowania jednokrotnego poprzedniej aplikacji SAP. Upewnij się, że używasz tego samego dostawcy tożsamości firmowej w konsoli administracyjnej uwierzytelniania tożsamości platformy SAP w chmurze.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com) podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory** > **aplikacje dla przedsiębiorstw** zaznacz **logowania jednokrotnego** karty i dostępu do osadzonego Dokumentacja usługi za pośrednictwem **konfiguracji** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzone w [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie wybierz pozycję **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, wybierz opcję **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-an-sap-cloud-platform-identity-authentication-test-user"></a>Tworzenie użytkownika testowego uwierzytelniania tożsamości platformy SAP w chmurze

Nie trzeba utworzyć użytkownika w uwierzytelniania tożsamości platformy SAP w chmurze. Użytkownicy, którzy znajdują się w magazynie użytkownika usługi Azure AD można używać funkcji logowania jednokrotnego.

Uwierzytelnianie tożsamości platformy SAP w chmurze obsługuje opcję federacji tożsamości. Ta opcja umożliwia aplikacji sprawdzić, czy użytkownicy, którzy są uwierzytelniani przez dostawcę tożsamości firmowej istnieje w magazynie programu SAP Cloud platformy tożsamości uwierzytelnianie użytkownika. 

Opcja federacji tożsamości jest domyślnie wyłączona. Włączenie federacji tożsamości użytkowników, które są importowane w uwierzytelniania tożsamości platformy SAP w chmurze dostęp do aplikacji. 

Aby uzyskać więcej informacji o tym, jak włączyć lub wyłączyć Federację tożsamości za pomocą uwierzytelniania tożsamości platformy SAP w chmurze, zobacz "Włącz tożsamości federacyjnej z SAP platforma tożsamości uwierzytelniania w chmurze" w [skonfigurować Federację tożsamości za pomocą Uwierzytelnianie tożsamości platformy w chmurze Store SAP użytkownika](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do uwierzytelniania tożsamości platformy SAP w chmurze.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon do uwierzytelniania tożsamości platformy SAP w chmurze, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu. Następnie przejdź do **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **uwierzytelniania tożsamości platformy SAP w chmurze**.

    ![Link uwierzytelniania tożsamości platformy SAP w chmurze na liście aplikacji](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_app.png)  

1. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Wybierz **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** w **Dodaj przydziału** okno dialogowe.

    ![Okienko Dodawanie przypisania][203]

1. W **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się w **użytkowników i grup** okno dialogowe.

1. Wybierz **przypisać** znajdujący się w **Dodaj przydziału** okno dialogowe.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po wybraniu kafelka uwierzytelniania tożsamości platformy SAP w chmurze w panelu dostępu, możesz pobrać automatyczne zalogowanie do aplikacji uwierzytelniania tożsamości platformy SAP w chmurze.

Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sapcloudauth-tutorial/tutorial_general_01.png
[2]: ./media/sapcloudauth-tutorial/tutorial_general_02.png
[3]: ./media/sapcloudauth-tutorial/tutorial_general_03.png
[4]: ./media/sapcloudauth-tutorial/tutorial_general_04.png

[100]: ./media/sapcloudauth-tutorial/tutorial_general_100.png

[200]: ./media/sapcloudauth-tutorial/tutorial_general_200.png
[201]: ./media/sapcloudauth-tutorial/tutorial_general_201.png
[202]: ./media/sapcloudauth-tutorial/tutorial_general_202.png
[203]: ./media/sapcloudauth-tutorial/tutorial_general_203.png

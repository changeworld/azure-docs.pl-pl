---
title: 'Samouczek: Integracja usługi Azure Active Directory z platformą SAP HANA | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i SAP HANA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: cef4a146-f4b0-4e94-82de-f5227a4b462c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: c466e811d868403c59d6615882422996442d792a
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39045831"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Samouczek: Integracja usługi Azure Active Directory z platformą SAP HANA

W tym samouczku dowiesz się, jak zintegrować platformy SAP HANA przy użyciu usługi Azure Active Directory (Azure AD).

SAP HANA można zintegrować z usługą Azure AD, pojawi się następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do oprogramowania SAP HANA.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanie do platformy SAP HANA przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Aby uzyskać więcej informacji na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z platformą SAP HANA, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Subskrypcji platformy SAP HANA, która jest logowanie jednokrotne (SSO) włączone
- Wystąpienie platformy HANA, które działa w dowolnej usłudze IaaS publicznych, lokalnie, maszyny Wirtualnej platformy Azure lub duże wystąpienia SAP na platformie Azure
- Interfejs sieci web administracji XSA, a także HANA Studio zainstalowany na wystąpienie oprogramowania HANA

> [!NOTE]
> Kroki testu w ramach tego samouczka przy użyciu środowiska produkcyjnego platformy SAP Hana nie jest zalecane. Testowanie integracji najpierw w rozwoju lub środowisko przejściowe aplikacji, a następnie użyć w środowisku produkcyjnym.

Aby przetestować czynności w ramach tego samouczka, wykonaj te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- [Bezpłatna wersja próbna miesięcznej](https://azure.microsoft.com/pricing/free-trial/) usługi Azure AD, jeśli nie masz jeszcze środowisko w wersji próbnej usługi Azure AD.

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz, który jest opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie oprogramowania SAP HANA z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="add-sap-hana-from-the-gallery"></a>Dodaj oprogramowanie SAP HANA z galerii
Aby skonfigurować integrację z platformą SAP HANA w usłudze Azure AD, Dodaj oprogramowanie SAP HANA z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać platformy SAP HANA z galerii, wykonaj następujące czynności:**

1. W [witryny Azure portal](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, wybierz **nową aplikację** przycisk u góry okno dialogowe.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **platformy SAP HANA**. Następnie wybierz pozycję **platformy SAP HANA** z panelu wyników. Na koniec wybierz pozycję **Dodaj** przycisk, aby dodać aplikację. 

    ![Nowa aplikacja](./media/saphana-tutorial/tutorial_saphana_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD logowanie jednokrotne z platformą SAP HANA w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi wiedzieć, kim jest odpowiednikiem użytkownik na platformie SAP HANA dla użytkownika w usłudze Azure AD. Innymi słowy należy ustanowić łącze między użytkownika usługi Azure AD i powiązanych użytkowników na platformie SAP HANA.

W przypadku oprogramowania SAP HANA udzielić **Username** wartość taką samą wartość **nazwy użytkownika** w usłudze Azure AD. Ten krok pozwala ustanowić łącze między dwóch użytkowników.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z platformą SAP HANA, należy wykonać poniższe bloki konstrukcyjne:

1. [Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on) aby umożliwić użytkownikom korzystać z tej funkcji.
2. [Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user) do testowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. [Tworzenie użytkownika testowego platformy SAP HANA](#creating-a-sap-hana-test-user) mieć odpowiednikiem Britta Simon platformy SAP HANA jest połączony z usługi Azure AD reprezentacja użytkownika.
4. [Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user) umożliwiające Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. [Testowanie logowania jednokrotnego](#testing-single-sign-on) Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji SAP HANA.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z platformą SAP HANA, wykonaj następujące czynności:**

1. W witrynie Azure portal na **platformy SAP HANA** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

2. W **logowanie jednokrotne** dialogowego **opartej na SAML logowania jednokrotnego**, wybierz opcję **tryb**.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/saphana-tutorial/tutorial_saphana_samlbase.png)

3. W **SAP HANA domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/saphana-tutorial/tutorial_saphana_url.png)

    a. W **identyfikator** polu, wpisz następujące polecenie: `HA100` 

    b. W **adres URL odpowiedzi** wpisz adres URL z następującym wzorcem: `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości z rzeczywistego identyfikatora, a adres URL odpowiedzi. Skontaktuj się z pomocą [zespół obsługi klienta platformy SAP HANA](https://cloudplatform.sap.com/contact.html) do uzyskania tych wartości. 

4. W **certyfikat podpisywania SAML** zaznacz **XML metadanych**. Następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/saphana-tutorial/tutorial_saphana_certificate.png) 

    >[!Note]
    >Jeśli certyfikat nie jest aktywne, następnie ją uaktywnić, wybierając **Ustaw nowy certyfikat jako aktywny** pole wyboru w usłudze Azure AD. 

5. Aplikacja platformy SAP HANA oczekuje twierdzenia SAML w określonym formacie. Poniższy zrzut ekranu przedstawia przykład tego formatu. 

    W tym miejscu Przedstawiliśmy **identyfikator użytkownika** z **ExtractMailPrefix()** funkcji **user.mail**. Dzięki temu wartość prefiksu adres e-mail użytkownika, który jest identyfikator unikatowy użytkownika. Ta nazwa użytkownika jest wysyłana do aplikacji SAP HANA w każdej pomyślnej odpowiedzi.

    ![Konfigurowanie logowania jednokrotnego](./media/saphana-tutorial/attribute.png)

6. W **atrybutów użytkownika** części **logowanie jednokrotne** okna dialogowego pole, wykonaj następujące czynności:

    a. W **identyfikator użytkownika** listy rozwijanej wybierz **ExtractMailPrefix**.
    
    b. W **poczty** listy rozwijanej wybierz **user.mail**.

7. Wybierz ikonę **Zapisz**.

    ![Konfigurowanie logowania jednokrotnego przycisk zapisywania](./media/saphana-tutorial/tutorial_general_400.png)
    
8. Aby skonfigurować logowanie jednokrotne, po stronie SAP HANA, zaloguj się do Twojej **konsoli sieci Web XSA HANA** , przechodząc do odpowiednich punktu końcowego HTTPS.

    > [!NOTE]
    > W konfiguracji domyślnej adresu URL przekierowuje żądanie do rejestrowania ekranu, który wymaga poświadczeń uwierzytelnionego użytkownika bazy danych SAP HANA. Użytkownik, który się zaloguje, musi mieć uprawnienia do wykonywania zadań administracyjnych SAML.

9. W interfejsie sieci Web XSA przejdź do **dostawcy tożsamości SAML**. Z tego miejsca wybierz **+** przycisk w dolnej części ekranu, aby wyświetlić **Dodaj tożsamość dostawcach** okienka. Następnie wykonaj następujące czynności:

    ![Dodaj dostawcę tożsamości](./media/saphana-tutorial/sap1.png)

    a. W **Dodaj tożsamość dostawcach** okienko, Wklej zawartość XML metadanych, (który został pobrany z witryny Azure portal) **metadanych** pole.

    ![Dodawanie ustawień dostawcy tożsamości](./media/saphana-tutorial/sap2.png)

    b. Jeśli zawartość dokumentu XML są prawidłowe, proces analizowania wyodrębnia informacje, które jest wymagane dla **podmiot, identyfikator jednostki i Wystawca** pola w **ogólne dane** obszar ekranu. Wyodrębnia informacje, które są niezbędne do pola Adres URL w **docelowy** ekranu obszaru, na przykład **podstawowy adres URL i adresu URL SingleSignOn (*)** pola.

    ![Dodawanie ustawień dostawcy tożsamości](./media/saphana-tutorial/sap3.png)

    c. W **nazwa** pole **dane ogólne** obszar ekranu, wprowadź nazwę dla nowego dostawcy tożsamości SAML logowania jednokrotnego.

    > [!NOTE]
    > Nazwa dostawcy tożsamości SAML jest wymagany i musi być unikatowa. Wydaje się na liście dostępnych dostawców tożsamości SAML, wyświetlanego po wybraniu SAML jako metodę uwierzytelniania dla aplikacji SAP HANA XS do użycia. Na przykład, można to zrobić w **uwierzytelniania** obszar narzędzia do administrowania artefaktu XS ekranu.

10. Wybierz **Zapisz** zapisać szczegóły dostawcy tożsamości SAML i dodawanie nowego dostawcy tożsamości SAML do listy znanych dostawców tożsamości SAML.

    ![Przycisk Zapisz](./media/saphana-tutorial/sap4.png)

11. W programie Studio HANA we właściwościach systemu **konfiguracji** filtrowania ustawień, a **saml**. Następnie Dostosuj **assertion_timeout** z **10 s** do **120 s**.

    ![Ustawienie assertion_timeout](./media/saphana-tutorial/sap7.png)

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com) podczas konfigurowania aplikacji! Po dodaniu tej aplikacji z **usługi Active Directory** > **aplikacje dla przedsiębiorstw** zaznacz **logowania jednokrotnego** karty i dostępu do osadzonego Dokumentacja usługi za pośrednictwem **konfiguracji** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzone w [dokumentacja embedded usługi Azure AD](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku po lewej stronie wybierz **usługi Azure Active Directory** ikony.

    ![Przycisk usługi Azure Active Directory](./media/saphana-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**. Następnie wybierz pozycję **wszyscy użytkownicy**.
    
    !["Użytkownicy i grupy" i "All users" linki](./media/saphana-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okno dialogowe, wybierz opcję **Dodaj** w górnej części okna dialogowego.
 
    ![Przycisk Dodaj](./media/saphana-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego pole, wykonaj następujące czynności:
 
    ![Okno dialogowe użytkownika](./media/saphana-tutorial/create_aaduser_04.png) 

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło**, a następnie Zapisz hasło.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-a-sap-hana-test-user"></a>Tworzenie użytkownika testowego platformy SAP HANA

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do platformy SAP HANA, można udostępnić je na platformie SAP HANA.
SAP HANA obsługę just-in-time, który jest przez domyślnie włączone.

Jeśli potrzebujesz ręcznie utworzyć użytkownika, należy wykonać następujące czynności:

>[!NOTE]
>Możesz zmienić uwierzytelniania zewnętrznego, których użytkownik używa. Podczas uwierzytelniania się z systemem zewnętrznym, na przykład protokołu Kerberos. Aby uzyskać szczegółowe informacje o tożsamości zewnętrznych, skontaktuj się z [administrator domeny](https://cloudplatform.sap.com/contact.html).

1. Otwórz [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) jako administrator, a następnie włączyć je użytkownik bazy danych na potrzeby logowania jednokrotnego SAML.

    ![Utwórz użytkownika](./media/saphana-tutorial/sap5.png)

2. Zaznacz pole wyboru niewidoczne po lewej stronie **SAML**, a następnie wybierz pozycję **Konfiguruj** łącza.

3. Wybierz **Dodaj** do dodania dostawcy tożsamości SAML.  Wybierz odpowiedni dostawcy tożsamości SAML, a następnie wybierz **OK**.

4. Dodaj **tożsamości zewnętrznych** (w tym przypadku BrittaSimon) lub wybierz **wszelkie**. Następnie wybierz przycisk **OK**.

    >[!Note]
    >Jeśli **wszelkie** nie zaznaczono pole wyboru, a następnie nazwę użytkownika na platformie HANA musi dokładnie pasować do nazwy użytkownika w głównej nazwy użytkownika przed sufiksem domeny. (Na przykład BrittaSimon@contoso.com staje się BrittaSimon na platformie HANA.)

5. Do celów testowych, należy przypisać wszystkich **XS** role dla użytkownika.

    ![Przypisywanie ról](./media/saphana-tutorial/sap6.png)

    > [!TIP]
    > Należy nadać kontu uprawnienia, które są odpowiednie dla Twojej zastosowań.

6. Zapisz użytkownika.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do oprogramowania SAP HANA.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon platformy SAP HANA, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji. Przejdź do widoku katalogu, a **aplikacje dla przedsiębiorstw**. Wybierz **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **platformy SAP HANA**.

    ![Przypisz użytkownika](./media/saphana-tutorial/tutorial_saphana_app.png) 

3. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202] 

4. Wybierz **Dodaj** przycisku. W **Dodaj przydziału** okno dialogowe, wybierz opcję **użytkowników i grup**.

    ![Okienko Dodawanie przypisania][203]

5. W **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** w **użytkowników** listy.

6. Kliknij przycisk **wybierz** znajdujący się w **użytkowników i grup** okno dialogowe.

7. Wybierz **przypisać** znajdujący się w **Dodaj przydziału** okno dialogowe.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po wybraniu kafelka platformy SAP HANA w panelu dostępu, należy pobrać, że w automatycznym zalogowaniem w aplikacji SAP HANA.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/saphana-tutorial/tutorial_general_01.png
[2]: ./media/saphana-tutorial/tutorial_general_02.png
[3]: ./media/saphana-tutorial/tutorial_general_03.png
[4]: ./media/saphana-tutorial/tutorial_general_04.png

[100]: ./media/saphana-tutorial/tutorial_general_100.png

[200]: ./media/saphana-tutorial/tutorial_general_200.png
[201]: ./media/saphana-tutorial/tutorial_general_201.png
[202]: ./media/saphana-tutorial/tutorial_general_202.png
[203]: ./media/saphana-tutorial/tutorial_general_203.png


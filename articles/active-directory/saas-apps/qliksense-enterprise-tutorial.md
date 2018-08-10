---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą Qlik Sense Enterprise | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Qlik Sense Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 8c27e340-2b25-47b6-bf1f-438be4c14f93
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/06/2018
ms.author: jeedes
ms.openlocfilehash: a8816451b45171e0ba8cbd7acc937201c587c481
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627954"
---
# <a name="tutorial-azure-active-directory-integration-with-qlik-sense-enterprise"></a>Samouczek: Integracja usługi Azure Active Directory z usługą Qlik Sense Enterprise

W tym samouczku dowiesz się, jak zintegrować Qlik Sense przedsiębiorstwa za pomocą usługi Azure Active Directory (Azure AD).

Integrowanie Qlik Sense Enterprise z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Qlik Sense przedsiębiorstwa.
- Użytkowników, aby automatycznie uzyskać zalogowanych do Qlik Sense Enterprise (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD, Qlik Sense Enterprise, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Qlik Sense przedsiębiorstwa logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięcznej wersji próbnej tutaj: [oferta wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Qlik Sense przedsiębiorstwa z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Dodawanie Qlik Sense przedsiębiorstwa z galerii
Aby skonfigurować integrację Qlik Sense przedsiębiorstwa w usłudze Azure AD, należy dodać Qlik Sense przedsiębiorstwa z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Qlik Sense przedsiębiorstwa z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Qlik Sense Enterprise**, wybierz opcję **Qlik Sense Enterprise** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Qlik Sense przedsiębiorstwa na liście wyników](./media/qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Qlik Sense przedsiębiorstwa, w oparciu o nazwie "Britta Simon" użytkownika testowego.

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Qlik Sense Enterprise do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w programie Qlik Sense Enterprise musi zostać ustanowione.

W programie Qlik Sense Enterprise należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Qlik Sense przedsiębiorstwa, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on) ** — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) ** — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Qlik Sense Enterprise](#create-a-qlik-sense-enterprise-test-user) ** — aby odpowiednikiem Britta Simon w Qlik Sense przedsiębiorstwa, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) ** — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on) ** — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Qlik Sense przedsiębiorstwa.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne, Qlik Sense Enterprise, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Qlik Sense Enterprise** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_samlbase.png)

3. Na **Qlik Sense Enterprise domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Qlik Sense Enterprise domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<Qlik Sense Fully Qualifed Hostname>:4443/azure/hub`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:
    | |
    |--|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qlikpoc.com`|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qliksense.com`|
    | |

    c. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:

    `https://<Qlik Sense Fully Qualifed Hostname>:4443/samlauthn/`

    > [!NOTE]
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywisty adres URL logowania, identyfikator i adresu URL odpowiedzi, które opisano w dalszej części tego samouczka lub skontaktuj się z [zespołem pomocy technicznej Qlik Sense Enterprise Client](https://www.qlik.com/us/services/support) do uzyskania tych wartości.

4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_certificate.png)

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/qliksense-enterprise-tutorial/tutorial_general_400.png)

6. Przygotowanie pliku XML metadanych Federacji, dzięki czemu można przekazać, usługa Qlik Sense serwer.

    > [!NOTE]
    > Przed przekazaniem metadanych tożsamości serwera Usługa Qlik Sense, plik musi można edytować, aby usunąć informacje, aby zapewnić prawidłowe działanie między usługą Azure AD i usługa Qlik Sense serwera.

    ![QlikSense][qs24]

    a. Otwórz plik FederationMetaData.xml, który został pobrany z witryny Azure portal w edytorze tekstów.

    b. Wyszukaj wartość **deskryptora roli**.  Istnieją cztery wpisy (dwie pary otwierające i zamykające znaczniki elementów).

    c. Usuwanie tagów deskryptora roli i wszystkie informacje między pliku.

    d. Zapisz plik i przechowywać je w pobliżu do użycia w dalszej części tego dokumentu.

7. Przejdź do Qlik Sense Qlik Management Console (QMC) jako użytkownik, który można utworzyć konfiguracji wirtualnego serwera proxy.

8. W QMC, kliknij polecenie **wirtualnych serwerów proxy** elementu menu.

    ![QlikSense][qs6]

9. W dolnej części ekranu kliknij **Utwórz nową** przycisku.

    ![QlikSense][qs7]

10. Zostanie wyświetlony ekran edycji wirtualnych serwera proxy.  Po prawej stronie ekranu jest menu dla uwidaczniania opcje konfiguracji.

    ![QlikSense][qs9]

11. Za pomocą opcji menu identyfikacji zaznaczone wprowadź informacje identyfikujące konfiguracji serwera proxy wirtualnych Azure.

    ![QlikSense][qs8]  

    a. **Opis** pole jest przyjazną nazwę dla konfiguracji wirtualnego serwera proxy.  Wprowadź wartość zawierającą opis.

    b. **Prefiksu** pole identyfikuje punkt końcowy wirtualnego serwera proxy do łączenia się z usługa Qlik Sense za pomocą usługi Azure AD logowania jednokrotnego.  Wprowadź unikatowy prefiks nazwy dla tego wirtualnego serwera proxy.

    c. **Limit czasu bezczynności sesji (w minutach)** jest limit czasu dla połączeń przy użyciu tego wirtualnego serwera proxy.

    d. **Nazwę nagłówka pliku cookie sesji** jest nazwą pliku cookie przechowywanie identyfikator sesji dla sesji usługa Qlik Sense użytkownik otrzymuje po pomyślnym uwierzytelnieniu.  Ta nazwa musi być unikatowa.

12. Kliknij opcję menu uwierzytelniania, aby stał się widoczny.  Zostanie wyświetlony ekran uwierzytelniania.

    ![QlikSense][qs10]

    a. **Tryb dostępu anonimowego** listy rozwijanej określa anonimowych użytkowników może udostępniać usługa Qlik Sense za pośrednictwem wirtualnych serwera proxy.  Opcja domyślna to nie użytkownika anonimowego.

    b. **Metodę uwierzytelniania** listy rozwijanej określa użyje schematu uwierzytelniania wirtualnego serwera proxy.  Wybierz SAML z listy rozwijanej.  Dodatkowe opcje są wyświetlane w wyniku.

    c. W **pole identyfikatora URI hosta SAML**, dane wejściowe wprowadzenia przez użytkownika nazwa hosta dostęp usługa Qlik Sense przez ten serwer proxy do wirtualnego protokołu SAML.  Nazwa hosta jest identyfikator uri serwera, usługa Qlik Sense.

    d. W **identyfikator jednostki SAML**, wprowadź taką samą wartość wprowadzona dla pola identyfikatora URI hosta SAML.

    e. **Metadanych SAML dostawcy tożsamości** jest edytowany wcześniej w **edytowanie metadanych federacji z konfiguracji usługi Azure AD** sekcji.  **Przed przekazaniem metadanych tożsamości, należy edytować plik** usunąć informacje, aby zapewnić prawidłowe działanie między usługą Azure AD i usługa Qlik Sense serwera.  **Zapoznaj się z instrukcjami powyżej, jeśli go jeszcze nie można edytować.**  Jeśli plik został zmodyfikowany. Kliknij przycisk Przeglądaj i wybierz plik metadanych edytowanych do przekazania go do konfiguracji wirtualnego serwera proxy.

    f. Wprowadź odwołanie do schematu lub nazwa atrybutu do reprezentowania atrybutów SAML **UserID** usługi Azure AD wysyła do serwera Usługa Qlik Sense.  Dokumentacja informacje schematu są dostępne w konfiguracji po ekrany aplikacji platformy Azure.  Aby użyć nazwy atrybutu, wprowadź `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    g. Wprowadź wartość w **katalogu użytkownika** , jest dołączana do użytkowników podczas uwierzytelniania z serwerem usługa Qlik Sense za pośrednictwem usługi Azure AD.  Zapisane na stałe wartości muszą być ujęte w **nawiasy kwadratowe []**.  Aby użyć atrybutu wysyłane potwierdzenie Azure AD SAML, wprowadź nazwę atrybutu, w tym polu tekstowym **bez** nawiasami kwadratowymi.

    h. **Algorytm podpisywania SAML** ustawia dostawcy (w tym przypadku server usługa Qlik Sense) w certyfikacie podpisywania Konfigurowanie wirtualnego serwera proxy.  Jeśli usługa Qlik Sense serwer używa zaufany certyfikat wygenerowany za pomocą Microsoft Enhanced RSA and AES Cryptographic Provider, zmień algorytm podpisywania protokołu SAML do **algorytmu SHA-256**.

    i. W sekcji mapowania atrybutów SAML umożliwia dodatkowe atrybuty, takie jak grupy do wysłania do użytku w regułach zabezpieczeń, aby usługa Qlik Sense.

13. Kliknij pozycję **RÓWNOWAŻENIA obciążenia** opcję menu, aby stał się widoczny.  Zostanie wyświetlony ekran równoważenia obciążenia.

    ![QlikSense][qs11]

14. Kliknij pozycję **Dodaj nowy węzeł serwera** przycisku, aparat wybierz węzeł lub węzłów, usługa Qlik Sense wyśle na potrzeby równoważenia obciążenia, a następnie kliknij przycisk sesji **Dodaj** przycisku.

    ![QlikSense][qs12]

15. Kliknij opcję menu Zaawansowane, aby stał się widoczny. Zostanie wyświetlony ekran Zaawansowane.

    ![QlikSense][qs13]

    Białą listę hostów identyfikuje nazw hostów, które są akceptowane, podczas nawiązywania połączenia z serwerem usługa Qlik Sense.  **Wprowadź nazwę hosta, który użytkownicy będą wpisywać, podczas nawiązywania połączenia z serwerem usługa Qlik Sense.** Nazwa hosta jest taka sama wartość jak identyfikatora uri hosta SAML bez https://.

16. Kliknij przycisk **Zastosuj** przycisku.

    ![QlikSense][qs14]

17. Kliknij przycisk OK, aby zaakceptować komunikat ostrzegawczy informujący o tym, serwery proxy połączone z wirtualnego serwera proxy zostanie ponownie uruchomiona.

    ![QlikSense][qs15]

18. Po prawej stronie ekranu zostanie wyświetlone menu elementy skojarzone.  Kliknij pozycję **proxy** opcji menu.

    ![QlikSense][qs16]

19. Zostanie wyświetlony ekran serwera proxy.  Kliknij przycisk **łącze** znajdujący się u dołu, aby połączyć serwer proxy wirtualnego serwera proxy.

    ![QlikSense][qs17]

20. Wybierz węzeł serwera proxy, który będzie obsługiwać połączenia tej wirtualnej serwera proxy, a następnie kliknij przycisk **łącze** przycisku.  Po połączeniu, serwer proxy będzie wyświetlane w obszarze skojarzone serwery proxy.

    ![QlikSense][qs18]
  
    ![QlikSense][qs19]

21. Po około pięciu do dziesięciu sekund zostanie wyświetlony komunikat QMC odświeżania.  Kliknij przycisk **Odśwież QMC** przycisku.

    ![QlikSense][qs20]

22. Po odświeżeniu QMC kliknij **proxy wirtualnej** elementu menu. Nowy wpis wirtualnego serwera proxy protokołu SAML znajduje się w tabeli na ekranie.  Jednym kliknięciem w zapisie wirtualnego serwera proxy.

    ![QlikSense][qs51]

23. W dolnej części ekranu uaktywni się przycisk Pobierz SP metadanych.  Kliknij przycisk **SP Pobieranie metadanych** przycisk, aby zapisać metadane do pliku.

    ![QlikSense][qs52]

24. Otwórz plik metadanych sp.  Obserwuj **entityID** wejścia i **AssertionConsumerService** wpisu.  Te wartości są równoważne **identyfikator**, **adres URL logowania** i **adres URL odpowiedzi** w konfiguracji aplikacji usługi Azure AD. Wklej te wartości w **Qlik Sense Enterprise domena i adresy URL** sekcji w konfiguracji aplikacji usługi Azure AD, jeśli ich nie pasują do, a następnie należy zastąpić je w Kreatorze konfiguracji aplikacji usługi Azure AD.

    ![QlikSense][qs53]

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

   ![Przycisk usługi Azure Active Directory](./media/qliksense-enterprise-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

   !["Użytkownicy i grupy" i "All users" linki](./media/qliksense-enterprise-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

   ![Przycisk Dodaj](./media/qliksense-enterprise-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

   ![Okno dialogowe użytkownika](./media/qliksense-enterprise-tutorial/create_aaduser_04.png)

   a. W **nazwa** wpisz **BrittaSimon**.

   b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

   c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

   d. Kliknij pozycję **Utwórz**.

### <a name="create-a-qlik-sense-enterprise-test-user"></a>Tworzenie użytkownika testowego Qlik Sense przedsiębiorstwa

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Qlik Sense przedsiębiorstwa. Praca z [zespołem pomocy technicznej Qlik Sense Enterprise Client](https://www.qlik.com/us/services/support) Aby dodać użytkowników na platformie Qlik Sense przedsiębiorstwa. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Qlik Sense przedsiębiorstwa.

![Przypisanie roli użytkownika][200]

**Aby przypisać Britta Simon Qlik Sense przedsiębiorstwa, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **Qlik Sense Enterprise**.

    ![Link Qlik Sense przedsiębiorstwa, na liście aplikacji](./media/qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Qlik Sense przedsiębiorstwa, w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Qlik Sense przedsiębiorstwa.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/qliksense-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/qliksense-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/qliksense-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/qliksense-enterprise-tutorial/tutorial_general_04.png

[100]: ./media/qliksense-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/qliksense-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/qliksense-enterprise-tutorial/tutorial_general_201.png
[202]: ./media/qliksense-enterprise-tutorial/tutorial_general_202.png
[203]: ./media/qliksense-enterprise-tutorial/tutorial_general_203.png

[qs6]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs24]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs51]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png
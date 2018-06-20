---
title: 'Samouczek: Integracji Azure Active Directory z przedsiębiorstwa znaczeniu Qlik | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Qlik przedsiębiorstwa znaczeniu.
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
ms.date: 07/26/2017
ms.author: jeedes
ms.openlocfilehash: 09e1850956150eab6bd7fea365a17c12fa15bdc3
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36230104"
---
# <a name="tutorial-azure-active-directory-integration-with-qlik-sense-enterprise"></a>Samouczek: Integracji Azure Active Directory z przedsiębiorstwa znaczeniu Qlik

Z tego samouczka dowiesz się sposobu integracji przedsiębiorstwa znaczeniu Qlik z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD przedsiębiorstwa znaczeniu Qlik zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Qlik przedsiębiorstwa znaczeniu.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do przedsiębiorstwa znaczeniu Qlik (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Qlik przedsiębiorstwa znaczeniu, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Przedsiębiorstwa znaczeniu Qlik logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna tutaj: [oferta wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie przedsiębiorstwa znaczeniu Qlik z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Dodawanie przedsiębiorstwa znaczeniu Qlik z galerii
Aby skonfigurować integrację usługi Azure AD Qlik przedsiębiorstwa znaczeniu, należy dodać przedsiębiorstwa znaczeniu Qlik z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać przedsiębiorstwa znaczeniu Qlik z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **przedsiębiorstwa znaczeniu Qlik**, wybierz pozycję **przedsiębiorstwa znaczeniu Qlik** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Przedsiębiorstwa znaczeniu Qlik na liście wyników](./media/qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Qlik przedsiębiorstwa znaczeniu, w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w przedsiębiorstwie znaczeniu Qlik jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i danemu użytkownikowi w przedsiębiorstwie znaczeniu Qlik musi określone.

W Qlik przedsiębiorstwa znaczeniu, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Qlik przedsiębiorstwa znaczeniu, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego przedsiębiorstwa znaczeniu Qlik](#create-a-qlik-sense-enterprise-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Qlik znaczeniu przedsiębiorstwo, które jest połączone z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne do aplikacji przedsiębiorstwa znaczeniu Qlik.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Qlik przedsiębiorstwa znaczeniu, wykonaj następujące czynności:**

1. W portalu Azure na **przedsiębiorstwa znaczeniu Qlik** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_samlbase.png)

3. Na **adresy URL i domeny przedsiębiorstwa znaczeniu Qlik** sekcji, wykonaj następujące czynności:

    ![Adresy URL i domeny przedsiębiorstwa znaczeniu Qlik pojedynczy informacje logowania jednokrotnego](./media/qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<Qlik Sense Fully Qualifed Hostname>:443//samlauthn/`
    
    > [!NOTE]
    > Należy zwrócić uwagę ukośnika na końcu tego identyfikatora URI. Jest wymagane.
    
    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca:
    | |
    |--|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qlikpoc.com`|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qliksense.com`|

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Zaktualizować te wartości z rzeczywisty adres URL logowania i identyfikator, które opisano szczegółowo w dalszej części tego samouczka lub skontaktuj się z [zespołem pomocy technicznej klienta przedsiębiorstwa znaczeniu Qlik](https://www.qlik.com/us/services/support) uzyskać te wartości. 

4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/qliksense-enterprise-tutorial/tutorial_general_400.png)

6. Przygotuj plik XML metadanych Federacji, dzięki czemu można przekazać który serwerowi Qlik znaczeniu.
   
    > [!NOTE]
    > Przed przekazaniem metadanych IdP serwerowi Qlik znaczeniu, plik musi można edytować, aby usunąć informacje, aby zapewnić poprawne działanie między usługą Azure AD i serwer Qlik znaczeniu.
    
    ![QlikSense][qs24]
   
    a. Otwórz plik FederationMetaData.xml, który został pobrany z portalu Azure w edytorze tekstów.
   
    b. Wyszukaj wartość **RoleDescriptor**.  Istnieją cztery wpisy (dwie pary otwierające i zamykające znaczniki elementów).
   
    c. Usuwanie tagów RoleDescriptor i wszystkie informacje między z pliku.
   
    d. Zapisz plik i zachować w pobliżu do użytku w dalszej części tego dokumentu.

7. Przejdź do Qlik znaczeniu Qlik Management Console (QMC) jako użytkownik, który można utworzyć konfiguracji wirtualnego serwera proxy.

8. W QMC, kliknij polecenie **wirtualnego proxy** elementu menu.
   
    ![QlikSense][qs6] 

9. W dolnej części ekranu kliknij **Utwórz nowy** przycisku.
   
    ![QlikSense][qs7]

10. Zostanie wyświetlony ekran edycji proxy wirtualnej.  Po prawej stronie ekranu jest menu do wybierania opcji konfiguracji widoczne.
   
    ![QlikSense][qs9]

11. Z zaznaczoną opcją menu identyfikacji wprowadź informacje identyfikujące konfiguracji serwera proxy wirtualnych Azure.
    
    ![QlikSense][qs8]  
    
    a. **Opis** pole jest przyjazną nazwę dla konfiguracji wirtualnego serwera proxy.  Wprowadź wartość opis.
    
    b. **Prefiksu** pole identyfikuje punkt końcowy wirtualnego serwera proxy w celu nawiązania znaczeniu Qlik z usługi Azure AD rejestracji jednokrotnej.  Wprowadź nazwę unikatowy prefiks dla tego wirtualnego serwera proxy.
    
    c. **Limit czasu bezczynności sesji (w minutach)** jest limit czasu dla połączeń za pośrednictwem tego wirtualnego serwera proxy.
    
    d. **Nazwy nagłówka pliku cookie sesji** jest nazwą pliku cookie przechowywania identyfikatora sesji dla sesji Qlik znaczeniu, użytkownik otrzymuje po pomyślnym uwierzytelnieniu.  Ta nazwa musi być unikatowa.

12. Kliknij opcję menu uwierzytelniania, aby go wyświetlić.  Zostanie wyświetlony ekran uwierzytelniania.
    
    ![QlikSense][qs10]
    
    a. **Tryb dostępu anonimowego** listy rozwijanej określa użytkowników anonimowych może udostępniać znaczeniu Qlik za pośrednictwem wirtualnej serwera proxy.  Opcją domyślną jest nie użytkownika anonimowego.
    
    b. **Metodę uwierzytelniania** listy rozwijanej określa użyje schemat uwierzytelniania wirtualnego serwera proxy.  Z listy rozwijanej wybierz SAML.  W związku z tym pojawi się więcej opcji.
    
    c. W **pola identyfikatora URI hosta SAML**, wprowadzania nazwy hosta wprowadzania dostęp znaczeniu Qlik za pośrednictwem tego serwera proxy do wirtualnego SAML.  Nazwa hosta jest identyfikator uri serwera Qlik znaczeniu.
    
    d. W **identyfikator jednostki SAML**, wprowadzić tę samą wartość wprowadzona w polu identyfikatora URI hosta SAML.
    
    e. **Metadanych SAML IdP** jest edytowany wcześniej w pliku **edytowanie metadanych federacji z konfiguracji usługi Azure AD** sekcji.  **Przed przekazaniem metadanych IdP plik musi można edytowane** usunąć informacje, aby zapewnić poprawne działanie między usługą Azure AD i serwer Qlik znaczeniu.  **Zapoznaj się instrukcje powyżej, jeśli go jeszcze nie można edytować.**  Jeśli plik został zmodyfikowany. Kliknij przycisk Przeglądaj i wybierz plik metadanych edytowanych go przekazać do konfiguracji wirtualnego serwera proxy.
    
    f. Wprowadź odwołanie do atrybutu name lub schemat dla reprezentujący atrybut SAML **UserID** usługi Azure AD, wysyła do serwera Qlik znaczeniu.  Informacje o odwołaniu schematu jest dostępna w konfiguracji wpisu ekrany aplikacji Azure.  Aby użyć atrybutu name, wprowadź `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    g. Wprowadź wartość dla **katalogu użytkownika** która jest dołączana do użytkowników podczas uwierzytelniania serwera znaczeniu Qlik za pośrednictwem usługi Azure AD.  Wartości zapisane na stałe muszą być ujęte w **nawiasy kwadratowe []**.  Aby użyć atrybutu wysyłane potwierdzenia języka SAML programu Azure AD, wprowadź nazwę atrybutu, w tym polu tekstowym **bez** nawiasy kwadratowe.
    
    h. **Algorytm podpisywania SAML** ustawia certyfikatu dostawcy (w tym wielkość server znaczeniu Qlik) usługi podpisywania konfiguracji wirtualnego serwera proxy.  Jeśli serwer znaczeniu Qlik używa zaufanego certyfikatu wygenerowanych przy użyciu Microsoft Enhanced RSA and AES Cryptographic Provider, zmień algorytm podpisywania SAML do **algorytmu SHA-256**.
    
    i. W sekcji mapowanie atrybutu SAML umożliwia dodatkowych atrybutów, takich jak grupy do wysłania do wykrywania Qlik do użytku w zasadach zabezpieczeń.

13. Polecenie **RÓWNOWAŻENIA obciążenia** opcji menu, aby go wyświetlić.  Zostanie wyświetlony ekran równoważenia obciążenia.
    
    ![QlikSense][qs11]

14. Polecenie **Dodaj nowy węzeł serwera** przycisku, wybierz aparat węzła lub węzłów znaczeniu Qlik wyśle sesji do celów równoważenia obciążenia, a następnie kliknij przycisk **Dodaj** przycisku.
    
    ![QlikSense][qs12]

15. Kliknij opcję menu Zaawansowane, aby go wyświetlić. Zostanie wyświetlony ekran Zaawansowane.
    
    ![QlikSense][qs13]
    
    Białe listy hostów identyfikuje nazwy hostów, które są akceptowane, gdy połączenie z serwerem Qlik znaczeniu.  **Wprowadź nazwę hosta, którego użytkownicy będą wpisywać podczas łączenia z serwerem Qlik znaczeniu.** Nazwa hosta jest taka sama wartość jak identyfikatora uri hosta SAML bez https://.

16. Kliknij przycisk **Zastosuj** przycisku.
    
    ![QlikSense][qs14]

17. Kliknij przycisk OK, aby zaakceptować komunikat ostrzegawczy z informacją, serwery proxy połączone z wirtualnego serwera proxy zostanie uruchomiona ponownie.
    
    ![QlikSense][qs15]

18. Po prawej stronie ekranu zostanie wyświetlone menu elementy skojarzone.  Polecenie **proxy** opcji menu.
    
    ![QlikSense][qs16]

19. Zostanie wyświetlony ekran serwera proxy.  Kliknij przycisk **łącze** znajdujący się u dołu do połączenia serwera proxy do wirtualnego serwera proxy.
    
    ![QlikSense][qs17]

20. Wybierz węzeł serwera proxy, który będzie obsługiwać tego połączenia wirtualnego serwera proxy, a następnie kliknij przycisk **łącze** przycisku.  Po połączeniu, serwer proxy, będą wyświetlane w skojarzone serwery proxy.
    
    ![QlikSense][qs18]
  
    ![QlikSense][qs19]

21. Po około pięciu do dziesięciu sekund zostanie wyświetlony komunikat QMC odświeżania.  Kliknij przycisk **Odśwież QMC** przycisku.
    
    ![QlikSense][qs20]

22. Po odświeżeniu QMC kliknij **proxy wirtualnej** elementu menu. Nowy wpis wirtualnego serwera proxy SAML to wymienione w tabeli na ekranie.  Kliknij wpis wirtualnego serwera proxy.
    
    ![QlikSense][qs51]

23. W dolnej części ekranu zostanie aktywowany, przycisk Pobierz SP metadanych.  Kliknij przycisk **SP pobrać metadanych** przycisk, aby zapisać w pliku metadanych.
    
    ![QlikSense][qs52]

24. Otwórz plik metadanych sp.  Obserwować **entityID** wejścia i **AssertionConsumerService** wpisu.  Te wartości są równoważne **identyfikator** i **Zaloguj się na adres URL** w konfiguracji aplikacji usługi Azure AD. Wklej te wartości w **adresy URL i domeny przedsiębiorstwa znaczeniu Qlik** sekcji w konfiguracji aplikacji usługi Azure AD, jeśli ich są niezgodne, a następnie należy zastąpić je w Kreatorze konfiguracji aplikacji Azure AD.
    
    ![QlikSense][qs53]

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

   ![Przycisk usługi Azure Active Directory](./media/qliksense-enterprise-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

   !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/qliksense-enterprise-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

   ![Przycisk Dodaj](./media/qliksense-enterprise-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

   ![Okno dialogowe użytkownika](./media/qliksense-enterprise-tutorial/create_aaduser_04.png)

   a. W **nazwa** wpisz **BrittaSimon**.

   b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

   c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

   d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-qlik-sense-enterprise-test-user"></a>Tworzenie użytkownika testowego przedsiębiorstwa znaczeniu Qlik

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w Qlik przedsiębiorstwa znaczeniu. Praca z [zespołem pomocy technicznej klienta przedsiębiorstwa znaczeniu Qlik](https://www.qlik.com/us/services/support) Aby dodać użytkowników na platformie Qlik przedsiębiorstwa znaczeniu. Użytkownicy muszą utworzyć i aktywowana, aby użyć rejestracji jednokrotnej. 

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do Qlik przedsiębiorstwa znaczeniu.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Qlik przedsiębiorstwa znaczeniu, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **przedsiębiorstwa znaczeniu Qlik**.

    ![Łącze przedsiębiorstwa znaczeniu Qlik na liście aplikacji](./media/qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Qlik przedsiębiorstwa znaczeniu w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji przedsiębiorstwa znaczeniu Qlik. 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

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


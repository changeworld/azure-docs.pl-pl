---
title: 'Samouczek: Integracji Azure Active Directory z platformy w chmurze SAP | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i SAP platformy w chmurze.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: bd398225-8bd8-4697-9a44-af6e6679113a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: fed3e6c27e5921a293a9431500cfbfbe21dbd5f4
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35942806"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Samouczek: Integracji Azure Active Directory z platformy w chmurze SAP

Z tego samouczka dowiesz się integrowanie SAP platformy w chmurze w usłudze Azure Active Directory (Azure AD).

Integrowanie SAP platformy w chmurze z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do programu SAP platformy w chmurze.
- Umożliwia użytkownikom automatycznie pobrać zalogowane platformy Chmurowej SAP (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z platformy w chmurze SAP, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Platforma chmury SAP logowanie jednokrotne włączone subskrypcji

Ten samouczek użytkowników usługi Azure AD, zostały przypisane do platformy w chmurze SAP będzie można funkcji logowania jednokrotnego do aplikacji przy użyciu [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md).

>[!IMPORTANT]
>Należy wdrożyć własną aplikację lub subskrybować aplikację na Twoim koncie platformy w chmurze SAP testowanie funkcji logowania jednokrotnego w. W tym samouczku aplikacja jest wdrażana w ramach konta.
> 

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie SAP platformy w chmurze z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>Dodawanie SAP platformy w chmurze z galerii
Aby skonfigurować integrację usługi Azure AD platformy w chmurze SAP, należy dodać SAP platformy w chmurze z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać SAP platformy w chmurze z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **platformy w chmurze SAP**, wybierz pozycję **SAP Cloud Platform** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Platforma chmury SAP na liście wyników](./media/sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z platformy w chmurze SAP w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w SAP platformy w chmurze jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i danemu użytkownikowi SAP chmury platformy musi określone.

Platforma chmury SAP, przypisywanie wartości **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z platformy w chmurze programu SAP, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego platformy w chmurze SAP](#create-a-sap-cloud-platform-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta SAP chmury platformy, która jest połączona z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji platformy w chmurze SAP.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z platformy w chmurze SAP, wykonaj następujące czynności:**

1. W portalu Azure na **platformy w chmurze SAP** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_samlbase.png)

3. Na **adresy URL i SAP Cloud Platform domeny** sekcji, wykonaj następujące czynności:

    ![Adresy URL i SAP Cloud Platform domeny pojedynczy informacje logowania jednokrotnego](./media/sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_url.png)

    a. W **na adres URL logowania** tekstowym, wpisz adres URL używany przez użytkowników do logowania się do sieci **platformy w chmurze SAP** aplikacji. To jest adres URL konta określonego zasobu chronionego w aplikacji platformy w chmurze SAP. Adres URL jest oparty na następujący wzór: `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
     >[!NOTE]
     >To jest adres URL w aplikacji platformy w chmurze SAP, która wymaga uwierzytelnienia użytkownika.
     > 

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |

    b. W **identyfikator** pole tekstowe zapewni platformy chmury SAP, wpisz adres URL przy użyciu jednej z następujących wzorców: 

    | |
    |--|
    | `https://hanatrial.ondemand.com/<instancename>` |
    | `https://hana.ondemand.com/<instancename>` |
    | `https://us1.hana.ondemand.com/<instancename>` |
    | `https://ap1.hana.ondemand.com/<instancename>` |

    c. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca:

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>` |

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania, identyfikator i adres URL odpowiedzi, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej SAP Cloud Platform klient](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html) uzyskać adres URL logowania i identyfikator. Adres URL odpowiedzi można uzyskać z sekcji zarządzania zaufania, który znajduje się w dalszej części tego samouczka.
    > 
     
4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/sap-hana-cloud-platform-tutorial/tutorial_general_400.png)

6. W oknie przeglądarki innej witryny sieci web, zaloguj się do panelu sterowania platformy chmury SAP w `https://account.<landscape host>.ondemand.com/cockpit`(na przykład: https://account.hanatrial.ondemand.com/cockpit).

7. Kliknij przycisk **zaufania** kartę.
   
    ![Zaufania](./media/sap-hana-cloud-platform-tutorial/ic790800.png "zaufania")

8. W sekcji Zarządzanie zaufania w obszarze **lokalnego dostawcy usług**, wykonaj następujące czynności:

    ![Zaufania zarządzania](./media/sap-hana-cloud-platform-tutorial/ic793931.png "zaufania zarządzania")
   
    a. Kliknij pozycję **Edytuj**.

    b. Jako **typu konfiguracji**, wybierz pozycję **niestandardowy**.

    c. Jako **lokalna nazwa dostawcy**, pozostaw wartość domyślną. Skopiuj tę wartość i wklej ją do **identyfikator** w konfiguracji usługi Azure AD dla programu SAP platformy w chmurze.

    d. Aby wygenerować **klucza podpisywania** i **certyfikat podpisywania** parę kluczy, kliknij przycisk **generowanie pary klucz**.

    e. Jako **główna propagacji**, wybierz pozycję **wyłączone**.

    f. Jako **siły uwierzytelniania**, wybierz pozycję **wyłączone**.

    g. Kliknij pozycję **Zapisz**.

9. Po zapisaniu **lokalnego dostawcy usług** ustawienia, wykonaj następujące polecenie, aby uzyskać adres URL odpowiedzi:
   
    ![Pobranie metadanych](./media/sap-hana-cloud-platform-tutorial/ic793930.png "pobranie metadanych")

    a. Pobierz plik metadanych platformy w chmurze SAP, klikając **pobrać metadanych**.

    b. Otwórz pobrany plik XML metadanych platformy w chmurze SAP, a następnie zlokalizuj **ns3:AssertionConsumerService** tagu.
 
    c. Skopiuj wartość **lokalizacji** atrybutu, a następnie wklej go do **adres URL odpowiedzi** w konfiguracji usługi Azure AD dla programu SAP platformy w chmurze.

10. Kliknij przycisk **zaufany dostawca tożsamości** , a następnie kliknij pozycję **dodać zaufanego dostawcę tożsamości**.
   
    ![Zaufania zarządzania](./media/sap-hana-cloud-platform-tutorial/ic790802.png "zaufania zarządzania")
   
    >[!NOTE]
    >Do zarządzania listą zaufanych dostawców tożsamości, musisz wybrany typ konfiguracji niestandardowej w sekcji lokalnego dostawcy usług. Typ konfiguracji domyślnej masz nieedytowalnego i niejawne zaufania z usługą identyfikator SAP. Dla żadnego nie masz żadnych ustawień zaufania.
    > 
    > 

11. Kliknij przycisk **ogólne** , a następnie kliknij pozycję **Przeglądaj** można przekazać pliku pobranego metadanych.
    
    ![Zaufania zarządzania](./media/sap-hana-cloud-platform-tutorial/ic793932.png "zaufania zarządzania")
    
    >[!NOTE]
    >Po przekazaniu pliku metadanych wartości **URL rejestracji jednokrotnej**, **pojedynczego adresu URL wylogowania**, i **certyfikat podpisywania** są wypełniane automatycznie.
    > 
     
12. Kliknij kartę **Atrybuty**.

13. Na **atrybuty** karcie, wykonaj następujące kroki:
    
    ![Atrybuty](./media/sap-hana-cloud-platform-tutorial/ic790804.png "atrybutów") 

    a. Kliknij przycisk **atrybutu Add Assertion-Based**, a następnie dodaj następujące atrybuty oparte na potwierdzenia:
       
    | Atrybut potwierdzenia | Atrybut podmiotu |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |Imię |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |nazwisko |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |wyślij wiadomość e-mail |
   
     >[!NOTE]
     >Konfiguracja atrybutów, zależy od tego, jak aplikacje na punkt połączenia usługi są tworzone, oznacza to, które atrybutów oczekiwane w odpowiedzi SAML i w których nazwa (atrybut podmiotu zabezpieczeń) będą uzyskiwać dostęp do tego atrybutu w kodzie.
     > 
    
    b. **Domyślny atrybut** na zrzucie ekranu jest tylko do celów ilustracyjnych. Nie jest wymagane dokonanie scenariuszu działać.  
 
    c. Nazwy i wartości dla **atrybut podmiotu zabezpieczeń** na zrzucie ekranu pokazano zależą od sposobu zaprojektowano w aplikacji. Istnieje możliwość, że aplikacja wymaga innego mapowania.

### <a name="assertion-based-groups"></a>Grupy oparte na potwierdzenie

Ten opcjonalny krok można skonfigurować grupy oparte na potwierdzenie dla usługi Azure Active Directory dostawcy tożsamości.

Na SAP platformy w chmurze przy użyciu grup umożliwia dynamiczne przypisywanie co najmniej jednego użytkownika do co najmniej jedną rolę w aplikacjach platformy w chmurze SAP, określany przez wartości atrybutów w potwierdzenia SAML 2.0. 

Na przykład, jeśli potwierdzenie zawiera atrybut "*kontraktu = tymczasowego*", może być wszystkich użytkowników mają zostać dodane do grupy"*tymczasowego*". Grupa "*tymczasowego*" może zawierać co najmniej jedną rolę z co najmniej jednej aplikacji wdrożonych na koncie platformy w chmurze SAP.
 
Grupy oparte na potwierdzenie należy użyć jednocześnie przypisać wielu użytkowników do co najmniej jedną rolę aplikacji na koncie platformy w chmurze SAP. Jeśli chcesz przypisać tylko jednego lub mała liczba użytkowników do określonych ról, zalecane jest przypisywania do nich bezpośrednio w programie "**autoryzacje**" kartę platformy w chmurze SAP Panelu sterowania.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/sap-hana-cloud-platform-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/sap-hana-cloud-platform-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/sap-hana-cloud-platform-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/sap-hana-cloud-platform-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-sap-cloud-platform-test-user"></a>Tworzenie użytkownika testowego SAP platformy w chmurze

Aby włączyć użytkowników usługi Azure AD zalogować się do platformy w chmurze SAP, należy przypisać role na platformie chmury SAP do nich.

**Aby przypisać rolę do użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do Twojego **platformy w chmurze SAP** Panelu sterowania.

2. Wykonaj następujące czynności:
   
    ![Autoryzacje](./media/sap-hana-cloud-platform-tutorial/ic790805.png "zezwolenia")
   
    a. Kliknij przycisk **autoryzacji**.

    b. Kliknij przycisk **użytkowników** kartę.

    c. W **użytkownika** tekstowym, wpisz adres e-mail użytkownika.

    d. Kliknij przycisk **przypisać** można przypisać do roli użytkownika.

    e. Kliknij pozycję **Zapisz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do programu SAP platformy w chmurze.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta platformy w chmurze SAP, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **platformy w chmurze SAP**.

    ![Łącze platformy w chmurze SAP na liście aplikacji](./media/sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka SAP Cloud Platform w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji platformy w chmurze SAP.


## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sap-hana-cloud-platform-tutorial/tutorial_general_01.png
[2]: ./media/sap-hana-cloud-platform-tutorial/tutorial_general_02.png
[3]: ./media/sap-hana-cloud-platform-tutorial/tutorial_general_03.png
[4]: ./media/sap-hana-cloud-platform-tutorial/tutorial_general_04.png

[100]: ./media/sap-hana-cloud-platform-tutorial/tutorial_general_100.png

[200]: ./media/sap-hana-cloud-platform-tutorial/tutorial_general_200.png
[201]: ./media/sap-hana-cloud-platform-tutorial/tutorial_general_201.png
[202]: ./media/sap-hana-cloud-platform-tutorial/tutorial_general_202.png
[203]: ./media/sap-hana-cloud-platform-tutorial/tutorial_general_203.png


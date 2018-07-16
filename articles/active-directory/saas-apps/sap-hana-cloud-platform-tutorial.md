---
title: 'Samouczek: Integracja usługi Azure Active Directory z platformy SAP Cloud Platform | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i SAP Cloud Platform.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: bd398225-8bd8-4697-9a44-af6e6679113a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: 176ed1718818b24866620d86d7eff4d9af1d378f
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39050878"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Samouczek: Integracja usługi Azure Active Directory z platformy SAP Cloud Platform

W tym samouczku dowiesz się, jak zintegrować SAP Cloud Platform w usłudze Azure Active Directory (Azure AD).

Integrowanie platformy usług w chmurze SAP z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do platformy SAP Cloud Platform.
- Użytkowników, aby automatycznie uzyskać zalogowanych do platformy SAP Cloud Platform (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą platformy SAP Cloud Platform, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Platforma usług w chmurze SAP logowanie jednokrotne włączone subskrypcji

Po ukończeniu tego samouczka użytkowników usługi Azure AD została przypisana do platformy SAP Cloud Platform będzie można logowania jednokrotnego do aplikacji przy użyciu [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

>[!IMPORTANT]
>Należy wdrożyć własną aplikację lub subskrybować aplikacji na Twoim koncie platformy SAP Cloud Platform, aby przetestować logowanie jednokrotne. W tym samouczku aplikacja jest wdrażana w ramach konta.
> 

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie SAP Cloud Platform za pomocą galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>Dodawanie SAP Cloud Platform za pomocą galerii
Aby skonfigurować integrację programu SAP Cloud Platform w usłudze Azure AD, należy dodać SAP Cloud Platform z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać SAP Cloud Platform z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **SAP Cloud Platform**, wybierz opcję **SAP Cloud Platform** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![SAP Cloud Platform na liście wyników](./media/sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą oparte na użytkownika testu o nazwie "Britta Simon" platformy SAP Cloud Platform.

Dla logowania jednokrotnego do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w SAP Cloud Platform jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w SAP Cloud Platform musi zostać nawiązane.

W systemie SAP Cloud Platform przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą platformy SAP Cloud Platform, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego SAP Cloud Platform](#create-a-sap-cloud-platform-test-user)**  — aby odpowiednikiem Britta Simon w platformy SAP Cloud Platform jest połączony z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji SAP Cloud Platform.

**Aby skonfigurować usługi Azure AD logowanie jednokrotne na platformie SAP Cloud Platform, wykonaj następujące czynności:**

1. W witrynie Azure portal na **SAP Cloud Platform** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_samlbase.png)

3. Na **SAP Cloud Platform domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![SAP Cloud Platform domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_url.png)

    a. W **na adres URL logowania** pole tekstowe, wpisz adres URL używany przez użytkowników, aby zalogować się do swojej **SAP Cloud Platform** aplikacji. To jest adres URL konta zasobu chronionego w aplikacji SAP Cloud Platform. Adres URL jest oparty na następującym wzorcem: `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
     >[!NOTE]
     >To jest adres URL do aplikacji SAP Cloud Platform, która wymaga uwierzytelnienia użytkownika.
     > 

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |

    b. W **identyfikator** textbox zapewni platformy chmury SAP wpisz adres URL przy użyciu jednej z następujących wzorców: 

    | |
    |--|
    | `https://hanatrial.ondemand.com/<instancename>` |
    | `https://hana.ondemand.com/<instancename>` |
    | `https://us1.hana.ondemand.com/<instancename>` |
    | `https://ap1.hana.ondemand.com/<instancename>` |

    c. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:

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
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywisty adres URL logowania, identyfikator i adres URL odpowiedzi. Skontaktuj się z pomocą [zespołem pomocy technicznej SAP Cloud Platform Client](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html) można pobrać adresu URL logowania jednokrotnego i identyfikator. Adres URL odpowiedzi można uzyskać z sekcję zarządzania zaufania, co zostało wyjaśnione w dalszej części tego samouczka.
    > 
     
4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_certificate.png) 

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/sap-hana-cloud-platform-tutorial/tutorial_general_400.png)

6. W oknie przeglądarki internetowej innej, zaloguj się do panelu sterowania SAP Cloud Platform na `https://account.<landscape host>.ondemand.com/cockpit`(na przykład: https://account.hanatrial.ondemand.com/cockpit).

7. Kliknij przycisk **zaufania** kartę.
   
    ![Zaufanie](./media/sap-hana-cloud-platform-tutorial/ic790800.png "zaufania")

8. W sekcji Zarządzanie zaufania w obszarze **lokalnego usługodawcy**, wykonaj następujące czynności:

    ![Zaufanie zarządzania](./media/sap-hana-cloud-platform-tutorial/ic793931.png "zaufania zarządzania")
   
    a. Kliknij pozycję **Edytuj**.

    b. Jako **typu konfiguracji**, wybierz opcję **niestandardowe**.

    c. Jako **lokalna nazwa dostawcy**, pozostaw wartość domyślną. Skopiuj tę wartość i wklej go do **identyfikator** pola w konfiguracji usługi Azure AD dla platformy SAP Cloud Platform.

    d. Aby wygenerować **klucza podpisywania** i **certyfikat podpisywania** pary kluczy, kliknij przycisk **generowania pary klucza**.

    e. Jako **jednostki propagacji**, wybierz opcję **wyłączone**.

    f. Jako **siły uwierzytelniania**, wybierz opcję **wyłączone**.

    g. Kliknij pozycję **Zapisz**.

9. Po zapisaniu **lokalnego usługodawcy** ustawienia, wykonaj następujące polecenie, aby uzyskać adres URL odpowiedzi:
   
    ![Pobierz metadane](./media/sap-hana-cloud-platform-tutorial/ic793930.png "Pobierz metadane")

    a. Pobierz plik metadanych SAP Cloud Platform, klikając **Get Metadata**.

    b. Otwórz pobrany plik XML metadanych SAP Cloud Platform, a następnie zlokalizuj **ns3:AssertionConsumerService** tagu.
 
    c. Skopiuj wartość **lokalizacji** atrybutu, a następnie wklej go do **adres URL odpowiedzi** pola w konfiguracji usługi Azure AD dla platformy SAP Cloud Platform.

10. Kliknij przycisk **zaufanego dostawcy tożsamości** kartę, a następnie kliknij przycisk **Dodawanie zaufanego dostawcy tożsamości**.
   
    ![Zaufanie zarządzania](./media/sap-hana-cloud-platform-tutorial/ic790802.png "zaufania zarządzania")
   
    >[!NOTE]
    >Aby zarządzać listą zaufanych dostawców tożsamości, musisz wybrany typ konfiguracji niestandardowych w sekcji lokalnego usługodawcy. Dla typu konfiguracji domyślnej należy nie można edytować i niejawne zaufanie w usłudze identyfikator SAP. Dla żadnego z nich nie masz żadnych ustawień zaufania.
    > 
    > 

11. Kliknij przycisk **ogólne** kartę, a następnie kliknij przycisk **Przeglądaj** można przekazać pliku pobranego metadanych.
    
    ![Zaufanie zarządzania](./media/sap-hana-cloud-platform-tutorial/ic793932.png "zaufania zarządzania")
    
    >[!NOTE]
    >Po przekazaniu pliku metadanych wartości **pojedynczy adres URL logowania**, **adres URL wylogowania jednokrotnego**, i **certyfikat podpisywania** są wypełniane automatycznie.
    > 
     
12. Kliknij kartę **Atrybuty**.

13. Na **atrybuty** karcie, wykonaj następujące kroki:
    
    ![Atrybuty](./media/sap-hana-cloud-platform-tutorial/ic790804.png "atrybutów") 

    a. Kliknij przycisk **atrybut Add Assertion-Based**, a następnie dodaj następujące atrybuty oparte na potwierdzenie:
       
    | Atrybut asercji | Atrybut podmiotu zabezpieczeń |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |Imię |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |nazwisko |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |wyślij wiadomość e-mail |
   
     >[!NOTE]
     >Konfiguracja atrybutów zależy od tego, jak aplikacje na punkt połączenia usługi są tworzone, oznacza to, atrybutów, które spełniają oczekiwane w odpowiedzi SAML i pod nazwą, której (atrybut podmiotu zabezpieczeń), uzyskiwania dostępu do tego atrybutu w kodzie.
     > 
    
    b. **Domyślny atrybut** na zrzucie ekranu jest tylko w celach ilustracyjnych. Nie jest wymagane dokonanie scenariusza pracy.  
 
    c. Nazwy i wartości dla **atrybutu jednostki** pokazano na zrzucie ekranu są zależne od jak opracowano aplikację. Istnieje możliwość, że aplikacja wymaga innego mapowania.

### <a name="assertion-based-groups"></a>Grupy oparte na potwierdzenie

Jako opcjonalny krok można skonfigurować grupy oparte na potwierdzenie dla usługi Azure Active Directory dostawcy tożsamości.

Na platformie SAP w chmurze przy użyciu grup umożliwia dynamiczne przypisywanie co najmniej jednego użytkownika do co najmniej jedną rolę w aplikacjach platformy SAP Cloud Platform ustalany na podstawie wartości atrybutów w potwierdzenie SAML 2.0. 

Na przykład, jeśli potwierdzenie zawiera atrybut "*kontraktu = tymczasowe*", możesz postanowić skanuje użytkowników, które mają zostać dodane do grupy"*tymczasowe*". Grupa "*tymczasowe*" może zawierać co najmniej jedną rolę z co najmniej jednej aplikacji wdrożonych w ramach Twojego konta platformy SAP Cloud Platform.
 
Jeśli chcesz jednocześnie przypisać wielu użytkowników do co najmniej jedną rolę aplikacji na Twoim koncie platformy SAP Cloud Platform przy użyciu grup opartych na potwierdzenie. Jeśli chcesz przypisać tylko jednego lub małych liczbę użytkowników do określonych ról, zaleca się przydzielenie ich bezpośrednio w "**autoryzacji**" na karcie Panelu sterowania platformy SAP Cloud Platform.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/sap-hana-cloud-platform-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/sap-hana-cloud-platform-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/sap-hana-cloud-platform-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/sap-hana-cloud-platform-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-sap-cloud-platform-test-user"></a>Tworzenie użytkownika testowego SAP Cloud Platform

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do platformy SAP Cloud Platform, należy przypisać role w programie SAP Cloud Platform do nich.

**Aby przypisać rolę użytkownikowi, wykonaj następujące czynności:**

1. Zaloguj się do Twojej **SAP Cloud Platform** Panelu sterowania.

2. Wykonaj następujące czynności:
   
    ![Autoryzacje](./media/sap-hana-cloud-platform-tutorial/ic790805.png "autoryzacji")
   
    a. Kliknij przycisk **autoryzacji**.

    b. Kliknij przycisk **użytkowników** kartę.

    c. W **użytkownika** pole tekstowe, wpisz adres e-mail użytkownika.

    d. Kliknij przycisk **przypisać** można przypisać użytkownika do roli.

    e. Kliknij pozycję **Zapisz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do platformy SAP Cloud Platform.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon SAP Cloud Platform, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **SAP Cloud Platform**.

    ![Link SAP Cloud Platform na liście aplikacji](./media/sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka SAP Cloud Platform na panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji SAP Cloud Platform.


## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
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


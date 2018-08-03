---
title: 'Samouczek: Integracja usługi Azure Active Directory z rozwiązaniem SAP Cloud klienta | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i w chmurze SAP dla klienta.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 90154dab-eba2-4563-bcf0-f2acc797ea97
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: jeedes
ms.openlocfilehash: 59bbcbf9aaef17394151e7db1471b63b87a46288
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39445721"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-for-customer"></a>Samouczek: Integracja usługi Azure Active Directory z rozwiązaniem SAP Cloud klienta

W tym samouczku dowiesz się, jak zintegrować z rozwiązaniem SAP Cloud dla klienta w usłudze Azure Active Directory (Azure AD).

Integracja z rozwiązaniem SAP Cloud dla klientów z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do usługi SAP Cloud klienta
- Można włączyć użytkowników, aby automatycznie uzyskać zalogowanych do rozwiązaniem SAP Cloud klienta (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integracji z usługą Azure AD z rozwiązaniem SAP Cloud klienta, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Chmurę SAP dla klientów logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięcznej wersji próbnej tutaj: [oferta wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie chmura SAP dla klientów z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>Dodawanie chmura SAP dla klientów z galerii
Aby skonfigurować integrację z rozwiązaniem SAP Cloud klienta w usłudze Azure AD, należy dodać chmura SAP dla klientów z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać chmura SAP dla klientów z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **rozwiązaniem SAP Cloud klienta**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_search.png)

1. W panelu wyników wybierz **rozwiązaniem SAP Cloud klienta**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowania usługi Azure AD logowanie jednokrotne z rozwiązaniem SAP Cloud klienta, w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w chmurze SAP dla klientów z użytkownikiem w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w chmurze SAP dla klientów musi zostać nawiązane.

W chmurze SAP dla klientów, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z rozwiązaniem SAP Cloud klienta, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie chmury SAP dla użytkownika testowego klienta](#creating-a-sap-cloud-for-customer-test-user)**  — aby odpowiednikiem Britta Simon w chmurze SAP dla klienta, który jest połączony z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i skonfigurować logowanie jednokrotne w chmurze SAP dla aplikacji klienta.

**Aby skonfigurować usługi Azure AD logowanie jednokrotne z rozwiązaniem SAP Cloud klienta, wykonaj następujące czynności:**

1. W witrynie Azure portal na **rozwiązaniem SAP Cloud klienta** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_samlbase.png)

1. Na **chmura SAP dla klientów, domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<server name>.crm.ondemand.com`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<server name>.crm.ondemand.com`

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania jednokrotnego i identyfikator. Skontaktuj się z pomocą [chmura SAP dla zespołu pomocy technicznej klienta klient](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) do uzyskania tych wartości. 

1. Na **atrybutów użytkownika** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_attribute.png)

    a. W **identyfikator użytkownika** listy wybierz **ExtractMailPrefix()** funkcji.

    b. Z **poczty** wybierz atrybut użytkownika, którego chcesz użyć dla wdrożenia.
    Na przykład jeśli chcesz użyć identyfikatorem EmployeeID jako identyfikator unikatowy użytkownika, a wartość atrybutu jest przechowywany w ExtensionAttribute2, wybierz user.extensionattribute2.  

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/sap-customer-cloud-tutorial/tutorial_general_400.png)

1. Na **rozwiązaniem SAP Cloud klienta konfiguracji** , kliknij przycisk **skonfigurować rozwiązaniem SAP Cloud klienta** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_configure.png) 

1. Aby skonfigurować logowanie Jednokrotne, wykonaj następujące czynności:
   
    a. Zaloguj się w chmurze SAP portalu klienta z uprawnieniami administratora.
   
    b. Przejdź do **aplikacji i typowe zadanie zarządzania użytkownikami** i kliknij przycisk **dostawcy tożsamości** kartę.
   
    c. Kliknij przycisk **nowego dostawcę tożsamości** i wybierz plik XML metadanych, które zostały pobrane z witryny Azure portal. Przez importowanie metadanych, system automatycznie przekazuje certyfikat wymagany podpis i certyfikat szyfrowania.
   
    ![Konfigurowanie logowania jednokrotnego](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_54.png)
   
    d. Usługa Azure Active Directory wymaga elementu adres URL usługi konsumenta potwierdzenie żądania języka SAML, dlatego wybierz **zawierają potwierdzenie konsumenta adres URL usługi** pola wyboru.
   
    e. Kliknij przycisk **aktywacji rejestracji jednokrotnej**.
   
    f. Zapisz zmiany.
   
    g. Kliknij przycisk **systemie** kartę.
   
    ![Konfigurowanie logowania jednokrotnego](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_52.png)
   
    h. W **adres URL logowania usługi AD Azure** pola tekstowego, Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowanej w witrynie Azure portal.
   
    ![Konfigurowanie logowania jednokrotnego](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_53.png)
   
    i. Określ, czy pracownik można ręcznie wybrać między logowanie się przy użyciu Identyfikatora użytkownika i hasła lub logowania jednokrotnego, wybierając **wyboru dostawcy tożsamości ręczne**.
   
    j. W **adres URL logowania jednokrotnego** sekcji, podaj adres URL, do którego należy używanych przez pracowników w celu logowania się do systemu. 
    W **adresu URL wysyłanych do pracownika** listy, można wybrać jedną z następujących opcji:
   
    **Adres URL — Usługa rejestracji Jednokrotnej**
   
    System wysyła tylko adres URL systemu normalnej do pracownika. Pracownik nie można zalogować się przy użyciu logowania jednokrotnego i musi użyć hasła lub zamiast tego certyfikatu.
   
    **ADRES URL LOGOWANIA JEDNOKROTNEGO** 
   
    System wysyła tylko adres URL logowania jednokrotnego do pracownika. Pracownik może zalogować się przy użyciu logowania jednokrotnego. Żądanie uwierzytelnienia zostanie przeniesiona za pośrednictwem dostawcy tożsamości.
   
    **Wybór automatyczny**
   
    Jeśli logowanie Jednokrotne nie jest aktywne, system wysyła system normalny adres URL do pracownika. Jeśli usługa rejestracji Jednokrotnej jest aktywna, system sprawdza, czy pracownik ma hasło. Jeśli hasło jest dostępny, adres URL logowania jednokrotnego i adres URL logowania jednokrotnego nie są wysyłane do pracownika. Jeśli pracownik nie ma hasła, adres URL logowania jednokrotnego są wysyłane do pracownika.
   
    k. Zapisz zmiany.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sap-customer-cloud-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sap-customer-cloud-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sap-customer-cloud-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sap-customer-cloud-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-sap-cloud-for-customer-test-user"></a>Tworzenie chmury SAP dla użytkownika testowego klienta

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w chmurze SAP dla klienta. Skontaktuj się z [chmury dla zespół pomocy technicznej dla oprogramowania SAP](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) Aby dodać użytkowników w chmurze SAP dla platformy klienta. 

> [!NOTE]
> Upewnij się, wartość NameID powinny być zgodne z pola Nazwa użytkownika w chmurze SAP dla platformy klienta.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon korzystać z platformy Azure logowania jednokrotnego przez udzielenie dostępu do usługi SAP Cloud klienta.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon rozwiązaniem SAP Cloud klienta, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **rozwiązaniem SAP Cloud klienta**.

    ![Konfigurowanie logowania jednokrotnego](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu w chmurze SAP dla klientów kafelka w panelu dostępu, możesz powinna uzyskać automatycznie zalogowanych do chmury SAP dla aplikacji klienta.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sap-customer-cloud-tutorial/tutorial_general_01.png
[2]: ./media/sap-customer-cloud-tutorial/tutorial_general_02.png
[3]: ./media/sap-customer-cloud-tutorial/tutorial_general_03.png
[4]: ./media/sap-customer-cloud-tutorial/tutorial_general_04.png

[100]: ./media/sap-customer-cloud-tutorial/tutorial_general_100.png

[200]: ./media/sap-customer-cloud-tutorial/tutorial_general_200.png
[201]: ./media/sap-customer-cloud-tutorial/tutorial_general_201.png
[202]: ./media/sap-customer-cloud-tutorial/tutorial_general_202.png
[203]: ./media/sap-customer-cloud-tutorial/tutorial_general_203.png


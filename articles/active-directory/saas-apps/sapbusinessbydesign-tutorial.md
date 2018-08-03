---
title: 'Samouczek: Integracja usługi Azure Active Directory z oprogramowaniem SAP Business ByDesign | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i oprogramowaniem SAP Business ByDesign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 82938920-33ba-47cb-b141-511b46d19e66
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.openlocfilehash: a7a08eb6062f134058bb63f5a3e2a78f661026ff
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39445217"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Samouczek: integracja usługi Azure Active Directory z oprogramowaniem SAP Business ByDesign

W tym samouczku dowiesz się, jak zintegrować z oprogramowaniem SAP Business ByDesign za pomocą usługi Azure Active Directory (Azure AD).

Integracja z oprogramowaniem SAP Business ByDesign z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do oprogramowaniem SAP Business ByDesign.
- Użytkowników, aby automatycznie uzyskać zalogowanych do oprogramowaniem SAP Business ByDesign (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z oprogramowaniem SAP Business ByDesign, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Oprogramowaniem SAP Business ByDesign logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie z oprogramowaniem SAP Business ByDesign z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>Dodawanie z oprogramowaniem SAP Business ByDesign z galerii
Aby skonfigurować integrację z oprogramowaniem SAP Business ByDesign w usłudze Azure AD, należy dodać oprogramowaniem SAP Business ByDesign z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać oprogramowaniem SAP Business ByDesign z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **oprogramowaniem SAP Business ByDesign**, wybierz opcję **oprogramowaniem SAP Business ByDesign** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Oprogramowaniem SAP Business ByDesign na liście wyników](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z oprogramowaniem SAP Business ByDesign oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w oprogramowaniem SAP Business ByDesign do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w oprogramowaniem SAP Business ByDesign musi zostać ustanowione.

Przypisywanie wartości z oprogramowaniem SAP Business ByDesign **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z oprogramowaniem SAP Business ByDesign, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego oprogramowaniem SAP Business ByDesign](#create-an-sap-business-bydesign-test-user)**  — aby odpowiednikiem Britta Simon w oprogramowaniem SAP Business ByDesign jest połączony z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i skonfigurować logowanie jednokrotne do aplikacji z oprogramowaniem SAP Business ByDesign.

**Aby skonfigurować usługi Azure AD logowanie jednokrotne z oprogramowaniem SAP Business ByDesign, wykonaj następujące czynności:**

1. W witrynie Azure portal na **oprogramowaniem SAP Business ByDesign** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_samlbase.png)

1. Na **SAP Business oprogramowaniem ByDesign domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![SAP Business oprogramowaniem ByDesign domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<servername>.sapbydesign.com`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<servername>.sapbydesign.com`

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania jednokrotnego i identyfikator. Skontaktuj się z pomocą [zespołem pomocy technicznej SAP Business oprogramowaniem ByDesign klienta](https://www.sap.com/products/cloud-analytics.support.html) do uzyskania tych wartości.

1. Na **atrybutów użytkownika** sekcji, wykonaj następujące czynności:

    ![SAP Business oprogramowaniem ByDesign atrybutów sekcji](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_attribute.png)
    
    a. W **identyfikator użytkownika** listy wybierz **ExtractMailPrefix()** funkcji.
    
    b. Z **poczty** wybierz atrybut użytkownika, którego chcesz użyć dla wdrożenia. Na przykład jeśli chcesz użyć identyfikatorem EmployeeID jako identyfikator unikatowy użytkownika, a wartość atrybutu jest przechowywany w ExtensionAttribute2, wybierz user.extensionattribute2.     

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/sapbusinessbydesign-tutorial/tutorial_general_400.png)

1. Na **SAP Business oprogramowaniem ByDesign konfiguracji** kliknij **skonfigurować oprogramowaniem SAP Business ByDesign** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![SAP Business oprogramowaniem ByDesign konfiguracji](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_configure.png) 

1. Aby uzyskać logowanie Jednokrotne skonfigurowane pod kątem swojej aplikacji, wykonaj następujące czynności:
   
    a. Zaloguj się do portalu oprogramowaniem SAP Business ByDesign z uprawnieniami administratora.
   
    b. Przejdź do **aplikacji i typowe zadanie zarządzania użytkownikami** i kliknij przycisk **dostawcy tożsamości** kartę.
   
    c. Kliknij przycisk **nowego dostawcę tożsamości** i wybierz plik XML metadanych, który został pobrany z witryny Azure portal. Przez importowanie metadanych, system automatycznie przekazuje certyfikat wymagany podpis i certyfikat szyfrowania.
   
    ![Konfigurowanie logowania jednokrotnego](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)
   
    d. Obejmujący **adres URL usługi konsumenta potwierdzenie** w żądaniu języka SAML wybierz **zawierają potwierdzenie konsumenta adres URL usługi**.
   
    e. Kliknij przycisk **aktywacji rejestracji jednokrotnej**.
   
    f. Zapisz zmiany.
   
    g. Kliknij przycisk **systemie** kartę.
   
    ![Konfigurowanie logowania jednokrotnego](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)
   
    h. Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL**, który skopiowano z witryny Azure portal do **adres URL logowania usługi AD Azure** pola tekstowego.
   
    ![Konfigurowanie logowania jednokrotnego](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)
   
    i. Określ, czy pracownik można ręcznie wybrać między logowanie się przy użyciu Identyfikatora użytkownika i hasła lub logowania jednokrotnego, wybierając **wyboru dostawcy tożsamości ręczne**.
   
    j. W **adres URL logowania jednokrotnego** sekcji, określ adres URL, które mają być używane przez pracowników do logowania się do systemu. 
    W URL wysyłane do listy rozwijanej pracownika można wybrać jedną z następujących opcji:
   
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

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/sapbusinessbydesign-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/sapbusinessbydesign-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/sapbusinessbydesign-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/sapbusinessbydesign-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-an-sap-business-bydesign-test-user"></a>Tworzenie użytkownika testowego oprogramowaniem SAP Business ByDesign

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w oprogramowaniem SAP Business ByDesign. Skontaktuj się z [zespołem pomocy technicznej SAP Business oprogramowaniem ByDesign klienta](https://www.sap.com/products/cloud-analytics.support.html) Aby dodać użytkowników z oprogramowaniem SAP Business ByDesign platformy. 

> [!NOTE]
> Upewnij się, że wartość identyfikatora NameID powinny być zgodne z pola Nazwa użytkownika na platformie oprogramowaniem SAP Business ByDesign.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon korzystać z platformy Azure logowania jednokrotnego przez udzielenie dostępu z oprogramowaniem SAP Business ByDesign.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon oprogramowaniem SAP Business ByDesign, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **oprogramowaniem SAP Business ByDesign**.

    ![Link oprogramowaniem SAP Business ByDesign na liście aplikacji](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka oprogramowaniem SAP Business ByDesign w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikację z oprogramowaniem SAP Business ByDesign.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sapbusinessbydesign-tutorial/tutorial_general_01.png
[2]: ./media/sapbusinessbydesign-tutorial/tutorial_general_02.png
[3]: ./media/sapbusinessbydesign-tutorial/tutorial_general_03.png
[4]: ./media/sapbusinessbydesign-tutorial/tutorial_general_04.png

[100]: ./media/sapbusinessbydesign-tutorial/tutorial_general_100.png

[200]: ./media/sapbusinessbydesign-tutorial/tutorial_general_200.png
[201]: ./media/sapbusinessbydesign-tutorial/tutorial_general_201.png
[202]: ./media/sapbusinessbydesign-tutorial/tutorial_general_202.png
[203]: ./media/sapbusinessbydesign-tutorial/tutorial_general_203.png


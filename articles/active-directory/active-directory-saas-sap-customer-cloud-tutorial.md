---
title: 'Samouczek: Integracji Azure Active Directory z chmurą SAP dla klienta | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i w chmurze SAP dla klienta.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 90154dab-eba2-4563-bcf0-f2acc797ea97
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: jeedes
ms.openlocfilehash: 18172fb634c369fb4add5fe7c55e5fec08df7670
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352304"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-for-customer"></a>Samouczek: Integracji Azure Active Directory z chmurą SAP dla klienta

Z tego samouczka dowiesz się integrowanie SAP chmury dla klienta w usłudze Azure Active Directory (Azure AD).

Integrowanie SAP chmury dla klienta z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do chmury SAP dla klienta
- Umożliwia użytkownikom automatycznie pobrać zalogowane do chmury SAP dla klienta (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z chmurą SAP dla odbiorcy, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Chmurę SAP do klienta logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna tutaj: [oferta wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie SAP chmury dla klienta z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>Dodawanie SAP chmury dla klienta z galerii
Aby skonfigurować integrację SAP chmury dla klienta do usługi Azure AD, należy dodać SAP chmury dla klienta z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać SAP chmury dla klienta z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **SAP chmury dla klienta**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_search.png)

5. W panelu wyników wybierz **SAP chmury dla klienta**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z chmurą SAP dla klienta w oparciu o użytkownika testowego o nazwie "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w chmurze SAP dla klienta jest dla użytkownika, w usłudze Azure AD. Innymi słowy musi można ustanowić łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w chmurze SAP dla klienta.

W chmurze SAP dla klienta, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z chmurą SAP dla klienta, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie chmury SAP dla użytkownika testowego klienta](#creating-a-sap-cloud-for-customer-test-user)**  — aby odpowiednikiem Simona Britta w chmurze SAP dla klienta, który jest połączony z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w chmurze SAP dla aplikacji klienta.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z chmurą SAP dla klienta, wykonaj następujące czynności:**

1. W portalu Azure na **SAP chmury dla klienta** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_samlbase.png)

3. Na **chmury SAP do domeny klienta i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<server name>.crm.ondemand.com`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<server name>.crm.ondemand.com`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Skontaktuj się z [chmurę SAP do zespołu pomocy technicznej klienta klienta](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) uzyskać te wartości. 

4. Na **atrybuty użytkownika** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_attribute.png)

    a. W **identyfikator użytkownika** listy, wybierz **ExtractMailPrefix()** funkcji.

    b. Z **poczty** wybierz atrybut użytkownika, którego chcesz użyć implementacji.
    Na przykład jeśli ma być używany jako identyfikator użytkownika unikatowy identyfikator pracownika, a wartość atrybutu są przechowywane w ExtensionAttribute2, wybierz user.extensionattribute2.  

5. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_certificate.png) 

6. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_400.png)

7. Na **SAP chmury dla konfiguracji klienta** , kliknij przycisk **skonfigurować chmurę SAP do klienta** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_configure.png) 

8. Aby uzyskać dostęp skonfigurowane logowania jednokrotnego, wykonaj następujące czynności:
   
    a. Zaloguj się w chmurze SAP do portalu klienta z prawami administratora.
   
    b. Przejdź do **aplikacji i typowych zadań zarządzania użytkownika** i kliknij przycisk **dostawcy tożsamości** kartę.
   
    c. Kliknij przycisk **nowego dostawcy tożsamości** i wybierz plik XML metadanych został pobrany z portalu Azure. Przez importowanie metadanych, system automatycznie wysyła certyfikat wymagany podpis, jak i certyfikat szyfrowania.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_54.png)
   
    d. Usługa Azure Active Directory wymaga elementu adres URL usługi klienta potwierdzenia w żądaniu SAML, dlatego wybierz **zawierają potwierdzenie konsumenta adres URL usługi** wyboru.
   
    e. Kliknij przycisk **aktywacji rejestracji jednokrotnej**.
   
    f. Zapisz zmiany.
   
    g. Kliknij przycisk **systemie** kartę.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_52.png)
   
    h. W **Azure AD znaku w adresie URL** pole tekstowe, Wklej **SAML pojedynczy znak na adres URL usługi** którego została skopiowana z portalu Azure.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_53.png)
   
    i. Określ, czy pracownik może ręcznie wybrać między zalogowanie się przy użyciu Identyfikatora użytkownika i hasła lub logowania jednokrotnego, wybierając **wybór dostawcy tożsamości ręcznego**.
   
    j. W **adres URL logowania jednokrotnego** sekcji, podaj adres URL, które mają być używane przez pracowników do logowania do systemu. 
    W **adres URL wysyłane do pracowników** liście, można wybrać jedną z następujących opcji:
   
    **Adres URL — Usługa rejestracji Jednokrotnej**
   
    System wysyła tylko adres URL normalne systemu do pracownika. Pracownik nie może zalogować się przy użyciu logowania jednokrotnego i musi użyć hasła lub zamiast tego certyfikatu.
   
    **ADRES URL LOGOWANIA JEDNOKROTNEGO** 
   
    System wysyła tylko adres URL logowania jednokrotnego do pracownika. Pracownik może zalogować się przy użyciu logowania jednokrotnego. Żądanie uwierzytelnienia jest przekierowywane przez dostawców tożsamości.
   
    **Automatyczne wybieranie**
   
    Jeśli logowania jednokrotnego nie jest aktywne, system wysyła system normalny adres URL do pracownika. Jeśli usługa rejestracji Jednokrotnej jest aktywny, system sprawdza, czy pracownik ma hasło. Jeśli hasło jest dostępny, zarówno adres URL logowania jednokrotnego i adres URL logowania jednokrotnego nie są wysyłane do pracownika. Jeśli pracownik nie ma hasła, adres URL logowania jednokrotnego są wysyłane do pracownika.
   
    k. Zapisz zmiany.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-sap-customer-cloud-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-sap-customer-cloud-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-sap-customer-cloud-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-sap-customer-cloud-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-sap-cloud-for-customer-test-user"></a>Tworzenie chmury SAP dla użytkownika testowego klienta

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w chmurze SAP dla klienta. We współpracy z [SAP chmury zespół obsługi klienta](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) Aby dodać użytkowników w chmurze SAP platformy klienta. 

> [!NOTE]
> Upewnij się, wartość NameID powinny być zgodne z pola Nazwa użytkownika w chmurze SAP dla klienta platformy.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do chmury SAP dla klienta.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta SAP chmury dla klienta, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **SAP chmury dla klienta**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu chmury SAP do klienta kafelka w panelu dostępu należy należy pobrać automatycznie zalogowane z Twoją chmurą SAP dla aplikacji klienta.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_203.png


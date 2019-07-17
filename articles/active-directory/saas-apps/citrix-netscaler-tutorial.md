---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Citrix NetScaler | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Citrix NetScaler.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af501bd0-8ff5-468f-9b06-21e607ae25de
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 64dd67680626857db7f39fa7fd721b28a02d1561
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68276900"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-netscaler"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Citrix NetScaler

W tym samouczku dowiesz się, jak zintegrować Citrix NetScaler z usługą Azure Active Directory (Azure AD).
Integrowanie Citrix NetScaler z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Citrix NetScaler.
* Użytkownikom można automatycznie zalogowany do Citrix NetScaler (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Citrix NetScaler, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Citrix NetScaler logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje Citrix NetScaler **SP** jednokrotne logowanie inicjowane przez

* Obsługuje Citrix NetScaler **Just In Time** aprowizacji użytkowników

## <a name="adding-citrix-netscaler-from-the-gallery"></a>Dodawanie Citrix NetScaler z galerii

Aby skonfigurować integrację programu Citrix NetScaler w usłudze Azure AD, należy dodać Citrix NetScaler z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Citrix NetScaler z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Citrix NetScaler**, wybierz opcję **Citrix NetScaler** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![Citrix NetScaler na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz i test usługi Azure AD logowanie jednokrotne za pomocą Citrix NetScaler w oparciu o nazwie użytkownika testowego **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w Citrix NetScaler musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Citrix NetScaler, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie Citrix NetScaler logowania jednokrotnego](#configure-citrix-netscaler-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego Citrix NetScaler](#create-citrix-netscaler-test-user)**  — aby odpowiednikiem Britta Simon w Citrix NetScaler, połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z Citrix NetScaler, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **Citrix NetScaler** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Citrix NetScaler domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/sp-identifier-reply.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`
    
    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<<Your FQDN>>`

    c. W **adres URL odpowiedzi (adres URL usługi Assertion konsumenta)** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`
    
    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej firmy Citrix NetScaler klienta](https://www.citrix.com/contact/technical-support.html) do uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    > [!NOTE]
    > Aby rozpocząć pracę z logowania jednokrotnego, te adresy URL powinien być dostępny publicznych witryn. Musisz włączyć zapory lub inne ustawienia zabezpieczeń, na stronie Netscaler enble usługi Azure AD można opublikować tokenu na skonfigurowany adres URL usługi ACS.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

6. Na **Konfigurowanie Citrix NetScaler** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-citrix-netscaler-single-sign-on"></a>Konfigurowanie Citrix NetScaler logowania jednokrotnego

1. W oknie przeglądarki internetowej innej logowanie jednokrotne do swojej dzierżawy Citrix NetScaler jako administrator.

2. Upewnij się, że **wersja oprogramowania układowego NetScaler = NS12.1: Tworzenie 48.13.nc**.

    ![Konfigurowanie logowania jednokrotnego](./media/citrix-netscaler-tutorial/configure01.png)

3. Na **wirtualnego serwera sieci VPN** strony, wykonaj następujące czynności:

     ![Konfigurowanie logowania jednokrotnego](./media/citrix-netscaler-tutorial/configure02.png)

    a. Ustawienia bramy **ICA tylko** jako **true**.
    
    b. Ustaw **Włącz uwierzytelnianie** jako **true**.
    
    c. **DTLS** jest opcjonalne.
    
    d. Upewnij się, że **SSLv3** jako **wyłączone**.

4. Dostosowany **mechanizmów szyfrowania SSL** grupa zostanie utworzona w celu osiągnięcia A + na https://www.ssllabs.com jak pokazano poniżej:

    ![Konfigurowanie logowania jednokrotnego](./media/citrix-netscaler-tutorial/configure03.png)

5. Na **Konfigurowanie uwierzytelniania SAML serwera** strony, wykonaj następujące czynności:

      ![Konfigurowanie logowania jednokrotnego](./media/citrix-netscaler-tutorial/configure04.png)

    a. W **nazwa** polu tekstowym wpisz nazwę serwera.

    b. W **adresu URL przekierowania** pola tekstowego, Wklej wartość **adres URL logowania** skopiowanej w witrynie Azure portal.

    c. W **adres URL wylogowania jednokrotnego** pola tekstowego, Wklej wartość **adres URL wylogowania** skopiowanej w witrynie Azure portal.

    d. W **nazwa certyfikatu tożsamości**, kliknij przycisk **"+"** Zaloguj się dodać certyfikat, który został pobrany z witryny Azure portal. Po przekazaniu wybierz certyfikat z listy rozwijanej.

    e. Następujące więcej pól należy ustawić na tej stronie

      ![Konfigurowanie logowania jednokrotnego](./media/citrix-netscaler-tutorial/configure24.png)

    f. Wybierz **żądane kontekstu uwierzytelniania** jako **dokładnie**.

    g. Wybierz **algorytm podpisu** jako **algorytm RSA-SHA256**.

    h. Wybierz **szyfrowane metoda** jako **SHA256**.

    i. Sprawdź **wymusić Username**.

    j. Kliknij przycisk **OK**.

6. Aby skonfigurować **profilu sesji**, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/citrix-netscaler-tutorial/configure06.png)

    a. W **nazwa** polu tekstowym wpisz nazwę profilu sesji.

    b. Na **środowisko klienta** kartę, wprowadzić zmiany, jak pokazano na poniższym zrzucie ekranu.

    c. Kontynuuj wprowadzanie zmian na **na karcie Ogólne** pokazany poniżej i kliknij przycisk **OK**

    ![Konfigurowanie logowania jednokrotnego](./media/citrix-netscaler-tutorial/configure07.png)

    d. Na **opublikowane aplikacje** kartę, wprowadzić zmiany, jak pokazano na poniższym zrzucie ekranu i kliknij przycisk **OK**.

    ![Konfigurowanie logowania jednokrotnego](./media/citrix-netscaler-tutorial/configure08.png)

    e. Na **zabezpieczeń** kartę, wprowadzić zmiany, jak pokazano na poniższym zrzucie ekranu i kliknij przycisk **OK**.

    ![Konfigurowanie logowania jednokrotnego](./media/citrix-netscaler-tutorial/configure09.png)

7. Nawiązuj kontakty ICA, łącząc się przez Port niezawodność sesji **2598** jak pokazano w poniższy zrzut ekranu.

    ![Konfigurowanie logowania jednokrotnego](./media/citrix-netscaler-tutorial/configure10.png)

8. Na **SAML** Dodaj **serwerów** jak pokazano na poniższym zrzucie ekranu.

    ![Konfigurowanie logowania jednokrotnego](./media/citrix-netscaler-tutorial/configure11.png)

9. Na **SAML** Dodaj **zasady** jak pokazano na poniższym zrzucie ekranu.

     ![Konfigurowanie logowania jednokrotnego](./media/citrix-netscaler-tutorial/configure12.png)

10. Na **ustawienia globalne** strony, przejdź do **niezależnie dostępu** sekcji.

    ![Konfigurowanie logowania jednokrotnego](./media/citrix-netscaler-tutorial/configure13.png)

11. Na **konfiguracji** karcie, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/citrix-netscaler-tutorial/configure14.png)

    a. Wybierz **Zezwalaj domen**.

    b. W **nazwy domeny** pola tekstowego, wybierz domenę.

    c. Kliknij przycisk **OK**.

12. Wprowadź **StoreFront** ustawień na **odbiornika dla witryn sieci Web** jak pokazano na poniższym zrzucie ekranu:

    ![Konfigurowanie logowania jednokrotnego](./media/citrix-netscaler-tutorial/configure15.png)

13. Na **Zarządzanie metodami uwierzytelniania - Corp** wyskakujące, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/citrix-netscaler-tutorial/configure16.png)

    a. Wybierz **nazwy użytkownika i hasła**.

    b. Wybierz **przekazywanego z bramy NetScaler**.

    c. Kliknij przycisk **OK**.

14. Na **skonfiguruj zaufane domeny** wyskakujące, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/citrix-netscaler-tutorial/configure17.png)

    a. Wybierz **zaufanych domen tylko**.

    b. Kliknij pozycję **Dodaj** dodać swoją domenę na platformie **zaufanych domen** pola tekstowego.

    c. Wybierz domeny domyślnej z Twojej **domyślnej domeny** listy.

    d. Wybierz **Pokaż listę domen, na stronie logowania**.

    e. Kliknij przycisk **OK**.

15. Na **Zarządzaj bramami NetScaler** wyskakujące, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/citrix-netscaler-tutorial/configure18.png)

    a. Kliknij pozycję **Dodaj** dodać bram sieci NetScaler w **bram NetScaler** pola tekstowego.

    b. Kliknij przycisk **Zamknij**.

16. Na **ustawienia ogólne StoreFront** karcie, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/citrix-netscaler-tutorial/configure19.png)

    a. W **nazwę wyświetlaną** polu tekstowym wpisz nazwę NetScaler bramy.

    b. W **adres URL bramy NetScaler** polu tekstowym wpisz adres URL bramy NetScaler.

    c. Wybierz **użycia lub roli** jako **uwierzytelniania i HDX routingu**.

    d. Kliknij przycisk **OK**.

17. Na **StoreFront Secure biletu urząd** karcie, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/citrix-netscaler-tutorial/configure20.png)

    a. Kliknij pozycję **Dodaj** przycisk, aby dodać swoje **Secure biletu urząd adresu URL** w polu tekstowym.

    b. Wybierz **Włącz niezawodność sesji**.

    c. Kliknij przycisk **OK**.

18. Na **ustawienia uwierzytelniania StoreFront** karcie, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/citrix-netscaler-tutorial/configure21.png)

    a. Wybierz swoje **wersji**.

    b. Wybierz **typ logowania** jako **domeny**.

    c. Wprowadź swoje **adresów URL wywołania zwrotnego**.

    d. Kliknij przycisk **OK**.

19. Na **StoreFront wdrożenia Citrix odbiorcy** karcie, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/citrix-netscaler-tutorial/configure22.png)

    a. Wybierz **opcji wdrożenia** jako **odbiorcy użycia za pomocą protokołu HTML5 w przypadku niedostępności lokalnego odbiornika**.

    b. Kliknij przycisk **OK**.

20. Na **Zarządzanie sygnałów nawigacyjnych** wyskakujące, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/citrix-netscaler-tutorial/configure23.png)

    a. Wybierz **wewnętrznego sygnału** jako **Użyj adresu URL usługi**.

    b. Kliknij przycisk **Dodaj** dodać swoje adresy URL w **sygnały nawigacyjne w zewnętrznej** pola tekstowego.

    c. Kliknij przycisk **OK**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Citrix NetScaler.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Citrix NetScaler**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Citrix NetScaler**.

    ![Link Citrix NetScaler na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-citrix-netscaler-test-user"></a>Tworzenie użytkownika testowego Citrix NetScaler

W tej sekcji użytkownika o nazwie Britta Simon jest tworzony w Citrix NetScaler. Citrix NetScaler obsługuje aprowizacji użytkowników w czasie, który jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w Citrix NetScaler, nowy katalog jest tworzony po uwierzytelnieniu.

>[!NOTE]
>Jeśli potrzebujesz ręcznie utworzyć użytkownika, musisz skontaktować się z [zespołem pomocy technicznej firmy Citrix NetScaler klienta](https://www.citrix.com/contact/technical-support.html).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Citrix NetScaler w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze Citrix NetScaler, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


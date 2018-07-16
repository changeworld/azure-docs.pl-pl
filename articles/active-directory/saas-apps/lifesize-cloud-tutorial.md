---
title: 'Samouczek: Integracja usługi Azure Active Directory z chmurą Lifesize | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między środowiskiem usługi Azure Active Directory i Lifesize chmurą.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 75fab335-fdcd-4066-b42c-cc738fcb6513
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: b699714a2ab90fd0ad1c2f290681ccdae7aeb1ba
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39052197"
---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Samouczek: Integracja usługi Azure Active Directory z chmurą Lifesize

W tym samouczku dowiesz się, jak zintegrować Lifesize chmury z usługi Azure Active Directory (Azure AD).

Integrowanie Lifesize chmury z usługi Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do chmury Lifesize
- Użytkowników, aby automatycznie uzyskać zalogowanych do chmury Lifesize (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu chmury Lifesize, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Lifesize pojedynczego logowania jednokrotnego włączonych subskrypcji usługi w chmurze

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Lifesize chmury z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-lifesize-cloud-from-the-gallery"></a>Dodawanie Lifesize chmury z galerii
Aby skonfigurować integrację Lifesize chmury w usłudze Azure AD, należy dodać Lifesize chmury z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Lifesize chmury z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **chmury Lifesize**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_search.png)

5. W panelu wyników wybierz **chmury Lifesize**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji można skonfigurować, i test usługi Azure AD logowanie jednokrotne za pomocą chmury Lifesize oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w chmurze Lifesize do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w chmurze Lifesize musi zostać ustanowione.

W chmurze Lifesize przypisze się wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu chmury Lifesize, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego chmury Lifesize](#creating-a-lifesize-cloud-test-user)**  — aby odpowiednikiem Britta Simon w chmurze Lifesize, która jest połączona z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i skonfigurować logowanie jednokrotne w aplikacji w chmurze Lifesize.

**Aby skonfigurować usługi Azure AD logowania jednokrotnego przy użyciu chmury Lifesize, wykonaj następujące czynności:**

1. W witrynie Azure portal na **chmury Lifesize** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_samlbase.png)

3. Na **Lifesize chmury domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://login.lifesizecloud.com/ls/?acs`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://login.lifesizecloud.com/<companyname>`

     
4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL**, wykonaj następujące kroki:    
   
    ![Konfigurowanie logowania jednokrotnego](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_url1.png)

    W **przekazywania stanu** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://webapp.lifesizecloud.com/?ent=<identifier>`
   
   > [!NOTE] 
   >Należy pamiętać, że nie są rzeczywiste wartości. Musisz zaktualizować te wartości przy użyciu rzeczywisty adres URL logowania, stan przekazywania i identyfikator. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta chmury Lifesize](https://www.lifesize.com/support) uzyskać adres URL logowania, a wartości identyfikatora i można pobrać stan przekazywania wartości z konfiguracji logowania jednokrotnego, która jest opisane w dalszej części tego samouczka.

4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_certificate.png) 

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/lifesize-cloud-tutorial/tutorial_general_400.png)

6. Na **Konfiguracja chmury Lifesize** , kliknij przycisk **Konfigurowanie chmury Lifesize** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego adres URL usługi** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_configure.png) 

7. Aby uzyskać logowanie Jednokrotne skonfigurowane dla aplikacji, zaloguj się do aplikacji w chmurze Lifesize z uprawnieniami administratora.

8. W prawym górnym rogu kliknij swoją nazwę, a następnie kliknij polecenie **ustawienia zaawansowane**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)

9. W ustawieniach Zaawansowane teraz kliknąć **konfiguracji logowania jednokrotnego** łącza. Go spowoduje otwarcie strony konfiguracji logowania jednokrotnego, wystąpienia.
   
    ![Konfigurowanie logowania jednokrotnego](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)

10. Teraz można skonfigurować następujące wartości w interfejsie użytkownika konfiguracji logowania jednokrotnego.    
   
    ![Konfigurowanie logowania jednokrotnego](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)
    
    a. W **wystawca dostawcy tożsamości** pola tekstowego, Wklej wartość **identyfikator jednostki SAML** skopiowanej w witrynie Azure portal.

    b.  W **adres URL logowania** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowanej w witrynie Azure portal.

    c. Otwórz swój certyfikat zakodowany base-64 w Notatniku pobranego z witryny Azure portal, skopiuj jego zawartość do Schowka, a następnie wklej go do **certyfikat X.509** pola tekstowego.
  
    d. Mapowania atrybutów SAML dla pola tekstowego imię wprowadzenie wartości jako **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**
    
    e. Mapowanie atrybutów SAML dla **nazwisko** polu tekstowym wprowadź wartość jako **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**
    
    f. Mapowanie atrybutów SAML dla **E-mail** polu tekstowym wprowadź wartość jako **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**

11. Aby sprawdzić konfigurację, możesz kliknąć **testu** przycisku.
   
    >[!NOTE]
    >Do pomyślnego testowania należy zakończyć działanie Kreatora konfiguracji w usłudze Azure AD, a także zapewnić dostęp do użytkowników lub grupy, którzy mogą wykonywać test.

12. Włączanie logowania jednokrotnego, sprawdzając na **włączyć logowanie Jednokrotne** przycisku.

13. Teraz kliknąć **aktualizacji** przycisk Tak, aby wszystkie ustawienia są zapisywane. Spowoduje to wygenerowanie wartości użytkownika. Kopiuj wartość użytkownika, który jest generowany w polu tekstowym, wklej ją w **tan przekaźnika** polu tekstowym w obszarze **Lifesize chmury domena i adresy URL** sekcji. 

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/lifesize-cloud-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/lifesize-cloud-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/lifesize-cloud-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/lifesize-cloud-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-lifesize-cloud-test-user"></a>Tworzenie użytkownika testowego Lifesize chmury

W tej sekcji utworzysz użytkownika w chmurze Lifesize o nazwie Britta Simon. Chmura Lifesize obsługuje automatyczna aprowizacja użytkowników. Po pomyślnym uwierzytelnieniu w usłudze Azure AD użytkownik zostanie automatycznie aprowizowana w aplikacji. 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do udzielania dostępu do chmury Lifesize za pomocą platformy Azure logowania jednokrotnego.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Lifesize chmury, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **chmury Lifesize**.

    ![Konfigurowanie logowania jednokrotnego](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Lifesize chmury w panelu dostępu, należy uzyskać stronę logowania aplikacji w chmurze Lifesize.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/lifesize-cloud-tutorial/tutorial_general_01.png
[2]: ./media/lifesize-cloud-tutorial/tutorial_general_02.png
[3]: ./media/lifesize-cloud-tutorial/tutorial_general_03.png
[4]: ./media/lifesize-cloud-tutorial/tutorial_general_04.png

[100]: ./media/lifesize-cloud-tutorial/tutorial_general_100.png

[200]: ./media/lifesize-cloud-tutorial/tutorial_general_200.png
[201]: ./media/lifesize-cloud-tutorial/tutorial_general_201.png
[202]: ./media/lifesize-cloud-tutorial/tutorial_general_202.png
[203]: ./media/lifesize-cloud-tutorial/tutorial_general_203.png


---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą usługi zabezpieczeń firmy Symantec w sieci Web (WSS) | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i usług zabezpieczeń firmy Symantec w sieci Web (WSS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d6e4d893-1f14-4522-ac20-0c73b18c72a5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: dbf21c7c22a9b3273a65f7e186a2ac02ccae6ba2
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39436211"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą usługi zabezpieczeń firmy Symantec w sieci Web (WSS)

W tym samouczku zostanie dowiesz się, jak zintegrować konta usług zabezpieczeń firmy Symantec w sieci Web (WSS) przy użyciu konta usługi Azure Active Directory (Azure AD), tak, aby WSS może uwierzytelnić konta użytkownika końcowego aprowizowane w usłudze Azure AD przy użyciu uwierzytelniania SAML i wymuszanie użytkownika lub reguły poziomu zasad grupy.

Integrowanie usług zabezpieczeń firmy Symantec w sieci Web (WSS) z usługą Azure AD zapewnia następujące korzyści:

- Zarządzanie wszystkimi użytkownicy i grupy używane przez konto usługi WSS z poziomu portalu usługi Azure AD. 

- Zezwolić użytkownikom końcowym uwierzytelnić programu WSS przy użyciu swoich poświadczeń usługi Azure AD.

- Włącz wymuszanie użytkowników i grupy poziomu zasady zdefiniowane na Twoim koncie programu WSS.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą usługi zabezpieczeń sieci Web (WSS) firmy Symantec, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Konta usług zabezpieczeń firmy Symantec w sieci Web (WSS)

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się przy użyciu konta programu WSS, który jest aktualnie używany w celach produkcyjnych.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać konta programu WSS, który jest aktualnie używany w celu produkcji dla tego testu, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku skonfigurujesz usługi Azure AD, aby włączyć logowanie jednokrotne do programu WSS przy użyciu poświadczeń użytkownika zdefiniowane w ramach konta usługi Azure AD.
Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie aplikacji usług zabezpieczeń firmy Symantec w sieci Web (WSS) z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>Dodawanie usług zabezpieczeń firmy Symantec w sieci Web (WSS) z galerii
Aby skonfigurować integrację usług zabezpieczeń firmy Symantec w sieci Web (WSS) w usłudze Azure AD, należy dodać usługi zabezpieczeń firmy Symantec w sieci Web (WSS) z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać usługi zabezpieczeń firmy Symantec w sieci Web (WSS) z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **usług zabezpieczeń firmy Symantec w sieci Web (WSS)**, wybierz opcję **usług zabezpieczeń firmy Symantec w sieci Web (WSS)** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikacja.

    ![Firmy Symantec w sieci Web zabezpieczeń usługi (WSS) na liście wyników](./media/symantec-tutorial/tutorial_symantecwebsecurityservicewss_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowania, w usłudze Azure AD logowanie jednokrotne, z firmy Symantec w sieci Web zabezpieczeń usługi (WSS) w oparciu o nazwie "Britta Simon" użytkownika testowego.

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w sieci Web usług zabezpieczeń (WSS) firmy Symantec dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w sieci Web usług zabezpieczeń (WSS) firmy Symantec musi zostać ustanowione.

W firmy Symantec w sieci Web zabezpieczeń usługi (WSS), przypisz wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z sieci Web usług zabezpieczeń (WSS) firmy Symantec, musisz wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego usług zabezpieczeń firmy Symantec w sieci Web (WSS)](#create-a-symantec-web-security-service-wss-test-user)**  — aby odpowiednikiem Britta Simon w firmy Symantec w sieci Web usługi zabezpieczeń (WSS) połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji usługi zabezpieczeń firmy Symantec w sieci Web (WSS).

**Aby skonfigurować usługi Azure AD logowanie jednokrotne z sieci Web usług zabezpieczeń (WSS) firmy Symantec, wykonaj następujące czynności:**

1. W witrynie Azure portal na **usług zabezpieczeń firmy Symantec w sieci Web (WSS)** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/symantec-tutorial/tutorial_symantecwebsecurityservicewss_samlbase.png)

1. Na **firmy Symantec w sieci Web zabezpieczeń usługi (WSS) domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Domena usługi zabezpieczeń firmy Symantec w sieci Web (WSS) i adresy URL pojedynczego logowania jednokrotnego informacji](./media/symantec-tutorial/tutorial_symantecwebsecurityservicewss_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL: `https://saml.threatpulse.net:8443/saml/saml_realm`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL: `https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > Skontaktuj się z pomocą [zespołem pomocy technicznej klienta usług zabezpieczeń firmy Symantec w sieci Web (WSS)](https://www.symantec.com/contact-us) Jeśli wartości **identyfikator** i **adres URL odpowiedzi** nie działają dla jakiegoś powodu.

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/symantec-tutorial/tutorial_symantecwebsecurityservicewss_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/symantec-tutorial/tutorial_general_400.png)
    
1. Aby skonfigurować logowanie jednokrotne, po stronie usługi zabezpieczeń firmy Symantec w sieci Web (WSS), zapoznaj się z dokumentacją online programu WSS. Pobrany **XML metadanych** plik będzie musiał być importowane do portalu programu WSS. Skontaktuj się z pomocą [zespołu pomocy technicznej usługi zabezpieczeń firmy Symantec w sieci Web (WSS)](https://www.symantec.com/contact-us) Jeśli potrzebujesz pomocy przy użyciu konfiguracji w portalu programu WSS.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/symantec-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/symantec-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/symantec-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/symantec-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-symantec-web-security-service-wss-test-user"></a>Tworzenie użytkownika testowego usług zabezpieczeń firmy Symantec w sieci Web (WSS)

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w sieci Web usług zabezpieczeń (WSS) firmy Symantec. Odpowiednie nazwy użytkownika końcowego mogą być tworzone ręcznie w portalu programu WSS lub możesz poczekać, aż użytkownicy/grupy aprowizowane w usłudze Azure AD synchronizowane z portalem programu WSS po kilku minutach (około 15 minut). Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego. Publiczny adres IP komputera użytkownika końcowego, który będzie używany do przeglądania witryny sieci Web muszą być obsługiwana w portalu usługi zabezpieczeń firmy Symantec w sieci Web (WSS).

> [!NOTE]
> Proszę [tutaj](http://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1) można pobrać maszynie przez publiczny adres IP.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do sieci Web usług zabezpieczeń (WSS) firmy Symantec.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon do sieci Web usług zabezpieczeń (WSS) firmy Symantec, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **usług zabezpieczeń firmy Symantec w sieci Web (WSS)**.

    ![Link usługi zabezpieczeń firmy Symantec w sieci Web (WSS) na liście aplikacji](./media/symantec-tutorial/tutorial_symantecwebsecurityservicewss_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji będzie przetestować pojedynczego funkcję logowania jednokrotnego, teraz, gdy skonfigurowano usługi WSS konta usługi Azure AD dla uwierzytelnianie SAML.

Po skonfigurowaniu serwera proxy ruchu do programu WSS, przeglądarki sieci web, po otwarciu przeglądarki sieci web i spróbuj przejść do witryny, a następnie nastąpi przekierowanie do strony logowania platformy Azure. Wprowadź poświadczenia użytkownika testowego, że zainicjowano w usłudze Azure AD (czyli BrittaSimon) oraz skojarzone hasło. Po uwierzytelnieniu będzie można przejść do witryny sieci Web, która została wybrana. Należy utworzyć regułę zasad stronie WSS, aby zablokować BrittaSimon z przejście do określonej lokacji, a następnie powinna zostać wyświetlona strona bloku WSS, gdy spróbujesz przejść do tej lokacji jako użytkownik BrittaSimon.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/symantec-tutorial/tutorial_general_01.png
[2]: ./media/symantec-tutorial/tutorial_general_02.png
[3]: ./media/symantec-tutorial/tutorial_general_03.png
[4]: ./media/symantec-tutorial/tutorial_general_04.png

[100]: ./media/symantec-tutorial/tutorial_general_100.png

[200]: ./media/symantec-tutorial/tutorial_general_200.png
[201]: ./media/symantec-tutorial/tutorial_general_201.png
[202]: ./media/symantec-tutorial/tutorial_general_202.png
[203]: ./media/symantec-tutorial/tutorial_general_203.png


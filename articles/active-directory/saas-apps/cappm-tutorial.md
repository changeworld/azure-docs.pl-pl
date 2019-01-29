---
title: 'Samouczek: Integracja usługi Azure Active Directory przy użyciu urzędu certyfikacji PPM | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i PPM urzędu certyfikacji.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: ca9d5e71-e429-4891-8d10-3498e7210e89
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: jeedes
ms.openlocfilehash: f7d91dec828f9f10f9f127a8619c9796975788c1
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55181065"
---
# <a name="tutorial-azure-active-directory-integration-with-ca-ppm"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą PPM urzędu certyfikacji

W tym samouczku dowiesz się, jak zintegrować PPM urzędu certyfikacji za pomocą usługi Azure Active Directory (Azure AD).

Integrowanie PPM urzędu certyfikacji z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do urzędu certyfikacji PPM
- Użytkowników, aby automatycznie uzyskać zalogowanych do PPM urzędu certyfikacji (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z urzędu certyfikacji PPM, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Urząd certyfikacji PPM logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie PPM urzędu certyfikacji za pomocą galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-ca-ppm-from-the-gallery"></a>Dodawanie PPM urzędu certyfikacji za pomocą galerii
Aby skonfigurować integrację ppm urzędu certyfikacji w usłudze Azure AD, należy dodać PPM urzędu certyfikacji z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać PPM urzędu certyfikacji z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **PPM urzędu certyfikacji**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/cappm-tutorial/tutorial_cappm_search.png)

1. W panelu wyników wybierz **PPM urzędu certyfikacji**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/cappm-tutorial/tutorial_cappm_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą PPM urzędu certyfikacji, w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w PPM urzędu certyfikacji do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w PPM urzędu certyfikacji musi zostać ustanowione.

W PPM urzędu certyfikacji, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą PPM urzędu certyfikacji, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego urzędu certyfikacji PPM](#creating-a-ca-ppm-test-user)**  — aby odpowiednikiem Britta Simon w PPM urzędu certyfikacji, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji PPM urzędu certyfikacji.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z PPM urzędu certyfikacji, wykonaj następujące czynności:**

1. W witrynie Azure portal na **PPM urzędu certyfikacji** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/cappm-tutorial/tutorial_cappm_samlbase.png)

1. Na **urzędu certyfikacji PPM domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/cappm-tutorial/tutorial_cappm_url.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://ca.ondemand.saml.20.post.<companyname>`
    
    b. W **adres URL odpowiedzi** pola tekstowego, typ: `https://fedsso.ondemand.ca.com/affwebservices/public/saml2assertionconsumer`

    > [!NOTE] 
    > Ta wartość nie jest prawdziwe. Zaktualizuj tę wartość za pomocą rzeczywistego identyfikatora. Skontaktuj się z pomocą [zespołem pomocy technicznej PPM urzędu certyfikacji](mailto:catechnicalsupport@ca.com) aby zyskać tę wartość.
 
1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/cappm-tutorial/tutorial_cappm_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/cappm-tutorial/tutorial_general_400.png)

1. Na **konfiguracji PPM urzędu certyfikacji** , kliknij przycisk **skonfigurować urząd certyfikacji PPM** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki SAML** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/cappm-tutorial/tutorial_cappm_configure.png) 

1. Aby skonfigurować logowanie jednokrotne na **PPM urzędu certyfikacji** stronie, musisz wysłać pobrany **Certificate(Base64)** i **identyfikator jednostki SAML** do [zespołem pomocy technicznej PPM urzędu certyfikacji](mailto:catechnicalsupport@ca.com).

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/cappm-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/cappm-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/cappm-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/cappm-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-ca-ppm-test-user"></a>Tworzenie użytkownika testowego PPM urzędu certyfikacji

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w PPM urzędu certyfikacji. Praca z [zespołem pomocy technicznej PPM urzędu certyfikacji](mailto:catechnicalsupport@ca.com) Aby dodać użytkowników na platformie PPM urzędu certyfikacji.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do PPM urzędu certyfikacji.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon PPM urzędu certyfikacji, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **PPM urzędu certyfikacji**.

    ![Konfigurowanie logowania jednokrotnego](./media/cappm-tutorial/tutorial_cappm_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji możesz przetestować konfigurację logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po kliknięciu kafelka PPM urzędu certyfikacji w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji PPM urzędu certyfikacji.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cappm-tutorial/tutorial_general_01.png
[2]: ./media/cappm-tutorial/tutorial_general_02.png
[3]: ./media/cappm-tutorial/tutorial_general_03.png
[4]: ./media/cappm-tutorial/tutorial_general_04.png

[100]: ./media/cappm-tutorial/tutorial_general_100.png

[200]: ./media/cappm-tutorial/tutorial_general_200.png
[201]: ./media/cappm-tutorial/tutorial_general_201.png
[202]: ./media/cappm-tutorial/tutorial_general_202.png
[203]: ./media/cappm-tutorial/tutorial_general_203.png


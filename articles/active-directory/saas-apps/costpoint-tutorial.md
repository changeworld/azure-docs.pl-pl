---
title: 'Samouczek: Integracja usługi Azure Active Directory z programem Costpoint | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a costpoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9ecc5f58-4462-4ade-ab73-0a4f61027504
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01168540e27605db0d240c0774159a710b5d5254
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "71840093"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>Samouczek: Integrowanie costpoint z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować program Costpoint z usługą Azure Active Directory (Azure AD). Po zintegrowaniu costpoint z usługą Azure AD, można:

* Kontrola w usłudze Azure AD, który ma dostęp do Costpoint.
* Włącz użytkownikom automatyczne logowanie do costpoint za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego costpoint.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz samouszeńcę usługi Azure AD w środowisku testowym. Costpoint obsługuje **sp i idp** zainicjowane SSO.

## <a name="generate-costpoint-metadata"></a>Generowanie metadanych costpoint

Konfiguracja SSO costpoint SAML jest wyjaśniona w przewodniku **DeltekCostpoint711Security.pdf.** Pobierz ten przewodnik z witryny pomocy technicznej Deltek Costpoint i zapoznaj się z **sekcją Samozwiązkowanie** > z logiem**JEDNOkrotnym SAML między costpointem a platformą Microsoft Azure.** Postępuj zgodnie z instrukcjami i wygeneruj plik **XML metadanych federacji Costpoint SP.** 

![Narzędzie konfiguracji costpoint](./media/costpoint-tutorial/config-utility.png)

## <a name="add-costpoint-from-the-gallery"></a>Dodawanie costpoint z galerii

Aby zintegrować costpoint z usługą Azure AD, najpierw dodaj costpoint do listy zarządzanych aplikacji SaaS z galerii w witrynie Azure portal:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.

1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**

   ![Przycisk Azure Active Directory](common/select-azuread.png)

1. Wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

   ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.

   ![Przycisk Nowa aplikacja](common/add-new-app.png)

1. W sekcji **Dodaj z galerii** wprowadź **pozycję Costpoint** w polu wyszukiwania.

   ![Costpoint na liście wyników](common/search-new-app.png)

1. Na liście wyników wybierz pozycję **Costpoint**, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sgn-on"></a>Konfigurowanie i testowanie pojedynczego sgn-on usługi Azure AD

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą programu Costpoint przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w programie Costpoint.

Aby skonfigurować i przetestować sytą usługę Azure AD z kosztówie coli, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj sytuasz usługi Azure AD,](#configure-azure-ad-sso)** aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Skonfiguruj costpoint,](#configure-costpoint)** aby skonfigurować ustawienia logowania sytego SAML po stronie aplikacji.
1. **[Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user)** aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
1. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby włączyć B.Simon do korzystania z usługi Azure AD logowania jednokrotnego.
1. **[Utwórz użytkownika testowego Costpoint](#create-a-costpoint-test-user)** mieć odpowiednik B.Simon w Costpoint, który jest połączony z reprezentacji usługi Azure AD użytkownika.
1. **[Przetestuj sytą próbę sycącą,](#test-sso)** aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal:

1. Na stronie Integracja aplikacji **Costpoint** wybierz pozycję **Logowanie jednokrotne**.

   ![Konfigurowanie łącza logowania jednokrotnego](common/select-sso.png)

1. Jeśli *plik metadanych usługodawcy*zawiera **podstawową konfigurację SAML,** wykonaj następujące czynności:

   > [!NOTE]
   > Plik metadanych dostawcy usług w pliku [Generuj metadane Costpoint](#generate-costpoint-metadata). Jak korzystać z pliku jest wyjaśnione w dalszej części samouczka.
 
   1. Wybierz przycisk **Przekaż plik metadanych,** a następnie wybierz plik **XML XML federacji Costpoint SP** wcześniej wygenerowany przez costpoint, a następnie wybierz przycisk **Dodaj,** aby przekazać plik.

      ![Przekazywanie pliku metadanych](./media/costpoint-tutorial/upload-metadata.png)
    
   1. Po pomyślnym przekazaniu pliku metadanych wartości **identyfikatora** i **adresu URL odpowiedzi** są automatycznie wypełniane w sekcji Costpoint.

      > [!NOTE]
      > Jeśli wartości **adresu URL** **identyfikatora** i odpowiedzi nie są automatycznie polulated, wprowadź wartości ręcznie zgodnie z wymaganiami. Sprawdź, czy **identyfikator (identyfikator jednostki)** i **adres URL odpowiedzi (adres URL usługi konsumenta potwierdzenia)** są poprawnie ustawione, a **adres URL usługi ACS** jest prawidłowym adresem URL punktu costpoint, który kończy się **na pliku /LoginServlet.cps**.

   1. Wybierz **pozycję Ustaw dodatkowe adresy URL**. W polu **Stan przekazywania**wprowadź wartość`system=[your system]` przy użyciu następującego wzorca: (na przykład **system=DELTEKCP**).

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** wybierz ikonę **Kopiuj,** aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go w Notatniku.

   ![Certyfikat podpisywania SAML](common/copy-metadataurl.png)

### <a name="configure-costpoint"></a>Konfigurowanie punktu kosztów

1. Wróć do narzędzia konfiguracji costpoint. W polu **tekstowym XML metadanych federacji IdP** wklej zawartość pliku *adresu URL metadanych federacji aplikacji.* 

   ![Narzędzie konfiguracji costpoint](./media/costpoint-tutorial/config-utility-idp.png)

1. Kontynuuj instrukcje z **przewodnika DeltekCostpoint711Security.pdf,** aby zakończyć konfigurację Costpoint SAML.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest utworzenie użytkownika testowego w witrynie Azure portal o nazwie B.Simon.

1. W witrynie Azure portal w lewym okienku wybierz pozycję**Użytkownicy** >  **usługi Azure Active Directory** > **Wszyscy użytkownicy**.

   ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

1. Wybierz przycisk **Nowy użytkownik**.

   ![Przycisk Nowy użytkownik](common/new-user.png)

1. We właściwościach **Użytkownika** wykonaj następujące kroki:

   ![Okno dialogowe Użytkownik](common/user-properties.png)

   1. W polu **Nazwa** wprowadź **b.simon**.
   
   1. W polu **Nazwa** użytkownika `b.simon\@yourcompanydomain.extension` wprowadź (na przykład B.Simon@contoso.com).
   
   1. Zaznacz pole wyboru **Pokaż hasło,** a następnie zapisz wartość wyświetlaną w polu **Hasło.**
   
   1. Wybierz **pozycję Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć B.Simon do korzystania z usługi Azure logowania jednokrotnego, przyznając B.Simon dostęp do Costpoint.

1. W portalu Azure wybierz pozycję **Aplikacje** > dla**przedsiębiorstw Wszystkie aplikacje**.

1. Na liście aplikacji wybierz pozycję **Costpoint**.

1. W sekcji **Zarządzanie** na stronie przegląd aplikacji wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz przycisk **Dodaj użytkownika**. W oknie dialogowym **Dodawanie przydziału** wybierz pozycję **Użytkownicy i grupy**.

   ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** na liście **Użytkownicy** wybierz pozycję **B.Simon**. Następnie wybierz pozycję **Wybierz**.

1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie wybierz pozycję **Wybierz**.

1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="create-a-costpoint-test-user"></a>Tworzenie użytkownika testowego Costpoint

W tej sekcji utworzysz użytkownika w Costpoint. Załóżmy, że identyfikator użytkownika to **B.SIMON,** a nazwa użytkownika to **B.Simon**. Praca z [zespołem pomocy technicznej klienta Costpoint,](https://www.deltek.com/about/contact-us) aby dodać użytkownika na platformie Costpoint. Użytkownik musi zostać utworzony i aktywowany, zanim będzie mógł korzystać z logowania jednokrotnego.

Po utworzeniu użytkownika wybór **metody uwierzytelniania** użytkownika musi być **usługą Active Directory, należy zaznaczyć**pole wyboru **Saml z logiem jednokrotnym,** a nazwą użytkownika z usługi Azure Active Directory musi być **usługa Active Directory lub identyfikator certyfikatu** (pokazana na poniższym zrzucie ekranu).

![Użytkownik costpoint](./media/costpoint-tutorial/costpoint-user.png)

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka Costpoint w Panelu dostępu należy automatycznie zalogować się do aplikacji Costpoint, ponieważ skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integracji aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

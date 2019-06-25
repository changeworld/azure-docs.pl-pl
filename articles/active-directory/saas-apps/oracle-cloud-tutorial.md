---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą konsoli infrastruktury chmury Oracle | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Oracle chmurze infrastruktury konsoli.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: f045fe19-11f8-4ccf-a3eb-8495fdc8716f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 456c984e577e3427ce8cd62d6f63987118f2c8ed
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164152"
---
# <a name="tutorial-integrate-oracle-cloud-infrastructure-console-with-azure-active-directory"></a>Samouczek: Integracja programu Oracle chmurze infrastruktury konsoli za pomocą usługi Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować Oracle chmurze infrastruktury konsoli za pomocą usługi Azure Active Directory (Azure AD). Po zintegrowaniu programu Oracle chmurze infrastruktury konsoli z usługą Azure AD, możesz wykonywać następujące czynności:

* Kontrolowanie w usłudze Azure AD, kto ma dostęp do konsoli infrastruktury chmury programu Oracle.
* Umożliwianie użytkownikom można automatycznie zalogowany do oprogramowania Oracle chmurze infrastruktury konsoli przy użyciu konta usługi Azure AD.
* Zarządzanie Twoimi kontami w jednej centralnej lokalizacji — witryny Azure portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/).
* Oracle Cloud Console infrastruktury logowania jednokrotnego (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku, skonfiguruj i przetestuj logowania jednokrotnego usługi Azure AD w środowisku testowym. Konsola infrastruktury chmury Oracle obsługuje **SP** jednokrotne logowanie inicjowane przez.

## <a name="adding-oracle-cloud-infrastructure-console-from-the-gallery"></a>Dodawanie bazy danych Oracle Cloud Console infrastruktury za pomocą galerii

Aby skonfigurować integrację konsoli infrastruktury chmury programu Oracle do usługi Azure AD, należy dodać Oracle chmurze infrastruktury konsoli z galerii z listą zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** usługi.
1. Przejdź do **aplikacje dla przedsiębiorstw** , a następnie wybierz **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz **nową aplikację**.
1. W **Dodaj z galerii** sekcji, wpisz **Oracle chmurze infrastruktury konsoli** w polu wyszukiwania.
1. Wybierz **Oracle chmurze infrastruktury konsoli** z wyników panelu, a następnie dodać aplikację. Odczekaj kilka sekund, podczas gdy aplikacja zostanie dodany do Twojej dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Konfiguracja i testowanie logowania jednokrotnego usługi Azure AD za pomocą Oracle chmurze infrastruktury konsoli, za pomocą użytkownika testu o nazwie **B. Simon**. Logowania jednokrotnego do pracy należy ustanowić relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w konsoli programu Oracle chmurze infrastruktury.

Aby skonfigurować i przetestować logowania jednokrotnego usługi Azure AD za pomocą konsoli programu Oracle chmurze infrastruktury, wykonaj poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-sso)**  aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Konfigurowanie programu Oracle chmurze infrastruktury konsoli](#configure-oracle-cloud-infrastructure-console)**  do konfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  do testowania usługi Azure AD logowanie jednokrotne za pomocą B. Simon.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  umożliwiające Simon B. korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Tworzenie użytkownika testowego Oracle chmurze infrastruktury konsoli](#create-oracle-cloud-infrastructure-console-test-user)**  taki odpowiednik B. Simon w Oracle chmurze infrastruktury konsoli, która jest połączona z usługi Azure AD reprezentacja użytkownika.
1. **[Testowanie logowania jednokrotnego](#test-sso)**  Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

Wykonaj następujące kroki, aby włączyć logowania jednokrotnego usługi Azure AD w witrynie Azure portal.

1. W [witryny Azure portal](https://portal.azure.com/)na **Oracle chmurze infrastruktury konsoli** strona integracji aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **pojedynczego Logowanie jednokrotne**.
1. Na **wybierz jedną metodę logowania jednokrotnego** wybierz **SAML**.
1. Na **Ustaw się logowanie jednokrotne z SAML** kliknij ikonę edycji/pióra **podstawową konfigurację protokołu SAML** edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na **podstawową konfigurację protokołu SAML** strony, wprowadź wartości dla następujących pól:

   > [!NOTE]
   > Zostanie wyświetlony plik metadanych dostawcy usług z **Konfigurowanie programu Oracle chmurze infrastruktury konsoli logowania jednokrotnego** części samouczka.
    
   1. Kliknij przycisk **przekazywania pliku metadanych**.

   1. Kliknij pozycję **logo folderu** wybierz plik metadanych, a następnie kliknij przycisk **przekazywanie**.

   1. Po pomyślnym przekazaniu pliku metadanych **identyfikator** i **adres URL odpowiedzi** wartości Uzyskaj automatycznie wypełnione w **podstawową konfigurację protokołu SAML** sekcji w polu tekstowym.
    
      > [!NOTE]
      > Jeśli wartości **Identyfikator** i **Adres URL odpowiedzi** nie zostaną automatycznie wypełnione, wpisz te wartości ręcznie zgodnie z wymaganiami.

      W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://console.<REGIONNAME>.oraclecloud.com/`

      > [!NOTE]
      > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta konsoli infrastruktury chmury Oracle](https://www.oracle.com/support/advanced-customer-support/products/cloud.html) można uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na **Ustaw się logowanie jednokrotne z SAML** strony w **certyfikat podpisywania SAML** sekcji, Znajdź **XML metadanych Federacji** i wybierz **Pobierz** do pobrania certyfikatu i zapisz go na komputerze.

   ![Link pobierania certyfikatu](common/metadataxml.png)

1. Aplikacja Konsolowa infrastruktury chmury Oracle oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę  **Edytuj** , aby otworzyć okno dialogowe Atrybuty użytkownika.

   ![image](common/edit-attribute.png)

1. Ponadto powyżej aplikacja Konsolowa infrastruktury chmury Oracle oczekuje, że kilka więcej atrybutów, które mają być przekazywane w odpowiedzi SAML. W **atrybutów użytkowników i oświadczeń** sekcji na **oświadczenia grupy (wersja zapoznawcza)**  okno dialogowe, należy wykonać następujące czynności:

   1. Kliknij przycisk **pióra** obok **nazwę wartości identyfikatora**.

   1. Wybierz **trwały** jako **format identyfikatora nazwy wybierz**.
 
   1. Kliknij pozycję **Zapisz**.

      ![image](./media/oracle-cloud-tutorial/config07.png)
    
      ![image](./media/oracle-cloud-tutorial/config11.png)

   1. Kliknij przycisk **pióra** obok **grup zwracane w oświadczenie**.

   1. Wybierz **grup zabezpieczeń** z listy opcji.

   1. Wybierz **atrybutu źródła** z **identyfikator grupy**.

   1. Sprawdź **dostosować nazwy oświadczenia grupy**.

   1. W **nazwa** polu tekstowym **groupName**.

   1. W **Namespace (opcjonalnie)** polu tekstowym `https://auth.oraclecloud.com/saml/claims`.

   1. Kliknij pozycję **Zapisz**.

      ![image](./media/oracle-cloud-tutorial/config08.png)

1. Na **Konfigurowanie programu Oracle chmurze infrastruktury konsoli** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-oracle-cloud-infrastructure-console"></a>Konfigurowanie programu Oracle chmurze infrastruktury konsoli

1. W oknie przeglądarki internetowej innej Zaloguj się w konsoli programu Oracle chmurze infrastruktury jako Administrator.

1. Kliknij w menu po lewej stronie, a następnie kliknij pozycję **tożsamości** a następnie przejdź do **Federacji**.

   ![Konfigurowanie](./media/oracle-cloud-tutorial/config01.png)

1. Zapisz **plik metadanych dostawcy usług** , klikając **Pobierz ten dokument** łącze, a następnie przekaż go do **podstawową konfigurację protokołu SAML** części witryny Azure portal i następnie Kliknij pozycję **Dodawanie dostawcy tożsamości**.

   ![Konfigurowanie](./media/oracle-cloud-tutorial/config02.png)

1. Na **Dodawanie dostawcy tożsamości** wyskakujące, wykonaj następujące czynności:

   ![Konfigurowanie](./media/oracle-cloud-tutorial/config03.png)

   1. W **nazwa** tekstu wprowadź swoją nazwę.

   1. W **opis** tekstu wprowadź opis.

   1. Wybierz **MICROSOFT ACTIVE DIRECTORY FEDERATION Services (ADFS) lub dostawcy tożsamości zgodnych SAML 2.0** jako **typu**.

   1. Kliknij przycisk **Przeglądaj** do przekazania XML metadanych Federacji, który został pobrany z witryny Azure portal.

   1. Kliknij przycisk **Kontynuuj** i **Edytowanie dostawcy tożsamości** sekcji należy wykonać następujące czynności:

      ![Konfigurowanie](./media/oracle-cloud-tutorial/config09.png)

   1. Aby uzyskać **grupy dostawcy tożsamości** wprowadź nazwę grupy i identyfikator grupy, które są skonfigurowane w witrynie Azure portal. Grupa musi być mapowane z odpowiedniej grupy w **grupy OCI** pola.

   1. Zgodny z konfiguracją w witrynie Azure portal i Twoich potrzeb organizacji, można mapować wielu grup. Kliknij pozycję **+ Dodaj mapowanie** można dodać dowolną liczbę grup.

   1. Kliknij przycisk **Submit** (Prześlij).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzymy użytkownika testowego w witrynie Azure portal, o nazwie B. Simon.

1. W okienku po lewej stronie w witrynie Azure portal wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. W **użytkownika** właściwości, wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B. Simon`.  
   1. W **nazwa_użytkownika** wprowadź username@companydomain.extension. Na przykład `B. Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można udostępnić Simon B. do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do bazy danych Oracle chmurze infrastruktury konsoli.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz **Oracle chmurze infrastruktury konsoli**.
1. Na stronie Przegląd usługi aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **użytkowników i grup**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz **Dodaj użytkownika**, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

   ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W **użytkowników i grup** okno dialogowe, wybierz opcję **B. Simon** z listy użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. Jeśli oczekujesz wszelkie wartości roli dla asercji SAML w **wybierz rolę** okno dialogowe, wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-oracle-cloud-infrastructure-console-test-user"></a>Tworzenie użytkownika testowego Oracle chmurze infrastruktury konsoli

 Oracle chmurze infrastruktury konsoli obsługę just-in-time, który jest domyślnie. W tej sekcji nie musisz niczego robić. Podczas próby dostępu, a także nie konieczności tworzenia użytkownika nie utworzyć nowego użytkownika.

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka Oracle chmurze infrastruktury konsoli w panelu dostępu, nastąpi przekierowanie do strony logowania konsoli infrastruktury chmury programu Oracle. Wybierz **dostawcy tożsamości** menu rozwijanego, a następnie kliknij **Kontynuuj** jak pokazano poniżej, aby zalogować się. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

![Konfigurowanie](./media/oracle-cloud-tutorial/config10.png)

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

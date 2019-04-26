---
title: Ustawianie konta rozliczeniowego dla umowy klienta firmy Microsoft — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować konta rozliczeniowego dla umowy klienta firmy Microsoft.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: 90f832319fa2343003af58bd99eb64c0cbd94dd8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60371807"
---
# <a name="set-up-your-billing-account-for-a-microsoft-customer-agreement"></a>Ustawianie konta rozliczeniowego dla umowy klienta firmy Microsoft

Jeśli wygasł rejestrację umowy Enterprise Agreement lub chcesz być uznawane za wygasłe, można podpisać umowy klienta firmy Microsoft w celu odnowienia rejestracji. Odnowienie obejmuje następujące kroki:

1. Zaakceptuj Umowę programu nowych klientów firmy Microsoft. Praca z przedstawicielem firmy Microsoft pola, aby poznać szczegóły i zaakceptuj umowę z nowego.
2. Konfigurowanie nowego konta rozliczeniowego, utworzonym dla nowej umowy klienta firmy Microsoft.

W tym artykule opisano zmiany w istniejących rozliczeniami po instalacji i przeprowadzi Cię przez instalację nowego konta rozliczeniowego.

Aby skonfigurować konta rozliczeniowego, należy przejście rozliczeń subskrypcji platformy Azure ze rejestrację umowy Enterprise Agreement, do nowego konta. Instalator nie ma wpływu na usługi platformy Azure, które działają w Twoich subskrypcjach. Jednak zmienia sposób będziesz zarządzać rozliczeń dla subskrypcji.

- Zamiast [portalu EA](https://ea.azure.com), usług i rozliczeń, będziesz zarządzać w [witryny Azure portal](https://portal.azure.com).
- Przedstawiamy faktury miesięczna, cyfrowy opłat. Można przeglądać i analizować faktury w usłudze Azure Cost Management + rozliczenia strony.
- Zamiast działów i konto w rejestrację umowy Enterprise Agreement użyjesz rozliczeń struktury i zakresy z nowego konta zarządzania i organizowania rozliczeniami.

Przed rozpoczęciem instalacji, zaleca się, że wykonano następujące czynności:

- **Zrozumienie nowe konto rozliczeniowe**
  - Nowe konto upraszcza rozliczeń dla Twojej organizacji. [Uzyskać szybki przegląd konta rozliczeniowego](billing-mca-overview.md)
- **Sprawdź dostęp do ukończenia instalacji**
  - Tylko użytkownicy z określonymi uprawnieniami administracyjnymi może ukończyć instalację. Sprawdź, czy [dostępu do uprawnień wymaganych do ukończenia instalacji](#access-required-to-complete-the-setup).
- **Omówienie zmian w hierarchii rozliczeń**
  - Możesz nowe konto rozliczeniowe są zorganizowane w odmienny sposób niż rejestrację umowy Enterprise Agreement. [Omówienie zmian w hierarchii rozliczeń na nowym koncie](#understand-changes-to-your-billing-hierarchy).
- **Omówienie zmiany dostępu użytkownika administratorzy rozliczeń**
  - Administratorzy z rejestrację umowy Enterprise Agreement uzyskują dostęp do rozliczeń zakresy nowym koncie. [Omówienie zmian w ich dostęp](#understand-changes-to-your-billing-administrators-access).
- **Widok umowy Enterprise Agreement funkcji, które są zastępowane przez nowe konto**
  - Wyświetl funkcje rejestracji umowy Enterprise Agreement, które są zastępowane przez funkcje nowym koncie.
- **Wyświetlanie odpowiedzi na najczęściej zadawane pytania**
  - Widok [dodatkowe informacje](#additional-information) Aby dowiedzieć się więcej o ustawieniach.

## <a name="access-required-to-complete-the-setup"></a>Dostępu do uprawnień wymaganych do ukończenia instalacji

Aby ukończyć instalację, należy następujący dostęp:

- Właściciel profil rozliczeniowy, który został utworzony po podpisaniu umowy klienta firmy Microsoft. Aby dowiedzieć się więcej na temat rozliczeń profilów, zobacz [zrozumieć profile rozliczeń](billing-mca-overview.md#understand-billing-profiles).

- Administrator przedsiębiorstwa dotyczące rejestracji, który zostanie odnowiony.

### <a name="if-youre-not-an-enterprise-administrator-on-the-enrollment"></a>Jeśli nie jesteś administratorem przedsiębiorstwa na rejestrację

Możesz poprosić rejestracji Administratorzy przedsiębiorstwa, aby ukończyć instalację konta rozliczeniowego.

1. Zaloguj się do witryny Azure portal przy użyciu linku w wiadomości e-mail, która została wysłana do Ciebie po podpisaniu umowy klienta firmy Microsoft.

2. Jeśli ktoś w Twojej organizacji podpisanej umowy lub nie masz wiadomości e-mail, zaloguj się przy użyciu następującego linku. Zastąp **enrollmentNumber** numerem rejestracji umowy enterprise agreement, która została odnowiona.

   `https://portal.azure.com/#blade/Microsoft_Azure_Billing/EATransitionToMCA/enrollmentId/enrollmentNumber`

3. Wybierz Administratorzy przedsiębiorstwa, które mają zostać wysłane żądanie.

   ![Zrzut ekranu przedstawia zapraszanie Administratorzy przedsiębiorstwa](./media/billing-mca-setup-account/ea-mca-invite-admins.png)

4. Wybierz **żądań wysłania**.

   Administratorzy będą otrzymywać wiadomość e-mail z instrukcjami, aby ukończyć instalację.

### <a name="if-youre-not-an-owner-of-the-billing-profile"></a>Jeśli nie jesteś właścicielem profil rozliczeniowy

Użytkownik w Twojej organizacji, którzy podpisanej umowy klienta firmy Microsoft jest dodawany jako właściciela na profil rozliczeniowy. Należy poprosić użytkownika o dodanie Cię jako właściciela, dzięki czemu można ukończyć instalację.  <!-- Todo Are there any next steps -->

## <a name="understand-changes-to-your-billing-hierarchy"></a>Informacje o zmianach hierarchii rozliczeń

Nowe konto rozliczeniowe upraszcza rozliczeń w organizacji przy jednoczesnym zapewnieniu rozszerzone, rozliczeń i kosztów zarządzania. Poniższy diagram wyjaśnia sposób organizowania rozliczeń w nowe konta rozliczeniowego.

![Obraz przedstawiający ea mca-post przejścia hierarchii](./media/billing-mca-setup-account/mca-post-transition-hierarchy.png)

1. Konto rozliczeniowe umożliwia zarządzanie rozliczeniami dla umowy klienta firmy Microsoft. Aby dowiedzieć się więcej na temat konta rozliczeniowego, zobacz [zrozumieć konto rozliczeniowe](billing-mca-overview.md#understand-billing-account).
2. Profil rozliczeniowy umożliwia zarządzanie rozliczeń dla całej organizacji, podobnie jak rejestrację umowy Enterprise Agreement. Administratorzy przedsiębiorstwa usług stają się właścicieli profil rozliczeniowy. Aby dowiedzieć się więcej na temat rozliczeń profilów, zobacz [zrozumieć profile rozliczeń](billing-mca-overview.md#understand-billing-profiles).
3. Sekcja faktury umożliwia organizowanie koszty, zgodnie z potrzebami, podobnie jak działom w rejestrację umowy Enterprise Agreement. Dział staje się sekcje faktury, a dział Administratorzy stają się właścicieli sekcje odpowiednich faktury. Aby uzyskać więcej informacji na temat sekcje faktury, zobacz [zrozumienie faktury sekcje](billing-mca-overview.md#understand-invoice-sections).
4. Konta, które zostały utworzone w ramach umowy Enterprise Agreement, nie są obsługiwane przez nowe konta rozliczeniowego. Subskrypcje dla konta należą do sekcji faktury odpowiednie dla swojego wydziału. Właściciele kont można tworzyć i zarządzać subskrypcji dla ich sekcji faktury.

## <a name="understand-changes-to-your-billing-administrators-access"></a>Omówienie zmiany dostępu użytkownika administratorzy rozliczeń

W zależności od ich dostęp Administratorzy rozliczeń na rejestrację umowy Enterprise Agreement, Uzyskaj dostęp do rozliczeń zakresy na nowe konto. W poniższej tabeli opisano zmiany w programie access podczas instalacji:

| Istniejącej roli | Po przejściu roli |
| --- | --- |
| **Administrator przedsiębiorstwa (odczytu tylko = No)** | **— Właściciela profilu rozliczenia** </br> Zarządzanie wszystkim na profil rozliczeniowy </br> - **Właściciel sekcji faktury na wszystkie sekcje faktury** </br> Zarządzanie wszystkim, w sekcjach faktury |
| **Administrator przedsiębiorstwa (odczytu tylko = Yes)** | **— Czytelnik profilu rozliczenia** </br> Widok tylko do odczytu wszystkich elementów na konto rozliczeniowe</br>**— Faktury sekcja czytelnika na wszystkich sekcji faktury**</br> — Widok tylko do odczytu wszystkich elementów w sekcji faktury|
| **Administrator działu (odczytu tylko = No)** |**-Właściciel sekcji faktury w sekcji faktury utworzone dla swojego wydziału odpowiednich** </br>Zarządzanie wszystkim, w sekcji faktury|
| **Administrator działu (odczytu tylko = Yes)**|**-Czytnik sekcji faktury w sekcji faktury utworzone dla swojego wydziału odpowiednich**</br> Widok tylko do odczytu wszystkich elementów w sekcji faktury|
| **Właściciel konta** | **— Procedura tworzenia subskrypcji platforma azure w sekcji faktury utworzone dla swojego wydziału odpowiednich** </br>  Tworzenie subskrypcji platformy Azure dla swoich sekcji faktury|

Dzierżawy usługi Azure Active Directory jest zaznaczone dla nowego konta rozliczeniowego podczas podpisywania umowy klienta firmy Microsoft. Jeśli dzierżawa nie istnieje w Twojej organizacji, nową dzierżawę, zostanie utworzony. Dzierżawca reprezentuje organizacji w usłudze Azure Active Directory. Administratorzy dzierżawy globalnego w organizacji umożliwia zarządzanie dostępem do aplikacji i danych w Twojej organizacji dzierżawcy.

Nowe konto obsługuje tylko użytkownicy z dzierżawy, który został wybrany podczas podpisywania umowy klienta firmy Microsoft. Jeśli użytkownicy z uprawnieniami administracyjnymi w ramach umowy Enterprise Agreement są częścią dzierżawy, uzyskają dostęp do nowego konta rozliczeniowego podczas instalacji. Jeśli tak nie jest częścią dzierżawy, będą mogli korzystać z nowego konta rozliczeniowego, chyba, że możesz zaprosić.

Zaproszenie użytkowników są dodawane do dzierżawy jako gość użytkowników i uzyskać dostęp do konta rozliczeniowego. Aby zaprosić użytkowników, dostęp gościa musi być wyłączony dla dzierżawy. Aby uzyskać więcej informacji, zobacz [kontrolować dostęp gościa w usłudze Azure Active Directory](https://docs.microsoft.com/en-us/microsoftteams/teams-dependencies#control-guest-access-in-azure-active-directory). Dostęp gościa jest wyłączony, należy skontaktować się z Administratorzy globalni w Twojej dzierżawy, aby włączyć tę funkcję. <!-- Todo - How can they find their global administrator -->

## <a name="view-features-replaced-by-the-new-billing-account"></a>Funkcje widoku zastąpione przez nowe konto rozliczeniowe

Enterprise Agreement następujące funkcje są zastępowane nowych funkcji w ramach konta rozliczeniowego dla umowy klienta firmy Microsoft.

### <a name="enterprise-agreement-accounts"></a>Konta umowy Enterprise Agreement

Konta, które zostały utworzone w rejestrację umowy Enterprise Agreement, nie są obsługiwane przez nowe konta rozliczeniowego. Subskrypcje dla konta należą do sekcji faktury utworzone dla swojego wydziału odpowiednich. Właściciele kont stają się subskrypcji platformy Azure dla twórców i można tworzyć i zarządzać subskrypcji dla ich sekcji faktury.

### <a name="notification-contacts"></a>Kontakty powiadomień

Kontakty powiadomienia są wysyłane wiadomości e-mail z informacjami o usłudze Azure Enterprise Agreement. Nie są obsługiwane w ramach nowego konta rozliczeniowego. Powiadomienia dotyczące środków platformy Azure i faktury są wysyłane do użytkowników, którzy mają dostęp do profilów rozliczeń w ramach Twojego konta rozliczeniowego.

### <a name="spending-quotas"></a>Limity przydziału wydatków

Przydziały wydatków, które zostały ustawione dla działów w rejestrację umowy Enterprise Agreement są zastępowane budżetów w nowe konta rozliczeniowego. Budżet jest tworzony dla każdego przydziału wydatków nastavit działom w Twojej rejestracji. Aby uzyskać więcej informacji dotyczących budżetów, zobacz [tworzenie i zarządzanie Azure budżetów](../cost-management/manage-budgets.md).

### <a name="cost-centers"></a>Centrów kosztów

Centrum kosztu, które zostały ustawione na subskrypcje platformy Azure w Twojej rejestracji Enterprise Agreement zostaną przeniesione na nowym koncie rozliczeń. Jednakże centrów kosztów dla działów i umowy Enterprise Agreement kont nie są obsługiwane.

## <a name="additional-information"></a>Dodatkowe informacje

Poniższe sekcje zawierają dodatkowe informacje na temat konfigurowania konta rozliczeniowego.

### <a name="no-service-downtime"></a>Nie przestojów

Usługi platformy Azure w ramach Twojej subskrypcji będą działały bez jakiejkolwiek przerwy. Przenosimy jedynie relacje rozliczeniowe dotyczące Twoich subskrypcji platformy Azure. Nie będzie to miało wpływu na istniejące zasoby, grupy zasobów czy grupy zarządzania.

### <a name="user-access-to-azure-resources"></a>Dostęp użytkowników do zasobów platformy Azure

Dostęp do zasobów platformy Azure, która została ustawiona przy użyciu funkcji Azure RBAC (kontrola dostępu oparta na rolach) nie zmienia się podczas przejścia.

### <a name="azure-reservations"></a>Rezerwacje platformy Azure

Wszystkie rezerwacje platformy Azure w Twojej rejestracji Enterprise Agreement jest przenoszona do nowego konta rozliczeniowego. Podczas przenoszenia nie nastąpią żadne zmiany rabatów dotyczących rezerwacji, które obowiązują dla Twoich subskrypcji.

### <a name="azure-marketplace-products"></a>Produkty platformy Azure Marketplace

Wszystkie produkty z witryny Azure Marketplace w Twojej rejestracji umowy Enterprise są przenoszone wraz z subskrypcji. Nie będzie wszelkie zmiany dostępu do usługi Marketplace produktów podczas przejścia.

### <a name="support-plan"></a>Plan pomocy technicznej

Korzyści z pomocy technicznej nie są przenoszone w ramach przejścia. Kup nowy plan pomocy technicznej Aby uzyskać korzyści dla subskrypcji platformy Azure w ramach nowego konta rozliczeniowego.

### <a name="past-charges-and-balance"></a>Ostatnie opłaty i saldo

Naliczanie opłat i środki na korzystanie z Saldo przed przejścia mogą być wyświetlane w Twojej rejestracji Enterprise Agreement, za pośrednictwem witryny Azure portal. <!--Todo - Add a link for this-->

### <a name="when-should-the-setup-be-completed"></a>Kiedy należy wykonać instalację?

Przed wygaśnięciem rejestrację umowy Enterprise Agreement, należy ukończyć konfigurowanie konta rozliczeniowego. Jeśli rejestracja wygaśnie, usługi w subskrypcji platformy Azure nadal pozostanie na uruchomione bez przerw w działaniu. Jednak opłata wyniesie stawki detaliczne dla usług.

### <a name="changes-to-the-enterprise-agreement-enrollment-after-the-setup"></a>Zmiany w rejestracji umowy Enterprise Agreement po instalacji

Subskrypcje platformy Azure, które są tworzone dla rejestracji umowy Enterprise Agreement, po przejściu można ręcznie przenieść do nowego konta rozliczeniowego. Aby uzyskać więcej informacji, zobacz [uzyskać rozliczeń własności subskrypcji platformy Azure od innych użytkowników](billing-mca-request-billing-ownership.md). Można przenieść rezerwacje platformy Azure, zakupionego po przeniesieniu [skontaktuj się z działem pomocy technicznej systemu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Możesz również zapewnić użytkownikom dostęp do konta rozliczeniowego po przeniesieniu. Aby uzyskać więcej informacji, zobacz [Zarządzanie rolami rozliczeń w witrynie Azure portal](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)

### <a name="revert-the-transition"></a>Przywróć przejścia

Nie można przywrócić przejścia. Gdy rozliczeń subskrypcji platformy Azure jest przenoszone do nowego konta rozliczeniowego, nie można przywrócić go do Twojej rejestracji Enterprise Agreement.

### <a name="closing-your-browser-during-setup"></a>Zamknięcie przeglądarki podczas instalacji

Przed kliknięciem przycisku na **Rozpocznij przejście**, możesz zamknąć przeglądarkę. Możesz wrócić do Instalatora przy użyciu łącze, które masz w wiadomości e-mail i uruchom przejścia. Zamknięcie przeglądarki, po uruchomieniu przejścia przejście pozostanie na uruchomione. Wróć do strony stan przejścia do monitorowania najnowszy stan przejście. Otrzymasz wiadomość e-mail po zakończeniu przejścia.

## <a name="complete-the-setup-in-the-azure-portal"></a>Kończenie konfiguracji w witrynie Azure portal

Aby ukończyć instalację, należy dostęp do nowego konta rozliczeniowego oraz rejestracji umowy Enterprise Agreement. Aby uzyskać więcej informacji, zobacz [dostępu do uprawnień wymaganych, aby ukończyć konfigurowanie konta rozliczeniowego](#access-required-to-complete-the-setup).

1. Zaloguj się do witryny Azure portal przy użyciu linku w wiadomości e-mail, która została wysłana do Ciebie po podpisaniu umowy klienta firmy Microsoft.

2. Jeśli ktoś w Twojej organizacji podpisanej umowy lub nie masz wiadomości e-mail, zaloguj się przy użyciu następującego linku. Zastąp **enrollmentNumber** numerem rejestracji umowy Enterprise Agreement, która została odnowiona.

   `https://portal.azure.com/#blade/Microsoft_Azure_Billing/EATransitionToMCA/enrollmentId/enrollmentNumber`

3. Wybierz **Rozpocznij przejście** w ostatnim kroku konfiguracji. Po wybraniu przejścia rozpoczęcia:

    ![Zrzut ekranu przedstawiający Kreatora instalacji](./media/billing-mca-setup-account/ea-mca-set-up-wizard.png)

    - Rozliczeń hierarchii odpowiadający hierarchii umowy Enterprise Agreement, jest tworzony w nowe konta rozliczeniowego. Aby uzyskać więcej informacji, zobacz [zrozumieć zmiany rozliczeń hierarchii](#understand-changes-to-your-billing-hierarchy).
    - Administratorzy z rejestrację umowy Enterprise Agreement mających dostęp do nowego konta rozliczeniowego, tak, aby nadal zarządzają rozliczeniami dla Twojej organizacji.
    - Naliczanie opłat za subskrypcje platformy Azure jest przenoszone do nowego konta. **Nie będzie żadnego wpływu na usługi platformy Azure podczas tego przejścia. Będą działały bez żadnych zakłóceń**.
    - W przypadku rezerwacji platformy Azure są przenoszone do nowego konta rozliczeniowego przy użyciu tego samego rabat w wysokości i czas trwania. Rabat związany z rezerwacją będzie nadal stosowany podczas przejścia.

4. Możesz monitorować stan przejścia na **przejście stanu** strony.

   ![Zrzut ekranu pokazujący stan przejścia](./media/billing-mca-setup-account/ea-mca-set-up-status.png)

## <a name="validate-the-billing-account-is-set-up-properly"></a>Sprawdź poprawność rozliczenia konta są prawidłowo skonfigurowane

 Sprawdź poprawność następujących czynności, aby upewnić się, że nowe konto rozliczeniowe jest prawidłowo skonfigurowane:

### <a name="azure-subscriptions"></a>Subskrypcje platformy Azure

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

   ![Zrzut ekranu pokazujący usługi Azure search w portalu](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. Wybierz profil rozliczeniowy. W zależności od praw dostępu może być konieczne wybranie konta rozliczeniowego. Wybierz z konta rozliczeniowego **rozliczeń profile** i następnie profil rozliczeniowy.

4. Wybierz **subskrypcji platformy Azure** z lewej strony.

   ![Zrzut ekranu przedstawiający listę subskrypcji](./media/billing-mca-setup-account/billing-mca-subscriptions-post-transition.png)

Subskrypcje platformy Azure, które są przenoszone z rejestrację umowy Enterprise Agreement do nowego konta rozliczeniowego, są wyświetlane na stronie subskrypcje platformy Azure. Jeśli brakuje dowolnej subskrypcji, przejście rozliczania subskrypcji ręcznie w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [uzyskać rozliczeń własności subskrypcji platformy Azure od innych użytkowników](billing-mca-request-billing-ownership.md)

### <a name="azure-reservations"></a>Rezerwacje platformy Azure

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

   ![Zrzut ekranu pokazujący usługi Azure search w portalu](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. Wybierz sekcję faktury. W zależności od praw dostępu może być konieczne wybranie konta rozliczeniowego lub profilu rozliczeniowego.  Konto rozliczeniowe lub profil rozliczeniowy wybierz **faktury sekcje** i następnie sekcji faktury.

    ![Zrzut ekranu przedstawiający listę przejścia wpis w sekcji faktury](./media/billing-mca-setup-account/billing-mca-invoice-sections-post-transition.png)

4. Wybierz **wszystkie produkty** z lewej strony.

5. Wyszukiwanie **zarezerwowanych**.

    ![Zrzut ekranu przedstawiający listę subskrypcji wpis przejścia](./media/billing-mca-setup-account/billing-mca-azure-reservations-post-transition.png)

Rezerwacje platformy Azure, które są przenoszone z rejestrację umowy Enterprise Agreement do nowego konta rozliczeniowego, są wyświetlane na stronie wszystkie produkty. Powtórz kroki dla wszystkich sekcji faktury sprawdzić, czy wszystkie rezerwacje na platformie Azure są przenoszone z rejestrację umowy Enterprise Agreement. Jeśli uważasz, że brakuje zastrzeżenie Azure [skontaktuj się z działem pomocy technicznej platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) przenieść rezerwacji do nowego konta rozliczeniowego.

### <a name="access-of-enterprise-administrators-on-the-billing-profile"></a>Dostęp Administratorzy przedsiębiorstwa na profil rozliczeniowy

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

   ![Zrzut ekranu pokazujący usługi Azure search w portalu](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. Wybierz profil rozliczeniowy utworzony dla Twojej rejestracji. W zależności od praw dostępu może być konieczne wybranie konta rozliczeniowego.  Wybierz z konta rozliczeniowego **rozliczeń profile** i następnie profil rozliczeniowy.

4. Wybierz **kontrola dostępu (IAM)** po lewej stronie.

   ![Zrzut ekranu pokazujący dostępu przejścia wpis Administratorzy przedsiębiorstwa](./media/billing-mca-setup-account/billing-mca-ea-admins-access-post-transition.png)

Administratorzy przedsiębiorstwa są wyświetlane jako rozliczeń właścicieli profilu podczas przedsiębiorstwa, Administratorzy z uprawnieniami tylko do odczytu są wymienione jako rozliczeń czytelnicy profilu. Jeśli Twoim zdaniem Brak dostępu do żadnych administratorów przedsiębiorstwa, możesz udzielić im dostępu w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [Zarządzanie rolami rozliczeń w witrynie Azure portal](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="access-of-enterprise-administrators-department-administrators-and-account-owners-on-invoice-sections"></a>Dostęp Administratorzy przedsiębiorstwa, Administratorzy działów i właścicieli kont z odpowiednimi w sekcjach faktury

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

   ![Zrzut ekranu pokazujący usługi Azure search w portalu](./media/billing-mca-setup-account/billing-search-cost-management-billing.png).

3. Wybierz sekcję faktury. Faktury sekcje mają taką samą nazwę jak ich poszczególnych działach w rejestracji umowy Enterprise Agreement. W zależności od dostęp może być konieczne wybierz profil rozliczeń lub konta rozliczeniowego. Profil rozliczeniowy lub konta rozliczeniowego, zaznacz **faktury sekcje** , a następnie wybierz sekcję faktury.

   ![Zrzut ekranu przedstawiający listę przejścia wpis w sekcji faktury](./media/billing-mca-setup-account/billing-mca-invoice-sections-post-transition.png)

4. Wybierz **kontrola dostępu (IAM)** po lewej stronie.

    ![Zrzut ekranu pokazujący dostępu działu i konta administratorów dostępu przejścia wpis](./media/billing-mca-setup-account/billing-mca-department-account-admins-access-post-transition.png)

Administratorzy przedsiębiorstwa i Administratorzy działów są wyświetlane jako właścicieli sekcji faktury lub czytników sekcji faktury podczas właścicieli kont z odpowiednimi używanych w dziale są wyświetlane jako twórców subskrypcji platformy Azure. Powtórz krok dla wszystkich sekcji faktury sprawdzić dostęp do wszystkich działach w rejestrację umowy Enterprise Agreement. Właściciele kont, które nie były częścią dział otrzyma uprawnienia w sekcji faktury o nazwie **domyślnej sekcji faktury**. Jeśli Twoim zdaniem Brak dostępu do żadnych administratorów, możesz udzielić im dostępu w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [Zarządzanie rolami rozliczeń w witrynie Azure portal](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.

## <a name="next-steps"></a>Kolejne kroki

- [Rozpoczynanie pracy z nowym kontem rozliczeniowym](billing-mca-overview.md)

- [Wykonywanie zadań umowy Enterprise Agreement do konta rozliczeniowego dla umowy klienta firmy Microsoft](billing-mca-enterprise-operations.md)

- [Zarządzanie dostępem do konta rozliczeniowego](billing-understand-mca-roles.md)

---
title: Konfigurowanie konta rozliczeniowego dla umowy klienta firmy Microsoft — Azure
description: Dowiedz się, jak skonfigurować konto rozliczeniowe dla umowy klienta firmy Microsoft.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 947bfe85d94a5d11eeb54bd6b24c4c515af024d4
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490690"
---
# <a name="set-up-your-billing-account-for-a-microsoft-customer-agreement"></a>Konfigurowanie konta rozliczeniowego dla umowy klienta firmy Microsoft

Jeśli Twoja rejestracja umowy Enterprise Agreement wygasła lub wkrótce wygaśnie, możesz podpisać umowę klienta firmy Microsoft, aby odnowić rejestrację. W tym artykule opisano zmiany w istniejących rozliczeniach po skonfigurowaniu i procedurę konfigurowania nowego konta rozliczeniowego. Procedura odnowienia obejmuje następujące kroki:

1. Zaakceptowanie nowej umowy klienta firmy Microsoft. Skontaktowanie się z przedstawicielem firmy Microsoft w celu poznania szczegółów nowej umowy i zaakceptowania jej.
2. Skonfigurowanie nowego konta rozliczeniowego utworzonego dla nowej umowy klienta firmy Microsoft.

Aby skonfigurować konto rozliczeniowe, musisz przenieść rozliczanie Twoich subskrypcji platformy Azure z rejestracji umowy Enterprise Agreement do nowego konta. Konfiguracja nie wpływa na usługi platformy Azure, które działają w ramach Twoich subskrypcji. Jednak zmienia sposób zarządzania rozliczeniami dla subskrypcji.

- Zamiast w witrynie [EA Portal](https://ea.azure.com) będziesz zarządzać usługami i rozliczeniami platformy Azure w witrynie [Azure Portal](https://portal.azure.com).
- Będziesz otrzymywać miesięczne faktury cyfrowe z naliczonymi opłatami. Fakturę możesz wyświetlić i przeanalizować na stronie Zarządzanie kosztami i rozliczenia platformy Azure.
- Zamiast działów i konta w ramach rejestracji umowy Enterprise Agreement będziesz używać struktury i zakresów rozliczeń z nowego konta do zarządzania rozliczeniami i organizowania ich.

Przed rozpoczęciem konfigurowania zalecamy zapoznanie się z następującymi artykułami:

- **Omówienie nowego konta rozliczeniowego**
  - Nowe konto upraszcza rozliczanie dla organizacji. [Krótki przegląd nowego konta rozliczeniowego](billing-mca-overview.md)
- **Weryfikowanie dostępu na potrzeby wykonania konfiguracji**
  - Konfigurację mogą wykonać tylko użytkownicy z pewnymi uprawnieniami administracyjnymi. Sprawdź, czy masz [dostęp wymagany do wykonania konfiguracji](#access-required-to-complete-the-setup).
- **Omówienie zmian w hierarchii rozliczeń**
  - Nowe konto rozliczeniowe jest zorganizowane inaczej niż rejestracja umowy Enterprise Agreement. [Omówienie zmian w hierarchii rozliczeń w ramach nowego konta](#understand-changes-to-your-billing-hierarchy).
- **Omówienie zmian dostępu dla administratorów rozliczeń**
  - Administratorzy z rejestracji umowy Enterprise Agreement uzyskają dostęp do zakresów rozliczeń na nowym koncie. [Zapoznaj się ze zmianami dotyczącymi ich dostępu](#changes-to-billing-administrator-access).
- **Wyświetlanie funkcji umowy Enterprise Agreement zastąpionych przez nowe konto**
  - Wyświetl funkcje rejestracji umowy Enterprise Agreement zastąpione przez funkcje na nowym koncie.
- **Wyświetlanie odpowiedzi na najczęściej zadawane pytania**
  - Wyświetl [dodatkowe informacje](#additional-information), aby dowiedzieć się więcej o konfiguracji.

## <a name="access-required-to-complete-the-setup"></a>Dostęp wymagany do wykonania konfiguracji

Do wykonania konfiguracji jest wymagany następujący poziom dostępu:

- Właściciel profilu rozliczeniowego utworzonego podczas podpisywania umowy klienta firmy Microsoft. Aby dowiedzieć się więcej o profilach rozliczeniowych, zobacz [Omówienie profilów rozliczeniowych](billing-mca-overview.md#billing-profiles).

- Administrator przedsiębiorstwa dla odnawianej rejestracji.

### <a name="if-youre-not-an-enterprise-administrator-on-the-enrollment"></a>Jeśli nie jesteś administratorem przedsiębiorstwa dla rejestracji

Możesz zwrócić się do administratorów przedsiębiorstwa dla rejestracji o wykonanie konfiguracji Twojego konta rozliczeniowego.

1. Zaloguj się do witryny Azure Portal przy użyciu linku w wiadomości e-mail wysłanej do Ciebie po podpisaniu umowy klienta firmy Microsoft.

2. Jeśli ktoś inny z organizacji podpisał umowę lub nie masz wiadomości e-mail, zaloguj się przy użyciu poniższego linku. Zastąp ciąg **enrollmentNumber** numerem rejestracyjnym odnowionej umowy Enterprise Agreement.

   `https://portal.azure.com/#blade/Microsoft_Azure_Billing/EATransitionToMCA/enrollmentId/enrollmentNumber`

3. Wybierz administratorów przedsiębiorstwa, do których chcesz wysłać wniosek.

   ![Zrzut ekranu pokazujący zaproszenie dla administratorów przedsiębiorstwa](./media/billing-mca-setup-account/ea-mca-invite-admins.png)

4. Wybierz pozycję **Wyślij wniosek**.

   Administratorzy otrzymają wiadomość e-mail z instrukcjami wykonania konfiguracji.

### <a name="if-youre-not-an-owner-of-the-billing-profile"></a>Jeśli nie jesteś właścicielem profilu rozliczeniowego

Użytkownik organizacji, który podpisał umowę klienta firmy Microsoft, jest dodawany jako właściciel profilu rozliczeniowego. Zwróć się do tego użytkownika o dodanie Cię jako właściciela, aby umożliwić Ci wykonanie konfiguracji.  <!-- Todo Are there any next steps -->

## <a name="understand-changes-to-your-billing-hierarchy"></a>Omówienie zmian w hierarchii rozliczeń

Nowe konto rozliczeniowe upraszcza rozliczanie w organizacji, a jednocześnie udostępnia rozszerzone możliwości zarządzania rozliczeniami i kosztami. Na poniższym diagramie pokazano organizację rozliczeń w ramach nowego konta rozliczeniowego.

![Obraz elementu ea-mca-post-transition-hierarchy](./media/billing-mca-setup-account/mca-post-transition-hierarchy.png)

1. Konto rozliczeniowe służy do zarządzania rozliczeniami dla umowy klienta firmy Microsoft. Aby dowiedzieć się więcej o koncie rozliczeniowym, zobacz [Omówienie konta rozliczeniowego](billing-mca-overview.md#your-billing-account).
2. Profil rozliczeniowy służy do zarządzania rozliczeniami dla organizacji, podobnie jak w przypadku rejestracji umowy Enterprise Agreement. Administratorzy przedsiębiorstwa stają się właścicielami profilu rozliczeniowego. Aby dowiedzieć się więcej o profilach rozliczeniowych, zobacz [Omówienie profilów rozliczeniowych](billing-mca-overview.md#billing-profiles).
3. Sekcja faktur służy do organizowania kosztów zgodnie z potrzebami, podobnie jak w przypadku działów w rejestracji umowy Enterprise Agreement. Działy stają się sekcjami faktur, a administratorzy działów stają się właścicielami odpowiednich sekcji faktur. Aby dowiedzieć się więcej na temat sekcji faktur, zobacz [omówienie sekcji faktur](billing-mca-overview.md#invoice-sections).
4. Konta utworzone w ramach umowy Enterprise Agreement nie są obsługiwane na nowym koncie rozliczeniowym. Subskrypcje konta należą do sekcji faktur odpowiedniej dla ich działu. Właściciele konta mogą tworzyć subskrypcje dla swoich sekcji faktur i zarządzać nimi.

## <a name="changes-to-billing-administrator-access"></a>Zmiany w dostępie administratora rozliczeń

W zależności od posiadanego dostępu administratorzy rozliczeń w rejestracji umowy Enterprise Agreement uzyskują dostęp do zakresów rozliczeń na nowym koncie. W poniższej tabeli przedstawiono zmiany dostępu w ramach konfiguracji:

| Istniejąca rola | Rola po przeniesieniu |
| --- | --- |
| **Administrator przedsiębiorstwa (Tylko odczyt = Nie)** | **- Właściciel profilu rozliczeniowego** </br> Zarządzanie wszystkimi elementami profilu rozliczeniowego </br> - **Właściciel sekcji faktur dla wszystkich sekcji faktur** </br> Zarządzanie wszystkimi elementami w sekcjach faktur |
| **Administrator przedsiębiorstwa (Tylko odczyt = Tak)** | **- Czytelnik profilu rozliczeniowego** </br> - Widok tylko do odczytu dla wszystkich elementów konta rozliczeniowego</br>**- Czytelnik sekcji faktur dla wszystkich sekcji faktur**</br> - Widok tylko do odczytu wszystkich elementów sekcji faktur|
| **Administrator działu (Tylko odczyt = Nie)** |**- Właściciel sekcji faktur utworzonej dla jego działu** </br>Zarządzanie wszystkimi elementami sekcji faktur|
| **Administrator działu (Tylko odczyt = Tak)**|**- Czytelnik sekcji faktur utworzonej dla jego działu**</br> Widok tylko do odczytu wszystkich elementów w sekcji faktur|
| **Właściciel konta** | **- Twórca subskrypcji platformy Azure dla sekcji faktur utworzonej dla jego działu** </br>  Tworzenie subskrypcji platformy Azure dla swojej sekcji faktur|

Dla nowego konta rozliczeniowego jest wybierana dzierżawa usługi Azure Active Directory podczas podpisywania umowy klienta firmy Microsoft. Jeśli dzierżawa dla organizacji nie istnieje, zostanie utworzona nowa dzierżawa. Dzierżawa reprezentuje organizację w usłudze Azure Active Directory. Globalni administratorzy dzierżawy w organizacji używają dzierżawy do zarządzania dostępem dla aplikacji i danych w organizacji.

Nowe konto obsługuje tylko użytkowników z dzierżawy wybranej podczas podpisywania umowy klienta firmy Microsoft. Jeśli użytkownicy z uprawnieniami administracyjnymi dla umowy Enterprise Agreement są częścią dzierżawy, uzyskają dostęp do nowego konta rozliczeniowego podczas konfigurowania. Jeśli nie są częścią dzierżawy, nie będą mieli dostępu do nowego konta rozliczeniowego, chyba że zostaną zaproszeni.

Gdy zapraszasz użytkowników, są oni dodawani do dzierżawy jako użytkownicy-goście i uzyskują dostęp do konta rozliczeniowego. Aby zaprosić użytkowników, należy włączyć dla dzierżawy dostęp dla gości. Aby uzyskać więcej informacji, zobacz [Kontrolowanie dostępu gościa w usłudze Azure Active Directory](https://docs.microsoft.com/microsoftteams/teams-dependencies#control-guest-access-in-azure-active-directory). Jeśli dostęp gościa jest wyłączony, skontaktuj się z administratorem globalnym dzierżawy, aby go włączyć. <!-- Todo - How can they find their global administrator -->

## <a name="view-replaced-features"></a>Wyświetlanie zastąpionych funkcji

Następujące funkcje umowy Enterprise Agreement są zastępowane nowymi funkcjami w ramach konta rozliczeniowego umowy klienta firmy Microsoft.

### <a name="enterprise-agreement-accounts"></a>Konta umowy Enterprise Agreement

Konta utworzone w ramach rejestracji umowy Enterprise Agreement nie są obsługiwane na nowym koncie rozliczeniowym. Subskrypcje konta należą do sekcji faktur utworzonej dla odpowiadającego im działu. Właściciele konta stają się twórcami subskrypcji platformy Azure oraz mogą tworzyć subskrypcje i zarządzać nimi dla swoich sekcji faktur.

### <a name="notification-contacts"></a>Kontakty dla powiadomień

Kontakty dla powiadomień otrzymują wiadomości e-mail dotyczące umowy Enterprise Agreement platformy Azure. Nie są one obsługiwane na nowym koncie rozliczeniowym. Wiadomości e-mail dotyczące środków i faktur platformy Azure są wysyłane do użytkowników z dostępem do profilów rozliczeniowych w ramach konta rozliczeniowego.

### <a name="spending-quotas"></a>Limity przydziału wydatków

Limity przydziału wydatków ustawione dla działów w rejestracji umowy Enterprise Agreement są zastępowane budżetami na nowym koncie rozliczeniowym. Dla każdego limitu przydziału wydatków ustawionego dla działów w rejestracji jest tworzony budżet. Aby uzyskać więcej informacji na temat budżetów, zobacz [Tworzenie budżetów platformy Azure i zarządzanie nimi](../cost-management/manage-budgets.md).

### <a name="cost-centers"></a>Centra kosztów

Centra kosztów ustawione dla subskrypcji platformy Azure w ramach rejestracji umowy Enterprise Agreement są przenoszone do nowego konta rozliczeniowego. Jednak centra kosztów działów i kont umowy Enterprise Agreement nie są obsługiwane.

## <a name="additional-information"></a>Dodatkowe informacje

W następujących sekcjach znajdują się dodatkowe informacje na temat konfigurowania konta rozliczeniowego.

### <a name="no-service-downtime"></a>Brak przestojów usługi

Usługi platformy Azure w ramach Twojej subskrypcji będą działały bez jakiejkolwiek przerwy. Przenosimy jedynie relacje rozliczeniowe dotyczące Twoich subskrypcji platformy Azure. Nie będzie to miało wpływu na istniejące zasoby, grupy zasobów czy grupy zarządzania.

### <a name="user-access-to-azure-resources"></a>Dostęp użytkowników do zasobów platformy Azure

Proces przeniesienia nie wpływa na dostęp do zasobów platformy Azure ustawiony przy użyciu funkcji kontroli dostępu opartej na rolach platformy Azure.

### <a name="azure-reservations"></a>Rezerwacje platformy Azure

Wszystkie rezerwacje platformy Azure w ramach rejestracji umowy Enterprise Agreement są przenoszone na nowe konto rozliczeniowe. Podczas przenoszenia nie nastąpią żadne zmiany rabatów dotyczących rezerwacji, które obowiązują dla Twoich subskrypcji.

### <a name="azure-marketplace-products"></a>Produkty z witryny Azure Marketplace

Wszystkie produkty z witryny Azure Marketplace w ramach rejestracji umowy Enterprise Agreement są przenoszone razem z subskrypcjami. Podczas przeniesienia nie zostaną wprowadzone żadne zmiany w dostępie do usług dla produktów z witryny Marketplace.

### <a name="support-plan"></a>Plan pomocy technicznej

Korzyści pomocy technicznej nie są transferowane w ramach przenoszenia. Kup nowy plan pomocy technicznej, aby uzyskać korzyści dla subskrypcji platformy Azure na nowym koncie rozliczeniowym.

### <a name="past-charges-and-balance"></a>Wcześniejsze opłaty i saldo

Opłaty i saldo środków sprzed przeniesienia można wyświetlić w rejestracji umowy Enterprise Agreement w witrynie Azure Portal. <!--Todo - Add a link for this-->

### <a name="when-should-the-setup-be-completed"></a>Kiedy należy wykonać konfigurację?

Wykonaj konfigurację konta rozliczeniowego przed wygaśnięciem rejestracji umowy Enterprise Agreement. Jeśli rejestracja wygaśnie, usługi w ramach subskrypcji platformy Azure nadal będą działać bez zakłóceń. Jednak będą za nie naliczane opłaty według stawek detalicznych.

### <a name="changes-to-the-enterprise-agreement-enrollment-after-the-setup"></a>Zmiany rejestracji umowy Enterprise Agreement po wykonaniu konfiguracji

Subskrypcje platformy Azure utworzone na potrzeby rejestracji umowy Enterprise Agreement można po przeniesieniu ręcznie przetransferować do nowego konta rozliczeniowego. Aby uzyskać więcej informacji, zobacz [Pobieranie własności rozliczeń subskrypcji platformy Azure od innych użytkowników](billing-mca-request-billing-ownership.md). Aby przetransferować rezerwacje platformy Azure zakupione po przeniesieniu, [skontaktuj się z pomocą techniczną platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Możesz także zapewnić użytkownikom dostęp do konta rozliczeniowego po przeniesieniu. Aby uzyskać więcej informacji, zobacz [Zarządzanie rolami rozliczeń w witrynie Azure Portal](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="revert-the-transition"></a>Cofnięcie przeniesienia

Nie można cofnąć przeniesienia. Po przeniesieniu rozliczania Twoich subskrypcji platformy Azure na nowe konto rozliczeniowe nie można przywrócić rejestracji umowy Enterprise Agreement.

### <a name="closing-your-browser-during-setup"></a>Zamykanie przeglądarki podczas konfiguracji

Przed kliknięciem przycisku **Rozpocznij przenoszenie** można zamknąć przeglądarkę. Możesz wrócić do konfiguracji przy użyciu linku w wiadomości e-mail i rozpocząć przenoszenie. Jeśli zamkniesz przeglądarkę po rozpoczęciu przenoszenia będzie ono nadal wykonywane. Wróć na stronę stanu przenoszenia, aby sprawdzić najnowszy stan przenoszenia. Po zakończeniu przenoszenia otrzymasz wiadomość e-mail.

## <a name="complete-the-setup-in-the-azure-portal"></a>Wykonanie konfiguracji w witrynie Azure Portal

Aby wykonać konfigurację, musisz mieć dostęp do nowego konta rozliczeniowego i rejestracji umowy Enterprise Agreement. Aby uzyskać więcej informacji, zobacz [Dostęp wymagany do wykonania konfiguracji konta rozliczeniowego](#access-required-to-complete-the-setup).

1. Zaloguj się do witryny Azure Portal przy użyciu linku w wiadomości e-mail wysłanej do Ciebie po podpisaniu umowy klienta firmy Microsoft.

2. Jeśli ktoś inny z organizacji podpisał umowę lub nie masz wiadomości e-mail, zaloguj się przy użyciu poniższego linku. Zastąp ciąg **enrollmentNumber** numerem rejestracyjnym odnowionej umowy Enterprise Agreement.

   `https://portal.azure.com/#blade/Microsoft_Azure_Billing/EATransitionToMCA/enrollmentId/enrollmentNumber`

3. Wybierz pozycję **Rozpocznij przenoszenie** w ostatnim kroku konfiguracji. Po wybraniu polecenia Rozpocznij przenoszenie:

    ![Zrzut ekranu pokazujący kreatora konfiguracji](./media/billing-mca-setup-account/ea-mca-set-up-wizard.png)

    - Hierarchia rozliczeń odpowiadająca hierarchii umowy Enterprise Agreement zostanie utworzona na nowym koncie rozliczeniowym. Aby uzyskać więcej informacji, zobacz [Omówienie zmian w hierarchii rozliczeń](#understand-changes-to-your-billing-hierarchy).
    - Administratorzy rejestracji umowy Enterprise Agreement uzyskają dostęp do nowego konta rozliczeniowego, aby nadal zarządzać rozliczeniami dla organizacji.
    - Rozliczenia subskrypcji platformy Azure są przenoszone do nowego konta. **Wykonywanie tego przenoszenia nie ma żadnego wpływu na usługi platformy Azure. Będą one nadal działać bez żadnych przerw**.
    - Jeśli masz rezerwacje platformy Azure, są one przenoszone do nowego konta rozliczeniowego z zachowaniem rabatu i terminu. Rabat za rezerwację będzie nadal stosowany podczas przenoszenia.

4. Stan przenoszenia można monitorować na stronie **Stan przenoszenia**.

   ![Zrzut ekranu pokazujący stan przenoszenia](./media/billing-mca-setup-account/ea-mca-set-up-status.png)

## <a name="validate-billing-account-set-up"></a>Walidacja konfiguracji konta rozliczeniowego

 Zweryfikuj następujące elementy, aby sprawdzić poprawność konfiguracji nowego konta rozliczeniowego:

### <a name="azure-subscriptions"></a>Subskrypcje platformy Azure

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

   ![Zrzut ekranu przedstawiający wyszukiwanie w witrynie Azure Portal](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. Wybierz profil rozliczeniowy. W zależności od praw dostępu może być konieczne wybranie konta rozliczeniowego. Z poziomu konta rozliczeniowego wybierz pozycję **Profile rozliczeniowe**, a następnie profil rozliczeniowy.

4. Wybierz **subskrypcje platformy Azure** z lewej strony.

   ![Zrzut ekranu przedstawiający listę subskrypcji](./media/billing-mca-setup-account/billing-mca-subscriptions-post-transition.png)

Subskrypcje platformy Azure przenoszone z rejestracji umowy Enterprise Agreement do nowego konta rozliczeniowego są wyświetlane na stronie subskrypcji platformy Azure. Jeśli uważasz, że brakuje którejkolwiek subskrypcji, przenieś rozliczenie subskrypcji ręcznie w witrynie Azure Portal. Aby uzyskać więcej informacji, zobacz [Pobieranie własności rozliczeń subskrypcji platformy Azure od innych użytkowników](billing-mca-request-billing-ownership.md).

### <a name="azure-reservations"></a>Rezerwacje platformy Azure

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

   ![Zrzut ekranu przedstawiający wyszukiwanie w witrynie Azure Portal](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. Wybierz sekcję faktury. W zależności od praw dostępu może być konieczne wybranie konta rozliczeniowego lub profilu rozliczeniowego.  Z poziomu profilu lub konta rozliczeniowego wybierz pozycję **Sekcje faktur**, a następnie wybierz sekcję faktury.

    ![Zrzut ekranu przedstawiający listę sekcji faktur po przeniesieniu](./media/billing-mca-setup-account/billing-mca-invoice-sections-post-transition.png)

4. Zaznacz pozycję **Wszystkie produkty** z lewej strony.

5. Wyszukaj pozycję **Zarezerwowane**.

    ![Zrzut ekranu przedstawiający listę subskrypcji po przeniesieniu](./media/billing-mca-setup-account/billing-mca-azure-reservations-post-transition.png)

Rezerwacje platformy Azure przenoszone z rejestracji umowy Enterprise Agreement do nowego konta rozliczeniowego są wyświetlane na stronie Wszystkie produkty. Powtórz kroki dla wszystkich sekcji faktur, aby zweryfikować, że wszystkie rezerwacje platformy Azure zostały przeniesione z rejestracji umowy Enterprise Agreement. Jeśli uważasz, że brakuje którejkolwiek rezerwacji platformy Azure, [skontaktuj się z pomocą techniczną platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), aby przenieść rezerwację do nowego konta rozliczeniowego.

### <a name="access-of-enterprise-administrators-on-the-billing-profile"></a>Dostęp administratorów przedsiębiorstwa dla profilu rozliczeniowego

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

   ![Zrzut ekranu przedstawiający wyszukiwanie w witrynie Azure Portal](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. Wybierz profil rozliczeniowy utworzony na potrzeby rejestracji. W zależności od praw dostępu może być konieczne wybranie konta rozliczeniowego.  Z poziomu konta rozliczeniowego wybierz pozycję **Profile rozliczeniowe**, a następnie profil rozliczeniowy.

4. Po lewej stronie wybierz pozycję **Kontrola dostępu (IAM)** .

   ![Zrzut ekranu pokazujący dostęp administratorów przedsiębiorstwa po przeniesieniu](./media/billing-mca-setup-account/billing-mca-ea-admins-access-post-transition.png)

Administratorzy przedsiębiorstwa są wyświetlani jako właściciele profilu rozliczeniowego, a administratorzy przedsiębiorstwa z uprawnieniami tylko do odczytu są wyświetlani jako czytelnicy profilu rozliczeniowego. Jeśli uważasz, że administratorzy przedsiębiorstwa nie mają odpowiedniego dostępu, możesz udzielić im dostępu w witrynie Azure Portal. Aby uzyskać więcej informacji, zobacz [Zarządzanie rolami rozliczeń w witrynie Azure Portal](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="access-of-enterprise-administrators-department-administrators-and-account-owners-on-invoice-sections"></a>Dostęp administratorów przedsiębiorstwa, administratorów działu i właścicieli kont dla sekcji faktur

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

   ![Zrzut ekranu przedstawiający wyszukiwanie w witrynie Azure Portal](./media/billing-mca-setup-account/billing-search-cost-management-billing.png).

3. Wybierz sekcję faktury. Sekcje faktur mają takie same nazwy jak odpowiadające im działy w ramach rejestracji umowy Enterprise Agreement. W zależności od praw dostępu może być konieczne wybranie konta rozliczeniowego lub profilu rozliczeniowego. Z poziomu profilu lub konta rozliczeniowego wybierz pozycję **Sekcje faktur**, a następnie wybierz sekcję faktury.

   ![Zrzut ekranu przedstawiający listę sekcji faktur po przeniesieniu](./media/billing-mca-setup-account/billing-mca-invoice-sections-post-transition.png)

4. Po lewej stronie wybierz pozycję **Kontrola dostępu (IAM)** .

    ![Zrzut ekranu pokazujący dostęp administratorów działu i konta po przeniesieniu](./media/billing-mca-setup-account/billing-mca-department-account-admins-access-post-transition.png)

Administratorzy przedsiębiorstwa i administratorzy działu są wyświetlani jako właściciele lub czytelnicy sekcji faktur, a właściciele konta w dziale są wyświetlani jako twórcy subskrypcji platformy Azure. Powtórz kroki dla wszystkich sekcji faktur, aby sprawdzić dostęp dla wszystkich działów w ramach rejestracji umowy Enterprise Agreement. Właściciele konta, którzy nie należeli do żadnego działu, otrzymają uprawnienie do sekcji faktur o nazwie **Domyślna sekcja faktur**. Jeśli uważasz, że administratorzy nie mają odpowiedniego dostępu, możesz udzielić im dostępu w witrynie Azure Portal. Aby uzyskać więcej informacji, zobacz [Zarządzanie rolami rozliczeń w witrynie Azure Portal](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), aby szybko rozwiązać problem.

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do nowego konta rozliczeniowego](billing-mca-overview.md)

- [Wykonywanie zadań umowy Enterprise Agreement na koncie rozliczeniowym dla umowy klienta firmy Microsoft](billing-mca-enterprise-operations.md)

- [Zarządzanie dostępem do konta rozliczeniowego](billing-understand-mca-roles.md)

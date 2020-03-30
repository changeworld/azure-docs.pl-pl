---
title: Zarządzanie potencjalnymi klientami dla Dynamics 365 dla customer engagement | Azure Marketplace
description: Skonfiguruj zarządzanie potencjalnymi klientami dla Dynamics 365 dla customer engagement.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: dsindona
ms.openlocfilehash: 8af6b3a451d20bcc9cab3fa4adb9643f82b85e49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288822"
---
# <a name="configure-lead-management-for-dynamics-365-for-customer-engagement"></a>Konfigurowanie zarządzania potencjalnymi klientami dla dynamics 365 dla customer engagement

W tym artykule opisano sposób konfigurowania dynamics 365 for Customer Engagement (wcześniej Dynamics CRM Online), przeczytaj więcej o zmianach [tutaj,](https://docs.microsoft.com/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online) aby przetwarzać potencjalnych klientów z oferty marketplace. 

>[!Note]
>Te instrukcje są specyficzne dla środowiska Dynamics 365 for Customer Engagement w chmurze firmy Microsoft. Łączenie bezpośrednio ze środowiskiem dynamicznym na przedm nie jest obecnie obsługiwane, istnieją inne opcje, aby odbierać potencjalnych klientów, takie jak konfigurowanie [punktu końcowego https](./commercial-marketplace-lead-management-instructions-https.md) lub [tabeli platformy Azure](./commercial-marketplace-lead-management-instructions-azure-table.md) do odbierania potencjalnych klientów.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania czynności określonych w tym artykule potrzebne są następujące uprawnienia użytkownika:

* Musisz być administratorem w wystąpieniu Dynamics 365 dla customer engagement, aby móc zainstalować rozwiązanie i postępować zgodnie z tymi instrukcjami.
* Musisz być administratorem dzierżawy, aby utworzyć nowe konto usługi dla usługi potencjalnego klienta używanej do wysyłania potencjalnych klientów z ofert marketplace.
* Musisz mieć dostęp do portalu administracyjnego usługi Office 365.
* Musisz mieć dostęp do witryny Azure portal.

## <a name="install-the-solution"></a>Zainstaluj rozwiązanie

1.  Pobierz [rozwiązanie Microsoft Marketplace Lead Writer](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) i zapisz je lokalnie na komputerze.

2.  Otwórz Dynamics 365 for Customer Engagement, przechodząc do adresu URL `https://tenant.crm.dynamics.com`wystąpienia Dynamics (np. ).

3.  Przejdź do ustawień dostępu, wybierając ikonę koła zębatego i **ustawienia zaawansowane** na górnym pasku nawigacyjnym.
 
    ![Dynamics - Ustawienia zaawansowane](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

4.  Po przejściu na stronę Ustawienia przejdź do menu Ustawienia z górnego paska nawigacyjnego i wybierz pozycję **Rozwiązania**.

    >[!Note]
    >Jeśli nie widzisz opcji w następnym przechwytywaniu ekranu, nie masz uprawnień, które musisz kontynuować. Skontaktuj się z administratorem w wystąpieniu Dynamics 365 dla customer engagement.

    ![Dynamics 365 - Rozwiązania](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

5. Po przejściu na stronę Rozwiązania wybierz **pozycję Importuj** i przejdź do miejsca, w którym zapisano rozwiązanie *programu Microsoft Marketplace Lead Writer* pobrane w kroku 1.

    ![Dynamics 365 dla zaangażowania klienta — import](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

6. Pełne importowanie rozwiązania, wykonując kreatora importu rozwiązania.

## <a name="configure-user-permissions"></a>Konfigurowanie uprawnień użytkowników

Aby zapisać potencjalnych klientów w wystąpieniu Dynamics 365 for Customer Engagement, musisz udostępnić nam konto usługi i skonfigurować uprawnienia do konta.

Aby utworzyć konto usługi i przypisać uprawnienia, należy wykonać następujące czynności. Możesz użyć **usługi Azure Active Directory** lub usługi Office **365**.

>[!Note]
>Na podstawie wybranej opcji uwierzytelniania można przejść do odpowiednich instrukcji na podstawie wybranego wyboru. Zobacz [Usługa Azure Active Directory](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#azure-active-directory) lub Office [365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#office-365).

### <a name="azure-active-directory"></a>Usługa Azure Active Directory

Zalecamy tę opcję, ponieważ otrzymujesz dodatkową korzyść, ponieważ nigdy nie musisz aktualizować nazwy użytkownika/hasła, aby nadal otrzymywać potencjalnych klientów. Aby użyć opcji Usługi Azure Active Directory, należy podać identyfikator aplikacji, klucz aplikacji i identyfikator katalogu z aplikacji usługi Active Directory.

Aby skonfigurować usługę Azure Active Directory for Dynamics 365 for Customer Engagement, należy wykonać następujące kroki.

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com/), a następnie wybierz usługę Azure Active Directory z lewej strony nawigacji.

2. Wybierz **właściwości** z lewej nawigacji usługi Azure Active Directory i skopiuj wartość **identyfikatora katalogu** na tej stronie. Zapisz tę wartość, ponieważ jest to wartość *identyfikatora katalogu,* którą należy podać w portalu publikowania, aby otrzymywać potencjalnych klientów dla oferty marketplace.

    ![Usługa Azure Active Directory — właściwości](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

3. Wybierz **pozycję Rejestracje aplikacji** z lewej strony usługi Azure Active Directory, a następnie wybierz pozycję Nowa **rejestracja** na tej stronie.
4. Wprowadź nazwę nazwy aplikacji. Podaj opisową nazwę aplikacji.
5. W obszarze Obsługiwane typy kont wybierz pozycję **Konta w dowolnym katalogu organizacji**.
6. W obszarze Przekierowanie **Web** identyfikatora URI wybierz `https://contosoapp1/auth`pozycję Web i podaj identyfikator URI (na przykład ). 
7. Wybierz pozycję **Zarejestruj**.

    ![Rejestrowanie aplikacji](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

8. Teraz, gdy aplikacja jest zarejestrowana, przejdź do strony przeglądu aplikacji i skopiuj wartość **identyfikatora aplikacji (klienta)** na tej stronie. Zapisz tę wartość, ponieważ jest to wartość *identyfikatora aplikacji (klienta),* którą należy podać w portalu publikowania i w dynamics, aby otrzymywać potencjalnych klientów dla oferty portalu marketplace.

    ![Identyfikator aplikacji (klienta)](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

9. Wybierz **certyfikaty i wpisy tajne** z lewej nawigacji aplikacji i wybierz **pozycję Nowy klucz tajny klienta** na tej stronie. Wprowadź opis klucza tajnego klienta i wybierz opcję **Nigdy w** obszarze Wygasa. Wybierz **pozycję Dodaj,** aby utworzyć klucz tajny klienta.

    ![Zastosowanie - Certyfikacja i tajemnice](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

10. Jak tylko klucz tajny klienta zostanie pomyślnie utworzony, **skopiuj wartość tajnego klienta**. Nie będzie można pobrać wartości po przejściu od strony. Zapisz tę wartość, ponieważ jest to wartość *tajnego klienta,* którą musisz podać w portalu publikowania, aby otrzymywać potencjalnych klientów dla oferty marketplace. 
11. Wybierz **uprawnienia interfejsu API** z lewej nawigacji aplikacji, a następnie wybierz pozycję Dodaj **uprawnienie**.
12. Wybierz pozycję Interfejsy API firmy Microsoft, a następnie jako interfejs API wybierz pozycję **Dynamics CRM.**
13. W obszarze *Jaki typ uprawnień wymaga aplikacji*, upewnij się, że **wybrano uprawnienia delegowane.** Sprawdź uprawnienie **user_impersonation** *Access Common Data Service jako użytkownicy organizacji*. Wybierz pozycję **Dodaj uprawnienia**.

    ![Dodawanie uprawnień](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

14. Po wykonaniu kroków 1-13 w witrynie Azure portal przejdź do wystąpienia Dynamics 365 `https://tenant.crm.dynamics.com`for Customer Engagement, przechodząc do adresu URL (np. ).
15. Przejdź do ustawień dostępu, wybierając ikonę koła zębatego i **ustawienia zaawansowane** na górnym pasku nawigacyjnym.
16. Po przejściu na stronę Ustawienia przejdź do menu Ustawienia z górnego paska nawigacyjnego i wybierz pozycję **Zabezpieczenia**.
17. Po przejściu na stronę Zabezpieczenia wybierz pozycję **Użytkownicy**.  Na stronie Użytkownicy wybierz pozycję rozwijanej "Włączeni użytkownicy", aby przełączyć się na **Użytkowników aplikacji**.
18. Wybierz **pozycję Nowy,** aby utworzyć nowego użytkownika. 

    ![Tworzenie konta nowego użytkownika](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

19. W **uinowa użytkowniczka**upewnij się, że wybrano użytkownika użytkownika użytkownika aplikacji. Podaj nazwę użytkownika, imię i nazwisko oraz adres e-mail użytkownika, którego chcesz użyć za pomocą tego połączenia. Ponadto wklej identyfikator **aplikacji** dla aplikacji utworzonej w witrynie Azure portal z kroku 8. Wybierz **pozycję Zapisz i zamknij,** aby zakończyć dodawanie użytkownika.

    ![Nowy użytkownik](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

20. Przejdź do sekcji "Ustawienia zabezpieczeń" w tym artykule, aby zakończyć konfigurowanie połączenia dla tego użytkownika.

### <a name="office-365"></a>Office 365

Jeśli nie chcesz korzystać z usługi Azure Active Directory, możesz zarejestrować nowego użytkownika w *centrum administracyjnym usługi Microsoft 365*. Aby kontynuować pozyskiwanie potencjalnych klientów, musisz aktualizować nazwę użytkownika/hasło co 90 dni.

Konfigurowanie usługi Office 365 dla dynamics 365 dla customer engagement umożliwia skonfigurowanie następujących kroków.

1. Zaloguj się do [centrum administracyjnego usługi Microsoft 365](https://admin.microsoft.com).

2. Wybierz **pozycję Dodaj użytkownika**.

    ![Centrum administracyjne usługi Microsoft 365 — dodawanie użytkownika](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

4. Utwórz nowego użytkownika dla usługi modułu zapisującego potencjalnych klienty. Skonfiguruj następujące ustawienia:

    * Podaj nazwę użytkownika
    * Podaj hasło i usuń zaznaczenie opcji "Make this user change their password when they first sign in".
    * Wybierz "Użytkownik (brak dostępu administratora)" jako rolę dla użytkownika.
    * Wybierz opcję "Plan zaangażowania klientów Dynamics 365" jako licencję produktu pokazaną w następnym przechwycie ekranu. Zostanie naliczona opłata za wybraną licencję. 

Zapisz te wartości, ponieważ są to wartości *nazwy użytkownika i hasła,* które należy podać w portalu publikowania, aby otrzymywać potencjalnych klientów dla oferty marketplace.

![Centrum administracyjne usługi Microsoft 365 — nowy użytkownik](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>Ustawienia zabezpieczeń

Ostatnim krokiem jest umożliwienie użytkownikowi utworzonego do pisania potencjalnych klientów.

1. Otwórz Dynamics 365 for Customer Engagement, przechodząc do adresu URL `https://tenant.crm.dynamics.com`wystąpienia Dynamics (np. ).
2. Przejdź do ustawień dostępu, wybierając ikonę koła zębatego i **ustawienia zaawansowane** na górnym pasku nawigacyjnym.
3. Po przejściu na stronę Ustawienia przejdź do menu Ustawienia z górnego paska nawigacyjnego i wybierz pozycję **Zabezpieczenia**.
4. Po przejściu na stronę Zabezpieczenia wybierz pozycję **Użytkownicy** i wybierz użytkownika utworzonego w sekcji Konfigurowanie uprawnień użytkownika tego dokumentu, a następnie wybierz pozycję **Zarządzaj rolami**. 

    ![Zarządzanie rolami](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

5. Wyszukaj nazwę roli "Główny moduł zapisujący witrynę Microsoft Marketplace" i wybierz ją, aby przypisać użytkownikowi rolę.

    ![Zarządzanie rolami użytkowników](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!Note]
    >Ta rola jest tworzona przez zaimportowane rozwiązanie i ma tylko uprawnienia do zapisywania potencjalnych klientów i śledzenia wersji rozwiązania w celu zapewnienia zgodności.

6. Przejdź wstecz do pozycji Bezpieczeństwo i wybierz pozycję **Role zabezpieczeń**. Wyszukaj rolę "Główny moduł zapisujący w portalu Microsoft Marketplace" i wybierz ją.

    ![Role zabezpieczeń](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

7. Po wejściu w rolę zabezpieczeń wybierz kartę **Rekordy podstawowe.** Wyszukaj encję "Ustawienia interfejsu użytkownika jednostki użytkownika" i włącz uprawnienia Utwórz, Odczytuj i Zapisu dla użytkownika (1/4 żółtego koła) dla tej encji, klikając raz w każdym z odpowiednich okręgów.

    ![Główny moduł zapisujący w portalu Microsoft Marketplace — rekordy podstawowe](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

8. Teraz przejdź do karty **Dostosowywanie.** Wyszukaj jednostkę "Zadanie systemowe" i włącza uprawnienia Odczyt, Zapis i Dołącz do organizacji (zielony stały) dla tej jednostki, klikając cztery razy w każdym z odpowiednich okręgów.

    ![Microsoft Marketplace Lead Writer — dostosowywanie](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

9. **Zapisz i zamknij**.

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-for-customer-engagement"></a>Skonfiguruj ofertę do wysyłania potencjalnych klientów do Dynamics 365 For Customer Engagement

Gdy będziesz gotowy skonfigurować informacje o zarządzaniu potencjalnymi klientami dla oferty w portalu publikowania, wykonaj poniższe czynności:

1. Przejdź do strony **Ustawienia oferty** dla swojej oferty.
2. Wybierz **pozycję Połącz** w sekcji Zarządzanie potencjalnymi klientami.

    ![Łączenie się z zarządzaniem potencjalnymi klientami](./media/commercial-marketplace-lead-management-instructions-dynamics/connect-lead-management.png)

3. W oknie podręcznym Szczegóły połączenia wybierz **dynamics 365 dla zaangażowania klienta** dla miejsca docelowego potencjalnego klienta

    ![Szczegóły połączenia — miejsce docelowe potencjalnego klienta](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

4. Podaj **adres URL wystąpienia Dynamics 365,** taki jak `https://contoso.crm4.dynamics.com`.
5. Wybierz metodę **uwierzytelniania,** usługi Azure Active Directory lub usługi Office 365. 
6. Jeśli wybrano usługę Azure Active Directory, podaj **identyfikator aplikacji (klienta)** `23456052-aaaa-bbbb-8662-1234df56788f`(przykład: ), `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`identyfikator **katalogu** (przykład: `12345678-8af1-4asf-1234-12234d01db47`) i klucz tajny **klienta** (przykład: ).

    ![Szczegóły połączenia — usługa Azure Active Directory](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

7. Jeśli wybrano pozycję Office 365, `contoso@contoso.onmicrosoft.com`podaj nazwę `P@ssw0rd` **użytkownika** (przykład: ) i Hasło (przykład: ).

    ![Szczegóły połączenia — nazwa użytkownika](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

>[!Note]
>Musisz zakończyć konfigurowanie pozostałej części oferty i opublikować ją, zanim będzie można odbierać potencjalnych klientów dla oferty.

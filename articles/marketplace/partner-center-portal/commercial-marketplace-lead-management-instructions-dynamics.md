---
title: Zarządzanie potencjalnymi klientami dla systemu Dynamics 365 dla zaangażowania klienta | Portal Azure Marketplace
description: Skonfiguruj zarządzanie potencjalnymi klientami dla systemu Dynamics 365 na potrzeby zaangażowania klientów.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: evansma
ms.openlocfilehash: 37cf613b6e0bd2ec9910dd3e7431c0feaa02431c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812311"
---
# <a name="configure-lead-management-for-dynamics-365-for-customer-engagement"></a>Konfigurowanie zarządzania liderem dla usługi Dynamics 365 na potrzeby zaangażowania klienta

W tym artykule opisano sposób konfigurowania usługi Dynamics 365 dla zaangażowania klienta (wcześniej usługi Dynamics CRM Online). Przeczytaj więcej na temat zmiany w [tym miejscu](https://docs.microsoft.com/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online) , aby przetwarzać potencjalnych klientów z oferty w portalu Marketplace. 

>[!Note]
>Te instrukcje są specyficzne dla środowiska obsługi klienta hostowanego przez firmę Microsoft w chmurze 365. Bezpośrednie nawiązywanie połączenia ze środowiskiem Dynamics on-Premium nie jest obecnie obsługiwane, istnieją inne opcje umożliwiające otrzymywanie potencjalnych klientów, takich jak konfigurowanie [punktu końcowego HTTPS](./commercial-marketplace-lead-management-instructions-https.md) lub [tabeli platformy Azure](./commercial-marketplace-lead-management-instructions-azure-table.md) w celu otrzymywania potencjalnych klientów.

## <a name="prerequisites"></a>Wymagania wstępne

Następujące uprawnienia użytkownika są wymagane do wykonania kroków opisanych w tym artykule:

* Aby móc zainstalować rozwiązanie i postępować zgodnie z tymi instrukcjami, musisz mieć uprawnienia administratora w usłudze Dynamics 365 dla wystąpienia zaangażowania klienta.
* Musisz być administratorem dzierżawy, aby utworzyć nowe konto usługi dla usługi lidera użytej do wysłania potencjalnych klientów z ofert portalu Marketplace.
* Musisz mieć dostęp do portalu administracyjnego pakietu Office 365.
* Musisz mieć dostęp do Azure Portal.

## <a name="install-the-solution"></a>Instalowanie rozwiązania

1.  Pobierz [rozwiązanie Microsoft Marketplaceego modułu zapisywania](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) i Zapisz je lokalnie na komputerze.

2.  Otwórz program Dynamics 365 dla zaangażowania klienta, przechodząc do adresu URL dla wystąpienia programu Dynamics (takiego jak `https://tenant.crm.dynamics.com`).

3.  Ustawienia dostępu, wybierając ikonę koła zębatego i **Ustawienia zaawansowane** na górnym pasku nawigacyjnym.
 
    ![Dynamics — ustawienia zaawansowane](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

4.  Na stronie Ustawienia dostęp do menu Ustawienia na górnym pasku nawigacyjnym i wybierz pozycję **rozwiązania**.

    >[!Note]
    >Jeśli nie widzisz opcji w następnym przechwyceniu ekranu, nie masz uprawnień, które należy wykonać. Skontaktuj się z administratorem usługi Dynamics 365 dla wystąpienia zaangażowania klienta.

    ![Dynamics 365 — rozwiązania](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

5. Na stronie rozwiązania wybierz pozycję **Importuj** i przejdź do lokalizacji, w której zapisano Microsoft Marketplace rozwiązanie do *zapisywania potencjalnych klientów* pobrane w kroku 1.

    ![Dynamics 365 dla zaangażowania klienta — import](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

6. Zaimportuj rozwiązanie przy użyciu Kreatora importu rozwiązania.

## <a name="configure-user-permissions"></a>Konfigurowanie uprawnień użytkownika

Aby napisać klientów do usługi Dynamics 365 dla wystąpienia zaangażowania klienta, należy udostępnić konto usługi z nami i skonfigurować uprawnienia dla konta.

Wykonaj następujące kroki, aby utworzyć konto usługi i przypisać uprawnienia. Możesz użyć **Azure Active Directory** lub **pakietu Office 365**.

>[!Note]
>Na podstawie wybranej opcji uwierzytelniania można przejść do odpowiednich instrukcji w zależności od wyboru. Zobacz [Azure Active Directory](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#azure-active-directory) lub [Office 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#office-365).

### <a name="azure-active-directory"></a>Usługa Azure Active Directory

Zalecamy korzystanie z tej opcji, ponieważ w celu uzyskania potencjalnych klientów nie trzeba aktualizować nazwy użytkownika ani hasła. Aby użyć opcji Azure Active Directory, należy podać identyfikator aplikacji, klucz aplikacji i identyfikator katalogu w aplikacji Active Directory.

Wykonaj następujące kroki, aby skonfigurować Azure Active Directory dla programu Dynamics 365 na potrzeby zaangażowania klienta.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/), a następnie wybierz usługę Azure Active Directory w lewym okienku nawigacji.

2. Wybierz **Właściwości** z Azure Active Directory lewym przyciskiem myszy i skopiuj wartość **Identyfikator katalogu** na tej stronie. Zapisz tę wartość, ponieważ jest to wartość *identyfikatora katalogu* , którą należy podać w portalu wydawców, aby otrzymywać potencjalnych klientów do oferty w witrynie Marketplace.

    ![Azure Active Directory — właściwości](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

3. Wybierz pozycję **rejestracje aplikacji** w lewym Azure Active Directory nawigacyjnym, a następnie wybierz pozycję **Nowa rejestracja** na tej stronie.
4. Wprowadź nazwę dla nazwy aplikacji. Podaj zrozumiałą nazwę aplikacji.
5. W obszarze obsługiwane typy kont wybierz pozycję **konta w dowolnym katalogu organizacyjnym**.
6. W obszarze Identyfikator URI przekierowania wybierz pozycję **Sieć Web** i podaj identyfikator URI (na przykład `https://contosoapp1/auth`). 
7. Wybierz pozycję **Zarejestruj**.

    ![Rejestrowanie aplikacji](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

8. Teraz, gdy aplikacja jest zarejestrowana, uzyskaj dostęp do strony przegląd aplikacji i skopiuj wartość **identyfikatora aplikacji (klienta)** na tej stronie. Zapisz tę wartość, ponieważ jest to wartość *identyfikatora aplikacji (klienta)* , którą należy podać w portalu wydawców i w usłudze Dynamics, aby otrzymywać potencjalni klienci na potrzeby oferty z witryny Marketplace.

    ![Identyfikator aplikacji (klienta)](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

9. Wybierz pozycję **Certyfikaty i wpisy tajne** w lewym obszarze nawigacji aplikacji, a następnie wybierz pozycję **Nowy wpis tajny klienta** na tej stronie. Wprowadź Opisowy wpis tajny klienta i wybierz opcję **nigdy nie** w obszarze wygaśnięcie. Wybierz pozycję **Dodaj** , aby utworzyć klucz tajny klienta.

    ![Certyfikacja i wpisy tajne aplikacji](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

10. Po pomyślnym utworzeniu wpisu tajnego klienta **skopiuj wartość klucz tajny klienta**. Nie będzie można pobrać wartości po opuszczeniu strony. Zapisz tę wartość, ponieważ jest to wartość *klucza tajnego klienta* , którą musisz podać w portalu wydawców, aby otrzymywać potencjalnych klientów na potrzeby oferty z witryny Marketplace. 
11. Wybierz pozycję **uprawnienia interfejsu API** z lewej strony aplikacji, a następnie wybierz pozycję **Dodaj uprawnienie**.
12. Wybierz pozycję Microsoft API, a następnie wybierz pozycję **Dynamics CRM** jako interfejs API.
13. W obszarze *jakiego typu uprawnienia aplikacja jest wymagana*, upewnij się, że wybrano **uprawnienia delegowane** . Sprawdź uprawnienia **user_impersonation** *dostępu Common Data Service jako użytkownicy organizacji*. Wybierz pozycję **Dodaj uprawnienia**.

    ![Dodawanie uprawnień](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

14. Po wykonaniu kroków 1-13 w Azure Portal przejdź do wystąpienia usługi Dynamics 365 for Customer Engagement, przechodząc do adresu URL (na przykład `https://tenant.crm.dynamics.com`).
15. Ustawienia dostępu, wybierając ikonę koła zębatego i **Ustawienia zaawansowane** na górnym pasku nawigacyjnym.
16. Na stronie Ustawienia dostęp do menu Ustawienia na górnym pasku nawigacyjnym i wybierz pozycję **zabezpieczenia**.
17. Na stronie Zabezpieczenia wybierz pozycję **Użytkownicy**.  Na stronie użytkownicy wybierz listę rozwijaną "włączone użytkownicy", aby przełączyć się do opcji **Użytkownicy aplikacji**.
18. Wybierz pozycję **Nowy** , aby utworzyć nowego użytkownika. 

    ![Tworzenie konta nowego użytkownika](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

19. W obszarze **nowy użytkownik**upewnij się, że użytkownik: aplikacja jest zaznaczona. Wprowadź nazwę użytkownika, pełną nazwę i adres e-mail użytkownika, który ma być używany z tym połączeniem. Ponadto wklej **Identyfikator aplikacji** dla aplikacji utworzonej w Azure Portal z kroku 8. Wybierz pozycję **Zapisz i Zamknij** , aby zakończyć Dodawanie użytkownika.

    ![Nowy użytkownik](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

20. Przejdź do pozycji "Ustawienia zabezpieczeń" w tym artykule, aby zakończyć konfigurowanie połączenia dla tego użytkownika.

### <a name="office-365"></a>Office 365

Jeśli nie chcesz używać Azure Active Directory, możesz zarejestrować nowego użytkownika w *centrum administracyjnym Microsoft 365*. Aby nadal otrzymywać potencjalni klienci, musisz zaktualizować nazwę użytkownika/hasło co 90 dni.

Wykonaj następujące kroki, aby skonfigurować pakiet Office 365 dla usługi Dynamics 365 na potrzeby zaangażowania klienta.

1. Zaloguj się do [Centrum administracyjnego Microsoft 365](https://admin.microsoft.com).

2. Wybierz pozycję **Dodaj użytkownika**.

    ![Microsoft 365 centrum administracyjnego — Dodaj użytkownika](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

4. Utwórz nowego użytkownika dla usługi składnika zapisywania potencjalnych klientów. Skonfiguruj następujące ustawienia:

    * Podaj nazwę użytkownika
    * Podaj hasło i usuń zaznaczenie opcji "Uczyń ten użytkownik zmianami hasła podczas pierwszego logowania".
    * Wybierz pozycję "użytkownik (brak dostępu administratora)" jako rolę dla użytkownika.
    * Wybierz pozycję "Plan zaangażowania klientów w Dynamics 365" jako licencję produktu pokazaną w następnym przechwyceniu ekranu. Opłata zostanie naliczona za licencję, którą wybierzesz. 

Zapisz te wartości, ponieważ są to wartości *Nazwa użytkownika i hasło* , które należy podać w portalu wydawców, aby otrzymywać potencjalnych klientów na potrzeby oferty portalu Marketplace.

![Microsoft 365 centrum administracyjnego — nowy użytkownik](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>Ustawienia zabezpieczeń

Ostatnim krokiem jest umożliwienie użytkownikowi utworzonego w celu zapisania potencjalnych klientów.

1. Otwórz program Dynamics 365 dla zaangażowania klienta, przechodząc do adresu URL dla wystąpienia programu Dynamics (takiego jak `https://tenant.crm.dynamics.com`).
2. Ustawienia dostępu, wybierając ikonę koła zębatego i **Ustawienia zaawansowane** na górnym pasku nawigacyjnym.
3. Na stronie Ustawienia dostęp do menu Ustawienia na górnym pasku nawigacyjnym i wybierz pozycję **zabezpieczenia**.
4. Na stronie Zabezpieczenia wybierz pozycję **Użytkownicy** , a następnie wybierz użytkownika, który został utworzony w sekcji Konfigurowanie uprawnień użytkownika tego dokumentu, a następnie wybierz pozycję **Zarządzaj rolami**. 

    ![Zarządzanie rolami](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

5. Wyszukaj nazwę roli "Microsoft Marketplace autorze klienta" i wybierz ją, aby przypisać użytkownika do roli.

    ![Zarządzanie rolami użytkowników](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!Note]
    >Ta rola jest tworzona przez zaimportowane rozwiązanie i ma uprawnienia do pisania potencjalnych klientów i śledzenia wersji rozwiązania w celu zapewnienia zgodności.

6. Przejdź z powrotem do strony zabezpieczenia i wybierz pozycję **role zabezpieczeń**. Wyszukaj rolę "Microsoft Marketplace autorze klienta" i wybierz ją.

    ![Role zabezpieczeń](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

7. W roli zabezpieczeń wybierz kartę **rekordy podstawowe** . Wyszukaj wartość "ustawienia interfejsu użytkownika jednostki użytkownika" i Włącz 1/4 uprawnienia Tworzenie, Odczyt i zapis dla tej jednostki, klikając jeden raz w każdym z odpowiednich okręgów.

    ![Moduł zapisujący potencjalnych klientów Microsoft Marketplace — rekordy podstawowe](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

8. Teraz przejdź do karty **Dostosowywanie** . wartość Wyszukaj umożliwia rozłączenie jednostki "zadanie systemowe" oraz umożliwienie uprawnień Odczyt, zapis i AppendTo organizacji (Solid Green) dla tej jednostki, klikając cztery razy w każdym z odpowiednich okręgów.

    ![Składnik zapisywania Microsoft Marketplace potencjalnego klienta — Dostosowywanie](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

9. **Zapisz i Zamknij**.

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-for-customer-engagement"></a>Skonfiguruj ofertę, aby wysyłać do usługi Dynamics 365 potencjalne zaangażowanie klienta

Gdy wszystko jest gotowe do skonfigurowania informacji dotyczących zarządzania potencjalnym liderem oferty w portalu wydawców, wykonaj następujące czynności:

1. Przejdź do strony **Konfiguracja oferty** dla swojej oferty.
2. Wybierz pozycję **Połącz** w sekcji Zarządzanie potencjalnymi klientami.

    ![Łączenie z zarządzaniem potencjalnymi klientami](./media/commercial-marketplace-lead-management-instructions-dynamics/connect-lead-management.png)

3. W oknie podręcznym szczegóły połączenia wybierz pozycję **Dynamics 365 dla zaangażowania klienta** w miejsce docelowe potencjalnego klienta

    ![Szczegóły połączenia — miejsce docelowe lidera](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

4. Podaj **adres URL wystąpienia Dynamics 365** , taki jak `https://contoso.crm4.dynamics.com`.
5. Wybierz metodę **uwierzytelniania**, Azure Active Directory lub Office 365. 
6. W przypadku wybrania Azure Active Directory Podaj **Identyfikator aplikacji (** na przykład: `23456052-aaaa-bbbb-8662-1234df56788f`), **Identyfikator katalogu** (przykład: `12345678-8af1-4asf-1234-12234d01db47`) i **klucz tajny klienta** (przykład: `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`).

    ![Szczegóły połączenia — Azure Active Directory](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

7. W przypadku wybrania pakietu Office 365 Podaj **nazwę użytkownika** (przykład: `contoso@contoso.onmicrosoft.com`) i hasło (przykład: `P@ssw0rd`).

    ![Szczegóły połączenia — nazwa użytkownika](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

>[!Note]
>Musisz zakończyć konfigurowanie reszty oferty i opublikować ją przed odebraniem potencjalnych klientów do oferty.

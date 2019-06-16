---
title: Dynamics CRM | Portal Azure Marketplace
description: Skonfiguruj zarządzanie potencjalnymi klientami dla usługi Dynamics CRM.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: 6fdab26bb5a4da5402a3a0a895a7c8835ef22c2f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935794"
---
# <a name="configure-lead-management-for-dynamics-crm-online"></a>Konfigurowanie — Zarządzanie potencjalnymi klientami dla usługi Dynamics CRM online

W tym artykule opisano sposób konfigurowania usługi Dynamics CRM Online do przetworzenia potencjalnych klientów.

## <a name="prerequisites"></a>Wymagania wstępne

Następujące uprawnienia użytkownika są potrzeby wykonaniu kroków w tym artykule:
- Musisz być administratorem na wystąpienie usługi Dynamics CRM Online, aby zainstalować rozwiązania.
- Musisz być administratorem dzierżawy do utworzenia nowego konta usługi dla potencjalnych klientów usługi.

<a name="install-the-solution"></a>Zainstalować rozwiązanie
--------------------

1.  Pobierz [rozwiązania Microsoft Marketplace prowadzić Writer](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) i zapisać go lokalnie.

2.  Otwórz Dynamics CRM Online i przejdź do ustawień.
    >[!NOTE]
    >Jeśli nie widzisz opcji na następnym zrzucie ekranu, nie masz potrzebnych uprawnień.
 
       ![Wyświetlanie ustawień Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline1.png)

3.  Wybierz **importu**, a następnie wybierz rozwiązanie, który został pobrany w kroku 1.
 
    ![Opcja importu Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline2.png)

4.  Zakończ instalowanie rozwiązania.

## <a name="configure-user-permissions"></a>Skonfiguruj uprawnienia użytkownika

Aby zapisać prowadzi do wystąpienia usługi Dynamics CRM, które należy udostępnić nam konta usługi i konfigurowanie uprawnień dla konta.

Wykonaj następujące kroki, aby utworzyć konto usługi i przypisywanie uprawnień. Możesz użyć **usługi Azure Active Directory** lub **usługi Office 365**.

### <a name="azure-active-directory"></a>Usługa Azure Active Directory

Zaleca się tej opcji, ponieważ pobieranie jednocześnie ma dodatkową zaletę nigdy nie musi zaktualizować nazwy użytkownika/hasła, aby zachować możliwość pobierania potencjalnych klientów. Aby skorzystać z opcji usługi Azure Active Directory podasz identyfikator aplikacji, klucz aplikacji i identyfikator katalogu z poziomu aplikacji usługi Active Directory.

Wykonaj następujące kroki, aby skonfigurować usługę Azure Active Directory dla usługi Dynamics CRM.

1.  Zaloguj się do [witryny Azure portal](https://portal.azure.com/) a następnie wybierz usługę Azure Active Directory.

2.  Wybierz **właściwości** , a następnie skopiuj **identyfikator katalogu**. To jest identyfikator konta Twojej dzierżawy, który należy używać w portalu Cloud Partner.

    ![Pobierz identyfikator katalogu](./media/cloud-partner-portal-lead-management-instructions-dynamics/directoryid.png)

3.  Wybierz **rejestracje aplikacji**, a następnie wybierz pozycję **rejestrowanie nowej aplikacji**.
4.  Wprowadź nazwę aplikacji.
5.  W polu Typ wybierz **aplikacji sieci Web / interfejs API**.
6.  Podaj adres URL. To pole nie jest wymagane dla potencjalnych klientów, ale jest wymagany do tworzenia aplikacji.
7. Wybierz pozycję **Utwórz**.
8.  Teraz, gdy Twoja aplikacja będzie zarejestrowana, wybierz **właściwości** , a następnie wybierz **Skopiuj identyfikator aplikacji**. Informacje o połączeniu użyjemy w portalu Cloud Partner.
9.  W oknie właściwości ustaw ją jako wielodostępnych, a następnie wybierz pozycję **Zapisz**.

10. Wybierz **klucze** i Utwórz nowy klucz o czasie trwania *nigdy nie wygasa*. Wybierz **Zapisz** utworzenia klucza. 
11. W menu klucze wybierz **skopiuj wartość klucza.** Zapisz kopię tej wartości, ponieważ będzie potrzebny do portalu Cloud Partner.
    
    ![Dynamics Pobierz klucz zarejestrowane](./media/cloud-partner-portal-lead-management-instructions-dynamics/registerkeys.png)
    
12. Wybierz **wymagane uprawnienia** , a następnie wybierz **Dodaj**. 
13. Wybierz **Dynamics CRM Online** jako nowy interfejs API oraz czy uprawnienia do *dostępu usługi CRM Online jako użytkownicy organizacji*.

14. Na Dynamics CRM, przejdź do obszaru Użytkownicy, a następnie wybierz listę rozwijaną "Użytkowników", aby przełączyć się do **użytkowników aplikacji**.
    
    ![Użytkownicy aplikacji](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuserfirst.PNG)

15. Wybierz **New** do utworzenia nowego użytkownika. Wybierz **użytkownika: UŻYTKOWNIK aplikacji** listy rozwijanej.
    
    ![Dodawanie nowego użytkownika aplikacji](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuser.PNG)

16. W **nowego użytkownika**, podaj nazwę i wiadomości e-mail chcesz użyć z tym połączeniem. Wklej **identyfikator aplikacji** aplikacji utworzonej w witrynie Azure portal.

     ![Konfigurowanie nowego użytkownika](./media/cloud-partner-portal-lead-management-instructions-dynamics/leadgencreateuser.PNG)

17. Wybierz pozycję "Ustawienia zabezpieczeń" w tym artykule, aby ukończyć konfigurowanie połączenia dla tego użytkownika.

### <a name="office-365"></a>Office 365

Jeśli nie chcesz używać usługi Azure Active Directory, możesz zarejestrować nowego użytkownika na *Centrum administracyjnego usługi Microsoft 365*. Możliwe będzie wymagany do zaktualizowania nazwy użytkownika/hasła co 90 dni, aby nadal korzystać z potencjalnymi klientami.

Wykonaj następujące kroki, aby skonfigurować usługi Office 365 dla usługi Dynamics CRM.

1. Zaloguj się do [Centrum administracyjnego usługi Microsoft 365](https://admin.microsoft.com).

2. Wybierz **administratora** kafelka.

    ![Centrum administracyjnego usługi Office Online](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline3.png)

3. Wybierz **Dodawanie użytkownika**.

    ![Dodawanie użytkownika](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline4.png)

4. Utwórz nowego użytkownika usługi składnika zapisywania potencjalnego klienta. Skonfiguruj następujące ustawienia:

    -   Podaj hasło, a następnie usuń zaznaczenie opcji "Ustaw tego użytkownika, zmiana hasła podczas pierwszego logowania".
    -   Wybierz "Użytkownik (bez dostępu administratora)" jako rolę dla użytkownika.
    -   Wybierz licencję produktu, pokazane na następnym zrzucie ekranu. Użytkownik zostanie naliczona opłata licencji, którą wybierzesz. Rozwiązanie współdziałają z usługą Dynamics CRM Online Basic licencji.
    
    ![Skonfiguruj uprawnienia użytkowników i licencji](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline5.png)

## <a name="security-settings"></a>Ustawienia zabezpieczeń

Ostatnim krokiem jest włączanie użytkownika został utworzony do zapisu potencjalnych klientów.

1.  Zaloguj się do usługi Dynamics CRM online.
2.  Na **ustawienia**, wybierz opcję **zabezpieczeń**.
    
    ![Ustawienia zabezpieczeń](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline6.png)

3.  Wybierz użytkownika, który został utworzony w **uprawnienia użytkownika**, a następnie wybierz pozycję **Zarządzanie rolami użytkowników**. Sprawdź **Microsoft Marketplace prowadzić Writer** Aby przypisać rolę.

    ![Przypisanie roli użytkownika](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline7.png)\

    >[!NOTE]
    >Ta rola jest tworzony przez to rozwiązanie, możesz zaimportować i ma tylko uprawnienia do zapisu potencjalnych klientów i śledzenia wersji rozwiązania w celu zapewnienia zgodności.

4.  Zabezpieczenia, wybierz **role zabezpieczeń** i Znajdź rolę Microsoft Marketplace prowadzić Writer.
    
    ![Skonfiguruj moduł zapisujący potencjalnego klienta zabezpieczeń](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline10.jpg)\

5. Wybierz **rekordy podstawowe** kartę. Włącz tworzenie/odczyt/zapis jednostki użytkownika interfejsu użytkownika.

    ![Włącz tworzenie/odczyt/zapis dla użytkownika](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline11.jpg)\

## <a name="wrap-up"></a>Podsumowanie

Zakończ konfigurowanie programu Dynamics CRM na potrzeby zarządzania potencjalnymi klientami, dodając informacje o koncie wygenerowane do portalu Cloud Partner. Na przykład:

-   **Usługa Azure Active Directory** - **identyfikator aplikacji** (przykład: *23456052-AAAA-bbbb-8662-1234df56788f*), **identyfikator katalogu** (przykład: *12345678-8af1-4asf-1234-12234d01db47*), a **klucz aplikacji** (przykład: *1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=* ).
-   **Usługi Office 365** - **adresu Url** (przykład: *https://contoso.crm4.dynamics.com* ), **nazwa_użytkownika** (przykład: *contoso\@ contoso.onmicrosoft.com*), a **hasło** (przykład: *P\@ ssw0rd*).

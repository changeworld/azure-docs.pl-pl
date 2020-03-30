---
title: Włączanie dostępu zdalnego do usługi Power BI za pomocą serwera proxy aplikacji usługi Azure AD
description: Obejmuje podstawowe informacje dotyczące integrowania lokalnej usługi Power BI z usługą Azure AD Application Proxy.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc96c94152b39cc70cfc4553690faaa5b9cb8d20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77111573"
---
# <a name="enable-remote-access-to-power-bi-mobile-with-azure-ad-application-proxy"></a>Włączanie dostępu zdalnego do usługi Power BI dla urządzeń przenośnych przy użyciu serwera proxy aplikacji usługi Azure AD

W tym artykule omówiono sposób używania serwera proxy aplikacji usługi Azure AD w celu umożliwienia aplikacji mobilnej usługi Power BI łączenia się z serwerem raportów usługi Power BI (PBIRS) i usługami SQL Server Reporting Services (SSRS) 2016 i nowszymi. Dzięki tej integracji użytkownicy z dala od sieci firmowej mogą uzyskiwać dostęp do swoich raportów usługi Power BI z aplikacji mobilnej Usługi Power BI i być chronieni przez uwierzytelnianie usługi Azure AD. Ta ochrona obejmuje korzyści związane z [zabezpieczeniami,](application-proxy-security.md#security-benefits) takie jak dostęp warunkowy i uwierzytelnianie wieloskładnikowe.  

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że usługi raportów zostały już wdrożone i [włączono serwer proxy aplikacji](application-proxy-add-on-premises-application.md).

- Włączenie serwera proxy aplikacji wymaga zainstalowania łącznika na serwerze Windows i ukończenia [wymagań wstępnych,](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) aby łącznik mógł komunikować się z usługami usługi Azure AD.  
- Podczas publikowania usługi Power BI zalecamy używanie tych samych domen wewnętrznych i zewnętrznych. Aby dowiedzieć się więcej o domenach niestandardowych, zobacz [Praca z domenami niestandardowymi w serwerze proxy aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain).
- Ta integracja jest dostępna dla aplikacji **Power BI Mobile dla systemów iOS i Android.**

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>Krok 1: Konfigurowanie delegowania ograniczonego protokołu Kerberos (KCD)

W przypadku aplikacji lokalnych korzystających z uwierzytelniania systemu Windows można uzyskać logowanie jednokrotne za pomocą protokołu uwierzytelniania Kerberos i funkcji o nazwie delegowanie ograniczone kerberos (KCD). Po skonfigurowaniu KCD umożliwia łącznikowi serwera proxy aplikacji uzyskanie tokenu systemu Windows dla użytkownika, nawet jeśli użytkownik nie zalogował się bezpośrednio do systemu Windows. Aby dowiedzieć się więcej o KCD, zobacz [Omówienie ograniczonego delegowania protokołu Kerberos](https://technet.microsoft.com/library/jj553400.aspx) i [delegowanie ograniczone kerberos do logowania jednokrotnego w aplikacjach za pomocą serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md).

W zakresie usług Reporting Services nie trzeba wykonywać wielu działań konfiguracyjnych. Pamiętaj tylko, aby mieć prawidłową nazwę głównej usługi (SPN), aby umożliwić prawidłowe uwierzytelnianie Kerberos. Upewnij się również, że serwer usług reporting services jest włączony do negocjowania uwierzytelniania.

Aby skonfigurować usługi KCD dla raportowania, przejdź do następujących kroków.

### <a name="configure-the-service-principal-name-spn"></a>Konfigurowanie nazwy głównej usługi (SPN)

Główna nazwa usługi jest unikatowym identyfikatorem usługi, która korzysta z uwierzytelniania Kerberos. Musisz upewnić się, że masz właściwą obecność spn HTTP dla serwera raportów. Aby uzyskać informacje na temat konfigurowania odpowiedniej głównej nazwy usługi dla serwera raportów, zobacz [Rejestrowanie głównej nazwy usługi dla serwera raportów](https://msdn.microsoft.com/library/cc281382.aspx).
Można sprawdzić, czy nazwa SPN została dodana, uruchamiając polecenie Setspn z opcją -L. Aby dowiedzieć się więcej o tym poleceniu, zobacz [Setspn](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spn-setspn-syntax.aspx).

### <a name="enable-negotiate-authentication"></a>Włącz uwierzytelnianie negocjuj

Aby włączyć serwer raportów do korzystania z uwierzytelniania Kerberos, należy skonfigurować typ uwierzytelniania serwera raportów jako RSWindowsNegotiate. Skonfiguruj to ustawienie za pomocą pliku rsreportserver.config.

```xml
<AuthenticationTypes>
    <RSWindowsNegotiate />
    <RSWindowsKerberos />
    <RSWindowsNTLM />
</AuthenticationTypes>
```

Aby uzyskać więcej informacji, zobacz [Modyfikowanie pliku konfiguracji usług Reporting Services](https://msdn.microsoft.com/library/bb630448.aspx) i [Konfigurowanie uwierzytelniania systemu Windows na serwerze raportów](https://msdn.microsoft.com/library/cc281253.aspx).

### <a name="ensure-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-reporting-services-application-pool-account"></a>Upewnij się, że łącznik jest zaufany dla delegowania do nazwy SPN dodane do konta puli aplikacji usług reporting services
Skonfiguruj KCD, aby usługa serwera proxy aplikacji usługi Azure AD mogła delegować tożsamości użytkowników do konta puli aplikacji usług Reporting Services. Skonfiguruj KCD, włączając łącznik serwera proxy aplikacji do pobierania biletów protokołu Kerberos dla użytkowników, którzy zostali uwierzytelnieni w usłudze Azure AD. Następnie ten serwer przekazuje kontekst do aplikacji docelowej lub Reporting Services w tym przypadku.

Aby skonfigurować KCD, powtórz następujące kroki dla każdego urządzenia łącznika:

1. Zaloguj się do kontrolera domeny jako administrator domeny, a następnie otwórz **active directory Użytkownicy i komputery**.
2. Znajdź komputer, na który działa łącznik.  
3. Kliknij dwukrotnie komputer, a następnie wybierz kartę **Delegowanie.**
4. Ustaw ustawienia delegowania **na Ufaj temu komputerowi tylko do delegowania do określonych usług**. Następnie wybierz pozycję **Użyj dowolnego protokołu uwierzytelniania**.
5. Wybierz **pozycję Dodaj**, a następnie wybierz pozycję **Użytkownicy lub Komputery**.
6. Wprowadź konto usługi używane w usługach Reporting Services. Jest to konto, do którego została dodana główna nazwa usługi w ramach konfiguracji usług Reporting Services.
7. Kliknij przycisk **OK**. Aby zapisać zmiany, kliknij ponownie przycisk **OK.**

Aby uzyskać więcej informacji, zobacz [Delegowanie ograniczone protokołu Kerberos do logowania jednokrotnego w aplikacjach za pomocą serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md).

## <a name="step-2-publish-report-services-through-azure-ad-application-proxy"></a>Krok 2: Publikowanie usług raportów za pośrednictwem serwera proxy aplikacji usługi Azure AD

Teraz możesz przystąpić do konfigurowania serwera proxy aplikacji usługi Azure AD.

1. Publikuj usługi raportów za pośrednictwem serwera proxy aplikacji z następującymi ustawieniami. Aby uzyskać instrukcje krok po kroku dotyczące publikowania aplikacji za pośrednictwem serwera proxy aplikacji, zobacz [Publikowanie aplikacji przy użyciu serwera proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   - **Wewnętrzny adres URL:** Wprowadź adres URL serwera raportów, do który łącznik może dotrzeć w sieci firmowej. Upewnij się, że ten adres URL jest osiągalny z serwera, na który jest zainstalowany łącznik. Najlepszym rozwiązaniem jest użycie domeny najwyższego `https://servername/` poziomu, takich jak aby uniknąć problemów z podścieżkami opublikowane za pośrednictwem serwera proxy aplikacji. Na przykład `https://servername/` użyj, `https://servername/reports/` `https://servername/reportserver/`a nie lub .
     > [!NOTE]
     > Zalecamy użycie bezpiecznego połączenia HTTPS z serwerem raportów. Aby uzyskać informacje na temat sposobu [konfigurowania połączeń SSL na serwerze raportów trybu macierzystego, zobacz Konfigurowanie połączeń SSL na serwerze raportów trybu macierzystego.](https://docs.microsoft.com/sql/reporting-services/security/configure-ssl-connections-on-a-native-mode-report-server?view=sql-server-2017)
   - **Zewnętrzny adres URL:** Wprowadź publiczny adres URL, z który połączy się aplikacja mobilna Usługi Power BI. Na przykład może wyglądać, `https://reports.contoso.com` jeśli używana jest domena niestandardowa. Aby użyć domeny niestandardowej, przekaż certyfikat domeny i wskaż rekord DNS domyślnej domenie msappproxy.net aplikacji. Aby uzyskać szczegółowe kroki, zobacz [Praca z domenami niestandardowymi w usłudze Azure AD Application Proxy](application-proxy-configure-custom-domain.md).

   - **Metoda wstępnego uwierzytelniania:** Usługa Azure Active Directory

2. Po opublikowaniu aplikacji skonfiguruj ustawienia logowania jednokrotnego, wykonując następujące czynności:

   a. Na stronie aplikacji w portalu wybierz pozycję **Logowanie jednokrotne**.

   b. W **trybie logowania jednokrotnego**wybierz pozycję **Zintegrowane uwierzytelnianie systemu Windows**.

   d. Ustaw **nazwę SPN aplikacji wewnętrznej** na wartość ustawioną wcześniej.  

   d. Wybierz **delegowaną tożsamość logowania** dla łącznika, który ma być używany w imieniu użytkowników. Aby uzyskać więcej informacji, zobacz [Praca z różnymi tożsamościami lokalnymi i tożsamościami w chmurze.](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

   e. Kliknij **przycisk Zapisz,** aby zapisać zmiany.

Aby zakończyć konfigurowanie aplikacji, przejdź do sekcji **Użytkownicy i grupy** i przypisz użytkowników, aby uzyskać dostęp do tej aplikacji.

## <a name="step-3-modify-the-reply-uris-for-the-application"></a>Krok 3: Modyfikowanie identyfikatora URI odpowiedzi dla aplikacji

Zanim aplikacja mobilna Usługi Power BI będzie mogła łączyć się z usługami raportowania i uzyskiwać do nich dostęp, należy skonfigurować rejestrację aplikacji, która została automatycznie utworzona w kroku 2. 

1. Na stronie **Przegląd** usługi Azure Active Directory wybierz pozycję **Rejestracje aplikacji**.
2. W zakładce **Wszystkie aplikacje** wyszukaj aplikację utworzoną w kroku 2.
3. Wybierz aplikację, a następnie wybierz pozycję **Uwierzytelnianie**.
4. Dodaj następujące identyfikatory URI przekierowania na podstawie używanej platformy.

   Podczas konfigurowania aplikacji dla usługi Power BI Mobile **iOS**dodaj następujące identyfikatory URI przekierowania typu Klient publiczny (Mobile & Desktop):
   - `msauth://code/mspbi-adal%3a%2f%2fcom.microsoft.powerbimobile`
   - `msauth://code/mspbi-adalms%3a%2f%2fcom.microsoft.powerbimobilems`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `mspbi-adalms://com.microsoft.powerbimobilems`
   
   Podczas konfigurowania aplikacji dla usługi Power BI Mobile **Android**dodaj następujące identyfikatory URI przekierowania typu Klient publiczny (Mobile & Desktop):
   - `urn:ietf:wg:oauth:2.0:oob`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `msauth://com.microsoft.powerbim/g79ekQEgXBL5foHfTlO2TPawrbI%3D` 
   - `msauth://com.microsoft.powerbim/izba1HXNWrSmQ7ZvMXgqeZPtNEU%3D`

   > [!IMPORTANT]
   > Identyfikatory URI przekierowania muszą zostać dodane, aby aplikacja działała poprawnie. Jeśli konfigurujesz aplikację zarówno dla systemu Power BI Mobile iOS, jak i Android, dodaj do listy identyfikatorów URI przekierowania typu `urn:ietf:wg:oauth:2.0:oob`Klient publiczny (Mobile & Desktop) skonfigurowanych dla systemu iOS: .

## <a name="step-4-connect-from-the-power-bi-mobile-app"></a>Krok 4: Łączenie się z aplikacji Power BI Mobile App

1. W aplikacji mobilnej Usługi Power BI połącz się z wystąpieniem usług Reporting Services. Aby to zrobić, wprowadź **zewnętrzny adres URL** aplikacji opublikowanej za pośrednictwem serwera proxy aplikacji.

   ![Aplikacja mobilna Power BI z zewnętrznym adresem URL](media/application-proxy-integrate-with-power-bi/app-proxy-power-bi-mobile-app.png)

2. Wybierz przycisk **Połącz**. Zostaniesz przekierowany do strony logowania usługi Azure Active Directory.

3. Wprowadź prawidłowe poświadczenia dla użytkownika i wybierz pozycję **Zaloguj się**. Zobaczysz elementy z serwera reporting services.

## <a name="step-5-configure-intune-policy-for-managed-devices-optional"></a>Krok 5: Konfigurowanie zasad usługi Intune dla zarządzanych urządzeń (opcjonalnie)

Za pomocą usługi Microsoft Intune można zarządzać aplikacjami klienckimi używanymi przez pracowników firmy. Usługa Intune umożliwia korzystanie z funkcji, takich jak szyfrowanie danych i dodatkowe wymagania dotyczące dostępu. Aby dowiedzieć się więcej o zarządzaniu aplikacjami za pośrednictwem usługi Intune, zobacz Zarządzanie aplikacjami usługi Intune. Aby umożliwić aplikacji mobilnej usługi Power BI pracę z zasadami usługi Intune, należy wykonać następujące czynności.

1. Przejdź do **usługi Azure Active Directory,** a następnie **rejestracji aplikacji**.
2. Wybierz aplikację skonfigurowane w kroku 3 podczas rejestrowania aplikacji klienta macierzystego.
3. Na stronie aplikacji wybierz pozycję **Uprawnienia interfejsu API**.
4. Kliknij **pozycję Dodaj uprawnienie**. 
5. W obszarze **Interfejsy API używa się interfejsów API**mojej organizacji wyszukaj hasło "Zarządzanie aplikacjami mobilnymi firmy Microsoft" i wybierz je.
6. Dodawanie uprawnienia **DeviceManagementManagedApps.ReadWrite** do aplikacji
7. Kliknij **przycisk Udziel zgody administratora,** aby udzielić dostępu uprawnienia do aplikacji.
8. Skonfiguruj żądane zasady usługi Intune, odwołując się [do zasad jak utworzyć i przypisać zasady ochrony aplikacji](https://docs.microsoft.com/intune/app-protection-policies).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli aplikacja zwraca stronę błędu po próbie załadowania raportu na więcej niż kilka minut, może być konieczna zmiana ustawienia limitu czasu. Domyślnie serwer proxy aplikacji obsługuje aplikacje, które zajmują do 85 sekund, aby odpowiedzieć na żądanie. Aby wydłużyć to ustawienie do 180 sekund, wybierz limit czasu zaplecza do **Long** na stronie ustawienia serwera proxy aplikacji dla aplikacji. Aby uzyskać wskazówki dotyczące tworzenia szybkich i niezawodnych [raportów, zobacz Najważniejsze wskazówki dotyczące raportów usługi Power BI](https://docs.microsoft.com/power-bi/power-bi-reports-performance).

## <a name="next-steps"></a>Następne kroki

- [Włączanie natywnych aplikacji klienckich do interakcji z aplikacjami proxy](application-proxy-configure-native-client-application.md)
- [Wyświetlanie lokalnych raportów serwera raportów i wskaźników KPI w aplikacjach mobilnych usługi Power BI](https://docs.microsoft.com/power-bi/consumer/mobile/mobile-app-ssrs-kpis-mobile-on-premises-reports)

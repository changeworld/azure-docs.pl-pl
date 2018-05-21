---
title: Zarządzanie dostępem do aplikacji w chmurze poprzez ograniczenie dzierżaw - Azure | Dokumentacja firmy Microsoft
description: Jak używać ograniczenia dzierżawy do zarządzania użytkowników, którzy mają dostęp do aplikacji, w oparciu o ich dzierżawy usługi Azure AD.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: yossib
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: barbkess
ms.reviewer: richagi
ms.openlocfilehash: 6584db755a586fcf5fd7318f9396b797edeaf0ad
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Ograniczenia dzierżawy do zarządzania dostępem do SaaS aplikacji w chmurze

Dużych organizacjach, które wyróżnić zabezpieczeń mają zostać przeniesione do usługi, takie jak usługi Office 365 w chmurze, ale muszą dowiedzieć się, że użytkownikom tylko masz dostęp do zasobów zatwierdzone. Tradycyjnie firm ograniczyć nazwy domeny lub adresy IP Chcąc zarządzać dostępem. Takie podejście kończy się niepowodzeniem w świecie, gdzie hostowane są aplikacji SaaS w chmurze publicznej, w systemie nazw domen udostępnionego, takich jak outlook.office.com i login.microsoftonline.com. Blokowanie tych adresów może uniemożliwić użytkownikom uzyskiwanie dostępu do programu Outlook w sieci web całkowicie, a nie tylko ograniczenie do zatwierdzonej tożsamości i zasobów.

Rozwiązanie usługi Azure Active Directory dla tego wyzwania jest funkcję ograniczenia dzierżawy. Ograniczenia dzierżawcy umożliwia organizacjom kontrolować dostęp do aplikacji w chmurze SaaS, oparte na dzierżawy usługi Azure AD, używanych przez aplikacje dla logowania jednokrotnego. Na przykład można zezwolić na dostęp do aplikacji usługi Office 365 w organizacji, uniemożliwiając dostęp do innych organizacji wystąpienia te same aplikacje.  

Ograniczenia dzierżawy umożliwia organizacjom Określ listę dzierżawców ich użytkownicy mogą uzyskiwać dostęp. Następnie usługi Azure AD tylko udziela dostępu do tych dozwolone dzierżaw.

Ten artykuł dotyczy ograniczenia dzierżawy dla usługi Office 365, ale funkcja powinien współpracować z dowolnej aplikacji w chmurze SaaS korzystający z protokołów nowoczesnego uwierzytelniania w usłudze Azure AD dla logowania jednokrotnego. Jeśli używasz SaaS aplikacji za pomocą innej usługi Azure AD dzierżawy dzierżawy używane przez usługi Office 365, upewnij się, czy wszystkie wymagane dzierżaw są dozwolone. Aby uzyskać więcej informacji o aplikacji SaaS w chmurze, zobacz [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).

## <a name="how-it-works"></a>Jak to działa

Całościowe rozwiązanie obejmuje następujące składniki: 

1. **Usługi Azure AD** — Jeśli `Restrict-Access-To-Tenants: <permitted tenant list>` jest obecny, Azure AD tylko problemy z tokenów zabezpieczających dla dozwolonych dzierżaw. 

2. **Lokalnej infrastruktury serwera proxy** — urządzenie serwera proxy do kontroli SSL, skonfigurowany tak, aby wstawić nagłówek zawierający listę umieszczone dzierżaw w ruch kierowany do usługi Azure AD. 

3. **Oprogramowanie klienckie** — obsługuje ograniczenia dzierżawy, oprogramowanie klienckie należy zażądać tokeny bezpośrednio z usługi Azure AD, aby ruch może zostać przechwycona przez infrastrukturę serwera proxy. Ograniczenia dzierżawy jest obecnie obsługiwane przez aplikacje pakietu Office 365 przeglądarki i klienci Office stosowania nowoczesnego uwierzytelniania (na przykład OAuth 2.0). 

4. **Nowoczesne uwierzytelnianie** — usługi w chmurze musi używają nowoczesnego uwierzytelniania ograniczenia dzierżawy i Zablokuj dostęp do wszystkich dzierżaw z systemem innym niż dozwolona. Usługi Office 365 chmury musi być skonfigurowana do używania protokołów nowoczesnego uwierzytelniania domyślnie. Aby uzyskać najnowsze informacje dla usługi Office 365 Obsługa nowoczesnego uwierzytelniania, przeczytaj [nowoczesne uwierzytelnianie usługi Office 365 aktualizacji](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/).

Na poniższym diagramie przedstawiono przepływ ruchu wysokiego poziomu. Inspekcja SSL jest wymagany tylko na ruch do usługi Azure AD, a nie do usługi Office 365 w chmurze. Ta różnica jest ważne, ponieważ wolumin ruchu do uwierzytelniania w usłudze Azure AD jest zwykle znacznie niższe niż wielkość ruchu aplikacji SaaS, takich jak Exchange Online i SharePoint Online.

![Ograniczenia ruchu dzierżawy — diagram](./media/active-directory-tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Konfigurowanie ograniczeń dzierżawy

Istnieją dwa kroki, aby rozpocząć pracę z ograniczeniami dzierżawy. Pierwszym krokiem jest upewnij się, że klienci mogą prawo adresów. Drugim jest konfigurowanie infrastruktury serwera proxy.

### <a name="urls-and-ip-addresses"></a>Adresy URL i adresów IP

Aby użyć ograniczenia dzierżawy, musi być nawiązać następujące usługi Azure AD adresy URL do uwierzytelniania klientów: login.microsoftonline.com, login.microsoft.com i login.windows.net. Ponadto do dostępu do usługi Office 365, klientów należy nawiązać nazw FQDN/adresy URL i adresów IP zdefiniowanych w [zakresów adresów IP i URL usługi Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2). 

### <a name="proxy-configuration-and-requirements"></a>Konfiguracja serwera proxy i wymagania

Aby włączyć ograniczenia dzierżawcy przy użyciu infrastruktury serwera proxy jest wymagana następująca konfiguracja. W tych wskazówkach jest ogólny, więc powinni zapoznać się z dokumentacją producenta serwera proxy dla wykonania określonych kroków.

#### <a name="prerequisites"></a>Wymagania wstępne

- Serwer proxy musi mieć możliwość wykonania przejęciu SSL, wstawiania nagłówka HTTP i je filtrować docelowych przy użyciu nazw FQDN/adresów URL. 

- Klienci muszą ufać łańcucha certyfikatów przedstawiony przez serwer proxy dla komunikacji SSL. Na przykład jeśli są używane certyfikaty z wewnętrznej infrastruktury kluczy publicznych, wewnętrzny wystawiających certyfikaty głównego urzędu certyfikacji musi być zaufany.

- Ta funkcja jest dostępna w subskrypcji usługi Office 365, ale jeśli chcesz użyć do kontrolowania dostępu do innych aplikacji SaaS ograniczenia dzierżawy następnie licencji Azure AD Premium 1 są wymagane.

#### <a name="configuration"></a>Konfigurowanie

Dla każdego żądania przychodzącego login.microsoftonline.com login.microsoft.com i login.windows.net Wstaw dwa nagłówki HTTP: *ograniczanie dostępu do dzierżawców* i *ograniczanie dostępu do kontekstu*.

Nagłówki powinny zawierać następujące elementy: 
- Dla *ograniczanie dostępu do dzierżawców*, wartość \<dozwolone listy dzierżawy\>, która jest rozdzielana przecinkami lista dzierżawcy, aby umożliwić użytkownikom dostęp do. Dowolnej domeny, która jest zarejestrowana w usłudze dzierżawcy może służyć do identyfikowania dzierżawy na tej liście. Na przykład aby zezwolić na dostęp do dzierżawców zarówno Contoso i Fabrikam, pary nazwa/wartość wygląda następująco:  `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com` 
- Dla *ograniczanie dostępu do kontekstu*, wartość identyfikatora jednego katalogu deklarowanie dzierżawy, który jest ustawienie ograniczenia dzierżawy. Na przykład aby zadeklarować Contoso jako dzierżawy, który ustawić zasady ograniczeń dzierżawy, pary nazwa/wartość wygląda następująco: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> Można znaleźć Identyfikatora katalogu w [portalu Azure](https://portal.azure.com). Zaloguj się jako administrator, wybierz opcję **usługi Azure Active Directory**, a następnie wybierz pozycję **właściwości**.

Aby uniemożliwić użytkownikom wstawianie własnych nagłówka HTTP z niezatwierdzonej dzierżawcami, serwer proxy musi zastąpić nagłówka ograniczanie dostępu do dzierżawców, jeśli jest już obecne w żądaniu przychodzącym. 

Użyj serwera proxy dla wszystkich żądań login.microsoftonline.com login.microsoft.com i login.windows.net można wymusić klientów. Na przykład jeśli pliki PAC są używane klientów bezpośrednich do używania serwera proxy, użytkownicy końcowi nie należy można edytować ani wyłączyć plików PAC.

## <a name="the-user-experience"></a>Środowisko użytkownika

Ta sekcja zawiera środowisko dla administratorów i użytkowników końcowych.

### <a name="end-user-experience"></a>Środowisko użytkownika końcowego

Przykład użytkownika znajduje się w sieci firmy Contoso, ale próbuje uzyskać dostępu do wystąpienia Fabrikam udostępnionego SaaS aplikacji takich jak program Outlook online. Jeśli firma Contoso jest dzierżawca nie jest dozwolone dla tego wystąpienia, użytkownik zobaczy następującą stronę:

![Strona dla użytkowników w dzierżaw z systemem innym niż dozwolone odmowa dostępu](./media/active-directory-tenant-restrictions/end-user-denied.png)

### <a name="admin-experience"></a>Środowisko pracy administratora

Podczas konfiguracji ograniczenia dzierżawy odbywa się w infrastrukturze firmowy serwer proxy, Administratorzy mają dostęp do raportów ograniczenia dzierżawy w portalu Azure bezpośrednio. Aby wyświetlić raporty, przejdź do strony Omówienie usługi Azure Active Directory, a następnie sprawdź w obszarze innych funkcji.

Administrator dzierżawy określony jako dzierżawy ograniczeniami dostępu kontekstu ten raport służy aby zobaczyć wszystkie logowania zablokowany ze względu na zasady ograniczeń dzierżawy, w tym tożsamość użyta i katalog docelowy identyfikator.

![Użyj portalu Azure, aby wyświetlić ograniczeniami prób logowania](./media/active-directory-tenant-restrictions/portal-report.png)

Podobnie jak inne raporty w portalu Azure Aby określić zakres raportu można używać filtrów. Można filtrować według określonego użytkownika, aplikacji, klienta lub interwał czasu.

## <a name="office-365-support"></a>Obsługa usługi Office 365

Aplikacje pakietu Office 365 musi spełniać dwa kryteria, aby w pełni obsługiwać ograniczenia dzierżawy:

1. Używaną przez klienta obsługuje nowoczesnego uwierzytelniania
2. Nowoczesne uwierzytelnianie jest włączony w domyślnym protokołem uwierzytelniania dla usługi w chmurze.

Zapoznaj się [nowoczesne uwierzytelnianie usługi Office 365 aktualizacji](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/) najnowsze informacje, na które Office klientami obsługuje obecnie nowoczesnego uwierzytelniania. Ta strona zawiera również linki do instrukcji dotyczących włączania nowoczesnego uwierzytelniania na określonych usługi Exchange Online i Skype dla firm Online dzierżaw. Domyślnie w usłudze SharePoint Online jest już włączone nowoczesnego uwierzytelniania.

Ograniczenia dzierżawy jest obecnie obsługiwane przez usługi Office 365 aplikacji opartych na przeglądarce (programu SharePoint portalu usługi Office, Yammer, witryny, Outlook Web itp.). Grubość klientów (program Outlook, Skype dla firm, Word, Excel, PowerPoint, itp.) Ograniczenia dzierżawy są wymuszane tylko, gdy nowoczesnego uwierzytelniania jest używany.  

Outlook i Skype dla firm klientów, którzy obsługują nowoczesnego uwierzytelniania może nadal mogli używać starszych protokołów przed dzierżawcy, gdzie nowoczesnego uwierzytelniania nie jest włączone, efektywnie pomijanie ograniczenia dzierżawy. Aplikacje używające starszych protokołów mogą być blokowane przez ograniczenia dzierżawy, jeśli zgłosić login.microsoftonline.com, login.microsoft.com lub login.windows.net podczas uwierzytelniania.

Dla programu Outlook w systemie Windows klienci mogą zadecydować o stosowaniu ograniczenia uniemożliwia użytkownikom dodawanie konta poczty niezatwierdzonych do ich profile. Na przykład, zobacz [Zapobiegaj dodawanie kont innych niż domyślne Exchange konta](http://gpsearch.azurewebsites.net/default.aspx?ref=1) ustawienie zasad grupy. Dla programu Outlook na platformach z systemem innym niż Windows, a dla usługi Skype dla firm na wszystkich platformach Pełna obsługa ograniczenia dzierżawy nie jest obecnie dostępna.

## <a name="testing"></a>Testowanie

Jeśli chcesz wypróbować ograniczenia dzierżawy przed rozpoczęciem implementacji dla całej organizacji, dostępne są dwie opcje: podejście oparta na hoście przy użyciu narzędzia, takiego jak Fiddler lub przemieszczanego Wdrażanie ustawień serwera proxy.

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler podejścia oparta na hoście

Fiddler to bezpłatna debugowanie serwera proxy, który może służyć do przechwytywania i zmodyfikować ruchu HTTP/HTTPS, w tym Wstawianie nagłówków HTTP sieci web. Aby skonfigurować narzędzia Fiddler do testowania ograniczenia dzierżawy, wykonaj następujące czynności:

1.  [Pobierz i zainstaluj Fiddler](http://www.telerik.com/fiddler).
2.  Konfigurowanie narzędzia Fiddler do odszyfrowania ruchu HTTPS na [Fiddler w dokumentacji pomocy](http://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).
3.  Konfigurowanie narzędzia Fiddler do wstawienia *ograniczanie dostępu do dzierżawców* i *ograniczanie dostępu do kontekstu* nagłówki za pomocą reguł niestandardowych:
  1. W narzędziu Fiddler sieci Web debugera wybierz **reguły** menu i wybierz **dostosować zasady...** Aby otworzyć plik CustomRules.
  2. Dodaj następujące wiersze na początku *OnBeforeRequest* funkcji. Zastąp \<domena dzierżawy\> z domeną zarejestrowany w dzierżawie, na przykład contoso.onmicrosoft.com. Zastąp \<identyfikator katalogu\> z identyfikatorem Azure AD GUID Twojej dzierżawy.

  ```
  if (oSession.HostnameIs("login.microsoftonline.com") || oSession.HostnameIs("login.microsoft.com") || oSession.HostnameIs("login.windows.net")){      oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";      oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";}
  ```

  Jeśli trzeba umożliwić wielu dzierżawców, użyj przecinka nazwy dzierżawy. Na przykład:

  ```
  oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";
  ```

4. Zapisz i zamknij plik CustomRules.

Po skonfigurowaniu Fiddler, można przechwytywać ruch, przechodząc do **pliku** menu i wybierając **przechwytywania ruchu**.

### <a name="staged-rollout-of-proxy-settings"></a>Wdrażanie przemieszczonych ustawienia serwera proxy

W zależności od możliwości infrastruktury serwera proxy można przemieścić wdrożenia ustawienia dla użytkowników. Poniżej przedstawiono kilka opcji wysokiego poziomu do rozważenia:

1.  Przy użyciu plików PAC punktu użytkowników testowych do testów infrastruktury serwera proxy, normalni użytkownicy nadal korzystać z infrastruktury serwera proxy produkcji.
2.  Niektóre serwery proxy mogą obsługiwać różne konfiguracje przy użyciu grup.

Zapoznaj się z dokumentacją serwera proxy, aby uzyskać szczegółowe informacje.

## <a name="next-steps"></a>Kolejne kroki

- Przeczytaj informacje o [aktualizacji usługi Office 365 nowoczesnego uwierzytelniania](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/)

- Przegląd [zakresów adresów IP i URL usługi Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)

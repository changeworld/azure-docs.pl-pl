---
title: Zarządzanie dostępem do aplikacji w chmurze przez ograniczenie możliwości wykonywania dzierżawy — Azure | Dokumentacja firmy Microsoft
description: Jak używać ograniczenia dotyczące dzierżawy do zarządzania użytkowników, którzy mają dostęp do aplikacji, w oparciu o ich dzierżawy usługi Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: celested
ms.reviewer: richagi
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2dc03b329ce8a4b42f44b958aee96654dafb098
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56197746"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Ograniczenia dzierżawy do zarządzania dostępem do aplikacji SaaS działających w chmurze

Duże organizacje, które zwracają uwagę zabezpieczenia chce się przejść do usług, takich jak Office 365 w chmurze, ale musisz wiedzieć, że użytkownikom tylko dostęp do zasobów zatwierdzone. Tradycyjnie firmy ograniczają nazwy domen i adresów IP podczas zarządzania dostępem. Ta metoda nie powiedzie się w świecie, w którym aplikacje SaaS są hostowane w chmurze publicznej, w systemie nazw domen udostępnionych, takich jak outlook.office.com i login.microsoftonline.com. Blokowanie tych adresów zachowa użytkownikom dostęp do programu Outlook w sieci web w całości, zamiast jedynie ograniczenie je do tożsamości zatwierdzone i zasobów.

Azure Active Directory rozwiązanie tego problemu jest funkcja o nazwie ograniczenia dotyczące dzierżawy. Ograniczenia dotyczące dzierżawy umożliwia organizacjom do kontrolowania dostępu do aplikacje SaaS w chmurze, oparty na dzierżawie usługi Azure AD, do której aplikacje na użytek logowania jednokrotnego. Na przykład można zezwolić na dostęp do aplikacji usługi Office 365 w Twojej organizacji, zezwalając jednocześnie dostęp do innych organizacji wystąpień tych tych samych aplikacji.  

Ograniczenia dotyczące dzierżawy umożliwia organizacjom możliwość określenia listy dzierżaw, które użytkownicy mogą uzyskiwać dostęp. Następnie usługa Azure AD tylko udziela dostępu do tych dozwolonych dzierżaw.

Ten artykuł koncentruje się na ograniczenia dotyczące dzierżawy dla usługi Office 365, ale funkcja powinny współpracować z dowolnej aplikacji w chmurze SaaS komunikującą nowoczesnego uwierzytelniania za pomocą usługi Azure AD na potrzeby logowania jednokrotnego. Jeśli używasz modelu SaaS aplikacji z różnymi usługami Azure AD dzierżawy w dzierżawie, używane przez usługi Office 365, upewnij się, czy wszystkie wymagane dzierżawców są dozwolone. Aby uzyskać więcej informacji na temat aplikacji SaaS w chmurze, zobacz [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).

## <a name="how-it-works"></a>Jak to działa

Ogólne rozwiązanie obejmuje następujące składniki: 

1. **Usługa Azure AD** — Jeśli `Restrict-Access-To-Tenants: <permitted tenant list>` jest obecny, Azure AD tylko problemy z tokenów zabezpieczających dla dozwolonych dzierżaw. 

2. **Infrastruktury serwera proxy w środowisku lokalnym** — urządzenie serwera proxy do inspekcji połączenia SSL, skonfigurowany do nagłówka zawierający listę Wstaw dozwolonych dzierżaw do ruchu skierowanego do usługi Azure AD. 

3. **Oprogramowanie klienckie** — obsługuje ograniczenia dzierżawy, oprogramowanie klienckie musi żądać tokenów bezpośrednio z usługi Azure AD, aby ruch może zostać przechwycona przez infrastruktury serwera proxy. Ograniczenia dotyczące dzierżawy jest obecnie obsługiwane przez oparte na przeglądarce aplikacji usługi Office 365 i klientach pakietu Office, stosowania nowoczesnego uwierzytelniania (takich jak OAuth 2.0). 

4. **Nowoczesne uwierzytelnianie** — usługi w chmurze musi korzystają z nowoczesnego uwierzytelniania, użyj ograniczeń dzierżawy i zablokować dostęp do wszystkich dzierżawców — dozwolone. Usług Office 365 cloud services, muszą domyślnie skonfigurowana do używania protokołów nowoczesnego uwierzytelniania. Aby uzyskać najnowsze informacje na temat obsługi usługi Office 365 nowoczesnego uwierzytelniania, przeczytaj [nowoczesne uwierzytelnianie usługi Office 365 zaktualizowane](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/).

Na poniższym diagramie przedstawiono przepływ ruchu wysokiego poziomu. Inspekcji połączenia SSL jest wymagany tylko na ruch do usługi Azure AD, a nie do usługi w chmurze usługi Office 365. Różnica ta jest ważne, ponieważ natężenia ruchu uwierzytelniania do usługi Azure AD jest zwykle znacznie niższa niż natężeniem ruchu do aplikacji SaaS, takich jak Exchange Online i SharePoint Online.

![Ograniczenia przepływu ruchu dzierżawy — diagram](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Skonfiguruj ograniczenia dotyczące dzierżawy

Istnieją dwa kroki, aby rozpocząć pracę z ograniczeniami dzierżawy. Pierwszym krokiem jest, aby upewnić się, czy klient może połączyć się odpowiednie adresy. Drugi cel to Konfigurowanie infrastruktury serwera proxy.

### <a name="urls-and-ip-addresses"></a>Adresy URL i adresów IP

Aby użyć ograniczenia dotyczące dzierżawy, klientów musi być w stanie połączyć się z następujące usługi Azure AD adresy URL uwierzytelniania: login.microsoftonline.com login.microsoft.com i login.windows.net. Ponadto dostęp do usługi Office 365 dla klientów należy nawiązać nazw FQDN/adresy URL i adresów IP określonych w [URL usługi Office 365 i zakresy adresów IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2). 

### <a name="proxy-configuration-and-requirements"></a>Wymagania i konfiguracji serwera proxy

Aby włączyć ograniczenia dotyczące dzierżawy za pośrednictwem infrastruktury serwera proxy jest wymagana następująca konfiguracja. Ten przewodnik jest ogólny, więc powinni zapoznać się z dokumentacją dostawcy serwera proxy dla konkretnej implementacji kroków.

#### <a name="prerequisites"></a>Wymagania wstępne

- Serwer proxy musi mieć możliwość wykonania przejęciu protokołu SSL, wstawiania nagłówka HTTP i filtrowanie miejsc docelowych przy użyciu nazw FQDN/adresów URL. 

- Klienci muszą ufać łańcucha certyfikatów przedstawiony przez serwer proxy dla komunikacji SSL. Na przykład jeśli są używane certyfikaty z wewnętrznej infrastruktury kluczy publicznych, wewnętrzny wystawiający certyfikaty głównego urzędu certyfikacji musi być zaufany.

- Ta funkcja jest dostępna w ramach subskrypcji usługi Office 365, ale jeśli chcesz kontrolować dostęp do innych aplikacji SaaS za pomocą ograniczenia dotyczące dzierżawy następnie licencje usługi Azure AD Premium 1 są wymagane.

#### <a name="configuration"></a>Konfigurowanie

Dla każdego żądania przychodzącego login.microsoftonline.com login.microsoft.com i login.windows.net Wstaw dwa nagłówki HTTP: *Ogranicz dostęp do dzierżaw* i *Ogranicz dostęp kontekst-*.

Nagłówki powinien zawierać następujące elementy: 
- Dla *ograniczanie dostępu do dzierżawy*, wartość \<dozwolone listy dzierżawy\>, który znajduje się lista rozdzielonych przecinkami dzierżaw, aby zezwolić użytkownikom na dostęp do. Dowolnej domeny, która jest zarejestrowana w dzierżawie może służyć do identyfikowania dzierżawy na tej liście. Na przykład aby zezwolić na dostęp do dzierżawy firmy Contoso i Fabrikam, pary nazwa/wartość wygląda następująco:  `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com` 
- Aby uzyskać *ograniczanie dostępu do kontekstu*, wartość Identyfikatora jeden katalog, deklarowania, której dzierżawy jest ustawianie ograniczeń dzierżawy. Na przykład aby zadeklarować Contoso jako dzierżawy, której ustawienie zasad ograniczenia dotyczące dzierżawy, pary nazwa/wartość wygląda następująco: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> Można znaleźć Identyfikatora katalogu w [witryny Azure portal](https://portal.azure.com). Zaloguj się jako administrator, wybierz opcję **usługi Azure Active Directory**, a następnie wybierz **właściwości**.

Aby uniemożliwić użytkownikom wstawianie własne nagłówka HTTP z niezatwierdzonych dzierżawami, serwer proxy musi zastąpić nagłówka ograniczanie dostępu do dzierżawy, jeśli jest już obecne w żądaniu przychodzącym. 

Klienci muszą zmuszeni do używania serwera proxy dla wszystkich żądań login.microsoftonline.com login.microsoft.com i login.windows.net. Na przykład jeśli PAC pliki są używane do kierowania klientów do używania serwera proxy, użytkownicy końcowi nie należy stanie edytować lub wyłączyć funkcję plików PAC.

## <a name="the-user-experience"></a>Środowisko użytkownika

W tej sekcji przedstawiono środowisko dla administratorów i użytkowników końcowych.

### <a name="end-user-experience"></a>Środowisko użytkownika końcowego

Przykładowy użytkownik znajduje się w sieci firmy Contoso, ale próbuje uzyskać dostęp Fabrikam wystąpienie udostępnione aplikacji SaaS takich jak program Outlook online. Jeśli firma Fabrikam jest dzierżawy nie jest dozwolone dla tego wystąpienia firmy Contoso, użytkownik zobaczy następujące strony:

![Odmowę dostępu do strony dla użytkowników w dzierżawach — dozwolone](./media/tenant-restrictions/end-user-denied.png)

### <a name="admin-experience"></a>Środowisko pracy administratora

Podczas konfiguracji ograniczenia dotyczące dzierżawy odbywa się na infrastrukturze firmowym serwerem proxy, Administratorzy mogą bezpośrednio dostępu do raportów ograniczenia dotyczące dzierżawy w witrynie Azure portal. Aby wyświetlić raporty, przejdź do strony Przegląd usługi Azure Active Directory, a następnie sprawdź w obszarze inne możliwości.

Administrator dzierżawy, określony jako dzierżawy ograniczeniami dostępu kontekstowe ten raport służy będzie zablokowany ze względu na zasady ograniczeń dzierżawy, w tym tożsamość używana logowania i katalog docelowy identyfikator. Logowania są uwzględnione, jeśli użytkownik dzierżawy lub zasobów dzierżawy dla logowania jest ustawienie ograniczenia dzierżawy.

![Umożliwia wyświetlanie ograniczeniami prób logowania w witrynie Azure portal](./media/tenant-restrictions/portal-report.png)

Podobnie jak inne raporty w witrynie Azure portal można użyć filtrów, aby określić zakres raportu. Można filtrować według określonego użytkownika, aplikacji, klienta lub przedział czasu.

## <a name="office-365-support"></a>Obsługa usługi Office 365

Aplikacje usługi Office 365, musi spełniać dwa kryteria, aby w pełni obsługiwać ograniczenia dotyczące dzierżawy:

1. Klient używany obsługuje nowoczesnego uwierzytelniania
2. Nowoczesne uwierzytelnianie jest włączony w domyślnym protokołem uwierzytelniania dla usługi w chmurze.

Zapoznaj się [nowoczesne uwierzytelnianie usługi Office 365 zaktualizowane](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/) najnowsze informacje na Office klienci obecnie obsługują nowoczesnego uwierzytelniania. Ta strona zawiera także łącza do instrukcje dotyczące włączania nowoczesnego uwierzytelniania na określonych usług Exchange Online i Skype dla firm Online dzierżaw. Nowoczesne uwierzytelnianie jest już włączone domyślnie w usłudze SharePoint Online.

Ograniczenia dotyczące dzierżawy jest obecnie obsługiwane przez aplikacje oparte na przeglądarce usługi Office 365 (SharePoint portalu usługi Office, usługi Yammer, witryn, aplikacji Outlook w sieci Web itp.). Aby uzyskać gęstych klientów (program Outlook, Skype dla firm, Word, Excel, PowerPoint, itp.) Ograniczenia dotyczące dzierżawy można wymusić tylko w przypadku, gdy jest używany nowoczesnego uwierzytelniania.  

Program Outlook i Skype dla klientów biznesowych, które obsługują nowoczesnego uwierzytelniania, mogą nadal mogli używać starszych protokołów dla dzierżaw, gdzie nowoczesnego uwierzytelniania nie jest włączone, efektywnie pomijanie ograniczenia dotyczące dzierżawy. Aplikacje, które używają starszych protokołów może być blokowany przez ograniczenia dotyczące dzierżawy, jeśli zgłosić login.microsoftonline.com login.microsoft.com i login.windows.net podczas uwierzytelniania.

Dla programu Outlook, Windows klientów może zadecydować o stosowaniu ograniczenia uniemożliwia użytkownikom dodawanie kont pocztowych niezatwierdzonych do ich profile. Na przykład zobacz [Zapobiegaj dodawanie kont innych niż domyślne Exchange konta](https://gpsearch.azurewebsites.net/default.aspx?ref=1) ustawienie zasad grupy.

## <a name="testing"></a>Testowanie

Jeśli chcesz wypróbować ograniczenia dotyczące dzierżawy przed rozpoczęciem implementacji dla całej organizacji, dostępne są dwie opcje: podejście oparte na hoście przy użyciu narzędzia, takiego jak Fiddler lub etapowe Wdrażanie ustawień serwera proxy.

### <a name="fiddler-for-a-host-based-approach"></a>Narzędzie fiddler podejścia opartego na hoście

Jest to bezpłatny internetowy serwer proxy, który może służyć do przechwytywania i zmodyfikować ruch HTTP/HTTPS, w tym Wstawianie nagłówków HTTP debugowania. Aby skonfigurować narzędzie Fiddler, aby przetestować ograniczenia dotyczące dzierżawy, wykonaj następujące czynności:

1.  [Pobierz i zainstaluj program Fiddler](https://www.telerik.com/fiddler).
2.  Konfigurowanie narzędzia Fiddler do odszyfrowywania ruchu HTTPS na [dokumentacji pomocy w narzędziu Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).
3.  Konfigurowanie programu Fiddler, aby wstawić *ograniczanie dostępu do dzierżawy* i *ograniczanie dostępu do kontekstu* nagłówki za pomocą reguł niestandardowych:
    1. W narzędziu Fiddler debuger sieci Web wybierz **reguły** menu, a następnie wybierz **Dostosuj reguły...** Aby otworzyć plik element CustomRules.
    2. Dodaj następujące wiersze na początku *OnBeforeRequest* funkcji. Zastąp \<domena dzierżawy\> z domeną zarejestrowany w swojej dzierżawie, na przykład contoso.onmicrosoft.com. Zastąp \<identyfikator katalogu\> z dzierżawy usługi Azure AD GUID identyfikatora.

    ```
    if (oSession.HostnameIs("login.microsoftonline.com") || oSession.HostnameIs("login.microsoft.com") || oSession.HostnameIs("login.windows.net")){      oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";      oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";}
    ```

    Jeśli potrzebujesz umożliwić wielu dzierżaw, użyj przecinka, aby rozdzielić nazwy dzierżawy. Na przykład:

    ```
    oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";
    ```

4. Zapisz i zamknij plik element CustomRules.

Po skonfigurowaniu programu Fiddler, można przechwytywać ruch, przechodząc do **pliku** menu i wybierając polecenie **Przechwytywanie ruchu**.

### <a name="staged-rollout-of-proxy-settings"></a>Etapowe Wdrażanie ustawień serwera proxy

W zależności od możliwości infrastruktury serwera proxy może mieć możliwość etap w miarę wprowadzania aktualizacji ustawień użytkowników. Poniżej przedstawiono kilka opcji wysokiego poziomu wziąć pod uwagę:

1.  Użyj plików PAC wskaż użytkowników testowych infrastruktury usługi Serwer proxy testu, podczas gdy normalni użytkownicy w dalszym ciągu używać infrastruktury serwera proxy w środowisku produkcyjnym.
2.  Niektóre serwery proxy mogą obsługiwać różne konfiguracje przy użyciu grup.

Zapoznaj się z dokumentacją serwera proxy, aby uzyskać szczegółowe.

## <a name="next-steps"></a>Kolejne kroki

- Przeczytaj o [aktualizacji usługi Office 365 nowoczesnego uwierzytelniania](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/)

- Przegląd [URL usługi Office 365 i zakresy adresów IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)

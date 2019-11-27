---
title: Używanie ograniczeń dzierżawy do zarządzania dostępem do aplikacji SaaS — Azure AD
description: Jak używać ograniczeń dzierżawy do zarządzania użytkownikami, którzy mogą uzyskiwać dostęp do aplikacji na podstawie ich dzierżawy usługi Azure AD.
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
ms.date: 03/28/2019
ms.author: mimart
ms.reviewer: richagi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64f73dd8dbef3f08cd4ea5841e4ec21bac2f55bf
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276501"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Używanie ograniczeń dzierżawy do zarządzania dostępem do aplikacji w chmurze SaaS

Duże organizacje, które zwracają uwagę zabezpieczenia chce się przejść do usług, takich jak Office 365 w chmurze, ale musisz wiedzieć, że użytkownikom tylko dostęp do zasobów zatwierdzone. Tradycyjnie firmy ograniczają nazwy domen i adresów IP podczas zarządzania dostępem. Takie podejście kończy się niepowodzeniem na świecie, w którym aplikacje SaaS (Software as a Service) są hostowane w chmurze publicznej, działające na nazwach udostępnionych domen, takich jak [Outlook.Office.com](https://outlook.office.com/) i [login.microsoftonline.com](https://login.microsoftonline.com/). Blokowanie tych adresów zachowa użytkownikom dostęp do programu Outlook w sieci web w całości, zamiast jedynie ograniczenie je do tożsamości zatwierdzone i zasobów.

Rozwiązanie Azure Active Directory (Azure AD) do tego wezwania jest funkcją z ograniczeniami dzierżawy. Ograniczenia dotyczące dzierżawców pozwalają organizacjom kontrolować dostęp do aplikacji w chmurze SaaS w oparciu o dzierżawę usługi Azure AD, której aplikacje używają do logowania jednokrotnego. Na przykład można zezwolić na dostęp do aplikacji usługi Office 365 w Twojej organizacji, zezwalając jednocześnie dostęp do innych organizacji wystąpień tych tych samych aplikacji.  

W przypadku ograniczeń dzierżawy organizacje mogą określić listę dzierżawców, do których użytkownicy mają prawo dostępu. Następnie usługa Azure AD tylko udziela dostępu do tych dozwolonych dzierżaw.

Ten artykuł koncentruje się na ograniczeniach dzierżawy dla pakietu Office 365, ale ta funkcja powinna współpracować z dowolną aplikacją SaaS w chmurze, która korzysta z nowoczesnego protokołu uwierzytelniania z usługą Azure AD w celu logowania jednokrotnego. Jeśli używasz modelu SaaS aplikacji z różnymi usługami Azure AD dzierżawy w dzierżawie, używane przez usługi Office 365, upewnij się, czy wszystkie wymagane dzierżawców są dozwolone. Aby uzyskać więcej informacji na temat aplikacji w chmurze SaaS, zobacz Portal [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).

## <a name="how-it-works"></a>Jak to działa

Ogólne rozwiązanie obejmuje następujące składniki:

1. **Azure AD**: jeśli istnieje `Restrict-Access-To-Tenants: <permitted tenant list>`, usługa Azure AD wystawia tokeny zabezpieczające tylko dla dozwolonych dzierżawców.

2. **Infrastruktura lokalnego serwera proxy**: ta infrastruktura jest urządzeniem serwera proxy z możliwością inspekcji SSL (SSL). Musisz skonfigurować serwer proxy, aby wstawić nagłówek zawierający listę dozwolonych dzierżawców do ruchu przeznaczonego dla usługi Azure AD.

3. **Oprogramowanie klienckie**: aby obsługiwać ograniczenia dzierżawy, oprogramowanie klienckie musi żądać tokenów bezpośrednio z usługi Azure AD, dzięki czemu infrastruktura serwera proxy może przechwytywać ruch. Aplikacje pakietu Office 365 oparte na przeglądarce obsługują obecnie ograniczenia dzierżawy, ponieważ klienci pakietu Office korzystający z nowoczesnego uwierzytelniania (na przykład OAuth 2,0).

4. **Nowoczesne uwierzytelnianie**: usługi w chmurze muszą używać nowoczesnego uwierzytelniania, aby używać ograniczeń dzierżawy i blokować dostęp do wszystkich niedozwolonych dzierżawców. Aby domyślnie używać nowoczesnych protokołów uwierzytelniania, należy skonfigurować usługi w chmurze pakietu Office 365. Aby uzyskać najnowsze informacje na temat obsługi nowoczesnego uwierzytelniania w pakiecie Office 365, Przeczytaj [zaktualizowany nowoczesne uwierzytelnianie pakietu Office 365](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

Na poniższym diagramie przedstawiono przepływ ruchu wysokiego poziomu. Ograniczenia dzierżawców wymagają inspekcji SSL tylko dla ruchu do usługi Azure AD, a nie do usług w chmurze pakietu Office 365. Ta różnica jest ważna, ponieważ ilość ruchu sieciowego do uwierzytelniania w usłudze Azure AD jest zwykle znacznie mniejsza niż ilość ruchu sieciowego do SaaS aplikacji, takich jak Exchange Online i SharePoint Online.

![Przepływ ruchu związany z ograniczeniami dzierżawy — diagram](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Konfigurowanie ograniczeń dzierżawy

Istnieją dwa kroki, które należy wykonać, aby rozpocząć pracę z ograniczeniami dzierżawy. Najpierw upewnij się, że klienci mogą łączyć się z prawidłowymi adresami. Następnie skonfiguruj infrastrukturę serwera proxy.

### <a name="urls-and-ip-addresses"></a>Adresy URL i adresów IP

Aby można było używać ograniczeń dzierżawy, klienci muszą mieć możliwość nawiązywania połączeń z następującymi adresami URL usługi Azure AD w celu uwierzytelniania: [login.microsoftonline.com](https://login.microsoftonline.com/), [login.Microsoft.com](https://login.microsoft.com/)i [login.Windows.NET](https://login.windows.net/). Ponadto w celu uzyskania dostępu do pakietu Office 365 klienci muszą również mieć możliwość nawiązywania połączeń z w pełni kwalifikowanymi nazwami domen (FQDN), adresami URL i adresami IP zdefiniowanymi w adresach [URL i zakresach adresów IP pakietu office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2). 

### <a name="proxy-configuration-and-requirements"></a>Wymagania i konfiguracji serwera proxy

Aby włączyć ograniczenia dzierżawy za pomocą infrastruktury serwera proxy, wymagana jest następująca konfiguracja. Ten przewodnik jest ogólny, więc powinni zapoznać się z dokumentacją dostawcy serwera proxy dla konkretnej implementacji kroków.

#### <a name="prerequisites"></a>Wymagania wstępne

- Serwer proxy musi mieć możliwość wykonania przejęciu protokołu SSL, wstawiania nagłówka HTTP i filtrowanie miejsc docelowych przy użyciu nazw FQDN/adresów URL.

- Klienci muszą ufać łańcucha certyfikatów przedstawiony przez serwer proxy dla komunikacji SSL. Na przykład jeśli używane są certyfikaty z wewnętrznej [infrastruktury kluczy publicznych (PKI)](/windows/desktop/seccertenroll/public-key-infrastructure) , certyfikat wewnętrznego wystawiania certyfikatu głównego urzędu certyfikacji musi być zaufany.

- Ta funkcja jest uwzględniona w subskrypcjach pakietu Office 365, ale jeśli chcesz użyć ograniczeń dzierżawy do kontrolowania dostępu do innych aplikacji SaaS, wówczas wymagane są Azure AD — wersja Premium 1 licencji.

#### <a name="configuration"></a>Konfigurowanie

Dla każdego żądania przychodzącego do login.microsoftonline.com, login.microsoft.com i login.windows.net, Wstaw dwa nagłówki HTTP: *ograniczanie dostępu do dzierżawców* i *ograniczanie dostępu do kontekstu*.

Nagłówki powinien zawierać następujące elementy:

- W przypadku *ograniczania dostępu do dzierżawców*użyj wartości \<dozwolonej listy dzierżawców\>, która jest rozdzielaną przecinkami listą dzierżawców, dla których chcesz zezwolić użytkownikom na dostęp. Dowolnej domeny, która jest zarejestrowana w dzierżawie może służyć do identyfikowania dzierżawy na tej liście. Na przykład, aby zezwolić na dostęp zarówno do dzierżaw contoso, jak i Fabrikam, para nazwa/wartość wygląda następująco: `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com`

- Aby *ograniczyć dostęp do kontekstu*, użyj wartości identyfikatora pojedynczego katalogu, deklarując, który dzierżawca ustawia ograniczenia dzierżawy. Na przykład, aby zadeklarować contoso jako dzierżawcę, który ustawił zasady ograniczeń dzierżawy, para nazwa/wartość wygląda następująco: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> Identyfikator katalogu można znaleźć w [portalu Azure Active Directory](https://aad.portal.azure.com/). Zaloguj się jako administrator, wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **Właściwości**.

Aby uniemożliwić użytkownikom wstawianie własnego nagłówka HTTP z niezatwierdzonymi dzierżawcami, serwer proxy musi zastąpić nagłówek *ograniczenia dostępu do dzierżawców* , jeśli jest już obecny w żądaniu przychodzącym.

Klienci muszą zmuszeni do używania serwera proxy dla wszystkich żądań login.microsoftonline.com login.microsoft.com i login.windows.net. Na przykład jeśli pliki PAC są używane do kierowania klientom do korzystania z serwera proxy, użytkownicy końcowi nie będą mogli edytować ani wyłączać plików PAC.

## <a name="the-user-experience"></a>Środowisko użytkownika

Ta sekcja zawiera opis środowiska zarówno dla użytkowników końcowych, jak i administratorów.

### <a name="end-user-experience"></a>Środowisko użytkownika końcowego

Przykładowy użytkownik znajduje się w sieci firmy Contoso, ale próbuje uzyskać dostęp Fabrikam wystąpienie udostępnione aplikacji SaaS takich jak program Outlook online. Jeśli firma Fabrikam jest niedozwolonym dzierżawcą dla wystąpienia contoso, użytkownik zobaczy komunikat odmowy dostępu, który informuje, że próbujesz uzyskać dostęp do zasobu, który należy do organizacji niezatwierdzonej przez dział IT.

### <a name="admin-experience"></a>Środowisko pracy administratora

Podczas konfigurowania ograniczeń dzierżawy w firmowej infrastrukturze serwerów proxy Administratorzy mogą uzyskać bezpośredni dostęp do raportów ograniczeń dzierżawy w Azure Portal. Aby wyświetlić raporty:

1. Zaloguj się do [portalu Azure Active Directory](https://aad.portal.azure.com/). Zostanie wyświetlony pulpit nawigacyjny **Centrum administracyjnego Azure Active Directory** .

2. W lewym okienku wybierz pozycję **Azure Active Directory**. Zostanie wyświetlona strona przegląd Azure Active Directory.

3. W polu **inne możliwości** wybierz pozycję **ograniczenia dzierżawy**.

Administrator dzierżawy określony jako dzierżawca kontekstu ograniczonego dostępu może użyć tego raportu, aby zobaczyć, że logowanie zostało zablokowane z powodu zasad ograniczeń dzierżawy, w tym użytych tożsamości i identyfikatora katalogu docelowego. Logowania są uwzględnione, jeśli użytkownik dzierżawy lub zasobów dzierżawy dla logowania jest ustawienie ograniczenia dzierżawy.

Podobnie jak inne raporty w witrynie Azure portal można użyć filtrów, aby określić zakres raportu. Można filtrować według określonego przedziału czasu, użytkownika, aplikacji, klienta lub stanu. Jeśli wybierzesz przycisk **kolumny** , możesz wybrać wyświetlanie danych z dowolną kombinacją następujących pól:

- **Użytkownicy**
- **Aplikacja**
- **Stan**
- **Dniu**
- **Data (UTC)** (gdzie UTC jest uniwersalnym czasem koordynowanym)
- **Metoda auth uwierzytelniania MFA (metoda uwierzytelniania wieloskładnikowego** )
- **Szczegóły uwierzytelniania MFA (szczegóły uwierzytelniania wieloskładnikowego** )
- **Wynik usługi MFA**
- **Adres IP**
- **Klient**
- **Nazwa użytkownika**
- **Lokalizacja**
- **Identyfikator dzierżawy docelowej**

## <a name="office-365-support"></a>Obsługa usługi Office 365

Aplikacje pakietu Office 365 muszą spełniać dwa kryteria, aby w pełni obsługiwać ograniczenia dzierżawy:

1. Używany klient obsługuje nowoczesne uwierzytelnianie.
2. Nowoczesne uwierzytelnianie jest włączony w domyślnym protokołem uwierzytelniania dla usługi w chmurze.

Zapoznaj się z [zaktualizowanym nowoczesnego uwierzytelniania pakietu office 365](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) , aby uzyskać najnowsze informacje na temat tego, którzy klienci pakietu Office obecnie obsługują nowoczesne uwierzytelnianie. Ta strona zawiera także łącza do instrukcje dotyczące włączania nowoczesnego uwierzytelniania na określonych usług Exchange Online i Skype dla firm Online dzierżaw. Usługi SharePoint online już domyślnie umożliwiają nowoczesne uwierzytelnianie.

Aplikacje oparte na przeglądarce pakietu Office 365 (Portal Office, Yammer, witryny programu SharePoint, program Outlook w sieci Web i inne) obsługują obecnie ograniczenia dzierżawy. Klienci o szerokim zakresie (Outlook, Skype dla firm, Word, Excel, PowerPoint i innych) mogą wymuszać ograniczenia dzierżawy tylko w przypadku korzystania z nowoczesnego uwierzytelniania.  

Klienci programu Outlook i Skype dla firm obsługujący nowoczesne uwierzytelnianie mogą nadal używać starszych protokołów wobec dzierżawców, w których nowoczesne uwierzytelnianie nie jest włączone, co skutecznie pomija ograniczenia dzierżawy. Ograniczenia dzierżawy mogą blokować aplikacje korzystające ze starszych protokołów, jeśli kontaktuje się z login.microsoftonline.com, login.microsoft.com lub login.windows.net podczas uwierzytelniania.

Dla programu Outlook, Windows klientów może zadecydować o stosowaniu ograniczenia uniemożliwia użytkownikom dodawanie kont pocztowych niezatwierdzonych do ich profile. Na przykład zapoznaj się z ustawieniem [zapobiegaj dodawaniu zasad grupy innych niż domyślne](https://gpsearch.azurewebsites.net/default.aspx?ref=1) .

## <a name="testing"></a>Testowanie

Jeśli chcesz wypróbować ograniczenia dzierżawy przed wdrożeniem jej w całej organizacji, masz dwie opcje: oparte na hoście rozwiązanie przy użyciu narzędzia, takiego jak programu Fiddler, lub etapowego wdrażania ustawień serwera proxy.

### <a name="fiddler-for-a-host-based-approach"></a>Narzędzie fiddler podejścia opartego na hoście

Jest to bezpłatny internetowy serwer proxy, który może służyć do przechwytywania i zmodyfikować ruch HTTP/HTTPS, w tym Wstawianie nagłówków HTTP debugowania. Aby skonfigurować programu Fiddler do testowania ograniczeń dzierżawy, wykonaj następujące czynności:

1. [Pobierz i zainstaluj programu Fiddler](https://www.telerik.com/fiddler).

2. Skonfiguruj programu Fiddler do odszyfrowywania ruchu HTTPS na [programu Fiddler dokumentacji pomocy](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

3. Skonfiguruj programu Fiddler, aby wstawić nagłówki *ograniczania dostępu do dzierżawców* i *ograniczenia dostępu do kontekstu* przy użyciu reguł niestandardowych:

   1. W narzędziu Web Debugger programu Fiddler wybierz menu **reguły** i wybierz polecenie **Dostosuj reguły...** Aby otworzyć plik element CustomRules.

   2. Dodaj następujące wiersze na początku funkcji `OnBeforeRequest`. Zastąp \<domenę dzierżawcy\> domeną zarejestrowaną w dzierżawie (na przykład `contoso.onmicrosoft.com`). Zastąp \<identyfikator katalogu\> identyfikatorem GUID usługi Azure AD Twojej dzierżawy.

      ```JScript.NET
      if (
          oSession.HostnameIs("login.microsoftonline.com") ||
          oSession.HostnameIs("login.microsoft.com") ||
          oSession.HostnameIs("login.windows.net")
      )
      {
          oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";
          oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";
      }
      ```

      Jeśli potrzebujesz umożliwić wielu dzierżaw, użyj przecinka, aby rozdzielić nazwy dzierżawy. Na przykład:

      `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. Zapisz i zamknij plik element CustomRules.

Po skonfigurowaniu programu Fiddler można przechwytywać ruch, przechodząc do menu **plik** i wybierając pozycję **Przechwyć ruch**.

### <a name="staged-rollout-of-proxy-settings"></a>Etapowe Wdrażanie ustawień serwera proxy

W zależności od możliwości infrastruktury serwera proxy może mieć możliwość etap w miarę wprowadzania aktualizacji ustawień użytkowników. Poniżej przedstawiono kilka opcji wysokiego poziomu wziąć pod uwagę:

1. Użyj plików PAC wskaż użytkowników testowych infrastruktury usługi Serwer proxy testu, podczas gdy normalni użytkownicy w dalszym ciągu używać infrastruktury serwera proxy w środowisku produkcyjnym.
2. Niektóre serwery proxy mogą obsługiwać różne konfiguracje przy użyciu grup.

Aby uzyskać szczegółowe informacje, zapoznaj się z dokumentacją serwera proxy.

## <a name="next-steps"></a>Następne kroki

- Przeczytaj o [zaktualizowanym nowoczesnej uwierzytelnianiu pakietu Office 365](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)
- Przejrzyj [adresy URL i zakresy adresów IP pakietu Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)

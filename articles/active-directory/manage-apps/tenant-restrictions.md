---
title: Zarządzanie dostępem do aplikacji SaaS — Azure AD za pomocą ograniczeń dzierżawy
description: Jak używać ograniczeń dzierżawy do zarządzania, którzy użytkownicy mogą uzyskiwać dostęp do aplikacji na podstawie ich dzierżawy usługi Azure AD.
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
ms.openlocfilehash: ecd49b340810f92727f0fc98f84031c8cbf68179
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481181"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Zarządzanie dostępem do aplikacji w chmurze SaaS za pomocą ograniczeń dzierżawy

Duże organizacje, które podkreślają bezpieczeństwo, chcą przejść do usług w chmurze, takich jak Office 365, ale muszą wiedzieć, że ich użytkownicy mogą uzyskiwać dostęp tylko do zatwierdzonych zasobów. Tradycyjnie firmy ograniczają nazwy domen lub adresy IP, gdy chcą zarządzać dostępem. Takie podejście nie powiedzie się w świecie, w którym aplikacje oprogramowania jako usługi (lub SaaS) są hostowane w chmurze publicznej, działając na udostępnionych nazwach domen, takich jak [outlook.office.com](https://outlook.office.com/) i [login.microsoftonline.com](https://login.microsoftonline.com/). Zablokowanie tych adresów uniemożliwiłoby użytkownikom całkowity dostęp do programu Outlook w sieci Web, a nie ograniczałoby ich jedynie do zatwierdzonych tożsamości i zasobów.

Rozwiązanie usługi Azure Active Directory (Azure AD) do tego wyzwania jest funkcją o nazwie ograniczenia dzierżawy. Dzięki ograniczeniom dzierżawy organizacje mogą kontrolować dostęp do aplikacji w chmurze SaaS, na podstawie dzierżawy usługi Azure AD, której aplikacje używają do logowania jednokrotnego. Na przykład można zezwolić na dostęp do aplikacji usługi Office 365 w organizacji, jednocześnie uniemożliwiając dostęp do wystąpień innych organizacji tych samych aplikacji.  

W ograniczeniach dzierżawy organizacje mogą określić listę dzierżaw, do których użytkownicy mogą uzyskiwać dostęp. Usługa Azure AD udziela tylko dostępu do tych dozwolonych dzierżaw.

W tym artykule skupiono się na ograniczeniach dzierżawy usługi Office 365, ale funkcja powinna działać z dowolną aplikacją w chmurze SaaS, która używa nowoczesnych protokołów uwierzytelniania z usługą Azure AD do logowania jednokrotnego. Jeśli używasz aplikacji SaaS z inną dzierżawą usługi Azure AD z dzierżawy używanej przez usługę Office 365, upewnij się, że wszystkie wymagane dzierżawy są dozwolone. Aby uzyskać więcej informacji o aplikacjach saaS w chmurze, zobacz [Rynek usługi Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).

## <a name="how-it-works"></a>Jak to działa

Ogólne rozwiązanie składa się z następujących elementów:

1. **Usługa Azure AD:** Jeśli `Restrict-Access-To-Tenants: <permitted tenant list>` usługa Azure AD jest obecna, usługa Azure AD wystawia tylko tokeny zabezpieczające dla dozwolonych dzierżaw.

2. **Lokalna infrastruktura serwera proxy:** Ta infrastruktura jest urządzeniem proxy umożliwiającym inspekcję usługi Transport Layer Security (TLS). Należy skonfigurować serwer proxy, aby wstawić nagłówek zawierający listę dozwolonych dzierżaw do ruchu przeznaczonego dla usługi Azure AD.

3. **Oprogramowanie klienckie:** Aby obsługiwać ograniczenia dzierżawy, oprogramowanie klienckie musi żądać tokenów bezpośrednio z usługi Azure AD, aby infrastruktura serwera proxy mogła przechwytywać ruch. Aplikacje usługi Office 365 oparte na przeglądarce obsługują obecnie ograniczenia dzierżawy, podobnie jak klienci pakietu Office korzystający z nowoczesnego uwierzytelniania (np.

4. **Nowoczesne uwierzytelnianie:** usługi w chmurze muszą używać nowoczesnego uwierzytelniania, aby używać ograniczeń dzierżawy i blokować dostęp do wszystkich niedozwolonych dzierżaw. Należy skonfigurować usługi w chmurze usługi Office 365, aby domyślnie korzystały z nowoczesnych protokołów uwierzytelniania. Aby uzyskać najnowsze informacje na temat obsługi usługi Office 365 dla nowoczesnego uwierzytelniania, przeczytaj [zaktualizowane nowoczesne uwierzytelnianie usługi Office 365](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

Na poniższym diagramie przedstawiono przepływ ruchu wysokiego poziomu. Ograniczenia dzierżawy wymagają inspekcji protokołu TLS tylko w ruchu do usługi Azure AD, a nie do usług w chmurze usługi Office 365. To rozróżnienie jest ważne, ponieważ wolumin ruchu do uwierzytelniania w usłudze Azure AD jest zazwyczaj znacznie niższy niż wolumin ruchu do aplikacji SaaS, takich jak Exchange Online i SharePoint Online.

![Ograniczenia dzierżawy przepływ ruchu - diagram](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Konfigurowanie ograniczeń dzierżawy

Istnieją dwa kroki, aby rozpocząć z ograniczeniami dzierżawy. Najpierw upewnij się, że klienci mogą łączyć się z właściwymi adresami. Po drugie, skonfiguruj infrastrukturę serwera proxy.

### <a name="urls-and-ip-addresses"></a>Adresy URL i adresy IP

Aby korzystać z ograniczeń dzierżawy, klienci muszą mieć możliwość łączenia się z następującymi adresami URL usługi Azure AD w celu uwierzytelnienia: [login.microsoftonline.com](https://login.microsoftonline.com/), [login.microsoft.com](https://login.microsoft.com/)i [login.windows.net](https://login.windows.net/). Ponadto, aby uzyskać dostęp do usługi Office 365, klienci muszą mieć również możliwość łączenia się z w pełni kwalifikowanymi nazwami domen (FQDN), adresami URL i adresami IP zdefiniowanymi w [adresach URL i zakresach adresów IP usługi Office 365.](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) 

### <a name="proxy-configuration-and-requirements"></a>Konfiguracja serwera proxy i wymagania

Następująca konfiguracja jest wymagana do włączenia ograniczeń dzierżawy za pośrednictwem infrastruktury serwera proxy. Te wskazówki są ogólne, dlatego należy zapoznać się z dokumentacją dostawcy serwera proxy, aby uzyskać szczegółowe kroki implementacji.

#### <a name="prerequisites"></a>Wymagania wstępne

- Serwer proxy musi mieć możliwość przechwytywania protokołu TLS, wstawiania nagłówka HTTP i filtrowania miejsc docelowych przy użyciu sieci FQDN/adresów URL.

- Klienci muszą ufać łańcuchowi certyfikatów przedstawione przez serwer proxy dla komunikacji TLS. Jeśli na przykład używane są certyfikaty z wewnętrznej [infrastruktury kluczy publicznych (PKI),](/windows/desktop/seccertenroll/public-key-infrastructure) należy ufać wewnętrzneemu certyfikatowi głównego urzędu certyfikacji wystawiającego certyfikat.

- Ta funkcja jest uwzględniona w subskrypcjach usługi Office 365, ale jeśli chcesz użyć ograniczeń dzierżawy do kontrolowania dostępu do innych aplikacji SaaS, wymagane są licencje usługi Azure AD Premium 1.

#### <a name="configuration"></a>Konfigurowanie

Dla każdego żądania przychodzącego do login.microsoftonline.com, login.microsoft.com i login.windows.net wstaw dwa nagłówki HTTP: *Ogranicz dostęp do dzierżawców* i *Ogranicz dostęp-Kontekst*.

Nagłówki powinny zawierać następujące elementy:

- W przypadku *opcji Ograniczyć dostęp do dzierżawców*należy\>użyć wartości listy dozwolonych \<dzierżaw dzierżaw , która jest oddzieloną przecinkami listą dzierżaw, do której mają być dostęp użytkownicy. Każda domena, która jest zarejestrowana w dzierżawie może służyć do identyfikowania dzierżawy na tej liście. Na przykład, aby zezwolić na dostęp do dzierżaw contoso i Fabrikam, para nazwa/wartość wygląda następująco: `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com`

- W polu *Ogranicz dostęp do kontekstu*należy użyć wartości identyfikatora pojedynczego katalogu, deklarując, która dzierżawa ustawia ograniczenia dzierżawy. Na przykład, aby zadeklarować Contoso jako dzierżawcę, która ustawia zasady ograniczeń dzierżawy, para nazwa/wartość wygląda następująco: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> Identyfikator katalogu można znaleźć w [portalu usługi Azure Active Directory](https://aad.portal.azure.com/). Zaloguj się jako administrator, wybierz **pozycję Azure Active Directory**, a następnie wybierz pozycję **Właściwości**.

Aby uniemożliwić użytkownikom wstawianie własnego nagłówka HTTP z niezatwierdzonych dzierżaw, serwer proxy musi zastąpić nagłówek *Ogranicz dostęp do dzierżawców,* jeśli jest już obecny w żądaniu przychodzącym.

Klienci muszą być zmuszeni do używania serwera proxy dla wszystkich żądań do login.microsoftonline.com, login.microsoft.com i login.windows.net. Na przykład jeśli pliki PAC są używane do kierowania klientów do korzystania z serwera proxy, użytkownicy końcowi nie powinni być w stanie edytować lub wyłączyć pliki PAC.

## <a name="the-user-experience"></a>Środowisko użytkownika

W tej sekcji opisano środowisko zarówno dla użytkowników końcowych, jak i administratorów.

### <a name="end-user-experience"></a>Środowisko użytkownika końcowego

Przykładowy użytkownik znajduje się w sieci Contoso, ale próbuje uzyskać dostęp do wystąpienia programu Fabrikam udostępnionej aplikacji SaaS, takiej jak Outlook online. Jeśli firma Fabrikam jest niedozwoloną dzierżawą wystąpienia contoso, użytkownik zobaczy komunikat odmowy dostępu, który mówi, że próbujesz uzyskać dostęp do zasobu, który należy do organizacji niezatwierdzonej przez dział IT.

### <a name="admin-experience"></a>Środowisko administratora

Podczas konfigurowania ograniczeń dzierżawy odbywa się w infrastrukturze firmowego serwera proxy, administratorzy mogą uzyskać bezpośredni dostęp do raportów ograniczeń dzierżawy w witrynie Azure portal. Aby wyświetlić raporty:

1. Zaloguj się do [portalu usługi Azure Active Directory](https://aad.portal.azure.com/). Pojawi się pulpit nawigacyjny **centrum administracyjnego usługi Azure Active Directory.**

2. W lewym okienku wybierz pozycję **Azure Active Directory**. Zostanie wyświetlona strona przeglądu usługi Azure Active Directory.

3. W nagłówku **Inne możliwości** wybierz pozycję **Ograniczenia dzierżawy**.

Administrator dzierżawy określony jako dzierżawa kontekstu ograniczonego dostępu może użyć tego raportu, aby zobaczyć logowania zablokowane z powodu zasad ograniczeń dzierżawy, w tym identyfikatora używanej tożsamości i identyfikatora katalogu docelowego. Logowania są uwzględniane, jeśli dzierżawca ustawienie ograniczenia jest dzierżawcą użytkownika lub dzierżawy zasobów dla logowania.

> [!NOTE]
> Raport może zawierać ograniczone informacje, takie jak identyfikator katalogu docelowego, gdy użytkownik, który jest w dzierżawie innych niż ograniczony dostęp kontekstu dzierżawy loguje się. W takim przypadku informacje umożliwiające identyfikację użytkownika, takie jak nazwa i nazwa główna użytkownika, są maskowane w celu ochrony danych użytkownika w innych dzierżawach.

Podobnie jak inne raporty w witrynie Azure portal, można użyć filtrów, aby określić zakres raportu. Można filtrować według określonego przedziału czasu, użytkownika, aplikacji, klienta lub stanu. Po wybraniu przycisku **Kolumny** można wybrać wyświetlanie danych przy dowolnej kombinacji następujących pól:

- **Użytkownik**
- **Aplikacja**
- **Stan**
- **Data**
- **Data (UTC)** (gdzie UTC jest skoordynowanym czasem uniwersalnym)
- **Metoda Auth uwierzytelniania MFA** (metoda uwierzytelniania wieloskładnikowego)
- **Szczegóły Auth MFA** (szczegóły uwierzytelniania wieloskładnikowego)
- **Wynik usługi MFA**
- **Adres IP**
- **Klienta**
- **Nazwę użytkownika**
- **Lokalizacja**
- **Identyfikator dzierżawy docelowej**

## <a name="office-365-support"></a>Pomoc techniczna usługi Office 365

Aplikacje usługi Office 365 muszą spełniać dwa kryteria, aby w pełni obsługiwać ograniczenia dzierżawy:

1. Używany klient obsługuje nowoczesne uwierzytelnianie.
2. Nowoczesne uwierzytelnianie jest włączone jako domyślny protokół uwierzytelniania dla usługi w chmurze.

Najnowsze informacje na temat klientów pakietu Office, którzy obecnie obsługują nowoczesne uwierzytelnianie, można znaleźć w [zaktualizowanym nowoczesnym uwierzytelnianiu usługi Office 365.](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) Ta strona zawiera również łącza do instrukcji włączania nowoczesnego uwierzytelniania w określonych dzierżawach usługi Exchange Online i Skype dla firm Online. Program SharePoint Online domyślnie włącza już nowoczesne uwierzytelnianie.

Aplikacje oparte na przeglądarce usługi Office 365 (portal office, usługa Yammer, witryny programu SharePoint, aplikacja Outlook w sieci Web i inne) obsługują obecnie ograniczenia dotyczące dzierżawy. Grubi klienci (Outlook, Skype dla firm, Word, Excel, PowerPoint i inne) mogą wymuszać ograniczenia dzierżawy tylko podczas korzystania z nowoczesnego uwierzytelniania.  

Klienci programu Outlook i Skype dla firm obsługujący nowoczesne uwierzytelnianie mogą nadal używać starszych protokołów względem dzierżawców, w których nowoczesne uwierzytelnianie nie jest włączone, skutecznie pomijając ograniczenia dzierżawy. Ograniczenia dzierżawy mogą blokować aplikacje korzystające ze starszych protokołów, jeśli kontaktują się login.microsoftonline.com, login.microsoft.com lub login.windows.net podczas uwierzytelniania.

W systemie Outlook w systemie Windows klienci mogą zaimplementować ograniczenia uniemożliwiające użytkownikom końcowym dodawanie niezatwierdzonych kont pocztowych do swoich profili. Na przykład zobacz ustawienie zasad grupy [Zapobiegaj dodawaniu kont innych niż domyślne.](https://gpsearch.azurewebsites.net/default.aspx?ref=1)

## <a name="testing"></a>Testowanie

Jeśli chcesz wypróbować ograniczenia dzierżawy przed wdrożeniem go dla całej organizacji, masz dwie opcje: podejście oparte na hoście przy użyciu narzędzia, takiego jak Fiddler, lub etapowe wdrażanie ustawień serwera proxy.

### <a name="fiddler-for-a-host-based-approach"></a>Skrzypek dla podejścia opartego na hoście

Fiddler to darmowy serwer proxy do debugowania sieci Web, który może być używany do przechwytywania i modyfikowania ruchu HTTP/HTTPS, w tym wstawiania nagłówków HTTP. Aby skonfigurować fiddler do testowania ograniczeń dzierżawy, wykonaj następujące kroki:

1. [Pobierz i zainstaluj Fiddler](https://www.telerik.com/fiddler).

2. Skonfiguruj Fiddlera do odszyfrowania ruchu HTTPS zgodnie z [dokumentacją pomocy Fiddlera.](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)

3. Skonfiguruj fiddler, aby wstawić nagłówki *Ogranicz dostęp do dzierżawy* i *Ogranicz dostęp-Kontekst* przy użyciu reguł niestandardowych:

   1. W narzędziu Debuger sieci Web Fiddler wybierz menu **Reguły** i wybierz polecenie **Dostosuj reguły...** , aby otworzyć plik CustomRules.

   2. Dodaj następujące wiersze na `OnBeforeRequest` początku funkcji. Zastąp \<domenę\> dzierżawy domeną `contoso.onmicrosoft.com`zarejestrowaną w dzierżawie (na przykład). Zamień \<identyfikator\> katalogu identyfikatorem identyfikatora identyfikatora identyfikatora identyfikatora identyfikatora usługi Azure AD.

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

      Jeśli chcesz zezwolić wielu dzierżawców, użyj przecinka, aby oddzielić nazwy dzierżawy. Przykład:

      `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. Zapisz i zamknij plik CustomRules.

Po skonfigurowaniu fiddler, można przechwycić ruch, przechodząc do menu **Plik** i wybierając **przechwytywanie ruchu**.

### <a name="staged-rollout-of-proxy-settings"></a>Stopniowe wdrażanie ustawień serwera proxy

W zależności od możliwości infrastruktury serwera proxy można zorganizować wdrożenie ustawień dla użytkowników. Oto kilka opcji wysokiego poziomu do rozważenia:

1. Użyj plików PAC, aby wskazać użytkownikom testowym infrastrukturę serwera proxy testów, podczas gdy normalni użytkownicy nadal korzystają z infrastruktury serwera proxy produkcji.
2. Niektóre serwery proxy mogą obsługiwać różne konfiguracje przy użyciu grup.

Szczegółowe informacje można znaleźć w dokumentacji serwera proxy.

## <a name="next-steps"></a>Następne kroki

- Przeczytaj o [zaktualizowanym nowoczesnym uwierzytelnianiu usługi Office 365](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)
- Przeglądanie [adresów URL i zakresów adresów IP usługi Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)

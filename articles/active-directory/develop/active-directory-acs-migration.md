---
title: Migrowanie z usługi kontroli dostępu platformy Azure | Dokumentacja firmy Microsoft
description: Opcje przenoszenia aplikacje i usługi z usługi kontroli dostępu platformy Azure
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/14/2017
ms.author: celested
ms.reviewer: dastrock
ms.openlocfilehash: c1c86f21d5a99cf251b0b83f41576c2cdaf96dfb
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="migrate-from-the-azure-access-control-service"></a>Migrowanie z usługi kontroli dostępu platformy Azure

Kontroli dostępu platformy Azure, usługa Azure Active Directory (Azure AD), zostaną wycofane w 7 listopada 2018. Aplikacji i usług, które obecnie używają kontroli dostępu muszą być całkowicie migrowane do mechanizm uwierzytelniania inny wtedy. W tym artykule opisano zalecenia dotyczące bieżącego klientów podczas planowania można zastąpić korzystanie z kontroli dostępu. Kontrola dostępu nie jest obecnie używany, nie musisz podejmować żadnych działań.


## <a name="overview"></a>Przegląd

Kontrola dostępu jest usługa uwierzytelniania w chmurze zapewnia prosty sposób uwierzytelniania i autoryzacji użytkowników do uzyskiwania dostępu do usług i aplikacji sieci web. Dzięki temu wiele funkcji uwierzytelniania i autoryzacji, aby uwzględnić poza swój kod. Kontrola dostępu jest głównie używana przez deweloperów i architektów klientów platformy Microsoft .NET, aplikacji sieci web ASP.NET i usługi sieci web Windows Communication Foundation (WCF).

Przypadki użycia do kontroli dostępu mogą być podzielone na trzy główne kategorie:

- Uwierzytelniania niektórych usług chmurowych firmy Microsoft, w tym usługi Azure Service Bus oraz usługi Dynamics CRM. Aplikacje klienckie uzyskać tokeny z kontroli dostępu do uwierzytelniania tych usługach można wykonywać różne akcje.
- Dodawanie uwierzytelniania do aplikacji sieci web, opakowaniach jednostkowych (np. SharePoint) i niestandardowych. Przy użyciu uwierzytelniania "pasywny" kontroli dostępu, aplikacji sieci web mogą obsługiwać logowania przy użyciu konta Microsoft (dawniej identyfikator Live) i z konta Google, Facebook, Yahoo, usługi Azure AD i usługi Active Directory Federation Services (AD FS).
- Zabezpieczanie usług sieci web niestandardowego z tokenów wystawionych przez kontroli dostępu. Przy użyciu uwierzytelniania "active", usług sieci web można zapewnić, że zezwalają na dostęp tylko do znane aplikacje klienckie, które uwierzytelniane przy użyciu kontroli dostępu.

Użyj każdego z tych przypadkach oraz ich zalecane migracji, które opisano strategie w poniższych sekcjach. 

> [!WARNING]
> W większości przypadków istotne zmiany kodu są wymagane do migracji istniejącej aplikacji i usług do poziomu nowych technologii. Firma Microsoft zaleca, aby natychmiast rozpocząć planowania i wykonywania migracji w celu uniknięcia potencjalnych awarii żadnego przestoju.

Kontrola dostępu obejmuje następujące składniki:

- Bezpieczne usługi tokenu (STS), który odbiera uwierzytelniania żądania i w zamian wystawia tokeny zabezpieczające.
- Klasycznym portalu Azure, gdzie można utworzyć, usunąć i włączanie i wyłączanie kontroli dostępu w przestrzeni nazw.
- Oddzielne kontroli dostępu portalu zarządzania, gdzie dostosowywania i konfigurowania kontroli dostępu w przestrzeni nazw.
- Usługa zarządzania, która umożliwia automatyzację funkcji portali.
- Token przekształcania aparat reguł, które służy do definiowania złożonej logiki do manipulowania format danych wyjściowych tokenów, które wystawia kontroli dostępu.

Aby korzystać z tych składników, należy utworzyć jeden lub kilka przestrzeni nazw kontroli dostępu. A *przestrzeni nazw* jest dedykowane wystąpienie kontroli dostępu, które aplikacje i usługi komunikować się z. Przestrzeń nazw jest odizolowana od innych klientów kontroli dostępu. Inne kontroli dostępu używać własnej przestrzeni nazw. Przestrzeń nazw w kontroli dostępu zawiera dedykowany adres URL, który wygląda następująco:

```HTTP
https://<mynamespace>.accesscontrol.windows.net
```

Cała komunikacja z STS i operacji zarządzania są wykonywane pod tym adresem URL. Różne ścieżki są używane do różnych celów. Aby ustalić, czy aplikacje lub usługi za pomocą kontroli dostępu, monitorować cały ruch do https://<namespace>. accesscontrol.windows.net. Cały ruch do tego adresu URL jest obsługiwany przez kontroli dostępu i musi być wstrzymana. 

Wyjątkiem jest cały ruch do `https://accounts.accesscontrol.windows.net`. Ruch do tego adresu URL jest już obsługiwany przez inną usługę i **nie jest** wpływ amortyzacja kontroli dostępu. 

Aby uzyskać więcej informacji na temat kontroli dostępu, zobacz [2.0 usługi kontroli dostępu (zarchiwizowane)](https://msdn.microsoft.com/library/hh147631.aspx).

## <a name="retirement-schedule"></a>Wycofanie harmonogramu

Począwszy od listopada 2017 wszystkie składniki kontroli dostępu są w pełni obsługiwane i operacyjnej. Jedynym ograniczeniem jest to, że możesz [nie można utworzyć nowych nazw kontroli dostępu za pośrednictwem klasycznego portalu Azure](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/).

Oto harmonogram wycofano składniki kontroli dostępu:

- **2017 listopada**: środowisko administratora usługi Azure AD w klasycznym portalu Azure [jest wycofywany](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/). W tym momencie zarządzania przestrzeni nazw do kontroli dostępu jest dostępna na nowe, dedykowany adres URL: `http://manage.windowsazure.com?restoreClassic=true`. Użyj tego adresu URl, aby wyświetlić istniejące obszary nazw, włączanie i wyłączanie obszary nazw i usunąć przestrzenie nazw, jeśli chcesz.
- **2 kwietnia 2018**: klasyczny portal Azure całkowicie został wycofany, co oznacza zarządzania przestrzeni nazw kontroli dostępu nie jest już dostępny za pomocą dowolnego adresu URL. W tym momencie nie można wyłączyć lub włączyć, usunąć ani wyliczyć obszary nazw kontroli dostępu. Jednak w portalu zarządzania kontroli dostępu będą w pełni funkcjonalny i znajduje się w lokalizacji `https://\<namespace\>.accesscontrol.windows.net`. Wszystkie inne składniki kontroli dostępu w dalszym ciągu działać normalnie.
- **7 listopada 2018**: kontrola dostępu wszystkie składniki są trwale zamknąć. W tym portalu zarządzania kontroli dostępu, Usługa zarządzania STS i aparat reguł przekształcania tokenu. W tym momencie wszelkie żądania wysyłane do kontroli dostępu (znajdujący się w \<przestrzeni nazw\>. accesscontrol.windows.net) się nie powieść. Należy poddano migracji wszystkich istniejących aplikacji i usług do innych technologii również przed upływem wskazanego terminu.


## <a name="migration-strategies"></a>Strategie migracji

W poniższych sekcjach opisano ogólne zalecenia dotyczące migrowania kontroli dostępu do innych technologii firmy Microsoft.

### <a name="clients-of-microsoft-cloud-services"></a>Klienci usług chmurowych firmy Microsoft

Każdej usługi chmury firmy Microsoft, który akceptuje tokeny wystawione przez kontroli dostępu teraz obsługuje co najmniej jeden z alternatywnych formy uwierzytelniania. Mechanizm uwierzytelniania poprawne może być różna dla każdej usługi. Firma Microsoft zaleca, należy się zapoznać się z dokumentacją określonych dla każdej usługi oficjalnego wskazówki. Dla wygody każdego zestawu dokumentacji podano tutaj:

| Usługa | Wskazówki |
| ------- | -------- |
| Azure Service Bus | [Migrowanie do sygnatur dostępu współdzielonego](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-migrate-acs-sas) |
| Azure Service Bus Relay | [Migrowanie do sygnatur dostępu współdzielonego](https://docs.microsoft.com/azure/service-bus-relay/relay-migrate-acs-sas) |
| Azure Managed Cache | [Migracja do usługi Azure Redis Cache](https://docs.microsoft.com/azure/redis-cache/cache-faq#which-azure-cache-offering-is-right-for-me) |
| Azure DataMarket | [Migrowanie do interfejsów API usługi kognitywnych](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| BizTalk Services | [Migrowanie do funkcji Logic Apps w usłudze Azure App Service](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Azure Media Services | [Migrowanie do uwierzytelniania usługi Azure AD](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure Backup | [Uaktualnij agenta usługi Kopia zapasowa Azure](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: http://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->


### <a name="sharepoint-customers"></a>Klienci programu SharePoint

SharePoint 2013 2016 r., oraz klientów usługi SharePoint Online długo użyto ACS na potrzeby uwierzytelniania w chmurze, lokalnego i hybrydowych scenariuszach. Niektóre funkcje programu SharePoint i przypadki użycia dotyczy wycofanie ACS, podczas gdy inne osoby nie będą. Poniższej tabeli przedstawiono wskazówki dotyczące migracji dotyczących funkcji niektóre najpopularniejszych SharePoint tego ACS korzysta:

| Cecha | Wskazówki |
| ------- | -------- |
| Uwierzytelnianie użytkowników z usługi Azure AD | Poprzednio usługi Azure AD nie obsługuje tokenów SAML 1.1 wymaganych przez program SharePoint do uwierzytelniania i ACS została użyta jako formatuje pośrednik wprowadzone tokenu compatibile programu SharePoint z usługą Azure AD. Teraz, można [SharePoint bezpośrednie łączenie się z usługi Azure AD przy użyciu zasady wystawiania tokenu](https://docs.microsoft.com/Office365/Enterprise/using-azure-ad-for-sharepoint-server-authentication). |
| [Uwierzytelniania aplikacji i serwera do uwierzytelniania w programie SharePoint lokalnego](https://technet.microsoft.com/library/jj219571(v=office.16).aspx) | Nie dotyczy wycofanie ACS; Brak zmian konieczne. | 
| [Niski zaufania autoryzacji dla programu SharePoint dodatków (Dostawca hostowany i SharePoint hostowany)](https://docs.microsoft.com/sharepoint/dev/sp-add-ins/three-authorization-systems-for-sharepoint-add-ins) | Nie dotyczy wycofanie ACS; Brak zmian konieczne. |
| [Wyszukiwanie hybrydowe chmury programu SharePoint](https://blogs.msdn.microsoft.com/spses/2015/09/15/cloud-hybrid-search-service-application/) | Nie dotyczy wycofanie ACS; Brak zmian konieczne. |

### <a name="web-applications-that-use-passive-authentication"></a>Aplikacje sieci Web, korzystających z uwierzytelnianiem pasywnym

Dla aplikacji sieci web korzystających z kontroli dostępu w celu uwierzytelniania użytkowników deweloperzy aplikacji sieci web i architektów kontroli dostępu zawiera następujące funkcje i możliwości:

- Ścisła integracja z systemem Windows Identity Foundation (WIF).
- Federacja z Google, Facebook, Yahoo, Azure Active Directory i AD FS kont i firmy Microsoft.
- Obsługa następujące protokoły uwierzytelniania: projekt OAuth 2.0 13, WS-Trust i federacji usług sieci Web (WS-Federation).
- Obsługę następujących formatów tokenu: Token sieci Web JSON (JWT), SAML 1.1 SAML 2.0 i Simple Web Token (SWT).
- Obsługi odnajdowania obszaru macierzystego, zintegrowane WIF, która umożliwia użytkownikom wybierz typ tworzonego konta używanego do logowania. To środowisko jest obsługiwana przez aplikację sieci web i można swobodnie dostosowywać.
- Token przekształcania, który umożliwia zaawansowane dostosowywanie oświadczeń odebranych przez aplikację sieci web z kontroli dostępu w tym:
    - Przekazuj oświadczenia od dostawcy tożsamości.
    - Dodawanie dodatkowych oświadczenia niestandardowe.
    - Logika Jeżeli to proste do wystawiania oświadczeń w niektórych warunkach.

Niestety nie ma jednej usługi, która oferuje wszystkie te funkcje równoważne. Należy ocenić, jakie funkcje kontroli dostępu można potrzebne, a następnie wybierz między [usługi Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/), [usługi Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) (Azure AD B2C) lub innego uwierzytelniania w chmurze Usługa.

#### <a name="migrate-to-azure-active-directory"></a>Migracja do usługi Azure Active Directory

Ścieżka wziąć pod uwagę jest integracja usług i aplikacji bezpośrednio z usługą Azure AD. Usługi Azure AD jest to dostawca tożsamości w chmurze firmy Microsoft służbowego konta. Usługi Azure AD jest dostawca tożsamości usługi Office 365, Azure i o wiele więcej. Udostępnia podobne federacyjnych funkcji uwierzytelniania w celu kontroli dostępu, ale nie obsługuje wszystkich funkcji kontroli dostępu. 

Podstawowy przykład jest federacji z dostawców tożsamości społecznościowych, takich jak Facebook, Google i Yahoo. Jeśli użytkownicy Zaloguj się przy użyciu tych typów poświadczeń, usługi Azure AD nie jest w zakresie. 

Usługi Azure AD nie zawsze obsługuje również dokładnie tych samych protokołów uwierzytelniania jako kontroli dostępu. Na przykład Chociaż usługi Azure AD i kontroli dostępu obsługują OAuth, mogą występować niewielkie różnice między każdym implementacji. Implementacje różnych wymagać zmodyfikowania kodu w ramach migracji.

Usługi Azure AD zapewnia jednak wiele potencjalnych korzyści klientom kontroli dostępu. Go natywnie obsługuje Microsoft służbowe kont hostowanych w chmurze, które są często używane przez klientów kontroli dostępu. 

Dzierżawa usługi Azure AD można również federacyjną do jednego lub więcej wystąpień z lokalnej usługi Active Directory za pomocą usług AD FS. W ten sposób aplikacji mogą uwierzytelniać użytkowników w chmurze i użytkowników, które są hostowane lokalnie. Obsługuje ona również protokołu WS-Federation, co stanowi stosunkowo prosta do integracji z aplikacji sieci web przy użyciu programu WIF.

W poniższej tabeli porównano funkcje kontroli dostępu, które mają zastosowanie do aplikacji sieci web z tych funkcji, które są dostępne w usłudze Azure AD. 

Na wysokim poziomie *usługi Azure Active Directory jest prawdopodobnie najlepszym wyborem do migracji, jeśli pozwolić użytkownikom na podpisywanie w tylko z ich Microsoft służbowego konta*.

| Możliwości | Obsługa kontroli dostępu | Pomoc techniczna platformy Azure AD |
| ---------- | ----------- | ---------------- |
| **Typy kont** | | |
| Microsoft służbowego konta | Obsługiwane | Obsługiwane |
| Konta z systemu Windows Server Active Directory i AD FS |-Obsługiwane za pośrednictwem federacji z dzierżawy usługi Azure AD <br />-Obsługiwane za pośrednictwem bezpośredniego federacyjnego z usługami AD FS | Obsługiwane wyłącznie za pośrednictwem federacji z dzierżawy usługi Azure AD | 
| Konta z innymi systemami zarządzania tożsamościami enterprise |-Możliwe za pośrednictwem federacji z dzierżawy usługi Azure AD <br />-Obsługiwane za pośrednictwem bezpośredniego federacyjnego | Możliwe za pośrednictwem federacji z dzierżawy usługi Azure AD |
| Konta Microsoft do użytku osobistego | Obsługiwane | Obsługiwane za pośrednictwem protokołu OAuth 2.0 usługi Azure AD, ale nie przez inne protokoły | 
| Facebook, Google i Yahoo kont | Obsługiwane | Nieobsługiwane jakiejkolwiek |
| **Protokoły i zgodności zestawu SDK** | | |
| WIF | Obsługiwane | Obsługiwane, ale ograniczone instrukcje są dostępne |
| WS-Federation | Obsługiwane | Obsługiwane |
| Uwierzytelnianie OAuth 2.0 | Obsługa wersji roboczej 13 | Obsługa RFC 6749, większość nowoczesnych specyfikacji |
| WS-Trust | Obsługiwane | Nieobsługiwane |
| **Formaty tokenów** | | |
| JWT | Obsługiwane w wersji Beta | Obsługiwane |
| SAML 1.1 | Obsługiwane | Wersja zapoznawcza |
| SAML 2.0 | Obsługiwane | Obsługiwane |
| SWT | Obsługiwane | Nieobsługiwane |
| **Dostosowania** | | |
| Można dostosować obszaru macierzystego odnajdywania/konto pobranie interfejsu użytkownika | Kod do pobrania, który można włączyć do aplikacji | Nieobsługiwane |
| Przekazywanie niestandardowych certyfikatów podpisywania tokenu | Obsługiwane | Obsługiwane |
| Dostosowywanie oświadczenia w tokenach |-Przekazuj oświadczenia wejściowe od dostawców tożsamości<br />— Uzyskaj token dostępu z dostawcy tożsamości jako oświadczenia<br />-Wystawiać oświadczenia dane wyjściowe na podstawie wartości oświadczenia wejściowe<br />-Wystawiać oświadczenia dane wyjściowe z wartości stałych |-Nie można przekazać do oświadczeń od dostawców tożsamości federacyjnych<br />-Nie można uzyskać dostępu tokenu od dostawcy tożsamości jako oświadczenia<br />-Nie mogą wystawiać oświadczenia dane wyjściowe na podstawie wartości oświadczenia wejściowe<br />-Mogą wystawiać oświadczenia dane wyjściowe z wartości stałych<br />-Mogą wystawiać oświadczenia dane wyjściowe na podstawie właściwości użytkowników synchronizowane z usługą Azure AD |
| **Automatyzacja** | | |
| Automatyzowanie zadań konfiguracji i zarządzania | Obsługiwane za pośrednictwem usługi zarządzania kontroli dostępu | Obsługiwane przez program Microsoft Graph i Azure AD Graph API |

Jeśli zdecydujesz się, że usługi Azure AD jest najlepszym ścieżki migracji dla usług i aplikacji, należy zwrócić uwagę na dwa sposoby integracja aplikacji z usługą Azure AD.

Aby używać protokołu WS-Federation lub WIF do integracji z usługą Azure AD, zalecamy następujące podejście opisanego w [skonfigurować federacyjne logowanie jednokrotne dla aplikacji z systemem innym niż galerii](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery). Artykuł odwołuje się do konfigurowania usługi Azure AD na podstawie SAML logowania jednokrotnego, ale działa również w przypadku konfigurowania protokołu WS-Federation. Po tej metody wymaga licencji usługi Azure AD Premium. Takie podejście charakteryzuje się dwoma zalety:

- Możesz pobrać pełną elastyczność dostosowywania tokenu usługi Azure AD. Można dostosować oświadczenia, które są wydawane przez usługę Azure AD, aby dopasować oświadczenia, które są wydawane przez kontroli dostępu. Dotyczy to zwłaszcza oświadczenia Identyfikatora lub identyfikator nazwy użytkownika. Aby nadal otrzymywać spójne identyfikatory dla użytkowników po zmianie technologii, upewnij się, czy identyfikatory użytkowników wystawiony przez dopasowanie usługi Azure AD tymi wystawionymi przez kontroli dostępu.
- Można skonfigurować certyfikat podpisywania tokenu, która jest specyficzna dla aplikacji i o okresie istnienia, którą kontrolujesz.

<!--

Possible nameIdentifiers from Access Control (via AAD or AD FS):
- AD FS - Whatever AD FS is configured to send (email, UPN, employeeID, what have you)
- Default from AAD using App Registrations, or Custom Apps before ClaimsIssuance policy: subject/persistent ID
- Default from AAD using Custom apps nowadays: UPN
- Kusto can't tell us distribution, it's redacted

-->

> [!NOTE]
> Ta metoda wymaga licencji usługi Azure AD Premium. Jeśli wymagana jest licencja premium konfigurowania jednokrotnego dla aplikacji są klienta kontroli dostępu, skontaktuj się z nami. Chętnie udostępnianie licencji dewelopera, można użyć.

Informacje o innym podejściu jest wykonanie czynności opisanych [ten przykładowy kod](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation), co daje nieco inne instrukcje dotyczące konfigurowania usługi WS-Federation. Ten przykładowy kod nie korzysta z wersji WIF, ale raczej oprogramowania pośredniczącego OWIN 4.5 programu ASP.NET. Jednak instrukcje dotyczące rejestracji aplikacji są prawidłowe dla aplikacji korzystających z programu WIF i nie wymagają licencji usługi Azure AD Premium. 

Jeśli wybierzesz takie podejście, należy zrozumieć [podpisywania przerzucania kluczy w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). Ta metoda używa usługi Azure AD globalnego klucza do wydawania tokenów podpisywania. Domyślnie WIF nie odświeża automatycznie klucze podpisywania. Gdy usługi Azure AD obraca jego globalne klucze podpisywania, implementacji WIF trzeba przygotować aby zatwierdzić zmiany.

Jeśli można zintegrować z usługą Azure AD za pomocą protokoły OpenID Connect i OAuth, zaleca się w ten sposób. Mamy szczegółową dokumentację i wskazówki dotyczące sposobu integracji usługi Azure AD w aplikacji sieci web dostępnych w naszym [przewodnik dewelopera usługi Azure AD](https://aka.ms/aaddev).

<!-- TODO: If customers ask about authZ, let's put a blurb on role claims here -->

#### <a name="migrate-to-azure-active-directory-b2c"></a>Migracja do usługi Azure Active Directory B2C

Ścieżki migracji do rozważenia jest usługi Azure AD B2C. Usługa Azure AD B2C jest usługą uwierzytelniania w chmurze, takich jak kontrola dostępu, dzięki której deweloperzy mogą zewnętrzny ich logika zarządzania uwierzytelnianie i tożsamość usługi w chmurze. Jest usługą płatną (z warstwami bezpłatnymi i premium), która jest przeznaczona dla aplikacji dla użytkowników, które mogą zawierać miliony aktywni użytkownicy.

Podobnie jak kontroli dostępu jest jedną z najbardziej atrakcyjne funkcje usługi Azure AD B2C, że aplikacja obsługuje wiele różnych typów kont. Z usługi Azure AD B2C Aby móc zalogować w użytkowników przy użyciu swojego konta Microsoft lub konta usługi Facebook, Google, LinkedIn, GitHub lub Yahoo i inne. Usługa Azure AD B2C obsługuje również "kontami lokalnymi", lub nazwy użytkownika i hasła, które użytkownicy będą tworzyć specjalnie dla aplikacji. Usługa Azure AD B2C są także zaawansowanych rozszerzeń, których można dostosowywać przepływów logowania. 

Jednak usługi Azure AD B2C nie obsługuje szerokość protokołów uwierzytelniania i tokenu formatuje klienci mogą wymagać kontroli dostępu. Ponadto nie można użyć usługi Azure AD B2C można uzyskać tokeny i zapytania dodatkowe informacje o użytkowniku od dostawcy tożsamości, Microsoft lub w inny sposób.

W poniższej tabeli porównano funkcje kontroli dostępu, które mają zastosowanie do aplikacji sieci web z tymi, które są dostępne w usłudze Azure AD B2C. Na wysokim poziomie *usługi Azure AD B2C jest prawdopodobnie warto wybrać do migracji, czy aplikacja jest ukierunkowane konsumenta, czy obsługuje wiele różnych typów kont.*

| Możliwości | Obsługa kontroli dostępu | Pomoc techniczna platformy Azure AD B2C |
| ---------- | ----------- | ---------------- |
| **Typy kont** | | |
| Microsoft służbowego konta | Obsługiwane | Obsługiwane za pośrednictwem zasad niestandardowych  |
| Konta z systemu Windows Server Active Directory i AD FS | Obsługiwane za pośrednictwem bezpośredniego federacyjnego z usługami AD FS | Obsługiwane za pośrednictwem SAML federacyjnych za pomocą zasad niestandardowych |
| Konta z innymi systemami zarządzania tożsamościami enterprise | Obsługiwane za pośrednictwem bezpośredniego federacyjnego za pomocą protokołu WS-Federation | Obsługiwane za pośrednictwem SAML federacyjnych za pomocą zasad niestandardowych |
| Konta Microsoft do użytku osobistego | Obsługiwane | Obsługiwane | 
| Facebook, Google i Yahoo kont | Obsługiwane | Facebook i Google obsługiwane natywnie, Yahoo obsługiwane za pośrednictwem protokołu OpenID Connect federacyjnego za pomocą zasad niestandardowych |
| **Protokoły i zgodności zestawu SDK** | | |
| Program Windows Identity Foundation (WIF) | Obsługiwane | Nieobsługiwane |
| WS-Federation | Obsługiwane | Nieobsługiwane |
| Uwierzytelnianie OAuth 2.0 | Obsługa wersji roboczej 13 | Obsługa RFC 6749, większość nowoczesnych specyfikacji |
| WS-Trust | Obsługiwane | Nieobsługiwane |
| **Formaty tokenów** | | |
| JWT | Obsługiwane w wersji Beta | Obsługiwane |
| SAML 1.1 | Obsługiwane | Nieobsługiwane |
| SAML 2.0 | Obsługiwane | Nieobsługiwane |
| SWT | Obsługiwane | Nieobsługiwane |
| **Dostosowania** | | |
| Można dostosować obszaru macierzystego odnajdywania/konto pobranie interfejsu użytkownika | Kod do pobrania, który można włączyć do aplikacji | Można swobodnie dostosowywać interfejsu użytkownika za pomocą niestandardowych CSS |
| Przekazywanie niestandardowych certyfikatów podpisywania tokenu | Obsługiwane | Niestandardowe klucze podpisywania, certyfikaty nie obsługiwane za pośrednictwem zasad niestandardowych |
| Dostosowywanie oświadczenia w tokenach |-Przekazuj oświadczenia wejściowe od dostawców tożsamości<br />— Uzyskaj token dostępu z dostawcy tożsamości jako oświadczenia<br />-Wystawiać oświadczenia dane wyjściowe na podstawie wartości oświadczenia wejściowe<br />-Wystawiać oświadczenia dane wyjściowe z wartości stałych |-Mogą przechodzić do oświadczeń od dostawców tożsamości; zasady niestandardowe wymagane przez niektóre oświadczenia<br />-Nie można uzyskać dostępu tokenu od dostawcy tożsamości jako oświadczenia<br />-Mogą wystawiać oświadczenia dane wyjściowe na podstawie wartości oświadczenia wejściowe za pomocą zasad niestandardowych<br />-Mogą wystawiać oświadczenia dane wyjściowe z wartości stałych za pomocą zasad niestandardowych |
| **Automatyzacja** | | |
| Automatyzowanie zadań konfiguracji i zarządzania | Obsługiwane za pośrednictwem usługi zarządzania kontroli dostępu |-Tworzenie użytkowników, którzy mogą za pomocą interfejsu API Azure AD Graph<br />-Nie można utworzyć dzierżawcy usługi B2C, aplikacji lub zasad programowo |

Jeśli zdecydujesz się, że usługi Azure AD B2C jest najlepszym ścieżki migracji dla usług i aplikacji, Rozpocznij z następującymi zasobami:

- [Dokumentacja usługi Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Niestandardowe zasady usługi Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Cennik usługi Azure AD B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/)


#### <a name="migrate-to-ping-identity-or-auth0"></a>Migrowanie tożsamości Ping lub Auth0

W niektórych przypadkach może się okazać, że usługi Azure AD i Azure AD B2C nie są wystarczające, aby zastąpić kontroli dostępu w aplikacji sieci web bez wprowadzania zmian w kodzie głównych. Typowe przykłady mogą być następujące:

- Aplikacje sieci Web na użytek WIF lub WS-Federation logowanie z dostawców tożsamości społecznościowych, takich jak Google lub usługi Facebook.
- Aplikacji sieci Web, które wykonują bezpośredniego Federacji, do dostawcy tożsamości organizacji za pomocą protokołu WS-Federation.
- Aplikacje, które wymagają tokenu dostępu przez dostawcę tożsamości społecznościowych (takich jak Google lub usługi Facebook) jako oświadczenia w tokenach wystawiony przez kontroli dostępu w sieci Web.
- Aplikacje sieci Web z regułami przekształcania tokenów złożonych, które usługi Azure AD lub Azure AD B2C nie można odtworzyć.
- Aplikacji sieci web z wieloma dzierżawcami, które umożliwia centralne zarządzanie Federacji, do wielu dostawców tożsamości różnych usług ACS

W takich przypadkach możesz rozważyć migrowanie aplikacji sieci web do innej usługi uwierzytelniania w chmurze. Firma Microsoft zaleca rozważa następujące opcje. Każdy z poniższych opcji oferują funkcje podobne do kontroli dostępu:



|     |     | 
| --- | --- |
| ![Auth0](./media/active-directory-acs-migration/rsz_auth0.png) | [Auth0](https://auth0.com/acs) to usługa tożsamości w chmurze elastyczne utworzył [migracji ogólne wskazówki dotyczące klientów kontroli dostępu](https://auth0.com/acs)i obsługuje prawie wszystkich funkcji, który wykonuje ACS. |
| ![Ping](./media/active-directory-acs-migration/rsz_ping.png) | [Tożsamość ping](https://www.pingidentity.com) oferuje dwie podobne do usług ACS. Usługi tożsamości chmury, która obsługuje wiele te same funkcje co ACS jest PingOne i PingFederate jest podobny produkt tożsamości lokalnych, który zapewnia większą elastyczność. Zapoznaj się [wskazówki wycofanie usług ACS na polecenie Ping](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html) Aby uzyskać więcej informacji na temat używania tych produktów. |

Praca z tożsamości Ping i Auth0 naszym celem jest zapewnienie wszystkich klientów kontroli dostępu ścieżki migracji dla usług i aplikacji, która zminimalizować ilość pracy wymaganej w celu przeniesienia z kontroli dostępu.

<!--

## Sharepoint 2010, 2013, 2016

TODO: Azure AD only, use Azure AD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-that-use-active-authentication"></a>Usługi sieci Web, które używają uwierzytelniania w usłudze active

Dla usług sieci web, które są zabezpieczone przy użyciu tokenów wystawionych przez kontroli dostępu kontroli dostępu oferuje następujące funkcje i możliwości:

- Możliwość zarejestrowania co najmniej jeden *tożsamości usługi* w przestrzeni nazw kontroli dostępu. Tożsamości usług mogą być używane do żądania tokenów.
- Obsługa protokołów służący do żądania tokenów, korzystając z następujących typów poświadczeń ZAWIJAĆ OAuth i OAuth 2.0 projekt 13:
    - Proste hasło, który jest tworzony dla tożsamości usługi
    - A podpisane SWT za pomocą klucza symetrycznego lub X509 certyfikatu
    - Token SAML przez dostawcę tożsamości zaufanych (zazwyczaj wystąpienia usług AD FS)
- Obsługę następujących formatów tokenu: token JWT, SAML 1.1 SAML 2.0 i SWT.
- Reguły przekształcania tokenu proste.

Usługa tożsamości w kontroli dostępu są zwykle używane do uwierzytelniania serwera do zaimplementowania. 

#### <a name="migrate-to-azure-active-directory"></a>Migracja do usługi Azure Active Directory

Nasze zalecenia dla tego typu przepływ uwierzytelniania jest przeprowadzenie migracji do [usługi Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/). Usługi Azure AD jest to dostawca tożsamości w chmurze firmy Microsoft służbowego konta. Usługi Azure AD jest dostawca tożsamości usługi Office 365, Azure i o wiele więcej. 

Umożliwia także usługi Azure AD do uwierzytelniania serwera do serwera przy użyciu wdrożenia usługi Azure AD udzielania poświadczeń klienta OAuth. W poniższej tabeli porównano funkcje kontroli dostępu w serwera do uwierzytelniania za pomocą tych, które są dostępne w usłudze Azure AD.

| Możliwości | Obsługa kontroli dostępu | Pomoc techniczna platformy Azure AD |
| ---------- | ----------- | ---------------- |
| Jak zarejestrować usługi sieci web | Utwórz jednostki uzależnionej w portalu zarządzania kontroli dostępu | Tworzenie aplikacji sieci web usługi Azure AD w portalu Azure |
| Jak zarejestrować klienta | Tworzenie tożsamości usługi w portalu zarządzania kontroli dostępu | Utwórz inną aplikację sieci web usługi Azure AD w portalu Azure |
| Protokół używany |-Protokół OAuth WRAP<br />-Przyznanie poświadczeń klienta OAuth 2.0 projekt 13 | Przyznanie poświadczeń klienta OAuth 2.0 |
| Metody uwierzytelniania klienta |-Proste hasło<br />-Podpisem SWT<br />— Token SAML od dostawcy tożsamości federacyjnych |-Proste hasło<br />-Podpisany token JWT. |
| Formaty tokenów |- JWT<br />- SAML 1.1<br />- SAML 2.0<br />- SWT<br /> | Tylko JWT |
| Token przekształcania |-Dodaj oświadczenia niestandardowe<br />-Logika wystawiania oświadczeń Jeżeli to proste | Dodawanie oświadczenia niestandardowe | 
| Automatyzowanie zadań konfiguracji i zarządzania | Obsługiwane za pośrednictwem usługi zarządzania kontroli dostępu | Obsługiwane przez program Microsoft Graph i Azure AD Graph API |

Wskazówki dotyczące implementującej scenariusze do serwera zobacz następujące zasoby:

- Sekcja Service-to-Service [przewodnik dewelopera usługi Azure AD](https://aka.ms/aaddev)
- [Demon przykładowy kod przy użyciu poświadczeń klienta proste hasło](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [Demon przykładowy kod przy użyciu poświadczeń klienta certyfikatu](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migrowanie tożsamości Ping lub Auth0

W niektórych przypadkach może się okazać, że poświadczenia klienta usługi Azure AD i uwierzytelniania OAuth przyznać implementacja nie są wystarczające, aby zastąpić kontroli dostępu w architektury bez zmiany kodu głównych. Typowe przykłady mogą być następujące:

- Uwierzytelnianie serwera serwera przy użyciu tokenu formatuje innych niż tokenów Jwt.
- Uwierzytelnianie serwera serwera przy użyciu tokenu wejściowych dostarczonych przez dostawcę tożsamości zewnętrznych.
- Uwierzytelnianie serwera serwera z regułami przekształcania tokenu, które nie może odtworzyć usługi Azure AD.

W takich sytuacjach należy rozważyć migrowanie aplikacji sieci web do innej usługi uwierzytelniania w chmurze. Firma Microsoft zaleca rozważa następujące opcje. Każdy z poniższych opcji oferują funkcje podobne do kontroli dostępu:

|     |     | 
| --- | --- |
| ![Auth0](./media/active-directory-acs-migration/rsz_auth0.png) | [Auth0](https://auth0.com/acs) to usługa tożsamości w chmurze elastyczne utworzył [migracji ogólne wskazówki dotyczące klientów kontroli dostępu](https://auth0.com/acs)i obsługuje prawie wszystkich funkcji, który wykonuje ACS. |
| ![Ping](./media/active-directory-acs-migration/rsz_ping.png) | [Tożsamość ping](https://www.pingidentity.com) oferuje dwie podobne do usług ACS. Usługi tożsamości chmury, która obsługuje wiele te same funkcje co ACS jest PingOne i PingFederate jest podobny produkt tożsamości lokalnych, który zapewnia większą elastyczność. Zapoznaj się [wskazówki wycofanie usług ACS na polecenie Ping](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html) Aby uzyskać więcej informacji na temat używania tych produktów. |

Praca z tożsamości Ping i Auth0 naszym celem jest zapewnienie wszystkich klientów kontroli dostępu ścieżki migracji dla usług i aplikacji, która zminimalizować ilość pracy wymaganej w celu przeniesienia z kontroli dostępu.

## <a name="questions-concerns-and-feedback"></a>Pytania, problemy i opinie

Zdajemy wielu klientów kontroli dostępu nie odnajdzie ścieżki migracji wyczyść po przeczytaniu tego artykułu. Może być konieczne pewne pomocy lub wskazówek dotyczących określania właściwego planu. Jeśli chcesz omówienia scenariuszy migracji, a pytania, zostaw komentarz na tej stronie.

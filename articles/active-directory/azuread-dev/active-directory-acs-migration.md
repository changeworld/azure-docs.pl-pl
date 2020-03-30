---
title: Migrowanie z usługi Azure Access Control | Dokumenty firmy Microsoft
description: Dowiedz się więcej o opcjach przenoszenia aplikacji i usług z usługi Azure Access Control Service (ACS).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.custom: aaddev
ms.topic: conceptual
ms.workload: identity
ms.date: 10/03/2018
ms.author: ryanwi
ms.reviewer: jlu, annaba, hirsin
ROBOTS: NOINDEX
ms.openlocfilehash: 3168d36bf4c2d3c696173725f669b12dc168dcc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155005"
---
# <a name="how-to-migrate-from-the-azure-access-control-service"></a>Jak: Migrowanie z usługi Azure Access Control

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Usługa kontroli dostępu platformy Microsoft Azure (ACS), usługa usługi Azure Active Directory (Azure AD), zostanie wycofana 7 listopada 2018 r. Aplikacje i usługi, które obecnie korzystają z kontroli dostępu, muszą zostać w pełni zmigrowane do innego mechanizmu uwierzytelniania do tego czasu. W tym artykule opisano zalecenia dla bieżących klientów, ponieważ planujesz przestarzałe korzystanie z kontroli dostępu. Jeśli obecnie nie korzystasz z kontroli dostępu, nie musisz podejmować żadnych działań.

## <a name="overview"></a>Omówienie

Kontrola dostępu to usługa uwierzytelniania w chmurze, która oferuje łatwy sposób uwierzytelniania i autoryzowania użytkowników w celu uzyskania dostępu do aplikacji i usług sieci web. Umożliwia wiele funkcji uwierzytelniania i autoryzacji, które mają być brane pod uwagę z kodu. Kontrola dostępu jest używana głównie przez deweloperów i architektów klientów platformy Microsoft .NET, ASP.NET aplikacji sieci web i usług sieci Web Programu Windows Communication Foundation (WCF).

Przypadki użycia kontroli dostępu można podzielić na trzy główne kategorie:

- Uwierzytelnianie w niektórych usługach w chmurze firmy Microsoft, w tym usługi Azure Service Bus i Dynamics CRM. Aplikacje klienckie uzyskują tokeny z kontroli dostępu do uwierzytelniania w tych usługach w celu wykonywania różnych akcji.
- Dodawanie uwierzytelniania do aplikacji sieci Web, zarówno niestandardowych, jak i paczkowanych (takich jak SharePoint). Korzystając z "pasywnego" uwierzytelniania kontroli dostępu, aplikacje internetowe mogą obsługiwać logowanie za pomocą konta Microsoft (dawniej Live ID) oraz kont Google, Facebook, Yahoo, Azure AD i Usług Federacyjnych Active Directory (AD FS).
- Zabezpieczanie niestandardowych usług sieci web za pomocą tokenów wystawionych przez kontrolę dostępu. Korzystając z uwierzytelniania "aktywnego", usługi sieci web mogą zapewnić, że zezwalają na dostęp tylko znanym klientom, którzy uwierzytelnili się za pomocą kontroli dostępu.

Każdy z tych przypadków użycia i ich zalecane strategie migracji są omówione w poniższych sekcjach.

> [!WARNING]
> W większości przypadków istotne zmiany kodu są wymagane do migracji istniejących aplikacji i usług do nowszych technologii. Zaleca się natychmiastowe rozpoczęcie planowania i wykonywania migracji, aby uniknąć potencjalnych awarii lub przestojów.

Kontrola dostępu ma następujące składniki:

- Usługa bezpiecznego tokenu (STS), która odbiera żądania uwierzytelniania i wystawia tokeny zabezpieczające w zamian.
- Klasyczny portal platformy Azure, w którym tworzysz, usuwasz i włączasz i wyłączasz obszary nazw kontroli dostępu.
- Osobny portal zarządzania kontroli dostępu, w którym można dostosować i skonfigurować obszary nazw kontroli dostępu.
- Usługa zarządzania, za pomocą której można zautomatyzować funkcje portali.
- Aparat reguł przekształcania tokenu, który służy do definiowania złożonej logiki do manipulowania formatem wyjściowym tokenów, które problemuje z kontrolą dostępu.

Aby użyć tych składników, należy utworzyć co najmniej jedną przestrzeń nazw kontroli dostępu. *Obszar nazw* to dedykowane wystąpienie kontroli dostępu, z którą komunikują się aplikacje i usługi. Obszar nazw jest odizolowany od wszystkich innych klientów kontroli dostępu. Inni klienci kontroli dostępu używają własnych obszarów nazw. Obszar nazw w kontroli dostępu ma dedykowany adres URL, który wygląda następująco:

```HTTP
https://<mynamespace>.accesscontrol.windows.net
```

Cała komunikacja z STS i operacje zarządzania odbywa się pod tym adresem URL. Używasz różnych ścieżek do różnych celów. Aby ustalić, czy aplikacje lub usługi korzystają z kontroli&lt;dostępu,&gt;należy monitorować ruch, aby https:// obszarze nazw .accesscontrol.windows.net. Każdy ruch do tego adresu URL jest obsługiwany przez kontrolę dostępu i musi zostać przerwany. 

Wyjątkiem jest każdy ruch `https://accounts.accesscontrol.windows.net`do . Ruch do tego adresu URL jest już obsługiwany przez inną usługę i **nie ma** na nie wpływu deprecation kontroli dostępu. 

Aby uzyskać więcej informacji na temat kontroli dostępu, zobacz [Usługa kontroli dostępu 2.0 (zarchiwizowana)](https://msdn.microsoft.com/library/hh147631.aspx).

## <a name="find-out-which-of-your-apps-will-be-impacted"></a>Dowiedz się, które z Twoich aplikacji będą miały wpływ

Wykonaj czynności opisane w tej sekcji, aby dowiedzieć się, które z Twoich aplikacji będą miały wpływ na wycofanie usługi ACS.

### <a name="download-and-install-acs-powershell"></a>Pobieranie i instalowanie programu ACS PowerShell

1. Przejdź do Galerii programu PowerShell i pobierz [plik Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
2. Zainstaluj moduł, uruchamiając

    ```powershell
    Install-Module -Name Acs.Namespaces
    ```

3. Pobierz listę wszystkich możliwych poleceń, uruchamiając

    ```powershell
    Get-Command -Module Acs.Namespaces
    ```

    Aby uzyskać pomoc dotyczącą określonego polecenia, uruchom:

    ```
     Get-Help [Command-Name] -Full
    ```
    
    gdzie `[Command-Name]` jest nazwa polecenia ACS.

### <a name="list-your-acs-namespaces"></a>Wyświetlanie listy obszarów nazw acs

1. Połącz się z acs za pomocą polecenia cmdlet **Connect-AcsAccount.**
  
    Może być konieczne `Set-ExecutionPolicy -ExecutionPolicy Bypass` uruchomienie przed wykonaniem poleceń i być administratorem tych subskrypcji w celu wykonania poleceń.

2. Wyświetl listę dostępnych subskrypcji platformy Azure przy użyciu polecenia cmdlet **Get-AcsSubscription.**
3. Lista obszarów nazw usługi ACS przy użyciu polecenia cmdlet **Get-AcsNamespace.**

### <a name="check-which-applications-will-be-impacted"></a>Sprawdź, które aplikacje będą miały wpływ

1. Użyj obszaru nazw z poprzedniego kroku i przejdź do`https://<namespace>.accesscontrol.windows.net`

    Na przykład, jeśli jedna z przestrzeni nazw jest contoso-test, przejdź do`https://contoso-test.accesscontrol.windows.net`

2. W obszarze Relacje zaufania wybierz **pozycję Aplikacje jednostki uzależnione,** aby wyświetlić listę aplikacji, na które będzie miało wpływ wycofanie z usługi ACS. **Trust relationships**
3. Powtórz kroki 1-2 dla innych obszarów nazw ACS, które masz.

## <a name="retirement-schedule"></a>Harmonogram przejścia na emeryturę

Od listopada 2017 r. wszystkie składniki kontroli dostępu są w pełni obsługiwane i działają. Jedynym ograniczeniem jest to, że [nie można tworzyć nowych obszarów nazw kontroli dostępu za pośrednictwem klasycznego portalu platformy Azure.](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)

Oto harmonogram przestarzałego przestarzałego dostępu składników kontroli dostępu:

- **Listopad 2017:** Środowisko administratora usługi Azure AD w klasycznym portalu platformy Azure [jest wycofywane](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/). W tym momencie zarządzanie obszarami nazw dla kontroli dostępu `https://manage.windowsazure.com?restoreClassic=true`jest dostępne pod nowym, dedykowanym adresem URL: . Użyj tego języka umie, aby wyświetlić istniejące przestrzenie nazw, włączyć i wyłączyć obszary nazw oraz usunąć obszary nazw, jeśli wybierzesz.
- **2 kwietnia 2018 r.:** Klasyczny portal platformy Azure jest całkowicie wycofany, co oznacza, że zarządzanie obszarami nazw kontroli dostępu nie jest już dostępne za pośrednictwem dowolnego adresu URL. W tym momencie nie można wyłączyć ani włączyć, usunąć ani wyliczyć obszarów nazw kontroli dostępu. Portal zarządzania kontrola dostępu będzie jednak w `https://\<namespace\>.accesscontrol.windows.net`pełni funkcjonalny i znajduje się pod adresem . Wszystkie inne składniki kontroli dostępu nadal działają normalnie.
- **7 listopada 2018**r.: Wszystkie składniki kontroli dostępu są trwale wyłączone. Obejmuje to portal zarządzania kontroli dostępu, usługę zarządzania, STS i aparat reguł przekształcania tokenu. W tym momencie wszystkie żądania wysyłane \<do\>kontroli dostępu (znajdujące się w obszarze nazw .accesscontrol.windows.net) nie powiodą się. Przed tym czasem należy przeprowadzić migrację wszystkich istniejących aplikacji i usług do innych technologii.

> [!NOTE]
> Zasada wyłącza obszary nazw, które nie zażądały tokenu przez pewien czas. Od początku września 2018 r. okres ten wynosi obecnie 14 dni bezczynności, ale w najbliższych tygodniach zostanie skrócony do 7 dni bezczynności. Jeśli masz obszary nazw kontroli dostępu, które są obecnie wyłączone, możesz [pobrać i zainstalować program ACS PowerShell,](#download-and-install-acs-powershell) aby ponownie włączyć przestrzenie nazw.

## <a name="migration-strategies"></a>Strategie migracji

W poniższych sekcjach opisano zalecenia wysokiego poziomu dotyczące migracji z kontroli dostępu do innych technologii firmy Microsoft.

### <a name="clients-of-microsoft-cloud-services"></a>Klienci usług w chmurze firmy Microsoft

Każda usługa w chmurze firmy Microsoft, która akceptuje tokeny wystawione przez kontrolę dostępu, obsługuje teraz co najmniej jedną alternatywną formę uwierzytelniania. Mechanizm uwierzytelniania poprawne różni się dla każdej usługi. Zalecamy zapoznanie się z dokumentacją dla każdej usługi w celu uzyskania oficjalnych wskazówek. Dla wygody, każdy zestaw dokumentacji znajduje się tutaj:

| Usługa | Wskazówki |
| ------- | -------- |
| Azure Service Bus | [Migracja do podpisów dostępu współdzielonego](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-migrate-acs-sas) |
| Przekaźnik usługi Azure Service Bus | [Migracja do podpisów dostępu współdzielonego](https://docs.microsoft.com/azure/service-bus-relay/relay-migrate-acs-sas) |
| Zarządzana pamięć podręczna platformy Azure | [Migrowanie do pamięci podręcznej platformy Azure dla redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-faq#which-azure-cache-offering-is-right-for-me) |
| Azure DataMarket | [Migrowanie do interfejsów API usług Cognitive Services](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Usługi BizTalk | [Migrowanie do funkcji Aplikacje logiki usługi Azure App Service](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Usługi multimediów platformy Azure | [Migrowanie do uwierzytelniania usługi Azure AD](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure Backup | [Uaktualnianie agenta usługi Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: https://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->

### <a name="sharepoint-customers"></a>Klienci programu SharePoint

Klienci programu SharePoint 2013, 2016 i SharePoint Online od dawna korzystają z usługi ACS do celów uwierzytelniania w chmurze, lokalnie i w scenariuszach hybrydowych. Niektóre funkcje programu SharePoint i przypadki użycia będą miały wpływ na wycofanie usługi ACS, podczas gdy inne nie. Poniższa tabela zawiera podsumowanie wskazówek dotyczących migracji dla niektórych z najpopularniejszych funkcji programu SharePoint, które wykorzystują usługi ACS:

| Funkcja | Wskazówki |
| ------- | -------- |
| Uwierzytelnianie użytkowników z usługi Azure AD | Wcześniej usługa Azure AD nie obsługiwała tokenów SAML 1.1 wymaganych przez program SharePoint do uwierzytelniania, a usługa ACS była używana jako pośrednik, który powodował, że program SharePoint był zgodny z formatami tokenów usługi Azure AD. Teraz możesz [połączyć program SharePoint bezpośrednio z usługą Azure AD przy użyciu usługi Azure AD App Gallery SharePoint w aplikacji lokalnej](https://docs.microsoft.com/azure/active-directory/saas-apps/sharepoint-on-premises-tutorial). |
| [Uwierzytelnianie aplikacji & uwierzytelnianie między serwerami w programie SharePoint lokalnie](https://technet.microsoft.com/library/jj219571(v=office.16).aspx) | Emerytura ACS nie ma na nie wpływu; nie są konieczne żadne zmiany. | 
| [Autoryzacja niskiego zaufania dla dodatków programu SharePoint (dostawca hostowany i hostowany w programie SharePoint)](https://docs.microsoft.com/sharepoint/dev/sp-add-ins/three-authorization-systems-for-sharepoint-add-ins) | Emerytura ACS nie ma na nie wpływu; nie są konieczne żadne zmiany. |
| [Wyszukiwanie hybrydowe w chmurze programu SharePoint](https://blogs.msdn.microsoft.com/spses/2015/09/15/cloud-hybrid-search-service-application/) | Emerytura ACS nie ma na nie wpływu; nie są konieczne żadne zmiany. |

### <a name="web-applications-that-use-passive-authentication"></a>Aplikacje sieci Web korzystające z uwierzytelniania pasywnego

W przypadku aplikacji sieci Web, które używają kontroli dostępu do uwierzytelniania użytkowników, kontrola dostępu zapewnia następujące funkcje i możliwości dla deweloperów i architektów aplikacji sieci Web:

- Głęboka integracja z Windows Identity Foundation (WIF).
- Federacja z kontami Google, Facebook, Yahoo, Azure Active Directory i AD FS oraz kontami Microsoft.
- Obsługa następujących protokołów uwierzytelniania: OAuth 2.0 Wersja robocza 13, WS-Trust i Federacja usług sieci Web (Federacja WS).
- Obsługa następujących formatów tokenów: JSON Web Token (JWT), SAML 1.1, SAML 2.0 i Simple Web Token (SWT).
- Doświadczenie wykrywania obszaru macierzystego, zintegrowane z WIF, które pozwala użytkownikom wybrać typ konta, którego używają do logowania. To środowisko jest hostowane przez aplikację sieci web i jest w pełni konfigurowalne.
- Transformacja tokenu, która umożliwia zaawansowane dostosowanie oświadczeń otrzymanych przez aplikację sieci web z kontroli dostępu, w tym:
    - Przekazywanie oświadczeń od dostawców tożsamości.
    - Dodawanie dodatkowych oświadczeń niestandardowych.
    - Prosta logika if-then do wystawiania oświadczeń pod pewnymi warunkami.

Niestety, nie ma jednej usługi, która oferuje wszystkie te równoważne możliwości. Należy ocenić, jakie możliwości kontroli dostępu są potrzebne, a następnie wybrać między [użyciem usługi Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/), [usługi Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) (Azure AD B2C) lub innej usługi uwierzytelniania w chmurze.

#### <a name="migrate-to-azure-active-directory"></a>Migrowanie do usługi Azure Active Directory

Ścieżka do rozważenia jest integracja aplikacji i usług bezpośrednio z usługą Azure AD. Usługa Azure AD jest dostawcą tożsamości opartych na chmurze dla kont służbowych firmy Microsoft. Usługa Azure AD jest dostawcą tożsamości dla usługi Office 365, platformy Azure i wielu innych. Zapewnia podobne możliwości uwierzytelniania federacyjnego do kontroli dostępu, ale nie obsługuje wszystkich funkcji kontroli dostępu. 

Głównym przykładem jest federacja z dostawcami tożsamości społecznościowych, takimi jak Facebook, Google i Yahoo. Jeśli użytkownicy logują się przy użyciu tych typów poświadczeń, usługa Azure AD nie jest rozwiązaniem dla Ciebie. 

Usługa Azure AD również nie musi obsługiwać dokładnie tych samych protokołów uwierzytelniania, co kontrola dostępu. Na przykład chociaż zarówno kontroli dostępu i usługi Azure AD obsługuje OAuth, istnieją subtelne różnice między każdej implementacji. Różne implementacje wymagają modyfikowania kodu w ramach migracji.

Jednak usługa Azure AD zapewnia kilka potencjalnych korzyści dla klientów kontroli dostępu. Natywnie obsługuje konta służbowe firmy Microsoft hostowane w chmurze, które są powszechnie używane przez klientów kontroli dostępu. 

Dzierżawy usługi Azure AD może być również sfederowany do jednego lub więcej wystąpień lokalnej usługi Active Directory za pośrednictwem usług AD FS. W ten sposób aplikacja może uwierzytelniać użytkowników w chmurze i użytkowników, którzy są hostowani lokalnie. Obsługuje również protokół WS-Federation, co sprawia, że stosunkowo łatwo zintegrować z aplikacją sieci web przy użyciu WIF.

W poniższej tabeli porównano funkcje kontroli dostępu, które są istotne dla aplikacji sieci web z tymi funkcjami, które są dostępne w usłudze Azure AD. 

Na wysokim poziomie *usługa Azure Active Directory jest prawdopodobnie najlepszym wyborem dla twojej migracji, jeśli zezwalasz użytkownikom na logowanie się tylko za pomocą swoich kont służbowych firmy Microsoft.*

| Możliwości | Obsługa kontroli dostępu | Obsługa usługi Azure AD |
| ---------- | ----------- | ---------------- |
| **Typy kont** | | |
| Konta służbowe firmy Microsoft | Obsługiwane | Obsługiwane |
| Konta z usługi Active Directory systemu Windows Server i usług AD FS |- Obsługiwane przez federację z dzierżawą usługi Azure AD <br />- Obsługiwane przez bezpośrednią federację z ad fs | Obsługiwane tylko przez federację z dzierżawą usługi Azure AD | 
| Konta z innych systemów zarządzania tożsamościami przedsiębiorstwa |- Możliwe za pośrednictwem federacji z dzierżawy usługi Azure AD <br />- Obsługiwane przez bezpośrednią federację | Możliwe za pośrednictwem federacji z dzierżawą usługi Azure AD |
| Konta Microsoft do użytku osobistego | Obsługiwane | Obsługiwane za pośrednictwem protokołu OAuth usługi Azure AD AD 2.0, ale nie w innych protokołach | 
| Konta Facebook, Google, Yahoo | Obsługiwane | Nie jest w ogóle obsługiwana |
| **Protokoły i zgodność z SDK** | | |
| WIF | Obsługiwane | Obsługiwane, ale dostępne są ograniczone instrukcje |
| WS-Federation | Obsługiwane | Obsługiwane |
| OAuth 2.0 | Wsparcie dla projektu 13 | Obsługa RFC 6749, najnowocześniejszej specyfikacji |
| WS-Trust | Obsługiwane | Nieobsługiwane |
| **Formaty tokenów** | | |
| Jwt | Obsługiwane w wersji beta | Obsługiwane |
| SAML 1.1 | Obsługiwane | Wersja zapoznawcza |
| SAML 2.0 | Obsługiwane | Obsługiwane |
| Swt | Obsługiwane | Nieobsługiwane |
| **Dostosowania** | | |
| Konfigurowalny interfejs użytkownika wykrywania/wybierania konta w obszarze do domu | Kod do pobrania, który można włączyć do aplikacji | Nieobsługiwane |
| Przekazywanie niestandardowych certyfikatów podpisywania tokenów | Obsługiwane | Obsługiwane |
| Dostosowywanie oświadczeń w tokenach |- Przekazywanie oświadczeń wejściowych od dostawców tożsamości<br />- Pobierz token dostępu od dostawcy tożsamości jako oświadczenie<br />- Wystawianie oświadczeń wyjściowych na podstawie wartości oświadczeń wejściowych<br />- Emisja oświadczeń wyjściowych ze stałymi wartościami |- Nie można przejść przez roszczenia od dostawców tożsamości federacyjnej<br />- Nie można uzyskać token dostępu od dostawcy tożsamości jako oświadczenie<br />- Nie można wystawiać oświadczeń wyjściowych na podstawie wartości oświadczeń wejściowych<br />- Może wystawiać oświadczenia wyjściowe z wartościami stałymi<br />- Może wystawiać oświadczenia wyjściowe na podstawie właściwości użytkowników zsynchronizowanych z usługą Azure AD |
| **Automatyzacji** | | |
| Automatyzacja zadań konfiguracyjnych i zarządzania | Obsługiwane za pośrednictwem usługi zarządzania kontrolą dostępu | Obsługiwane przy użyciu interfejsu API programu Microsoft Graph |

Jeśli zdecydujesz, że usługa Azure AD jest najlepszą ścieżką migracji dla aplikacji i usług, należy pamiętać o dwa sposoby integracji aplikacji z usługą Azure AD.

Aby zintegrować się z usługą Azure AD za pomocą federacji WS lub WIF, zalecamy zastosowanie podejścia opisanego w [temacie Konfigurowanie rejestracji jednokrotnej federacyjnego dla aplikacji niezwiązanych z galerią.](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery) Ten artykuł odnosi się do konfigurowania usługi Azure AD dla logowania jednokrotnego opartego na SAML, ale działa również do konfigurowania federacji WS. Zgodnie z tym podejściem wymaga licencji usługi Azure AD Premium. Takie podejście ma dwie zalety:

- Możesz uzyskać pełną elastyczność dostosowywania tokenu usługi Azure AD. Można dostosować oświadczenia, które są wystawiane przez usługę Azure AD, aby dopasować oświadczenia, które są wystawiane przez kontrolę dostępu. Dotyczy to w szczególności identyfikatora użytkownika lub oświadczenia identyfikatora nazwy. Aby nadal otrzymywać spójne identyfikatory użytkowników dla użytkowników po zmianie technologii, upewnij się, że identyfikatory użytkowników wystawione przez usługę Azure AD są zgodne z identyfikatorami wydanymi przez formę kontroli dostępu.
- Można skonfigurować certyfikat podpisywania tokenu, który jest specyficzny dla aplikacji i z okresem istnienia, który kontrolujesz.

> [!NOTE]
> Takie podejście wymaga licencji usługi Azure AD Premium. Jeśli jesteś klientem kontroli dostępu i potrzebujesz licencji premium do konfigurowania logowania jednokrotnego dla aplikacji, skontaktuj się z nami. Z przyjemnością udostępnimy licencje dla programistów.

Alternatywne podejście jest następujące [ten przykład kodu,](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation)który zawiera nieco inne instrukcje dotyczące konfigurowania federacji WS. Ten przykładowy kod nie używa WIF, ale raczej ASP.NET 4.5 OWIN oprogramowania pośredniczącego. Jednak instrukcje dotyczące rejestracji aplikacji są ważne dla aplikacji korzystających z WIF i nie wymagają licencji usługi Azure AD Premium. 

Jeśli wybierzesz takie podejście, musisz zrozumieć [rolowanie klucza podpisywania w usłudze Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover) Takie podejście używa globalnego klucza podpisywania usługi Azure AD do wystawiania tokenów. Domyślnie WIF nie odświeża automatycznie kluczy podpisywania. Gdy usługa Azure AD obraca swoje globalne klucze podpisywania, implementacja WIF musi być przygotowana do zaakceptowania zmian. Aby uzyskać więcej informacji, zobacz [Ważne informacje dotyczące podpisywania narzutu kluczy w usłudze Azure AD](https://msdn.microsoft.com/library/azure/dn641920.aspx).

Jeśli można zintegrować z usługą Azure AD za pośrednictwem protokołów OpenID Connect lub OAuth, zaleca się to zrobić. Mamy obszerną dokumentację i wskazówki dotyczące integracji usługi Azure AD z aplikacją sieci Web dostępną w naszym [przewodniku dla deweloperów usługi Azure AD.](https://aka.ms/aaddev)

#### <a name="migrate-to-azure-active-directory-b2c"></a>Migrowanie do usługi B2C usługi Azure Active Directory

Inną ścieżką migracji do rozważenia jest usługa Azure AD B2C. Usługa Azure AD B2C to usługa uwierzytelniania w chmurze, która, podobnie jak kontrola dostępu, umożliwia deweloperom outsourcing ich logiki uwierzytelniania i zarządzania tożsamościami do usługi w chmurze. Jest to płatna usługa (z warstwami bezpłatna i premium), która jest przeznaczona dla aplikacji przeznaczonych dla konsumentów, które mogą mieć nawet miliony aktywnych użytkowników.

Podobnie jak kontrola dostępu, jedną z najbardziej atrakcyjnych funkcji usługi Azure AD B2C jest to, że obsługuje wiele różnych typów kont. Za pomocą usługi Azure AD B2C możesz logować się do użytkowników przy użyciu ich konta Microsoft lub kont Facebook, Google, LinkedIn, GitHub lub Yahoo i nie tylko. Usługa Azure AD B2C obsługuje również "konta lokalne" lub nazwę użytkownika i hasła, które użytkownicy tworzą specjalnie dla aplikacji. Usługa Azure AD B2C zapewnia również rozszerzoność, której można użyć do dostosowania przepływów logowania. 

Jednak usługa Azure AD B2C nie obsługuje zakresu protokołów uwierzytelniania i formatów tokenów, które mogą wymagać klienci kontroli dostępu. Nie można również użyć usługi Azure AD B2C, aby uzyskać tokeny i kwerendy w celu uzyskania dodatkowych informacji o użytkowniku od dostawcy tożsamości, firmy Microsoft lub w inny sposób.

W poniższej tabeli porównano funkcje kontroli dostępu, które są istotne dla aplikacji sieci web z tymi, które są dostępne w usłudze Azure AD B2C. Na wysokim poziomie *usługi Azure AD B2C jest prawdopodobnie właściwym wyborem dla migracji, jeśli aplikacja jest skierowana do konsumenta lub jeśli obsługuje wiele różnych typów kont.*

| Możliwości | Obsługa kontroli dostępu | Obsługa usługi Azure AD B2C |
| ---------- | ----------- | ---------------- |
| **Typy kont** | | |
| Konta służbowe firmy Microsoft | Obsługiwane | Obsługiwane za pomocą zasad niestandardowych  |
| Konta z usługi Active Directory systemu Windows Server i usług AD FS | Obsługiwane przez bezpośrednią federację z usługą AD FS | Obsługiwane przez federację SAML przy użyciu zasad niestandardowych |
| Konta z innych systemów zarządzania tożsamościami przedsiębiorstwa | Obsługiwane przez bezpośrednią federację za pośrednictwem Federacji WS | Obsługiwane przez federację SAML przy użyciu zasad niestandardowych |
| Konta Microsoft do użytku osobistego | Obsługiwane | Obsługiwane | 
| Konta Facebook, Google, Yahoo | Obsługiwane | Facebook i Google obsługiwane natywnie, Yahoo obsługiwane przez federację OpenID Connect przy użyciu niestandardowych zasad |
| **Protokoły i zgodność z SDK** | | |
| Windows Identity Foundation (WIF) | Obsługiwane | Nieobsługiwane |
| WS-Federation | Obsługiwane | Nieobsługiwane |
| OAuth 2.0 | Wsparcie dla projektu 13 | Obsługa RFC 6749, najnowocześniejszej specyfikacji |
| WS-Trust | Obsługiwane | Nieobsługiwane |
| **Formaty tokenów** | | |
| Jwt | Obsługiwane w wersji beta | Obsługiwane |
| SAML 1.1 | Obsługiwane | Nieobsługiwane |
| SAML 2.0 | Obsługiwane | Nieobsługiwane |
| Swt | Obsługiwane | Nieobsługiwane |
| **Dostosowania** | | |
| Konfigurowalny interfejs użytkownika wykrywania/wybierania konta w obszarze do domu | Kod do pobrania, który można włączyć do aplikacji | W pełni konfigurowalny interfejs użytkownika za pośrednictwem niestandardowego CSS |
| Przekazywanie niestandardowych certyfikatów podpisywania tokenów | Obsługiwane | Niestandardowe klucze podpisywania, a nie certyfikaty, obsługiwane za pomocą zasad niestandardowych |
| Dostosowywanie oświadczeń w tokenach |- Przekazywanie oświadczeń wejściowych od dostawców tożsamości<br />- Pobierz token dostępu od dostawcy tożsamości jako oświadczenie<br />- Wystawianie oświadczeń wyjściowych na podstawie wartości oświadczeń wejściowych<br />- Emisja oświadczeń wyjściowych ze stałymi wartościami |- Może przechodzić przez roszczenia od dostawców tożsamości; zasady niestandardowe wymagane dla niektórych roszczeń<br />- Nie można uzyskać token dostępu od dostawcy tożsamości jako oświadczenie<br />- Może wystawiać oświadczenia wyjściowe na podstawie wartości oświadczeń wejściowych za pośrednictwem zasad niestandardowych<br />- Może wydawać oświadczenia wyjściowe ze stałymi wartościami za pośrednictwem zasad niestandardowych |
| **Automatyzacji** | | |
| Automatyzacja zadań konfiguracyjnych i zarządzania | Obsługiwane za pośrednictwem usługi zarządzania kontrolą dostępu |- Tworzenie użytkowników dozwolonych za pomocą interfejsu API Microsoft Graph<br />- Nie można programowo tworzyć dzierżaw, aplikacji lub zasad B2C |

Jeśli zdecydujesz, że usługa Azure AD B2C jest najlepszą ścieżką migracji dla aplikacji i usług, zacznij od następujących zasobów:

- [Dokumentacja usługi Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Zasady niestandardowe usługi Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Cennik usługi Azure AD B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migrowanie do tożsamości ping lub auth0

W niektórych przypadkach może się okazać, że usługi Azure AD i Usługi Azure AD B2C nie są wystarczające do zastąpienia kontroli dostępu w aplikacjach sieci web bez wprowadzania istotnych zmian w kodzie. Niektóre typowe przykłady mogą obejmować:

- Aplikacje sieci Web korzystające z WIF lub WS-Federation do logowania się u dostawców tożsamości społecznościowych, takich jak Google lub Facebook.
- Aplikacje sieci Web, które wykonują bezpośrednią federację do dostawcy tożsamości przedsiębiorstwa za pośrednictwem protokołu Federacji WS.
- Aplikacje sieci Web, które wymagają tokenu dostępu wystawionego przez dostawcę tożsamości społecznościowych (takiego jak Google lub Facebook) jako oświadczenia w tokenach wystawionych przez kontrolę dostępu.
- Aplikacje sieci Web ze złożonymi regułami przekształcania tokenów, których usługa Azure AD B2C nie może odtworzyć.
- Wielodostępne aplikacje internetowe, które używają usługi ACS do centralnego zarządzania federacją z wieloma różnymi dostawcami tożsamości

W takich przypadkach warto rozważyć migrację aplikacji sieci web do innej usługi uwierzytelniania w chmurze. Zalecamy zapoznanie się z poniższymi opcjami. Każda z następujących opcji oferuje funkcje podobne do kontroli dostępu:

|     |     |
| --- | --- |
| ![Na tym zdjęciu widać logo Auth0](./media/active-directory-acs-migration/rsz-auth0.png) | [Auth0](https://auth0.com/acs) to elastyczna usługa tożsamości w chmurze, która stworzyła [wskazówki dotyczące migracji wysokiego poziomu dla klientów kontroli dostępu](https://auth0.com/acs)i obsługuje prawie każdą funkcję, którą wykonuje usługa ACS. |
| ![Na tym obrazie jest wyświetlane logo Tożsamość ping](./media/active-directory-acs-migration/rsz-ping.png) | [Ping Identity](https://www.pingidentity.com) oferuje dwa rozwiązania podobne do ACS. PingOne to usługa tożsamości w chmurze, która obsługuje wiele takich samych funkcji jak ACS, a PingFederate jest podobnym produktem tożsamości lokalnej, który oferuje większą elastyczność. Więcej informacji na temat korzystania z tych produktów można znaleźć w wskazówkach dotyczących przejścia na [emeryturę usługi ACS firmy Ping.](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html) |

Naszym celem w pracy z Ping Identity i Auth0 jest zapewnienie, że wszyscy klienci kontroli dostępu mają ścieżkę migracji dla swoich aplikacji i usług, która minimalizuje ilość pracy wymaganej do przejścia z kontroli dostępu.

<!--

## SharePoint 2010, 2013, 2016

TODO: Azure AD only, use Azure AD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-that-use-active-authentication"></a>Usługi sieci Web korzystające z aktywnego uwierzytelniania

W przypadku usług sieci web zabezpieczonych tokenami wydanymi przez kontrolę dostępu kontrola dostępu oferuje następujące funkcje i możliwości:

- Możliwość rejestrowania jednej lub więcej *tożsamości usługi* w obszarze nazw kontroli dostępu. Tożsamości usługi mogą służyć do żądania tokenów.
- Obsługa protokołów OAuth WRAP i OAuth 2.0 Draft 13 do żądania tokenów przy użyciu następujących typów poświadczeń:
    - Proste hasło utworzone dla tożsamości usługi
    - Podpisany swt przy użyciu klucza symetrycznego lub certyfikatu X509
    - Token SAML wystawiony przez zaufanego dostawcę tożsamości (zazwyczaj wystąpienie usług AD FS)
- Obsługa następujących formatów tokenów: JWT, SAML 1.1, SAML 2.0 i SWT.
- Proste reguły przekształcania tokenów.

Tożsamości usługi w kontroli dostępu są zazwyczaj używane do implementowania uwierzytelniania między serwerami. 

#### <a name="migrate-to-azure-active-directory"></a>Migrowanie do usługi Azure Active Directory

Naszą rekomendacją dla tego typu przepływu uwierzytelniania jest migracja do [usługi Azure Active Directory.](https://azure.microsoft.com/develop/identity/signin/) Usługa Azure AD jest dostawcą tożsamości opartych na chmurze dla kont służbowych firmy Microsoft. Usługa Azure AD jest dostawcą tożsamości dla usługi Office 365, platformy Azure i wielu innych. 

Można również użyć usługi Azure AD do uwierzytelniania między serwerami przy użyciu implementacji usługi Azure AD grantu poświadczeń klienta OAuth. W poniższej tabeli porównano możliwości kontroli dostępu w uwierzytelnianiu między serwerami z możliwościami, które są dostępne w usłudze Azure AD.

| Możliwości | Obsługa kontroli dostępu | Obsługa usługi Azure AD |
| ---------- | ----------- | ---------------- |
| Jak zarejestrować usługę internetową | Tworzenie jednostki uzależniającej w portalu zarządzania kontrola dostępu | Tworzenie aplikacji sieci Web usługi Azure AD w portalu Azure |
| Jak zarejestrować klienta | Tworzenie tożsamości usługi w portalu zarządzania kontroli dostępu | Tworzenie innej aplikacji sieci Web usługi Azure AD w witrynie Azure portal |
| Używany protokół |- Protokół OAuth WRAP<br />- OAuth 2.0 Projekt 13 poświadczeń klienta dotacji | Przyznawanie poświadczeń klienta OAuth 2.0 |
| Metody uwierzytelniania klienta |- Proste hasło<br />- Podpisano SWT<br />- Token SAML od dostawcy tożsamości federacyjnej |- Proste hasło<br />- Podpisano JWT |
| Formaty tokenów |- JWT<br />- SAML 1.1<br />- SAML 2.0<br />- SWT<br /> | Tylko JWT |
| Transformacja tokenów |- Dodawanie oświadczeń niestandardowych<br />- Prosta logika wydawania oświadczeń if-then | Dodawanie oświadczeń niestandardowych | 
| Automatyzacja zadań konfiguracyjnych i zarządzania | Obsługiwane za pośrednictwem usługi zarządzania kontrolą dostępu | Obsługiwane przy użyciu interfejsu API programu Microsoft Graph |

Aby uzyskać wskazówki dotyczące implementowania scenariuszy między serwerami, zobacz następujące zasoby:

- Sekcja Service-to-Service [przewodnika dla deweloperów usługi Azure AD](https://aka.ms/aaddev)
- [Przykładowy kod demona przy użyciu prostych poświadczeń klienta hasła](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [Przykładowy kod demona przy użyciu poświadczeń klienta certyfikatu](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migrowanie do tożsamości ping lub auth0

W niektórych przypadkach może się okazać, że poświadczenia klienta usługi Azure AD i implementacja grantu OAuth nie są wystarczające do zastąpienia kontroli dostępu w architekturze bez większych zmian kodu. Niektóre typowe przykłady mogą obejmować:

- Uwierzytelnianie między serwerami przy użyciu formatów tokenów innych niż JWTs.
- Uwierzytelnianie między serwerami przy użyciu tokenu wejściowego dostarczonego przez zewnętrznego dostawcę tożsamości.
- Uwierzytelnianie między serwerami za pomocą reguł przekształcania tokenów, których nie można odtworzyć w usłudze Azure AD.

W takich przypadkach można rozważyć migrację aplikacji sieci web do innej usługi uwierzytelniania w chmurze. Zalecamy zapoznanie się z poniższymi opcjami. Każda z następujących opcji oferuje funkcje podobne do kontroli dostępu:

|     |     |
| --- | --- |
| ![Na tym zdjęciu widać logo Auth0](./media/active-directory-acs-migration/rsz-auth0.png) | [Auth0](https://auth0.com/acs) to elastyczna usługa tożsamości w chmurze, która stworzyła [wskazówki dotyczące migracji wysokiego poziomu dla klientów kontroli dostępu](https://auth0.com/acs)i obsługuje prawie każdą funkcję, którą wykonuje usługa ACS. |
| ![Na tym obrazie jest wyświetlane logo Tożsamość ping](./media/active-directory-acs-migration/rsz-ping.png) | [Ping Identity](https://www.pingidentity.com) oferuje dwa rozwiązania podobne do ACS. PingOne to usługa tożsamości w chmurze, która obsługuje wiele takich samych funkcji jak ACS, a PingFederate jest podobnym produktem tożsamości lokalnej, który oferuje większą elastyczność. Więcej informacji na temat korzystania z tych produktów można znaleźć w wskazówkach dotyczących przejścia na [emeryturę usługi ACS firmy Ping.](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html) |

Naszym celem w pracy z Ping Identity i Auth0 jest zapewnienie, że wszyscy klienci kontroli dostępu mają ścieżkę migracji dla swoich aplikacji i usług, która minimalizuje ilość pracy wymaganej do przejścia z kontroli dostępu.

#### <a name="passthrough-authentication"></a>Uwierzytelnianie przekazywania

W przypadku uwierzytelniania przekazywanego z dowolną transformacją tokenu nie ma równoważnej technologii firmy Microsoft do usługi ACS. Jeśli to jest to, czego potrzebują Twoi klienci, Auth0 może być tym, który zapewnia najbliższe rozwiązanie przybliżenia.

## <a name="questions-concerns-and-feedback"></a>Pytania, wątpliwości i opinie

Rozumiemy, że wielu klientów kontroli dostępu nie znajdzie jasnej ścieżki migracji po przeczytaniu tego artykułu. Może być potrzebna pomoc lub wskazówki przy określaniu odpowiedniego planu. Jeśli chcesz omówić scenariusze migracji i pytania, zostaw komentarz na tej stronie.

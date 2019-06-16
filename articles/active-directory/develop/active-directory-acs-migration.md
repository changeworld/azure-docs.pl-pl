---
title: Migrowanie z usługi Azure Access Control Service | Dokumentacja firmy Microsoft
description: Poznaj opcje dotyczące przenoszenia usług i aplikacji z usługi Azure Access Control Service (ACS).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/03/2018
ms.author: ryanwi
ms.reviewer: jlu, annaba, hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84a8c2954473401a9e57cba045907c60862ed61f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65546233"
---
# <a name="how-to-migrate-from-the-azure-access-control-service"></a>Instrukcje: Migrowanie z usługi Azure Access Control Service

Microsoft Azure Access Control Service (ACS), usługa Azure Active Directory (Azure AD) zostanie wycofana 7 listopada 2018 r. Aplikacje i usługi, które obecnie korzystanie z kontroli dostępu muszą być w pełni migrowane na inny mechanizm uwierzytelniania, następnie. W tym artykule opisano zalecenia dotyczące obecni klienci, planując wycofana korzystanie z kontroli dostępu. Obecnie nie używasz kontroli dostępu, nie trzeba podejmować żadnych działań.

## <a name="overview"></a>Omówienie

Kontrola dostępu jest usługi uwierzytelniania w chmurze zapewnia prosty sposób do uwierzytelniania i autoryzacji użytkowników, aby uzyskać dostęp do aplikacji sieci web i usług. Umożliwia ona wiele funkcji uwierzytelniania i autoryzacji, aby uwzględnić poza swój kod. Kontrola dostępu jest używany głównie przez deweloperów i architektów klientów programu Microsoft .NET, aplikacje sieci web platformy ASP.NET oraz usług sieci web Windows Communication Foundation (WCF).

Przypadki użycia dla kontroli dostępu mogą być podzielone na trzy główne kategorie:

- Uwierzytelnianie niektórych usług chmurowych firmy Microsoft, w tym usługi Azure Service Bus i Dynamics CRM. Aplikacje klienckie uzyskiwania tokenów z kontroli dostępu do uwierzytelniania tych usług, aby wykonywać różne akcje.
- Dodawanie uwierzytelniania do aplikacji sieci web, to wstępnie spakowane zestawy (np. SharePoint) i niestandardowych. Za pomocą kontroli dostępu "pasywny" uwierzytelnianie, aplikacje sieci web może obsługiwać Zaloguj się przy użyciu konta Microsoft (dawniej identyfikator Live ID) i za pomocą kont Google, Facebook, Yahoo, usługa Azure AD i usługi Active Directory Federation Services (AD FS).
- Zabezpieczanie usług niestandardowe sieci web za pomocą tokenów wystawionych przez kontrolę dostępu. Przy użyciu uwierzytelniania "active", usług sieci web można upewnić się, czy zezwolić na dostęp tylko znane aplikacje klienckie, które zostały uwierzytelnione przy użyciu kontroli dostępu.

Każda z tych przypadków użycia i ich zalecane migracji, które zostały omówione strategie w poniższych sekcjach.

> [!WARNING]
> W większości przypadków istotne zmiany kodu są wymagane do migracji istniejących aplikacji i usług do nowszymi technologiami. Firma Microsoft zaleca, aby natychmiast rozpocząć planowanie i wykonywanie migracji w celu uniknięcia potencjalnych przestojów ani przestoje.

Kontrola dostępu obejmuje następujące składniki:

- Usługa bezpiecznych tokenów (STS), które odbiera żądania uwierzytelniania i w zamian wystawia tokeny zabezpieczające.
- Klasycznym portalu Azure, której można utworzyć, usunąć i włączać i wyłączać przestrzeni nazw kontroli dostępu.
- Oddzielne kontrola dostępu do portalu zarządzania, którym dostosowywanie i skonfigurować przestrzeni nazw kontroli dostępu.
- Usługa zarządzania, która umożliwia automatyzowanie funkcji portali.
- Przekształcenie tokenu aparat reguł, które służy do definiowania złożoną logikę do manipulowania format danych wyjściowych tokenów wystawianych przez urząd kontroli dostępu.

Aby korzystać z tych składników, należy utworzyć co najmniej jeden nazw kontroli dostępu. A *przestrzeni nazw* to dedykowane wystąpienie kontroli dostępu, które aplikacje i usługi komunikują się za pomocą. Przestrzeń nazw jest odizolowana od innych klientów kontroli dostępu. Inni klienci kontroli dostępu, użyj własne przestrzenie nazw. Przestrzeń nazw kontroli dostępu ma dedykowanego adresu URL, który wygląda w następujący sposób:

```HTTP
https://<mynamespace>.accesscontrol.windows.net
```

Cała komunikacja z usługą STS i operacje zarządzania są wykonywane tylko pod tym adresem URL. Używasz różnych ścieżek do różnych celów. Aby ustalić, czy aplikacje lub usługi za pomocą kontroli dostępu, Monitor wykrywający sytuacje, cały ruch do https://&lt;przestrzeni nazw&gt;. accesscontrol.windows.net. Cały ruch do tego adresu URL odbywa się przez kontrolę dostępu i musi zostać zakończona. 

Wyjątkiem jest cały ruch do `https://accounts.accesscontrol.windows.net`. Ruch do tego adresu URL jest już obsługiwane przez inną usługę i **nie** wpływ wycofywania kontroli dostępu. 

Aby uzyskać więcej informacji na temat kontroli dostępu, zobacz [usługi 2.0 kontroli dostępu, (zarchiwizowanych)](https://msdn.microsoft.com/library/hh147631.aspx).

## <a name="find-out-which-of-your-apps-will-be-impacted"></a>Dowiedz się, której aplikacji dotyczy problem

Wykonaj kroki opisane w tej sekcji, aby dowiedzieć się, który aplikacji będzie wpływać na wycofanie usługi ACS.

### <a name="download-and-install-acs-powershell"></a>Pobieranie i instalowanie programu PowerShell usługi ACS

1. Przejdź do galerii programu PowerShell i Pobierz [Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
1. Zainstaluj moduł, uruchamiając

    ```powershell
    Install-Module -Name Acs.Namespaces
    ```

1. Pobierz listę wszystkie możliwe polecenia, uruchamiając

    ```powershell
    Get-Command -Module Acs.Namespaces
    ```

    Aby uzyskać pomoc dotyczącą określonego polecenia, uruchom polecenie:

    ```
     Get-Help [Command-Name] -Full
    ```
    
    gdzie `[Command-Name]` to nazwa polecenia ACS.

### <a name="list-your-acs-namespaces"></a>Lista przestrzeniami nazw usługi ACS

1. Nawiązywanie połączenia przy użyciu usługi ACS **Connect AcsAccount** polecenia cmdlet.
  
    Może być konieczne uruchomienie `Set-ExecutionPolicy -ExecutionPolicy Bypass` przed wykonaniem polecenia i zostać administratorem te subskrypcje w celu wykonania polecenia.

1. Lista dostępnych subskrypcji platformy Azure przy użyciu **Get AcsSubscription** polecenia cmdlet.
1. Listy, używając przestrzeni nazw usługi ACS **Get AcsNamespace** polecenia cmdlet.

### <a name="check-which-applications-will-be-impacted"></a>Sprawdź, które aplikacje będą mieć wpływ na

1. Użyj przestrzeni nazw z poprzedniego kroku, a następnie przejdź do `https://<namespace>.accesscontrol.windows.net`

    Na przykład jeśli jeden z przestrzeni nazw jest contoso-test, przejdź do strony `https://contoso-test.accesscontrol.windows.net`

1. W obszarze **relacje zaufania**, wybierz opcję **aplikacji jednostki uzależnionej** Aby wyświetlić listę aplikacji, które będzie mieć wpływ na wycofanie usługi ACS.
1. Powtórz kroki 1 i 2 dla wszelkich innych usług ACS namespace (s), masz.

## <a name="retirement-schedule"></a>Wycofanie harmonogramu

Od listopada 2017 r. wszystkie składniki kontroli dostępu są w pełni obsługiwane i operacyjnej. Jedynym ograniczeniem jest fakt, że możesz [nie można utworzyć nowych nazw kontroli dostępu za pośrednictwem klasycznego portalu Azure](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/).

Oto harmonogram wycofano składniki kontroli dostępu:

- **Listopad 2017**:  Administrator usługi Azure AD występują w klasycznej witrynie Azure portal [została wycofana](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/). W tym momencie zarządzanie przestrzenią nazw kontroli dostępu jest dostępna na nowego, dedykowanego adresu URL: `https://manage.windowsazure.com?restoreClassic=true`. Użyj tego adresu URl, aby wyświetlić swoje istniejące obszary nazw, włączanie i wyłączanie przestrzenie nazw i można usunąć przestrzeni nazw, jeśli zdecydujesz się.
- **2 kwietnia 2018 r.** : Klasyczny portal Azure jest całkowicie wycofane, co oznacza, że zarządzanie przestrzenią nazw kontroli dostępu nie jest już dostępna za pośrednictwem dowolnego adresu URL. W tym momencie nie można wyłączyć lub włączyć, usuń lub wyliczanie przestrzeniami nazw usługi Access Control. Jednak, w portalu zarządzania kontroli dostępu jest w pełni funkcjonalne i znajduje się w `https://\<namespace\>.accesscontrol.windows.net`. Wszystkie inne składniki kontroli dostępu w dalszym ciągu działać normalnie.
- **7 listopada 2018**: Wszystkie składniki kontroli dostępu są trwale zamknąć. W tym portalu zarządzania kontroli dostępu, Usługa zarządzania, usługi STS i aparat reguł przekształcania tokenu. W tym momencie wszelkie żądania wysyłane do kontroli dostępu (znajdujący się w \<przestrzeni nazw\>. accesscontrol.windows.net) się nie powieść. Użytkownik powinien zostały zmigrowane wszystkie istniejące aplikacje i usługi do innych technologii również przed upływem wskazanego terminu.

> [!NOTE]
> Zasada wyłącza przestrzenie nazw, które nie żądanego tokenu w okresie czasu. Począwszy od września 2018 r. ten okres jest obecnie w ciągu 14 dni braku aktywności, ale to zostanie skrócona do 7 dni nieaktywności w najbliższych tygodniach. Jeśli masz przestrzeni nazw kontroli dostępu, które są obecnie wyłączone, możesz to zrobić [pobrać i zainstalować program ACS PowerShell](#download-and-install-acs-powershell) ponowne włączenie namespace (s).

## <a name="migration-strategies"></a>Strategie migracji

W poniższych sekcjach opisano ogólne zalecenia dotyczące migrowania z kontroli dostępu do innych technologii firmy Microsoft.

### <a name="clients-of-microsoft-cloud-services"></a>Klienci usług chmurowych firmy Microsoft

Każda usługa chmury firmy Microsoft, który akceptuje tokeny wystawione przez kontrolę dostępu teraz obsługuje co najmniej jeden z alternatywnej formy uwierzytelniania. Mechanizm uwierzytelniania poprawne różni się dla każdej usługi. Zaleca się odwoływać się do dokumentacji dla każdej usługi oficjalne wskazówki. Dla wygody każdy zestaw dokumentację można znaleźć tu:

| Usługa | Wskazówki |
| ------- | -------- |
| Azure Service Bus | [Migrowanie do sygnatur dostępu współdzielonego](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-migrate-acs-sas) |
| Azure Service Bus Relay | [Migrowanie do sygnatur dostępu współdzielonego](https://docs.microsoft.com/azure/service-bus-relay/relay-migrate-acs-sas) |
| Azure Managed Cache | [Migrowanie do usługi Azure Cache dla usługi Redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-faq#which-azure-cache-offering-is-right-for-me) |
| Azure DataMarket | [Migrowanie do interfejsów API usług Cognitive Services](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| BizTalk Services | [Migrowanie do funkcji Logic Apps w usłudze Azure App Service](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Azure Media Services | [Migrowanie do uwierzytelniania usługi Azure AD](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure Backup | [Uaktualnienie agenta usługi Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: https://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->

### <a name="sharepoint-customers"></a>Klienci programu SharePoint

Program SharePoint 2013, 2016 i SharePoint Online klienci wykorzystali długo ACS na potrzeby uwierzytelniania w chmurze, lokalnie i w scenariuszach hybrydowych. Niektóre funkcje programu SharePoint i przypadki użycia dotyczy wycofanie usług ACS, podczas gdy inne osoby nie będą. Poniższej tabeli przedstawiono wskazówki dotyczące migracji dla niektórych z najpopularniejszych programu SharePoint są wyposażone w tym ACS Wykorzystaj:

| Cecha | Wskazówki |
| ------- | -------- |
| Uwierzytelnianie użytkowników z usługi Azure AD | Wcześniej usługa Azure AD nie obsługuje tokeny SAML 1.1 wymaganych przez program SharePoint do uwierzytelniania i ACS została użyta jako pośrednik, który zgłosił programu SharePoint jest zgodny z formatami tokenu usługi Azure AD. Teraz możesz [połączenia programu SharePoint bezpośrednio do usługi Azure AD przy użyciu programu SharePoint w galerii aplikacji usługi Azure AD dla aplikacji lokalnych](https://docs.microsoft.com/azure/active-directory/saas-apps/sharepoint-on-premises-tutorial). |
| [Uwierzytelnianie aplikacji i serwera do uwierzytelniania w programie SharePoint w środowisku lokalnym](https://technet.microsoft.com/library/jj219571(v=office.16).aspx) | Nie dotyczy usług ACS wycofanie; Brak wymaganych zmian. | 
| [Autoryzacja niski zaufania dla dodatków programu SharePoint (Dostawca obsługiwany i programu SharePoint hostowany)](https://docs.microsoft.com/sharepoint/dev/sp-add-ins/three-authorization-systems-for-sharepoint-add-ins) | Nie dotyczy usług ACS wycofanie; Brak wymaganych zmian. |
| [Wyszukiwanie programu SharePoint chmury hybrydowej](https://blogs.msdn.microsoft.com/spses/2015/09/15/cloud-hybrid-search-service-application/) | Nie dotyczy usług ACS wycofanie; Brak wymaganych zmian. |

### <a name="web-applications-that-use-passive-authentication"></a>Aplikacje sieci Web, które używają uwierzytelnianiem pasywnym

Aplikacje sieci web korzystających z kontroli dostępu do uwierzytelniania użytkowników Access Control oferuje następujące funkcje i możliwości dla deweloperów aplikacji sieci web i architektów:

- Głęboka integracja za pomocą programu Windows Identity Foundation (WIF).
- Federacja z usługą Google, Facebook, Yahoo, usługi Azure Active Directory i AD FS i firmy Microsoft.
- Obsługuje następujące protokoły uwierzytelniania: Projekt protokołu OAuth 2.0 13, WS-Trust i federacji usług sieci Web (WS-Federation).
- Pomoc techniczna dla następujące formaty tokenów: Tokenu Web JSON (JWT), SAML 1.1, SAML 2.0 i Simple Web Token (SWT).
- Obsługi odnajdowania obszaru macierzystego, zintegrowane z programu WIF, która umożliwia użytkownikom na wybranie typu konta używanego do logowania. To środowisko jest hostowana przez aplikację sieci web i jest w pełni dostosowywalny.
- Token transformacji, która umożliwia zaawansowane dostosowywanie oświadczeń otrzymanych przez aplikację sieci web z kontroli dostępu, w tym:
    - Przekazuj oświadczeń od dostawców tożsamości.
    - Dodawanie dodatkowych oświadczenia niestandardowe.
    - Proste logiki if, a następnie do wystawiania oświadczeń w określonych warunkach.

Niestety nie ma jednego usługa, która oferuje wszystkie możliwości równoważne. Należy ocenić, które możliwości kontroli dostępu użytkownik należy, a następnie wybierz między [usługi Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/), [usługi Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) (Azure AD B2C) lub innego uwierzytelniania w chmurze Usługa.

#### <a name="migrate-to-azure-active-directory"></a>Migrowanie do usługi Azure Active Directory

Ścieżka do rozważenia wprowadzi integrację usług i aplikacji bezpośrednio z usługą Azure AD. Usługa Azure AD jest to dostawca tożsamości opartej na chmurze dla firmy Microsoft kont służbowych. Usługa Azure AD jest dostawcy tożsamości dla usługi Office 365, platformy Azure i wiele więcej. Zapewnia podobne możliwości uwierzytelniania w celu kontroli dostępu federacyjnego, ale nie obsługuje wszystkie funkcje kontroli dostępu. 

Podstawowy przykład jest Federacji za pomocą dostawców tożsamości społecznościowych, takich jak Facebook, Google i Yahoo. Jeśli użytkownicy zalogować się przy użyciu tego rodzaju poświadczeń, usługi Azure AD nie jest rozwiązaniem dla Ciebie. 

Usługa Azure AD nie zawsze obsługuje również dokładnie tych samych protokołów uwierzytelniania jako kontrola dostępu. Na przykład mimo że zarówno kontroli dostępu, jak i usługi Azure AD obsługuje uwierzytelniania OAuth, istnieją drobne różnice między każdego wdrożenia. Różne implementacje trzeba zmodyfikować kod w ramach migracji.

Usługa Azure AD zapewnia jednak wiele potencjalnych korzyści klientom kontroli dostępu. Go natywnie obsługuje Microsoft kont służbowych hostowane w chmurze, które są często używane przez klientów kontroli dostępu. 

Dzierżawa usługi Azure AD można również federacyjną do co najmniej jednego wystąpienia usługi Active Directory w środowisku lokalnym za pomocą usług AD FS. W ten sposób aplikację można uwierzytelniać użytkowników w chmurze i użytkowników, którzy są hostowane w środowisku lokalnym. Obsługuje ona również protokołu WS-Federation, który sprawia, że stosunkowo prosta do integracji z aplikacją sieci web przy użyciu programu WIF.

W poniższej tabeli porównano funkcje kontroli dostępu, które mają zastosowanie do aplikacji sieci web za pomocą tych funkcji, które są dostępne w usłudze Azure AD. 

Na wysokim poziomie *usługi Azure Active Directory jest prawdopodobnie jest najlepszym wyborem dla migracji, jeśli możesz umożliwić użytkownikom rejestrowanie w tylko przy użyciu ich Microsoft kont służbowych*.

| Możliwości | Lepsza obsługa kontroli dostępu | Obsługa usługi Azure AD |
| ---------- | ----------- | ---------------- |
| **Typy kont** | | |
| Microsoft kont służbowych | Obsługiwane | Obsługiwane |
| Konta z systemu Windows Server Active Directory i AD FS |-Obsługiwanych za pośrednictwem federacji z dzierżawy usługi Azure AD <br />-Obsługiwanych za pośrednictwem bezpośredniego federacji z usługami AD FS | Obsługiwane wyłącznie za pośrednictwem federacji z dzierżawy usługi Azure AD | 
| Konta z innymi systemami zarządzania tożsamością przedsiębiorstwa |— To możliwe za pośrednictwem federacji z dzierżawy usługi Azure AD <br />-Obsługiwanych za pośrednictwem bezpośredniego Federacji | Możliwe za pośrednictwem federacji z dzierżawy usługi Azure AD |
| Konta Microsoft do użytku osobistego | Obsługiwane | Obsługiwane za pośrednictwem usługi Azure AD v2.0 protokołu OAuth, ale nie w żadnych innych protokołów | 
| Facebook, Google, Yahoo kont | Obsługiwane | Nieobsługiwane jakiejkolwiek |
| **Protokoły i zgodność z zestawu SDK** | | |
| WIF | Obsługiwane | Obsługiwane, ale ograniczone instrukcje są dostępne |
| WS-Federation | Obsługiwane | Obsługiwane |
| OAuth 2.0 | Obsługa wersji roboczej 13 | Obsługa 6749 RFC, większości współczesnych specyfikacji |
| WS-Trust | Obsługiwane | Nieobsługiwane |
| **Format tokenu** | | |
| JWT | Obsługiwane w wersji Beta | Obsługiwane |
| SAML 1.1 | Obsługiwane | Wersja zapoznawcza |
| SAML 2.0 | Obsługiwane | Obsługiwane |
| SWT | Obsługiwane | Nieobsługiwane |
| **Dostosowania** | | |
| Można dostosować obszaru głównego odnajdywanie/konto pobrania interfejsu użytkownika | Do pobrania kod, który może zostać włączona do aplikacji | Nieobsługiwane |
| Przekazać niestandardowe certyfikaty podpisywania tokenu | Obsługiwane | Obsługiwane |
| Dostosowywanie oświadczeń w tokenach |-Przekazywania danych wejściowych oświadczeń od dostawców tożsamości<br />-Uzyskaj token dostępu z dostawcy tożsamości jako oświadczenia<br />-Wydawania oświadczeń danych wyjściowych na podstawie wartości oświadczeń wejściowych<br />-Wydawania oświadczeń danych wyjściowych za pomocą wartości stałych |-Nie można przekazać za pośrednictwem oświadczeń od dostawców tożsamości federacyjnych<br />-Nie Uzyskaj token dostępu z dostawcy tożsamości jako oświadczenia<br />-Nie mogą wystawiać oświadczeń danych wyjściowych na podstawie wartości oświadczeń wejściowych<br />-Może wystawiać oświadczenia wyjściowego przy użyciu wartości stałych<br />-Może wystawiać oświadczenia dane wyjściowe na podstawie właściwości użytkowników zsynchronizowanych z usługą Azure AD |
| **Automatyzacja** | | |
| Automatyzowanie zadań konfiguracji i zarządzania | Obsługiwane z użyciem usługi zarządzania kontroli dostępu | Obsługiwane za pośrednictwem programu Microsoft Graph i Azure AD interfejsu API programu Graph |

Jeśli zdecydujesz, że usługa Azure AD jest najlepszej ścieżki migracji aplikacji i usług, należy pamiętać o Integrowanie aplikacji z usługą Azure AD na dwa sposoby.

Aby korzystać z protokołu WS-Federation lub programu WIF do integracji z usługą Azure AD, zalecamy następujące podejście opisane w [skonfigurować federacyjne logowanie jednokrotne dla aplikacji spoza galerii](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery). Artykuł odnosi się do konfigurowania usługi Azure AD dla opartej na SAML logowania jednokrotnego, ale działa również w przypadku konfigurowania protokołu WS-Federation. Tego podejścia wymaga licencji usługi Azure AD Premium. Takie podejście ma dwie zalety:

- Możesz uzyskać pełną elastyczność dostosowywania tokenu usługi Azure AD. Można dostosować oświadczenia, które są wydawane przez zgodne oświadczenia, które są wydawane przez kontrolę dostępu w usłudze Azure AD. Dotyczy to zwłaszcza identyfikator lub identyfikator nazwy oświadczenia użytkownika. Nadal wyświetlany użytkownikom spójne identyfikatory dla użytkowników po zmianie technologii, upewnij się, że identyfikatory użytkowników wystawiony przez usługę Azure AD jest zgodny tymi wystawionymi przez kontrolę dostępu.
- Można skonfigurować certyfikat podpisywania tokenu, która zależy od aplikacji i możesz kontrolować okres istnienia.

> [!NOTE]
> Takie podejście wymaga licencji usługi Azure AD Premium. Jeśli korzystasz z kontroli dostępu i wymaga licencji premium do konfigurowania logowania jednokrotnego dla aplikacji, skontaktuj się z nami. Będziemy wszystkiego zapewnienie licencji dewelopera do użycia.

Alternatywnym podejściem jest wykonanie czynności opisanych [ten przykładowy kod](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation), co daje nieco inne instrukcje dotyczące konfigurowania protokołu WS-Federation. Ten przykładowy kod nie używa programu WIF, ale raczej oprogramowania pośredniczącego OWIN 4.5 programu ASP.NET. Jednak z instrukcjami dotyczącymi rejestracji aplikacji są prawidłowe dla aplikacji przy użyciu programu WIF i nie wymaga licencji usługi Azure AD Premium. 

Jeśli wybierzesz tę opcję, musisz zrozumieć [Przerzucanie klucza podpisywania w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). To podejście używa usługi Azure AD, globalne, podpisywania klucza do wydawania tokenów. Domyślnie program WIF nie będzie odświeżana automatycznie klucze podpisywania. W przypadku usługi Azure AD obraca się jego globalne klucze podpisywania, implementacji programu WIF trzeba przygotować, aby zatwierdzić zmiany. Aby uzyskać więcej informacji, zobacz [ważne informacje na temat Przerzucanie klucza podpisywania w usłudze Azure AD](https://msdn.microsoft.com/library/azure/dn641920.aspx).

Można zintegrować z usługą Azure AD za pomocą protokołów uwierzytelniania OpenID Connect lub OAuth, zaleca się temu. Mamy obszerną dokumentację i wskazówki dotyczące sposobu integracji usługi Azure AD w aplikacji sieci web dostępnych w naszej [przewodnik dewelopera usługi Azure AD](https://aka.ms/aaddev).

#### <a name="migrate-to-azure-active-directory-b2c"></a>Migrowanie do usługi Azure Active Directory B2C

Ścieżki migracji do rozważenia jest usługi Azure AD B2C. Usługa Azure AD B2C to usługa uwierzytelniania w chmurze, takich jak kontrola dostępu pozwala deweloperom na oddelegowanie logikę zarządzania uwierzytelnianiem i tożsamościami do usługi w chmurze. Jest to usługa płatne (korzystającego z warstwy bezpłatna i premium), która jest przeznaczona dla aplikacji przeznaczonych dla konsumentów, które mogą mieć milionów aktywnych użytkowników.

Podobnie jak kontrola dostępu jest jedną z najbardziej atrakcyjnych funkcji usługi Azure AD B2C, obsługuje wiele różnych typów kont. Za pomocą usługi Azure AD B2C można logowania użytkowników za pomocą swojego konta Microsoft lub kont usługi Facebook, Google, LinkedIn, GitHub lub Yahoo i więcej. Usługa Azure AD B2C obsługuje również "kontami lokalnymi" lub nazwy użytkownika i hasła, które są tworzone specjalnie dla twojej aplikacji. Usługa Azure AD B2C zapewnia również rozbudowane rozszerzalności, która umożliwia dostosowywanie przepływów logowania. 

Jednak usługa Azure AD B2C nie obsługuje szeroki zakres protokołów uwierzytelniania tokenu formatów i klienci mogą wymagać kontroli dostępu. Ponadto nie można użyć usługi Azure AD B2C można uzyskać tokenów i zapytania dodatkowe informacje o użytkowniku od dostawcy tożsamości firmy Microsoft lub w inny sposób.

W poniższej tabeli porównano funkcje kontroli dostępu, które mają zastosowanie do aplikacji sieci web z tymi, które są dostępne w usłudze Azure AD B2C. Na wysokim poziomie *usługi Azure AD B2C jest prawdopodobnie dobrym wyborem dla migracji, jeśli aplikacja odbiorcy z, czy obsługuje ona wiele różnych typów kont.*

| Możliwości | Lepsza obsługa kontroli dostępu | Obsługa usługi Azure AD B2C |
| ---------- | ----------- | ---------------- |
| **Typy kont** | | |
| Microsoft kont służbowych | Obsługiwane | Obsługiwane z użyciem zasad niestandardowych  |
| Konta z systemu Windows Server Active Directory i AD FS | Obsługiwane za pośrednictwem bezpośredniego federacji z usługami AD FS | Obsługiwane za pośrednictwem SAML federation za pomocą zasad niestandardowych |
| Konta z innymi systemami zarządzania tożsamością przedsiębiorstwa | Obsługiwane za pośrednictwem federacji bezpośredniego za pośrednictwem usługi WS-Federation | Obsługiwane za pośrednictwem SAML federation za pomocą zasad niestandardowych |
| Konta Microsoft do użytku osobistego | Obsługiwane | Obsługiwane | 
| Facebook, Google, Yahoo kont | Obsługiwane | Facebook i Google obsługiwane natywnie, Yahoo obsługiwanych za pośrednictwem OpenID Connect federation za pomocą zasad niestandardowych |
| **Protokoły i zgodność z zestawu SDK** | | |
| Windows Identity Foundation (WIF) | Obsługiwane | Nieobsługiwane |
| WS-Federation | Obsługiwane | Nieobsługiwane |
| OAuth 2.0 | Obsługa wersji roboczej 13 | Obsługa 6749 RFC, większości współczesnych specyfikacji |
| WS-Trust | Obsługiwane | Nieobsługiwane |
| **Format tokenu** | | |
| JWT | Obsługiwane w wersji Beta | Obsługiwane |
| SAML 1.1 | Obsługiwane | Nieobsługiwane |
| SAML 2.0 | Obsługiwane | Nieobsługiwane |
| SWT | Obsługiwane | Nieobsługiwane |
| **Dostosowania** | | |
| Można dostosować obszaru głównego odnajdywanie/konto pobrania interfejsu użytkownika | Do pobrania kod, który może zostać włączona do aplikacji | W pełni dostosowywalne interfejsu użytkownika za pomocą niestandardowych CSS |
| Przekazać niestandardowe certyfikaty podpisywania tokenu | Obsługiwane | Niestandardowe klucze podpisywania, nie certyfikaty obsługiwane z użyciem zasad niestandardowych |
| Dostosowywanie oświadczeń w tokenach |-Przekazywania danych wejściowych oświadczeń od dostawców tożsamości<br />-Uzyskaj token dostępu z dostawcy tożsamości jako oświadczenia<br />-Wydawania oświadczeń danych wyjściowych na podstawie wartości oświadczeń wejściowych<br />-Wydawania oświadczeń danych wyjściowych za pomocą wartości stałych |— Można przekazać za pośrednictwem oświadczeń od dostawców tożsamości; wymagane pewne oświadczenia niestandardowe zasady<br />-Nie Uzyskaj token dostępu z dostawcy tożsamości jako oświadczenia<br />-Może wystawiać oświadczenia dane wyjściowe na podstawie wartości oświadczeń wejściowych za pomocą zasad niestandardowych<br />-Może wystawiać oświadczenia wyjściowego przy użyciu stałych wartości za pomocą zasad niestandardowych |
| **Automatyzacja** | | |
| Automatyzowanie zadań konfiguracji i zarządzania | Obsługiwane z użyciem usługi zarządzania kontroli dostępu |-Tworzenie użytkowników, którzy mogą za pomocą interfejsu API programu Graph usługi Azure AD<br />-Nie można utworzyć zasad, aplikacji lub dzierżaw B2C programowe |

Jeśli zdecydujesz, że usługi Azure AD B2C jest najlepszej ścieżki migracji aplikacji i usług, Rozpocznij od poniższych zasobów:

- [Dokumentacja usługi Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Niestandardowe zasady usługi Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Cennik usługi Azure AD B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Przenoszonych do oprogramowania lub Auth0 Ping Identity

W niektórych przypadkach może stwierdzisz, że usługa Azure AD i Azure AD B2C nie są wystarczające do zastąpienia kontroli dostępu w aplikacji sieci web bez wprowadzania zmian w kodzie głównych. Typowe przykłady mogą być następujące:

- Aplikacje sieci Web na użytek programu WIF i usługi WS-Federation Zaloguj się przy użyciu dostawców tożsamości społecznościowych, takich jak Facebook lub Google.
- Aplikacje internetowe, które wykonują bezpośrednie Federacji dostawcy tożsamości organizacji za pomocą protokołu WS-Federation.
- Aplikacje, które wymagają token dostępu wystawiony przez dostawcę tożsamości dla sieci społecznościowej (np. Facebook lub Google) jako oświadczenia w tokeny wystawione przez kontrolę dostępu w sieci Web.
- Aplikacje sieci Web za pomocą reguł skomplikowaną transformację tokenów, które usługi Azure AD lub Azure AD B2C nie można odtworzyć.
- Wielodostępnych aplikacji sieci web, które umożliwia centralne zarządzanie Federację wielu dostawców tożsamości z innej usługi ACS

W takich przypadkach warto rozważyć migrowanie aplikacji sieci web w taki sposób, aby inna usługa uwierzytelniania w chmurze. Firma Microsoft zaleca Eksplorowanie następujące opcje. Każdy z poniższych opcji, oferuje funkcje podobne do kontroli dostępu:

|     |     | 
| --- | --- |
| ![Rozwiązanie Auth0](./media/active-directory-acs-migration/rsz_auth0.png) | [Rozwiązanie Auth0](https://auth0.com/acs) jest elastyczne chmurze Usługa zarządzania tożsamościami, która została utworzona [wskazówek dotyczących migracji wysokiego poziomu dla klientów, Access Control](https://auth0.com/acs), obsługuje prawie w każdym funkcję, która wykonuje ACS. |
| ![Ping](./media/active-directory-acs-migration/rsz_ping.png) | [Ping Identity](https://www.pingidentity.com) oferuje dwie podobne do usług ACS. PingOne jest usługa tożsamości w chmurze obsługuje wiele same funkcje co usługa ACS, a serwera PingFederate jest podobny sposób na lokalne tożsamości produktu, który zapewnia większą elastyczność. Zapoznaj się [wskazówek dotyczących emerytury ACS firmy Ping](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html) Aby uzyskać więcej informacji na temat korzystania z tych produktów. |

Naszym celem w pracy firmy Ping Identity i Auth0 jest upewnij się, że wszyscy klienci kontrola dostępu do ścieżki migracji dla swoich aplikacji i usług, które minimalizuje nakład pracy wymagany do przenoszenia z kontroli dostępu.

<!--

## SharePoint 2010, 2013, 2016

TODO: Azure AD only, use Azure AD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-that-use-active-authentication"></a>Usługi sieci Web, które korzystają z uwierzytelniania usługi active

W przypadku usług sieci web, które są zabezpieczone przy użyciu tokenów wystawionych przez kontrolę dostępu Access Control oferuje następujące funkcje i możliwości:

- Możliwość zarejestrowania co najmniej jeden *tożsamości usługi* w Twojej przestrzeni nazw kontroli dostępu. Tożsamości usługi może służyć do żądania tokenów.
- Obsługa OAuth opakowywanie i OAuth 2.0 projekt 13 protokołów do żądania tokenów, korzystając z następujących typów poświadczeń:
    - Prostych haseł, które są tworzone dla tożsamości usługi
    - Element podpisane SWT przy użyciu klucza symetrycznego lub X509 certyfikatu
    - Token SAML, wystawiony przez zaufanego dostawcy tożsamości (zazwyczaj wystąpienia usług AD FS)
- Pomoc techniczna dla następujące formaty tokenów: JWT, SAML 1.1, SAML 2.0, and SWT.
- Reguły prostą transformację tokenu.

Tożsamości usługi kontroli dostępu są zazwyczaj używane do uwierzytelniania serwera do zaimplementowania. 

#### <a name="migrate-to-azure-active-directory"></a>Migrowanie do usługi Azure Active Directory

Nasze zalecenie, dla tego typu przepływu uwierzytelniania jest przeprowadzenie migracji do [usługi Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/). Usługa Azure AD jest to dostawca tożsamości opartej na chmurze dla firmy Microsoft kont służbowych. Usługa Azure AD jest dostawcy tożsamości dla usługi Office 365, platformy Azure i wiele więcej. 

Umożliwia także usługi Azure AD do uwierzytelniania serwera do serwera przy użyciu wdrożenia usługi Azure AD przyznanie poświadczenia klienta OAuth. W poniższej tabeli porównano funkcje kontroli dostępu w uwierzytelnianiu serwera serwera z tymi, które są dostępne w usłudze Azure AD.

| Możliwości | Lepsza obsługa kontroli dostępu | Obsługa usługi Azure AD |
| ---------- | ----------- | ---------------- |
| Jak zarejestrować usługi sieci web | Tworzenie jednostki uzależnionej ze stron w portalu zarządzania kontroli dostępu | Tworzenie aplikacji sieci web usługi Azure AD w witrynie Azure portal |
| Jak zarejestrować klienta | Tworzenie tożsamości usługi w portalu zarządzania kontroli dostępu | Utwórz inną aplikację sieci web usługi Azure AD w witrynie Azure portal |
| Protokół używany |-Protokół OAuth WRAP<br />-Przyznawanie poświadczeń klienta OAuth 2.0 projekt 13 | Przyznawanie poświadczeń klienta OAuth 2.0 |
| Metody uwierzytelniania klienta |-Proste hasło<br />-Podpisem SWT<br />— Token SAML od dostawcy tożsamości federacyjnych |-Proste hasło<br />-Podpisany token JWT. |
| Format tokenu |- JWT<br />- SAML 1.1<br />- SAML 2.0<br />- SWT<br /> | Token JWT tylko |
| Przekształcenie tokenu |— Dodawanie oświadczenia niestandardowe<br />— Prosta oświadczenia if, a następnie wystawiania logiki | Dodawanie oświadczenia niestandardowe | 
| Automatyzowanie zadań konfiguracji i zarządzania | Obsługiwane z użyciem usługi zarządzania kontroli dostępu | Obsługiwane za pośrednictwem programu Microsoft Graph i Azure AD interfejsu API programu Graph |

Wskazówki dotyczące implementującej scenariuszach serwer serwer na ten temat można znaleźć w następujących zasobach:

- Usługa-Usługa części [przewodnik dewelopera usługi Azure AD](https://aka.ms/aaddev)
- [Demon przykładowy kod przy użyciu poświadczeń klienta proste hasło](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [Demon przykładowy kod przy użyciu poświadczeń klienta certyfikatu](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Przenoszonych do oprogramowania lub Auth0 Ping Identity

W niektórych przypadkach może się okazać, że poświadczeń klienta usługi Azure AD i OAuth udzielić wdrożenia nie są wystarczające, aby zastąpić kontroli dostępu w ramach architektury bez zmian w kodzie głównych. Typowe przykłady mogą być następujące:

- Uwierzytelnianie serwera serwera przy użyciu tokenu formatów innych niż tokenów Jwt.
- Uwierzytelnianie serwera serwera przy użyciu tokenu danych wejściowych dostarczonych przez zewnętrznego dostawcy tożsamości.
- Uwierzytelnianie serwera serwera z regułami przekształcania tokenów, które nie można odtworzyć usługi Azure AD.

W takich przypadkach należy rozważyć, migrowanie aplikacji sieci web w taki sposób, aby inna usługa uwierzytelniania w chmurze. Firma Microsoft zaleca Eksplorowanie następujące opcje. Każdy z poniższych opcji, oferuje funkcje podobne do kontroli dostępu:

|     |     | 
| --- | --- |
| ![Rozwiązanie Auth0](./media/active-directory-acs-migration/rsz_auth0.png) | [Rozwiązanie Auth0](https://auth0.com/acs) jest elastyczne chmurze Usługa zarządzania tożsamościami, która została utworzona [wskazówek dotyczących migracji wysokiego poziomu dla klientów, Access Control](https://auth0.com/acs), obsługuje prawie w każdym funkcję, która wykonuje ACS. |
| ![Ping](./media/active-directory-acs-migration/rsz_ping.png) | [Ping Identity](https://www.pingidentity.com) oferuje dwie podobne do usług ACS. PingOne jest usługa tożsamości w chmurze obsługuje wiele same funkcje co usługa ACS, a serwera PingFederate jest podobny sposób na lokalne tożsamości produktu, który zapewnia większą elastyczność. Zapoznaj się [wskazówek dotyczących emerytury ACS firmy Ping](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html) Aby uzyskać więcej informacji na temat korzystania z tych produktów. |

Naszym celem w pracy firmy Ping Identity i Auth0 jest upewnij się, że wszyscy klienci kontrola dostępu do ścieżki migracji dla swoich aplikacji i usług, które minimalizuje nakład pracy wymagany do przenoszenia z kontroli dostępu.

#### <a name="passthrough-authentication"></a>Uwierzytelnianie przekazujących

Przekazywanie do uwierzytelniania przy użyciu dowolnego przekształcenia tokenu jest nie równoważne technologii firmy Microsoft w usłudze ACS. Jeśli to potrzebnych klientom, Auth0 może być osobą, która zapewnia najbliższego rozwiązanie zbliżenia.

## <a name="questions-concerns-and-feedback"></a>Pytania, uwagi i opinie

Rozumiemy, że wielu klientów kontroli dostępu nie odnajdzie ścieżki migracji wyczyść po przeczytaniu tego artykułu. Może być konieczne niektóre pomocy lub wskazówek przy ustalaniu właściwego planu. Jeśli chcesz omówić swoje scenariusze migracji i pytania, pozostaw komentarz na tej stronie.

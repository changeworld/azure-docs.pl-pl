---
title: Zabezpieczanie wdrożeń typu PaaS | Dokumentacja firmy Microsoft
description: " Omówienie zabezpieczeń zalety rozwiązania PaaS i inne modele usług w chmurze i Dowiedz się, zalecane rozwiązania dotyczące zabezpieczania wdrożenia modelu PaaS platformy Azure. "
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: techlake
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2019
ms.author: terrylan
ms.openlocfilehash: e833317fa16576fa0006a774226d12974fd93ed8
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62107450"
---
# <a name="securing-paas-deployments"></a>Zabezpieczanie wdrożeń PaaS

Ten artykuł zawiera informacje ułatwiające:

- Poznaj zalety zabezpieczeń, obsługi aplikacji w chmurze
- Ocena korzyści związane z bezpieczeństwem, platforma jako usługa (PaaS) w porównaniu z innymi modelami usługi chmury
- Zmienić nastawienie zabezpieczeń sieci skoncentrowane na podejście obwodowej skoncentrowane na tożsamość zabezpieczeń
- Ogólne PaaS najlepszych praktyk zalecenia dotyczące implementowania zabezpieczeń

## <a name="cloud-security-advantages"></a>Korzyści związane z bezpieczeństwem chmury
Istnieją korzyści związane z bezpieczeństwem do bycia w chmurze. W środowisku lokalnym, prawdopodobnie organizacjach niewypełnienia obowiązków i ograniczone zasoby dostępne dla wartości inwestycji w zabezpieczeń, która tworzy środowisko, gdzie osoby atakujące mogą wykorzystać luki w zabezpieczeniach we wszystkich warstwach.

![Korzyści związane z bezpieczeństwem ery chmury][1]

Organizacje mogą zwiększyć ich wykrywanie zagrożeń i najlepsze czasy odpowiedzi przy użyciu funkcji zabezpieczeń opartych na chmurze dostawcy i rozwiązań inteligentnych w chmurze.  Przesuwając obowiązki do dostawcy chmury, organizacje mogą uzyskać więcej zakresu zabezpieczeń, co umożliwia ich ponowne przydzielenie zasoby dotyczące zabezpieczeń i budżetu do innych priorytetów biznesowych.

## <a name="division-of-responsibility"></a>Podział odpowiedzialności
Należy zrozumieć podział odpowiedzialności pomiędzy użytkownikiem a firmą Microsoft. W środowisku lokalnym, jesteś właścicielem całego stosu, ale po przeniesieniu do chmury pewne obowiązki przesyłanie do firmy Microsoft. Następujące macierzy odpowiedzialność przedstawiono obszary stosu w danym wdrożeniu rozwiązania SaaS, PaaS i IaaS, które ponosisz odpowiedzialność za i firmy Microsoft jest odpowiedzialna.

![Odpowiedzialność za stref][2]

Dla wszystkich typów wdrożeń chmury jesteś właścicielem swoich danych i tożsamości. Odpowiedzialność za ochronę bezpieczeństwa danych i tożsamości, zasoby lokalne i chmurowe składniki Ty określasz, (która zależy od typu usługi).

Obowiązki, które są zawsze zachowywane przez użytkownika, niezależnie od tego typu wdrożenia, są następujące:

- Dane
- Punkty końcowe
- Konto
- Zarządzanie dostępem

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>Korzyści związane z bezpieczeństwem rozwiązania paas, model usług w chmurze
Korzystając z tej samej macierzy odpowiedzialność, Przyjrzyjmy się korzyści związane z bezpieczeństwem, wdrożenia usługi PaaS platformy Azure, czy w środowisku lokalnym.

![Korzyści związane z bezpieczeństwem rozwiązania paas][3]

Uruchamianie w dolnej części stosu infrastruktury fizycznej, Microsoft zmniejsza typowych zagrożeń i obowiązków. Ponieważ firmy Microsoft w chmurze jest stale monitorowane przez firmę Microsoft, jest trudny do ataków. Nie ma sensu osobie atakującej do prowadzenia firmy Microsoft w chmurze jako element docelowy. Chyba, że osoba atakująca ma dużo pieniędzy i zasobów, osoba atakująca jest prawdopodobne przejść do innego obiektu docelowego.  

W środku stosu nie ma żadnej różnicy między to wdrożenie PaaS i lokalne. Warstwa aplikacji oraz warstwa zarządzania kontem i dostępu będziesz mieć podobne ryzyka. W następnej sekcji kroki opisane w tym artykule firma Microsoft przeprowadzi Cię do najlepsze rozwiązania dotyczące eliminując lub zmniejszenia ryzyka.

W górnej części stosu, nadzór nad danymi i rights management, należy wykonać na jednym ryzyko, że może być ograniczona przez zarządzanie kluczami. (Zarządzanie kluczami omówiono najlepsze rozwiązania.) Podczas zarządzania kluczami odpowiada dodatkowych, masz obszarów to wdrożenie PaaS, która nie jest już konieczne zarządzanie nimi, dzięki czemu mogą przeprowadzić migrację zasobów do zarządzania kluczami.

Platforma Azure również zapewnia silną ochronę przed atakami DDoS przy użyciu różnych technologiach opartych na sieci. Wszystkie typy sieciowymi programami wykorzystującymi metody ochrony przed atakami DDoS jednak ich ograniczeń na zasadach na jedno łącze i na centrum danych. Aby uniknąć wpływu ataków DDoS, możesz korzystać z zalet platformy Azure core chmury możliwości dzięki któremu można szybko i automatyczne skalowanie w poziomie obrony przed atakami DDoS. Szczegółowo omówimy bardziej szczegółowo w jaki sposób można to zrobić w artykułach zalecane praktyki.

## <a name="modernizing-the-defenders-mindset"></a>Modernizowanie sposób myślenia programu defender
Dzięki temu wdrożenia Przyjdź na zmianę w danej metody ogólnej zabezpieczeń. Następuje przejście od konieczności kontrolowania wszystko samodzielnie na potrzeby współużytkowania odpowiedzialność firmy Microsoft.

Inny istotną różnicą między PaaS i tradycyjnych lokalnych wdrożeń jest nowy widok definiuje głównych zabezpieczeń obwodowych. W przeszłości zabezpieczeń obwodowych podstawowego środowiska lokalnego zostało sieci i większości projektów zabezpieczeń lokalnych sieć jest używana jako jego pivot głównych zabezpieczeń. Dla wdrożeń typu PaaS są lepiej byłoby, biorąc pod uwagę tożsamości jako głównych zabezpieczeń obwodowych.

## <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Przyjęcie zasad tożsamości jako głównych zabezpieczeń obwodowych
Jeden z pięć istotnych cech chmury obliczeniowej jest szeroki dostęp do sieci, co sprawia, że na interfejsach sieciowych myśl mniej istotne. Celem większości chmury obliczeniowej jest umożliwienie użytkownikom dostęp do zasobów, niezależnie od lokalizacji. Dla większości użytkowników ich lokalizacji będzie zawarty w Internecie.

Na poniższej ilustracji przedstawiono, jak zabezpieczeń obwodowych powstał z obwód sieci obwodowej tożsamości. Zabezpieczenia staje się mniej o obronie sieci i więcej o obronie dane, a także zarządzanie zabezpieczeniami aplikacji i użytkowników. Główną różnicą jest chcesz wypychana bliżej zabezpieczeń, co ważne jest, aby Twoja firma.

![Tożsamość jako nową strefą zabezpieczeń][4]

Początkowo usług PaaS platformy Azure (na przykład ról sieć web i Azure SQL) podany niewielkiego lub żadnego tradycyjnej sieci obwodowej zabezpieczenia. Został rozumie, że celem elementu było łączyć się z Internetem (rola sieci web) i uwierzytelniania oferuje nową strefą (na przykład obiekt BLOB lub Azure SQL).

Rozwiązania w zakresie zabezpieczeń nowoczesnych przyjęto założenie, że osoba planująca atak złamała obwód sieci. W związku z tym defense nowoczesnych rozwiązań zostało przeniesione do tożsamości. Organizacje należy ustanowić oparta na tożsamości zabezpieczeń obwodowych przy użyciu silnego uwierzytelniania i autoryzacji higieny (najlepsze rozwiązania).

Zasady i wzorców dla sieci obwodowej były dostępne od dziesięcioleci. Natomiast w branży zawiera stosunkowo mniej środowisko przy użyciu tożsamości jako głównych zabezpieczeń obwodowych. Dzięki temu powiedział zakumulowaliśmy wystarczająco dużo doświadczenie w celu zapewnienia ogólne zalecenia są sprawdzone w polu, które dotyczą praktycznie wszystkie usługi PaaS.

Poniżej przedstawiono najlepsze rozwiązania dotyczące zarządzania obwodowej tożsamości.

**Najlepsze rozwiązanie**: Zabezpiecz swoje klucze i poświadczenia, aby zabezpieczyć wdrożenie PaaS.   
**Szczegóły**: Utraty kluczy i poświadczeń jest to powszechny problem. Umożliwia to scentralizowane rozwiązanie, których klucze i wpisy tajne są przechowywane w sprzętowych modułach zabezpieczeń (HSM). [Usługa Azure Key Vault](../key-vault/key-vault-whatis.md) zabezpiecza kluczy i wpisów tajnych, szyfrując klucze uwierzytelniania, klucze konta magazynu, klucze szyfrowania danych, pliki PFX i hasła, przy użyciu kluczy chronionych przez sprzętowe moduły zabezpieczeń.

**Najlepsze rozwiązanie**: Nie umieszczaj poświadczeń i innych wpisów tajnych w kodzie źródłowym lub GitHub.   
**Szczegóły**: Jedyną czynnością co gorsza, nie tracąc kluczy i poświadczeń jest nieautoryzowana osoba uzyskać do nich dostęp. Osoby atakujące mogą korzystać z bota technologie, aby znaleźć klucze i wpisy tajne przechowywane w repozytoriach kodu, takich jak GitHub. Nie umieszczaj klucza i wpisów tajnych w tych repozytoriów publicznych.

**Najlepsze rozwiązanie**: Ochrona interfejsów zarządzania sieci maszyny Wirtualnej na hybrydowego PaaS i usługi IaaS, za pomocą interfejsu zarządzania, która pozwala na zdalne zarządzanie te maszyny wirtualne bezpośrednio.   
**Szczegóły**: Zdalne zarządzanie protokoły, takie jak [SSH](https://en.wikipedia.org/wiki/Secure_Shell), [RDP](https://support.microsoft.com/kb/186607), i [komunikacji zdalnej programu PowerShell](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/enable-psremoting) mogą być używane. Ogólnie rzecz biorąc zaleca się, że nie zostanie włączone bezpośredniego dostępu zdalnego do maszyn wirtualnych z Internetu.

Jeśli to możliwe używaj alternatywne podejścia, takie jak korzystanie z wirtualnych sieci prywatnych w usłudze Azure virtual network. Jeśli alternatywnych metod nie są dostępne, upewnij się, że używasz złożonych haseł oraz korzystania z uwierzytelniania dwuskładnikowego (takie jak [usługi Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)).

**Najlepsze rozwiązanie**: Użyj silnego uwierzytelniania i autoryzacji platformy.   
**Szczegóły**: W usłudze Azure AD zamiast niestandardowych magazynów użytkownika, należy używać tożsamości federacyjnej. Korzystając z tożsamości federacyjnej, możesz korzystać z podejścia opartego na platformie i delegować Zarządzanie tożsamościami autoryzowanych do partnerów. To podejście tożsamości federacyjnej jest szczególnie ważne, gdy pracownicy są kończone, a informacje muszą być uwzględniane w wielu systemów tożsamości i autoryzacji.

Użyj dostarczone przez platformę mechanizmy uwierzytelniania i autoryzacji, zamiast kodu niestandardowego. Przyczyną jest to, że opracowanie kodu, uwierzytelnianie niestandardowe może prowadzić do występowania błędów. Większość deweloperów nie są ekspertami w dziedzinie zabezpieczeń i prawdopodobnie nie trzeba pamiętać precyzyjnie i Poznaj najnowsze osiągnięcia w sekcji uwierzytelnianie i autoryzację. Komercyjne kodu (na przykład od firmy Microsoft) jest często często przeglądowi zabezpieczeń.

Użyj uwierzytelniania dwuskładnikowego. Uwierzytelnianie dwuskładnikowe jest bieżące normy, do uwierzytelniania i autoryzacji, ponieważ eliminuje luki bezpieczeństwa związane z typów uwierzytelniania nazwy użytkownika i hasła. Dostęp do usług przeznaczonych dla klientów i interfejsy zarządzania platformy Azure (portal/zdalnego programu PowerShell) powinien być zaprojektowana i skonfigurowana do używania [usługi Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md).

Użyj standardowych protokołów uwierzytelniania, takich jak OAuth2 i protokołu Kerberos. Protokoły te zostały często elementu równorzędnego przeglądu i prawdopodobnie są implementowane jako część platformy bibliotek uwierzytelniania i autoryzacji.

## <a name="use-threat-modeling-during-application-design"></a>Użyj podczas projektowania aplikacji do modelowania zagrożeń
Microsoft [cyklu projektowania zabezpieczeń](https://www.microsoft.com/en-us/sdl) Określa, że w procesie nazywanym zagrożeń modelowania w fazie projektowania należy zaangażowaniu zespołów. Aby ułatwić ten proces, firma Microsoft opracowała [narzędzie do modelowania zagrożeń SDL](../security/azure-security-threat-modeling-tool.md). Modelowanie projektu aplikacji i wyliczania [STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) zagrożenia we wszystkich relacji zaufania może przechwycić granice projektowania błędy na wczesnym etapie.

Poniższa lista zagrożeń STRIDE i zapewnia niektóre przykład środki zaradcze, które korzystają z funkcji platformy Azure. Te czynniki nie będą działać w każdej sytuacji.

| Przed zagrożeniami | Właściwości zabezpieczeń | Potencjalne środki zaradcze platformy Azure |
| --- | --- | --- |
| Fałszowanie zawartości | Authentication | Wymaga połączenia HTTPS. |
| Manipulowanie | Integralność | Sprawdzanie poprawności certyfikatów SSL. |
| odrzucenie | Weryfikacja tożsamości | Włączanie usługi Azure [monitorowania i diagnostyki](https://docs.microsoft.com/azure/architecture/best-practices/monitoring). |
| Ujawnienie informacji | Poufność | Szyfruj poufne dane podczas przechowywania przy użyciu [usługi certyfikatów](https://docs.microsoft.com/rest/api/appservice/certificates). |
| Odmowa usługi | Dostępność | Monitoruj metryki wydajności dla potencjalnych warunków typu "odmowa usługi". Implementuje filtry połączenia. |
| Podniesienie uprawnień | Autoryzacja | Użyj [Privileged Identity Management](../active-directory/privileged-identity-management/subscription-requirements.md). |

## <a name="develop-on-azure-app-service"></a>Programuj w usłudze Azure App Service
[Usługa Azure App Service](../app-service/overview.md) jest PaaS, oferty, która umożliwia tworzenie aplikacji internetowych i mobilnych dla dowolnej platformy lub urządzenia i nawiązać połączenie z danymi przechowywanymi w chmurze lub lokalnie. Usługa App Service obejmuje sieci web i mobilnych możliwości, które były wcześniej dostępne oddzielnie jako usługi Azure Websites i Azure Mobile Services. Obejmuje ona także nowe funkcje automatyzacji procesów biznesowych i hostowania interfejsów API w chmurze. Jako pojedyncza zintegrowana usługa App Service oferuje bogaty zestaw funkcji do sieci web, mobilnych i scenariuszy integracji.

Poniżej przedstawiono najlepsze rozwiązania dotyczące korzystania z usługi App Service.

**Najlepsze rozwiązanie**: [Uwierzytelnianie za pomocą usługi Azure Active Directory](../app-service/overview-authentication-authorization.md).   
**Szczegóły**: Usługa App Service udostępnia usługę OAuth 2.0 dla dostawcy tożsamości. OAuth 2.0 koncentruje się na prostotę dewelopera klienta przy jednoczesnym zapewnieniu przepływów określonych autoryzacji dla aplikacji sieci web, aplikacji klasycznych i telefony komórkowe. Usługa Azure AD używa protokołu OAuth 2.0 umożliwia autoryzowanie dostępu dla urządzeń przenośnych i aplikacji sieci web.

**Najlepsze rozwiązanie**: Ograniczanie dostępu na podstawie musi znać i zasad zabezpieczeń w usłudze najniższych uprawnień.   
**Szczegóły**: Ograniczanie dostępu jest dla organizacji, które chcesz wymuszać zasady zabezpieczeń dostępu do danych. RBAC umożliwia przypisywanie uprawnień do użytkowników, grup i aplikacji w określonym zakresie. Aby dowiedzieć się więcej o udzielenie użytkownikom dostępu do aplikacji, zobacz [wprowadzenie do zarządzania dostępem](../role-based-access-control/overview.md).

**Najlepsze rozwiązanie**: Chroń klucze.   
**Szczegóły**: Usługa Azure Key Vault pomaga chronić klucze kryptograficzne i wpisy tajne, które aplikacje i usługi w chmurze Użyj. Usługa Key Vault można szyfrować klucze i wpisy tajne (takie jak klucze uwierzytelniania, klucze konta magazynu, klucze szyfrowania danych. Pliki PFX oraz hasła) przy użyciu kluczy chronionych przez sprzętowe moduły zabezpieczeń (HSM). W celu zapewnienia dodatkowego bezpieczeństwa możesz zaimportować lub wygenerować klucze w modułach HSM. Zobacz [usługi Azure Key Vault](../key-vault/key-vault-whatis.md) Aby dowiedzieć się więcej. Usługi Key Vault umożliwia także zarządzanie certyfikaty protokołu TLS z automatycznego odnawiania.

**Najlepsze rozwiązanie**: Ogranicz przychodzące źródłowych adresów IP.   
**Szczegóły**: [Środowisko App Service Environment](../app-service/environment/intro.md) ma funkcji integracji sieci wirtualnej, która pomaga ograniczyć przychodzące źródłowych adresów IP za pomocą sieciowych grup zabezpieczeń. Sieci wirtualnych umożliwia umieszczenie zasobów platformy Azure w sieci-internet, podlegającego routingowi, która umożliwia kontrolę dostępu do. Aby dowiedzieć się więcej, zobacz [Integrowanie aplikacji z siecią wirtualną platformy Azure](../app-service/web-sites-integrate-with-vnet.md).

**Najlepsze rozwiązanie**: Monitoruj stan zabezpieczeń środowiska usługi App Service.   
**Szczegóły**: Usługa Azure Security Center do monitorowania środowiska usługi App Service. Gdy usługa Security Center zidentyfikuje potencjalnych luk w zabezpieczeniach, tworzy [zalecenia](../security-center/security-center-virtual-machine-recommendations.md) który prowadzi przez proces konfigurowania wymaganych kontrolek.

> [!NOTE]
> Monitorowanie usługi App Service jest dostępna w wersji zapoznawczej i jest dostępna tylko w systemie [w warstwie standardowa](../security-center/security-center-pricing.md) usługi Security Center.
>
>

## <a name="install-a-web-application-firewall"></a>Zainstaluj zaporę aplikacji sieci web
Aplikacje internetowe coraz częściej stają się obiektami złośliwych ataków wykorzystujących znane luki w zabezpieczeniach. Wśród nich często zdarzają się np. ataki polegające na iniekcji SQL i ataki z użyciem skryptów wykorzystywanych w wielu witrynach. Zapobieganie takim atakom z poziomu kodu aplikacji może być trudne. Może też wymagać rygorystycznego przestrzegania harmonogramu konserwacji, poprawek i monitorowania na wielu warstwach topologii aplikacji. Scentralizowana zapora aplikacji internetowej ułatwia zarządzanie zabezpieczeniami oraz zapewnia lepszą ochronę administratorów aplikacji przed zagrożeniami i intruzami. Zapora aplikacji internetowej może reagować na zagrożenia bezpieczeństwa szybciej — poprzez wdrażanie poprawek zapobiegających wykorzystaniu znanych luk w zabezpieczeniach w centralnej lokalizacji zamiast w poszczególnych aplikacjach internetowych. Istniejące bramy Application Gateway można łatwo przekonwertować na bramę Application Gateway obsługującą zaporę aplikacji internetowej.

[Zapora aplikacji sieci Web (WAF)](../application-gateway/waf-overview.md) jest funkcją usługi Application Gateway, która zapewnia scentralizowaną ochronę aplikacji sieci web z typowe luki w zabezpieczeniach i luk w zabezpieczeniach. Zapora aplikacji sieci Web na podstawie reguł z [Open Web Application zabezpieczeń projektu () podstawowych zestawów reguł OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) wersji 3.0 lub 2.2.9.

## <a name="monitor-the-performance-of-your-applications"></a>Monitorowanie wydajności aplikacji
Monitorowanie to proces zbierania i analizowania danych w celu ustalania wydajności, kondycji i dostępności aplikacji. Skuteczna strategia monitorowania pomaga w zrozumieniu szczegółów działania składników aplikacji. Pomaga zwiększyć czas aktywności przez powiadomienie o istotnych kwestiach, dzięki czemu można je rozwiązać, zanim obejmą. Ułatwia on także wykrywać nieprawidłowe stany, które mogą być związane z zabezpieczeniami.

Użyj [usługi Azure Application Insights](https://azure.microsoft.com/documentation/services/application-insights) monitorowanie dostępności, wydajności i użycia aplikacji, czy jest hostowana w chmurze lub lokalnie. Za pomocą usługi Application Insights, możesz szybko identyfikować i diagnozować błędy w aplikacji bez oczekiwania na zgłoszenie ich przez użytkownika. Dzięki zbieranym informacjom można podejmować świadome decyzje w zakresie konserwacji i ulepszeń aplikacji.

Usługa Application Insights ma szeroką gamę narzędzi do interakcji z danymi, które zbiera. Usługa Application Insights przechowuje dane we wspólnym repozytorium. Może korzystać z udostępnionego funkcje, takie jak alerty, pulpitów nawigacyjnych i szczegółowa analiza za pomocą języka zapytania Kusto.



## <a name="next-steps"></a>Kolejne kroki
W tym artykule skupiliśmy się na korzyści związane z bezpieczeństwem wdrożenia usługi PaaS platformy Azure i najlepsze rozwiązania dotyczące aplikacji w chmurze. Dowiedz się, zalecane praktyki zabezpieczanie aplikacji PaaS w sieci web i rozwiązań mobilnych przy użyciu określonych usług platformy Azure. Rozpoczniemy od usługi Azure App Service, Azure SQL Database i Azure SQL Data Warehouse i Azure Storage. W miarę udostępniania artykuły dotyczące zalecanych rozwiązań dotyczących innych usług platformy Azure będzie świadczona łącza na poniższej liście:

- [Azure App Service](security-paas-applications-using-app-services.md)
- [Azure SQL Database i Azure SQL Data Warehouse](security-paas-applications-using-sql.md)
- [Azure Storage](security-paas-applications-using-storage.md)
- Azure Cache for Redis
- Azure Service Bus
- Zapory aplikacji sieci Web

Zobacz [zabezpieczeń platformy Azure najlepsze rozwiązania i wzorce](security-best-practices-and-patterns.md) dla więcej najważniejsze wskazówki dotyczące zabezpieczeń do użycia podczas one projektowanie, wdrażanie i zarządzanie rozwiązań w chmurze, korzystając z platformy Azure.

Do dyspozycji więcej ogólnych informacji na temat zabezpieczeń platformy Azure i powiązane usługi firmy Microsoft są następujące zasoby:
* [Blog zespołu usługi Azure Security](https://blogs.msdn.microsoft.com/azuresecurity/) — aktualne instrukcje dotyczące najnowszych zabezpieczeń platformy Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) — w przypadku, gdy luk w zabezpieczeniach firmy Microsoft, w tym problemów z platformą Azure, mogą zostać zgłoszone lub za pośrednictwem poczty e-mail do secure@microsoft.com

<!--Image references-->
[1]: ./media/security-paas-deployments/advantages-of-cloud.png
[2]: ./media/security-paas-deployments/responsibility-zones.png
[3]: ./media/security-paas-deployments/advantages-of-paas.png
[4]: ./media/security-paas-deployments/identity-perimeter.png

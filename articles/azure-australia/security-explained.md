---
title: Opis zabezpieczeń platformy Azure (Australia)
description: Najbardziej zadawane informacje o regionach australijskich i spełniające określone wymagania australijskich zasad i przepisów obowiązujących dla instytucji rządowych.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 79f5773cf6f4906f93a5b95ce5c042da09c789fc
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575466"
---
# <a name="azure-australia-security-explained"></a>Opis zabezpieczeń platformy Azure (Australia)

W tym artykule opisano niektóre często zadawane pytania i obszary interesów dla instytucji rządowych Australii, które badają się w programie, na przykład w celu zaprojektowania i wdrożenia do Microsoft Azure Australii.

## <a name="irap-and-certified-cloud-services-list-documents"></a>IRAP i certyfikowane dokumenty listy Cloud Services

Australia cybernetycznymi Security Centre (ACSC) zawiera listę certyfikatów, raport o certyfikacji i Podręcznik klienta dotyczące usługi, gdy zostanie ona dodana do listy certyfikowanych Cloud Services (CCSL).

Firma Microsoft jest obecnie wymieniona w witrynie CCSL dla platformy Azure, pakietu Office 365 i programu Dynamics 365 CRM.

Firma Microsoft udostępnia naszym klientom i partnerom ACSC dokumenty dotyczące inspekcji, oceny i certyfikacji na stronie specyficznej dla Australii w [portalu zaufania usługi firmy Microsoft](https://aka.ms/au-irap).

## <a name="dissemination-limiting-markers-and-protected-certification"></a>Wydawanie znaczników ograniczających i chronionych certyfikatów

Proces posiadania systemów, w tym usług w chmurze, zatwierdzonych do użytku przez organizacje rządowe, jest zdefiniowany w [podręczniku zabezpieczeń informacji (ISM)](https://acsc.gov.au/infosec/ism/) , który jest produkowany i publikowany przez ACSC. ACSC jest jednostką w Australii sygnalizującej Dyrektoriat (ASD), która jest odpowiedzialna za bezpieczeństwo cybernetycznymi i certyfikację w chmurze.

Proces zatwierdzania obejmuje dwie etapy:

1. **Ocena zabezpieczeń (IRAP)** : Proces polegający na tym, że zarejestrowani Specjaliści oceniają systemy, usługi i rozwiązania przed kontrolami technicznymi w ISM i oceniają, czy formanty zostały zaimplementowane efektywnie. Ocena określa również wszelkie określone zagrożenia dla urzędu zatwierdzania, które należy wziąć pod uwagę przed wystawieniem zatwierdzenia do działania (ATO).

1. **Zatwierdzenie do działania**: Proces, w którym starszy oficer agencji rządowej w sposób nieuznawany i akceptuje pozostałe ryzyko systemu do informacji przetwarzanych, sklepów i komunikacji. Danymi wejściowymi tego procesu jest ocena zabezpieczeń.

Ocena usług platformy Azure na poziomie CHRONIONYm pozwala określić, że implementacja kontroli zabezpieczeń wymagane do przechowywania i przetwarzania chronionych i poniżej danych zostało potwierdzone jako gotowe i działa efektywnie.

## <a name="australian-data-classification-changes"></a>Zmiany klasyfikacji danych australijskich

W dniu 1 października 2018 Departament publiczny ogłosił publicznie ogłoszone zmiany w środowisku ochrony zabezpieczeń (PSPF), w tym w odniesieniu do [systemu informacji poufnych i](https://www.protectivesecurity.gov.au/information/sensitive-classified-information/Pages/default.aspx)niejawnych.

![Zrewidowane klasyfikacje PSPF](media/pspf-classifications.png)

Te zmiany mają wpływ wszystkie agencje i organizacje australijskie działające na PSPF. Podstawowa zmiana wpływająca na nasze bieżące certyfikaty IRAP/CCSL polega na tym, że obecne ograniczenia dotyczące ograniczania upowszechniania (DLMs) zostały wycofane. URZĘDNIK: Oznakowanie poufne zastępuje różne DLMs używane do ochrony poufnych informacji. Wprowadzono również trzy znaczniki zarządzania informacjami, które mogą być stosowane do wszystkich oficjalnych informacji na wszystkich poziomach czułości i klasyfikacji. CHRONIONa Klasyfikacja pozostaje niezmieniona.

Termin "niesklasyfikowany" jest usuwany z nowego systemu i termin "unoficjalny" jest stosowany do informacji niezwiązanych z rządem, chociaż nie wymaga formalnego oznakowania.

## <a name="choose-an-azure-region-for-official-sensitive-and-protected-workloads"></a>Wybierz region platformy Azure dla URZĘDNIKa: Wrażliwe i chronione obciążenia

URZĘDNIK platformy Azure: Poufne i chronione certyfikowane usługi są wdrażane w wszystkich czterech regionach australijskiego centrum danych: Australia Wschodnia, Australia Południowo-Wschodnia, Australia Środkowa i Australia Środkowa 2. Raport o certyfikacji wydany przez ACSC zaleca wdrożenie chronionych danych w regionach Azure Government w Canberra, jeśli usługa jest tam dostępna. Aby uzyskać więcej informacji na temat chronionych certyfikowanych usług platformy Azure, zobacz [stronę Australia w portalu zaufania usługi](https://aka.ms/au-irap).

>[!NOTE]
>Firma Microsoft zaleca, aby dane rządowe wszystkich sensitivities i klasyfikacji zostały wdrożone w regionach Australii Środkowej i Australii środkowej 2, ponieważ są one zaprojektowane i obsługiwane specjalnie dla potrzeb instytucji rządowych.

Aby uzyskać więcej informacji na temat specjalnego charakteru regionów australijskich platformy Azure, zobacz [regiony platformy Azure w Australii środkowej](https://azure.microsoft.com/global-infrastructure/australia/).

## <a name="how-microsoft-separates-classified-and-official-data"></a>Jak firma Microsoft dzieli dane klasyfikowane i oficjalne

Firma Microsoft współpracuje z platformą Azure i pakietem Office 365 w Australii, tak jakby wszystkie dane były poufne lub sklasyfikowane, co sprawia, że nasze środki kontroli zabezpieczeń są na tym wysokim pasku.

Infrastruktura, która obsługuje platformę Azure, może obsługiwać dane wielu klasyfikacji. Ponieważ założono, że dane klienta są klasyfikowane, są stosowane odpowiednie środki kontroli. Firma Microsoft przyjęła podstawowe stan zabezpieczeń dla naszych usług, które są zgodne z wymaganiami PSPF do przechowywania i przetwarzania chronionych informacji klasyfikowanych.

Aby zapewnić naszym klientom, że jedna dzierżawa na platformie Azure nie jest narażona na ryzyko od innych dzierżawców, firma Microsoft implementuje kompleksową kontrolę nad obroną.

Poza możliwościami wdrożonymi w ramach platformy Microsoft Azure, dodatkowe konfigurowalne formanty klienta, takie jak szyfrowanie za pomocą kluczy zarządzanych przez klienta, możliwość zagnieżdżonej wirtualizacji i dostęp administracyjny just in Time, mogą dodatkowo ograniczyć ryzyko. W regionach Azure Government Australii w Canberra, proces dla formalnej listy dozwolonych dotyczy tylko instytucji rządowych Australii i Nowej Zelandii. Ta chmura społeczności zapewnia dodatkowe gwarancje organizacjom, które są wrażliwe na ryzyko związane z współdzierżawcą.

Raport CHRONIONEj certyfikacji Microsoft Azure potwierdza, że te kontrolki są skuteczne do przechowywania i przetwarzania chronionych danych klasyfikowanych oraz ich izolacji.

## <a name="relevance-of-irapccsl-to-state-government-and-critical-infrastructure-providers"></a>Znaczenie IRAP/CCSL dla dostawców stanu i infrastruktury krytycznej

Wielu instytucji rządowych i krytycznych dostawców infrastruktury, w których są spełnione wymagania instytucji rządowych do ich zasad zabezpieczeń i struktury gwarancji. Organizacje te również obsługują OFICJALNEgo URZĘDNIKa: Poufne i pewną ilość chronionych danych klasyfikowanych — od ich interakcji z federalnym rządem lub własnym prawem.

Australijski rząd jest coraz bardziej ukierunkowany na zasady i ustawodawstwo dotyczące ochrony danych niezwiązanych z administracją rządową, które w zasadniczy sposób wpływają na bezpieczeństwo i gospodarczą dobrobyt Australii. W takim przypadku regiony platformy Azure w Australii i certyfikat CCSL mają zastosowanie do wszystkich tych branż.

![Sektory infrastruktury krytycznej](media/nci-sectors.png)

Certyfikaty firmy Microsoft wykazują, że usługi platformy Azure zostały poddane gruntownej, rygorystycznej i formalnej ocenie ochrony bezpieczeństwa i zostały zatwierdzone do obsługi takich bardzo poufnych danych.

## <a name="location-and-control-of-microsoft-data-centres"></a>Lokalizacja i kontrola centrów danych firmy Microsoft

Jest to obowiązkowe wymaganie dla instytucji rządowych i infrastruktury krytycznej, aby jawnie znać lokalizację i własność centrum danych w ramach usług Cloud Services przetwarzających swoje dane. Firma Microsoft jest unikatowym dostawcą usług w chmurze w celu udostępniania szczegółowych informacji na temat tych lokalizacji i własności.

Regiony platformy Azure w Australii (Australia Środkowa i Australia Środkowa 2) działają w ramach udogodnień centrów danych przechwytywania. Własność centrów danych przepustek to australijskie kontrolowane przez 48% własność od Superannuation Corporation, 48% własność z Infratil (Nowa Zelandia, Podwójna Australia i Nowa Zelandia giełdowa Fundusz zasobów) i 4% australijskiego zarządzania. 

Zarządzanie centrami danych przechwytywania przestawnego ma gwarancje umowne w stosunku do instytucji rządowych Australii, które ograniczają przyszły transfer własności i kontroli. Przejrzystość łańcucha dostaw i własności przez partnerstwo firmy Microsoft z centrami danych przejmowania danych jest zgodne z zasadami całościowej [strategii hostingu](https://www.dta.gov.au/our-projects/whole-government-hosting-strategy) oraz definicją zatwierdzonego suwerennego centrum danych.

## <a name="azure-services-that-are-included-in-the-current-ccsl-certification"></a>Usługi platformy Azure, które są uwzględnione w bieżącym certyfikacji CCSL

W czerwcu 2017 ACSC certyfikowane 41 usługi platformy Azure do przechowywania i przetwarzania danych niesklasyfikowanych: Poziom DLM. W kwietniu 2018, 24 z tych usług były certyfikowane za chronione dane klasyfikowane.

Dostępność usług platformy Azure certyfikowanych ACSC w ramach naszych regionów platformy Azure w Australii jest następująca (usługi pokazane w pogrubieniu są certyfikowane na poziomie CHRONIONYm).

|Regiony platformy Azure w Australii środkowej|Usługi nieregionalne i inne regiony|
|---|---|
|API Management, App Gateway, Usługi aplikacji, **Automation**, **Azure Portal**, **Backup**, **Batch**, **Cloud Services**, Cosmos DB, Event Hubs, **ExpressRoute**, HDInsight, **Key Vault**, Load Balancer, log Analiza, **uwierzytelnianie wieloskładnikowe**, Redis Cache, **Menedżer zasobów**, **Service Bus**, **Service Fabric**, **Site Recovery**, **SQL Database**, **Magazyn**, Traffic Manager, **wirtualne Maszyny**, **Virtual Network**, **VPN Gateway**|**Azure Active Directory**, CDN, Data Catalog, **Import Export**, **Information Protection**, **IOT Hub**, Machine Learning, Media Services, **Notification Hubs**, Power BI, **Scheduler**, **Centrum zabezpieczeń**, Wyszukaj, Stream Analytics|
|

Firma Microsoft publikuje [przegląd Microsoft Azure zgodności](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/44/Microsoft%20Azure%20Compliance%20Offerings.pdf) , który zawiera listę wszystkich usług w zakresie dla wszystkich globalnych, rządowych, branżowych i regionalnych procesów zgodności i oceny, w których odbywa się platforma Azure, w tym IRAP/CCSL.

## <a name="azure-service-not-listed-or-assessed-at-a-lower-level-than-needed"></a>Usługa platformy Azure niewymieniona lub oceniona na niższym poziomie niż wymagana

Usługi, które nie są certyfikowane lub których certyfikat został certyfikowany: Poufny, ale nie na poziomie CHRONIONYm, można używać razem z lub jako część rozwiązania obsługującego chronione dane, które są następujące:

- Przechowywanie i przetwarzanie chronionych danych nie jest zaszyfrowana lub
- Ukończono ocenę ryzyka i zatwierdzono usługę do samodzielnego przechowywania chronionych danych.

Możesz użyć usługi, która nie jest uwzględniona w CCSL do przechowywania i przetwarzania danych URZĘDOWych, ale usługa ISM wymaga, aby przed wprowadzeniem lub odnowieniem kontraktu z dostawcą usług w chmurze poinformować o tym ACSC.

Każda usługa, która jest używana przez Agencję na potrzeby chronionych obciążeń, musi być oceniona i zatwierdzona zgodnie z procesami opisanymi w procesie oceny IRAP i zarządzanej przez Agencję, w [strategii bezpiecznego chmury usługi DTA](https://www.dta.gov.au/files/cloud-strategy/secure-cloud-strategy.pdf).

![Zastrzeżony proces certyfikacji strategii chmurowej DTA](media/certification.png)

Firma Microsoft stale ocenia nasze usługi, aby upewnić się, że platforma jest bezpieczna i dostosowana do celu w przypadku Australii dla instytucji rządowych. Skontaktuj się z firmą Microsoft, jeśli potrzebujesz pomocy w przypadku usługi, która nie jest obecnie na CCSL na poziomie CHRONIONYm.

Ponieważ firma Microsoft ma wiele usług certyfikowanych na CCSL w przypadku niesklasyfikowanych DLM i chronionych klasyfikacji, firma ISM wymaga, aby przeprowadzili IRAPą ocenę naszych usług co najmniej co dwa lata. Firma Microsoft podejmuje coroczną ocenę, która stanowi również okazję do uwzględnienia dodatkowych usług.

## <a name="certified-protected-gateway-in-azure"></a>Certyfikowana Brama CHRONIONa na platformie Azure

Firma Microsoft nie działa z certyfikatem Secure Internet Gateway (SIG) certyfikowanym dla instytucji rządowych ze względu na ograniczenia dotyczące liczby SIGs dozwolonych w ramach programu konsolidacji bramy. Oczekiwane i niezbędne możliwości tego SIG można skonfigurować w ramach Microsoft Azure.

Za pośrednictwem CHRONIONEj certyfikacji usług platformy Azure ACSC ma konkretne zalecenia dla agencji do łączenia się z platformą Azure i wdrażania segmentacji sieci między domenami zabezpieczeń, na przykład między CHRONIONYm a internetowym. Zalecenia te obejmują używanie sieciowych grup zabezpieczeń, zapór i wirtualnych sieci prywatnych. ACSC zaleca korzystanie z urządzenia bramy wirtualnej. Na platformie Azure dostępne są kilka urządzeń wirtualnych, które mają fizyczny odpowiednik na liście produktów ocenionych na serwerze ASD lub zostały ocenione względem profilów ochrony wspólnych kryteriów i są one wymienione w portalu wspólnych kryteriów. Te produkty są wzajemnie uznawane przez narzędzie ASD jako Sygnatariusz o wspólnych ustaleniach dotyczących rozpoznawania kryteriów.

Firma Microsoft wygenerowała wskazówki dotyczące wdrażania funkcji opartych na platformie Azure, które zapewniają funkcje zabezpieczeń wymagane do ochrony granicy między różnymi domenami zabezpieczeń, które w związku z tym łączą się z certyfikowanym SIG. Wielu partnerów może pomóc w projektowaniu i wdrażaniu tych możliwości, a wiele rozwiązań partnerskich jest dostępnych w taki sam sposób.

## <a name="security-clearances-and-citizenship-of-microsoft-support-personnel"></a>Prawa do bezpieczeństwa i obywatelstwo personelu pomocy technicznej firmy Microsoft

Firma Microsoft zarządza naszych usług globalnie z osłanianym i przeszkolonym personelem zabezpieczeń. Pracownicy, którzy mają nieeskortowy dostęp fizyczny do Udogodnień w Sydney i Melbourne, posiadają australijskie prawa do bezpieczeństwa dla instytucji rządowych. Personel w regionach Australii Środkowej i Australii środkowej 2 ma minimum przed sprawdzeniem 1 (NV1) (odpowiednio do danych TAJNych). Te wymagania dotyczące rozliczeń zapewniają klientom wysoką pewność, że personel w centrach danych działający na platformie Azure jest wysoce godny zaufania.

Firma Microsoft ma zerowe zasady dostępu z dostępem przyznanym przez system "just in Time" i wystarczą do użycia na podstawie kontroli dostępu opartej na rolach. W większości przypadków Administratorzy nie potrzebują dostępu do danych klientów ani uprawnień do nich w celu rozwiązywania problemów i utrzymania usługi. Duże stopnie automatyzacji i wykonywanie skryptów zadań związanych z wykonywaniem zdalnego Negate potrzebę bezpośredniego dostępu do danych klienta.

Departament generalny prawnik stwierdził, że zasady i procedury zabezpieczeń personelu firmy Microsoft w ramach platformy Azure są zgodne z intencją dostępu PSPF do przepisów dotyczących oprogramowania INFOSEC-9.

## <a name="store-international-traffic-of-arms-regulations-itar-or-export-administration-regulations-ear-data"></a>Przechowuj międzynarodowy ruch (ITAR) lub wyeksportuj dane rozporządzeń administracyjnych

Kontrolki techniczne na platformie Azure, które pomagają klientom sprostania ich zobowiązaniom do danych z kontroli eksportu, są takie same na platformie Azure. Ważne jest, że nie ma formalnej struktury oceny i certyfikacji dla danych kontrolowanych eksportem.

W przypadku Azure Government i pakietu Office 365 dla instytucji rządowych Stanów Zjednoczonych firma Microsoft udostępnia dodatkowe środki na umowne i procesy, które są przeznaczone dla klientów, którzy podlegają kontrolom eksportowym. Te dodatkowe klauzule umowne i gwarantowane wsparcie Narodowe w Stanach Zjednoczonych oraz administrację regionami świadczenia usługi Azure nie są przeznaczone dla Australii.

Nie oznacza to, że platformy Azure w Australii nie można używać na potrzeby ITAR/KOLCZYKów, ale musisz jasno zrozumieć ograniczenia narzucone na podstawie licencji eksportowej. Przed użyciem platformy Azure do przechowywania tych danych należy również zaimplementować dodatkowe zabezpieczenia. Na przykład może być konieczne:

- Przynależność do kompilacji jako atrybut do Azure Active Directory.
- Użyj Azure Information Protection, aby wymusić reguły szyfrowania dla danych i ograniczyć je tylko do USA, a wszelkie inne narodowe są zawarte w licencji eksportowej.
- Szyfruj wszystkie dane lokalnie przed ich zapisaniem na platformie Azure przy użyciu klucza klienta lub Zachowaj własny klucz dla danych ITAR.

Ponieważ ITAR nie jest formalnym certyfikatem, należy zapoznać się z ograniczeniami i ograniczeniami związanymi z licencją eksportowania. Następnie możesz wykonać odpowiednie działania w systemie Azure, aby spełnić te wymagania. W takim przypadku jednym z problemów, które należy wziąć pod uwagę, jest dostęp przez naszych inżynierów, którzy mogą nie być obywatelami zatwierdzonymi na licencji eksportowej.

## <a name="next-steps"></a>Następne kroki

 W przypadku konfiguracji i implementacji łączności sieci VPN z usługą Azure Australia, która jest zgodna z usługą ISM, zobacz [azure VPN Gateway](vpn-gateway.md).

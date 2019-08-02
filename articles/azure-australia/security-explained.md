---
title: Opis zabezpieczeń platformy Azure (Australia)
description: Najbardziej zadawane informacje o regionach australijskich i spełniające określone wymagania australijskich zasad i przepisów obowiązujących dla instytucji rządowych.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 212d14e31c152e50c216f2f34fb225c29fe054b5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571434"
---
# <a name="azure-australia-security-explained"></a>Opis zabezpieczeń platformy Azure (Australia)

W tym artykule opisano niektóre typowe pytania i obszary istotne w przypadku instytucji rządowych Australii, które badają, projektują i wdrażają do Microsoft Azure Australii.

## <a name="irap-and-certified-cloud-services-list-ccsl-documents"></a>IRAP i certyfikowane dokumenty listy Cloud Services (CCSL)

Australia cybernetycznymi Security Centre (ACSC) zawiera listę certyfikatów, raport o certyfikacji i Podręcznik klienta dotyczące usługi, gdy zostanie ona dodana do CCSL.

Firma Microsoft jest obecnie wymieniona w witrynie CCSL dla platformy Azure, pakietu Office 365 i programu Dynamics 365 CRM.

Firma Microsoft udostępnia naszym klientom i partnerom ACSC dokumenty dotyczące inspekcji, oceny i certyfikacji na stronie specyficznej dla Australii w [portalu zaufania usługi firmy Microsoft](https://aka.ms/au-irap).

## <a name="dissemination-limiting-markers-dlm-and-protected-certification"></a>DLM () i chronione certyfikaty

Proces posiadania systemów, w tym usług w chmurze, zatwierdzonych do użytku przez organizacje rządowe, jest zdefiniowany w [podręczniku zabezpieczeń informacji (ISM)](https://acsc.gov.au/infosec/ism/) produkowanym i publikowanym przez australijskie Centrum zabezpieczeń CYBERNETYCZNYMI (ACSC). Australia cybernetycznymi Security Centre (ACSC) jest jednostką w ramach programu ASD odpowiedzialną za cybernetycznymi zabezpieczenia i certyfikację w chmurze.

Proces zatwierdzania obejmuje dwie etapy:

1. Ocena zabezpieczeń (IRAP) — proces polegający na tym, że zarejestrowani Specjaliści oceniają systemy, usługi i rozwiązania względem kontroli technicznej w ISM i oceniają, czy formanty zostały zaimplementowane efektywnie. Ocena określa również wszelkie określone zagrożenia dla urzędu zatwierdzania, które należy wziąć pod uwagę przed wystawieniem zatwierdzenia do działania (ATO).

1. Zatwierdzenie do działania — proces, w którym starszy oficer agencji rządowej w sposób nieuznawany i akceptuje pozostałe ryzyko systemu do informacji przetwarzanych, sklepów i komunikacji.  Danymi wejściowymi tego procesu jest ocena zabezpieczeń.

Ocena usług platformy Azure na poziomie CHRONIONYm pozwala określić, że implementacja kontroli zabezpieczeń wymagane do przechowywania i przetwarzania chronionych i poniżej danych zostało potwierdzone jako gotowe i działa efektywnie.

## <a name="australian-data-classification-changes"></a>Zmiany klasyfikacji danych australijskich

Od 1 października 2018 Departamentu prawnika publicznie ogłoszone zmiany w środowisku ochrony zabezpieczeń (PSPF), a w odniesieniu do [systemu informacji poufnych](https://www.protectivesecurity.gov.au/information/sensitive-classified-information/Pages/default.aspx).

![Zrewidowane klasyfikacje PSPF](media/pspf-classifications.png)

Te zmiany mają wpływ na wszystkich agencje i organizacje australijskie działające na PSPF. Podstawowa zmiana, która ma wpływ na nasze bieżące certyfikaty IRAP/CCSL, to bieżące, ograniczone oznaczenia (DLM), które zostały wycofane. Urzędnik oznaczania **: Poufne** zastępuje różne DLMs używane do ochrony poufnych informacji. Wprowadzono również trzy znaczniki zarządzania informacjami, które mogą być stosowane do wszystkich oficjalnych informacji na wszystkich poziomach czułości i klasyfikacji. **Chroniona** Klasyfikacja pozostaje niezmieniona.

Niesklasyfikowany termin jest usuwany z nowego systemu, a termin nieoficjalny jest stosowany do informacji poza rządem, chociaż nie wymaga formalnego oznakowania.

## <a name="choosing-an-azure-region-for-my-official-sensitive-and-protected-workloads"></a>Wybieranie regionu świadczenia usługi Azure dla mojego URZĘDNIKa: Wrażliwe i chronione obciążenia

Urzędnik platformy **Azure: Poufne** i **chronione** certyfikowane usługi są wdrażane dla wszystkich czterech regionów australijskiego centrum danych (Australia Wschodnia, Australia Południowo-Wschodnia, Australia Środkowa i Australia Środkowa 2); Jednakże Raport certyfikacji wystawiany przez ACSC zaleca wdrożenie **chronionych** danych w regionach Azure government w Canberra, jeśli usługa jest tam dostępna. Bardziej szczegółowe informacje na temat **chronionych** certyfikowanych usług platformy Azure są dostępne [na stronie Australii w STP](https://aka.ms/au-irap).

>[!NOTE]
>Firma Microsoft zaleca, aby wszystkie sensitivities i klasyfikacje rządowe zostały wdrożone w regionach Australii Środkowej i Australii środkowej 2, ponieważ są one zaprojektowane i obsługiwane specjalnie dla potrzeb instytucji rządowych.

Więcej informacji na temat specjalnego charakteru regionów australijskich platformy Azure jest dostępnych w [regiony świadczenia usługi Azure — Australia Środkowa](https://azure.microsoft.com/global-infrastructure/australia/).

## <a name="how-microsoft-separates-classified-and-official-data"></a>Jak firma Microsoft dzieli dane klasyfikowane i oficjalne

Firma Microsoft współpracuje z platformą Azure i pakietem Office 365 w Australii, tak jakby wszystkie dane były poufne i/lub sklasyfikowane.

Infrastruktura obsługująca platformę Azure może potencjalnie obsługiwać dane wielu klasyfikacji.  Jednak zgodnie z naszym założeniami dane klienta są klasyfikowane, odpowiednie środki kontroli są stosowane w taki sposób. Firma Microsoft przyjęła podstawowe stan zabezpieczeń dla naszych usług, które są zgodne z wymaganiami PSPF do przechowywania i przetwarzania **chronionych** informacji klasyfikowanych.

Aby zapewnić naszym klientom, że jedna dzierżawa na platformie Azure nie jest narażona na ryzyko od innych dzierżawców, firma Microsoft implementuje kompleksowe kontrolki w zakresie obrony.

Poza możliwościami wdrożonymi w ramach platformy Microsoft Azure, dodatkowe konfigurowalne formanty klienta, takie jak szyfrowanie za pomocą kluczy zarządzanych przez klienta, możliwość zagnieżdżonej wirtualizacji i dostęp administracyjny just in Time, mogą dodatkowo ograniczyć ryzyko. W regionach Azure Government Australii w Canberra, proces dla formalnego listy dozwolonych jest przeznaczony tylko dla instytucji rządowych (& Australia), w których zainstalowano organizacje krajowe o znaczeniu narodowym. Ta chmura społeczności zapewnia dodatkowe gwarancje organizacjom, które są wrażliwe na ryzyko związane z współdzierżawcą.

Raport **chronionej** certyfikacji Microsoft Azure potwierdza, że te kontrolki są skuteczne do przechowywania i przetwarzania **chronionych** danych klasyfikowanych oraz ich izolacji.

## <a name="relevance-of-irapccsl-to-state-government-and-critical-infrastructure-providers"></a>Znaczenie IRAP/CCSL dla dostawców stanu i infrastruktury krytycznej

Wielu instytucji rządowych i krytycznych dostawców infrastruktury, w których są spełnione wymagania instytucji rządowych do ich zasad zabezpieczeń i struktury gwarancji. Organizacje te również obsługująoficjalnego **urzędnika: Poufne** i pewną ilość **chronionych** danych klasyfikowanych — od ich interakcji z federalnym rządem lub własnym prawem.

Australijski rząd jest coraz bardziej ukierunkowany na zasady i ustawodawstwo dotyczące ochrony danych niezwiązanych z administracją rządową, które w zasadniczy sposób wpływają na bezpieczeństwo i gospodarczą dobrobyt Australii. W takim przypadku regiony platformy Azure w Australii i certyfikat CCSL mają zastosowanie do wszystkich tych branż.

![Sektory infrastruktury krytycznej](media/nci-sectors.png)

Certyfikaty firmy Microsoft wykazują, że usługi platformy Azure zostały poddane gruntownej, rygorystycznej i formalnej ocenie ochrony bezpieczeństwa i zostały zatwierdzone do obsługi takich bardzo poufnych danych.

## <a name="location-and-control-of-microsoft-data-centres"></a>Lokalizacja i kontrola centrów danych firmy Microsoft

Jest to obowiązkowe wymaganie dla instytucji rządowych i infrastruktury krytycznej, aby jawnie znać lokalizację i własność centrum danych w ramach usług Cloud Services przetwarzających swoje dane.  Firma Microsoft jest unikatowym dostawcą usług w chmurze w celu udostępniania szczegółowych informacji na temat tych lokalizacji i własności.

Regiony platformy Azure w Australii (Australia Środkowa i Australia Środkowa 2) są obsługiwane w ramach udogodnień centrów danych przechwytywania.  Własność centrów danych przepustek to australijskie kontrolowane przez 48% własność od Wspólnoty wspólnotowej Superannuation Corporation, 48% własności z Infratil (oparta na technologii NZ, podwójny australijski i Nowa Zelandia giełdowa) i 4% australijskiego zarządzania.  

Zarządzanie centrami danych przechwytywania przestawnego ma gwarancje umowne w stosunku do instytucji rządowych Australii, które ograniczają przyszły transfer własności i kontroli. Przejrzystość łańcucha dostaw i własności przez partnerstwo firmy Microsoft z centrami danych przejmowania danych jest zgodne z zasadami [całościowej strategii hostingu dla instytucji rządowych](https://www.dta.gov.au/our-projects/whole-government-hosting-strategy) i definicji **certyfikowanego suwerennego centrum**danych.

## <a name="the-azure-services-that-are-included-in-the-current-ccsl-certification"></a>Usługi platformy Azure, które są uwzględnione w bieżącym certyfikacji CCSL

W czerwcu 2017 ACSC certyfikowane 41 usługi platformy Azure do przechowywania i przetwarzania danych **niesklasyfikowanych: Poziom** DLM. W kwietniu 2018, 24 z tych usług były certyfikowane za **chronione** dane klasyfikowane.

Dostępność ACSC certyfikowanych usług platformy Azure w naszych regionach platformy Azure w Australii jest następująca (pogrubienie jest certyfikowane w ramach **ochrony**):

|Regiony platformy Azure — Australia Środkowa|Usługi nieregionalne i inne regiony|
|---|---|
|API Management, App Gateway, Usługi aplikacji, **Automation, Azure Portal, Backup, Batch, Cloud Services**, Cosmos DB, Event Hubs, **ExpressRoute**, HDInsight, **Key Vault**, Load Balancer, log Analytics, **wieloskładnikowe Uwierzytelnianie**, Redis Cache, **Menedżer zasobów, Service Bus, Service Fabric, Site Recovery, SQL Database, magazyn**, Traffic Manager, **Virtual Machines,** Virtual Network, VPN Gateway|**Azure Active Directory**, CDN, Data Catalog, **Import Export, Information Protection, IOT Hub**, Machine Learning, Media Services, **Notification Hubs**, Power BI, **centrum zabezpieczeń, harmonogram**, wyszukiwanie, Stream Analytics|
|

Firma Microsoft publikuje [przegląd Microsoft Azure zgodności](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/44/Microsoft%20Azure%20Compliance%20Offerings.pdf) , który zawiera listę wszystkich usług w zakresie dla wszystkich globalnych, rządowych, branżowych i regionalnych procesów zgodności i oceny, które są wykonywane przez platformę Azure, w tym IRAP/CCSL.

## <a name="azure-service-not-listed-or-assessed-at-a-lower-level-than-i-need"></a>Usługa platformy Azure niewymieniona lub oceniona na niższym poziomie niż jest to potrzebne

Usługi, które nie są certyfikowane lub certyfikowane w oficjalnym miejscu **: Poufne** , ale nie **chronione**, można używać razem z lub w ramach rozwiązania obsługującego **chronione** dane, które są następujące:
1. przechowywanie i przetwarzanie **chronionych** danych nie jest zaszyfrowana lub
1. Ukończono ocenę ryzyka i zatwierdzono usługę do samodzielnego przechowywania **chronionych** danych.

Jeśli chcesz korzystać z usługi, która nie jest uwzględniona w CCSL do przechowywania i przetwarzania danych **urzędowych** , możesz, ale w tym przypadku firma ISM wymaga powiadomienia ACSC o tym przed wprowadzeniem lub odnowieniem kontraktu z dostawcą usług w chmurze.

Każda usługa używana przez agencję do **chronionych** obciążeń nadal musi być oceniona i zatwierdzona zgodnie z procesami opisanymi w procesie oceny IRAP i zarządzanym przez Agencję w ramach usługi w ramach [strategii bezpiecznego chmury](https://www.dta.gov.au/files/cloud-strategy/secure-cloud-strategy.pdf).

![Zastrzeżony proces certyfikacji strategii chmurowej DTA](media/certification.png)

Firma Microsoft stale ocenia nasze usługi, aby upewnić się, że platforma jest bezpieczna i zgodna z przepisami dla instytucji rządowych w Australii, dlatego skontaktuj się z firmą Microsoft, jeśli potrzebujesz pomocy w przypadku usługi, która nie jest obecnie włączona w **CCSL.**

Ponieważ firma Microsoft ma wiele usług certyfikowanych na CCSL w przypadku niesklasyfikowanych **DLM** i **chronionych** klasyfikacji, firma ISM wymaga, aby przeprowadzili IRAPą ocenę naszych usług co najmniej co dwa lata. Firma Microsoft podejmuje roczną ocenę, która jest również okazją do uwzględnienia dodatkowych usług.

## <a name="certified-protected-gateway-in-azure"></a>Certyfikowana Brama CHRONIONa na platformie Azure

Firma Microsoft nie współpracuje z certyfikowaną bramą internetową (SIG) dla instytucji rządowych ze względu na ograniczenia liczby SIGs dozwolonych w ramach programu konsolidacji bramy.  Oczekiwane i niezbędne możliwości tego SIG można skonfigurować w ramach Microsoft Azure.

Za pośrednictwem **chronionej** certyfikacji usług platformy Azure ACSC ma konkretne zalecenia dla agencji na potrzeby łączenia się z platformą Azure oraz wdrażania segmentacji sieci między domenami zabezpieczeń, na przykład między **chronionym** i Internet. Zalecenia te obejmują używanie sieciowych grup zabezpieczeń, zapór i wirtualnych sieci prywatnych.  ACSC zaleca korzystanie z urządzenia bramy wirtualnej. Na platformie Azure dostępne są kilka urządzeń wirtualnych, które mają fizyczny odpowiednik na liście produktów ocenionych na serwerze ASD lub zostały ocenione względem profilów ochrony wspólnych kryteriów i są one wymienione w portalu wspólnych kryteriów. Te produkty są wzajemnie uznawane przez narzędzie ASD jako Sygnatariusz o wspólnych ustaleniach dotyczących rozpoznawania kryteriów (CCRA).

Firma Microsoft wygenerowała wskazówki dotyczące wdrażania funkcji opartych na platformie Azure, które udostępniają funkcje zabezpieczeń wymagane do ochrony granicy między różnymi domenami zabezpieczeń, które w związku z tym łączą się z certyfikowanym PODPISem. Istnieje wielu partnerów, którzy mogą pomóc w projektowaniu i wdrażaniu tych możliwości, a także kilka dostępnych rozwiązań partnerskich, które wykonują te same działania.

## <a name="security-clearances-and-citizenship-of-microsoft-support-personnel"></a>Prawa do bezpieczeństwa i obywatelstwo personelu pomocy technicznej firmy Microsoft

Firma Microsoft zarządza naszych usług globalnie z osłanianym i przeszkolonym personelem zabezpieczeń.  Pracownicy, którzy mają nieeskortowy dostęp fizyczny do Udogodnień w Sydney i Melbourne, posiadają australijskie prawa do bezpieczeństwa dla instytucji rządowych. Personel w regionach Australii Środkowej i Australii środkowej 2 ma minimum przed sprawdzeniem 1 (NV1) (odpowiednio do danych tajnych). Zapewnia to klientowi dodatkową gwarancję, że personel w centrach danych działających na platformie Azure jest wysoce godny zaufania.

Firma Microsoft ma zerowe zasady dostępu z dostępem przyznanym przez system just-in-Time i wystarczy do administrowania na podstawie kontroli dostępu opartej na rolach. W większości przypadków Administratorzy nie potrzebują dostępu do danych klientów ani uprawnień do nich w celu rozwiązywania problemów i utrzymania usługi.  Duże stopnie automatyzacji i wykonywanie skryptów zadań związanych z wykonywaniem zdalnego Negate potrzebę bezpośredniego dostępu do danych klienta.

Departament "prawnik generalny" potwierdził, że zasady i procedury zabezpieczeń personelu firmy Microsoft w ramach platformy Azure są zgodne z intencją PSPF dostępu do przepisów dotyczących informacji w INFOSEC-9.

## <a name="storing-international-traffic-of-arms-regulations-itar-or-export-administration-regulations-ear-data"></a>Przechowywanie międzynarodowego ruchu (ITAR) lub eksportowanie przepisów administracyjnych

Kontrolki techniczne na platformie Azure, które pomagają klientom sprostania ich zobowiązaniom do danych z kontroli eksportu, są takie same na platformie Azure. Ważne nie jest "taktowanie pola ITAR/paska", ponieważ nie ma formalnej oceny i struktury certyfikacji dla danych kontrolowanych eksportem.

W przypadku Azure Government i pakietu Office 365 dla instytucji rządowych USA na rzecz ochrony wprowadziliśmy dodatkowe środki umowne i procesy w celu wsparcia klientom, którzy podlegają kontrolom eksportowym. Te dodatkowe klauzule umowne i gwarantowane wsparcie narodowe USA oraz administrację regionami świadczenia usługi Azure nie są przeznaczone dla Australii.

Nie oznacza to, że platformy Azure w Australii nie można używać na potrzeby ITAR/KOLCZYKów, ale musisz jasno zrozumieć ograniczenia narzucone na podstawie licencji eksportowej i musisz zaimplementować dodatkowe zabezpieczenia, aby spełnić te zobowiązania przed rozpoczęciem korzystania z platformy Azure te dane. Na przykład może być konieczne skompilowanie obywatelstwa jako atrybutu do Azure Active Directory, użycie Azure Information Protection, aby wymusić reguły szyfrowania dla danych i ograniczyć je tylko do USA, a wszelkie inne obywatelstwa są zawarte w licencji eksportowej. Szyfruj wszystkie dane lokalnie przed zapisaniem na platformie Azure, korzystając z klucza klienta lub przechowuj własny klucz dla danych ITAR, a lista zostanie przestawiona na......

Ponieważ ITAR nie jest formalnym certyfikatem, należy zapoznać się z ograniczeniami i ograniczeniami związanymi z licencją eksportową, a następnie wykonać odpowiednie działania w systemie Azure, aby spełnić te wymagania. W takim przypadku jednym z problemów, które należy wziąć pod uwagę, jest dostęp przez naszych inżynierów, którzy mogą nie być obywatelami zatwierdzonymi w ramach licencji eksportowej.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z artykułem na [platformie azure VPN Gateway](vpn-gateway.md) na potrzeby konfiguracji i implementacji łączności sieci VPN z usługą Azure Australia

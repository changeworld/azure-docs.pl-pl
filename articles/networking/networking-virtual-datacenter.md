---
title: 'Wirtualne centrum danych platformy Microsoft Azure: Perspektywy sieci | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak utworzyć centrum danych wirtualnych na platformie Azure
services: networking
author: tracsman
manager: rossort
tags: azure-resource-manager
ms.service: virtual-network
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/3/2018
ms.author: jonor
ms.openlocfilehash: 1aab466a06711a334df0584334e5229b33f57754
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
---
# <a name="microsoft-azure-virtual-datacenter-a-network-perspective"></a>Wirtualne centrum danych platformy Microsoft Azure: Perspektywy sieci
**Microsoft Azure**: szybszą, oszczędności, integracja danych i aplikacji lokalnych

## <a name="overview"></a>Przegląd
Migrowanie aplikacji lokalnej na platformie Azure, nawet bez wprowadzania żadnych znaczących zmian (podejście jest ogólnie znana jako "przyrostu i przesunięcia"), pozwala organizacjom zalet infrastruktury zabezpieczonych i ekonomiczny sposób. Jednak aby umożliwić większości elastyczność z rozwiązań w chmurze, przedsiębiorstw powinien rozwijać ich architektury, aby móc korzystać z funkcji platformy Azure. Microsoft Azure oferuje hiperskali usług i infrastruktury, funkcje klasy korporacyjnej i niezawodność oraz wiele opcji na potrzeby łączności hybrydowej. Klienci mogą wybrać dostępu do tych usług chmury, za pośrednictwem Internetu lub z usługi Azure ExpressRoute, który zapewnia łączność w sieci prywatnej. Platforma Microsoft Azure umożliwia klientom bezproblemowo rozszerzają infrastruktury do chmury i tworzenia wielu architektur. Ponadto partnerów firmy Microsoft zawiera udoskonalone funkcje za oferty usług zabezpieczeń i wirtualnych urządzeń, które są zoptymalizowane do uruchamiania na platformie Azure.

Ten artykuł zawiera omówienie wzorców i projekty, które mogą służyć do rozwiązywania problemów architektury skali, wydajności i zabezpieczeń wiele stojących przed klientami w przypadku o czasie masse przenoszeniu do chmury. Omówienie jak dostosować różnych organizacji ról do zarządzania i nadzór nad systemu jest również omówione wyróżnieniem do wymagań dotyczących zabezpieczeń i kosztów optymalizacji.

## <a name="what-is-a-virtual-data-center"></a>Co to jest centrum danych wirtualnej?
Na początku istnienia rozwiązań w chmurze są przeznaczone do jednej, stosunkowo izolowanego aplikacji hosta, w publicznych spektrum. Takie podejście dobrze działały dla kilka lat. Jednak jako z zalet chmury rozwiązań stała się oczywista i wiele obciążeń na dużą skalę zostały hostowanych w chmurze, adresowania bezpieczeństwo, niezawodność, wydajność i kosztów dotyczy wdrożeń w jednym lub więcej regionów stał się istotny przez cały cykl życia usługi w chmurze.

Na poniższym diagramie wdrożenia chmury przedstawiono kilka przykładów luk zabezpieczeń (czerwonym prostokątem) i miejsce dla optymalizacji sieci wirtualnych urządzeń między obciążeń (żółty pole).

[![0]][0]

Centrum danych (vDC) wirtualnej powstał z tego konieczność skalowania do obsługi obciążeń przedsiębiorstwa i potrzeb dotyczących rozwiązywania problemów wprowadzona podczas obsługi dużych aplikacji w chmurze publicznej.

VDC nie jest po prostu obciążeń aplikacji w chmurze, ale również sieci, zabezpieczeń, zarządzania i infrastruktury (na przykład, DNS i usługi katalogowe). Zwykle zapewnia także prywatnego połączenia lokalnego Centrum sieci i danych. Jak przenieść coraz więcej obciążeń na platformie Azure, warto pomyśleć o obsługę infrastruktury i obiektów znajdujących się w tych obciążeń. Analiza dokładnie struktury zasobów można uniknąć mnożenie setki "obciążenie Wyspy", które muszą być zarządzane oddzielnie z przepływu danych niezależnych, modele zabezpieczeń i zgodności wyzwania.

Centrum danych wirtualnej jest zasadniczo zbiór jednostek oddzielnej, ale powiązane z typowych funkcji pomocniczych, funkcje i infrastruktury. Wyświetlając obciążeń jako vDC zintegrowanego, można zrealizować mniejsze koszty ze względu na oszczędności skali, zoptymalizowane zabezpieczeń za pomocą składnika i danych centralizacji przepływu, oraz operacje łatwiejsze, zarządzania i kontroli zgodności.

> [!NOTE]
> Ważne jest, aby dowiedzieć się, że vDC **nie** odrębny produktu Azure, ale kombinacją różnych funkcji i możliwości, zgodnie z wymaganiami dokładne. vDC jest sposób planowania obciążeń i użycia Azure zmaksymalizować wszystkie zasoby i możliwości w chmurze. Wirtualny kontroler domeny jest w związku z tym podejściu na temat tworzenia usług IT na platformie Azure, odpowiednich organizacji role i obowiązki.

VDC mogą pomóc przedsiębiorstwom uzyskiwanie obciążeń i aplikacji na platformie Azure w następujących scenariuszach:

-   Obsługa wielu powiązanych obciążeń pracą
-   Migrowanie obciążeń ze środowiska lokalnego do platformy Azure
-   Wdrażanie udostępnionego lub scentralizowanych zabezpieczeń i wymagania dotyczące dostępu między obciążeń
-   Mieszanie DevOps i scentralizowane IT odpowiednio dla dużych przedsiębiorstw

Klucz do odblokowywania zalet vDC, jest scentralizowane topologii (koncentrator i klienci) za pomocą różnych funkcje platformy Azure: [sieci wirtualnej Azure][VNet], [grup NSG] [ NSG], [Sieci wirtualnej komunikacji równorzędnej][VNetPeering], [trasy zdefiniowane przez użytkownika (przez)][UDR], a tożsamość platformy Azure z [Base roli Kontrola (RBAC) dostępu][RBAC].

## <a name="who-needs-a-virtual-data-center"></a>Który musi wirtualnego centrum danych?
Wszelkie Azure klienta, które należy przenieść więcej niż kilka obciążeń na platformie Azure mogą korzystać z myślenia o za pomocą wspólnych zasobów. W zależności od wielkości nawet pojedyncze aplikacje mogą korzystać z wzorców i składniki używane do tworzenia vDC.

Jeśli Twoja organizacja ma scentralizowanych zabezpieczeń IT, sieci, i/lub team/dział kontroli zgodności, vDC może pomóc wymusić zasady punktów podziału należności i zapewnić jednolitość podstawowych składników wspólne podczas przekazywania tyle swobodę zespoły aplikacji i kontroli zgodnie z odpowiednią do wymagań.

Organizacje, które chce się dowiedzieć, do opracowywania oprogramowania mogą korzystać z pojęć vDC Podaj autoryzowanych kieszeni zasobów platformy Azure i upewnij się, mają pełną kontrolę w ramach tej grupy (albo subskrypcji lub grupy zasobów w subskrypcji wspólnej), ale sieci i granice zabezpieczeń pozostają zgodne, zgodnie z definicją w scentralizowanych zasad w Centrum sieci i grupy zasobów.

## <a name="considerations-on-implementing-a-virtual-data-center"></a>Uwagi dotyczące implementacji wirtualnego centrum danych
Podczas projektowania vDC, istnieje kilka kluczową problemów, które należy wziąć pod uwagę:

-   Tożsamości i usług katalogowych
-   Infrastruktura zabezpieczeń
-   Łączność w chmurze
-   Łączność w chmurze

##### <a name="identity-and-directory-service"></a>*Tożsamości i usługi katalogowej*
Tożsamości i usługi katalogowe są kluczowym aspektem wszystkie dane w centrach, zarówno lokalnie i w chmurze. Tożsamość jest związana z wszystkich aspektów dostępu i autoryzacja w zakresie usług w vDC. Aby upewnić się, że tylko autoryzowani użytkownicy i procesy dostęp do zasobów oraz Twojego konta Azure, Azure używa kilku typów poświadczeń do uwierzytelniania. Obejmują one hasła (Aby uzyskać dostęp do konta platformy Azure), kluczy kryptograficznych podpisów cyfrowych i certyfikatów. [*Usługa Azure Multi-Factor Authentication* (MFA)] [ MFA] jest dodatkową warstwę zabezpieczeń do uzyskiwania dostępu do usług platformy Azure. Usługa Azure MFA zapewnia silne uwierzytelnianie za pomocą różnych opcji weryfikacji łatwe — połączenie telefoniczne, wiadomość tekstowa lub powiadomienie aplikacji mobilnej — i umożliwiają klientom wybrać metodę preferowany.

Wszystkie duże przedsiębiorstwo musi definiować procesu zarządzania tożsamościami, opisujący Zarządzanie tożsamościami poszczególnych, ich uwierzytelniania, autoryzacji, role i uprawnienia lub wielu vDC. Cele tego procesu należy zwiększyć bezpieczeństwo i wydajność przy jednoczesnym ograniczeniu kosztów, czasem przestoju oraz powtarzające się zadania ręczne.

Enterprise/organizacje mogą wymagać różnych wymagających usług dla różnych wiersza elementu firm obiektów (LOB), a pracownicy często mają różne role w przypadku z różnych projektów. VDC wymaga dobrej współpracy między zespołami różnych, każdy z definicjami określoną rolę, można pobrać z systemami z dobrego zarządzania. Macierz obowiązki, dostępu i prawa mogą być bardzo złożone. Zarządzanie tożsamościami w vDC jest implementowane za pośrednictwem [ *usługi Azure Active Directory* (AAD)] [ AAD] i kontroli dostępu opartej na rolach (RBAC).

Usługa katalogowa jest informacje udostępniane w infrastrukturze lokalizowanie, zarządzanie administrowanie i organizowanie elementów codziennie i zasobów sieciowych. Te zasoby mogą obejmować woluminów, folderów, plików, drukarek, użytkowników, grup, urządzeń i innych obiektów. Każdy zasób w sieci jest uważany za obiektu przez serwer w katalogu. Informacje o zasobie jest przechowywana jako kolekcja atrybutów skojarzona z tego zasobu lub obiektu.

Wszystkie usługi biznesowe online firmy Microsoft polega na usługi Azure Active Directory (AAD) dla logowania i wymaga innych tożsamości. Usługa Azure Active Directory to kompleksowe rozwiązanie o wysokiej dostępności do zarządzania tożsamościami i dostępem w chmurze, które oferuje podstawowe usługi katalogowe, zaawansowane funkcje zarządzania tożsamościami i zarządzania dostępem do aplikacji. AAD można zintegrować z lokalnej usługi Active Directory umożliwiające logowanie jednokrotne dla wszystkich opartych na chmurze i lokalnie hostowanej (lokalnego) aplikacji. Mogą być automatycznie synchronizowane atrybuty użytkowników z lokalnej usługi Active Directory do usługi AAD.

Aby przypisać wszystkie uprawnienia w vDC nie jest wymagane jednego administratora globalnego. Zamiast tego każdego określonego działu (lub grupy użytkowników lub usług w usłudze katalogowej) może mieć uprawnień wymaganych do zarządzania zasobami w ramach vDC. Struktura uprawnień wymaga równoważenia. Zbyt wiele uprawnień, co może utrudniać wydajności wydajności i zbyt mało utracić uprawnienia można zwiększyć za zagrożenie bezpieczeństwa. Aby rozwiązać ten problem, oferując precyzyjne zarządzanie dostępem do zasobów vDC pomaga w Azure opartej na rolach kontroli dostępu (RBAC).

##### <a name="security-infrastructure"></a>*Infrastruktura zabezpieczeń*
Infrastruktura zabezpieczeń w kontekście vDC, głównie powiązany jest podział ruchu w segmencie sieci wirtualnej określonej vDC i sterowanie przychodzące i wychodzące przepływy w całym vDC. Azure jest oparty na architekturze wielodostępne, który uniemożliwia nieautoryzowanym oraz niezamierzonych ruchu między wdrożeniami, przy użyciu izolacji sieci wirtualnej (VNet), listy kontroli dostępu (ACL), obciążenia równoważenia i filtry IP wraz z zasadami przepływu ruchu. Translator adresów sieciowych (NAT) oddziela ruchu w sieci wewnętrznej od zewnętrznego ruchu.

Sieci szkieletowej Azure przydziela zasoby infrastruktury obciążeń dzierżawców i zarządza komunikacja do i z maszyn wirtualnych (VM). Azure funkcji hypervisor wymusza pamięci i procesu separacji między maszynami wirtualnymi i bezpiecznie kieruje ruchem dzierżawców systemu operacyjnego gościa sieciowym.

##### <a name="connectivity-to-the-cloud"></a>*Łączność w chmurze*
VDC musi mieć łączność z sieci zewnętrznych do oferty usług dla klientów, partnerów i/lub użytkowników wewnętrznych. Zwykle oznacza to połączenie, nie tylko z Internetem, ale do lokalnej sieci i w centrum danych.

Klienci mogą tworzyć swoje zasady zabezpieczeń do kontrolowania co i jak określonych vDC hostowanej usługi są dostępny z Internetu przy użyciu wirtualnych urządzeń sieciowych (za pomocą filtrowania ruchu i inspekcji) i niestandardowych, routing zasady i filtrowanie (sieci Routing zdefiniowane przez użytkownika i grupy zabezpieczeń sieci).

Przedsiębiorstwa często muszą nawiązać VDC lokalnych centrów danych lub innych zasobów. Łączność między sieciami Azure i lokalnymi w związku z tym jest kluczowy aspekt podczas projektowania architektury skuteczne. Przedsiębiorstwach istnieją dwa różne sposoby tworzenia połączenia między vDC i lokalnej na platformie Azure: przesyłane w Internecie i/lub prywatnej połączeń bezpośrednich.

[ **Azure VPN lokacja-lokacja** ] [ VPN] jest usługą połączeń przez Internet między sieciami lokalnymi i szyfrowane vDC, nawiązane za pośrednictwem bezpiecznego połączenia (tuneli IPsec i IKE). Azure połączenie lokacja-lokacja jest elastyczną i szybko utworzyć i nie wymaga żadnych dalszych zamówień jako połączenia za pośrednictwem Internetu.

[**ExpressRoute** ] [ ExR] jest usługą platformy Azure łączności, która umożliwia tworzenie prywatnych połączeń między vDC i sieciami lokalnymi. Połączenia ExpressRoute nie go za pośrednictwem publicznej sieci Internet i oferują wyższy poziom zabezpieczeń, niezawodności i większe szybkości (do 10 GB/s) oraz spójne opóźnienia. ExpressRoute jest bardzo przydatna dla VDC, jak klienci mogą uzyskać korzyści wynikające z reguły zgodności skojarzone z prywatnych połączeń ExpressRoute.

Wdrażanie połączenia ExpressRoute obejmuje współpraca przy użyciu dostawcy usługi ExpressRoute. Klienci, którzy mają szybko rozpocząć początkowo Nawiąż łączność programów vDC przy użyciu sieci VPN typu lokacja-lokacja i zasobów lokalnych i jest następnie przeprowadzić migrację do połączenia ExpressRoute.

##### <a name="connectivity-within-the-cloud"></a>*Łączność w chmurze*
[Sieci wirtualne] [ VNet] i [sieci wirtualnej komunikacji równorzędnej] [ VNetPeering] są podstawowych usług sieciowych łączności wewnątrz vDC. Fizyczne granic izolacji zasobów vDC gwarantuje sieci wirtualnej i sieci wirtualnej komunikacji równorzędnej pozwala wzajemnego komunikowania między różnych sieci wirtualnych w obrębie tego samego regionu Azure, lub nawet w różnych regionach. Kontrola ruchu w sieci wirtualnej i między sieciami wirtualnymi musi być zgodny zestaw reguł zabezpieczeń określone za pomocą list kontroli dostępu ([sieciowej grupy zabezpieczeń][NSG]), [wirtualnych urządzeń sieciowych ] [ NVA]i niestandardowe tabele routingu ([przez][UDR]).

## <a name="virtual-data-center-overview"></a>Omówienie wirtualnych centrum danych

### <a name="topology"></a>Topologia
Model koncentratora i partnerzy rozszerzony wirtualnej centrum danych w pojedynczym regionie Azure

[![1]][1]

Koncentrator jest strefy centralnej, która określa i sprawdza ruch przychodzący lub wychodzący ruch sieciowy między różnych stref: Internetu, lokalnego i klienci. Topologia gwiazdy daje dział IT efektywnym sposobem wymuszania zasad zabezpieczeń w centralnej lokalizacji, jednocześnie zmniejszając potencjalnych błędnej konfiguracji i zagrożeń.

Koncentrator zawiera typowe składniki usługi używane przez klienci. Poniżej przedstawiono kilka typowych przykładów typowych centralnej usług:

-   Infrastruktura usługi Active Directory systemu Windows (przy użyciu pokrewne usługi AD FS) wymagane do uwierzytelniania użytkowników osób trzecich, uzyskiwanie dostępu z niezaufanymi sieciami przed uzyskaniem dostępu do obciążeń w gwiazdy
-   Usługa DNS do rozpoznawania nazw dla obciążeń w klienci, aby dostęp do zasobów lokalnych i w Internecie
-   Infrastruktury kluczy publicznych, aby zaimplementować rejestracji jednokrotnej w obciążeń
-   Sterowanie przepływem (TCP/UDP) między partnerzy i Internet
-   Sterowanie przepływem między gwiazdy i lokalnych
-   W razie potrzeby przepływu sterowania między jeden gwiazdy

VDC zmniejsza całkowity koszt przy użyciu infrastruktury udostępnionej koncentratora od wielu partnerów.

Rola każdego gwiazdy może być do różnych rodzajów obciążeń hosta. Klienci można też podać podejściu powtarzalnych wdrożeń (na przykład deweloperów i testowania, testów akceptacyjnych przez użytkowników, produkcji wstępnej i produkcji) tego samego obciążeń. Klienci mogą również segregowanie i Włącz różnych grup w organizacji (na przykład grupy DevOps). Wewnątrz gwiazdy to można wdrożyć na podstawowe obciążenie lub złożonych obciążeń wielowarstwową z kontrola ruchu sieciowego między warstwami.

##### <a name="subscription-limits-and-multiple-hubs"></a>Limity subskrypcji i wiele centrów
Na platformie Azure co składnik, niezależnie od typu, jest wdrażana w subskrypcji platformy Azure. Izolacja składników platformy Azure w ramach różnych subskrypcji platformy Azure można spełnić ich wymagań w różnych obiektów LOB, takich jak konfigurowanie różne poziomy dostępu i autoryzacja.

Pojedynczy vDC można zwiększać dużej liczby partnerów, mimo że, podobnie jak w przypadku każdego systemu IT są limity platform. Wdrożenia Centrum jest powiązany z określonej subskrypcji Azure, który ma ograniczenia i limity (na przykład, zobacz maksymalna liczba komunikacji równorzędnych sieci wirtualnej - [subskrypcji platformy Azure i usługi limity, przydziały i ograniczenia] [ Limits] szczegółowe informacje). W przypadku, gdy limity może się problemu, architektury można skalować się dalsze przez rozszerzanie modelu z jednym partnerzy koncentratora do klastra koncentratora i klienci. Wiele koncentratorów w co najmniej jeden regiony platformy Azure mogą wzajemnie połączone przy użyciu sieci wirtualnej komunikacji równorzędnej ExpressRoute i sieci VPN typu lokacja lokacja.

[![2]][2]

Wprowadzenia wiele koncentratorów powoduje zwiększenie kosztów i zarządzania nakładu pracy systemu i jest uzasadnione tylko przez skalowalność (przykłady: limity systemu lub nadmiarowość) i regionalnych replikacji (przykłady: wydajności lub awaryjnego odzyskiwania przez użytkownika końcowego). W scenariuszach wymagających wielu koncentratory, wszystkie centra powinien starań, aby oferować ten sam zestaw usług w celu ułatwienia operacyjną.

##### <a name="interconnection-between-spokes"></a>Połączenie między partnerzy
Wewnątrz jednej gwiazdy jest możliwość implementacji złożonego obciążeń wielu warstw. Konfiguracje wielowarstwowych można zaimplementować przy użyciu podsieci (po jednej dla każdej warstwy) w tej samej sieci wirtualnej i filtrowanie przepływów za pomocą grup NSG.

Z drugiej strony architektów warto wdrożyć wielowarstwową obciążenia między wieloma sieciami wirtualnymi. Przy użyciu sieci wirtualnej komunikacji równorzędnej, klienci mogą łączyć się pozostali partnerzy, w tym samym Centrum lub różnych koncentratorów. Typowym przykładem w tym scenariuszu jest to, gdzie serwery aplikacji znajdują się w jednym gwiazdy (VNet), podczas wdrażania bazy danych w różnych gwiazdy (VNet). W tym przypadku jest łatwo łączyć klienci z sieci wirtualnej komunikacji równorzędnej i tym samym uniknąć przejeżdżających przez przez koncentrator. Dokładne Przegląd architekturze i zabezpieczeniach należy wykonać, aby upewnić się, że pomijanie koncentratora nie obejścia ważne zabezpieczeń lub inspekcji punktów, które mogą znajdować się tylko w Centrum.

[![3]][3]

Partnerzy mogą również być z sobą połączone do gwiazdy, która pełni funkcję Centrum. Ta metoda tworzy dwupoziomowej hierarchii: gwiazdy w wyższego poziomu (poziom 0) stają się koncentratora partnerzy niższe (poziom 1) w hierarchii. Klienci vDC konieczne do przesyłania ruchu do Centrum dotrzeć do sieci lokalnej lub w Internecie. Architektura o dwa poziomy Centrum wprowadza złożonego routingu, która usuwa zalet relacji proste Gwiazda.

Mimo że Azure umożliwia złożonej topologii, jest jedną z zasad podstawowych koncepcji vDC powtarzalność i prostota. Aby zminimalizować nakład zarządzania, projekt proste Gwiazda jest architektura referencyjna vDC zalecane.

### <a name="components"></a>Składniki
Centrum danych wirtualnej składa się z czterech typów podstawowych składników: **infrastruktury**, **sieci obwodowej**, **obciążeń**, i **monitorowanie**.

Każdy typ składnika składa się z różnych funkcji platformy Azure i zasobów. Twoje vDC składa się z wystąpień wiele typów składników i wiele różnych wersji tego samego typu składnika. Na przykład może istnieć wiele wystąpień innych, logicznie rozdzielonych obciążenia, które reprezentują różnych aplikacji. Umożliwia tych typów różnych składników i wystąpień ostatecznie kompilacji vDC.

[![4]][4]

Architektura wysokiego poziomu poprzedniego vDC przedstawiono typy różnych składników używane w różnych strefach topologii partnerzy koncentratora. Na diagramie przedstawiono składniki infrastruktury w różnych części architektury.

Dostęp dobrym rozwiązaniem (dla kontrolera domeny z lokalnymi lub vDC) praw i uprawnień powinna być oparte na grupach. Dotyczących grupy zamiast poszczególnych użytkowników pomaga konserwacji zasad dostępu spójnie między zespołami i pomocy w przypadku minimalizowania błędów konfiguracji. Przypisywanie i usuwanie użytkowników z odpowiednich grup i pomaga, aktualizując uprawnienia określonego użytkownika.

Każda grupa roli musi mieć unikatowy prefiks we ich nazw, co ułatwia identyfikację grupy, do której jest skojarzony z którym obciążenie. Na przykład obciążenie usługi uwierzytelniania hosta może być grupy o nazwie *AuthServiceNetOps, AuthServiceSecOps AuthServiceDevOps i AuthServiceInfraOps.* Podobnie dla scentralizowane ról lub role nie dotyczą określonej usługi, może być poprzedzone znakiem "Corp" *CorpNetOps* np.

Wiele organizacji w celu dostarczenia poważnej awarii ról zastosować zmianę następujących grup:

-   *Centralna grupa IT (Corp)* ma prawa własności do kontrolowania składników infrastruktury (na przykład sieci i zabezpieczeń) i dlatego musi mieć rolę współautora dla tej subskrypcji (oraz mają kontrolę nad koncentratora) i uprawnienia współautora sieci w klienci. Dużej organizacji często podzielić te obowiązki zarządzania między kilka zespołów, takich jak; Operacje sieciowe (CorpNetOps) grupy (wyłączne fokus na obsługę sieci) i grupy operacje zabezpieczeń (CorpSecOps) (odpowiedzialny za zasad zapory i zabezpieczeń). W tym przypadku określone dwie różne grupy muszą zostać utworzone dla przypisania tych ról niestandardowych.
-   *Deweloperów & Grupa testowa (AppDevOps)* jest odpowiedzialny za wdrażania obciążeń (aplikacji lub usługi). Ta grupa ma rolę współautora maszyny wirtualnej dla wdrożenia IaaS i/lub jeden lub więcej współautora PaaS ról (zobacz [wbudowanych ról dla kontroli dostępu][Roles]). Opcjonalnie zespół deweloperów & testu może być konieczne mają widoczność na zasady zabezpieczeń (NSG) i zasad routingu (przez) wewnątrz koncentratora lub określonych gwiazdy. W związku z tym oprócz role współautora dla obciążeń, tej grupy należy roli czytnika danych sieciowych.
-   *Grupy operacji i konserwacji (CorpInfraOps lub AppInfraOps)* odpowiedzialny za zarządzanie obciążeń w środowisku produkcyjnym. Ta grupa musi być współautorem subskrypcji na obciążeń w żadnych subskrypcji produkcji. Niektóre organizacje mogą również ocenić, czy potrzebne dodatkowe eskalacji pomocy technicznej zespół grupy z rolą Współautor subskrypcji w środowisku produkcyjnym i w subskrypcji centralnego Aby rozwiązać potencjalne problemy z konfiguracją w produkcji środowisko.

VDC mają strukturę, dzięki czemu grup utworzonych dla centralnej grup IT zarządzanie koncentratora mają odpowiednie grupy na poziomie obciążenia. Oprócz zarządzania zasobami Centrum tylko centralnej grup IT będą mogli kontroli dostępu zewnętrznego i najwyższego poziomu uprawnień w subskrypcji. Jednak grupy obciążenia będą mogli kontrolę zasobów oraz uprawnienia ich sieci wirtualnej niezależnie w centralnym dziale IT.

VDC musi być dzielony na partycje bezpiecznie udostępniać wielu projektów przez różne wiersza elementu firmy (obiektów LOB). Wszystkie projekty wymagają różnych środowiskach izolowanego (deweloperów, UAT, produkcyjnego). Oddzielne subskrypcji platformy Azure dla każdego z tych środowisk zapewniają izolację fizycznych.

[![5]][5]

Na powyższym diagramie przedstawiono relację między projektów organizacji, użytkowników, grup i środowisk wdrożonym składniki platformy Azure.

Zwykle w IT, środowisko (lub warstwy) jest system, w którym wiele aplikacji są wdrożone i są stosowane. Duże przedsiębiorstwa Użyj środowisko projektowe (gdzie zmiany pierwotnie wprowadzone i przetestowane) i środowiska produkcyjnego (co użytkownicy końcowi używają). Tych środowisk są oddzielone, często w kilku środowiskach Between je, aby umożliwić wdrożenie etapowe (wdrażanie) na potrzeby przemieszczania, testowania i wycofania w razie problemów. Wdrożenia architektury różnić, ale zazwyczaj nadal występuje podstawowy proces zaczynając od rozwoju (deweloperów) i koniec (produkcyjną) w środowisku produkcyjnym.

Architektura dla tych typów środowisk wielowarstwowych składa się z DevOps (projektowania i testowania), UAT (środowisko przejściowe) i środowisk produkcyjnych. Organizacje mogą korzystać z jednego lub wiele dzierżaw usługi Azure AD, aby zdefiniować dostęp i prawa do tych środowisk. Poprzedni diagram przedstawia przypadek w przypadku, gdy dwa różne służą dzierżaw usługi Azure AD: jeden dla DevOps i UAT, a drugi wyłącznie w środowisku produkcyjnym.

Obecność różnych usługi Azure AD dzierżaw odnosi się do separacji między środowiskami. Tej samej grupy użytkowników (na przykład centralnym dziale IT) wymaga uwierzytelnienia przy użyciu innego identyfikatora URI innej dzierżawy AD dostęp do modyfikowania ról lub uprawnień DevOps albo produkcji środowisk projektu. Obecność uwierzytelniania innego użytkownika na dostęp do różnych środowiskach zmniejsza możliwe awarie i inne problemy spowodowane przez błędy człowieka.

#### <a name="component-type-infrastructure"></a>Typ składnika: infrastruktury
Ten typ składnika jest, gdzie znajduje się większość infrastruktury pomocniczej. Istnieje również gdzie Twoje scentralizowane IT, zabezpieczeń, i/lub zgodności zespoły poświęcają większość czasu.

[![6]][6]

Składniki infrastruktury zapewniają połączenia między poszczególnymi składnikami vDC i znajdują się w Centrum i klienci. Odpowiedzialność za utrzymanie składników infrastruktury i zarządzanie zwykle jest przypisany do centralną IT i/lub zespołu ochrony obiektu.

Jednym z podstawowych zadań zespołu, infrastruktury IT jest zagwarantowania spójności Schematy adresów IP w całym przedsiębiorstwie. Prywatnego adresu IP adresów miejsca przypisane do potrzeb vDC, aby były spójne i nie nakładać się na prywatne adresy IP przypisane w sieci lokalnej.

Podczas translacji adresów Sieciowych w routery brzegowe lokalnej lub w środowisku Azure można uniknąć konfliktów adresów IP, dodaje komplikacji w poszczególnych składnikach infrastruktury. Łatwość zarządzania jest jednym z podstawowych celów vDC, dlatego przy użyciu translatora adresów Sieciowych do obsługi problemów IP nie jest zalecane rozwiązanie.

Składniki infrastruktury zawiera następujące funkcje:

-   [**Tożsamości i usługi katalogowe**][AAD]. Dostęp do wszystkich typów zasobów na platformie Azure jest kontrolowany przez tożsamości przechowywane w usłudze katalogowej. Usługa katalogowa przechowuje nie tylko listę użytkowników, ale również prawa dostępu do zasobów w określonej subskrypcji platformy Azure. Te usługi może istnieć tylko na chmurze lub mogą być synchronizowane z tożsamością lokalnie przechowywane w usłudze Active Directory.
-   [**Sieć wirtualna**][VPN]. Sieci wirtualne są jednym z głównych składników vDC i umożliwiają tworzenie granic izolacji ruchu na platformie Azure. Sieć wirtualna składa się z jednym lub wielu segmentach sieci wirtualnej, każda z określonego adresu IP prefiksu sieci (podsieci). Sieci wirtualnej definiuje obszar obwodowej wewnętrznego, gdy maszyny wirtualne IaaS i PaaS usługi może nawiązać komunikacji prywatnej. Maszyny wirtualne (i usługi PaaS) w jednej sieci wirtualnej nie może komunikować się bezpośrednio do maszyn wirtualnych (i usługi PaaS) w innej sieci wirtualnej, nawet jeśli obie sieci wirtualne są tworzone przez tego samego klienta w ramach tej samej subskrypcji. Izolacja jest krytyczne właściwość, która zapewnia klienta maszyn wirtualnych i komunikacja pozostanie prywatnej sieci wirtualnej.
-   [**PRZEZ**][UDR]. Ruch w sieci wirtualnej jest kierowany przez domyślne na podstawie tabeli routingu systemu. Zdefiniuj użytkownika trasy jest niestandardowe tabele routingu, które administratorzy sieci można kojarzyć z co najmniej jednej podsieci, aby zastąpić zachowania tabeli routingu systemu oraz zdefiniować ścieżki komunikacji w ramach sieci wirtualnej. Obecność Udr gwarancje ruch wychodzący z przesyłania gwiazdy przez określone niestandardowe maszyny wirtualne i/lub wirtualnych urządzeń sieciowych i występuje w Centrum i klienci usługi równoważenia obciążenia.
-   [**NSG**][NSG]. Grupa zabezpieczeń sieci znajduje się lista reguł zabezpieczeń, które działają jako ruchu filtrowania portów IP źródła, docelowego adresu IP, protokołów, portów IP źródłowego i docelowego adresu IP. Grupy NSG można zastosować do podsieci, karty wirtualnej karty Sieciowej, skojarzonych z maszyny Wirtualnej platformy Azure lub oba. Grupy NSG są niezbędne do zaimplementowania kontroli prawidłowego przepływu w Centrum i klienci. Poziom zabezpieczeń oferowanych przez grupa NSG jest funkcja otwierania portów i w jaki celu. Klienci powinni zastosować filtry wirtualna dodatkowych zapór oparta na hoście, takie jak IPtables lub Zapora systemu Windows.
-   [**DNS**][DNS]. Rozpoznawanie nazw zasobów w sieci wirtualnych z vDC podano przy użyciu systemu DNS. Platforma Azure udostępnia usługi DNS dla obu [publicznego][DNS] i [prywatnej] [ PrivateDNS] rozpoznawanie nazw. Strefy prywatnej zapewniają rozpoznawania nazw w obrębie sieci wirtualnej i sieci wirtualnych. Masz prywatnej strefy nie tylko zakresu między sieciami wirtualnymi w tym samym regionie, ale także w różnych regionach i subskrypcje. Rozpoznanie publiczne usługi Azure DNS udostępnia usługę hostingu domen DNS, rozpoznawania nazw przy użyciu infrastruktury Microsoft Azure. Dzięki hostowaniu swoich domen na platformie Azure możesz zarządzać rekordami DNS z zastosowaniem tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń co w przypadku innych usług platformy Azure.
-   [** Subskrypcji] [ SubMgmt] i [zarządzania grupy zasobów][RGMgmt]**. Subskrypcja definiuje fizycznych granic można utworzyć wiele grup zasobów na platformie Azure. Zasobów w subskrypcji są łączone ze sobą w kontenerach logiczną o nazwie grupy zasobów. Grupa zasobów reprezentuje grupę logiczną do organizowania zasobów vDC.
-   [**RBAC**][RBAC]. Za pomocą RBAC jest możliwość mapy ról organizacyjnych wraz z prawa dostępu do określonych zasobów Azure, umożliwiając Ogranicz użytkowników do pewnych podzestaw akcje. Z RBAC można przyznać dostęp, przypisując odpowiednią rolę dla użytkowników, grup i aplikacji w obrębie odpowiedniego zakresu. Zakres przypisania roli może być pojedynczego zasobu, grupy zasobów lub subskrypcji platformy Azure. RBAC umożliwia dziedziczenie uprawnień. Rola przypisana w zakresie nadrzędnej również udziela dostępu do podrzędnych w nim zawarte. Przy użyciu funkcji RBAC, można rozdzielenie obowiązków i udzielić tylko takiego dostępu dla użytkowników, które są niezbędne do wykonywania swoich zadań. Na przykład użycie funkcji RBAC, aby umożliwić jednego pracownika zarządzać maszyn wirtualnych w ramach subskrypcji, gdy inny Zarządzanie bazami danych SQL w ramach tej samej subskrypcji.
-   [**Sieci wirtualnej komunikacji równorzędnej**][VNetPeering]. Funkcja podstawowych używany do tworzenia infrastruktury vDC jest sieci wirtualnej komunikacji równorzędnej, mechanizm, który łączy dwie sieci wirtualnych (sieci wirtualne), w tym samym regionie za pośrednictwem sieci centrum danych Azure lub przy użyciu platformy Azure szkieletu na całym świecie w regionach.

#### <a name="component-type-perimeter-networks"></a>Typ składnika: Sieci obwodowej
[Sieć obwodowa] [ DMZ] składników (znanej także jako sieci DMZ) pozwalają zapewnić łączność w sieci lokalnej lub w sieci centrum danych fizycznych, wraz z dowolnego łączności do i z Internetu. Istnieje również gdy zespoły sieciowe i zabezpieczeń może poświęcać większość czasu.

Pakiety przychodzące powinny przepływać przez urządzenia zabezpieczeń w Centrum, takie jak zapory, identyfikatorów i adresów IP, przed dotarciem do serwerów zaplecza w klienci. Pakiety powiązane z Internetu od obciążenia powinny również przepływać przez urządzenia zabezpieczeń w sieci obwodowej do wymuszania zasad inspekcji i celów inspekcji przed opuszczeniem sieci.

Składniki sieci obwodowej zapewniają następujące funkcje:

-   [Sieci wirtualne][VNet], [przez][UDR], [NSG][NSG]
-   [Urządzenie wirtualne sieci][NVA]
-   [Usługa równoważenia obciążenia][ALB]
-   [Brama aplikacji w][AppGW] / [zapory aplikacji sieci Web][WAF]
-   [Publiczne adresy IP][PIP]

Zazwyczaj centralną IT i zespoły zabezpieczeń mają odpowiedzialność za definicja wymagania oraz operacji w sieci obwodowej.

[![7]][7]

Na powyższym diagramie przedstawiono wymuszania dwie strefy z dostępem do Internetu i sieci lokalnej, zarówno w Centrum. W jednego koncentratora sieci obwodowej Internetem można skalować do obsługi dużej liczby obiektów LOB, za pomocą wielu farmach zapór aplikacji sieci Web (WAFs) i/lub zapory.

[**Sieci wirtualne** ] [ VNet] koncentratora zazwyczaj jest tworzony w sieci wirtualnej z wieloma podsieciami do hostowania różnych typów usług filtrowania i kontrolę ruchu do lub z Internetu za pośrednictwem NVAs, WAFs i na platformie Azure Bramy aplikacji.

[**PRZEZ** ] [ UDR] przy użyciu przez, klienci mogą wdrożyć zapór, Identyfikatory/adresów IP i innych urządzeń wirtualnych i kierować ruchem sieciowym za pomocą tych urządzeń zabezpieczeń do wymuszania zasad granic zabezpieczeń, Inspekcja i inspekcji. Udr mogą być tworzone w Centrum i partnerów, aby zagwarantować, że tranzytów ruchu przez określonych niestandardowych maszyn wirtualnych, sieci wirtualnych urządzeń i używane przez vDC usługi równoważenia obciążenia. Aby zagwarantować ruch wygenerowanych z maszyn wirtualnych znajdują się w drodze gwiazdy poprawne urządzenie wirtualne, przez musi być ustawiona w podsieciach gwiazdy przez ustawienie adres IP frontonu modułu równoważenia obciążenia wewnętrznego następnego przeskoku. Wewnętrzny moduł równoważenia obciążenia dystrybuuje wewnętrzny ruch do urządzeń wirtualnych (puli zaplecza modułu równoważenia obciążenia).

[![8]][8]

[**Sieci wirtualnych urządzeń** ] [ NVA] w Centrum, w sieci obwodowej z dostępem do Internetu zwykle odbywa się za pośrednictwem zapór i/lub zapór aplikacji sieci Web (WAFs) na farmie.

Różnych obiektów LOB często używanych wiele aplikacji sieci web, a te aplikacje mają na celu boryka się z różnych luk w zabezpieczeniach i potencjalnych luk w zabezpieczeniach. Zapory aplikacji sieci Web to specjalny rodzaj produktu używane do wykrywania ataków na aplikacje sieci web (HTTP/HTTPS) bardziej szczegółowo niż ogólny zapory. W porównaniu z technologii zapory tradycji WAFs ma zestaw określone funkcje do ochrony przed zagrożeniami serwerów wewnętrznej sieci web.

Farmy zapory jest grupa zapór praca wspólnie w tej samej wspólnego zarządzania z zestawem reguł zabezpieczeń, aby chronić obciążenia w klienci, kontrolować dostęp do lokalnych i w sieci. Farma zapory ma mniej specjalizowany oprogramowania w porównaniu z zapory aplikacji sieci Web, ale ma aplikacji szeroki zakres do filtrowania i sprawdzić dowolnego typu ruch wychodzący i przychodzący. Zapora farm zwykle są implementowane na platformie Azure za pośrednictwem sieci wirtualnych urządzeń (NVAs), które są dostępne w portalu Azure marketplace.

Zalecane jest korzystanie z jednego zestawu NVAs dla ruchu pochodzącego z Internetem i drugą dla ruchu pochodzącego lokalnymi. Przy użyciu tylko jeden zestaw NVAs zarówno stanowi zagrożenie bezpieczeństwa, ponieważ zapewnia nie granicznej zabezpieczeń między dwoma zestawami ruchu sieciowego. Przy użyciu osobnych NVAs zmniejsza się złożoność sprawdzanie zasad zabezpieczeń i czyni go wyczyścić reguły odpowiadają które przychodzących żądań sieci.

Najbardziej dużych przedsiębiorstw Zarządzanie wielu domen. Usługa DNS platformy Azure może służyć do hostowania rekordów DNS dla określonej domeny. Jako przykład wirtualny adres IP (VIP) usługi Azure zewnętrznej usługi równoważenia obciążenia (lub WAFs) może być zarejestrowany w rekordzie rekordu DNS platformy Azure.

[**Moduł równoważenia obciążenia Azure** ] [ ALB] modułu równoważenia obciążenia Azure oferuje wysoką dostępność usługi warstwy 4 (TCP, UDP), który można dystrybuować przychodzący ruch między wystąpieniami usługi zdefiniowane w zestawie o zrównoważonym obciążeniu. Ruch przesyłany do usługi równoważenia obciążenia z frontonu punktów końcowych (publicznego adresu IP punktów końcowych lub prywatnego adresu IP punktów końcowych) można rozdzielana z lub bez translacji adresów do zestawu puli adresów IP zaplecza (przykłady są; Urządzenie wirtualne sieci lub maszyn wirtualnych).

Moduł równoważenia obciążenia Azure można badanie kondycji także różne wystąpienia serwera i po badanie nie odpowiada usługi równoważenia obciążenia przestaje wysyłania ruchu do wystąpienia złej kondycji. W vDC mamy obecności zewnętrznej usługi równoważenia obciążenia w Centrum (na przykład równoważenie ruchu do NVAs) i klienci (do wykonania zadania, takie jak Równoważenie ruchu między różnych maszyn wirtualnych wielowarstwowej aplikacji).

[**Brama aplikacji w** ] [ AppGW] bramę aplikacji usługi Microsoft Azure jest dedykowany urządzenie wirtualne udostępniające kontroler dostarczania aplikacji (ADC) jako usługa, oferty różnych warstwy 7 równoważenia obciążenia możliwości aplikacji. Umożliwia optymalizowanie wydajności kolektywu serwerów sieci web dzięki przeniesieniu Procesora znacznym kończenia żądań SSL na bramie aplikacji. Zapewnia także inne możliwości routingu warstwy 7, takie jak okrężna dystrybucja ruchu przychodzącego, koligacja sesji na podstawie plików cookie, routing oparty na ścieżkach URL i możliwość hostowania wielu witryn sieci Web za pojedynczą bramą Application Gateway. Zapora aplikacji internetowych jest również udostępniana w ramach jednostki SKU zapory aplikacji internetowych w usłudze Application Gateway. Ta jednostka SKU zapewnia ochronę do aplikacji sieci web z typowych luk w zabezpieczeniach sieci web i luk w zabezpieczeniach. Usługę Application Gateway można skonfigurować jako bramę umożliwiającą dostęp do Internetu, bramę tylko wewnętrzną lub jako kombinację obu tych opcji. 

[**Publiczne adresy IP** ] [ PIP] Azure niektóre funkcje umożliwiają skojarzyć punktów końcowych usługi do publicznego adresu IP, umożliwiający zasobu można uzyskać dostęp z Internetu. Ten punkt końcowy używa translatora adresów sieciowych (NAT), aby kierować ruchem wewnętrzny adres i port na sieć wirtualna platformy Azure. Ta ścieżka jest podstawowy sposób dla zewnętrznego ruchu do przekazania do sieci wirtualnej. Publiczny adres IP można skonfigurować tak, aby określić, jaki ruch jest przekazywany w jak i gdzie jest translacja do sieci wirtualnej.

#### <a name="component-type-monitoring"></a>Typ składnika: monitorowania
Składniki monitorowania zapewniają wgląd i alertów z innych typów składników. Wszystkie zespoły powinny mieć dostęp do monitorowania składników i usług mają dostęp. Jeśli masz scentralizowane pomocy technicznej lub operacji zespołów one musi jest zintegrowany dostęp do danych dostarczonych przez tych składników.

Azure oferuje różne typy rejestrowania i monitorowania usług do śledzenia zachowania Azure hostowanych zasobów. Zarządzanie i sterowanie obciążeń na platformie Azure jest oparte na nie tylko na zbieranie danych dziennika, ale także możliwość na podstawie określonych zdarzeń zgłoszonych akcje wyzwalacza.

[**Azure Monitor** ] [ Monitor] -Azure zawiera wiele usług, wykonujących indywidualnie określonych ról i zadań w obszarze monitorowania. Te usługi dostarczania ze sobą, kompleksowe rozwiązanie umożliwiające obsługę zbierania, analizowanie i działające na podstawie danych telemetrycznych z aplikacji i zasobów platformy Azure, które je obsługują. Może również współpracować do monitorowania zasobów lokalnych krytyczne zapewnić monitorowanie środowiska hybrydowego. Opis narzędzi i dane, które są dostępne jest pierwszym etapem opracowanie pełnej strategii monitorowania aplikacji.

Istnieją dwa główne typy dzienników na platformie Azure:

-   [**Dzienniki aktywności** ] [ ActLog] (określane również jako "Dziennik operacyjny") zapewniają wgląd w operacje wykonywane na zasobów w subskrypcji platformy Azure. Te dzienniki zgłaszać zdarzenia płaszczyzny kontroli dla subskrypcji. Każdy zasób Azure tworzy dzienniki inspekcji.

-   [**Azure dzienników diagnostycznych** ] [ DiagLog] są dzienniki generowane przez zasób, które zawierają rozbudowane, często dane dotyczące operacji tego zasobu. Zawartość tych dzienników zależy od typu zasobu.

[![9]][9]

W vDC jest bardzo ważne, aby śledzić dzienniki grup NSG, szczególnie tych informacji:

-   [**Dzienniki zdarzeń**][NSGLog]: zawiera informacje o jakie reguły NSG są stosowane do maszyn wirtualnych i wystąpień ról na podstawie adresu MAC.
-   [**Licznik dzienniki**][NSGLog]: śledzi, ile razy wykonano każdej reguły NSG na odmawianie lub zezwolić na ruch.

Wszystkie dzienniki mogą być przechowywane na kontach magazynu Azure do inspekcji, analizę statyczną lub wykonywania kopii zapasowych. Gdy dzienniki są przechowywane na koncie magazynu platformy Azure, klientów można pobrać, przygotowywanie, analizowanie przy użyciu różne typy struktur, a następnie wizualizacji tych danych do raportowania stanu i kondycji zasobów w chmurze.

Duże przedsiębiorstwa powinna już zostały nabyte standardowych ramy do monitorowania systemów lokalnych i można rozszerzyć tego framework do integracji dzienniki generowane przez wdrożenia chmury. Dla organizacji, które chcesz zachować wszystkie rejestrowania w chmurze, — Log Analytics [LogAnalytics] jest doskonałym wyborem. Ponieważ analizy dzienników jest zaimplementowany jako usługa w chmurze, można mieć on działa szybko z minimalnym inwestycji w infrastrukturę usług. Analiza dzienników można również zintegrować z składników programu System Center, takich jak System Center Operations Manager, aby rozszerzyć istniejące inwestycje w technologie zarządzania w chmurze.

Analiza dzienników jest usługą platformy Azure, która pomaga zbierania, korelowania wyszukiwania i działają na dane dziennika i wydajności, które są generowane przez systemów operacyjnych, aplikacji i składników infrastruktury chmury. Udostępnia klientom w czasie rzeczywistym operational insights przy użyciu wyszukiwanie zintegrowane i niestandardowe pulpity nawigacyjne, aby analizować wszystkie rekordy we wszystkich obciążeń w vDC.

[Sieci monitora wydajności (NPM)] [ NPM] rozwiązania wewnątrz OMS zapewniają szczegółowe sieci informacji end-to-end, łącznie z pojedynczego widoku sieci platformy Azure i lokalnymi sieciami. Z monitorami określonych ExpressRoute, jak i usługi publiczne.

#### <a name="component-type-workloads"></a>Typ składnika: obciążeń
Składniki obciążenia są, gdzie znajdują się rzeczywista aplikacji i usług. Istnieje również gdzie zespołów deweloperów aplikacji spędzają większość czasu.

Możliwości obciążenia są naprawdę nieskończona. Poniżej przedstawiono kilka typów możliwych obciążenia:

**Aplikacje LOB wewnętrzny**

Aplikacji biznesowych z są krytyczne aplikacji komputera do pracy przedsiębiorstwa. Aplikacje LOB ma pewne cechy wspólne:

-   **Interakcyjne**. Aplikacje BIZNESOWE są interaktywne natury: wprowadzone dane, a wynik/raporty są zwracane.
-   **Opartych na danych**. Aplikacje LOB są znacznym częste dostęp do bazy danych lub innych magazynów danych.
-   **Zintegrowane**. BIZNESOWE aplikacje oferta integracji z innymi systemami, w obrębie lub poza organizacją.

**Klientów witryn sieci web (skierowany do Internetu lub wewnętrzne)** większość aplikacji, które współdziałają z Internetu są witryn sieci web. Platforma Azure oferuje możliwość uruchomienia witryny sieci web na maszynie Wirtualnej IaaS lub z [Azure Web Apps] [ WebApps] lokacji (PaaS). Aplikacje sieci Web platformy Azure obsługuje integrację z sieciami wirtualnymi, umożliwiająca wdrażanie aplikacji sieci Web w gwiazdy z vDC. Podczas przeglądania wewnętrzne połączonej witryny sieci web, dzięki integracji sieci Wirtualnej, nie trzeba ujawniać punkt końcowy aplikacji internetowych, ale można zamiast tego użyj zasobów za pośrednictwem prywatne adresy routingu — do Internetu z sieci prywatnej.

**Big Data/Analytics** podczas konieczne skalowanie w górę do bardzo dużych ilości danych bazy danych może nie skalowanie w górę poprawnie. Technologia Hadoop oferuje systemu równolegle zapytania rozproszone na wielu węzłach. Klienci mają opcję uruchamiania obciążeń danych w maszyny wirtualne IaaS i PaaS ([HDInsight][HDI]). HDInsight obsługuje wdrażanie na podstawie lokalizacji sieci wirtualnej, można wdrożyć w klastrze w gwiazdy vDC.

**Zdarzenia i komunikatów**
[Azure Event Hubs] [ EventHubs] jest usługą wprowadzanie telemetrii w hiperskali, która zbiera, przekształca i przechowuje miliony zdarzeń. Jako rozproszonej platformy przesyłania strumieniowego zapewnia małe opóźnienia i można skonfigurować czas przechowywania, co umożliwia pozyskiwania olbrzymich ilości danych telemetrycznych na platformie Azure i odczytywania danych z wielu aplikacji. Z usługą Event Hubs jednego strumienia może obsługiwać potoków w czasie rzeczywistym i na podstawie partii.

Chmurę wysokiej niezawodnej obsługi komunikatów usługi między aplikacjami i usługami, można zaimplementować za pośrednictwem [Azure Service Bus] [ ServiceBus] czy oferty asynchroniczne komunikatów obsługiwanych przez brokera między klientem a serwerem, wraz z programem strukturę pierwszy w pierwszym out (FIFO) do obsługi komunikatów i możliwości publikowania/subskrypcji.

[![10]][10]

### <a name="multiple-vdc"></a>Wiele vDC
Do tej pory w tym artykule ma fokus w jednego kontrolera vDC opisujący podstawowe składniki i architekturę, która współtworzyć odporność vDC. Funkcje platformy Azure, takich jak zestawy dostępności NVAs, moduł równoważenia obciążenia Azure, zestawy skalowania, oraz innych mechanizmów przyczyniają się do systemu, który pozwala na utworzenie stałe poziomy SLA do usług produkcji.

Jednak pojedynczy vDC znajduje się w jednym regionie i jest narażony na głównych awarii, który może mieć wpływ na cały regionu. Klienci, których chcesz osiągnąć wysoką SLA konieczna jest ochrona usługi do wdrożenia tego samego projektu w VDC dwie (lub więcej), umieszczone w różnych regionach.

Oprócz dotyczy SLA istnieją kilka typowych scenariuszy, w którym wdrażania wielu VDC sens:

-   Obecność regionalne/Global
-   Odzyskiwanie po awarii
-   Mechanizm kierować ruchem między kontrolera domeny

#### <a name="regionalglobal-presence"></a>Obecność regionalne/Global
Centra danych platformy Azure znajdują się w wielu regionach na całym świecie. Podczas wybierania wielu centrów danych Azure, klienci muszą wziąć pod uwagę dwa czynniki związane z: odległości geograficzne i opóźnień. Klienci muszą ocenić geograficzne odległość między VDC i odległość między vDC i użytkowników końcowych, aby oferować najlepsze środowisko użytkownika.

Regionu Azure, gdzie hostowane są VDC muszą być zgodne z przepisami dotyczącymi działalności ustala żadnych właściwości prawne, pod którą działa organizacji.

#### <a name="disaster-recovery"></a>Odzyskiwanie po awarii
Wykonanie planu odzyskiwania po awarii silnie powiązany jest typ obciążenia danych oraz możliwość synchronizacji stanu obciążenia między różnych VDC. Najlepiej, jeśli większość klientów chcesz synchronizować dane aplikacji między wdrożeniami w dwóch różnych VDC do zaimplementowania mechanizm szybkiego awaryjnym. Większość aplikacji są wrażliwy na opóźnienia, a które mogą spowodować potencjalne limitu czasu i opóźnienia w synchronizacji danych.

Synchronizacji lub Puls monitorowania aplikacji w różnych VDC wymaga komunikacji między nimi. Dwa VDC w różnych regionach, mogą zostać połączone za pomocą:

-   Komunikacja równorzędna VNet - równorzędna sieci wirtualnej można połączyć koncentratory w regionach
-   ExpressRoute prywatnej komunikacji równorzędnej, gdy koncentratory vDC są połączone z tym samym obwodu usługi expressroute
-   obwody usługi ExpressRoute wiele połączonych za pośrednictwem sieci firmowej sieci szkieletowej i połączony z siatki vDC obwody usługi ExpressRoute
-   Połączenia sieci VPN lokacja-lokacja między Twojej koncentratorów vDC w każdym regionie Azure

Połączenia sieci wirtualnej komunikacji równorzędnej lub ExpressRoute są zwykle preferowany powodu większej przepustowości i opóźnień spójne podczas przejeżdżających przez za pośrednictwem sieci szkieletowej firmy Microsoft.

Nie ma żadnych magic przepisu, aby zweryfikować aplikacji rozdzielone między dwa (lub więcej), różnych VDC znajdujących się w różnych regionach. Klienci powinni uruchamiać testy sieci kwalifikacji do weryfikowania opóźnienia i przepustowości połączenia i docelowej, czy Replikacja synchroniczna lub asynchroniczna danych jest odpowiedni i cel czas optymalnego odzyskiwania (RTO) można dla obciążeń.

#### <a name="mechanism-to-divert-traffic-between-dc"></a>Mechanizm kierować ruchem między kontrolera domeny
Skuteczne chcemy kierowanie ruchu przychodzącego w jednym kontrolerem domeny do innego opiera się na DNS. [Menedżer ruchu Azure] [ TM] używa mechanizmu systemu nazw domen (DNS) w celu kierowania ruchu przez użytkownika końcowego na najbardziej odpowiednim publiczny punkt końcowy w określonych vDC. Za pomocą sondy Traffic Manager okresowo sprawdza, czy usługa kondycji publiczne punkty końcowe w różnych VDC i w razie awarii tych punktów końcowych, kieruje automatycznie do dodatkowej vDC.

Menedżer ruchu działa na publiczne punkty końcowe systemu Azure i można na przykład do sterowania/odwrócenia ruchu do maszyn wirtualnych platformy Azure i aplikacji sieci Web w odpowiedniej vDC. Menedżer ruchu jest odporność nawet w wypadku niepowodzenie cały region platformy Azure i można kontrolować dystrybucję ruchu użytkowników dla punktów końcowych usług w różnych VDC na podstawie wielu kryteriów (na przykład niepowodzenie usługi w określonych vDC lub wybierając vDC z najniższe opóźnienia sieci dla klienta).

### <a name="conclusion"></a>Podsumowanie
Wirtualne centrum danych jest podejściu do migracji centrum danych w chmurze, która używa kombinacji funkcji i możliwości w celu utworzenia Skalowalna architektura na platformie Azure, które pozwala zmaksymalizować wykorzystanie zasobów chmury, zmniejszenie kosztów i uproszczenia zarządzania systemu. Pojęcie vDC opiera się na topologii partnerzy koncentratora, udostępnia typowe usługi udostępnionych w Centrum, dzięki czemu określonych aplikacji/obciążeń w klienci. VDC odpowiada struktury ról firmy, gdzie różnych działów (centralnym dziale IT, metodyki DevOps, operacji i konserwacji) współdziałają ze sobą, każda z określonej listy ról i obowiązków. VDC spełnia wymagania dotyczące migracji "Przyrostu i Shift", ale również zapewnia wiele zalet wdrożenia natywnego chmury.

## <a name="references"></a>Dokumentacja
Następujące funkcje zostały omówione w tym dokumencie. Kliknij łącza, aby dowiedzieć się więcej.

| | | |
|-|-|-|
|Funkcje sieci|Równoważenie obciążenia|Łączność|
|[Sieci wirtualnych platformy Azure][VNet]</br>[Grupy zabezpieczeń sieci][NSG]</br>[NSG Logs][NSGLog]</br>[Routing zdefiniowane przez użytkownika][UDR]</br>[Urządzenie wirtualne sieci][NVA]</br>[Publiczny adres IP][PIP]</br>[DNS]|[Moduł równoważenia obciążenia Azure (L3) ][ALB]</br>[Brama aplikacji (P7) ][AppGW]</br>[Zapora aplikacji sieci Web][WAF]</br>[Usługi Azure Traffic Manager][TM] |[Komunikacja równorzędna sieci wirtualnej][VNetPeering]</br>[Wirtualna sieć prywatna][VPN]</br>[ExpressRoute][ExR]
|Tożsamość</br>|Monitorowanie</br>|Najlepsze rozwiązania</br>|
|[Azure Active Directory][AAD]</br>[Multi-Factor Authentication][MFA]</br>[Rola dostępu bazowego formantów][RBAC]</br>[Domyślne role usługi AAD][Roles] |[Monitor systemu Azure][Monitor]</br>[Dzienniki aktywności][ActLog]</br>[Dzienniki diagnostyczne][DiagLog]</br>[Microsoft Operations Management Suite][OMS]</br>[Monitor wydajności sieci][NPM]|[Najlepsze rozwiązania w zakresie sieci obwodowej][DMZ]</br>[Zarządzanie subskrypcją][SubMgmt]</br>[Zarządzanie grupami zasobów][RGMgmt]</br>[Limity subskrypcji platformy Azure][Limits] |
|Innymi usługami platformy Azure|
|[Aplikacje sieci Web Azure][WebApps]</br>[HDInsights (Hadoop) ][HDI]</br>[Event Hubs][EventHubs]</br>[Service Bus][ServiceBus]|



## <a name="next-steps"></a>Następne kroki
 - Eksploruj [sieci wirtualnej komunikacji równorzędnej][VNetPeering], technologii podstawa dla projektów vDC gwiazdy
 - Implementowanie [AAD] [ AAD] wprowadzenie [RBAC] [ RBAC] eksploracji
 - Tworzenie subskrypcji i zasobu model zarządzania i RBAC modelu w celu spełnienia struktury, wymagań, a zasady organizacji. Działanie najważniejszych jest planowania. Jak praktyczne Zaplanuj reorganizacji, łączenia, nowych wierszy produktu, itp.

<!--Image References-->
[0]: ./media/networking-virtual-datacenter/redundant-equipment.png "przykłady nakładają się na siebie składnika" 
[1]: ./media/networking-virtual-datacenter/vdc-high-level.png "wysokiego poziomu przykład vDC gwiazdy"
[2]: ./media/networking-virtual-datacenter/hub-spokes-cluster.png "klastra koncentratorów i partnerów"
[3]: ./media/networking-virtual-datacenter/spoke-to-spoke.png "Spoke-to-spoke"
[4]: ./media/networking-virtual-datacenter/vdc-block-level-diagram.png "poziomu diagram blokowy serwera wirtualnego"
[5]: ./media/networking-virtual-datacenter/users-groups-subsciptions.png "użytkowników, grup, subskrypcje i projektów"
[6]: ./media/networking-virtual-datacenter/infrastructure-high-level.png "diagramu wysokiego poziomu infrastruktury"
[7]: ./media/networking-virtual-datacenter/highlevel-perimeter-networks.png "diagramu wysokiego poziomu infrastruktury"
[8]: ./media/networking-virtual-datacenter/vnet-peering-perimeter-neworks.png "równorzędna sieci wirtualnej i obwód sieci"
[9]: ./media/networking-virtual-datacenter/high-level-diagram-monitoring.png "diagramu wysokiego poziomu do monitorowania"
[10]: ./media/networking-virtual-datacenter/high-level-workloads.png "diagramu wysokiego poziomu obciążenia"

<!--Link References-->
[Limits]: https://docs.microsoft.com/azure/azure-subscription-service-limits
[Roles]: https://docs.microsoft.com/azure/role-based-access-control/built-in-roles
[VNet]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview
[NSG]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg
[DNS]: https://docs.microsoft.com/azure/dns/dns-overview
[PrivateDNS]: https://docs.microsoft.com/azure/dns/private-dns-overview
[VNetPeering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview 
[UDR]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview 
[RBAC]: https://docs.microsoft.com/azure/role-based-access-control/overview
[MFA]: https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication
[AAD]: https://docs.microsoft.com/azure/active-directory/active-directory-whatis
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways 
[ExR]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction 
[NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[SubMgmt]: https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-subscription-governance 
[RGMgmt]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview
[DMZ]: https://docs.microsoft.com/azure/best-practices-network-security
[ALB]: https://docs.microsoft.com/azure/load-balancer/load-balancer-overview
[PIP]: https://docs.microsoft.com/azure/virtual-network/resource-groups-networking#public-ip-address
[AppGW]: https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction
[WAF]: https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview
[Monitor]: https://docs.microsoft.com/azure/monitoring-and-diagnostics/
[ActLog]: https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs 
[DiagLog]: https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs
[NSGLog]: https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log
[OMS]: https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview
[NPM]: https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor
[WebApps]: https://docs.microsoft.com/azure/app-service/
[HDI]: https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-introduction
[EventHubs]: https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs 
[ServiceBus]: https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-overview
[TM]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview

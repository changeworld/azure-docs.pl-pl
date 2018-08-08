---
title: Wirtualne centrum danych Azure — Perspektywa sieci
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
ms.openlocfilehash: fc3f334771c11d6917e15628557adfb59051f0f6
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621025"
---
# <a name="azure-virtual-datacenter-a-network-perspective"></a>Wirtualne centrum danych Azure: Perspektywa sieci
**Microsoft Azure**: poruszać się szybciej, Oszczędzaj pieniądze, integracja lokalnych aplikacji i danych

## <a name="overview"></a>Przegląd
Migracji aplikacji lokalnych do platformy Azure, nawet bez wprowadzania żadnych znaczących zmian (z podejścia znanego jako metodą "lift and shift"), zapewnia organizacjom korzyści bezpiecznym i efektywnym środowisku kosztów infrastruktury. Jednak aby umożliwić większość elastyczność dzięki chmurze obliczeniowej, przedsiębiorstwa powinna się rozwijać, ich architektury, aby wykorzystać możliwości platformy Azure. Microsoft Azure oferuje usługi w hiperskali i infrastruktury, funkcji przeznaczonych dla przedsiębiorstw i niezawodności i wiele możliwości na potrzeby łączności hybrydowej. Klienci mogą wybrać opcję dostępu do tych usług w chmurze za pośrednictwem Internetu lub z usługą Azure ExpressRoute, która zapewnia łączność sieci prywatnej. Platforma Microsoft Azure umożliwia klientom bezproblemowo rozszerzyć swoją infrastrukturę do chmury i tworzenie architektury wielowarstwowej. Ponadto partnerzy firmy Microsoft zapewniają poprawione możliwości, udostępniając usługi i zabezpieczeń urządzeń wirtualnych, które są zoptymalizowane pod kątem uruchamiania na platformie Azure.

Ten artykuł zawiera omówienie wzorców i projekty, które może służyć do rozwiązania architektury wątpliwości skalę, wydajność i bezpieczeństwo wielu klientów twarzy rozważają Przechodzenie do chmury. Omówienie Dopasuj różnych ról IT organizacji do zarządzania i nadzór nad systemu jest również omówione, z naciskiem na wymagania dotyczące zabezpieczeń i optymalizacji kosztów.

## <a name="what-is-a-virtual-data-center"></a>Co to jest wirtualne centrum danych?
W czasach, kiedy rozwiązań w chmurze są przeznaczone do jednego, stosunkowo izolowane, aplikacji hosta, w publicznych spektrum. To podejście dobrze działały dla kilka lat temu. Jednak jako korzyści z używania chmury rozwiązania stało się jasne i wielu dużych obciążeń zostały hostowanych w chmurze, adresowania bezpieczeństwo, niezawodność, wydajność i koszt dotyczy wdrożeń w jednym lub większej liczbie regionów stało się ważne przez cały cykl życia usługi w chmurze.

Na poniższym diagramie wdrożenia chmury przedstawiono kilka przykładów luk zabezpieczeń (czerwoną otoczkę) oraz miejsce dla wirtualnych urządzeń sieciowych optymalizacji na potrzeby różnych obciążeń (żółte pole).

[![0]][0]

Wirtualne centrum danych (vDC) powstała na bazie tej konieczności skalowania do obsługi obciążeń przedsiębiorstwa i konieczności rozwiązywania problemów, wprowadzona podczas obsługi aplikacji na dużą skalę w chmurze publicznej.

VDC nie jest po prostu obciążeń aplikacji w chmurze, ale również sieci, zabezpieczeń, zarządzania i infrastruktury (na przykład, DNS i usługa katalogowa). Zwykle zapewnia również prywatne połączenie lokalne Centrum sieci i danych. Gdy coraz więcej obciążeń na platformie Azure, należy wziąć pod uwagę infrastrukturę obsługującą i obiekty, które te obciążenia są umieszczane w. Myśleć dokładnie o strukturze zasobów można uniknąć rozprzestrzenianie setki Wysp"obciążenie", które muszą być zarządzane oddzielnie z przepływu danych niezależnie od, modele zabezpieczeń i zgodności wyzwania.

Wirtualne centrum danych jest zasadniczo zbiorem oddzielne, ale powiązanych jednostek z typowych funkcji pomocniczych, funkcje i infrastruktury. Wyświetlając obciążenia jako vDC zintegrowanego, należy pamiętać niższych kosztach z powodu ekonomii skali, zoptymalizowane zabezpieczenia dzięki centralizacji przepływu komponentów i danych, wraz z łatwiejsze operacji, zarządzanie i kontrole zgodności.

> [!NOTE]
> Jest ważne zrozumieć, że kontrolera vDC **nie** dyskretnych produktów platformy Azure, ale kombinacji różnych funkcji i możliwości, zgodnie z wymaganiami dokładne. vDC to sposób myślenia o obciążeń i użycie platformy Azure do zmaksymalizowania zasobów i możliwości w chmurze. Wirtualnego kontrolera domeny, dlatego jest podejściu na temat tworzenia usług IT na platformie Azure, uwzględniając organizacji ról i obowiązków.

VDC mogą pomóc przedsiębiorstwom osiągnięcia jak obciążeń i aplikacji na platformę Azure w następujących scenariuszach:

-   Hostowanie wielu powiązanych obciążeń
-   Migrowanie obciążeń ze środowiska lokalnego do platformy Azure
-   Implementowanie udostępnionego lub scentralizowanych zabezpieczeń i wymagań dotyczących dostępu na potrzeby różnych obciążeń
-   Mieszanie DevOps i scentralizowane IT odpowiednio dla dużych przedsiębiorstw

Kluczem do odblokowania zalety kontrolera vDC, jest scentralizowane topologii (gwiazdy) przy użyciu różnych funkcji platformy Azure: [sieci wirtualnej platformy Azure][VNet], [sieciowych grup zabezpieczeń] [ NSG], [Komunikacja równorzędna sieci wirtualnych][VNetPeering], [trasy zdefiniowane przez użytkownika (UDR)][UDR]i tożsamość platformy Azure za pomocą [Base roli Access Control (RBAC)][RBAC].

## <a name="who-needs-a-virtual-data-center"></a>Kogo należy wirtualnego centrum danych?
Klientów platformy Azure, który wymaga, aby przenieść więcej niż kilku obciążeń na platformie Azure mogą korzystać z myśleć o za pomocą wspólnych zasobów. W zależności od wielkości nawet pojedyncze aplikacje mogą korzystać z wzorców i składniki używane do tworzenia kontrolera vDC.

Jeśli Twoja organizacja ma scentralizowanych zabezpieczeń sieci, infrastruktury informatycznej, i/lub zgodności zespołu/dział, vDC może pomóc wymusić zasady punktów podziału należności i upewnij się jednolitość typowe składniki, zapewniając swobodę tyle zespoły aplikacji i kontrolki jest odpowiednia dla wymagań.

Organizacje, które chcą DevOps mogą korzystać z pojęć kontrolera vDC Podaj kieszenie autoryzowanych zasobów platformy Azure i upewnij się, mają całkowitą kontrolę w tej grupie (albo subskrypcji lub grupy zasobów w ramach wspólnej subskrypcji), ale do sieci i granice zabezpieczeń pozostają zgodne, zgodnie z definicją scentralizowanych zasad w sieci wirtualnej serwera centralnego i grupie zasobów.

## <a name="considerations-on-implementing-a-virtual-data-center"></a>Uwagi dotyczące implementowania wirtualnego centrum danych
Podczas projektowania vDC, istnieje kilka problemów pivotal, które należy wziąć pod uwagę:

-   Tożsamości i usług katalogowych
-   Infrastruktura zabezpieczeń
-   Łączność z chmurą
-   Łączność w chmurze.

##### <a name="identity-and-directory-service"></a>*Tożsamości i usługi katalogowej*
Tożsamości i usługi katalogowe są kluczowym aspektem wszystkie dane centra lokalnie i w chmurze. Tożsamość jest związana z wszystkimi aspektami dostępu i autoryzacji dla usług w ramach kontrolera vDC. Aby upewnić się, że tylko autoryzowani użytkownicy i procesy dostęp do zasobów oraz Twojego konta platformy Azure, platforma Azure używa kilku typów poświadczeń do uwierzytelniania. Należą do nich haseł (Aby uzyskać dostęp do konta platformy Azure), klucze szyfrowania, podpisom cyfrowym i certyfikatom. [*Usługa Azure Multi-Factor Authentication* (MFA)] [ MFA] to dodatkowa warstwa zabezpieczeń do uzyskiwania dostępu do usług platformy Azure. Usługa Azure MFA zapewnia silne uwierzytelnianie z szerokim zakresem prostych opcji weryfikacji — połączenie telefoniczne, wiadomość tekstowa lub powiadomienie aplikacji mobilnej — i pozwalając użytkownikom na wybór odpowiadającej im metody.

Wszelkie duże przedsiębiorstwo musi zdefiniować proces zarządzania tożsamościami, opisujący Zarządzanie indywidualne tożsamości, ich uwierzytelniania, autoryzacji, ról i uprawnień w ramach lub kontrolera vDC. Cele tego procesu należy w celu zwiększenia bezpieczeństwa i produktywności przy jednoczesnym ograniczeniu kosztów, czas przestoju i powtarzające się zadania ręczne.

/ Przedsiębiorstwa to organizacje mogą wymagać wymagających różnych usług dla innego wiersza z firm (LOB), a pracownicy często mają różne role, jeśli związane z różnymi projektami. VDC wymaga dobrej współpracy między różne zespoły, każdy z definicji określonej roli, w systemach z dobrego zarządzania. Macierz obowiązków, dostępu i uprawnień mogą być bardzo złożone. Do zarządzania tożsamościami w kontrolera vDC jest implementowane za pomocą [ *usługi Azure Active Directory* (AAD)] [ AAD] i kontroli dostępu opartej na rolach (RBAC).

Usługa katalogowa jest udostępnianych informacji infrastruktura lokalizowanie, administrowanie i zarządzanie organizowanie elementów codzienne i zasobów sieciowych. Te zasoby mogą obejmować woluminów, folderów, plików, drukarki, użytkownikom, grupom, urządzeń i innych obiektów. Każdy zasób w sieci jest uważany za obiektu przez serwer katalogu. Informacje o zasobie, są przechowywane jako kolekcja atrybutów skojarzona z tego zasobu lub obiektu.

Logowanie na wszystkich usług online firmy Microsoft opierają się na usłudze Azure Active Directory (AAD) i wymaga innych tożsamości. Usługa Azure Active Directory to kompleksowe rozwiązanie o wysokiej dostępności do zarządzania tożsamościami i dostępem w chmurze, które oferuje podstawowe usługi katalogowe, zaawansowane funkcje zarządzania tożsamościami i zarządzania dostępem do aplikacji. Można zintegrować z usługą Active Directory w środowisku lokalnym, aby włączyć logowanie jednokrotne do wszystkich opartych na chmurze i lokalnie hostowanych (lokalnie) usługi AAD aplikacji. Atrybuty użytkownika usługi Active Directory lokalnych można automatycznie synchronizować do usługi AAD.

Jednego administratora globalnego nie jest wymagany, aby przypisać wszystkie uprawnienia w vDC. Zamiast tego każdy określonej dział (lub grupy użytkowników lub usług w usłudze katalogowej) może mieć uprawnienia wymagane do zarządzania zasobami w ramach vDC. Tworzenie struktury uprawnień wymaga równoważenia. Zbyt wiele uprawnień, co może utrudniać efektywności działania i jest zbyt mało lub luźne uprawnienia mogą zwiększyć zagrożenia dla bezpieczeństwa. Azure opartej na rolach kontrola dostępu (RBAC) pomaga rozwiązać ten problem, oferując precyzyjne zarządzanie dostępem do zasobów kontrolera vDC.

##### <a name="security-infrastructure"></a>*Infrastruktura zabezpieczeń*
Infrastruktura zabezpieczeń, w kontekście vDC, przede wszystkim jest powiązany z podział ruchu w segmencie określonej sieci wirtualnej vDC i jak kontrolować ruch przychodzący i wychodzący przepływy w całym vDC. Azure opiera się na architektury wielodostępnej, który uniemożliwia nieautoryzowanych i niezamierzonych ruchu między wdrożeniami, korzystając z izolacji sieci wirtualnej (VNet), listy kontroli dostępu (ACL), modułów równoważenia obciążenia i filtrów IP, oraz zasady przepływu ruchu. Translator adresów sieciowych (NAT) oddziela ruchu w sieci wewnętrznej od ruchu zewnętrznego.

Usługa Azure Service fabric przydziela zasoby infrastruktury do obciążeń dzierżawcy i zarządza komunikacja do i z maszyn wirtualnych (VM). Funkcja hypervisor platformy Azure wymusza rozdzielenie pamięci i procesów między maszynami wirtualnymi oraz bezpiecznie kieruje ruch sieciowy do dzierżawców systemu operacyjnego gościa.

##### <a name="connectivity-to-the-cloud"></a>*Łączność z chmurą*
VDC musi mieć łączność z sieciami zewnętrznymi w celu oferowania usług dla klientów, partnerów i/lub użytkowników wewnętrznych. Zwykle oznacza to połączenie nie tylko z Internetem, ale do lokalnych centrów danych i sieci.

Klienci mogą tworzyć swoje zasady zabezpieczeń można kontrolować, co i jak określonego kontrolera vDC hostowanej usługi są dostępne z Internetu przy użyciu wirtualnych urządzeń sieciowych (za pomocą inspekcji ruchu i filtrowanie) i niestandardowe, routingu, zasad i filtrowanie (sieci Routing zdefiniowany przez użytkownika i sieciowymi grupami zabezpieczeń).

Przedsiębiorstwa często konieczne jest połączenie VDC do lokalnych centrów danych lub innych zasobów. Łączność między sieciami Azure i lokalne w związku z tym jest kluczowym aspektem podczas projektowania architektury skuteczne. Dwa różne sposoby tworzenia wzajemne połączenie między kontrolera vDC i lokalnych na platformie Azure w przedsiębiorstwach mają: przesyłane za pośrednictwem Internetu i/lub prywatnej bezpośrednich połączeń.

[ **Usługi Azure Site-to-Site VPN** ] [ VPN] jest usługą połączeń przez Internet między sieciami lokalnymi i kontrolera vDC, ustanowione przez bezpieczne, szyfrowane połączenia (tuneli protokołu IPsec/IKE). Usługa Azure połączenia lokacja lokacja jest elastyczny i szybko utworzyć i nie wymaga żadnych dalszych zamówień, jak połączenia za pośrednictwem Internetu.

[**Usługa ExpressRoute** ] [ ExR] jest usługą łączność platformy Azure, która pozwala na tworzenie prywatnych połączeń między kontrolera vDC i sieciami lokalnymi. Połączenia ExpressRoute nie omijają publiczny Internet i oferują lepsze zabezpieczenia, niezawodność i szybkość (10 GB/s) oraz opóźnienia spójne. Usługa ExpressRoute jest bardzo przydatne w przypadku VDC, jak klienci mogą uzyskać korzyści wynikające z reguły zgodności, skojarzone z prywatnych połączeń usługi ExpressRoute.

Wdrażanie połączeń usługi ExpressRoute obejmuje angażowanie dostawcy usługi ExpressRoute. Dla klientów, których potrzebujesz, aby szybko rozpocząć pracę go często początkowo nawiązywania łączności między vDC przy użyciu sieci VPN typu lokacja-lokacja i zasobów lokalnych, a następnie przeprowadzić migrację do połączenia usługi ExpressRoute.

##### <a name="connectivity-within-the-cloud"></a>*Łączność w chmurze.*
[Sieci wirtualne] [ VNet] i [komunikacja równorzędna sieci wirtualnych] [ VNetPeering] podstawowych usług łączności sieci wewnątrz vDC. Sieć wirtualną gwarantuje naturalnych granic izolacji dla kontrolera vDC zasobów i komunikacja równorzędna sieci wirtualnych umożliwia wzajemnego komunikowania się między różnych sieciach wirtualnych, w tym samym regionie platformy Azure lub nawet regionach. Kontrola ruchu w sieci wirtualnej i między sieciami wirtualnymi muszą być zgodne zestaw reguł zabezpieczeń określone za pomocą list kontroli dostępu ([sieciowej grupy zabezpieczeń][NSG]), [wirtualnych urządzeń sieciowych ] [ NVA]i niestandardowe tabele routingu ([trasy zdefiniowanej przez użytkownika][UDR]).

## <a name="virtual-data-center-overview"></a>Omówienie wirtualnego centrum danych

### <a name="topology"></a>Topologia
Model gwiazdy rozszerzone wirtualne centrum danych w jednym regionie platformy Azure

[![1]][1]

Piasta jest centralnym strefy, który kontroluje i sprawdza ruch przychodzący lub wychodzący ruch między różnymi strefami: Internet, lokalnie i szprychy. Topologia gwiazdy zapewnia dział IT efektywnym sposobem wymuszania zasad zabezpieczeń, w centralnej lokalizacji, jednocześnie zmniejszając ryzyko błędnej konfiguracji i zagrożeń.

Centrum zawiera typowe składniki usługi używane przez szprychy. Poniżej przedstawiono kilka przykładów typowych wspólnych centralnej usług:

-   Infrastruktura usługi Windows Active Directory (za pomocą powiązanej usługi AD FS) wymagane do uwierzytelniania użytkowników stron trzecich, uzyskiwanie dostępu z niezaufanymi sieciami, zanim uzyska dostęp do obciążenia w szprysze
-   Usługi DNS, aby rozwiązać nazewnictwa dla obciążenia w szprychy, aby dostęp do zasobów lokalnych i w Internecie
-   Infrastruktury kluczy publicznych, do implementowania logowania jednokrotnego dla obciążeń
-   Sterowanie przepływem (TCP/UDP) między szprychami i Internet
-   Przepływ sterowania między gwiazdy i lokalnych
-   Jeśli to konieczne, przepływ sterowania między szprychy jednego i drugiego

VDC zmniejsza całkowity koszt przy użyciu infrastruktury udostępnionej Centrum między wielu szprych.

Rola w każdej szprysze może być do różnych rodzajów obciążeń hostów. Szprychy mogą również podanie podejściu powtarzalne wdrożenia (na przykład tworzenia i testowania, testy odbiorcze użytkowników przedprodukcyjnych i produkcyjnych) z tego samego obciążenia. Klienci mogą również segregowanie i włączyć różnych grup w organizacji (na przykład grupy metodyka DevOps). Wewnątrz szprychy jest możliwe wdrożenie podstawowego obciążenia lub złożonych obciążeń obejmujące wiele warstw przy użyciu kontroli ruchu między warstwami.

##### <a name="subscription-limits-and-multiple-hubs"></a>Limity subskrypcji i wiele centrów
Na platformie Azure każdy składnik, niezależnie od typu, jest wdrażana w subskrypcji platformy Azure. Izolacja składników platformy Azure w różnych subskrypcjach platformy Azure może spełnić wymagań różnych obiektów LOB, takich jak konfigurowanie różne poziomy dostępu i autoryzacja.

Jednego kontrolera vDC skalować w górę do dużej liczby szprych, mimo że, podobnie jak w przypadku wszystkich systemów IT istnieją ograniczenia platform. Wdrożenie Centrum jest powiązany z określonej subskrypcji platformy Azure, która ma ograniczenia i limity (na przykład, zobacz temat maksymalnej liczby wirtualne sieci równorzędne — [subskrypcji platformy Azure i limity, przydziały i ograniczenia] [ Limits] Aby uzyskać szczegółowe informacje). W przypadkach, gdy limity może to stanowić problem, można skalować w architekturze dalsze przez rozszerzanie modelu z jednego centrum szprychy do klastra gwiazdy. Wiele centrów w jednym lub wielu regionach platformy Azure mogą być połączone ze sobą za pomocą komunikacji równorzędnej sieci wirtualnych, usługi ExpressRoute lub sieci VPN typu lokacja lokacja.

[![2]][2]

Wprowadzenie wiele centrów zwiększa koszty i pracochłonność zarządzania systemu i tylko jest uzasadnione, skalowalność (przykłady: ograniczeń systemowych lub ich nadmiarowością) i regionalne replikacji (przykłady: odzyskiwanie przez użytkownika końcowego wydajności lub po awarii). W scenariuszach wymagających wiele centrów, wszystkie centra powinien Dokładamy wszelkich starań, oferowanie ten sam zestaw usług w celu ułatwienia operacyjnej.

##### <a name="interconnection-between-spokes"></a>Wzajemne połączenie między szprychami
Wewnątrz pojedynczego szprychy jest możliwe zaimplementowanie złożonych obciążeń w wielu warstwach. Konfiguracje obejmujące wiele warstw można zaimplementować przy użyciu podsieci (po jednym dla każdej warstwy) w tej samej sieci wirtualnej i filtrowanie przepływu przy użyciu sieciowych grup zabezpieczeń.

Z drugiej strony Architekt chcieć wdrażanie wielowarstwowych obciążenia między wieloma sieciami wirtualnymi. Korzystając z wirtualną sieć równorzędną połączyć szprychy do innych szprych, w tym samym Centrum lub w różnych centrach. Typowym przykładem tego scenariusza jest to przypadek, gdzie serwery przetwarzania aplikacji znajdują się na jednym gwiazdy (VNet), bazy danych jest wdrażana w różnych gwiazdy (VNet). W tym przypadku jest łatwe łączenie się szprychy za pomocą komunikacji równorzędnej sieci wirtualnej i tym samym uniknąć tranzyt przez koncentrator. Dokładnej weryfikacji architekturze i zabezpieczeniach należy wykonać, aby upewnić się, że pomijanie Centrum nie obejścia ważne zabezpieczeń lub inspekcji punkty, które mogą znajdować się tylko w Centrum.

[![3]][3]

Szprychy mogą również być połączone ze sobą do szprychy, który działa jako koncentrator. Ta metoda tworzy hierarchii dwupoziomowej: szprychy w wyższego poziomu (poziom 0) stają się Centrum szprychy niższe (poziom 1) w hierarchii. Szprychy kontrolera vDC konieczne kierują ruch do Centrum dotrzeć do sieci lokalnej lub w Internecie. Architektura z dwoma poziomami Centrum wprowadza złożonym routingiem, który usuwa zalety relacji proste piasty i szprych.

Mimo że platforma Azure zezwoli złożonych topologii, jest jedną z zasad podstawowych koncepcji kontrolera vDC, powtarzalność i uproszczenia. Aby zminimalizować wysiłek związany z zarządzaniem, projekt proste piasty i szprych jest architektura referencyjna vDC zalecane.

### <a name="components"></a>Składniki
Wirtualne centrum danych składa się z czterech typów podstawowych składników: **infrastruktury**, **sieci obwodowych**, **obciążeń**, i **monitorowanie**.

Każdy typ składnika składa się z różnych funkcji platformy Azure i zasobów. Z kontrolera vDC składa się z wystąpień wiele typów składników i wiele zmian tego samego typu składnika. Na przykład może mieć wiele wystąpień różnych, logicznie oddzielone obciążenia, które reprezentują różne aplikacje. Umożliwia te różne typy składników i wystąpień ostatecznie Tworzenie kontrolera vDC.

[![4]][4]

Architektura wysokiego poziomu poprzedniego vDC przedstawia różne typy składników używane w różnych strefach topologii szprychy koncentratora. Na diagramie przedstawiono składniki infrastruktury w różnych części architektury.

Jak dostęp dobrze (dla środowiska lokalnego kontrolera domeny lub kontrolera vDC) prawa i uprawnienia powinny być oparte na grupach. Rozwiązywania problemów związanych z grupami, zamiast poszczególnych użytkowników pomaga spójnie utrzymywanie zasady dostępu między zespołami i pomocy w minimalizuje błędów konfiguracji. Przypisywanie i usuwanie użytkowników z odpowiednich grup i pomaga aktualizowanie uprawnienia określonego użytkownika.

Każda grupa roli musi mieć unikatowy prefiks w ich nazw, dzięki czemu można łatwo identyfikować ich grupę, do której jest skojarzony z obciążeniem, które. Na przykład obciążenie hostingu usługi uwierzytelniania może być grupy o nazwie *AuthServiceNetOps, AuthServiceSecOps, AuthServiceDevOps i AuthServiceInfraOps.* Podobnie dla scentralizowane ról lub ról, które nie są związane z określonej usługi, może być poprzedzony znakami "Corp" *CorpNetOps* na przykład.

Wiele organizacji w celu dostarczenia poważnej awarii ról zastosować zmianę z następujących grup:

-   *Centralna grupa IT (Corp)* ma prawa własności do kontrolowania składników infrastruktury (na przykład sieci i zabezpieczeń), a w związku z tym musi mieć rolę współautora w ramach subskrypcji (i mają kontrolę nad Centrum) i prawa Współautor sieci w szprychy. Dużej organizacji często podziału obowiązków zarządzania między wiele zespołów, takich jak; Operacje sieciowe (CorpNetOps) grupy (wyłączne fokus na temat sieci) i grupy operacji zabezpieczeń (CorpSecOps) (odpowiedzialny za zasad zapory i zabezpieczeń). W tym konkretnym przypadku dwie różne grupy muszą zostać utworzone dla przypisania tych ról niestandardowych.
-   *Dev & grupę testową (AppDevOps)* jest odpowiedzialny za wdrażanie obciążeń (aplikacji lub usług). Ta grupa ma rola Współautor maszyny wirtualnej dla wdrożeń rozwiązań IaaS i/lub jeden lub więcej Współautor PaaS ról (zobacz [wbudowane role based Access Control][Roles]). Opcjonalnie zespołu programistycznego i testowego może być konieczne zapewnienie widoczności na zasady zabezpieczeń (NSG) i zasad routingu (UDR) wewnątrz koncentratora lub określonych szprychy. W związku z tym oprócz ról współautora dla obciążeń, tej grupy należy również rolę czytnika danych sieciowych.
-   *Działanie i utrzymanie grupy (CorpInfraOps lub AppInfraOps)* odpowiedzialny za zarządzanie obciążenia w środowisku produkcyjnym. Ta grupa musi być współautorem subskrypcji obciążenia na żadne subskrypcje produkcyjne. W niektórych organizacjach może być również ocenić, czy potrzebują dodatkowych eskalacji pomocy technicznej zespołu grupy z rolą współautora subskrypcji w środowisku produkcyjnym i w ramach subskrypcji centralnego, aby rozwiązać potencjalne problemy z konfiguracją w produkcji środowisko.

VDC ma struktury, tak aby grup utworzonych dla centralnej grup IT zarządzanie Centrum miały odpowiadających im grup na poziomie obciążenia. Oprócz zarządzanie zasobami Centrum hub tylko centralnej grup IT będą mogli kontrolować dostęp zewnętrzny i najwyższego poziomu uprawnień w ramach subskrypcji. Jednak grupy obciążenia będzie kontrolować zasoby i uprawnienia ich sieci wirtualnej niezależnie w centralnym dziale IT.

VDC musi być dzielony na partycje bezpieczne hostowanie wielu projektów przez innego wiersza z firmy (LOB). Wszystkie projekty wymagają różnych środowiskach izolowane (Dev, UAT, produkcja). Użycie osobnych subskrypcji platformy Azure dla każdego z tych środowisk zapewniają izolację fizyczną.

[![5]][5]

Na powyższym diagramie przedstawiono relację między projektami w organizacji, użytkownikom, grupom i środowiska, w których są wdrażane składników platformy Azure.

Zwykle w infrastruktury informatycznej środowiska (lub warstwy) to system, w której wiele aplikacji są wdrażane i są stosowane. Dla dużych przedsiębiorstw użycia środowiska deweloperskiego (gdzie zmiany pierwotnie wykonywane i testowane) i w środowisku produkcyjnym (co użytkownicy końcowi używają). Tych środowisk rozdziela się często z kilku przejściowe Between go, aby zezwolił we wdrożeniu etapowym (wdrażanie), testowanie i wycofania w razie problemów. Wdrożenie architektury się istotnie różnić, ale zazwyczaj nadal występuje proces podstawowy, od rozwoju (DEV) i kończąc na produkcyjne (PROD).

Architektura dla tych typów, wielowarstwowych środowisk składa się z DevOps (programowania i testowania), przeprowadzających testowanie Akceptacyjne (wdrażanie przejściowe) i środowisk produkcyjnych. Organizacje mogą korzystać z jednego lub wielu dzierżaw usługi Azure AD, aby zdefiniować dostęp i uprawnienia do tych środowisk. Poprzedni diagram przedstawia przypadek w przypadku, gdy dwóch różnych dzierżaw usługi Azure AD są używane: jeden dla metodyki DevOps i UAT, a druga klasy przeznaczona wyłącznie do celów produkcyjnych.

Obecność z różnymi usługami Azure AD dzierżaw wymusza rozdzielenie między środowiskami. Tej samej grupy użytkowników (na przykład centralnym dziale IT) wymaga uwierzytelnienia, przy użyciu innego identyfikatora URI dostępu do innej dzierżawy usługi AD modyfikowanie ról lub uprawnień środowiska DevOps lub produkcji projektu. Obecność uwierzytelniania innego użytkownika, dostęp do różnych środowisk zmniejsza możliwe awarii i innych problemów spowodowanych błędami ludzkimi.

#### <a name="component-type-infrastructure"></a>Typ składnika: infrastruktura
Ten typ składnika jest, gdzie większość wspierająca infrastruktura znajduje się. Jest również gdzie swoje scentralizowane IT, bezpieczeństwa i/lub zespoły zgodności spędzają większość czasu.

[![6]][6]

Składniki infrastruktury zapewniają wzajemne połączenie między różnymi składnikami vDC i znajdują się w Centrum i szprychy. Odpowiedzialność za utrzymanie składników infrastruktury i zarządzanie nimi, zwykle jest przypisany do centralnej IT i/lub zespół ds. zabezpieczeń.

Jedną z podstawowych zadań w zespole infrastruktury IT ma na celu zagwarantowania spójności schematów adresów IP w całym przedsiębiorstwie. Prywatny adres IP przestrzeń adresowa przypisany do kontrolera vDC musi być zgodne, a nie nakładających się przy użyciu prywatnych adresów IP przypisanych w sieciach lokalnych.

Gdy translatora adresów Sieciowych na routerach granicznych w środowisku lokalnym lub w środowiskach platformy Azure można uniknąć konfliktów adresów IP, dodaje kompilacji do składników infrastruktury. Uproszczenie zarządzania jest jedną z podstawowych celów kontrolera vDC, więc przy użyciu translatora adresów Sieciowych, aby obsłużyć uwagi IP nie jest zalecanym rozwiązaniem.

Składniki infrastruktury zawiera następujące funkcje:

-   [**Tożsamości i usługi katalogowe**][AAD]. Dostęp do każdego typu zasobu na platformie Azure jest kontrolowany przez tożsamość przechowywanych w usłudze katalogowej. Usługa katalogowa przechowuje nie tylko listę użytkowników, ale również prawa dostępu do zasobów w określonej subskrypcji platformy Azure. Te usługi może istnieć tylko w chmurze lub mogą być synchronizowane z lokalną tożsamością, przechowywane w usłudze Active Directory.
-   [**Sieć wirtualna**][VPN]. Sieci wirtualne są jednym z głównych składników vDC i umożliwiają tworzenie granicę izolacji ruchu na platformie Azure. Wirtualna sieć składa się z jednego lub wielu segmentów sieci wirtualnej, każdy z określonego adresu IP prefiksu sieci (podsieci). Sieć wirtualna definiuje obszar obwodowej wewnętrznego gdzie maszyn wirtualnych IaaS i PaaS usług mogą nawiązywać komunikację prywatną. Maszyny wirtualne (i usług PaaS) w jednej sieci wirtualnej nie może komunikować się bezpośrednio do maszyn wirtualnych (i usług PaaS) w innej sieci wirtualnej, nawet jeśli obie sieci wirtualne są tworzone przez tego samego klienta w tej samej subskrypcji. Izolacja jest krytyczne właściwości, które gwarantuje, że maszyny wirtualne klientów i komunikacja pozostanie w prywatnej sieci wirtualnej.
-   [**TRASA ZDEFINIOWANA PRZEZ UŻYTKOWNIKA**][UDR]. Ruch w sieci wirtualnej odbywa się domyślnie na podstawie tabeli routingu systemu. Definiowanie przez użytkownika trasy jest niestandardowe tabele routingu, które administratorzy sieci można skojarzyć z co najmniej jednej podsieci, aby zastąpić zachowanie tabeli routingu systemu i definiują ścieżki komunikacji w ramach sieci wirtualnej. Obecność tras zdefiniowanych przez użytkownika gwarancje tego ruchu wychodzącego z przesyłania szprychy za pomocą określonych niestandardowych maszyn wirtualnych i/lub sieciowych urządzeń wirtualnych i równoważenia obciążenia jest obecny w piaście i szprychy.
-   [**NSG**][NSG]. Sieciowa grupa zabezpieczeń znajduje się lista reguł zabezpieczeń, które działają jako porty IP źródła, docelowego adresu IP, protokołów, portów źródłowych adresów IP i docelowego adresu IP z filtrowaniem ruchu. Sieciowa grupa zabezpieczeń można zastosować do podsieci skojarzonych z Maszyną wirtualną platformy Azure i / lub karty wirtualnej karty Sieciowej. Sieciowe grupy zabezpieczeń są niezbędne do zaimplementowania kontroli prawidłowego przepływu w Centrum i szprychy. Poziom zabezpieczeń udostępnianych przez sieciową grupę zabezpieczeń jest funkcją, które porty należy otworzyć i w jakim celu. Klienci powinni zastosować filtry VM dodatkowe za pomocą zapory oparte na hoście, takie jak IPtables lub zaporę Windows.
-   [**DNS**][DNS]. Rozpoznawanie nazw zasobów w wirtualnych vDC znajduje się za pośrednictwem systemu DNS. System Azure oferuje usługi DNS dla obu [publicznych][DNS] i [prywatnej] [ PrivateDNS] rozpoznawania nazw. Strefy prywatne zapewniają rozpoznawanie nazw zarówno w sieci wirtualnej, jak i między różnymi sieciami wirtualnymi. Może mieć stref prywatnych nie tylko zakresu, w sieciach wirtualnych, w tym samym regionie, ale także w różnych regionach i subskrypcje. Rozpoznawanie publicznych usługę Azure DNS usługa hostingowa przeznaczona dla domen DNS, która umożliwia rozpoznawanie nazw przy użyciu infrastruktury Microsoft Azure. Dzięki hostowaniu swoich domen na platformie Azure możesz zarządzać rekordami DNS z zastosowaniem tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń co w przypadku innych usług platformy Azure.
-   [**Subskrypcja** ] [ SubMgmt] i [ **Zarządzanie grupami zasobów**][RGMgmt]. Subskrypcja definiuje granicę naturalnych utworzyć wiele grup zasobów na platformie Azure. Zasoby w subskrypcji są łączone ze sobą w kontenerach logiczną o nazwie grupy zasobów. Grupa zasobów reprezentuje grupę logiczną, aby zorganizować zasoby vDC.
-   [**RBAC**][RBAC]. Przy użyciu RBAC istnieje możliwość mapy ról organizacyjnych wraz z uprawnienia do dostępu do określonych zasobów platformy Azure, co pozwala ograniczyć użytkowników do pewnych podzbiorem akcji. Przy użyciu RBAC można przyznać dostęp, przypisując odpowiednie role do użytkowników, grup i aplikacji w zakresie odpowiednie. Zakres przypisania roli może być subskrypcji platformy Azure, grupę zasobów lub pojedynczy zasób. RBAC umożliwia dziedziczenie uprawnień. Rola przypisana na zakresie nadrzędnym udziela również dostępu do elementów podrzędnych w nim zawarte. Przy użyciu funkcji RBAC, można segregować obowiązki i udzielać uprawnień dostępu do użytkowników, które są im niezbędne do wykonywania swoich zadań. Na przykład użyć RBAC, aby umożliwić jednego pracownika zarządzać maszyn wirtualnych w ramach subskrypcji, gdy inny zarządzać baz danych SQL w ramach tej samej subskrypcji.
-   [**Komunikacja równorzędna sieci wirtualnych**][VNetPeering]. Podstawową cechą użyty do utworzenia infrastruktury vDC jest komunikacja równorzędna sieci wirtualnych, mechanizm, który łączy dwie sieci wirtualne (Vnet), w tym samym regionie za pośrednictwem sieci centrum danych platformy Azure lub przy użyciu sieci szkieletowej platformy Azure na całym świecie w wielu regionach.

#### <a name="component-type-perimeter-networks"></a>Typ składnika: Sieci obwodowej
[Sieć obwodowa] [ DMZ] składników (znany także jako sieci DMZ) umożliwiają połączenie sieciowe z sieci lokalnej lub sieci centrum danych fizycznych, wraz z łączności z Internetem i. Jest również, gdzie Twoje zespoły sieciowe i zabezpieczeń prawdopodobnie spędzają większość czasu.

Pakiety przychodzące powinna przepływać za pośrednictwem urządzenia zabezpieczeń w Centrum, takich jak zapory, Identyfikatory i systemy IPS, przed dotarciem do serwerów zaplecza w szprychy. Pakiety z Internetu od obciążenia należy również przepływać za pośrednictwem urządzenia zabezpieczeń w sieci obwodowej, wymuszanie zasad zostaje wyłączone, inspekcji i celów inspekcji przed opuszczeniem sieci.

Składniki sieci obwodowej zapewniają następujące funkcje:

-   [Sieci wirtualne][VNet], [trasy zdefiniowanej przez użytkownika][UDR], [sieciowej grupy zabezpieczeń][NSG]
-   [Wirtualne urządzenie sieciowe][NVA]
-   [Moduł równoważenia obciążenia][ALB]
-   [Usługa Application Gateway][AppGW] / [zapory aplikacji sieci Web][WAF]
-   [Publiczne adresy IP][PIP]

Zazwyczaj centralnego IT i zespoły zabezpieczeń mają odpowiedzialność za definicja wymagania oraz jej operacje w sieci obwodowej.

[![7]][7]

Na powyższym diagramie przedstawiono wymuszania co dwie z dostępem do Internetu i siecią lokalną, zarówno w Centrum. W jednym Centrum sieci obwodowej z Internetem można skalować do obsługi dużej liczby obiektów LOB, przy użyciu wielu farmach zapory aplikacji sieci Web (Waf) i/lub zapory.

[**Sieci wirtualne** ] [ VNet] Centrum zwykle jest oparta na sieci wirtualnej z wieloma podsieciami do obsługi różnych typów usług, filtrowanie i sprawdzając ruch do i z Internetu za pośrednictwem urządzenia WUS, Waf i platformy Azure Bramy aplikacji.

[**Trasa zdefiniowana przez użytkownika** ] [ UDR] przy użyciu trasy zdefiniowanej przez użytkownika, klienci mogą wdrażać zapór, IDS/IPS i innych urządzeń wirtualnych i kierowanie ruchem sieciowym za pomocą tych urządzeń zabezpieczeń do wymuszania zasad granicy zabezpieczeń, Inspekcja i inspekcji. Można tworzyć tras zdefiniowanych przez użytkownika w Centrum i szprychy w celu zagwarantowania, że tranzytu ruchu przy użyciu określonych niestandardowych maszyn wirtualnych, wirtualne urządzenia sieciowe i używane przez kontrolera vDC modułów równoważenia obciążenia. Aby zagwarantować, że ruch generowany na podstawie maszyn wirtualnych znajdują się w przesyłania szprychy do poprawne urządzenie wirtualne, trasa zdefiniowana przez użytkownika musi być ustawiona w podsieciach szprychy, ustawiając frontonu adres IP wewnętrznego modułu równoważenia obciążenia jako następnego przeskoku. Wewnętrzny moduł równoważenia obciążenia dystrybuuje ruch wewnętrzny do urządzeń wirtualnych (puli zaplecza modułu równoważenia obciążenia).

[![8]][8]

[**Sieciowych urządzeń wirtualnych** ] [ NVA] w piaście, sieci obwodowej z dostępem do Internetu zwykle odbywa się za pośrednictwem farmy zapory i/lub zapory aplikacji sieci Web (Waf).

Różnych obiektów LOB zazwyczaj używają wielu aplikacji sieci web, a te aplikacje zwykle borykają się z różnych luk w zabezpieczeniach i potencjalne luki w zabezpieczeniach. Zapory aplikacji sieci Web to specjalny rodzaj produktu używane do wykrywania ataków na aplikacje sieci web (HTTP/HTTPS) omówiona bardziej szczegółowo niż ogólny zapory. W porównaniu z technologia zapory tradycji sieciowi mieć zestaw określonych funkcji, aby chronić serwery sieci web wewnętrznej przed zagrożeniami.

Farma zapory znajduje się w grupie współpracującą w ramach tego samego wspólnego zarządzania z zestawem reguł zabezpieczeń w celu włączenia ochrony obciążeń hostowanych w szprychy, zapory i kontrola dostępu do sieci lokalnej. Farma zapory ma mniej wyspecjalizowany oprogramowania w porównaniu z zaporą aplikacji internetowych, ale nie ma aplikacji szerokiego zakresu do filtrowania i sprawdzić ruch wychodzący i przychodzący dowolnego typu. Farm zapory są zwykle implementowane na platformie Azure za pośrednictwem wirtualnych urządzeń sieciowych (urządzeń WUS), które są dostępne w witrynie Azure marketplace.

Zaleca się używać jednego zestawu urządzeń WUS dla ruchu pochodzącego z Internetu, a drugą dla ruchu pochodzącego w środowisku lokalnym. Przy użyciu tylko jeden zestaw urządzeń WUS dla obu stanowi zagrożenie bezpieczeństwa, ponieważ on nie zapewnia obwodu zabezpieczeń między dwoma zestawami ruchu sieciowego. Przy użyciu oddzielnych urządzeń WUS zmniejsza złożoność sprawdzania zasad zabezpieczeń i wyraźnie, które reguły dotyczą którego przychodzącego żądania sieciowego.

Większość dużych przedsiębiorstw Zarządzanie wieloma domenami. Usługa DNS platformy Azure może służyć do hostowania rekordów DNS dla określonej domeny. Jako przykład można zarejestrować wirtualnego adresu IP (VIP) modułu równoważenia obciążenia zewnętrzne z platformy Azure (lub Waf) w rekordzie rekordu DNS platformy Azure.

[**Usługa Azure Load Balancer** ] [ ALB] równoważenia obciążenia platformy Azure oferuje wysoką dostępność usługi warstwy 4 (TCP, UDP), który można dystrybuować ruch przychodzący między wystąpieniami usługi zdefiniowane w zestawie z równoważeniem obciążenia. Może być rozprowadzany ruch wysyłany do modułu równoważenia obciążenia z frontonu punktów końcowych (publicznych adresów IP punktów końcowych lub prywatnego adresu IP punktów końcowych), z lub bez translatora adresów do zestawu puli adresów IP zaplecza (przykłady są; Sieciowych urządzeń wirtualnych lub maszyn wirtualnych).

Usługa Azure Load Balancer można badanie kondycji także różne wystąpienia serwera, a Jeśli sonda nie odpowiada moduł równoważenia obciążenia zaprzestaje wysyłania ruchu do wystąpienia usługi w złej kondycji. W vDC mamy obecności zewnętrznym modułem równoważenia obciążenia w Centrum (na przykład równoważenie ruchu do urządzeń WUS), a w szprychy (do wykonania zadania, takie jak równoważenia ruchu między różnych maszyn wirtualnych dla wielowarstwowej aplikacji).

[**Usługa Application Gateway** ] [ AppGW] Microsoft Azure Application Gateway to dedykowane urządzenie wirtualne udostępniające kontroler dostarczania aplikacji (ADC) jako usługa oferująca różne warstwy 7 równoważenia obciążenia możliwości w aplikacji. Umożliwia optymalizowanie wydajności farmy sieci web dzięki przeniesieniu procesor CPU o znacznym wykorzystaniu kończenia żądań SSL na bramie aplikacji. Zapewnia także inne możliwości routingu warstwy 7, takie jak okrężna dystrybucja ruchu przychodzącego, koligacja sesji na podstawie plików cookie, routing oparty na ścieżkach URL i możliwość hostowania wielu witryn sieci Web za pojedynczą bramą Application Gateway. Zapora aplikacji internetowych jest również udostępniana w ramach jednostki SKU zapory aplikacji internetowych w usłudze Application Gateway. Ta jednostka SKU oferuje ochronę aplikacji sieci web z typowych internetowymi wykorzystującymi luki w zabezpieczeniach. Usługę Application Gateway można skonfigurować jako bramę umożliwiającą dostęp do Internetu, bramę tylko wewnętrzną lub jako kombinację obu tych opcji. 

[**Publiczne adresy IP** ] [ PIP] funkcji niektóre platformy Azure umożliwiają skojarzenie punktów końcowych usługi dla publicznego adresu IP, umożliwiający zasobu, można uzyskać dostęp z Internetu. Ten punkt końcowy korzysta z translacji adresów sieciowych (NAT) do kierowania ruchu do wewnętrznych adresów i portów w sieci wirtualnej platformy Azure. Ta ścieżka jest podstawowym sposobem dla ruchu zewnętrznego do przekazania do sieci wirtualnej. Publiczne adresy IP można skonfigurować tak, aby określić, jaki ruch jest przekazywany w jak i gdzie jest tłumaczony na sieć wirtualną.

#### <a name="component-type-monitoring"></a>Typ składnika: monitorowanie
Składniki monitorowania zapewniają widoczność i alertów z innych typów składników. Wszystkie zespoły powinni mieć dostęp do monitorowania dla składników i usług, których mają dostęp. W przypadku zespołów scentralizowane pomocy technicznej lub operacji one należałoby zintegrowano dostęp do danych dostarczone przez te składniki.

Platforma Azure oferuje różne rodzaje rejestrowanie i monitorowanie usług, aby śledzić zachowanie Azure hostowanych zasobów. Zarządzanie i kontrolę nad obciążeń na platformie Azure jest nie tylko na zbieranie danych dziennika, ale także zdolność do wyzwalania akcji na podstawie konkretnych zgłoszonych zdarzeń.

[**Usługa Azure Monitor** ] [ Monitor] -Azure obejmuje wiele usług, wykonujących indywidualnie określonej roli lub zadania w obszarze monitorowania. Razem usługi te zapewniają kompleksowe rozwiązanie umożliwiające zbieranie, analizowanie oraz działanie na podstawie danych telemetrycznych z aplikacji użytkownika oraz zasobów platformy Azure, które je obsługują. Mogą one również służyć do monitorowania krytycznych zasobów lokalnych, aby zapewnić hybrydowe środowisko monitorowania. Zapoznanie się z dostępnymi narzędziami i danymi stanowi pierwszy etap tworzenia pełnej strategii monitorowania aplikacji.

Istnieją dwa główne typy dzienników na platformie Azure:

-   [**Dzienniki aktywności** ] [ ActLog] (określane również jako "Dziennik operacyjny") udostępniają szczegółowe dane operacji wykonywanych na zasobach w subskrypcji platformy Azure. Te dzienniki zgłaszać zdarzenia płaszczyznę kontroli dla subskrypcji. Każdy zasób platformy Azure tworzy dzienniki inspekcji.

-   [**Dzienniki diagnostyczne platformy Azure** ] [ DiagLog] dzienników generowanych przez zasób, które zapewniają bogate, często dane o działaniu tego zasobu. Zawartość tych dzienników jest zależna od typu zasobu.

[![9]][9]

W vDC jest bardzo ważne śledzić dzienniki sieciowych grup zabezpieczeń, szczególnie te informacje:

-   [**Dzienniki zdarzeń**][NSGLog]: zawiera informacje dotyczące reguły sieciowej grupy zabezpieczeń, które są stosowane do maszyn wirtualnych i ról wystąpień na podstawie adresu MAC.
-   [**Licznik dzienniki**][NSGLog]: śledzi, ile razy każdą regułę sieciowej grupy zabezpieczeń został wykonany w celu odmowy lub zezwolić na ruch.

Wszystkie dzienniki mogą być przechowywane na kontach magazynu Azure do inspekcji, analizę statyczną lub wykonywania kopii zapasowych. Jeśli dzienniki są przechowywane na koncie usługi Azure storage, klienci mogą Użyj różne rodzaje struktur do pobrania, przygotowania, analizowanie i wizualizację tych danych do raportowania stanu i kondycji zasobów w chmurze.

Duże przedsiębiorstwa powinna już uzyskali Standardowa Architektura służąca do monitorowania systemów lokalnych i można rozszerzyć tej struktury, aby zintegrować dzienniki generowane przez wdrożeń w chmurze. W przypadku organizacji, które chcą zachować wszystkie rejestrowania w chmurze [Log Analytics] [... / / log-analytics — Przegląd usługi log analytics MD] jest doskonałym wyborem. Ponieważ usługi Log Analytics jest zaimplementowany jako usługa w chmurze, możesz mieć go działa szybko przy minimalnych inwestycjach w usługi infrastruktury. Usługa log Analytics można również zintegrować ze składnikami programu System Center, takich jak System Center Operations Manager, aby rozszerzyć istniejące inwestycje zarządzania w chmurze.

Usługa log Analytics to usługa platformy Azure, która pomaga gromadzić, korelować, wyszukiwanie i działają na dane dzienników i wydajności, które są generowane przez systemy operacyjne, aplikacje i składniki chmury infrastruktury. Daje ona klientów w czasie rzeczywistym operational insights za pomocą zintegrowanej funkcji wyszukiwania i niestandardowym pulpitom nawigacyjnym umożliwiającym analizowanie wszystkich obciążeń w vDC wszystkie rekordy.

[Network Performance Monitor (NPM)] [ NPM] rozwiązania w pakiecie OMS może zapewnić sieci szczegółowe informacje end-to-end, łącznie z pojedynczego widoku dla sieci platformy Azure i sieciami lokalnymi. Z monitorami określonego dla usługi ExpressRoute i usług publicznych.

#### <a name="component-type-workloads"></a>Typ składnika: obciążeń
Składniki obciążenia są na tym, gdzie znajdują się Twoje rzeczywiste aplikacje i usługi. Jest również, gdzie zespołom programistycznym aplikacji spędzają większość czasu.

Możliwości obciążenia są naprawdę nieskończone. Poniżej przedstawiono kilka typów możliwych obciążeń:

**Aplikacje LOB wewnętrznego**

Line-of-business, aplikacje są krytyczne aplikacje komputerowe ciągłego działania przedsiębiorstwa. Aplikacje LOB mają niektóre typowe cechy:

-   **Interaktywne**. Aplikacje LOB są interaktywne zgodnie z naturą: dane są wprowadzane, a wynik/raporty są zwracane.
-   **W oparciu o dane**. Aplikacje LOB to dane o znacznym wykorzystaniu częste dostęp do bazy danych lub innego urządzenia pamięci masowej.
-   **Zintegrowane**. BIZNESOWE aplikacje oferty integracji z innymi systemami w obrębie lub poza organizacją.

**Witryny sieci web (dostępnego z Internetu lub wewnętrzne) przeznaczonych dla klientów** większość aplikacji, które współdziałają z Internetem to witryny sieci web. Platforma Azure oferuje możliwość uruchomienia witryny sieci web na Maszynie wirtualnej IaaS lub [Azure Web Apps] [ WebApps] lokacji (PaaS). Usługa Azure Web Apps obsługuje integrację z sieciami wirtualnymi, które umożliwia wdrażanie aplikacji internetowych w szprysze vDC. Po wyświetleniu wewnętrzne dostępnego z witryny sieci web, za pomocą integracji sieci Wirtualnej nie ma potrzeby uwidocznić punkt końcowy dla aplikacji internetowych, ale można użyć zasobów za pomocą prywatnych adresów Routing-internet z prywatnej sieci wirtualnej.

**Analizy danych bigdata/** podczas konieczne skalowanie w górę do bardzo dużych ilości danych, baz danych może nie skalować w górę prawidłowo. Technologia Hadoop oferuje system do uruchamiania rozproszonych zapytań równolegle na dużą liczbę węzłów. Klienci mają możliwość uruchamiania obciążeń danych na maszynach wirtualnych IaaS lub PaaS ([HDInsight][HDI]). HDInsight obsługują wdrażanie w sieci wirtualnej, na podstawie lokalizacji, którą można wdrożyć w klastrze na platformie szprychy vDC.

**Zdarzenia i komunikaty**
[usługi Azure Event Hubs] [ EventHubs] jest usługą pozyskiwania danych telemetrycznych w hiperskali, która umożliwia zbieranie, przekształcanie i przechowywanie milionów zdarzeń. To rozproszona platforma przesyłania strumieniowego zapewnia małe opóźnienia i możliwość konfigurowania czasu przechowywania, co umożliwia pozyskiwanie dużych ilości danych telemetrycznych do usługi Azure oraz odczytywania danych z wielu aplikacji. Za pomocą usługi Event Hubs jeden strumień może obsługiwać potoki w czasie rzeczywistym i opartych na partiach.

Przesyłania komunikatów usługi między aplikacjami i usługami w chmurze o wysokiej niezawodności, może być implementowany przez [usługi Azure Service Bus] [ ServiceBus] czy ofert asynchronicznych komunikatów obsługiwanych przez brokera między klientem i serwerem oraz wraz z strukturalnych pierwszy wejściu — pierwszy na wyjściu (FIFO) komunikatów i możliwości publikowania/subskrybowania.

[![10]][10]

### <a name="multiple-vdc"></a>Wiele kontrolera vDC
Do tej pory w tym artykule skupiła się na pojedynczej kontrolera vDC zawierająca opis podstawowych składników i architektury, które przyczyniają się do wirtualnego odporne na błędy. Funkcje platformy Azure, takich jak zestawy dostępności urządzeń WUS, moduł równoważenia obciążenia platformy Azure, zestawów skalowania, wraz z innych mechanizmów przyczynić się do systemu, które pozwalają na tworzenie stałych poziomów umowy SLA do usługi w środowisku produkcyjnym.

Jednak jednego kontrolera vDC znajduje się w jednym regionie i jest narażony na poważnej awarii, mogą mieć wpływ na ten cały region. Klientów, którzy potrzebują w celu osiągnięcia wysokiej umowy SLA trzeba chronić usług przez wdrażanie na tym samym projekcie w VDC dwie (lub więcej), umieszczone w różnych regionach.

Oprócz obaw umowy SLA istnieje kilka typowych scenariuszy, w którym wdrażania wielu VDC ma sens:

-   Obecność regionalne/Global
-   Odzyskiwanie po awarii
-   Mechanizm na kierowanie ruchu między kontrolera domeny

#### <a name="regionalglobal-presence"></a>Obecność regionalne/Global
Centra danych platformy Azure znajdują się w wielu regionach na całym świecie. Podczas wybierania wielu centrów danych platformy Azure, klienci muszą wziąć pod uwagę dwa czynniki związane z: geograficznej odległości i opóźnień. Klienci muszą oceniać geograficznej odległości między VDC i odległość między vDC i użytkowników końcowych, która oferuje najlepsze środowisko użytkownika.

Region platformy Azure, gdzie znajdują się wirtualne centra danych muszą być zgodna z wymaganiami prawnymi, ustanowione przez każdej jurysdykcji prawne w ramach której działa Twoja organizacja.

#### <a name="disaster-recovery"></a>Odzyskiwanie po awarii
Implementacja planu odzyskiwania po awarii jest silnie związane z typu obciążenia danych oraz możliwość Synchronizuj stan obciążenia między różnych VDC. W idealnym przypadku większość klientów mają być synchronizowane dane aplikacji między wdrożeniami w dwóch różnych VDC można zaimplementować mechanizm szybkiego trybu failover. Większość aplikacji są wrażliwe na czas oczekiwania i który może spowodować potencjalne limitu czasu i opóźnienia w synchronizacji danych.

Synchronizacja lub Puls monitorowania aplikacji w różnych VDC wymaga komunikacji między nimi. Dwie wirtualne centra danych w różnych regionach mogą być połączone za pomocą:

-   Komunikacja równorzędna sieci wirtualnej — komunikacja równorzędna sieci wirtualnych można połączyć koncentratory między regionami
-   Usługa ExpressRoute prywatnej komunikacji równorzędnej, gdy centra kontrolera vDC są połączone z tym samym obwodem usługi ExpressRoute
-   Wiele obwodów usługi ExpressRoute za pośrednictwem sieci firmowej sieci szkieletowej oraz usługi sieci kontrolera vDC podłączony do obwodów usługi ExpressRoute
-   Połączenia sieci VPN lokacja-lokacja między swoje centra kontrolera vDC w każdym regionie platformy Azure

Połączenia komunikacji równorzędnej sieci wirtualnej lub usługi ExpressRoute są zwykle to preferowany sposób ukończenia wyższą przepustowość i opóźnienie spójne podczas tranzyt za pośrednictwem sieci szkieletowej firmy Microsoft.

Nie ma żadnych magic przepisu, aby sprawdzić poprawność aplikacji rozproszonych między dwie (lub więcej) różne wirtualne centra danych znajdujące się w różnych regionach. Klienci powinni uruchamiać testy kwalifikacji sieci, aby sprawdzić opóźnienia i przepustowości połączenia i docelowej, czy Replikacja synchroniczna lub asynchroniczna danych jest odpowiednia i cel czasu odzyskiwania optymalne (RTO) można dla obciążeń.

#### <a name="mechanism-to-divert-traffic-between-dc"></a>Mechanizm na kierowanie ruchu między kontrolera domeny
Jedna z technik skuteczne na kierowanie ruchu przychodzącego w jednym kontrolerem domeny do innego opiera się na DNS. [Usługa Azure Traffic Manager] [ TM] używa mechanizmu systemu nazw domen (DNS, Domain Name System) do kierowania ruchu przez użytkownika końcowego do najbardziej odpowiednich publicznych punktów końcowych w określonego kontrolera vDC. Za pomocą sondy usługi Traffic Manager okresowo sprawdza, czy usługa kondycji publicznych punktów końcowych w różnych VDC, a następnie w razie awarii tych punktów końcowych, kieruje automatycznie do dodatkowej kontrolera vDC.

Traffic Manager działa na publiczne punkty końcowe platformy Azure i może służyć, na przykład na formant/kierowanie ruchu do maszyn wirtualnych platformy Azure i aplikacji sieci Web w odpowiedniej kontrolera vDC. Usługa Traffic Manager są odporne na błędy, nawet w przypadku niepowodzenia całym regionie platformy Azure i umożliwia kontrolowanie dystrybucji ruchu użytkowników do punktów końcowych usługi w różnych VDC na podstawie wielu kryteriów (na przykład awarii usługi w określonym kontrolera vDC lub wybranie kontrolera vDC zapewnia najniższe opóźnienie sieci dla klienta).

### <a name="conclusion"></a>Podsumowanie
Wirtualne centrum danych to podejście do migracji centrum danych w chmurze, która używa kombinacji funkcje i możliwości w celu tworzenie Skalowalna architektura na platformie Azure, które pozwala zmaksymalizować wykorzystanie zasobów chmury, obniżając koszty i uproszczenia zarządzania systemu. Koncepcja kontrolera vDC opiera się na topologii szprychy Centrum, zapewniając typowych usług udostępnionych w piaście, dzięki czemu konkretnych aplikacji/obciążeń w szprychy. VDC pasuje do struktury firmy role, w którym różne działy (centralnym dziale IT, DevOps, operacji i konserwacji) współpracują ze sobą, każdy z konkretnej listy ról i obowiązków. VDC spełnia wymagania dotyczące migracji "metodą Lift and Shift", ale również udostępnia wiele zalet wdrożeń w chmurze natywnych.

## <a name="references"></a>Dokumentacja
Następujące funkcje zostały omówione w tym dokumencie. Kliknij łącza, aby dowiedzieć się więcej.

| | | |
|-|-|-|
|Funkcje sieci|Równoważenie obciążenia|Łączność|
|[Sieci wirtualne platformy Azure][VNet]</br>[Sieciowe grupy zabezpieczeń][NSG]</br>[NSG Logs][NSGLog]</br>[Trasy definiowane przez użytkownika][UDR]</br>[Wirtualne urządzenia sieciowe][NVA]</br>[Publiczne adresy IP][PIP]</br>[DNS]|[Usługa Azure Load Balancer (L3) ][ALB]</br>[Usługa Application Gateway (L7) ][AppGW]</br>[Zapora aplikacji sieci Web][WAF]</br>[Usługa Azure Traffic Manager][TM] |[Komunikacja równorzędna sieci wirtualnych][VNetPeering]</br>[Wirtualna sieć prywatna][VPN]</br>[ExpressRoute][ExR]
|Tożsamość</br>|Monitorowanie</br>|Najlepsze rozwiązania</br>|
|[Azure Active Directory][AAD]</br>[Multi-Factor Authentication][MFA]</br>[Rola dostępu bazowego formantów][RBAC]</br>[Domyślne role usługi AAD][Roles] |[Usługa Azure Monitor][Monitor]</br>[Dzienniki aktywności][ActLog]</br>[Dzienniki diagnostyczne][DiagLog]</br>[Microsoft Operations Management Suite][OMS]</br>[Rozwiązanie Network Performance Monitor][NPM]|[Obwód sieci najlepszych rozwiązań][DMZ]</br>[Zarządzanie subskrypcjami][SubMgmt]</br>[Zarządzanie grupami zasobów][RGMgmt]</br>[Limity subskrypcji platformy Azure][Limits] |
|Inne usługi platformy Azure|
|[Aplikacje sieci Web platformy Azure][WebApps]</br>[HDInsights (Hadoop) ][HDI]</br>[Event Hubs][EventHubs]</br>[Service Bus][ServiceBus]|



## <a name="next-steps"></a>Następne kroki
 - Zapoznaj się z [komunikacja równorzędna sieci wirtualnych][VNetPeering], technologii i projekty gwiazdy kontrolera vDC
 - Implementowanie [AAD] [ AAD] wprowadzenie [RBAC] [ RBAC] eksploracji
 - Tworzenie subskrypcji i zasobów modelu zarządzania i RBAC modelu w celu spełnienia struktury, wymagań, zasady organizacji. Planuje najważniejszych działań. W miarę praktyczne Zaplanuj reorganizacji, łączenia, nowe linie produktów, itp.

<!--Image References-->
[0]: ./media/networking-virtual-datacenter/redundant-equipment.png "przykłady składnika nakładają się" 
[1]: ./media/networking-virtual-datacenter/vdc-high-level.png "wysokiego poziomu przykład vDC gwiazdy"
[2]: ./media/networking-virtual-datacenter/hub-spokes-cluster.png "klastra koncentratorów i szprych"
[3]: ./media/networking-virtual-datacenter/spoke-to-spoke.png "Spoke-to-spoke"
[4]: ./media/networking-virtual-datacenter/vdc-block-level-diagram.png "blokowych poziomu diagram kontrolera vDC"
[5]: ./media/networking-virtual-datacenter/users-groups-subsciptions.png "użytkownikom, grupom, subskrypcji i projektów"
[6]: ./media/networking-virtual-datacenter/infrastructure-high-level.png "diagramu wysokiego poziomu infrastruktury"
[7]: ./media/networking-virtual-datacenter/highlevel-perimeter-networks.png "diagramu wysokiego poziomu infrastruktury"
[8]: ./media/networking-virtual-datacenter/vnet-peering-perimeter-neworks.png "komunikacja równorzędna sieci wirtualnych i obwód sieci"
[9]: ./media/networking-virtual-datacenter/high-level-diagram-monitoring.png "diagram wysokiego poziomu do monitorowania"
[10]: ./media/networking-virtual-datacenter/high-level-workloads.png "diagram wysokiego poziomu dla obciążenia"

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

---
title: Scenariusze i przykłady dotyczące ładu subskrypcji | Dokumentacja firmy Microsoft
description: Przykłady sposobu implementacji ładu subskrypcji platformy Azure dla typowych scenariuszy.
services: azure-resource-manager
documentationcenter: na
author: rdendtler
manager: timlt
editor: tysonn
ms.assetid: e8fbeeb8-d7a1-48af-804f-6fe1a6024bcb
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2017
ms.author: rodend;karlku;tomfitz
ms.openlocfilehash: 2c16c0414ddf023e7055a8b57c514fc069f3112a
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="examples-of-implementing-azure-enterprise-scaffold"></a>Przykłady stosowania szkieletu Azure enterprise
W tym artykule przedstawiono przykłady sposobu przedsiębiorstwa można zaimplementować zalecenia dotyczące [szkieletu Azure enterprise](resource-manager-subscription-governance.md). Aby zilustrować najlepsze rozwiązania dotyczące typowych scenariuszy używa fikcyjnej firmy o nazwie Contoso.

## <a name="background"></a>Tło
Contoso to firma na całym świecie, która zapewnia rozwiązania łańcucha dostaw dla klientów. Udostępniają one wszystkie elementy z oprogramowania jako modelu usług do modelu spakowanych wdrożonych lokalnie.  Opracowują one oprogramowania na całym świecie z produkcyjnymi znaczących Programowanie w Indie, Stanów Zjednoczonych i Kanady.

Część niezależnego dostawcy oprogramowania firmy jest podzielona na kilka jednostek biznesowych niezależne, zarządzających produktów w znaczący biznesowych. Poszczególnych jednostek biznesowych ma własną deweloperów, menedżerów produktu i architektów.

Jednostki biznesowej usługi technologii przedsiębiorstwa (ETS) zapewnia scentralizowane możliwości IT i zarządza kilka centrach danych, w których jednostki biznesowe host swoich aplikacji. Oraz zarządzanie centrami danych, organizacja ETS zawiera i zarządza scentralizowane współpracy (np. poczty e-mail i witryn sieci Web) i usług sieciowych/telefonii. Mniejszych jednostek biznesowych, którzy nie mają personel operacyjny one również zarządzać obciążeń skierowane do klienta.

Następujące osoby są używane w tym artykule:

* Dave jest administratorem ETS Azure.
* Alicja jest programowanie z Dyrektor firmy Contoso w jednostce biznesowej łańcucha dostaw.

Contoso musi tworzenie aplikacji biznesowych systemu i aplikacji dostępnych dla klienta. Zdecydował się je do uruchomienia aplikacji na platformie Azure. Odczytuje Dave [ładu przetestowanego subskrypcji](resource-manager-subscription-governance.md) artykułu i jest gotowy do wdrożenia zalecenia.

## <a name="scenario-1-line-of-business-application"></a>Scenariusz 1: aplikacji biznesowych —
Firma Contoso jest kompilowany systemu zarządzania kodu źródłowego (BitBucket) do użycia przez deweloperów w całym świecie.  Aplikacja wykorzystuje infrastrukturę jako usługę (IaaS) do obsługi i składa się z serwerami sieci web a serwerem bazy danych. Deweloperzy uzyskać dostępu do serwerów w swoich środowiskach programistycznych, ale nie potrzebują dostępu do serwerów na platformie Azure. Contoso ETS chce zezwolić właściciel aplikacji i zespołu zarządzać aplikacją. Aplikacja jest dostępna tylko podczas firmy Contoso w sieci firmowej. Dave musi do skonfigurowania subskrypcji dla tej aplikacji. Subskrypcja będzie również obsługiwać innego oprogramowania związane z programowaniem w przyszłości.  

### <a name="naming-standards--resource-groups"></a>Standardy nazewnictwa & grup zasobów
Dave tworzy subskrypcję do obsługi narzędzia deweloperskie, które są wspólne dla wszystkich jednostek biznesowych. Dave musi utworzyć łatwy do rozpoznania nazwy dla subskrypcji i zasobu grup (dla aplikacji i sieci). Tworzy następujące grupy subskrypcji i zasobu:

| Element | Name (Nazwa) | Opis |
| --- | --- | --- |
| Subskrypcja |Contoso ETS DeveloperTools produkcji |Obsługuje typowych narzędzi deweloperskich |
| Grupa zasobów |bitbucket produkcyjną-zarządcy zasobów |Zawiera serwer aplikacji sieci web i serwera bazy danych |
| Grupa zasobów |corenetworks produkcyjną-zarządcy zasobów |Zawiera sieci wirtualnych i połączenie bramy lokacja lokacja |

### <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach
Po utworzeniu jego subskrypcji, Dave chce mieć pewność, że odpowiednie zespoły i właścicielom aplikacji dostęp do swoich zasobów. Dave rozpoznaje, że każdy zespół ma inne wymagania. ADAM korzysta z grup, które zostały zsynchronizowane z firmy Contoso lokalnej usługi Active Directory (AD) do usługi Azure Active Directory i zapewnia odpowiedni poziom dostępu do zespołów.

Dave przypisuje następujące role dla subskrypcji:

| Rola | Przypisane do | Opis |
| --- | --- | --- |
| [Właściciel](../role-based-access-control/built-in-roles.md#owner) |Zarządzane identyfikator z firmy Contoso AD |Ten identyfikator jest kontrolowany przy użyciu tylko w czasie (JIT) dostęp za pomocą narzędzia do zarządzania tożsamościami firmy Contoso i zapewnia, że pełni podlega inspekcji dostępu do właściciela subskrypcji |
| [Czytnik zabezpieczeń](../role-based-access-control/built-in-roles.md#security-reader) |Bezpieczeństwo i ryzyka działu zarządzania |Ta rola pozwala użytkownikom wyświetlać w Centrum zabezpieczeń Azure i stan zasobów |
| [Współautor sieci](../role-based-access-control/built-in-roles.md#network-contributor) |Zespół sieci |Ta rola umożliwia zespołu sieci firmy Contoso do zarządzania sieci VPN między lokacjami i sieci wirtualnych |
| *Rola niestandardowa* |Właściciel aplikacji |Dave tworzy rolę, która przyznaje uprawnienia do modyfikowania zasobów w grupie zasobów. Aby uzyskać więcej informacji, zobacz [niestandardowych ról w Azure RBAC](../role-based-access-control/custom-roles.md) |

### <a name="policies"></a>Zasady
Dave ma następujące wymagania dotyczące zarządzania zasobami subskrypcji:

* Narzędzia deweloperskie obsługują deweloperzy całym świecie, on nie chcesz uniemożliwić użytkownikom tworzenia zasobów w dowolnym regionie. Jednak musi wiedzieć, gdzie zasoby są tworzone.
* Jest on dane z kosztami. W związku z tym chce zapobiec właściciele aplikacji tworzenia niepotrzebnie kosztowne maszyn wirtualnych.  
* Ponieważ ta aplikacja obsługuje deweloperów w wiele jednostek biznesowych, chce tagu każdego zasobu z właścicielem jednostki i aplikacji biznesowych. Przy użyciu tych tagów, ETS można naliczać opłaty odpowiednie zespołów.

Tworzy następujące [zasady Azure](../azure-policy/azure-policy-introduction.md):

| Pole | Efekt | Opis |
| --- | --- | --- |
| location |Inspekcji |Przeprowadź inspekcję tworzenia zasobów w dowolnym regionie |
| type |odrzuć |Tworzenie maszyn wirtualnych serii G Odmów |
| tags |odrzuć |Wymagaj tag właściciel aplikacji |
| tags |odrzuć |Wymagaj tag Centrum kosztu |
| tags |Dołącz |Dołącz nazwy tagu **jednostką biznesową** , wartość tagu **ETS** do wszystkich zasobów |

### <a name="resource-tags"></a>Tagi zasobów
Dave rozumie, że musi mieć określone informacje na rachunek, aby zidentyfikować Centrum kosztów dla implementacji BitBucket. Ponadto Dave chce wiedzieć, wszystkie zasoby należące do ETS.

Dodaje następujące [tagi](resource-group-using-tags.md) do grupy zasobów i zasobów.

| Nazwa tagu | Wartość tagu |
| --- | --- |
| ApplicationOwner |Nazwa osoby, która zarządza tej aplikacji |
| Centrum kosztów |Centrum kosztów grupy, która jest płatności wykorzystania platformy Azure |
| Jednostką biznesową |**ETS** (skojarzone z subskrypcją jednostki biznesowej) |

### <a name="core-network"></a>Sieci podstawowej
Contoso ETS informacji zabezpieczeń i ryzyka kadra kierownicza przegląda firmy Dave proponowanego planu przenoszenia aplikacji na platformie Azure. Chcą upewnić się, że aplikacja nie jest połączenie z Internetem.  Dave ma również Deweloper aplikacji, które w przyszłości zostaną przeniesione do platformy Azure. Te aplikacje wymagają interfejsów publicznych.  Aby spełnić te wymagania, udostępnia on wewnętrznych i zewnętrznych sieci wirtualnych i sieciową grupę zabezpieczeń do ograniczenia dostępu.

Tworzy on następujące zasoby:

| Typ zasobu | Name (Nazwa) | Opis |
| --- | --- | --- |
| Virtual Network |wewnętrzna sieć wirtualna |Używane z aplikacją BitBucket i jest połączony za pośrednictwem usługi do sieci firmowej firmy Contoso.  Podsieć (`bitbucket`) udostępnia aplikacji z określonych przestrzeni adresów IP |
| Virtual Network |sieć wirtualna zewnętrzne |Dostępne dla przyszłych aplikacji, które wymagają publicznych punktów końcowych |
| Sieciowa grupa zabezpieczeń |Grupa nsg bitbucket |Zapewnia, że ataku to obciążenie jest zminimalizowany przez zezwala na połączenia tylko na porcie 443 dla podsieci, gdzie znajduje się aplikacja (`bitbucket`) |

### <a name="resource-locks"></a>Blokady zasobów
Dave rozpoznaje, że łączność z siecią firmową firmy Contoso wewnętrzna sieć wirtualna musi być zabezpieczony przed dowolny skrypt wayward lub przypadkowym usunięciem.

Tworzy następujące [Blokada zasobu](resource-group-lock-resources.md):

| Typ blokady | Zasób | Opis |
| --- | --- | --- |
| **CanNotDelete** |wewnętrzna sieć wirtualna |Uniemożliwia użytkownikom usuwanie sieci wirtualnej lub podsieci, ale nie uniemożliwia dodanie nowych podsieci |

### <a name="azure-automation"></a>Azure Automation
Dave nie ma nic do automatyzacji dla tej aplikacji. Mimo że utworzył konto usługi Automatyzacja Azure on nie będzie początkowo go używać.

### <a name="azure-security-center"></a>Azure Security Center
Zarządzanie usługami IT firmy Contoso musi szybko zidentyfikować i obsługiwać zagrożeń. Firma chce również zrozumieć, jakie problemy mogą występować.  

Aby spełnić te wymagania, umożliwia Dave [Centrum zabezpieczeń Azure](../security-center/security-center-intro.md)oraz zapewnia dostęp do roli zabezpieczeń czytnika.

## <a name="scenario-2-customer-facing-app"></a>Scenariusz 2: uwzględniającym klienta aplikacji
Kierowniczej biznesowych w jednostce biznesowej łańcucha dostaw zidentyfikował różnych możliwości do zwiększenia interakcji użytkowników z klientami firmy Contoso za pomocą karty lojalność. Zespół Alicji należy utworzyć tę aplikację i decyduje o tym, że Azure zwiększa ich możliwości spełnienia potrzeb biznesowych. Alicja współpracuje z Dave z ETS do konfigurowania subskrypcji dwóch dotyczące tworzenia i obsługi tej aplikacji.

### <a name="azure-subscriptions"></a>Subskrypcje platformy Azure
Dave logowania witrynie Enterprise Portal Azure i widzi dział łańcucha dostaw już istnieje.  Jednak ten projekt jest projektem pierwszej dla zespołu łańcucha dostaw na platformie Azure, Dave rozpoznaje potrzebę nowego konta, aby zespół deweloperów Alicji.  ADAM tworzy konto "R & D" dla swojego zespołu i przypisuje dostęp do Alicji. Alicja zaloguje się za pośrednictwem portalu Azure i utworzenie dwóch subskrypcje: jeden do przechowywania programowanie serwery i jeden do przechowywania serwerów produkcyjnych.  Uprzednio ustanowionym Standardy nazewnictwa ona zgodna podczas tworzenia następujących subskrypcji:

| Użyj subskrypcji | Name (Nazwa) |
| --- | --- |
| Opracowywanie zawartości |Programowanie ResearchDevelopment LoyaltyCard SupplyChain firmy Contoso |
| Produkcja |Contoso SupplyChain operacji LoyaltyCard produkcji |

### <a name="policies"></a>Zasady
Dave Alicja omówiono w nim aplikacji i określenie, że ta aplikacja służy tylko klienci w Ameryce Północnej regionie.  Alicja i jej zespół planowanie na potrzeby tworzenia aplikacji środowiska usługi aplikacji Azure i Azure SQL. One może być konieczne utworzenie maszyn wirtualnych podczas programowania.  Alicja chce upewnij się, że jej deweloperzy mają zasoby, które są niezbędne do eksplorowania i badanie problemów bez ściąganie w ETS.

Dla **subskrypcji programowanie**, tworzenia następujące zasady:

| Pole | Efekt | Opis |
| --- | --- | --- |
| location |Inspekcji |Przeprowadź inspekcję tworzenia zasobów w dowolnym regionie |

Nie ograniczają typ jednostki sku utworzone przez użytkownika w rozwoju i nie wymagają tagi dla wszystkich grup zasobów lub zasobów.

Dla **subskrypcji produkcji**, tworzenia następujące zasady:

| Pole | Efekt | Opis |
| --- | --- | --- |
| location |odrzuć |Odmów tworzenia zasobów poza centrami danych stany USA |
| tags |odrzuć |Wymagaj tag właściciel aplikacji |
| tags |odrzuć |Wymagaj działu tag |
| tags |Dołącz |Dołącz tag do każdej z grup zasobów, która wskazuje środowiska produkcyjnego |

Nie ograniczają typ jednostki sku utworzone przez użytkownika w środowisku produkcyjnym.

### <a name="resource-tags"></a>Tagi zasobów
Dave rozumie, że musi mieć określonych informacji do identyfikowania grup biznesowych poprawne rozliczeń i własności. Definiuje znaczniki zasobu dla grupy zasobów i zasoby.

| Nazwa tagu | Wartość tagu |
| --- | --- |
| ApplicationOwner |Nazwa osoby, która zarządza tej aplikacji |
| Dział |Centrum kosztów grupy, która jest płatności wykorzystania platformy Azure |
| EnvironmentType |**Produkcji** (nawet jeśli subskrypcja obejmuje **produkcji** w nazwie, łącznie z tym znacznikiem umożliwia łatwą identyfikację podczas przeglądania zasobów w portalu lub BOM) |

### <a name="core-networks"></a>Sieci podstawowej
Contoso ETS informacji zabezpieczeń i ryzyka kadra kierownicza przegląda firmy Dave proponowanego planu przenoszenia aplikacji na platformie Azure. Chcą upewnić się, że izolowane i chronione w sieci Obwodowej aplikacji lojalność karty.  W celu spełnienia tego wymagania, Dave i Alicja utworzyć zewnętrzną sieć wirtualną i sieciową grupę zabezpieczeń do izolowania aplikacji lojalność karty z sieci firmy Contoso.  

Dla **subskrypcji programowanie**, tworzenia:

| Typ zasobu | Name (Nazwa) | Opis |
| --- | --- | --- |
| Virtual Network |wewnętrzna sieć wirtualna |Służy środowisko projektowe karty lojalność Contoso i jest połączony za pośrednictwem usługi do sieci firmowej firmy Contoso |

Dla **subskrypcji produkcji**, tworzenia:

| Typ zasobu | Name (Nazwa) | Opis |
| --- | --- | --- |
| Virtual Network |sieć wirtualna zewnętrzne |Obsługuje aplikację lojalność karty i nie jest podłączony bezpośrednio do firmy Contoso ExpressRoute. Kod spoczywa bezpośrednio z usługami PaaS za pośrednictwem ich systemu kodu źródłowego |
| Sieciowa grupa zabezpieczeń |Grupa nsg loyaltycard |Zapewnia, że ataku to obciążenie jest zminimalizowany, zezwalając tylko komunikatu przychodzącego na porcie TCP 443.  Contoso również bada dodatkową ochronę za pomocą zapory aplikacji sieci Web |

### <a name="resource-locks"></a>Blokady zasobów
Dave Alicja przyznaje i dodaną do blokowania zasobów na niektóre z kluczowych zasobów w środowisku zapobiega przypadkowemu usunięciu podczas wypychania wadliwe kodu.

Tworzenia następującej blokady:

| Typ blokady | Zasób | Opis |
| --- | --- | --- |
| **CanNotDelete** |sieć wirtualna zewnętrzne |Aby uniemożliwić osobom usunięcie podsieci lub sieci wirtualnej. Blokady nie uniemożliwia dodanie nowych podsieci |

### <a name="azure-automation"></a>Azure Automation
Alicja i jej zespół deweloperów mają szeroką gamę elementów runbook do zarządzania środowiskiem dla tej aplikacji. Elementy runbook umożliwiają dodanie/usunięcie węzły aplikacji i innych zadań DevOps.

Aby korzystać z tych elementów runbook, umożliwiają one [automatyzacji](../automation/automation-intro.md).

### <a name="azure-security-center"></a>Azure Security Center
Zarządzanie usługami IT firmy Contoso musi szybko zidentyfikować i obsługiwać zagrożeń. Firma chce również zrozumieć, jakie problemy mogą występować.  

Aby spełnić te wymagania, Dave umożliwia Centrum zabezpieczeń Azure. Zapewnia on, Centrum zabezpieczeń Azure jest monitorowanie zasobów i zapewnia dostęp do zespołu DevOps i zabezpieczeń.

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać informacje dotyczące tworzenia szablonów usługi Resource Manager, zobacz [najlepszych rozwiązań dotyczących tworzenia szablonów usługi Azure Resource Manager](resource-manager-template-best-practices.md).

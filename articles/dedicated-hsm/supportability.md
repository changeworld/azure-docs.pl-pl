---
title: Obsługa — dedykowany moduł HSM platformy Azure | Dokumenty firmy Microsoft
description: Opcje pomocy technicznej i obszary odpowiedzialności za dedykowany moduł HSM platformy Azure w różnych scenariuszach
services: dedicated-hsm
author: johndaw
manager: rkarlin
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: seodec18
ms.date: 03/27/2019
ms.author: mbaldwin
ms.openlocfilehash: d83d688707baf6098d63dfde9b4181eb04fb9729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70881006"
---
# <a name="azure-dedicated-hsm-supportability"></a>Obsługa dedykowanego modułu HSM platformy Azure

Usługa Azure Dedicated HSM zapewnia fizyczne urządzenie do wyłącznego użytku klienta z pełną kontrolą administracyjną i odpowiedzialnością za zarządzanie. Udostępnione urządzenie jest [Gemalto SafeNet Luna 7 HSM model A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). Firma Microsoft nie będzie miała dostępu administracyjnego po zainicjowaniu administracyjnym przez klienta, poza fizycznym załącznikiem portu szeregowego jako roli monitorowania.  Bez dostępu firma Microsoft nie może mieć bieżących obowiązków związanych z konserwacją poziomu oprogramowania ani administracją systemu. W rezultacie klienci są odpowiedzialni za typowe działania operacyjne.
Klienci są w pełni odpowiedzialni za aplikacje korzystające z modułów HSM i powinni współpracować z gemalto w celu uzyskania pomocy technicznej lub konsultingowej. Ze względu na zakres własności klienta higieny operacyjnej firma Microsoft nie może zaoferować żadnej gwarancji wysokiej dostępności dla tej usługi. Obowiązkiem klienta jest upewnienie się, że ich aplikacje są poprawnie skonfigurowane w celu osiągnięcia wysokiej dostępności. Firma Microsoft będzie monitorować i utrzymywać kondycję urządzenia i łączność sieciową.

## <a name="getting-support"></a>Uzyskiwanie pomocy technicznej

Obsługa klienta dla dedykowanego modułu HSM jest wspólnym wysiłkiem firmy Microsoft i Gemalto. Wszelkie problemy ze sprzętem lub ścieżką sieciową zostaną rozwiązane przez firmę Microsoft, a wszelkie problemy związane z rzeczywistym modułem HSM, takie jak konfiguracja, oprogramowanie układowe i tworzenie aplikacji, zostaną rozwiązane przez Gemalto. Ten model pomocy technicznej zapewnia najszybszą drogę do najbardziej efektywnej obsługi. W razie wątpliwości co do określonego problemu należy zgłosić żądanie pomocy technicznej z firmą Microsoft, a my zapewnimy, że użytkownik jest odpowiednio ukierunkowany. Firma Microsoft będzie nadal angażować się we wszystkie scenariusze pomocy technicznej i dążyć do najlepszego środowiska pomocy technicznej dla naszych klientów.

## <a name="gemalto-support"></a>Pomoc techniczna firmy Gemalto

Klienci korzystający z dedykowanej usługi HSM kwalifikują się do wsparcia od Gemalto zgodnie z planem pomocy technicznej Plus. Wymaga to tylko procesu rejestracji za pomocą portalu wsparcia Gemalto. Identyfikator klienta i instrukcje zostaną dostarczone w tym celu w ramach początkowego zaangażowania firmy Microsoft w celu uzyskania dostępu do dedykowanej usługi HSM. Mechanizm, aby uzyskać wsparcie od Gemalto jest za pośrednictwem [portalu obsługi klienta.](https://supportportal.gemalto.com/csm/)
Kluczową kwestią jest to, że Gemalto dostarczy całe oprogramowanie i dokumentację wymaganą do korzystania z modułu HSM (na przykład oprogramowanie dostępu klienta i zestawów SDK) za pośrednictwem pobierania na portalu obsługi klienta.

### <a name="software-components"></a>Składniki oprogramowania

Różne składniki oprogramowania są używane w konfiguracji urządzeń HSM:

* Oprogramowanie klienckie
* SDK
* Narzędzia

### <a name="guidance"></a>Wskazówki

Gemalto udostępnia wskazówki dotyczące administracji i konfiguracji za pośrednictwem [portalu obsługi klienta.](https://supportportal.gemalto.com/csm/) Po zalogowaniu się przy użyciu prawidłowego identyfikatora klienta dokumenty te są dostępne do pobrania. Gemalto oferuje również szereg przewodników integracyjnych, aby pomóc klientom w różnych scenariuszach i integracji oprogramowania. Aby uzyskać więcej informacji, zobacz [witrynę partnera Gemalto dla firmy Microsoft](https://safenet.gemalto.com/partners/microsoft/).

### <a name="support"></a>Pomoc techniczna

Wszelkie kwestie związane z poziomem oprogramowania lub pytania dotyczące korzystania z modułów HSM w ramach usługi dedykowanego modułu HSM powinny być kierowane bezpośrednio do obsługi gemalto. Wszystkie składniki oprogramowania wymienione powyżej i wszelkie niestandardowe konfiguracji hsm, który jest po inicjowania obsługi administracyjnej, zostaną omówione przez Gemalto. Aby uzyskać więcej informacji, zobacz [portal obsługi klienta Gemalto](https://supportportal.gemalto.com/csm/).

### <a name="consulting-services"></a>Usługi konsultingowe

Aby uzyskać pomoc w projektowaniu, opracowywaniu i wdrażaniu niestandardowych aplikacji korzystających z modułu HSM, skontaktuj się z przedstawicielem konta Gemalto.

## <a name="microsoft-support"></a>Pomoc techniczna firmy Microsoft

Firma Microsoft zapewni, że fizyczne urządzenia HSM są dostępne w sieci i w stanie operacyjnym do wyłącznego użytku jednego klienta. Klienci są odpowiedzialni za konfigurację, administrowanie i zarządzanie urządzeniem. Do obowiązków firmy Microsoft należą:

* Upewnianie się, że urządzenie jest zasilane i chłodzące
* Utrzymywanie stanu operacyjnego modułu HSM (na przykład scenariusze podziału/naprawy)
* Urządzenie jest dostępne za pośrednictwem sieci.

Problemy, takie jak następujące, należy zgłaszać firmie Microsoft:

* Awarie komponentów
* Pełna awaria urządzenia
* Problemy z dostępem do sieci
* Problemy z inicjowania obsługi administracyjnej i anulowania obsługi administracyjnej.

Firma Microsoft ma fizyczny dostęp do portu szeregowego do urządzenia za pośrednictwem roli monitorowania (czyli nie roli administracyjnej), która umożliwia podstawowe dane telemetryczne kondycji.  Umożliwi to firmie Microsoft proaktywne powiadamianie o problemach klientowi, chyba że klient zdecyduje się ograniczyć to uprawnienie. 

### <a name="provisioning-and-decommissioning"></a>Tworzenie rezerw i likwidacja

Po zatwierdzeniu przez klienta rejestracji usługi dedykowanego modułu HSM będzie mógł tworzyć zasoby modułu HSM (obecnie za pośrednictwem programu PowerShell lub interfejsu wiersza polecenia, a nie portalu Azure). Zasób przechodzi przez proces alokacji, który mapuje urządzenie fizyczne w określonym regionie do wstępnie zdefiniowanej sieci wirtualnej klienta(VNet). Po widoczne na sieci wirtualnej, klient może uzyskać dostęp do urządzenia i skonfigurować go dalej zgodnie z wymaganiami. Klienci uzyskują dostęp do dedykowanych modułów HSM za pomocą oprogramowania i narzędzi klienckich Gemalto. Proces tworzenia zasobów jest obsługiwany przez firmę Microsoft. Niestandardowy proces konfiguracji i nie tylko są obsługiwane przez Gemalto. (patrz obsługa Gemalto powyżej). Po zakończeniu korzystania z modułu HSM, musi zostać zresetowany (lub zerowany), aby zapewnić brak trwałości danych. Proces resetowania urządzenia usuwa całą niestandardową konfigurację i dane. Firma Microsoft przydziela urządzenie i zwraca go do puli w stanie nieskazitelnym. Oznacza to, że gdy urządzenie jest zwracane do puli nie ma dowodów na wcześniejsze działania klienta. 

### <a name="hardware-issues"></a>Problemy ze sprzętem

Urządzenie HSM jest wyposażone w nadmiarowe i wymienne zasilacze oraz wentylatory.  Jednak usunięcie jednostki wentylatora nadal będzie powodować zdarzenie sabotażu. W przypadku wystąpienia awarii składnika firma Microsoft użyje najbardziej odpowiedniego procesu, aby rozwiązać problem na poziomie składnika w sposób, który powoduje minimalne przerwy i najniższe ryzyko dla dostępności usług naszych klientów.
Każda poważniejsza awaria urządzenia spowoduje, że urządzenie zostanie zastąpione przez świeże urządzenie z bezpłatnej puli. Klient po prostu zawiera nowe urządzenie w istniejącej parze wysokiej jakości, aby można było go zsynchronizować i powrócić do pełnego stanu operacyjnego. Nieudane urządzenie będzie miało usunięte i rozdrobnione urządzenia z danymi na miejscu w centrum danych. Tylko podwozie zostanie zwrócone do Gemalto w celu recyklingu.


### <a name="networking-issues"></a>Problemy z siecią

Jeśli klienci doświadczają problemów z dostępem do sieci do urządzenia HSM, powinni skontaktować się z pomocą techniczną firmy Microsoft. Prostym testem dostępu do sieci jest użycie SSH do łączenia się z urządzeniem HSM. Jeśli to się nie powiedzie, skontaktuj się z pomocą techniczną firmy Microsoft.

## <a name="service-level-expectations-for-support"></a>Oczekiwania dotyczące poziomu usług w zakresie wsparcia

Aby uzyskać poziomy usług pomocy technicznej firmy Microsoft, zapoznaj się z [planem pomocy technicznej platformy Azure](https://azure.microsoft.com/support/plans/).
Aby uzyskać poziom usług wsparcia Gemalto, zapoznaj się z [elementami wsparcia Gemalto.](https://azure.microsoft.com/support/plans/)

## <a name="next-steps"></a>Następne kroki

Zaleca się, aby kluczowe pojęcia, takie jak wysoka dostępność i zabezpieczenia, były dobrze rozumiane przed inicjowania obsługi administracyjnej urządzenia i projektowania aplikacji lub wdrażania.

* [Architektura wdrażania](deployment-architecture.md)
* [Wysoka dostępność](high-availability.md)
* [Zabezpieczenia fizyczne](physical-security.md)
* [Obsługa sieci](networking.md)


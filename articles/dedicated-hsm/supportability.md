---
title: Wsparcie — platformy Azure w wersji dedykowanej przez moduł HSM | Dokumentacja firmy Microsoft
description: Opcje pomocy technicznej i obszary odpowiedzialności dla platformy Azure w wersji dedykowanej w module HSM w różnych scenariuszach
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
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881006"
---
# <a name="azure-dedicated-hsm-supportability"></a>Wsparcie dla platformy Azure dedykowanej przez moduł HSM

Usługi Azure w wersji dedykowanej przez moduł HSM zapewnia pełną odpowiedzialność administracyjna kontroli i zarządzania urządzenia fizycznego do użycia wyłącznie klientów. Urządzenia udostępnione [modelu sprzętowego modułu zabezpieczeń firmy Gemalto SafeNet Luna 7 A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). Microsoft będzie miał nie dostęp administracyjny, w raz udostępnionych przez klienta, po przekroczeniu załącznika fizyczny port szeregowy rolę monitorowania.  Bez dostępu do firmy Microsoft mogą mają nie bieżące oprogramowanie poziomu konserwacji lub system administracji obowiązki. W rezultacie klienci są zobowiązani do typowych działań operacyjnych.
Klienci są w pełni odpowiedzialny za aplikacje, użyć modułów HSM, które powinny współpracować z firmy Gemalto dla pomocy technicznej i konsultacji Pomoc. Ze względu na zakres klienta własności higieny operacyjnej nie jest możliwe, firma Microsoft oferuje wszelkiego rodzaju gwarancja wysokiej dostępności dla tej usługi. Jest klienta ponosić odpowiedzialność za zapewnienie ich aplikacji są poprawnie skonfigurowane w celu osiągnięcia wysokiej dostępności. Firma Microsoft będzie monitorować i obsługa urządzenia kondycji oraz łączności sieciowej.

## <a name="getting-support"></a>Uzyskiwanie pomocy technicznej

Obsługa klienta dedykowanego modułu HSM jest wspólnym nakładem pracy między firmą Microsoft i firmy Gemalto. Wszelkie problemy z sprzętem lub ścieżką sieciową są rozwiązywane przez firmę Microsoft i wszystko, co należy zrobić z rzeczywistym modułem HSM, takim jak konfiguracja, oprogramowanie, oprogramowanie układowe i programowanie aplikacji, zostanie rozwiązane przez firmy Gemalto. Ten model wsparcia zapewnia najszybszą trasę do najbardziej efektywnej pomocy technicznej. W razie wątpliwości z konkretnym problemem Zgłoś żądanie pomocy technicznej z firmą Microsoft, aby upewnić się, że nastąpi odpowiednie skierowanie. Firma Microsoft będzie utrzymywać wszystkie scenariusze pomocy technicznej i dążyć do osiągnięcia najlepszego wsparcia dla naszych klientów.

## <a name="gemalto-support"></a>Obsługa firmy Gemalto

Klienci korzystający z dedykowanej usługi HSM kwalifikują się do pomocy technicznej z firmy Gemalto zgodnie z planem pomocy technicznej Plus. Wymaga to jedynie procesu rejestracji przy użyciu portalu pomocy technicznej firmy Gemalto. W celu uzyskania dostępu do dedykowanej usługi HSM w ramach wstępnego zaangażowania z firmą Microsoft zostanie dostarczony identyfikator klienta i instrukcje. Mechanizm, aby uzyskać pomoc techniczną od firmy Gemalto jest za pośrednictwem ich [portalu pomocy technicznej klienta](https://supportportal.gemalto.com/csm/).
Najważniejszym punktem jest to, że firmy Gemalto będzie udostępniać wszystkie programy i dokumentację wymagane do korzystania z modułu HSM (na przykład oprogramowanie dostępu klienta i zestawy SDK) za pośrednictwem pobierania w portalu obsługi klienta.

### <a name="software-components"></a>Składniki oprogramowania

Różne składniki oprogramowania są używane w konfiguracji urządzenia HSM:

* Oprogramowanie klienckie
* SDK
* Narzędzia

### <a name="guidance"></a>Wskazówki

Firmy Gemalto sprawia, że dostępne wskazówki dotyczące administracji i konfiguracji za pomocą [portalu pomocy technicznej klienta](https://supportportal.gemalto.com/csm/). Po zalogowaniu się przy użyciu Identyfikatora ważnym zwolnieniem, dokumenty te są dostępne do pobrania. Firmy Gemalto także szereg przewodnikach dotyczących integracji, aby ułatwić klientom z różnych scenariuszy i integracji oprogramowania. Aby uzyskać więcej informacji, zobacz [firmy Gemalto witryny partnera firmy Microsoft](https://safenet.gemalto.com/partners/microsoft/).

### <a name="support"></a>Pomoc techniczna

Problem z poziomu oprogramowania dowolnej zapytania względem przy użyciu modułów HSM w ramach usługi w wersji dedykowanej przez moduł HSM, powinny być kierowane do firmy Gemalto obsługują bezpośrednio. Wszystkie składniki oprogramowania wymienione powyżej i wszelkie niestandardowej konfiguracji modułu HSM, który jest używany po aprowizowaniu problem zostanie rozwiązany przez firmy Gemalto. Aby uzyskać więcej informacji, zobacz [portal obsługi klienta firmy Gemalto](https://supportportal.gemalto.com/csm/).

### <a name="consulting-services"></a>Usługi konsultingowe

Aby uzyskać pomoc w projekcie tworzenia i wdrażania aplikacji niestandardowych, które używają modułu HSM, skontaktuj się z przedstawicielem klienta firmy Gemalto.

## <a name="microsoft-support"></a>Pomoc techniczna firmy Microsoft

Firma Microsoft zapewnia, że fizyczne urządzenia HSM są dostępne w sieci i w stanie operacyjnym na wyłączne korzystanie z jednego klienta. Klienci są odpowiedzialni za konfigurację, administrację i zarządzanie urządzeniem. Obowiązki firmy Microsoft obejmują:

* Co się, że urządzenie ma zasilania i chłodzenia
* Obsługa stan operacyjny sprzętowego modułu zabezpieczeń (na przykład scenariusze w zakresie usuwania awarii)
* Urządzenie jest dostępne za pośrednictwem sieci.

Następujące kwestie powinny być raportowane do firmy Microsoft:

* Awarii składników sprzętowych
* Awaria urządzenia pełne
* Problemy dotyczące dostępu do sieci
* Problemy i cofania aprowizacji.

Firma Microsoft ma dostęp fizyczny port szeregowy do urządzenia za pomocą monitorowania rola (rola nie administracyjnych), która umożliwia kondycji podstawowe dane telemetryczne.  Dzięki temu firmy Microsoft, aby podać powiadomień z wyprzedzeniem o problemy z klientem, chyba że klient zdecyduje się na ograniczenie to uprawnienie. 

### <a name="provisioning-and-decommissioning"></a>Inicjowanie obsługi administracyjnej i wycofywanie z eksploatacji

Po klient został zarejestrowany zatwierdzonego dla usługi w wersji dedykowanej przez moduł HSM, będą mogli tworzyć zasoby sprzętowego modułu zabezpieczeń (obecnie przy użyciu programu PowerShell lub interfejsu wiersza polecenia i nie witryny Azure portal). Zasób przechodzi przez proces alokacji, który mapuje urządzenie fizyczne w określonym regionie klienta wstępnie zdefiniowane siecią wirtualną (VNet). Po widoczny w sieci wirtualnej, klient może uzyskać dostęp do urządzenia i konfigurowanie ich dalszą zgodnie z wymaganiami. Klienci uzyskiwać dostęp do ich dedykowanych sprzętowych modułów zabezpieczeń przy użyciu oprogramowania klienckiego firmy Gemalto i narzędzi. Proces tworzenia zasobu jest obsługiwana przez firmę Microsoft. Konfiguracja niestandardowa przetwarzania i nowszych są obsługiwane przez firmy Gemalto. (patrz firmy Gemalto obsługuje powyżej). Gdy klient zakończy się za pomocą modułu HSM, musi być resetowania (lub zeroized) aby upewnić się, żadna trwałość danych. Proces zresetowanie urządzenia spowoduje usunięcie wszystkich niestandardowych konfiguracji i danych. Firma Microsoft powoduje cofnięcie przydziału urządzenia i zwraca go do puli w stanu idealnego. Oznacza to, że gdy urządzenie jest zwracany do puli, jest oznak poprzedniego działania klienta. 

### <a name="hardware-issues"></a>Problemów ze sprzętem

Urządzenia HSM zawiera nadmiarowe i wymienne zasilacze i wentylator jednostki.  Jednak usunięcie jednostki wentylatora nadal spowoduje naruszenie zdarzenia. W przypadku awarii składnika firma Microsoft użyje wyborze odpowiedniego procesu w celu rozwiązania problemu poziomu składnika w sposób, który powoduje, że minimalną przerwą i najniższego ryzyka na naszym klientom dostępność usługi.
Jakiekolwiek niepowodzenie poważniejsze urządzenia spowoduje to urządzenie jest zastępowany przez nową z wolnej puli. Klient po prostu zawiera nowe urządzenie w istniejącej pary HA dla niego synchronizować i powrócić do stanu pełnej działania. Nie powiodło się urządzenie będzie mieć swoje dane, mając urządzenia usuwane i rozdrobniony w centrum danych w witrynie. Tylko obudowy zostaną zwrócone do firmy Gemalto odtwarzania.


### <a name="networking-issues"></a>Problemy z siecią

Jeśli klienci problemy sieciowe dostępu do urządzenia sprzętowego modułu zabezpieczeń, powinni skontaktować się pomocą techniczną firmy Microsoft. Prosty test, aby uzyskać dostęp do sieci jest korzystanie z protokołu SSH do nawiązania połączenia z urządzeniem przez moduł HSM. W przypadku niepowodzenia skontaktuj się z pomocą techniczną firmy Microsoft.

## <a name="service-level-expectations-for-support"></a>Usługi poziomu oczekiwania dotyczące pomocy technicznej

Poziomy usług pomocy technicznej firmy Microsoft, można znaleźć [planu pomocy technicznej Azure](https://azure.microsoft.com/support/plans/).
Poziomy usług pomocy technicznej firmy Gemalto, można znaleźć [podstawowe informacje dotyczące pomocy technicznej firmy Gemalto](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Kolejne kroki

Zalecane jest, podstawowych pojęć, takich jak wysoka dostępność i zabezpieczenia są dobrze zrozumiałe przed Inicjowanie obsługi administracyjnej urządzeń i projektu aplikacji lub wdrożenia.

* [Architektura wdrożenia](deployment-architecture.md)
* [Wysoka dostępność](high-availability.md)
* [Zabezpieczenia fizyczne](physical-security.md)
* [Sieć](networking.md)


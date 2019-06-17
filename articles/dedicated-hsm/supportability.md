---
title: Wsparcie — platformy Azure w wersji dedykowanej przez moduł HSM | Dokumentacja firmy Microsoft
description: Opcje pomocy technicznej i obszary odpowiedzialności dla platformy Azure w wersji dedykowanej w module HSM w różnych scenariuszach
services: dedicated-hsm
author: johndaw
manager: barbkess
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: seodec18
ms.date: 03/27/2019
ms.author: barclayn
ms.openlocfilehash: 8341e9057b07fbb72957eddc342ae875e4591993
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60912268"
---
# <a name="azure-dedicated-hsm-supportability"></a>Wsparcie dla platformy Azure dedykowanej przez moduł HSM

Usługi Azure w wersji dedykowanej przez moduł HSM zapewnia pełną odpowiedzialność administracyjna kontroli i zarządzania urządzenia fizycznego do użycia wyłącznie klientów. Urządzenia udostępnione [modelu sprzętowego modułu zabezpieczeń firmy Gemalto SafeNet Luna 7 A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). Microsoft będzie miał nie dostęp administracyjny, w raz udostępnionych przez klienta, po przekroczeniu załącznika fizyczny port szeregowy rolę monitorowania.  Bez dostępu do firmy Microsoft mogą mają nie bieżące oprogramowanie poziomu konserwacji lub system administracji obowiązki. W rezultacie klienci są zobowiązani do typowych działań operacyjnych.
Klienci są w pełni odpowiedzialny za aplikacje, użyć modułów HSM, które powinny współpracować z firmy Gemalto dla pomocy technicznej i konsultacji Pomoc. Ze względu na zakres klienta własności higieny operacyjnej nie jest możliwe, firma Microsoft oferuje wszelkiego rodzaju gwarancja wysokiej dostępności dla tej usługi. Jest klienta ponosić odpowiedzialność za zapewnienie ich aplikacji są poprawnie skonfigurowane w celu osiągnięcia wysokiej dostępności. Firma Microsoft będzie monitorować i obsługa urządzenia kondycji oraz łączności sieciowej.

## <a name="getting-support"></a>Uzyskiwanie pomocy technicznej

Obsługa klienta w wersji dedykowanej przez moduł HSM jest wspólnego nakładu pracy między firmami Microsoft i firmy Gemalto. Wszelkie problemy sprzętowe lub problemy z siecią ścieżki problem zostanie rozwiązany przez firmę Microsoft i nic wspólnego z rzeczywistych modułu HSM, takie jak konfiguracja, oprogramowania, programowanie aplikacji i oprogramowania układowego, problem zostanie rozwiązany przez firmy Gemalto. Ten model pomocy technicznej zapewnia najszybszy sposób najbardziej efektywne pomocy technicznej. W razie wątpliwości dla konkretnego problemu, Prześlij żądanie pomocy technicznej firmy Microsoft i firma Microsoft będzie upewnij się, że użytkownik jest kierowany odpowiednio. Firma Microsoft będzie zabawnej we wszystkich scenariuszach pomocy technicznej i Dokładamy wszelkich starań, aby uzyskać najlepsze wyniki pomocy technicznej dla naszych klientów.

## <a name="gemalto-support"></a>Obsługa firmy Gemalto

Klienci korzystający z usługi w wersji dedykowanej HSM kwalifikować się do pomocy technicznej od firmy Gemalto zgodnie z ich oraz Plan pomocy technicznej. Wymaga to po prostu proces rejestracji przy użyciu portalu firmy Gemalto pomocy technicznej. Identyfikator klienta i instrukcje, będą dostępne w tym w ramach początkowej zaangażowania firmy Microsoft do uzyskania dostępu do usługi w wersji dedykowanej sprzętowego modułu zabezpieczeń. Mechanizm, aby uzyskać pomoc techniczną od firmy Gemalto jest za pośrednictwem ich [portalu pomocy technicznej klienta](https://supportportal.gemalto.com/csm/).
Kluczowy punkt notatki to, że firmy Gemalto zapewni oprogramowania i dokumentacji, trzeba używać sprzętowego modułu zabezpieczeń (na przykład oprogramowanie dostępu klienta i zestawów SDK) przy użyciu pobierania w portalu pomocy technicznej klienta.

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

Microsoft będzie upewnij się, że fizyczne urządzenia HSM korzystają z sieci, dostępny i stan operacyjny do wyłącznego użytku pojedynczemu klientowi. Klienci są zobowiązani do konfiguracji, Administracja i zarządzanie urządzeniem. Obowiązki firmy Microsoft obejmują:

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

Urządzenia HSM zawiera nadmiarowe i wymienne zasilacze i wentylator jednostki.  Jednak usunięcie jednostki wentylator nadal powodują zdarzeń odporne. W przypadku awarii składnika firma Microsoft użyje wyborze odpowiedniego procesu w celu rozwiązania problemu poziomu składnika w sposób, który powoduje, że minimalną przerwą i najniższego ryzyka na naszym klientom dostępność usługi.
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


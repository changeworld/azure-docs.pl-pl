---
title: Monitorowanie opcji - sprzętowego modułu zabezpieczeń platformy Azure w wersji dedykowanej | Dokumentacja firmy Microsoft
description: Omówienie platformy Azure w wersji dedykowanej sprzętowego modułu zabezpieczeń monitorowanie obowiązki i opcje monitorowania
services: dedicated-hsm
author: barclayn
manager: barbkess
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: 2bcf1d1e007398cf80839f5e1d0bac78fd80db07
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60912233"
---
# <a name="azure-dedicated-hsm-monitoring"></a>Monitorowanie platformy Azure w wersji dedykowanej przez moduł HSM

Usługi Azure w wersji dedykowanej przez moduł HSM zapewnia pełną odpowiedzialność administracyjna kontroli i zarządzania urządzenia fizycznego do użycia wyłącznie klientów. Urządzenia udostępnione [modelu sprzętowego modułu zabezpieczeń firmy Gemalto SafeNet Luna 7 A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/).  Microsoft będzie miał nie dostęp administracyjny, w raz udostępnionych przez klienta, po przekroczeniu załącznika fizyczny port szeregowy rolę monitorowania. W rezultacie klienci są odpowiedzialni za typowe działania operacyjne są wykonywane w tym kompleksowe monitorowanie i dziennika analizy.
Klienci są w pełni odpowiedzialny za aplikacje, użyć modułów HSM, które powinny współpracować z firmy Gemalto pomocy technicznej lub pomocy doradcze. Ze względu na zakres klienta własności higieny operacyjnej nie jest możliwe, firma Microsoft oferuje wszelkiego rodzaju gwarancja wysokiej dostępności dla tej usługi. Jest klienta ponosić odpowiedzialność za zapewnienie ich aplikacji są poprawnie skonfigurowane w celu uzyskania wysokiej dostępności. Firma Microsoft będzie monitorować i obsługa urządzenia kondycji oraz łączności sieciowej.

## <a name="microsoft-monitoring"></a>Firma Microsoft monitoruje

Urządzenia firmy Gemalto SafeNet używana ma domyślnie SNMP i portu szeregowego jako opcje monitorowania urządzenia. Microsoft został użyty połączenia port szeregowy, zgodnie z fizycznego oznacza, że można połączyć się z urządzeniem, aby pobierać podstawowe dane telemetryczne dotyczące kondycji urządzeń. Obejmuje to elementy, takie jak temperatury i stan składnika takich jak zasilacze i wentylatory.
Aby to osiągnąć, firma Microsoft korzysta z innych niż administracyjne roli "Monitoruj" na urządzeniu firmy Gemalto. Ta rola zapewnia możliwość pobierania danych telemetrycznych, ale mu dostęp do wszystkich urządzeń pod kątem zadań administracyjnych, lub w jakikolwiek sposób wyświetlania informacji kryptograficznych. Naszym klientom mogą mieć pewność, że ich urządzenia są naprawdę własne zarządzania, administracji i na użytek poufnych magazynu kluczy kryptograficznych. W przypadku, gdy każdy klient nie jest spełniony przy użyciu tego minimalnego dostępu do podstawowe monitorowanie kondycji, mają możliwość wyłączenia konto monitorowania. Oczywiste skutkiem tego jest, że firma Microsoft zastrzega sobie żadne informacje, a więc generuje możliwość zapewnienia aktywnego powiadomienia o kondycji urządzenia. W tej sytuacji klient jest odpowiedzialny za kondycji urządzenia.
Sama funkcja monitor jest skonfigurowany do sondowanie co 10 minut można pobrać danych o kondycji urządzenia. Ze względu na charakter podatne błąd komunikacji szeregowej dopiero po kilku wskaźników kondycji ujemna w okresie jednej godziny będą alert wygenerowany. Ten alert ostatecznie będzie prowadzić do komunikacji z klientami proaktywne powiadamianie problem.
W zależności od rodzaju problemu znalazłoby się właściwego sposobu działania, aby zmniejszyć wpływ i zagwarantować korygowania niskiego ryzyka. Na przykład awaria zasilania jest procedury wymienić z nie wynikowy naruszanie zdarzeń, dzięki czemu mogą być wykonywane przy użyciu mały wpływ i minimalnego ryzyka dla operacji. Inne procedury może wymagać urządzenia zeroized i anulowanie aprowizacji, aby zminimalizować ryzyko zabezpieczeń do klienta. W tej sytuacji klient będzie zainicjować obsługę administracyjną alternatywnego urządzenia, ponowne przyłączenie wysokiej dostępności parowania, dlatego wyzwolenie synchronizacji urządzeń. W krótkim czasie przy minimalnym zakłóceniu i najniższego ryzyko związane z zabezpieczeniami spowoduje przywrócenie normalnego funkcjonowania.  

## <a name="customer-monitoring"></a>Monitorowanie klienta

Korzyści z użytkowania usługi w wersji dedykowanej przez moduł HSM jest formant, który klient pobiera urządzenia, szczególnie biorąc pod uwagę że chmurze dostarczane urządzenia. Wynikiem tej kontrolki jest odpowiedzialny za monitorowanie i zarządzanie nią kondycji urządzenia. Urządzenia firmy Gemalto SafeNet zawiera wskazówki dotyczące implementacji protokołu SNMP i Syslog. Klienci usługi w wersji dedykowanej sprzętowego modułu zabezpieczeń są zalecane do stosowania, to nawet wtedy, gdy konto Microsoft monitor pozostaje aktywna należy wziąć pod uwagę jej obowiązkowe wyłączenie konta monitora programu Microsoft.
Albo techniki, które są dostępne pozwoliłoby klienta w celu zidentyfikowania problemów i pomocy technicznej firmy Microsoft do zainicjowania pracy korygowania odpowiednie wywołania.

## <a name="next-steps"></a>Kolejne kroki

Zaleca się, że wszystkie najważniejsze pojęcia związane z usługi, takie jak wysoka dostępność i bezpieczeństwo na przykład są dobrze zrozumiałe przed dowolnego Inicjowanie obsługi administracyjnej urządzeń i projektu aplikacji lub wdrożenia. Dodatkowe tematy poziomu pojęcia:

* [Wysoka dostępność](high-availability.md)
* [Zabezpieczenia fizyczne](physical-security.md)
* [Sieć](networking.md)
* [Możliwości obsługi](supportability.md)

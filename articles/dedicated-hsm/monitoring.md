---
title: Opcje monitorowania — dedykowany moduł HSM platformy Azure | Dokumenty firmy Microsoft
description: Omówienie opcji monitorowania dedykowanego modułu HSM platformy Azure i obowiązków związanych z monitorowaniem
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: mbaldwin
ms.openlocfilehash: 3fde577a6b0efb7584e1c9efd57c95583ebe4ec9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70881422"
---
# <a name="azure-dedicated-hsm-monitoring"></a>Monitorowanie dedykowanego modułu HSM platformy Azure

Usługa Azure Dedicated HSM zapewnia fizyczne urządzenie do wyłącznego użytku klienta z pełną kontrolą administracyjną i odpowiedzialnością za zarządzanie. Udostępnione urządzenie jest [Gemalto SafeNet Luna 7 HSM model A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/).  Firma Microsoft nie będzie miała dostępu administracyjnego po zainicjowaniu administracyjnym przez klienta, poza fizycznym załącznikiem portu szeregowego jako roli monitorowania. W rezultacie klienci są odpowiedzialni za typowe działania operacyjne, w tym kompleksowe monitorowanie i analizę dzienników.
Klienci są w pełni odpowiedzialni za aplikacje korzystające z modułów HSM i powinni współpracować z Gemalto w celu uzyskania pomocy technicznej lub konsultacji. Ze względu na zakres własności klienta higieny operacyjnej firma Microsoft nie może zaoferować żadnej gwarancji wysokiej dostępności dla tej usługi. Obowiązkiem klienta jest upewnienie się, że ich aplikacje są poprawnie skonfigurowane w celu osiągnięcia wysokiej dostępności. Firma Microsoft będzie monitorować i utrzymywać kondycję urządzenia i łączność sieciową.

## <a name="microsoft-monitoring"></a>Monitorowanie firmy Microsoft

Urządzenie Gemalto SafeNet w użyciu ma domyślnie SNMP i port szeregowy jako opcje monitorowania urządzenia. Firma Microsoft użyła połączenia portu szeregowego jako fizycznego środka do łączenia się z urządzeniem w celu pobrania podstawowych danych telemetrycznych dotyczących kondycji urządzenia. Obejmuje to elementy, takie jak temperatura i stan komponentów, takie jak zasilacze i wentylatory.
Aby to osiągnąć, firma Microsoft używa niead administrative "monitor" rolę skonfigurowaną na urządzeniu Gemalto. Ta rola daje możliwość pobierania danych telemetrycznych, ale nie daje dostępu do urządzenia pod względem zadania administracyjnego lub w jakikolwiek sposób przeglądania informacji kryptograficznych. Nasi klienci mogą mieć pewność, że ich urządzenie jest naprawdę ich własne do zarządzania, administrowania i używania do przechowywania poufnych kluczy kryptograficznych. W przypadku, gdy klient nie jest zadowolony z tego minimalnego dostępu do podstawowego monitorowania kondycji, mają możliwość wyłączenia konta monitorowania. Oczywistą konsekwencją tego jest to, że Firma Microsoft nie będzie miała żadnych informacji, a zatem nie będzie miała możliwości zapewnienia proaktywnego powiadamiania o problemach ze zdrowiem urządzenia. W tej sytuacji klient jest odpowiedzialny za kondycję urządzenia.
Sama funkcja monitora jest skonfigurowana do sondowania urządzenia co 10 minut w celu uzyskania danych dotyczących kondycji. Ze względu na podatny na błędy charakter komunikacji szeregowej, tylko po wielu negatywnych wskaźników kondycji w ciągu jednej godziny zostanie podniesiony alert. Ten alert ostatecznie doprowadzi do proaktywnej komunikacji z klientem powiadamiającej o problemie.
W zależności od charakteru problemu podjętoby odpowiednie działania w celu zmniejszenia wpływu i zapewnienia działań naprawczych niskiego ryzyka. Na przykład awaria zasilania jest procedurą wymiany na gorąco bez wynikowego zdarzenia sabotażu, dzięki czemu można je wykonać przy niskim wpływie i minimalnym ryzyku pracy. Inne procedury mogą wymagać wyzeritwa i anulowania obsługi administracyjnej urządzenia w celu zminimalizowania ryzyka dla klienta. W tej sytuacji klient będzie aprowizować alternatywne urządzenie, ponownie dołączyć do parowania wysokiej dostępności, wyzwalając w ten sposób synchronizację urządzenia. Normalna praca zostałaby wznowiona w minimalnym czasie, przy minimalnych zakłóceniach i najniższym ryzyku bezpieczeństwa.  

## <a name="customer-monitoring"></a>Monitorowanie klienta

Propozycją wartości dedykowanej usługi HSM jest kontrola, którą klient otrzymuje od urządzenia, zwłaszcza biorąc pod uwagę, że jest to urządzenie dostarczane w chmurze. Konsekwencją tej kontroli jest odpowiedzialność za monitorowanie kondycji urządzenia i zarządzanie nim. Urządzenie Gemalto SafeNet jest wyposażone w wskazówki dotyczące implementacji SNMP i Syslog. Klienci dedykowanej usługi HSM powinni używać tej usługi nawet wtedy, gdy konto monitora Microsoft pozostaje aktywne i należy uznać je za obowiązkowe, jeśli wyłączą konto monitora Microsoft.
Dostępna każda z dostępnych technik umożliwia klientowi identyfikowanie problemów i wywoływanie pomocy technicznej firmy Microsoft w celu zainicjowania odpowiednich prac naprawczych.

## <a name="next-steps"></a>Następne kroki

Zaleca się, aby wszystkie kluczowe pojęcia usługi, takie jak wysoka dostępność i zabezpieczenia, na przykład były dobrze rozumiane przed każdym projektem lub wdrożeniem inicjowania obsługi administracyjnej urządzenia i projektowania aplikacji. Dalsze tematy na poziomie koncepcji:

* [Wysoka dostępność](high-availability.md)
* [Zabezpieczenia fizyczne](physical-security.md)
* [Obsługa sieci](networking.md)
* [Możliwości obsługi](supportability.md)

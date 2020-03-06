---
title: Opis Azure Security Center dotyczących zaleceń dotyczących zabezpieczeń IoT | Microsoft Docs
description: Dowiedz się więcej na temat koncepcji zaleceń dotyczących zabezpieczeń i sposobu ich użycia w Azure Security Center na potrzeby IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 02ced504-d3aa-4770-9d10-b79f80af366c
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: a8de821abcedf6bb9a331852a2c0af9b6439667a
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303583"
---
# <a name="security-recommendations"></a>Zalecenia dotyczące zabezpieczeń

Azure Security Center dla IoT skanuje zasoby platformy Azure i urządzenia IoT oraz zapewnia zalecenia dotyczące zabezpieczeń, aby zmniejszyć obszar narażony na ataki. Zalecenia dotyczące zabezpieczeń są funkcjonalne i mają na celu pomóc klientom w zgodności z najlepszymi rozwiązaniami w zakresie zabezpieczeń.

W tym artykule znajdziesz listę zaleceń, które mogą być wyzwalane na urządzeniach IoT Hub i/lub IoT.

## <a name="recommendations-for-iot-devices"></a>Zalecenia dotyczące urządzeń IoT

Zalecenia dotyczące urządzeń zapewniają szczegółowe informacje i sugestie dotyczące usprawnienia stan zabezpieczeń urządzeń. 

| Ważność | Name (Nazwa)                                                      | Źródło danych | Opis                                                                                                                                                                                           |
|----------|-----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Średnia   | Otwórz porty na urządzeniu                                      | Agent       | Znaleziono punkt końcowy nasłuchiwania na urządzeniu.                                                                                                                                                        |
| Średnia   | Odnoszące się do zasad zapory znajdują się w jednym z łańcuchów. | Agent       | Znaleziono dozwolone zasady zapory (dane wejściowe/wyjściowe). Zasady zapory powinny domyślnie blokować cały ruch i definiować reguły zezwalające na niezbędną komunikację z urządzeniem lub z niego.                               |
| Średnia   | Znaleziono ograniczającą regułę zapory w łańcuchu wejściowym     | Agent       | Znaleziono regułę w zaporze, która zawiera oddzielny wzorzec dla szerokiego zakresu adresów IP lub portów.                                                                                    |
| Średnia   | Znaleziono ograniczającą regułę zapory w łańcuchu wyjściowym    | Agent       | Znaleziono regułę w zaporze, która zawiera oddzielny wzorzec dla szerokiego zakresu adresów IP lub portów.                                                                                   |
| Średnia   | Weryfikacja linii bazowej systemu operacyjnego nie powiodła się           | Agent       | Urządzenie nie jest zgodne z wynikami [testów CIS systemu Linux](https://www.cisecurity.org/cis-benchmarks/).                                                                                                        |

### <a name="operational-recommendations-for-iot-devices"></a>Zalecenia operacyjne dotyczące urządzeń IoT

Zalecenia dotyczące działania zapewniają szczegółowe informacje i sugestie dotyczące poprawy konfiguracji agenta zabezpieczeń.

| Ważność | Name (Nazwa)                                    | Źródło danych | Opis                                                                       |
|----------|-----------------------------------------|-------------|-----------------------------------------------------------------------------------|
| Niski      | Agent wysyła niewykorzystywane wiadomości          | Agent       | 10% lub więcej komunikatów o zabezpieczeniach było mniejsze niż 4 KB w ciągu ostatnich 24 godzin.  |
| Niski      | Konfiguracja przędzy zabezpieczającej nie jest optymalna | Agent       | Konfiguracja przędzy zabezpieczeń nie jest optymalna.                                        |
| Niski      | Konflikt konfiguracji przędzy zabezpieczającej    | Agent       | Zidentyfikowano konflikty w konfiguracji dwuosiowych zabezpieczeń. |                          |
|

## <a name="recommendations-for-iot-hub"></a>Zalecenia dotyczące IoT Hub

Alerty dotyczące rekomendacji zapewniają wgląd i sugestie dotyczące działań usprawniających stan zabezpieczeń środowiska.  

| Ważność | Name (Nazwa)                                                     | Źródło danych | Opis                                                                                                                                                                                                             |
|----------|----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Wysoki     | Identyczne poświadczenia uwierzytelniania używane przez wiele urządzeń | Centrum IoT     | Poświadczenia uwierzytelniania IoT Hub są używane przez wiele urządzeń. Może to wskazywać na to, że urządzenie illegitimate personifikuje legalne urządzenie. Zduplikowane użycie poświadczeń zwiększa ryzyko personifikacji urządzenia przez złośliwy aktor. |
| Średnia   | Domyślne zasady filtrowania adresów IP powinny być Odmów                  | Centrum IoT     | Konfiguracja filtru IP powinna mieć reguły zdefiniowane dla dozwolonego ruchu i powinna domyślnie odmówić całego ruchu.                                                                                                     |
| Średnia   | Reguła filtru IP obejmuje duży zakres adresów IP                   | Centrum IoT     | Źródłowy zakres adresów IP reguły filtru adresów IP jest zbyt duży. Nadmiernie ograniczające reguły mogą uwidocznić Centrum IoT w złośliwych aktorach.                                                                                       |
| Niski      | Włączanie dzienników diagnostycznych w IoT Hub                       | Centrum IoT     | Włącz dzienniki i zachowaj je przez maksymalnie rok. Przechowywanie dzienników umożliwia ponowne utworzenie śladów aktywności w celach dochodzeniowych w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub zabezpieczenia sieci.                                       |
|

## <a name="next-steps"></a>Następne kroki

- Azure Security Center usługi IoT [— Omówienie](overview.md)
- Dowiedz się, jak [uzyskać dostęp do danych zabezpieczeń](how-to-security-data-access.md)
- Dowiedz się więcej [na temat badania urządzenia](how-to-investigate-device.md)
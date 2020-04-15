---
title: Zalecenia dotyczące zabezpieczeń
description: Dowiedz się więcej o koncepcji zaleceń dotyczących zabezpieczeń i o tym, jak są one używane w usłudze Azure Security Center dla IoT.
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
ms.openlocfilehash: 213595ac69efc90ec855b2891641e1f00bd1ba92
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311542"
---
# <a name="security-recommendations"></a>Zalecenia dotyczące zabezpieczeń

Usługa Azure Security Center for IoT skanuje zasoby platformy Azure i urządzenia IoT i udostępnia zalecenia dotyczące zabezpieczeń w celu zmniejszenia powierzchni ataku.
Zalecenia dotyczące zabezpieczeń są wykonalne i mają na celu pomoc klientom w przestrzeganiu najlepszych praktyk w zakresie zabezpieczeń.

W tym artykule znajdziesz listę zaleceń, które mogą być wyzwalane na urządzeniach IoT Hub i/lub IoT.

## <a name="recommendations-for-iot-devices"></a>Zalecenia dla urządzeń IoT

Zalecenia dotyczące urządzeń zawierają szczegółowe informacje i sugestie, aby poprawić stan bezpieczeństwa urządzeń.

| Ważność | Nazwa                                                      | Źródło danych | Opis                                                                                                                                                                                           |
|----------|-----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Medium   | Otwórz porty na urządzeniu                                      | Agent       | Na urządzeniu znaleziono punkt końcowy odsłuchu.                                                                                                                                                        |
| Medium   | Permisywna zasada zapory znaleziona w jednym z łańcuchów. | Agent       | Znaleziono dozwoloną zasadę zapory (INPUT/OUTPUT). Zasady zapory powinny domyślnie odmówić całego ruchu i zdefiniować reguły umożliwiające niezbędną komunikację do/z urządzenia.                               |
| Medium   | Znaleziono zasadę zapory permisywnej w łańcuchu wprowadzania     | Agent       | Znaleziono regułę w zaporze, która zawiera wzorzec dopuszczalny dla szerokiego zakresu adresów IP lub portów.                                                                                    |
| Medium   | Znaleziono zasadę zapory permisywnej w łańcuchu wyjściowym    | Agent       | Znaleziono regułę w zaporze, która zawiera wzorzec dopuszczalny dla szerokiego zakresu adresów IP lub portów.                                                                                   |
| Medium   | Sprawdzanie poprawności linii bazowej systemu operacyjnego nie powiodło się           | Agent       | Urządzenie nie jest zgodne z [testami porównawczymi CIS Linux.](https://www.cisecurity.org/cis-benchmarks/)                                                                                                        |

### <a name="operational-recommendations-for-iot-devices"></a>Zalecenia operacyjne dotyczące urządzeń IoT

Zalecenia operacyjne zawierają szczegółowe informacje i sugestie dotyczące poprawy konfiguracji agenta zabezpieczeń.

| Ważność | Nazwa                                    | Źródło danych | Opis                                                                       |
|----------|-----------------------------------------|-------------|-----------------------------------------------------------------------------------|
| Małe      | Agent wysyła niezauwalone wiadomości          | Agent       | 10% lub więcej komunikatów zabezpieczających było mniejszych niż 4 KB w ciągu ostatnich 24 godzin.  |
| Małe      | Konfiguracja bliźniaczej zabezpieczeń nie jest optymalna | Agent       | Konfiguracja bliźniaczej reprezentacji zabezpieczeń nie jest optymalna.                                        |
| Małe      | Konflikt konfiguracji bliźniaczej reprezentacji zabezpieczeń    | Agent       | Konflikty zostały zidentyfikowane w konfiguracji bliźniaczej reprezentacji zabezpieczeń. |                          |
|

## <a name="recommendations-for-iot-hub"></a>Zalecenia dla Usługi IoT Hub

Alerty rekomendacji zapewniają wgląd i sugestie dotyczące działań mających na celu poprawę postawy zabezpieczeń środowiska.

| Ważność | Nazwa                                                     | Źródło danych | Opis                                                                                                                                                                                                             |
|----------|----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Wysoka     | Identyczne poświadczenia uwierzytelniania używane przez wiele urządzeń | Usługa IoT Hub     | Poświadczenia uwierzytelniania usługi IoT Hub są używane przez wiele urządzeń. Może to wskazywać na bezprawne urządzenie podszywające się pod legalne urządzenie. Użycie zduplikowanych poświadczeń zwiększa ryzyko personifikacji urządzenia przez złośliwego aktora. |
| Medium   | Domyślna zasada filtru IP powinna zostać odmówiła                  | Usługa IoT Hub     | Konfiguracja filtru IP powinna mieć zdefiniowane reguły dla dozwolonego ruchu i domyślnie powinna domyślnie odmówić wszelkiego innego ruchu.                                                                                                     |
| Medium   | Reguła filtru IP obejmuje duży zakres adresów IP                   | Usługa IoT Hub     | Zakres IP źródła reguły filtru IP zezwala na protokół IP jest zbyt duży. Zbyt dopuszczalne reguły mogą uwidaczniać centrum IoT hub złośliwym podmiotom.                                                                                       |
| Małe      | Włączanie dzienników diagnostycznych w Centrum IoT                       | Usługa IoT Hub     | Włącz dzienniki i zachowaj je przez okres do roku. Zachowywanie dzienników umożliwia ponowne tworzenie śladów aktywności w celach dochodzeniowych w przypadku wystąpienia zdarzenia zabezpieczeń lub naruszenia zabezpieczeń sieci.                                       |
|

## <a name="next-steps"></a>Następne kroki

- Usługa Azure Security Center dla usługi IoT [— omówienie](overview.md)
- Dowiedz się, jak [uzyskać dostęp do danych zabezpieczeń](how-to-security-data-access.md)
- Dowiedz się więcej o [badaniu urządzenia](how-to-investigate-device.md)

---
title: Przewodnik po alertach dotyczących zabezpieczeń usługi Azure Security Center dla usługi IoT| Dokumenty firmy Microsoft
description: Dowiedz się więcej o konfigurowalnych alertach zabezpieczeń i zalecanych środkach korygowania przy użyciu usługi Azure Security Center dla funkcji i usługi IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/04/2020
ms.author: mlottner
ms.openlocfilehash: 32d79267559480de8ec91e5e66196bfd57e01cf1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299577"
---
# <a name="azure-security-center-for-iot-security-alerts"></a>Azure Security Center for IoT security alerts (Alerty zabezpieczeń usługi Azure Security Center dla IoT)

Usługa Azure Security Center for IoT stale analizuje rozwiązanie IoT przy użyciu zaawansowanej analizy i analizy zagrożeń, aby ostrzegać o złośliwej aktywności.

Zachęcamy do tworzenia niestandardowych alertów na podstawie wiedzy o oczekiwanym zachowaniu urządzenia, aby zapewnić, że alerty działają jako najbardziej efektywne wskaźniki potencjalnego naruszenia zabezpieczeń w unikatowym wdrożeniu organizacyjnym i krajobrazie. 

Poniższa lista alertów usługi Azure Security Center dla IoT są definiowane przez Użytkownika na podstawie oczekiwanego zachowania usługi IoT Hub i/lub urządzenia. Aby uzyskać więcej informacji na temat dostosowywania każdego alertu, zobacz [tworzenie alertów niestandardowych](quickstart-create-custom-alerts.md).

## <a name="iot-hub-alerts-available-for-customization"></a>Alerty Usługi IoT Hub dostępne do dostosowywania 



| Ważność | Nazwa alertu | Źródło danych | Opis | Sugerowane środki zaradcze|
|---|---|---|---|---|
| Małe      | Alert niestandardowy — liczba komunikatów z chmury do urządzenia w protokole AMQP wykracza poza dozwolony zakres          | Usługa IoT Hub     | Liczba komunikatów z chmury do urządzenia (protokół AMQP) w określonym przedziale czasu wykracza poza aktualnie skonfigurowany i dopuszczalny zakres.||
| Małe      | Alert niestandardowy — liczba odrzuconych komunikatów z chmury do urządzenia w protokole AMQP wykracza poza dozwolony zakres | Usługa IoT Hub     | Liczba komunikatów z chmury do urządzenia (protokół AMQP) odrzuconych przez urządzenie, w określonym przedziale czasu znajduje się poza aktualnie skonfigurowanym i dopuszczalnym zakresem.||
| Małe      | Alert niestandardowy — liczba komunikatów urządzenia do chmury w protokole AMQP wykracza poza dozwolony zakres      | Usługa IoT Hub     | Ilość komunikatów urządzenia do chmury (protokół AMQP) w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dopuszczalnym zakresem.|   |
| Małe      | Alert niestandardowy — liczba wywoływanych metod bezpośrednich wykracza poza dozwolony zakres | Usługa IoT Hub     | Ilość metody bezpośredniej wywoływana w określonym przedziale czasu znajduje się poza aktualnie skonfigurowanym i dopuszczalnym zakresem.||
| Małe      | Alert niestandardowy — liczba przesłanych plików wykracza poza dozwolony zakres | Usługa IoT Hub     | Ilość przekazywania plików w określonym przedziale czasu wykracza poza aktualnie skonfigurowany i dopuszczalny zakres.| |
| Małe      | Alert niestandardowy — liczba komunikatów z chmury do urządzenia w protokole HTTP wykracza poza dozwolony zakres | Usługa IoT Hub     | Ilość komunikatów w chmurze do urządzenia (protokół HTTP) w przedziale czasu nie znajduje się w skonfigurowanym dozwolonym zakresie                                  |
| Małe      | Alert niestandardowy — liczba odrzuconych komunikatów z chmury do urządzenia w protokole HTTP nie znajduje się w dozwolonym zakresie | Usługa IoT Hub     | Ilość komunikatów z chmury do urządzenia (protokół HTTP) w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dopuszczalnym zakresem. |
| Małe      | Alert niestandardowy — liczba komunikatów w chmurze na urządzeniu w protokole HTTP wykracza poza dozwolony zakres | Usługa IoT Hub| Ilość komunikatów w chmurze (protokół HTTP) w określonym przedziale czasu znajduje się poza aktualnie skonfigurowanym i dopuszczalnym zakresem.|    |
| Małe      | Alert niestandardowy - liczba komunikatów z chmury do urządzenia w protokole MQTT wykracza poza dozwolony zakres | Usługa IoT Hub     | Ilość komunikatów z chmury do urządzenia (protokół MQTT) w określonym przedziale czasu wykracza poza aktualnie skonfigurowany i dopuszczalny zakres.|   |
| Małe      | Alert niestandardowy — liczba odrzuconych komunikatów z chmury do urządzenia w protokole MQTT wykracza poza dozwolony zakres | Usługa IoT Hub     | Ilość komunikatów z chmury do urządzenia (protokół MQTT) odrzuconych przez urządzenie w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dopuszczalnym zakresem. |
| Małe      | Alert niestandardowy - liczba komunikatów urządzenia do chmury w protokole MQTT wykracza poza dozwolony zakres          | Usługa IoT Hub     | Ilość komunikatów w chmurze (protokół MQTT) w określonym przedziale czasu wykracza poza aktualnie skonfigurowany i dopuszczalny zakres.|
| Małe      | Alert niestandardowy - liczba przeczyści kolejek poleceń znajduje się poza dozwolonym zakresem                               | Usługa IoT Hub     | Ilość przeczyszczania kolejki poleceń w określonym przedziale czasu znajduje się poza aktualnie skonfigurowanym i dopuszczalnym zakresem.||
| Małe      | Alert niestandardowy - liczba aktualizacji bliźniaczych modułów wykracza poza dozwolony zakres                                       | Usługa IoT Hub     | Ilość bliźniaczych aktualizacji modułu w określonym przedziale czasu znajduje się poza aktualnie skonfigurowanym i dopuszczalnym zakresem.|
| Małe      | Alert niestandardowy - liczba nieautoryzowanych operacji wykracza poza dozwolony zakres  | Usługa IoT Hub     | Ilość nieautoryzowanych operacji w określonym przedziale czasu wykracza poza aktualnie skonfigurowany i dopuszczalny zakres.|
|

## <a name="agent-alerts-available-for-customization"></a>Alerty agenta dostępne do dostosowania 

| Ważność | Nazwa alertu | Źródło danych | Opis | Sugerowane środki zaradcze|
|---|---|---|---|---|
| Małe      | Alert niestandardowy - liczba aktywnych połączeń znajduje się poza dozwolonym zakresem  | Agent       | Liczba aktywnych połączeń w określonym przedziale czasu znajduje się poza aktualnie skonfigurowanym i dopuszczalnym zakresem.|  Zbadaj dzienniki urządzeń. Dowiedz się, skąd pochodzi połączenie i sprawdź, czy jest ono łagodne czy złośliwe. Jeśli złośliwe, usunąć możliwe złośliwe oprogramowanie i zrozumieć źródło. Jeśli jest łagodny, dodaj źródło do listy dozwolonych połączeń.  |
| Małe      | Alert niestandardowy — połączenie wychodzące utworzone z adresem IP, które nie jest dozwolone                             | Agent       | Połączenie wychodzące zostało utworzone z adresem IP, który znajduje się poza dozwoloną listą adresów IP. |Zbadaj dzienniki urządzeń. Dowiedz się, skąd pochodzi połączenie i sprawdź, czy jest ono łagodne czy złośliwe. Jeśli złośliwe, usunąć możliwe złośliwe oprogramowanie i zrozumieć źródło. Jeśli jest łagodny, dodaj źródło do listy dozwolonych adresów IP.                        |
| Małe      | Alert niestandardowy - liczba nieudanych logowań lokalnych wykracza poza dozwolony zakres                               | Agent       | Ilość nieudanych logowań lokalnych w określonym przedziale czasu znajduje się poza aktualnie skonfigurowanym i dopuszczalnym zakresem. |   |
| Małe      | Alert niestandardowy - logowanie użytkownika, który nie znajduje się na liście dozwolonych użytkowników | Agent       | Użytkownik lokalny spoza listy dozwolonych użytkowników, zalogowany do urządzenia.|  Jeśli zapisujesz nieprzetworzone dane, przejdź do konta analizy dziennika i użyj ich do zbadania urządzenia, zidentyfikowania źródła, a następnie naprawienia listy dozwolonych/zablokowanych dla tych ustawień. Jeśli obecnie nie zapisujesz nieprzetworzonych danych, przejdź do urządzenia i napraw listę dozwolonych/zablokowanych dla tych ustawień.|
| Małe      | Alert niestandardowy — proces został wykonany, który nie jest dozwolony | Agent       | Proces, który nie jest dozwolony został wykonany na urządzeniu. |Jeśli zapisujesz nieprzetworzone dane, przejdź do konta analizy dziennika i użyj ich do zbadania urządzenia, zidentyfikowania źródła, a następnie naprawienia listy dozwolonych/zablokowanych dla tych ustawień. Jeśli obecnie nie zapisujesz nieprzetworzonych danych, przejdź do urządzenia i napraw listę dozwolonych/zablokowanych dla tych ustawień.  |
|

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [dostosować alert](quickstart-create-custom-alerts.md)
- Usługa Azure Security Center dla usługi IoT [— omówienie](overview.md)
- Dowiedz się, jak [uzyskać dostęp do danych zabezpieczeń](how-to-security-data-access.md)
- Dowiedz się więcej o [badaniu urządzenia](how-to-investigate-device.md)
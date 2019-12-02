---
title: 'Szybki Start: tworzenie alertów niestandardowych dla Azure Security Center usługi IoT'
description: Informacje o tworzeniu i przypisywaniu niestandardowych alertów urządzeń dla Azure Security Center usługi zabezpieczeń IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: d1757868-da3d-4453-803a-7e3a309c8ce8
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: 992dd7b85dee0774b8388ae22fe18377ca9e5691
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2019
ms.locfileid: "74664839"
---
# <a name="quickstart-create-custom-alerts"></a>Szybki Start: tworzenie alertów niestandardowych


Korzystanie z niestandardowych grup zabezpieczeń i alertów pozwala w pełni wykorzystać kompleksowe informacje o zabezpieczeniach i kategoriie urządzenia, aby zapewnić lepsze zabezpieczenia rozwiązań IoT. 

## <a name="why-use-custom-alerts"></a>Dlaczego warto używać alertów niestandardowych? 

Wiesz, że Twoje urządzenia IoT są najlepsze.

W przypadku klientów, którzy w pełni rozumieją oczekiwane zachowanie urządzenia, Azure Security Center dla usługi IoT umożliwia przetłumaczenie tego zrozumienia na zasady zachowania urządzeń i alert dotyczący odchylenia od oczekiwanego, normalnego zachowania.

## <a name="security-groups"></a>Grupy zabezpieczeń

Grupy zabezpieczeń umożliwiają definiowanie logicznych grup urządzeń oraz zarządzanie ich stanem zabezpieczeń w scentralizowany sposób.

Te grupy mogą reprezentować urządzenia z określonym sprzętem, urządzeniami wdrożonymi w określonej lokalizacji lub dowolną inną grupą odpowiednią do konkretnych potrzeb.

Grupy zabezpieczeń są definiowane przez właściwość tag urządzenia o nazwie " **zabezpieczenia**". Domyślnie każde rozwiązanie IoT w IoT Hub ma jedną grupę zabezpieczeń o nazwie **default**. Zmień wartość właściwości Grupa **zabezpieczeń** , aby zmienić grupę zabezpieczeń urządzenia.
 
Na przykład:

```
{
  "deviceId": "VM-Contoso12",
  "etag": "AAAAAAAAAAM=",
  "deviceEtag": "ODA1BzA5QjM2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 4,
  "tags": {
    "SecurityGroup": "default"
  }, 
```

Używanie grup zabezpieczeń do grupowania urządzeń w kategorie logiczne. Po utworzeniu grup Przypisz je do wybranych niestandardowych alertów, aby uzyskać najbardziej efektywne kompleksowe rozwiązanie zabezpieczeń IoT. 

## <a name="customize-an-alert"></a>Dostosowywanie alertu

1. Otwórz IoT Hub. 
2. Kliknij pozycję **alerty niestandardowe** w sekcji **zabezpieczenia** . 
3. Wybierz grupę zabezpieczeń, do której chcesz zastosować dostosowanie. 
4. Kliknij przycisk **Dodaj alert niestandardowy**.
5. Wybierz niestandardowy alert z listy rozwijanej. 
6. Edytuj wymagane właściwości, a następnie kliknij przycisk **OK**.
7. Pamiętaj, aby kliknąć przycisk **Zapisz**. Bez zapisywania nowego alertu alert zostanie usunięty przy następnym zamknięciu IoT Hub.

 
## <a name="alerts-available-for-customization"></a>Alerty dostępne do dostosowania

Poniższa tabela zawiera podsumowanie alertów dostępnych do dostosowania.


| Ważność | Nazwa | Źródło danych | Opis | Sugerowane korygowanie|
|---|---|---|---|---|
| Niska      | Alert niestandardowy — liczba komunikatów z chmury do urządzeń w protokole AMQP jest poza dozwolonym zakresem          | IoT Hub     | Liczba komunikatów z chmury do urządzeń (protokół AMQP) w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.||
| Niska      | Alert niestandardowy — Liczba odrzuconych chmur do komunikatów urządzenia w protokole AMQP jest poza dozwolonym zakresem | IoT Hub     | Liczba komunikatów z chmury do urządzeń (protokół AMQP) odrzuconych przez urządzenie w określonym przedziale czasu poza aktualnie skonfigurowanym i dozwolonym zakresem.||
| Niska      | Alert niestandardowy — liczba urządzeń z komunikatami w chmurze w protokole AMQP jest poza dozwolonym zakresem      | IoT Hub     | Ilość urządzenia do komunikatów w chmurze (protokół AMQP) w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.|   |
| Niska      | Alert niestandardowy — liczba wywoływanych metod bezpośrednich jest poza dozwolonym zakresem | IoT Hub     | Liczba metod bezpośrednich wywoływanych w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.||
| Niska      | Alert niestandardowy — liczba operacji przekazywania plików jest poza dozwolonym zakresem | IoT Hub     | Ilość przekazywania plików w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.| |
| Niska      | Alert niestandardowy — liczba komunikatów z chmury do urządzeń w protokole HTTP jest poza dozwolonym zakresem | IoT Hub     | Ilość komunikatów z chmury do urządzeń (protokół HTTP) w przedziale czasu nie jest w skonfigurowanym dozwolonym zakresie                                  |
| Niska      | Alert niestandardowy — Liczba odrzuconych chmur do komunikatów urządzenia w protokole HTTP nie jest w dozwolonym zakresie | IoT Hub     | Ilość komunikatów z chmury do urządzeń (protokół HTTP) w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem. |
| Niska      | Alert niestandardowy — liczba urządzeń z komunikatami w chmurze w protokole HTTP jest poza dozwolonym zakresem | IoT Hub| Ilość urządzenia do komunikatów w chmurze (protokół HTTP) w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.|    |
| Niska      | Alert niestandardowy — liczba komunikatów z chmury do urządzeń w protokole MQTT jest poza dozwolonym zakresem | IoT Hub     | Ilość komunikatów z chmury do urządzeń (protokół MQTT) w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.|   |
| Niska      | Alert niestandardowy — Liczba odrzuconych chmur do komunikatów urządzenia w protokole MQTT jest poza dozwolonym zakresem | IoT Hub     | Ilość komunikatów z chmury do urządzeń (protokół MQTT) odrzuconych przez urządzenie w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem. |
| Niska      | Alert niestandardowy — liczba urządzeń z komunikatami w chmurze w protokole MQTT jest poza dozwolonym zakresem          | IoT Hub     | Ilość urządzenia do komunikatów w chmurze (protokół MQTT) w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.|
| Niska      | Alert niestandardowy — liczba przeczyszczania kolejki poleceń jest poza dozwolonym zakresem                               | IoT Hub     | Ilość przeczyszczania kolejki poleceń w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.||
| Niska      | Alert niestandardowy — liczba aktualizacji bliźniaczych modułu jest poza dozwolonym zakresem                                       | IoT Hub     | Liczba aktualizacji sieci dwuosiowych modułu w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.|
| Niska      | Alert niestandardowy — liczba niedozwolonych operacji jest poza dozwolonym zakresem  | IoT Hub     | Ilość niedozwolonych operacji w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.|
| Niska      | Alert niestandardowy — liczba aktywnych połączeń jest poza dozwolonym zakresem  | Agent       | Liczba aktywnych połączeń w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.|  Sprawdź dzienniki urządzeń. Dowiedz się, skąd pochodzi połączenie i ustal, czy jest ono niegroźne lub złośliwe. Jeśli jest to złośliwe, Usuń możliwe złośliwe oprogramowanie i poznanie źródła. Jeśli niegroźne, Dodaj źródło do listy dozwolonych połączeń.  |
| Niska      | Alert niestandardowy — połączenie wychodzące utworzone w niedozwolonym adresie IP                             | Agent       | Połączenie wychodzące zostało utworzone w adresie IP spoza listy dozwolonych adresów IP. |Sprawdź dzienniki urządzeń. Dowiedz się, skąd pochodzi połączenie i ustal, czy jest ono niegroźne lub złośliwe. Jeśli jest to złośliwe, Usuń możliwe złośliwe oprogramowanie i poznanie źródła. Jeśli niegroźne, Dodaj źródło do listy dozwolonych adresów IP.                        |
| Niska      | Alert niestandardowy — liczba nieudanych logowań lokalnych jest poza dozwolonym zakresem                               | Agent       | Liczba nieudanych logowań lokalnych w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem. |   |
| Niska      | Niestandardowy alert — Logowanie użytkownika, który nie znajduje się na liście dozwolonych użytkowników | Agent       | Użytkownik lokalny spoza listy dozwolonych użytkowników zalogował się na urządzeniu.|  Jeśli zapisujesz dane pierwotne, przejdź do swojego konta usługi log Analytics i Użyj danych do zbadania urządzenia, Zidentyfikuj źródło, a następnie napraw listę dozwolonych/zablokowanych dla tych ustawień. Jeśli obecnie nie zapisujesz danych pierwotnych, przejdź do urządzenia i popraw listę dozwolonych/zablokowanych dla tych ustawień.|
| Niska      | Alert niestandardowy — proces został wykonany, który jest niedozwolony | Agent       | Proces, który jest niedozwolony, został wykonany na urządzeniu. |Jeśli zapisujesz dane pierwotne, przejdź do swojego konta usługi log Analytics i Użyj danych do zbadania urządzenia, Zidentyfikuj źródło, a następnie napraw listę dozwolonych/zablokowanych dla tych ustawień. Jeśli obecnie nie zapisujesz danych pierwotnych, przejdź do urządzenia i popraw listę dozwolonych/zablokowanych dla tych ustawień.  |
|


## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak wdrożyć agenta zabezpieczeń...

> [!div class="nextstepaction"]
> [Wdrażanie agenta zabezpieczeń](how-to-deploy-agent.md)

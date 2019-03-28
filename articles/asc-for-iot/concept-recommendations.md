---
title: Zrozumieć ASC, aby uzyskać zalecenia dotyczące zabezpieczeń IoT (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat koncepcji zalecenia dotyczące zabezpieczeń i jak są one używane w ASC dla IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 02ced504-d3aa-4770-9d10-b79f80af366c
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2019
ms.author: mlottner
ms.openlocfilehash: 1e4582d93d1e3380ecdabdb241f27839d4da4565
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541860"
---
# <a name="security-recommendations"></a>Zalecenia dotyczące zabezpieczeń

> [!IMPORTANT]
> ASC IoT jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Na podstawie bieżące rozwiązanie analizy, ASC IoT udostępnia następujące zalecenia w razie potrzeby, aby pomóc w udoskonaleniu i chronić urządzenia, stan operacyjny i całego środowiska usługi IoT Hub. 


## <a name="device-recommendations"></a>Zalecenia dotyczące urządzeń

Zalecenia dotyczące urządzenia zapewniają szczegółowe informacje i sugestie zwiększające ustawień zabezpieczeń i zachowania. 

| Ważność | Name (Nazwa)                                                      | Źródło danych | Opis                                                                                                                                                                                           |
|----------|-----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Medium   | Otwieranie portów na urządzeniu                                      | Agent       | Znaleziono punkt końcowy nasłuchiwania na urządzeniu                                                                                                                                                          |
| Medium   | Zasady zapory ograniczające w jednej z łańcuchów. | Agent       | Dozwolone zasady zapory znaleziono (wejście/wyjście). Zasady zapory należy odmówić cały ruch domyślnie i zdefiniować reguły zezwalające na potrzeby komunikacji z urządzenia.                               |
| Medium   | Znaleziono reguły zapory ograniczające w łańcuchu danych wejściowych     | Agent       | Reguła zapory znaleziono zawierający warunki dotyczące wzorca dla szerokiego zakresu adresów IP lub porty.                                                                                    |
| Medium   | Znaleziono reguły zapory ograniczające w łańcuchu danych wyjściowych    | Agent       | Reguła zapory znaleziono zawierający warunki dotyczące wzorca dla szerokiego zakresu adresów IP lub porty.                                                                                   |
| Medium   | Weryfikacja linii bazowej systemu operacja nie powiodła się           | Agent       | Urządzenie nie jest zgodne z [wzorców w konfiguracji (ci) w systemie Linux](https://www.cisecurity.org/cis-benchmarks/)                                                                                                         |

### <a name="operational-recommendation"></a>Zalecenie operacyjne

Zalecenia operacyjnej zapewniają szczegółowe informacje i sugestie zwiększające konfiguracji agenta.

| Ważność | Name (Nazwa)                                    | Źródło danych | Opis                                                                       |
|----------|-----------------------------------------|-------------|-----------------------------------------------------------------------------------|
| Małe      | Agent wysyła komunikaty unutilized          | Agent       | 10% lub więcej komunikatów zabezpieczeń były mniejsze niż 4kb w ciągu ostatnich 24 godzin.  |
| Małe      | Nie optymalną konfigurację zabezpieczeń bliźniaczych reprezentacji | Agent       | Konfiguracja bliźniaczej reprezentacji zabezpieczeń nie jest optymalna.                                        |
| Małe      | Konflikt konfiguracji bliźniaczej reprezentacji zabezpieczeń    | Agent       | Konflikty zostały zidentyfikowane w bliźniaczej reprezentacji konfiguracji zabezpieczeń.                           |


## <a name="iot-hub-recommendations"></a>Zalecenia dotyczące usługi IoT Hub

Zalecenia, alerty zapewniają szczegółowe informacje i sugestie dotyczące działań w celu zwiększenia poziomu bezpieczeństwa środowiska.  

| Ważność | Name (Nazwa)                                                     | Źródło danych | Opis                                                                                                                                                                                                             |
|----------|----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Wysoka     | Poświadczenia uwierzytelniania identyczne używane przez wiele urządzeń | Usługa IoT Hub     | Poświadczenia uwierzytelniania usługi IoT Hub są używane przez wiele urządzeń. Może to oznaczać, że urządzenia z systemem nielegalne personifikacji autoryzowanych urządzeń. Użyj poświadczeń zduplikowane zwiększa ryzyko personifikacji urządzenia przez złośliwych aktora. |
| Medium   | Domyślne zasady filtru IP powinien Odmów                  | Usługa IoT Hub     | Konfiguracja filtru IP mieć reguły zdefiniowane dla dopuszczonego ruchu, a powinien domyślnie, zezwalają na ruch wszystkie inne domyślnie.                                                                                                     |
| Medium   | Reguły filtru adresu IP zawiera duży zakres adresów IP                   | Usługa IoT Hub     | Zakres IP dozwolonych adresów IP filtr reguły źródła jest zbyt duży. Zbyt liberalnych regułach może narazić Centrum IoT hub do uczestników złośliwych działań.                                                                                       |
| Małe      | Włącz dzienniki diagnostyczne w usłudze IoT Hub                       | Usługa IoT Hub     | Włączanie dzienników i Zachowaj ich do jednego roku. Zachowywanie dzienników można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci.                                       |
|
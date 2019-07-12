---
title: Omówienie usługi Azure Security Center dla zalecenia dotyczące zabezpieczeń IoT (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat koncepcji zalecenia dotyczące zabezpieczeń i jak są one używane w Centrum zabezpieczeń Azure dla IoT.
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
ms.date: 03/24/2019
ms.author: mlottner
ms.openlocfilehash: 1ee71bbacdba7a14e94de41563a04be9c0f00d13
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618414"
---
# <a name="security-recommendations"></a>Zalecenia dotyczące zabezpieczeń

> [!IMPORTANT]
> Centrum zabezpieczeń Azure dla IoT jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Usługa Azure Security Center (ASC) dla IoT skanuje swoje zasoby platformy Azure i urządzeń IoT i zapewnia zalecenia dotyczące zabezpieczeń, aby zmniejszyć obszar ataków. Zalecenia dotyczące zabezpieczeń są wiarygodne i mają na celu pomóc klientom w dostosowaniu się do najlepszych rozwiązań dotyczących zabezpieczeń.

W tym artykule znajdziesz listę zaleceń, które mogą być wyzwalane na usługi IoT Hub i/lub urządzeń IoT.

## <a name="recommendations-for-iot-devices"></a>Zalecenia dotyczące urządzeń IoT

Zalecenia dotyczące urządzenia zapewniają szczegółowe informacje i sugestie w celu zwiększenia poziomu bezpieczeństwa urządzenia. 

| severity | Name (Nazwa)                                                      | Źródło danych | Opis                                                                                                                                                                                           |
|----------|-----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Średni   | Otwieranie portów na urządzeniu                                      | Agent       | Znaleziono nasłuchiwania punkt końcowy na urządzeniu                                                                                                                                                          |
| Średni   | Zasady zapory ograniczające w jednej z łańcuchów. | Agent       | Dozwolone zasady zapory znaleziono (wejście/wyjście). Zasady zapory należy odmówić cały ruch domyślnie i zdefiniować reguły zezwalające na potrzeby komunikacji z urządzenia.                               |
| Średni   | Znaleziono reguły zapory ograniczające w łańcuchu danych wejściowych     | Agent       | Reguła zapory znaleziono zawierający warunki dotyczące wzorca dla szerokiego zakresu adresów IP lub porty.                                                                                    |
| Średni   | Znaleziono reguły zapory ograniczające w łańcuchu danych wyjściowych    | Agent       | Reguła zapory znaleziono zawierający warunki dotyczące wzorca dla szerokiego zakresu adresów IP lub porty.                                                                                   |
| Średni   | Weryfikacja linii bazowej systemu operacja nie powiodła się           | Agent       | Urządzenie nie jest zgodne z [wzorców w konfiguracji (ci) w systemie Linux](https://www.cisecurity.org/cis-benchmarks/)                                                                                                         |

### <a name="operational-recommendations-for-iot-devices"></a>Operational zaleceń dotyczących urządzeń IoT

Zalecenia operacyjnej zapewniają szczegółowe informacje i sugestie zwiększające konfiguracji agenta zabezpieczeń.

| severity | Name (Nazwa)                                    | Źródło danych | Opis                                                                       |
|----------|-----------------------------------------|-------------|-----------------------------------------------------------------------------------|
| Małe      | Agent wysyła komunikaty unutilized          | Agent       | 10% lub więcej komunikatów zabezpieczeń były mniejsze niż 4kb w ciągu ostatnich 24 godzin.  |
| Małe      | Nie optymalną konfigurację zabezpieczeń bliźniaczych reprezentacji | Agent       | Konfiguracja bliźniaczej reprezentacji zabezpieczeń nie jest optymalna.                                        |
| Małe      | Konflikt konfiguracji bliźniaczej reprezentacji zabezpieczeń    | Agent       | Konflikty zostały zidentyfikowane w bliźniaczej reprezentacji konfiguracji zabezpieczeń.                           |


## <a name="recommendations-for-iot-hub"></a>Zalecenia dotyczące usługi IoT Hub

Zalecenia, alerty zapewniają szczegółowe informacje i sugestie dotyczące działań w celu zwiększenia poziomu bezpieczeństwa środowiska.  

| severity | Name (Nazwa)                                                     | Źródło danych | Opis                                                                                                                                                                                                             |
|----------|----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Wysoka     | Poświadczenia uwierzytelniania identyczne używane przez wiele urządzeń | Usługa IoT Hub     | Poświadczenia uwierzytelniania usługi IoT Hub są używane przez wiele urządzeń. Może to oznaczać, że urządzenia z systemem nielegalne personifikacji autoryzowanych urządzeń. Użyj poświadczeń zduplikowane zwiększa ryzyko personifikacji urządzenia przez złośliwych aktora. |
| Średni   | Domyślne zasady filtru IP powinien Odmów                  | Usługa IoT Hub     | Konfiguracja filtru IP mieć reguły zdefiniowane dla dopuszczonego ruchu, a powinien domyślnie, zezwalają na ruch wszystkie inne domyślnie.                                                                                                     |
| Średni   | Reguły filtru adresu IP zawiera duży zakres adresów IP                   | Usługa IoT Hub     | Zakres IP dozwolonych adresów IP filtr reguły źródła jest zbyt duży. Zbyt liberalnych regułach może narazić Centrum IoT hub do uczestników złośliwych działań.                                                                                       |
| Małe      | Włącz dzienniki diagnostyki w usłudze IoT Hub                       | Usługa IoT Hub     | Włączanie dzienników i Zachowaj ich do jednego roku. Zachowywanie dzienników można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci.                                       |
|
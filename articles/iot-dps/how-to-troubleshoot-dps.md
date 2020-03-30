---
title: Diagnozowanie i rozwiązywanie problemów z rozłączeniami za pomocą usługi Azure IoT Hub DPS
description: Dowiedz się, jak diagnozować i rozwiązywać typowe błędy za pomocą łączności urządzenia dla usługi inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub (DPS)
author: xujing-ms
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: xujing
ms.openlocfilehash: 3cbab09c6b50abb590cfe9f2720713a8fa547aa7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646476"
---
# <a name="troubleshooting-with-azure-iot-hub-device-provisioning-service"></a>Rozwiązywanie problemów z usługą inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub

Problemy z łącznością dla urządzeń IoT mogą być trudne do rozwiązania, ponieważ istnieje wiele możliwych punktów awarii, takich jak awarie zaświadczania, błędy rejestracji itp. Ten artykuł zawiera wskazówki dotyczące wykrywania i rozwiązywania problemów z łącznością urządzenia za pośrednictwem [usługi Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/overview)

## <a name="using-azure-monitor-to-view-metrics-and-set-up-alerts"></a>Używanie usługi Azure Monitor do wyświetlania metryk i konfigurowania alertów

W poniższej procedurze opisano sposób wyświetlania i konfigurowania alertu w metryce usługi inicjowania obsługi administracyjnej urządzeń usługi IoT Hub. 

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

2. Przejdź do usługi inicjowania obsługi administracyjnej urządzeń usługi IoT Hub.

3. Wybierz pozycję **Metryki**.

4. Wybierz żądaną metrykę. 
   <br />Obecnie istnieją trzy metryki dla DPS:

    | Nazwa metryki | Opis |
    |-------|------------|
    | Próby zaświadczania | Liczba urządzeń, które próbowały uwierzytelnić się za pomocą usługi inicjowania obsługi urządzeń|
    | Próby rejestracji | Liczba urządzeń, które próbowały zarejestrować się w Urzędzie Obsługi Klienta po pomyślnym uwierzytelnieniu|
    | Przypisane urządzenie | Liczba urządzeń, które zostały pomyślnie przypisane do usługi IoT Hub|

5. Wybierz żądaną metodę agregacji, aby utworzyć wizualny widok metryki. 

6. Aby skonfigurować alert metryki, wybierz **nowe reguły alertów** z prawej górnej części bloku metryki, podobnie można przejść do **bloku alertów** i wybierz pozycję **Nowe reguły alertów**.

7. Wybierz **pozycję Dodaj warunek**, a następnie wybierz żądaną metrykę i próg, wykonując monity.

Aby dowiedzieć się więcej, zobacz [Co to są klasyczne alerty na platformie Microsoft Azure?](../azure-monitor/platform/alerts-overview.md)

## <a name="using-log-analytic-to-view-and-resolve-errors"></a>Używanie analitycznego loga do wyświetlania i rozwiązywania błędów

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

2. Przejdź do centrum IoT Hub.

3. Wybierz **ustawienia diagnostyki**.

4. Wybierz **włącz diagnostykę**.

5. Włącz żądane dzienniki do zebrania.

    | Nazwa dziennika | Opis |
    |-------|------------|
    | Działanie urządzenia | Dzienniki związane ze zdarzeniami połączenia urządzenia |
    | Obsługa usługi | Dzienniki zdarzeń związane z korzystaniem z sdk usługi (np. tworzenie lub aktualizowanie grup rejestracji)|

6. Włącz **funkcję Wyślij do usługi Log Analytics** [(patrz cennik).](https://azure.microsoft.com/pricing/details/log-analytics/) 

7. Przejdź do karty Dzienniki w witrynie Azure portal w obszarze **Zasób** usługi inicjowania obsługi administracyjnej urządzeń.

8. Kliknij **przycisk Uruchom,** aby wyświetlić ostatnie zdarzenia.

9. Jeśli są wyniki, `OperationName`poszukaj , `ResultType`, `ResultSignature`i `ResultDescription` (komunikat o błędzie), aby uzyskać więcej szczegółów na temat błędu.


## <a name="common-error-codes"></a>Typowe kody błędów
Ta tabela służy do zrozumienia i rozwiązania typowych błędów.

| Kod błędu| Opis | Kod stanu HTTP |
|-------|------------|------------|
| 400 | Treść żądania jest nieprawidłowa; na przykład nie można go przeanalizować lub nie można zweryfikować obiektu.| 400 Zły format |
| 401 | Nie można sprawdzić poprawności tokenu autoryzacji; na przykład wygasł lub nie ma zastosowania do identyfikatora URI żądania. Ten kod błędu jest również zwracany do urządzeń jako część przepływu zaświadczania modułu TPM. | 401 Nieautoryzowane|
| 404 | Wystąpienie usługi inicjowania obsługi administracyjnej urządzeń lub zasób (np. rejestracja) nie istnieje. |404 — Nie znaleziono |
| 412 | ETag w żądaniu nie jest zgodny z ETag istniejącego zasobu, zgodnie z RFC7232. | 412 Warunek wstępny nie powiódł się |
| 429 | Operacje są ograniczane przez usługę. Aby uzyskać określone limity usług, zobacz [Limity usługi inicjowania obsługi administracyjnej urządzeń usługi IoT Hub](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#iot-hub-device-provisioning-service-limits). | 429 Zbyt wiele żądań |
| 500 | Wystąpił błąd wewnętrzny. | 500 — wewnętrzny błąd serwera|

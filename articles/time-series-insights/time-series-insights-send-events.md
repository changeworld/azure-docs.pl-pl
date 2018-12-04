---
title: Sposób wysyłania zdarzeń do środowiska usługi Azure Time Series Insights | Dokumentacja firmy Microsoft
description: W tym samouczku wyjaśniono, jak utworzyć i skonfigurować Centrum zdarzeń i uruchomić przykładową aplikację do wypychania zdarzeń, który ma być wyświetlane w usłudze Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 64fb9f72cfd7edef18d56f15cbcce726dd33b50d
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52847273"
---
# <a name="send-events-to-a-time-series-insights-environment-using-event-hub"></a>Wysyłanie zdarzeń do środowiska usługi Time Series Insights za pomocą centrum zdarzeń

W tym artykule wyjaśniono, jak utworzyć i skonfigurować Centrum zdarzeń i uruchomić przykładową aplikację do wypychania zdarzeń. Jeśli masz już Centrum zdarzeń, które ze zdarzeniami w formacie JSON, pominąć ten samouczek i wyświetlić swoje środowisko w [Azure Time Series Insights](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Konfigurowanie Centrum zdarzeń

1. Aby utworzyć Centrum zdarzeń, postępuj zgodnie z instrukcjami z Centrum zdarzeń [dokumentacji](https://docs.microsoft.com/azure/event-hubs/).
1. Wyszukaj Centrum zdarzeń, na pasku wyszukiwania. Kliknij przycisk **usługi Event Hubs** na zwracanej liście.
1. Wybierz Centrum zdarzeń, klikając jego nazwę.
1. W obszarze **jednostek** kliknij w oknie konfiguracji środkowej **usługi Event Hubs** ponownie.
1. Wybierz nazwę Centrum zdarzeń, aby go skonfigurować.

    ![Grupa odbiorców][1]

1. W obszarze **jednostek**, wybierz opcję **grupy konsumentów**.
1. Upewnij się, że utworzono grupę konsumentów, która jest używana wyłącznie przez źródło zdarzeń usługi TSI.

> [!IMPORTANT]
> Upewnij się, że ta grupa odbiorców nie jest używany przez żadną inną usługę (np. zadania usługi Stream Analytics lub innego środowiska TSI). Jeśli grupa odbiorców jest używana przez inne usługi, operacja odczytu ma negatywny wpływ na to środowisko i innych usług. Jeśli używasz `$Default` grupy odbiorców, może to spowodować potencjalne ponownego użycia przez inne czytniki.

1. W obszarze **ustawienia** nagłówka, wybierz **zasad dostępu udziału**.
1. W tym Centrum zdarzeń Utwórz **Moje_zasady_wysyłania** używany do wysyłania zdarzeń w C# próbki.

    ![udostępnione = co dostępu][2]

    ![udostępnione dostępu — dwa][3]

## <a name="add-time-series-insights-instances"></a>Dodawanie wystąpień usługi Time Series Insights

Aktualizacja TSI używa wystąpienia, aby dodać kontekstowego dane do przychodzących danych telemetrycznych. Dane sprzężony w zapytania przy użyciu **identyfikator serii czasu**. **Identyfikator serii czasu** windmills przykładowy projekt jest `Id`. Aby dowiedzieć się więcej o wystąpieniach serii czasu i **identyfikatory serii czasu**, przeczytaj artykuł [modeli szeregów czasowych](./time-series-insights-update-tsm.md).

### <a name="create-time-series-insights-event-source"></a>Tworzenie źródła zdarzeń usługi Time Series Insights

1. Jeśli jeszcze nie utworzono źródła zdarzeń, wykonaj te instrukcje, aby utworzyć źródła zdarzeń.
1. Określ `timeSeriesId` — dotyczą [modeli szeregów czasowych](./time-series-insights-update-tsm.md) Aby dowiedzieć się więcej na temat **identyfikatory serii czasu**.

### <a name="push-events-sample-windmills"></a>Wypychanie zdarzeń (przykładowy windmills)

1. Wyszukaj Centrum zdarzeń, na pasku wyszukiwania. Kliknij przycisk **usługi Event Hubs** na zwracanej liście.
1. Wybierz Centrum zdarzeń, klikając jego nazwę.
1. Przejdź do **współużytkowane zasady dostępu** i następnie **RootManageSharedAccessKey**. Kopiuj **połączenia stingu — klucz podstawowy**

   ![connection-string][4]

1. Przejdź do pozycji https://tsiclientsample.azurewebsites.net/windFarmGen.html (Plik > Nowy > Inny). Spowoduje to uruchomienie Wiatrak symulowanych urządzeń.
1. Wklej parametry połączenia skopiowane z kroku 3 w **parametry połączenia Centrum zdarzeń**
1. Kliknij pozycję **kliknij przycisk do ekranu startowego**
1. Wróć do Centrum zdarzeń. Powinny zostać wyświetlone nowe zdarzenia są odbierane przez Centrum:

   ![telemetria][5]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wyświetlanie środowiska w Eksplorator usługi Time Series Insights](https://insights.timeseries.azure.com).

<!-- Images -->
[1]: media/send-events/consumer-group.png
[2]: media/send-events/shared-access-policy.png
[3]: media/send-events/shared-access-policy-2.png
[4]: media/send-events/sample-code-connection-string.png
[5]: media/send-events/telemetry.png
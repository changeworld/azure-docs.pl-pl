---
title: Konfigurowanie zabezpieczeń w celu dostępu i zarządzania Azure czas serii Insights | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania zabezpieczeń i uprawnienia zarządzania dostęp zasad i dostępu do danych zasad, aby zabezpieczyć Azure czas serii Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: jhubbard
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.openlocfilehash: 4306d22f03faa55fb6fc8be1a359aea3410e8038
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34653814"
---
# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Przyznawanie dostępu do danych w środowisku usługi Time Series Insights przy użyciu witryny Azure Portal

Środowiska usługi Time Series Insights udostępniają dwa niezależne rodzaje zasad dostępu:

* Zasady dostępu do zarządzania
* Zasady dostępu do danych

Oba rodzaje zasad umożliwiają przyznawanie podmiotom usługi Azure Active Directory (użytkownikom i aplikacjom) różnych uprawnień w określonym środowisku. Podmioty zabezpieczeń (Użytkownicy i aplikacje) muszą należeć do usługi active directory (nazywane dzierżawą platformy Azure) skojarzone z subskrypcją zawierający środowiska.

Zasady dostępu do zarządzania pozwalają przyznawać uprawnienia związane z konfiguracją środowiska, takie jak
*   tworzenie i usuwanie środowiska, źródła zdarzeń, zestawy danych referencyjnych i
*   zarządzanie zasadami dostępu do danych.

Zasady dostępu do danych umożliwiają przyznawanie uprawnień do wysyłania zapytań dotyczących danych, manipulowania danymi referencyjnymi w środowisku oraz udostępniania zapisanych zapytań i perspektyw skojarzonych ze środowiskiem.

Dwa rodzaje zasad pozwalają wyraźnie oddzielić dostęp do zarządzania środowiskiem od dostępu do danych znajdujących się w tym środowisku. Na przykład istnieje możliwość konfigurowania środowiska w taki sposób, że właściciela/twórcy środowisko zostanie usunięte z dostępu do danych. Ponadto użytkowników i usług, które mogą odczytywać dane ze środowiska może otrzymać Brak dostępu do konfiguracji środowiska.

## <a name="grant-data-access"></a>Przyznawanie dostępu do danych
Wykonaj następujące kroki, aby zezwolić na dostęp do nazwy głównej użytkownika:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Zlokalizuj środowiska Insights serii czasu. Typ **szeregów czasowych** w **wyszukiwania** pole. Wybierz **środowisko serii** w wynikach wyszukiwania. 

3. Wybierz środowisko usługi Time Series Insights z listy.
   
4. Wybierz **zasady dostępu do danych**, a następnie wybierz pozycję **+ Dodaj**.
  ![Zarządzanie źródło czasu serii Insights - środowiska](media/data-access/getstarted-grant-data-access1.png)

5. Wybierz **wybierz użytkownika**.  Wyszukaj użytkownika nazwy lub adresu e-mail można znaleźć użytkownika, którego chcesz dodać. Kliknij przycisk **wybierz** aby potwierdzić wybór. 

   ![Zarządzanie źródłem usługi Time Series Insights — dodawanie](media/data-access/getstarted-grant-data-access2.png)

6. Wybierz **wybierz rolę**. Wybierz rolę odpowiedniego dostępu dla użytkownika:
   - Wybierz **współautora** Jeśli chcesz zezwolić użytkownikom na zmianę danych referencyjnych i perspektyw z innymi użytkownikami środowiska i udziału zapisane kwerendy. 
   - W przeciwnym razie wybierz **czytnika** aby umożliwić pobiera dane użytkownika w środowisku i zapisać osobiste (nie udostępnione) zapytania w środowisku.

   Wybierz **Ok** aby potwierdzić wybór roli.

   ![Zarządzanie źródłem usługi Time Series Insights — wybieranie użytkownika](media/data-access/getstarted-grant-data-access3.png)

8. Wybierz **Ok** w **wybierz rolę użytkownika** strony.

   ![Zarządzanie źródłem usługi Time Series Insights — wybieranie roli](media/data-access/getstarted-grant-data-access4.png)

9. **Zasady dostępu do danych** strona zawiera listę użytkowników i rolami dla każdego użytkownika.

   ![Zarządzanie źródłem usługi Time Series Insights — wyniki](media/data-access/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się [sposób dodawania źródła zdarzenia Centrum zdarzeń do środowiska Azure czas serii Insights](time-series-insights-how-to-add-an-event-source-eventhub.md).
* [Wysyłanie zdarzeń](time-series-insights-send-events.md) w źródle zdarzeń.
* Wyświetl środowiska w [Eksplorator czasu serii Insights](https://insights.timeseries.azure.com).

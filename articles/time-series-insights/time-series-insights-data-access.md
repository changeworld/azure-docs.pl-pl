---
title: Skonfiguruj zabezpieczenia dostępu i zarządzanie Azure Time Series Insights | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania zabezpieczeń i uprawnień jako dostęp do funkcji zarządzania zasadami i dostępu do danych zasad, aby zabezpieczyć usługi Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.openlocfilehash: 97c9480d6f2b75d83252bfb6410d7b5f946757ef
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39630657"
---
# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Przyznawanie dostępu do danych w środowisku usługi Time Series Insights przy użyciu witryny Azure Portal

Środowiska usługi Time Series Insights udostępniają dwa niezależne rodzaje zasad dostępu:

* Zasady dostępu do zarządzania
* Zasady dostępu do danych

Oba rodzaje zasad umożliwiają przyznawanie podmiotom usługi Azure Active Directory (użytkownikom i aplikacjom) różnych uprawnień w określonym środowisku. Podmioty (Użytkownicy i aplikacje) muszą należeć do usługi active directory (tzn. dzierżawy platformy Azure) skojarzonego z subskrypcją zawierającą środowisko.

Zasady dostępu do zarządzania pozwalają przyznawać uprawnienia związane z konfiguracją środowiska, takie jak
*   tworzenie i usuwanie środowiska, źródła zdarzeń, zestawy danych referencyjnych i
*   zarządzanie zasadami dostępu do danych.

Zasady dostępu do danych umożliwiają przyznawanie uprawnień do wysyłania zapytań dotyczących danych, manipulowania danymi referencyjnymi w środowisku oraz udostępniania zapisanych zapytań i perspektyw skojarzonych ze środowiskiem.

Dwa rodzaje zasad pozwalają wyraźnie oddzielić dostęp do zarządzania środowiskiem od dostępu do danych znajdujących się w tym środowisku. Na przykład istnieje możliwość konfigurowania środowiska w taki sposób, że właściciel/twórca środowiska jest usuwany z dostępu do danych. Ponadto użytkowników i usług, które mogą odczytać danych ze środowiska może otrzymać dostęp do konfiguracji środowiska.

## <a name="grant-data-access"></a>Przyznawanie dostępu do danych
Wykonaj następujące kroki, aby zezwolić na dostęp do nazwy głównej użytkownika:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Znajdź środowiska usługi Time Series Insights. Typ **szeregów czasowych** w **wyszukiwania** pole. Wybierz **środowiska usługi Time Series** w wynikach wyszukiwania. 

3. Wybierz środowisko usługi Time Series Insights z listy.
   
4. Wybierz **zasady dostępu do danych**, a następnie wybierz **+ Dodaj**.
  ![Zarządzanie źródłem usługi Time Series Insights — środowisko](media/data-access/getstarted-grant-data-access1.png)

5. Wybierz **wybierz użytkownika**.  Wyszukaj adres nazwy lub adresu e-mail użytkownika zlokalizować użytkownika, którego chcesz dodać. Kliknij przycisk **wybierz** aby potwierdzić wybór. 

   ![Zarządzanie źródłem usługi Time Series Insights — dodawanie](media/data-access/getstarted-grant-data-access2.png)

6. Wybierz **wybierz rolę**. Wybierz rolę odpowiedni dostęp dla użytkownika:
   - Wybierz **Współautor** Jeśli chcesz zezwolić użytkownikowi na zmianę danych referencyjnych i udziału zapisany, zapytań i perspektyw innym użytkownikom środowiska. 
   - W przeciwnym razie wybierz **czytnika** do zezwalania na zapytania o dane użytkownika w środowisku i zapisywanie osobistych zapytań (nie udostępnianych) w środowisku.

   Wybierz **Ok** aby potwierdzić wybór roli.

   ![Zarządzanie źródłem usługi Time Series Insights — wybieranie użytkownika](media/data-access/getstarted-grant-data-access3.png)

8. Wybierz **Ok** w **wybierz rolę użytkownika** strony.

   ![Zarządzanie źródłem usługi Time Series Insights — wybieranie roli](media/data-access/getstarted-grant-data-access4.png)

9. **Zasady dostępu do danych** strona zawiera listę użytkowników i rolami dla każdego użytkownika.

   ![Zarządzanie źródłem usługi Time Series Insights — wyniki](media/data-access/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, [sposób dodawania źródła zdarzeń Centrum zdarzeń do środowiska usługi Azure Time Series Insights](time-series-insights-how-to-add-an-event-source-eventhub.md).
* [Wysyłanie zdarzeń](time-series-insights-send-events.md) do źródła zdarzenia.
* Wyświetlanie środowiska w [Eksploratora usługi Time Series Insights](https://insights.timeseries.azure.com).

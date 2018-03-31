---
title: Usługi Azure Mobile Engagement przewodnik - Analytics rozwiązywania problemów
description: Rozwiązywanie problemów analizy, monitorowanie segmentacji i pulpit nawigacyjny w usłudze Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 04a7020a-ad74-4491-be69-0bd574890029
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: b19d018b83ee8b3d5848d29afff190d3dcaf3fde
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="troubleshooting-guide-for-analytics-monitoring-segmentation-and-dashboard-issues"></a>Przewodnik rozwiązywania problemów analizy, monitorowanie segmentacji i pulpitu nawigacyjnego
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

Poniżej przedstawiono możliwe problemy mogą się pojawić w jaki sposób usługi Azure Mobile Engagement zbiera informacje o aplikacji, urządzeń i użytkowników.

## <a name="missingdelayed-information"></a>Brak opóźnione informacji
### <a name="issue"></a>Problem
* Informacje o jest opóźnione w znajdujących się w Analytics, segmentacji lub pulpitu nawigacyjnego.
* Monitorowanie brakuje informacji.
* Brakuje informacji Analytics, segmentacji lub pulpitu nawigacyjnego.
* Naciśnięcie limity segmentacji.

### <a name="causes"></a>Powoduje, że
* Można użyć interfejsu API Analytics API monitora i segmentów API, aby sprawdzić, czy wszystkie dane można w Interfejsie brak jest widoczny za pośrednictwem interfejsów API.
* Jeśli zestaw SDK usługi Azure Mobile Engagement nie został poprawnie zintegrowany ze aplikacji nie będzie można zobaczyć informacje w Analytics, segmentacji, monitorowanie i pulpitów nawigacyjnych.
* Segmenty nie można zmienić po ich utworzeniu, segmentów może być tylko "sklonowane" (skopiowany) lub "zniszczone" (usunięty). Segmenty może zawierać tylko 10 kryteriów.
* Najlepszym sposobem sprawdzenia brakujące informacje z monitorowania jest skonfigurować urządzenie testu, odinstaluj i/lub zainstaluj ponownie aplikację na urządzeniu testu.
* Informacje są odświeżane co 24 godziny dla analityka, segmentacji lub pulpitów nawigacyjnych.
* Informacje zawarte w nowych segmentów mogą być niewidoczne do 24 godzin po ich utworzeniu, nawet jeśli segment jest oparta na poprzednich informacji.
* Filtrowanie danych analityka w interfejsie użytkownika zostaną wyświetlone wszystkie przykłady tego typu, niezależnie od wersji aplikacji (np. "Awarii" przefiltrowane według nazwy zostaną wyświetlone w wersji 1 i aplikacji w wersji 2).
* Okres czasu w celu wykonania analizy opiera się na datę z ustawień urządzeń użytkowników, dlatego użytkownik, którego telefon ma niepoprawnie ustawione Data mogą pojawiać się w przedziale czasu niewłaściwy.
* Wypchnięcia nie po stronie serwera, które dane są rejestrowane, korzystając z przycisku do "test", tylko rejestrowane są dane dla kampanie wypychania prawdziwe.

## <a name="cant-locate-items-in-ui"></a>Nie można zlokalizować elementów w interfejsie użytkownika
### <a name="issue"></a>Problem
* Nie można utworzyć segmenty oparte na niektóre wbudowane lub informacje o aplikacji niestandardowych tagów kryteriów.
* Nie można znaleźć niektórych wbudowanych lub informacje o aplikacji niestandardowych tagów kryteria analizy, monitorowanie lub pulpitów nawigacyjnych.
* Nie można zinterpretować dane analizy, monitorowanie, segmentacji lub pulpitów nawigacyjnych.

### <a name="causes"></a>Powoduje, że
* Niektóre wbudowane elementy i tagi informacje o aplikacji są dostępne tylko jako kryteriów wypychania, ale nie może być dodany do segmentu lub widoczne z analizy, monitorowanie lub pulpitu nawigacyjnego. 
* Wbudowane elementów i tagi informacje o aplikacji, których nie można dodać do segmentu należy do listy konfiguracji docelowych kryteriów w każdej kampanii wykonać taką samą funkcję jak oparte na segment.
* Wyświetlić menu kontekstowe w sekcjach analizy, monitorowanie segmentacji i pulpitów nawigacyjnych usługi Azure Mobile Engagement interfejs użytkownika więcej pomocy i informacje.

## <a name="crash-troubleshooting"></a>Awarii, rozwiązywanie problemów
### <a name="issue"></a>Problem
* Wystąpiła awaria aplikacji znajdujących się w analizy, monitorowanie lub pulpitu nawigacyjnego.

### <a name="causes"></a>Powoduje, że
* Aby rozwiązać aplikacja przestaje działać w analizy, monitorowanie lub pulpit nawigacyjny upewnij się, że Sprawdź informacje o wersji pod kątem znanych problemów w poprzednich wersjach zestawu SDK.
* Aby kontynuować rozwiązywanie awarii aplikacji wykonaj zdarzenia z urządzenia testu z zainstalowania aplikacji i wyszukiwanie w sekcji "Monitor zdarzeń —" interfejsu użytkownika usługi Azure Mobile Engagement identyfikator urządzenia. Następnie wykonaj zdarzenia, które powoduje aplikacji do awarii i poszukaj dodatkowych informacji w sekcji "Awarii — Monitor" interfejsu użytkownika usługi Azure Mobile Engagement. 


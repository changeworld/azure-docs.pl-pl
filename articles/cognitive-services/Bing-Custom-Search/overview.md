---
title: Co to jest interfejs API wyszukiwania niestandardowego Bing?
titlesuffix: Azure Cognitive Services
description: Interfejs API wyszukiwania niestandardowego Bing umożliwia tworzenie dostosowanych funkcji wyszukiwania dla interesujących Cię tematów.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: overview
ms.date: 03/4/2019
ms.author: aahi
ms.openlocfilehash: e788da047cb0567fc00f27130621a2f21e575dc4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61335543"
---
# <a name="what-is-the-bing-custom-search-api"></a>Co to jest interfejs API wyszukiwania niestandardowego Bing?

Interfejs API wyszukiwania niestandardowego Bing umożliwia tworzenie dostosowanych funkcji wyszukiwania dla interesujących Cię tematów bez wyświetlania reklam. Możesz określać domeny i strony internetowe do wyszukiwania w usłudze Bing, a także przypinać, podnosić lub obniżać określoną zawartość, aby tworzyć niestandardowe widoki internetowe i ułatwiać użytkownikom szybkie znajdowanie odpowiednich wyników wyszukiwania. 

## <a name="features"></a>Funkcje

|Cecha  |Opis  |
|---------|---------|
|[Sugestie dotyczące wyszukiwania niestandardowego w czasie rzeczywistym](define-custom-suggestions.md)     | Podaj sugestie dotyczące wyszukiwania, które mogą być wyświetlane jako lista rozwijana podczas wpisywania przez użytkowników.       | 
|[Środowiska niestandardowego wyszukiwania obrazów](get-images-from-instance.md)     | Pozwól użytkownikom wyszukiwać obrazy z domen i witryn internetowych określonych w Twoim wystąpieniu wyszukiwania niestandardowego.        |        
|[Środowiska niestandardowego wyszukiwania wideo](get-videos-from-instance.md)     | Pozwól użytkownikom wyszukiwać filmy wideo z domen i witryn określonych w Twoim wystąpieniu wyszukiwania niestandardowego.        |    
|[Udostępnianie wystąpienia wyszukiwania niestandardowego](share-your-custom-search.md)     | Edytuj i testuj wystąpienie wyszukiwania zespołowo, udostępniając je członkom Twojego zespołu.        | 
|[Konfigurowanie interfejsu użytkownika dla aplikacji i witryn internetowych](hosted-ui.md)     | Edytuj i testuj wystąpienie wyszukiwania zespołowo, udostępniając je członkom Twojego zespołu.        | 
## <a name="workflow"></a>Przepływ pracy

Używając [portalu wyszukiwania niestandardowego Bing](https://customsearch.ai), możesz utworzyć wystąpienie wyszukiwania dostosowanego. Portal umożliwia utworzenie wystąpienia wyszukiwania niestandardowego, określającego domeny, witryny i strony internetowe, które będą lub nie będą przeszukiwane przez usługę Bing. Portal umożliwia również: wyświetlanie podglądu środowiska wyszukiwania, dostosowywanie rankingów wyszukiwania udostępnianych przez interfejs API oraz opcjonalnie konfigurowanie interfejsu użytkownika z możliwością wyszukiwania, aby można było renderować go w Twoich witrynach internetowych i aplikacjach.

Po utworzeniu wystąpienia wyszukiwania możesz zintegrować je (opcjonalnie także interfejs użytkownika) z witryną internetową lub aplikacją, wywołując interfejs API wyszukiwania niestandardowego Bing:

![Obraz pokazujący, że można nawiązać połączenie z funkcją wyszukiwania niestandardowego Bing za pośrednictwem interfejsu API](media/BCS-Overview.png "Sposób działania wyszukiwania niestandardowego Bing.")


## <a name="next-steps"></a>Kolejne kroki

Aby szybko rozpocząć pracę, zobacz [Tworzenie pierwszego wystąpienia usługi wyszukiwania niestandardowego Bing](quick-start.md).

Aby uzyskać szczegółowe informacje na temat dostosowywania wystąpienia wyszukiwania, zobacz [Define a custom search instance (Definiowanie wystąpienia wyszukiwania niestandardowego)](define-your-custom-view.md).

Pamiętaj o przeczytaniu [wymagań dotyczących używania i wyświetlania usługi Bing](./use-and-display-requirements.md) w celu korzystania z wyników wyszukiwania w Twoich usługach i aplikacjach.

Zapoznaj się z dokumentacją poszczególnych punktów końcowych wyszukiwania niestandardowego. Dokumentacja zawiera listę punktów końcowych, nagłówków i parametrów zapytań, które są stosowane w żądaniach wyników wyszukiwania. Zawiera także definicje obiektów odpowiedzi.

- [Interfejs API wyszukiwania niestandardowego](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference)
- [Interfejs API wyszukiwania niestandardowego obrazów](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-images-api-v7-reference)
- [Niestandardowy interfejs API wideo](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-videos-api-v7-reference)
- [Interfejs API niestandardowego automatycznego sugerowania](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-autosuggest-api-v7-reference)


---
title: Czym jest wyszukiwanie niestandardowe Bing? | Microsoft Docs
description: Zawiera ogólne omówienie wyszukiwania niestandardowego Bing
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/29/2017
ms.author: v-brapel
ms.openlocfilehash: b6f50844d6571cca6d63c1db7a85863e3d22d411
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948081"
---
# <a name="what-is-bing-custom-search"></a>Czym jest wyszukiwanie niestandardowe Bing?

Usługa Bing Custom Search umożliwia utworzenie środowiska wyszukiwania dostosowane dla tematów, które Cię interesują. Na przykład jeśli jesteś właścicielem witryny sieci Web, która zawiera funkcję wyszukiwania, możesz określić, domen, witryn sieci Web i stron sieci Web, która wyszukuje Bing. Wyniki wyszukiwania, dostosowane do zawartości są interesujące zamiast widoczne dla użytkowników na stronę za pośrednictwem wyników wyszukiwania, które mają zawartość nie ma znaczenia.

Aby utworzyć niestandardowy widok sieci web, użyj wyszukiwania niestandardowego Bing [portal](https://customsearch.ai). Portal umożliwia tworzenie wystąpienia wyszukiwania niestandardowego, który określa domeny, witryn sieci Web i stron sieci Web, które Bing, wyszukiwanie i witryn sieci Web, których nie chcesz, aby wyszukać. Oprócz określenia adresów URL informacji o zawartości, umożliwia także portalu można znaleźć odpowiedniej zawartości, który chcesz dodać.

Portal umożliwia także przypiąć konkretnej strony sieci Web na początku wyników wyszukiwania, jeśli użytkownik wprowadzi określonych wyszukiwany termin. 

Po zdefiniowaniu wystąpienie usługi, można zintegrować wyszukiwanie niestandardowe witryny sieci Web, aplikacji pulpitu lub aplikacji mobilnej, wywołując interfejs API wyszukiwania niestandardowego. Jeśli masz witrynę sieci web lub aplikacji, można pozwolić hostowanej interfejsu użytkownika renderowania interfejsu wyszukiwania dla Ciebie.

Na poniższej ilustracji przedstawiono prostotę integracji wyszukiwania niestandardowego.

![Obraz alt](./media/bcs-overview.png "działa jak wyszukiwanie niestandardowe Bing.")

## <a name="adding-custom-search-box-suggestions"></a>Dodawanie sugestii pole wyszukiwania niestandardowego

Możesz wzbogacić środowiska wyszukiwania niestandardowego z sugestiami pole wyszukiwania niestandardowego. Ta funkcja pozwala zapewnić odpowiednie środowiska wyszukiwania sugestie wyszukiwania niestandardowego. Jako użytkownik wpisze w polu wyszukiwania lista rozwijana zawiera ciągi sugerowane zapytania, na podstawie ciągu zapytania częściowego użytkownika. Można określić, czy zwracać tylko niestandardowych sugestii lub również zawierają sugestie Bing. [Dowiedz się więcej](define-custom-suggestions.md).

## <a name="adding-custom-image-search-experience"></a>Dodawanie funkcji wyszukiwania niestandardowego obrazu

Możesz wzbogacić twoje środowisko wyszukiwania niestandardowego przy użyciu obrazów. Podobnie jak wyniki z Internetu, obsługuje wyszukiwanie niestandardowe wyszukiwanie obrazów na liście wystąpienia usługi witryny sieci Web. [Dowiedz się więcej](get-images-from-instance.md).

## <a name="adding-custom-video-search-experience"></a>Dodawanie niestandardowego wyszukiwania wideo

Możesz wzbogacić środowisko niestandardowe wyszukiwanie filmów wideo. Podobnie jak wyniki z Internetu, obsługuje wyszukiwanie niestandardowe wyszukiwanie filmów wideo na liście wystąpienia usługi witryny sieci Web. [Dowiedz się więcej](get-videos-from-instance.md).

## <a name="sharing-your-custom-search-instance-with-others"></a>Udostępnianie swojego wystąpienia wyszukiwania niestandardowego

Łatwo można zezwolić na współpracy, edycji i testowania wystąpienia usługi przez udostępnianie członkom zespołu. [Dowiedz się więcej](share-your-custom-search.md).

## <a name="next-steps"></a>Kolejne kroki

Aby szybko rozpocząć pracę, zobacz [Tworzenie pierwszego wystąpienia wyszukiwania niestandardowego Bing](quick-start.md).

Aby uzyskać szczegółowe informacje dotyczące dostosowywania wystąpienia wyszukiwania, zobacz [Definiowanie wystąpienia wyszukiwania niestandardowego](define-your-custom-view.md).

Zapoznaj się z zawartość odniesienia dla każdego z punktów końcowych wyszukiwania niestandardowego. Odwołania zawiera punkty końcowe, nagłówki i parametry zapytania, które są używane do żądania w wynikach wyszukiwania. Zawiera także definicje obiektów odpowiedzi.

- [Interfejs API wyszukiwania niestandardowego](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference)
- [Obraz niestandardowy interfejs API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-images-api-v7-reference)
- [Vidoe niestandardowego interfejsu API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-videos-api-v7-reference)
- [Niestandardowy interfejs API automatycznego sugerowania](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-autosuggest-api-v7-reference)


Nie zapomnij przeczytać [wymagań w zakresie korzystania z usługi Bing i wyświetlania danych z niej](./use-and-display-requirements.md), aby nie złamać żadnych reguł dotyczących korzystania z wyników wyszukiwania.
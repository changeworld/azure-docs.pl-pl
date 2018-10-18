---
title: Czym jest wyszukiwanie niestandardowe Bing?
titlesuffix: Azure Cognitive Services
description: Ogólne omówienie wyszukiwania niestandardowego Bing.
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: overview
ms.date: 09/29/2017
ms.author: v-brapel
ms.openlocfilehash: 2483bf36bb18af21bc454e08f3321b33094c43c8
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48814308"
---
# <a name="what-is-bing-custom-search"></a>Czym jest wyszukiwanie niestandardowe Bing?

Wyszukiwanie niestandardowe Bing umożliwia tworzenie dostosowanych funkcji wyszukiwania dla interesujących Cię tematów. Na przykład jeśli jesteś właścicielem witryny internetowej, w której znajduje się środowisko wyszukiwania, możesz określić domeny, witryny i strony internetowe, które będą przeszukiwane przez usługę Bing. Użytkownicy zobaczą wyniki wyszukiwania dostosowane do zawartości, która ich interesuje, zamiast przewijać kolejne strony ogólnych wyników wyszukiwania zawierające nieistotną zawartość.

Aby utworzyć niestandardowy widok internetowy, użyj [portalu](https://customsearch.ai) usługi wyszukiwania niestandardowego Bing. Portal umożliwia utworzenie wystąpienia wyszukiwania niestandardowego, określającego domeny, witryny i strony internetowe, które będą lub nie będą przeszukiwane przez usługę Bing. Oprócz określenia adresów URL treści, które znasz, możesz użyć portalu do znalezienia istotnych treści, które możesz dodać.

Portal umożliwia także przypięcie określonej strony internetowej na początku listy wyników wyszukiwania w przypadku wprowadzenia przez użytkownika określonego terminu. 

Po zdefiniowaniu wystąpienia możesz zintegrować wyszukiwanie niestandardowe ze swoją witryną internetową, aplikacją klasyczną lub aplikacją mobilną przez wywołanie interfejsu API wyszukiwania niestandardowego Bing. Jeśli masz aplikację lub witrynę internetową, możesz skorzystać z hostowanego interfejsu użytkownika do obsługi interfejsu wyszukiwania.

Na poniższej ilustracji pokazano, jak łatwo można integrować funkcję wyszukiwania niestandardowego.

![picture alt](./media/bcs-overview.png "Jak działa wyszukiwanie niestandardowe Bing.")

## <a name="adding-custom-search-box-suggestions"></a>Dodawanie niestandardowych sugestii w polu wyszukiwania

Możesz wzbogacić środowisko wyszukiwania niestandardowego, dodając niestandardowe sugestie w polu wyszukiwania. Ta funkcja umożliwia podawanie niestandardowych sugestii wyszukiwań, dopasowanych do środowiska wyszukiwania. Podczas wpisywania tekstu w polu wyszukiwania przez użytkownika jest wyświetlana lista rozwijana zawierająca ciągi zapytań sugerowane na podstawie częściowo wpisanego ciągu zapytania. Możesz określić, czy mają być zwracane tylko wprowadzone przez Ciebie sugestie niestandardowe, czy również sugestie usługi Bing. [Dowiedz się więcej](define-custom-suggestions.md).

## <a name="adding-custom-image-search-experience"></a>Dodawanie środowiska niestandardowego wyszukiwania obrazów

Możesz wzbogacić środowisko wyszukiwania niestandardowego, dodając do niego wyszukiwanie obrazów. Podobnie jak w przypadku wyników z Internetu interfejs API wyszukiwania niestandardowego obsługuje wyszukiwanie obrazów w witrynach internetowych uwzględnionych na liście w Twoim wystąpieniu. [Dowiedz się więcej](get-images-from-instance.md).

## <a name="adding-custom-video-search-experience"></a>Dodawanie środowiska niestandardowego wyszukiwania wideo

Możesz wzbogacić środowisko wyszukiwania niestandardowego, dodając do niego wyszukiwanie wideo. Podobnie jak w przypadku wyników z Internetu interfejs API wyszukiwania niestandardowego obsługuje wyszukiwanie wideo w witrynach internetowych uwzględnionych na liście w Twoim wystąpieniu. [Dowiedz się więcej](get-videos-from-instance.md).

## <a name="sharing-your-custom-search-instance-with-others"></a>Udostępnianie wystąpienia wyszukiwania niestandardowego innym osobom

Możesz łatwo umożliwić wspólne edytowanie i testowanie wystąpienia, udostępniając je członkom swojego zespołu. [Dowiedz się więcej](share-your-custom-search.md).

## <a name="next-steps"></a>Następne kroki

Aby szybko rozpocząć pracę, zobacz [Tworzenie pierwszego wystąpienia usługi wyszukiwania niestandardowego Bing](quick-start.md).

Aby uzyskać szczegółowe informacje na temat dostosowywania wystąpienia wyszukiwania, zobacz [Define a custom search instance (Definiowanie wystąpienia wyszukiwania niestandardowego)](define-your-custom-view.md).

Zapoznaj się z dokumentacją poszczególnych punktów końcowych wyszukiwania niestandardowego. Dokumentacja zawiera listę punktów końcowych, nagłówków i parametrów zapytań, które są stosowane w żądaniach wyników wyszukiwania. Zawiera także definicje obiektów odpowiedzi.

- [Interfejs API wyszukiwania niestandardowego](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference)
- [Interfejs API wyszukiwania niestandardowego obrazów](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-images-api-v7-reference)
- [Interfejs API wyszukiwania niestandardowego wideo](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-videos-api-v7-reference)
- [Interfejs API niestandardowego automatycznego sugerowania](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-autosuggest-api-v7-reference)


Nie zapomnij przeczytać [wymagań w zakresie korzystania z usługi Bing i wyświetlania danych z niej](./use-and-display-requirements.md), aby nie złamać żadnych reguł dotyczących korzystania z wyników wyszukiwania.
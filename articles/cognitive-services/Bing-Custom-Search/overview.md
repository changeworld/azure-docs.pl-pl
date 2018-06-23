---
title: Co to jest wyszukiwania usługi Bing niestandardowe? | Microsoft Docs
description: Ogólne omówienie wyszukiwania usługi Bing niestandardowe
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/29/2017
ms.author: v-brapel
ms.openlocfilehash: 7cd61fc63d0d7734b842ed222c67c6753da9a418
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347872"
---
# <a name="what-is-bing-custom-search"></a>Co to jest wyszukiwania usługi Bing niestandardowe?

Wyszukiwanie niestandardowe Bing umożliwia utworzenie środowiska wyszukiwania dopasowane do tematów, które są dla Ciebie ważne. Na przykład jeśli jesteś właścicielem witryny sieci Web, który zapewnia obsługę wyszukiwania, można określić domeny, witryn sieci Web i stron sieci Web, która wyszukuje Bing. Użytkownicy zobaczą, dostosowane do zawartości one interesujących zamiast wyniki wyszukiwania do strony za pośrednictwem wyników wyszukiwania, które mają nie znaczenia zawartość.

Aby utworzyć niestandardowy widok sieci web, użyj wyszukiwania usługi Bing niestandardowego [portal](https://customsearch.ai). Portal umożliwia utworzenie wystąpienia wyszukiwanie niestandardowe, które określa domen, witryn sieci Web i interesujące wyszukiwania usługi Bing stron sieci Web i witryn sieci Web, których nie chcesz, aby wyszukać. Oprócz określania adresów URL informacji o zawartości, można także skorzystać z portalu można znaleźć odpowiedniej zawartości, który chcesz dodać.

Portal umożliwia także przypiąć określonej strony sieci Web na początku wyników wyszukiwania po wprowadzeniu terminu wyszukiwania określonych. 

Po zdefiniowaniu wystąpienia, można zintegrować wyszukiwania niestandardowej witryny sieci Web, aplikację pulpitu lub aplikacji mobilnej wywołując niestandardowego interfejsu API wyszukiwania. Jeśli masz witrynę sieci web lub aplikacji, możesz pozwolić hostowanej interfejsu użytkownika renderowania interfejsu wyszukiwania dla Ciebie.

Na poniższej ilustracji przedstawiono łatwość integracji wyszukiwania niestandardowego.

![Obraz alt](./media/bcs-overview.png "działa jak wyszukiwania usługi Bing niestandardowe.")

## <a name="customize-search-suggestions"></a>Dostosowywanie sugestie dotyczące wyszukiwania

Jeśli masz subskrypcję wyszukiwanie niestandardowe na odpowiednim poziomie (zobacz [cennik stron](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), można dostosować sugestie dotyczące wyszukiwania w działania niestandardowego. Interfejs API automatycznego sugerowania niestandardowe zwraca listę sugerowane zapytań opartych na ciąg zapytania częściowy, który zawiera użytkownika. Z automatycznego sugerowania niestandardowych można zapewnić odpowiednie środowiska wyszukiwania sugestie wyszukiwania niestandardowego. Możesz określić, czy zwracać tylko niestandardowych sugestie lub zawierają sugestie Bing. Jeśli sugestie Bing są włączone, niestandardowe sugestie występować przed sugestii, który udostępnia usługi Bing. Sugestie Bing są ograniczone do kontekstu wystąpienia wyszukiwanie niestandardowe.

## <a name="next-steps"></a>Kolejne kroki

Aby szybko rozpocząć pracę, zobacz [Tworzenie pierwszego wystąpienia wyszukiwania usługi Bing niestandardowe](quick-start.md).

Aby uzyskać więcej informacji o dostępnych opcjach, aby dostosować wystąpienia wyszukiwania, zobacz [Definiowanie wystąpienia wyszukiwania niestandardowego](define-your-custom-view.md).

Zapoznaj się z [niestandardowego interfejsu API Search](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference) odwołania. Odwołanie zawiera listę punktów końcowych, nagłówki i parametry zapytania, które ma zostać użyty do żądania w wynikach wyszukiwania. Zawiera także definicje obiektów odpowiedzi.

Aby dowiedzieć się, jak dostosować sugestii, zobacz [zdefiniować sugestie dotyczące wyszukiwania niestandardowego](define-custom-suggestions.md).

Należy przeczytać [Bing oraz wymagania dotyczące wyświetlania](./use-and-display-requirements.md) , nie zostanie przerwane dowolne zasady dotyczące korzystania z wyników wyszukiwania.
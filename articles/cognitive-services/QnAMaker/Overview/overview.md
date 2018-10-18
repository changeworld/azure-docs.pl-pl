---
title: Co jest usługa QnA Maker?
titleSuffix: Azure Cognitive Services
description: Usługa QnA Maker to oparta na chmurze usługa interfejsu API, która stosuje niestandardową analizę uczenia maszynowego wobec pytań użytkownika zadawanych w języku naturalnym, aby zapewnić najlepszą odpowiedź.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: overview
ms.date: 10/09/2018
ms.author: tulasim
ms.openlocfilehash: 742c18815445b038e85c33a96743790491976945
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901111"
---
# <a name="what-is-qna-maker"></a>Co jest usługa QnA Maker?

Usługa QnA Maker to usługa bazy wiedzy z pytaniami i odpowiedziami, która stosuje niestandardową analizę uczenia maszynowego wobec pytań użytkownika zadawanych w języku naturalnym, aby określić najlepszą odpowiedź.

Usługa QnA Maker umożliwia zasilanie usługi opartej na chmurze za pomocą częściowo ustrukturyzowanej zawartości, takiej jak dokumenty z często zadawanymi pytaniami, adresy URL, podręczniki dotyczące produktów oraz niestandardowe pytania i odpowiedzi. Łatwy w obsłudze [portal internetowy](https://qnamaker.ai) umożliwia tworzenie, zarządzanie, szkolenie i publikowanie usługi bez jakiegokolwiek doświadczenia programistycznego. Po opublikowaniu usługi w punkcie końcowym aplikacja kliencka, np. czatbot, może zarządzać konwersacjami z użytkownikami, aby uzyskiwać pytania i na nie odpowiadać. 

![Omówienie](../media/qnamaker-overview-learnabout/overview.png)

## <a name="key-qna-maker-processes"></a>Kluczowe procesy usługi QnA Maker

Usługa QnA Maker zapewnia dwie kluczowe usługi dotyczące danych:

* **Wyodrębnianie**: strukturalne dane pytań i odpowiedzi są wyodrębniane z częściowo ustrukturyzowanych [źródeł danych](../Concepts/data-sources-supported.md), takich jak często zadawane pytania i podręczniki produktów. To wyodrębnienie można wykonać w ramach [tworzenia](https://aka.ms/qnamaker-docs-createkb) bazy wiedzy lub później, w ramach procesu edycji.

* **Dopasowywanie**: po [nauczeniu i przetestowaniu](https://aka.ms/qnamaker-docs-trainkb) bazy wiedzy możesz ją [opublikować](https://aka.ms/qnamaker-docs-publishkb). Umożliwia to użycie punktu końcowego w bazie wiedzy usługi QnA Maker, którego można następnie użyć w bocie lub aplikacji klienckiej. Ten punkt końcowy akceptuje pytanie użytkownika i odpowiada za pomocą najlepszej odpowiedzi znajdującej się w bazie wiedzy oraz udostępnia współczynnik ufności dopasowania.

```JSON
{
    "answers": [
        {
            "questions": [
                "How do I share a knowledge base with other?"
            ],
            "answer": "Sharing works at the level of a QnA Maker service, i.e. all knowledge bases in the services will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base)how to collaborate on a knowledge base.",
            "score": 70.95,
            "id": 4,
            "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
            "metadata": []
        }
    ]
}

```

## <a name="qna-maker-architecture"></a>Architektura usługi QnA Maker

Usługa QnA Maker składa się z następujących usług interfejsu API:

1. **Usługi zarządzania QnA Maker**: środowisko zarządzania na potrzeby bazy wiedzy usługi QnA Maker, które obejmuje początkowe tworzenie, aktualizowanie, uczenie i publikowanie. Te działania mogą zostać wykonane za pośrednictwem [portalu](https://qnamaker.ai) lub [interfejsów API zarządzania](https://aka.ms/qnamaker-v4-apis). 

2. **Usługa przewidywania QnA Maker**: jest wdrażana w subskrypcji platformy Azure w określonym regionie. Zawartość bazy wiedzy klienta jest przechowywana w usłudze [Azure Search](https://azure.microsoft.com/services/search/), a punkt końcowy jest wdrażany jako [usługa aplikacji](https://azure.microsoft.com/services/app-service/). Możesz przeprowadzić wdrożenie zasobu usługi [Application Insights](https://azure.microsoft.com/services/application-insights/) na potrzeby analizy.

![Architektura](../media/qnamaker-overview-learnabout/architecture.png)


## <a name="service-highlights"></a>Najważniejsze informacje dotyczące usługi

- Kompletna środowisko **bez kodu** służące do [tworzenia bota często zadawanych pytań](https://aka.ms/qnamaker-docs-create-faqbot).
- **Brak ograniczania przepustowości sieci w przypadku przewidywań**. Opłaty są naliczane za hostowanie usługi, a nie za liczbę transakcji. Więcej szczegółów można znaleźć na [stronie cennika](https://aka.ms/qnamaker-docs-pricing).
- **Skalowanie wedle potrzeb**. Wybierz odpowiednie jednostki SKU poszczególnych składników pasujących do danego scenariusza. Zobacz, jak [wybrać pojemność](https://aka.ms/qnamaker-docs-capacity) dla używanej usługi QnA Maker.
- **Pełna zgodność z danymi**. Składniki usługi przewidywania są wdrażane w ramach subskrypcji platformy Azure i granic jej zgodności.


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie usługi QnA Maker](../how-to/set-up-qnamaker-service-azure.md)

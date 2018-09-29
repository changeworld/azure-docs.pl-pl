---
title: Co jest usługa QnA Maker?
titleSuffix: Azure Cognitive Services
description: Usługa QnA Maker umożliwia zasilanie usługi pytań i odpowiedzi za pomocą częściowo ustrukturyzowanej zawartości, takiej jak dokumenty z często zadawanymi pytaniami, adresy URL i podręczniki produktów.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: overview
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: eba7355faf304721fdac4bfdb88f8a69ed41fcba
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47038157"
---
# <a name="what-is-qna-maker"></a>Co jest usługa QnA Maker?

Usługa [QnA Maker](https://qnamaker.ai) umożliwia zasilanie usługi pytań i odpowiedzi za pomocą częściowo ustrukturyzowanej zawartości, takiej jak dokumenty z często zadawanymi pytaniami, adresy URL i podręczniki dotyczące produktów. Możesz utworzyć model pytań i odpowiedzi elastyczny względem zapytań użytkowników i zapewniający odpowiedzi, których bot będzie uczony w celu używania ich w sposób naturalny i konwersacyjny.

Łatwy w użyciu graficzny interfejs użytkownika umożliwia tworzenie i uczenie usługi oraz zarządzanie nią oraz pozwala ją uruchamiać bez potrzeby posiadania jakiegokolwiek doświadczenia programistycznego.

![Omówienie](../media/qnamaker-overview-learnabout/overview.png)

## <a name="highlights"></a>Najważniejsze informacje

- Kompletna środowisko **bez kodu** służące do [tworzenia bota często zadawanych pytań](https://aka.ms/qnamaker-docs-create-faqbot).
- **Koniec z ograniczaniem przepustowości sieci**. Opłaty są naliczane za hostowanie usługi, a nie za liczbę transakcji. Więcej szczegółów można znaleźć na [stronie cennika](https://aka.ms/qnamaker-docs-pricing).
- **Skalowanie zgodnie z potrzebami**. Wybierz odpowiednie jednostki SKU poszczególnych składników pasujących do danego scenariusza. Zobacz, jak [wybrać pojemność](https://aka.ms/qnamaker-docs-capacity) dla używanej usługi QnA Maker.
- **Pełna zgodność z danymi**. Dane i składniki środowiska uruchomieniowego są wdrażane w ramach subskrypcji platformy Azure użytkownika i granic jej zgodności. Więcej informacji można znaleźć poniżej.

## <a name="key-qna-maker-processes"></a>Kluczowe procesy usługi QnA Maker

Usługa QnA Maker zapewnia dwie kluczowe usługi dotyczące danych:

* **Wyodrębnianie**: strukturalne dane pytań i odpowiedzi są wyodrębniane z częściowo ustrukturyzowanych źródeł danych, takich jak często zadawane pytania i podręczniki produktów. Ta operacja wyodrębniania odbywa się podczas tworzenia bazy wiedzy. Utwórz swoją bazę wiedzy [tutaj](https://aka.ms/qnamaker-docs-createkb).

* **Dopasowywanie**: po [nauczeniu i przetestowaniu](https://aka.ms/qnamaker-docs-trainkb) bazy wiedzy możesz ją [opublikować](https://aka.ms/qnamaker-docs-publishkb). Umożliwia to użycie punktu końcowego w bazie wiedzy usługi QnA Maker, którego można następnie użyć w bocie lub aplikacji. Ten punkt końcowy akceptuje pytanie użytkownika i odpowiada za pomocą najlepszego dopasowania pytanie/odpowiedź znajdującego się w bazie wiedzy oraz udostępnia współczynnik ufności dopasowania.

## <a name="qna-maker-architecture"></a>Architektura usługi QnA Maker

Stos usługi QnA Maker składa się z następujących usług:

1. **Usługi zarządzania QnA Maker (warstwa kontroli)**: środowisko zarządzania na potrzeby bazy wiedzy usługi QnA Maker, które obejmuje początkowe tworzenie, aktualizowanie, uczenie i publikowanie. Te działania mogą zostać wykonane za pośrednictwem [portalu](https://qnamaker.ai) lub [interfejsów API zarządzania](https://aka.ms/qnamaker-v4-apis). Usługi zarządzania komunikują się z poniższym składnikiem środowiska uruchomieniowego.

2. **Środowisko uruchomieniowe usługi QnA Maker (warstwa danych)**: dane i środowisko uruchomieniowe są wdrażane w ramach subskrypcji platformy Azure użytkownika w wybranym regionie. Zawartość pytań i odpowiedzi klienta jest przechowywana w usłudze [Azure Search](https://azure.microsoft.com/services/search/), a środowisko uruchomieniowe jest wdrażane jako [usługa aplikacji](https://azure.microsoft.com/services/app-service/). Opcjonalnie możesz przeprowadzić wdrożenie zasobu usługi [Application Insights](https://azure.microsoft.com/services/application-insights/) na potrzeby analizy.

![Architektura](../media/qnamaker-overview-learnabout/architecture.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie usługi QnA Maker](../how-to/set-up-qnamaker-service-azure.md)

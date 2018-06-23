---
title: Tworzenie punktu końcowego mowy niestandardowych przy użyciu niestandardowej usługi rozpoznawania mowy na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć niestandardowe endpoint mowy na tekst przy użyciu niestandardowej usługi mowy w usługach kognitywnych.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 07/08/2017
ms.author: panosper
ms.openlocfilehash: 99bc275db1f0c1b45b3db440d2e03d0db9ab5cf6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347040"
---
# <a name="create-a-custom-speech-to-text-endpoint"></a>Tworzenie niestandardowych końcowego mowy na tekst
Po utworzeniu niestandardowych modeli akustycznego lub języka modeli, można je wdrożyć w niestandardowych endpoint mowy na tekst. 

## <a name="create-an-endpoint"></a>Tworzenie punktu końcowego
Aby utworzyć nowy punkt końcowy niestandardowe, wybierz **wdrożeń** na **mowy niestandardowych** menu w górnej części strony. Ta akcja powoduje przejście do **wdrożeń** strony, która zawiera tabelę z bieżącej niestandardowe punkty końcowe. Jeśli jeszcze nie utworzono żadnych punktów końcowych, tabela jest pusta. Bieżące ustawienia regionalne znajduje odzwierciedlenie w tytule tabeli. 

Aby utworzyć wdrożenie w innym języku, wybierz **zmiany ustawień regionalnych**. Aby uzyskać więcej informacji o obsługiwanych języków, zobacz [obsługiwanych ustawień regionalnych w niestandardowej usługi rozpoznawania mowy](cognitive-services-custom-speech-change-locale.md).

Aby utworzyć nowy punkt końcowy, wybierz **Utwórz nowy**. W **tworzenia wdrożenia** okienku, wprowadź informacje w **nazwa** i **opis** pola niestandardowe wdrożenia.

W **subskrypcji** kombi Wybierz subskrypcję, która ma być używany. Jeśli subskrypcja S2, możesz wybrać jednostek skalowania i rejestrowanie zawartości. Aby uzyskać więcej informacji na temat jednostek skalowania i rejestrowania, zobacz [liczników niestandardowych mowy usługi i przydziały](../cognitive-services-custom-speech-meters.md).

W poniższej tabeli przedstawiono sposobu mapowania jednostki skalowania dostępne równoczesnych żądań:

| Jednostki skalowania | Liczba równoczesnych żądań |
| ------ | ----- |
| 0 | 1 |
| 1 | 5 |
| 2 | 10 |
| 3 | 15 |
| n | 5 * n |

Można również wybrać opcję rejestrowania zawartości jest włączony lub wyłączony. Oznacza to, że wybrano, czy ruch punktu końcowego są przechowywane do wewnętrznego użytku firmy Microsoft. Jeśli nie jest zaznaczone, będą pomijane przechowywania ruchu. Pomijanie powoduje rejestrowanie zawartości dodatkowych kosztów. Zapoznaj się [strona informacji o cenach](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/) szczegółowe informacje.

> [!NOTE]
> Na stronie dotyczącej cen zawartości rejestrowania nosi "Nie Śledź".
>


Ponadto firma Microsoft udostępnia oszacowanie kosztów tak, aby poznać wpływ na koszty jednostek skalowania i rejestrowanie zawartości. Ta Szacowana została wstępnie oszacowana i może się różnić od rzeczywistej kosztów.

> [!NOTE]
> Te ustawienia nie są dostępne z F0 subskrypcjami (warstwa bezpłatna).
>

W **modelu akustycznego** akustycznego model, który ma, wybierz i w **Model języka** , wybierz model języka, który ma na liście. Opcje dla modeli akustycznego i język, zawsze należy uwzględniać modeli bazowych firmy Microsoft. Wybór podstawowego modelu ogranicza kombinacji. Nie można mieszać modeli bazowych konwersacji z wyszukiwania i dyktowania modeli bazowych.

![Strona Tworzenie wdrożenia](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-create2.png)

> [!NOTE]
> Pamiętaj zaakceptować warunki użytkowania i uzyskać informacje o cenach, zaznaczając pole wyboru.
>

Po wybraniu modeli akustycznego i język, wybierz **Utwórz**. Ta akcja wraca do **wdrożeń** strony. Tabela zawiera teraz wpis odpowiadający nowego punktu końcowego. Stan punktu końcowego odzwierciedla swojego bieżącego stanu, gdy jest tworzony. Może upłynąć do 30 minut, można utworzyć nowy punkt końcowy z niestandardowych modeli. Gdy stan wdrożenia jest *Complete*, punkt końcowy jest gotowy do użycia.

![Na stronie wdrożenia](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-ready.png)

Gdy wdrożenie jest gotowy, nazwa wdrożenia staje się łącza. Wyświetla kliknąć łącze **informacje na temat wdrażania** stronę, która wyświetla adresy URL punktu końcowego niestandardowych do użycia z albo żądanie HTTP lub Microsoft kognitywnych usług mowy klienta biblioteki, który używa gniazda sieci web.

![Strona informacje o wdrożeniu](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-info2.png)

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej samouczków zobacz:
* [Użyj niestandardowych endpoint mowy na tekst](cognitive-services-custom-speech-use-endpoint.md)
* [Tworzenie niestandardowych modelu akustycznego](cognitive-services-custom-speech-create-acoustic-model.md)
* [Tworzenie modelu języków niestandardowych](cognitive-services-custom-speech-create-language-model.md)

---
title: Migrowanie ceny warstwy punktów końcowych w usłudze Custom Speech Service na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przeprowadzić migrację wdrożenia z warstwy S0 i S1, S2 Custom Speech Service punktami końcowymi, w usługach Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 07/05/2017
ms.author: nitinme
ms.openlocfilehash: 8e6c662545ae1275b2193341f9311f403551671f
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55858257"
---
# <a name="migrate-deployments-to-the-new-pricing-model"></a>Migracja wdrożenia do nowego modelu cen

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

Począwszy od lipca 2017 r. Custom Speech Service oferuje [nowego modelu cen](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/). Nowy model jest *łatwiejsze do zrozumienia*, *prostsza do obliczania kosztów*, i *bardziej elastyczne* pod kątem skalowania. Skalowanie, firma Microsoft wprowadziła pojęcie jednostki skalowania. Każda jednostka skalowania może obsługiwać pięciu współbieżnych żądań. Skalowanie na potrzeby współbieżnych żądań, w tym modelu została ustawiona na 5 równoczesnych żądań dla warstwy S0 i został ustawiony na 12 równoczesnych żądań dla warstwy S1. Możemy otworzyć te limity, która oferuje większą elastyczność dzięki wymagań dotyczących przypadków użycia.

Jeśli uruchamiasz stare warstwy S0 lub S1, zaleca się migrację istniejących wdrożeń do nowej warstwy S2. Nowa warstwa S2 obejmuje warstwy S0 i S1. Można wyświetlić dostępne opcje na poniższej ilustracji:

![Strona "Wybieranie warstwy cenowej"](../../../media/cognitive-services/custom-speech-service/custom-speech-pricing-tier.png)

Firma Microsoft podchodzi do migracji w taki sposób, lub częściowo zautomatyzowanych. Po pierwsze możesz wyzwolić migracji przez wybranie nowej warstwy cenowej. Następnie możemy migracji wdrożenia automatycznie.

Mapowanie ze starego warstwy, aby przeskalować jednostki przedstawiono w poniższej tabeli:

| Warstwa | Równoczesnych żądań (stary model) | Migracja | Równoczesnych żądań |
|----- | ----- | ---- | ---- |
| S0 |  5   |   => **S2** z jednostką skalowania 1 |   5 |
| S1 |  12  |   => **S2** przy użyciu jednostek skalowania 3 |  15 |

Aby przeprowadzić migrację do nowej warstwy, wykonaj następujące czynności:

## <a name="step-1-check-your-existing-deployment"></a>Krok 1: Sprawdzanie istniejącego wdrożenia
Przejdź do [portal usługi Custom Speech Service](http://cris.ai)i sprawdź istniejących wdrożeń. W tym przykładzie istnieją dwa wdrożenia. Jedno wdrożenie działa w warstwie S0 i innych wdrożeń działających w warstwie S1. Wdrożenia są wyświetlane w **opcje wdrażania** kolumny w następującej tabeli:

![Na stronie wdrożeń](../../../media/cognitive-services/custom-speech-service/custom-speech-deployments.png)

## <a name="step-2-select-your-new-pricing-tier-in-the-azure-portal"></a>Krok 2: Wybierz nowy warstwę cenową w witrynie Azure portal
1. Otwórz nową kartę przeglądarki i zaloguj się do [witryny Azure portal](http://ms.portal.azure.com/). 

2. W **usług Cognitive Services** okienku w obszarze **subskrypcje** , wybierz swoją subskrypcję niestandardowa zamiana mowy na liście. 

3. W okienku dla Twojej subskrypcji, wybierz **warstwa cenowa**.

    ![Link "Warstwa cenowa"](../../../media/cognitive-services/custom-speech-service/custom-speech-update-tier.png)

4. Na **Wybieranie warstwy cenowej** wybierz opcję **S2 standardowa**. Ta warstwa cenowa jest nowy, uproszczony i bardziej elastyczne warstwy cenowej.

5. Wybierz **wybierz** przycisku.

    ![Strona "Wybieranie warstwy cenowej"](../../../media/cognitive-services/custom-speech-service/custom-speech-update-pricing.png)

## <a name="step-3-check-the-migration-status-in-the-custom-speech-service-portal"></a>Krok 3: Sprawdź stan migracji w portalu usługi Custom Speech Service
Wróć do portalu usługi Custom Speech Service i sprawdź wdrożeń. (Jeśli okno przeglądarki jest wciąż otwarty, Odśwież go.) 

Stan wdrożenia powiązanych należy nastąpiło przełączenie na *przetwarzania*. Można również sprawdzić poprawność migracji, sprawdzając **opcje wdrażania** kolumny. Można teraz znaleźć informacje o jednostkach skalowania i rejestrowania. Jednostki skalowania powinny odzwierciedlać poprzedniej warstwy cenowej. Rejestrowanie powinien również zostać włączony, jak pokazano w tabeli:

![Kolumna opcje wdrażania](../../../media/cognitive-services/custom-speech-service/custom-speech-deployments-new.png)


> [!NOTE]
> Jeśli masz problemy podczas migracji, skontaktuj się z nami.
>

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej samouczków zobacz:
* [Tworzenie niestandardowego modelu akustycznego](cognitive-services-custom-speech-create-acoustic-model.md)
* [Tworzenie niestandardowego modelu językowego](cognitive-services-custom-speech-create-language-model.md)
* [Utwórz niestandardowy punkt końcowy rozpoznawania mowy na tekst](cognitive-services-custom-speech-create-endpoint.md)

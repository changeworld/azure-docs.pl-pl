---
title: Migrowanie ceny warstw punktów końcowych z niestandardowej usługi rozpoznawania mowy na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przeprowadzić migrację wdrożenia z warstw S0 i S1 do punktów końcowych S2 niestandardowej usługi rozpoznawania mowy w usługach kognitywnych.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 07/05/2017
ms.author: panosper
ms.openlocfilehash: 6d92459deb3464cd97c215cbf9a8320628b6da80
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347569"
---
# <a name="migrate-deployments-to-the-new-pricing-model"></a>Migracja wdrożeń do nowego modelu cenowego
Począwszy od 2017 lipca oferuje niestandardowej usługi rozpoznawania mowy [nowy model cenowy](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/). Nowy model jest *łatwiejsze do zrozumienia*, *łatwiejsze do obliczania kosztów*, i *bardziej elastyczne* pod względem skalowania. Do skalowania, firma Microsoft wprowadziła pojęcie jednostki skalowania. Każdą jednostką skalowania może obsługiwać pięć równoczesnych żądań. Skalowanie dla żądań jednocześnie w starym model został ustawiony na 5 równoczesnych żądań dla warstwy S0 i została ustawiona na 12 równoczesnych żądań dla warstwy S1. Możemy otworzyć te limity oferowanie większą elastyczność z wymaganiami dotyczącymi przypadek użycia.

Po uruchomieniu starego warstwy S0 lub S1 zalecamy migracji istniejących wdrożeń do nową warstwę S2. Nowa warstwa S2 obejmuje zarówno S0, jak i S1 warstw. Można wyświetlić dostępne opcje na poniższej ilustracji:

![Na stronie "Wybierz poziom cenowy"](../../../media/cognitive-services/custom-speech-service/custom-speech-pricing-tier.png)

Firma Microsoft podchodzi do migracji w sposób częściowo zautomatyzowanych. Najpierw należy wywołać migracji, wybierając nową warstwę cenową. Następnie możemy przeprowadzić migrację wdrożenia automatycznie.

Mapowanie z warstwy stare do jednostek skalowania przedstawiono w poniższej tabeli:

| Warstwa | Równoczesnych żądań (stary model) | Migracja | Równoczesnych żądań |
|----- | ----- | ---- | ---- |
| S0 |  5   |   => **S2** z jednostką skalowania 1 |   5 |
| S1 |  12  |   => **S2** z 3 jednostki skalowania |  15 |

Aby przeprowadzić migrację do nowego warstwy, wykonaj następujące czynności:

## <a name="step-1-check-your-existing-deployment"></a>Krok 1: Sprawdź istniejącego wdrożenia
Przejdź do [portalu niestandardowe mowy](http://cris.ai)i sprawdź istniejących wdrożeń. W naszym przykładzie występują dwa wdrożenia. Jedno wdrożenie działa w warstwie S0, a drugie wdrożenie działa w warstwie S1. Wdrożenia są wyświetlane w **opcje wdrażania** kolumny w następującej tabeli:

![Na stronie wdrożenia](../../../media/cognitive-services/custom-speech-service/custom-speech-deployments.png)

## <a name="step-2-select-your-new-pricing-tier-in-the-azure-portal"></a>Krok 2: Wybierz poziom cenowy w portalu Azure
1. Otwórz nową kartę przeglądarki i zaloguj się do [portalu Azure](http://ms.portal.azure.com/). 

2. W **kognitywnych usług** okienko w **subskrypcje** listy, wybierz subskrypcję niestandardowych mowy. 

3. W okienku dla Twojej subskrypcji, wybierz **warstwa cenowa**.

    ![Łącze "Warstwa cenowa"](../../../media/cognitive-services/custom-speech-service/custom-speech-update-tier.png)

4. Na **wybierz warstwę cenową** wybierz pozycję **standardowe S2**. Ta warstwa cenowa jest nowe, uproszczone i bardziej elastyczne warstwę cenową.

5. Wybierz **wybierz** przycisku.

    ![Na stronie "Wybierz poziom cenowy"](../../../media/cognitive-services/custom-speech-service/custom-speech-update-pricing.png)

## <a name="step-3-check-the-migration-status-in-the-custom-speech-service-portal"></a>Krok 3: Sprawdź stan migracji w portalu usługi mowy niestandardowe
Wróć do portalu usługi mowy niestandardowe, a następnie sprawdzić wdrożeń. (Jeśli okno przeglądarki jest nadal otwarty, Odśwież jego.) 

Stan wdrożenia pokrewne powinien przełączono się do *przetwarzania*. Można również sprawdzić poprawność migracji sprawdzając **opcje wdrażania** kolumny. Można teraz znaleźć informacje o jednostek skalowania i rejestrowania. Jednostki skalowania powinien odzwierciedlać poprzedniej warstwy cenowej. Rejestrowanie powinien również być włączony, jak pokazano w tabeli:

![Kolumna opcje wdrażania](../../../media/cognitive-services/custom-speech-service/custom-speech-deployments-new.png)


> [!NOTE]
> Jeśli masz problemy podczas migracji, skontaktuj się z nami.
>

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej samouczków zobacz:
* [Tworzenie niestandardowych modelu akustycznego](cognitive-services-custom-speech-create-acoustic-model.md)
* [Tworzenie modelu języków niestandardowych](cognitive-services-custom-speech-create-language-model.md)
* [Tworzenie niestandardowych końcowego mowy na tekst](cognitive-services-custom-speech-create-endpoint.md)

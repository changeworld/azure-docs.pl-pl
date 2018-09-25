---
title: Tworzenie punktu końcowego niestandardowa zamiana mowy z usługa Custom Speech Service na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć niestandardowy punkt końcowy rozpoznawania mowy na tekst za pomocą usługa Custom Speech Service w usługach Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 07/08/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: ed93afa8e10fdfbb0d45f4500b4a648716e25e00
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46952226"
---
# <a name="create-a-custom-speech-to-text-endpoint"></a>Tworzenie niestandardowego punktu końcowego funkcji zamiany mowy na tekst
Po utworzeniu niestandardowych modeli akustycznych lub modeli językowych, można je wdrożyć w niestandardowy punkt końcowy rozpoznawania mowy na tekst. 

## <a name="create-an-endpoint"></a>Tworzenie punktu końcowego
Aby utworzyć nowy niestandardowy punkt końcowy, wybierz **wdrożeń** na **Custom Speech** menu w górnej części strony. Ta akcja spowoduje przejście do **wdrożeń** strony, która zawiera tabelę bieżącego niestandardowe punkty końcowe. Jeśli jeszcze nie utworzono żadnych punktów końcowych, tabela jest pusta. Bieżące ustawienia regionalne są odzwierciedlone w tytule tabeli. 

Aby utworzyć wdrożenie w innym języku, wybierz **Zmień ustawienia regionalne**. Aby uzyskać więcej informacji na temat obsługiwanych języków, zobacz [obsługiwane ustawienia regionalne w usłudze Custom Speech Service](cognitive-services-custom-speech-change-locale.md).

Aby utworzyć nowy punkt końcowy, wybierz **Utwórz nowy**. W **tworzenia wdrożenia** okienku, wprowadź informacje w **nazwa** i **opis** pola niestandardowe wdrożenie.

W **subskrypcji** kombi Wybierz subskrypcję, dla której chcesz użyć. Jeśli subskrypcję S2, możesz wybrać jednostki skali i rejestrowanie zawartości. Aby uzyskać więcej informacji dotyczących jednostki skali i rejestrowanie, zobacz [liczniki usługi Custom Speech Service i przydziały](../cognitive-services-custom-speech-meters.md).

W poniższej tabeli przedstawiono, jak mapowania jednostek skalowania dostępna równoczesnych żądań:

| Jednostka skalowania | Liczba równoczesnych żądań |
| ------ | ----- |
| 0 | 1 |
| 1 | 5 |
| 2 | 10 |
| 3 | 15 |
| n | 5 * n |

Możesz również wybrać, czy rejestrowanie zawartości to włączać i wyłączać. Oznacza to, że wybrano, czy ruch punktu końcowego są przechowywane do użytku wewnętrznego firmy Microsoft. Jeśli nie jest zaznaczone, przechowywania ruch zostanie pominięte. Pomijanie powoduje rejestrowanie zawartości dodatkowych kosztów. Zapoznaj się z [informacji stronę z cennikiem](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/) Aby uzyskać szczegółowe informacje.

> [!NOTE]
> Rejestrowanie zawartości jest nazywany "Nie Śledź" na stronie z cennikiem.
>


Ponadto firma Microsoft zapewnia przybliżoną prognozę kosztów, więc, że masz świadomość wpływu na koszt jednostki skali i rejestrowanie zawartości. Te dane szacunkowe została wstępnie oszacowana i może różnić się od rzeczywistej koszty.

> [!NOTE]
> Te ustawienia nie są dostępne z subskrypcjami (warstwa bezpłatna) F0.
>

W **Model akustyczny** wybierz model akustyczny, który chcesz, a następnie w **modelu językowego** , wybierz model języka, który ma na liście. Opcje modele akustyczne i językowe zawsze zawierać modeli bazowych firmy Microsoft. Wybór modelu bazowego ogranicza kombinacje. Nie można mieszać modeli bazowych konwersacji z wyszukiwania i dyktowania modeli bazowych.

![Strona Tworzenie wdrożenia](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-create2.png)

> [!NOTE]
> Pamiętaj zaakceptować warunki użytkowania i informacje o cenach, zaznaczając pole wyboru.
>

Po wybraniu swoje modele akustyczne i językowe wybierz **Utwórz**. Spowoduje powrót do **wdrożeń** strony. Tabela zawiera teraz wpis, który odnosi się do nowego punktu końcowego. Stan punktu końcowego odzwierciedla bieżącego stanu podczas jej tworzenia. Może potrwać do 30 minut utworzyć nowy punkt końcowy przy użyciu niestandardowych modeli. Gdy stan wdrożenia jest *Complete*, punkt końcowy jest gotowy do użycia.

![Na stronie wdrożeń](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-ready.png)

Gdy wdrożenie będzie gotowe, nazwa wdrożenia staje się linkiem. Klikając link Wyświetla **informacje na temat wdrażania** stronę, która wyświetla adresy URL niestandardowego punktu końcowego za pomocą albo żądanie HTTP lub Microsoft Cognitive Services mowy klienta biblioteki, która korzysta z gniazda sieci web.

![Na stronie informacje o wdrożeniu](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-info2.png)

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej samouczków zobacz:
* [Użyj niestandardowego punktu końcowego mowy na tekst](cognitive-services-custom-speech-use-endpoint.md)
* [Tworzenie niestandardowego modelu akustycznego](cognitive-services-custom-speech-create-acoustic-model.md)
* [Utwórz model języka niestandardowego](cognitive-services-custom-speech-create-language-model.md)

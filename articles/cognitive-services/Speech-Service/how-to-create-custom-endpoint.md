---
title: Tworzenie punktu końcowego niestandardowa zamiana mowy z usług przetwarzania mowy na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć niestandardowy punkt końcowy rozpoznawania mowy na tekst za pomocą usług przetwarzania mowy platformy Azure.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 03/12/2018
ms.author: panosper
ms.openlocfilehash: 1f7a84d187ba6279caad4926d54bfc56254152af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60539030"
---
# <a name="create-a-custom-speech-to-text-endpoint"></a>Tworzenie niestandardowego punktu końcowego funkcji zamiany mowy na tekst

Po utworzeniu niestandardowych modeli akustycznych lub modeli językowych, można je wdrożyć w niestandardowy punkt końcowy rozpoznawania mowy na tekst.

## <a name="create-an-endpoint"></a>Tworzenie punktu końcowego
Aby utworzyć nowy niestandardowy punkt końcowy, wybierz **punktów końcowych** na **Custom Speech** menu w górnej części strony. Ta akcja spowoduje przejście do **punktów końcowych** strony, która zawiera tabelę bieżącego niestandardowe punkty końcowe. Jeśli jeszcze nie utworzono żadnych punktów końcowych, tabela jest pusta. Bieżące ustawienia regionalne są odzwierciedlone w tytule tabeli.

Aby utworzyć wdrożenie w innym języku, wybierz **Zmień ustawienia regionalne**. Aby uzyskać więcej informacji o obsługiwanych językach.

Aby utworzyć nowy punkt końcowy, wybierz **Utwórz nowy**. W **Create Endpoint** okienku, wprowadź informacje w **nazwa** i **opis** pola niestandardowe wdrożenie.

W **subskrypcji** kombi Wybierz subskrypcję, dla której chcesz użyć. Jeśli jest to S0 subskrypcji (płatniczej), możesz wybrać współbieżnych żądań.

Możesz również wybrać, czy rejestrowanie zawartości to włączać i wyłączać. Oznacza to, że w przypadku zaznaczenia czy ruch punktu końcowego są przechowywane. Jeśli nie jest zaznaczone, przechowywania ruch zostanie pominięte. Wszystkie zarejestrowane zawartości można znaleźć łącza pobierania w ramach punktu końcowego -> Widok szczegółów

W **Model akustyczny** wybierz model akustyczny, który chcesz, a następnie w **modelu językowego** , wybierz model języka, który ma na liście. Opcje modele akustyczne i językowe zawsze zawierać modeli bazowych firmy Microsoft. Wybór modelu bazowego ogranicza kombinacje. Nie można mieszać modeli bazowych konwersacji z wyszukiwania i dyktowania modeli bazowych.

> [!NOTE]
> Pamiętaj zaakceptować warunki użytkowania i informacje o cenach, zaznaczając pole wyboru.
>

Po wybraniu swoje modele akustyczne i językowe wybierz **Utwórz**. Spowoduje powrót do **wdrożeń** strony. Tabela zawiera teraz wpis, który odnosi się do nowego punktu końcowego. Stan punktu końcowego odzwierciedla bieżącego stanu podczas jej tworzenia. Może potrwać do 30 minut utworzyć nowy punkt końcowy przy użyciu niestandardowych modeli. Gdy stan wdrożenia jest *Complete*, punkt końcowy jest gotowy do użycia.

Gdy wdrożenie będzie gotowe, nazwę punktu końcowego staje się łącze. Klikając link Wyświetla **informacje o punkcie końcowym** stronę, która wyświetla adresy URL niestandardowego punktu końcowego za pomocą albo żądanie HTTP lub Microsoft Cognitive Services mowy klienta biblioteki, która korzysta z gniazda sieci web.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej samouczków zobacz:
- [Pobierz subskrypcję wersji próbnej usługi Speech Services](https://azure.microsoft.com/try/cognitive-services/)
- [Utwórz niestandardowy model akustyczny](how-to-customize-acoustic-models.md)
- [Utwórz model języka niestandardowego](how-to-customize-language-model.md)

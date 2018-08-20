---
title: Omówienie usługi Azure Video Indexer | Microsoft Docs
description: Ten temat zawiera omówienie usługi Video Indexer.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.component: video-indexer
ms.topic: overview
ms.date: 07/25/2018
ms.author: nolachar
ms.openlocfilehash: f52c4af29d0c7de8b5edbe869640ffc5dddb5c5e
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397895"
---
# <a name="what-is-video-indexer-preview"></a>Co to jest Video Indexer? (wersja zapoznawcza)

Usługa Video Indexer to aplikacja w chmurze utworzona za pomocą usługi Azure Media Analytics, usług Cognitive Services (takich jak interfejs API rozpoznawania twarzy, Microsoft Translator, interfejs API przetwarzania obrazów i Custom Speech Service) oraz usługi Azure Search. Umożliwia wyodrębnianie następujących szczegółowych informacji z wideo przy użyciu technologii sztucznej inteligencji:

- **Automatyczne wykrywanie języka**: usługa Video Indexer może automatycznie wykrywać język w wideo. Automatyczne wykrywanie języka obsługuje obecnie język angielski, hiszpański, francuski, niemiecki, włoski, chiński (uproszczony), japoński i rosyjski. Gdy języka nie będzie można wykryć, użyty zostanie język angielski.
- **Transkrypcja audio**: usługa Video Indexer ma funkcję zamiany mowy na tekst, która pozwala klientom uzyskać transkrypcję wypowiadanych słów. Obsługiwane języki obejmują angielski, hiszpański, francuski, niemiecki, włoski, chiński (uproszczony), portugalski (Brazylia), japoński i rosyjski (w przyszłości będzie ich więcej). 
- **Śledzenie i identyfikacja twarzy**: technologie rozpoznawania twarzy umożliwiają wykrywanie twarzy w wideo. Wykryte twarze są porównywane z bazą danych osobistości w celu oceny, które z nich występują w wideo. Klienci mogą również oznaczać etykietami twarze, które nie należą do osobistości. Usługa Video Indexer tworzy model twarzy na podstawie tych etykiet i może rozpoznawać te twarze w wideo przesłanych w przyszłości.
- **Indeksowanie osób mówiących**: usługa Video Indexer ma możliwość mapowania i rozróżniania, które osoby mówiące wypowiedziały które wyrazy i kiedy.
- **Wizualne rozpoznawanie tekstu**: dzięki tej technologii usługa Video Indexer wyodrębnia tekst widoczny w wideo.  
- **Wykrywanie głosu**: ta funkcja umożliwia usłudze Video Indexer odróżnianie hałasu w tle od głosu. 
- **Wykrywanie scen**: usługa Video Indexer ma możliwość wykonywania analizy wizualnej wideo, aby określić moment zmiany sceny w wideo.
- **Wyodrębnianie ramki kluczowej**: usługa Video Indexer automatycznie wykrywa ramki kluczowe w wideo. 
- **Analiza tonacji**: usługa Video Indexer przeprowadza analizę tonacji tekstu wyodrębnionego za pomocą funkcji przekształcania mowy na tekst i optycznego rozpoznawania znaków, a następnie udostępnia te informacje w formie tonacji pozytywnej, negatywnej lub neutralnej wraz z kodami czasowymi.
- **Tłumaczenie**: usługa Video Indexer może tłumaczyć transkrypcję audio z jednego języka na inny. Obsługiwane są następujące języki: angielski, hiszpański, francuski, niemiecki, włoski, chiński uproszczony, portugalski (Brazylia), japoński i rosyjski. Po wykonaniu tłumaczenia użytkownik może nawet uzyskać napisy w innych językach w odtwarzaczu wideo.
- **Moderowanie zawartości wizualnej**: ta technologia umożliwia wykrywanie zawartości dla dorosłych i/lub erotycznej w wideo i może służyć do filtrowania zawartości. 
- **Wyodrębnianie słów kluczowych**: usługa Video Indexer wyodrębnia słowa kluczowe z transkrypcji wypowiedzianych słów i tekstu rozpoznanego przez aparat wizualnego rozpoznawania tekstu.
- **Etykiety**: usługa Video Indexer udostępnia etykiety dla obiektów wizualnych, takie jak kot, pies, stół i samochód, a także dla czynności, takie jak stanie, bieganie lub latanie.
- **Marki**: usługa Video Indexer wyodrębnia marki firm z transkrypcji wypowiedzianych słów i tekstu rozpoznanego przez aparat wizualnego rozpoznawania tekstu.

Gdy usługa Video Indexer zakończy przetwarzanie i analizę, można przeglądać, selekcjonować, przeszukiwać i publikować szczegółowe informacje o wideo.

Bez względu na to, czy jesteś menedżerem zawartości, czy deweloperem, usługa Video Indexer jest w stanie spełnić Twoje wymagania. Menedżerowie zawartości mogą za pomocą portalu internetowego usługi Video Indexer korzystać z niej bez konieczności pisania choćby wiersza kodu — zobacz [Rozpoczynanie korzystania z portalu usługi Video Indexer](video-indexer-get-started.md). Deweloperzy mogą korzystać z interfejsów API, aby przetwarzać zawartość na dużą skalę — zobacz [Korzystanie z interfejsu API REST usługi Video Indexer](video-indexer-use-apis.md). Usługa ta pozwala też klientom używać widgetów do publikowania strumieni wideo i wyodrębnionych szczegółowych informacji w ich własnych aplikacjach — zobacz [Osadzanie widgetów wizualnych we własnej aplikacji](video-indexer-embed-widgets.md).

Do korzystania z tej usługi możesz się zarejestrować za pomocą już posiadanego konta usługi AAD, LinkedIn, Facebook, Google lub MSA. Aby uzyskać więcej informacji, zobacz [wprowadzenie](video-indexer-get-started.md).

## <a name="scenarios"></a>Scenariusze

Poniżej przedstawiono kilka scenariuszy, w których usługa Video Indexer może być przydatna

- Wyszukiwanie — szczegółowe informacje wyodrębnione z wideo mogą służyć do rozbudowy funkcji wyszukiwania w bibliotece wideo. Na przykład indeksowanie wypowiedzianych słów i twarzy może umożliwić wyszukiwanie w wideo momentów, gdy konkretna osoba wypowiada określone słowa lub gdy dwie osoby są widoczne razem. Wyszukiwanie oparte na takich szczegółowych informacjach przyda się agencjom informacyjnym, instytucjom edukacyjnych, nadawcom, właścicielom zawartości rozrywkowej, w aplikacjach LOB dla przedsiębiorstw i ogólnie w każdej branży z biblioteką wideo, którą użytkownicy muszą przeszukiwać.

- Monetyzacja— usługa Video Indexer może zwiększyć wartość wideo. Przykładowo w branżach zarabiających na reklamach (takich jak media informacyjne czy społecznościowe) możliwe jest dostarczanie trafniejszych reklam przez przekazywanie wyodrębnionych szczegółowych informacji jako dodatkowych danych do serwera reklamowego (wyświetlenie reklamy butów sportowych bardziej pasuje do meczu futbolowego niż zawodów pływackich).

- Zaangażowanie użytkowników — szczegółowe informacje o wideo mogą służyć do zwiększenia stopnia zaangażowania użytkowników przez ustawianie dla nich wideo na odpowiednich momentach. Jako przykład weźmy wideo edukacyjne, w którym przez pierwsze 30 minut omawiane są sfery, a przez kolejne 30 minut — ostrosłupy. Uczeń czytający o ostrosłupach będzie bardziej zadowolony, jeśli wideo będzie ustawione na znaczniku 30. minuty.

Więcej informacji można znaleźć w tym [blogu](http://aka.ms/videoindexerblog).

## <a name="next-steps"></a>Następne kroki

Możesz już rozpocząć pracę z usługą Video Indexer. Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Rozpoczynanie pracy z portalem usługi Video Indexer](video-indexer-get-started.md)
- [Przetwarzanie zawartości przy użyciu interfejsu REST API usługi Video Indexer](video-indexer-use-apis.md)
- [Osadzanie widgetów wizualnych w aplikacji](video-indexer-embed-widgets.md)

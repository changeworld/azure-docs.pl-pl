---
title: Co to jest Video Indexer?
titlesuffix: Azure Cognitive Services
description: Ten temat zawiera omówienie usługi Video Indexer.
services: cognitive services
author: juliako
manager: cgronlun
ms.service: cognitive-services
ms.component: video-indexer
ms.topic: overview
ms.date: 09/15/2018
ms.author: nolachar
ms.openlocfilehash: fd92e91989bd1a37626227b327d644c9d704ab6c
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "45983018"
---
# <a name="what-is-video-indexer"></a>Co to jest Video Indexer?

Usługa Azure Video Indexer to aplikacja w chmurze utworzona za pomocą usługi Azure Media Analytics, usługi Azure Search oraz usług Cognitive Services (takich jak interfejs API rozpoznawania twarzy, Microsoft Translator, interfejs API przetwarzania obrazów i Custom Speech Service). Umożliwia wyodrębnianie szczegółowych informacji z plików wideo przy użyciu opisanych poniżej modeli usługi Video Indexer:
 
- **Automatyczne wykrywanie języka**: automatycznie rozpoznaje dominujący język mówiony. Obsługiwane języki: angielski, chiński (uproszczony), francuski, hiszpański, japoński, niemiecki, portugalski (Brazylia), rosyjski i włoski. Gdy języka nie będzie można wykryć, użyty zostanie język angielski.
- **Transkrypcja audio**: konwertuje mowę na tekst w 10 językach i umożliwia stosowanie rozszerzeń. Obsługiwane języki: angielski, arabski, chiński (uproszczony), francuski, hiszpański, japoński, niemiecki, portugalski (Brazylia), rosyjski i włoski.
- **Napisy (transkrypcja)**: tworzy napisy w trzech formatach: VTT, TTML i SRT.
- **Przetwarzanie dwukanałowe**: automatycznie wykrywa, oddziela transkrypcje i scala je w ramach pojedynczej osi czasu.
- **Redukcja szumów**: oczyszcza dźwięk rozmów telefonicznych lub nagrań z szumem (na podstawie filtrów programu Skype).
- **Dostosowywanie transkrypcji (CRIS)**: szkoli i wykonuje modele rozszerzonej niestandardowej zamiany mowy na tekst, aby utworzyć transkrypcje branżowe.
- **Wyliczanie osób mówiących**: mapuje i interpretuje to, które osoby mówiące i kiedy wypowiedziały poszczególne wyrazy.
- **Statystyka osób mówiących**: zapewnia statystykę dotyczącą proporcji aktywności poszczególnych osób mówiących.
- **Wizualne rozpoznawanie tekstu (OCR)**: wyodrębnia tekst widoczny w nagraniu wideo.
- **Wyodrębnianie ramek kluczowych**: wykrywa ramki kluczowe w nagraniu wideo.
- **Analiza tonacji**: rozpoznaje pozytywne, negatywne i neutralne opinie w padających wypowiedziach i tekście widocznym w nagraniu wideo.
- **Moderowanie zawartości wizualnej**: wykrywa widoczną zawartość dla dorosłych i/lub erotyczną.
- **Wyodrębnianie słów kluczowych**: wyodrębnia słowa kluczowe z wypowiedzianych słów i widocznego tekstu.
- **Identyfikowanie etykiet**: identyfikuje widoczne obiekty i czynności.
- **Wyodrębnianie marek**: wyodrębnia marki z wypowiedzianych słów i widocznego tekstu.
- **Wykrywanie twarzy**: wykrywa i grupuje twarze pojawiające się w nagraniu wideo.
- **Wyodrębnianie miniatur dla twarzy („najlepsza wersja twarzy”)**: automatycznie identyfikuje najlepszą wersję twarzy przechwyconą w każdej grupie twarzy (na podstawie jakości, rozmiaru i skierowania w stronę obiektywu) i wyodrębnia ją jako zasób obrazu.
- **Rozpoznawanie osobistości**: rozpoznaje osobistości w nagraniu wideo na podstawie bazy danych z informacjami o milionie osobistości. Źródłami są witryny IMDB i Wikipedia oraz lista najważniejszych influencerów w serwisie LinkedIn.
- **Niestandardowe rozpoznawanie twarzy**: rozpoznaje twarze w nagraniu wideo na podstawie modelu niestandardowego szkolonego dla konkretnego konta.
- **Moderowanie zawartości tekstowej**: wykrywa jawny tekst w transkrypcji audio.
- **Wykrywanie ujęć**: wykrywa zmiany scen w nagraniu wideo.
- **Wykrywanie czarnych ramek**: identyfikuje pojawiające się w nagraniu wideo czarne ramki.
- **Efekty dźwiękowe**: rozpoznaje efekty dźwiękowe, takie jak klaskanie, mowa i cisza.
- **Ustalanie tematów**: ustala główne tematy na podstawie transkrypcji. Uwzględniana jest taksonomia [IPTC](https://iptc.org/standards/media-topics/) 1. poziomu.
- **Wykrywanie emocji**: rozpoznaje emocje na podstawie wskazówek głosowych i dźwiękowych. Mogą to być emocje takie jak radość, smutek, gniew czy strach.
- **Artefakty**: wyodrębnia rozbudowany zestaw artefaktów „następnego poziomu szczegółów” dla poszczególnych modeli.
- **Tłumaczenie**: tworzy tłumaczenia transkrypcji audio na 54 języki.

Gdy usługa Video Indexer zakończy przetwarzanie i analizę, można przeglądać, selekcjonować, przeszukiwać i publikować szczegółowe informacje o wideo.

Bez względu na to, czy jesteś menedżerem zawartości, czy deweloperem, usługa Video Indexer jest w stanie spełnić Twoje wymagania. Menedżerowie zawartości mogą za pomocą portalu internetowego usługi Video Indexer korzystać z niej bez konieczności pisania choćby wiersza kodu — zobacz [Rozpoczynanie korzystania z witryny internetowej usługi Video Indexer](video-indexer-get-started.md). Deweloperzy mogą korzystać z interfejsów API, aby przetwarzać zawartość na dużą skalę — zobacz [Korzystanie z interfejsu API REST usługi Video Indexer](video-indexer-use-apis.md). Usługa ta pozwala też klientom używać widgetów do publikowania strumieni wideo i wyodrębnionych szczegółowych informacji w ich własnych aplikacjach — zobacz [Osadzanie widgetów wizualnych we własnej aplikacji](video-indexer-embed-widgets.md).

Do korzystania z tej usługi możesz się zarejestrować za pomocą już posiadanego konta usługi AAD, LinkedIn, Facebook, Google lub MSA. Aby uzyskać więcej informacji, zobacz [wprowadzenie](video-indexer-get-started.md).

## <a name="scenarios"></a>Scenariusze

Poniżej przedstawiono kilka scenariuszy, w których usługa Video Indexer może być przydatna

- Wyszukiwanie — szczegółowe informacje wyodrębnione z wideo mogą służyć do rozbudowy funkcji wyszukiwania w bibliotece wideo. Na przykład indeksowanie wypowiedzianych słów i twarzy może umożliwić wyszukiwanie w wideo momentów, gdy konkretna osoba wypowiada określone słowa lub gdy dwie osoby są widoczne razem. Wyszukiwanie oparte na takich szczegółowych informacjach przyda się agencjom informacyjnym, instytucjom edukacyjnych, nadawcom, właścicielom zawartości rozrywkowej, w aplikacjach LOB dla przedsiębiorstw i ogólnie w każdej branży z biblioteką wideo, którą użytkownicy muszą przeszukiwać.

- Monetyzacja— usługa Video Indexer może zwiększyć wartość wideo. Przykładowo w branżach zarabiających na reklamach (takich jak media informacyjne czy społecznościowe) możliwe jest dostarczanie trafniejszych reklam przez przekazywanie wyodrębnionych szczegółowych informacji jako dodatkowych danych do serwera reklamowego (wyświetlenie reklamy butów sportowych bardziej pasuje do meczu futbolowego niż zawodów pływackich).

- Zaangażowanie użytkowników — szczegółowe informacje o wideo mogą służyć do zwiększenia stopnia zaangażowania użytkowników przez ustawianie dla nich wideo na odpowiednich momentach. Jako przykład weźmy wideo edukacyjne, w którym przez pierwsze 30 minut omawiane są sfery, a przez kolejne 30 minut — ostrosłupy. Uczeń czytający o ostrosłupach będzie bardziej zadowolony, jeśli wideo będzie ustawione na znaczniku 30. minuty.

Więcej informacji można znaleźć w tym [blogu](http://aka.ms/videoindexerblog).

## <a name="next-steps"></a>Następne kroki

Możesz już rozpocząć pracę z usługą Video Indexer. Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Rozpoczynanie korzystania z witryny internetowej usługi Video Indexer](video-indexer-get-started.md)
- [Przetwarzanie zawartości przy użyciu interfejsu REST API usługi Video Indexer](video-indexer-use-apis.md)
- [Osadzanie widgetów wizualnych w aplikacji](video-indexer-embed-widgets.md)

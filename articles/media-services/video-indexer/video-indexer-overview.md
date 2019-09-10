---
title: Co to jest Azure Media Services Video Indexer?
titleSuffix: Azure Media Services
description: Ten temat zawiera omówienie usługi Azure Media Services Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/06/2019
ms.author: juliako
ms.openlocfilehash: e3f60b5fb0693e40c9db040f7b14f487fce8f68e
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860255"
---
# <a name="what-is-video-indexer"></a>Co to jest Video Indexer?

Usługa Azure Media Services Video Indexer to aplikacja w chmurze utworzona za pomocą usług Azure Media Analytics, Azure Search oraz Cognitive Services (takich jak interfejs API rozpoznawania twarzy, Microsoft Translator, interfejs API przetwarzania obrazów i Custom Speech Service). Umożliwia ona wyodrębnienie szczegółowych informacji z filmów wideo przy użyciu Video Indexer modeli wideo i audio opisanych poniżej:
  
## <a name="video-insights"></a>Szczegółowe informacje wideo

- **Wykrywanie czołowe**: Wykrywa i grupuje twarze znajdujące się w filmie wideo.
- **Osobistości**: Video Indexer automatycznie identyfikuje ponad 1 000 000 osobistości — takie jak świecie liderzy, aktorzy, Actresses, sportowców, badaczy, biznesowe i techniczne liderów na całym świecie. Dane dotyczące tych osobistości można znaleźć również w różnych znanych witrynach internetowych, takich jak IMDB czy Wikipedia.
- **Identyfikacja kroju na podstawie konta**: Video Indexer pociąga za siebie model dla określonego konta. Następnie rozpoznaje twarze w wideo w oparciu o szkolony model. Aby uzyskać więcej informacji, zobacz [Dostosowywanie modelu osoby z witryny internetowej Video Indexer](customize-person-model-with-website.md) i [Dostosowywanie modelu osoby za pomocą interfejsu API Video Indexer](customize-person-model-with-api.md).
- **Wyodrębnianie miniatur dla** twarzy ("Najlepsza wartość"): Program automatycznie identyfikuje najlepiej przechwyconą twarz w każdej grupie twarzy (na podstawie jakości, rozmiaru i położenia czołowego) i wyodrębnia ją jako zasób obrazu.
- **Rozpoznawanie tekstu wizualnego** (OCR): Wyodrębnia tekst, który jest wizualnie wyświetlany w filmie wideo.
- **Moderowanie zawartości wizualnej**: Wykrywa wizualizacje dla dorosłych i/lub erotycznej.
- **Identyfikacja etykiet**: Identyfikuje wyświetlane obiekty i akcje wizualne.
- **Segmentacja sceny**: określa, kiedy scena zmienia się w wideo na podstawie podpowiedzi wizualizacji. Scena przedstawia pojedyncze zdarzenie i składa się z szeregu kolejnych zrzutów, które są semantycznie powiązane. 
- **Wykrywanie zrzutu**: określa, kiedy zrzut w wideo jest zmieniany na podstawie podpowiedzi wizualizacji. Zrzut to seria ramek pobieranych z tego samego aparatu obrazu ruchu. Aby uzyskać więcej informacji, zobacz [sceny, zrzuty i ramki kluczowe](scenes-shots-keyframes.md).
- **Wykrywanie czarnej ramki**: Identyfikuje czarne ramki prezentowane w klipie wideo.
- **Wyodrębnianie klatek kluczowych**: Wykrywa stabilne ramki kluczowe w filmie wideo.
- **Kredyty kroczące**: Zidentyfikuj początkową i końcową kredyty kroczące na końcu programów telewizyjnych i filmów.
- **Wykrywanie znaków animowanych** (wersja zapoznawcza): wykrywanie, grupowanie i rozpoznawanie znaków w animowanej zawartości poprzez integrację z [Cognitive Services wzrokiem niestandardowym](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Aby uzyskać więcej informacji, zobacz [wykrywanie znaków animowanych](animated-characters-recognition.md).
- **Wykrywanie typu zrzutu redakcyjnego**: wypełnianie zrzutów na podstawie ich typu (na przykład szerokiego zrzutu, średniej zrzutu, zamknięcia, ekstremalnego zamknięcia, dwóch zastrzelonych, wielu osób, na zewnątrz i wewnątrz, itp.). Aby uzyskać więcej informacji, zobacz [wykrywanie typów zrzutów redakcyjnych](scenes-shots-keyframes.md#editorial-shot-type-detection).

## <a name="audio-insights"></a>Szczegółowe informacje o usłudze audio

- **Automatyczne wykrywanie języka**: Automatycznie identyfikuje dominujący język. Obsługiwane języki to angielski, hiszpański, francuski, niemiecki, włoski, chiński (uproszczony), japoński, rosyjski i portugalski (Brazylia). Jeśli nie można zidentyfikować języka z pewnością, Video Indexer zakłada, że język mówiony jest w języku angielskim. Aby uzyskać więcej informacji, zobacz [Language Identification model](language-identification-model.md).
- **Identyfikacja i transkrypcja mowy w wielu językach** (wersja zapoznawcza): Automatycznie identyfikuje język mówiony w różnych segmentach z audio, wysyłając każdy segment pliku nośnika do uzyskanego i połączyć transkrypcję z powrotem do jednego ujednoliconego transkrypcji. Aby uzyskać więcej informacji, zobacz [Automatyczne identyfikowanie i transkrypcja zawartości w wielu językach](multi-language-identification-transcription.md).
- **Transkrypcja audio**: Konwertuje mowę na tekst w 12 językach i zezwala na rozszerzenia. Obsługiwane języki to angielski, hiszpański, francuski, niemiecki, włoski, chiński (uproszczony), japoński, arabski, rosyjski, portugalski (Brazylia), hindi i koreański.
- **Napisy kodowane**: Tworzy napisy w trzech formatach: VTT, TTML, NARZĘDZIA SRT.
- **Przetwarzanie dwóch kanałów**: Wykrywa automatyczne wykrywanie, oddzielne transkrypcje i scalanie do pojedynczej osi czasu.
- **Redukcja szumu**: Czyści nagrania audio lub dźwiękowe dotyczące rozmowy telefonicznej (na podstawie filtrów Skype).
- **Dostosowanie transkrypcji** (CRI): Pociąga za siebie niestandardowe mowy do modeli tekstowych, aby utworzyć transkrypcje specyficzne dla branż. Aby uzyskać więcej informacji, zobacz [Dostosowywanie modelu języka z witryny sieci web video Indexer](customize-language-model-with-website.md) i [Dostosowywanie modelu języka za pomocą interfejsów API Video Indexer](customize-language-model-with-api.md).
- **Wyliczenie głośników**: Mapuje i rozumie, które osoby mówiące i kiedy.
- **Statystyki głośników**: Zapewnia statystykę współczynnika mowy dla głośników.
- **Moderowanie zawartości tekstowej**: Wykrywa jawny tekst w transkrypcji audio.
- **Efekty audio**: Identyfikuje efekty audio, takie jak claps, Speech i wyciszenie.
- **Wykrywanie rozpoznawania emocji**: Identyfikuje emocji w oparciu o mowę (co jest określane) i tonów głosu (jak są używane).  Mogą to być emocje takie jak radość, smutek, gniew czy strach.
- **Tłumaczenie**: Tworzy tłumaczenia transkrypcji audio do 54 różnych języków.

## <a name="audio-and-video-insights-multi-channels"></a>Wgląd w dane audio i wideo (wiele kanałów)

Gdy indeksowanie według jednego kanału częściowy wynik dla tych modeli będzie dostępne

- **Wyodrębnianie słów kluczowych**: Wyodrębnia słowa kluczowe z mowy i tekstu wizualnego.
- **Wyodrębnianie nazwanych jednostek**: Wyodrębnianie marek, lokalizacji i osób z mowy i tekstu wizualnego za pośrednictwem przetwarzania języka naturalnego (NLP).
- **Wnioskowanie dotyczące tematu**: Wykonuje wnioskowanie głównych tematów z transkrypcji. Dołączona jest Taksonomia IPTC pierwszego poziomu.
- **Artefakty**: Wyodrębnia bogaty zestaw artefaktów "Next Level of details" dla każdego z modeli.
- **Analiza tonacji**: Identyfikuje pozytywne, ujemne i neutralne mową z mowy i tekstu wizualnego.
 
Gdy usługa Video Indexer zakończy przetwarzanie i analizę, można przeglądać, selekcjonować, przeszukiwać i publikować szczegółowe informacje o wideo.

Bez względu na to, czy jesteś menedżerem zawartości, czy deweloperem, usługa Video Indexer jest w stanie spełnić Twoje wymagania. Menedżerowie zawartości mogą za pomocą portalu internetowego usługi Video Indexer korzystać z niej bez konieczności pisania choćby wiersza kodu — zobacz [Rozpoczynanie korzystania z witryny internetowej usługi Video Indexer](video-indexer-get-started.md). Deweloperzy mogą korzystać z interfejsów API, aby przetwarzać zawartość na dużą skalę — zobacz [Korzystanie z interfejsu API REST usługi Video Indexer](video-indexer-use-apis.md). Usługa ta pozwala też klientom używać widgetów do publikowania strumieni wideo i wyodrębnionych szczegółowych informacji w ich własnych aplikacjach — zobacz [Osadzanie widgetów wizualnych we własnej aplikacji](video-indexer-embed-widgets.md).

Do korzystania z tej usługi możesz się zarejestrować za pomocą już posiadanego konta usługi AAD, LinkedIn, Facebook, Google lub MSA. Aby uzyskać więcej informacji, zobacz [wprowadzenie](video-indexer-get-started.md).

## <a name="scenarios"></a>Scenariusze

Poniżej przedstawiono kilka scenariuszy, w których usługa Video Indexer może być przydatna

- Wyszukiwanie — szczegółowe informacje wyodrębnione z wideo mogą służyć do rozbudowy funkcji wyszukiwania w bibliotece wideo. Na przykład indeksowanie wypowiedzianych słów i twarzy może umożliwić wyszukiwanie w wideo momentów, gdy konkretna osoba wypowiada określone słowa lub gdy dwie osoby są widoczne razem. Wyszukiwanie oparte na takich szczegółowych informacjach przyda się agencjom informacyjnym, instytucjom edukacyjnych, nadawcom, właścicielom zawartości rozrywkowej, w aplikacjach LOB dla przedsiębiorstw i ogólnie w każdej branży z biblioteką wideo, którą użytkownicy muszą przeszukiwać.
- Tworzenie zawartości — szczegółowe informacje wyodrębnione z filmów wideo i pomagające w efektywnym tworzeniu zawartości, takiej jak przyczepy, zawartość mediów społecznościowych, klipy informacyjne itp. z istniejącej zawartości w archiwum organizacji 
- Monetyzacja— usługa Video Indexer może zwiększyć wartość wideo. Przykładowo w branżach zarabiających na reklamach (takich jak media informacyjne czy społecznościowe) możliwe jest dostarczanie trafniejszych reklam przez przekazywanie wyodrębnionych szczegółowych informacji jako dodatkowych danych do serwera reklamowego (wyświetlenie reklamy butów sportowych bardziej pasuje do meczu futbolowego niż zawodów pływackich).
- Zaangażowanie użytkowników — szczegółowe informacje o wideo mogą służyć do zwiększenia stopnia zaangażowania użytkowników przez ustawianie dla nich wideo na odpowiednich momentach. Jako przykład weźmy wideo edukacyjne, w którym przez pierwsze 30 minut omawiane są sfery, a przez kolejne 30 minut — ostrosłupy. Uczeń czytający o ostrosłupach będzie bardziej zadowolony, jeśli wideo będzie ustawione na znaczniku 30. minuty.

## <a name="next-steps"></a>Następne kroki

Możesz już rozpocząć pracę z usługą Video Indexer. Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Rozpoczynanie korzystania z witryny internetowej usługi Video Indexer](video-indexer-get-started.md)
- [Przetwarzanie zawartości przy użyciu interfejsu REST API usługi Video Indexer](video-indexer-use-apis.md)
- [Osadzanie widgetów wizualnych w aplikacji](video-indexer-embed-widgets.md)

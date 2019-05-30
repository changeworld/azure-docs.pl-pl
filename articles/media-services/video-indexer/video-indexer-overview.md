---
title: Co to jest Video Indexer?
titlesuffix: Azure Media Services
description: Ten temat zawiera omówienie usługi Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 2c72c7c493c0a887adab147054c725a2e1c0659f
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65799134"
---
# <a name="what-is-video-indexer"></a>Co to jest Video Indexer?

Usługa Azure Video Indexer to aplikacja w chmurze utworzona za pomocą usługi Azure Media Analytics, usługi Azure Search oraz usług Cognitive Services (takich jak interfejs API rozpoznawania twarzy, Microsoft Translator, interfejs API przetwarzania obrazów i Custom Speech Service). Umożliwia prowadzenie analiz wideo przy użyciu modeli wideo i audio Video Indexer opisanych poniżej:
  
## <a name="video-insights"></a>Informacjom o filmie wideo

- **Wykrywanie twarzy**: Wykrywa i grupuje twarzy w filmie wideo.
- **Identyfikacja osobistości**: Indeksator wideo automatycznie rozpoznaje osobistości ponad 1 milion — liderów świata, aktorów, aktorki, sportowców, pracowników naukowo-badawczych, praca i liderzy techniczni na całym świecie. Dane dotyczące tych osobistości można znaleźć również w różnych znanych witrynach internetowych, takich jak IMDB czy Wikipedia.
- **Identyfikowanie twarzy oparte na koncie**: Usługa Video Indexer przygotowuje modelu dla określonego konta. Rozpoznaje twarzy w filmie wideo, na podstawie uczonego modelu. Aby uzyskać więcej informacji, zobacz [dostosowania modelu osoby w witrynie sieci Web Video Indexer](customize-person-model-with-website.md) i [dostosować model osoby przy użyciu interfejsu API indeksatora wideo](customize-person-model-with-api.md).
- **Wyodrębnianie miniatury dla twarzy** ("najlepsza twarzy"): Automatycznie identyfikuje najlepiej twarzy przechwyconych w każdej grupie twarze (w oparciu jakości, rozmiar i położenie czołowego) i wyodrębnij go jako zasób obrazu.
- **Rozpoznawanie tekstu Visual** (OCR): Wyodrębnianie tekstu wyświetlanego wizualnie w trakcie filmu wideo.
- **Moderowanie zawartości Visual**: Wykrywa przeznaczonej dla osób dorosłych i/lub erotycznej wizualizacji.
- **Identyfikator etykiety**: Identyfikuje obiektów wizualnych i wyświetlane czynności.
- **Segmentacja sceny**: Określa zmiany scenę w oparciu o podpowiedzi wizualne wideo. Scena przedstawia pojedyncze zdarzenie i składa się przez kolejne zrzuty, które są semantycznie ze sobą powiązane. 
- **Zrzut wykrywania**: Określa, kiedy zrzut zmienia się w oparciu o podpowiedzi wizualne wideo. Zrzut jest serią ramek wykonane z tego samego aparatu obraz ruchu. Aby uzyskać więcej informacji, zobacz [scen, zrzuty i klatki kluczowe](scenes-shots-keyframes.md).
- **Wykrywanie ramki czarnego**: Identyfikuje czarne ramek przedstawiony w wideo.
- **Ramka kluczowa wyodrębniania**: Wykrywa stabilne ramek kluczowych w filmie wideo.
- **Stopniowe środki na korzystanie z**: Identyfikowanie początek i koniec stopniowe środków na końcu programów telewizyjnych i filmów.

## <a name="audio-insights"></a>Audio insights

- **Automatyczne wykrywanie języka**: Automatycznie rozpoznaje dominujący język mówiony. Obsługiwane języki angielski, hiszpański, francuski, niemiecki, włoski, chiński (uproszczony), japoński, rosyjski i portugalskim portugalski będzie powrót do języka angielskiego, gdy nie można wykryć język.
- **Transkrypcję audio**: Konwertuje mowy na tekst w 12 języków i umożliwia rozszerzeń. Obsługiwane języki angielski, hiszpański, francuski, niemiecki, włoski, chiński (uproszczony), japoński, arabski, rosyjski, portugalski (Brazylia), Hindi i koreańskim.
- **Podpisy kodowane**: Tworzy kodowanych w trzech formatach: VTT, TTML, SRT.
- **Dwa kanału przetwarzania**: Automatycznie wykryje, oddziel transkrypcji i scalenia na pojedynczej osi czasu.
- **Redukcja szumów**: Czyści się nagrania audio lub hałas telefonii (oparte na filtry Skype).
- **Dostosowywanie transkrypcji** (CRI): Szkolenie modeli niestandardowa zamiana mowy na tekst modele, aby utworzyć zapisy branżowych. Aby uzyskać więcej informacji, zobacz [dostosować model języka w witrynie sieci Web Video Indexer](customize-language-model-with-website.md) i [dostosować model języka, korzystając z interfejsów API indeksatora wideo](customize-language-model-with-api.md).
- **Wyliczenie osoby mówiącej**: Mapuje i zrozumienie, które osoby mówiącej typu gwiazda które wyrazy i kiedy.
- **Statystyki osoby mówiącej**: Zawiera dane statystyczne prelegentów mowy proporcji.
- **Moderowanie zawartości tekstowej**: Wykrywa jawnego tekstu transkrypcji audio.
- **Efekty dźwiękowe**: Identyfikuje efekty dźwiękowe, takie jak Oklaski ręcznie, mowy i wyciszenia.
- **Wykrywanie emocji**: Rozpoznaje emocje, w oparciu o mowy (co to jest po uwzględnieniu) i tony głosowej, (jak jest ono).  Mogą to być emocje takie jak radość, smutek, gniew czy strach.
- **Tłumaczenie**: Tworzy tłumaczenia transkrypcjach audio do 54 różnych języków.

## <a name="audio-and-video-insights-multi-channels"></a>Insights audio i wideo (Obsługa wielu kanałów)

Kiedy indeksowania przez jeden kanał częściowy wynik dla tych modeli będzie dostępna

- **Wyodrębnianie słów kluczowych**: Wyodrębnianie słów kluczowych z mowy i tekstu.
- **Marki wyodrębniania**: Wyodrębnia marek z mowy i tekstu.
- **Wnioskowanie o temacie**: Sprawia, że wnioskowania główne tematy z transkrypcji. Dołączono taksonomii IPTC poziomu 1.
- **Artefakty**: Wyodrębnia bogaty zestaw "następny poziom szczegółów" artefaktów dla wszystkich modeli.
- **Analiza tonacji**: Identyfikuje dodatnie, ujemne i neutralne tonacji, rozpoznawania mowy i tekstu.
 
Gdy usługa Video Indexer zakończy przetwarzanie i analizę, można przeglądać, selekcjonować, przeszukiwać i publikować szczegółowe informacje o wideo.

Bez względu na to, czy jesteś menedżerem zawartości, czy deweloperem, usługa Video Indexer jest w stanie spełnić Twoje wymagania. Menedżerowie zawartości mogą za pomocą portalu internetowego usługi Video Indexer korzystać z niej bez konieczności pisania choćby wiersza kodu — zobacz [Rozpoczynanie korzystania z witryny internetowej usługi Video Indexer](video-indexer-get-started.md). Deweloperzy mogą korzystać z interfejsów API, aby przetwarzać zawartość na dużą skalę — zobacz [Korzystanie z interfejsu API REST usługi Video Indexer](video-indexer-use-apis.md). Usługa ta pozwala też klientom używać widgetów do publikowania strumieni wideo i wyodrębnionych szczegółowych informacji w ich własnych aplikacjach — zobacz [Osadzanie widgetów wizualnych we własnej aplikacji](video-indexer-embed-widgets.md).

Do korzystania z tej usługi możesz się zarejestrować za pomocą już posiadanego konta usługi AAD, LinkedIn, Facebook, Google lub MSA. Aby uzyskać więcej informacji, zobacz [wprowadzenie](video-indexer-get-started.md).

## <a name="scenarios"></a>Scenariusze

Poniżej przedstawiono kilka scenariuszy, w których usługa Video Indexer może być przydatna

- Wyszukiwanie — szczegółowe informacje wyodrębnione z wideo mogą służyć do rozbudowy funkcji wyszukiwania w bibliotece wideo. Na przykład indeksowanie wypowiedzianych słów i twarzy może umożliwić wyszukiwanie w wideo momentów, gdy konkretna osoba wypowiada określone słowa lub gdy dwie osoby są widoczne razem. Wyszukiwanie oparte na takich szczegółowych informacjach przyda się agencjom informacyjnym, instytucjom edukacyjnych, nadawcom, właścicielom zawartości rozrywkowej, w aplikacjach LOB dla przedsiębiorstw i ogólnie w każdej branży z biblioteką wideo, którą użytkownicy muszą przeszukiwać.
- Tworzenie zawartości — szczegółowe informacje wyodrębnione z filmów wideo i skutecznie tworzenia zawartości, takich jak przyczepy zawartości mediów społecznościowych, grup dyskusyjnych klipy wideo itd. z istniejącej zawartości w warstwie archiwum organizacji 
- Monetyzacja— usługa Video Indexer może zwiększyć wartość wideo. Przykładowo w branżach zarabiających na reklamach (takich jak media informacyjne czy społecznościowe) możliwe jest dostarczanie trafniejszych reklam przez przekazywanie wyodrębnionych szczegółowych informacji jako dodatkowych danych do serwera reklamowego (wyświetlenie reklamy butów sportowych bardziej pasuje do meczu futbolowego niż zawodów pływackich).
- Zaangażowanie użytkowników — szczegółowe informacje o wideo mogą służyć do zwiększenia stopnia zaangażowania użytkowników przez ustawianie dla nich wideo na odpowiednich momentach. Jako przykład weźmy wideo edukacyjne, w którym przez pierwsze 30 minut omawiane są sfery, a przez kolejne 30 minut — ostrosłupy. Uczeń czytający o ostrosłupach będzie bardziej zadowolony, jeśli wideo będzie ustawione na znaczniku 30. minuty.

## <a name="next-steps"></a>Kolejne kroki

Możesz już rozpocząć pracę z usługą Video Indexer. Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Rozpoczynanie korzystania z witryny internetowej usługi Video Indexer](video-indexer-get-started.md)
- [Przetwarzanie zawartości przy użyciu interfejsu REST API usługi Video Indexer](video-indexer-use-apis.md)
- [Osadzanie widgetów wizualnych w aplikacji](video-indexer-embed-widgets.md)

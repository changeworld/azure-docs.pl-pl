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
ms.date: 09/17/2019
ms.author: juliako
ms.openlocfilehash: d346f68534a9fdbc286cd5521c00d81c2a5fec78
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203547"
---
# <a name="what-is-video-indexer"></a>Co to jest Video Indexer?

Azure Media Services Video Indexer to rozwiązanie AI utworzone na platformie Azure Cognitive Services. Video Indexer zapewnia możliwość wyodrębnienia szczegółowego wglądu (bez konieczności analizowania danych lub umiejętności kodowania) przy użyciu modeli uczenia maszynowego na podstawie wielu kanałów (Voice, vocals, Wizualizacja). Modele można dostosowywać i wyszkolić. Usługa umożliwia głębokie wyszukiwanie, zmniejsza koszty operacyjne, udostępnia nowe możliwości zysków, nowe środowisko użytkownika na dużą liczbę archiwów wideo (z niskimi ograniczeniami wejścia). 

Aby rozpocząć wyodrębnianie szczegółowych informacji z Video Indexer, musisz utworzyć konto i przekazać wideo. Po przekazaniu wideo do Video Indexer, analizuje wizualizacje i dźwięk, uruchamiając różne modele AI. Ponieważ Video Indexer analizuje wideo, szczegółowe informacje, które są wyodrębniane przez modele.

![Diagram przepływu](./media/video-indexer-overview/model-chart.png)

## <a name="what-can-i-do-with-video-indexer"></a>Co mogę zrobić z Video Indexer?

Szczegółowe informacje o Video Indexerach można zastosować do wielu scenariuszy, między innymi:

* *Głębokie wyszukiwanie* — użyj szczegółowych informacji uzyskanych z filmu wideo, aby zwiększyć możliwości wyszukiwania w bibliotece wideo. Na przykład indeksowanie słów i twarzy może umożliwić wyszukiwanie w miejscu wideo, w którym osoba połączyła się z pewnymi słowami lub kiedy dwie osoby były widoczne wspólnie. Wyszukiwanie oparte na takich szczegółowych informacjach przyda się agencjom informacyjnym, instytucjom edukacyjnych, nadawcom, właścicielom zawartości rozrywkowej, w aplikacjach LOB dla przedsiębiorstw i ogólnie w każdej branży z biblioteką wideo, którą użytkownicy muszą przeszukiwać.
* *Tworzenie zawartości*: Tworzenie przyczep, wyróżnianie elementów, zawartości mediów społecznościowych lub klipów informacyjnych na podstawie wyciągów Video Indexer szczegółowych informacji z zawartości. Klatki kluczowe, znaczniki scen i sygnatury czasowe dla osób i etykiet, sprawiają, że proces tworzenia jest znacznie bardziej gładszy i pozwala uzyskać dostęp do interesujących części filmów wideo potrzebnych do utworzenia zawartości.
* *Ułatwienia dostępu*: Czy chcesz udostępnić zawartość osobom niepełnosprawnym lub jeśli chcesz, aby zawartość była dystrybuowana do różnych regionów przy użyciu różnych języków, możesz użyć transkrypcji i tłumaczenia dostarczonego przez wideo. Indeksator w wielu językach.
* *Zysków*: Video Indexer może pomóc zwiększyć wartość wideo. Na przykład branże korzystające z przychodów usługi AD (na przykład nośniki wiadomości, Media społecznościowe itp.) mogą dostarczać odpowiednie reklamy przy użyciu wyodrębnionych informacji jako dodatkowych sygnałów do serwera usługi AD.
* *Moderowanie zawartości*: Użyj modeli moderowania treści tekstowych i wizualnych, aby zapewnić użytkownikom bezpieczeństwo przed niestosowną zawartością i sprawdzać, czy zawartość publikowana odpowiada wartościom organizacji. Możesz automatycznie blokować niektóre wideo lub otrzymywać alerty dla użytkowników w odniesieniu do zawartości. 
* *Zalecenia*: Szczegółowe informacje na temat wideo można wykorzystać w celu usprawnienia zaangażowania użytkowników, wyróżnianie odpowiednich filmów wideo użytkownikom. Oznaczając każdy klip wideo z dodatkowymi metadanymi, można polecać użytkownikom najbardziej odpowiednie wideo i wyróżnić część filmu wideo, która będzie odpowiadała potrzebom. 

## <a name="features"></a>Funkcje

Poniżej znajduje się lista szczegółowych informacji, które można pobrać z filmów wideo przy użyciu Video Indexer modeli wideo i audio:

### <a name="video-insights"></a>Szczegółowe informacje wideo

* **Wykrywanie czołowe**: Wykrywa i grupuje twarze znajdujące się w filmie wideo.
* **Osobistości**: Video Indexer automatycznie identyfikuje ponad 1 000 000 osobistości — takie jak świecie liderzy, aktorzy, Actresses, sportowców, badaczy, biznesowe i techniczne liderów na całym świecie. Dane dotyczące tych osobistości można znaleźć również w różnych znanych witrynach internetowych, takich jak IMDB czy Wikipedia.
* **Identyfikacja kroju na podstawie konta**: Video Indexer pociąga za siebie model dla określonego konta. Następnie rozpoznaje twarze w wideo w oparciu o szkolony model. Aby uzyskać więcej informacji, zobacz [Dostosowywanie modelu osoby z witryny internetowej Video Indexer](customize-person-model-with-website.md) i [Dostosowywanie modelu osoby za pomocą interfejsu API Video Indexer](customize-person-model-with-api.md).
* **Wyodrębnianie miniatur dla** twarzy ("Najlepsza wartość"): Program automatycznie identyfikuje najlepiej przechwyconą twarz w każdej grupie twarzy (na podstawie jakości, rozmiaru i położenia czołowego) i wyodrębnia ją jako zasób obrazu.
* **Rozpoznawanie tekstu wizualnego** (OCR): Wyodrębnia tekst, który jest wizualnie wyświetlany w filmie wideo.
* **Moderowanie zawartości wizualnej**: Wykrywa wizualizacje dla dorosłych i/lub erotycznej.
* **Identyfikacja etykiet**: Identyfikuje wyświetlane obiekty i akcje wizualne.
* **Segmentacja sceny**: określa, kiedy scena zmienia się w wideo na podstawie podpowiedzi wizualizacji. Scena przedstawia pojedyncze zdarzenie i składa się z szeregu kolejnych zrzutów, które są semantycznie powiązane.
* **Wykrywanie zrzutu**: określa, kiedy zrzut w wideo jest zmieniany na podstawie podpowiedzi wizualizacji. Zrzut to seria ramek pobieranych z tego samego aparatu obrazu ruchu. Aby uzyskać więcej informacji, zobacz sceny, zrzuty i ramki kluczowe.
* **Wykrywanie czarnej ramki**: Identyfikuje czarne ramki prezentowane w klipie wideo.
* **Wyodrębnianie klatek kluczowych**: Wykrywa stabilne ramki kluczowe w filmie wideo.
* **Kredyty kroczące**: Zidentyfikuj początkową i końcową kredyty kroczące na końcu programów telewizyjnych i filmów.
* **Wykrywanie znaków animowanych** (wersja zapoznawcza): wykrywanie, grupowanie i rozpoznawanie znaków w animowanej zawartości poprzez integrację z [Cognitive Services wzrokiem niestandardowym](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Aby uzyskać więcej informacji, zobacz [wykrywanie znaków animowanych](animated-characters-recognition.md).
* **Wykrywanie typu zrzutu redakcyjnego**: wypełnianie zrzutów na podstawie ich typu (na przykład szerokiego zrzutu, średniej zrzutu, zamknięcia, ekstremalnego zamknięcia, dwóch zastrzelonych, wielu osób, na zewnątrz i wewnątrz, itp.). Aby uzyskać więcej informacji, zobacz [wykrywanie typów zrzutów redakcyjnych](scenes-shots-keyframes.md#editorial-shot-type-detection).

### <a name="audio-insights"></a>Szczegółowe informacje o usłudze audio

* **Automatyczne wykrywanie języka**: Automatycznie identyfikuje dominujący język. Obsługiwane języki to angielski, hiszpański, francuski, niemiecki, włoski, chiński (uproszczony), japoński, rosyjski i portugalski (Brazylia). Jeśli nie można zidentyfikować języka z pewnością, Video Indexer zakłada, że język mówiony jest w języku angielskim. Aby uzyskać więcej informacji, zobacz [Language Identification model](language-identification-model.md).
* **Identyfikacja i transkrypcja mowy w wielu językach** (wersja zapoznawcza): Automatycznie identyfikuje język mówiony w różnych segmentach z audio, wysyłając każdy segment pliku nośnika do uzyskanego i połączyć transkrypcję z powrotem do jednego ujednoliconego transkrypcji. Aby uzyskać więcej informacji, zobacz [Automatyczne identyfikowanie i transkrypcja zawartości w wielu językach](multi-language-identification-transcription.md).
* **Transkrypcja audio**: Konwertuje mowę na tekst w 12 językach i zezwala na rozszerzenia. Obsługiwane języki to angielski, hiszpański, francuski, niemiecki, włoski, chiński (uproszczony), japoński, arabski, rosyjski, portugalski (Brazylia), hindi i koreański.
* **Napisy kodowane**: Tworzy napisy w trzech formatach: VTT, TTML, NARZĘDZIA SRT.
* **Przetwarzanie dwóch kanałów**: Wykrywa automatyczne wykrywanie, oddzielne transkrypcje i scalanie do pojedynczej osi czasu.
* **Redukcja szumu**: Czyści nagrania audio lub dźwiękowe dotyczące rozmowy telefonicznej (na podstawie filtrów Skype).
* **Dostosowanie transkrypcji** (CRI): Pociąga za siebie niestandardowe mowy do modeli tekstowych, aby utworzyć transkrypcje specyficzne dla branż. Aby uzyskać więcej informacji, zobacz [Dostosowywanie modelu języka z witryny sieci web video Indexer](customize-language-model-with-website.md) i [Dostosowywanie modelu języka za pomocą interfejsów API Video Indexer](customize-language-model-with-api.md).
* **Wyliczenie głośników**: Mapuje i rozumie, które osoby mówiące i kiedy.
* **Statystyki głośników**: Zapewnia statystykę współczynniki mowy dla głośników.
* **Moderowanie zawartości tekstowej**: Wykrywa jawny tekst w transkrypcji audio.
* **Efekty audio**: Identyfikuje efekty audio, takie jak claps, Speech i wyciszenie.
* **Wykrywanie rozpoznawania emocji**: Identyfikuje emocji w oparciu o mowę (co jest określane) i tonów głosu (jak są używane). Rozpoznawania emocji może być radością, smutek, gniew lub obawą.
* **Tłumaczenie**: Tworzy tłumaczenia transkrypcji audio do 54 różnych języków.

### <a name="audio-and-video-insights-multi-channels"></a>Wgląd w dane audio i wideo (wiele kanałów)

Gdy indeksowanie według jednego kanału częściowy wynik dla tych modeli będzie dostępne

* **Wyodrębnianie słów kluczowych**: Wyodrębnia słowa kluczowe z mowy i tekstu wizualnego.
* **Wyodrębnianie nazwanych jednostek**: Wyodrębnianie marek, lokalizacji i osób z mowy i tekstu wizualnego za pośrednictwem przetwarzania języka naturalnego (NLP).
* **Wnioskowanie dotyczące tematu**: Wykonuje wnioskowanie głównych tematów z transkrypcji. Dołączona jest Taksonomia IPTC pierwszego poziomu.
* **Artefakty**: Wyodrębnia bogaty zestaw artefaktów "Next Level of details" dla każdego z modeli.
* **Analiza tonacji**: Identyfikuje pozytywne, ujemne i neutralne mową z mowy i tekstu wizualnego.

## <a name="how-can-i-get-started-with-video-indexer"></a>Jak rozpocząć pracę z Video Indexer?

Dostęp do Video Indexer możliwości można uzyskać, korzystając z trzech różnych sposobów: 

* Portal Video Indexer — łatwe w użyciu rozwiązanie, które umożliwia ocenę produktu, zarządzanie kontem i dostosowywanie modeli. 

    Aby uzyskać więcej informacji na temat portalu, zobacz [Rozpoczynanie pracy z witryną Video Indexer](video-indexer-get-started.md).  
* Integracja z interfejsem API — wszystkie możliwości Video Indexer są dostępne za pomocą interfejsu API REST, aby umożliwić integrację rozwiązania z aplikacjami i infrastrukturą. 

    Aby rozpocząć pracę jako deweloper, zobacz [Korzystanie z interfejsu API REST Video Indexer](video-indexer-use-apis.md). 
* Widżet Emendable — umożliwia osadzenie środowiska wideo indeksatora, odtwarzacza i edytora w aplikacji. 

    Aby uzyskać więcej informacji, zobacz [osadzanie wizualizacji wizualnych w aplikacji](video-indexer-embed-widgets.md). 

Jeśli używasz witryny sieci Web, szczegółowe informacje są dodawane jako metadane i widoczne w portalu. Jeśli używasz interfejsów API, szczegółowe informacje są dostępne jako plik JSON. 

## <a name="next-steps"></a>Następne kroki

Możesz już rozpocząć pracę z usługą Video Indexer. Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Rozpoczynanie korzystania z witryny internetowej usługi Video Indexer](video-indexer-get-started.md)
- [Przetwarzanie zawartości przy użyciu interfejsu REST API usługi Video Indexer](video-indexer-use-apis.md)
- [Osadzanie widgetów wizualnych w aplikacji](video-indexer-embed-widgets.md)

---
title: Co to jest Video Indexer?
titleSuffix: Azure Media Services
description: Ten artykuł zawiera omówienie usługi Video Indexer Azure Media Services.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/23/2019
ms.author: juliako
ms.openlocfilehash: 6d03e66d125c4234a95e5a79f212bc81e7cea560
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76933395"
---
# <a name="what-is-video-indexer"></a>Co to jest Video Indexer?

Video Indexer (VI) to rozwiązanie Azure Media Services AI i część marki Cognitive Services platformy Azure. Video Indexer zapewnia możliwość wyodrębnienia szczegółowego wglądu (bez konieczności analizowania danych lub umiejętności kodowania) przy użyciu modeli uczenia maszynowego na podstawie wielu kanałów (Voice, vocals, Wizualizacja). Modele można dostosowywać i wyszkolić. Usługa umożliwia głębokie wyszukiwanie, zmniejsza koszty operacyjne, udostępnia nowe możliwości zysków i tworzy nowe środowisko użytkownika w dużych archiwach filmów wideo (z niskimi ograniczeniami wejścia).

Aby rozpocząć wyodrębnianie szczegółowych informacji z Video Indexer, musisz utworzyć konto i przekazać wideo. Po przekazaniu wideo do Video Indexer, analizuje wizualizacje i dźwięk, uruchamiając różne modele AI. Ponieważ Video Indexer analizuje wideo, szczegółowe informacje, które są wyodrębniane przez modele AI.

Na poniższym diagramie przedstawiono ilustrację, a nie objaśnienie techniczne sposobu działania Video Indexer w zapleczu.

![Diagram przepływu Video Indexer Azure Media Services](./media/video-indexer-overview/model-chart.png)

## <a name="compliance-privacy-and-security"></a>Zgodność, prywatność i zabezpieczenia
 
Ważną kwestią jest przestrzeganie wszystkich obowiązujących przepisów dotyczących używania Video Indexer i nie można używać Video Indexer lub żadnej innej usługi platformy Azure w sposób naruszający prawa innych lub mogą być szkodliwe dla innych osób. Przed przekazaniem jakichkolwiek filmów wideo, w tym wszelkich danych biometrycznych, do usługi Video Indexer na potrzeby przetwarzania i przechowywania, należy dysponować wszystkimi właściwymi prawami, w tym wszystkimi odpowiednimi komunikatami o zgodzie, z poszczególnych osób w filmie wideo. Aby dowiedzieć się o zgodności, ochronie prywatności i bezpieczeństwie w Video Indexer, [warunki Cognitive Services](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)firmy Microsoft. W celu zachowania poufności informacji firmy Microsoft i obsługi danych należy zapoznać się z zasadami [zachowania poufności informacji](https://privacy.microsoft.com/PrivacyStatement)firmy Microsoft, tematami dotyczącymi [usług online ("Ost")](https://www.microsoft.com/licensing/product-licensing/products) i [uzupełnieniem przetwarzania danych](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Dodatkowe informacje o ochronie prywatności, takie jak przechowywanie danych, usuwanie/niszczenie, są dostępne w pliku OST i [tutaj](faq.md). Korzystając z Video Indexer, wyrażasz zgodę na związanie Cognitive Services warunkami, OST, DPA i zasad zachowania poufności informacji.

## <a name="what-can-i-do-with-video-indexer"></a>Co mogę zrobić z Video Indexer?

Szczegółowe informacje o Video Indexerach można zastosować do wielu scenariuszy, między innymi:

* *Głębokie wyszukiwanie*: Korzystaj z szczegółowych informacji uzyskanych z filmu wideo, aby ulepszyć środowisko wyszukiwania w bibliotece wideo. Na przykład indeksowanie słów i twarzy może umożliwić wyszukiwanie w miejscu wideo, w którym osoba połączyła się z pewnymi słowami lub kiedy dwie osoby były widoczne wspólnie. Wyszukiwanie w oparciu o te szczegółowe informacje z filmów wideo dotyczy agencji Aktualności, instytutów edukacyjnych, nadawców, właścicieli zawartości rozrywkowej, aplikacji biznesowych przedsiębiorstwa, a także ogólnie dla każdej branży, która ma bibliotekę wideo, której użytkownicy muszą wyszukiwać.
* *Tworzenie zawartości*: Tworzenie przyczep, wyróżnianie elementów, zawartości mediów społecznościowych lub klipów informacyjnych na podstawie wyciągów Video Indexer szczegółowych informacji z zawartości. Klatki kluczowe, znaczniki scen i sygnatury czasowe dla osób i etykiet mogą sprawiać, że proces tworzenia jest znacznie bardziej gładszy i łatwiejszy w obsłużyć do części wideo, które są potrzebne do tworzenia zawartości.
* *Ułatwienia dostępu*: Czy chcesz udostępnić zawartość osobom niepełnosprawnym lub jeśli chcesz, aby zawartość była dystrybuowana do różnych regionów przy użyciu różnych języków, możesz użyć transkrypcji i tłumaczenia udostępnianego przez indeksator wideo w wielu językach.
* *Zysków*: Video Indexer może pomóc zwiększyć wartość wideo. Na przykład branże korzystające z przychodów usługi AD (Media News, Media społecznościowe itp.) mogą dostarczać odpowiednie reklamy przy użyciu wyodrębnionych informacji jako dodatkowych sygnałów do serwera usługi AD.
* *Moderowanie zawartości*: Użyj modeli moderowania treści tekstowych i wizualnych, aby zapewnić użytkownikom bezpieczeństwo przed niestosowną zawartością i sprawdzać, czy zawartość publikowana odpowiada wartościom organizacji. Możesz automatycznie blokować niektóre wideo lub otrzymywać alerty dla użytkowników o zawartości.
* *Zalecenia*: szczegółowe informacje można wykorzystać w celu usprawnienia zaangażowania użytkowników, wyróżnianie odpowiednich filmów wideo użytkownikom. Oznaczając każdy klip wideo z dodatkowymi metadanymi, możesz zalecić użytkownikom najbardziej odpowiednie wideo i wyróżnić części filmu wideo, które będą zgodne z ich potrzebami.

## <a name="features"></a>Funkcje

Na poniższej liście przedstawiono szczegółowe informacje, które można pobrać z filmów wideo przy użyciu Video Indexer modele wideo i audio:

### <a name="video-insights"></a>Szczegółowe informacje wideo

* **Wykrywanie twarzy**: wykrywa i grupuje twarze pojawiające się w nagraniu wideo.
* **Osobistości Identification**: Video Indexer automatycznie określa ponad 1 000 000 osobistości, takich jak światowy liderzy, aktorzy, Actresses, sportowców, badacze, biznesowe i techniczne liderzy na całym świecie. Dane dotyczące tych osobistości można także znaleźć w różnych witrynach sieci Web (IMDB, Wikipedia i tak dalej).
* **Identyfikowanie twarzy na podstawie konta**: usługa Video Indexer szkoli model na podstawie określonego konta. Następnie rozpoznaje twarze w wideo w oparciu o szkolony model. Aby uzyskać więcej informacji, zobacz [Dostosowywanie modelu osoby z witryny internetowej Video Indexer](customize-person-model-with-website.md) i [Dostosowywanie modelu osoby za pomocą interfejsu API Video Indexer](customize-person-model-with-api.md).
* **Wyodrębnianie miniatur dla** twarzy ("Najlepsza twarz"): program automatycznie identyfikuje najlepiej przechwyconą twarz w każdej grupie twarzy (na podstawie jakości, rozmiaru i położenia czołowego) i wyodrębnia ją jako zasób obrazu.
* **Rozpoznawanie tekstu wizualnego** (OCR): wyodrębnia tekst, który jest wizualnie wyświetlany w filmie wideo.
* **Moderowanie zawartości wizualnej**: wykrywa widoczną zawartość dla dorosłych i/lub erotyczną.
* **Identyfikowanie etykiet**: identyfikuje widoczne obiekty i czynności.
* **Segmentacja sceny**: określa, kiedy scena zmienia się w wideo na podstawie podpowiedzi wizualizacji. Scena przedstawia pojedyncze zdarzenie i składa się z szeregu kolejnych zrzutów, które są semantycznie powiązane.
* **Wykrywanie zrzutu**: określa, kiedy zrzut w wideo jest zmieniany na podstawie podpowiedzi wizualizacji. Zrzut to seria ramek pobieranych z tego samego aparatu obrazu ruchu. Aby uzyskać więcej informacji, zobacz [sceny, zrzuty i ramki kluczowe](scenes-shots-keyframes.md).
* **Wykrywanie czarnych ramek**: identyfikuje pojawiające się w nagraniu wideo czarne ramki.
* **Wyodrębnianie ramek kluczowych**: wykrywa ramki kluczowe w nagraniu wideo.
* **Kredyty kroczące**: określa początek i koniec okresów kroczących na końcu programów telewizyjnych i filmów.
* **Wykrywanie znaków animowanych** (wersja zapoznawcza): wykrywanie, grupowanie i rozpoznawanie znaków w animowanej zawartości poprzez integrację z [Cognitive Services wzrokiem niestandardowym](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Aby uzyskać więcej informacji, zobacz [wykrywanie znaków animowanych](animated-characters-recognition.md).
* **Wykrywanie typu zrzutu redakcyjnego**: wypełnianie zrzutów na podstawie ich typu (na przykład szerokiego zrzutu, średniego zrzutu, zamknięcia, ekstremalnego zamknięcia, dwóch zastrzelonych, wielu osób, na zewnątrz i do wewnątrz itd.). Aby uzyskać więcej informacji, zobacz [wykrywanie typów zrzutów redakcyjnych](scenes-shots-keyframes.md#editorial-shot-type-detection).

### <a name="audio-insights"></a>Szczegółowe informacje o usłudze audio

* **Automatyczne wykrywanie języka**: automatycznie rozpoznaje dominujący język mówiony. Obsługiwane języki to angielski, hiszpański, francuski, niemiecki, włoski, chiński (uproszczony), japoński, rosyjski i portugalski (Brazylia). Jeśli nie można zidentyfikować języka z pewnością, Video Indexer zakłada, że język mówiony jest w języku angielskim. Aby uzyskać więcej informacji, zobacz [Language Identification model](language-identification-model.md).
* **Identyfikacja i transkrypcja mowy w wielu językach** (wersja zapoznawcza): automatycznie identyfikuje język mówiony w różnych segmentach z dźwiękiem. Wysyła każdy segment pliku nośnika jako uzyskanego, a następnie łączy transkrypcję z powrotem do jednego ujednoliconego transkrypcji. Aby uzyskać więcej informacji, zobacz [Automatyczne identyfikowanie i transkrypcja zawartości w wielu językach](multi-language-identification-transcription.md).
* **Transkrypcja audio**: konwertuje mowę na tekst w 12 językach i zezwala na rozszerzenia. Obsługiwane języki to angielski, hiszpański, francuski, niemiecki, włoski, chiński (uproszczony), japoński, arabski, rosyjski, portugalski (Brazylia), hindi i koreański.
* **Napisy (transkrypcja)** : tworzy napisy w trzech formatach: VTT, TTML i SRT.
* **Przetwarzanie dwóch kanałów**: automatycznie wykrywa odrębne transkrypcje i scala do pojedynczej osi czasu.
* **Redukcja szumu**: czyści nagranie audio lub szumów z dźwiękiem (na podstawie filtrów Skype).
* **Dostosowanie transkrypcji** (CRI): pociąga za siebie niestandardowe mowy do modeli tekstowych, aby utworzyć transkrypcje specyficzne dla branży. Aby uzyskać więcej informacji, zobacz [Dostosowywanie modelu języka z witryny sieci web video Indexer](customize-language-model-with-website.md) i [Dostosowywanie modelu języka za pomocą interfejsów API Video Indexer](customize-language-model-with-api.md).
* **Wyliczenie głośników**: mapuje i rozumie, które osoby mówiące i kiedy.
* **Statystyka prelegenta**: zawiera statystykę współczynniki mowy dla głośników.
* **Moderowanie zawartości tekstowej**: wykrywa jawny tekst w transkrypcji audio.
* **Efekty audio**: identyfikuje efekty audio, takie jak claps, Speech i wyciszenie.
* **Wykrywanie rozpoznawania emocji**: identyfikuje emocji w oparciu o mowę (co jest określane) i tonów głosu (jak są używane). Rozpoznawania emocji może być radością, smutek, gniew lub obawą.
* **Tłumaczenie**: tworzy tłumaczenia transkrypcji audio na 54 języki.

### <a name="audio-and-video-insights-multi-channels"></a>Wgląd w dane audio i wideo (wiele kanałów)

Podczas indeksowania według jednego kanału będzie dostępny częściowy wynik dla tych modeli.

* **Wyodrębnianie słów kluczowych**: wyodrębnia słowa kluczowe z mowy i tekstu wizualnego.
* **Wyodrębnianie nazwanych jednostek**: wyodrębnia marki, lokalizacje i ludzie z mowy i tekstu wizualnego za pośrednictwem przetwarzania języka naturalnego (NLP).
* **Ustalanie tematów**: ustala główne tematy na podstawie transkrypcji. Dołączona jest Taksonomia IPTC 2.
* **Artefakty**: wyodrębnia rozbudowany zestaw artefaktów „następnego poziomu szczegółów” dla poszczególnych modeli.
* **Analiza tonacji**: rozpoznaje pozytywne, negatywne i neutralne opinie w padających wypowiedziach i tekście widocznym w nagraniu wideo.

## <a name="how-can-i-get-started-with-video-indexer"></a>Jak rozpocząć pracę z Video Indexer?

Dostęp do Video Indexer możliwości można uzyskać na trzy sposoby:

* Portal Video Indexer: łatwe w użyciu rozwiązanie, które umożliwia ocenę produktu, zarządzanie kontem i dostosowywanie modeli.

    Aby uzyskać więcej informacji na temat portalu, zobacz [Rozpoczynanie pracy z witryną Video Indexer](video-indexer-get-started.md).  

* Integracja z interfejsem API: wszystkie funkcje Video Indexer są dostępne za pomocą interfejsu API REST, który umożliwia integrację rozwiązania z aplikacjami i infrastrukturą.

    Aby rozpocząć pracę jako deweloper, zobacz [Korzystanie z interfejsu API REST Video Indexer](video-indexer-use-apis.md).

* Element widget z możliwością osadzenia: umożliwia osadzenie Video Indexer szczegółowych informacji, odtwarzacza i środowiska edytora w aplikacji.

    Aby uzyskać więcej informacji, zobacz [osadzanie wizualizacji wizualnych w aplikacji](video-indexer-embed-widgets.md).

Jeśli używasz witryny sieci Web, szczegółowe informacje są dodawane jako metadane i są widoczne w portalu. Jeśli używasz interfejsów API, szczegółowe informacje są dostępne jako plik JSON.

## <a name="next-steps"></a>Następne kroki

Możesz już rozpocząć pracę z usługą Video Indexer. Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Rozpocznij pracę z witryną sieci web video Indexer](video-indexer-get-started.md).
- [Przetwarzaj zawartość za pomocą interfejsu API REST Video Indexer](video-indexer-use-apis.md).
- [Osadź wizualne elementy widget w aplikacji](video-indexer-embed-widgets.md).

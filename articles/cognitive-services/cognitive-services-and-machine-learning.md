---
title: Usługi kognitywne i uczenie maszynowe
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak usługi Azure Cognitive Services wygląda w porównaniu z innymi ofertami platformy Azure w zakresie uczenia maszynowego.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: diberry
ms.openlocfilehash: cde505e4c95de9b9693a0e9d260d7fa84f3e905b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75531483"
---
# <a name="cognitive-services-and-machine-learning"></a>Usługi Cognitive Services i uczenie maszynowe

Usługi Cognitive Services zapewniają możliwości uczenia maszynowego w celu rozwiązywania ogólnych problemów, takich jak analizowanie tekstu pod kątem tonacji emocjonalnej lub analizowanie obrazów w celu rozpoznawania obiektów lub twarzy. Do korzystania z tych usług nie jest potrzebna specjalna wiedza na temat uczenia maszynowego ani nauki o danych. 

[Usługi Cognitive Services](welcome.md) to grupa usług, z których każda obsługuje różne, uogólnione możliwości przewidywania. Usługi są podzielone na różne kategorie, aby pomóc Ci znaleźć właściwą usługę. 

|Kategoria usługi|Przeznaczenie|
|--|--|
|[Decyzja](https://azure.microsoft.com/services/cognitive-services/directory/decision/)|Twórz aplikacje wyświetlające rekomendacje pozwalające skuteczniej podejmować lepsze decyzje.|
|[Język](https://azure.microsoft.com/services/cognitive-services/directory/lang/)|Umożliwiaj aplikacjom przetwarzanie języka naturalnego za pomocą wbudowanych skryptów, ocenianie tonacji i uczenie się rozpoznawania potrzeb użytkowników.|
|[Wyszukaj](https://azure.microsoft.com/services/cognitive-services/directory/search/)|Dodawaj interfejsy API wyszukiwania Bing do aplikacji i wykorzystuj możliwości przeczesywania miliardów stron internetowych, obrazów, wideo i wiadomości za pomocą jednego wywołania interfejsu API.|
|[Mowa](https://azure.microsoft.com/services/cognitive-services/directory/speech/)|Konwertuj mowę na tekst i tekst na naturalnie brzmiącą mowę. Tłumacz jeden język na inny oraz włącz weryfikację i rozpoznawanie osoby mówiącej.|
|[Obraz](https://azure.microsoft.com/services/cognitive-services/directory/vision/)|Rozpoznawaj, identyfikuj, podpisuj, indeksuj i moderuj swoje zdjęcia, klipy wideo i zawartość elektronicznego pisma odręcznego.|
||||

Korzystanie z usług Cognitive Services podczas:

* Można użyć uogólnionego rozwiązania.
* Dostęp do rozwiązania z interfejsu API REST programowania lub SDK. 

Użyj innego rozwiązania do uczenia maszynowego, gdy:

* Trzeba wybrać algorytm i trzeba trenować na bardzo konkretnych danych.

## <a name="what-is-machine-learning"></a>Co to jest uczenie maszynowe?

Uczenie maszynowe to koncepcja, w której można połączyć dane i algorytm, aby rozwiązać określoną potrzebę. Po przeszkoleniu danych i algorytmu dane wyjściowe jest modelem, którego można użyć ponownie z różnymi danymi. Przeszkolony model zapewnia szczegółowe informacje na podstawie nowych danych. 

Proces tworzenia systemu uczenia maszynowego wymaga pewnej wiedzy na temat uczenia maszynowego lub nauki o danych.

Uczenie maszynowe jest dostarczane przy użyciu [produktów i usług usługi Azure Machine Learning (AML).](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning?context=azure/machine-learning/studio/context/ml-context)

## <a name="what-is-a-cognitive-service"></a>Co to jest usługa poznawcza?

Usługa Cognitive Service udostępnia część lub wszystkie składniki w rozwiązaniu uczenia maszynowego: dane, algorytm i przeszkolony model. Te usługi są przeznaczone do wymagają ogólnej wiedzy na temat danych bez konieczności korzystania z uczenia maszynowego lub nauki o danych. Usługi te zapewniają zarówno interfejsy API REST, jak i sdk oparte na języku. W rezultacie, aby korzystać z usług, musisz mieć znajomość języka programowania.

## <a name="how-are-cognitive-services-and-azure-machine-learning-aml-similar"></a>W jaki sposób usługi Cognitive Services i azure machine learning (AML) są podobne?

Oba mają cel końcowy stosowania sztucznej inteligencji (AI) w celu usprawnienia działalności biznesowej, choć sposób, w jaki każdy z nich zapewnia to w odpowiednich ofertach, jest inny. 

Ogólnie rzecz biorąc, odbiorcy są różni:

* Usługi Cognitive Services są dla deweloperów bez doświadczenia uczenia maszynowego.
* Usługa Azure Machine Learning jest dostosowana do potrzeb analityków danych. 

## <a name="how-is-a-cognitive-service-different-from-machine-learning"></a>Czym różni się usługa Cognitive Service od uczenia maszynowego?

Usługa Cognitive Service zapewnia wyszkolony model dla Ciebie. Łączy dane i algorytm, dostępne z interfejsów API REST lub SDK. Tę usługę można zaimplementować w ciągu kilku minut, w zależności od scenariusza.  Usługa cognitive service zapewnia odpowiedzi na ogólne problemy, takie jak kluczowe frazy w identyfikacji tekstu lub elementu w obrazach. 

Uczenie maszynowe to proces, który zazwyczaj wymaga dłuższego okresu czasu na pomyślne wdrożenie. Ten czas poświęć na zbieranie danych, czyszczenie, transformację, wybór algorytmów, szkolenie modelu i wdrażanie, aby uzyskać ten sam poziom funkcjonalności zapewnianych przez usługę Cognitive Service. Dzięki uczeniu maszynowemu możliwe jest udzielanie odpowiedzi na wysoce wyspecjalizowane i/lub konkretne problemy. Problemy z uczeniem maszynowym wymagają znajomości konkretnego przedmiotu i danych rozpatrywanego problemu, a także wiedzy specjalistycznej w dziedzinie nauki o danych.

## <a name="what-kind-of-data-do-you-have"></a>Jakie masz dane?

Usługi Cognitive Services, jako grupa usług, może wymagać żadnych, niektóre lub wszystkie dane niestandardowe dla przeszkolonego modelu. 

### <a name="no-additional-training-data-required"></a>Nie są wymagane żadne dodatkowe dane szkoleniowe

Usługi, które zapewniają w pełni wyszkolony model, mogą być traktowane jako _czarna skrzynka._ Nie musisz wiedzieć, jak działają i jakie dane zostały wykorzystane do ich przeszkolenia. Przenieś swoje dane do w pełni przeszkolonego modelu, aby uzyskać prognozę. 

### <a name="some-or-all-training-data-required"></a>Niektóre lub wszystkie wymagane dane szkoleniowe

Niektóre usługi umożliwiają przynoszenie własnych danych, a następnie szkolenie modelu. Dzięki temu można rozszerzyć model przy użyciu danych i algorytmu Usługi z własnych danych. Dane wyjściowe są zgodne z Twoimi potrzebami. Po wprowadzeniu własnych danych może być konieczne oznaczenie danych w sposób specyficzny dla usługi. Na przykład, jeśli trenujesz model do identyfikacji kwiatów, można podać katalog obrazów kwiatów wraz z lokalizacją kwiatu w każdym obrazie, aby trenować model. 

Usługa może _umożliwiać_ dostarczanie danych w celu zwiększenia własnych danych. Usługa może _wymagać_ podania danych. 

### <a name="real-time-or-near-real-time-data-required"></a>Wymagane dane w czasie rzeczywistym lub w czasie zbliżonym do rzeczywistego

Usługa może wymagać danych w czasie rzeczywistym lub w czasie zbliżonym do rzeczywistego, aby utworzyć skuteczny model. Te usługi przetwarzają znaczne ilości danych modelu. 

## <a name="service-requirements-for-the-data-model"></a>Wymagania serwisowe dla modelu danych

Poniższe dane kategoryzują każdą usługę, za pomocą którego rodzaju danych zezwala lub wymaga.

|Usługa poznawcza|Nie są wymagane żadne dane szkoleniowe|Podajesz pewne lub wszystkie dane szkoleniowe|Gromadzenie danych w czasie rzeczywistym lub w pobliżu ich gromadzenia|
|--|--|--|--|
|[Narzędzie do wykrywania anomalii](./Anomaly-Detector/overview.md)|x|x|x|
|Wyszukiwanie Bing |x|||
|[Wizja komputerowa](./Computer-vision/Home.md)|x|||
|[Moderator zawartości](./Content-Moderator/overview.md)|x||x|
|[Custom Vision](./Custom-Vision-Service/home.md)||x||
|[Rozpoznawanie twarzy](./Face/Overview.md)|x|x||
|[Rozpoznawanie formularzy](./form-recognizer/overview.md)||x||
|[Czytnik immersyjny](./immersive-reader/overview.md)|x|||
|[Rozpoznawanie pisma odręcznego](./Ink-recognizer/overview.md)|x|x||
|[Rozumienie języka (LUIS)](./LUIS/what-is-luis.md)||x||
|[Personalizacja](./personalizer/what-is-personalizer.md)|x*|x*|x|
|[QnA Maker](./QnAMaker/Overview/overview.md)||x||
|[Aparat rozpoznawania głośników](./speaker-recognition/home.md)||x||
|[Zamiana tekstu na mowę (TTS)](speech-service/text-to-speech.md)|x|x||
|[Mowa mowy na tekst (STT)](speech-service/speech-to-text.md)|x|x||
|[Tłumaczenie mowy](speech-service/speech-translation.md)|x|||
|[Analiza tekstu](./text-analytics/overview.md)|x|||
|[Tekst tłumacza](./translator/translator-info-overview.md)|x|||
|[Tekst tłumacza - tłumacz niestandardowy](./translator/custom-translator/overview.md)||x||

* Personalizer potrzebuje tylko danych szkoleniowych zebranych przez usługę (ponieważ działa w czasie rzeczywistym) do oceny twojej polityki i danych. Personalizer nie potrzebuje dużych historycznych zestawów danych do szkolenia z góry lub wsadowego. 

## <a name="where-can-you-use-cognitive-services"></a>Gdzie można korzystać z usług Cognitive Services?
 
Usługi są używane w dowolnej aplikacji, która może wykonywać interfejsy API REST lub wywołania SDK. Przykładami aplikacji są strony internetowe, boty, wirtualna lub mieszana rzeczywistość, aplikacje komputerowe i mobilne. 

## <a name="how-is-azure-cognitive-search-related-to-cognitive-services"></a>W jaki sposób usługa Azure Cognitive Search jest powiązana z usługami Cognitive Services?

[Usługa Azure Cognitive Search](../search/search-what-is-azure-search.md) to oddzielna usługa wyszukiwania w chmurze, która opcjonalnie używa usług Cognitive Services do dodawania przetwarzania obrazu i języka naturalnego do indeksowania obciążeń. Usługi Cognitive Services są udostępniane w usłudze Azure Cognitive Search za pomocą [wbudowanych umiejętności,](../search/cognitive-search-predefined-skills.md) które zawijają poszczególne interfejsy API. Można użyć bezpłatnego zasobu dla instruktaży, ale zaplanować tworzenie i dołączanie [zasobu podlegającego rozliczaniu](../search/cognitive-search-attach-cognitive-services.md) dla większych woluminów.

## <a name="how-can-you-use-cognitive-services"></a>Jak można korzystać z usług Cognitive Services?

Każda usługa dostarcza informacji o twoich danych. Usługi można łączyć ze sobą w rozwiązania łańcuchowe, takie jak konwertowanie mowy (audio) na tekst, tłumaczenie tekstu na wiele języków, a następnie używanie przetłumaczonych języków w celu uzyskania odpowiedzi z bazy wiedzy. Usługi Cognitive Services mogą być używane do samodzielnego tworzenia inteligentnych rozwiązań, ale można je również łączyć z tradycyjnymi projektami uczenia maszynowego w celu uzupełnienia modeli lub przyspieszenia procesu rozwoju. 

Usługi Cognitive Services, które dostarczają eksportowane modele dla innych narzędzi uczenia maszynowego:

|Usługa poznawcza|Informacje o modelu|
|--|--|
|[Custom Vision](./custom-vision-service/home.md)|[Eksport dla](./Custom-Vision-Service/export-model-python.md) Tensorflow dla Systemu Android, CoreML dla iOS11, ONNX dla Windows ML|

## <a name="learn-more"></a>Dowiedz się więcej

* [Przewodnik architektury — jakie są produkty uczenia maszynowego firmy Microsoft?](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning)
* [Uczenie maszynowe — wprowadzenie do uczenia głębokiego a uczenia maszynowego](../machine-learning/concept-deep-learning-vs-machine-learning.md)

## <a name="next-steps"></a>Następne kroki

* Utwórz konto usługi Cognitive Service w [witrynie Azure portal](cognitive-services-apis-create-account.md) lub za pomocą [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli).
* Dowiedz się, jak [uwierzytelnić](authentication.md) się w usłudze cognitive.
* Użyj [rejestrowania diagnostycznego](diagnostic-logging.md) do identyfikacji i debugowania problemów. 
* Wdrażanie usługi Cognitive Service w [kontenerze](cognitive-services-container-support.md)platformy Docker .
* Bądź na bieżąco z [aktualizacjami usług](https://azure.microsoft.com/updates/?product=cognitive-services).

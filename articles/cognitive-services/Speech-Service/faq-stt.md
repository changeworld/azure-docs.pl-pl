---
title: Często zadawane pytania dotyczące zamiana mowy na tekst usługa na platformie Azure
titleSuffix: Azure Cognitive Services
description: Uzyskaj odpowiedzi na najpopularniejsze pytania dotyczące zamiana mowy na tekst usługi.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: 6cc530d2680c0410081ad3ad3e573cd59d5583d6
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341957"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Zamiana mowy na tekst — często zadawane pytania

Jeśli nie możesz znaleźć odpowiedzi na pytania w tych często zadawanych PYTAŃ, zapoznaj się z [inne opcje pomocy technicznej](support.md).

## <a name="general"></a>Ogólne

**Pyt.: Jaka jest różnica między modelem linii bazowej a custom Speech modelu tekstu?**

**Odp.:** Model linii bazowej przeszkoleni przy użyciu danych należącymi do firmy Microsoft i została już wdrożona w chmurze.  Niestandardowy model można użyć do dostosowania modelu, aby lepiej dopasować się w określonym środowisku, zawierający określone hałasu otoczenia lub języka. Fabryk, samochodach czy hałas streets wymagałoby dostosowany model akustyczny. Tematy, takie jak biologii, fizyki, radiologii, nazwy produktu, a niestandardowe akronimów wymagałoby modelu językowego dostosowane.

**Pyt.: Gdzie rozpocząć, jeśli chcę korzystać z modelu odniesienia?**

**Odp.:** Najpierw pobierz [klucz subskrypcji](get-started.md). Jeśli chcesz wykonywać wywołania REST do modeli predeployed linii bazowej, zobacz [interfejsów API REST](rest-apis.md). Jeśli chcesz użyć funkcji WebSockets, [Pobierz zestaw SDK](speech-sdk.md).

**Pyt.: Zawsze należy do tworzenia modelu mowy niestandardowej?**

**Odp.:** Nie. Jeśli aplikacja korzysta z ogólnego, codzienne języka, nie trzeba dostosować model. Jeśli aplikacja jest używana w środowisku, w przypadku niewielkiego lub żadnego hałas w tle, nie trzeba dostosować model.

Można wdrożyć punkt odniesienia i niestandardowych modeli w portalu i uruchomi testy dokładności względem nich. Ta funkcja służy do mierzenia dokładności modelu linii bazowej w porównaniu z modelu niestandardowego.

**Pyt.: Skąd mam wiedzieć, po zakończeniu przetwarzania dla mojego zestawu danych lub modelu?**

**Odp.:** Obecnie stan modelu lub zestawu danych w tabeli jest jedynym sposobem, aby znać. Po zakończeniu przetwarzania stan to **Powodzenie**.

**Pyt.: Można utworzyć więcej niż jednego modelu?**

**Odp.:** Nie ma żadnego limitu liczbę modeli, jaką może mieć w kolekcji.

**Pyt.: Czy mogę rzędu milionów dolarów, czy I w przypadku popełnienia. Jak anulować Moje importowania danych lub modelu tworzenia, który jest w toku?**

**Odp.:** Obecnie nie można wycofać procesem adaptacji akustyczne lub języka. Można usunąć zaimportowanych danych i modeli, gdy są one w stan końcowy.

**Pyt.: Jaka jest różnica między modelem wyszukiwania i dyktowania i konwersacji modelu?**

**Odp.:** Możesz wybrać więcej niż jednego modelu odniesienia w usłudze rozpoznawania mowy. Model konwersacji jest przydatna do rozpoznawania mowy, który jest używany w stylu konwersacji. Ten model jest idealny dla przepisywania połączeń telefonicznych. Model wyszukiwania i dyktowanie jest idealny dla aplikacji wyzwalany głosu. Uniwersalne model jest nowy model, który ma na celu obu scenariuszy. Uniwersalne modelu jest obecnie poziom jakości modelu konwersacji w większości ustawień regionalnych lub wyższy.

**Pyt.: Czy mogę zaktualizować Mój istniejący model (model układania)**

**Odp.:** Nie można zaktualizować istniejący model. Jako rozwiązanie należy połączyć stary zestaw danych za pomocą nowego zestawu danych i readapt.

Stary zestaw danych i nowy zestaw danych musi być połączone jednego pliku zip (Aby uzyskać dane akustyczne) lub w pliku txt (w przypadku danych języka). Po zakończeniu dostosowania modelu nowe, zaktualizowane musi być ponownie wdrażana w celu uzyskania nowego punktu końcowego

**Pyt.: Gdy dostępna jest nowa wersja linii bazowej jest Moje wdrożenie automatycznie zaktualizowane?**

**Odp.:** Wdrożenia nie zostanie automatycznie zaktualizowany.

Jeśli masz dostosowane i wdrożyć model z linią bazową w wersji 1.0, tego wdrożenia pozostaje niezmieniona. Klientów można zlikwidować wdrożony model, ponownie dostosowania przy użyciu nowszej wersji linii bazowej i Wdróż ponownie.

**Pyt.: Co zrobić, jeśli potrzebne zapewnienie większej współbieżności dla mojego wdrożony model niż treści oferowanych w portalu?**

**Odp.:** Możesz skalować w górę w przyrostach co 20 równoczesnych żądań modelu.

Skontaktuj się z pomocą [obsługa mowy](mailto:speechsupport@microsoft.com?subject=Request%20for%20higher%20concurrency%20for%20Speech-to-text) Jeśli potrzebujesz większej skali.

**Pyt.: Czy mogę pobrać swój model i uruchomić go lokalnie?**

**Odp.:** Modele nie pobierane i wykonywany lokalnie.

**Pyt.: Moje żądania są rejestrowane?**

**Odp.:** Masz do wyboru podczas tworzenia wdrożenia na wyłączenie śledzenia. W tym momencie nie audio lub transkrypcje będą rejestrowane. W przeciwnym razie żądania zwykle są rejestrowane na platformie Azure w bezpiecznym magazynie.

**Pyt.: Moje żądania są ograniczane?**

**Odp.:** Interfejs API REST ogranicza żądania do 25 na 5 sekund. Szczegółowe informacje można znaleźć w naszych stron [zamiana mowy na tekst](speech-to-text.md).

Jeśli masz więcej prywatności, które uniemożliwiają za pomocą usługa custom Speech service, skontaktuj się z jednego z kanałów pomocy technicznej.

## <a name="importing-data"></a>Importowanie danych

**Pyt.: Co to jest limit rozmiaru zestawu danych i dlaczego jest limit?**

**Odp.:** Bieżący limit dla zestawu danych wynosi 2 GB. Limit wynosi ze względu na ograniczenia rozmiaru pliku do przekazania HTTP. 

**Pyt.: Moje pliki tekstowe można skompresować tak można przekazać plik tekstowy większych?** 

**Odp.:** Nie. Obecnie dozwolone są tylko pliki nieskompresowanych tekstu.

**Pyt.: Raport danych mówi wystąpiły wypowiedzi nie powiodło się. Na czym polega problem?**

**Odp.:** Nie można przekazać 100 procent wypowiedzi w pliku nie jest problemem. Jeśli większość wypowiedzi akustyczne lub języka zestawu danych (na przykład więcej niż 95 procent) są pomyślnie zaimportowane, zestaw danych może być użyteczne. Jednak zaleca się, że próby zrozumieć, dlaczego wypowiedzi nie powiodło się i rozwiązać problemy. Najbardziej typowe problemy, takie jak błędy, formatowania można łatwo rozwiązać. 

## <a name="creating-an-acoustic-model"></a>Tworzenie modelu akustycznego

**Pyt.: Ile dane akustyczne potrzebuję?**

**Odp.:** Firma Microsoft zaleca, począwszy od 30 minut do godziny danych akustycznych.

**Pyt.: Jakie dane należy zbierać?**

**Odp.:** Zbieranie danych, które są jak najbliższe danym scenariuszu aplikacji i przypadek użycia, jak to możliwe. Zbieranie danych powinien być zgodny w aplikacji docelowej i użytkowników pod kątem urządzeń lub urządzeń, środowisk i typy głośników. Ogólnie rzecz biorąc należy zebrać dane z jako szerokiego zakresu mówiące, jak to możliwe. 

**Pyt.: Jak zbierać dane akustyczne?**

**Odp.:** Można tworzenie autonomicznych aplikacji zbierania danych lub użyć oprogramowania nagrywania dźwięku off-the-shelf. Można również utworzyć wersję aplikacji, który rejestruje dane audio, a następnie używa danych. 

**Pyt.: Należy także dostosowania danych, samodzielnie?**

**Odp.:** Tak! Można także samodzielnie lub użyj usługi profesjonalne transkrypcji. Niektórzy użytkownicy preferują professional transcribers i inne Użyj crowdsourcing lub wykonaj transkrypcje, samodzielnie.

## <a name="accuracy-testing"></a>Testowanie dokładności

**Pyt.: Czy można wykonać w trybie offline testowania Moje niestandardowy model akustyczny przy użyciu modelu języka niestandardowego?**

**Odp.:** Tak, po prostu wybierz język niestandardowy model w menu rozwijanym podczas konfigurowania testu w trybie offline.

**Pyt.: Czy można wykonać w trybie offline testuje swój model języka niestandardowego za pomocą niestandardowy model akustyczny?**

**Odp.:** Tak, po prostu wybierz niestandardowy model akustyczny w menu rozwijanym podczas konfigurowania testu w trybie offline.

**Pyt.: Co to jest współczynnik błędów dla programu word (WER) i jak jest ona obliczana?**

**Odp.:** Raportowanie błędów systemu Windows jest metryki oceny funkcji rozpoznawania mowy. Raportowanie błędów systemu Windows jest traktowana jako liczba błędów, w tym liczba operacji wstawienia, usuwania i podstawienia, podzielona przez całkowitą liczbę słów w transkrypcji odwołania. Aby uzyskać więcej informacji, zobacz [współczynnik błędów word](https://en.wikipedia.org/wiki/Word_error_rate).

**Pyt.: Jak ustalić, czy wyniki testu dokładność są dobre**

**Odp.:** Wyniki pokazują porównania między modelem linii bazowej i modeli, które zostały dostosowane. Należy dążyć do zapewniała modelu odniesienia umożliwiają dostosowanie cenna.

**Pyt.: Jak określić raportowanie błędów systemu Windows w modelu podstawowej, aby można było zobaczyć jeśli było poprawę?** 

**Odp.:** Wyniki testu w trybie offline Pokaż linii bazowej dokładności modelu niestandardowego, a także ulepszanie względem punktu odniesienia.

## <a name="creating-a-language-model"></a>Tworzenie modelu języka

**Pyt.: Jak dużo danych tekstu należy przekazać?**

**Odp.:** To zależy od różnych słownictwo i wyrażenia używane w aplikacji pochodzą od początkowego modeli językowych. Dla wszystkich nowych wyrazów warto zawierają przykłady tyle możliwie użycia tych słów. Dla typowych fraz, które są używane w aplikacji łącznie z wyrażeń w danym języku jest również przydatne ponieważ poleca systemu do nasłuchiwania też niniejsze postanowienia. Jest to często mają co najmniej 100 i zazwyczaj kilka kilkuset lub kilku wypowiedzi w zestawie danych języka. Ponadto niektóre typy zapytań powinny być częściej niż inne, można wstawić wiele kopii typowych zapytań w zestawie danych.

**Pyt.: Czy po prostu przekazać z listy słów?**

**Odp.:** Przekazywanie listę słów, które spowoduje to dodanie słowa do słownictwa, ale go nie będzie uczyć system jak wyrazy są zwykle używane. Dostarczając pełnej lub częściowej wypowiedzi (zdań lub fraz rzeczy, które użytkownicy mogą powiedzieć), model języka informacji można znaleźć nowych słów i sposobu ich używania. Model języka niestandardowego jest dobra, nie tylko na dodawanie nowych słów w systemie, ale również dostosowywania prawdopodobieństwo znanych wyrazy dla aplikacji. Zapewnienie pełnej wypowiedzi pomaga systemu, Dowiedz się, lepiej. 

## <a name="next-steps"></a>Kolejne kroki

* [Rozwiązywanie problemów](troubleshooting.md)
* [Informacje o wersji](releasenotes.md)

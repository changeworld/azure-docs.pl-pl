---
title: Często zadawane pytania dotyczące zamiana mowy na tekst usługa na platformie Azure
description: Uzyskaj odpowiedzi na najpopularniejsze pytania dotyczące zamiana mowy na tekst usługi.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: 31515d6867fc5524df1b081932dd2a28b0cf989c
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2018
ms.locfileid: "44022162"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Zamiana mowy na tekst — często zadawane pytania

Jeśli nie możesz znaleźć odpowiedzi na pytania w tych często zadawanych PYTAŃ, zapoznaj się z [inne opcje pomocy technicznej](support.md).

## <a name="general"></a>Ogólne

**P: jaka jest różnica między modelem linii bazowej i custom Speech modelu tekstu?**

**A**: modelu odniesienia przeszkoleni przy użyciu danych należącymi do firmy Microsoft i została już wdrożona w chmurze.  Niestandardowy model można użyć do dostosowania modelu, aby lepiej dopasować się w określonym środowisku, zawierający określone hałasu otoczenia lub języka. Fabryk, samochodach czy hałas streets wymagałoby dostosowany model akustyczny. Tematy, takie jak biologii, fizyki, radiologii, nazwy produktu, a niestandardowe akronimów wymagałoby modelu językowego dostosowane.

**P: gdzie rozpocząć, jeśli chcę korzystać z modelu odniesienia?**

**A**: najpierw Pobierz [klucz subskrypcji](get-started.md). Jeśli chcesz wykonywać wywołania REST do modeli predeployed linii bazowej, zobacz [interfejsów API REST](rest-apis.md). Jeśli chcesz użyć funkcji WebSockets, [Pobierz zestaw SDK](speech-sdk.md).

**Pyt.: zawsze należy do tworzenia modelu mowy niestandardowej?**

**Element**: nie. Jeśli aplikacja korzysta z ogólnego, codzienne języka, nie trzeba dostosować model. Jeśli aplikacja jest używana w środowisku, w przypadku niewielkiego lub żadnego hałas w tle, nie trzeba dostosować model. 

Można wdrożyć punkt odniesienia i niestandardowych modeli w portalu i uruchomi testy dokładności względem nich. Ta funkcja służy do mierzenia dokładności modelu linii bazowej w porównaniu z modelu niestandardowego.

**P: jak będę wiedzieć, że po zakończeniu przetwarzania do zestawu danych lub modelu?**

**A**: obecnie stan modelu lub zestawu danych w tabeli jest jedynym sposobem, aby znać. Po zakończeniu przetwarzania stan to **Powodzenie**.

**P: czy można utworzyć więcej niż jednego modelu?**

**Element**: nie ma żadnego limitu liczby modeli, które mogą mieć w kolekcji.

**P: czy mogę rzędu milionów dolarów, czy I w przypadku popełnienia. Jak anulować Moje importowania danych lub modelu tworzenia, który jest w toku?**

**A**: obecnie nie można wycofać procesem adaptacji akustyczne lub języka. Można usunąć zaimportowanych danych i modeli, gdy są one w stan końcowy.

**P: jaka jest różnica między modelu wyszukiwania i dyktowania i konwersacji modelu?**

**A**: możesz korzystać z więcej niż jednego modelu odniesienia w usłudze rozpoznawania mowy. Model konwersacji jest przydatna do rozpoznawania mowy, który jest używany w stylu konwersacji. Ten model jest idealny dla przepisywania połączeń telefonicznych. Model wyszukiwania i dyktowanie jest idealny dla aplikacji wyzwalany głosu. Uniwersalne model jest nowy model, który ma na celu obu scenariuszy.

**P: czy mogę zaktualizować Mój istniejący model (model układania)?**

**Element**: nie można zaktualizować istniejący model. Jako rozwiązanie należy połączyć stary zestaw danych za pomocą nowego zestawu danych i readapt.

Stary zestaw danych i nowy zestaw danych musi być połączone jednego pliku zip (Aby uzyskać dane akustyczne) lub w pliku txt (w przypadku danych języka). Po zakończeniu dostosowania modelu nowe, zaktualizowane musi być ponownie wdrażana w celu uzyskania nowego punktu końcowego

**P: co mogę zrobić, jeśli potrzebne zapewnienie większej współbieżności dla mojego wdrożony model niż treści oferowanych w portalu?** 

**A**: można zwiększać z przyrostem równym 20 równoczesnych żądań modelu. 

Skontaktuj się z nami, jeśli potrzebujesz większej skali.

**P: czy mogę Pobierz swój model i uruchomić go lokalnie?**

**A**: modeli nie mogą być pobierane i wykonywany lokalnie.

**P: czy Moje żądania rejestrowane?**

**A**: masz do wyboru podczas tworzenia wdrożenia na wyłączenie śledzenia. W tym momencie nie audio lub transkrypcje będą rejestrowane. W przeciwnym razie żądania zwykle są rejestrowane na platformie Azure w bezpiecznym magazynie. 

Jeśli masz więcej prywatności, które uniemożliwiają za pomocą usługa custom Speech service, skontaktuj się z jednego z kanałów pomocy technicznej.

## <a name="importing-data"></a>Importowanie danych

**Pyt.: co to jest limit rozmiaru zestawu danych i dlaczego jest limit?**

**A**: bieżący limit dla zestawu danych wynosi 2 GB. Limit wynosi ze względu na ograniczenia rozmiaru pliku do przekazania HTTP. 

**P: czy Moje pliki tekstowe można skompresować tak można przekazać plik tekstowy większych?** 

**Element**: nie. Obecnie dozwolone są tylko pliki nieskompresowanych tekstu.

**Pytanie raport danych mówi wystąpiły wypowiedzi nie powiodło się. Na czym polega problem?**

**Element**: nie można przekazać 100 procent wypowiedzi w pliku nie jest problemem. Jeśli większość wypowiedzi akustyczne lub języka zestawu danych (na przykład więcej niż 95 procent) są pomyślnie zaimportowane, zestaw danych może być użyteczne. Jednak zaleca się, że próby zrozumieć, dlaczego wypowiedzi nie powiodło się i rozwiązać problemy. Najbardziej typowe problemy, takie jak błędy, formatowania można łatwo rozwiązać. 

## <a name="creating-an-acoustic-model"></a>Tworzenie modelu akustycznego

**P: jak dużo danych akustycznych potrzebuję?**

**A**: Firma Microsoft zaleca, począwszy od 30 minut do godziny danych akustycznych.

**P: jakie dane należy zbierać?**

**A**: zbieranie danych, które są jak najbliższe danym scenariuszu aplikacji i przypadek użycia, jak to możliwe. Zbieranie danych powinien być zgodny w aplikacji docelowej i użytkowników pod kątem urządzeń lub urządzeń, środowisk i typy głośników. Ogólnie rzecz biorąc należy zebrać dane z jako szerokiego zakresu mówiące, jak to możliwe. 

**P: jak należy zbierać dane akustyczne**

**A**: można utworzyć oddzielną aplikację zbierania danych lub użyć oprogramowania nagrywania dźwięku off-the-shelf. Można również utworzyć wersję aplikacji, który rejestruje dane audio, a następnie używa danych. 

**P: czy potrzebujesz także dostosowania danych, samodzielnie?**

**A**: tak! Można także samodzielnie lub użyj usługi profesjonalne transkrypcji. Niektórzy użytkownicy preferują professional transcribers i inne Użyj crowdsourcing lub wykonaj transkrypcje, samodzielnie.

## <a name="accuracy-testing"></a>Testowanie dokładności

**P: czy można przeprowadzić testowanie offline z moich niestandardowy model akustyczny przy użyciu modelu języka niestandardowego?**

**A**: tak, po prostu wybierz model języka niestandardowego w menu rozwijanym, podczas konfigurowania testu w trybie offline.

**P: czy można wykonać w trybie offline testuje swój model języka niestandardowego za pomocą niestandardowy model akustyczny?**

**A**: tak, wystarczy wybrać niestandardowy model akustyczny w menu rozwijanym, podczas konfigurowania testu w trybie offline.

**Pytanie: jaka jest częstotliwość błędów programu word (WER) i jak jest ona obliczana?**

**A**: raportowanie błędów systemu Windows jest metryki oceny funkcji rozpoznawania mowy. Raportowanie błędów systemu Windows jest traktowana jako liczba błędów, w tym liczba operacji wstawienia, usuwania i podstawienia, podzielona przez całkowitą liczbę słów w transkrypcji odwołania. Aby uzyskać więcej informacji, zobacz [współczynnik błędów word](https://en.wikipedia.org/wiki/Word_error_rate).

**P: jak określić, czy wyniki testu dokładność są dobre**

**A**: Wyniki pokazują porównania między modelem linii bazowej i model możesz dostosować. Należy dążyć do zapewniała modelu odniesienia umożliwiają dostosowanie cenna.

**P: jak określić raportowanie błędów systemu Windows w modelu podstawowej, dlatego mogę zobaczyć, jeśli było poprawę?** 

**A**: wyniki testu w trybie offline Pokaż linii bazowej dokładności modelu niestandardowego, a także ulepszanie względem punktu odniesienia.

## <a name="creating-a-language-model"></a>Tworzenie modelu języka

**P: jak dużo danych tekstowych należy przekazać?**

**A**: zależy od różnych słownictwo i wyrażenia używane w aplikacji pochodzą od początkowego modeli językowych. Dla wszystkich nowych wyrazów warto zawierają przykłady tyle możliwie użycia tych słów. Dla typowych fraz, które są używane w aplikacji łącznie z wyrażeń w danym języku jest również przydatne ponieważ poleca systemu do nasłuchiwania też niniejsze postanowienia. Jest to często mają co najmniej 100 i zazwyczaj kilka kilkuset lub kilku wypowiedzi w zestawie danych języka. Ponadto niektóre typy zapytań powinny być częściej niż inne, można wstawić wiele kopii typowych zapytań w zestawie danych.

**P: czy można po prostu przekazać z listy słów?**

**A**: przekazywanie listę słów, które spowoduje to dodanie słowa do słownictwa, ale go nie będzie uczyć system jak wyrazy są zwykle używane. Dostarczając pełnej lub częściowej wypowiedzi (zdań lub fraz rzeczy, które użytkownicy mogą powiedzieć), model języka informacji można znaleźć nowych słów i sposobu ich używania. Model języka niestandardowego jest dobra, nie tylko na dodawanie nowych słów w systemie, ale również dostosowywania prawdopodobieństwo znanych wyrazy dla aplikacji. Zapewnienie pełnej wypowiedzi pomaga systemu, Dowiedz się, lepiej. 

## <a name="next-steps"></a>Kolejne kroki

* [Rozwiązywanie problemów](troubleshooting.md)
* [Informacje o wersji](releasenotes.md)

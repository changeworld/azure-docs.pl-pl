---
title: Najczęściej zadawane pytania dotyczące mowy do tekstu
titleSuffix: Azure Cognitive Services
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące usługi Zamiana mowy na tekst.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/4/2019
ms.author: panosper
ms.openlocfilehash: a279aebdd19ebd3a41ddad0c1c279937e00838c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77168460"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Najczęściej zadawane pytania dotyczące mowy do tekstu

Jeśli nie możesz znaleźć odpowiedzi na swoje pytania w tym faq, zapoznaj się z [innymi opcjami pomocy technicznej](support.md).

## <a name="general"></a>Ogólne

**Pyt.: Jaka jest różnica między modelem bazowym a niestandardowym modelem zamiany mowy na tekst?**

**Odp**.: Model linii bazowej został przeszkolony przy użyciu danych należących do firmy Microsoft i jest już wdrożony w chmurze. Za pomocą modelu niestandardowego można dostosować model, aby lepiej dopasować go do określonego środowiska, które ma określony szum otoczenia lub język. Podłogi fabryczne, samochody lub hałaśliwe ulice wymagałyby dostosowanego modelu akustycznego. Tematy takie jak biologia, fizyka, radiologia, nazwy produktów i niestandardowe akronimy wymagałyby dostosowanego modelu językowego.

**Pyt.: Od czego zacząć, jeśli chcę używać modelu linii bazowej?**

**A:** Po pierwsze, pobierz [klucz subskrypcji](get-started.md). Aby wywołać rest do wstępnie wypalonych modeli linii bazowych, zobacz [interfejsy API REST](rest-apis.md). Jeśli chcesz używać websockets, [pobierz SDK](speech-sdk.md).

**Pyt.: Czy zawsze muszę tworzyć niestandardowy model mowy?**

**Odp.:** Nie. Jeśli aplikacja używa ogólnego, codziennego języka, nie trzeba dostosowywać modelu. Jeśli aplikacja jest używana w środowisku, w którym szum w tle jest niewielki lub nie występuje, nie trzeba dostosowywać modelu.

Można wdrożyć modele linii bazowej i dostosowane w portalu, a następnie uruchomić testy dokładności przeciwko nim. Za pomocą tej funkcji można zmierzyć dokładność modelu linii bazowej w porównaniu z modelem niestandardowym.

**Pyt.: Skąd będę wiedzieć, kiedy przetwarzanie dla mojego zestawu danych lub modelu jest zakończone?**

**A:** Obecnie stan modelu lub zestawu danych w tabeli jest jedynym sposobem, aby wiedzieć. Po zakończeniu przetwarzania stan to **Powodzenie**.

**P: Czy mogę utworzyć więcej niż jeden model?**

**O:** Nie ma ograniczeń co do liczby modeli, które możesz mieć w swojej kolekcji.

**P: Zdałem sobie sprawę, że popełniłem błąd. Jak anulować importowanie danych lub tworzenie modelu, które jest w toku?**

**Odp.:** Obecnie nie można cofnąć procesu adaptacji akustycznej lub językowej. Importowane dane i modele można usunąć, gdy znajdują się one w stanie terminala.

**Pyt.: Jaka jest różnica między modelem wyszukiwania i dyktowania a modelem konwersacyjnym?**

**A:** W usłudze Mowy można wybrać jeden z więcej niż jednego modelu linii bazowej. Model konwersacji jest przydatny do rozpoznawania mowy, która jest wymawiana w stylu konwersacyjnym. Ten model jest idealny do transkrypcji połączeń telefonicznych. Model wyszukiwania i dyktowania jest idealny dla aplikacji wyzwalanych głosem. Model uniwersalny to nowy model, który ma na celu rozwiązanie obu scenariuszy. Model uniwersalny jest obecnie na poziomie jakości lub powyżej modelu konwersacji w większości ustawień regionalnych.

**P: Czy mogę zaktualizować istniejący model (układanie modelu)?**

**A:** Nie można zaktualizować istniejącego modelu. Jako rozwiązanie połącz stary zestaw danych z nowym zestawem danych i odczytem.

Stary zestaw danych i nowy zestaw danych muszą być połączone w pojedynczy plik zip (dla danych akustycznych) lub w pliku txt (dla danych językowych). Po zakończeniu adaptacji nowy, zaktualizowany model musi zostać ponownie rozmieszczony w celu uzyskania nowego punktu końcowego

**Pyt.: Gdy dostępna jest nowa wersja planu bazowego, czy moje wdrożenie jest automatycznie aktualizowane?**

**A:** Wdrożenia NIE będą automatycznie aktualizowane.

Jeśli dostosowano i wdrożono model z linią bazową V1.0, to wdrożenie pozostanie takie, jakie jest. Klienci mogą wycofać wdrożony model, odczytać przy użyciu nowszej wersji linii bazowej i ponownie wdrożyć.

**P: Czy mogę pobrać mój model i uruchomić go lokalnie?**

**A:** Modeli nie można pobrać i wykonać lokalnie.

**P: Czy moje żądania są rejestrowane?**

**A:** Masz wybór podczas tworzenia wdrożenia, aby wyłączyć śledzenie. W tym momencie nie będą rejestrowane żadne audio ani transkrypcje. W przeciwnym razie żądania są zazwyczaj rejestrowane na platformie Azure w bezpiecznym magazynie.

**P: Czy moje żądania są ograniczone?**

**A:** Interfejs API REST ogranicza żądania do 25 na 5 sekund. Szczegóły można znaleźć na naszych stronach [mowy do tekstu](speech-to-text.md).

**P: Jak naliczane są opłaty za dźwięk dwukanałowy?**

**A:** Jeśli prześlesz każdy kanał oddzielnie (każdy kanał we własnym pliku), zostanie naliczona opłata za czas trwania każdego pliku. Jeśli prześlesz jeden plik z każdym kanałem multipleksowanym razem, zostanie naliczona opłata za czas trwania pojedynczego pliku. Szczegółowe informacje na temat cen można znaleźć na [stronie cennika usług Azure Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Jeśli masz dalsze problemy dotyczące prywatności, które uniemożliwią korzystanie z niestandardowej usługi mowy, skontaktuj się z jednym z kanałów pomocy technicznej.

## <a name="increasing-concurrency"></a>Rosnąca współbieżność

**Pyt.: Co zrobić, jeśli potrzebuję wyższej współbieżności dla mojego wdrożonego modelu niż to, co jest oferowane w portalu?**

**A:** Model można skalować w górę w przyrostach 20 równoczesnych żądań.

Za pomocą wymaganych informacji utwórz żądanie pomocy technicznej w [portalu pomocy technicznej platformy Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). Nie umieszczaj informacji na żadnym z kanałów publicznych (GitHub, Stackoverflow, ...) wymienionych na [stronie pomocy technicznej](support.md).

Aby zwiększyć współbieżność dla ***modelu niestandardowego,*** potrzebujemy następujących informacji:

- Region, w którym model jest wdrażany,
- identyfikator punktu końcowego wdrożonego modelu:
  - Dostałem się do [niestandardowego portalu mowy](https://aka.ms/customspeech),
  - zaloguj się (jeśli to konieczne),
  - wybierz swój projekt i wdrożenie,
  - wybierz punkt końcowy, dla którego potrzebny jest wzrost współbieżności,
  - skopiuj plik `Endpoint ID`.

Aby zwiększyć współbieżność dla ***modelu podstawowego,*** potrzebujemy następujących informacji:

- Region usługi,

i albo

- token dostępu dla subskrypcji (zobacz [tutaj](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text#how-to-get-an-access-token)),

lub

- identyfikator zasobu dla subskrypcji:
  - Przejdź do [witryny Azure portal](https://portal.azure.com),
  - zaznacz `Cognitive Services` w polu wyszukiwania,
  - z wyświetlanych usług wybierz usługę mowy, dla której ma zostać zwiększona współbieżność,
  - wyświetlić `Properties` dla tej usługi,
  - skopiuj kompletny `Resource ID`plik .

## <a name="importing-data"></a>Importowanie danych

**Pyt.: Jaki jest limit rozmiaru zestawu danych i dlaczego jest to limit?**

**A:** Bieżący limit dla zestawu danych wynosi 2 GB. Limit wynika z ograniczenia rozmiaru pliku do przekazywania http.

**P: Czy mogę spakować pliki tekstowe, aby przesłać większy plik tekstowy?**

**Odp.:** Nie. Obecnie dozwolone są tylko nieskompresowane pliki tekstowe.

**P: Raport danych mówi, że nie powiodło się wypowiedzi. Na czym polega problem?**

**A:** Nie można przekazać 100 procent wypowiedzi w pliku nie jest problemem. Jeśli zdecydowana większość wypowiedzi w zestawie danych akustycznych lub językowych (na przykład więcej niż 95 procent) są pomyślnie importowane, zestaw danych może być użyteczny. Jednak zaleca się, aby spróbować zrozumieć, dlaczego wypowiedzi nie powiodło się i rozwiązać problemy. Najczęstsze problemy, takie jak błędy formatowania, są łatwe do naprawienia.

## <a name="creating-an-acoustic-model"></a>Tworzenie modelu akustycznego

**P: Ile danych akustycznych potrzebuję?**

**Odp**.: Zalecamy rozpoczęcie od 30 minut do jednej godziny danych akustycznych.

**P: Jakie dane należy zbierać?**

**A:** Zbieranie danych, które są jak najbliżej scenariusza aplikacji i przypadek użycia, jak to możliwe. Zbieranie danych powinno być zgodne z aplikacją docelową i użytkownikami pod względem urządzenia lub urządzeń, środowisk i typów głośników. Ogólnie rzecz biorąc, należy zbierać dane z jak najszerszego grona głośników, jak to możliwe.

**P: Jak należy zbierać dane akustyczne?**

**O:** Można utworzyć samodzielną aplikację do zbierania danych lub użyć gotowego oprogramowania do nagrywania dźwięku. Można również utworzyć wersję aplikacji, która rejestruje dane audio, a następnie używa danych.

**P: Czy muszę samodzielnie transkrybować dane adaptacyjne?**

**A:** Tak! Możesz przepisać go samodzielnie lub skorzystać z profesjonalnej usługi transkrypcji. Niektórzy użytkownicy wolą profesjonalnych transkrypcji, a inni używają crowdsourcingu lub robią transkrypcje sami.

## <a name="accuracy-testing"></a>Testowanie dokładności

**Pyt.: Czy można przeprowadzić testowanie w trybie offline mojego niestandardowego modelu akustycznego przy użyciu niestandardowego modelu języka?**

**A:** Tak, po prostu wybierz niestandardowy model języka w menu rozwijanym podczas konfigurowania testu w trybie offline.

**Pyt.: Czy można przeprowadzić testowanie w trybie offline mojego niestandardowego modelu języka przy użyciu niestandardowego modelu akustycznego?**

**A:** Tak, po prostu wybierz niestandardowy model akustyczny w menu rozwijanym podczas konfigurowania testu w trybie offline.

**Pyt.: Co to jest poziom błędu słowa (WER) i jak jest obliczany?**

**A:** WER jest metryką oceny rozpoznawania mowy. WER jest liczony jako całkowita liczba błędów, która obejmuje wstawienia, usunięcia i podstawienia, podzielone przez całkowitą liczbę słów w transkrypcji odwołania. Aby uzyskać więcej informacji, zobacz [poziom błędu słowa](https://en.wikipedia.org/wiki/Word_error_rate).

**Pyt.: Jak ustalić, czy wyniki testu dokładności są dobre?**

**A:** Wyniki pokazują porównanie między modelem bazowym a modelem, który został dostosowany. Należy dążyć do pokonania modelu bazowego, aby dostosowanie warto.

**Pyt.: Jak określić WER modelu podstawowego, aby sprawdzić, czy nastąpiła poprawa?**

**A:** Wyniki testów w trybie offline pokazują dokładność linii bazowej modelu niestandardowego i poprawę w stosunku do linii bazowej.

## <a name="creating-a-language-model"></a>Tworzenie modelu języka

**P: Ile danych tekstowych muszę przesłać?**

**Odp**.: To zależy od tego, jak różne jest słownictwo i frazy używane w aplikacji od początkowych modeli języka. Dla wszystkich nowych słów warto podać jak najwięcej przykładów użycia tych słów. Dla typowych fraz, które są używane w aplikacji, w tym frazy w danych języka jest również przydatne, ponieważ mówi systemowi, aby również nasłuchiwać tych terminów. Często ma co najmniej 100 i zazwyczaj kilkaset lub więcej wypowiedzi w zestawie danych języka. Ponadto jeśli niektóre typy zapytań powinny być bardziej powszechne niż inne, można wstawić wiele kopii typowych zapytań w zestawie danych.

**P: Czy mogę po prostu przesłać listę słów?**

**A:** Przesłanie listy słów spowoduje dodanie wyrazów do słownictwa, ale nie nauczy systemu, w jaki sposób słowa są zwykle używane. Zapewniając pełne lub częściowe wypowiedzi (zdania lub frazy rzeczy, które użytkownicy mogą powiedzieć), model języka można nauczyć się nowych słów i jak są one używane. Niestandardowy model języka jest dobry nie tylko do dodawania nowych słów do systemu, ale także do dostosowywania prawdopodobieństwa znanych słów dla aplikacji. Zapewnienie pełnych wypowiedzi pomaga systemowi uczyć się lepiej.

## <a name="tenant-model-custom-speech-with-office-365-data"></a>Model dzierżawy (niestandardowa mowa z danymi usługi Office 365)

**Pyt.: Jakie informacje są zawarte w modelu dzierżawy i jak jest tworzony?**

**Odp.:** Model dzierżawy jest zbudowany przy użyciu [publicznych grupowych](https://support.office.com/article/learn-about-office-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2) wiadomości e-mail i dokumentów, które mogą być widoczne dla wszystkich osób w organizacji.

**Pyt.: Jakie środowiska mowy są ulepszone przez model dzierżawy?**

**Odp.:** Gdy model dzierżawy jest włączony, utworzony i opublikowany, jest on używany do poprawy rozpoznawania dla wszystkich aplikacji przedsiębiorstwa utworzonych przy użyciu usługi mowy; który również przekazać token AAD użytkownika wskazujące członkostwa do przedsiębiorstwa.

Środowiska mowy wbudowane w usłudze Office 365, takie jak dyktowanie i podpisy programu PowerPoint, nie są zmieniane podczas tworzenia modelu dzierżawy dla aplikacji usługi mowy.

## <a name="next-steps"></a>Następne kroki

- [Rozwiązywanie problemów](troubleshooting.md)
- [Informacje o wersji](releasenotes.md)

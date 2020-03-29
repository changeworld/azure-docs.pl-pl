---
title: Ocena pewności siebie - QnA Maker
titleSuffix: Azure Cognitive Services
description: Należy opublikować bazę wiedzy. Po opublikowaniu baza wiedzy jest wyszukiwana w punkcie końcowym przewidywania środowiska wykonawczego przy użyciu interfejsu API generateAnswer.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: d901a803311805825c22503af6098e805a67e8f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76843456"
---
# <a name="the-confidence-score-of-an-answer"></a>Ocena pewności siebie w odpowiedzi
Gdy zapytanie użytkownika jest dopasowywało się do bazy wiedzy, program QnA Maker zwraca odpowiednie odpowiedzi wraz z wynikiem zaufania. Ten wynik wskazuje pewność, że odpowiedź jest odpowiednie dopasowanie dla danego zapytania użytkownika.

Wskaźnik pewności siebie to liczba z przedziału od 0 do 100. Wynik 100 jest prawdopodobnie dokładnym dopasowaniem, podczas gdy wynik 0 oznacza, że nie znaleziono żadnej odpowiadającej odpowiedzi. Im wyższy wynik, tym większe zaufanie do odpowiedzi. Dla danego zapytania może być wiele odpowiedzi zwracanych. W takim przypadku odpowiedzi są zwracane w kolejności malejącego wyniku zaufania.

W poniższym przykładzie możesz zobaczyć jedną jednostkę QnA z 2 pytaniami.


![Przykładowa para QnA](../media/qnamaker-concepts-confidencescore/ranker-example-qna.png)

W powyższym przykładzie - można oczekiwać, że wyniki, takie jak zakres wyników próbki poniżej- dla różnych typów zapytań użytkowników:


![Zakres punktacji rankera](../media/qnamaker-concepts-confidencescore/ranker-score-range.png)


W poniższej tabeli przedstawiono typowe zaufanie skojarzone dla danego wyniku.

|Wartość wyniku|Znaczenie wyniku|Przykładowa kwerenda|
|--|--|--|
|90 - 100|Niemal dokładne dopasowanie zapytania użytkownika i pytanie o zblikę|"Moje zmiany nie są aktualizowane w KB po opublikowaniu"|
|> 70|Wysoka pewność siebie - zazwyczaj dobra odpowiedź, która całkowicie odpowiada na zapytanie użytkownika|"Opublikowałem swoją bazę wiedzy, ale nie jest ona aktualizowana"|
|50 - 70|Średnie zaufanie - zazwyczaj dość dobra odpowiedź, która powinna odpowiedzieć na główne intencje zapytania użytkownika|"Czy powinienem zapisać aktualizacje przed opublikowaniem bazy wiedzy?"|
|30 - 50|Niskie zaufanie — zazwyczaj powiązana odpowiedź, która częściowo odpowiada na intencje użytkownika|" Co robi oszczędzać i trenować?"|
|< 30|Bardzo niska pewność siebie — zazwyczaj nie odpowiada na zapytanie użytkownika, ale zawiera kilka pasujących słów lub fraz |" Gdzie mogę dodać synonimy do mojej bazy wiedzy"|
|0|Nie pasuje, więc odpowiedź nie jest zwracana.|"Ile kosztuje usługa"|

## <a name="choose-a-score-threshold"></a>Wybieranie progu punktacji
Powyższa tabela przedstawia wyniki, które są oczekiwane na większości KB. Jednak ponieważ każda kb jest inna i ma różne typy słów, intencji i celów- zalecamy przetestowanie i wybranie progu, który najlepiej działa dla Ciebie. Domyślnie próg jest ustawiony na 0, dzięki czemu wszystkie możliwe odpowiedzi są zwracane. Zalecany próg, który powinien działać dla większości KB, wynosi **50**.

Wybierając próg, pamiętaj o równowadze między dokładnością a zasięgiem, a także zmieniaj próg w zależności od wymagań.

- Jeśli **dokładność** (lub precyzja) jest ważniejsza dla scenariusza, należy zwiększyć próg. W ten sposób, za każdym razem, gdy zwrócisz odpowiedź, będzie to o wiele bardziej pewny siebie przypadek i znacznie bardziej prawdopodobne, że użytkownicy będą mogli uzyskać odpowiedź. W takim przypadku może skończyć się pozostawiając więcej pytań bez odpowiedzi. *Na przykład:* jeśli przekroczysz próg **70,** możesz przegapić kilka niejednoznacznych przykładów, takich jak "co to jest oszczędzanie i trenowanie?".

- Jeśli **pokrycie** (lub odwołanie) jest ważniejsze- i chcesz odpowiedzieć na jak najwięcej pytań, jak to możliwe, nawet jeśli istnieje tylko częściowy stosunek do pytania użytkownika- a następnie obniżyć próg. Oznacza to, że może być więcej przypadków, w których odpowiedź nie odpowiada na rzeczywiste zapytanie użytkownika, ale daje inną nieco powiązaną odpowiedź. *Na przykład:* jeśli próg **30**, możesz udzielić odpowiedzi na pytania, takie jak "Gdzie mogę edytować moją bazę wiedzy?"

> [!NOTE]
> Nowsze wersje programu QnA Maker zawierają ulepszenia logiki oceniania i mogą mieć wpływ na próg. Za każdym razem, gdy aktualizujesz usługę, w razie potrzeby należy przetestować i dostosować próg. Możesz sprawdzić wersję usługi QnA [tutaj](https://www.qnamaker.ai/UserSettings)i zobaczyć, jak uzyskać najnowsze aktualizacje [tutaj.](../How-To/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)

## <a name="set-threshold"></a>Ustaw próg

Ustaw wynik progu jako właściwość [właściwości właściwości generateanswer API JSON.](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) Oznacza to, że można ustawić go dla każdego wywołania GenerateAnswer.

W ramach bota ustaw wynik jako część obiektu opcji za pomocą [języka C#](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-c) lub [Node.js](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-nodejs).

## <a name="improve-confidence-scores"></a>Popraw wyniki zaufania
Aby poprawić wynik zaufania określonej odpowiedzi na zapytanie użytkownika, można dodać zapytanie użytkownika do bazy wiedzy jako pytanie alternatywne dotyczące tej odpowiedzi. Możesz również użyć [zmian wyrazów](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) bez uwzględniania wielkości liter, aby dodać synonimy do słów kluczowych w bazy wiedzy.


## <a name="similar-confidence-scores"></a>Podobne wyniki zaufania
Gdy wiele odpowiedzi mają podobny wynik zaufania, jest prawdopodobne, że kwerenda była zbyt ogólna i w związku z tym dopasowane z równym prawdopodobieństwem z wielu odpowiedzi. Postaraj się lepiej ustrukturyzować qna, aby każda jednostka QnA ma odrębny zamiar.


## <a name="confidence-score-differences-between-test-and-production"></a>Różnice w wskaźniku zaufania między testem a produkcją
Wynik zaufania odpowiedzi może zmienić się negatywnie między testem a opublikowaną wersją bazy wiedzy, nawet jeśli zawartość jest taka sama. Dzieje się tak, ponieważ zawartość testu i opublikowanej bazy wiedzy znajdują się w różnych indeksach usługi Azure Cognitive Search.

Indeks testu zawiera wszystkie pary QnA twoich baz wiedzy. Podczas wykonywania zapytań o indeks testu kwerenda ma zastosowanie do całego indeksu, a następnie wyniki są ograniczone do partycji dla tej konkretnej bazy wiedzy. Jeśli wyniki kwerendy testowej negatywnie wpływają na zdolność sprawdzania poprawności bazy wiedzy, można:
* zorganizuj swoją bazę wiedzy, korzystając z jednej z następujących czynności:
    * 1 zasób ograniczony do 1 KB: ogranicz pojedynczy zasób QnA (i wynikowy indeks testu usługi Azure Cognitive Search) do jednej bazy wiedzy.
    * 2 zasoby - 1 dla testu, 1 do produkcji: mają dwa zasoby QnA Maker, przy użyciu jednego do testowania (z własnymi indeksami testowymi i produkcyjnymi) i jednego dla produktu (również posiadającego własne indeksy testowe i produkcyjne)
* i zawsze używaj tych samych parametrów, takich jak **[góra](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)** podczas wykonywania zapytań zarówno o test, jak i bazę wiedzy o produkcji

Podczas publikowania bazy wiedzy zawartość pytania i odpowiedzi bazy wiedzy przenosi się z indeksu testów do indeksu produkcji w wyszukiwarce platformy Azure. Zobacz, jak działa operacja [publikowania.](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)

Jeśli masz bazę wiedzy w różnych regionach, każdy region używa własnego indeksu usługi Azure Cognitive Search. Ponieważ używane są różne indeksy, wyniki nie będą dokładnie takie same.


## <a name="no-match-found"></a>Nie znaleziono dopasowania
Gdy ranking nie znajdzie żadnego dobrego dopasowania, zwracany jest wynik zaufania 0,0 lub "Brak", a domyślna odpowiedź to "Nie znaleziono dobrego dopasowania w KB". Tę [domyślną odpowiedź](../How-To/metadata-generateanswer-usage.md) można zastąpić w kodzie bota lub aplikacji wywołującym punkt końcowy. Alternatywnie można również ustawić odpowiedź zastępowania na platformie Azure i spowoduje to zmianę wartości domyślnej dla wszystkich baz wiedzy wdrożonych w określonej usłudze QnA Maker.

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Najważniejsze wskazówki](./best-practices.md)


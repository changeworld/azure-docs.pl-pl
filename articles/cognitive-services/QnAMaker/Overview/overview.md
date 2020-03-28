---
title: Co to jest usługa QnA Maker?
titleSuffix: Azure Cognitive Services
description: QnA Maker to oparta na chmurze usługa NLP, która łatwo tworzy naturalną warstwę konwersacyjną nad danymi. Może służyć do znalezienia najbardziej odpowiedniej odpowiedzi dla danego języka naturalnego wprowadzania, z bazy wiedzy niestandardowej (KB) informacji.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 02/21/2020
ms.author: diberry
ms.openlocfilehash: 2863a086343b0a5d3bc69ae319dbe93d557bcb4a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80052927"
---
# <a name="what-is-the-qna-maker-service"></a>Co to jest usługa QnA Maker?

[!INCLUDE [TLS 1.2 enforcement](../../../../includes/cognitive-services-tls-announcement.md)]

QnA Maker to oparta na chmurze usługa przetwarzania języka naturalnego (NLP), która łatwo tworzy naturalną warstwę konwersacyjną nad danymi. Może służyć do znalezienia najbardziej odpowiedniej odpowiedzi dla danego języka naturalnego wprowadzania, z bazy wiedzy niestandardowej (KB) informacji.

Aplikacja kliencka dla QnA Maker to dowolna aplikacja konwersacyjna, która komunikuje się z użytkownikiem w języku naturalnym, aby odpowiedzieć na pytanie. Przykładami aplikacji klienckich są aplikacje mediów społecznościowych, czatboty i aplikacje klasyczne z funkcją rozpoznawania mowy.

## <a name="when-to-use-qna-maker"></a>Kiedy używać QnA Maker

* **Gdy masz informacje statyczne** - Użyj QnA Maker, gdy masz informacje statyczne w bazie wiedzy odpowiedzi. Ta baza wiedzy jest dostosowana do Twoich potrzeb, które zostały zbudowane z dokumentów, takich jak [pliki PDF i adresy URL.](../concepts/content-types.md)
* **Jeśli chcesz podać tę samą odpowiedź na żądanie, pytanie lub polecenie** — gdy różni użytkownicy przesyłają to samo pytanie, zwracana jest ta sama odpowiedź.
* **Jeśli chcesz filtrować informacje statyczne na podstawie metainformatycznych** — dodaj tagi [metadanych,](../how-to/metadata-generateanswer-usage.md) aby zapewnić dodatkowe opcje filtrowania odpowiednie dla użytkowników aplikacji klienckiej i informacji. Typowe informacje o metadanych obejmują [chit-chat,](../how-to/chit-chat-knowledge-base.md)typ lub format zawartości, cel zawartości i świeżość treści.
* **Jeśli chcesz zarządzać konwersacji bota, który zawiera informacje statyczne** - baza wiedzy pobiera tekst konwersacji użytkownika lub polecenia i odpowiedzi na to. Jeśli odpowiedź jest częścią wstępnie określonego przepływu konwersacji, reprezentowanego w bazie wiedzy z [kontekstem wielu kolejek,](../how-to/multiturn-conversation.md)bot może łatwo zapewnić ten przepływ.

## <a name="use-qna-maker-knowledge-base-in-a-chat-bot"></a>Korzystanie z bazy wiedzy QnA Maker w botie czatu

Po opublikowaniu bazy wiedzy QnA Maker aplikacja kliencka wysyła pytanie do punktu końcowego bazy wiedzy i otrzymuje wyniki jako odpowiedź JSON. Wspólną aplikacją kliencką dla QnA Maker jest czat bot.

![Zadaj botowi pytanie i uzyskaj odpowiedź z zawartości bazy wiedzy](../media/qnamaker-overview-learnabout/bot-chat-with-qnamaker.png)

|Krok|Akcja|
|:--|:--|
|1|Aplikacja kliencka wysyła _pytanie_ użytkownika (tekst własnymi słowami), "Jak programowo zaktualizować bazę wiedzy?" do punktu końcowego bazy wiedzy.|
|2|QnA Maker korzysta z wyszkolonej bazy wiedzy, aby zapewnić poprawną odpowiedź i wszelkie monity uzupełniające, które mogą być wykorzystane do uściślić wyszukiwanie najlepszej odpowiedzi. Funkcja QnA Maker zwraca odpowiedź w formacie JSON.|
|3|Aplikacja kliencka używa odpowiedzi JSON do podejmowania decyzji dotyczących sposobu kontynuowania konwersacji. Decyzje te mogą obejmować pokazanie najwyższej odpowiedzi i przedstawienie większej liczby opcji, aby udoskonalić poszukiwanie najlepszej odpowiedzi. |
|||

## <a name="what-is-a-knowledge-base"></a>Co to jest baza wiedzy?

QnA Maker [importuje twoje treści](../concepts/knowledge-base.md) do bazy wiedzy zestawów pytań i odpowiedzi. Proces importowania wyodrębnia informacje o relacji między częściami zawartości strukturalnej i półstrukturalną, aby sugerować relacje między zestawami pytań i odpowiedzi. Możesz edytować te zestawy pytań i odpowiedzi lub dodać nowe zestawy.

Treść zestawu pytań i odpowiedzi obejmuje:
* Wszystkie alternatywne formy pytania
* Znaczniki metadanych używane do filtrowania opcji odpowiedzi podczas wyszukiwania
* Monity o kontynuowanie wyszukiwania

![Przykładowe pytanie i odpowiedź z metadanymi](../media/qnamaker-overview-learnabout/example-question-and-answer-with-metadata.png)

Po opublikowaniu bazy wiedzy aplikacja kliencka wysyła pytanie użytkownika do punktu końcowego. Usługa QnA Maker przetwarza pytanie i odpowiada z najlepszą odpowiedzią.

## <a name="create-manage-and-publish-to-a-bot-without-code"></a>Tworzenie, zarządzanie i publikowanie na bot bez kodu

Portal QnA Maker zapewnia pełne doświadczenie tworzenia bazy wiedzy. Dokumenty można importować w ich bieżącej formie do bazy wiedzy. Dokumenty te (takie jak często zadawane pytania, instrukcja obsługi produktu, arkusz kalkulacyjny lub strona internetowa) są konwertowane na zestawy pytań i odpowiedzi. Każdy zestaw jest analizowany pod kątem monitów uzupełniających i połączony z innymi zestawami. Ostateczny format _znaczników_ obsługuje bogatą prezentację, w tym obrazy i linki.

Po edycji bazy wiedzy opublikuj bazę wiedzy na działającym [botie aplikacji Azure Web App](https://azure.microsoft.com/services/bot-service/) bez pisania kodu. Przetestuj bota w [witrynie Azure portal](https://portal.azure.com) lub pobierz i kontynuuj tworzenie.

## <a name="search-quality-and-ranking-provides-the-best-possible-answer"></a>Jakość wyszukiwania i ranking zapewnia najlepszą możliwą odpowiedź

System QnA Maker jest podejściem warstwowym. Dane są przechowywane w usłudze Azure Search, która służy również jako pierwsza warstwa rankingowa. Najlepsze wyniki wyszukiwania platformy Azure są następnie przekazywane za pośrednictwem modelu ponownego rankingu NLP firmy QnA Maker w celu uzyskania ostatecznych wyników i wskaźnika zaufania.

## <a name="qna-maker-improves-the-conversation-process"></a>QnA Maker usprawnia proces konwersacji

QnA Maker zapewnia wiele szybkich komunikatów i aktywne uczenie się, które pomogą Ci ulepszyć podstawowe zestawy pytań i odpowiedzi.

**Wielokrotne monity** dają możliwość łączenia par pytań i odpowiedzi. To połączenie umożliwia aplikacji klienckiej, aby zapewnić najwyższą odpowiedź i zawiera więcej pytań, aby uściślić wyszukiwanie ostatecznej odpowiedzi.

Po otrzymaniu pytań od użytkowników w opublikowanym punkcie końcowym baza wiedzy, program QnA Maker stosuje **aktywne uczenie się** do tych rzeczywistych pytań, aby zaproponować zmiany w bazie wiedzy w celu poprawy jakości.

## <a name="development-lifecycle"></a>Cykl projektowania oprogramowania

QnA Maker zapewnia tworzenie, szkolenie i publikowanie wraz z uprawnieniami do współpracy, aby zintegrować się z pełnym cyklem życia rozwoju.

> [!div class="mx-imgBorder"]
> ![Koncepcyjny obraz cyklu rozwoju](../media/qnamaker-overview-learnabout/development-cycle.png)


## <a name="how-do-i-start"></a>Jak rozpocząć?

**Krok 1:** Tworzenie zasobu programu QnA Maker w [witrynie Azure portal](https://portal.azure.com).

**Krok 2:** Tworzenie bazy wiedzy w portalu [QnA Maker.](https://www.qnamaker.ai) Dodaj [pliki i adresy URL,](../concepts/content-types.md) aby utworzyć bazę wiedzy.

**Krok 3:** Opublikuj swoją bazę wiedzy i testuj z niestandardowego punktu końcowego za pomocą [cURL lub Listonosza](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md).

**Krok 4:** Z aplikacji klienckiej programowo wywołaj punkt końcowy bazy wiedzy. Aplikacja kliencka przetwarza odpowiedź JSON, aby pokazać najlepszą odpowiedź dla użytkownika.

## <a name="next-steps"></a>Następne kroki
QnA Maker zapewnia wszystko, czego potrzebujesz do tworzenia, zarządzania i wdrażania niestandardowej bazy wiedzy.

> [!div class="nextstepaction"]
> [Przejrzyj najnowsze zmiany](../whats-new.md)

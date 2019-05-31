---
title: Co to jest słownikiem? — Niestandardowy w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Słownik jest wyrównany dokumentu, który określa listę frazy lub zdania (i tłumaczenia), który ma być zawsze Microsoft Translator do tłumaczenia w taki sam sposób. Słowniki są czasami skrót glosariuszu lub określenie zasad.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-pawal
ms.topic: conceptual
ms.openlocfilehash: 4bf112974befd7063b3da8e2b1c1dcbb7ad55608
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66385239"
---
# <a name="what-is-a-dictionary"></a>Co to jest słownikiem?

Słownik jest wyrównany pary dokumentów określa listę frazy lub zdania i ich odpowiednich tłumaczenia. Słownik w szkolenia, należy użyć Microsoft Translator do tłumaczenia zawsze wszystkie wystąpienia źródła frazy lub zdania, za pomocą tłumaczenia, podane w słowniku. Słowniki są czasami nazywane glosariuszu lub określenie zasad. Słownika można potraktować jako siłowe "Kopiuj i Zamień" do listy wszystkich warunków umowy.

Słowniki działają tylko dla projektów w parach języka, które mają w pełni obsługiwany system neuronowego tłumaczenia maszynowego (NMT) firmy Microsoft za ich. [Wyświetl pełną listę języków](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization).

## <a name="phrase-dictionary"></a>Słownik frazy
Po dołączeniu słownika frazy szkolenie modelu, dowolny wyraz lub frazę, wyświetlane jest tłumaczony w taki sposób, który określiłeś. Pozostała część zdanie jest tłumaczony w zwykły sposób. Słownik frazy służy do określania fraz, które nie powinien być tłumaczony, podając tę samą frazę nieprzetłumaczonych w pliku źródłowym i docelowym w słowniku.

## <a name="sentence-dictionary"></a>Słownik zdania
Słownik zdania pozwala określić dokładną docelowego Translacja zdania źródła. Aby wystąpiło dopasowanie słownika zdania całe zdanie przesłane musi odpowiadać wpis w słowniku źródła.  Jeśli tylko część zdanie jest zgodny, nie będzie zgodne z wpisu.  Po wykryciu dopasowania zostanie zwrócona docelowego wpisu słownika zdania.

## <a name="dictionary-only-trainings"></a>Szkolenia w trybie tylko do słownika
Możesz uczyć model przy użyciu tylko dane słownika. Aby to zrobić, wybierz słowniki dokumentu (lub wiele dokumentów słownika) czy chcesz dołączyć, a następnie naciśnij pozycję Utwórz model. Ponieważ jest to szkolenie tylko do słownika, istnieje nie minimalną liczbę zdań szkolenia wymagane. Model są zazwyczaj wykonywane szkolenia znacznie szybsze niż standardowe szkolenia.  Modele wynikowy będzie używać modeli podstawowych w Microsoft tłumaczenia dodając słowników, które wcześniej dodałeś.  Nie otrzymasz raport z testu.

>[!Note]
>Niestandardowe w usłudze Translator nie jak w zdaniu wyrównać pliki słownika tak ważne jest, aby były równej liczby wyrażeń źródłowych i docelowych / zdania w słowniku dokumentów i że dokładnie wyrównana.

## <a name="recommendations"></a>Zalecenia

- Słowniki są nie stanowi zastępstwa dla uczonego modelu przy użyciu danych szkoleniowych.  Słowniki zasadniczo Znajdź i Zamień słowa lub zdania.  Co system, ucz się od Twojego materiał szkoleniowy w pełnych zdań ogólnie jest lepszym rozwiązaniem niż przy użyciu słownika.
- Słownik frazy powinny być używane rzadko. Po frazy w obrębie zdania, kontekst, w tym zdaniu utraty lub ograniczone do tłumaczenia pozostałą część zdania. Wynik jest to, że podczas frazy lub programu word w zdaniu przekształci zgodnie ze słownika frazy, ogólną jakość tłumaczenia zdania często będzie słabsza.
- Słownik frazy dobrze się sprawdza rzeczowniki złożone, takich jak nazwy produktów ("Microsoft SQL Server"), prawidłowe nazwy ("miasto Hamburg") lub funkcji tego produktu ("Tabela przestawna"). Nie działa równie dobrze dla zleceń lub określeniem, ponieważ te są zwykle modulowany w źródle lub w języku docelowym. Należy unikać frazy wpisy słownika tylko rzeczowniki złożone.
- Podczas korzystania ze słownika, wielkość liter i znaków interpunkcyjnych w tłumaczenia odzwierciedlają wielkość liter i znaków interpunkcyjnych, podany w pliku docelowego. Wielkość liter i znaków interpunkcyjnych, są ignorowane podczas próby zidentyfikować są takie same Twoje zdanie danych wejściowych i zdania źródło w pliku słownika. Załóżmy na przykład, że szkoliliśmy anglojęzyczną systemowi hiszpański używane słownik tego określonego "miasto Hamburg" w pliku źródłowym, a "Ciudad de hamburg" w pliku docelowym. Jeśli jest to wymagane I tłumaczenie zdanie, które uwzględnione frazę "miasto Hamburg", następnie "miasto Hamburg" będzie pasował do pliku słownika dla wpisu "Miasto Hamburg", a mapującej do "Ciudad de hamburg" w mojej końcowego tłumaczenia.
- Słowa występuje więcej niż raz w pliku słownika, system zawsze będzie korzystać ostatni wpis podane. Słownika nie może zawierać wiele tłumaczenia tego samego wyrazu.

## <a name="next-steps"></a>Kolejne kroki

- Przeczytaj o [wskazówki dotyczące formatowania dokumentu](document-formats-naming-convention.md).

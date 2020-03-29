---
title: Co to jest słownik? - Tłumacz niestandardowy
titleSuffix: Azure Cognitive Services
description: Słownik jest wyrównanym dokumentem, który określa listę fraz lub zdań (i ich tłumaczenia), które zawsze mają tłumaczyć w ten sam sposób. Słowniki są czasami nazywane glosariuszami lub bazami terminowymi.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 36b449c4c4ca30eb658c9519ce8e870a4f1fab32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "71970737"
---
# <a name="what-is-a-dictionary"></a>Co to jest słownik?

Słownik jest wyrównaną parą dokumentów, która określa listę fraz lub zdań oraz odpowiadające im tłumaczenia. Użyj słownika w szkoleniu, jeśli chcesz, aby program Microsoft Translator zawsze tłumaczył wszystkie wystąpienia frazy źródłowej lub zdania przy użyciu tłumaczenia podanego w słowniku. Słowniki są czasami nazywane glosariuszami lub bazami terminowymi. Możesz myśleć o słowniku jako o brutalnej sile "kopiuj i zamieniaj" dla wszystkich terminów, które wymieniasz. Ponadto usługa Microsoft Custom Translator tworzy i korzysta z własnych słowników ogólnego przeznaczenia w celu poprawy jakości tłumaczenia. Jednak słownik dostarczony przez klienta ma precedens i zostanie przeszukany najpierw w celu odszukania słów lub zdań.

Słowniki działają tylko dla projektów w parach językowych, które mają w pełni obsługiwany ogólny model sieci neuronowej firmy Microsoft za nimi. [Wyświetl pełną listę języków](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization).

## <a name="phrase-dictionary"></a>Słownik fraz
Po dołączeniu słownika frazy do szkolenia modelu, każdy wyraz lub fraza na liście jest tłumaczony w określony sposób. Reszta zdania jest tłumaczona jak zwykle. Słownika fraz można użyć do określenia fraz, które nie powinny być tłumaczone przez podanie tej samej nieprzetłumaczonej frazy w pliku źródłowym i docelowym w słowniku.

## <a name="sentence-dictionary"></a>Słownik zdań
Słownik zdań umożliwia określenie dokładnego tłumaczenia docelowego dla zdania źródłowego. Aby doszło do dopasowania słownika zdań, całe przesłane zdanie musi być zgodne z wpisem słownika źródłowego.  Jeśli tylko część zdania jest zgodna, wpis nie będzie zgodny.  Po wykryciu dopasowania zostanie zwrócony wpis docelowy słownika zdań.

## <a name="dictionary-only-trainings"></a>Szkolenia tylko do słownika
Można trenować model przy użyciu tylko danych słownika. Aby to zrobić, wybierz tylko dokument słownika (lub wiele dokumentów słownika), które chcesz dołączyć, i naciśnij pozycję Utwórz model. Ponieważ jest to szkolenie tylko słownik, nie jest wymagana minimalna liczba zdań szkoleniowych. Model zazwyczaj ukończy szkolenie znacznie szybciej niż standardowe szkolenie.  Wynikowe modele będą używać modeli linii bazowych firmy Microsoft do tłumaczenia z dodatkiem dodanych słowników.  Nie otrzymasz raportu z testu.

>[!Note]
>Translator niestandardowy nie wyrównuje plików słownikowych, dlatego ważne jest, aby w dokumentach słownika istniała taka sama liczba fraz/zdań źródłowych i docelowych i aby były one dokładnie wyrównane.

## <a name="recommendations"></a>Zalecenia

- Słowniki nie zastępują szkolenia modelu przy użyciu danych szkoleniowych. Zaleca się, aby ich unikać i pozwolić systemowi uczyć się na podstawie danych szkoleniowych. Jednak gdy zdania lub rzeczowniki złożone muszą być renderowane w stanie spożyciu, należy użyć słownika.
- Słownik frazy powinien być używany oszczędnie. Należy więc pamiętać, że po zastąpieniu frazy w zdaniu kontekst w tym zdaniu jest tracony lub ograniczony do tłumaczenia reszty zdania. Rezultatem jest to, że podczas gdy wyrażenie lub słowo w zdaniu będzie tłumaczyć zgodnie z podanym słownikiem, ogólna jakość tłumaczenia zdania często cierpi.
- Słownik fraz działa dobrze w przypadku rzeczowników złożonych, takich jak nazwy produktów ("Microsoft SQL Server"), nazwy własne ("Miasto Hamburg") lub funkcje produktu ("tabela przestawna"). Nie działa równie dobrze dla zleceń lub przymiotników, ponieważ są one zazwyczaj bardzo odmieniane w źródle lub w języku docelowym. Najlepsze rozwiązania to unikanie wpisów słownika fraz dla niczego, ale rzeczowniki złożone.
- Podczas korzystania ze słownika fraz ważne są wielkie litery i znaki interpunkcyjne. Wpisy słownika będą pasować tylko wyrazy i frazy w zdaniu wejściowym, które używają dokładnie tej samej litery i znaków interpunkcyjnych, jak określono w pliku słownika źródłowego. Również tłumaczenia będą odzwierciedlać wielkość liter i znaki interpunkcyjne podane w pliku słownika docelowego. Na przykład, jeśli przeszkolony angielski do hiszpańskiego systemu, który używa słownika fraz, który określa "US" w pliku źródłowym i "EE. uu". w pliku docelowym. Jeśli zażądasz tłumaczenia zdania, które zawiera słowo "nas" (nie kapitalizowane), nie będzie to zgodne ze słownikiem. Jeśli jednak zażądasz tłumaczenia zdania, które zawiera słowo "US" (kapitalizowany), będzie ono zgodne ze słownikiem, a tłumaczenie będzie zawierało "EE. uu". Należy zauważyć, że wielkość liter i znaki interpunkcyjne w tłumaczeniu mogą być inne niż określone w pliku docelowym słownika i mogą różnić się od wielkich liter i znaków interpunkcyjnych w źródle. Jest zgodna z zasadami języka docelowego.
- Jeśli słowo pojawia się więcej niż raz w pliku słownika, system zawsze użyje ostatniego wpisu. W związku z tym słownik nie powinien zawierać wielu tłumaczeń tego samego słowa.

## <a name="next-steps"></a>Następne kroki

- Przeczytaj o [wskazówkach dotyczących formatów dokumentów](document-formats-naming-convention.md).

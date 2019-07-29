---
title: Co to jest słownik? — Niestandardowy w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Słownik jest dokumentem wyrównanym, który określa listę fraz lub zdań (i ich tłumaczenia), które są zawsze potrzebne do tłumaczenia usługi Microsoft Translator w taki sam sposób. Słowniki są czasami nazywane również bazami Glossaries lub terminami.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 5103526956b5041771a1d8e4abb5e8800b971059
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595370"
---
# <a name="what-is-a-dictionary"></a>Co to jest słownik?

Słownik jest wyrównanym parę dokumentów, które określają listę fraz lub zdań i odpowiadające im tłumaczenia. Użyj słownika w ramach szkolenia, jeśli chcesz, aby program Microsoft Translator zawsze przetłumaczy wszystkie wystąpienia frazy źródłowej lub zdania przy użyciu tłumaczenia podanego w słowniku. Słowniki są czasami nazywane glossariesami lub terminami. Słownik można traktować jako "Kopiuj i Zamień" dla wszystkich wystawianych terminów.

Słowniki działają tylko w przypadku projektów w parach języka, które mają w pełni obsługiwany system Microsoft neuronowych Machine Translation (NMT). [Zapoznaj się z pełną listą języków](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization).

## <a name="phrase-dictionary"></a>Słownik fraz
Po dołączeniu słownika wyrazów w ramach szkolenia modelu wszystkie wymienione wyrazy lub frazy są tłumaczone w określony sposób. Pozostałe zdanie jest tłumaczone w zwykły sposób. Możesz użyć słownika frazy, aby określić frazy, które nie powinny być tłumaczone przez podanie tej samej nieprzetłumaczonej frazy w pliku źródłowym i docelowym w słowniku.

## <a name="sentence-dictionary"></a>Słownik zdań
Słownik zdań umożliwia określenie dokładnego tłumaczenia docelowego dla zdania źródłowego. Aby można było dopasować słownik zdań, całe przesłane zdanie musi być zgodne z wpisem słownika źródłowego.  Jeśli tylko część zdania jest zgodna, wpis nie będzie zgodny.  Po wykryciu dopasowania zostanie zwrócony wpis docelowy słownika zdań.

## <a name="dictionary-only-trainings"></a>Szkolenia dotyczące tylko słownika
Możesz nauczyć model przy użyciu tylko danych słownika. W tym celu zaznacz tylko dokument słownika (lub wiele dokumentów słownika), które chcesz dołączyć, i naciśnij pozycję Utwórz model. Ponieważ jest to szkolenie tylko do słownika, nie jest wymagana minimalna liczba zdań szkoleniowych. Model zwykle kończy szkolenia znacznie szybciej niż w przypadku standardowego szkolenia.  Modele powstające będą używać modeli bazowych firmy Microsoft do tłumaczenia z dodaniem dodanych słowników.  Nie otrzymasz raportu testowego.

>[!Note]
>W przypadku translatora niestandardowego nie są wyrównania plików słowników, dlatego ważne jest, aby w dokumentach słownika była równa Liczba zwrotów źródłowych i docelowych, a także dokładnie wyrównane.

## <a name="recommendations"></a>Zalecenia

- Słowniki nie są zamiennikiem przeszkolonego modelu zawierającego dane szkoleniowe.  Słowniki zasadniczo znajdują i zamieniają wyrazy lub zdania.  Umożliwienie systemowi uczenia się z materiału szkoleniowego w pełnych zdaniach jest lepszym rozwiązaniem niż używanie słownika.
- Słownik wyrazów powinien być oszczędnie używany. Gdy fraza w zdaniu jest zastępowana, kontekst w tym zdaniu zostanie utracony lub ograniczony do przetłumaczenia reszty zdania. Wynikiem tego jest to, że podczas gdy fraza lub wyraz w zdaniu zostanie przetłumaczona zgodnie ze słownikiem fraz, ogólna jakość tłumaczenia zdania często się pogorszy.
- Słownik fraz dobrze sprawdza się w przypadku niezłożonej rzeczowników, takich jak nazwy produktów ("Microsoft SQL Server"), odpowiednie nazwy ("miasto Hamburg") lub funkcje produktu ("tabela przestawna"). Nie działa równie dobrze w przypadku czasowników lub przymiotników, ponieważ są zwykle wysoce oddzielone w źródle lub w języku docelowym. Unikaj wpisów słownika frazy dla niczego, ale rzeczowników złożonych.
- W przypadku używania słownika, litery i interpunkcja w tłumaczeniach będą odzwierciedlać wielkie litery i znaki interpunkcyjne podane w pliku docelowym. Podczas próby zidentyfikowania dopasowań między zdanie wejściowe i zdaniami źródłowymi w pliku słownika są ignorowane wielkie litery i znaki interpunkcyjne. Załóżmy na przykład, że przeszkolony jest język angielski do hiszpańskiego systemu, który używa słownika, który określił "miasto Hamburg" w pliku źródłowym i "Ciudad de Hamburg" w pliku docelowym. Jeśli zażądano tłumaczenia zdania zawierającego frazę "miasto Hamburg", to "miasto Hamburg" będzie pasować do mojego pliku słownika dla wpisu "miasto Hamburg" i zostanie zamapowana na "Ciudad de Hamburg" w końcowym tłumaczeniu.
- Jeśli słowo pojawia się więcej niż raz w pliku słownika, system zawsze będzie używać ostatniego dostarczonego wpisu. Słownik nie powinien zawierać wielu tłumaczeń tego samego wyrazu.

## <a name="next-steps"></a>Następne kroki

- Przeczytaj o [wytycznych dotyczących formatów dokumentów](document-formats-naming-convention.md).

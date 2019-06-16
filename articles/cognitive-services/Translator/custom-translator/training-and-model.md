---
title: Co to jest, szkolenia i modelu? — Niestandardowy w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Model jest systemu, która zapewnia translacji dla pary określonego języka. Wynikiem pomyślnym szkolenia jest model. Podczas uczenia modelu, trzy wzajemnie wykluczających się zestawy danych są wymagane, zestaw danych szkoleniowych, dostosowywania zestawu danych i testowanie zestawu danych.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-pawal
ms.openlocfilehash: 530e87a84899b9659acd19f90c7c30ad3da3e7ba
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66382309"
---
# <a name="what-are-trainings-and-models"></a>Jakie są modele i środkom?

Model jest systemu, która zapewnia translacji dla pary określonego języka.
Wynikiem pomyślnym szkolenia jest model. Podczas uczenia modelu, wymagane są trzy wzajemnie wykluczających się zestawów danych: szkolenia zestawu danych dostosowywania zestawu danych i testowanie zestawu danych. Można również podać dane słownika.

Jeśli tylko dane szkoleniowe podano podczas kolejkowania szkoleniowe, niestandardowe w usłudze Translator automatycznie zostanie złóż, dostosowywania i testowania zestawów danych. Wówczas wykluczyć 5000 zdania z danych szkoleniowych i 2500 każdego można złożyć dostrajanie i zestawów testów.

## <a name="training-dataset-for-custom-translator"></a>Zestaw danych szkoleniowych niestandardowe w usłudze Translator

Dokumenty objęte zestaw szkoleniowy są używane przez niestandardowe w usłudze Translator jako podstawy do tworzenia modelu. Podczas wykonywania szkolenia zdań, które znajdują się w tych dokumentach są wyrównane do (lub sparowanych). Możesz korzystać z ochroną praw w tworzenie zestawu dokumentów szkolenia. Możesz dołączyć dokumenty, które Twoim zdaniem są istotne stycznej w jednym modelu. Wyklucz je ponownie w innej, aby zobaczyć wpływ na [BELEU (Understudy dwujęzyczny oceny) wynik](what-is-bleu-score.md). Tak długo, jak możesz zachować dostosowywania zestawu i stały zestaw testów, możesz eksperymentować w skład zestawu szkoleniowego. To podejście jest efektywny sposób modyfikowania jakość tłumaczenia systemu.

Można uruchomić wiele szkoleniach w obrębie projektu i porównaj [wyniki BELEU](what-is-bleu-score.md) we wszystkich przebiegów szkoleniowych. Po uruchomieniu wielu szkoleniach dla porównania, upewnij się, w tej samej dostrajania / dane testowe określono każdorazowo. Ponadto upewnij się, że również sprawdzić ręcznie w wynikach ["Testowanie"](how-to-view-system-test-results.md) kartę.

## <a name="tuning-dataset-for-custom-translator"></a>Dostrajanie zestawu danych dla niestandardowych w usłudze Translator

Równoległe dokumenty zawarte w tym zestawie są używane przez niestandardowe w usłudze Translator należy dostosować system tłumaczeń, aby uzyskać optymalne wyniki.

Dostrajania zestaw jest używany podczas szkolenia dostosowanie wszystkich parametrów i wagi systemu tłumaczenia optymalnej wartości. Wybierz usługi dostrajania zestaw dokładnie: dostosowywania zestawu powinna być reprezentatywna dla zawartości dokumentów, których zamierzasz w przyszłości tłumaczenie. Zestaw dostrajania ma poważny wpływ na jakość tłumaczenia utworzone. Dostrajanie umożliwia systemowi tłumaczenia Podaj tłumaczenia, które znajdują się najbliżej przykłady podane w zestawie danych dostosowywania. Ponad 2500 zdania jako dostosowywania zestawu nie jest konieczne. Jakości tłumaczenia optymalne zalecane jest ręcznie wybrać zestaw dostrajania przez wybranie najbardziej reprezentatywnego wyboru zdań.

Podczas tworzenia zestawu dostrajania, wybierz zdań, które są zrozumiałe i reprezentatywnej długość przyszłych zdań, które oczekują na tłumaczenie. Należy również wybrać zdań, które mają słów i fraz, które chcesz przetłumaczyć w przybliżony dystrybucji, który powinien być w przyszłości tłumaczenia. W praktyce długość zdania, 8-18 słów dadzą najlepsze wyniki, ponieważ te zdania zawiera wystarczający kontekst, aby pokazać zmienną modulacją i podaj długości frazę, która jest znaczący, nie będąc zbyt skomplikowana.

Dobry opis typu zdania służące do dostosowywania zestawu jest prose: rzeczywiste fluent zdań. Nie komórki tabeli, nie poems, nie listę elementów, nie tylko znak interpunkcyjny lub liczb w zdaniu - regularne języka.

Jeśli ręcznie wybierzesz dostosowywania zestawu danych, go nie powinna mieć żadnego z tych samych zdania jako dane szkolenia i testowania. Dostrajania zestaw ma znaczący wpływ na jakość tłumaczenia - rozważnych zdania.

Jeśli nie masz pewności, co do wyboru dla zestawu dostrajania, po prostu zaznacz zestaw szkoleniowy i pozwól wybierz zestaw dostrajania dla Ciebie niestandardowego w usłudze Translator. Jeśli umożliwisz Translator niestandardowe, wybierz zestaw dostrajania automatycznego, spowoduje to losowy podzbiór zdania ze swoich dokumentów dwujęzyczny szkolenia i Wyklucz te zdania z samych materiałów szkoleniowych.

## <a name="testing-dataset-for-custom-translator"></a>Testowanie zestawu danych dla niestandardowych w usłudze Translator

Równoległe dokumenty zawarte w zestawie testów są używane do obliczenia wyniku BELEU (Understudy dwujęzyczny oceny). Ten wynik wskazuje jakość tłumaczenia systemu. Ten wynik faktycznie informuje, jak blisko tłumaczenia wykonywane przez system tłumaczeń wynikające z to szkolenie dopasowania zdań odwołanie do zestawu danych testowych.

Wynik BELEU jest pomiar różnica między automatycznego tłumaczenia i tłumaczenie odwołania. Jego wartość z zakresu od 0 do 100. Wynik 0 wskazuje, czy nie jednowyrazowego odwołanie jest wyświetlana w tłumaczenia. Wynikiem 100 oznacza, że automatycznego tłumaczenia dokładnie odpowiada odwołanie: samego wyrazu jest dokładnie tym samym położeniu. Otrzymany wynik jest średnią ocenę BELEU dla wszystkich zdań zestawu testów.

Zestaw testów powinny obejmować równoległe dokumentów, których najbardziej pożądane tłumaczenia odpowiedniego zdań języka źródłowego w parze zdania język docelowy. Można użyć takich samych kryteriów, używane do tworzenia zestawu dostrajania. Zestaw testów ma jednak żadnego wpływu na jakość system tłumaczeń. Służy wyłącznie do generowania oceny BELEU dla Ciebie i nic innego.

Nie musisz ponad 2500 zdania jako zestawu testowego. Jeśli umożliwisz system automatycznie wybierz zestaw testów, wówczas losowy podzbiór zdania ze swoich dokumentów dwujęzyczny szkolenia i Wyklucz te zdania z samych materiałów szkoleniowych.

Wyświetl tłumaczenia niestandardowych zestawu testowego i porównaj je z tłumaczenia podane w zestawie testów, przechodząc do karty testu w obrębie modelu.

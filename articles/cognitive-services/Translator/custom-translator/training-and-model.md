---
title: Co to jest szkolenie i model? - Tłumacz niestandardowy
titleSuffix: Azure Cognitive Services
description: Model jest systemem, który zapewnia tłumaczenie dla określonej pary języków. Wynikiem udanego szkolenia jest model. Podczas szkolenia modelu, trzy wzajemnie wykluczające się zestawy danych są wymagane zestaw danych szkolenia, dostrajanie zestawu danych i testowania zestawu danych.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: swmachan
ms.openlocfilehash: 71f1e3f460fa58b999af0a60c8cffa90c8ac8cd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219457"
---
# <a name="what-are-trainings-and-models"></a>Czym są szkolenia i modele?

Model jest systemem, który zapewnia tłumaczenie dla określonej pary języków.
Wynikiem udanego szkolenia jest model. Podczas szkolenia modelu wymagane są trzy wzajemnie wykluczające się typy dokumentów: szkolenie, dostrajanie i testowanie. Można również podać typ dokumentu słownika. Odnoszą się do [wyrównania zdania](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/sentence-alignment#suggested-minimum-number-of-sentences).

Jeśli podczas kolejkowania szkolenia są dostarczane tylko dane szkoleniowe, usługa Custom Translator automatycznie zmontuje dane dostrajania i testowania. Użyje losowego podzbioru zdań z dokumentów szkoleniowych i wykluczy te zdania z samych danych szkoleniowych.

## <a name="training-document-type-for-custom-translator"></a>Typ dokumentu szkoleniowego dla translatora niestandardowego

Dokumenty zawarte w zestawie szkoleniowym są używane przez tłumacza niestandardowego jako podstawa do tworzenia modelu. Podczas wykonywania szkolenia zdania, które są obecne w tych dokumentach są wyrównane (lub sparowane). Możesz podjąć wolności w komponowaniu zestawu dokumentów szkoleniowych. Dokumenty, które uważasz za mające znaczenie styczne, można dołączyć do jednego modelu. Ponownie wykluczyć je w innym, aby zobaczyć wpływ w [BLEU (Dwujęzyczna Ocena Understudy) wynik](what-is-bleu-score.md). Tak długo, jak zachować zestaw strojenia i zestaw testowy stały, nie krępuj się eksperymentować ze składem zestawu treningowego. Takie podejście jest skutecznym sposobem modyfikowania jakości systemu tłumaczenia.

Można uruchomić wiele szkoleń w ramach projektu i porównać [wyniki BLEU](what-is-bleu-score.md) we wszystkich przebiegach szkoleniowych. Podczas uruchamiania wielu szkoleń do porównania, upewnij się, że te same dane dostrajania / testu jest określony za każdym razem. Upewnij się również, aby sprawdzić wyniki ręcznie w zakładce ["Testowanie".](how-to-view-system-test-results.md)

## <a name="tuning-document-type-for-custom-translator"></a>Dostrajanie typu dokumentu dla translatora niestandardowego

Dokumenty równoległe zawarte w tym zestawie są używane przez tłumacza niestandardowego do dostrajania systemu tłumaczenia w celu uzyskania optymalnych wyników.

Dane strojenia są używane podczas treningu w celu dostosowania wszystkich parametrów i wag systemu translacji do wartości optymalnych. Starannie dostrajając dane: dane dostrajające powinny być reprezentatywne dla treści dokumentów, które zamierzasz przetłumaczyć w przyszłości. Dane tuningowe mają duży wpływ na jakość produkowanych tłumaczeń. Dostrajanie umożliwia systemowi tłumaczenia, aby zapewnić tłumaczenia, które są najbliżej przykładów, które można podać w danych dostrajania. Nie potrzebujesz więcej niż 2500 zdań w danych strojenia. Aby uzyskać optymalną jakość tłumaczenia, zaleca się ręczne wybranie zestawu strojenia, wybierając najbardziej reprezentatywny wybór zdań.

Podczas tworzenia zestawu strojenia wybierz zdania, które są znaczącą i reprezentatywną długością przyszłych zdań, które mają być przetłumaczone. Należy również wybrać zdania, które mają słowa i frazy, które zamierzasz przetłumaczyć w przybliżonym dystrybucji, której oczekujesz w przyszłych tłumaczeniach. W praktyce długość zdania od 7 do 10 słów przyniesie najlepsze wyniki, ponieważ zdania te zawierają wystarczający kontekst, aby pokazać przegięcia i zapewnić długość wyrażenia, która jest znacząca, bez zbyt skomplikowanego.

Dobrym opisem typu zdań do użycia w zestawie strojenia jest proza: rzeczywiste płynne zdania. Nie komórki tabeli, nie wiersze, nie listy rzeczy, nie tylko interpunkcji lub liczb w zdaniu - zwykły język.

Jeśli ręcznie wybierzesz dane dostrajania, nie powinny mieć żadnych z tych samych zdań, co dane szkoleniowe i testowe. Dane strojenia ma znaczący wpływ na jakość tłumaczeń - starannie wybrać zdania.

Jeśli nie masz pewności, co wybrać dla danych dostrajania, po prostu wybierz dane szkoleniowe i pozwól translatorowi niestandardowemu wybrać dane dostrajania. Po umożliwieniu translatora niestandardowego automatycznie wybrać dane dostrajania, użyje losowego podzbioru zdań z dwujęzycznych dokumentów szkoleniowych i wykluczyć te zdania z samego materiału szkoleniowego.

## <a name="testing-dataset-for-custom-translator"></a>Testowanie zestawu danych dla usługi Translator niestandardowy

Równoległe dokumenty zawarte w zestawie testowym są używane do obliczania wyniku BLEU (Dwujęzyczne badanie wstępne). Ten wynik wskazuje jakość systemu tłumaczenia. Ten wynik faktycznie informuje, jak ściśle tłumaczenia wykonane przez system tłumaczenia wynikające z tego szkolenia odpowiadają zdaniach referencyjnych w zestawie danych testowych.

Wynik BLEU jest miarą różnicy między automatycznym tłumaczeniem a tłumaczeniem referencyjnym. Jego wartość waha się od 0 do 100. Wynik 0 oznacza, że w tłumaczeniu nie pojawia się ani jedno słowo odwołania. Wynik 100 oznacza, że automatyczne tłumaczenie dokładnie odpowiada odwołaniu: to samo słowo znajduje się dokładnie w tej samej pozycji. Wynik, który otrzymujesz jest średnia wyniku BLEU dla wszystkich zdań danych testowych.

Dane testowe powinny zawierać dokumenty równoległe, w których zdania języka docelowego są najbardziej pożądanymi tłumaczeniami odpowiadających im zdań języka źródłowego w parze źródło-obiekt. Można użyć tych samych kryteriów, które zostały użyte do tworzenia danych dostrajania. Jednak dane testowe nie mają wpływu na jakość systemu tłumaczenia. Jest on używany wyłącznie do generowania wyniku BLEU dla Ciebie.

Nie potrzebujesz więcej niż 2500 zdań jako danych testowych. Gdy system automatycznie wybierze zestaw testowy, użyje losowego podzbioru zdań z dwujęzycznych dokumentów szkoleniowych i wykluczy te zdania z samego materiału szkoleniowego.

Można wyświetlić niestandardowe tłumaczenia zestawu testów i porównać je z tłumaczeniami dostarczonymi w zestawie testów, przechodząc do karty testu w modelu.

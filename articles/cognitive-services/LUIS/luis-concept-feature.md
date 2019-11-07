---
title: Funkcje — LUIS
titleSuffix: Azure Cognitive Services
description: Dodaj funkcje do modelu języka, aby przedstawić wskazówki dotyczące sposobu rozpoznawania danych wejściowych, które mają być oznaczone etykietami lub klasyfikacją.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: diberry
ms.openlocfilehash: 0cab6eb38459a632f1e7bd1a21e6a7251d33f683
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647414"
---
# <a name="machine-learned-features"></a>Funkcje uczenia maszynowego 

W uczeniu maszynowym *Funkcja* jest odróżnianą cechą lub atrybutem danych, które system obserwuje &. W Language Understanding (LUIS) funkcja opisuje i wyjaśnia, co jest istotne dla Twoich intencji i jednostek.

W [portalu Luis w wersji zapoznawczej](https://preview.luis.ai)funkcje są _deskryptorami_ , ponieważ są używane do _opisywania_ zamiaru lub jednostki.  

## <a name="features-_descriptors_-in-language-understanding"></a>Funkcje (_deskryptory_) w Language Understanding

Funkcje, znane także jako deskryptory, opisują wskazówki ułatwiające Language Understanding identyfikowanie przykładu wyrażenia długości. Funkcje obejmują: 

* Lista fraz jako funkcja dla intencji lub jednostek
* Jednostki jako funkcje dla intencji lub jednostek

Funkcje powinny być traktowane jako niezbędna część schematu do dekompozycji modelu. 

## <a name="what-is-a-phrase-list"></a>Co to jest lista fraz

Lista fraz jest listą wyrazów, fraz, cyfr lub innych znaków, które ułatwiają zidentyfikowanie koncepcji, którą próbujesz zidentyfikować. Na liście nie jest rozróżniana wielkość liter. 

## <a name="when-to-use-a-phrase-list"></a>Kiedy używać listy fraz

Za pomocą listy fraz LUIS traktuje kontekst i generalizacje w celu zidentyfikowania elementów, które są podobne do, ale nie dokładnego dopasowania tekstu. Jeśli potrzebujesz aplikacji LUIS, aby móc uogólniać i identyfikować nowe elementy, Użyj listy fraz. 

Aby można było rozpoznać nowe wystąpienia, takie jak harmonogram spotkań, który powinien rozpoznawać nazwy nowych kontaktów lub aplikację spisu, która powinna rozpoznawać nowe produkty, Zacznij od jednostki, na której nauczy się Uczenie maszynowe. Następnie utwórz listę zwrotów ułatwiającą LUIS znalezienie wyrazów o podobnym znaczeniu. W tej frazie przedstawiono wskazówki LUIS do rozpoznawania przykładów poprzez dodanie dodatkowego znaczenia do wartości tych wyrazów. 

Listy fraz są podobne do słownictwa specyficznego dla domeny, który ułatwia zwiększenie jakości wiedzy o intencjach i jednostkach. 

## <a name="considerations-when-using-a-phrase-list"></a>Zagadnienia dotyczące korzystania z listy fraz

Lista fraz jest domyślnie stosowana do wszystkich modeli w aplikacji. To działanie będzie działało dla list fraz, które mogą przekroczyć wszystkie intencje i jednostki. W celu odtworzenia listy wyrazów należy zastosować tylko do modeli, do których odnosi się. 

Jeśli utworzysz listę fraz (domyślnie utworzoną globalnie), później Zastosuj ją jako deskryptor (funkcję) do określonego modelu, zostanie ona usunięta z innych modeli. To usunięcie dodaje istotność do listy fraz dla modelu, do którego jest stosowana, pomagając poprawić dokładność zapewnianą przez ten model. 

Flaga `enabledForAllModels` kontroluje ten zakres modelu w interfejsie API. 

<a name="how-to-use-phrase-lists"></a>

### <a name="how-to-use-a-phrase-list"></a>Jak używać listy fraz

[Utwórz listę list fraz](luis-how-to-add-features.md) , gdy zamiara lub jednostka ma słowa lub frazy, które są ważne, takie jak:

* warunki branżowe
* żargonu
* skróty
* Język specyficzny dla firmy
* Język pochodzący z innego języka, ale często używany w aplikacji
* kluczowe słowa i frazy w przykładowym wyrażenia długości

**Nie** należy dodawać każdego wyrazu lub frazy. Zamiast tego Dodaj kilka wyrazów lub fraz jednocześnie, a następnie ponów próbę i Opublikuj. Gdy lista rośnie wraz z upływem czasu, niektóre terminy mogą zawierać wiele form (synonimy). Podziel je na inną listę. 

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="when-to-use-an-entity-as-a-feature"></a>Kiedy używać jednostki jako funkcji 

Jednostkę można dodać jako funkcję w celu zamiaru lub poziomu jednostki. 

### <a name="entity-as-a-feature-to-an-intent"></a>Jednostka jako funkcja do celu

Dodaj jednostkę jako deskryptor (funkcję) do celu, gdy wykrycie tego obiektu jest istotne dla zamiaru.

Na przykład, jeśli zachodzi taka potrzeba dla rezerwacji, a jednostka jest informacji o biletach (takich jak liczba miejsc, pochodzenia i miejsca przeznaczenia), znalezienie jednostki informacji o biletach powinno spowodować dodanie wagi do przewidywania zamiaru lotów do książki. 

### <a name="entity-as-a-feature-to-another-entity"></a>Jednostka jako funkcja innej jednostki

Jednostkę (A) należy dodać jako funkcję do innej jednostki (B), gdy wykrycie tej jednostki (A) jest znaczące dla (B).

Na przykład jeśli wykryto jednostkę ulica (A), a następnie znalezienie adresu ulicy (A) powoduje dodanie wagi do prognozowania jednostki adres wysyłkowy (B). 

## <a name="best-practices"></a>Najlepsze praktyki
Poznaj [najlepsze rozwiązania](luis-concept-best-practices.md).

## <a name="next-steps"></a>Następne kroki

Zobacz [Dodawanie funkcji](luis-how-to-add-features.md) , aby dowiedzieć się więcej na temat dodawania funkcji do aplikacji Luis.
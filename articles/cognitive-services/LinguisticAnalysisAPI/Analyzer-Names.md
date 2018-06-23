---
title: Analizator struktury w interfejsie API językową analiza | Dokumentacja firmy Microsoft
description: Dowiedz się, jak językowe interfejsu API analizy używa jej nazewnictwa struktury analizatorów, aby umożliwić precyzyjnego.
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 03/23/2016
ms.author: lesun
ms.openlocfilehash: 2729b7126e82862660fc8e1a995cc87ae996ea03
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346997"
---
# <a name="analyzer-names"></a>Analizator nazwy

Używamy nieco skomplikowane struktura nazewnictwa dla analizatorów umożliwia zarówno elastyczność na analizatory i dokładność zrozumieć, co oznacza nazwę.
Analizator nazwy składają się z czterech części: ID, rodzaj specyfikację i implementację.
Rola każdego składnika jest zdefiniowana poniżej.

## <a name="id"></a>ID
Po pierwsze analizator ma unikatowy identyfikator; Identyfikator GUID.
Te identyfikatory GUID należy zmienić stosunkowo rzadko, ale są jedynym sposobem, aby jednoznacznie opisują określonego analizatora.

## <a name="kind"></a>Rodzaj
Następnie jest każdy analizator **rodzaj**.
Definiuje bardzo ogólnych określeń typu analizy zwrócił i unikatowo należy zdefiniować struktury danych używany do reprezentowania tej analizy.
Obecnie istnieją trzy różne typy:
 - [Tokeny](Sentences-and-Tokens.md)
 - [Położenie znaczników](Pos-Tagging.md)
 - [Wyborczy drzewa](constituency-parsing.md)

## <a name="specification"></a>Specyfikacja
W ramach danego typu jednak różnych ekspertów może nie zgadzają się w sposób można analizować określonego zjawisko.
W odróżnieniu od języków programowania nie obowiązuje nie wyczyść i dokładne definicja jak to robić.

Na przykład wyobrazić sobie próbowaliśmy znaleźć tokeny w języku angielskim zdaniu "On nie Przejdź."
W szczególności należy wziąć pod uwagę ciąg "nie".
Interpretacja możliwe jest, że to powinien zostać podzielony na dwa tokeny: "czy" i "nie".
Następnie alternatywnych zdania "on nie się", czy mają ten sam zestaw tokenów.
Inną możliwością jest oznacza, że powinna zostać podzielona na tokeny "czy" i "e".
Token to drugie nie będą zwykle interpretowane jako wyraz, ale takie podejście zachowuje więcej informacji na temat powierzchni ciąg, który czasami mogą być przydatne.
Lub może tego zmniejszenia należy traktować jako jednego wyrazu.

Niezależnie od tego wyboru staje się, że zawsze należy wybór.
Jest to dokładnie rolę **specyfikacji**: aby zdecydować, co powinna być poprawna reprezentacja.

Wyniki analizatora można dość porównywać tylko do danych, który jest zgodny ze specyfikacją tego samego.

## <a name="implementation"></a>Wdrażanie

Często istnieje wiele modeli, które mają na celu osiągnięcie takie same wyniki, ale o różnych parametrów.
Jeden model może być szybsza jednak mniej dokładne; inny może mieć różne zależność.

**Implementacji** część nazwy analizatora służy do identyfikowania tego typu danych, tak, aby użytkownicy można wybrać najbardziej odpowiedni analizatora do swoich potrzeb.

---
title: Analizator struktury - interfejsu API analizy językowej
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak struktury nazewnictwa językowej analizatora interfejsu API analizy umożliwia precyzyjnego.
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 03/23/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: c989f1115bc5a85bf09270c553ac1cb51bb4f170
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65954709"
---
# <a name="analyzer-names"></a>Nazwy analizatorów

> [!IMPORTANT]
> Wersja zapoznawcza analizy językowej została wycofana 9 sierpnia 2018 r. W celu przetwarzania i analizy tekstu zalecamy korzystanie z [modułów analizy tekstu w usłudze Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics).

Używamy nieco skomplikowane struktura nazewnictwa analizatorów, aby zezwalał na elastyczność, zarówno na analizatory i dokładność zrozumieć, co oznacza nazwę.
Nazwy analizatorów składają się z czterech części: identyfikator, rodzaj, Specyfikacja i implementację.
Rola każdego składnika jest zdefiniowana poniżej.

## <a name="id"></a>ID
Po pierwsze analizator ma unikatowy identyfikator; Identyfikator GUID.
Te identyfikatory GUID należy zmienić stosunkowo rzadko, ale są jedynym sposobem, aby jednoznacznie opisują określonego analizatora.

## <a name="kind"></a>rodzaj
Następnie każdy analyzer jest **rodzaj**.
Definiuje warunki bardzo ogólny typ analizy zwracane, a także jednoznacznie należy zdefiniować struktury danych, używany do reprezentowania tej analizy.
Obecnie istnieją trzy różne typy:
 - [Tokeny](Sentences-and-Tokens.md)
 - [Tagi części Przemowy](Pos-Tagging.md)
 - [Drzewo fraz](constituency-parsing.md)

## <a name="specification"></a>Specyfikacja
W ramach danego typu jednak różnych ekspertami może nie zgadzają się na jak określonego zjawisko powinno być analizowane.
W odróżnieniu od języków programowania Brak ma teraz bardziej czytelne i dokładne definicji jak to robić.

Na przykład załóżmy, że próby znalezienia tokeny w języku angielskim zdaniu "Nie przejdą."
W szczególności należy wziąć pod uwagę ciąg "nie".
Interpretacja możliwe jest, że to można podzielić na dwa tokeny: "czy" i "not".
Wówczas alternatywnych zdania "one nie się" będzie miała ten sam zestaw tokenów.
Inną możliwością jest powiedzieć, że powinna zostać podzielony na tokeny "czy" i "e".
Ostatni token nie się na ogół za wyrazu, ale takie podejście zachowuje więcej informacji na temat powierzchni parametry, które czasami mogą być przydatne.
Lub prawdopodobnie należy rozważyć zmniejszenie tego pojedynczego wyrazu.

Niezależnie od tego wyboru się, że wyboru należy dokonać spójne.
Jest to dokładnie roli **specyfikacji**: w przypadku podejmowania decyzji powinien być prawidłową reprezentację.

Dane wyjściowe Analyzer można dość porównywać tylko do danych, który jest zgodny ze specyfikacją tego samego.

## <a name="implementation"></a>Wdrażanie

Często istnieje wiele modeli, które próbują osiągać te same wyniki, ale z różną charakterystykę wydajności.
Jeden model może być szybsze jednak mniej dokładne; inny może spowodować, że różne zależność.

**Implementacji** część nazwy analizator używany do identyfikowania tego typu danych, dzięki czemu użytkownicy mogą wybrać najbardziej odpowiedni analizator odpowiadające ich potrzebom.

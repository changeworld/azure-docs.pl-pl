---
title: Rozpoznawanie prelegenta interfejsu API | Dokumentacja firmy Microsoft
description: Użyj zaawansowane algorytmy weryfikacji osoby mówiącej i identyfikacji osoby mówiącej przy użyciu interfejsu API rozpoznawania prelegenta w usługach kognitywnych.
services: cognitive-services
author: dwlin
manager: zhang
ms.service: cognitive-services
ms.component: speaker-recognition
ms.topic: article
ms.date: 03/20/2016
ms.author: dwlin
ms.openlocfilehash: 6d5e4e4bbe0cb5e57d2556f680ffcf8d16ee1818
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347396"
---
# <a name="speaker-recognition-api"></a>Interfejs API rozpoznawania osoby mówiącej

Zapraszamy do interfejsów API rozpoznawania prelegenta firmy Microsoft. Interfejsy API rozpoznawania osoby mówiącej są oparte na chmurze interfejsów API, które udostępniają najbardziej zaawansowane algorytmy weryfikacji osoby mówiącej i identyfikacji osoby mówiącej. Rozpoznawanie prelegenta można podzielić na dwie kategorie: weryfikacji osoby mówiącej i identyfikacji osoby mówiącej.


## <a name="speaker-verification"></a>Weryfikacja osoby mówiącej


Głos ma unikatowych parametrów, które mogą służyć do identyfikowania osoby, podobnie jak przy użyciu linii papilarnych.  Przy użyciu głosu, jak siła sygnału jest wystarczająca dla scenariuszy uwierzytelniania i kontroli dostępu ma status nowe narzędzie innowacyjne — zasadniczo zapewniających poziom zabezpieczeń, które upraszcza środowisko uwierzytelniania dla klientów.

Interfejsy API weryfikacji osoby mówiącej automatycznie można sprawdzić i uwierzytelnianie użytkowników przy użyciu ich głosu lub mowy.

### <a name="enrollment"></a>Rejestracji

Rejestrowanie na potrzeby weryfikacji osoby mówiącej jest zależne tekstu, co oznacza, że głośniki należy wybrać określone hasło do użycia podczas faz zarówno rejestracji i weryfikacji. 

W rejestracji prelegenta głos jest rejestrowany informujący o tym określone wyrażenie, a następnie szereg funkcji są wyodrębniane i frazy wybrany został rozpoznany. Ze sobą zarówno wyodrębnionego funkcje i wybranego frazę formularza podpisu unikatowy głosu.

### <a name="verification"></a>Weryfikacja
###
W weryfikacji, wejściowych głosu i hasło są porównywane podpis głosu i hasło rejestracji — w celu sprawdzenia, czy są one z tej samej osoby, a jeśli są one opinie poprawne hasło.

Aby uzyskać więcej informacji o weryfikacji osoby mówiącej, zapoznaj się z interfejsu API [prelegenta — Weryfikacja](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652).

## <a name="speaker-identification"></a>Identyfikacja osoby mówiącej

Interfejsy API identyfikacji osoby mówiącej automatycznie można zidentyfikować osoby mówiącej w pliku audio danej grupy potencjalnego głośników. Wejście audio parowania względem wprowadzonej grupy głośniki, a w przypadku, gdy brak Znaleziono dopasowanie, jest zwracany prelegenta tożsamości.

Wszystkie głośniki powinien przejść przez proces rejestracji, aby najpierw uzyskać głosu zarejestrowany w systemie i zostały utworzone drukowania głosu.


### <a name="enrollment"></a>Rejestracji

Rejestracja do identyfikacji osoby mówiącej jest niezależny tekstu, co oznacza, że nie ma żadnych ograniczeń na prelegenta mówi w audio. Prelegenta głos jest rejestrowana i szereg funkcji są wyodrębniane do tworzenia podpisu unikatowy głosu. 


### <a name="recognition"></a>Uznawanie

Audio nieznany prelegenta, wraz z potencjalnego grupy głośniki podano podczas rozpoznawania. Wejściowy głos jest porównywany względem wszystkich głośników w celu ustalenia, których głos jest, a jeśli brak Znaleziono dopasowanie, zwracany jest tożsamość prelegenta.


Aby uzyskać więcej informacji na temat identyfikacji osoby mówiącej, zapoznaj się z interfejsu API [prelegenta — identyfikator](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e).

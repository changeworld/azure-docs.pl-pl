---
title: Mapowanie transformacji wybierz przepływ danych w usłudze Azure Data Factory
description: Mapowanie transformacji wybierz przepływ danych w usłudze Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: bc83b41067d587adce41658a2c4b3d68969750ba
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61364492"
---
# <a name="azure-data-factory-mapping-data-flow-select-transformation"></a>Mapowanie transformacji wybierz przepływ danych w usłudze Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Ta transformacja na użytek selektywność kolumny (zmniejszenie liczby kolumn) lub aliasu kolumny i nazwy strumienia.

Wybierz przekształcenie umożliwia alias strumień całej lub kolumny w strumieniu, przypisz różne nazwami (aliasami), a następnie Odwołaj te nowe nazwy w dalszej części przepływu danych. Ta transformacja jest przydatne w scenariuszach samosprzężenie. Sposób implementacji samosprzężenie w przepływ danych usługi ADF jest podjęcie strumień gałęzi ją dodać za pomocą "Nową gałąź", a następnie zaraz potem przekształcenie "Select". Strumieniu mają teraz nową nazwę, która służy do przyłączenia do oryginalnego strumienia tworzenia samosprzężenie:

![Samosprzężenie](media/data-flow/selfjoin.png "samosprzężenie")

Na powyższym diagramie Wybierz przekształcenie znajduje się na górze. To jest aliasowanie oryginalnego strumienia do "OrigSourceBatting". W przekształcenie sprzężenie higlighted poniżej można zobaczyć, że używamy tego strumienia wybierz aliasu jako sprzężenia po prawej stronie, może odwoływać się do tego samego klucza w lewej i prawej strony sprzężenia wewnętrznego.

Wybierz opcję można również jako sposób Usuń zaznaczenie pola wyboru kolumn z przepływu danych. Na przykład jeśli zdefiniowano w ujściu usługi 6 kolumn, ale chcesz wybrać określone 3 do transformacji i następnie przepływać do ujścia, można wybrać tylko te 3 za pomocą wybierz transformacji.

> [!NOTE]
> Musisz przełączyć off "Zaznacz wszystko" w celu pobrania tylko określone kolumny

Opcje

Ustawieniem domyślnym dla "Select" jest Uwzględnij wszystkie kolumny przychodzących i zachować oryginalne nazwy. Możesz alias strumienia, ustawiając nazwę, wybierz transformacji.

Aby alias poszczególnych kolumn Usuń zaznaczenie opcji "Zaznacz wszystko" i użyj mapowanie kolumny u dołu.

![Wybierz przekształcania](media/data-flow/select001.png "wybrać Alias")

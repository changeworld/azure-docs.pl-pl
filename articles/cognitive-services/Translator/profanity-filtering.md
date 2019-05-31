---
title: Filtrowanie wulgaryzmów — interfejs API tekstu usługi Translator
titlesuffix: Azure Cognitive Services
description: Użyj wulgaryzmów filtrowanie danych na interfejs API tekstu usługi Translator.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-pawal
ms.openlocfilehash: 3ffe14450f0191b88a8d8a71b5f28ae4c59b68c1
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66387582"
---
# <a name="add-profanity-filtering-with-the-translator-text-api"></a>Dodaj wulgaryzmów filtrowanie przy użyciu interfejsu API tłumaczenia tekstu

Zazwyczaj usługa Translator zachowuje wulgaryzmów, który znajduje się w źródle w tłumaczeniu. Stopień wulgaryzmów i kontekst, który sprawia, że wyrazy obsceniczne różnią się od kultury. W wyniku stopień wulgaryzmów w języku docelowym może być większy lub mniejsze.

Jeśli chcesz uniknąć wulgaryzmów w tłumaczeniu, nawet jeśli wulgaryzmów znajduje się w tekście źródłowym, należy użyć wulgaryzmów dostępną w metodzie Translate() opcję filtrowania. Ta opcja umożliwia użytkownikowi oznaczona za pomocą odpowiednich tagów wybierz, czy chcesz zobaczyć wulgaryzmów usunięte, lub wykonać nie podjęto żadnej akcji.

Metoda Translate() przyjmuje parametr "Opcje", który zawiera nowy element "ProfanityAction". Akceptowane wartości ProfanityAction to "NoAction", "Oznaczone" i "Usunięte".

## <a name="accepted-values-of-profanityaction-and-examples"></a>Dopuszczalne wartości ProfanityAction i przykłady
|Wartość ProfanityAction | Akcja | Przykład: Source — japoński | Przykład: TARGET — angielski|
| :---|:---|:---|:---|
| NoAction | Domyślne. Taka sama jak nie Ustawianie opcji. Wulgaryzmów przekazuje ze źródła do docelowego. | 彼は変態です。 | Jest on ostatnim draniem. |
| Oznaczone jako | Obsceniczne wyrazy są ujęte w znaczników XML \<wulgaryzmów >... \</profanity >. | 彼は変態です。 | Jest on \<wulgaryzmów > jerk\</profanity >. |
| Usunięte | Obsceniczne wyrazy są usuwane z danych wyjściowych bez zastępowania. | 彼は。 | Jest on. |

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Zastosuj wulgaryzmów filtrowanie przy użyciu wywołania interfejsu API usługi Translator](reference/v3-0-translate.md)

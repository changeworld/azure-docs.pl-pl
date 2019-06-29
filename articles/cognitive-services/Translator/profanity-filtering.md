---
title: Filtrowanie wulgaryzmów — interfejs API tekstu usługi Translator
titlesuffix: Azure Cognitive Services
description: Użyj wulgaryzmów filtrowanie danych na interfejs API tekstu usługi Translator.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: cc6bf9766912aa55d7869d90976d3089cfd6cf4c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448259"
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

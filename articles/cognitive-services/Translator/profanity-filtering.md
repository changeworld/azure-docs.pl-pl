---
title: Filtrowanie wulgaryzmów — interfejs API tekstu usługi Translator
titlesuffix: Azure Cognitive Services
description: Użyj wulgaryzmów filtrowanie danych na interfejs API tekstu usługi Translator.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-jansko
ms.openlocfilehash: ab54f34dbdbda5af94c6ebc2bc8c1818ebf3d601
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/23/2019
ms.locfileid: "56729886"
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

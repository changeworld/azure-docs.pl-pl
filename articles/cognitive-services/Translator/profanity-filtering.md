---
title: Filtrowanie przy użyciu interfejsu API Microsoft Translator tekst niestosownych wyrażeń | Dokumentacja firmy Microsoft
description: Użyj niestosownych wyrażeń filtrowania w interfejsie API Microsoft Translator tekstu.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: a7172e1e8aa336c011fb06e93fc5c4b54d26a3cd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347677"
---
# <a name="how-to-add-profanity-filtering-with-the-microsoft-translator-text-api"></a>Jak dodać niestosownych wyrażeń filtrowanie przy użyciu interfejsu API Microsoft Translator tekstu

Zazwyczaj usługa Translator zachowuje niestosownych wyrażeń, który znajduje się w źródle w tłumaczenia. Stopień niestosownych wyrażeń i kontekst sprawia, że słowa profanujących różnią się od kultury. W związku z tym stopień niestosownych wyrażeń w języku docelowym może być rozszerzone lub zmniejszenie.

Jeśli chcesz uniknąć wyświetlania niestosownych wyrażeń w tłumaczeniu (niezależnie od obecności niestosownych wyrażeń w tekst źródłowy), korzystając opcji filtrowania niestosownych wyrażeń w metodzie Translate(). Opcję można wybrać, czy mają być wyświetlane niestosownych wyrażeń usunięty lub oznaczonych odpowiednich tagów lub nie podjęto żadnej akcji.

Metoda Translate() przyjmuje parametr "Opcje", która zawiera nowego elementu "ProfanityAction". Dopuszczalne wartości ProfanityAction są "NoAction", "Oznaczone" i "Usunięte".

## <a name="accepted-values-of-profanityaction-and-examples"></a>Zaakceptowane wartości ProfanityAction i przykłady
|Wartość ProfanityAction | Akcja | Przykład: Source — japoński | Przykład: Docelowy - język angielski|
| :---|:---|:---|:---|
| NoAction | Domyślne. Taka sama jak nie ustawienie opcji. Niestosownych wyrażeń przekazuje ze źródła do docelowego. | 彼は変態です。 | Jest ostatnim draniem. |
| Oznaczone | Profanujących wyrazy są ujęte w tagi XML \<niestosownych wyrażeń >... \</Profanity >. | 彼は変態です。 | Jest on \<niestosownych wyrażeń > jerk\</profanity >. |
| Usunięte | Profanujących wyrazy są usuwane z danych wyjściowych bez zastępowania. | 彼は。 | Jest on. |

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Zastosuj filtrowanie z wywołania interfejsu API Translator niestosownych wyrażeń](reference/v3-0-translate.md)


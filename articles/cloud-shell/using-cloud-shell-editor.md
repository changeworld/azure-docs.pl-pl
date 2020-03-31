---
title: Korzystanie z edytora usługi Azure Cloud Shell | Dokumenty firmy Microsoft
description: Omówienie sposobu korzystania z edytora usługi Azure Cloud Shell.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: damaerte
ms.openlocfilehash: 7f597bb5cba1a12bdb93325fe2b877ffc644e3e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60199236"
---
# <a name="using-the-azure-cloud-shell-editor"></a>Korzystanie z edytora usługi Azure Cloud Shell

Usługa Azure Cloud Shell zawiera zintegrowany edytor plików utworzony z [edytora Monaco editor](https://github.com/Microsoft/monaco-editor)open source. Edytor usługi Cloud Shell obsługuje różne funkcje, takie jak wyróżnianie elementów języka, paleta poleceń i eksplorator plików.

![Edytor powłoki chmury](media/using-cloud-shell-editor/open-editor.png)

## <a name="opening-the-editor"></a>Otwieranie edytora

Na potrzeby prostego tworzenia i edytowania plików otwórz edytor, uruchamiając polecenie `code .` w terminalu usługi Cloud Shell. Działanie to powoduje otwarcie edytora z aktywnym katalogiem roboczym ustawionym w terminalu.

Aby bezpośrednio otworzyć plik do szybkiej edycji, uruchom polecenie `code <filename>` w celu otwarcia edytora bez eksploratora plików.

Aby otworzyć edytor za pomocą przycisku, kliknij ikonę edytora `{}` na pasku narzędzi. Edytor zostanie otwarty, a w eksploratorze plików zostanie domyślnie wyświetlony katalog `/home/<user>`.

## <a name="closing-the-editor"></a>Zamykanie edytora

Aby zamknąć edytor, `...` otwórz panel akcji w prawym `Close editor`górnym rogu edytora i wybierz opcję .

![Zamknij edytora](media/using-cloud-shell-editor/close-editor.png)

## <a name="command-palette"></a>Paleta poleceń

Aby uruchomić paletę poleceń, użyj klawisza, `F1` gdy fokus jest ustawiony na edytorze. Otwieranie palety poleceń można również wykonać za pośrednictwem panelu akcji.

![Paleta cmd](media/using-cloud-shell-editor/cmd-palette.png)

## <a name="contributing-to-the-monaco-editor"></a>Współtworzenie edytora w Monako

Obsługa wyróżnienia języka w edytorze Cloud Shell jest obsługiwana przez funkcję nadrzędną w użyciu definicji składni Monarch w [Edytorze Monako.](https://github.com/Microsoft/monaco-editor) Aby dowiedzieć się, jak wnieść wkład, przeczytaj [przewodnik po współpracownikach z Monako.](https://github.com/Microsoft/monaco-editor/blob/master/CONTRIBUTING.md)

## <a name="next-steps"></a>Następne kroki
[Wypróbuj szybki start dla programu Bash w usłudze Cloud Shell](quickstart.md)
[Wyświetl pełną listę zintegrowanych narzędzi Cloud Shell](features.md)
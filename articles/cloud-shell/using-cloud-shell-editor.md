---
title: Za pomocą edytora usługi Azure Cloud Shell | Dokumentacja firmy Microsoft
description: Omówienie sposobu używania edytora usługi Azure Cloud Shell.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60199236"
---
# <a name="using-the-azure-cloud-shell-editor"></a>Za pomocą edytora usługi Azure Cloud Shell

Usługa Azure Cloud Shell obejmuje edytora plików zintegrowanego, utworzony na podstawie typu open-source [edytorze Monaco](https://github.com/Microsoft/monaco-editor). Edytor usługi Cloud Shell obsługuje funkcje, takie jak wyróżnianie, paletę poleceń i Eksploratora plików.

![Cloud Shell edytora](media/using-cloud-shell-editor/open-editor.png)

## <a name="opening-the-editor"></a>Otwieranie Edytora

Tworzenie prostego pliku i edytowania, uruchamiania edytora, uruchamiając `code .` w terminalu usługi Cloud Shell. Spowoduje to otwarcie edytora usługi active Directory pracy w terminalu.

Aby otworzyć bezpośrednio pliku szybką edycję, uruchom `code <filename>` można otworzyć edytora bez Eksploratora plików.

Aby otworzyć Edytor za pomocą przycisku interfejsu użytkownika, kliknij przycisk `{}` Edytor ikon na pasku narzędzi. Spowoduje to otwarcie edytora i domyślnie Eksploratora plików, aby `/home/<user>` katalogu.

## <a name="closing-the-editor"></a>Zamknąć Edytor

Aby zamknąć Edytor, otwórz `...` panelu akcji w prawym górnym rogu edytora i wybierz pozycję `Close editor`.

![Zamknij Edytor](media/using-cloud-shell-editor/close-editor.png)

## <a name="command-palette"></a>Paleta poleceń

Aby uruchomić paletę poleceń, użyj `F1` klucza, gdy fokus jest ustawiony w edytorze. Otwarciem paleta poleceń można także przeprowadzić za pomocą panelu akcji.

![Paleta polecenia](media/using-cloud-shell-editor/cmd-palette.png)

## <a name="contributing-to-the-monaco-editor"></a>Udział w edytorze Monaco

Języki wyróżnienie w edytorze usługi Cloud Shell jest obsługiwany przez nadrzędne funkcji w [edytorze Monaco](https://github.com/Microsoft/monaco-editor)używania Monarch składni definicji. Aby dowiedzieć się, jak współtworzenia, przeczytaj [przewodnik współautora Monako](https://github.com/Microsoft/monaco-editor/blob/master/CONTRIBUTING.md).

## <a name="next-steps"></a>Kolejne kroki
[Spróbuj tego przewodnika Szybki Start dla programu Bash w usłudze Cloud Shell](quickstart.md)
[wyświetlić pełną listę zintegrowanych narzędzi usługi Cloud Shell](features.md)
---
title: Dostosowywanie przycisku Czytnik immersyjny
titleSuffix: Azure Cognitive Services
description: W tym artykule pokazano, jak dostosować przycisk uruchamiający czytnik Immersive.
services: cognitive-services
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 6461cc60a237cf834226b07db5e1ea6fbbea7a92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946211"
---
# <a name="how-to-customize-the-immersive-reader-button"></a>Jak dostosować przycisk Czytnik immersyjny

W tym artykule pokazano, jak dostosować przycisk, który uruchamia immersive reader, aby dopasować się do potrzeb aplikacji.

## <a name="add-the-immersive-reader-button"></a>Dodaj przycisk Czytnik wciągający

Zestaw Immersive Reader SDK zapewnia domyślną stylistykę przycisku uruchamiającego czytnik Immersive Reader. Użyj `immersive-reader-button` atrybutu klasy, aby włączyć tę stylizację.

```html
<div class='immersive-reader-button'></div>
```

## <a name="customize-the-button-style"></a>Dostosowywanie stylu przycisku

Użyj `data-button-style` atrybutu, aby ustawić styl przycisku. Dozwolone wartości to `icon` `text`, `iconAndText`i . Wartością domyślną jest `icon`.

### <a name="icon-button"></a>Przycisk Ikona

```html
<div class='immersive-reader-button' data-button-style='icon'></div>
```

Spowoduje to następujące informacje:

![Przycisk Ikona](./media/button-icon.png)

### <a name="text-button"></a>Przycisk Tekst

```html
<div class='immersive-reader-button' data-button-style='text'></div>
```

Spowoduje to następujące informacje:

![Przycisk Ikona](./media/button-text.png)

### <a name="icon-and-text-button"></a>Przycisk Ikona i tekst

```html
<div class='immersive-reader-button' data-button-style='iconAndText'></div>
```

Spowoduje to następujące informacje:

![Przycisk Ikona](./media/button-icon-and-text.png)

## <a name="customize-the-button-text"></a>Dostosowywanie tekstu przycisku

Skonfiguruj język i tekst alternatywny `data-locale` dla przycisku za pomocą atrybutu. Językiem domyślnym jest język angielski.

```html
<div class='immersive-reader-button' data-locale='fr-FR'></div>
```

## <a name="customize-the-size-of-the-icon"></a>Dostosowywanie rozmiaru ikony

Rozmiar ikony Czytnik wciągający można skonfigurować `data-icon-px-size` za pomocą atrybutu. Spowoduje to ustawienie rozmiaru ikony w pikselach. Domyślny rozmiar to 20px.

```html
<div class='immersive-reader-button' data-icon-px-size='50'></div>
```

## <a name="next-steps"></a>Następne kroki

* Poznaj [dokumentację immersyjnego sdk czytnika](./reference.md)
---
title: Wyświetlanie matematyki w czytniku Immersive Reader
titleSuffix: Azure Cognitive Services
description: W tym artykule pokazano, jak wyświetlić matematykę w czytniku Immersive Reader.
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: pasta
ms.openlocfilehash: e01cc9e8cedb6c38da0b56e04419c706d5d0566e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946123"
---
# <a name="how-to-display-math-in-the-immersive-reader"></a>Jak wyświetlić matematykę w czytniku Immersive Reader

Immersive Reader może wyświetlać matematykę, gdy jest dostarczana w postaci matematycznego języka znaczników[(MathML).](https://developer.mozilla.org/docs/Web/MathML)
Typ MIME można ustawić za pomocą [fragmentu](../reference.md#chunk)Immersive Reader . Zobacz [obsługiwane typy MIME, aby](../reference.md#supported-mime-types) uzyskać więcej informacji.

## <a name="send-math-to-the-immersive-reader"></a>Wyślij matematykę do wciągającego czytnika
Aby wysłać matematykę do czytnika Immersive Reader, podaj fragment zawierający MathML i ustaw typ MIME na ```application/mathml+xml```;

Jeśli na przykład zawartość użytkownika była następująca:

```html
<div id='ir-content'>
    <math xmlns='http://www.w3.org/1998/Math/MathML'>
        <mfrac>
            <mrow>
                <msup>
                    <mi>x</mi>
                    <mn>2</mn>
                </msup>
                <mo>+</mo>
                <mn>3</mn>
                <mi>x</mi>
                <mo>+</mo>
                <mn>2</mn>
            </mrow>
            <mrow>
                <mi>x</mi>
                <mo>−</mo>
                <mn>3</mn>
            </mrow>
        </mfrac>
        <mo>=</mo>
        <mn>4</mn>
    </math>
</div>
```

Następnie możesz wyświetlić zawartość za pomocą następującego javascriptu.

```javascript
const data = {
    title: 'My Math',
    chunks: [{
        content: document.getElementById('ir-content').innerHTML.trim(),
        mimeType: 'application/mathml+xml'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

Po uruchomieniu czytnika Immersive Reader powinien zostać wyświetlony:

![Matematyka w wciągający czytnik](../media/how-tos/1-math.png)

## <a name="next-steps"></a>Następne kroki

* Poznaj [immersyjny moduł SDK czytnika](https://github.com/microsoft/immersive-reader-sdk) i [immersyjny moduł SDK czytnika](../reference.md)
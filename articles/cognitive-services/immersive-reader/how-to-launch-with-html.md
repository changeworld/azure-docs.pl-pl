---
title: Uruchamianie czytnika immersyjnego z zawartością HTML
titleSuffix: Azure Cognitive Services
description: W tym artykule pokazano, jak uruchomić czytnik Immersive Reader z zawartością HTML.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: bc7ab46113e1b819fc71a9f6e8a18400f8acfbef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946244"
---
# <a name="how-to-launch-the-immersive-reader-with-html-content"></a>Jak uruchomić czytnik Immersive Reader z zawartością HTML

W tym artykule pokazano, jak uruchomić program Immersive Reader z zawartością HTML.

## <a name="prepare-the-html-content"></a>Przygotowywanie zawartości HTML

Umieść zawartość, którą chcesz renderować w czytniku Immersive wewnątrz elementu kontenera. Upewnij się, że element `id`kontenera ma unikatowy . Immersive Reader zapewnia obsługę podstawowych elementów HTML, zobacz [odwołanie, aby](./reference.md#html-support) uzyskać więcej informacji.

```html
<div id='immersive-reader-content'>
    <b>Bold</b>
    <i>Italic</i>
    <u>Underline</u>
    <strike>Strikethrough</strike>
    <code>Code</code>
    <sup>Superscript</sup>
    <sub>Subscript</sub>
    <ul><li>Unordered lists</li></ul>
    <ol><li>Ordered lists</li></ol>
</div>
```

## <a name="get-the-html-content-in-javascript"></a>Pobierz zawartość HTML w javascript

Użyj `id` elementu kontenera, aby uzyskać zawartość HTML w kodzie JavaScript.

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>Uruchom czytnik Immersive Reader z zawartością HTML

Podczas `ImmersiveReader.launchAsync`wywoływania ustaw właściwość `mimeType` `text/html` fragmentu, aby włączyć renderowanie HTML.

```javascript
const data = {
    chunks: [{
        content: htmlContent,
        mimeType: 'text/html'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

## <a name="next-steps"></a>Następne kroki

* Poznaj [dokumentację immersyjnego sdk czytnika](./reference.md)
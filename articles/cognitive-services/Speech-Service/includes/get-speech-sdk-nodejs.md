---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: a79d42255a9d04f7a7c8c1be151beda1804df89b
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656687"
---
:::row:::
    :::column span="3":::
        JavaScript Speech SDK jest dostępny jako pakiet npm, zobacz <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">microsoft-cognitiveservices-speech-sdk <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i jest towarzyszem repozytorium GitHub <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">cognitive-services-speech-sdk-js <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Node.js" src="https://docs.microsoft.com/media/logos/logo_nodejs.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Chociaż javascript speech SDK jest dostępny jako pakiet npm, w związku z tym zarówno Node.js i przeglądarki sieci web klienta mogą go zużywać - należy wziąć pod uwagę różne implikacje architektoniczne każdego środowiska. Na przykład <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">model obiektu dokumentu <span class="docon docon-navigate-external x-hidden-focus"></span> (DOM)</a> nie jest dostępny dla aplikacji po stronie serwera, podobnie jak <a href="https://nodejs.org/api/fs.html" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> system plików</a> nie jest dostępny dla aplikacji po stronie klienta.

### <a name="nodejs-package-manager-npm"></a>Menedżer pakietów node.js (NPM)

Aby zainstalować pakiet JavaScript Speech SDK, uruchom poniższe polecenie. `npm install`

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

Aby uzyskać więcej informacji, zobacz <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node" target="_blank">sdk <span class="docon docon-navigate-external x-hidden-focus"> </span>node.js Speech SDK </a>.

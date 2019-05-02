---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: 594b4090fcdfa18432563269743b88cb9d2d723b
ms.sourcegitcommit: ed66a704d8e2990df8aa160921b9b69d65c1d887
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64947356"
---
## <a name="add-the-applications-registration-information-to-your-app"></a>Dodaj informacje o rejestracji aplikacji do aplikacji

W tym kroku należy dodać identyfikator aplikacji do projektu:

1. W `ViewController.swift`, Zastąp wiersz, rozpoczynając od "`let kClientID`" za pomocą:

```swift
let kClientID = "[Enter the application Id here]"
```

<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Control + kliknij <code>Info.plist</code> przywołać menu kontekstowe, a następnie kliknij przycisk: <code>Open As</code> > <code>Source Code</code>
</li>
<li>
W obszarze <code>dict</code> węzła głównego, Dodaj następujący kod:
</li>
</ol>

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msal[Enter the application Id here]</string>
        </array>
    </dict>
</array>
```

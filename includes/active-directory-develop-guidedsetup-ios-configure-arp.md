---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: b3ced6bbd69b38e187b3ed4fa1f2b22e5dd05ce4
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50097937"
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

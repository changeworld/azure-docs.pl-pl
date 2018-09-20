---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: brandwe
manager: mtillman
ms.openlocfilehash: ca7a47fbe2c5ee2a4eb10abf3b9b50a2d28c252e
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2018
ms.locfileid: "46466186"
---
## <a name="register-your-application"></a>Rejestrowanie aplikacji
Aplikację można zarejestrować na dwa sposoby, zgodnie z opisem w dwóch następnych sekcjach.

### <a name="option-1-express-mode"></a>Opcja 1: Tryb ekspresowy
Teraz musisz zarejestrować aplikację w *portalu rejestracji aplikacji Microsoft*:
1. Zarejestruj swoją aplikację za pośrednictwem [portalu rejestracji aplikacji firmy Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=ios&step=configure)
2.  Wprowadź nazwę swojej aplikacji i poczty e-mail
3.  Upewnij się, że jest zaznaczona opcja Instalatora z przewodnikiem
4.  Postępuj zgodnie z instrukcjami, aby uzyskać identyfikator aplikacji i wklej go w kodzie

### <a name="option-2-advanced-mode"></a>Opcja 2: Tryb zaawansowany

1.  Przejdź do [portalu rejestracji aplikacji firmy Microsoft](https://apps.dev.microsoft.com/portal/register-app)
2.  Wprowadź nazwę aplikacji
3.  Upewnij się, że zaznaczono opcję instrukcje konfiguracji
4.  Kliknij przycisk `Add Platform`, a następnie wybierz `Native Application` i kliknij przycisk `Save`
5.  Wróć do programu Xcode. W `ViewController.swift`, Zastąp wiersz, rozpoczynając od "`let kClientID`" identyfikator aplikacji, które właśnie zostało zarejestrowane:

```swift
let kClientID = "Your_Application_Id_Here"
```

<!-- Workaround for Docs conversion bug -->
<ol start="6">
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
            <string>msal[Your_Application_Id_Here]</string>
        </array>
    </dict>
</array>
```
<ol start="8">
<li>
Zastąp <i> <code>[Your_Application_Id_Here]</code> </i> identyfikatorem aplikacji, które właśnie zostało zarejestrowane
</li>
</ol>

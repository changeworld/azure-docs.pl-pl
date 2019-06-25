---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 5940195207f85d8011f61336c0318e456c2a8a4c
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203832"
---
## <a name="configure-your-aspnet-web-app-with-the-applications-registration-information"></a>Konfigurowanie aplikacji internetowej ASP.NET przy użyciu aplikacji informacje rejestracyjne

W tym kroku zostaną Konfigurowanie projektu do używania protokołu SSL, a następnie użyj adresu URL protokołu SSL do skonfigurowania aplikacji informacje rejestracyjne. Po tym Dodaj aplikację "informacje rejestracyjne do rozwiązania za pośrednictwem *web.config*.

1. W Eksploratorze rozwiązań wybierz projekt i przyjrzyj się `Properties` okna (Jeśli nie widzisz okna właściwości, naciśnij klawisz F4)
2. Zmiana `SSL Enabled` do `True`
3. Skopiuj wartości z `SSL URL` powyżej i wkleić go w `Redirect URL` polu u góry tej strony, a następnie kliknij przycisk *aktualizacji*:<br/><br/>![Właściwości projektu](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
4. Dodaj następujący kod w `web.config` plik znajdujący się w folderze głównego, w sekcji `configuration\appSettings`:

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```

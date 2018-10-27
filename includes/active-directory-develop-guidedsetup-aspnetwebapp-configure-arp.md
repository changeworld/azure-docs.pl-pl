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
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: f3048daa7d597d92041733cdf5c6f299cebc2f73
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142431"
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

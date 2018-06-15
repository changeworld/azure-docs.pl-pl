---
title: Azure zastrzeżone błędów nazw zasobów | Dokumentacja firmy Microsoft
description: Opisuje sposób Rozwiązywanie błędów podczas tworzenia nazwy zasobu, zawierający słowo zastrzeżone.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: b91a53d17d64afb0a56f745505f10e8cabbc22cc
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34357065"
---
# <a name="resolve-reserved-resource-name-errors"></a>Rozwiązywanie błędów nazw zarezerwowanych zasobów

W tym artykule opisano błąd napotykanych podczas wdrażania zasobu, który zawiera słowo zastrzeżone w jego nazwy.

## <a name="symptom"></a>Objaw

W przypadku wdrażania z zasobem, który jest dostępny za pośrednictwem publicznego punktu końcowego, może zostać wyświetlony następujący błąd:

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>Przyczyna

Zasoby, które mają publiczny punkt końcowy nie można użyć słowa zastrzeżone lub znakami w nazwie.

Wyrazy są zarezerwowane:

* DOSTĘP
* AZURE
* USŁUGI BING
* BIZSPARK
* BIZTALK
* CORTANA
* PROGRAM DIRECTX
* DOTNET
* DYNAMICS
* EXCEL
* PROGRAM EXCHANGE
* FOREFRONT
* GROOVE
* HOLOLENS
* FUNKCJI HYPER-V
* KINECT
* LYNC
* MSDN
* O365
* OFFICE
* USŁUGI OFFICE 365
* USŁUGI ONEDRIVE
* PROGRAM ONENOTE
* PROGRAM OUTLOOK
* POWERPOINT
* PROGRAM SHAREPOINT
* SKYPE
* VISIO
* VISUAL STUDIO

Nie można użyć słowa jako całe wyrazy lub podciąg nazwy:

* LOGOWANIE
* MICROSOFT
* SYSTEMU WINDOWS
* XBOX

## <a name="solution"></a>Rozwiązanie

Podaj nazwę, która nie używa jedno ze słów zastrzeżonych.
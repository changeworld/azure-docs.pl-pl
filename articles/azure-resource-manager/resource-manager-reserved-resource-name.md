---
title: Zarezerwowane błędy nazw zasobów na platformie Azure | Dokumentacja firmy Microsoft
description: Opisuje sposób Rozwiązywanie błędów podczas podawania nazwy zasobu, który zawiera słowo zastrzeżone.
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
ms.openlocfilehash: 922389b7c6c1bb7ad1d9b8f6ec35ccc1c5656723
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64683933"
---
# <a name="resolve-reserved-resource-name-errors"></a>Rozwiązywanie błędów nazw zarezerwowanych zasobów

W tym artykule opisano błąd, który wystąpi podczas wdrażania zasobu, który zawiera wyrazem zastrzeżonym w jego nazwę.

## <a name="symptom"></a>Objaw

Podczas wdrażania zasobów, która jest dostępna za pośrednictwem publicznego punktu końcowego, może zostać wyświetlony następujący błąd:

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>Przyczyna

Zasoby, które mają publiczny punkt końcowy nie można użyć wyrazów zastrzeżonych ani znaków towarowych, w nazwie.

Wyrazy są zarezerwowane:

* DOSTĘP DO
* AZURE
* BING
* BIZSPARK
* BIZTALK
* CORTANA
* DIRECTX
* POLECENIA DOTNET
* DYNAMICS
* EXCEL
* PROGRAM EXCHANGE
* FOREFRONT
* GROOVE
* URZĄDZENIA HOLOLENS
* FUNKCJI HYPER-V
* ZESTAW KINECT
* LYNC
* MSDN
* O365
* PAKIETU OFFICE
* OFFICE365
* ONEDRIVE
* PROGRAM ONENOTE
* PROGRAM OUTLOOK
* POWERPOINT
* PROGRAM SHAREPOINT
* SKYPE
* VISIO
* VISUALSTUDIO

Następujące słowa nie można użyć jako całe wyrazy lub podciąg nazwy:

* LOGIN
* MICROSOFT
* SYSTEMU WINDOWS
* XBOX

## <a name="solution"></a>Rozwiązanie

Podaj nazwę, która nie korzysta z jednego ze słów zastrzeżonych.
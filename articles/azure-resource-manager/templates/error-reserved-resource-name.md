---
title: Błędy nazwy zasobu zarezerwowanego
description: W tym artykule opisano sposób rozwiązywania błędów podczas podawania nazwy zasobu, która zawiera słowo zastrzeżone.
ms.topic: troubleshooting
ms.date: 11/08/2017
ms.openlocfilehash: e76f4bf9bfee7de6e7523d69acf1388d2dd80e93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75477631"
---
# <a name="resolve-reserved-resource-name-errors"></a>Rozwiązywanie problemów z nazwami zarezerwowanych zasobów

W tym artykule opisano błąd napotkany podczas wdrażania zasobu, który zawiera słowo zastrzeżone w jego nazwie.

## <a name="symptom"></a>Objaw

Podczas wdrażania zasobu, który jest dostępny za pośrednictwem publicznego punktu końcowego, może pojawić się następujący błąd:

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>Przyczyna

Zasoby, które mają publiczny punkt końcowy, nie mogą używać słów zastrzeżonych ani znaków towarowych w nazwie.

Następujące słowa są zastrzeżone:

* DOSTĘP
* Azure
* Bing
* Bizspark
* Biztalk
* Cortana
* Directx
* Dotnet
* Dynamics
* EXCEL
* Exchange
* Forefront
* Groove
* Hololens
* Funkcja HYPERV
* Sensora kinect
* Lync
* MSDN
* O365
* Office
* USŁUGA OFFICE365
* Onedrive
* Programu onenote
* Programu outlook
* Powerpoint
* Programu sharepoint
* Skype
* Visio
* Visualstudio

Następujące słowa nie mogą być używane jako całe słowo lub podciąg w nazwie:

* Logowania
* Microsoft
* Windows
* Konsoli xbox

## <a name="solution"></a>Rozwiązanie

Podaj nazwę, która nie używa jednego ze słów zastrzeżonych.
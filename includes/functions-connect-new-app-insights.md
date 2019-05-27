---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 04/06/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: b6cafcfe6c892cd43f056458fe3586da834c2fd1
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66131419"
---
Funkcje pozwala w prosty sposób dodać Integracja usługi Application Insights do aplikacji funkcji z [Azure Portal].

1. W [portal][azure portal], wybierz opcję **wszystkie usługi > aplikacje funkcji**, wybierz aplikację funkcji, a następnie wybierz **usługi Application Insights** transparent w górnej części okna

    ![Włącz usługę Application Insights z poziomu portalu](media/functions-connect-new-app-insights/enable-application-insights.png)

1. Utwórz zasób usługi Application Insights przy użyciu ustawień określonych w tabeli pod ilustracją:

   ![Tworzenie zasobu usługi Application Insights](media/functions-connect-new-app-insights/ai-general.png)

    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nazwa** | Unikatową nazwę aplikacji | Najłatwiej można użyć tej samej nazwie co aplikacja funkcji, która musi być unikatowa w ramach subskrypcji. | 
    | **Lokalizacja** | Europa Zachodnia | Jeśli to możliwe, używać tego samego [region](https://azure.microsoft.com/regions/) jako aplikację funkcji lub w pobliżu. |

1. Wybierz **OK**. Zasób usługi Application Insights jest tworzony w tej samej grupie zasobów i subskrypcji co aplikacja funkcji. Po zakończeniu tworzenia, zamknij okno usługi Application Insights.

1. W aplikacji funkcji, wybierz **ustawienia aplikacji**i przewiń w dół do **ustawienia aplikacji**. Po wyświetleniu ustawienie o nazwie `APPINSIGHTS_INSTRUMENTATIONKEY`, oznacza to, że integracja usługi Application Insights jest włączone dla aplikacji funkcji działających na platformie Azure.

[Azure Portal]: https://portal.azure.com

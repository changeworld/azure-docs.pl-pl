---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/06/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 266cd52117f36b282fdd4bc8615a15e451cc203f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132550"
---
Funkcje ułatwiają dodawanie integracji usługi Application Insights do aplikacji funkcji z [witryny Azure portal.]

1. W [portalu][Azure Portal]wpisz `Function Apps` na pasku wyszukiwania u góry strony wybierz aplikację funkcji, a następnie wybierz plik danych Usługi Application Insights nie jest **skonfigurowany** baner w górnej części okna. Jeśli nie widzisz tego banera, aplikacja ma już włączoną statystykę aplikacji.

    ![Włączanie usługi Application Insights z portalu](media/functions-connect-new-app-insights/enable-application-insights.png)

1. Utwórz zasób usługi Application Insights przy użyciu ustawień określonych w tabeli poniżej obrazu.

   ![Tworzenie zasobu usługi Application Insights](media/functions-connect-new-app-insights/ai-general.png)

    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nazwa** | Unikatowa nazwa aplikacji | Najłatwiej jest użyć tej samej nazwy co aplikacja funkcji, która musi być unikatowa w subskrypcji. | 
    | **Lokalizacja** | Europa Zachodnia | Jeśli to możliwe, użyj tego samego [regionu](https://azure.microsoft.com/regions/) co aplikacja funkcji lub takiego, który znajduje się w pobliżu tego regionu. |

1. Kliknij przycisk **OK**. Zasób usługi Application Insights jest tworzony w tej samej grupie zasobów i subskrypcji co aplikacja funkcji. Po utworzeniu zasobu zamknij okno Usługi Application Insights.

1. Wróć do aplikacji funkcji wybierz **pozycję Ustawienia aplikacji**, a następnie przewiń w dół do pozycji Ustawienia **aplikacji**. Jeśli zostanie wyświetlone `APPINSIGHTS_INSTRUMENTATIONKEY`ustawienie o nazwie , integracja usługi Application Insights jest włączona dla aplikacji funkcji uruchomionej na platformie Azure.

[Azure Portal]: https://portal.azure.com

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
ms.openlocfilehash: b1bbc11d7772e4f56d7dc6ead580b0a0cbd3cd8d
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68669758"
---
Funkcje ułatwiają dodawanie Application Insights integracji z aplikacją funkcji z [Azure Portal].

1. W portalu [][Azure Portal]wybierz pozycję **wszystkie usługi > Aplikacje funkcji**, wybierz aplikację funkcji, a następnie wybierz transparent **Application Insights** w górnej części okna

    ![Włączanie Application Insights z poziomu portalu](media/functions-connect-new-app-insights/enable-application-insights.png)

1. Utwórz zasób Application Insights przy użyciu ustawień określonych w tabeli poniżej obrazu.

   ![Tworzenie zasobu usługi Application Insights](media/functions-connect-new-app-insights/ai-general.png)

    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nazwa** | Unikatowa nazwa aplikacji | Najłatwiej używać tej samej nazwy, co aplikacja funkcji, która musi być unikatowa w subskrypcji. | 
    | **Location** | Europa Zachodnia | Jeśli to możliwe, użyj tego samego [regionu](https://azure.microsoft.com/regions/) , w którym znajduje się aplikacja funkcji lub która znajduje się blisko tego regionu. |

1. Kliknij przycisk **OK**. Zasób Application Insights jest tworzony w tej samej grupie zasobów i subskrypcji co aplikacja funkcji. Po utworzeniu zasobu Zamknij okno Application Insights.

1. Wróć do aplikacji funkcji, wybierz pozycję **Ustawienia aplikacji**, a następnie przewiń w dół do **ustawień aplikacji**. Jeśli zobaczysz ustawienie o nazwie `APPINSIGHTS_INSTRUMENTATIONKEY`, Application Insights integracja jest włączona dla aplikacji funkcji działającej na platformie Azure.

[Azure Portal]: https://portal.azure.com

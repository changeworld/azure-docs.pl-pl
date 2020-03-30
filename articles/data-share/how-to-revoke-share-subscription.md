---
title: Odwoływanie subskrypcji udziału w udziale danych platformy Azure
description: Dowiedz się, jak odwołać subskrypcję udziału od adresata przy użyciu usługi Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 97f672b266c2df6956282aea6dec8d5207944943
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73476388"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share"></a>Jak odwołać subskrypcję udziału konsumenta w usłudze Azure Data Share

W tym artykule wyjaśniono, jak odwołać subskrypcję udziału od jednego lub więcej konsumentów przy użyciu usługi Azure Data Share. Uniemożliwia to konsumentowi wyzwalanie kolejnych migawek. Jeśli konsument nie wyzwolił jeszcze migawki, nigdy nie otrzyma danych po odwołaniu subskrypcji udziału. Jeśli wcześniej wyzwolili migawkę, najnowsze dane, które mają, pozostaną na ich koncie.

## <a name="navigate-to-a-sent-data-share"></a>Przechodzenie do folderu udostępniania danych wysłanych

W usłudze Azure Data Share przejdź do wysłanego udziału i wybierz kartę **Udostępnij subskrypcje.**

![Odwołaj subskrypcję udziału](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

Zaznacz pola obok adresatów, których subskrypcje udziału chcesz usunąć, a następnie kliknij przycisk **Odwołaj**. Konsument nie będzie już otrzymywać aktualizacji swoich danych.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [monitorowaniu udziału danych](how-to-monitor.md).
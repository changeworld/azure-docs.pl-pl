---
title: Odwoływanie subskrypcji udostępniania w wersji zapoznawczej udziału danych platformy Azure
description: Dowiedz się, jak odwołać subskrypcję udziału z odbiorcy przy użyciu wersji zapoznawczej udziału danych platformy Azure.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: bd8ef23ead500acb9e403b38fd52a2d980d12bf4
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169054"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share-preview"></a>Jak odwołać subskrypcję udziału użytkownika w wersji zapoznawczej udziału danych platformy Azure

W tym artykule wyjaśniono, jak odwołać subskrypcję udziału z co najmniej jednego użytkownika przy użyciu wersji zapoznawczej udziału danych platformy Azure. Zapobiega to wyzwoleniu większej liczby migawek przez konsumenta. Jeśli użytkownik nie wyzwolił jeszcze migawki, nigdy nie otrzyma danych po odwołaniu subskrypcji udziału. Jeśli wcześniej wyzwoliły migawkę, najnowsze dane, które mają pozostawać w ich koncie.

## <a name="navigate-to-a-sent-data-share"></a>Przejdź do wysłanego udziału danych

W wersji zapoznawczej usługi Azure Data Share przejdź do wysłanego udziału i wybierz kartę **udostępnianie subskrypcji** .

![Odwołaj subskrypcję udziałów](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

Zaznacz pola obok adresatów, których subskrypcje mają być usunięte, a następnie kliknij przycisk **odwołaj**. Odbiorca nie będzie już otrzymywać aktualizacji swoich danych.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o tym, jak [monitorować udziały danych](how-to-monitor.md).
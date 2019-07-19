---
title: Odwoływanie subskrypcji udostępniania w wersji zapoznawczej udziału danych platformy Azure
description: Odwoływanie subskrypcji udostępniania
author: madams0013
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: t-maadam
ms.openlocfilehash: fb5572aee513172d06b5a75c2d3b11885081f792
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "67877209"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share-preview"></a>Jak odwołać subskrypcję udziału użytkownika w wersji zapoznawczej udziału danych platformy Azure

W tym artykule wyjaśniono, jak odwołać subskrypcję udziału z co najmniej jednego użytkownika przy użyciu wersji zapoznawczej udziału danych platformy Azure. Zapobiega to wyzwoleniu większej liczby migawek przez konsumenta. Jeśli użytkownik nie wyzwolił jeszcze migawki, nigdy nie otrzyma danych po odwołaniu subskrypcji udziału. Jeśli wcześniej wyzwoliły migawkę, najnowsze dane, które mają pozostawać w ich koncie.

## <a name="navigate-to-a-sent-data-share"></a>Przejdź do wysłanego udziału danych

W wersji zapoznawczej usługi Azure Data Share przejdź do wysłanego udziału i wybierz kartę **udostępnianie subskrypcji** .

![Odwołaj subskrypcję udziałów](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

Zaznacz pola obok adresatów, których subskrypcje mają być usunięte, a następnie kliknij przycisk **odwołaj**. Odbiorca nie będzie już otrzymywać aktualizacji swoich danych.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o tym, jak [monitorować udziały danych](how-to-monitor.md).
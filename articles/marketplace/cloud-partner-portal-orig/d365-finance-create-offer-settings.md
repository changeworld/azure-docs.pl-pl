---
title: Jak wypełnić formularz ustawień oferty | Dokumentacja firmy Microsoft
description: W tym artykule wyjaśniono różne pola, które wymagają wartości w postaci oferty ustawienia dla nowej aplikacji Dynamics 365 Business Central.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: a050a8cfdd67f662786fc0b9b7ed5451c88dccb1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60397825"
---
<a name="how-to-fill-out-the-offer-settings-form"></a>Jak wypełnić formularz ustawień oferty
=======================================

Formularz ustawień oferty jest podstawowej postaci, aby określić ustawienia oferty.
Wymagane pola są opisane poniżej.

### <a name="offer-id"></a>Identyfikator oferty

`OfferId` jest unikatowy identyfikator oferty w ramach profilu wydawcy.
Ten identyfikator będzie widoczny w adresach URL produktu. Może składać się tylko z małych znaków alfanumerycznych i łączników (-). Identyfikator nie może kończyć się kreską i nie może przekraczać 50 znaków. To pole jest zablokowane, gdy oferty przechodzi na żywo.

Jeśli na przykład partnera "Contoso" utworzy Identyfikatora oferty o nazwie "Przykładowy — Web App", zostanie wyświetlona w usłudze AppSource jako:

&emsp; `https://appsource.microsoft.com/marketplace/apps/contoso.sample-Web App?tab=Overview`


### <a name="publisher-id"></a>Identyfikator wydawcy

Tej listy rozwijanej można wybrać profil wydawcy, który chcesz opublikować tę ofertę, w obszarze. To pole jest zablokowane, gdy oferty przechodzi na żywo.


### <a name="name"></a>Name (Nazwa)

Jest to nazwa wyświetlana aplikacji/oferty, która będzie wyświetlana w programie Microsoft [AppSource](https://appsource.microsoft.com/). Może zawierać maksymalnie 50 znaków.

> [!NOTE]
> Krótka nazwa musi być taka sama jak nazwa wydawcy, określone w manifeście aplikacji.

Kliknij pozycję **Zapisz** Aby zapisać postęp. Następnym krokiem powinno być, aby dodać informacje techniczne dla oferty.

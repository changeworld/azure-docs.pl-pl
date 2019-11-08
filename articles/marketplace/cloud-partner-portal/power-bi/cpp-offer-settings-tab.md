---
title: Ustawienia oferty dla oferty aplikacji Power BI | Portal Azure Marketplace
description: Skonfiguruj ustawienia oferty dla oferty aplikacji Power BI dla witryny Microsoft AppSource Marketplace.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: ca326b10a0707d5e4b1a5f05dccc303c9ec28269
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822676"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Karta Ustawienia oferty aplikacji Power BI

Po otwarciu nowej strony **oferty** dla aplikacji usługi, najpierw zobaczysz kartę **Ustawienia oferty** . Na tej karcie Podaj identyfikatory podstawowe i nazwę oferty. Gwiazdka (*) wskazuje pole wymagane.

![Karta ustawień oferty](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Pola ustawień oferty 

Na karcie **Ustawienia oferty** musisz wprowadzić informacje w następujących wymaganych polach. Wymagane pola są indicted przez gwiazdkę (*).

|  Pole        |  Opis                                                               |
|---------------|----------------------------------------------------------------------------|
| **Identyfikator oferty\***  | Unikatowy identyfikator (w ramach profilu wydawcy) oferty. Ten identyfikator będzie widoczny w adresach URL produktów, szablonach Azure Resource Manager i raportach dotyczących rozliczeń. Maksymalna długość to 50 znaków. Może zawierać tylko małe znaki alfanumeryczne i łączniki (-). Nie może kończyć się kreską. Nie można zmienić tego identyfikatora po przejściu oferty na żywo. Jeśli firma Contoso opublikuje ofertę o IDENTYFIKATORze oferty `sample-SvcApp`, do oferty zostanie przypisany adres URL AppSource `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`.      |
| **\* wydawcy** | Unikatowy identyfikator organizacji w [AppSource](https://appsource.microsoft.com). Wszystkie oferty powinny być skojarzone z IDENTYFIKATORem wydawcy. Tej wartości nie można zmienić po zapisaniu oferty.                         |
| **Nazwa\***      | Nazwa wyświetlana oferty. Ta nazwa będzie wyświetlana w AppSource i na portal Cloud Partner. Maksymalna długość to 50 znaków. Użyj nazwy marki, która jest rozpoznawana jako produkt. Nie dodawaj tutaj nazwy organizacji, chyba że aplikacja zostanie wprowadzona na rynek. Jeśli udostępniasz tę ofertę w innych witrynach sieci Web i publikacjach, Użyj tej samej nazwy we wszystkich publikacjach.    <br/>W przypadku wydania oferty w okresie zapoznawczym dla aplikacji Power BI należy dodać ciąg `(Preview)` na końcu nazwy aplikacji, na przykład: `Sample Scv App (Preview)`. |
|     |     |


## <a name="next-steps"></a>Następne kroki

Na następnej karcie określisz [Informacje techniczne](./cpp-technical-info-tab.md) dla swojej oferty.

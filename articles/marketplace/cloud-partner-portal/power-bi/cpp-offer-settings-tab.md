---
title: Ustawienia dla oferty aplikacji Power BI oferty | Portal Azure Marketplace
description: Skonfiguruj ustawienia oferty dla oferty aplikacji Power BI w portalu Marketplace Microsoft AppSource.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: be2c2b4f5d9461aa0fdc6dde89931ed4b6418ced
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64943465"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Karta Ustawienia oferty aplikacji Power BI zasilania

Po otwarciu **nowa oferta** stronie dla aplikacji usługi, najpierw zobacz **oferują ustawienia** kartę. Na tej karcie możesz podać identyfikatorów podstawowych i nazwę dla oferty. Znak gwiazdki (*) wskazuje wymagane pole.

![Karta ustawień oferty](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Oferta ustawienia pola 

Na **oferują ustawienia** kartę, należy wprowadzić informacje w następujących wymaganych pól. Wymagane pola są wskazanych przez znak gwiazdki (*).

|  Pole        |  Opis                                                               |
|---------------|----------------------------------------------------------------------------|
| **Identyfikator oferty\***  | Unikatowy identyfikator (w ramach profilu wydawcy) dla tej oferty. Ten identyfikator będzie widoczny w adresach URL produktu, szablony usługi Azure Resource Manager, i zgłasza rozliczeń. Maksymalna długość to 50 znaków. Może zawierać tylko małe znaki alfanumeryczne i łączniki (-). Nie może kończyć się kreską. Nie można zmienić tego identyfikatora, po oferty przechodzi na żywo. Jeśli Contoso publikuje ofertę z identyfikator oferty `sample-SvcApp`, oferty jest przypisany adres URL usługi AppSource `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`.      |
| **Publisher\*** | Unikatowy identyfikator organizacji z [AppSource](https://appsource.microsoft.com). Ofert użytkownika powinna być skojarzona z Twojego identyfikatora wydawcy. Nie można zmienić tej wartości, po zapisaniu tej oferty.                         |
| **Nazwa\***      | Nazwa wyświetlana oferty. Ta nazwa będzie wyświetlana w usłudze AppSource i w portalu Cloud Partner. Maksymalna długość to 50 znaków. Użyj nazwy marki rozpoznawalne na produkt. Nie dołączaj tutaj nazwę organizacji, chyba że aplikacja jest sprzedawane o tej nazwie. Jeśli udostępniasz tej oferty w innych witryn sieci Web i publikacji, należy użyć takiej samej nazwie w wszystkie publikacje typu.    <br/>Jeśli zostanie wydana oferty w okresie wersji zapoznawczej dla aplikacji usługi Power BI, Dodaj parametry `(Preview)` na końcu nazwy aplikacji, takich jak to: `Sample Scv App (Preview)`. |
|     |     |


## <a name="next-steps"></a>Kolejne kroki

Na następnej karcie należy podać [informacje techniczne](./cpp-technical-info-tab.md) oferty.

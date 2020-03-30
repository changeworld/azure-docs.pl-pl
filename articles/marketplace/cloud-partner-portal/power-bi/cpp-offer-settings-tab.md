---
title: Ustawienia oferty dla oferty aplikacji Power BI | Azure Marketplace
description: Konfigurowanie ustawień oferty dla oferty aplikacji Usługi Power BI dla witryny Microsoft AppSource marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: dsindona
ms.openlocfilehash: f3a8d740d391edc09a290d3dba4307af7eec00b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286357"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Karta Ustawienia oferty aplikacji usługi Power BI

Po otwarciu strony **Nowa oferta** dla aplikacji usługowych najpierw zostanie wyświetlona karta **Ustawienia oferty.** Na tej karcie podajesz identyfikatory podstawowe i nazwę oferty. Gwiazdka (*) wskazuje wymagane pole.

![Karta ustawień oferty](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Pola Ustawienia oferty 

Na karcie **Ustawienia oferty** należy wprowadzić informacje w następujących wymaganych polach. Wymagane pola są oskarżane gwiazdką (*).

|  Pole        |  Opis                                                               |
|---------------|----------------------------------------------------------------------------|
| **Identyfikator oferty\***  | Unikatowy identyfikator (w profilu wydawcy) dla oferty. Ten identyfikator będzie widoczny w adresach URL produktów, szablonach usługi Azure Resource Manager i raportach rozliczeniowych. Maksymalna długość wynosi 50 znaków. Może zawierać tylko małe litery alfanumeryczne i myślniki (-). To nie może skończyć się kreską. Tego identyfikatora nie można zmienić po uruchomieniu oferty. Jeśli firma Contoso opublikuje ofertę `sample-SvcApp`z identyfikatorem oferty, do `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`oferty zostanie przypisany adres URL źródła usług AppSource .      |
| **Wydawca\*** | Unikatowy identyfikator organizacji w [uźródle aplikacji AppSource](https://appsource.microsoft.com). Wszystkie oferty powinny być powiązane z identyfikatorem wydawcy. Tej wartości nie można zmienić po zapisaniu oferty.                         |
| **Nazwa\***      | Wyświetlana nazwa oferty. Ta nazwa pojawi się w usłudze AppSource i w portalu cloud partner. Maksymalna długość wynosi 50 znaków. Użyj nazwy marki, która jest rozpoznawalna dla Twojego produktu. Nie podawaj tutaj nazwy organizacji, chyba że aplikacja jest sprzedawana pod tą nazwą. Jeśli udostępniasz tę ofertę na innych stronach internetowych i w publikacjach, użyj tej samej nazwy we wszystkich publikacjach.    <br/>Jeśli wydasz ofertę w okresie podglądu aplikacji usługi `(Preview)` Power BI, dodaj ciąg na końcu `Sample Scv App (Preview)`nazwy aplikacji, w stylu: . |
|     |     |


## <a name="next-steps"></a>Następne kroki

Na następnej karcie określ informacje [techniczne](./cpp-technical-info-tab.md) oferty.

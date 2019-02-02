---
title: Oferty ustawień na potrzeby aplikacji Power BI oferują - portalu Azure Marketplace | Dokumentacja firmy Microsoft
description: Skonfiguruj ustawienia oferty Microsoft AppSource Marketplace oferty aplikacji Power BI.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
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
ms.date: 01/29/2019
ms.author: pbutlerm
ms.openlocfilehash: fef2455129d94913c5b51a08c04403834861bb48
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2019
ms.locfileid: "55666786"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Karta Ustawienia oferty aplikacji Power BI zasilania

**Nowa oferta** zostanie otwarta strona usługi App Service na pierwszej karcie o nazwie **oferują ustawienia**.  Należy podać identyfikatory podstawowego i nazwę dla oferty na tej karcie.  Dołączonych gwiazdki (*) na nazwę pola wskazuje, że jest wymagane.

![Karta Ustawienia oferty](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Oferta ustawienia pola 

W **oferują ustawienia** kartę, należy podać następujące wymagane pola.

|  Pole        |  Opis                                                               |
|---------------|----------------------------------------------------------------------------|
| **Identyfikator oferty**  | Unikatowy identyfikator (w ramach profilu wydawcy) dla tej oferty. Ten identyfikator będzie widoczny w adresach URL produktu, szablonów usługi Resource Manager i raporty rozliczeń. Jego ma maksymalnej długości 50 znaków i może zawierać tylko składa się z małe znaki alfanumeryczne i łączniki (-), ale nie może kończyć się kreską. W tym polu nie można zmienić po oferty przechodzi na żywo. Na przykład, jeśli Contoso publikuje ofertę z identyfikator oferty `sample-SvcApp`, jest przypisany adres URL usługi AppSource `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`.      |
| **Wydawca** | Unikatowy identyfikator organizacji z [AppSource](https://appsource.microsoft.com). Ofert użytkownika powinna być skojarzona z Twojego identyfikatora wydawcy. Nie można zmodyfikować tej wartości, po zapisaniu tej oferty.                         |
| **Nazwa**      | Nazwa wyświetlana oferty. Będzie wyświetlana w usłudze AppSource oraz w portalu Cloud Partner. Może zawierać maksymalnie 50 znaków. W tym miejscu są wskazówki do uwzględnienia rozpoznawalną nazwę markę produktu. Nie dołączaj nazwę swojej organizacji w tym miejscu, chyba że jak odbywa się aplikacja. Jeśli są marketingu tej oferty w ramach innych witryn sieci Web i publikacji, upewnij się, że nazwa jest taka sama we wszystkich publikacjach.    <br/>Jeśli zostanie wydana oferty w okresie zapoznawczym aplikacje usługi Power BI, tryb podglądu, Dołącz ciąg `(Preview)` na nazwę swojej aplikacji, na przykład `Sample Scv App (Preview)`. |
|     |     |


## <a name="next-steps"></a>Kolejne kroki

Następna karta określisz [informacje techniczne](./cpp-technical-info-tab.md) oferty.

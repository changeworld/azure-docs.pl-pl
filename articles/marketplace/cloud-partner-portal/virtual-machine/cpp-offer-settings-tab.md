---
title: Karta Ustawienia oferty maszyny wirtualnej w portalu Cloud Partner portalu Azure Marketplace | Dokumentacja firmy Microsoft
description: W tym artykule opisano na karcie Ustawienia oferują używanego podczas tworzenia oferty maszyny Wirtualnej portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 43c830bea57a4c6f3b6c56552dbcacaccc75d54e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844363"
---
# <a name="virtual-machine-offer-settings-tab"></a>Karta Ustawienia oferty maszyny wirtualnej

**Nowa oferta** zostanie otwarta strona maszyn wirtualnych na pierwszej karcie o nazwie **oferują ustawienia**.  Dołączonych gwiazdki (*) na nazwę pola wskazuje, że jest wymagane. 

![Nowa strona oferty dla maszyn wirtualnych](./media/publishvm_004.png)

W **oferują ustawienia** kartę, należy podać następujące wymagane pola.

|  **Pole**       |     **Opis**                                                          |
|  ---------       |     ---------------                                                          |
| **Identyfikator oferty**       | Unikatowy identyfikator (w ramach profilu wydawcy) dla tej oferty. Ten identyfikator będzie widoczny w adresach URL produktu, szablony usługi Azure Resource Manager, i zgłasza rozliczeń. Jego ma maksymalnej długości 50 znaków i może zawierać tylko składa się z małe znaki alfanumeryczne i łączniki (-), ale nie może kończyć się kreską. W tym polu nie można zmienić po oferty przechodzi na żywo. <br> Na przykład, jeśli Contoso publikuje ofertę z identyfikator oferty **maszyny wirtualnej na przykład**, jest przypisany adres URL witryny Marketplace Azure `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview` . |
| **Wydawca**     | Unikatowy identyfikator organizacji w witrynie Azure Marketplace. Ofert użytkownika powinna być skojarzona z Twojego identyfikatora wydawcy. Nie można zmodyfikować tej wartości, po zapisaniu tej oferty. |
| **Nazwa**          | Nazwa wyświetlana oferty. Będzie wyświetlana w portalu Azure Marketplace i w portalu Cloud Partner. Może zawierać maksymalnie 50 znaków. W tym miejscu są wskazówki do uwzględnienia rozpoznawalną nazwę markę produktu. Nie dołączaj tutaj nazwę organizacji, chyba że jest, jak jest sprzedawane. Jeśli są marketingu tej oferty w ramach innych witryn sieci Web i publikacji, upewnij się, że nazwa jest dokładnie taka sama we wszystkich publikacjach. |
|  |  |
 
Kliknij przycisk **Zapisz** Aby zapisać postęp. Następna karta dodasz [jednostki SKU](./cpp-skus-tab.md) oferty.

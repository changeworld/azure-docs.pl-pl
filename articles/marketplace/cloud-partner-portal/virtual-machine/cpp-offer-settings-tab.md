---
title: Karta Ustawienia oferty maszyny wirtualnej w portalu Cloud Partner portalu Azure Marketplace
description: W tym artykule opisano na karcie Ustawienia oferują używanego podczas tworzenia oferty maszyny Wirtualnej portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: d361b6b8b08f9556cd57215ebdf82c1bf69d372d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938152"
---
# <a name="virtual-machine-offer-settings-tab"></a>Karta Ustawienia oferty maszyny wirtualnej

**Nowa oferta** zostanie otwarta strona maszyn wirtualnych na pierwszej karcie o nazwie **oferują ustawienia**.  

![Nowa strona oferty dla maszyn wirtualnych](./media/publishvm_004.png)


## <a name="offer-settings-fields"></a>Oferta ustawienia pola

W **oferują ustawienia** kartę, należy podać następujące pola.  Dołączonych gwiazdki (*) na nazwę pola wskazuje, że jest wymagane. 

|  **Pole**       |     **Opis**                                                          |
|  ---------       |     ---------------                                                          |
| **Identyfikator oferty\***   | Unikatowy identyfikator (w ramach profilu wydawcy) dla tej oferty. Ten identyfikator będzie widoczny w adresach URL produktu, szablony usługi Azure Resource Manager, i zgłasza rozliczeń. Jego ma maksymalnej długości 50 znaków i może zawierać tylko składa się z małe znaki alfanumeryczne i łączniki (-), ale nie może kończyć się kreską. W tym polu nie można zmienić po oferty przechodzi na żywo. <br> Na przykład, jeśli Contoso publikuje ofertę z identyfikator oferty **maszyny wirtualnej na przykład**, jest przypisany adres URL witryny Marketplace Azure `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview` . |
| **Publisher\***  | Unikatowy identyfikator organizacji w witrynie Azure Marketplace. Ofert użytkownika powinna być skojarzona z Twojego identyfikatora wydawcy. Nie można zmodyfikować tej wartości, po zapisaniu tej oferty. |
| **Nazwa\***       | Nazwa wyświetlana oferty. Będzie wyświetlana w portalu Azure Marketplace i w portalu Cloud Partner. Może zawierać maksymalnie 50 znaków. W tym miejscu są wskazówki do uwzględnienia rozpoznawalną nazwę markę produktu. Nie dołączaj tutaj nazwę organizacji, chyba że jest, jak jest sprzedawane. Jeśli są marketingu tej oferty w ramach innych witryn sieci Web i publikacji, upewnij się, że nazwa jest dokładnie taka sama we wszystkich publikacjach. |
|   |   |
 
Kliknij przycisk **Zapisz** po wprowadzonych wszystkie pola. 


## <a name="next-steps"></a>Kolejne kroki

Następna karta dodasz [jednostki SKU](./cpp-skus-tab.md) oferty.

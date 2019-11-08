---
title: Karta Ustawienia oferty maszyny wirtualnej w portal Cloud Partner portalu Azure Marketplace
description: Opisuje kartę Ustawienia oferty używane podczas tworzenia oferty maszyny wirtualnej portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: 6f7b90f6b02999869026db24836091233692143c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824440"
---
# <a name="virtual-machine-offer-settings-tab"></a>Karta Ustawienia oferty maszyny wirtualnej

**Nowa strona oferty** dla maszyn wirtualnych otwiera się na pierwszej karcie o nazwie **Ustawienia oferty**.  

![Nowa strona oferty dla maszyn wirtualnych](./media/publishvm_004.png)


## <a name="offer-settings-fields"></a>Pola ustawień oferty

Na karcie **Ustawienia oferty** musisz podać następujące pola.  Dołączona gwiazdka (*) w nazwie pola wskazuje, że jest to wymagane. 

|  **Pole**       |     **Opis**                                                          |
|  ---------       |     ---------------                                                          |
| **Identyfikator oferty\***   | Unikatowy identyfikator (w ramach profilu wydawcy) oferty. Ten identyfikator będzie widoczny w adresach URL produktów, szablonach Azure Resource Manager i raportach dotyczących rozliczeń. Ma maksymalną długość 50 znaków, może składać się tylko z małych znaków alfanumerycznych i kresek (-), ale nie może kończyć się znakiem kreski. Nie można zmienić tego pola po zakończeniu oferty. <br> Na przykład, jeśli firma Contoso publikuje ofertę z IDENTYFIKATORem oferty **przykładem-VM**, przypiszemy adres URL witryny Azure Marketplace `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview`. |
| **\* wydawcy**  | Unikatowy identyfikator organizacji w witrynie Azure Marketplace. Wszystkie oferty powinny być skojarzone z IDENTYFIKATORem wydawcy. Nie można zmodyfikować tej wartości po zapisaniu oferty. |
| **Nazwa\***       | Nazwa wyświetlana oferty. Ta nazwa będzie wyświetlana w witrynie Azure Marketplace i w portal Cloud Partner. Może zawierać maksymalnie 50 znaków. Wskazówki tutaj zawierają rozpoznawalną nazwę marki dla danego produktu. Nie dodawaj tutaj nazwy organizacji, chyba że jest to marketingowe. W przypadku wprowadzania tej oferty w innych witrynach sieci Web i publikacjach upewnij się, że nazwa jest dokładnie taka sama dla wszystkich publikacji. |
|   |   |
 
Po podaniu wszystkich pól kliknij przycisk **Zapisz** . 


## <a name="next-steps"></a>Następne kroki

Na następnej karcie dodasz [jednostki SKU](./cpp-skus-tab.md) do oferty.

---
title: Karta Ustawienia oferty maszyny wirtualnej w portalu partnerów w chmurze dla portalu Azure Marketplace
description: W tym artykule opisano kartę Ustawienia oferty używane podczas tworzenia oferty maszyny wirtualnej w portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: 94be2e5d3c2c941ab17401a743ea86acbe8b6252
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273804"
---
# <a name="virtual-machine-offer-settings-tab"></a>Karta Ustawienia oferty maszyny wirtualnej

> [!IMPORTANT]
> Od 13 kwietnia 2020 r. rozpoczniemy przenoszenie zarządzania ofertami maszyny wirtualnej platformy Azure do centrum partnerskiego. Po migracji utworzysz oferty i zarządzasz nimi w Centrum partnerów. Postępuj zgodnie z instrukcjami w [tworzenie oferty maszyny wirtualnej platformy Azure,](https://aka.ms/CreateAzureVMoffer) aby zarządzać zmigrowanymi ofertami.

Strona **Nowa oferta** dla maszyn wirtualnych zostanie otwarta na pierwszej karcie o nazwie Ustawienia **oferty**.  

![Nowa strona oferty dla maszyn wirtualnych](./media/publishvm_004.png)


## <a name="offer-settings-fields"></a>Pola Ustawienia oferty

Na karcie **Ustawienia oferty** należy podać następujące pola.  Dołączona gwiazdka (*) w nazwie pola wskazuje, że jest ona wymagana. 

|  **Pole**       |     **Opis**                                                          |
|  ---------       |     ---------------                                                          |
| **Identyfikator oferty\***   | Unikatowy identyfikator (w profilu wydawcy) dla oferty. Ten identyfikator będzie widoczny w adresach URL produktów, szablonach usługi Azure Resource Manager i raportach rozliczeniowych. Ma maksymalną długość 50 znaków, może składać się tylko z małych liter alfanumeryczne znaki i myślniki (-), ale nie może zakończyć się kreską. Tego pola nie można zmienić po uruchomieniu oferty. <br> Na przykład jeśli firma Contoso opublikuje ofertę z identyfikatorem oferty **sample-vm,** zostanie przypisany adres URL `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview` usługi Azure Marketplace . |
| **Wydawca\***  | Unikatowy identyfikator twojej organizacji w portalu Azure Marketplace. Wszystkie oferty powinny być powiązane z identyfikatorem wydawcy. Tej wartości nie można zmodyfikować po zapisaniu oferty. |
| **Nazwa\***       | Wyświetlana nazwa oferty. Ta nazwa będzie wyświetlana w portalu Azure Marketplace i w portalu cloud partnerów. Może zawierać maksymalnie 50 znaków. Wskazówki te obejmują rozpoznawalną markę produktu. Nie podawaj tutaj nazwy organizacji, chyba że jest ona w jaki sposób sprzedawana. Jeśli sprzedajesz tę ofertę w innych witrynach internetowych i publikacjach, upewnij się, że nazwa jest dokładnie taka sama we wszystkich publikacjach. |
|   |   |
 
Po podasz wszystkie pola **kliknij przycisk Zapisz.** 


## <a name="next-steps"></a>Następne kroki

W następnej karcie dodasz [jednostki SKU](./cpp-skus-tab.md) do oferty.

---
title: Ustawienia oferty aplikacji platformy Azure | Portal Azure Marketplace
description: Skonfiguruj ustawienia oferty dla oferty aplikacji platformy Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: c5e00f77ffeec61fb4fee4b4e8eb72b7c7ed1077
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827542"
---
# <a name="azure-application-offer-settings-tab"></a>Karta Ustawienia oferty aplikacji platformy Azure

W tym artykule opisano sposób konfigurowania ustawień oferty dla aplikacji platformy Azure.

Zostanie otwarta strona **Azure applications > Nowa oferta** z fokusem na karcie **Ustawienia oferty** . Gwiazdka (*) dołączona do nazwy pola wskazuje, że jest to wymagane.

![Formularz tożsamości oferty](./media/azureapp-offer-settings-tab.png)

## <a name="offer-identity-settings"></a>Ustawienia tożsamości oferty

W obszarze **tożsamość oferty**musisz podać informacje dotyczące pól opisanych w poniższej tabeli.  

|    Pole         |       Opis                                                            |
|  ---------       |     ---------------                                                          |
| **Identyfikator oferty\***       | Unikatowy identyfikator (w ramach profilu wydawcy) oferty. Ten identyfikator będzie widoczny w raportach dotyczących adresów URL produktów i szczegółowych informacji. Ma maksymalną długość 50 znaków i może zawierać małe litery alfanumeryczne i kreski (-). (Identyfikator nie może kończyć się znakiem kreski). **Uwaga:** Nie można zmienić tego pola po zakończeniu oferty. <br> Na przykład, jeśli firma Contoso publikuje ofertę z IDENTYFIKATORem oferty **przykładowo-Container**, przypiszemy adres URL witryny Azure Marketplace `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-container?tab=Overview`. |
| **IDENTYFIKATOR wydawcy\***     | Unikatowy identyfikator organizacji w witrynie Azure Marketplace. Wszystkie oferty powinny być skojarzone z IDENTYFIKATORem wydawcy. Tej wartości nie można zmienić po zapisaniu oferty. |
| **Nazwa\***          | Nazwa wyświetlana oferty. Ta nazwa jest wyświetlana w portalu Azure Marketplace i w portal Cloud Partner. Może zawierać maksymalnie 50 znaków. Zalecamy użycie rozpoznawalnej nazwy marki dla produktu. Nie dołączaj nazwy organizacji, chyba że jest to produkt sprzedawany na rynku. W przypadku wprowadzania tej oferty w innych witrynach sieci Web i publikacjach upewnij się, że nazwa jest dokładnie taka sama dla wszystkich publikacji. |
|  |  |

Wybierz pozycję **Zapisz** , aby zapisać ustawienia oferty.

## <a name="next-steps"></a>Następne kroki

Użyj karty [jednostki SKU](./cpp-skus-tab.md) , aby skonfigurować jednostki SKU oferty.
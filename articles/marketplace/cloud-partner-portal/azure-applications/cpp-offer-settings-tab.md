---
title: Ustawienia oferty aplikacji platformy Azure | Portal Azure Marketplace
description: Skonfiguruj ustawienia oferty dla oferty aplikacji platformy Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 789b783629b3cc3528eba1883b21051604cf6e14
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942929"
---
# <a name="azure-application-offer-settings-tab"></a>Karta Ustawienia oferty aplikacji platformy Azure

W tym artykule opisano sposób konfigurowania ustawień oferty aplikacji platformy Azure.

**Aplikacji platformy Azure > Nowa oferta** zostanie otwarta strona z fokusem na **oferują ustawienia** kartę. Znak gwiazdki (*) dodanym na końcu nazwy pola wskazuje, że jest to wymagane.

![Formularz tożsamości oferty](./media/azureapp-offer-settings-tab.png)

## <a name="offer-identity-settings"></a>Ustawienia tożsamości oferty

W obszarze **oferują tożsamości**, musisz podać informacje w polach, które opisano w poniższej tabeli.  

|    Pole         |       Opis                                                            |
|  ---------       |     ---------------                                                          |
| **Identyfikator oferty\***       | Unikatowy identyfikator (w ramach profilu wydawcy) dla tej oferty. Ten identyfikator będzie widoczna w raportach produktu adresów URL i szczegółowych informacji. Może się składać maksymalnie 50 znaków i może używać małe znaki alfanumeryczne i łączniki (-). (Identyfikator nie może kończyć się kreską.) **Uwaga:** W tym polu nie można zmienić po oferty przechodzi na żywo. <br> Na przykład, jeśli Contoso publikuje ofertę z identyfikator oferty **przykładowy kontener**, został przypisany adres URL witryny Marketplace Azure `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-container?tab=Overview`. |
| **Identyfikator wydawcy\***     | Unikatowy identyfikator organizacji w witrynie Azure Marketplace. Ofert użytkownika powinna być skojarzona z Twojego identyfikatora wydawcy. Nie można zmienić tej wartości, po zapisaniu oferty. |
| **Nazwa\***          | Nazwa wyświetlana oferty. Ta nazwa jest wyświetlana w portalu Azure Marketplace i w portalu Cloud Partner. Może zawierać maksymalnie 50 znaków. Zalecamy używanie rozpoznawalną nazwę markę produktu. Nie dołączaj nazwę swojej organizacji, chyba że jest, jak Twój produkt jest oferowana na rynku. Jeśli są marketingu tej oferty w ramach innych witryn sieci Web i publikacji, upewnij się, że nazwa jest dokładnie taka sama we wszystkich publikacjach. |
|  |  |

Wybierz **Zapisz** można zapisać ustawień oferty.

## <a name="next-steps"></a>Kolejne kroki

Użyj [jednostki SKU](./cpp-skus-tab.md) kartę, aby skonfigurować jednostki SKU oferty.
---
title: Ustawienia dla obrazu kontenerów platformy Azure oferty | Dokumentacja firmy Microsoft
description: Skonfiguruj ustawienia oferty dla kontenerów platformy Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pbutlerm
ms.openlocfilehash: 9a669ebad674f938d11c8e372e6f586ca3c67f03
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61472932"
---
# <a name="container-offer-settings-tab"></a>Karta Ustawienia oferują kontenera

**Kontenery > Nowa oferta** zostanie otwarta strona z fokusem na **oferują ustawienia** kartę. 

![Zaoferowania tożsamości](./media/containers-offer-settings.png)

## <a name="offer-identity-settings"></a>Ustawienia tożsamości oferty

W obszarze **oferują tożsamości**, musisz podać informacje w polach, które opisano w poniższej tabeli. Znak gwiazdki (*) dodanym na końcu nazwy pola wskazuje, że jest to wymagane. 

|  **Pole**       |     **Opis**                                                          |
|  ---------       |     ---------------                                                          |
| **Identyfikator oferty**       | Unikatowy identyfikator (w ramach profilu wydawcy) dla tej oferty. Ten identyfikator będzie widoczna w raportach produktu adresów URL i szczegółowych informacji. Może się składać maksymalnie 50 znaków i może używać małe znaki alfanumeryczne i łączniki (-). (Identyfikator nie może kończyć się kreską.) **Uwaga:** W tym polu nie można zmienić po oferty przechodzi na żywo. <br> Na przykład, jeśli Contoso publikuje ofertę z identyfikator oferty **przykładowy kontener**, został przypisany adres URL witryny Marketplace Azure `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-container?tab=Overview`. |
| **Identyfikator wydawcy**     | Unikatowy identyfikator organizacji w witrynie Azure Marketplace. Ofert użytkownika powinna być skojarzona z Twojego identyfikatora wydawcy. Nie można zmienić tej wartości, po zapisaniu oferty. |
| **Nazwa**          | Nazwa wyświetlana oferty. Ta nazwa jest wyświetlana w portalu Azure Marketplace i w portalu Cloud Partner. Może zawierać maksymalnie 50 znaków. Zalecamy używanie rozpoznawalną nazwę markę produktu. Nie dołączaj nazwę swojej organizacji, chyba że jest, jak Twój produkt jest oferowana na rynku. Jeśli są marketingu tej oferty w ramach innych witryn sieci Web i publikacji, upewnij się, że nazwa jest dokładnie taka sama we wszystkich publikacjach. |
|  |  |

Wybierz **Zapisz** można zapisać ustawień oferty.

## <a name="next-steps"></a>Kolejne kroki

Użyj [jednostki SKU](./cpp-skus-tab.md) kartę, aby skonfigurować jednostki SKU oferty.

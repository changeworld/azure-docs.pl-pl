---
title: Informacje techniczne dla oferty aplikacji Power BI — portalu Azure Marketplace | Dokumentacja firmy Microsoft
description: Skonfiguruj pola informacje techniczne dla oferty aplikacji Power BI dla Microsoft AppSource Marketplace.
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
ms.openlocfilehash: 71f226d2ba471a2ccbba3123015697da43d2cf80
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667066"
---
# <a name="power-bi-apps-technical-info-tab"></a>Karta informacje techniczne aplikacji Power BI zasilania

**Informacje techniczne** karcie **nowa oferta** strony są określane Instalatora usługi Power BI — adres URL pakietu oraz wszelkie dodatkowe informacje wymagane do weryfikacji nowej oferty.  We wstępnym wydaniu wszystkie aplikacje usługi Power BI są bezpłatne i dostępne do pobrania z witryny AppSource bez dodatkowych opłat. W rezultacie nie można zdefiniować żadnych magazynową jednostki (jednostki SKU) dla tego typu oferty.

![Karta informacje techniczne](./media/technical-info-tab.png)


## <a name="technical-info-fields"></a>Techniczne informacje o polach 

W **informacje techniczne** kartę, należy podać następujące pola.  Dołączonych gwiazdki (*) na etykiecie pola wskazuje, że jest wymagane.

|        Pole          |  Opis                                                                 |
|    ---------------    |  ----------------------------------------------------------------------------|
| **Adres URL Instalatora**     | Adres generowane przez usługę Power BI podczas publikowania aplikacji i przenieś go do produkcji.  Aby uzyskać więcej informacji na temat sposobu generowania adresu URL, zobacz [publikowanie aplikacji usługi w usłudze Power BI](https://docs.microsoft.com/power-bi/service-create-distribute-apps).  |
|  **Instrukcje weryfikacji**  |  Opcjonalny tekst instrukcji (max 3000 znaków) zespół weryfikacyjny Microsoft do pomocy w konfigurowaniu, łączenie i testowania aplikacji, w tym: typową konfigurację ustawień, testowe konta lub parametry, które mogą służyć do testowania opcji "Połącz dane" itp. Te informacje tylko będą widoczne dla zespołu sprawdzania poprawności i służy tylko do celów sprawdzania poprawności.  |
| **Czy ta aplikacja zostanie utworzona jako pakiet zawartości Power BI?** | Obecnie to pole jest używane wewnętrznie. Pozostaw wartość ustawiona na wartość domyślną `No`; w przeciwnym razie zmiany w tym polu, aby `No` może utrudniać publikowania.  |  
|  |  |


## <a name="next-steps"></a>Kolejne kroki

W ciągu następnych [szczegóły Storefront](./cpp-storefront-details-tab.md) karcie będzie zapewniać informacji prawnych i marketingu dla aplikacji.


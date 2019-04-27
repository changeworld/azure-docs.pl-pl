---
title: Power BI aplikacji ofertę — portalu Azure Marketplace | Dokumentacja firmy Microsoft
description: Jak opublikować aplikację usługi Power BI w portalu Marketplace, Microsoft AppSource.
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
ms.date: 03/27/2019
ms.author: pbutlerm
ms.openlocfilehash: f18a1b05e5a38a79945d8df6706dd2147d6b43df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725842"
---
# <a name="power-bi-app-offer"></a>Oferta aplikacji Power BI

|              |                                |
|--------------|--------------------------------|
| W tym artykule opisano sposób publikowania aplikacji Power BI, Microsoft [marketplace usługi AppSource](https://appsource.microsoft.com/).  Aplikacja Power BI pakiety można dostosować zawartość usługi Power BI, w tym zestawy danych, raporty i pulpity nawigacyjne. Można wdrożyć aplikację do innych dzierżaw usługi Power BI za pomocą usługi AppSource, korekt i dostosowania dozwolone przez dewelopera i podłącz go do swoich danych. | ![Ikona usługi Power BI](./media/powerbi-icon.png) |


W tym artykule jest podzielony na trzy główne elementy:

-   [Wymagania wstępne](./cpp-prerequisites.md). Wymagania techniczne i biznesowe, które do tworzenia i publikowania oferty aplikacji Power BI.
-   [Tworzenie oferty aplikacji Power BI](./cpp-create-offer.md). Jak utworzyć wpis oferty aplikacji Power BI przy użyciu [portalu Cloud Partner](https://cloudpartner.azure.com).
-   [Publikowanie oferty aplikacji Power BI](./cpp-publish-offer.md). Jak przesłać nową ofertę usługi AppSource do publikowania i jak aktualizowanie istniejącej oferty.


## <a name="publishing-steps"></a>Kroki publikowania

Poniżej przedstawiono ogólne kroki umożliwiające publikowanie oferty aplikacji Power BI:

![Aplikacja Power BI oferują kroki publikowania](media/publishing-steps.png)

Oto oferty aplikacji Power BI procesu publikowania:

1. Tworzenie aplikacji szablonu w usłudze Power BI. Ta akcja generuje pakiet instalacyjny adresu URL, który reprezentuje podstawowy zasobów technicznych dla oferty. Również w tej chwili przyczyniają się do pakietu test przedprodukcyjnego. Aby uzyskać więcej informacji, zobacz [co to są aplikacje szablonu usługi Power BI?](https://docs.microsoft.com/power-bi/service-template-apps-overview). 
2. Zbieraj lub Utwórz oferty materiały marketingowe, w tym: oficjalna nazwa, opis, logo, itp. 
3. Zbieraj lub Utwórz oferty prawnych i obsługuje dokumentów: *warunki użytkowania*, *zasady zachowania poufności informacji*, *zasady pomocy technicznej*, pomoc dla użytkowników itp.
4. Utwórz ofertę: Użyj portalu Cloud Partner, aby skonfigurować szczegóły oferty, łącznie z opisem oferty, materiały marketingowe, informacje prawne, informacje o pomocy technicznej i specyfikacje zasobów.  Po tej oferty jest w pełni określona, Prześlij go do publikacji.
5. Monitoruj proces publikowania w portalu Cloud Partner.  W tym kroku zespół organizujący dołączanie usługi AppSource testy, weryfikuje i potwierdza aplikację. 
6. Po pomyślnym certyfikowaniu aplikacji przejrzyj go w jego środowisku testowym, a następnie zwolnij go. 
7. Aplikacja Power BI znajduje się w usłudze AppSource, ("zbliża się na żywo" to).
8. W usłudze Power BI przyczyniają się do pakietu przedprodukcyjnego do środowiska produkcyjnego. Aby uzyskać więcej informacji, zobacz [Zarządzanie wersja aplikacji szablonu](https://docs.microsoft.com/power-bi/service-template-apps-create#manage-the-template-app-release).


## <a name="next-steps"></a>Kolejne kroki

Przed utworzeniem oferty aplikacji Power BI i opublikuj go w usłudze AppSource, musi spełniać [wymagania](./cpp-prerequisites.md) publikowania aplikacji Power BI do usługi AppSource.

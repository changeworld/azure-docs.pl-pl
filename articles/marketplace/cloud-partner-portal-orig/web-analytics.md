---
title: Analiza sieci Web
description: .
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: d47a67be157f71a30a6ae155790ed78a78fd6743
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73814646"
---
<a name="web-analytics"></a>Analiza sieci Web
=============

Ten artykuł zawiera instrukcje dotyczące uczenia się i używania analizy sieci Web w celu najlepszego rozwoju firmy. Obecnie ta karta Insights jest dostępna dla dowolnych ofert AppSource.

Po skompilowaniu i opublikowaniu oferty następnym etapem podróży jest śledzenie i mierzenie sukcesu\'. Dzięki funkcji **Web Analytics**mogliśmy dowiedzieć się, jak dobrze każda z ofert działa w portalu Marketplace. Aby rozpocząć podróż, przejdź do strony usługi Insights po lewej stronie portal Cloud Partner, aby wyświetlić kartę nowe analizy.

![Strona usługi Webanalytics](./media/si-getting-started/WebAnalytics1.png)

Zobaczysz rozbudowany pulpit nawigacyjny dla identyfikatora wydawcy, który został utworzony za pomocą firmy Microsoft Power BI i umożliwi wyświetlenie poszczególnych ofert\' danych odświeżanych codziennie.

<a name="microsoft-campaigns"></a>**Kampanie firmy Microsoft**
-----------------------

Aby można było rozwijać oferty i śledzić rozwój ofert, umożliwia korzystanie z **kampanii firmy Microsoft** na portal Cloud partner. Kampanie to nowo obsługiwana funkcja portalu Marketplace, która umożliwi śledzenie różnych kanałów wysyłających klientów do strony szczegółów aplikacji.

### <a name="how-to-make-a-campaign"></a>**Jak utworzyć kampanię**

Najprostszym sposobem opisywania kampanii jest dodanie niestandardowego słowa/terminu do adresu URL, który znajduje się na stronie szczegółów aplikacji w portalu Marketplace. Usługi Google, Bing, LinkedIn i wiele innych lokacji zachęcają do utworzenia anonsu, linku z witryny do żądanej lokacji.

Ogólnie rzecz biorąc, te wysiłki mają na celu ułatwienie klientom nowych klientów i istotne jest zmierzenie sukcesu wykonywanych przez poszczególne kanały. Tutaj są dostępne kampanie.

Istnieją dwa sposoby generowania własnych kampanii.

1. Dodaj do adresu URL parametr zapytania **mktcmpid** , który opisuje, co to jest kampania oraz na jakie dane/wydarzenie pochodzą od tych klientów.

Na przykład można użyć: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign>

1. (Zaawansowane): Użyj jednego z naszych obsługiwanych, ogólnych identyfikatorów kampanii w adresie URL. Chcemy, aby dodaliśmy do nich dodatkowe Tagi ref, które należy użyć, dlatego firma Microsoft obsługuje Konwencję, aby automatycznie rozpoznawać te dodatkowe Tagi:
    
    1. **UTM\_kampania**
    2. **Źródło\_UTM**
    3. **umieszczone**
    4. **SRC**

Na przykład można użyć: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?utm_campaign=NewCampaign>

Możesz wybrać kombinację wielu tych identyfikatorów kampanii, aby dodatkowo identyfikować wiele źródeł, które będą napędzać daną kampanię, taką jak miejsce, z którego pochodzi klient (poczta e-mail, blog, Źródło mediów społecznościowych itp.).

Na przykład:

1. Odwołujący biuletyn: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=newsletter>
2. Odwołujący serwis LinkedIn: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=LinkedIn>

### <a name="ensuring-campaigns-pass-through-all-your-pages"></a>**Zapewnienie, że kampanie przechodzą przez wszystkie strony**

Może istnieć scenariusz, w którym kampanie zawierają pośrednią stronę, do której prowadzi ruch, a następnie wysyła klientów do portalu Marketplace. Ważne jest, aby przekazać początkowe identyfikatory kampanii do końcowego adresu URL, który został wysłany do portalu Marketplace.

Oto przykład:

1. Pracownicy działu marketingu kupowają reklamy z usługi Google w celu kierowania ruchu do firmy\'<https://contoso.com>stronie docelowej. Ta strona docelowa zawiera \"spróbuj użyć linku\" produktu, który prowadzi do <https://appsource.com>.
2. Użytkownik klika pozycję AD i grunty na stronie docelowej\'s firmy.
    1.  Adres URL odwołania = google.com
    2.  Adres URL strony docelowej = <https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName>
3. Użytkownik klika \"Wypróbuj mój produkt\" link i przechodzi do AppSource.
    1. Adres URL odwołania = <https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName>
    2. Adres URL strony docelowej (**upewnij się, że ten adres URL ma utm\_kampanię i UTM źródło\_dodane do tego adresu URL**) = [https://appsource.microsoft.com/product/dynamics-365/contoso.offername?**UTM\_kampania = MyCampaignAdName & UTM\_Source = MySourceAdName** ](https://appsource.microsoft.com/product/dynamics-365/contoso.offername?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName)

<a name="how-to-evaluate-the-success-of-a-campaign"></a>Jak oszacować sukces kampanii
-----------------------------------------

### <a name="page-visits-by-campaign"></a>**Odwiedziny stron według kampanii**

![WebAnalytics2](./media/si-getting-started/WebAnalytics2.png)

Jest to podział poszczególnych codziennych odwiedzin stron przez kampanię, z której pochodzą.

### <a name="conversion-rate-by-campaign"></a>**Współczynnik konwersji według kampanii**

![WebAnalytics3](./media/si-getting-started/WebAnalytics3.png)

Podobnie jak pokazujemy stopień konwersji całej oferty, na tym wykresie można zobaczyć, jak działają różne kampanie. Ten wykres powinien pomóc w ustaleniu, gdzie pochodzą z wyższego stopnia konwersji.

### <a name="distribution-by-campaign"></a>**Dystrybucja według kampanii**

![WebAnalytics4](./media/si-getting-started/WebAnalytics4.png)

Podobnie jak w przypadku domen klientów, ten wykres umożliwia wyświetlenie dystrybucji danych na kampanię, do której użytkownicy znajdują się w portalu Marketplace. \_nokampania oznacza, że klient nie miał identyfikatora kampanii w adresie URL podczas przechodzenia do portalu Marketplace.

<a name="next-steps"></a>**Następne kroki**
--------------

Teraz, gdy masz możliwość śledzenia sukcesu ofert, chcemy zachęcić Cię do tworzenia własnych kampanii.

Jeśli masz pytania/żądania funkcji, Podziel je za pośrednictwem opinii znajdującej się w prawym górnym rogu.

![Opinie w portal Cloud Partner](./media/si-getting-started/WebAnalytics5.png)

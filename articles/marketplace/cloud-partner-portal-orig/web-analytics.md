---
title: Analiza sieci Web
description: .
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 4b75b2cbd629204e7edbf1196eec9b03cb7a6736
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64943153"
---
<a name="web-analytics"></a>Analiza sieci Web
=============

Ten artykuł zawiera o instrukcje do nauczenia i używania funkcji Web Analytics, aby jak najlepiej Rozwijaj swoją działalność. Obecnie na tej karcie Insights jest dostępna dla dowolnej oferty usługi AppSource.

Skoro już skompilowane i opublikowane oferty, następnej części swoją podróż po jest śledzenie i mierzyć jej\' sukces. Za pomocą **funkcji Web Analytics**, dodaliśmy możliwość wyświetlania, dokładnie tak jak robi wszystkich Twoich ofert w portalu marketplace. Aby rozpocząć ten proces, przejdź do strony szczegółowych informacji po lewej stronie portalu Cloud Partner, aby zobaczyć nową kartę analizy.

![Strona WebAnalytics](./media/si-getting-started/WebAnalytics1.png)

Zobaczysz rozbudowany pulpit nawigacyjny dla Twojego Identyfikatora wydawcy, została opracowana za pomocą usługi Microsoft Power BI, która umożliwia wyświetlenie wszystkich Twoich ofert\' danych, który jest ustawiony na codzienne odświeżanie.

<a name="microsoft-campaigns"></a>**Microsoft Campaigns**
-----------------------

Rozwijaj swoje oferty i śledzić wzrostem Twoich ofert, w celu udostępniliśmy możliwość używania **kampanie firmy Microsoft** w portalu Cloud Partner. Kampanie są funkcją nowo obsługiwanych dla portalu marketplace, która umożliwi służących do śledzenia różnych kanałów, które wysyłają klientów na stronie szczegółów aplikacji.

### <a name="how-to-make-a-campaign"></a>**Jak utworzyć kampanię**

Najprostszym sposobem opisują kampanii jest dodajesz niestandardowe słowo/termin do adresu URL, umieszczać swoje dokumenty na stronie szczegółów aplikacji w portalu marketplace. Google, Bing, LinkedIn i wiele innych witryn zachęcamy do tworzenia anonsu, połączyć się z ich witryny w żądanej witryny.

Ogólnie rzecz biorąc tych działań, które mają pomóc klientom nowego dysku w produkcie i jest niezbędne do oceny sukcesu sposobu działania wszystkich kanałów. Jest to, gdzie są dostępne w kampanii.

Istnieją dwa sposoby generowania własnych kampanii.

1. Dodaj do adresu URL parametru zapytania **mktcmpid** opisujący, co to jest kampanii i strony lub zdarzenia tych klientów pochodzą.

Na przykład można użyć: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign>

1. (Zaawansowane): Użyj jednej z naszych obsługiwana i ogólny kampanii identyfikatorów w adresie URL. Chcemy być ograniczanych znacznikami ref dodatkowe, które należy użyć, aby firma Microsoft obsługuje Konwencji automatycznie rozpoznaje te dodatkowe tagi:
    
    1. **utm\_campaign**
    2. **utm\_źródła**
    3. **REF**
    4. **src**

Na przykład można użyć: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?utm_campaign=NewCampaign>

Użytkownik może być kombinacją wielu z tych identyfikatorów kampanii do dalszego identyfikowania wielu źródeł kierowania ruchu dla kampanii, takich jak klient pochodzenia (wiadomości e-mail, blogów, źródła mediów społecznościowych itp.).

Na przykład:

1. Biuletyn — odwołania:  <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=newsletter>
2. LinkedIn odwołania:  <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=LinkedIn>

### <a name="ensuring-campaigns-pass-through-all-your-pages"></a>**Zapewnianie kampanii przekazywania wszystkich stron**

Może wystąpić scenariusz, w których kampanii mają strona pośrednia które napędzają ruchu, następnie przechodzi do wysyłania klientów w portalu Marketplace. Jest ważne dopuszczone początkowej kampanię z użyciem identyfikatorów do końcowego adresu URL, wysyłanych do portalu marketplace.

Oto przykład:

1. Pracownik Marketing kupuje reklam od firmy Google do dysku ruchu do firmy\'strona docelowa s <https://contoso.com>. Ta strona docelowa będzie mieć \"spróbuj dany produkt\" łącze prowadzące do <https://appsource.com>.
2. Użytkownik klika ad i wyładowuje na jego firma\'strona docelowa s.
    1.  Adres odwołania URL = google.com
    2.  Adres URL strony = <https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName>
3. Użytkownik klika polecenie \"spróbuj dany produkt\" połączyć, a następnie przechodzi do usługi AppSource.
    1. Adres odwołania URL =  <https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName>
    2. Adres URL strony (**upewnij się, że ten adres URL ma utm\_kampanii i utm\_źródło dodane do tego adresu URL**) = [ https://appsource.microsoft.com/en-us/product/dynamics-365/contoso.offername? **utm\_ kampanii = MyCampaignAdName & utm\_ źródła = MySourceAdName**](https://appsource.microsoft.com/en-us/product/dynamics-365/contoso.offername?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName)

<a name="how-to-evaluate-the-success-of-a-campaign"></a>Jak ocenić Powodzenie kampanii
-----------------------------------------

### <a name="page-visits-by-campaign"></a>**Strona wizyty według kampanii**

![WebAnalytics2](./media/si-getting-started/WebAnalytics2.png)

Jest to podział każdego swoje codzienne odwiedzin strony przez kampanii, które pochodzą z.

### <a name="conversion-rate-by-campaign"></a>**Współczynnik konwersji według kampanii**

![WebAnalytics3](./media/si-getting-started/WebAnalytics3.png)

Podobnie jak pokazujemy współczynnik konwersji całej oferty, na tym wykresie można zobaczyć podział jak robią różnych kampanii. Ten wykres pomoże zidentyfikować, skąd pochodzą kampanii wyższe szybkości konwersji.

### <a name="distribution-by-campaign"></a>**Rozkład według kampanii**

![WebAnalytics4](./media/si-getting-started/WebAnalytics4.png)

Podobnie jak przyjrzymy się domen klientów, ten wykres umożliwia zobaczyć rozkład danych na kampanię, który użytkownicy będą dostępne w portalu Marketplace, w obszarze. \_NoCampaign oznacza, że klient nie miał identyfikator kampanii w adresie url po ich przejście do portalu marketplace.

<a name="next-steps"></a>**Następne kroki**
--------------

Teraz, gdy masz możliwość śledzenia sukces swojej oferty, chcemy zachęcamy do tworzenia własnych kampanii.

Jeśli masz pytania/funkcja żądań, udostępniać je za pomocą informacji zwrotnych dotyczących znajduje się w prawym górnym rogu.

![Opinie w portalu Cloud Partner](./media/si-getting-started/WebAnalytics5.png)

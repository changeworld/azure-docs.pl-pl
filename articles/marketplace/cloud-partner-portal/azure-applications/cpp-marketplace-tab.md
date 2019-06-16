---
title: Oferty platformy Azure aplikacji karta w portalu Marketplace
description: Karta w portalu Marketplace umożliwia zidentyfikowanie zasobów marketingowych dla oferty aplikacji platformy Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 7ea6e6be0597a114b02fad8c41e37d21ce1f6028
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942964"
---
# <a name="azure-application-marketplace-tab"></a>Karta Marketplace aplikacji platformy Azure

Karta witryny Marketplace do opisu aplikacji systemu Azure i zapewnia zasobów marketingowych. Ta karta zawiera następujące formy: Omówienie, Marketing artefaktów, zarządzania potencjalnych klientów i informacje prawne.

## <a name="overview-form"></a>Formularz przeglądu

Formularz przeglądu ma wymagane i opcjonalne pola, pokazane na następnym zrzucie ekranu. Wymagane pola są wskazanych przez znak gwiazdki (*).

![Formularz przeglądu](./media/azureapp-marketplace-overview.png)

W poniższej tabeli opisano ustawienia służące do tworzenia storefront dla oferty.   Wymagane są pola dołączany gwiazdką.

|      Pole         |    Opis    |
|  ---------------   |  ---------------  |
| **Tytuł\***        | Tytuł oferty. Będzie ona wyświetlana ważnej w portalu marketplace. Maksymalna długość to 50 znaków. |
| **Podsumowanie\***      | Krótkie podsumowanie oferty. Maksymalna długość wynosi 100 znaków.           |
| **Długie podsumowanie\*** | Już podsumowanie ofertę (chociaż może być taki sam jak podsumowanie). Maksymalna długość to 256 znaków.           |
| **Opis elementu\***  | Opis oferty. Maksymalna długość to 3000 znaków. Proste formatowanie HTML jest dozwolony, w tym &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt; znacznikami nagłówka.  |
| **Identyfikator marketingu\*** | Unikatowy adres URL, aby skojarzyć tę ofertę, obejmują zazwyczaj organizacji i nazwę rozwiązania, maksymalnej długości 50 znaków. Wybierz krótkie, przyjazne identyfikator marketing dla usługi. To posłuży w portalu marketplace w adresach URL dla tej oferty. Na przykład jeśli Twój identyfikator wydawcy to "contoso" i identyfikatora marketing jest "aplikacja", adres URL do swojej oferty w portalu Azure Marketplace będzie https://azuremarketplace.microsoft.com/en-us/marketplace/apps/contoso.sampleApp  
| **Identyfikatory subskrypcji (wersja zapoznawcza)\*** | Dodaj od 1 do 100 identyfikatorów subskrypcji podglądy. Te subskrypcje biały na liście mają dostęp do oferty, mimo że jest dostępna w wersji zapoznawczej, po jego opublikowaniu, zanim usługa zostanie wprowadzona na żywo.          |
| **Przydatne linki**    | Opcjonalnie możesz podać łącza do różnych zasobów dla użytkowników, oferty, takie jak obsługa, dokumentacji, forum itp.  Zalecane jest, Dodaj co najmniej jedno łącze do dokumentacji.            |
| **Sugerowane kategorie (maks. 5)\*** | Wybierz kategorie jednej do siedmiu. Wybrane kategorie są używane do mapowania oferty dostępne kategorie produktów w witrynie Azure Marketplace i witryny Azure Portal. Będą one wyświetlane na stronie przeglądania i na stronie szczegółów produktu. |
|  |  |


## <a name="marketing-artifacts"></a>Marketing artefaktów

Formularz marketingu artefaktów ma wymagane i opcjonalne pola, pokazane na następnym zrzucie ekranu. Wymagane pola są wskazanych przez znak gwiazdki (*).

![Marketing artefaktów formularza](./media/azureapp-marketplace-artifacts.png)

W poniższej tabeli opisano marketingu artefaktów.

|      Pole         |    Opis    |
|  ---------------   |  ---------------  |
| **Małe\***        | Małe logo: 40 40 pikseli w formacie PNG     |
| **Średni\***       | Średnie logo: 90 x 90 pikseli w formacie PNG    |
| **Duże\***        | Duże logo: 115 x 115 pikseli w formacie PNG   |
| **Szerokie\***         | Szerokie logo: 255 x 115 pikseli w formacie PNG    |
| **Element Hero**           | Opcjonalny element hero logo: 815 x 290 pikseli w formacie PNG. **Uwaga:** Nie można usunąć ikonę hero, po zakończeniu przekazywania. |
| **Zrzuty ekranu (maks. 5)** |        Zrzuty ekranu są wyświetlane na stronie szczegółów produktu. Są one dobrym sposobem na wizualne przedstawianie działanie aplikacji i sposób jej działania. Na przykład można wyświetlić diagramy architektury lub użyj ilustracje wielkości liter. Zrzuty ekranu są opcjonalne i maksymalnie 5 na jednostki SKU. Aby dodać zrzut ekranu:<ul><li>Wybierz **+ Dodaj zrzut ekranu** aby otworzyć okno zrzut ekranu</li><li>**Nazwa** — wprowadź nazwę/tytuł (maksymalna długość 100 znaków).</li><li>**Przekaż** -przekazaniem obrazu. Musi być w formacie PNG, a rozmiar jest 533 x 324 pikseli.</li></ul>           |
| **Dodawanie filmu wideo**      | Opcjonalnie, filmy wideo są wyświetlane na stronie szczegółów produktu. Są one dobrym sposobem na wizualne przedstawianie działanie aplikacji i sposób jej działania. Aby dodać film wideo: <ul><li>Wybierz **+ Dodaj wideo** aby otworzyć okno wideo</li><li>**Nazwa** — wprowadź nazwę/tytuł (maksymalna długość 100 znaków).</li><li>**Link** — wprowadź adres URL dla witryny, który jest hostem wideo (działanie serwisu YouTube lub Vimeo)</li><li>**Miniatura** — Przekaż miniaturę. Musi być w formacie PNG, a rozmiar jest 533 x 324 pikseli.</li></ul>          |
|  |  |


### <a name="artifact-examples-in-azure-marketplace"></a>Przykłady artefaktu w portalu Azure Marketplace

Następny zrzut ekranu przedstawia przykład wynik wyszukiwania w witrynie Marketplace.

![Wynik wyszukiwania oferty w portalu Marketplace](./media/azureapp-marketplace-example-browse.png)

Na poniższej ilustracji przedstawiono sposób wyświetlania oferty w portalu Marketplace po odbiorca kliknie kafelka oferty w wynikach wyszukiwania.

![Szczegóły wyniku wyszukiwania oferty w portalu Marketplace](./media/azureapp-marketplace-example-details.png)


### <a name="artifact-examples-in-azure-portal"></a>Przykłady artefaktu w witrynie Azure Portal

Poniższy ekran przechwytuje show wyświetlania oferty w witrynie Azure Portal. Oferty aplikacji, w tym przykładzie zostanie znaleziony, przechodząc do **Marketplace > wszystko > Programowanie i testowanie > Jenkins**. Oferta usługi Jenkins pokazuje logo, tytuł i nazwa wyświetlana wydawcy.

![Przeglądaj ofert w witrynie Azure portal](./media/azureapp-portalbrowse-artifacts-jenkins.png)

Następnym zrzucie ekranu przedstawiono szczegółowe informacje o aplikacji, gdy użytkownik wybierze Jenkins.

![Szczegóły oferty w witrynie Azure portal](./media/azureapp-portal-artifacts-jenkins-details.png)


### <a name="logo-guidelines"></a>Wytyczne dotyczące logo

Wszystkie logo, które są przekazywane do portalu Cloud Partner powinien postępuj zgodnie z wytycznymi:

- Projekt platformy Azure ma prostą paletę kolorów. Niskich numer podstawowy i pomocniczy kolory na logo.
- Kolory motywu w witrynie Azure Portal są białe i czarne. Należy unikać używania tych kolorów jako kolor tła dla Twojego logo. Użyj koloru, który spowoduje, że Twoje logo widocznym w witrynie Azure portal. Zalecamy proste kolory podstawowe. Jeśli korzystasz z przezroczystym tłem, upewnij się, że logo/tekstu nie są białe, black lub niebieski.
- Nie używaj gradientu tła na logo.
- Należy unikać wprowadzania tekstu, nawet Twoja firma lub nazwa marki na logo. Wygląd i działanie logo powinno być "płaską" i unikać gradientów.
- Nie rozciąganie logo.


#### <a name="hero-logo"></a>Logo Duży obraz

Logo usługi Hero jest opcjonalne.

>[!IMPORTANT]
>Nie można usunąć Hero logo, po zakończeniu przekazywania.

Użyj następujących wytycznych dotyczących Hero logo:

- Czarny, biały i przezroczyste tło nie są dozwolone.
- Należy unikać używania żadnych jasny kolor tła logo. Nazwa wyświetlana wydawcy, plan tytuł oferty długie podsumowanie są wyświetlane w kolorze białym czcionki i musi być na tle.
- Należy unikać większość tekstu podczas projektowania logo. Nazwa wydawcy, tytuł planu, oferty długie Podsumowanie i przycisk Utwórz są osadzane programowo wewnątrz logo gdy oferty na liście.
- Obejmują nieużywane miejsce prostokątny po prawej stronie logo usługi hero. To puste miejsce jest 415 x 100 pikseli i przesunięcie od lewej w 370 pikseli.


## <a name="lead-management"></a>Zarządzanie potencjalnymi klientami

Formularz prowadzić zarządzania zawiera opcjonalne pole, aby skonfigurować zarządzanie potencjalnymi klientami. Aby skonfigurować zarządzanie potencjalnymi klientami, wybierz miejsce docelowe potencjalnych klientów z listy rozwijanej. Następny zrzut ekranu przedstawia dostępne miejsca docelowe.

![Wybierz potencjalnego klienta zarządzania docelowego](./media/azureapp-marketplace-leadmgmt.png)

>[!TIP]
>Wybierz ikonę informacji, aby ten komunikat jest wyświetlany: "Wybierz system przechowywania potencjalnymi klientami. Dowiedz się, jak połączyć się z systemu CRM [tutaj](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) . "

Aby uzyskać więcej informacji, zobacz [skonfigurować potencjalnych klientów](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads).


## <a name="legal"></a>Informacje prawne

Formularz prawne służy do zapewnienia dokumentacji prawnej, wymagane dla każdej oferty.

Podaj następujące informacje:

- **Adres URL zasad zachowania poufności\***  — wprowadź link do zasad ochrony prywatności aplikacji.
- **Warunki użytkowania\***  — wprowadź warunki użytkowania aplikacji. Klienci są proszeni o zaakceptowanie tych warunków, zanim użytkownik podejmie próbę aplikacji.

![Formularz prawne](./media/azureapp-marketplace-legal.png)


## <a name="next-steps"></a>Kolejne kroki

[Karta pomocy technicznej](./cpp-support-tab.md)

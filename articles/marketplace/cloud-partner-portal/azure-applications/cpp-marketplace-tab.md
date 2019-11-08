---
title: Karta Marketplace z ofertą aplikacji platformy Azure
description: Karta Marketplace służy do identyfikowania zasobów marketingowych dla oferty aplikacji platformy Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 967b66a67d51b3a79bcf930ce977af48acc3dd63
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827576"
---
# <a name="azure-application-marketplace-tab"></a>Karta Marketplace aplikacji platformy Azure

Skorzystaj z karty Marketplace, aby opisać aplikację platformy Azure i udostępnić zasoby marketingowe. Ta karta zawiera następujące formy: przegląd, artefakty marketingowe, zarządzanie potencjalnymi klientami i informacje prawne.

## <a name="overview-form"></a>Formularz przeglądu

Formularz przeglądu zawiera pola wymagane i opcjonalne wyświetlane w następnym przechwyceniu ekranu. Wymagane pola są indicted przez gwiazdkę (*).

![Formularz przeglądu](./media/azureapp-marketplace-overview.png)

W poniższej tabeli opisano ustawienia do użycia podczas tworzenia witryny sklepu dla oferty.   Pola dołączone do gwiazdki są wymagane.

|      Pole         |    Opis    |
|  ---------------   |  ---------------  |
| **\* tytułu**        | Tytuł oferty. Zostanie ona wyświetlona w widocznym miejscu w portalu Marketplace. Maksymalna długość to 50 znaków. |
| **\* podsumowania**      | Krótkie podsumowanie oferty. Maksymalna długość to 100 znaków.           |
| **Długi\* podsumowujący** | Dłuższe podsumowanie oferty (chociaż może być takie samo, jak w przypadku podsumowania). Maksymalna długość to 256 znaków.           |
| **\* opisu**  | Opis oferty. Maksymalna długość to 3000 znaków. Proste formatowanie HTML jest dozwolone, w tym &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;olu&gt; i tagi nagłówków.  |
| **\* identyfikatora marketingu** | Unikatowy adres URL do skojarzenia z ofertą zwykle zawiera nazwę organizacji i rozwiązania, maksymalną długość 50 znaków. Wybierz krótki, przyjazny identyfikator marketingu dla usługi. Ta oferta zostanie użyta w adresach URL portalu Marketplace. Na przykład jeśli identyfikator wydawcy to "contoso", a identyfikator marketingu to "sampleApp", adres URL oferty w witrynie Azure Marketplace zostanie https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp  
| **Zapodgląd identyfikatorów subskrypcji\*** | Dodaj od jednego do 100 identyfikatorów subskrypcji dla recenzentów. Te białe subskrypcje będą miały dostęp do oferty, gdy będzie ona dostępna w wersji zapoznawczej po jej opublikowaniu.          |
| **Przydatne linki**    | Opcjonalnie możesz udostępnić linki do różnych zasobów użytkownikom oferty, takie jak pomoc techniczna, dokumentacja, fora itp.  Zaleca się dodanie co najmniej jednego linku do dokumentacji.            |
| **Sugerowane kategorie (maksymalnie 5)\*** | Wybierz jedną z pięciu kategorii. Wybrane kategorie są używane do mapowania oferty do kategorii produktów dostępnych w witrynie Azure Marketplace i witrynie Azure Portal. Będą one wyświetlane na stronach przeglądania i na stronie szczegółów produktu. |
|  |  |


## <a name="marketing-artifacts"></a>Artefakty marketingowe

Formularz artefaktów marketingowych zawiera pola wymagane i opcjonalne wyświetlane w następnym przechwyceniu ekranu. Wymagane pola są indicted przez gwiazdkę (*).

![Formularz artefaktów marketingowych](./media/azureapp-marketplace-artifacts.png)

W poniższej tabeli opisano artefakty marketingowe.

|      Pole         |    Opis    |
|  ---------------   |  ---------------  |
| **Małe\***        | Małe logo: 40x40 pikseli w formacie PNG     |
| **Średni\***       | Średnie logo: 90x90 pikseli w formacie PNG    |
| **Duże\***        | Duże logo: 115x115 pikseli w formacie PNG   |
| **Szerokie\***         | Szerokie logo: 255x115 pikseli w formacie PNG    |
| **Hero**           | Opcjonalne logo Hero: 815x290 pikseli w formacie PNG. **Uwaga:** Po przekazaniu nie można usunąć ikony Hero. |
| **Zrzuty ekranu (maks. 5)** |        Zrzuty ekranu są wyświetlane na stronie ze szczegółowymi informacjami o produkcie. Są one dobrym sposobem na wizualną komunikację z aplikacją i jej działaniem. Na przykład można wyświetlić diagramy architektury lub ilustracje przypadków użycia. Zrzuty ekranu są opcjonalne i ograniczone do 5 dla jednostki SKU. Aby dodać zrzut ekranu:<ul><li>Wybierz pozycję **+ Dodaj zrzut ekranu** , aby otworzyć okno zrzut ekranu</li><li>**Nazwa** — wprowadź nazwę/tytuł (maksymalną długość wynoszącą 100 znaków).</li><li>**Przekaż** — Przekaż obraz. Musi być w formacie PNG, a rozmiar wynosi 533 x 324 pikseli.</li></ul>           |
| **Dodaj wideo**      | Opcjonalnie na stronie szczegółów produktu są wyświetlane wideo. Są one dobrym sposobem na wizualną komunikację z aplikacją i jej działaniem. Aby dodać film wideo: <ul><li>Wybierz pozycję **+ Dodaj film wideo** , aby otworzyć okno wideo</li><li>**Nazwa** — wprowadź nazwę/tytuł (maksymalną długość wynoszącą 100 znaków).</li><li>**Link** — wprowadź adres URL witryny, która jest hostem wideo (YouTube lub Vimeo)</li><li>**Miniatura** — Przekaż miniaturę. Musi być w formacie PNG, a rozmiar wynosi 533 x 324 pikseli.</li></ul>          |
|  |  |


### <a name="artifact-examples-in-azure-marketplace"></a>Przykłady artefaktów w portalu Azure Marketplace

Następny zrzut ekranu przedstawia przykład wyniku wyszukiwania w portalu Marketplace.

![Wynik wyszukiwania oferty w witrynie Marketplace](./media/azureapp-marketplace-example-browse.png)

Na poniższej ilustracji przedstawiono sposób wyświetlania oferty w portalu Marketplace po kliknięciu przez klienta kafelka oferty w wyniku wyszukiwania.

![Szczegóły wyników wyszukiwania oferty w witrynie Marketplace](./media/azureapp-marketplace-example-details.png)


### <a name="artifact-examples-in-azure-portal"></a>Przykłady artefaktów w witrynie Azure Portal

Na poniższym ekranie przedstawiono sposób wyświetlania oferty w witrynie Azure Portal. Ofertę aplikacji w tym przykładzie można znaleźć, przechodząc do **witryny Marketplace > wszystko > Dev + Test > Jenkins**. Oferta Jenkins zawiera logo, tytuł i nazwę wyświetlaną wydawcy.

![Przeglądaj oferty w Azure Portal](./media/azureapp-portalbrowse-artifacts-jenkins.png)

Przechwytywanie następnego ekranu pokazuje szczegółowe informacje o aplikacji, gdy użytkownik wybierze Jenkins.

![Szczegóły oferty w Azure Portal](./media/azureapp-portal-artifacts-jenkins-details.png)


### <a name="logo-guidelines"></a>Wytyczne dotyczące logo

Wszystkie logo przekazane do portal Cloud Partner powinny postępować zgodnie z wytycznymi:

- Projekt platformy Azure ma prostą paletę kolorów. Zachowaj niewielką liczbę kolorów podstawowych i pomocniczych w logo.
- Kolory motywu w witrynie Azure Portal są białe i czarne. Należy unikać używania tych kolorów jako koloru tła logo. Użyj koloru, który spowoduje, że logo zostanie uwidocznione w Azure Portal. Zalecamy proste kolory podstawowe. Jeśli używasz przezroczystego tła, upewnij się, że logo/tekst nie są białe, czarne lub niebieskie.
- Nie używaj tła gradientu w logo.
- Unikaj umieszczania tekstu, nawet nazwy firmy lub marki, w logo. Wygląd i zachowanie logo powinny być "płaskie" i nie powinno się unikać gradientów.
- Nie rozciągaj logo.


#### <a name="hero-logo"></a>Logo Duży obraz

Logo Hero jest opcjonalne.

>[!IMPORTANT]
>Po przekazaniu nie można usunąć logo Hero.

W przypadku logo Hero należy stosować następujące wytyczne:

- Czarne, białe i przezroczyste tła są niedozwolone.
- Należy unikać używania żadnego jasnego koloru jako tła logo. Nazwa wyświetlana wydawcy, tytuł planu i długi podsumowanie oferty są wyświetlane w białym kolorze czcionki i muszą zostać wystawione w tle.
- Unikaj używania większości tekstu podczas projektowania logo. Nazwa wydawcy, tytuł planu, podsumowanie oferty długiej i przycisk Utwórz są osadzane programowo w logo, gdy zostanie wyświetlona.
- Uwzględnij nieużywane prostokątne miejsce po prawej stronie logo Hero. To puste miejsce to 415x100 pikseli i przesunięcie od lewej do 370 pikseli.


## <a name="lead-management"></a>Zarządzanie potencjalnymi klientami

Formularz zarządzanie potencjalnymi klientami zawiera opcjonalne pole umożliwiające skonfigurowanie zarządzania liderem. Aby skonfigurować zarządzanie potencjalnymi klientami, wybierz z listy rozwijanej element docelowy potencjalnego klienta. Przechwytywanie następnego ekranu pokazuje dostępne miejsca docelowe.

![Wybierz miejsce docelowe zarządzania liderem](./media/azureapp-marketplace-leadmgmt.png)

>[!TIP]
>Wybierz ikonę informacji, aby wyświetlić ten komunikat: "Wybierz system, w którym będą przechowywane potencjalni klienci. Dowiedz się, jak połączyć się z systemem CRM w [tym miejscu](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) .

Aby uzyskać więcej informacji, zobacz [Konfigurowanie potencjalnych](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)klientów.


## <a name="legal"></a>Informacje prawne

Użyj formularza prawnego, aby dostarczyć dokumentację prawną wymaganą dla każdej oferty.

Podaj następujące informacje:

- **Adres URL zasad ochrony prywatności\*** — wprowadź link do zasad zachowania poufności informacji aplikacji.
- **Warunki użytkowania\*** — wprowadź warunki użytkowania aplikacji. Aby móc wypróbować aplikację, klienci muszą zaakceptować te warunki.

![Formularz prawny](./media/azureapp-marketplace-legal.png)


## <a name="next-steps"></a>Następne kroki

[Karta pomocy technicznej](./cpp-support-tab.md)

---
title: Interpretację danych w usłudze Azure Cost Management | Dokumentacja firmy Microsoft
description: Ten artykuł pomaga lepiej zrozumieć, jakie dane znajdują się w usłudze Azure Cost Management i jak często jest przetwarzany, zebranych, jak pokazano i zamknięte.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 07/01/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 44b95c92f51ca9782fca492f3dec3142087ecc91
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797019"
---
# <a name="understand-cost-management-data"></a>Omówienie danych usługi Cost Management

Ten artykuł pomoże Ci lepiej zrozumieć danych platformy Azure kosztów i użycia, który znajduje się w usłudze Azure Cost Management. Wyjaśniono, jak często dane są przetwarzane, zebranych, jak pokazano i zamknięte. Są naliczane za użycie systemu Azure co miesiąc. Mimo że Cykl rozliczeniowy są okresy miesięczne, rozpoczęcia cyklu i daty zakończenia zależą od typu subskrypcji. Jak często Cost Management odbiera użycia, który różni się w danych na podstawie różnych czynników. Takie czynniki obejmują, jak długo trwa przetwarzanie danych i jak często usług platformy Azure emitować użycia systemów rozliczeniowych.

Usługa Cost Management obejmuje wszystkie użycia i zakupów, w tym zastrzeżenia i ofert innych firm dla kont z umową Enterprise Agreement (EA). Konta umowy klienta firmy Microsoft (MCA) i poszczególne subskrypcje z płatnością za rzeczywiste użycie współczynnikami są uwzględnione jedynie użycia z usług platformy Azure i Portal Marketplace. Pomoc techniczna i innych kosztów z góry nie są uwzględniane. Koszty są szacowane, dopóki nie jest generowany faktury i nie wziąć pod uwagę środków.

## <a name="supported-microsoft-azure-offers"></a>Obsługiwana platforma Microsoft Azure oferuje

Poniższe informacje zawierają aktualnie obsługiwany [platforma Microsoft Azure oferuje](https://azure.microsoft.com/support/legal/offer-details/) w usłudze Azure Cost Management. Oferta platformy Azure jest typ subskrypcji platformy Azure, czy masz. Dane są dostępne w Cost Management, począwszy od **danych z** daty. Zmiany oferty subskrypcji koszty przed datą zmiany oferty nie będą dostępne. 

| **Kategoria**  | **Nazwa oferty** | **Identyfikator przydziału** | **Numer oferty** | **Dostępne z danych** |
| --- | --- | --- | --- | --- |
| **Azure (Niemcy)** | [Płatność za rzeczywiste użycie platformy Azure (Niemcy)](https://azure.microsoft.com/offers/ms-azr-de-0003p)      | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P | 2 października 2018<sup>2</sup> |
| **Platforma Azure Government** | Azure Government — przedsiębiorstwa                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | Maj 2014<sup>1</sup> |
| **Umowy Enterprise Agreement (EA)** | Enterprise — tworzenie i testowanie                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | Maj 2014<sup>1</sup> |
| **Umowy Enterprise Agreement (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | Maj 2014<sup>1</sup> |
| **Umowy klienta firmy Microsoft** | [Plan platformy Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | ND | Marca 2019<sup>3</sup> |
| **Umowy klienta firmy Microsoft** | [Plan platformy Microsoft Azure — tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0148g) | MSDNDevTest_2014-09-01 | ND | Marca 2019<sup>3</sup> |
| **Sieć Microsoft Developer Network (MSDN)** | [Platformy MSDN](https://azure.microsoft.com/offers/ms-azr-0062p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | 2 października 2018<sup>2</sup> |
| **Pay-As-You-Go** | [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p)                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | 2 października 2018<sup>2</sup> |
| **Pay-As-You-Go** | [Płatność za rzeczywiste użycie, tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0023p)         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | 2 października 2018<sup>2</sup> |
| **Pay-As-You-Go** | [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p)      | MPN_2014-09-01 | MS-AZR-0025P | 2 października 2018<sup>2</sup> |
| **Pay-As-You-Go** | [Bezpłatna wersja próbna](https://azure.microsoft.com/offers/ms-azr-0044p)<sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | 2 października 2018<sup>2</sup> |
| **Pay-As-You-Go** | [Azure in Open](https://azure.microsoft.com/offers/ms-azr-0111p)<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | 2 października 2018<sup>2</sup> |
| **Pay-As-You-Go** | [Platforma Azure dla studentów](https://azure.microsoft.com/offers/ms-azr-0170p)<sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P | 2 października 2018<sup>2</sup> |
| **Pay-As-You-Go** | Azure — dostęp próbny<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P - MS-AZR-0125P, MS-AZR-0128P - MS-AZR-0130P | 2 października 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)<sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | 2 października 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | 2 października 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | 2 października 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | 2 października 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | 2 października 2018<sup>2</sup> |

_<sup>**1** </sup> danych przed maja 2014 roku, odwiedź stronę [portalu Azure Enterprise](https://ea.azure.com)._

_<sup>**2** </sup> danych przed 2 października 2018 r., odwiedź stronę [Centrum konta platformy Azure](https://account.azure.com/subscriptions)._

_<sup>**3** </sup> umowy klienta firmy Microsoft, pracę w marca 2019 r i nie masz żadnych danych historycznych przed tego punktu._

_<sup>**4** </sup> dane historyczne dotyczące subskrypcji opartej na środki i płatności z góry mogą być niezgodne z fakturze. Zobacz [dane historyczne mogą być niezgodne faktury](#historical-data-might-not-match-invoice) poniżej._

W poniższej tabeli przedstawiono oferty, które nie są jeszcze obsługiwane.

| Kategoria  | **Nazwa oferty** | **Identyfikator przydziału** | **Numer oferty** |
| --- | --- | --- | --- |
| **Azure (Niemcy)** | [Płatność za rzeczywiste użycie platformy Azure (Niemcy)](https://azure.microsoft.com/offers/ms-azr-de-0003p) | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Dostawca rozwiązań w chmurze (CSP)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **Dostawca rozwiązań w chmurze (CSP)** | Azure Government — CSP                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **Dostawca rozwiązań w chmurze (CSP)** | Platforma Azure (Niemcy) w programie CSP dla usługi Microsoft Cloud (Niemcy)   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **Pay-As-You-Go**                 | Azure for Students Starter | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **Pay-As-You-Go**                 | [Dostęp sponsorowany Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **Plany pomocy technicznej** | Pomoc techniczna Standard                    | Default_2014-09-01 | MS-AZR-0041P |
| **Plany pomocy technicznej** | Pomoc techniczna Professional Direct         | Default_2014-09-01 | MS-AZR-0042P |
| **Plany pomocy technicznej** | Pomoc techniczna Developer                   | Default_2014-09-01 | MS-AZR-0043P |
| **Plany pomocy technicznej** | Plan pomocy technicznej (Niemcy)                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **Plany pomocy technicznej** | Pomoc techniczną Standard platformy Azure dla instytucji rządowych   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **Plany pomocy technicznej** | Pomoc techniczna Pro-Direct platformy Azure dla instytucji rządowych | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **Plany pomocy technicznej** | Pomoc techniczną Developer platformy Azure dla instytucji rządowych  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

## <a name="determine-your-offer-type"></a>Określić typ oferty
Jeśli nie widzisz danych w ramach subskrypcji i chcesz określić, jeśli Twoja subskrypcja jest objęta obsługiwanych oferty, może sprawdzić, czy Twoja subskrypcja jest obsługiwana. Aby zweryfikować, że subskrypcja platformy Azure jest obsługiwany, zaloguj się do [witryny Azure portal](https://portal.azure.com). Następnie wybierz pozycję **wszystkich usług** w okienku menu po lewej stronie. Na liście usług wybierz **subskrypcje**. W menu listy subskrypcji kliknij subskrypcję, którą chcesz zweryfikować. Twoja subskrypcja jest wyświetlana na karcie Przegląd i będzie widoczny **oferują** i **identyfikatora oferty Offer ID**. Na poniższej ilustracji przedstawiono przykładowy raport.

![Przykład na karcie Przegląd subskrypcji, oferty i identyfikator oferty](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Koszty objęte Cost Management

W poniższych tabelach przedstawiono dane, które ma włączone lub nie znajduje się w Cost Management. Wszelkie koszty są szacowane, dopóki faktura jest generowana. Koszty wyświetlane nie zawierają bezpłatne i przedpłaconych środków.

**Dane kosztów i użycia**

| **Uwzględnione** | **Niedołączone** |
| --- | --- |
| Użycie usługi Azure<sup>5</sup>        | Obsługuje opłaty — Aby uzyskać więcej informacji, zobacz [warunki wyjaśniono faktur](../billing/billing-understand-your-invoice.md). |
| Sposób użycia oferty w portalu Marketplace<sup>6</sup> | Podatki — Aby uzyskać więcej informacji, zobacz [warunki wyjaśniono faktur](../billing/billing-understand-your-invoice.md). |
| Zakupy w portalu Marketplace<sup>6</sup>      | Środki na korzystanie z — Aby uzyskać więcej informacji, zobacz [warunki wyjaśniono faktur](../billing/billing-understand-your-invoice.md). |
| Zakup rezerwacji<sup>7</sup>      |  |

_<sup>**5** </sup> opiera się na rezerwacji użycie usług platformy azure i umożliwić jego negocjację ceny._

_<sup>**6** </sup> użycie witryny marketplace i zakupów nie są dostępne dla płatność za rzeczywiste użycie, MSDN, i Visual Studio oferuje w tej chwili._

_<sup>**7** </sup> zakupu rezerwacji są dostępne tylko dla kont z umową Enterprise Agreement (EA) w tej chwili._

**Metadata**

| **Uwzględnione** | **Niedołączone** |
| --- | --- |
| Tagi zasobów<sup>8</sup> | Pozycja znaczniki grupy zasobów |

_<sup>**8** </sup> tagi zasobów są stosowane są informacje o użytkowaniu generowane w poszczególnych usług i nie są dostępne wstecznie historycznych danych użycia._

## <a name="rated-usage-data-refresh-schedule"></a>Harmonogram odświeżania danych użycia ocenione

Dane kosztów i użycia są dostępne w zarządzanie kosztami i rozliczenia w witrynie Azure portal i [Obsługa interfejsów API](index.yml). Podczas przeglądania kosztów, należy pamiętać o następujące kwestie:

- Oszacowanie opłat dla bieżącego okresu rozliczeniowego zostaną zaktualizowane sześć razy dziennie.
- Oszacowanie opłat w bieżącym okresie rozliczeniowym mogą zmienić wiąże się z większą funkcjonalność.
- Każda aktualizacja jest zbiorcza i zawiera wszystkie elementy wierszu i informacje z poprzedniej aktualizacji.
- Kończenie znajdujących się platformy Azure lub _zamyka_ bieżącego okresu rozliczeniowego nawet do 72 godzin (trzy dni kalendarzowych) od zakończenia okresu rozliczeniowego.

Poniższe przykłady ilustrują, jak może wystąpić okresów rozliczeniowych.

Subskrypcji Enterprise Agreement (EA) — Jeśli miesiąca rozliczeniowego obowiązuje do 31 marca szacowane opłaty są aktualizowane w górę do 72 godzin później. W tym przykładzie do północy (czas UTC) 4 kwietnia.

Subskrypcje zgodnie z rzeczywistym użyciem — w przypadku miesiąca rozliczeniowego kończy się w dniu 15 maja, a następnie oszacowanie opłat może zostać zaktualizowana się do 72 godzin później. W tym przykładzie do północy (czas UTC) 19 maja.

### <a name="rerated-data"></a>Rerated danych

Czy używać [interfejsów API usługi Cost Management](index.yml), usługa Power BI lub portalu Azure, które można pobrać danych, należy oczekiwać bieżącego okresu rozliczeniowego opłaty ponownie uzyskiwanie oceniane i w związku z tym zmienić, dopóki nie zostanie zamknięty faktury.

## <a name="usage-data-update-frequency-varies"></a>Różni się częstotliwości aktualizacji danych użycia

Dostępność danych użycia poniesionych kosztów zarządzania zależy od kilku czynników, takich jak:

- Jak często usług platformy Azure (np. magazynu, obliczeniowych, sieci CDN i SQL) emitować użycia.
- Czas poświęcony na przetwarzanie danych użycia przez aparat klasyfikacji i kosztów zarządzania potoków.

Niektóre usługi emituje użycie częściej niż inne. Tak można napotkać dane w Cost Management w przypadku niektórych usług szybciej niż inne usługi, które emitują często mniej danych. Użycie usługi trwa zwykle, 8-24 godzin są wyświetlane w Cost Management. Należy pamiętać, że dane dla miesiąca otwarcia zostanie odświeżony, ponieważ naliczane większą funkcjonalność, ponieważ aktualizacje są aktualizacjami zbiorczymi.

## <a name="historical-data-might-not-match-invoice"></a>Dane historyczne mogą być niezgodne z faktury

Dane historyczne dla oferty opartej na środki i płatności z góry, mogą być niezgodne fakturze. Niektóre rozliczana według bieżącego użycia platformy Azure, MSDN i Visual Studio oferuje może mieć zaawansowane płatności i kredyty systemu Azure uwzględnione na fakturze. Jednak historyczne dane wyświetlane w Cost Management opiera się na Twoje opłaty Szacowane użycie. Usługa Cost Management dane historyczne nie obejmuje, płatności i środki na korzystanie z. W rezultacie historyczne dane wyświetlane dla następujących ofert może nie odpowiadać dokładnie za pomocą faktury.

- Oferta Azure for Students (MS-AZR - 0170P)
- Azure in Open (MS-AZR - 0111P)
- Azure — dostęp próbny (MS-AZR — 0120P, MS - AZR - 0123P, MS — AZR — 0125P, MS — AZR — 0128P, MS - AZR — 0129P)
- Bezpłatna wersja próbna (MS-AZR - 0044P)
- MSDN (MS-AZR - 0062P)
- Visual Studio (MS-AZR-0029P, MS-AZR-0059P, MS-AZR-0060P, MS-AZR-0063P, MS-AZR-0064P)

## <a name="see-also"></a>Zobacz także

- Jeśli nie została już zakończona pierwszym przewodniku Szybki Start usługi Cost Management, zapoznaj się z adresem [zacząć analizować koszty](quick-acm-cost-analysis.md).

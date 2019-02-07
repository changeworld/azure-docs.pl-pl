---
title: Interpretację danych w usłudze Azure Cost Management | Dokumentacja firmy Microsoft
description: Ten artykuł pomaga lepiej zrozumieć, jakie dane znajdują się w usłudze Azure Cost Management i jak często jest przetwarzany, zebranych, jak pokazano i zamknięte.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/06/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 0531c106228190fdc40f494e8eee70ec550f6404
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55820263"
---
# <a name="understand-cost-management-data"></a>Zrozumienie danych rozwiązania Cost Management

Ten artykuł pomoże Ci lepiej zrozumieć, jakie dane znajdują się w usłudze Azure Cost Management. I wyjaśniono, jak często dane są przetwarzane, zebranych, jak pokazano i zamknięte. Są naliczane za użycie systemu Azure co miesiąc. Jednak do typu Twojej subskrypcji platformy Azure Określa, kiedy kończy się miesiąca rozliczeniowego. Jak często Cost Management odbiera użycia, który różni się w danych na podstawie różnych czynników. Takie czynniki obejmują, jak długo trwa przetwarzanie danych i jak często usług platformy Azure emitować użycia systemów rozliczeniowych.

## <a name="supported-microsoft-offers"></a>Obsługiwane oferty firmy Microsoft

Poniższe informacje zawierają aktualnie obsługiwany [platforma Microsoft Azure oferuje](https://azure.microsoft.com/support/legal/offer-details/) w usłudze Azure Cost Management.  Oferta platformy Azure jest typ subskrypcji platformy Azure, czy masz.

| Kategoria  | **Nazwa oferty** | **Numer oferty** |
| --- | --- | --- |
| **Umowy Enterprise Agreement (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade/) | MS-AZR-0017P |
| **Umowy Enterprise Agreement (EA)**| Tworzenie i testowanie (przedsiębiorstwo) | MS-AZR-0148P |
| **Sieć Microsoft Developer Network (MSDN)** | [Platformy MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/) | MS-AZR-0062P |
| **Program Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p/) | MS-AZR-0029P |
| **Program Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) | MS-AZR-0059P |
| **Program Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) | MS-AZR-0060P |
| **Program Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) | MS-AZR-0063P |
| **Program Visual Studio** | [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/) | MS-AZR-0064P |
| **Direct/Pay-As-You-Go** | [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) | MS-AZR-0003P |
| **Direct/Pay-As-You-Go** | Microsoft Azure — użytek wewnętrzny | MS-AZR-0015P |
| **Direct/Pay-As-You-Go** | [Płatność za rzeczywiste użycie, tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0023p/) | MS-AZR-0023P |
| **Direct/Pay-As-You-Go** | [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/) | MS-AZR-0025P |
| **Direct/Pay-As-You-Go** | [Dostęp sponsorowany Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0036p/) | MS-AZR-0036P |
| **Direct/Pay-As-You-Go** | [Bezpłatna wersja próbna](https://azure.microsoft.com/offers/ms-azr-0044p/) | MS-AZR-0044P |
| **Direct/Pay-As-You-Go** | [Azure in Open](https://azure.microsoft.com/offers/ms-azr-0111p/) | MS-AZR-0111P |
| **Direct/Pay-As-You-Go** | [Oferta Azure for Students](https://azure.microsoft.com/en-us/offers/ms-azr-0170p/) | MS-AZR-0170P |
| **Direct/Pay-As-You-Go** | Azure — dostęp próbny | MS-AZR-0120P, MS-AZR-0122P - MS-AZR-0125P, MS-AZR-0128P - MS-AZR-0130P |

W poniższej tabeli przedstawiono oferty nieobsługiwany.

| Kategoria  | **Nazwa oferty** | **Numer oferty** |
| --- | --- | --- |
| **Dostawca rozwiązań w chmurze (CSP)** | Microsoft Azure | MS-AZR-0145P |
| **Dostawca rozwiązań w chmurze (CSP)** | Azure Government — CSP | MS-AZR-USGOV-0145P |
| **Dostawca rozwiązań w chmurze (CSP)** | Platforma Azure (Niemcy) w programie CSP dla usługi Microsoft Cloud (Niemcy) | MS-AZR-DE-0145P |
| **Direct/Pay-As-You-Go** | Azure for Students Starter | MS-AZR-0144P |
| **Plany pomocy technicznej** | Pomoc techniczna Standard | MS-AZR-0041P |
| **Plany pomocy technicznej** | Pomoc techniczna Professional Direct | MS-AZR-0042P |
| **Plany pomocy technicznej** | Pomoc techniczna Developer | MS-AZR-0043P |
| **Plany pomocy technicznej** | Plan pomocy technicznej (Niemcy) | MS-AZR-DE-0043P |
| **Plany pomocy technicznej** | Pomoc techniczną Standard platformy Azure dla instytucji rządowych | MS-AZR-USGOV-0041P |
| **Plany pomocy technicznej** | Pomoc techniczna Pro-Direct platformy Azure dla instytucji rządowych | MS-AZR-USGOV-0042P |
| **Plany pomocy technicznej** | Pomoc techniczną Developer platformy Azure dla instytucji rządowych | MS-AZR-USGOV-0043P |

Jeśli nie widzisz danych w ramach subskrypcji i chcesz określić, jeśli Twoja subskrypcja jest objęta obsługiwanych oferty, może sprawdzić, czy Twoja subskrypcja jest obsługiwana. Aby zweryfikować, że subskrypcja platformy Azure jest obsługiwany, zaloguj się do [witryny Azure portal](https://portal.azure.com). Następnie wybierz pozycję **wszystkich usług** w okienku menu po lewej stronie. Na liście usług wybierz **subskrypcje**. W menu listy subskrypcji kliknij subskrypcję, którą chcesz zweryfikować. Twoja subskrypcja jest wyświetlana na karcie Przegląd i będzie widoczny **oferują** i **identyfikatora oferty Offer ID**. Na poniższej ilustracji przedstawiono przykładowy raport.

![Przykład na karcie Przegląd subskrypcji, oferty i identyfikator oferty](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Koszty objęte Cost Management

W poniższych tabelach przedstawiono dane, które ma włączone lub nie znajduje się w Cost Management.

**Typy kont**

| **Uwzględnione** | **Niedołączone** |
| --- | --- |
| Enterprise Agreement (EA) | W chmurze dostawcy rozwiązań (CSP) — Aby uzyskać więcej informacji, zobacz [Przegląd Centrum partnerskiego](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview). |
| Płatność za rzeczywiste użycie (PAYG) |   |
| Tworzenie i testowanie aplikacji |   |
| Bezpłatna wersja próbna i sponsorowanego |   |
| Partner Network |   |
| Licencja Azure w ramach programu licencjonowania Open | &nbsp;  |

**Dane kosztów i użycia**

| **Uwzględnione** | **Niedołączone** |
| --- | --- |
| Użycie usługi Azure<sup>1</sup> | Zakup rezerwacji — Aby uzyskać więcej informacji, zobacz [interfejsów API automatyzacji Azure rezerwacji](../billing/billing-reservation-apis.md). |
| Użycie ofert portalu Marketplace | Zakupy w witrynie Marketplace — Aby uzyskać więcej informacji, zobacz [opłat za usługi innych firm](../billing/billing-understand-your-azure-marketplace-charges.md). |
|   | Obsługuje opłaty — Aby uzyskać więcej informacji, zobacz [warunki wyjaśniono faktur](../billing/billing-understand-your-invoice.md). |
|   | Podatki — Aby uzyskać więcej informacji, zobacz [warunki wyjaśniono faktur](../billing/billing-understand-your-invoice.md). |
|   | Środki na korzystanie z — Aby uzyskać więcej informacji, zobacz [warunki wyjaśniono faktur](../billing/billing-understand-your-invoice.md). |

<sup>1</sup> opiera się na rezerwacji użycie usług platformy azure i umożliwić jego negocjację ceny.

**Metadata**

| **Uwzględnione** | **Niedołączone** |
| --- | --- |
| Tagi zasobów<sup>2</sup> | Pozycja znaczniki grupy zasobów |

<sup>2</sup> tagi zasobów są stosowane są informacje o użytkowaniu generowane w poszczególnych usług i nie są dostępne wstecznie historycznych danych użycia.

## <a name="rated-usage-data-refresh-schedule"></a>Harmonogram odświeżania danych użycia ocenione

Dane kosztów i użycia są dostępne w zarządzanie kosztami i rozliczenia w witrynie Azure portal i [Obsługa interfejsów API](https://aka.ms/costmgmt/docs). Podczas przeglądania kosztów, należy pamiętać o następujące kwestie:

- Oszacowanie opłat dla bieżącego okresu rozliczeniowego zostaną zaktualizowane sześć razy dziennie.
- Oszacowanie opłat w bieżącym okresie rozliczeniowym mogą zmienić wiąże się z większą funkcjonalność.
- Każda aktualizacja jest zbiorcza i zawiera wszystkie elementy wierszu i informacje z poprzedniej aktualizacji.
- Kończenie znajdujących się platformy Azure lub _zamyka_ bieżącego okresu rozliczeniowego nawet do 72 godzin (trzy dni kalendarzowych) od zakończenia okresu rozliczeniowego.

Poniższe przykłady ilustrują, jak może wystąpić okresów rozliczeniowych.

Subskrypcji Enterprise Agreement (EA) — Jeśli miesiąca rozliczeniowego obowiązuje do 31 marca szacowane opłaty są aktualizowane w górę do 72 godzin później. W tym przykładzie do północy (czas UTC) 4 kwietnia.

Subskrypcje zgodnie z rzeczywistym użyciem — w przypadku miesiąca rozliczeniowego kończy się w dniu 15 maja, a następnie oszacowanie opłat może zostać zaktualizowana się do 72 godzin później. W tym przykładzie do północy (czas UTC) 19 maja.

### <a name="rerated-data"></a>Rerated danych

Czy używać [interfejsów API usługi Cost Management](https://aka.ms/costmgmt/docs), usługi Power bi lub w portalu Azure w celu pobrania danych, należy oczekiwać bieżącego okresu rozliczeniowego opłaty ponownie uzyskiwanie oceniane i w związku z tym zmienić, dopóki nie zostanie zamknięty faktury.

## <a name="usage-data-update-frequency-varies"></a>Różni się częstotliwości aktualizacji danych użycia

Dostępność danych użycia poniesionych kosztów zarządzania zależy od kilku czynników, takich jak:

- Jak często usług platformy Azure (np. magazynu, obliczeniowych, sieci CDN i SQL) emitować użycia.
- Czas poświęcony na przetwarzanie danych użycia przez aparat klasyfikacji i kosztów zarządzania potoków.

Niektóre usługi emituje użycie częściej niż inne. Tak można napotkać dane w Cost Management w przypadku niektórych usług szybciej niż inne usługi, które emitują często mniej danych. Użycie usługi trwa zwykle, 8-24 godzin są wyświetlane w Cost Management. Należy pamiętać, że dane dla miesiąca otwarcia zostanie odświeżony, ponieważ naliczane większą funkcjonalność, ponieważ aktualizacje są aktualizacjami zbiorczymi.

## <a name="see-also"></a>Zobacz także

- Jeśli nie została już zakończona pierwszym przewodniku Szybki Start usługi Cost Management, zapoznaj się z adresem [zacząć analizować koszty](quick-acm-cost-analysis.md).

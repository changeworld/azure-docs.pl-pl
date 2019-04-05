---
title: Interpretację danych w usłudze Azure Cost Management | Dokumentacja firmy Microsoft
description: Ten artykuł pomaga lepiej zrozumieć, jakie dane znajdują się w usłudze Azure Cost Management i jak często jest przetwarzany, zebranych, jak pokazano i zamknięte.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 0defb1a8572cc3adad48dbcdb27a04e366fc3e60
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59046790"
---
# <a name="understand-cost-management-data"></a>Omówienie danych usługi Cost Management

Ten artykuł pomoże Ci lepiej zrozumieć, jakie dane znajdują się w usłudze Azure Cost Management. I wyjaśniono, jak często dane są przetwarzane, zebranych, jak pokazano i zamknięte. Są naliczane za użycie systemu Azure co miesiąc. Jednak do typu Twojej subskrypcji platformy Azure Określa, kiedy kończy się miesiąca rozliczeniowego. Jak często Cost Management odbiera użycia, który różni się w danych na podstawie różnych czynników. Takie czynniki obejmują, jak długo trwa przetwarzanie danych i jak często usług platformy Azure emitować użycia systemów rozliczeniowych.

## <a name="supported-microsoft-offers"></a>Obsługiwane oferty firmy Microsoft

Poniższe informacje zawierają aktualnie obsługiwany [platforma Microsoft Azure oferuje](https://azure.microsoft.com/support/legal/offer-details/) w usłudze Azure Cost Management.  Oferta platformy Azure jest typ subskrypcji platformy Azure, czy masz.

| Kategoria  | **Nazwa oferty** | **Identyfikator przydziału** | **Numer oferty** |
| --- | --- | --- | --- |
| **Azure (Niemcy)** | [Azure (Niemcy) — płatność zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-de-0003p)      | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Azure Government** | Azure Government — przedsiębiorstwa                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P |
| **Enterprise Agreement (EA)** | Enterprise — tworzenie i testowanie                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P |
| **Enterprise Agreement (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P |
| **Umowa klienta firmy Microsoft** | [Plan platformy Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017G |
| **Umowa klienta firmy Microsoft** | [Plan platformy Microsoft Azure na potrzeby tworzenia i testowania](https://azure.microsoft.com/offers/ms-azr-0148g)  | MSDNDevTest_2014-09-01 | MS-AZR-0148G |
| **Sieć Microsoft Developer Network (MSDN)** | [Platformy MSDN](https://azure.microsoft.com/offers/ms-azr-0062p) | MSDN_2014-09-01 | MS-AZR-0062P |
| **Płatność zgodnie z rzeczywistym użyciem** | [Płatność zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p)                       | PayAsYouGo_2014-09-01 | MS-AZR-0003P |
| **Płatność zgodnie z rzeczywistym użyciem** | [Płatność zgodnie z rzeczywistym użyciem — tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0023p)              | MSDNDevTest_2014-09-01 | MS-AZR-0023P |
| **Płatność zgodnie z rzeczywistym użyciem** | [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p)           | MPN_2014-09-01 | MS-AZR-0025P |
| **Płatność zgodnie z rzeczywistym użyciem** | [Bezpłatna wersja próbna](https://azure.microsoft.com/offers/ms-azr-0044p)                          | FreeTrial_2014-09-01 | MS-AZR-0044P |
| **Płatność zgodnie z rzeczywistym użyciem** | [Licencja Azure w ramach programu licencjonowania Open](https://azure.microsoft.com/offers/ms-azr-0111p)                       | AzureInOpen_2014-09-01 | MS-AZR-0111P |
| **Płatność zgodnie z rzeczywistym użyciem** | [Azure for Students](https://azure.microsoft.com/offers/ms-azr-0170p)                  | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **Płatność zgodnie z rzeczywistym użyciem** | Azure — dostęp próbny                                                                             | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P - MS-AZR-0125P, MS-AZR-0128P - MS-AZR-0130P |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)      | MPN_2014-09-01 | MS-AZR-0029P |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)          | MSDN_2014-09-01 | MS-AZR-0059P |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)     | MSDNDevTest_2014-09-01 | MS-AZR-0060P |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)            | MSDN_2014-09-01 | MS-AZR-0063P |
| **Visual Studio** | [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)  | MSDN_2014-09-01 | MS-AZR-0064P |

W poniższej tabeli przedstawiono oferty nieobsługiwany.

| Kategoria  | **Nazwa oferty** | **Identyfikator przydziału** | **Numer oferty** |
| --- | --- | --- | --- |
| **Cloud Solution Provider (CSP)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **Cloud Solution Provider (CSP)** | Azure Government — CSP                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **Cloud Solution Provider (CSP)** | Platforma Azure (Niemcy) w programie CSP dla usługi Microsoft Cloud (Niemcy)   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **Płatność zgodnie z rzeczywistym użyciem**                 | Azure for Students Starter | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **Płatność zgodnie z rzeczywistym użyciem**                 | [Dostęp sponsorowany Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **Plany pomocy technicznej** | Pomoc techniczna Standard                    | Default_2014-09-01 | MS-AZR-0041P |
| **Plany pomocy technicznej** | Pomoc techniczna Professional Direct         | Default_2014-09-01 | MS-AZR-0042P |
| **Plany pomocy technicznej** | Pomoc techniczna Developer                   | Default_2014-09-01 | MS-AZR-0043P |
| **Plany pomocy technicznej** | Plan pomocy technicznej (Niemcy)                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **Plany pomocy technicznej** | Pomoc techniczną Standard platformy Azure dla instytucji rządowych   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **Plany pomocy technicznej** | Pomoc techniczna Pro-Direct platformy Azure dla instytucji rządowych | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **Plany pomocy technicznej** | Pomoc techniczną Developer platformy Azure dla instytucji rządowych  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

W przypadku klientów z kategoriami ofertę płatność za rzeczywiste użycie, MSDN i Visual Studio dane są dostępne w Cost Management, zaczynając od 10 02 2018. Aby uzyskać dostęp do danych dla Twojej subskrypcji przed 10 02 2018, można użyć [Centrum konta platformy Azure](https://account.azure.com/subscriptions) można pobrać użycia szczegółowych informacji w pliku CSV lub użyć [interfejs API szczegóły użycia](/rest/api/consumption/usagedetails).

## <a name="determine-your-offer-type"></a>Określić typ oferty
Jeśli nie widzisz danych w ramach subskrypcji i chcesz określić, jeśli Twoja subskrypcja jest objęta obsługiwanych oferty, może sprawdzić, czy Twoja subskrypcja jest obsługiwana. Aby zweryfikować, że subskrypcja platformy Azure jest obsługiwany, zaloguj się do [witryny Azure portal](https://portal.azure.com). Następnie wybierz pozycję **wszystkich usług** w okienku menu po lewej stronie. Na liście usług wybierz **subskrypcje**. W menu listy subskrypcji kliknij subskrypcję, którą chcesz zweryfikować. Twoja subskrypcja jest wyświetlana na karcie Przegląd i będzie widoczny **oferują** i **identyfikatora oferty Offer ID**. Na poniższej ilustracji przedstawiono przykładowy raport.

![Przykład na karcie Przegląd subskrypcji, oferty i identyfikator oferty](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Koszty objęte Cost Management

W poniższych tabelach przedstawiono dane, które ma włączone lub nie znajduje się w Cost Management. Wszelkie koszty są szacowane, dopóki faktura jest generowana. Koszty wyświetlane nie zawierają bezpłatne i przedpłaconych środków.

**Dane kosztów i użycia**

| **Dołączono** | **Nie dołączono** |
| --- | --- |
| Użycie usługi Azure<sup>1</sup> | Zakup rezerwacji — Aby uzyskać więcej informacji, zobacz [interfejsów API automatyzacji Azure rezerwacji](../billing/billing-reservation-apis.md). |
| Użycie ofert portalu Marketplace | Zakupy w witrynie Marketplace — Aby uzyskać więcej informacji, zobacz [opłat za usługi innych firm](../billing/billing-understand-your-azure-marketplace-charges.md). |
|   | Obsługuje opłaty — Aby uzyskać więcej informacji, zobacz [warunki wyjaśniono faktur](../billing/billing-understand-your-invoice.md). |
|   | Podatki — Aby uzyskać więcej informacji, zobacz [warunki wyjaśniono faktur](../billing/billing-understand-your-invoice.md). |
|   | Środki na korzystanie z — Aby uzyskać więcej informacji, zobacz [warunki wyjaśniono faktur](../billing/billing-understand-your-invoice.md). |

<sup>1</sup> opiera się na rezerwacji użycie usług platformy azure i umożliwić jego negocjację ceny.

**Metadane**

| **Dołączono** | **Nie dołączono** |
| --- | --- |
| Tagi zasobów<sup>2</sup> | Pozycja znaczniki grupy zasobów |

<sup>2</sup> tagi zasobów są stosowane są informacje o użytkowaniu generowane w poszczególnych usług i nie są dostępne wstecznie historycznych danych użycia.

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

Czy używać [interfejsów API usługi Cost Management](https://aka.ms/costmgmt/docs), usługi Power bi lub w portalu Azure w celu pobrania danych, należy oczekiwać bieżącego okresu rozliczeniowego opłaty ponownie uzyskiwanie oceniane i w związku z tym zmienić, dopóki nie zostanie zamknięty faktury.

## <a name="usage-data-update-frequency-varies"></a>Różni się częstotliwości aktualizacji danych użycia

Dostępność danych użycia poniesionych kosztów zarządzania zależy od kilku czynników, takich jak:

- Jak często usług platformy Azure (np. magazynu, obliczeniowych, sieci CDN i SQL) emitować użycia.
- Czas poświęcony na przetwarzanie danych użycia przez aparat klasyfikacji i kosztów zarządzania potoków.

Niektóre usługi emituje użycie częściej niż inne. Tak można napotkać dane w Cost Management w przypadku niektórych usług szybciej niż inne usługi, które emitują często mniej danych. Użycie usługi trwa zwykle, 8-24 godzin są wyświetlane w Cost Management. Należy pamiętać, że dane dla miesiąca otwarcia zostanie odświeżony, ponieważ naliczane większą funkcjonalność, ponieważ aktualizacje są aktualizacjami zbiorczymi.

## <a name="see-also"></a>Zobacz także

- Jeśli nie została już zakończona pierwszym przewodniku Szybki Start usługi Cost Management, zapoznaj się z adresem [zacząć analizować koszty](quick-acm-cost-analysis.md).

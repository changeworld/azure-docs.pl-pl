---
title: Warunki plików użycia i opłat platformy Azure do umowy klienta firmy Microsoft
description: Dowiedz się, jak czytać i zrozumieć sekcje użycia platformy Azure i opłaty za CSV dla swojego profilu rozliczeń.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: d11e31366ea5aa15cf7a790eaee800fa2ea6dabe
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490628"
---
# <a name="terms-in-the-azure-usage-and-charges-file-for-a-microsoft-customer-agreement"></a>Warunki plików użycia i opłat platformy Azure do umowy klienta firmy Microsoft

Ten artykuł dotyczy konta rozliczeniowego dla umowy klienta firmy Microsoft. [Sprawdź, czy dostęp do umowy klienta Microsoft](#check-access-to-a-microsoft-customer-agreement).

Azure użycia i opłat plik CSV zawiera opłaty za użycie dzienne i poziom licznika dla bieżącego okresu rozliczeniowego.

Aby uzyskać plików platformy Azure użycia i opłat, zobacz [opłat za umowy klienta firmy Microsoft i Pobierz użycie platformy Azure i widoku](billing-download-azure-daily-usage.md). Jest ona dostępna w formacie wartości rozdzielanych przecinkami (CSV), który można otworzyć w aplikacji arkusza kalkulacyjnego.

Opłaty za zużycie to suma **miesięczne** opłaty za subskrypcję. Opłaty za użycie nie brać pod uwagę wszystkie środki na korzystanie z ani rabatów.

## <a name="changes-from-azure-ea-usage-and-charges"></a>Zmiany w porównaniu z umowy EA platformy Azure, użycia i opłat

Gdyby klientem z umową EA, zauważysz, że warunki Azure pliku CSV rozliczania użycia profilu różnią się od warunków w pliku CSV użycia umowy EA platformy Azure. Poniżej przedstawiono mapowanie EA warunki korzystania z rozliczeniami — warunki użytkowania profilu:

| Użycie platformy Azure EA CSV | Microsoft Azure umowy klienta użycia i opłat CSV |
| --- | --- |
| Date | date |
| Miesiąc| date |
| Dzień | date |
| Rok | date |
| Product (Produkt) | product |
| MeterId | meterID |
| meterCategory | MeterCategory |
| MeterSubCategory | meterSubCategory |
| MeterRegion | meterRegion |
| MeterName | meterName |
| ConsumedQuantity | Ilość |
| ResourceRate | effectivePrice |
| ExtendedCost | cost |
| resourceLocation | resourceLocation |
| ConsumedService | consumedService |
| InstanceId | instanceId |
| ServiceInfo1 | ServiceInfo1 |
| ServiceInfo2 | serviceInfo2 |
| AdditionalInfo | AdditionalInfo |
| `Tags` | tags |
| StoreServiceIdentifier | ND |
| Nazwa działu | invoiceSection |
| CostCenter | costCenter |
| UnitOfMeasure | unitofMeasure |
| ResourceGroup | resourceGroup |
| ChargesBilledSeparately | isAzureCreditEligible |

## <a name="detailed-terms-and-descriptions"></a>Szczegółowy opis warunków wraz z opisami

Poniższe terminy są wyświetlane w plików platformy Azure, użycia i opłat.

Termin | Opis
--- | ---
invoiceId | Identyfikator unikatowy dokumentu na fakturze PDF
previousInvoiceId | Odwołanie do oryginalnej faktury w przypadku tej pozycji zwrot pieniędzy
billingAccountName | Nazwa konta, rozliczeń
billingAccountId | Unikatowy identyfikator dla głównego konto rozliczeniowe
billingProfileId | Nazwa profilu rozliczeń, która opłaty, które są fakturowane
billingProfileName | Unikatowy identyfikator opłaty, które są fakturowane profil rozliczeniowy
invoiceSectionId | Unikatowy identyfikator dla sekcji faktury
invoiceSectionName | Nazwa sekcji faktury
costCenter | Centrum kosztów, zdefiniowane w ramach subskrypcji dla śledzenia kosztów (dostępne tylko dla okresów rozliczeniowych open)
billingPeriodStartDate | Data rozpoczęcia okresu rozliczeniowego, dla którego jest generowany faktury
billingPeriodEndDate | Data zakończenia okresu rozliczeniowego, dla którego jest generowany faktury
servicePeriodStartDate | Data rozpoczęcia okresu oceny który zdefiniowane i zablokowane, cennik usługi użyte lub zakupionych
servicePeriodEndDate | Data zakończenia okresu oceny który zdefiniowane i zablokowane, cennik usługi użyte lub zakupionych
date | Na platformie Azure i Portal Marketplace opartej na użyciu opłaty za jest to Data klasyfikacji. Jednorazowe zakupów (rezerwacje, portalu Marketplace) lub stałej opłaty cykliczne (oferty pomocy technicznej) jest to data zakupu.
serviceFamily | Rodzina usługi, której należy usługa
productOrderId | Unikatowy identyfikator zamówienia produktu
productOrderName | Unikatową nazwę dla zamówienia produktu
consumedService | Nazwa użyta usługa
meterId | Unikatowy identyfikator miernika
meterName | Nazwa licznika
MeterCategory | Nazwa kategorii klasyfikacji dla licznika. Na przykład *usług w chmurze*, *sieć*itp.
meterSubCategory | Nazwa kategorii klasyfikacji podrzędnych miernika
meterRegion | Nazwa regionu, w których mierniku usługi jest dostępny. Określa lokalizację centrum danych pewnych usług, które są wyceniane na podstawie lokalizacji centrum danych.
oferty | Nazwa oferty zakupu
productId | Unikatowy identyfikator produktu opłaty
product | Nazwa produktu, opłaty
Identyfikator subskrypcji | Unikatowy identyfikator subskrypcji opłaty
subscriptionName | Nazwa subskrypcji opłaty
reservationId | Unikatowy identyfikator wystąpienia zakupionej rezerwacji
reservationName | Nazwa wystąpienia zakupionej rezerwacji
publisherType | Typ wydawcy (wartości: firstParty thirdPartyReseller, thirdPartyAgency)
publisherName | Wydawcy dla usług witryny Marketplace
resourceGroupId | Unikatowy identyfikator dla grupy zasobów, skojarzone z zasobem
resourceGroupName | Nazwa grupy zasobów, skojarzone z zasobem
resourceId | Unikatowy identyfikator wystąpienia zasobu
resourceType | Typ wystąpienia zasobu
resourceLocation | Określa lokalizację centrum danych, w którym jest uruchamiany zasób.
location | Znormalizowana lokalizację zasobu, jeśli inny zasób lokalizacje są skonfigurowane dla tych samych regionach
Ilość | Liczbę jednostek zakupionych lub wykorzystali
unitOfMeasure | Jednostka miary dla rozliczeń dla usługi. Na przykład usługi obliczeniowe są rozliczane godzinowo.
chargeType | Typ opłaty. Wartości: <ul><li>AsCharged-Usage: Opłaty naliczane na podstawie użycia usługi platformy Azure. W tym użycie względem maszyn wirtualnych, które nie są naliczane z powodu wystąpienia zarezerwowane.</li><li>AsCharged-PurchaseMarketplace: Jednorazowe lub stałej opłaty cykliczne z zakupów w portalu Marketplace</li><li>AsCharged-UsageMarketplace: Opłaty za usługi w portalu Marketplace, które są naliczane na podstawie jednostek zużycia</li></ul>
isAzureCreditEligible | Flaga, która wskazuje, czy opłata za korzystająca z usługi jest uprawniona do zapłaty za pomocą środków platformy Azure (wartości: True, False)
ServiceInfo1 | Metadane właściwe dla usługi
serviceInfo2 | Starszego pole, które zawiera opcjonalne metadane właściwe dla usługi
AdditionalInfo | Dodatkowe metadane specyficzne dla usługi.
tags | Tagi, które można przypisać do zasobu

### <a name="make-sure-that-charges-are-correct"></a>Upewnij się, że opłaty są poprawne

Jeśli chcesz upewnić się, że opłaty w szczegółowym zestawieniem użycia pliku są poprawne, możesz je sprawdzić. Zobacz [informacje o opłatach na fakturze profil rozliczeniowy](billing-mca-understand-your-bill.md)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdź dostęp do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Kolejne kroki

- [Wyświetl i Pobierz fakturę usługi Microsoft Azure](billing-download-azure-invoice.md)
- [Przeglądanie i pobieranie usługi Microsoft Azure użycia i opłat](billing-download-azure-daily-usage.md)

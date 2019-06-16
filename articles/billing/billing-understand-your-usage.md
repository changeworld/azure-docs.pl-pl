---
title: Ułatwią zrozumienie szczegółowym zestawieniem użycia i opłat | Dokumentacja firmy Microsoft
description: Dowiedz się, jak czytać i zrozumieć swoje szczegóły użycia i opłat
author: bandersmsft
manager: micflan
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/24/2019
ms.author: banders
ms.openlocfilehash: 9ff9b6b5313026d2102b98659183fa97c6a5ef84
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64683983"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>Zrozumienie warunki w plików platformy Azure użycia i opłat

Szczegółowy plik użycia i opłat zawiera dzienne użycie ocenione na podstawie stawki wynegocjowanym, zakupy (na przykład rezerwacje, opłaty w witrynie Marketplace) i zwroty przez określony okres.
Opłaty za nie uwzględniają, środki na korzystanie z, podatków, lub inne opłaty ani rabatów.
Wpisz następujące obejmuje tabeli opłaty, które są uwzględniane dla poszczególnych kont.

Typ konta | Użycie platformy Azure | Użycie witryny Marketplace | Zakupy | Zwroty
--- | --- | --- | --- | ---
Enterprise Agreement (EA) | Yes | Yes | Yes | Nie
Umowa klienta firmy Microsoft | Tak | Yes | Yes | Tak
Płatność za rzeczywiste użycie (PAYG) | Tak | Nie | Nie | Nie

Aby dowiedzieć się więcej na temat zamówienia witryny Marketplace (nazywane także usługami zewnętrznymi), zobacz [o opłatach za usługi platformy Azure zewnętrznej usługi](billing-understand-your-azure-marketplace-charges.md).

Zobacz [sposobu uzyskania usługi Azure billing faktury i dane dziennego użycia](billing-download-azure-invoice-daily-usage-date.md) instrukcje pobierania.
Plik użycia i opłat jest dostępny w formacie pliku wartości rozdzielanych przecinkami (CSV), który można otworzyć w aplikacji arkusza kalkulacyjnego.

## <a name="list-of-terms-and-descriptions"></a>Lista warunków i opisów

W poniższej tabeli opisano ważne terminy używane w najnowszej wersji plików platformy Azure, użycia i opłat.
Lista obejmuje płatności (PAYG), Enterprise Agreement (EA) i umowy klienta firmy Microsoft (MCA) konta.

Termin | Typ konta | Opis
--- | --- | ---
AccountName | EA | Nazwa wyświetlana konta rejestracji.
AccountOwnerId | EA | Unikatowy identyfikator konta rejestracji.
AdditionalInfo | Wszyscy | Metadane specyficzne dla usługi. Na przykład typ obrazu dla maszyny wirtualnej.
BillingAccountId | EA, MCA | Unikatowy identyfikator dla głównego konto rozliczeniowe.
BillingAccountName | EA, MCA | Nazwa konta rozliczeniowego.
billingCurrency | EA, MCA | Waluta skojarzone z kontem rozliczeniowym.
BillingPeriod | EA | Okresu rozliczeniowego opłaty.
billingPeriodEndDate | EA, MCA | Data zakończenia okresu rozliczeniowego.
billingPeriodStartDate | EA, MCA | Data rozpoczęcia okresu rozliczeniowego.
BillingProfileId | EA, MCA | Unikatowy identyfikator rejestracji EA lub MCA rozliczeń profilu.
BillingProfileName | EA, MCA | Nazwa rejestracji EA lub MCA rozliczeń profilu.
chargeType | EA, MCA | Wskazuje, czy opłata reprezentuje użycia (**użycia**), zakup (**zakupu**), lub refundacji (**zwrotu**).
ConsumedQuantity | PAYG | Zobacz ilość.
ConsumedService | Wszyscy | Nazwa usługi opłata jest skojarzony.
Koszty | EA | Zobacz CostInBillingCurrency.
CostCenter | EA, MCA | Centrum kosztów zdefiniowane dla subskrypcji śledzenie kosztów (Ta funkcja jest dostępna tylko w otwartych okresów rozliczeniowych dla kont MCA).
costInBillingCurrency | MCA | Koszt w waluta rozliczeniowa przed środków lub podatków.
CostInPricingCurrency | MCA | Koszt w walucie cen przed środków lub podatków.
Waluta | PAYG | Zobacz BillingCurrency.
Date | EA, MCA | Data użycia lub zakupu dodatkowego.
ExchangeRateDate | MCA | Data ustanowiono kurs.
ExchangeRatePricingToBilling | MCA | Kurs użyty do konwertowania koszt w walucie cen do waluta rozliczeniowa.
Częstotliwość | EA, MCA | Wskazuje, czy jest opłata za oczekuje się powtórzyć. Opłaty za albo zdarzyć, gdy (**OneTime**), powtórz tę procedurę na podstawie miesięczny lub roczny (**cyklicznie**), lub być na podstawie użycia (**UsageBased**).
IncludedQuantity | PAYG | Wartość licznika, który jest dołączony, bez dodatkowych opłat w trakcie bieżącego okresu rozliczeniowego.
InstanceId | PAGY | Zobacz ResourceId.
InvoiceId | EA, MCA | Identyfikator unikatowy dokumentu na fakturze PDF.
invoiceSection | MCA | Zobacz InvoiceSectionName.
InvoiceSectionId | EA, MCA | Unikatowy identyfikator dla działu EA lub MCA faktury sekcji.
InvoiceSectionName | EA, MCA | Nazwa umowy EA, dział lub MCA faktury sekcji.
IsAzureCreditEligible | EA, MCA | Wskazuje, czy opłata jest uprawniona do zapłaty za pomocą środków platformy Azure (wartości: True, False).
Lokalizacja | EA, MCA | Lokalizację centrum danych, w którym jest uruchamiany zasób.
MeterCategory | Wszyscy | Nazwa kategorii klasyfikacji dla licznika. Na przykład *usług w chmurze* i *sieć*.
MeterId | Wszyscy | Unikatowy identyfikator dla licznika.
MeterName | Wszyscy | Nazwa licznika.
MeterRegion | Wszyscy | Nazwa lokalizacji centrum danych dla usług wyceniane na podstawie lokalizacji. Zobacz lokalizacji.
MeterSubCategory | Wszyscy | Nazwa kategorii podrzędna miernika.
OfferId | EA, MCA | Nazwa oferty zakupu.
PartNumber | EA | Identyfikator używany do pobierania ceny określonego licznika.
PlanName | EA | Nazwa planu witryny Marketplace.
PreviousInvoiceId | MCA | Odwołanie do oryginalnej faktury w przypadku tej pozycji zwrot pieniędzy.
pricingCurrency | MCA | Waluta używana podczas oceny na podstawie wynegocjowanym cen.
Product (Produkt) | MCA | Zobacz ProductName.
Identyfikator produktu | EA, MCA | Unikatowy identyfikator dla tego produktu.
ProductName | EA | Nazwa produktu.
ProductOrderId | EA, MCA | Unikatowy identyfikator zamówienia produktu.
ProductOrderName | EA, MCA | Unikatowa nazwa dla zamówienia produktu.
PublisherName | EA, MCA | Wydawca dla usług witryny Marketplace.
publisherType | EA, MCA | Typ wydawcy (wartości: firstParty thirdPartyReseller, thirdPartyAgency).
Liczba | EA, MCA | Liczbę jednostek zakupionych lub wykorzystali.
Stawka | PAYG | Zobacz UnitPrice.
ReservationId | EA, MCA | Unikatowy identyfikator wystąpienia zakupionej rezerwacji.
ReservationName | EA, MCA | Nazwa wystąpienia zakupionej rezerwacji.
ResourceGroupId | EA, MCA | Unikatowy identyfikator [grupy zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) zasób jest w.
ResourceGroupName | EA, MCA | Nazwa [grupy zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) zasób jest w.
ResourceId | EA, MCA | Unikatowy identyfikator [usługi Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources) zasobów.
resourceLocation | EA, MCA | Lokalizację centrum danych, w którym jest uruchamiany zasób. Zobacz lokalizacji.
ResourceName | EA | Nazwa zasobu.
ResourceType | MCA | Typ wystąpienia zasobu.
ServiceFamily | EA, MCA | Rodzina usługi, której należy usługa.
ServiceInfo1 | Wszyscy | Metadane specyficzne dla usługi.
ServiceInfo2 | Wszyscy | Starszego pole opcjonalne metadane specyficzne dla usługi.
ServicePeriodEndDate | MCA | Data zakończenia okresu klasyfikacji zdefiniowane i zablokowane, cennik usługi użyte lub zakupione.
servicePeriodStartDate | MCA | Data rozpoczęcia okresu klasyfikacji zdefiniowane i zablokowane, cennik usługi użyte lub zakupione.
SubscriptionId | Wszyscy | Unikatowy identyfikator dla subskrypcji.
SubscriptionName | Wszyscy | Nazwa subskrypcji.
`Tags` | Wszyscy | Tagi przypisane do tego zasobu. Nie zawiera tagi z grupy zasobów. Może służyć do grupowania lub dystrybuować koszty do wewnętrznego obsługi obciążenia zwrotnego. Aby uzyskać więcej informacji, zobacz [organizowania zasobów platformy Azure przy użyciu tagów](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/).
Jednostka | PAYG | Zobacz UnitOfMeasure.
UnitOfMeasure | Wszyscy | Jednostka miary dla rozliczeń dla usługi. Na przykład usługi obliczeniowe są rozliczane godzinowo.
Cena jednostkowa | EA | Cena jednostkowa opłata.
UsageDate | PAYG | Zobaczyć datę.

Należy pamiętać, że niektóre pola mogą się różnić w wielkość liter i odstępy między typami kont.
Starsze wersje plików zgodnie z rzeczywistym użyciem użycia ma oddzielnymi sekcjami dla instrukcji i dziennego użycia.

## <a name="ensure-that-your-charges-are-correct"></a>Upewnij się, że opłaty są poprawne

Aby dowiedzieć się więcej na temat szczegółowym zestawieniem użycia i opłat, uzyskać informacje dotyczące sposobu zrozumieć swoje [płatność za rzeczywiste użycie](./billing-understand-your-bill.md) lub [umowy klienta Microsoft](billing-mca-understand-your-bill.md) faktury.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Kolejne kroki

- [Wyświetl i Pobierz fakturę usługi Microsoft Azure](billing-download-azure-invoice.md)
- [Przeglądanie i pobieranie usługi Microsoft Azure użycia i opłat](billing-download-azure-daily-usage.md)

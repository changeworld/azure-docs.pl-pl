---
title: Interpretacja szczegółów użycia i opłat | Microsoft Docs
description: Dowiedz się, jak odczytywać i interpretować szczegółowe informacje dotyczące użycia i opłat
author: bandersmsft
manager: micflan
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/24/2019
ms.author: banders
ms.openlocfilehash: 93fad58e971d761f626b79dc68f9576ccc2e1892
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75994001"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>Interpretacja terminów w pliku Użycie i opłaty platformy Azure

Plik szczegółów użycia i opłat zawiera informacje na temat dziennego użycia oparte na stawkach negocjowanych, zakupach (na przykład rezerwacjach, opłatach w witrynie Marketplace) i zwrotach w wybranym okresie.
Opłaty nie obejmują kredytów, podatków ani innych obciążeń i rabatów.
W poniższej tabeli przedstawiono opłaty za poszczególne typy kont.

Typ konta | Użycie platformy Azure | Użycie portalu Marketplace | Zakupy | Zwroty
--- | --- | --- | --- | ---
Umowa Enterprise Agreement (EA) | Tak | Tak | Tak | Nie
Umowa klienta firmy Microsoft | Tak | Tak | Tak | Tak
Płatność zgodnie z rzeczywistym użyciem | Tak | Tak | Nie | Nie

Aby dowiedzieć się więcej o zamówieniach w witrynie Marketplace (znanych również jako usługi zewnętrzne), zobacz temat [Understand your Azure external service charges](understand-azure-marketplace-charges.md) (Omówienie opłat za usługi zewnętrzne platformy Azure).

Instrukcje dotyczące pobierania można znaleźć w temacie [How to get your Azure billing invoice and daily usage data](../manage/download-azure-invoice-daily-usage-date.md) (Jak pobrać fakturę rozliczeniową za platformę Azure i dane dziennego użycia).
Plik CSV użycia i opłat można otworzyć w programie Microsoft Excel lub w innej aplikacji obsługującej arkusze kalkulacyjne.

## <a name="list-of-terms-and-descriptions"></a>Lista terminów i opisów

W poniższej tabeli przedstawiono ważne terminy używane w najnowszej wersji pliku użycia i opłat platformy Azure.
Lista obejmuje konta płatności zgodnie z rzeczywistym użyciem, umów Enterprise Agreement (EA) i umów klienta firmy Microsoft (MCA).

Okres obowiązywania Umowy | Typ konta | Opis
--- | --- | ---
AccountName | EA, płatność zgodnie z rzeczywistym użyciem | Nazwa wyświetlana konta rejestracji umowy EA lub konta rozliczeniowego płatności zgodnie z rzeczywistym użyciem.
AccountOwnerId<sup>1</sup> | EA, płatność zgodnie z rzeczywistym użyciem | Unikatowy konta rejestracji umowy EA lub konta rozliczeniowego płatności zgodnie z rzeczywistym użyciem.
AdditionalInfo | Wszystko | Metadane dotyczące konkretnej usługi. Na przykład typ obrazu dla maszyny wirtualnej.
BillingAccountId<sup>1</sup> | Wszystko | Unikatowy identyfikator głównego konta rozliczeniowego.
BillingAccountName | Wszystko | Nazwa konta rozliczeniowego.
BillingCurrency | Wszystko | Waluta skojarzona z kontem rozliczeniowym.
BillingPeriod | EA, płatność zgodnie z rzeczywistym użyciem | Okres rozliczeniowy opłaty.
BillingPeriodEndDate | Wszystko | Data zakończenia okresu rozliczeniowego.
BillingPeriodStartDate | Wszystko | Data rozpoczęcia okresu rozliczeniowego.
BillingProfileId<sup>1</sup> | Wszystko | Unikatowy identyfikator rejestracji umowy EA, subskrypcji płatności zgodnie z rzeczywistym użyciem, profilu rozliczeniowego umowy MCA lub skonsolidowanego konta AWS.
BillingProfileName | Wszystko | Nazwa rejestracji umowy EA, subskrypcji płatności zgodnie z rzeczywistym użyciem, profilu rozliczeniowego umowy MCA lub skonsolidowanego konta AWS.
ChargeType | Wszystko | Wskazuje, czy opłata reprezentuje użycie (**Usage**), zakup (**Purchase**) czy zwrot (**Refund**).
ConsumedService | Wszystko | Nazwa usługi, z którą skojarzono opłatę.
CostCenter<sup>1</sup> | EA, umowa klienta firmy Microsoft | Centrum kosztów zdefiniowane dla subskrypcji na potrzeby śledzenia kosztów (dostępne tylko w otwartych okresach rozliczeniowych dla kont umów klienta z firmą Microsoft).
Koszt | EA, płatność zgodnie z rzeczywistym użyciem | Zobacz CostInBillingCurrency.
CostInBillingCurrency | Umowa klienta firmy Microsoft | Koszt opłaty w walucie rozliczeniowej przed odliczeniem środków lub podatków.
CostInPricingCurrency | Umowa klienta firmy Microsoft | Koszt opłaty w walucie ceny przed odliczeniem środków lub podatków.
Waluta | EA, płatność zgodnie z rzeczywistym użyciem | Zobacz BillingCurrency.
Data<sup>1</sup> | Wszystko | Użycie lub data zakupu dla opłaty.
EffectivePrice | Wszystko | Łączna cena jednostkowa w danym okresie. Ceny łączne umożliwiają uśrednienie wahań ceny jednostkowej, takich jak w przypadku stopniowej obsługi warstw, które obniżają cenę w miarę wzrostu ilości w czasie.
ExchangeRateDate | Umowa klienta firmy Microsoft | Data ustalenia kursu wymiany.
ExchangeRatePricingToBilling | Umowa klienta firmy Microsoft | Kurs wymiany używany do konwersji kosztów w walucie ceny na walutę rozliczeń.
Częstotliwość | Wszystko | Wskazuje, czy oczekuje się powtórzenia opłaty. Opłaty mogą mieć być naliczane jednorazowo (**OneTime**), powtarzać się co miesiąc lub co rok (**Recurring**) lub opierać się na użyciu (**UsageBased**).
InvoiceId | Płatność zgodnie z rzeczywistym użyciem, umowa klienta firmy Microsoft | Unikatowy identyfikator dokumentu w pliku PDF faktury.
InvoiceSection | Umowa klienta firmy Microsoft | Zobacz InvoiceSectionName.
InvoiceSectionId<sup>1</sup> | EA, umowa klienta firmy Microsoft | Unikatowy identyfikator dla działu umów EA lub sekcji faktury umowy klienta firmy Microsoft.
InvoiceSectionName | EA, umowa klienta firmy Microsoft | Nazwa działu umów EA lub sekcji faktury umowy klienta firmy Microsoft.
IsAzureCreditEligible | Wszystko | Wskazuje, czy opłata jest uprawniona do płatności za korzystanie z kredytów platformy Azure (wartości: true, false).
Lokalizacja | Umowa klienta firmy Microsoft | Lokalizacja centrum danych, w którym jest uruchamiany zasób.
MeterCategory | Wszystko | Nazwa kategorii klasyfikacji dla miernika. Na przykład *Usługi w chmurze* i *Sieć*.
MeterId<sup>1</sup> | Wszystko | Unikatowy identyfikator miernika.
MeterName | Wszystko | Nazwa miernika.
MeterRegion | Wszystko | Nazwa lokalizacji centrum danych dla usług, które są wyceniane na podstawie lokalizacji. Zobacz Location.
MeterSubCategory | Wszystko | Nazwa kategorii klasyfikacji podrzędnej miernika.
OfferId<sup>1</sup> | Wszystko | Nazwa zakupionej oferty.
PartNumber<sup>1</sup> | EA, płatność zgodnie z rzeczywistym użyciem | Identyfikator używany do uzyskiwania określonych cen taryfowych.
PlanName | EA, płatność zgodnie z rzeczywistym użyciem | Nazwa planu witryny Marketplace.
PreviousInvoiceId | Umowa klienta firmy Microsoft | Odwołanie do oryginalnej faktury, jeśli ten element wiersza to zwrot.
PricingCurrency | Umowa klienta firmy Microsoft | Waluta używana w przypadku klasyfikacji na podstawie cen negocjowanych.
Produkt | Wszystko | Nazwa produktu.
ProductId<sup>1</sup> | Umowa klienta firmy Microsoft | Unikatowy identyfikator produktu.
ProductOrderId | Wszystko | Unikatowy identyfikator zamówienia produktu.
ProductOrderName | Wszystko | Unikatowy identyfikator zamówienia produktu.
PublisherName | Wszystko | Wydawca dla usług Marketplace.
PublisherType | Wszystko | Typ wydawcy (wartości: **Azure**, **AWS**, **Marketplace**).
Ilość | Wszystko | Liczba jednostek zakupionych lub użytych.
ReservationId | EA, umowa klienta firmy Microsoft | Unikatowy identyfikator zakupionego wystąpienia rezerwacji.
ReservationName | EA, umowa klienta firmy Microsoft | Nazwa zakupionego wystąpienia rezerwacji.
ResourceGroup | Wszystko | Nazwa [grupy zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), w której znajduje się zasób.
ResourceId<sup>1</sup> | Wszystko | Unikatowy identyfikator zasobu usługi [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources).
ResourceLocation | Wszystko | Lokalizacja centrum danych, w którym jest uruchamiany zasób. Zobacz Location.
ResourceName | EA, płatność zgodnie z rzeczywistym użyciem | Nazwa zasobu.
ResourceType | Umowa klienta firmy Microsoft | Typ wystąpienia zasobu.
ServiceFamily | Umowa klienta firmy Microsoft | Rodzina usług, do której należy usługa.
ServiceInfo1 | Wszystko | Metadane dotyczące konkretnej usługi.
ServiceInfo2 | Wszystko | Starsze pole z opcjonalnymi metadanymi specyficznymi dla usługi.
ServicePeriodEndDate | Umowa klienta firmy Microsoft | Data zakończenia okresu klasyfikacji, w którym zdefiniowano i zablokowano ceny usługi wykorzystanej lub zakupionej.
ServicePeriodStartDate | Umowa klienta firmy Microsoft | Data rozpoczęcia okresu klasyfikacji, w którym zdefiniowano i zablokowano ceny usługi wykorzystanej lub zakupionej.
SubscriptionId<sup>1</sup> | Wszystko | Unikatowy identyfikator subskrypcji platformy Azure.
SubscriptionName | Wszystko | Nazwa subskrypcji platformy Azure.
Tags<sup>1</sup> | Wszystko | Tagi przypisane do zasobu. Nie obejmuje tagów grupy zasobów. Może służyć do grupowania lub dystrybuowania kosztów na potrzeby wewnętrznego obciążenia zwrotnego. Aby uzyskać więcej informacji, zobacz temat [Organize your Azure resources with tags](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) (Organizowanie zasobów platformy Azure za pomocą tagów).
Okres obowiązywania Umowy | Wszystko | Przedstawia okres ważności oferty. Na przykład: w przypadku wystąpień zarezerwowanych, wyświetla 12 miesięcy jako termin. W przypadku zakupów jednorazowych lub cyklicznych okres to 1 miesiąc (SaaS, pomoc techniczna witryny Marketplace). Nie dotyczy to użycia platformy Azure.
UnitOfMeasure | Wszystko | Jednostka miary dla rozliczeń usługi. Na przykład opłaty za usługi obliczeniowe są naliczane godzinowo.
UnitPrice | EA, płatność zgodnie z rzeczywistym użyciem | Cena za jednostkę dla opłaty.

_<sup>**1**</sup> Pola używane do tworzenia unikatowego identyfikatora dla pojedynczego rekordu kosztu._

Pamiętaj, że niektóre pola mogą różnić się wielkością liter i odstępów między typami kont.
Starsze wersje plików użycia typu „płatność zgodnie z rzeczywistym użyciem” mają oddzielne sekcje dla instrukcji i użycia dziennego.

### <a name="list-of-terms-from-older-apis"></a>Lista terminów ze starszych interfejsów API
Poniższa tabela mapuje terminy używane we wcześniejszych interfejsach API na nowe terminy. Opisy można znaleźć w powyższej tabeli.

Stary termin | Nowy termin
--- | ---
ConsumedQuantity | Ilość
IncludedQuantity | ND
InstanceId | ResourceId
Stawka | EffectivePrice
Jednostka | UnitOfMeasure
UsageDate | Data
UsageEnd | Data
UsageStart | Data


## <a name="ensure-charges-are-correct"></a>Sprawdzanie, czy opłaty są poprawne

Aby dowiedzieć się więcej na temat szczegółów użycia i opłat, przeczytaj więcej na temat interpretowania faktury typu [Płatność zgodnie z rzeczywistym użyciem](review-individual-bill.md) lub [Umowa klienta firmy Microsoft](review-customer-agreement-bill.md).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

- [Wyświetlanie i pobieranie faktury platformy Microsoft Azure](download-azure-invoice.md)
- [Wyświetlanie i pobieranie danych na temat użycia i opłat na platformie Microsoft Azure](download-azure-daily-usage.md)

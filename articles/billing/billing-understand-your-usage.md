---
title: Zapoznaj się ze szczegółowymi informacjami na temat użycia i opłat | Microsoft Docs
description: Dowiedz się, jak czytać i zrozumieć szczegółowe informacje dotyczące użycia i opłat
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
ms.openlocfilehash: 1842d32a838470d9b2af3a778c44c37464d32294
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954343"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>Zapoznaj się z postanowieniami dotyczącymi pliku użycia i opłat związanych z platformą Azure

Plik szczegółowego użycia i opłat zawiera dzienne użycie w oparciu o stawki negocjowane, zakupy (na przykład rezerwacje, opłaty dla witryny Marketplace) i zwroty za określony okres.
Opłaty nie obejmują kredytów, podatków ani opłat ani rabatów.
W poniższej tabeli przedstawiono opłaty za poszczególne typy kont.

Typ konta | Użycie platformy Azure | Użycie witryny Marketplace | Kupione | Zwroty
--- | --- | --- | --- | ---
Enterprise Agreement (EA) | Tak | Yes | Yes | Nie
Umowa klienta firmy Microsoft | Tak | Yes | Yes | Tak
Płatność zgodnie z rzeczywistym użyciem (PAYG) | Tak | Yes | Nie | Nie

Aby dowiedzieć się więcej o zamówieniach w witrynie Marketplace (znanych również jako usługi zewnętrzne), zobacz [Omówienie opłat za usługi zewnętrzne platformy Azure](billing-understand-your-azure-marketplace-charges.md).

Aby uzyskać instrukcje dotyczące pobierania, zobacz artykuł [jak uzyskać fakturę rozliczeń na platformie Azure oraz dane dziennego użycia](billing-download-azure-invoice-daily-usage-date.md).
Plik CSV użycia i opłaty można otworzyć w programie Microsoft Excel lub w innej aplikacji arkusza kalkulacyjnego.

## <a name="list-of-terms-and-descriptions"></a>Lista warunków i opisów

W poniższej tabeli opisano ważne terminy używane w najnowszej wersji pliku użycia i opłat platformy Azure.
Lista obejmuje konta płatność zgodnie z rzeczywistym użyciem (PAYG), Umowa Enterprise (EA) i Microsoft Customer Agreement (MCA).

Termin | Typ konta | Opis
--- | --- | ---
Nazwa konta | EA, PAYG | Nazwa wyświetlana konta rejestracji EA lub konta rozliczeniowego PAYG.
AccountOwnerId<sup>1</sup> | EA, PAYG | Unikatowy identyfikator konta rejestracji EA lub konta rozliczeniowego PAYG.
Informacje dodatkowe | Wszyscy | Metadane dotyczące usługi. Na przykład typ obrazu dla maszyny wirtualnej.
BillingAccountId<sup>1</sup> | Wszyscy | Unikatowy identyfikator konta rozliczeń głównych.
BillingAccountName | Wszyscy | Nazwa konta rozliczeniowego.
BillingCurrency | Wszyscy | Waluta skojarzona z kontem rozliczeniowym.
BillingPeriod | EA, PAYG | Okres rozliczeniowy opłaty.
BillingPeriodEndDate | Wszyscy | Data końcowa okresu rozliczeniowego.
BillingPeriodStartDate | Wszyscy | Data rozpoczęcia okresu rozliczeniowego.
BillingProfileId<sup>1</sup> | Wszyscy | Unikatowy identyfikator rejestracji EA, subskrypcji PAYG, profilu rozliczania MCA lub skonsolidowanego konta AWS.
BillingProfileName | Wszyscy | Nazwa rejestracji EA, subskrypcji PAYG, profilu rozliczania MCA lub skonsolidowanego konta AWS.
Opłatatype | Wszyscy | Wskazuje, czy opłata reprezentuje użycie (**użycie**), zakup (**zakup**), czy zwrot (**zwrot**).
Użyta usługa | Wszyscy | Nazwa usługi, z którą jest skojarzona opłata.
CostCenter<sup>1</sup> | EA, MCA | Centrum kosztów zdefiniowane dla subskrypcji na potrzeby śledzenia kosztów (dostępne tylko w otwartych okresach rozliczeniowych dla kont MCA).
Koszt | EA, PAYG | Zobacz CostInBillingCurrency.
CostInBillingCurrency | MCA | Koszt opłaty w walucie rozliczeniowej przed kredytami lub podatkami.
CostInPricingCurrency | MCA | Koszt opłaty w walucie cenowej przed kredytami lub podatkami.
Currency | EA, PAYG | Zobacz BillingCurrency.
Data<sup>1</sup> | Wszyscy | Użycie lub data zakupu opłaty.
EffectivePrice | Wszyscy | Cena jednostkowa zmieszana za okres. Mieszane ceny przekraczają wahania cen jednostkowych, takie jak stopniowanie warstwowe, które obniżają cenę w miarę wzrostu ilości w czasie.
ExchangeRateDate | MCA | Data ustalenia kursu wymiany.
ExchangeRatePricingToBilling | MCA | Kurs wymiany używany do konwersji kosztów w walucie cenowej na walutę rozliczeń.
Częstotliwość | Wszyscy | Wskazuje, czy opłata powinna zostać powtórzona. Opłaty mogą mieć miejsce raz (**jednorazowej**), powtarzać się w oparciu o co miesiąc lubco rok (cyklicznie) lub być oparte na użyciu (**UsageBased**).
InvoiceId | PAYG, MCA | Unikatowy identyfikator dokumentu w pliku PDF faktury.
InvoiceSection | MCA | Zobacz InvoiceSectionName.
InvoiceSectionId<sup>1</sup> | EA, MCA | Unikatowy identyfikator dla działu EA lub faktury MCA.
InvoiceSectionName | EA, MCA | Nazwa działu EA lub faktury MCA.
IsAzureCreditEligible | Wszyscy | Wskazuje, czy opłata jest uprawniona do płatności za korzystanie z kredytów platformy Azure (wartości: True, false).
Location | MCA | Lokalizacja centrum danych, w którym jest uruchomiony zasób.
Kategoria miernika | Wszyscy | Nazwa kategorii klasyfikacji dla miernika. Na przykład *usługi w chmurze* i *sieci*.
MeterId<sup>1</sup> | Wszyscy | Unikatowy identyfikator miernika.
Nazwa miernika | Wszyscy | Nazwa miernika.
MeterRegion | Wszyscy | Nazwa lokalizacji centrum danych dla usług, które są wyceniane na podstawie lokalizacji. Zobacz lokalizację.
MeterSubCategory | Wszyscy | Nazwa kategorii podklasyfikacji miernika.
OfferId<sup>1</sup> | Wszyscy | Nazwa zakupionej oferty.
PartNumber<sup>1</sup> | EA, PAYG | Identyfikator używany do uzyskiwania określonych cen taryfowych.
PlanName | EA, PAYG | Nazwa planu witryny Marketplace.
PreviousInvoiceId | MCA | Odwołanie do oryginalnej faktury, jeśli ten element wiersza jest zwrotem.
PricingCurrency | MCA | Waluta używana w przypadku klasyfikacji na podstawie cen wynegocjowanych.
Produkt | Wszyscy | Nazwa produktu.
ProductId<sup>1</sup> | MCA | Unikatowy identyfikator produktu.
ProductOrderId | Wszyscy | Unikatowy identyfikator zamówienia produktu.
ProductOrderName | Wszyscy | Unikatowa nazwa zamówienia produktu.
PublisherName | Wszyscy | Wydawca dla usług Marketplace.
Wydawcatype | Wszyscy | Typ wydawcy (wartości: **Azure**, **AWS**, **Marketplace**).
Ilość | Wszyscy | Liczba zakupionych lub zużytych jednostek.
ReservationId | EA, MCA | Unikatowy identyfikator zakupionego wystąpienia rezerwacji.
Rezerwacjaname | EA, MCA | Nazwa zakupionego wystąpienia rezerwacji.
Grupa zasobów | Wszyscy | Nazwa [grupy zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) , w której znajduje się zasób.
Identyfikator zasobu<sup>1</sup> | Wszyscy | Unikatowy identyfikator zasobu [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources) .
Lokalizacja zasobu | Wszyscy | Lokalizacja centrum danych, w którym jest uruchomiony zasób. Zobacz lokalizację.
ResourceName | EA, PAYG | Nazwa zasobu.
ResourceType | MCA | Typ wystąpienia zasobu.
Rodzina servicefamily | MCA | Rodzina usług, do której należy usługa.
Informacje o usłudze 1 | Wszyscy | Metadane dotyczące usługi.
Informacje o usłudze 2 | Wszyscy | Pole starsze z opcjonalnymi metadanymi specyficznymi dla usługi.
ServicePeriodEndDate | MCA | Data końcowa okresu oceny, który został zdefiniowany i zablokowany w przypadku usługi zużytej lub zakupionej.
ServicePeriodStartDate | MCA | Data rozpoczęcia okresu oceny, który został zdefiniowany i zablokowany Cennik dla usługi zużytej lub zakupionej.
Identyfikator subskrypcji<sup>1</sup> | Wszyscy | Unikatowy identyfikator dla subskrypcji platformy Azure.
SubscriptionName | Wszyscy | Nazwa subskrypcji platformy Azure.
Tagi<sup>1</sup> | Wszyscy | Tagi przypisane do zasobu. Nie obejmuje tagów grupy zasobów. Może służyć do grupowania lub dystrybucji kosztów dla wewnętrznej obciążenia zwrotnego. Aby uzyskać więcej informacji, zobacz [organizowanie zasobów platformy Azure za pomocą tagów](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/).
Termin | Wszyscy | Przedstawia termin ważności oferty. Na przykład: W przypadku wystąpień zarezerwowanych w danym okresie będzie wyświetlana 12 miesięcy. Dla jednorazowych zakupów lub cyklicznych zakupów termin wynosi 1 miesiąc (SaaS, pomoc techniczna Marketplace). Nie dotyczy to użycia platformy Azure.
Jednostka miary | Wszyscy | Jednostka miary dla rozliczeń dla usługi. Na przykład usługi obliczeniowe są rozliczane na godzinę.
Jednostk | EA, PAYG | Cena za jednostkę dla opłaty.

_<sup>**1**</sup> pola używane do tworzenia unikatowego identyfikatora dla pojedynczego rekordu kosztu._

Pamiętaj, że niektóre pola mogą różnić się wielkością liter i odstępów między typami kont.
Starsze wersje plików użycia z opcją płatność zgodnie z rzeczywistym użyciem mają oddzielne sekcje dla instrukcji i dziennego użycia.

### <a name="list-of-terms-from-older-apis"></a>Lista terminów ze starszych interfejsów API
Poniższa tabela mapuje terminy używane we wcześniejszych interfejsach API do nowych warunków. Opisy można znaleźć w powyższej tabeli.

Stary termin | Nowy termin
--- | ---
Użyta ilość | Ilość
IncludedQuantity | ND
Identyfikator wystąpienia | ResourceId
Stawka | EffectivePrice
Jednostka | Jednostka miary
UsageDate | Date
UsageEnd | Date
UsageStart | Date


## <a name="ensure-charges-are-correct"></a>Upewnij się, że opłaty są poprawne

Aby dowiedzieć się więcej na temat szczegółowych informacji o użyciu i opłatach, Przeczytaj o tym, jak poznać fakturę z [płatnością zgodnie z rzeczywistym](./billing-understand-your-bill.md) użyciem lub z umowy dotyczącej umów dla [klientów firmy Microsoft](billing-mca-understand-your-bill.md) .

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

- [Wyświetlanie i pobieranie faktury Microsoft Azure](billing-download-azure-invoice.md)
- [Wyświetlaj i pobieraj Microsoft Azure użycie i opłaty](billing-download-azure-daily-usage.md)

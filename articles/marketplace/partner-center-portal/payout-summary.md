---
title: Podsumowanie komercyjnej wypłaty z witryny Marketplace | Portal Azure Marketplace
description: Podsumowanie wypłaty zawiera szczegółowe informacje o pieniędzy uzyskanych w ramach oferty. Pozwala on również wiedzieć, kiedy otrzymasz płatności i ile będziesz płacić.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: guide
ms.date: 12/10/2019
ms.openlocfilehash: 0d05802c9d5d80f91913291d710b674369f0ff17
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74980272"
---
# <a name="payout-reporting"></a>Raportowanie wypłaty

**Podsumowanie wypłaty** zawiera szczegółowe informacje o pieniędzy uzyskanych w firmie Microsoft. Pozwala on również wiedzieć, kiedy otrzymasz płatności i ile będziesz płacić.

Jeśli sprzedajesz oferty w portalu Azure Marketplace, zobaczysz również informacje o pomyślnych wypłatach w **podsumowaniu wypłat**. Aby uzyskać więcej informacji na temat płatności w portalu Azure Marketplace, zapoznaj się z tematem [Microsoft Azure Marketplace zasady udziału](https://go.microsoft.com/fwlink/p/?LinkId=722436) oraz [umowę wydawcy Microsoft Azure Marketplace](https://go.microsoft.com/fwlink/p/?LinkID=699560).

> [!NOTE]
> Aby można było skorzystać z wypłat, Twoje przejścia muszą osiągać [próg płatności](payment-thresholds-methods-timeframes.md) wynoszący $50. Aby uzyskać szczegółowe informacje na temat progu płatności, zobacz Tę stronę i zapoznaj się z [umową wydawcy Microsoft Azure Marketplace](https://go.microsoft.com/fwlink/p/?LinkID=699560).

- [Raport wypłaty: różnica między portal Cloud Partner a centrum partnerskim](#payout-report-difference-between-cloud-partner-portal-and-partner-center)
- [Typy klientów](#customer-types)
- [Relacja między wypłatami i użyciem](#corelation-between-payout-and-usage)
- [Pobieranie historii transakcji](#transaction-history-download-export)
- [Pytania i pomoc techniczna dotyczące rozliczeń](#billing-questions-and-support)

## <a name="payout-report-difference-between-cloud-partner-portal-and-partner-center"></a>Raport wypłaty: różnica między portal Cloud Partner a centrum partnerskim

| | Portal Cloud Partner | Centrum partnerskie |
|---------|---------|---------|
| Linki | https://cloudpartner.azure.com/ | https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory i https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| Nawigacja | Raportowanie wypłaty w usłudze Insights | Raportowanie wypłaty w centrum partnerskim — ikona wypłaty |
| Zakres | <ul> <li>Element Transaction na wiersz jest widoczny dla kolekcji w toku, zebranych i płatnych </li> <li>Raportowanie — pokazuje wszystkie elementy wiersza po utworzeniu zamówienia zakupu, w tym zbieranie w toku i rozliczanie w toku oraz stan kolekcji i elementy wierszy, które nie są jeszcze kwalifikujące się do płatności. </li> </ul> | <ul> <li>Pokazuje elementy wiersza, gdy są one uznawane za kwalifikujące się zyski.</li> <li>Klienci zwracają się najpierw do firmy Microsoft, a następnie dostawcy ISV mogą zobaczyć raport wypłaty.</li> <li>Raport wypłaty nie będzie wyświetlał kolekcji w toku i rozliczeń w toku.  </li> </ul>  |
| Transakcja nie jest gotowa do wypłaty | Rozliczanie w toku | Następna Szacowana płatność: stan wypłaty jest w stanie nieprzetworzonym.  |
| Stan wypłaty |  | Nieprzetworzone <br> Zdobywanie oferty kwalifikuje się do płatności. Pozostaje w tym stanie przez okres chłodzenia, zgodnie z definicją w przewodniku programu dla programu zachęty. <br> <br> Nowego <br> Zamówienie płatności — generowane w trakcie weryfikacji wewnętrznej przed przetworzeniem płatności. <br> <br> Wysłana <br> Płatność została wysłana do Twojego banku. |

## <a name="customer-types"></a>Typy klientów 

### <a name="enterprise-agreement"></a>Umowa Enterprise Agreement

Klienci bez Umowa Enterprise są rozliczani miesięcznie za licencje na oprogramowanie w portalu Marketplace. Klienci z Umowa Enterprise są rozliczani miesięcznie za pośrednictwem faktury prezentowanej kwartalnie.

### <a name="credit-cards-and-monthly-invoice"></a>Karty kredytowe i Faktura miesięczna

Klienci mogą również skorzystać z karty kredytowej i faktury miesięcznej. W tym przypadku opłaty za licencje na oprogramowanie będą naliczane miesięcznie.

### <a name="csp-and-direct-pay-users"></a>Dostawcy usług kryptograficznych i bezpośrednich wynagrodzeń

Na przykład jeśli klient kupi przy użyciu karty kredytowej.

## <a name="corelation-between-payout-and-usage"></a>Relacja między wypłatami i użyciem 

|Opis    |    Data  | Zamówienia/użycie  | Wypłata |
|----------|----------|-----------|-------------|
|Okres zamówienia   | 15 sierpnia 2019 — 30 sierpnia, 2019 | **Kolejność atrybutów korelacji** <br> <ul> <li>OrderId (Identyfikator zamówienia)</li> <li>CustomerId (Identyfikator klienta)</li> </ul> <br> **Użycie** <br> <ul> <li>CustomerId (Identyfikator klienta) </li> <li>Nazwa klienta</li> <li>(UsageReference) PurchaseRecordId/LineItemId</li> <li> Szacowana opłata rozszerzona <br> Szacowana wypłata (komputer) </li> </ul> |  |
|Zakończenie okresu (miesiąc)   | 30 sie 2019 | | |
|Data rozliczenia | Września 1, 2019 | | |
|Data płatności klienta | Września 1, 2019 | | |
|Okres płatności (tylko karty kredytowe:, 30 dni) | Września 1, 2019 – września 30, 2019 | | **Kolejność atrybutów korelacji:** <br> <ul><li>AssetId</li> <li>Identyfikator klienta</li> <li> Nazwa klienta</li> </ul> <br> **Użycie** <br> <ul> <li>AssetId</li> <li>CustomerId (Identyfikator klienta)</li> <li>Nazwa klienta</li> <li>OrderId (Identyfikator zamówienia)</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Stan wypłaty:** Nieprzetworzone |
|Początek okresu zbierania | Września 1, 2019 | | |
|Koniec okresu zbierania danych (maksymalnie 30 dni) | Września 30, 2019 | | |
|Data wyliczenia wypłaty (co miesiąc 15) | 1 października 2019 | | **Atrybuty korelacji** <br> <ul><li>AssetId</li> <li>Identyfikator klienta</li> <li>Nazwa klienta</li> </ul> <br> **Użycie** <br> <ul> <li>AssetId</li> <li>CustomerId (Identyfikator klienta)</li> <li>Nazwa klienta</li> <li>OrderId (Identyfikator zamówienia)</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Stan wypłaty:** Nowego |
|Data wypłaty | 15 października 2019 | | **Atrybuty korelacji** <br> <ul><li>AssetId</li> <li>Identyfikator klienta</li> <li> Nazwa klienta</li> </ul> <br> **Użycie** <br> <ul> <li>AssetId</li> <li>CustomerId (Identyfikator klienta)</li> <li>Nazwa klienta</li> <li>OrderId (Identyfikator zamówienia)</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Stan wypłaty:** Płatność wysłana |

### <a name="enterprise-agreement-quarterlymonthly-customers"></a>Umowa Enterprise Agreement (kwartalne/miesięczne klienci)

| Opis |    Data  | Użycie | Wypłata |
|----------|----------|---------|-----------|
|Okres zamówienia | 15 sierpnia 2019 — 30 sierpnia, 2019 | **Kolejność atrybutów korelacji** <br> <ul> <li>OrderId (Identyfikator zamówienia)</li> <li>CustomerId (Identyfikator klienta)</li> </ul> <br> **Raport użycia** <br> <ul> <li>CustomerId (Identyfikator klienta) </li> <li>Nazwa klienta</li> <li>(UsageReference) PurchaseRecordId/LineItemId</li> <li> Szacowana opłata rozszerzona <br> Szacowana wypłata (komputer) </li> </ul> | |
|Zakończenie okresu (kwartał) | Września 30, 2019 | | |
|Data rozliczenia | 15 października 2019 | | |
|Okres płatności (tylko karty kredytowe:, 30 dni) | nd. | | |
|Początek okresu zbierania | 15 października 2019 | | |
|Tylko karty kredytowe, 30 dni | LIS 1, 2019-lis 30, 2019 | | |
|Koniec okresu zbierania danych (maksymalnie 90 dni) | 15 stycznia 2020 | | |
|Data płatności klienta | 30 grudnia, 2019 | | |
|Obliczanie wypłaty | 15 stycznia 2020 | | |
|Data wypłaty | 15 lutego 2020 | | **Dla kwartalnych klientów** <br> <br> **Raport o zamówieniach** <br> <ul><li>AssetId</li> <li>Identyfikator klienta</li> <li> Nazwa klienta</li> </ul> <br> **Użycie** <br> <ul> <li>AssetId</li> <li>CustomerId (Identyfikator klienta)</li> <li>Nazwa klienta</li> <li>OrderId (Identyfikator zamówienia)</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Stan wypłaty:** wysłano |

<!---
## Billing

Depending on the transaction option used, the publisher’s software license fees can be presented as follows:

* Free: No charge for software licenses.
* **Bring the own license (BYOL)**: Any applicable charges for software licenses are managed directly between the publisher and customer. Microsoft only passes through Azure infrastructure usage fees. (Virtual Machines and Azure Applications only).
* **Pay-as-you-go**: Software license fees are presented as a per-hour, per-core (VCPU) pricing rate based on the Azure infrastructure used. This only applies to Virtual Machines and Azure Applications. 
* **Subscription pricing**: Software license fees are presented as a monthly or annual recurring fee billed as a flat rate or per-seat. This only applies to SaaS Apps and Azure Applications - Managed Apps.

### Pay as you go

If you enable the Pay-As-You-Go option, then you have the following cost structure. 

* If you enable the Pay-As-You-Go option, then you have the following cost structure.

|Your license cost  | $1.00 per hour  |
|---------|---------|
|Azure usage cost (D1/1-Core)    |   $0.14 per hour     |
|*Customer is billed by Microsoft*    |  *$1.14 per hour*       |

* In this scenario, Microsoft bills $1.14 per hour for use of your published VM image.

|Microsoft bills  | $1.14 per hour  |
|---------|---------|
|Microsoft pays you 80% of your license cost|   $0.80 per hour     |
|Microsoft keeps 20% of your license cost  |  $0.20 per hour       |
|Microsoft keeps 100% of the Azure usage cost | $0.14 per hour |

### Bring Your Own License (BYOL)

* If you enable the BYOL option, then you have the following cost structure.

|Your license cost  | License fee negotiated and billed by you  |
|---------|---------|
|Azure usage cost (D1/1-Core)    |   $0.14 per hour     |
|*Customer is billed by Microsoft*    |  *$0.14 per hour*       |

* In this scenario, Microsoft bills $0.14 per hour for use of your published VM image.

|Microsoft bills  | $0.14 per hour  |
|---------|---------|
|Microsoft keeps the Azure usage cost    |   $0.14 per hour     |
|Microsoft keeps 0% of your license cost   |  $0.00 per hour       |

### SaaS App subscription

This option must be configured to sell through Microsoft and can be priced at a flat rate or per user on a monthly or annual basis.
•   If you enable the Sell through Microsoft option for a SaaS offer, then you have the following cost structure.

|Your license cost       | $100.00 per month  |
|--------------|---------|
|Azure usage cost (D1/1-Core)    | Billed directly to the publisher, not the customer |
|*Customer is billed by Microsoft*    |  *$100.00 per month (note: publisher must account for any incurred or pass-through infrastructure costs in the license fee)*  |

* In this scenario, Microsoft bills $100.00 for your software license and pays out $80.00 to the publisher.
* Partners who have qualified for the Reduced Marketplace Service Fee will see a reduced transaction fee on the SaaS offers from May 2019 until June 2020. In this scenario, Microsoft bills $100.00 for your software license and pays out $90.00 to the publisher.

|Microsoft bills  | $100.00 per month  |
|---------|---------|
|Microsoft pays you 80% of your license cost <br> \* Microsoft pays you 90% of your license cost for any qualified SaaS apps   |   $80.00 per month <br> \* $90.00 per month    |
|Microsoft keeps 20% of your license cost <br> \* Microsoft keeps 10% of your license cost for any qualified SaaS apps.  |  $20.00 per month <br> \* $10.00     |

**Reduced Marketplace Service Fee:** For certain SaaS Products that you publish on our Commercial Marketplace, Microsoft will reduce its Marketplace Service Fee from 20% (as described in the Microsoft Publisher Agreement) to 10%.  In order for your Product to qualify, at least one of your products must be designated by Microsoft as either IP co-sell ready or IP co-sell prioritized. To receive this reduced Marketplace Service Fee for the month, eligibility must be met at least five (5) business days before the end of the previous calendar month. Reduced Marketplace Service fee will not apply to VMs, Managed Apps or any other products made available through our Commercial Marketplace.  This Reduced Marketplace Service Fee will be available to qualified offers, with license charges collected by Microsoft between May 1, 2019 and June 30, 2020.  After that time, the Marketplace Service Fee will return to its normal amount.

### Customer invoicing, payment, billing, and collections

**Invoicing and payment**

Publisher can use the customer's preferred invoicing method to deliver subscription or PAYGO software license fees.

**Enterprise agreement** 

If the customer's preferred invoicing method is the Microsoft Enterprise Agreement, your software license fees will be billed using this invoicing method as an itemized cost, separate from any Azure-specific usage costs.

**Credit cards and monthly invoice** 

Customers can also pay using a credit card and a monthly invoice. In this case, your software license fees will be billed just like the Enterprise Agreement scenario, as an itemized cost, separate from any Azure-specific usage costs.



**Billing and collections** 

Publisher software license billing is presented using the customer selected method of invoicing and follows the invoicing timeline. Customers without an Enterprise Agreement in place are billed monthly for marketplace software licenses. Customers with an Enterprise Agreement are billed monthly via an invoice that is presented quarterly.

When subscription or Pay-as-You-Go pricing models are selected, Microsoft acts as the agent of the publisher and is responsible for all aspects of billing, payment, and collection.

### Publisher payout and reporting

* Any software licensing fees collected by Microsoft as an agent are subject to a 20% transaction fee unless otherwise specified and are deducted at the time of publisher payout.

* Customers typically purchase using the Enterprise Agreement or a credit-card enabled pay-as-you-go agreement. The agreement type determines billing, invoicing, collection, and payout timing.
--->

## <a name="transaction-history-download-export"></a>Pobieranie historii transakcji — eksport

Ta opcja zapewnia pobranie każdego elementu linii zdobywania, który jest widoczny na stronie historii transakcji, typu zdobywania, daty, skojarzonej transakcji, liczby klientów, produktu i innych informacji transakcyjnych dotyczących programu zachęt. 

| Nazwa kolumny     | Opis    | 
|-------------|-------------------------------|
| earningId                      | Unikatowy identyfikator dla każdego zdobywania                                                                                                       |
| participantId                  | Podstawowa tożsamość ponosząca partnera w ramach programu                                                                            | 
| participantIdType              | Przede wszystkim IDENTYFIKATORem programu dla programów zachęt i sprzedawcy w przypadku programów do magazynowania i Azure Marketplace                                          | 
| uczestnikname                | Nazwa partnera zdobywania                                                                                                              | 
| partnerCountryCode             | Lokalizacja/kraj partnera zdobywania                                                                                                  |
| programName                    | Nazwa programu zachęty/sklepu                                                                                                             | 
| transactionId                  | Unikatowy identyfikator transakcji                                                                                                    | 
| transactionCurrency            | Waluta, w której wystąpiła oryginalna transakcja klienta (nie jest to waluta lokalizacji partnera)                                     | 
| transactionDate                | Data transakcji. Przydatne w przypadku programów, w których wiele transakcji przyczynia się do jednego zdobywania                                           | 
| transactionExchangeRate        | Kurs wymiany używany do wyświetlania odpowiedniej kwoty (USD) transakcji                                                                 | 
| transactionAmount              | Kwota transakcji w pierwotnej walucie transakcji oparta na wygenerowanym wykorzystaniu                                              | 
| transactionAmountUSD           | Kwota transakcji w USD                                                                                                                | 
| górę                          | Oznacza regułę biznesową dla zdobywania                                                                                                  | 
| earningRate                    | Stawka zachęt zastosowana do kwoty transakcji w celu wygenerowania zdobywania                                                                      | 
| quantity                       | Różni się w zależności od programu. Wskazuje liczbę rozliczeń dla programów transakcyjnych                                                            | 
| Liczba                   | Wskazuje typ ilości, na przykład: rozliczona ilość, MAU                                                                                     |
| Pozostały                    | Wskazuje, czy jest to opłata, Rabat, Coop, sprzedaż itp.                                                                                          | 
| earningAmount                  | Kwota zdobywania w pierwotnej walucie transakcji                                                                                      |
| earningAmountUSD               | Kwota zdobywania w USD                                                                                                                    |
| earningDate                    | Data okresu zdobywania                                                                                                                      |
| calculationDate                | Data obliczenia okresu zdobywania w systemie                                                                                            |
| earningExchangeRate            | Kurs wymiany używany do wyświetlania odpowiedniej ilości USD                                                                                  |
| exchangeRateDate               | Data kursu wymiany używana do obliczania EarningAmount USD                                                                                   | 
| paymentAmountWOTax             | Kwota zarobków (bez podatku) w ramach płatności w walucie w przypadku płatności "wysłanych"                                                                 |
| paymentCurrency                | Płatność na walutę wybraną przez partnera w profilu płatności. Wyświetlane tylko dla wysłanych płatności                                                   |
| paymentExchangeRate            | Kurs wymiany używany do obliczania paymentAmountWOTax w walucie płatności przy użyciu ExchangeRateDate                                            |
| paymentId            | Unikatowy identyfikator płatności. Ta liczba jest widoczna w wyrażeniu bankowym                                            |
| paymentStatus            | Stan płatności                                            |
| paymentStatusDescription            | Przyjazny opis stanu płatności                                            |
| customerId                     | Zawsze będzie puste                                                                                                                     |
| customerName                   | Zawsze będzie puste                                                                                                                     |
| partNumber                     | Zawsze będzie puste                                                                                                                     |
| productName                    | Nazwa produktu połączona z transakcją                                                                                                       |
| productId                      | Unikatowy identyfikator produktu                                                                                                                |
| parentProductId                | Unikatowy identyfikator nadrzędnego produktu. Uwaga: Jeśli nie istnieje produkt nadrzędny dla transakcji, identyfikator produktu nadrzędny = identyfikator produktu. |
| parentProductName              | Nazwa produktu nadrzędnego. Uwaga: Jeśli nie istnieje produkt nadrzędny dla transakcji, nazwa produktu nadrzędnego = Nazwa produktu.   |
| productType                    | Typ produktu (na przykład aplikacja, dodatek, gra itp.)                                                                                        |
| invoiceNumber                  | Numer faktury (dotyczy tylko EA)                                                                                                  |
| resellerId                     | Identyfikator odsprzedawcy                                                                                                                      |
| Nazwa odsprzedawcy                   | Nazwa odsprzedawcy                                                                                                                            |
| transactionType                | Typ transakcji (na przykład zakup, zwrot, odwrócenie, obciążenia zwrotnego itd.)                                                               |
| localProviderSeller            | Dostawca lokalny/sprzedawca rekordu                                                                                                          |
| taxRemitted                    | Kwota podatku (sprzedaż, użycie lub podatki VAT/GST ().                                                                                   |
| taxRemitModel                  | Osoba odpowiedzialna za przekazaną podatki (sprzedaż, użycie lub podatki VAT/GST ().                                                                    |
| storeFee                       | Kwota zachowywana przez firmę Microsoft jako opłata za udostępnienie aplikacji lub dodatku w sklepie.                                            |
| transactionPaymentMethod       | Instrument płatniczy klienta używany do transakcji (na przykład karta, rozliczenia z nośnika komórkowego, płatności w systemie PayPal itp.)                                |
| tpan                           | Wskazuje sieć usługi AD innej firmy                                                                                                     |
| customerCountry                | Kraj klienta                                                                                                                         |
| customerCity                   | Miasto klienta                                                                                                                            |
| customerState                  | Stan klienta                                                                                                                           |
| customerZip                    | Kod pocztowy klienta                                                                                                                 |
| TenantID                       |                                                                                                                                          |
| externalReferenceId            | Unikatowy identyfikator programu                                                                                                        |
| externalReferenceIdLabel       | Etykieta unikatowego identyfikatora                                                                                                                  |
| transactionCountryCode       | Kod kraju, w którym wystąpiła transakcja                                                                                                                  |
| taxCountry       | Sprzedaż do kraju klienta                                                                                                                  |
| taxState       | Sprzedaż do stanu klienta                                                                                                                  |
| taxCity       | Sprzedaż na miasto klienta                                                                                                                  |
| taxZipCode       | Sprzedaż do pliku zip klienta                                                                                                                  |
| LicensingProgramName       |                                                                                                                   |
| Kod programu       | Ciąg do mapowania przy użyciu nazwy programu                                                                                                                   |
| earningAmountInLastPaymentCurrency       | Kwota zarobków w ostatniej walucie płatności (pole będzie puste, jeśli nie zostały uiszczone żadne wcześniejsze płatności)                                                                                                                   |
| lastPaymentCurrency       | Waluta ostatniej płatności (pole będzie puste, jeśli nie zapłacisz żadnej wcześniejszej płatności)                                                                                                                   |
| AssetId       | Unikatowy identyfikator zamówień klientów dla usługi w portalu Marketplace.  Reprezentuje elementy wiersza zakupu transakcji transakcyjnych. Może istnieć wiele zasobów.                                                                                                                   |
| OrderId (Identyfikator zamówienia)       | odnosi się do faktury klienta                                                                                                                   |
| LineItemId       | pojedynczy wiersz faktury klienta                                                                                                                   |
| Kraj klienta       | Nazwa kraju podana przez klienta.  Może to być inne niż kraj w subskrypcji platformy Azure klienta.                                                                                                                   |
| EmailAddress klienta       | Adres e-mail dostarczony przez klienta końcowego.  Może się to różnić od adresu e-mail w subskrypcji platformy Azure klienta.                                                                                                                   |
| Identyfikatora skuId       | Identyfikator jednostki SKU zdefiniowany podczas publikowania. Oferta może mieć wiele jednostek SKU, ale jednostka SKU może być skojarzona tylko z jedną ofertą.                                                                                                                   |

>[!Note]
>Wszystkie raporty i szczegółowe informacje dotyczące opcji publikowania Transact są dostępne za pośrednictwem sekcji szczegółowe informacje w sekcji portal Cloud Partner lub Analytics Centrum partnerskiego.

## <a name="billing-questions-and-support"></a>Pytania i pomoc techniczna dotyczące rozliczeń

Aby uzyskać pomoc dotyczącą pytań dotyczących rozliczeń, skontaktuj się z [pomocą techniczną wydawcy portalu Marketplace](https://aka.ms/marketplacepublishersupport).

---
title: Śledzenie salda środków na korzystanie z platformy Azure dla umowy klienta firmy Microsoft
description: Dowiedz się, jak sprawdzić saldo środków na korzystanie z platformy Azure dla umowy klienta firmy Microsoft.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 4eae7299ab696b01c57a27fd46cbf903c9395152
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375536"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>Śledzenie salda środków na korzystanie z platformy Azure dla umowy klienta firmy Microsoft

Saldo środków na korzystanie z platformy Azure dla Twojego konta rozliczeniowego związanego z umową klienta firmy Microsoft możesz sprawdzić w witrynie Azure Portal. 

Niektóre opłaty można regulować za pomocą tych środków. Opłaty są naliczane w przypadku korzystania z produktów, do których nie stosuje się środków, lub jeśli użycie przekroczy saldo środków. Aby uzyskać więcej informacji, zobacz [Produkty nieobjęte środkami na korzystanie z platformy Azure](#products-that-arent-covered-by-azure-credits).

Na koncie rozliczeniowym dla umowy klienta firmy Microsoft środki są przypisywane do profilu rozliczeniowego. Każdy profil rozliczeniowy ma swoje własne środki. Aby wyświetlić saldo środków na korzystanie z platformy Azure dla profilu rozliczeniowego, musisz mieć rolę właściciela, współautora, czytelnika lub menedżera faktur dla profilu rozliczeniowego albo właściciela, współautora lub czytelnika dla konta rozliczeniowego. Aby dowiedzieć się więcej na temat ról, zobacz [Omówienie ról administracyjnych dla umowy klienta firmy Microsoft na platformie Azure](billing-understand-mca-roles.md).

Ten artykuł dotyczy konta rozliczeniowego związanego z umową klienta firmy Microsoft. [Sprawdź, czy masz dostęp do umowy klienta firmy Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-your-credit-balance-in-the-azure-portal"></a>Sprawdzanie salda środków w witrynie Azure Portal

1. Zaloguj się w witrynie [Azure Portal]( https://portal.azure.com).

2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

    ![Zrzut ekranu przedstawiający wyszukiwanie zarządzania kosztami i rozliczeń w portalu](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3.  Wybierz pozycję **Środki na korzystanie z platformy Azure** wyświetlaną po lewej stronie. W zależności od praw dostępu może być konieczne wybranie profilu rozliczeniowego lub konta rozliczeniowego, a następnie wybranie pozycji **Środki na korzystanie z platformy Azure**.

4. Strona środków na korzystanie z platformy Azure zawiera następujące informacje:

   ![Zrzut ekranu przedstawiający saldo środków i transakcje w profilu rozliczeniowym](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | Termin               | Definicja                           |
   |--------------------|--------------------------------------------------------|
   | Szacowane saldo  | Szacowana kwota środków po uwzględnieniu wszystkich rozliczonych i oczekujących transakcji |
   | Bieżące saldo    | Kwota środków od ostatniej faktury. Nie obejmuje ona oczekujących transakcji |
   | Transakcje       | Transakcje rozliczeniowe, które miały wpływ na saldo środków na korzystanie z platformy Azure |

   Gdy szacowane saldo spadnie do 0, rozpocznie się naliczanie opłat za używanie wszystkich produktów, również tych, których użycie jest rozliczane za pomocą środków.

6. Wybierz pozycję **Lista środków**, aby wyświetlić listę środków dla profilu rozliczeniowego. Lista środków zawiera następujące informacje:

   ![Zrzut ekranu przedstawiający listę środków dla profilu rozliczeniowego](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | Termin | Definicja |
   |---|---|
   | Element źródłowy | Źródło pozyskiwania środków |
   | Data rozpoczęcia | Data nabycia środków |
   | Data wygaśnięcia | Data wygaśnięcia środków |
   | Bieżące saldo | Saldo od ostatniej faktury |
   | Pierwotna kwota | Pierwotna kwota środków |
   | Stan | Bieżący stan środków. Stan może mieć wartość: aktywne, użyte, wygasłe lub wygasające |

## <a name="check-your-credit-balance-programmatically"></a>Programowe sprawdzanie salda środków

Aby programowo uzyskać saldo środków na koncie rozliczeniowym, możesz użyć interfejsów API [rozliczeń](https://docs.microsoft.com/rest/api/billing/) i [użycia](https://docs.microsoft.com/rest/api/consumption/) platformy Azure.

W przykładach przedstawionych poniżej używane są interfejsy API REST. Obecnie program PowerShell i interfejs wiersza polecenia platformy Azure nie są obsługiwane.

### <a name="find-billing-profiles-you-have-access-to"></a>Znajdowanie profilu rozliczeniowego, do którego ma się dostęp

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?$expand=billingProfiles&api-version=2019-10-01-preview
```
Odpowiedź interfejsu API zwraca listę kont rozliczeniowych i ich profilów rozliczeniowych.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "billingProfiles": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
            "name": "PBFV-xxxx-xxx-xxx",
            "properties": {
              "address": {
                "addressLine1": "AddressLine1",
                "city": "City",
                "companyName": "CompanyName",
                "country": "Country",
                "postalCode": "xxxxx",
                "region": "Region"
              },
              "currency": "USD",
              "displayName": "Development",
              "hasReadAccess": true,
              "invoiceDay": 5,
              "invoiceEmailOptIn": true
            },
            "type": "Microsoft.Billing/billingAccounts/billingProfiles"
          }
        ],
        "displayName": "Contoso",
        "hasReadAccess": true,
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

Właściwość `displayName` profilu rozliczeniowego służy do identyfikowania profilu rozliczeniowego, dla którego chce się sprawdzić saldo środków. Skopiuj właściwość `id` profilu rozliczeniowego. Jeśli na przykład chcesz sprawdzić saldo środków dla profilu rozliczeniowego **Development**, skopiujesz wartość ```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```. Przeklej gdzieś tę wartość, aby można z niej było skorzystać w następnym kroku.

### <a name="get-azure-credit-balance"></a>Pobieranie salda środków na korzystanie z platformy Azure 

Wykonaj następujące żądanie, zastępując wartość `<billingProfileId>` wartością `id` skopiowaną z pierwszego kroku (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```). 

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/credits/balanceSummary?api-version=2019-10-01
```

Odpowiedź interfejsu API zwraca szacowane i bieżące saldo dla profilu rozliczeniowego.

```json
{
  "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/credits/balanceSummary/57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Consumption/credits/balanceSummary",
  "eTag": null,
  "properties": {
    "balanceSummary": {
      "estimatedBalance": {
        "currency": "USD",
        "value": 996.13
      },
      "currentBalance": {
        "currency": "USD",
        "value": 997.87
      }
    },
    "pendingCreditAdjustments": {
      "currency": "USD",
      "value": 0.0
    },
    "expiredCredit": {
      "currency": "USD",
      "value": 0.0
    },
    "pendingEligibleCharges": {
      "currency": "USD",
      "value": -1.74
    }
  }
}
```

| Nazwa elementu  | Opis                                                                           |
|---------------|---------------------------------------------------------------------------------------|
| `estimatedBalance` | Szacowana kwota środków po uwzględnieniu wszystkich rozliczonych i oczekujących transakcji. |
| `currentBalance`   | Kwota środków od ostatniej faktury. Nie obejmuje ona oczekujących transakcji.    |
| `pendingCreditAdjustments`      | Korekty, takie jak zwroty, które nie zostały jeszcze zafakturowane.  |
| `expiredCredit`      |  Środki, które wygasły od ostatniej faktury.  |
| `pendingEligibleCharges`  | Opłaty możliwe do pokrycia ze środków, które nie zostały jeszcze zafakturowane.   |

### <a name="get-list-of-credits"></a>Pobieranie listy środków

Wykonaj następujące żądanie, zastępując wartość `<billingProfileId>` wartością `id` skopiowaną z pierwszego kroku (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```). 

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/lots?api-version=2019-10-01
```
Odpowiedź interfejsu API zwraca listy środków na korzystanie z platformy Azure dla profilu rozliczeniowego.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 500.0
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/xxxx-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 497.87
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    }
  ]
}
```
| Nazwa elementu  | Opis                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `originalAmount` | Pierwotna kwota środków. |
| `closedBalance`   | Saldo od ostatniej faktury.    |
| `source`      | Źródło, które definiuje, kto i jak uzyskał środki. |
| `startDate`      |  Data aktywowania środków.  |
| `expirationDate`  | Data wygaśnięcia środków.   |
| `poNumber`  | Numer zamówienia zakupu faktury, na której rozliczono środki.   |

### <a name="get-transactions-that-affected-credit-balance"></a>Pobieranie transakcji, które wpłynęły na saldo środków

Wykonaj następujące żądanie, zastępując wartość `<billingProfileId>` wartością `id` skopiowaną z pierwszego kroku (```providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```). Aby pobrać transakcje dla określonego przedziału czasu, trzeba będzie przekazać wartości **startDate**  i **endDate**.

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/events?api-version=2019-10-01&startDate=2018-10-01T00:00:00.000Z&endDate=2019-10-11T12:00:00.000Z?api-version=2019-10-01
```
Odpowiedź interfejsu API zwraca wszystkie transakcje, które wpłynęły na saldo środków dla Twojego profilu rozliczeniowego.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx`/providers/Microsoft.Consumption/events/e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "10/11/2019",
        "description": "Credit eligible charges as of 10/11/2019",
        "newCredit": {
          "currency": "USD",
          "value": 0.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": -1.74
        },
        "closedBalance": {
          "currency": "USD",
          "value": 998.26
        },
        "eventType": "PendingCharges",
        "invoiceNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/events/381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "09/18/2019",
        "description": "New credit added on 09/18/2019",
        "newCredit": {
          "currency": "USD",
          "value": 500.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": 0.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 1000.0
        },
        "eventType": "PendingNewCredit",
        "invoiceNumber": ""
      }
    }
  ]
}
```
| Nazwa elementu  | Opis                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `transactionDate` | Data przeprowadzona transakcji. |
| `description` | Opis transakcji. |
| `adjustments`   | Korekty środków dla transakcji.    |
| `creditExpired`      | Kwota środków, która wygasła. |
| `charges`      |  Opłaty za transakcję.  |
| `closedBalance`  | Saldo po transakcji.   |
| `eventType`  | Typ transakcji.   |
| `invoiceNumber`  | Numer faktury, na której rozliczono transakcję. Dla oczekującej transakcji będzie on pusty.   |

## <a name="how-credits-are-used"></a>Sposób używania środków

Konto rozliczeniowe umowy klienta firmy Microsoft zawiera profile rozliczeniowe, które umożliwiają zarządzanie fakturami i formami płatności. W każdym profilu rozliczeniowym co miesiąc jest generowana faktura, którą opłaca się przy użyciu wybranych form płatności.

Nabyte środki przypisujesz do profilu rozliczeniowego. Podczas generowania faktury dla profilu rozliczeniowego środki są automatycznie stosowane do łącznych opłat w celu obliczenia kwoty, którą trzeba zapłacić. Pozostałą kwotę trzeba zapłacić za pomocą wybranej formy płatności, takiej jak czek/przelew lub karta kredytowa.

## <a name="products-that-arent-covered-by-azure-credits"></a>Produkty nieobjęte środkami na korzystanie z platformy Azure

 Następujące produkty nie są objęte środkami na korzystanie z platformy Azure. Za ich używanie są naliczane opłaty — niezależnie od salda środków:

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop
- Registered User
- Openlogic
- Remote Access Rights XenApp Essentials Registered User
- Ubuntu Advantage
- Visual Studio Enterprise (miesięczny)
- Visual Studio Enterprise (roczny)
- Visual Studio Professional (miesięczny)
- Visual Studio Professional (roczny)
- Produkty z witryny Azure Marketplace
- Plany pomocy technicznej platformy Azure

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdzanie dostępu do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.

Jeśli potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), aby szybko rozwiązać problem.

## <a name="next-steps"></a>Następne kroki

- [Omówienie konta rozliczeniowego umowy klienta firmy Microsoft](billing-mca-overview.md)
- [Omówienie terminów na fakturze za umowę klienta firmy Microsoft](billing-mca-understand-your-invoice.md)

---
title: Jak jest stosowany rabat w wysokości zakupu z góry usługi Azure Databricks
description: Dowiedz się, jak Rabat zakupu z góry usługi Azure Databricks ma zastosowanie do użycia.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: banders
ms.openlocfilehash: 7c1855b587ab1d603e9c6ac24a67b0f50065361f
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827635"
---
# <a name="how-azure-databricks-pre-purchase-discount-is-applied"></a>Jak jest stosowany rabat w wysokości zakupu z góry usługi Azure Databricks

W dowolnym momencie w ciągu okresu zakupu, można użyć wstępnie zakupionych jednostek zatwierdzania usługi Azure Databricks (DBCU). Wszystkie usługi Azure Databricks to wykorzystania odejmuje automatycznie z DBCUs kupowane wcześniej.

W przeciwieństwie do maszyn wirtualnych, wstępnie zakupionych jednostek nie wygasa w systemie godzinowym. Można je w dowolnym momencie w ciągu okresu zakupu. Aby uzyskać rabaty w ramach zakupu z góry, nie trzeba ponownie wdrażać ani przypisać kupowane wcześniej plan do obszarów roboczych usługi Azure Databricks za użycie.

Rabat zakupu z góry ma zastosowanie tylko do usługi Azure Databricks (DBU) jednostki użycia. Inne opłaty, takich jak wystąpienia obliczeniowe, Magazyn i sieć, są rozliczane osobno.

## <a name="pre-purchase-discount-application"></a>Wstępny zakup stosowania rabatów

Zakupu z góry usługi Databricks ma zastosowanie do wszystkich obciążeń usługi Databricks i warstw. Można potraktować zakupu z góry jako pula przedpłacony jednostek zatwierdzania usługi Databricks. Użycie jest odejmowany od puli, niezależnie od tego, czy obciążenie lub warstwy. Użycie jest odejmowany współczynnik następujące:

| **Obciążenie** | **Współczynnik aplikacji jednostek DBU — warstwa standardowa** | **Współczynnik aplikacji jednostek DBU — warstwa Premium** |
| --- | --- | --- |
| Analiza danych | 0.4 | 0.55 |
| Inżynieria danych | 0.15 | 0.30 |
| Inżynieria danych (lekka) | 0,07 | 0.22 |

Na przykład gdy ilość analizy danych — warstwa standardowa jest używane, wstępnie zakupionych jednostek usługi Databricks w zatwierdzenia jest odejmowany przez 0,4 jednostki. Po ilość światła Inżynieria danych — warstwa standardowa jest używany, kupowane wcześniej jednostka Databricks w zatwierdzenia jest odejmowany przez 0,07 jednostki

## <a name="determine-plan-use"></a>Określanie zastosowania planu

Aby ustalić użytkowanie DBCU planu, przejdź do witryny Azure portal > **rezerwacje** i kliknij przycisk zakupionego planu usługi Databricks. Twoje użycie do daty jest wyświetlany za pomocą wszelkie pozostałe jednostki. Aby uzyskać więcej informacji na temat określania rezerwacji, zobacz [Zobacz użycie rezerwacji](billing-reservation-apis.md#see-reservation-usage) artykułu.

## <a name="how-discount-application-shows-in-usage-data"></a>Sposób stosowania rabatów dotyczących pokazuje użycie danych

Gdy rabat zakupu z góry dotyczy użycia usługi Databricks, na żądanie, opłaty są traktowane jako zero w danych użycia. Aby uzyskać więcej informacji na temat rezerwacji kosztów i użycia, zobacz [koszty rezerwacji Pobierz umowy Enterprise Agreement i użycie](billing-understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak zarządzać rezerwacji, zobacz [Zarządzanie zastrzeżeniami Azure](billing-manage-reserved-vm-instance.md).
- Aby dowiedzieć się więcej na temat wstępnie kupując usługi Azure Databricks w celu zaoszczędzenia pieniędzy, zobacz [usługi Azure Databricks zoptymalizować koszty przy użyciu zakupu z góry](billing-prepay-databricks-reserved-capacity.md).
- Aby dowiedzieć się więcej na temat rezerwacji Azure, zobacz następujące artykuły:
  - [Co to jest Azure rezerwacje?](billing-save-compute-costs-reservations.md)
  - [Zarządzanie zastrzeżeniami na platformie Azure](billing-manage-reserved-vm-instance.md)
  - [Opis zastrzeżenia dla subskrypcji przy użyciu stawki](billing-understand-reserved-instance-usage.md)
  - [Opis zastrzeżenia dla Twojej rejestracji Enterprise](billing-understand-reserved-instance-usage-ea.md)

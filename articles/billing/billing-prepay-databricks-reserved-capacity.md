---
title: Optymalizować koszty usługi Azure Databricks przy użyciu zakupu z góry
description: Dowiedz się, jak można przedpłaty za usługi Azure Databricks opłaty za pomocą rezerwowanie pojemności, aby zaoszczędzić pieniądze.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: banders
ms.openlocfilehash: 99eb4de86aa227d558bec54d011a0b1548d27cf0
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811260"
---
# <a name="optimize-azure-databricks-costs-with-a-pre-purchase"></a>Optymalizować koszty usługi Azure Databricks przy użyciu zakupu z góry

Można zapisać w Twojej usłudze Azure Databricks koszt jednostkowy (DBU) w przypadku zakupu z góry usługi Azure Databricks zatwierdzenia jednostki (DBCU) przez rok lub trzy lata. W dowolnym momencie w ciągu okresu zakupu, można użyć DBCUs kupowane wcześniej. W przeciwieństwie do maszyn wirtualnych, wstępnie zakupionych jednostek nie wygasa w systemie godzinowym, a ich używać w dowolnym momencie w ciągu okresu zakupu.

Każde użycie usługi Azure Databricks automatycznie odejmuje z Dbu kupowane wcześniej. Nie trzeba ponownie wdrażać ani przypisać kupowane wcześniej plan do obszarów roboczych usługi Azure Databricks za użycie jednostki DBU uzyskać rabaty w ramach zakupu z góry.

Rabat zakupu z góry ma zastosowanie tylko do użycia jednostek DBU. Inne opłaty, takich jak wystąpienia obliczeniowe, Magazyn i sieć, są rozliczane osobno.

## <a name="determine-the-right-size-to-buy"></a>Rozmiar jest określany na prawo zakupu

Zakupu z góry usługi Databricks ma zastosowanie do wszystkich obciążeń usługi Databricks i warstw. Można potraktować zakupu z góry jako pula przedpłacony jednostek zatwierdzania usługi Databricks. Użycie jest odejmowany od puli, niezależnie od tego, czy obciążenie lub warstwy. Użycie jest odejmowany współczynnik następujące:

| **Obciążenie** | **Współczynnik aplikacji jednostek DBU — warstwa standardowa** | **Współczynnik aplikacji jednostek DBU — warstwa Premium** |
| --- | --- | --- |
| Analiza danych | 0.4 | 0.55 |
| Inżynieria danych | 0.15 | 0.30 |
| Inżynieria danych (lekka) | 0,07 | 0.22 |

Na przykład gdy ilość analizy danych — warstwa standardowa jest używane, wstępnie zakupionych jednostek usługi Databricks w zatwierdzenia jest odejmowany przez 0,4 jednostki.

Przed zakupem, Oblicz łączną ilość jednostek DBU używane na potrzeby różnych obciążeń i warstw. Poprzedni wskaźniki umożliwiają znormalizować do DBCU, a następnie uruchom rzutowaniem całkowite użycie przez następny rok lub trzy lata.

## <a name="purchase-databricks-commit-units"></a>Zakup jednostek zatwierdzania usługi Databricks

Możesz kupić plany usługi Databricks [witryny Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D). Aby kupić rezerwowanie pojemności, konieczne jest posiadanie roli właściciela dla przedsiębiorstw co najmniej jedną subskrypcję.

- Wstępny zakup obecnie jest dostępna tylko dla klientów z umowami Enterprise Agreement.
- Musisz być w roli właściciela dla co najmniej jedną subskrypcją przedsiębiorstwa.
- W przypadku subskrypcji Enterprise **Dodawanie wystąpień zarezerwowanych** musi być włączona w [portalu EA portal](https://ea.azure.com/). Lub, jeśli to ustawienie jest wyłączone, musi być administratorem subskrypcji umowy EA.

**Aby kupić:**

1. Przejdź do witryny [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D).
1. Wybierz subskrypcję. Użyj **subskrypcji** listę, aby wybrać subskrypcję, która jest używana do zapłacenia za rezerwowanie pojemności. Formy płatności dla subskrypcji jest naliczana opłata z góry kosztów rezerwowanie pojemności. Opłaty są odejmowane od salda zobowiązania pieniężnego rejestracji lub rozliczane jako nadwyżkowe użycie.
1. Wybierz zakres. Użyj **zakres** listę, aby wybrać zakres subskrypcji:
    - **Pojedynczy zakres grupy zasobów** — ma zastosowanie rabatu związanego z rezerwacją do dopasowywania zasobów w wybranej grupie zasobów tylko.
    - **Pojedynczy zakres subskrypcji** — ma zastosowanie rabatu związanego z rezerwacją do dopasowywania zasobów w wybranej subskrypcji.
    - **Zakres udostępniony** — ma zastosowanie rabat związany z rezerwacją do dopasowywania zasobów w uprawnione subskrypcje, które znajdują się w kontekstu rozliczeń. W przypadku klientów z umową Enterprise Agreement kontekstu rozliczeń jest rejestracja.
1. Wybierz liczbę jednostek zatwierdzania usługi Azure Databricks do zakupu, a następnie sfinalizować zakup.


![Przykład przedstawiający zakupu usługi Azure Databricks w witrynie Azure portal](./media/billing-prepay-databricks-reserved-capacity/data-bricks-pre-purchase.png)

## <a name="change-scope-and-ownership"></a>Zmiana zakresu i własność

Możesz wprowadzić następujące typy zmian po zakupie rezerwacji:

- Zaktualizować zakresu rezerwacji
- Dostęp oparty na rolach

Nie można dzielenia lub łączenia usługi Databricks zatwierdzenia jednostki zakupu z góry. Aby uzyskać więcej informacji o zarządzaniu rezerwacje, zobacz [Zarządzanie zastrzeżeniami po zakupie](billing-manage-reserved-vm-instance.md).

## <a name="cancellations-and-exchanges"></a>Anulowanie i wymiany

Anuluj i programu exchange nie jest obsługiwana w przypadku planów zakupu z góry usługi Databricks. Wszystkie zakupy dokonywane jest.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat rezerwacji Azure, zobacz następujące artykuły:
  - [Co to jest Azure rezerwacje?](billing-save-compute-costs-reservations.md)
  - [Zrozumienie, jak jest stosowany rabat DBCU zakupu z góry usługi Azure Databricks](billing-reservation-discount-databricks.md)
  - [Opis zastrzeżenia dla Twojej rejestracji Enterprise](billing-understand-reserved-instance-usage-ea.md)

---
title: Optymalizowanie kosztów usługi Azure Databricks dzięki wcześniejszemu zakupowi
description: Dowiedz się, jak można zaoszczędzić pieniądze, opłacając z góry używanie usługi Azure Databricks z wydajnością rezerwową.
services: billing
author: yashesvi
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 0165da8adbb28b97d524f1a07c725716b69277d4
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223635"
---
# <a name="optimize-azure-databricks-costs-with-a-pre-purchase"></a>Optymalizowanie kosztów usługi Azure Databricks dzięki wcześniejszemu zakupowi

Można zaoszczędzić na kosztach jednostek usługi Azure Databricks (DBU) przy wcześniejszym zakupie jednostek zatwierdzeń usługi Azure Databricks (DBCU) na rok lub 3 lata. Zakupionych wcześniej jednostek DBCU można używać w dowolnym czasie w okresie zakupu. W odróżnieniu od maszyn wirtualnych wcześniej zakupione jednostki nie wygasają co godzinę i można ich używać w dowolnym czasie w okresie zakupu.

Każde użycie usługi Azure Databricks powoduje automatyczne odjęcie wcześniej zakupionych jednostek DBU. Nie trzeba ponownie wdrażać ani przypisywać wcześniej zakupionego planu do obszarów roboczych usługi Azure Databricks na potrzeby użycia jednostek DBU, aby uzyskać rabaty za wcześniejszy zakup.

Rabat za wcześniejszy zakup dotyczy tylko użycia jednostek DBU. Pozostałe opłaty, na przykład za obliczenia, magazyn i sieć, są naliczane osobno.

## <a name="determine-the-right-size-to-buy"></a>Określanie odpowiedniego rozmiaru przed zakupem

Wcześniejszy zakup w usłudze Databricks jest stosowany do wszystkich obciążeń i warstw usługi Databricks. Można traktować tę opcję jako pulę opłaconych z góry jednostek zatwierdzeń usługi Databricks. Użycie jest odejmowane od tej puli niezależnie od obciążenia i warstwy. Użycie jest odejmowane zgodnie z poniższym współczynnikiem:

| **Obciążenie** | **Współczynnik naliczania jednostek DBU — warstwa Standardowa** | **Współczynnik naliczania jednostek DBU — warstwa Premium** |
| --- | --- | --- |
| Analiza danych | 0,4 | 0,55 |
| Inżynieria danych | 0,15 | 0,30 |
| Inżynieria danych (lekka) | 0,07 | 0,22 |

Na przykład w przypadku użycia obciążenia Analiza danych w warstwie Standardowa od zakupionych wcześniej jednostek zatwierdzeń usługi Databricks odejmuje się 0,4 jednostki.

Przed zakupem oblicz łączną ilość jednostek DBU używanych w różnych warstwach przez różne obciążenia. Przy użyciu powyższych współczynników znormalizuj tę wartość do jednostek DBCU, a następnie przeprowadź projekcję całkowitego użycia przez kolejny rok lub trzy lata.

## <a name="purchase-databricks-commit-units"></a>Zakup jednostek zatwierdzania usługi Databricks

Plany usługi Databricks można kupić w witrynie [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D). Do zakupu wydajności rezerwowej potrzebna jest rola właściciela co najmniej w jednej subskrypcji Enterprise.

- Aktualnie wcześniejszy zakup jest dostępny tylko dla klientów z umową Enterprise Agreement.
- Wymagana jest rola właściciela co najmniej w jednej subskrypcji Enterprise.
- W przypadku subskrypcji Enterprise w witrynie [EA Portal](https://ea.azure.com/) musi być włączona opcja **Dodaj wystąpienia zarezerwowane**. Jeśli to ustawienie jest wyłączone, wymagane są uprawnienia administratora EA subskrypcji.

**Aby dokonać zakupu:**

1. Przejdź do witryny [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D).
1. Wybierz subskrypcję. Z listy **Subskrypcja** wybierz subskrypcję, w ramach której jest opłacana wydajność rezerwowa. Forma płatności subskrypcji jest obciążana z góry za wydajność rezerwową. Opłaty są odliczane od salda zobowiązania pieniężnego rejestracji lub naliczane jako nadwyżka.
1. Wybierz zakres. Z listy **Zakres** wybierz zakres subskrypcji:
    - **Zakres pojedynczej grupy zasobów** — rabat na rezerwację jest stosowany do odpowiednich zasobów tylko w wybranej grupie zasobów.
    - **Zakres pojedynczej subskrypcji** — rabat na rezerwację jest stosowany do odpowiednich zasobów w wybranej subskrypcji.
    - **Zakres udostępniony** — rabat na rezerwację jest stosowany do odpowiednich zasobów w kwalifikujących się subskrypcjach w ramach kontekstu rozliczeń. W przypadku klientów korzystających z umowy Enterprise Agreement kontekstem rozliczeń jest rejestracja.
1. Wybierz liczbę jednostek zatwierdzania usługi Azure Databricks i sfinalizuj zakup.


![Przykładowy zakup jednostek usługi Azure Databricks w witrynie Azure Portal](./media/billing-prepay-databricks-reserved-capacity/data-bricks-pre-purchase.png)

## <a name="change-scope-and-ownership"></a>Zmiana zakresu i własności

W zakupionej rezerwacji można wprowadzać następujące zmiany:

- Aktualizacja zakresu rezerwacji
- Dostęp oparty na rolach

Wcześniej zakupionej jednostki zatwierdzania usługi Databricks nie można podzielić ani scalić. Aby uzyskać więcej informacji na temat zarządzania rezerwacjami, zobacz [Zarządzanie rezerwacjami po zakupie](billing-manage-reserved-vm-instance.md).

## <a name="cancellations-and-exchanges"></a>Anulowania i wymiany

Opcje anulowania i wymiany nie są obsługiwane w przypadku wcześniej zakupionych planów usługi Databricks. Wszystkie zakupy są ostateczne.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:
  - [Co to są rezerwacje platformy Azure?](billing-save-compute-costs-reservations.md)
  - [Omówienie stosowania rabatu za wcześniejszy zakup jednostek DBCU usługi Azure Databricks](billing-reservation-discount-databricks.md)
  - [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](billing-understand-reserved-instance-usage-ea.md)

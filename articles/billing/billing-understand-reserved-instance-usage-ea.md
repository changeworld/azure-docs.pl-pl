---
title: Informacje na temat użycia zastrzeżeń platformy Azure na potrzeby umów Enterprise Agreement
description: Dowiedz się, jak odczytać informacje o użyciu, aby zrozumieć, w jaki sposób zastosowana jest rezerwacja platformy Azure dla Twojej rejestracji przedsiębiorstwa.
author: bandersmsft
manager: yashar
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 507ad62a917120689bee3f1e293e23c9ab8b0f66
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598097"
---
# <a name="get-enterprise-agreement-reservation-costs-and-usage"></a>Pobierz koszty i użycie Umowa Enterprise rezerwacji

Koszty rezerwacji i dane użycia są dostępne dla Umowa Enterprise klientów w interfejsach API Azure Portal i REST. Ten artykuł ułatwia:

- Pobierz dane zakupu rezerwacji
- Wiedzieć, która subskrypcja, Grupa zasobów lub zasób użyła rezerwacji
- Obciążenia zwrotnego na potrzeby użycia rezerwacji
- Oblicz oszczędności rezerwacji
- Pobierz rezerwację danych użycia
- Amortyzowanie kosztów rezerwacji

Opłaty w witrynie Marketplace są konsolidowane w danych użycia. Opłaty są naliczane za użycie w ramach jednego źródła danych, użycia w witrynie Marketplace i zakupów.

## <a name="reservation-charges-in-azure-usage-data"></a>Opłaty za rezerwację w danych użycia platformy Azure

Dane są podzielone na dwa oddzielne zestawy danych: Koszt _rzeczywisty_ i _amortyzowany koszt_. Różnice między tymi dwoma zestawami danych:

**Koszt rzeczywisty** — zapewnia dane do uzgodnienia z rachunkiem miesięcznym. Te dane zawierają koszty zakupu rezerwacji i szczegóły aplikacji rezerwacji. Za pomocą tych danych możesz wiedzieć, która subskrypcja lub Grupa zasobów lub zasób otrzymały rabat rezerwacji w określonym dniu. EffectivePrice dla użycia, który odbiera rabat rezerwacji, wynosi zero.

**Amortyzowany koszt** — ten zestaw danych jest podobny do rzeczywistego kosztu zestawu danych, z wyjątkiem tego, że wartość EffectivePrice dla użycia, która pobiera rabat rezerwacji, jest proporcjonalnym kosztem rezerwacji (zamiast wartości zero). Pomaga to znać wartość pieniężną użycia rezerwacji w ramach subskrypcji, grupy zasobów lub zasobu, a także może pomóc Ci w rozliczeniu za użycie rezerwacji wewnętrznie. Zestaw danych ma także nieużywane godziny rezerwacji. Zestaw danych nie zawiera rekordów zakupu rezerwacji. 

Porównanie dwóch zestawów danych:

| Data | Rzeczywisty koszt — zestaw danych | Zestawienie danych kosztów amortyzowanych |
| --- | --- | --- |
| Rezerwacja zakupów | Dostępne w tym widoku.<br><br>  Aby uzyskać ten filtr &quot;danych, należy kupić.&quot; <br><br> Zapoznaj się z ReservationID lub zastrzeżeńname, aby dowiedzieć się, której rezerwacji dotyczy opłata.  | Nie dotyczy tego widoku. <br><br> Koszty zakupu nie są podawane w danych amortyzowanych. |
| EffectivePrice | Wartość jest równa zero w przypadku użycia, który pobiera rabat rezerwacji. | Wartość jest naliczana proporcjonalnie do godzinnego kosztu rezerwacji za użycie z rabatem rezerwacji. |
| Nieużywane zastrzeżenie (zawiera liczbę godzin, przez które rezerwacja nie została użyta w ciągu dnia i wartość pieniężną odpadów) | Nie dotyczy w tym widoku. | Dostępne w tym widoku.<br><br> Aby uzyskać te dane, odfiltruj wartość opłaty &quot;=&quot;UnusedReservation.<br><br>  Zapoznaj się z ReservationID lub zastrzeżeńname, aby dowiedzieć się, która rezerwacja była niedostatecznie wykorzystywana. Jest to ilość zastrzeżeń, która została utracona w ciągu dnia.  |
| Cena jednostkowa (cena zasobu z arkusza cen) | Dostępne | Dostępne |

Inne informacje dostępne w danych użycia platformy Azure uległy zmianie:

- Informacje o produkcie i mierniku — platforma Azure nie zastępuje pierwotnie zużytego miernika ReservationId i Reservationname, tak jak wcześniej.
- ReservationId i Reservationname — są one własnymi polami w danych. Wcześniej były dostępne tylko w ramach AdditionalInfo.
- ProductOrderId — identyfikator zamówienia rezerwacji, który został dodany jako własny pole.
- ProductOrderName — Nazwa produktu zakupionej rezerwacji.
- Termin 12 miesięcy lub 36 miesięcy.
- RINormalizationRatio — dostępne w AdditionalInfo. Jest to stosunek, w którym rezerwacja jest stosowana do rekordu użycia. Jeśli na potrzeby rezerwacji włączono elastyczność rozmiaru wystąpienia, może ona mieć zastosowanie do innych rozmiarów. Wartość pokazuje stopień zastosowania rezerwacji do rekordu użycia.

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>Pobieranie danych użycia platformy Azure i zastrzeżeń przy użyciu interfejsu API

Możesz pobrać dane przy użyciu interfejsu API lub pobrać je z Azure Portal.

Możesz wywołać [interfejs API szczegółów użycia](/rest/api/consumption/usagedetails/list) z interfejsem &quot;API w wersji&quot; 2019-04-01-Preview, aby uzyskać nowe dane. Aby uzyskać szczegółowe informacje na temat terminologii, zobacz [warunki użytkowania](billing-understand-your-usage.md). Obiekt wywołujący powinien być administratorem przedsiębiorstwa dla umowy Enterprise Agreement przy użyciu [portalu EA](https://ea.azure.com). Administratorzy przedsiębiorstwa tylko do odczytu mogą również uzyskać dane.

Dane nie są dostępne w usłudze [raportowanie interfejsów API dla klientów korporacyjnych — szczegóły użycia](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail).

Oto przykład wywołania interfejsu API:

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-04-01-preview&amp;$filter={filter}
```

Aby uzyskać więcej informacji na temat {enrollmentId} i {billingPeriodId}, zobacz artykuł dotyczący interfejsu API [listy szczegóły użycia](https://docs.microsoft.com/rest/api/consumption/usagedetails/list) .

Informacje przedstawione w poniższej tabeli dotyczące metryk i filtru mogą pomóc w rozwiązywaniu typowych problemów z rezerwacją.

| **Typ danych interfejsu API** | Akcja wywołania interfejsu API |
| --- | --- |
| **Wszystkie opłaty (użycie i zakupy)** | Zastąp wartość {Metric} za pomocą ActualCost |
| **Użycie, które uzyskało rabat rezerwacji** | Zastąp wartość {Metric} za pomocą ActualCost<br><br>Zastąp ciąg {Filter} z: Properties/reservationId% 20ne% 20 |
| **Użycie, które nie pobierze rabatu rezerwacji** | Zastąp wartość {Metric} za pomocą ActualCost<br><br>Zastąp ciąg {Filter} z: Properties/reservationId% 20eq% 20 |
| **Opłaty amortyzowane (użycie i zakupy)** | Zastąp wartość {Metric} za pomocą AmortizedCost |
| **Nieużywany raport rezerwacji** | Zastąp wartość {Metric} za pomocą AmortizedCost<br><br>Zastąp element {Filter} z: Properties/Opłatatype% 20eq% 20 ' UnusedReservation ' |
| **Rezerwacja zakupów** | Zastąp wartość {Metric} za pomocą ActualCost<br><br>Zastąp element {Filter} z: Properties/Opłatatype% 20eq% 20 "Purchase"  |
| **Zwroty** | Zastąp wartość {Metric} za pomocą ActualCost<br><br>Zastąp element {Filter} z: Properties/Opłatatype% 20eq% 20 "refund" |

## <a name="download-the-usage-csv-file-with-new-data"></a>Pobierz plik CSV użycia z nowymi danymi

Jeśli jesteś administratorem EA, możesz pobrać plik CSV zawierający nowe dane użycia z Azure Portal. Te dane nie są dostępne w [portalu EA](https://ea.azure.com).

W Azure Portal przejdź do [kosztów Cost Management + rozliczenia](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts).

1. Wybierz konto rozliczeń.
2. Kliknij pozycje **użycie i opłaty**.
3. Kliknij przycisk **Pobierz**.  
![Przykład pokazujący, gdzie pobrać plik danych użycia woluminów CSV w Azure Portal](./media/billing-understand-reserved-instance-usage-ea/portal-download-csv.png)
4. W obszarze **Pobierz użycie i opłaty** , w obszarze **szczegóły użycia w wersji 2** wybierz pozycję **wszystkie opłaty (użycie i zakupy)** , a następnie kliknij pozycję Pobierz. Powtórz tę czynność dla **amortyzowanych opłat (użycie i zakupy)** .

Pobrane pliki CSV zawierają rzeczywiste koszty i naniesione koszty.

## <a name="common-cost-and-usage-tasks"></a>Typowe zadania związane z kosztami i użyciem

Poniższe sekcje to typowe zadania, których większość osób używa do wyświetlania ich kosztów rezerwacji i danych użycia.

### <a name="get-reservation-purchase-costs"></a>Pobieranie kosztów zakupu rezerwacji

Koszty zakupu rezerwacji są dostępne w rzeczywistych kosztach. Filtr dla _opłaty = zakup_. Zapoznaj się z ProductOrderID, aby określić kolejność rezerwacji zakupu.

### <a name="get-underutilized-reservation-quantity-and-costs"></a>Uzyskaj nieużywaną ilość i koszty rezerwacji

Pobierz amortyzowane dane kosztów i _Filtr dla elementu_ costtype _= UnusedReservation_. Otrzymujesz dzienną nieużywaną ilość rezerwacji i koszt. Dane dla rezerwacji lub zamówienia rezerwacji można filtrować odpowiednio przy użyciu pól _ReservationId_ i _ProductOrderId_ . W przypadku użycia rezerwacji na 100% rekord ma ilość 0.

### <a name="amortize-reservation-costs"></a>Amortyzowanie kosztów rezerwacji

Pobierz amortyzowane dane kosztów i odfiltruj kolejność rezerwacji przy użyciu _ProductOrderID_ , aby uzyskać dzienne koszty związane z rezerwacją.

### <a name="chargeback-for-a-reservation"></a>Obciążenia zwrotnego dla rezerwacji

Możesz obciążenia zwrotnego użycie rezerwacji innym organizacjom według subskrypcji, grup zasobów lub tagów. Amortyzowane dane kosztów zapewniają wartość pieniężną wykorzystania rezerwacji dla następujących typów danych:

- Zasoby (na przykład maszyna wirtualna)
- Resource group
- `Tags`
- Subscription

### <a name="get-the-blended-rate-for-chargeback"></a>Pobierz szybkość mieszania dla obciążenia zwrotnego

Aby określić szybkość mieszania, należy uzyskać dane o amortyzowanych kosztach i agregować łączny koszt. W przypadku maszyn wirtualnych można użyć informacji o mierniku lub wartości z AdditionalInfo JSON. Podziel łączny koszt według ilości użytej do uzyskania stawki mieszanej.

### <a name="audit-optimum-reservation-use-for-instance-size-flexibility"></a>Inspekcja optymalnego użycia rezerwacji na potrzeby elastyczności rozmiaru wystąpienia

Wiele z _RINormalizationRatio_z AdditionalInfo. Wyniki wskazują, ile godzin użycia rezerwacji zostało zastosowanych do rekordu użycia.

### <a name="determine-reservation-savings"></a>Określanie oszczędności rezerwacji

Pobierz dane z amortyzowanych kosztów i przefiltruj dane dla wystąpienia zarezerwowanego. Następnie

1. Uzyskaj szacowane koszty płatność zgodnie z rzeczywistym użyciem. Pomnóż wartość _CenaJednostkowa_ z wartościami _ilościowymi_ , aby uzyskać szacowane koszty płatność zgodnie z rzeczywistym użyciem, jeśli rabat rezerwacji nie dotyczy użycia.
2. Pobierz koszty rezerwacji. Należy zsumować wartości _kosztów_ , aby uzyskać wartość pieniężną zapłaconego wystąpienia zarezerwowanego. Obejmuje on używane i niewykorzystane koszty rezerwacji.
3. Odejmij koszty rezerwacji od szacowanych kosztów płatność zgodnie z rzeczywistym użyciem, aby uzyskać szacowane oszczędności.

## <a name="reservation-purchases-and-amortization-in-cost-analysis"></a>Rezerwacja zakupów i amortyzacji w analizie kosztów

Koszty rezerwacji są dostępne w [analizie kosztów](https://aka.ms/costanalysis). Domyślnie analiza kosztów pokazuje **rzeczywisty koszt**, czyli sposób wyświetlania kosztów na rachunku. Aby wyświetlić zakupy rezerwacji podzielone na przerwane i skojarzone z zasobami, w których wykorzystano korzyść, przełącz się na **amortyzowany koszt**:

![Przykład pokazujący, gdzie wybierać amortyzowany koszt w analizie kosztów](./media/billing-understand-reserved-instance-usage-ea/portal-cost-analysis-amortized-view.png)

Grupuj według typu opłaty, aby wyświetlić podział użycia, zakupów i refundacji; lub w ramach rezerwacji na podział kosztów rezerwacji i na żądanie. Pamiętaj tylko koszty rezerwacji, które zobaczysz, gdy zaczniesz korzystać z rzeczywistych kosztów, ale koszt zostanie przydzielony do poszczególnych zasobów, które benfit podczas naliczanego kosztu. Zobaczysz również nowy typ kosztu **UnusedReservation** podczas naliczania opłat.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat Azure Reservations, zobacz następujące artykuły:

- [Co to są Azure Reservations?](billing-save-compute-costs-reservations.md)
- [Prepay for Virtual Machines with Azure Reserved VM Instances (Opłacanie maszyn wirtualnych z góry przy użyciu usługi Azure Reserved VM Instances)](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Prepay for SQL Database compute resources with Azure SQL Database reserved capacity (Opłacanie zasobów obliczeniowych usługi SQL Database z góry przy użyciu zarezerwowanej pojemności usługi Azure SQL Database)](../sql-database/sql-database-reserved-capacity.md)
- [Zarządzanie usługą Azure Reservations](billing-manage-reserved-vm-instance.md)
- [Zapoznaj się z zastosowaniem rabatu rezerwacji](billing-understand-vm-reservation-charges.md)
- [Informacje na temat użycia rezerwacji dla subskrypcji z opcją płatność zgodnie z rzeczywistym użyciem](billing-understand-reserved-instance-usage.md)
- [Koszty oprogramowania systemu Windows, które nie są objęte rezerwacjami](billing-reserved-instance-windows-software-costs.md)

---
title: Opis zastrzeżenia platformy Azure dla umów Enterprise Agreement
description: Dowiedz się, jak odczytać Twojego użycia, aby zrozumieć, jak zastosowano Azure zastrzeżenie dla Twojej rejestracji Enterprise.
author: bandersmsft
manager: yashar
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/07/2019
ms.author: banders
ms.openlocfilehash: b2452580eaecc0ab922f8e7db48676f70831a8ca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66126847"
---
# <a name="get-enterprise-agreement-reservation-costs-and-usage"></a>Pobieranie kosztów rezerwacji umowy Enterprise Agreement i danych użycia

Koszty rezerwacji i dane użycia są dostępne dla klientów z umowami Enterprise Agreement w witrynie Azure portal i interfejsy API REST. Ten artykuł pomoże Ci:

- Pobieranie danych zakupu rezerwacji
- Pobieranie danych niepełnego wykorzystania rezerwacji
- Agencyjnej kosztów rezerwacji
- Obciążenie zwrotne wykorzystania rezerwacji
- Oblicz oszczędności rezerwacji

Opłatach w portalu Marketplace i dalszych są skonsolidowane w danych użycia. Możesz wyświetlić opłat za pierwsze użycie innych firm, użycie witryny marketplace i zakupów od pojedynczego źródła danych.

## <a name="reservation-charges-in-azure-usage-data"></a>Opłaty rezerwacji w danych użycia platformy Azure

Dane są dzielone na dwa oddzielne zestawy danych: _Rzeczywisty koszt_ i _amortyzowanego kosztu_. Jak różnią się te dwa zestawy danych:

**Rzeczywisty koszt** — dostarcza dane do uzgodnienia z na miesięcznym rachunku. Te dane ma zakup kosztów rezerwacji. Zawiera zero EffectivePrice do użycia, który otrzymał rabat związany z rezerwacją.

**Amortyzowanego kosztu** — zasób EffectiveCost, który pobiera rabat związany z rezerwacją jest proporcjonalny koszt wystąpienia zarezerwowanego. Ma również zestaw danych kosztów nieużywanych rezerwacji. Suma kosztów rezerwacji i nieużywanych rezerwacji zawiera dzienne zamortyzowany koszt rezerwacji.

Porównanie dwóch zestawów danych:

| Dane | Zestaw danych w usłudze rzeczywisty koszt | Zestaw danych w usłudze amortyzowany koszt |
| --- | --- | --- |
| Zakup rezerwacji | Dostępne w tym widoku.<br><br>  Aby uzyskać ten filtr danych na temat ChargeType = &quot;zakupu&quot;. <br><br> Zajrzyj do ReservationID lub ReservationName, aby dowiedzieć się które rezerwacji, Opłata dotyczy.  | Nie ma zastosowania do tego widoku. <br><br> Koszty zakupu nie są zawarte w amortyzowanym danych. |
| effectivePrice | Ta wartość wynosi zero, użycia, który pobiera rabat związany z rezerwacją. | Wartość jest naliczana proporcjonalnie do liczby godzin kosztów rezerwacji do użycia, który ma rabat związany z rezerwacją. |
| Nieużywanych rezerwacji (zapewnia liczbę godzin, przez jaką, rezerwacji nie był używany w ciągu dnia i wartości pieniężnej odpadów) | Nie ma zastosowania w tym widoku. | Dostępne w tym widoku.<br><br> Aby uzyskać te dane, odfiltruj ChargeType = &quot;UnusedReservation&quot;.<br><br>  Zajrzyj do ReservationID lub ReservationName, aby dowiedzieć się, które rezerwacji został wykorzystany. Jest to, ile rezerwacji został zmarnowany w w ciągu dnia.  |
| Cena jednostkowa (cena za zasób z Arkusz cen) | Dostępne | Dostępne |

Zmienił się inne informacje, które są dostępne w danych użycia platformy Azure:

- Produktu i informacje licznika - Azure nie zastąpić pierwotnie użyte miernika ReservationId i ReservationName, tak jak wcześniej.
- ReservationId i ReservationName — są one własnych pól danych. Wcześniej wyglądał on dostępne tylko w ramach AdditionalInfo.
- ProductOrderId — identyfikator zamówienia rezerwacji, dodany jako odpowiednie pole.
- ProductOrderName — nazwę produktu dla zakupionej rezerwacji.
- Termin - 12 miesięcy lub 36 miesięcy.
- RINormalizationRatio — dostępne w obszarze AdditionalInfo. Jest to stosunek, gdzie Rezerwacja ma zostać zastosowana do rekordu użycia. Jeśli elastyczność rozmiar wystąpienia jest włączona na dla rezerwacji, a następnie stosować się do innych rozmiarów. Wartość zawiera stosunek, czy rezerwacja została zastosowana do rekordu użycia.

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>Pobieranie danych użycia zużycia i rezerwacji platformy Azure przy użyciu interfejsu API

Można uzyskać danych za pomocą interfejsu API lub ją pobrać z witryny Azure portal.

Należy wywołać [interfejs API użycia szczegóły](/rest/api/consumption/usagedetails/list) z wersją interfejsu API &quot;2019-04-01-preview&quot; można pobrać nowych danych. Aby uzyskać szczegółowe informacje dotyczące terminologii, zobacz [warunki korzystania](billing-understand-your-usage.md). Obiekt wywołujący powinien być administratorem przedsiębiorstwa przy użyciu umowy enterprise [portalu EA portal](https://ea.azure.com). Administratorzy przedsiębiorstwa tylko do odczytu można również uzyskać dane.

Dane nie są dostępne w [interfejsy API raportowania usługi dla klientów korporacyjnych — szczegóły użycia](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail).

Oto przykład wywołanie interfejsu API:

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-04-01-preview&amp;$filter={filter}
```

Aby uzyskać więcej informacji na temat {enrollmentId} i {billingPeriodId}, zobacz [szczegóły użycia — lista](https://docs.microsoft.com/rest/api/consumption/usagedetails/list) artykułu interfejsu API.

Informacje w poniższej tabeli przedstawiono metryki i filtr może pomóc w rozwiązywaniu typowych problemów z rezerwacji.

| **Typ danych interfejsu API** | Akcję do wywołania interfejsu API |
| --- | --- |
| **Wszystkie opłaty (użycia i zakupów)** | Zamień na ActualCost {Metryka} |
| **Użycie, które stało się rabat związany z rezerwacją** | Zamień na ActualCost {Metryka}<br><br>Zamień {filter}: properties/reservationId%20ne%20 |
| **Użycie, które nie dotarł rabat związany z rezerwacją** | Zamień na ActualCost {Metryka}<br><br>Zamień {filter}: properties/reservationId%20eq%20 |
| **Amortyzowany opłaty (użycia i zakupów)** | Zamień na AmortizedCost {Metryka} |
| **Raport nieużywanych rezerwacji** | Zamień na AmortizedCost {Metryka}<br><br>Zamień {filter}: properties/ChargeType%20eq%20'UnusedReservation " |
| **Zakup rezerwacji** | Zamień na ActualCost {Metryka}<br><br>Zamień {filter}: properties/ChargeType%20eq%20'Purchase "  |
| **Zwroty** | Zamień na ActualCost {Metryka}<br><br>Zamień {filter}: properties/ChargeType%20eq%20'Refund " |

## <a name="download-the-usage-csv-file-with-new-data"></a>Pobierz plik CSV użycia za pomocą nowych danych

Jeśli jesteś administratorem umowy EA, możesz pobrać plik CSV, który zawiera nowe dane dotyczące użycia z witryny Azure portal. Te dane nie jest dostępna z [portalu EA portal](https://ea.azure.com).

W witrynie Azure portal przejdź do [Zarządzanie kosztami i rozliczenia](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts).

1. Wybierz konto rozliczeniowe.
2. Kliknij przycisk **użycie i opłaty za**.
3. Kliknij przycisk **Pobierz**.  
![Przykład przedstawiający miejsce pobrać plik CSV użycia danych w witrynie Azure portal](./media/billing-understand-reserved-instance-usage-ea/portal-download-csv.png)
4. W **Pobierz dane użycia i opłat** w obszarze **szczegółów użycia w wersji 2** , wybierz opcję **wszystkie opłaty (użycia i zakupów)** , a następnie kliknij przycisk Pobierz. Powtórz tę procedurę dla **amortyzowanego opłaty (użycia i zakupów)** .

Pliki CSV, które możesz pobrać zawierają faktyczne koszty i zamortyzowany koszt.

## <a name="common-cost-and-usage-tasks"></a>Typowe zadania dotyczące kosztów i użycia

Poniższe sekcje zawierają typowych zadań najczęściej używane do wyświetlania danych kosztów i użycia ich rezerwacji.

### <a name="get-reservation-purchase-costs"></a>Pobierz koszty zakupu rezerwacji

Koszty zakupu rezerwacji są dostępne w danych rzeczywistego kosztu. Filtruj _ChargeType = zakupu_. Zapoznaj się z ProductOrderID, aby określić, które zamówienie rezerwacji, jest zakup.

### <a name="get-underutilized-reservation-quantity-and-costs"></a>Ilość rezerwacji niedostatecznie używanych i kosztów

Pobieranie danych amortyzowanego kosztu i je filtrować _ChargeType_ _= UnusedReservation_. Otrzymujesz dzienny ilość nieużywanych rezerwacji i kosztów. Można filtrować dane dla rezerwacji lub przy użyciu zamówienia rezerwacji _ReservationId_ i _ProductOrderId_ pola, odpowiednio. Jeśli rezerwacja był wykorzystywany w 100%, rekord zawiera ilość 0.

### <a name="amortize-reservation-costs"></a>Agencyjnej kosztów rezerwacji

Pobieranie danych amortyzowanego kosztu i filtr dla zamówienia rezerwacji przy użyciu _ProductOrderID_ można uzyskać dziennych kosztów zamortyzowany rezerwacji.

### <a name="chargeback-for-a-reservation"></a>Obciążenie zwrotne dla rezerwacji

Można używać rezerwacji obciążeń zwrotnych do innych organizacji subskrypcji, grupy zasobów lub tagów. Dane zamortyzowany koszt zawiera wartość pieniężną wykorzystania rezerwacji w następujących typów danych:

- Zasoby (na przykład maszyny wirtualnej)
- Grupa zasobów
- `Tags`
- Subskrypcja

### <a name="get-the-blended-rate-for-chargeback"></a>Pobierz mieszanego szybkość do obsługi obciążenia zwrotnego

Aby określić współczynnik mieszany, danych zamortyzowany koszt i agregowanie łączny koszt. W przypadku maszyn wirtualnych możesz użyć MeterName lub ServiceType informacje z danych AdditionalInfo JSON. Podziel łącznego kosztu według ilości używane do uzyskania mieszanego stawki.

### <a name="audit-optimum-reservation-use-for-instance-size-flexibility"></a>Optymalne rezerwacji inspekcji na przykład użyj elastyczność rozmiaru

Wiele ilość o _RINormalizationRatio_, z AdditionalInfo. Wyniki wskazują, ile godzin użytkowania rezerwacja została zastosowana do rekordu użycia.

### <a name="determine-reservation-savings"></a>Określić oszczędności rezerwacji

Pobierz dane kosztów Amortized i filtrować dane dotyczące wystąpień zarezerwowanych. Następnie:

1. Uzyskaj szacowane koszty zgodnie z rzeczywistym użyciem. Pomnóż _UnitPrice_ wartością _ilość_ wartości, aby uzyskać szacowane koszty zgodnie z rzeczywistym użyciem, jeśli nie można zastosować rabat związany z rezerwacją do użycia.
2. Uzyskaj kosztów rezerwacji. Suma _koszt_ wartości, które można pobrać wartości pieniężnej zapłacone za wystąpienie zarezerwowane. Obejmuje to koszty używane i nieużywanych rezerwacji.
3. Odejmij kosztów rezerwacji z szacowane koszty zgodnie z rzeczywistym użyciem, aby uzyskać Szacowane oszczędności.

## <a name="reservation-purchases-and-amortization-in-azure-cost-analysis"></a>Zakup rezerwacji i amortyzacji analizy kosztów platformy Azure

Koszt wystąpienia zarezerwowanego jest dostępna w [tryb podglądu analizy kosztów platformy Azure](https://preview.portal.azure.com/?feature.canmodifystamps=true&amp;microsoft_azure_costmanagement=stage2&amp;Microsoft_Azure_CostManagement_arm_canary=true&amp;Microsoft_Azure_CostManagement_apiversion=2019-04-01-preview&amp;Microsoft_Azure_CostManagement_amortizedCost=true#blade/Microsoft_Azure_CostManagement/Menu/costanalysis). Domyślnie wyświetlanie danych kosztów jest rzeczywisty koszt. Możesz przełączyć się do zamortyzowany koszt. Oto przykład.

![Przykład przedstawiający miejsce wybrać zamortyzowany koszt analizy kosztów](./media/billing-understand-reserved-instance-usage-ea/portal-cost-analysis-amortized-view.png)

Zastosuj filtry, aby wyświetlić opłat według typu rezerwacji lub jest rozładowany. Grupuj według Nazwa zastrzeżenia, aby zobaczyć koszty według rezerwacji.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat rezerwacji Azure, zobacz następujące artykuły:

- [Co to jest Azure rezerwacje?](billing-save-compute-costs-reservations.md)
- [Prepay for Virtual Machines with Azure Reserved VM Instances (Opłacanie maszyn wirtualnych z góry przy użyciu usługi Azure Reserved VM Instances)](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Prepay for SQL Database compute resources with Azure SQL Database reserved capacity (Opłacanie zasobów obliczeniowych usługi SQL Database z góry przy użyciu zarezerwowanej pojemności usługi Azure SQL Database)](../sql-database/sql-database-reserved-capacity.md)
- [Zarządzanie usługą Azure Reservations](billing-manage-reserved-vm-instance.md)
- [Zrozumienie, jak stosowany jest rabat związany z rezerwacją](billing-understand-vm-reservation-charges.md)
- [Opis zastrzeżenia dla Twojej subskrypcji zgodnie z rzeczywistym użyciem](billing-understand-reserved-instance-usage.md)
- [Koszty oprogramowania Windows nie jest dołączony do rezerwacji](billing-reserved-instance-windows-software-costs.md)

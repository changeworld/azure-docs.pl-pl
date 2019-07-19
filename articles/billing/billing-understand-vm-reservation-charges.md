---
title: Opis rabatu Azure Reserved VM Instancesowego | Microsoft Docs
description: Dowiedz się, jak rabat wystąpienia zarezerwowanych maszyn wirtualnych platformy Azure jest stosowany do uruchamiania maszyn wirtualnych.
author: yashesvi
manager: yashar
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2019
ms.author: banders
ms.openlocfilehash: 191160035f516d818d5537c5c47f9604998c46f7
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849991"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>Jak obowiązuje rabat w ramach rezerwacji platformy Azure dla maszyn wirtualnych

Po zakupie wystąpienia zarezerwowanego maszyny wirtualnej platformy Azure rabat rezerwacji jest automatycznie stosowany do maszyn wirtualnych, które pasują do atrybutów i ilości rezerwacji. Rezerwacja obejmuje koszty obliczeniowe maszyn wirtualnych.

Rabat związany z rezerwacją dotyczy podstawowych maszyn wirtualnych zakupionych w portalu Azure Marketplace.

Aby uzyskać SQL Database zarezerwowaną pojemność, zobacz [Opis rabatu zarezerwowanych na platformie Azure](billing-understand-reservation-charges.md).

W poniższej tabeli przedstawiono koszty związane z maszyną wirtualną po zakupie wystąpienia zarezerwowanych maszyn wirtualnych. We wszystkich przypadkach opłata jest naliczana za magazyn i sieć według normalnych stawek.

| Typ maszyny wirtualnej  | Naliczanie opłat za pomocą wystąpienia zastrzeżonej maszyny wirtualnej |
|-----------------------|--------------------------------------------|
|Maszyny wirtualne z systemem Linux bez dodatkowego oprogramowania | Rezerwacja obejmuje koszty związane z infrastrukturą maszyn wirtualnych.|
|Maszyny wirtualne z systemem Linux z opłatami za oprogramowanie (na przykład Red Hat) | Rezerwacja obejmuje koszty związane z infrastrukturą. Opłata jest naliczana za dodatkowe oprogramowanie.|
|Maszyny wirtualne z systemem Windows bez dodatkowego oprogramowania |Rezerwacja obejmuje koszty związane z infrastrukturą. Opłata jest naliczana za oprogramowanie systemu Windows.|
|Maszyny wirtualne z systemem Windows z dodatkowym oprogramowaniem (na przykład SQL Server) | Rezerwacja obejmuje koszty związane z infrastrukturą. Opłata jest naliczana za oprogramowanie systemu Windows i dodatkowe oprogramowanie.|
|Maszyny wirtualne z systemem Windows z [korzyść użycia hybrydowego platformy Azure](../virtual-machines/windows/hybrid-use-benefit-licensing.md) | Rezerwacja obejmuje koszty związane z infrastrukturą. Koszty oprogramowania systemu Windows są objęte Korzyść użycia hybrydowego platformy Azure. Każde dodatkowe oprogramowanie jest obciążane osobno.|

## <a name="how-reservation-discount-is-applied"></a>Jak jest naliczany rabat rezerwacji

Rabat rezerwacji to "*Użyj go lub*". Jeśli więc nie masz pasujących zasobów przez żadną godzinę, utracisz ilość rezerwacji dla tej godziny. Nie można przenieść nieużywanych godzin zarezerwowanych do przodu.

Po wyłączeniu zasobu rabat rezerwacji automatycznie stosuje się do innego pasującego zasobu w określonym zakresie. Jeśli w określonym zakresie nie znaleziono pasujących zasobów, zarezerwowanych godzin zostaną *utracone*.

## <a name="reservation-discount-for-non-windows-vms"></a>Rabat rezerwacji dla maszyn wirtualnych z systemem innym niż Windows

 Rabat w ramach rezerwacji platformy Azure jest stosowany do uruchamiania wystąpień maszyn wirtualnych co godzinę. Zakupione rezerwacje są dopasowywane do użycia emitowanego przez uruchomione maszyny wirtualne w celu zastosowania rabatu rezerwacji. W przypadku maszyn wirtualnych, które mogą nie działać w pełnej godzinie, rezerwacja zostanie wypełniona z innych maszyn wirtualnych, które nie używają rezerwacji, w tym współbieżnie uruchomionych maszyn wirtualnych. Na koniec godziny aplikacja rezerwacji dla maszyn wirtualnych w godzinie jest zablokowana. W przypadku, gdy maszyna wirtualna nie jest uruchamiana dla godzin lub współbieżnych maszyn wirtualnych w ciągu godziny, nie wypełniaj godziny rezerwacji, rezerwacja jest bezterminowo wykorzystywana przez daną godzinę. Poniższy wykres ilustruje zastosowanie rezerwacji do rozliczanego użycia maszyn wirtualnych. Ilustracja jest oparta na jednym zakupie rezerwacji i dwóch zgodnych wystąpieniach maszyn wirtualnych.

![Zrzut ekranu dotyczący jednej zastosowanej rezerwacji i dwóch zgodnych wystąpień maszyn wirtualnych](./media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1. Każde użycie powyżej wiersza rezerwacji będzie obciążane opłatami według zwykłych stawek płatność zgodnie z rzeczywistym użyciem. Nie jest naliczana opłata za żadne użycie poniżej wiersza rezerwacji, ponieważ została już zapłacona jako część zakupu rezerwacji.
2. W godzinie 1 działa wystąpienie 1 przez 0,75 godzin i wystąpienie 2 działa przez 0,5 godzin. Całkowite użycie dla godziny 1 to 1,25 godzin. Opłaty są naliczone według stawek płatność zgodnie z rzeczywistym użyciem przez pozostałe 0,25 godzin.
3. W przypadku godzin 2 i 3, oba wystąpienia były uruchamiane przez 1 godzinę każdego. Jedno wystąpienie jest objęte rezerwacją, a druga opłata jest naliczana według stawek płatność zgodnie z rzeczywistym użyciem.
4. Za godzinę 4, wystąpienie 1 jest uruchamiane przez 0,5 godzin i wystąpienie 2 jest uruchamiane przez 1 godzinę. Wystąpienie 1 jest w pełni objęte rezerwacji i podano 0,5 godzin wystąpienia 2. Opłata jest naliczana według stawki płatność zgodnie z rzeczywistym użyciem przez pozostałe 0,5 godzin.

Aby zrozumieć i wyświetlić aplikację Azure Reservations w raportach dotyczących użycia rozliczeń, zobacz [Opis użycia zastrzeżenia](billing-understand-reserved-instance-usage-ea.md).

## <a name="reservation-discount-for-windows-vms"></a>Rabat rezerwacji dla maszyn wirtualnych z systemem Windows

W przypadku uruchamiania wystąpień maszyn wirtualnych z systemem Windows rezerwacja jest stosowana w celu pokrycia kosztów infrastruktury. Zastosowanie rezerwacji do kosztów infrastruktury maszyny wirtualnej dla maszyn wirtualnych z systemem Windows jest takie samo jak w przypadku maszyn wirtualnych z systemem innym niż Windows. Opłata jest naliczana osobno dla oprogramowania systemu Windows na podstawie vCPU. Zobacz [koszty oprogramowania systemu Windows z rezerwacjami](billing-reserved-Instance-windows-software-costs.md). Koszt licencjonowania systemu Windows można obsłużyć [korzyść użycia hybrydowego platformy Azure dla systemu Windows Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

## <a name="discount-can-apply-to-different-sizes"></a>Rabat może dotyczyć różnych rozmiarów

W przypadku zakupu wystąpienia zarezerwowanego maszyny wirtualnej w przypadku wybrania opcji **Optymalizacja** **rozmiaru wystąpienia**, pokrycie rabatu zależy od wybranego rozmiaru maszyny wirtualnej. Rezerwacja może dotyczyć rozmiarów maszyn wirtualnych w tej samej grupie serii rozmiarów. Aby uzyskać więcej informacji, zobacz [elastyczność rozmiaru maszyny wirtualnej z wystąpieniami zarezerwowanych maszyn wirtualnych](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

## <a name="discount-applies-to-matching-servicetype-only"></a>Rabat dotyczy tylko pasujących ServiceType

Rabat związany z rezerwacją dotyczy tylko użycia maszyny wirtualnej, `ServiceType` gdzie wartość `AdditionalInfo` w jest równa zakupionej rezerwacji. Aplikacja rabatu rezerwacji ignoruje licznik używany dla maszyn wirtualnych i tylko `ServiceType`oblicza. Informacje o typie usług, dla których zakupiono maszynę wirtualną. Możesz wymienić rezerwację maszyny wirtualnej magazynu innej niż Premium dla rezerwacji magazynu w warstwie Premium lub odwrotnie.

## <a name="services-that-get-vm-reservation-discounts"></a>Usługi pobierające rabaty rezerwacji maszyn wirtualnych

Rezerwacje maszyn wirtualnych mogą dotyczyć użycia maszyn wirtualnych emitowanych z wielu usług — nie tylko dla wdrożeń maszyn wirtualnych. Zasoby pobierające rabaty rezerwacji zmieniają się w zależności od ustawienia elastyczności rozmiaru wystąpienia.

### <a name="instance-size-flexibility-setting"></a>Ustawienie elastyczności rozmiaru wystąpienia

Ustawienie elastyczność rozmiaru wystąpienia określa, które usługi pobierają rabaty wystąpień zarezerwowanych.

Niezależnie od tego, czy to ustawienie jest włączone, czy wyłączone, rabaty rezerwacji są `Microsoft.Compute`automatycznie stosowane do wszystkich pasujących użycia maszyn wirtualnych, gdy *ConsumedService* . Sprawdź dane użycia dla wartości *ConsumedService* . Oto niektóre przykłady:

- Maszyny wirtualne
- Zestawy skalowania maszyn wirtualnych
- Usługa kontenera
- Wdrożenia Azure Batch (w trybie subskrypcji użytkownika)
- Azure Kubernetes Service (AKS)
- Service Fabric

Gdy to ustawienie jest włączone, rabaty rezerwacji automatycznie stosują się do dopasowywania użycia maszyn wirtualnych, gdy *ConsumedService* jest dowolnym z następujących elementów:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

Sprawdź wartość *ConsumedService* w danych użycia, aby określić, czy użycie kwalifikuje się do rabatu rezerwacji.

Aby uzyskać więcej informacji o elastyczności rozmiaru wystąpienia, zobacz [elastyczność rozmiaru maszyny wirtualnej z wystąpieniami zarezerwowanymi maszyn wirtualnych](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).


## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie pomocy technicznej](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat Azure Reservations, zobacz następujące artykuły:

- [Co to są rezerwacje dla systemu Azure?](billing-save-compute-costs-reservations.md)
- [Prepay for Virtual Machines with Azure Reserved VM Instances (Opłacanie maszyn wirtualnych z góry przy użyciu usługi Azure Reserved VM Instances)](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Prepay for SQL Database compute resources with Azure SQL Database reserved capacity (Opłacanie zasobów obliczeniowych usługi SQL Database z góry przy użyciu zarezerwowanej pojemności usługi Azure SQL Database)](../sql-database/sql-database-reserved-capacity.md)
- [Zarządzanie rezerwacjami dla platformy Azure](billing-manage-reserved-vm-instance.md)
- [Informacje na temat użycia rezerwacji dla subskrypcji z opcją płatność zgodnie z rzeczywistym użyciem](billing-understand-reserved-instance-usage.md)
- [Informacje na temat użycia rezerwacji na potrzeby rejestracji w przedsiębiorstwie](billing-understand-reserved-instance-usage-ea.md)
- [Informacje o użyciu rezerwacji dla subskrypcji CSP](/partner-center/azure-reservations)
- [Koszty oprogramowania systemu Windows, które nie są objęte rezerwacjami](billing-reserved-instance-windows-software-costs.md)

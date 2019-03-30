---
title: Zrozumienie discount Azure Reserved VM Instances | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rezerwacji wystąpienia maszyny Wirtualnej platformy Azure, rabat jest stosowany do uruchomionych maszyn wirtualnych.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: banders
ms.openlocfilehash: c8994041d525186dd55bb4f4475f7e68036454f6
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58649833"
---
# <a name="understand-how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>Zrozumienie, jak rabat związany z rezerwacją platformy Azure są stosowane do maszyn wirtualnych

Po kupujesz rezerwacji wystąpienia maszyny wirtualnej platformy Azure, rabat związany z rezerwacją jest automatycznie stosowany do maszyn wirtualnych, które pasują do atrybutów i ilość rezerwacji. Rezerwacja obejmuje koszty operacji obliczeniowych maszyn wirtualnych.

W przypadku bazy danych SQL zastrzeżone pojemności, zobacz [zrozumieć zarezerwowane wystąpienia platformy Azure z rabatami](billing-understand-reservation-charges.md).

W poniższej tabeli przedstawiono koszty dla maszyny wirtualnej, po dokonaniu zakupu wystąpienia zarezerwowanego maszyny Wirtualnej. We wszystkich przypadkach, opłaty są naliczane dla magazynu i sieci w normalnych stawek za użycie.

| Typ maszyny wirtualnej  | Opłaty za pomocą rezerwacji wystąpienia maszyny Wirtualnej |
|-----------------------|--------------------------------------------|
|Maszyny wirtualne systemu Linux, bez dodatkowego oprogramowania | Rezerwacja obejmuje koszty infrastruktury maszyny Wirtualnej.|
|Maszyny wirtualne systemu Linux z opłatami oprogramowania (na przykład, Red Hat) | Rezerwacja obejmuje koszty związane z infrastrukturą. Opłaty są naliczane dla dodatkowego oprogramowania.|
|Windows VMs bez dodatkowego oprogramowania |Rezerwacja obejmuje koszty związane z infrastrukturą. Opłaty są naliczane dla oprogramowania Windows.|
|Windows maszyn wirtualnych, przy użyciu dodatkowego oprogramowania (na przykład SQL server) | Rezerwacja obejmuje koszty związane z infrastrukturą. Opłaty są naliczane w zakresie oprogramowania Windows oraz dodatkowego oprogramowania.|
|Maszyny wirtualne Windows, za pomocą [korzyść użycia hybrydowego platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | Rezerwacja obejmuje koszty związane z infrastrukturą. Koszty oprogramowania Windows są objęte korzyść użycia hybrydowego platformy Azure. Dodatkowe oprogramowanie jest rozliczany osobno.| 

## <a name="application-of-reservation-discount-to-non-windows-vms"></a>Zastosowanie rabat związany z rezerwacją do innych niż - Windows VMs

 Rabat związany z rezerwacją platformy Azure jest stosowany do uruchamiania wystąpień maszyn wirtualnych w systemie godzinowym. Rezerwy, które zostały nabyte są dopasowywane do użytkowaniu według działających maszyn wirtualnych, aby zastosować rabat związany z rezerwacją. W przypadku maszyn wirtualnych, które mogą nie działać całą godzinę rezerwacji zostaną wypełnione z innych maszyn wirtualnych, nie za pomocą rezerwacji, w tym jednocześnie działających maszyn wirtualnych. Na koniec godziny aplikacji rezerwacji dla maszyn wirtualnych w ciągu godziny jest zablokowany. W przypadku maszyny Wirtualnej kończy się niepowodzeniem na godzinę lub współbieżnych maszyn wirtualnych w ciągu godziny, nie należy wypełniać godzinę zastrzeżenia, rezerwacja jest dostatecznie wykorzystywane przez tę godzinę. Poniższy wykres przedstawia aplikacji rezerwację płatne użycie maszyny Wirtualnej. Ilustracja opiera się na zakup jedną rezerwację i dwa wystąpienia maszyn wirtualnych zgodnych.

![Zrzut ekranu przedstawiający jedną rezerwację zastosowane i dwa pasujących wystąpień maszyn wirtualnych](media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1. Opłaty za użycie powyżej wiersza rezerwacji pobiera naliczane regularne stawek zgodnie z rzeczywistym użyciem. Nie jesteś opłata za każde użycie poniżej wiersza rezerwacje, ponieważ on już zapłacono w ramach zakupu rezerwacji.
2. W ciągu godziny 1 uruchamiane wystąpienie 1 godziny 0,75 i uruchamiane wystąpienie 2 0,5 godziny. Łączne użycie przez 1 godzinę jest 1,25 godzin. Opłaty są naliczane zgodnie z rzeczywistym użyciem stawek pozostałe 0,25 godziny.
3. Godzina 2 i 3 godziny oba wystąpienia został uruchomiony przez 1 godzinę. Jedno wystąpienie jest objęte rezerwacji, a druga jest naliczana zgodnie z rzeczywistym użyciem.
4. Za godzinę 4 uruchamiane wystąpienie 1 0,5 godziny, a wystąpienie 2 jest uruchamiane przez 1 godzinę. Wystąpienia 1 jest w pełni objęte rezerwacji, które zostały omówione 0,5 godziny wystąpienia 2. Opłaty są naliczane zgodnie z rzeczywistym użyciem współczynnik pozostałe 0,5 godziny.

Aby zrozumieć i wyświetlić aplikację Azure rezerwacji w rozliczeniach raporty użycia, zobacz [użycia rezerwacji](https://go.microsoft.com/fwlink/?linkid=862757).

## <a name="application-of-reservation-discount-to-windows-vms"></a>Zastosowanie rabat związany z rezerwacją do maszyn wirtualnych Windows

Po uruchomieniu wystąpień maszyn wirtualnych Windows Rezerwacja ma zostać zastosowana do pokrycia kosztów infrastruktury. Aplikacja rezerwacji kosztów infrastruktury maszyny Wirtualnej dla maszyn wirtualnych Windows jest taka sama jak w przypadku innych niż - Windows maszyn wirtualnych. Opłaty są naliczane osobno dla oprogramowania Windows na podstawie poszczególnych procesorów wirtualnych. Zobacz [koszty oprogramowania Windows za pomocą rezerwacji](https://go.microsoft.com/fwlink/?linkid=862756). Mogą obejmować usługi Windows koszty za pomocą licencjonowania [Azure korzyści użycia hybrydowego dla systemu Windows Server](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing).

## <a name="discount-applies-to-different-sizes-with-instance-size-flexibility"></a>Rabat ma zastosowanie do różnych rozmiarów elastyczność rozmiaru wystąpienia

Przy zakupie rezerwacji wystąpienia maszyny Wirtualnej, jeśli zostanie wybrana **zoptymalizowane pod kątem**: **wystąpienia elastyczność rozmiaru**, pokrycia Rabat zależy od rozmiaru maszyny Wirtualnej, należy wybrać. Rezerwację można zastosować do rozmiarów maszyn wirtualnych (VM) w tej samej grupie rozmiar w serii. Aby uzyskać więcej informacji, zobacz [elastyczność rozmiaru maszyny wirtualnej z zarezerwowanymi wystąpieniami maszyn wirtualnych](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat rezerwacji Azure, zobacz następujące artykuły:

- [Co to są rezerwacji dla platformy Azure?](billing-save-compute-costs-reservations.md)
- [Prepay for Virtual Machines with Azure Reserved VM Instances (Opłacanie maszyn wirtualnych z góry przy użyciu usługi Azure Reserved VM Instances)](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Prepay for SQL Database compute resources with Azure SQL Database reserved capacity (Opłacanie zasobów obliczeniowych usługi SQL Database z góry przy użyciu zarezerwowanej pojemności usługi Azure SQL Database)](../sql-database/sql-database-reserved-capacity.md)
- [Zarządzanie rezerwacji dla platformy Azure](billing-manage-reserved-vm-instance.md)
- [Opis zastrzeżenia dla Twojej subskrypcji zgodnie z rzeczywistym użyciem](billing-understand-reserved-instance-usage.md)
- [Opis zastrzeżenia dla Twojej rejestracji Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Opis zastrzeżenia dla subskrypcji programu CSP](https://docs.microsoft.com/partner-center/azure-reservations)
- [Koszty oprogramowania Windows nie jest dołączony do rezerwacji](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).

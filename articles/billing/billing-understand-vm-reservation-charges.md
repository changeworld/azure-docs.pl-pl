---
title: Zrozumienie zastrzeżone wystąpieniach platformy Azure discount | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rabat zastrzeżone wystąpienia maszyny Wirtualnej platformy Azure są stosowane do uruchomionych maszyn wirtualnych.
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
ms.date: 05/09/2018
ms.author: yashar
ms.openlocfilehash: d1229a49a5bb3bf3198c91a748ed37b7a626c506
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063916"
---
# <a name="understand-how-the-reserved-instance-discount-is-applied"></a>Zrozumienie, jak stosowany jest rabat zastrzeżone wystąpienia
Po kupić zastrzeżone wystąpienia maszyny Wirtualnej Azure, rabat zastrzeżone wystąpienie jest automatycznie stosowana do dopasowania atrybuty i liczba zarezerwowanych wystąpienia maszyn wirtualnych. Zarezerwowane wystąpienia obejmuje kosztów infrastruktury maszyn wirtualnych. W poniższej tabeli przedstawiono kosztów dla maszyny wirtualnej, po dokonaniu zakupu zastrzeżone wystąpienia. We wszystkich przypadkach są naliczane opłaty dotyczące magazynu i sieci w normalnych stawek.

| Typ maszyny wirtualnej  | Opłaty za wystąpienia zastrzeżone |    
|-----------------------|--------------------------------------------|
|Maszyny wirtualne systemu Linux bez dodatkowego oprogramowania | Zarezerwowane wystąpienia obejmuje koszty infrastruktury maszyny Wirtualnej.|
|Maszyny wirtualne systemu Linux z opłat oprogramowania (na przykład Red Hat) | Zarezerwowane wystąpienia obejmuje kosztów infrastruktury. Naliczane są opłaty za dodatkowe oprogramowanie.|
|Maszyny wirtualne systemu Windows bez dodatkowego oprogramowania |Zarezerwowane wystąpienia obejmuje kosztów infrastruktury. Naliczane są opłaty oprogramowania systemu Windows.|
|Maszyny wirtualne systemu Windows z dodatkowego oprogramowania (na przykład SQL server) | Zarezerwowane wystąpienia obejmuje kosztów infrastruktury. Naliczane są opłaty oprogramowania systemu Windows i dodatkowe oprogramowanie.|
|Maszyny wirtualne systemu Windows z [korzyści hybrydowe platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | Zarezerwowane wystąpienia obejmuje kosztów infrastruktury. Koszty oprogramowania systemu Windows są objęte korzyści hybrydowe platformy Azure. Dodatkowe oprogramowanie jest pobierana osobno.| 

## <a name="application-of-reserved-instance-discount-to-non-windows-vms"></a>Aplikacja rabatu wystąpienia zarezerwowane do VMs z systemem innym niż Windows
 Zniżki zastrzeżone wystąpienia usługi Azure jest stosowany do uruchomionych wystąpień maszyny Wirtualnej na godzinę. Zarezerwowane wystąpienia, które zostały nabyte są dopasowywane do użycia emitowane przez uruchomionych maszyn wirtualnych stosowania rabatu wystąpienia zastrzeżone. Dla maszyn wirtualnych, które mogą nie działać z pełną godzina zastrzeżone wystąpienia zostaną wypełnione z innych maszyn wirtualnych nie jest używane wystąpienie zastrzeżonych, łącznie jednocześnie działających maszyn wirtualnych. Na koniec godziny zarezerwowane wystąpienia aplikacji dla maszyn wirtualnych w ciągu godziny jest zablokowany. W przypadku maszyny Wirtualnej nie jest uruchamiane na godzinę lub równoczesnych maszyn wirtualnych w ciągu godziny nie wypełnia godzinę wystąpienia zastrzeżonych, zarezerwowane wystąpienia jest wykorzystywane przez danej godziny. Wykres przedstawia stosowania zastrzeżone wystąpienia rozliczeniowy użycie maszyny Wirtualnej. Ilustracja jest oparta na jedno wystąpienie zastrzeżone zakupu i dwa zgodnych wystąpień maszyny Wirtualnej.

![Zrzut ekranu przedstawiający jedno wystąpienie zastrzeżone zastosowane i dwa zgodnych wystąpień maszyny Wirtualnej](media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1.  Bez użycia powyżej wiersza zastrzeżone wystąpienia pobiera obciążony stawkami płatności obejmujące regularne. Nie masz opłata za bez użycia poniżej wiersza zastrzeżone wystąpienia, ponieważ ma on już płatnej w ramach zakupu wystąpienia zastrzeżone.
2.  W ciągu 1 godziny jest uruchomione wystąpienie 1 godziny 0,75 i uruchamia wystąpienie 2 0,5 godzin. Całkowite użycie godzinę 1 jest 1,25 godzin. Płatności obejmujące stawki są naliczane pozostałych godzin 0,25.
3.  Godzina 2 i 3 godziny oba wystąpienia uruchomiono 1 godziny. Jedno wystąpienie jest objęta wystąpienia zastrzeżone i innych rozliczany z szybkością.
4.  4 godziny jest uruchomione wystąpienie 1 godziny 0,5 i uruchamia wystąpienie 2 1 godziny. Wystąpienia 1 pełni objęta zastrzeżone wystąpienia i jest objęte 0,5 godzin wystąpienia 2. Jest obciążony płatności obejmujące współczynnika 0,5 pozostałe godziny.

Aby zrozumieć i wyświetlić stosowania swoich Azure wystąpień zarezerwowane w rozliczeń raporty użycia, zobacz [użycia zrozumieć zastrzeżone wystąpienia](https://go.microsoft.com/fwlink/?linkid=862757).

## <a name="application-of-reserved-instance-discount-to-windows-vms"></a>Stosowanie rabatu zastrzeżone wystąpienia do maszyn wirtualnych systemu Windows
Po uruchomieniu wystąpień maszyny Wirtualnej systemu Windows, zarezerwowane wystąpienia jest stosowany do obejmują kosztów infrastruktury. Aplikacja zastrzeżone wystąpienia kosztów infrastruktury maszyny Wirtualnej dla maszyn wirtualnych systemu Windows jest taka sama jak w przypadku VMs z systemem innym niż Windows. Naliczane są opłaty oddzielnie oprogramowania systemu Windows na podstawie na vCPU. Zobacz [koszty oprogramowania systemu Windows z wystąpieniami zastrzeżone](https://go.microsoft.com/fwlink/?linkid=862756). Może obejmować koszty z [Azure hybrydowego korzyści dla systemu Windows Server] licencjonowania systemu Windows (https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing).

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej o wystąpieniach zastrzeżonych, zobacz następujące artykuły:

- [Co to są zastrzeżone wystąpień maszyn wirtualnych Azure?](billing-save-compute-costs-reservations.md)
- [Przedpłaty dla maszyn wirtualnych z wystąpień zastrzeżone maszyna wirtualna platformy Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Zarządzanie wystąpieniami zarezerwowane na platformie Azure](billing-manage-reserved-vm-instance.md)
- [Zrozumienie, jak stosowany jest rabat zastrzeżone wystąpienia](billing-understand-vm-reservation-charges.md)
- [Określić sposób użycia wystąpienia zarezerwowane dla Twojej subskrypcji z](billing-understand-reserved-instance-usage.md)
- [Określić sposób użycia wystąpienia zarezerwowane dla rejestracji w przedsiębiorstwie](billing-understand-reserved-instance-usage-ea.md)
- [Określić sposób użycia wystąpienia zarezerwowane dla subskrypcji dostawcy usług Kryptograficznych](https://docs.microsoft.com/partner-center/azure-reservations)
- [Koszty oprogramowania systemu Windows nie jest dołączony do wystąpienia zastrzeżone](billing-reserved-instance-windows-software-costs.md)


## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli masz więcej pytań, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać szybkie rozwiązanie problemu.

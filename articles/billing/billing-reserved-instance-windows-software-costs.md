---
title: Koszty oprogramowania Windows Azure rezerwacji | Dokumentacja firmy Microsoft
description: Dowiedz się, które liczniki oprogramowania Windows nie są uwzględnione w kosztów rezerwacji wystąpienia maszyny Wirtualnej platformy Azure.
services: billing
documentationcenter: ''
author: manish-shukla01
manager: manshuk
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: manshuk
ms.openlocfilehash: 8ff2d126fd491ddc40f6223b6d4ec1fc4505b8fb
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39630115"
---
# <a name="windows-software-costs-not-included-with-azure-reservations"></a>Koszty oprogramowania Windows nie jest dołączony do platformy Azure rezerwacji

Jeśli nie masz korzyści z używania hybrydowej platformy Azure w swoich wystąpień zarezerwowanych maszyn wirtualnych jest naliczana za liczniki oprogramowanie Windows, które są wymienione w poniższej sekcji.

## <a name="windows-software-meters-not-included-in-reservation-cost"></a>Liczniki oprogramowania Windows nie są objęte kosztów rezerwacji

| Identyfikator miernika | MeterName w pliku użycia | Używane przez maszynę Wirtualną |
| ------- | ------------------------| --- |
| e7e152ac-f29c-4cce-ad6e-026192c01ef2 | Maszyny rezerwacji Windows Server — seria (1 rdzeń) | Seria B |
| cac255a2-9f0f-4c62-8bd6-f0fa449c5f76 | Maszyny rezerwacji Windows Server — seria (2 rdzenie) | Seria B |
| 09756b58-3fb5-4390-976d-9ddd14f9ed18 | Maszyny rezerwacji Windows Server — seria (4 rdzenie) | Seria B |
| e828cb37-5920-4dc7-b30f-664e4dbcb6c7 | Maszyny rezerwacji Windows Server — seria (8 rdzeni) | Seria B |
| f65a06cf-c9c3-47a2-8104-f17a8542215a | Svr Rezerwacja Windows (1 rdzeń) | Wszystkie regiony z wyjątkiem seria B |
| b99d40ae-41fe-4d1d-842b-56d72f3d15ee | Svr Rezerwacja Windows (2 rdzenie) | Wszystkie regiony z wyjątkiem seria B |
| 1cb88381-0905-4843-9ba2-7914066aabe5 | Svr Rezerwacja Windows (4 rdzenie) | Wszystkie regiony z wyjątkiem seria B |
| 07d9e10d-3e3e-4672-ac30-87f58ec4b00a | Svr Rezerwacja Windows (6 rdzeni) | Wszystkie regiony z wyjątkiem seria B |
| 603f58d1-1e96-460b-a933-ce3775ac7e2e | Svr Rezerwacja Windows (8 rdzeni) | Wszystkie regiony z wyjątkiem seria B |
| 36aaadda-da86-484a-b465-c8b5ab292d71 | Svr Rezerwacja Windows (12 rdzeni) | Wszystkie regiony z wyjątkiem seria B |
| 02968a6b-1654-4495-ada6-13f378ba7172 | Svr Rezerwacja Windows (16 rdzeni) | Wszystkie regiony z wyjątkiem seria B |
| 175434d8-75f9-474b-9906-5d151b6bed84 | Svr Rezerwacja Windows (20 rdzeni) | Wszystkie regiony z wyjątkiem seria B |
| 77eb6dd0-88f5-4a16-ab39-05d1742efb25 | Svr Rezerwacja Windows (24 rdzenie) | Wszystkie regiony z wyjątkiem seria B |
| 0d5bdf46-b719-4b1f-a780-b9bdfffd0591 | Svr Rezerwacja Windows (32 rdzenie) | Wszystkie regiony z wyjątkiem seria B |
| f1214b5c-cc16-445f-be6c-a3bb75f8395a | Svr Rezerwacja Windows (40 rdzeni) | Wszystkie regiony z wyjątkiem seria B |
| 637b7c77-65ad-4486-9cc7-dc7b3e9a8731 | Svr Rezerwacja Windows (64 rdzenie) | Wszystkie regiony z wyjątkiem seria B |
| da612742-e7cc-4ca3-9334-0fb7234059cd | Svr Rezerwacja Windows (72 rdzenie) | Wszystkie regiony z wyjątkiem seria B |
| a485cb8c-069b-4cf3-9a8e-ddd84b323da2 | Svr Rezerwacja Windows (128 rdzeni) | Wszystkie regiony z wyjątkiem seria B |
| 904c5c71-1eb7-43a6-961c-d305a9681624 | Svr Rezerwacja Windows (256 rdzeni) | Wszystkie regiony z wyjątkiem seria B |
| 6fdab81b-4284-4df9-8939-c237cc7462fe | Svr Rezerwacja Windows (96 rdzeni) | Wszystkie regiony z wyjątkiem seria B |

Koszt każdego z tych liczników można uzyskać za pośrednictwem interfejsu API RateCard platformy Azure. Aby uzyskać informacje dotyczące sposobu uzyskania stawki dla mierników platformy azure, zobacz [uzyskiwanie informacji o cenach i metadanych dla zasobów używanych w subskrypcji platformy Azure](https://msdn.microsoft.com/library/azure/mt219004).

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:

- [Co to są rezerwacje platformy Azure?](billing-save-compute-costs-reservations.md)
- [Przedpłaty dotyczące maszyn wirtualnych przy użyciu wystąpienia zarezerwowane maszyn wirtualnych platformy Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Zarządzanie zastrzeżeniami na platformie Azure](billing-manage-reserved-vm-instance.md)
- [Zrozumienie, jak stosowany jest rabat związany z rezerwacją](billing-understand-vm-reservation-charges.md)
- [Opis zastrzeżenia dla Twojej subskrypcji zgodnie z rzeczywistym użyciem](billing-understand-reserved-instance-usage.md)
- [Opis zastrzeżenia dla Twojej rejestracji Enterprise](billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal masz dodatkowe pytania, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.




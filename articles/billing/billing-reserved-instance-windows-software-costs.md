---
title: Rezerwacje kosztów oprogramowania na platformie Azure | Microsoft Docs
description: Dowiedz się, które liczniki oprogramowania nie są uwzględnione w kosztach wystąpienia zarezerwowanych maszyn wirtualnych platformy Azure.
services: billing
documentationcenter: ''
author: yashar
manager: yashar
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2019
ms.author: banders
ms.openlocfilehash: 52e2e2503303c2a7525a3a6c156f648c097b27dd
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68478626"
---
# <a name="software-costs-not-included-with-azure-reserved-vm-instances"></a>Koszty oprogramowania nieuwzględnione w Azure Reserved VM Instances

Zarezerwowane wystąpienie maszyny wirtualnej i rabaty zarezerwowane dla programu SQL mają zastosowanie tylko do kosztów infrastruktury, a nie do kosztów oprogramowania. Jeśli używasz maszyny wirtualnej z systemem Windows i nie masz Korzyść użycia hybrydowego platformy Azure w wystąpieniach zarezerwowanych maszyn wirtualnych, opłata zostanie naliczona za liczniki oprogramowania wymienione w poniższej sekcji. W przypadku wdrożeń SQL PaaS koszt adresu IP będzie nadal naliczany przy użyciu oddzielnego miernika, jeśli nie wybrano Korzyść użycia hybrydowego platformy Azure.

## <a name="windows-software-meters-not-included-in-reservation-cost"></a>Liczniki oprogramowania systemu Windows, które nie są uwzględnione w koszcie rezerwacji

| MeterId | Miernikname w pliku użycia | Używane przez maszynę wirtualną |
| ------- | ------------------------| --- |
| e7e152ac-f29c-4cce-ad6e-026192c01ef2 | Rezerwacja — system Windows SVR z serii (1 rdzeń) | Seria B |
| cac255a2-9f0f-4c62-8bd6-f0fa449c5f76 | Rezerwacja — Windows SVR z serii (2 rdzenie) | Seria B |
| 09756b58-3fb5-4390-976d-9ddd14f9ed18 | Rezerwacja — Windows SVR z serii (4 rdzenie) | Seria B |
| e828cb37-5920-4dc7-b30f-664e4dbcb6c7 | Rezerwacja — Windows SVR z serii (8 rdzeni) | Seria B |
| f65a06cf-c9c3-47a2-8104-f17a8542215a | Rezerwacja — Windows SVR (1 rdzeń) | Wszystkie z wyjątkiem serii B |
| b99d40ae-41fe-4d1d-842b-56d72f3d15ee | Rezerwacja — Windows SVR (2 rdzenie) | Wszystkie z wyjątkiem serii B |
| 1cb88381-0905-4843-9ba2-7914066aabe5 | Rezerwacja — Windows SVR (4 rdzenie) | Wszystkie z wyjątkiem serii B |
| 07d9e10d-3e3e-4672-ac30-87f58ec4b00a | Rezerwacja — Windows SVR (6 rdzeni) | Wszystkie z wyjątkiem serii B |
| 603f58d1-1e96-460b-a933-ce3775ac7e2e | Rezerwacja — Windows SVR (8 rdzeni) | Wszystkie z wyjątkiem serii B |
| 36aaadda-da86-484a-b465-c8b5ab292d71 | Rezerwacja — Windows SVR (12 rdzeni) | Wszystkie z wyjątkiem serii B |
| 02968a6b-1654-4495-ada6-13f378ba7172 | Rezerwacja — Windows SVR (16 rdzeni) | Wszystkie z wyjątkiem serii B |
| 175434d8-75f9-474b-9906-5d151b6bed84 | Rezerwacja — Windows SVR (20 rdzeni) | Wszystkie z wyjątkiem serii B |
| 77eb6dd0-88f5-4a16-ab39-05d1742efb25 | Rezerwacja — Windows SVR (24 rdzenie) | Wszystkie z wyjątkiem serii B |
| 0d5bdf46-b719-4b1f-a780-b9bdfffd0591 | Rezerwacja — Windows SVR (32 rdzeni) | Wszystkie z wyjątkiem serii B |
| f1214b5c-cc16-445f-be6c-a3bb75f8395a | Rezerwacja — Windows SVR (40 rdzeni) | Wszystkie z wyjątkiem serii B |
| 637b7c77-65ad-4486-9cc7-dc7b3e9a8731 | Rezerwacja — Windows SVR (64 rdzeni) | Wszystkie z wyjątkiem serii B |
| da612742-e7cc-4ca3-9334-0fb7234059cd | Rezerwacja — Windows SVR (72 rdzeni) | Wszystkie z wyjątkiem serii B |
| a485cb8c-069b-4cf3-9a8e-ddd84b323da2 | Rezerwacja — Windows SVR (128 rdzeni) | Wszystkie z wyjątkiem serii B |
| 904c5c71-1eb7-43a6-961c-d305a9681624 | Rezerwacja — Windows SVR (256 rdzeni) | Wszystkie z wyjątkiem serii B |
| 6fdab81b-4284-4df9-8939-c237cc7462fe | Rezerwacja — Windows SVR (96 rdzeni) | Wszystkie z wyjątkiem serii B |

## <a name="cloud-services-software-meters-not-included-in-reservation-cost"></a>Liczniki oprogramowania usług Cloud Services nie są uwzględnione w koszcie rezerwacji

| MeterId | Miernikname w pliku użycia |
| ------- | ------------------------|
|ac9d47ff-ff68-4afc-a145-0c321cf8d0d5|Cloud Services 1 Licencja vCPU|
|e0434559-19ee-4132-9c46-05ad4044f3f7|Licencja na Cloud Services 2 vCPU|
|6ecc834e-39b3-48b3-8d10-cc5626bacb66|Licencja na Cloud Services 4 vCPU|
|13103090-ca72-4825-ab12-7f16c4931d95|Licencja na Cloud Services 8 vCPU|
|ecd2bb6e-45a5-49aa-a58b-3947ba21c364|Licencja na Cloud Services 16 vCPU|
|de2c7f1d-06dc-4b16-bc8b-c2ec5f4c8aee|Cloud Services 20 licencji vCPU|
|ca1af837-4b35-47f5-8d14-b1988149c4ca|Cloud Services 32 licencja vCPU|
|dc72ee45-2ab7-4698-b435-e2cf10d1f9f6|Cloud Services 64 licencja vCPU|
|7a803026-244c-4659-834c-11e6b2d6b76f|Cloud Services 80 licencja vCPU|

## <a name="rates-for-azure-meters"></a>Stawki za usługę Azure Gazomierze

Koszt każdego z tych liczników można uzyskać za pomocą interfejsu API usługi Azure RateCard. Aby uzyskać informacje na temat sposobu uzyskiwania stawek za usługę Azure licznik, zobacz [pobieranie informacji o cenach i metadanych dla zasobów używanych w ramach subskrypcji platformy Azure](/previous-versions/azure/reference/mt219004(v=azure.100)).

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat rezerwacji dla platformy Azure, zobacz następujące artykuły:

- [Co to są rezerwacje dla systemu Azure?](billing-save-compute-costs-reservations.md)
- [Prepay for Virtual Machines with Azure Reserved VM Instances (Opłacanie maszyn wirtualnych z góry przy użyciu usługi Azure Reserved VM Instances)](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Zarządzanie rezerwacjami dla platformy Azure](billing-manage-reserved-vm-instance.md)
- [Zapoznaj się z zastosowaniem rabatu rezerwacji](billing-understand-vm-reservation-charges.md)
- [Informacje na temat użycia rezerwacji dla subskrypcji z opcją płatność zgodnie z rzeczywistym użyciem](billing-understand-reserved-instance-usage.md)
- [Informacje na temat użycia rezerwacji na potrzeby rejestracji w przedsiębiorstwie](billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie pomocy technicznej](https://go.microsoft.com/fwlink/?linkid=2083458).

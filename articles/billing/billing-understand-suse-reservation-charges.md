---
title: Zrozumienie SUSE rezerwacją planu i użycia — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak SUSE plan rabaty dotyczą oprogramowania SUSE na maszynach wirtualnych.
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
ms.date: 08/28/2018
ms.author: banders
ms.openlocfilehash: 4305db991a8129b0ae4205300051391df893c52c
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "58917791"
---
# <a name="understand-how-the-suse-linux-enterprise-software-reservation-plan-discount-is-applied-for-azure"></a>Zrozumieć sposób zastosowania planu rezerwacją oprogramowania SUSE Linux Enterprise na platformie Azure

Po możesz kupić plan SUSE Linux, rabat jest automatycznie stosowany do wdrożonej SUSE maszyn wirtualnych (VM, które odpowiadają rezerwacji). Plan SUSE Linux po awarii obejmuje koszt z uruchamiania oprogramowania SUSE na Maszynie wirtualnej platformy Azure.

Aby kupić właściwego planu SUSE Linux, musisz zrozumieć, jakie maszyny wirtualne SUSE w uruchomieniu i liczba procesorów wirtualnych na tych maszynach wirtualnych. Następujące sekcje zawierają informacje ułatwiające identyfikację z pliku CSV użycia, co to jest plan zakupu.

## <a name="discount-applies-to-different-vm-sizes"></a>Rabat ma zastosowanie do różnych rozmiarów maszyn wirtualnych

Jak zarezerwowanych wystąpień maszyn wirtualnych, SUSE planu zakupów oferują elastyczność rozmiaru wystąpienia. Oznacza to, że ma zastosowanie rabat, nawet gdy wdrożysz maszynę Wirtualną wraz z liczbą różnych procesorów wirtualnych. Rabat stosuje się do różnych rozmiarów maszyn wirtualnych w ramach planu oprogramowania.

Rabat zależy od współczynnik wymienione w poniższych tabelach. Współczynnik porównanie względnej śladu w ramach każdego miernika, w tej grupie. Współczynnik zależy od wirtualnych procesorów CPU maszyny Wirtualnej. Aby obliczyć liczbę wystąpień maszyn wirtualnych Uzyskaj rabat w wysokości plan SUSE Linux, należy użyć wartości współczynnika.

Na przykład możesz kupić plan dla SUSE Linux Enterprise Server for HPC — priorytet maszyny wirtualnej z 3 lub 4 Vcpu, współczynnik dla tej rezerwacji to 2. Rabat po awarii obejmuje koszt oprogramowania SUSE na:

- 2 wdrożonych maszyn wirtualnych z 1 lub 2 procesorów wirtualnych
- 1 wdrożonych maszyn wirtualnych z 3 lub 4 Vcpu,
- lub 0.77 lub wkrótce % 77 maszyny wirtualnej z 5 lub więcej procesorów wirtualnych.

Współczynnik 5 lub więcej procesorów wirtualnych Vcpu jest 2.6. Dlatego Rezerwacja SUSE z maszyną Wirtualną z 5 lub więcej procesorów wirtualnych Vcpu obejmuje tylko część koszt oprogramowania, czyli około 77%.

## <a name="understand-suse-vm-usage-before-you-buy"></a>Opis przed zakupem maszyny Wirtualnej z systemem SUSE

W poniższych tabelach przedstawiono plany oprogramowania, które można kupić rezerwację dla ich użycia skojarzone liczniki i współczynniki dla każdego.

### <a name="suse-linux-enterprise-server-for-hpc-priority"></a>SUSE Linux Enterprise Server for HPC Priority

Nazwa witryny portalu Azure marketplace:

- SLES 12 z dodatkiem SP3 dla HPC (priorytet)

|MASZYNA WIRTUALNA Z SYSTEMEM SUSE | Identyfikator miernika| Współczynnik| Przykład rozmiar maszyny Wirtualnej|
| -------| ------------------------| --- |--- |
|System SLES dla HPC 1 – 2 procesorów wirtualnych Vcpu|e275a668-ce79-44e2-a659-f43443265e98|1|D2s_v3|
|System SLES dla HPC 3 – 4 Vcpu|e531e1c0-09c9-4d83-b7d0-a2c6741faa22|2|D4s_v3|
|SLES dla HPC 5 + procesorów wirtualnych|4edcd5a5-8510-49a8-a9fc-c9721f501913|2.6|D8s_v3|

### <a name="suse-linux-enterprise-server-for-hpc-standard"></a>SUSE Linux Enterprise Server for HPC Standard

Nazwa witryny portalu Azure marketplace:

- SLES 12 z dodatkiem SP3 dla HPC

|MASZYNA WIRTUALNA Z SYSTEMEM SUSE | Identyfikator miernika | Współczynnik|Przykład rozmiar maszyny Wirtualnej|
| ------- | --- | ------------------------| --- |
|System SLES dla HPC 1 – 2 procesorów wirtualnych Vcpu |8c94ad45-b93b-4772-aab1-ff92fcec6610|1|D2s_v3|
|System SLES dla HPC 3 – 4 Vcpu|4ed70d2d-e2bb-4dcd-b6fa-42da71861a1c|1.92308|D4s_v3|
|SLES dla HPC 5 + procesorów wirtualnych |907a85de-024f-4dd6-969c-347d47a1bdff|2.92308|D8s_v3|

### <a name="suse-linux-enterprise-server-for-sap-priority"></a>SUSE Linux Enterprise Server dla priorytetu SAP

Nazwy witryny portalu Azure marketplace:

- SLES for SAP 15 (priorytet)
- SLES for SAP 12 z dodatkiem SP3 (priorytet)
- SLES for SAP 12 z dodatkiem SP2 (priorytet)

|MASZYNA WIRTUALNA Z SYSTEMEM SUSE | Identyfikator miernika | Współczynnik|Przykład rozmiar maszyny Wirtualnej|
| ------- |------------------------| --- | --- |
|Z systemem SLES for SAP o priorytecie 1 – 2 procesorów wirtualnych Vcpu|497fe0b6-fa3c-4e3d-a66b-836097244142|1|D2s_v3|
|Z systemem SLES for SAP priorytet 3 – 4 Vcpu |847887de-68ce-4adc-8a33-7a3f4133312f|2|D4s_v3|
|Z systemem SLES for SAP o priorytecie 5 + procesorów wirtualnych Vcpu |18ae79cd-dfce-48c9-897b-ebd3053c6058|2.41176|D8s_v3|

### <a name="suse-linux-enterprise-server-priority"></a>SUSE Linux Enterprise Server Priority

Nazwy witryny portalu Azure marketplace:

- W SYSTEMIE SLES (PRIORYTET) 15
- SLES 12 z dodatkiem SP3 (priorytet)
- SLES 11 z dodatkiem SP4 (priorytet)

|MASZYNA WIRTUALNA Z SYSTEMEM SUSE | Identyfikator miernika | Współczynnik|Przykład rozmiar maszyny Wirtualnej|
| ------- |------------------------| --- |--- |
|SLES 1 Procesor wirtualny vCPU|462cd632-ec6b-4663-b79f-39715f4e8b38|1|B1ms|
|SLES 2 – 4 Vcpu |924bee71-5eb8-424f-83ed-a58823c33908|2|D4s_v3|
|SLES 2 – 4 Vcpu |60b3ae9d-e77a-46b2-9cdf-92fa87407969|2|D4s_v3|
|SLES 6 procesorów wirtualnych Vcpu |e8862232-6131-4dbe-bde4-e2ae383afc6f|3||
|SLES 8 wirtualnych procesorów CPU |e11331a8-fd32-4e71-b60e-4de2a818c67a|3.2|D8s_v3|
|SLES 12 rdzeni procesorów wirtualnych Vcpu |a5afd00d-d3ef-4bcd-8b42-f158b2799782|3.2||
|SLES 16 procesorów wirtualnych Vcpu |bb21066f-fe46-46d3-8006-b326b1663e52|3.2| D16s_v3|
|SLES 20 procesorów wirtualnych Vcpu |c5228804-1de6-4bd4-a61c-501d9003acc8|3.2| |
|24 w systemie SLES rdzeni procesorów wirtualnych |-005d-4075-ac11-822ccde9e8f6|3.2| ND24s|
|SLES 32 procesorów wirtualnych Vcpu |180c1a0a-b0a5-4de3-a032-f92925a4bf90|3.2| D32s_v3|
|SLES 40 rdzeni procesorów wirtualnych |a161d3d3-0592-4956-9b64-6829678b6506|3.2||
|SLES 64 wirtualnych procesorów CPU |7f5a36ed-d5b5-4732-b6bb-837dbf0fb9d8|3.2| D64s_v3|
|SLES 72 rdzeni procesorów wirtualnych |93329a72-24d7-4faa-93d9-203f367ed334|3.2|F72s_v2|
|SLES 96 rdzeni procesorów wirtualnych |2018c3a8-ff13-41f8-b64d-9558c5206547|3.2||
|SLES 128 rdzeni vCPUss |ac27e4d7-44b5-4fee-bc1a-78ac5b4abaf7|3.2| M128ms|

### <a name="suse-linux-enterprise-server-standard"></a>SUSE Linux Enterprise Server Standard

Nazwy witryny portalu Azure marketplace:

- SLES 15
- SLES 15 (standardowa)
- SLES 12 z dodatkiem SP3 (standardowa)

|MASZYNA WIRTUALNA Z SYSTEMEM SUSE | Identyfikator miernika | Współczynnik|Przykład rozmiar maszyny Wirtualnej|
| ------- |------------------------| --- |--- |
|SLES 1 – 2 rdzeni wirtualnych procesorów CPU |4b2fecfc-b110-4312-8f9d-807db1cb79ae|1|D2s_v3|
|SLES 3 do 4 rdzeni procesorów wirtualnych Vcpu |0c3ebb4c-db7d-4125-b45a-0534764d4bda|1.92308|D4s_v3|
|SLES 5 + procesorów wirtualnych |7b349b65-d906-42e5-833f-b2af38513468|2.30769| D8s_v3|

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat rezerwacji, zobacz następujące artykuły:

- [Co to jest Azure Reservations?](billing-save-compute-costs-reservations.md)
- [Zapłać z góry za plany oprogramowania SUSE z zastrzeżeniami platformy Azure](../virtual-machines/linux/prepay-suse-software-charges.md)
- [Przedpłaty dotyczące maszyn wirtualnych przy użyciu wystąpienia zarezerwowane maszyn wirtualnych platformy Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Zarządzanie zastrzeżeniami platformy Azure](billing-manage-reserved-vm-instance.md)
- [Opis zastrzeżenia dla Twojej subskrypcji zgodnie z rzeczywistym użyciem](billing-understand-reserved-instance-usage.md)
- [Opis zastrzeżenia dla Twojej rejestracji Enterprise](billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).

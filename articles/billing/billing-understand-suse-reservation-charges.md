---
title: Rabat w wysokości plan oprogramowania — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rabaty w ramach planu oprogramowanie są stosowane do oprogramowania na maszynach wirtualnych.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2019
ms.author: banders
ms.openlocfilehash: bcbf5ab48f3476a911fc4ade1eb0c395fb335d43
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60002735"
---
# <a name="azure-software-plan-discount"></a>Rabat plan oprogramowania platformy Azure

Plany oprogramowania platformy Azure, SUSE i RedHat są rezerwacji, które są stosowane do wdrożone maszyny wirtualne. Rabat w wysokości plan oprogramowania są stosowane do użytkowania oprogramowania, które odpowiadają rezerwacji wdrożone maszyny wirtualne.

Podczas zamykania maszyny Wirtualnej rabat jest automatycznie stosowana do innej maszyny Wirtualnej dopasowania, jeśli jest dostępny. Plan oprogramowania po awarii obejmuje koszt uruchamiania oprogramowania na maszynie Wirtualnej. Inne opłaty, takich jak wystąpienia obliczeniowe, Magazyn i sieć, są rozliczane osobno.

Aby kupić właściwego planu, należy zrozumieć wykorzystanie maszyny Wirtualnej i liczbę procesorów wirtualnych na tych maszynach wirtualnych. Następujące sekcje ułatwiają określenie, co w planie kupić, na podstawie danych użycia.

## <a name="how-reservation-discount-is-applied"></a>Jaki jest stosowany rabat związany z rezerwacją

Rabat związany z rezerwacją jest "*Użyj it lub utracić — it*". Tak Jeśli nie masz żadnych godzinę dopasowywania zasobów, następnie traci ilość rezerwacji dla danej godziny. Nie można wykonać nieużywane zastrzeżone godzin przekazywania.

Podczas zamykania zasobem rabat związany z rezerwacją jest automatycznie stosuje do innego zasobu zgodnego z określonego zakresu. Jeśli żadne pasujące zasoby znajdują się w określonym zakresie, a następnie są zarezerwowane godzin *utracone*.

## <a name="review-redhat-vm-usage-before-you-buy"></a>Przejrzyj użycie maszyny Wirtualnej z systemem RedHat przed zakupem

Pobierz nazwę produktu z danych użycia i Kup RedHat plan przy użyciu tego samego typu i rozmiaru.

Na przykład, jeśli użycie ma produktu **Red Hat Enterprise Linux - 1-4 vCPU licencji maszyny Wirtualnej**, musisz kupić **Red Hat Enterprise Linux** dla **1 – 4 vCPU maszyny Wirtualnej**.

<!--ADD RHEL SCREENSHOT -->

## <a name="review-suse-vm-usage-before-you-buy"></a>Przed zakupem, przejrzyj użycie maszyny Wirtualnej z systemem SUSE

Pobierz nazwę produktu z danych użycia i Kup SUSE plan przy użyciu tego samego typu i rozmiaru.

Na przykład w przypadku użycia produktu **SUSE Linux Enterprise Server priorytet — 2 – 4 vCPU maszyn wirtualnych z osłoną**, musisz kupić **SUSE Linux Enterprise Server priorytet** dla **2 – 4 vCPU**.

![Przykład wybierania produktów do zakupu](./media/billing-understand-suse-reservation-charges/select-suse-linux-enterprise-server-priority-2-4-vcpu.png)

## <a name="discount-applies-to-different-vm-sizes-for-suse-plans"></a>Rabat ma zastosowanie do różnych rozmiarów maszyn wirtualnych w przypadku planów SUSE

Jak zarezerwowanych wystąpień maszyn wirtualnych, SUSE planu zakupów oferują elastyczność rozmiaru wystąpienia. Oznacza to, że ma zastosowanie rabat, nawet gdy wdrożysz maszynę Wirtualną wraz z liczbą różnych procesorów wirtualnych. Rabat stosuje się do różnych rozmiarów maszyn wirtualnych w ramach planu oprogramowania.

Rabat zależy od współczynnik wymienione w poniższych tabelach. Współczynnik porównanie względnej śladu w ramach każdego miernika, w tej grupie. Współczynnik zależy od wirtualnych procesorów CPU maszyny Wirtualnej. Aby obliczyć liczbę wystąpień maszyn wirtualnych Uzyskaj rabat w wysokości plan SUSE Linux, należy użyć wartości współczynnika.

Na przykład możesz kupić plan dla SUSE Linux Enterprise Server for HPC — priorytet maszyny wirtualnej z 3 lub 4 Vcpu, współczynnik dla tej rezerwacji to 2. Rabat po awarii obejmuje koszt oprogramowania SUSE na:

- 2 wdrożonych maszyn wirtualnych z 1 lub 2 procesorów wirtualnych
- 1 wdrożonych maszyn wirtualnych z 3 lub 4 Vcpu,
- lub 0.77 lub wkrótce % 77 maszyny wirtualnej z 5 lub więcej procesorów wirtualnych.

Współczynnik 5 lub więcej procesorów wirtualnych Vcpu jest 2.6. Dlatego Rezerwacja SUSE z maszyną Wirtualną z 5 lub więcej procesorów wirtualnych Vcpu obejmuje tylko część koszt oprogramowania, czyli około 77%.

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

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat rezerwacji, zobacz następujące artykuły:

- [Co to jest Azure rezerwacje?](billing-save-compute-costs-reservations.md)
- [Zapłać z góry za plany oprogramowania SUSE z zastrzeżeniami platformy Azure](../virtual-machines/linux/prepay-suse-software-charges.md)
- [Prepay for Virtual Machines with Azure Reserved VM Instances (Opłacanie maszyn wirtualnych z góry przy użyciu usługi Azure Reserved VM Instances)](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Zarządzanie usługą Azure Reservations](billing-manage-reserved-vm-instance.md)
- [Opis zastrzeżenia dla Twojej subskrypcji zgodnie z rzeczywistym użyciem](billing-understand-reserved-instance-usage.md)
- [Opis zastrzeżenia dla Twojej rejestracji Enterprise](billing-understand-reserved-instance-usage-ea.md)

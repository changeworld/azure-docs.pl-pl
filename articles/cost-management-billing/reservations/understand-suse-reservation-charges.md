---
title: Rabat za plan oprogramowania — Azure | Microsoft Docs
description: Dowiedz się, jak są stosowane rabaty za plany oprogramowania na maszynach wirtualnych.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 9979ced6d5bd71dd1a42d19c222111a76ed18428
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200235"
---
# <a name="azure-software-plan-discount"></a>Rabat za plan oprogramowania na platformie Azure

Plany oprogramowania SUSE i Red Hat na platformie Azure to rezerwacje stosowane do wdrożonych maszyn wirtualnych. Rabat za plan oprogramowania jest stosowany do użycia oprogramowania na wdrożonych maszynach wirtualnych pasujących do rezerwacji.

Po zamknięciu maszyny wirtualnej rabat zostanie automatycznie zastosowany do innej pasującej maszyny wirtualnej, o ile jest dostępna. Plan oprogramowania pokrywa koszt korzystania z oprogramowania na maszynie wirtualnej. Pozostałe opłaty, na przykład za obliczenia, magazyn i sieć, są naliczane osobno.

Aby kupić właściwy plan, musisz wiedzieć, jakich maszyn wirtualnych używasz i ile procesorów wirtualnych mają te maszyny wirtualne. Informacje w poniższych sekcjach pomogą Ci określić, jaki plan należy kupić, na podstawie danych o użyciu.

## <a name="how-reservation-discount-is-applied"></a>Jak jest naliczany rabat za rezerwację

Rabat na rezerwację jest dostępny na zasadzie „*wykorzystaj lub strać*”. Zatem jeśli w ciągu jakiejś godziny nie będziesz mieć pasujących zasobów, utracisz ilość rezerwacji dla tej godziny. Niewykorzystanych godzin zarezerwowanych nie można przenieść na później.

Po wyłączeniu zasobu rabat za rezerwację automatycznie stosuje się do innego pasującego zasobu w określonym zakresie. Jeśli w określonym zakresie nie uda się znaleźć pasujących zasobów, zarezerwowane godziny zostaną *utracone*.

## <a name="review-redhat-vm-usage-before-you-buy"></a>Sprawdzanie użycia maszyn wirtualnych z oprogramowaniem Red Hat przed zakupem

Sprawdź nazwę produktu w danych o użyciu i kup plan oprogramowania Red Hat z takim samym typem i rozmiarem.

Na przykład jeśli w danych o użyciu znajdziesz produkt **Red Hat Enterprise Linux — licencja na maszyny wirtualne — od 1 do 4 procesorów wirtualnych**, należy zakupić plan **Red Hat Enterprise Linux** dla **maszyny wirtualnej mającej od 1 do 4 procesorów wirtualnych**.

<!--ADD RHEL SCREENSHOT -->

## <a name="review-suse-vm-usage-before-you-buy"></a>Sprawdzanie użycia maszyn wirtualnych z oprogramowaniem SUSE przed zakupem

Sprawdź nazwę produktu w danych o użyciu i kup plan oprogramowania SUSE z takim samym typem i rozmiarem.

Na przykład jeśli w danych o użyciu znajdziesz produkt **SUSE Linux Enterprise Server — subskrypcja Priorytetowa — obsługa maszyn wirtualnych od 2 do 4 procesorów**, należy zakupić plan **SUSE Linux Enterprise Server — subskrypcja Priorytetowa** dla **2–4 procesorów wirtualnych**.

![Przykład wyboru produktu do zakupu](./media/understand-suse-reservation-charges/select-suse-linux-enterprise-server-priority-2-4-vcpu.png)

## <a name="discount-applies-to-different-vm-sizes-for-suse-plans"></a>Rabat jest stosowany do różnych rozmiarów maszyn wirtualnych w ramach planów oprogramowania SUSE

Podobnie jak wystąpienia zarezerwowane maszyn wirtualnych zakupione plany oprogramowania SUSE oferują elastyczne rozmiary wystąpień. To oznacza, że rabat zostanie zastosowany nawet wtedy, gdy wdrożysz maszynę wirtualną z inną liczbą procesorów wirtualnych. Rabat jest stosowany do maszyn wirtualnych o różnych rozmiarach w ramach planu oprogramowania.

Kwota rabatu zależy od współczynnika określonego w poniższych tabelach. Ten współczynnik porównuje względne rozmiary dla każdego miernika w tej grupie. Współczynnik zależy od liczby procesorów wirtualnych maszyny wirtualnej. Używając wartości współczynnika, możesz obliczyć, ile wystąpień maszyn wirtualnych może otrzymać rabat za plan oprogramowania SUSE Linux.

Na przykład w przypadku zakupu planu oprogramowania SUSE Linux Enterprise Server dla HPC — subskrypcja Priorytetowa dla maszyny wirtualnej z 3 lub 4 procesorami wirtualnymi współczynnik dla tej rezerwacji wynosi 2. Rabat obejmuje koszt oprogramowania SUSE dla:

- 2 wdrożonych maszyn wirtualnych mających 1–2 procesory wirtualne,
- 1 wdrożonej maszyny wirtualnej mającej 3–4 procesory wirtualne
- albo 0,77 lub około 77% maszyny wirtualnej mającej 5 lub więcej procesorów wirtualnych.

Współczynnik dla 5 lub więcej procesorów wirtualnych wynosi 2,6. Dlatego rezerwacja oprogramowania SUSE dla maszyny wirtualnej mającej 5 lub więcej procesorów wirtualnych pokrywa tylko część kosztu oprogramowania — około 77%.

W poniższych tabelach przedstawiono plany oprogramowania, dla których możesz kupić rezerwacje, odpowiednie mierniki użycia i współczynniki.

### <a name="suse-linux-enterprise-server-for-hpc-priority"></a>SUSE Linux Enterprise Server dla HPC — subskrypcja Priorytetowa

Nazwa na platformie Marketplace i w witrynie Azure Portal:

- SLES 12 SP3 dla HPC (subskrypcja Priorytetowa)

|Maszyna wirtualna z oprogramowaniem SUSE | Identyfikator miernika| Współczynnik| Przykładowy rozmiar maszyny wirtualnej|
| -------| ------------------------| --- |--- |
|SLES dla HPC — od 1 do 2 procesorów wirtualnych|e275a668-ce79-44e2-a659-f43443265e98|1|D2s_v3|
|SLES dla HPC — od 3 do 4 procesorów wirtualnych|e531e1c0-09c9-4d83-b7d0-a2c6741faa22|2|D4s_v3|
|SLES dla HPC — 5 lub więcej procesorów wirtualnych|4edcd5a5-8510-49a8-a9fc-c9721f501913|2,6|D8s_v3|

### <a name="suse-linux-enterprise-server-for-hpc-standard"></a>SUSE Linux Enterprise Server dla HPC — subskrypcja Standardowa

Nazwa na platformie Marketplace i w witrynie Azure Portal:

- SLES 12 SP3 dla HPC

|Maszyna wirtualna z oprogramowaniem SUSE | Identyfikator miernika | Współczynnik|Przykładowy rozmiar maszyny wirtualnej|
| ------- | --- | ------------------------| --- |
|SLES dla HPC — od 1 do 2 procesorów wirtualnych |8c94ad45-b93b-4772-aab1-ff92fcec6610|1|D2s_v3|
|SLES dla HPC — od 3 do 4 procesorów wirtualnych|4ed70d2d-e2bb-4dcd-b6fa-42da71861a1c|1,92308|D4s_v3|
|SLES dla HPC — 5 lub więcej procesorów wirtualnych |907a85de-024f-4dd6-969c-347d47a1bdff|2,92308|D8s_v3|

### <a name="suse-linux-enterprise-server-for-sap-priority"></a>SUSE Linux Enterprise Server dla SAP — subskrypcja Priorytetowa

Nazwy na platformie Marketplace i w witrynie Azure Portal:

- SLES for SAP 15 (subskrypcja Priorytetowa)
- SLES for SAP 12 SP3 (subskrypcja Priorytetowa)
- SLES for SAP 12 SP2 (subskrypcja Priorytetowa)

|Maszyna wirtualna z oprogramowaniem SUSE | Identyfikator miernika | Współczynnik|Przykładowy rozmiar maszyny wirtualnej|
| ------- |------------------------| --- | --- |
|SUSE Linux Enterprise Server dla SAP — subskrypcja Priorytetowa — od 1 do 2 procesorów wirtualnych|497fe0b6-fa3c-4e3d-a66b-836097244142|1|D2s_v3|
|SUSE Linux Enterprise Server dla SAP — subskrypcja Priorytetowa — od 3 do 4 procesorów wirtualnych |847887de-68ce-4adc-8a33-7a3f4133312f|2|D4s_v3|
|SUSE Linux Enterprise Server dla SAP — subskrypcja Priorytetowa — 5 lub więcej procesorów wirtualnych |18ae79cd-dfce-48c9-897b-ebd3053c6058|2,41176|D8s_v3|

### <a name="suse-linux-enterprise-server-priority"></a>SUSE Linux Enterprise Server — subskrypcja Priorytetowa

Nazwy na platformie Marketplace i w witrynie Azure Portal:

- SLES 15 (subskrypcja Priorytetowa)
- SLES 12 SP3 (subskrypcja Priorytetowa)
- SLES 11 SP4 (subskrypcja Priorytetowa)

|Maszyna wirtualna z oprogramowaniem SUSE | Identyfikator miernika | Współczynnik|Przykładowy rozmiar maszyny wirtualnej|
| ------- |------------------------| --- |--- |
|SLES — 1 procesor wirtualny|462cd632-ec6b-4663-b79f-39715f4e8b38|1|B1MS|
|SLES — od 2 do 4 procesorów wirtualnych |924bee71-5eb8-424f-83ed-a58823c33908|2|D4s_v3|
|SLES — od 2 do 4 procesorów wirtualnych |60b3ae9d-e77a-46b2-9cdf-92fa87407969|2|D4s_v3|
|SLES — 6 procesorów wirtualnych |e8862232-6131-4dbe-bde4-e2ae383afc6f|3||
|SLES — 8 procesorów wirtualnych |e11331a8-fd32-4e71-b60e-4de2a818c67a|3,2|D8s_v3|
|SLES — 12 rdzeni procesorów wirtualnych |a5afd00d-d3ef-4bcd-8b42-f158b2799782|3,2||
|SLES — 16 procesorów wirtualnych |bb21066f-fe46-46d3-8006-b326b1663e52|3,2| D16s_v3|
|SLES — 20 procesorów wirtualnych |c5228804-1de6-4bd4-a61c-501d9003acc8|3,2| |
|SLES — 24 rdzenie procesorów wirtualnych |-005d-4075-ac11-822ccde9e8f6|3,2| ND24s|
|SLES — 32 procesory wirtualne |180c1a0a-b0a5-4de3-a032-f92925a4bf90|3,2| D32s_v3|
|SLES — 40 rdzeni procesorów wirtualnych |a161d3d3-0592-4956-9b64-6829678b6506|3,2||
|SLES — 64 procesory wirtualne |7f5a36ed-d5b5-4732-b6bb-837dbf0fb9d8|3,2| D64s_v3|
|SLES — 72 rdzenie procesorów wirtualnych |93329a72-24d7-4faa-93d9-203f367ed334|3,2|F72s_v2|
|SLES — 96 rdzeni procesorów wirtualnych |2018c3a8-ff13-41f8-b64d-9558c5206547|3,2||
|SLES — 128 rdzeni procesorów wirtualnych |ac27e4d7-44b5-4fee-bc1a-78ac5b4abaf7|3,2| M128ms|

### <a name="suse-linux-enterprise-server-standard"></a>SUSE Linux Enterprise Server — subskrypcja Standardowa

Nazwy na platformie Marketplace i w witrynie Azure Portal:

- SLES 15
- SLES 15 (subskrypcja Standardowa)
- SLES 12 SP3 (subskrypcja Standardowa)

|Maszyna wirtualna z oprogramowaniem SUSE | Identyfikator miernika | Współczynnik|Przykładowy rozmiar maszyny wirtualnej|
| ------- |------------------------| --- |--- |
|SLES — od 1 do 2 rdzeni procesorów wirtualnych |4b2fecfc-b110-4312-8f9d-807db1cb79ae|1|D2s_v3|
|SLES — od 3 do 4 rdzeni procesorów wirtualnych |0c3ebb4c-db7d-4125-b45a-0534764d4bda|1,92308|D4s_v3|
|SLES — 5 lub więcej procesorów wirtualnych |7b349b65-d906-42e5-833f-b2af38513468|2,30769| D8s_v3|

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat rezerwacji, zobacz następujące artykuły:

- [Co to są rezerwacje platformy Azure?](save-compute-costs-reservations.md)
- [Opłacanie planów oprogramowania SUSE z góry dzięki rezerwacjom platformy Azure](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Prepay for Virtual Machines with Azure Reserved VM Instances (Opłacanie maszyn wirtualnych z góry przy użyciu usługi Azure Reserved VM Instances)](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Zarządzanie usługą Azure Reservations](manage-reserved-vm-instance.md)
- [Understand reservation usage for your Pay-As-You-Go subscription (Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](understand-reserved-instance-usage.md)
- [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](understand-reserved-instance-usage-ea.md)

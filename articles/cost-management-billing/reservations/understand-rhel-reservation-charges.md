---
title: Zniżki w ramach planu rezerwacji Red Hat — Azure
description: Dowiedz się, jak są stosowane rabaty za plany oprogramowania Red Hat na maszynach wirtualnych.
author: yashesvi
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 173967356c17150d5694b48e6ed2acd78155ca4b
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199232"
---
# <a name="understand-how-the-red-hat-linux-enterprise-software-reservation-plan-discount-is-applied-for-azure"></a>Dowiedz się, jak na platformie Azure jest stosowany rabat za plany rezerwacji oprogramowania Red Hat Linux Enterprise

Po zakupie planu oprogramowania Red Hat Linux rabat jest automatycznie stosowany do wdrożonych maszyn wirtualnych z oprogramowaniem Red Hat pasujących do rezerwacji. Plan oprogramowania Red Hat Linux pokrywa koszt korzystania z oprogramowania Red Hat na maszynie wirtualnej platformy Azure.

Aby kupić właściwy plan oprogramowania Red Hat Linux, musisz wiedzieć, jakie maszyny wirtualne z oprogramowaniem Red Hat masz uruchomione i ile procesorów wirtualnych mają te maszyny wirtualne. Informacje w poniższych sekcjach pomogą Ci określić, jaki plan należy kupić, na podstawie pliku CSV z danymi o użyciu.

## <a name="discount-applies-to-different-vm-sizes"></a>Rabat ma zastosowanie do różnych rozmiarów maszyn wirtualnych

Podobnie jak wystąpienia zarezerwowane maszyn wirtualnych zakupione plany oprogramowania Red Hat oferują elastyczne rozmiary wystąpień. To oznacza, że rabat zostanie zastosowany nawet wtedy, gdy wdrożysz maszynę wirtualną z inną liczbą procesorów wirtualnych. Rabat jest stosowany do maszyn wirtualnych o różnych rozmiarach w ramach planu oprogramowania.

Kwota rabatu zależy od współczynnika określonego w poniższych tabelach. Ten współczynnik porównuje względne rozmiary dla każdego miernika w tej grupie. Współczynnik zależy od liczby procesorów wirtualnych maszyny wirtualnej. Używając wartości współczynnika, możesz obliczyć, ile wystąpień maszyn wirtualnych może otrzymać rabat za plan oprogramowania Red Hat Linux.

Na przykład w przypadku zakupu planu oprogramowania Red Hat Linux Enterprise Server dla maszyny wirtualnej z 3 lub 4 procesorami wirtualnymi współczynnik dla tej rezerwacji wynosi 2. Rabat obejmuje koszt oprogramowania Red Hat dla:

- 2 wdrożonych maszyn wirtualnych mających 1–2 procesory wirtualne,
- 1 wdrożonej maszyny wirtualnej mającej 3–4 procesory wirtualne
- albo 0,77 lub około 77% maszyny wirtualnej mającej 5 lub więcej procesorów wirtualnych.

Współczynnik dla 5 lub więcej procesorów wirtualnych wynosi 2,6. Dlatego rezerwacja oprogramowania Red Hat dla maszyny wirtualnej mającej 5 lub więcej procesorów wirtualnych pokrywa tylko część kosztu oprogramowania — około 77%.

## <a name="understand-red-hat-vm-usage-before-you-buy"></a>Określanie użycia maszyn wirtualnych z oprogramowaniem Red Hat przed zakupem

W poniższych tabelach przedstawiono plany oprogramowania, dla których możesz kupić rezerwacje, odpowiednie mierniki użycia i współczynniki.

### <a name="red-hat-enterprise-linux"></a>Red Hat Enterprise Linux

Nazwy na platformie Marketplace i w witrynie Azure Portal:

- Red Hat Enterprise Linux 6.7
- Red Hat Enterprise Linux 6.8
- Red Hat Enterprise Linux 6.9
- Red Hat Enterprise Linux 6.10
- Red Hat Enterprise Linux 7.2
- Red Hat Enterprise Linux 7.3
- Red Hat Enterprise Linux 7.4
- Red Hat Enterprise Linux 7.5
- Red Hat Enterprise Linux 7.6
- Red Hat Enterprise Linux 7 (najnowsza maszyna wirtualna z systemem Linux)

|Maszyna wirtualna z systemem Red Hat | Identyfikator miernika| Współczynnik| Przykładowy rozmiar maszyny wirtualnej|
| -------| ------------------------| --- |--- |
|Licencja na maszyny wirtualne — od 1 do 4 procesorów wirtualnych|077a07bb-20f8-4bc6-b596-ab7211a1e247|1|D4s_v3|
|Licencja na maszyny wirtualne — od 1 do 4 procesorów wirtualnych|2f96d035-3bac-46d6-b2bc-c6daa0938536|1|D4s_v3|
|Licencja na maszyny wirtualne — od 1 do 4 procesorów wirtualnych|4831a7b4-bdd4-48a2-8e95-18d053971ede|1|D4s_v3|
|Licencja na maszyny wirtualne — 5 lub więcej procesorów wirtualnych|291b2cbc-6c34-4e2b-a4e4-1ff8c106f672|2,166666667|D8s_v3|
|Licencja na maszyny wirtualne — 5 lub więcej procesorów wirtualnych|3b6661c4-03dd-45e7-88c9-512fcb7906d5|2,166666667|D8s_v3|
|Licencja na maszyny wirtualne — 5 lub więcej procesorów wirtualnych|037eddc0-fedd-4d73-b5d8-92fba9edb831|2,166666667|D8s_v3|
|Licencja na maszyny wirtualne — 5 lub więcej procesorów wirtualnych|432cdeee-4034-4ddf-9ba4-9250a19b0d5f|2,166666667|D8s_v3|
|Licencja na maszyny wirtualne — 5 lub więcej procesorów wirtualnych|794dcb90-0793-43e6-9909-70d29974e56d|2,166666667|D8s_v3|
|Licencja na maszyny wirtualne — 5 lub więcej procesorów wirtualnych|86b5b0b4-3c19-4720-82e9-874f8c58b48e|2,166666667|D8s_v3|
|Licencja na maszyny wirtualne — 5 lub więcej procesorów wirtualnych|86c35ec3-0a48-426a-9625-22d80e6ea55b|2,166666667|D8s_v3|
|Licencja na maszyny wirtualne — 5 lub więcej procesorów wirtualnych|8b698c7a-47f1-4cba-8ae1-9853d5ad562d|2,166666667|D8s_v3|
|Licencja na maszyny wirtualne — 5 lub więcej procesorów wirtualnych|a4daffb4-96f4-4fc5-b1e6-fd3a2cf3595e|2,166666667|D8s_v3|
|Licencja na maszyny wirtualne — 5 lub więcej procesorów wirtualnych|a838cfb1-0bd3-4965-84f0-663f49afc2e2|2,166666667|D8s_v3|
|Licencja na maszyny wirtualne — 5 lub więcej procesorów wirtualnych|99aed7b9-a0a9-4783-b90c-be7c2f3c7e30|2,166666667|D8s_v3|
|Licencja na maszyny wirtualne — 5 lub więcej procesorów wirtualnych|d09f877e-03b4-48b2-b11a-782b965cff19|2,166666667|D8s_v3|
|Licencja na maszyny wirtualne — 44 procesory wirtualne|6f44ae85-a70e-44be-83ec-153a0bc23979|2,166666667||
|Licencja na maszyny wirtualne — 60 procesorów wirtualnych|b9edcc5b-a429-4778-bc5a-82e7fa07fe55|2,166666667||

### <a name="red-hat-enterprise-linux-for-sap-with-ha"></a>Red Hat Enterprise Linux for SAP z wysoką dostępnością

Nazwa na platformie Marketplace i w witrynie Azure Portal:

|Maszyna wirtualna z systemem Red Hat | Identyfikator miernika | Współczynnik|Przykładowy rozmiar maszyny wirtualnej|
| ------- | --- | ------------------------| --- | --- |
|Licencja na maszyny wirtualne — od 1 do 4 procesorów wirtualnych |4d902611-eed7-4060-a33e-3c7fdbac6406|1|D4s_v3|
|Licencja na maszyny wirtualne — 5 lub więcej procesorów wirtualnych|6dfb482b-23ea-487f-810c-e66360f025de|2,333333333|D8s_v3|

### <a name="red-hat-enterprise-linux-with-ha"></a>Red Hat Enterprise Linux z wysoką dostępnością

Nazwy na platformie Marketplace i w witrynie Azure Portal:

|Maszyna wirtualna z systemem Red Hat | Identyfikator miernika | Współczynnik|Przykładowy rozmiar maszyny wirtualnej|
| ------- |------------------------| --- | --- |
|Licencja na maszyny wirtualne — od 1 do 4 procesorów wirtualnych|e9711132-d9d9-450c-8203-25cfc4bce8de|1|D4s_v3|
|Licencja na maszyny wirtualne — 5 lub więcej procesorów wirtualnych|93954aa4-b55f-4b7b-844d-a119d6bf3c4e|2|D8s_v3|

### <a name="rhel-for-sap-business-applications"></a>RHEL for SAP Business Applications

Nazwy na platformie Marketplace i w witrynie Azure Portal:

- Red Hat Enterprise Linux 6.8 for SAP Business Apps
- Red Hat Enterprise Linux 7.3 for SAP Business Apps
- Red Hat Enterprise Linux 7.4 for SAP
- Red Hat Enterprise Linux 7.5 for SAP

|Maszyna wirtualna z systemem Red Hat | Identyfikator miernika | Współczynnik|Przykładowy rozmiar maszyny wirtualnej|
| ------- |------------------------| --- |--- |
|Licencja na maszyny wirtualne — 1 procesor wirtualny|25889e91-c740-42ac-bc52-6b8f73b98575|1|D2s_v3|
|Licencja na maszyny wirtualne — 2 procesory wirtualne|2a0c92c8-23a7-4dc9-a39c-c4a73a85b5da|1|D2s_v3|
|Licencja na maszyny wirtualne — 4 procesory wirtualne|875898d3-3639-423c-82c1-38846281b7e8|1|D4s_v3|
|Licencja na maszyny wirtualne — 6 procesorów wirtualnych|69a140fa-e08e-415c-85f2-48158e4c73a0|2,166666667||
|Licencja na maszyny wirtualne — 8 procesorów wirtualnych|777a5a74-22d6-48c9-9705-ac38fe05a278|2,166666667|D8s_v3|
|Licencja na maszyny wirtualne — 12 procesorów wirtualnych|d6b8917a-5127-497a-9f48-1e959df98812|2,166666667||
|Licencja na maszyny wirtualne — 16 procesorów wirtualnych|03667e82-e009-425a-83f7-8ebddbca5af4|2,166666667|D16s_v3|
|Licencja na maszyny wirtualne — 20 procesorów wirtualnych|bbd65e5b-35f1-42be-b86d-6625fbc1f1a4|2,166666667||
|Licencja na maszyny wirtualne — 24 procesory wirtualne|c2c07d3e-a7d0-400b-8832-b532bfd0be25|2,166666667|ND24s|
|Licencja na maszyny wirtualne — 32 procesory wirtualne|633d1494-5ec1-46f0-a742-eaf58eeaec7e|2,166666667|D32s_v3|
|Licencja na maszyny wirtualne — 40 procesorów wirtualnych|737142c3-8e4f-4fc1-aa41-05b1661edff8|2,166666667||
|Licencja na maszyny wirtualne — 44 procesory wirtualne|722bda73-a8c8-4d04-b96b-541f0bb6c0c4|2,166666667||
|Licencja na maszyny wirtualne — 60 procesorów wirtualnych|a22bb342-BA9A-4529-a178-39a92ce770b6|2,166666667||
|Licencja na maszyny wirtualne — 64 procesory wirtualne|d37c8e17-e5f2-4060-881b-080dd4a8c4ce|2,166666667|64s_v3|
|Licencja na maszyny wirtualne — 72 procesory wirtualne|14341b96-e92c-4dca-ba66-322c88a79aa6|2,166666667|F72s_v2|
|Licencja na maszyny wirtualne — 96 procesorów wirtualnych|8b2e5cb8-0362-4cbf-a30a-115e8d6dbc49|2,166666667||
|Licencja na maszyny wirtualne — 128 procesorów wirtualnych|9b198a68-974a-47a7-9013-49169ac0f2e9|2,166666667| M128ms|

### <a name="rhel-for-sap-hana"></a>RHEL for SAP HANA

Nazwy na platformie Marketplace i w witrynie Azure Portal:

- Red Hat Enterprise Linux 6.7 for SAP HANA
- Red Hat Enterprise Linux 7.2 for SAP HANA
- Red Hat Enterprise Linux 7.3 for SAP HANA

|Maszyna wirtualna z systemem Red Hat | Identyfikator miernika | Współczynnik|Przykładowy rozmiar maszyny wirtualnej|
| ------- |------------------------| --- |--- |
|Licencja na maszyny wirtualne — 1 procesor wirtualny|be0a59d1-eed7-47ec-becd-453267753793|1|D2s_v3|
|Licencja na maszyny wirtualne — 2 procesory wirtualne|3b97c9f5-f5d5-4fd3-a421-b78fca32a656|1|D2s_v3|
|Licencja na maszyny wirtualne — 4 procesory wirtualne|b39feb58-57bf-40f2-8193-f4fe9ac3dda3|1|D4s_v3|
|Licencja na maszyny wirtualne — 6 procesorów wirtualnych|a5963812-0f5a-4053-8ace-2b5babd15ed8|2,166666667||
|Licencja na maszyny wirtualne — 8 procesorów wirtualnych|5460ab4d-ce9a-46af-8ad5-ca5e53d715b5|2,166666667|D8s_v3|
|Licencja na maszyny wirtualne — 12 procesorów wirtualnych|0e3bc72d-a888-4bcf-8437-119f763a3215|2,166666667||
|Licencja na maszyny wirtualne — 16 procesorów wirtualnych|b40e95d8-3176-42f0-967c-497785c031b2|2,166666667|D16s_v3|
|Licencja na maszyny wirtualne — 20 procesorów wirtualnych|81f34277-499d-40a3-a634-99adc08e2d45|2,166666667||
|Licencja na maszyny wirtualne — 24 procesory wirtualne|e03f1906-d35d-4084-b2cd-63281869c8ee|2,166666667|ND24s|
|Licencja na maszyny wirtualne — 32 procesory wirtualne|0a58c082-ceb8-4327-9b64-887c30dddb23|2,166666667|D32s_v3|
|Licencja na maszyny wirtualne — 40 procesorów wirtualnych|a14225c0-04e6-4669-974f-e2ddd61a9c5b|2,166666667||
|Licencja na maszyny wirtualne — 44 procesory wirtualne|378b8125-d8a5-4e09-99bc-c1462534ffb0|2,166666667||
|Licencja na maszyny wirtualne — 60 procesorów wirtualnych|5d7db11a-54e9-404e-aaa8-509fac7c0638|2,166666667||
|Licencja na maszyny wirtualne — 64 procesory wirtualne|3c8157b2-a57d-45ce-ba02-bd86e9209795|2,166666667|64s_v3|
|Licencja na maszyny wirtualne — 72 procesory wirtualne|5e87a3ee-7afb-4040-b8d9-b109ddb38f31|2,166666667|F72s_v2|
|Licencja na maszyny wirtualne — 96 procesorów wirtualnych|b13895fc-0d06-4de9-b860-627c471cd247|2,166666667||
|Licencja na maszyny wirtualne — 128 procesorów wirtualnych|6e67ac0b-19d3-4289-96df-05d0093d4b3b|2,166666667| M128ms|

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat rezerwacji, zobacz następujące artykuły:

- [Co to są rezerwacje platformy Azure](save-compute-costs-reservations.md)
- [Opłacanie planów oprogramowania Red Hat z góry dzięki rezerwacjom platformy Azure](../../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Prepay for Virtual Machines with Azure Reserved VM Instances (Opłacanie maszyn wirtualnych z góry przy użyciu usługi Azure Reserved VM Instances)](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Zarządzanie rezerwacjami platformy Azure](manage-reserved-vm-instance.md)
- [Understand reservation usage for your Pay-As-You-Go subscription (Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](understand-reserved-instance-usage.md)
- [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 09/18/2019
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: 3aeb0369ee4a04dbbe89a4d0684b2a6c97378d13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77123197"
---
Usługa Azure Compute oferuje rozmiary maszyn wirtualnych, które są izolowane do określonego typu sprzętu i dedykowane jednemu klientowi.  Te rozmiary maszyn wirtualnych najlepiej nadają się do obciążeń, które wymagają wysokiego stopnia izolacji od innych klientów dla obciążeń obejmujących elementy, takie jak zgodność i wymagania prawne.  Klienci mogą również zdecydować się na dalsze podział zasobów tych izolowanych maszyn wirtualnych przy użyciu [obsługi platformy Azure dla zagnieżdżonych maszyn wirtualnych.](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)

Korzystanie z izolowanego rozmiaru gwarantuje, że maszyna wirtualna będzie jedyną maszyną działającą w tym wystąpieniu serwera określonego.  Bieżące oferty izolowanej maszyny wirtualnej obejmują:
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standardowa_GS5
* Standard_G5
* Standard_DS15_v2
* Standard_D15_v2
* Standard_F72s_v2

Możesz dowiedzieć się więcej o każdym dostępnym izolowanym [rozmiarze tutaj](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory).

## <a name="retiring-d15_v2ds15_v2-isolation-on-may-15-2020"></a>Przejście na emeryturę D15_v2/DS15_v2 izolacji w dniu 15 maja 2020 r.
**Aktualizacja 10 lutego 2020 r.: Termin "izolacji" emerytur został przedłużony do 15 maja 2020 r."**

Usługa Azure Dedicated Host jest teraz ga, który umożliwia uruchamianie maszyn wirtualnych sieci Linux i Windows w organizacji na serwerach fizycznych jednej dzierżawy. Planujemy w pełni zastąpić izolowane maszyny wirtualne platformy Azure za pomocą dedykowanego hosta platformy Azure. Po **15 maja 2020** r. maszyny wirtualne platformy Azure D15_v2/DS15_v2 nie będą już izolowane sprzętowo.

## <a name="how-does-this-affect-me"></a>Jak to wpłynie na mnie?
Po 15 maja 2020 r. nie będziemy już dostarczać gwarancji izolacji dla maszyn wirtualnych platformy Azure D15_v2/DS15_v2. 

## <a name="what-actions-should-i-take"></a>Jakie działania należy podjąć?
Jeśli izolacja sprzętu nie jest wymagana dla Ciebie, nie ma żadnych działań, które należy podjąć. 

Jeśli wymagana jest izolacja, przed 15 maja 2020 r., musisz:

• [Migrowanie](https://azure.microsoft.com/blog/introducing-azure-dedicated-host) obciążenia do hosta dedykowanego platformy Azure.

• [Poproś o dostęp](https://aka.ms/D15iRequestAccess) do D15i_v2 i DS15i_v2 maszyny Wirtualnej platformy Azure, aby uzyskać taką samą wydajność cenową. Ta opcja jest dostępna tylko w scenariuszach wystąpienia płatności zgodnie z rzeczywistym użyciem i jednoroczne zarezerwowane.    

• [Migrowanie](https://azure.microsoft.com/blog/resize-virtual-machines/) obciążenia do innej izolowanej maszyny wirtualnej platformy Azure. 

Szczegółowe informacje można znaleźć poniżej:

## <a name="timeline"></a>Oś czasu
| Data | Akcja | 
| --- | --- |
| 18 listopada 2019 r. | Dostępność D/DS15i_v2 (PAYG, 1-letni RI) |
| 14 maja 2020 r.  | Ostatni dzień na zakup D/DS15i_v2 1-letni RI | 
| 15 maja 2020 r.   | D/DS15_v2 gwarancja izolacji usunięta | 
| 15 maja 2021 r.  | Wycofaj D/DS15i_v2 (wszyscy klienci z wyjątkiem osób, które kupiły 3-letnią ri d/DS15_v2 przed 18 listopada 2019 r.)| 
| 17 listopada 2022 r.  | Wycofaj D/DS15i_v2 po wykonaniu 3-letnich RI (dla klientów, którzy kupili 3-letni ą RI d/DS15_v2 przed 18 listopada 2019 r.) | 

## <a name="faq"></a>Najczęściej zadawane pytania
### <a name="q-is-the-size-dds15_v2-going-to-get-retired"></a>P: Czy rozmiar D/ DS15_v2 zostanie wycofany?
**A:** Nie, tylko funkcja "izolacji" zostanie wycofana. Jeśli nie potrzebujesz izolacji, nie musisz podejmować żadnych działań.

### <a name="q-is-the-size-dds15i_v2-going-to-get-retired"></a>P: Czy rozmiar D/ DS15i_v2 zostanie wycofany?
**Odp**.: Tak, rozmiar jest dostępny tylko do 15 maja 2021 roku. Klienci, którzy wykupili 3-letnie ri na D/DS15_v2 przed 18 listopada 2019 r., będą mieli dostęp do D/DS15i_v2 do 17 listopada 2022 r.

### <a name="q-why-am-i-not-seeing-the-new-dds15i_v2-sizes-in-the-portal"></a>P: Dlaczego nie widzę nowych rozmiarów D/DS15i_v2 w portalu?
**A:** Jeśli jesteś aktualnym klientem D/DS15_v2 i chcesz użyć nowych rozmiarów D/DS15i_v2, wypełnij ten [formularz](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUMkZUWEFPNjFPNVgyMkhZS05FSzlPTzRIOS4u)

### <a name="q-why-i-am-not-seeing-any-quota-for-the-new-dds15i_v2-sizes"></a>Pyt.: Dlaczego nie widzę żadnego przydziału dla nowych rozmiarów D/DS15i_v2?
**A:** Jeśli jesteś aktualnym klientem D/DS15_v2 i chcesz użyć nowych rozmiarów D/DS15i_v2, wypełnij ten [formularz](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUNU1XUkhZWkNXQUFMNEJWUk9VWkRRVUJPMy4u)

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>P: Kiedy inne pojedyncze rozmiary przejdą na emeryturę?
**Odp**.: Będziemy dostarczać przypomnienia 12 miesięcy przed oficjalną likwidacją rozmiarów.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>Pyt.: Czy istnieje przestój, gdy moja maszyna wirtualna ląduje na nieizolowanym sprzęcie?
**Odp**.: Jeśli nie potrzebujesz izolacji, nie musisz podejmować żadnych działań i nie zobaczysz żadnych przestojów.

### <a name="q-are-there-any-cost-changes-for-moving-to-a-non-isolated-virtual-machine"></a>Pyt.: Czy są jakieś zmiany kosztów dla przejścia do maszyny wirtualnej nieizolowanej?
**A**: Nie 

### <a name="q-i-already-purchased-1--or-3-year-reserved-instance-for-d15_v2-or-ds15_v2-how-will-the-discount-be-applied-to-my-vm-usage"></a>P: Zakupiłem już 1- lub 3-letnie wystąpienie zarezerwowane na D15_v2 lub Ds15_v2. W jaki sposób rabat zostanie zastosowany do użycia mojej maszyny Wirtualnej?
**A:** RIs zakupione przed 18 listopada 2019 automatycznie rozszerzy zasięg do nowej izolowanej serii maszyn wirtualnych. 

| RI |  Elastyczność rozmiaru wystąpienia | Uprawnienie do świadczenia |   
| --- | --- | --- |
|   D15_v2  |   Wyłączone     |   D15_v2 i D15i_v2 |    
|   D15_v2  |   Włączone  |   D15_v2 serii i D15i_v2 otrzymają świadczenie RI. |    
|   D14_v2  |   Włączone  |   D15_v2 serii i D15i_v2 otrzymają świadczenie RI. |    
 
Podobnie dla serii Dsv2.
 
### <a name="q-i-want-to-purchase-additional-reserved-instances-for-dv2-which-one-should-i-choose"></a>P: Chcę kupić dodatkowe wystąpienia zarezerwowane dla Dv2. Który z nich wybrać?
**Odp**.: Wszystkie RZS zakupione po 18 listopada 2019 r., mają następujące zachowanie. 

| RI |  Elastyczność rozmiaru wystąpienia | Uprawnienie do świadczenia |   
| --- | --- | --- |
| D15_v2 |  Wyłączone |   tylko D15_v2  
| D15_v2 |  Włączone |    D15_v2 serii otrzymają świadczenie RI. Nowy D15i_v2 nie będzie kwalifikować się do korzystania z RI z tego typu R. | 
| D15i_v2 |     Wyłączone | tylko D15i_v2 |  
| D15i_v2 |     Włączone  | tylko D15i_v2 | 
 
Elastyczność rozmiaru wystąpienia nie może być stosowana do innych rozmiarów, takich jak D2_v2, D4_v2 lub D15_v2. Podobnie dla serii Dsv2.  
 
### <a name="q-can-i-buy-a-new-3-year-ri-for-d15i_v2-and-ds15i_v2"></a>P: Czy mogę kupić nową 3-letnią ri dla D15i_v2 i DS15i_v2?
**A:** Niestety nie, tylko 1-letni RI jest dostępny do nowego zakupu.
 
### <a name="q-can-i-move-my-existing-d15_v2ds15_v2-reserve-instance-to-an-isolated-size-reserved-instance"></a>Pyt.: Czy mogę przenieść moje istniejące wystąpienie rezerwy D15_v2/DS15_v2 do wystąpienia zastrzeżonego o izolowanym rozmiarze?
**A:** Działanie to nie jest konieczne, ponieważ świadczenie będzie miało zastosowanie zarówno do pojedynczych, jak i nieizolowanych rozmiarów. Platforma Azure będzie jednak obsługiwać zmianę istniejących D15_v2/DS15_v2 wystąpieniach zarezerwowanych na D15i_v2/DS15i_v2. W przypadku wszystkich innych wystąpień zarezerwowanych dv2/Dsv2 użyj istniejącego wystąpienia zarezerwowanego lub kup nowe wystąpienia zarezerwowane dla izolowanych rozmiarów.

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>Pyt.: Jestem klientem sieci szkieletowej usług Azure, który polega na warstwach Silver lub Gold Durability. Czy ta zmiana ma na mnie wpływ?
**Odp.:** Nie. Gwarancje zapewniane przez [warstwy trwałości](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) sieci szkieletowej usług będą nadal działać nawet po tej zmianie. Jeśli wymagana jest fizyczna izolacja sprzętowa z innych powodów, może być konieczne podjęcie jednej z opisanych powyżej czynności. 

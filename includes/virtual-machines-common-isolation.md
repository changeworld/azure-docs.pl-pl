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
ms.openlocfilehash: 2251725597e44a15e421f33cc315be3fb1c7846f
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74485512"
---
Usługa Azure Compute oferuje różne rozmiary maszyn wirtualnych, które są izolowane pod kątem konkretnego rodzaju sprzętu i przeznaczone dla jednego klienta.  Te rozmiary maszyn wirtualnych są dostosowane do obciążeń wymagających wysokiego stopnia izolacji od innych klientów, a zwłaszcza obciążeń związanych z takimi elementami jak zgodność z przepisami i wymogi prawne.  Klienci mogą również dodatkowo podzielić zasoby tych izolowanych maszyn wirtualnych, korzystając z [pomocy technicznej platformy Azure dla zagnieżdżonych maszyn wirtualnych](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).

Wykorzystanie wyizolowanego rozmiaru gwarantuje, że Twoja maszyna wirtualna będzie jedyną uruchomioną w tym konkretnym wystąpieniu serwera.  Obecnie odizolowane oferty maszyn wirtualnych obejmują:
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2
* Standard_D15_v2
* Standard_F72s_v2

Więcej informacji o każdym dostępnym rozmiarze izolowanym można znaleźć [tutaj](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory).

## <a name="retiring-d15_v2ds15_v2-isolation-on-february-15-2020"></a>Wycofanie izolacji D15_v2/DS15_v2 15 lutego 2020
Niedawno zapowiedziano w wersji zapoznawczej dedykowanego hosta platformy Azure, który umożliwia uruchamianie maszyn wirtualnych z systemem Linux i Windows w organizacji na serwerach fizycznych z jedną dzierżawą. Planujemy w pełni zastąpić izolowane maszyny wirtualne platformy Azure za pomocą dedykowanego hosta platformy Azure. Po **15 lutego 2020** maszyny wirtualne D15_v2/DS15_v2 platformy Azure nie będą już izolowane sprzętowo.

## <a name="how-does-this-affect-me"></a>Jak to wpłynie na mnie?
Po 15 lutego 2020 nie będą już udzielane gwarancje izolacji dla D15_v2/DS15_v2 maszyn wirtualnych platformy Azure. 

## <a name="what-actions-should-i-take"></a>Jakie akcje należy wykonać?
Jeśli izolacja sprzętowa nie jest wymagana, nie trzeba wykonywać żadnych czynności. 

Jeśli dla Ciebie wymagana jest izolacja, przed 15 lutego 2020 należy:

• [Migrowanie](https://azure.microsoft.com/blog/introducing-azure-dedicated-host) obciążenia do dedykowanego hosta platformy Azure w wersji zapoznawczej

• [Zażądaj dostępu](https://aka.ms/D15iRequestAccess) do D15i_v2 i DS15i_v2 maszyny wirtualnej platformy Azure, aby uzyskać tę samą wydajność cenową. Ta opcja jest dostępna tylko w przypadku scenariuszy z opcją płatność zgodnie z rzeczywistym użyciem i jednoletnim scenariuszem wystąpienia zarezerwowanego.    

• [Migruj](https://azure.microsoft.com/blog/resize-virtual-machines/) obciążenie na inną maszynę wirtualną izolowaną od platformy Azure. 

Aby uzyskać szczegółowe informacje, zobacz:

## <a name="timeline"></a>Oś czasu
| Date | Akcja | 
| --- | --- |
| Lis 18, 2019  | Dostępność D/DS15i_v2 (PAYG, 1 – rok RI) |
| 14 lutego 2020  | Ostatni dzień zakupu D/DS15i_v2 1 roku RI | 
| 15 lutego 2020   | Gwarancja izolacji D/DS15_v2 usunięta | 
| 15 maja 2021  | Wycofaj D/DS15i_v2 (wszyscy klienci, z wyjątkiem sytuacji, w których zakupił 3-letni RI) D/DS15_v2 przed 18 listopada, 2019)| 
| Lis 17, 2022   | Wycofaj D/DS15i_v2 po 3-letnich usługach RIs (w przypadku klientów, którzy kupili 3-letnie RI) D/DS15_v2 przed 18 listopada, 2019) | 

## <a name="faq"></a>Często zadawane pytania
### <a name="q-why-am-i-not-seeing-the-new-dds15i_v2-sizes-in-the-portal"></a>P: Dlaczego nie widzę nowych rozmiarów D/DS15i_v2 w portalu?
Odp **.: Jeśli**jesteś bieżącym klientem d/DS15_v2 i chcesz użyć nowych rozmiarów d/DS15i_v2, Wypełnij ten [formularz](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUMkZUWEFPNjFPNVgyMkhZS05FSzlPTzRIOS4u)

### <a name="q-why-i-am-not-seeing-any-quota-for-the-new-dds15i_v2-sizes"></a>P: Dlaczego nie widzę żadnego przydziału dla nowych rozmiarów D/DS15i_v2?
Odp **.: Jeśli**jesteś bieżącym klientem d/DS15_v2 i chcesz użyć nowych rozmiarów d/DS15i_v2, Wypełnij ten [formularz](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUNU1XUkhZWkNXQUFMNEJWUk9VWkRRVUJPMy4u)

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>P: Kiedy inne izolowane rozmiary są wycofywane?
Odp **.: będziemy**podawać przypomnienia 12 miesięcy przed oficjalną likwidacją rozmiarów.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>P: czy w przypadku maszyn wirtualnych znajdujących się na innym sprzęcie nie jest wyizolowany?
Odp **.: Jeśli**nie potrzebujesz izolacji, nie musisz podejmować żadnych działań i nie widzisz żadnych przestojów.

### <a name="q-are-there-any-cost-changes-for-moving-to-a-non-isolated-virtual-machine"></a>P: czy istnieją jakiekolwiek zmiany kosztów dotyczące przechodzenia na nieizolowaną maszynę wirtualną?
Odp **.:** nie 

### <a name="q-i-already-purchased-1--or-3-year-reserved-instance-for-d15_v2-or-ds15_v2-how-will-the-discount-be-applied-to-my-vm-usage"></a>P: zakupione zostało już 1-lub 3-letnie wystąpienie zarezerwowane dla D15_v2 lub Ds15_v2. W jaki sposób rabat zostanie zastosowany do użycia maszyny wirtualnej?
Odp **.: usługi**RIs zakupione przed 18 listopada, 2019 automatycznie rozszery zapotrzebowanie na nową wyizolowaną serię maszyn wirtualnych. 

| WIĘZ |  Elastyczność rozmiaru wystąpienia | Uprawnienie do korzyści |   
| --- | --- | --- |
|   D15_v2  |   Wyłączone     |   D15_v2 i D15i_v2 |    
|   D15_v2  |   Włączone  |   W przypadku serii D15_v2 i D15i_v2 zostanie wyświetlona korzyść z wystąpienia zarezerwowanego. |    
|   D14_v2  |   Włączone  |   W przypadku serii D15_v2 i D15i_v2 zostanie wyświetlona korzyść z wystąpienia zarezerwowanego. |    
 
Podobnie w przypadku serii Dsv2.
 
### <a name="q-i-want-to-purchase-additional-reserved-instances-for-dv2-which-one-should-i-choose"></a>P: chcę zakupić dodatkowe zarezerwowane wystąpienia dla Dv2. Który z nich wybrać?
Odp **.: wszystkie**usługi RIs zakupione po 18 listopada 2019 mają następujące zachowanie. 

| WIĘZ |  Elastyczność rozmiaru wystąpienia | Uprawnienie do korzyści |   
| --- | --- | --- |
| D15_v2 |  Wyłączone |   Tylko D15_v2  
| D15_v2 |  Włączone |    W przypadku serii D15_v2 zostanie wyświetlona korzyść z wystąpienia zarezerwowanego. Nowy D15i_v2 nie będzie uprawniony do skorzystania z korzyści z wystąpienia zarezerwowanego z tego typu. | 
| D15i_v2 |     Wyłączone | Tylko D15i_v2 |  
| D15i_v2 |     Włączone  | Tylko D15i_v2 | 
 
Nie można użyć elastyczności rozmiaru wystąpienia do zastosowania do innych rozmiarów, takich jak D2_v2, D4_v2 lub D15_v2. Podobnie w przypadku serii Dsv2.  
 
### <a name="q-can-i-buy-a-new-3-year-ri-for-d15i_v2-and-ds15i_v2"></a>P: Czy można kupić nowy 3-letni RI dla D15i_v2 i DS15i_v2?
Odp **.: nie**, tylko jeden rok RI jest dostępny do nowego zakupu.
 
### <a name="q-can-i-move-my-existing-d15_v2ds15_v2-reserve-instance-to-an-isolated-size-reserved-instance"></a>P: Czy można przenieść istniejące wystąpienie rezerwy D15_v2/DS15_v2 do wystąpienia zarezerwowanego o rozmiarze wydzielonym?
Odp.: nie jest to **konieczne, ponieważ**korzyść dotyczy zarówno rozmiarów izolowanych, jak i nieizolowanych. Jednak platforma Azure będzie obsługiwać Zmienianie istniejących D15_v2/DS15_v2 zarezerwowanych na D15i_v2/DS15i_v2. Dla wszystkich innych zarezerwowanych wystąpień Dv2/Dsv2 Użyj istniejącego wystąpienia zarezerwowanego lub Kup nowe wystąpienia zarezerwowane dla rozmiarów izolowanych.

### <a name="q-im-a-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>Pytanie: jestem klientem Service Fabricm na platformie Azure, opierając się na warstwach trwałości Silver lub Gold. Czy ta zmiana wpłynie na mnie?
Odp **.: nie**. Gwarancje zapewniane przez Service Fabric [warstwy trwałości](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) będą nadal działać nawet po tej zmianie. Jeśli wymagana jest izolacja sprzętowa fizycznego z innych powodów, może być konieczne wykonanie jednej z opisanych powyżej czynności. 

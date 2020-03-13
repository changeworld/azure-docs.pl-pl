---
title: Adaptacyjne Zabezpieczanie sieci w Azure Security Center | Microsoft Docs
description: Dowiedz się, jak używać rzeczywistych wzorców ruchu do ochrony reguł sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń), a także ulepszania stan zabezpieczeń.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 09d62d23-ab32-41f0-a5cf-8d80578181dd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/11/2020
ms.author: memildin
ms.openlocfilehash: bc610fa1d7a5fa1a10db3298164404b92d5d9f85
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2020
ms.locfileid: "79139593"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Adaptacyjne Zabezpieczanie sieci w Azure Security Center
Dowiedz się, jak konfigurować adaptacyjną ochronę sieci w Azure Security Center.

## <a name="what-is-adaptive-network-hardening"></a>Co to jest adaptacyjna Funkcja ograniczania przepustowości sieci?
Zastosowanie [sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń)](https://docs.microsoft.com/azure/virtual-network/security-overview) do filtrowania ruchu do i z zasobów, usprawnia stan zabezpieczeń sieci. Nadal jednak mogą istnieć sytuacje, w których rzeczywisty ruch przepływający przez sieciowej grupy zabezpieczeń jest podzbiorem zdefiniowanych reguł sieciowej grupy zabezpieczeń. W takich przypadkach dalsze ulepszanie stan zabezpieczeń można osiągnąć przez zaostrzonie reguł sieciowej grupy zabezpieczeń na podstawie rzeczywistych wzorców ruchu.

Adaptacyjne zwiększanie przepustowości sieci zapewnia rekomendacje w celu dalszej ochrony reguł sieciowej grupy zabezpieczeń. Używa algorytmu uczenia maszynowego, który ma czynniki w rzeczywistym ruchu, znanej zaufanej konfiguracji, analizie zagrożeń i innych wskaźnikach naruszenia, a następnie udostępnia zalecenia zezwalające na ruch tylko z konkretnych krotek IP/portów.

Załóżmy na przykład, że istniejąca reguła sieciowej grupy zabezpieczeń ma zezwalać na ruch z 140.20.30.10/24 na porcie 22. Zalecane zalecenie dotyczące ograniczania przepustowości sieci oparte na analizie zawęża zakres i zezwala na ruch z 140.23.30.10/29 — który jest węższym zakresem adresów IP i odmówi cały ruch do tego portu.

>[!TIP]
> Zalecenia dotyczące zabezpieczania sieci adaptacyjnej są obsługiwane tylko na określonych portach. Aby uzyskać pełną listę, zobacz, [które porty są obsługiwane?](#which-ports-are-supported) poniżej. 


![Widok ograniczania funkcjonalności sieci](./media/security-center-adaptive-network-hardening/traffic-hardening.png)


## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>Wyświetl alerty i reguły ograniczania przepustowości sieci

1. W Security Center wybierz pozycję **sieć** -> **adaptacyjne Zabezpieczanie sieci**. Sieci maszyn wirtualnych są wymienione na trzech oddzielnych kartach:
   * **Zasoby w złej kondycji**: maszyny wirtualne, które aktualnie mają zalecenia i alerty, które zostały wyzwolone przez uruchomienie adaptacyjnego algorytmu ograniczania przepustowości sieci. 
   * **Dobra kondycja**: maszyny wirtualne bez alertów i zaleceń.
   * **Niezeskanowane zasoby**: maszyny wirtualne, na których algorytm ograniczania funkcjonalności sieci nie może zostać uruchomiony z jednego z następujących powodów:
      * **Maszyny wirtualne są klasycznymi maszynami wirtualnymi**: obsługiwane są tylko Azure Resource Manager maszyny wirtualne.
      * Brak **wystarczającej ilości danych**: w celu wygenerowania dokładnego zalecenia dotyczącego ograniczania ruchu, Security Center wymaga co najmniej 30 dni danych ruchu.
      * **Maszyna wirtualna nie jest chroniona przez Standard ASC**: tylko maszyny wirtualne, które są ustawione na standardową warstwę cenową Security Center, kwalifikują się do tej funkcji.

     ![zasoby w złej kondycji](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. Na karcie **zasoby w złej kondycji** wybierz maszynę wirtualną, aby wyświetlić jej alerty i zalecane reguły ograniczania funkcjonalności, które mają zostać zastosowane.

    ![Ograniczanie funkcjonalności alertów](./media/security-center-adaptive-network-hardening/anh-recommendation-rules.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>Przeglądanie i stosowanie zalecanych reguł ograniczania funkcjonalności sieci

1. Na karcie **zasoby w złej kondycji** wybierz maszynę wirtualną. Są wyświetlane alerty i zalecane reguły ograniczania funkcjonalności.

     ![zasady ograniczania funkcjonalności](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > Na karcie **reguły** znajduje się lista reguł, które zaleca się dodanie. Na karcie **alerty** znajduje się lista alertów, które zostały wygenerowane z powodu ruchu, przepływających do zasobu, który nie należy do zakresu adresów IP dozwolony w zalecanych regułach.

2. Jeśli chcesz zmienić niektóre parametry reguły, możesz ją zmodyfikować, zgodnie z opisem w [Modyfikuj regułę](#modify-rule).
   > [!NOTE]
   > Możesz również [usunąć](#delete-rule) lub [dodać](#add-rule) regułę.

3. Wybierz reguły, które chcesz zastosować w sieciowej grupy zabezpieczeń, a następnie kliknij pozycję **Wymuszaj**.

      > [!NOTE]
      > Zasady wymuszane są dodawane do sieciowej grupy ZABEZPIECZEŃów chroniących maszynę wirtualną. (Maszyna wirtualna może być chroniona przez sieciowej grupy zabezpieczeń, która jest skojarzona z kartą sieciową lub podsieć, w której znajduje się maszyna wirtualna, lub obie)

    ![Wymuś reguły](./media/security-center-adaptive-network-hardening/enforce-hard-rule2.png)


### Modyfikowanie reguły <a name ="modify-rule"> </a>

Możesz chcieć zmodyfikować parametry reguły, która została zalecana. Na przykład możesz chcieć zmienić zalecane zakresy adresów IP.

Niektóre ważne wskazówki dotyczące modyfikowania reguły ograniczania funkcjonalności sieci:

* Można modyfikować parametry tylko dla reguł "Zezwalaj". 
* Nie można zmienić reguł "Zezwalaj" na "Odmów" reguł. 

  > [!NOTE]
  > Tworzenie i modyfikowanie reguł "Odmów" jest wykonywane bezpośrednio w sieciowej grupy zabezpieczeń. Aby uzyskać więcej informacji, zobacz [Tworzenie, zmienianie lub usuwanie sieciowej grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

* Reguła **Odmów całego ruchu** jest jedynym typem reguły "Odmów", która będzie wyświetlana w tym miejscu, i nie może być modyfikowana. Można jednak go usunąć (zobacz [Usuwanie reguły](#delete-rule)).
  > [!NOTE]
  > Reguła **Odmów całego ruchu** jest zalecana, gdy w wyniku uruchomienia algorytmu Security Center nie zidentyfikuje ruchu, który powinien być dozwolony, na podstawie istniejącej konfiguracji sieciowej grupy zabezpieczeń. W związku z tym zalecaną regułą jest odmowa całego ruchu kierowanego do określonego portu. Nazwa tego typu reguły jest wyświetlana jako "*wygenerowany przez system*". Po wymuszeniu tej reguły jej rzeczywista nazwa w sieciowej grupy zabezpieczeń będzie ciągiem zawierającym Protokół, kierunek ruchu, "Odmów" i liczbę losową.

*Aby zmodyfikować adaptacyjną zasadę ograniczania funkcjonalności sieci:*

1. Aby zmodyfikować niektóre parametry reguły, na karcie **reguły** kliknij trzy kropki (...) na końcu wiersza reguły, a następnie kliknij pozycję **Edytuj**.

   ![Edytuj regułę](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. W oknie **Edytowanie reguły** Zaktualizuj szczegóły, które chcesz zmienić, a następnie kliknij przycisk **Zapisz**.

   > [!NOTE]
   > Po kliknięciu przycisku **Zapisz**pomyślnie zmieniono regułę. *Nie została jednak zastosowana do sieciowej grupy zabezpieczeń.* Aby go zastosować, należy wybrać regułę z listy, a następnie kliknąć pozycję **Wymuszaj** (zgodnie z opisem w następnym kroku).

   ![Edytuj regułę](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. Aby zastosować zaktualizowaną regułę, wybierz ją z listy, a następnie kliknij pozycję **Wymuszaj**.

    ![Wymuś regułę](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### Dodaj nową regułę <a name ="add-rule"> </a>

Można dodać regułę "Zezwalaj", która nie jest zalecana przez Security Center.

> [!NOTE]
> Tutaj można dodawać tylko reguły "Zezwalaj". Jeśli chcesz dodać reguły "Odmów", możesz to zrobić bezpośrednio w sieciowej grupy zabezpieczeń. Aby uzyskać więcej informacji, zobacz [Tworzenie, zmienianie lub usuwanie sieciowej grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

*Aby dodać zasadę ograniczania funkcjonalności sieci:*

1. Kliknij pozycję **Dodaj regułę** (znajdującą się w lewym górnym rogu).

   ![Dodaj regułę](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. W oknie **Nowa reguła** wprowadź szczegóły, a następnie kliknij przycisk **Dodaj**.

   > [!NOTE]
   > Po kliknięciu przycisku **Dodaj**reguła została pomyślnie dodana i zostanie wyświetlona na liście z innymi zalecanymi regułami. Nie została jednak zastosowana w sieciowej grupy zabezpieczeń. Aby go uaktywnić, należy wybrać regułę z listy, a następnie kliknąć pozycję **Wymuszaj** (zgodnie z opisem w następnym kroku).

3. Aby zastosować nową regułę, z listy wybierz nową regułę, a następnie kliknij pozycję **Wymuszaj**.

    ![Wymuś regułę](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### Usuwanie reguły <a name ="delete-rule"> </a>

W razie potrzeby można usunąć zalecaną regułę dla bieżącej sesji. Na przykład można określić, że stosowanie sugerowanej reguły może blokować wiarygodny ruch.

*Aby usunąć regułę adaptacyjnej ochrony sieci dla bieżącej sesji:*

1. Na karcie **reguły** kliknij trzy kropki (...) na końcu wiersza reguły, a następnie kliknij pozycję **Usuń**.  

    ![zasady ograniczania funkcjonalności](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)

 

## <a name="which-ports-are-supported"></a>Które porty są obsługiwane?

Zalecenia dotyczące zabezpieczania sieci adaptacyjnej są obsługiwane tylko na określonych portach. W tej tabeli przedstawiono pełną listę:

|Port|Protokół|Skojarzona usługa|
|:---:|:----:|:----|
|13|UDP|Usługa Daytime|
|17|UDP|QOTD — protokół|
|19|UDP|Protokół CHARGEN|
|22|TCP|Protokół SSH|
|23|TCP|Program|
|53|UDP|DNS|
|69|UDP|TFTP|
|81|TCP|Potencjalnie złośliwe (węzeł wyjścia sieci TOR)|
|111|TCP/UDP|RPC|
|119|TCP|SERWERÓW|
|123|UDP|NTP|
|135|TCP/UDP|Mapowanie punktów końcowych; RPC DCE|
|137|TCP/UDP|Usługa nazw NetBIOS|
|138|TCP/UDP|Usługa datagramów NetBIOS|
|139|TCP|Usługa sesji NetBIOS|
|161|TCP/UDP|SNMP|
|162|TCP/UDP|SNMP|
|389|TCP|LDAP|
|445|TCP|SMB|
|512|TCP|Rexec|
|514|TCP|Powłoka zdalna|
|593|TCP/UDP|HTTP RPC|
|636|TCP|LDAP|
|873|TCP|rsync|
|1433|TCP|MS SQL|
|1434|UDP|MS SQL|
|1900|UDP|Użycie SSDP|
|1900|UDP|Użycie SSDP|
|2049|TCP/UDP|NFS|
|2301|TCP|Usługa zarządzania Compaq|
|2323|TCP|3W — nfsd|
|2381|TCP|Usługa zarządzania Compaq|
|3268|TCP|LDAP|
|3306|TCP|MySQL|
|3389|TCP|RDP|
|4333|TCP|mSQL|
|5353|UDP|mDNS|
|5432|TCP|PostgreSQL|
|5555|TCP|Agent osobisty; HP OmniBack|
|5800|TCP|VNC|
|5900|TCP|Bufor ramki zdalnego; VNC|
|5900|TCP|VNC|
|5985|TCP|Windows PowerShell|
|5986|TCP|Windows PowerShell|
|6379|TCP|Redis|
|6379|TCP|Redis|
|7000|TCP|Cassandra|
|7001|TCP|Cassandra|
|7199|TCP|Cassandra|
|8081|TCP|CosmosDB Administrator Sun proxy|
|8089|TCP|Splunk|
|8545|TCP|Potencjalnie złośliwe (Cryptominer)|
|9042|TCP|Cassandra|
|9160|TCP|Cassandra|
|9300|TCP|Elasticsearch|
|11211|UDP|Memcached|
|16379|TCP|Redis|
|26379|TCP|Redis|
|27017|TCP|MongoDB|
|37215|TCP|Potencjalnie złośliwe|
||||
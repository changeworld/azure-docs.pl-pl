---
title: Adaptacyjne utwardzanie sieci w usłudze Azure Security Center | Dokumenty firmy Microsoft
description: Dowiedz się, jak używać rzeczywistych wzorców ruchu, aby wzmocnić reguły sieciowych grup zabezpieczeń i jeszcze bardziej poprawić stan bezpieczeństwa.
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
ms.openlocfilehash: a75be23e2e8215d86aebcfd7f4317f2f597d3c5b
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385082"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Adaptacyjne uostrzanie sieci w usłudze Azure Security Center
Dowiedz się, jak skonfigurować adaptacyjne wzmocnienie sieci w usłudze Azure Security Center.

## <a name="what-is-adaptive-network-hardening"></a>Co to jest adaptacyjne hartowanie sieci?
Stosowanie [sieciowych grup zabezpieczeń (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview) do filtrowania ruchu do i z zasobów poprawia stan bezpieczeństwa sieci. Jednak nadal może być kilka przypadków, w których rzeczywisty ruch przepływający przez nsg jest podzbiorem reguł sieciowej grupy płciowych zdefiniowanych. W takich przypadkach dalsze poprawienie postawy bezpieczeństwa można osiągnąć poprzez zaostrzenie reguł sieciowej sieciowej ochrony sieciowej w oparciu o rzeczywiste wzorce ruchu.

Adaptacyjne hartowanie sieci zawiera zalecenia dotyczące dalszego zaostrzenia reguł sieciowej sieciowej. Używa algorytmu uczenia maszynowego, który uwzględnia rzeczywisty ruch, znaną zaufaną konfigurację, analizę zagrożeń i inne wskaźniki naruszenia zabezpieczeń, a następnie udostępnia zalecenia, aby zezwolić na ruch tylko z określonych krotek IP/portu.

Załóżmy na przykład, że istniejącą regułą nsg jest zezwalanie na ruch z 140.20.30.10/24 na porcie 22. Zalecenie Adaptive Network Hardening, oparte na analizie, byłoby zawężenie zakresu i umożliwienie ruchu z 140.23.30.10/29 - który jest węższym zakresem IP i odmówić wszelkiego innego ruchu do tego portu.

>[!TIP]
> Adaptacyjne zalecenia dotyczące hartowania sieci są obsługiwane tylko w następujących określonych portach (zarówno dla UDP, jak i TCP): 13, 17, 19, 22, 23, 53, 69, 81, 111, 119, 123, 135, 137, 138, 139, 161, 162, 389, 445, 512, 514, 593, 636, 873, 1433, 1434, 1900, 2049, 2301 , 2323, 2381, 3268, 3306, 3389, 4333, 5353, 5432, 5555, 5800, 5900, 5900, 5985, 5986, 6379, 6379, 7000, 7001, 7199, 8081, 8089, 8545, 9042, 9160, 9300, 11211, 16379, 26379, 27017, 37215


![Widok hartowania sieci](./media/security-center-adaptive-network-hardening/traffic-hardening.png)


## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>Wyświetlanie alertów i reguł adaptacyjnego hartowania sieci

1. W centrum zabezpieczeń **wybierz** -> pozycję**Adaptacyjne utwardzanie sieci adaptacyjnej sieci.** Maszyny wirtualne sieci są wymienione w trzech oddzielnych kartach:
   * **Zasoby w złej kondycji:** maszyny wirtualne, które obecnie mają zalecenia i alerty, które zostały wyzwolone przez uruchomienie algorytmu adaptacyjnego hartowania sieci. 
   * **Zdrowe zasoby:** maszyny wirtualne bez alertów i zaleceń.
   * **Nieskanowane zasoby:** maszyny wirtualne, na których nie można uruchomić algorytmu adaptacyjnego hartowania sieci z jednego z następujących powodów:
      * **Maszyny wirtualne są klasycznymi maszynami wirtualnymi:** obsługiwane są tylko maszyny wirtualne usługi Azure Resource Manager.
      * **Za mało danych jest dostępna**: Aby wygenerować dokładne zalecenia dotyczące hartowania ruchu, usługa Security Center wymaga co najmniej 30 dni danych o ruchu drogowym.
      * **Maszyna wirtualna nie jest chroniona przez standard ASC:** tylko maszyny wirtualne, które są ustawione na standardową warstwę cenową Centrum zabezpieczeń, kwalifikują się do korzystania z tej funkcji.

     ![niezdrowe zasoby](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. Na karcie **Zasoby w złej kondycji** wybierz maszynę wirtualną, aby wyświetlić jej alerty i zalecane reguły zaostrzenia do zastosowania.

    ![alerty twardnienia](./media/security-center-adaptive-network-hardening/anh-recommendation-rules.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>Przegląd i stosowanie zalecanych zasad adaptacyjnego hartowania sieci

1. Na karcie **Zasoby w złej kondycji** wybierz maszynę wirtualną. Alerty i zalecane reguły hartowania są wymienione.

     ![zasady utwardzania](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > Karta **Reguły** zawiera listę reguł zalecanych przez adaptacyjne hartowanie sieci. Karta **Alerty** zawiera listę alertów, które zostały wygenerowane z powodu ruchu, który przepływa do zasobu, który nie mieści się w zakresie IP dozwolonym w zalecanych regułach.

2. Jeśli chcesz zmienić niektóre parametry reguły, możesz ją zmodyfikować, jak wyjaśniono w [obszarze Modyfikuj regułę](#modify-rule).
   > [!NOTE]
   > Można również [usunąć](#delete-rule) lub [dodać](#add-rule) regułę.

3. Wybierz reguły, które chcesz zastosować w sieciowej sieciowej, a następnie kliknij przycisk **Wymuś**.

      > [!NOTE]
      > Wymuszone reguły są dodawane do sieciowej ochrony maszyny wirtualnej. (Maszyna wirtualna może być chroniona przez sieciowej grupy sieciowej skojarzonej z jego kartą sieciową lub podsieci, w której znajduje się maszyna wirtualna lub obie)

    ![egzekwowanie przepisów](./media/security-center-adaptive-network-hardening/enforce-hard-rule2.png)


### <a name="modify-a-rule"></a>Modyfikowanie <a name ="modify-rule"> </a> reguły

Można zmodyfikować parametry reguły, która została zalecona. Na przykład można zmienić zalecane zakresy adresów IP.

Kilka ważnych wskazówek dotyczących modyfikowania reguły adaptacyjnego hartowania sieci:

* Można zmodyfikować tylko parametry reguł "zezwalaj". 
* Nie można zmienić reguł "zezwalaj", aby stały się regułami "odmów". 

  > [!NOTE]
  > Tworzenie i modyfikowanie reguł "odmów" odbywa się bezpośrednio na sieciowej sieciowej sieciowej. Aby uzyskać więcej informacji, zobacz [Tworzenie, zmienianie lub usuwanie sieciowej grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

* Reguła **Odmów wszystkich ruchu** jest jedynym typem reguły "odmów", która zostanie wyświetlona w tym miejscu i nie może zostać zmodyfikowana. Można go jednak usunąć (zobacz [Usuwanie reguły](#delete-rule)).
  > [!NOTE]
  > A **Odmów wszystkie reguły ruchu** jest zalecane, gdy w wyniku uruchomienia algorytmu Usługa Security Center nie identyfikuje ruchu, który powinien być dozwolony, na podstawie istniejącej konfiguracji sieciowej grupy zabezpieczeń. W związku z tym zalecana reguła jest odmowa całego ruchu do określonego portu. Nazwa tego typu reguły jest wyświetlana jako "*Wygenerowany system*". Po wyegzekwowaniu tej reguły jej rzeczywista nazwa w nsg będzie ciąg składający się z protokołu, kierunku ruchu, "ODMÓW" i liczby losowej.

*Aby zmodyfikować regułę adaptacyjnego hartowania sieci, należy:*

1. Aby zmodyfikować niektóre parametry reguły, na karcie **Reguły** kliknij trzy kropki (...) na końcu wiersza reguły i kliknij przycisk **Edytuj**.

   ![reguła edycji](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. W oknie **Edytuj regułę** zaktualizuj szczegóły, które chcesz zmienić, a następnie kliknij przycisk **Zapisz**.

   > [!NOTE]
   > Po kliknięciu przycisku **Zapisz**reguła została pomyślnie zmieniona. *Jednak nie zastosowano go do nsg.* Aby ją zastosować, należy zaznaczyć regułę na liście i kliknąć przycisk **Wymuś** (jak wyjaśniono w następnym kroku).

   ![reguła edycji](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. Aby zastosować zaktualizowaną regułę, z listy wybierz zaktualizowaną regułę i kliknij przycisk **Wymuś**.

    ![wymuszanie reguły](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### <a name="add-a-new-rule"></a>Dodawanie nowej <a name ="add-rule"> </a> reguły

Można dodać regułę "zezwalaj", która nie była zalecana przez Centrum zabezpieczeń.

> [!NOTE]
> W tym miejscu można dodać tylko reguły "zezwalaj". Jeśli chcesz dodać reguły "odmów", możesz to zrobić bezpośrednio w sieciowej sieciowej. Aby uzyskać więcej informacji, zobacz [Tworzenie, zmienianie lub usuwanie sieciowej grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

*Aby dodać regułę adaptacyjnego hartowania sieci:*

1. Kliknij pozycję **Dodaj regułę** (znajdującą się w lewym górnym rogu).

   ![dodaj regułę](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. W oknie **Nowa reguła** wprowadź szczegóły i kliknij przycisk **Dodaj**.

   > [!NOTE]
   > Po kliknięciu przycisku **Dodaj**reguła została pomyślnie dodana i zostanie wyświetlona wraz z innymi zalecanymi regułami. Jednak nie zastosowano go na nsg. Aby ją aktywować, należy wybrać regułę na liście i kliknąć **przycisk Wymuś** (jak wyjaśniono w następnym kroku).

3. Aby zastosować nową regułę, z listy wybierz nową regułę i kliknij przycisk **Wymuś**.

    ![wymuszanie reguły](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### <a name="delete-a-rule"></a>Usuwanie reguły <a name ="delete-rule"> </a>

W razie potrzeby można usunąć zalecaną regułę dla bieżącej sesji. Na przykład można ustalić, że zastosowanie sugerowanej reguły może blokować legalny ruch.

*Aby usunąć regułę adaptacyjnego hartowania sieci dla bieżącej sesji:*

1. Na karcie **Reguły** kliknij trzy kropki (...) na końcu wiersza reguły, a następnie kliknij przycisk **Usuń**.  

    ![zasady utwardzania](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)
---
title: Adaptacyjne sieci zaostrzanie poziomu zabezpieczeń w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: " Dowiedz się, jak włączyć adaptacyjne sieci wzmacniania ochrony w usłudze Azure Security Center. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: monhaber
ms.assetid: 09d62d23-ab32-41f0-a5cf-8d80578181dd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2019
ms.author: v-mohabe
ms.openlocfilehash: 17f01d89598d99425d157e4c9c31e64ab1ccbcda
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65966985"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Funkcje adaptacyjnego sterowania sieci zaostrzanie poziomu zabezpieczeń w usłudze Azure Security Center
Dowiedz się, jak skonfigurować funkcje adaptacyjnego sterowania ograniczania funkcjonalności sieci w Centrum zabezpieczeń Azure.

## <a name="what-is-adaptive-network-hardening"></a>Co to jest adaptacyjne ograniczania funkcjonalności sieci?
Stosowanie [sieciowe grupy zabezpieczeń (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview) do filtrowania ruchu do i z zasobów, poprawia strukturę bezpieczeństwa sieci. Jednak nadal można czasami, w których rzeczywisty ruch przepływający przez sieciową grupę zabezpieczeń jest podzbiór reguł sieciowej grupy zabezpieczeń zdefiniowane. W takich przypadkach dalsze zwiększanie poziomu bezpieczeństwa można osiągnąć przez wzmacnianie ochrony reguły sieciowej grupy zabezpieczeń, w oparciu o wzorce rzeczywisty ruch.

Adaptacyjne ograniczania funkcjonalności sieci zawiera zalecenia, aby dodatkowo zabezpieczyć reguły sieciowej grupy zabezpieczeń. Używa algorytmu, który uwzględnia rzeczywisty ruch, znane zaufanych konfiguracji, analizy zagrożeń i inne wskaźniki naruszenia zabezpieczeń, uczenia maszynowego i następnie udostępnia zalecenia, aby zezwolić na ruch tylko z określonego adresu IP i portu krotek.

Na przykład załóżmy, że jest istniejącą regułę sieciowej grupy zabezpieczeń zezwalającą na ruch z 140.20.30.10/24 na porcie 22. Adaptacyjne sieci wzmacniania ochrony firmy zalecenia na podstawie analizy, będzie można zawęzić zakres i zezwolić na ruch z 140.23.30.10/29 — czyli mniejszego zakresu adresów IP i odmowy cały ruch do tego portu.

![Widok zaostrzanie poziomu zabezpieczeń sieci](./media/security-center-adaptive-network-hardening/traffic-hardening.png)


> [!NOTE]
> Funkcje adaptacyjnego sterowania zalecenia dotyczące wzmacniania ochrony sieci są obsługiwane w następujących portów: 22, 3389, 21, 23, 445, 4333, 3306, 1433, 1434, 53, 20, 5985, 5986, 5432, 139, 66, 1128

## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>Wyświetl alerty adaptacyjne ograniczania funkcjonalności sieci i reguł

1. W usłudze Security Center wybierz **sieć** -> **adaptacyjne ograniczania funkcjonalności sieci**. Maszyny wirtualne w sieci zostaną wyświetlone w obszarze trzech osobnych kartach:
   * **Zasoby w złej kondycji**: Maszyny wirtualne, które mają obecnie zaleceń i alertów, które zostały wyzwolone przez uruchomienie algorytmu adaptacyjne ograniczania funkcjonalności sieci. 
   * **Zasoby w dobrej kondycji**: Maszyny wirtualne bez alerty i zalecenia.
   * **Nieprzeskanowane zasoby**: W przypadku maszyn wirtualnych, które algorytm adaptacyjne ograniczania funkcjonalności sieci, nie mogą być uruchamiane z jednego z następujących powodów:
      * **Maszyny wirtualne są klasyczne maszyny wirtualne**:-obsługiwane są tylko usługi Azure Resource Manager dla maszyn wirtualnych.
      * **Dostępnych jest za mało danych**: Aby wygenerować dokładne ruchu rekomendacje dotyczące ograniczania funkcjonalności, usługa Security Center wymaga co najmniej 30 dni danych w ruchu.
      * **Maszyna wirtualna nie jest chroniony przez ASC standard**: Tylko maszyny wirtualne, które są ustawione na warstwy cenowej standardowa Centrum zabezpieczeń kwalifikują się do tej funkcji.

     ![zasoby w złej kondycji](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. Z **zasoby w złej kondycji** , a następnie wybierz maszynę Wirtualną w celu wyświetlania jej alertów i reguł zalecanych zaostrzanie poziomu zabezpieczeń do zastosowania.

    ![zaostrzanie poziomu zabezpieczeń alertów](./media/security-center-adaptive-network-hardening/hardening-alerts.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>Przejrzyj i Zastosuj zalecane reguły kodu adaptacyjne wzmacnianie sieci

1. Z **zasoby w złej kondycji** , a następnie wybierz Maszynę wirtualną. Alerty i reguły wzmocnienia zabezpieczeń zalecane są wymienione.
   ![zaostrzanie poziomu zabezpieczeń alertów](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > **Reguły** karta zawiera listę reguł, które adaptacyjne ograniczania funkcjonalności sieci zaleca się dodawania. **Alerty** zawiera listę wszystkich alertów, które zostały wygenerowane z powodu ruchu, przepływać do zasobu, który nie jest w zakresie adresów IP dozwolonych w zalecanych reguł.

   ![reguły wzmocnienia zabezpieczeń](./media/security-center-adaptive-network-hardening/hardening-rules.png)

2. Jeśli chcesz zmienić niektóre parametry reguły, można zmodyfikować, jak wyjaśniono w [modyfikowanie reguły](#modify-rule).
   > [!NOTE]
   > Możesz również [Usuń](#delete-rule) lub [Dodaj](#add-rule) regułę.

3. Wybierz zasady, które chcesz zastosować sieciową grupę zabezpieczeń, a następnie kliknij przycisk **Wymuś**. 

### Modyfikowanie reguły  <a name ="modify-rule"> </a>

Można zmodyfikować parametry reguły, które było zalecane. Na przykład można zmienić zalecane zakresy adresów IP.

Ważne wskazówki dotyczące modyfikowania reguły adaptacyjne wzmacniania ochrony sieci:

* Można zmodyfikować parametry "Zezwalaj" tylko reguły. 
* Nie można zmienić, reguły, aby stać się "Odmów" zasady "Zezwalaj". 

  > [!NOTE]
  > Tworzenie i modyfikowanie "Odmów" zasady odbywa się bezpośrednio z sieciowej grupy zabezpieczeń dla uzyskać więcej informacji, zobacz [tworzenie, zmienianie lub usuwanie grupy zabezpieczeń sieci](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

* A **zezwalają na ruch z wszystkich** reguła jest jedynym typem "Odmów" reguły, które będą wyświetlane w tym miejscu i nie można modyfikować. Można jednak usunąć (zobacz [usunąć regułę](#delete-rule)).
  > [!NOTE]
  > A **zezwalają na ruch z wszystkich** reguły jest zalecane, gdy, w wyniku uruchomienia algorytm, usługa Security Center nie identyfikacji ruchu, który powinien być dozwolony, w oparciu o istniejącą konfigurację sieciową grupę zabezpieczeń. Dlatego zalecane reguły jest odrzucanie cały ruch do określonego portu. Nazwa tego typu reguł jest wyświetlany jako "wygenerowanej przez system". Po wymuszania tej reguły, jego rzeczywista nazwa w sieciowej grupie zabezpieczeń będzie ciąg składające się z protokołem, kierunek ruchu, "DENY" i liczbę losową.

*Aby zmodyfikować regułę adaptacyjne wzmacniania ochrony sieci:*

1. Aby zmodyfikować niektóre parametry reguły w **reguły** kartę, kliknij wielokropek (...) na końcu wiersza reguły, a następnie kliknij przycisk **edycji reguły**.

   ![Edytuj regułę](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. W **edycji reguły** okna, szczegółowe informacje, które chcesz zmienić, a następnie kliknij przycisk Aktualizuj **Zapisz**.

   > [!NOTE]
   > Po kliknięciu przycisku **Zapisz**, zostało pomyślnie zmienione reguły. *Jednak nie zastosowano je do sieciowej grupy zabezpieczeń.* Aby zastosować go, możesz wybierz regułę na liście i kliknij przycisk **Wymuś** (opisany w następnym kroku).

3. Aby zastosować regułę zaktualizowane, z listy, wybierz zaktualizowano regułę, a następnie kliknij przycisk **Wymuś**.

### Dodaj nową regułę <a name ="add-rule"> </a>


Możesz dodać regułę "Zezwalaj", które nie było zalecane przez usługę Security Center.

> [!NOTE]
> W tym miejscu można dodawać reguły tylko "Zezwalaj". Jeśli chcesz dodać reguły "Odmów", należy więc bezpośrednio w sieciowej grupie zabezpieczeń. Aby uzyskać więcej informacji, zobacz [tworzenie, zmienianie lub usuwanie grupy zabezpieczeń sieci](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

*Aby dodać regułę adaptacyjne wzmacniania ochrony sieci:*

1. Kliknij przycisk **Dodaj regułę** (znajdujący się w lewym górnym rogu).

   ![Dodaj regułę](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. W **edycji reguły** oknie wprowadź szczegółowe informacje i kliknij przycisk **Zapisz**.

   > [!NOTE]
   > Po kliknięciu przycisku **Zapisz**pomyślnie dodano regułę i jest ona wyświetlana z innymi regułami zalecane. Jednak nie zastosowano je na grupy zabezpieczeń sieci. Aby go uaktywnić, możesz wybierz regułę na liście i kliknij przycisk **Wymuś** (opisany w następnym kroku).

3. Aby zastosować nową regułę z listy, wybierz nową regułę, a następnie kliknij przycisk **Wymuś**.



### Usuwanie reguły <a name ="delete-rule"> </a>

Gdy jest to konieczne, możesz usunąć zalecane reguły. Na przykład mogą określić, że stosowanie reguły sugerowane może blokować ruch uzasadnione.

*Aby usunąć regułę adaptacyjne wzmacniania ochrony sieci:*

1. W **reguły** kartę, kliknij wielokropek (...) na końcu wiersza reguły, a następnie kliknij przycisk **Usuń regułę**.

   ![Usuń regułę](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)







 


---
title: Rozwiązanie Azure VMware by CloudSimple — konfigurowanie tabel i reguł zapory
description: W tym artykule opisano sposób konfigurowania tabel zapory chmury prywatnej i reguł ograniczających ruch w podsieciach i sieciach VLAN.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 986f4b0da7254ebac3725a704f32af785c72fbcc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244669"
---
# <a name="set-up-firewall-tables-and-rules-for-private-clouds"></a>Konfigurowanie tabel zapory i reguł dla chmur prywatnych

Tabele zapory i skojarzone reguły umożliwiają określenie ograniczeń ruchu w celu zastosowania do określonych podsieci i sieci VLAN.

* Podsieć może być skojarzona z jedną tabelą zapory.
* Tabela zapory może być skojarzona z wieloma podsieciami.

## <a name="add-a-new-firewall-table"></a>Dodawanie nowej tabeli zapory

1. [Przejdź do portalu CloudSimple](access-cloudsimple-portal.md) i wybierz polecenie **Sieć** w menu bocznym.
2. Wybierz **pozycję Tabele zapory**.
3. Wybierz **pozycję Utwórz tabelę zapory**.

    ![Strona sieci VLAN/podsieci](media/firewall-tables-page.png)

4. Wprowadź nazwę tabeli.
5. Na liście znajduje się domyślna reguła dla tabeli. Kliknij **pozycję Utwórz nową regułę,** aby utworzyć dodatkową regułę. Szczegółowe informacje można znaleźć w poniższej procedurze.
6. Kliknij **przycisk Gotowe,** aby zapisać tabelę zapory.

> [!IMPORTANT]
> Można utworzyć maksymalnie dwie tabele zapory na chmurę prywatną.

## <a name="firewall-rules"></a>Reguły zapory

Reguły zapory określają sposób, w jaki zapora traktuje określone typy ruchu. Karta **Reguły** dla wybranej tabeli zapory zawiera listę wszystkich skojarzonych reguł.

![Tabela reguł zapory](media/firewall-rules-tab.png)

## <a name="create-a-firewall-rule"></a>Tworzenie reguły zapory

1. Wyświetl ustawienia, aby utworzyć regułę zapory w jeden z następujących sposobów:
    * Podczas tworzenia tabeli zapory **kliknij pozycję Dodaj regułę.**
    * Wybierz konkretną tabelę zapory na stronie **Tabele zapory sieciowej >** i kliknij pozycję **Utwórz nową regułę zapory**.
2. Skonfiguruj regułę w następujący sposób:
    * **Nazwa**. Nadaj regule nazwę.
    * **Priorytet**. Przypisz priorytet do reguły. Reguły z niższymi liczbami są wykonywane jako pierwsze.
    * **Typ ruchu**. Wybierz, czy reguła dotyczy ruchu w chmurze prywatnej, Internetu lub sieci VPN (bezstanowy), czy publicznego adresu IP (stanowego).
    * **protokołu**. Wybierz protokół objęty regułą (TCP, UDP lub dowolny protokół).
    * **Kierunek**. Wybierz, czy reguła dotyczy ruchu przychodzącego, czy wychodzącego. Należy zdefiniować oddzielne reguły dla ruchu przychodzącego i wychodzącego.
    * **Akcja**. Wybierz akcję, którą należy podjąć, jeśli reguła jest zgodna (zezwalaj lub odrzucaj).
    * **Źródło**. Określ źródła objęte regułą (blok CIDR, wewnętrzny lub dowolne źródło).
    * **Zakres portów źródłowych**. Określ zakres portów podlegających regule.
    * **Kierunek**. Wybierz przychodzące lub wychodzące.
    * **Miejsce docelowe**. Określ miejsca docelowe objęte regułą (blok CIDR, wewnętrzny lub dowolne źródło).
    * **Zakres portów źródłowych**. Określ zakres portów podlegających regule.

    ![Reguła dodawania tabeli zapory](media/firewall-rule-create.png)

3. Kliknij **przycisk Gotowe,** aby zapisać regułę i dodać ją do listy reguł dla tabeli zapory.

> [!IMPORTANT]
> Każda tabela zapory może mieć maksymalnie 10 reguł przychodzących i 20 reguł wychodzących. Limity te można zwiększyć, [kontaktując się z pomocą techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="attach-vlanssubnets"></a><a name="attach-vlans-subnet"></a>Dołączanie sieci/podsieci VLAN

Po zdefiniowaniu tabeli zapory można określić podsieci, które podlegają regułom w tabeli.

1. Na stronie**Tabele zapory** **sieciowej** > wybierz tabelę zapory.
2. Otwórz kartę **Dołączone sieci VLAN/Podsieć.**
3. Kliknij **pozycję Dołącz do sieci VLAN/Podsieci**.
4. Wybierz chmurę prywatną i sieć VLAN. Wyświetlana jest skojarzona nazwa podsieci i blok CIDR.
5. Kliknij **przycisk Prześlij**.

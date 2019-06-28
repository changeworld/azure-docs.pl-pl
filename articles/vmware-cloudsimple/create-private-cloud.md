---
title: Utwórz rozwiązanie VMware na platformie Azure, Chmura prywatna CloudSimple
description: W tym artykule opisano sposób tworzenia chmury prywatnej CloudSimple rozszerzenie obciążeń oprogramowania VMware do chmury za pomocą elastyczność działania i ciągłości działania
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e5c03c1d8a865b792ce79e3e2b576a629b71e02c
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333073"
---
# <a name="create-a-cloudsimple-private-cloud"></a>Tworzenie chmury prywatnej CloudSimple

Tworzenie chmury prywatnej pomaga sprostać szereg typowych potrzeb infrastruktury sieci:

* **Wzrost**. Jeśli został osiągnięty punkt odświeżania sprzętu dla istniejącej infrastruktury, Chmura prywatna umożliwia rozszerzenie z nie nowych inwestycji w sprzęt wymagane.

* **Szybkie rozszerzenie**. Jeśli wydajność tymczasowy lub nieplanowane musi wystąpić, Chmura prywatna służy do tworzenia dodatkowej pojemności bez opóźnień.

* **Zwiększenie ochrony**. Chmurę prywatną z co najmniej trzema węzłami umożliwia skorzystanie z automatycznej nadmiarowości i wysokiej dostępności ochrony.

* **Wymaga infrastruktury długoterminowe**. Jeśli centra danych są w pojemności lub chcesz zmienić strukturę obniżyć koszty, Chmura prywatna umożliwia wycofywanie centrów danych, a następnie przeprowadzić migrację do opartych na chmurze rozwiązania pozostając zgodny z operacji w przedsiębiorstwie.

Podczas tworzenia chmury prywatnej, otrzymujesz vSphere jednego klastra i całe Zarządzanie maszynami wirtualnymi, które są tworzone w tym klastrze.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Musi być obsługiwana węzłami, przed utworzeniem chmury prywatnej.  Aby uzyskać więcej informacji na temat inicjowania obsługi administracyjnej węzłów, zobacz [Inicjowanie obsługi administracyjnej węzłów dla oprogramowania VMware Solution by CloudSimple - Azure](create-nodes.md) artykułu.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Uzyskiwanie dostępu do portalu CloudSimple

Dostęp do [CloudSimple portal](access-cloudsimple-portal.md).

## <a name="create-a-new-private-cloud"></a>Tworzenie nowej chmury prywatnej

1. Na **zasobów** kliknij **nowej chmury prywatnej**.

    ![Tworzenie chmury prywatnej — jak zacząć](media/create-pc-button.png)

2. Wybierz lokalizację hostowania zasobów w chmurze prywatnej.

3. Wybierz CS28 lub CS36 you'ev typ węzła zainicjowana obsługa administracyjna dla chmury prywatnej. Tę druga opcję zawiera maksymalną moc obliczeniową i pamięć.

4. Wybierz liczbę węzłów dla chmury prywatnej. Można wybrać maksymalnie liczba dostępnych węzłów tego you'ev [aprowizowane](create-nodes.md).

    ![Tworzenie chmury prywatnej — podstawowe ustawienia](media/create-private-cloud-basic-info.png)

5. Kliknij pozycję **Next: Zaawansowane opcje**.

6. Wprowadź zakres CIDR podsieci vSphere/sieci vSAN. Upewnij się, że zakres CIDR nie pokrywa się z żadną z lokalnych lub innych podsieci platformy Azure (sieci wirtualne) lub z podsiecią bramy.  Nie używaj dowolnego zakresu CIDR zdefiniowane w sieciach wirtualnych platformy Azure.
    
    **Opcje zakresu CIDR:** prefiksie/24, /23, /22 lub /21. A/24 zakres CIDR obsługuje maksymalnie dziewięć węzłów, /23 zakres CIDR obsługuje maksymalnie 41 węzłów, /22 i /21 zakres CIDR obsługuje maksymalnie 64 węzłami (maksymalna liczba węzłów w chmurze prywatnej).

    > [!CAUTION]
    > Adresy IP z zakresu CIDR vSphere/sieć vsan, która jest zarezerwowana do użycia przez infrastrukturę chmury prywatnej.  Nie należy używać adresu IP z tego zakresu, na dowolnej maszynie wirtualnej.

7. Kliknij pozycję **Next: Przejrzyj i Utwórz**.

8. Przejrzyj ustawienia. Jeśli potrzebujesz zmienić dowolne ustawienia, kliknij przycisk **Wstecz**.

9. Kliknij pozycję **Utwórz**.

Chmura prywatna aprowizacji rozpocznie się po kliknięciu przycisku Utwórz.  Możesz monitorować postęp na podstawie [zadania](https://docs.azure.cloudsimple.com/activity/#tasks) strony w portalu CloudSimple.  Inicjowanie obsługi administracyjnej może potrwać 30 minut do dwóch godzin.  Otrzymasz wiadomość e-mail po zakończeniu aprowizacji.

## <a name="next-steps"></a>Kolejne kroki

* [Rozwiń chmury prywatnej](expand-private-cloud.md)
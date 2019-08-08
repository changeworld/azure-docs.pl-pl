---
title: Utwórz rozwiązanie VMware platformy Azure według chmury prywatnej CloudSimple
description: Opisuje, jak utworzyć chmurę prywatną CloudSimple do rozbudowania obciążeń programu VMware do chmury z elastycznością operacyjną i ciągłością
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 02a2bd311ea1e89a49eb12ef57a167a08eea5f98
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812253"
---
# <a name="create-a-cloudsimple-private-cloud"></a>Utwórz chmurę prywatną CloudSimple

Tworzenie chmury prywatnej pomaga sprostać różnym typowym potrzebom związanym z infrastrukturą sieci:

* **Wzrostu**. Jeśli do istniejącej infrastruktury został osiągnięty punkt odświeżenia sprzętowego, Chmura prywatna pozwala rozszerzyć system bez konieczności używania nowych inwestycji sprzętowych.

* **Szybkie rozszerzanie**. Jeśli wystąpią jakiekolwiek tymczasowe lub nieplanowane zapotrzebowanie na pojemność, Chmura prywatna pozwala na utworzenie dodatkowej pojemności bez opóźnień.

* **Zwiększona ochrona**. W przypadku chmury prywatnej co najmniej trzech węzłów otrzymujesz automatyczną nadmiarowość i ochronę o wysokiej dostępności.

* **Długoterminowe potrzeby dotyczące infrastruktury**. Jeśli centra danych mają pojemność lub chcesz zmienić strukturę w celu obniżenia kosztów, Chmura prywatna pozwala na wycofanie centrów danych i migrację do rozwiązania opartego na chmurze, a pozostałe zgodne z operacjami przedsiębiorstwa.

Podczas tworzenia chmury prywatnej otrzymujesz jeden klaster vSphere i wszystkie maszyny wirtualne zarządzania utworzone w tym klastrze.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby można było utworzyć chmurę prywatną, węzły muszą być obsługiwane.  Aby uzyskać więcej informacji o węzłach aprowizacji, zobacz temat [Inicjowanie obsługi węzłów dla rozwiązań VMware przez CloudSimple — artykuł platformy Azure](create-nodes.md) .

Przydziel zakres CIDR dla podsieci vSphere/sieci vSAN dla chmury prywatnej. Chmura prywatna jest tworzona jako izolowany środowisko VMware (hosty ESXi, vCenter, sieci vSAN i NSX) zarządzane przez serwer vCenter. Składniki zarządzania są wdrażane w sieci wybranej dla podsieci vSphere/sieci vSAN CIDR. Zakres CIDR sieci jest podzielony na różne podsieci podczas wdrażania.  Przestrzeń adresów podsieci vSphere/sieci vSAN musi być unikatowa. Nie może pokrywać się z żadną siecią, która komunikuje się ze środowiskiem CloudSimple.  Sieci, które komunikują się z usługą CloudSimple, obejmują sieci lokalne i sieci wirtualne platformy Azure.  Aby uzyskać więcej informacji na temat podsieci vSphere/sieci vSAN, zobacz [sieci VLAN i podsieci — Omówienie](cloudsimple-vlans-subnets.md).

* Minimalna prefiks zakresu CIDR vSphere/sieci vSAN:/24 
* Maksymalna vSphere/sieci vSAN prefiks zakresu CIDR:/21

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Uzyskiwanie dostępu do portalu CloudSimple

Dostęp do [portalu CloudSimple](access-cloudsimple-portal.md).

## <a name="create-a-new-private-cloud"></a>Utwórz nową chmurę prywatną

1. Na stronie **zasoby** kliknij pozycję **Nowa Chmura prywatna**.

    ![Tworzenie chmury prywatnej — Rozpoczynanie pracy](media/create-pc-button.png)

2. Wybierz lokalizację, w której mają być hostowane zasoby chmury prywatnej.

3. Wybierz typ węzła CS28 lub CS36 you'ev zainicjowany dla chmury prywatnej. Ta ostatnia opcja obejmuje maksymalną moc obliczeniową i pojemność pamięci.

4. Wybierz liczbę węzłów dla chmury prywatnej. Można wybrać najwyżej dostępną liczbę węzłów, które you'ev. [](create-nodes.md)

    ![Tworzenie chmury prywatnej — ustawienia podstawowe](media/create-private-cloud-basic-info.png)

5. Kliknij pozycję **Next: Opcje**zaawansowane.

6. Wprowadź zakres CIDR dla podsieci vSphere/sieci vSAN. Upewnij się, że zakres CIDR nie nakłada się na żadną z lokalizacji lokalnych lub innych podsieci platformy Azure (sieci wirtualnych) lub z podsiecią bramy.  Nie należy używać żadnego zakresu CIDR zdefiniowanego w sieciach wirtualnych platformy Azure.
    
    **Opcje zakresu CIDR:** /24,/23,/22 lub/21. Zakres CIDR/24 obsługuje maksymalnie dziewięć węzłów, a/23 zakres CIDR obsługuje do 41 węzłów, a zakres CIDR/22 i/21 obsługuje maksymalnie 64 węzłów (maksymalną liczbę węzłów w chmurze prywatnej).

    > [!CAUTION]
    > Adresy IP w zakresie CIDR vSphere/sieci vSAN są zarezerwowane do użytku przez infrastrukturę chmury prywatnej.  Nie używaj adresu IP z tego zakresu na żadnej maszynie wirtualnej.

7. Kliknij pozycję **Next: Przejrzyj i Utwórz**.

8. Przejrzyj ustawienia. Jeśli musisz zmienić dowolne ustawienia, kliknij przycisk **Wstecz**.

9. Kliknij przycisk **Utwórz**.

Inicjowanie obsługi chmury prywatnej rozpocznie się po kliknięciu przycisku Utwórz.  Postęp można monitorować na stronie [zadania](https://docs.azure.cloudsimple.com/activity/#tasks) w portalu CloudSimple.  Inicjowanie obsługi może potrwać od 30 minut do 2 godzin.  Po zakończeniu aprowizacji otrzymasz wiadomość e-mail.

## <a name="next-steps"></a>Następne kroki

* [Rozwiń chmurę prywatną](expand-private-cloud.md)
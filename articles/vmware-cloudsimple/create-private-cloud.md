---
title: Rozwiązanie Azure VMware firmy CloudSimple — tworzenie chmury prywatnej CloudProste
description: W tym artykule opisano sposób tworzenia chmury CloudSimple Private Cloud w celu rozszerzenia obciążeń VMware na chmurę z elastycznością i ciągłością działania
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4f700ac34b6c6e2a651366bee7dd1785c608064f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024793"
---
# <a name="create-a-cloudsimple-private-cloud"></a>Tworzenie chmury prywatnej CloudSimple

Private Cloud to izolowany stos VMware, który obsługuje hosty ESXi, vCenter, vSAN i NSX. Chmury prywatne są zarządzane za pośrednictwem portalu CloudSimple. Mają własny serwer vCenter we własnej domenie zarządzania. Stos działa na dedykowanych węzłach i izolowanych węzłach sprzętowych z gołym metalem.

Tworzenie chmury prywatnej pomaga zaspokoić różne typowe potrzeby infrastruktury sieciowej:

* **Wzrost gospodarczy**. Jeśli osiągnięto punkt odświeżania sprzętu dla istniejącej infrastruktury, chmura prywatna umożliwia rozszerzenie bez konieczności inwestowania w nowy sprzęt.

* **Szybka ekspansja**. Jeśli pojawią się jakieś tymczasowe lub nieplanowane potrzeby w zakresie pojemności, chmura prywatna umożliwia utworzenie dodatkowej pojemności bez opóźnień.

* **Zwiększona ochrona**. Dzięki prywatnej chmurze z co najmniej trzema węzłami otrzymujesz automatyczną nadmiarowość i ochronę o wysokiej dostępności.

* **Długoterminowe potrzeby infrastrukturalne**. Jeśli centra danych są w stanie pojemności lub chcesz zrestrukturyzować się w celu obniżenia kosztów, private cloud umożliwia wycofanie centrów danych i migrację do rozwiązania opartego na chmurze, pozostając jednocześnie zgodne z operacjami przedsiębiorstwa.

Podczas tworzenia chmury prywatnej, otrzymasz jeden klaster vSphere i wszystkie maszyny wirtualne zarządzania, które są tworzone w tym klastrze.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Węzły muszą być aprowizowane przed utworzeniem chmury prywatnej. Aby uzyskać więcej informacji na temat inicjowania obsługi administracyjnej węzłów, zobacz [Inicjowanie obsługi administracyjnej węzłów rozwiązania Azure VMware przez CloudSimple](create-nodes.md).

Przydziel zakres CIDR dla podsieci vSphere/vSAN dla chmury prywatnej. Private Cloud jest tworzony jako izolowane środowisko stosu VMware (z hostami ESXi, vCenter, vSAN i NSX) zarządzanym przez serwer vCenter. Składniki zarządzania są wdrażane w sieci wybranej dla podsieci CIDR vSphere/vSAN. Zakres CIDR sieci jest podzielony na różne podsieci podczas wdrażania. Przestrzeń adresowa podsieci vSphere/vSAN musi być unikatowa. Nie może pokrywać się z żadną siecią, która komunikuje się ze środowiskiem CloudSimple. Sieci, które komunikują się z CloudSimple obejmują sieci lokalne i sieci wirtualne platformy Azure. Aby uzyskać więcej informacji na temat podsieci vSphere/vSAN, zobacz omówienie sieci VPN i podsieci.

* Minimalny prefiks zakresu podsieci vSphere/vSAN: /24
* Maksymalny prefiks zakresu podsieci vSphere/vSAN: /21


## <a name="access-the-cloudsimple-portal"></a>Uzyskiwanie dostępu do portalu CloudSimple

Uzyskaj dostęp do [portalu CloudSimple](access-cloudsimple-portal.md).

## <a name="create-a-new-private-cloud"></a>Tworzenie nowej chmury prywatnej

1. Wybierz pozycję **Wszystkie usługi**.
2. Wyszukaj **usługi CloudSimple**.
3. Wybierz usługę CloudSimple, w której chcesz utworzyć chmurę prywatną.
4. Z **przeglądu**kliknij pozycję **Utwórz chmurę prywatną,** aby otworzyć nową kartę przeglądarki dla portalu CloudSimple. Jeśli zostanie wyświetlony monit, zaloguj się przy użyciu poświadczeń logowania platformy Azure.

    ![Tworzenie chmury prywatnej z platformy Azure](media/create-private-cloud-from-azure.png)

5. W portalu CloudSimple podaj nazwę chmury prywatnej.
6. Wybierz **lokalizację** dla chmury prywatnej.
7. Wybierz **typ węzła,** zgodne z tym, co aprowizowana na platformie Azure.
8. Określ **liczbę węzłów**.  Do utworzenia chmury prywatnej wymagane są co najmniej trzy węzły.

    ![Tworzenie chmury prywatnej — podstawowe informacje](media/create-private-cloud-basic-info.png)

9. Kliknij **przycisk Dalej: Opcje zaawansowane**.
10. Wprowadź zakres CIDR dla podsieci vSphere/vSAN. Upewnij się, że zakres CIDR nie pokrywa się z żadnym z lokalnych lub innych podsieci platformy Azure (sieci wirtualnych) lub z podsiecią bramy.

    **Opcje zakresu CIDR:** /24, /23, /22 lub /21. Zakres /24 CIDR obsługuje do dziewięciu węzłów, /23 zakres CIDR obsługuje do 41 węzłów, a /22 i /21 zakres CIDR obsługuje do 64 węzłów (maksymalna liczba węzłów w chmurze prywatnej).

    > [!IMPORTANT]
    > Adresy IP w zakresie cidr vSphere/vSAN są zarezerwowane do użytku przez infrastrukturę private cloud.  Nie używaj adresu IP w tym zakresie na żadnej maszynie wirtualnej.

11. Kliknij **przycisk Dalej: Przejrzyj i utwórz**plik .
12. Przejrzyj ustawienia. Jeśli chcesz zmienić ustawienia, kliknij przycisk **Poprzedni**.
13. Kliknij przycisk **Utwórz**.

Rozpoczyna się proces inicjowania obsługi administracyjnej usługi Private Cloud. Może upłynąć do dwóch godzin dla chmury prywatnej do obsługi administracyjnej.

Aby uzyskać instrukcje dotyczące rozwijania istniejącej chmury [prywatnej, zobacz Rozszerzanie chmury prywatnej](expand-private-cloud.md).

---
title: Rozwiązanie VMware firmy Azure według CloudSimple — tworzenie CloudSimple prywatnej chmury
description: Opisuje, jak utworzyć chmurę prywatną CloudSimple do rozbudowania obciążeń programu VMware do chmury z elastycznością operacyjną i ciągłością
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: aacdb57c312946a9ec2b17a8d41aa9150efc277d
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640966"
---
# <a name="create-a-cloudsimple-private-cloud"></a>Utwórz chmurę prywatną CloudSimple

Chmura prywatna to izolowany stos oprogramowania VMware obsługujący hosty ESXi, vCenter, sieci vSAN i NSX. Chmurami prywatnymi zarządza się za pomocą portalu CloudSimple. Mają własny serwer vCenter we własnej domenie zarządzania. Stos działa na dedykowanych węzłach i izolowanych węzłach sprzętowych bez systemu operacyjnego.

Tworzenie chmury prywatnej pomaga sprostać różnym typowym potrzebom związanym z infrastrukturą sieci:

* **Wzrostu**. Jeśli do istniejącej infrastruktury został osiągnięty punkt odświeżenia sprzętowego, Chmura prywatna pozwala rozszerzyć system bez konieczności używania nowych inwestycji sprzętowych.

* **Szybkie rozszerzanie**. Jeśli wystąpią jakiekolwiek tymczasowe lub nieplanowane zapotrzebowanie na pojemność, Chmura prywatna pozwala na utworzenie dodatkowej pojemności bez opóźnień.

* **Zwiększona ochrona**. W przypadku chmury prywatnej co najmniej trzech węzłów otrzymujesz automatyczną nadmiarowość i ochronę o wysokiej dostępności.

* **Długoterminowe potrzeby dotyczące infrastruktury**. Jeśli centra danych mają pojemność lub chcesz zmienić strukturę w celu obniżenia kosztów, Chmura prywatna pozwala na wycofanie centrów danych i migrację do rozwiązania opartego na chmurze, a pozostałe zgodne z operacjami przedsiębiorstwa.

Podczas tworzenia chmury prywatnej otrzymujesz jeden klaster vSphere i wszystkie maszyny wirtualne zarządzania utworzone w tym klastrze.

## <a name="access-the-cloudsimple-portal"></a>Uzyskiwanie dostępu do portalu CloudSimple

Dostęp do [portalu CloudSimple](access-cloudsimple-portal.md).

## <a name="create-a-new-private-cloud"></a>Utwórz nową chmurę prywatną

1. Wybierz pozycję **Wszystkie usługi**.
2. Wyszukaj **usługi CloudSimple Services**.
3. Wybierz usługę CloudSimple, w której chcesz utworzyć chmurę prywatną.
4. W obszarze **Przegląd**kliknij pozycję **Utwórz chmurę prywatną** , aby otworzyć nową kartę przeglądarki dla portalu CloudSimple. Jeśli zostanie wyświetlony monit, zaloguj się przy użyciu poświadczeń logowania na platformie Azure.

    ![Utwórz chmurę prywatną na platformie Azure](media/create-private-cloud-from-azure.png)

5. W portalu CloudSimple Podaj nazwę chmury prywatnej.
6. Wybierz **lokalizację** chmury prywatnej.
7. Wybierz **Typ węzła**spójny z zakupionymi informacjami na platformie Azure.  Można wybrać [opcję CS28 lub CS36](cloudsimple-node.md#vmware-solution-by-cloudsimple-nodes-sku). Ta ostatnia opcja obejmuje maksymalną moc obliczeniową i pojemność pamięci.
8. Określ **liczbę węzłów**.  Aby można było utworzyć chmurę prywatną, wymagane są co najmniej trzy węzły.

    ![Tworzenie chmury prywatnej — informacje podstawowe](media/create-private-cloud-basic-info.png)

9. Kliknij pozycję **Next: Opcje**zaawansowane.
10. Wprowadź zakres CIDR dla podsieci vSphere/sieci vSAN. Upewnij się, że zakres CIDR nie nakłada się na żadną z lokalizacji lokalnych lub innych podsieci platformy Azure (sieci wirtualnych) lub z podsiecią bramy.

    **Opcje zakresu CIDR:** /24,/23,/22 lub/21. Zakres CIDR/24 obsługuje maksymalnie dziewięć węzłów, a/23 zakres CIDR obsługuje do 41 węzłów, a zakres CIDR/22 i/21 obsługuje maksymalnie 64 węzłów (maksymalną liczbę węzłów w chmurze prywatnej).

    > [!IMPORTANT]
    > Adresy IP w zakresie CIDR vSphere/sieci vSAN są zarezerwowane do użytku przez infrastrukturę chmury prywatnej.  Nie używaj adresu IP z tego zakresu na żadnej maszynie wirtualnej.

11. Kliknij pozycję **Next: Przejrzyj i Utwórz**.
12. Przejrzyj ustawienia. Jeśli musisz zmienić dowolne ustawienia, kliknij przycisk **Wstecz**.
13. Kliknij przycisk **Utwórz**.

Rozpocznie się proces aprowizacji w chmurze prywatnej. Zainicjowanie obsługi chmury prywatnej może potrwać do dwóch godzin.

Aby uzyskać instrukcje dotyczące rozwinięcia istniejącej chmury prywatnej, zobacz [Rozwiń chmurę prywatną](expand-private-cloud.md).

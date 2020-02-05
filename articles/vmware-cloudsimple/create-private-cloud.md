---
title: Azure VMware Solutions (Automatyczna synchronizacja) — Utwórz chmurę prywatną
description: Zawiera opis sposobu tworzenia chmury prywatnej do automatycznej synchronizacji w celu zwiększenia obciążeń programu VMware do chmury z elastycznością operacyjną i ciągłością
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 128a0a1eec03878d0deba93048c54324aab7d888
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024793"
---
# <a name="create-an-avs-private-cloud"></a>Utwórz chmurę prywatną do automatycznej synchronizacji

Chmura prywatna w wersji zaautomatycznej to izolowany stos oprogramowania VMware obsługujący hosty ESXi, vCenter, sieci vSAN i NSX. W portalu do automatycznej synchronizacji są zarządzane chmury prywatne. Mają własny serwer vCenter we własnej domenie zarządzania. Stos działa na dedykowanych węzłach i izolowanych węzłach sprzętowych bez systemu operacyjnego.

Tworzenie chmury prywatnej do automatycznej synchronizacji pomaga sprostać różnym typowym potrzebom związanym z infrastrukturą sieci:

* **Wzrostu**. Jeśli do istniejącej infrastruktury został osiągnięty punkt odświeżenia sprzętu, Chmura prywatna w wersji zaawansowanej pozwala rozwijać i nie wymagać nowych inwestycji w sprzęt.

* **Szybkie rozszerzanie**. Jeśli wystąpią jakiekolwiek tymczasowe lub nieplanowane zapotrzebowanie na pojemność, Chmura prywatna do automatycznej synchronizacji umożliwia utworzenie dodatkowej pojemności bez opóźnień.

* **Zwiększona ochrona**. W przypadku chmury prywatnej z co najmniej trzech węzłów otrzymujesz automatyczną nadmiarowość i ochronę o wysokiej dostępności.

* **Długoterminowe potrzeby dotyczące infrastruktury**. Jeśli Twoje centra danych mają pojemność lub chcesz zmienić strukturę w celu obniżenia kosztów, Chmura prywatna do automatycznej synchronizacji pozwala na wycofanie centrów danych i migrację do rozwiązania opartego na chmurze, a pozostałe są zgodne z operacjami przedsiębiorstwa.

Podczas tworzenia chmury prywatnej do automatycznej synchronizacji można uzyskać jeden klaster vSphere i wszystkie maszyny wirtualne zarządzania utworzone w tym klastrze.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby można było utworzyć chmurę prywatną do automatycznej synchronizacji, węzły muszą być obsługiwane. Aby uzyskać więcej informacji o węzłach aprowizacji, zobacz temat [udostępnianie węzłów dla rozwiązań VMware platformy Azure (Automatyczna synchronizacja)](create-nodes.md).

Przydziel zakres CIDR dla podsieci vSphere/sieci vSAN dla chmury prywatnej automatycznej synchronizacji. Chmura prywatna automatycznej synchronizacji jest tworzona jako izolowane środowisko stosu VMware (z hostami ESXi, vCenter, sieci vSAN i NSX) zarządzanych przez serwer vCenter. Składniki zarządzania są wdrażane w sieci wybranej dla podsieci vSphere/sieci vSAN CIDR. Zakres CIDR sieci jest podzielony na różne podsieci podczas wdrażania. Przestrzeń adresów podsieci vSphere/sieci vSAN musi być unikatowa. Nie może pokrywać się z żadną siecią, która komunikuje się ze środowiskiem automatycznej synchronizacji. Sieci, które komunikują się z funkcją automatycznej synchronizacji, obejmują sieci lokalne i sieci wirtualne platformy Azure. Aby uzyskać więcej informacji na temat podsieci vSphere/sieci vSAN, zobacz sieci VLAN i podsieci — Omówienie.

* Minimalna prefiks zakresu CIDR vSphere/sieci vSAN:/24
* Maksymalna vSphere/sieci vSAN prefiks zakresu CIDR:/21


## <a name="access-the-avs-portal"></a>Dostęp do portalu automatycznej synchronizacji

Uzyskaj dostęp do [portalu automatycznej synchronizacji](access-cloudsimple-portal.md).

## <a name="create-a-new-avs-private-cloud"></a>Utwórz nową chmurę prywatną do automatycznej synchronizacji

1. Wybierz pozycję **Wszystkie usługi**.
2. Wyszukaj **usługi automatycznej synchronizacji**.
3. Wybierz usługę automatycznej synchronizacji, w której chcesz utworzyć swoją chmurę prywatną do automatycznej synchronizacji.
4. W obszarze **Przegląd**kliknij pozycję **Utwórz swoją chmurę prywatną** , aby otworzyć nową kartę przeglądarki dla portalu automatycznej synchronizacji. Jeśli zostanie wyświetlony monit, zaloguj się przy użyciu poświadczeń logowania na platformie Azure.

    ![Utwórz chmurę prywatną na platformie Azure](media/create-private-cloud-from-azure.png)

5. W portalu automatycznej synchronizacji Podaj nazwę chmury prywatnej do automatycznej synchronizacji.
6. Wybierz **lokalizację** chmury prywatnej do automatycznej synchronizacji.
7. Wybierz **Typ węzła**spójny z zakupionymi informacjami na platformie Azure.
8. Określ **liczbę węzłów**. Do utworzenia chmury prywatnej automatycznej synchronizacji wymagane są co najmniej trzy węzły.
5. W portalu CloudSimple Podaj nazwę chmury prywatnej.
6. Wybierz **lokalizację** chmury prywatnej.
7. Wybierz **Typ węzła**, spójny z zainicjowaną obsługą na platformie Azure.
8. Określ **liczbę węzłów**.  Aby można było utworzyć chmurę prywatną, wymagane są co najmniej trzy węzły.

    ![Tworzenie automatycznej synchronizacji w chmurze prywatnej — informacje podstawowe](media/create-private-cloud-basic-info.png)

9. Kliknij przycisk **Dalej: Opcje zaawansowane**.
10. Wprowadź zakres CIDR dla podsieci vSphere/sieci vSAN. Upewnij się, że zakres CIDR nie nakłada się na żadną z lokalizacji lokalnych lub innych podsieci platformy Azure (sieci wirtualnych) lub z podsiecią bramy.

    **Opcje zakresu CIDR:** /24,/23,/22 lub/21. Zakres CIDR/24 obsługuje maksymalnie dziewięć węzłów, a/23 zakres CIDR obsługuje do 41 węzłów, a zakres między/22 i/21 obsługuje maksymalnie 64 węzłów (maksymalną liczbę węzłów w chmurze prywatnej automatycznej synchronizacji).

    > [!IMPORTANT]
    > Adresy IP w zakresie CIDR vSphere/sieci vSAN są zarezerwowane do użytku przez infrastrukturę chmury prywatnej do automatycznej synchronizacji. Nie używaj adresu IP z tego zakresu na żadnej maszynie wirtualnej.

11. Kliknij przycisk **Dalej: Przejrzyj i Utwórz**.
12. Przejrzyj ustawienia. Jeśli musisz zmienić dowolne ustawienia, kliknij przycisk **Wstecz**.
13. Kliknij pozycję **Utwórz**.

Trwa uruchamianie procesu inicjowania obsługi administracyjnej chmury prywatnej. Zainicjowanie obsługi automatycznej synchronizacji z chmurą prywatną może trwać do dwóch godzin.

Aby uzyskać instrukcje dotyczące rozwinięcia istniejącej chmury prywatnej do automatycznej synchronizacji, zobacz sekcję [Rozwiń chmurę prywatną](expand-private-cloud.md).

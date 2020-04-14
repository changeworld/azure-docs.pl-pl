---
title: Często zadawane pytania
description: Odpowiedzi na często zadawane pytania związane z usługą Wystąpienia kontenerów platformy Azure
author: dkkapur
ms.topic: article
ms.date: 04/10/2020
ms.openlocfilehash: 8730dcb24af61730d7f93ea37a53cf87435eb9f9
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261622"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>Często zadawane pytania dotyczące wystąpień kontenerów platformy Azure

Ten artykuł dotyczy często zadawanych pytań dotyczących wystąpień kontenera platformy Azure.

## <a name="deployment"></a>Wdrożenie

### <a name="how-large-can-my-container-image-be"></a>Jak duży może być mój obraz kontenera?

Maksymalny rozmiar obrazu kontenera można wdrożyć w przypadku wystąpienia kontenera platformy Azure wynosi 15 GB. Można wdrożyć większe obrazy w zależności od dokładnej dostępności w momencie wdrażania, ale nie jest to gwarantowane.

Rozmiar obrazu kontenera wpływa na czas wdrażania, więc ogólnie chcesz zachować obrazy kontenerów tak małe, jak to możliwe.

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>Jak przyspieszyć wdrażanie kontenera?

Ponieważ jednym z głównych czynników warunkujących czas wdrażania jest rozmiar obrazu, poszukaj sposobów na zmniejszenie rozmiaru. Usuń niepotrzebne warstwy lub zmniejsz rozmiar warstw obrazu (wybierając jaśniejszy obraz systemu operacyjnego). Na przykład, jeśli używasz kontenerów Linuksa, rozważ użycie Alpine jako obrazu podstawowego, a nie pełnego serwera Ubuntu. Podobnie w przypadku kontenerów systemu Windows należy użyć obrazu podstawowego serwera Nano Server, jeśli to możliwe. 

Należy również sprawdzić listę wstępnie buforowanych obrazów w usłudze Azure Container Images, dostępne za pośrednictwem interfejsu API [listy obrazów buforowanych.](/rest/api/container-instances/listcachedimages) Można przełączyć warstwę obrazu dla jednego z wstępnie buforowanych obrazów. 

Zobacz bardziej [szczegółowe wskazówki dotyczące](container-instances-troubleshooting.md#container-takes-a-long-time-to-start) skracenia czasu uruchamiania kontenera.

### <a name="what-windows-base-os-images-are-supported"></a>Jakie podstawowe obrazy systemu operacyjnego systemu Windows są obsługiwane?

#### <a name="windows-server-2016-base-images"></a>Obrazy podstawowe systemu Windows Server 2016

* [Nano](https://hub.docker.com/_/microsoft-windows-nanoserver)Serwer `10.0.14393.x`: ,`sac2016`
* [Rdzeń](https://hub.docker.com/_/microsoft-windows-servercore)systemu `ltsc2016`Windows Server : ,`10.0.14393.x`

> [!NOTE]
> Obrazy systemu Windows oparte na wersji kanału półrocznego 1709 lub 1803 nie są obsługiwane.

#### <a name="windows-server-2019-and-client-base-images-preview"></a>Windows Server 2019 i obrazy podstawowe klienta (wersja zapoznawcza)

* [Nano](https://hub.docker.com/_/microsoft-windows-nanoserver)Server `1809` `10.0.17763.914` : lub wcześniej
* [Windows Server](https://hub.docker.com/_/microsoft-windows-servercore) `ltsc2019`Core `1809` `10.0.17763.914` : , lub wcześniej
* [Windows](https://hub.docker.com/_/microsoft-windows) `1809`: `10.0.17763.914` lub wcześniej

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>Jakiej warstwy obrazu .NET lub .NET Core należy użyć w moim kontenerze? 

Użyj najmniejszego obrazu, który spełnia Twoje wymagania. W systemie Linux można użyć obrazu *programu runtime alpine* .NET Core, który był obsługiwany od czasu wydania programu .NET Core 2.1. W systemie Windows, jeśli używasz pełnego programu .NET Framework, należy użyć obrazu rdzenia systemu Windows Server (obraz tylko do środowiska wykonawczego, na przykład *4.7.2-windowsservercore-ltsc2016*). Obrazy tylko w czasie wykonywania są mniejsze, ale nie obsługują obciążeń, które wymagają sdk .NET.

## <a name="availability-and-quotas"></a>Dostępność i przydziały

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>Ile rdzeni i pamięci należy przydzielić dla moich kontenerów lub grupy kontenerów?

To naprawdę zależy od obciążenia. Rozpocznij małe i przetestować wydajność, aby zobaczyć, jak kontenery zrobić. [Monitoruj użycie zasobów procesora CPU i pamięci,](container-instances-monitor.md)a następnie dodaj rdzenie lub pamięć na podstawie rodzaju procesów wdrażanych w kontenerze. 

Upewnij się również, aby sprawdzić [dostępność zasobów](container-instances-region-availability.md#availability---general) dla regionu, w który wdrażasz dla górnych granic na rdzeniach procesora CPU i pamięci dostępnej dla grupy kontenerów. 

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>Na jakiej infrastrukturze bazowej działa ACI?

Instancja kontenerów platformy Azure ma być usługą kontenerów bezserwerowych na żądanie, dlatego chcemy, abyś skupił się na opracowywaniu kontenerów i nie martwił się o infrastrukturę! Dla tych, którzy są ciekawi lub chcą zrobić porównania wydajności, ACI działa na zestawach maszyn wirtualnych platformy Azure różnych jednostek SKU, głównie z serii F i D. Oczekujemy, że zmieni się to w przyszłości, ponieważ będziemy nadal rozwijać i optymalizować usługę. 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>Chcę wdrożyć tysiące rdzeni na ACI - czy mogę zwiększyć kwotę?
 
Tak (czasami). Zobacz artykuł [o przydziałach i limitach](container-instances-quotas.md) dla bieżących przydziałów i limitów, które można zwiększyć na żądanie.

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>Czy mogę wdrożyć z więcej niż 4 rdzeniami i 16 GB pamięci RAM?

Jeszcze nie. Obecnie są to wartości maksymalne dla grupy kontenerów. Skontaktuj się z pomocą techniczną platformy Azure, aby uzyskać określone wymagania lub żądania. 

### <a name="when-will-aci-be-in-a-specific-region"></a>Kiedy ACI będzie w danym regionie?

Bieżąca dostępność regionu jest publikowana [tutaj](container-instances-region-availability.md#availability---general). Jeśli masz wymagania dotyczące określonego regionu, skontaktuj się z pomocą techniczną platformy Azure.

## <a name="features-and-scenarios"></a>Funkcje i scenariusze

### <a name="how-do-i-scale-a-container-group"></a>Jak skalować grupę kontenerów?

Obecnie skalowanie nie jest dostępne dla kontenerów lub grup kontenerów. Jeśli chcesz uruchomić więcej wystąpień, użyj naszego interfejsu API, aby zautomatyzować i utworzyć więcej żądań tworzenia grupy kontenerów do usługi. 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>Jakie funkcje są dostępne dla wystąpień uruchomionych w niestandardowej sieci wirtualnej?

Można [wdrożyć grupy kontenerów w](container-instances-vnet.md) wybranej sieci wirtualnej platformy Azure i delegować prywatne adresy IP do grup kontenerów, aby kierować ruch w sieci wirtualnej w zasobach platformy Azure. Wdrażanie grupy kontenerów w sieci wirtualnej jest obecnie dostępne dla obciążeń produkcyjnych w podzbiór regionów platformy Azure.

## <a name="pricing"></a>Cennik

### <a name="when-does-the-meter-start-running"></a>Kiedy licznik zacznie działać?

Czas trwania grupy kontenerów jest obliczany od momentu rozpoczęcia ściągania obrazu pierwszego kontenera (dla nowego wdrożenia) lub ponowne uruchomienie grupy kontenerów (jeśli jest już wdrożone), dopóki grupa kontenerów nie zostanie zatrzymana. Zobacz szczegóły w [cenniku wystąpień kontenerów](https://azure.microsoft.com/pricing/details/container-instances/).

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>Czy przestanę być naliczane opłaty, gdy kontenery zostaną zatrzymane?

Liczniki przestają działać po zatrzymaniu całej grupy kontenerów. Tak długo, jak kontener w grupie kontenerów jest uruchomiony, przechowujemy zasoby w przypadku, gdy chcesz ponownie uruchomić kontenery. 

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej](container-instances-overview.md) o wystąpieniach kontenera platformy Azure.
* [Rozwiązywanie typowych problemów](container-instances-troubleshooting.md) w wystąpieniach kontenera platformy Azure.
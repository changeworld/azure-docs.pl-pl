---
title: Chmur prywatnych w VMware Solution by CloudSimple - Azure
description: Więcej informacji na temat CloudSimple chmur prywatnych i pojęcia.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: dc07b4eea553e6cb3d9b522826e860ddbfbc1513
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64577048"
---
# <a name="cloudsimple-private-cloud-overview"></a>Omówienie chmury prywatnej CloudSimple

CloudSimple przekształca i rozszerza obciążeń oprogramowania VMware z chmurami publicznymi w ciągu kilku minut. Korzystanie z niej CloudSimple, można wdrożyć VMware natywnie w infrastrukturze platformy Azure bez systemu operacyjnego na komputerach. Wdrożenie, znajduje się w lokalizacji platformy Azure i jest w pełni zintegrowany z pozostałą częścią chmury platformy Azure.

* Rozwiązanie CloudSimple zapewnia pełną VMware ciągłość operacyjną. To rozwiązanie zapewnia zalety chmury publicznej:
  * Elastyczność
  * Innowacja
  * Wydajność
* Za pomocą CloudSimple możesz skorzystać z modelu użycia chmury, która zmniejsza całkowity koszt posiadania. Zapewnia ona również na żądanie aprowizacji, płatność jako — liczbę i optymalizacji wydajności.
* CloudSimple jest w pełni zgodna z usługą:
  * Istniejące narzędzia
  * Umiejętności
  * Procesy
* Ta zgodność umożliwia zespołom do zarządzania obciążeniami w chmurze platformy Azure, bez zakłócania pracy zasad:
  * Sieć
  * Bezpieczeństwo  
  * Ochrona danych  
  * Inspekcja
* CloudSimple zarządza infrastrukturą i wszystkie wymagane usługi zarządzania i sieci. Usługa CloudSimple umożliwia zespołowi skoncentrować się na:
  * Wartość biznesowa
  * Aprowizacja aplikacji
  * Ciągłość działalności biznesowej
  * Pomoc techniczna
  * Wymuszanie zasad

## <a name="private-cloud-environment-overview"></a>Omówienie środowiska chmury prywatnej

Chmura prywatna to izolowane stos oprogramowania VMware, takich jak tych środowisk:

* Hosty ESXi
* vCenter
* vSAN
* NSX

Chmury prywatne są zarządzane przez serwer vCenter w domenie zarządzania.

Stos jest uruchamiane:

* Węzły dedykowane
* Izolowany bez sprzętu bez systemu operacyjnego węzłów

Użytkownicy korzystają z stosu za pośrednictwem natywnych narzędzi VMware, takich jak:

* vCenter
* Menedżer NSX

Możesz wdrożyć węzły dedykowane w lokalizacji platformy Azure. Można następnie zarządzać nimi przy użyciu platformy Azure i CloudSimple. Chmura prywatna składa się z co najmniej jeden klaster vSphere, a każdy klaster zawiera 3 – 16 węzłów.

Aby utworzyć chmury prywatnej, używając kupić węzły:

* Płatność za rzeczywiste użycie węzłów
* Węzły zastrzeżone, dedykowanej

Chmurę prywatną można połączyć do środowiska lokalnego i sieci platformy Azure przy użyciu następujących połączeń:

* Bezpieczeństwo
* Prywatne sieci VPN
* Usługa ExpressRoute systemu Azure

W środowisku chmury prywatnej została zaprojektowana, aby wyeliminować wdrożeń z pojedynczym punktem awarii:

* Klastry ESXi są skonfigurowane przy użyciu vSphere wysokiej dostępności i mają rozmiar występuje co najmniej jeden węzeł wolnym pod kątem odporności.
* sieć vsan, która zawiera nadmiarowe podstawowy magazyn. sieć vsan, która wymaga co najmniej trzy węzły w celu zapewnienia ochrony przed awarią jednego. Można skonfigurować sieć vSAN, aby zapewnić odporność wyższe w przypadku większych klastrów.
* VCenter, PSC i maszyny wirtualne NSX Manager można skonfigurować za pomocą macierzy RAID 10 magazynu zasad w celu ochrony przed awariami magazynu. Następnie są one chronione przez vSphere HA przed awariami węzła i sieci.

## <a name="scenarios-for-deploying-a-private-cloud"></a>Scenariusze wdrażania chmury prywatnej

* **Migracji lub wycofywania centrum danych**

  * Pobierz dodatkowej pojemności, gdy będziesz osiągać limity swojego istniejącego centrum danych lub odświeżania sprzętu.
  * Dodaj wymagane pojemności w chmurze i wyeliminować problemy z zarządzaniem sprzętu.
  * Zmniejsz ryzyko i koszty migracji w chmurze, w porównaniu do konwersji czasochłonne lub rearchitecture.
  * Skorzystaj ze znanych narzędzi VMware i umiejętności, aby przyspieszyć migracje do chmury. W chmurze należy za pomocą usług Azure modernizowanie aplikacji własnym tempie.

* **Rozwiń węzeł na żądanie**

  * Rozwiń do chmury do potrzeb nieprzewidziane, takie jak nowe środowiska programowania lub wzrosty sezonowych pojemności.
  * Utwórz nową pojemność na żądanie i Zachowaj ją tylko tak długo, jak długo będą potrzebne.
  * Zmniejszenie kosztów ponoszonych z góry inwestycji, przyspieszyć szybkie Inicjowanie obsługi administracyjnej i ograniczyć złożoność przy użyciu tego samego architektury i zasad zarówno lokalnych, jak i w chmurze.

* **Odzyskiwanie po awarii i pulpitów wirtualnych w chmurze platformy Azure**

  * Nawiąż połączenie zdalne z dane, aplikacje i komputery stacjonarne w chmurze platformy Azure. W przypadku połączeń o wysokiej przepustowości przekazywanie / pobieranie danych szybko, aby odzyskać z zdarzeniami. Sieci o małych opóźnieniach zapewniają szybkie odpowiedzi razy, Użytkownicy oczekują z aplikacji klasycznej.

  * Replikacja zasad i sieci w chmurze przy użyciu portalu CloudSimple i dobrze znanych narzędzi VMware. Ta replikacja zmniejsza nakład pracy oraz ryzyko tworzenie i zarządzanie nimi wdrożenia odzyskiwania po awarii i infrastruktury VDI.

* **Aplikacje o wysokiej wydajności i bazy danych**

  * Uruchamianie najbardziej wymagających obciążeń, przy użyciu architektury hiperzbieżnego, dostarczone przez CloudSimple.
  * Uruchom, Oracle, programu Microsoft SQL server, systemów oprogramowania pośredniczącego i baz danych o wysokiej wydajności nie SQL.

  * Korzystaj z chmury, jak własne centrum danych z połączenia sieciowe o dużej szybkości, 25 GB. Połączenia o dużej szybkości umożliwia uruchamianie aplikacji hybrydowych obejmujących lokalnych, programu VMware na platformie Azure, i prywatnego obciążeń platformy Azure bez uszczerbku dla wydajności.

* **Wartość true, hybrydowe**

  * Ujednolicenie metodyki DevOps w VMware i usług systemu Azure.
  * Optymalizuj VMware administracji dla usług platformy Azure i rozwiązań, które mogą być stosowane dla wszystkich obciążeń.
  * Dostęp do usług w chmurze publicznej bez konieczności zwiększania centrum danych lub Przekształcanie aplikacji.
  * Scentralizowanie tożsamości, zasady kontroli dostępu, rejestrowania i monitorowania aplikacji programu VMware na platformie Azure.

## <a name="limits"></a>Limits

W poniższej tabeli przedstawiono limity węzła zasobów chmury prywatnej.

| Resource | Limit |
|----------|-------|
| Minimalna liczba węzłów w celu utworzenia chmury prywatnej | 3 |
| Maksymalna liczba węzłów w klastrze w chmurze prywatnej | 16 |
| Maksymalna liczba węzłów w chmurze prywatnej | 64 |
| Minimalna liczba węzłów w nowym klastrze | 3 |

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [utworzyć chmurę prywatną](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Dowiedz się, jak [konfigurowania środowiska chmury prywatnej](quickstart-create-private-cloud.md)
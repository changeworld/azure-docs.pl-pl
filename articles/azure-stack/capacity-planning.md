---
title: Planowanie pojemności dla usługi Azure Stack | Dokumentacja firmy Microsoft
description: Więcej informacji o pojemności Planowanie wdrożenia usługi Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2019
ms.author: jeffgilb
ms.reviewer: prchint
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: e4678b445dce5b337fb7d51e1b938adb944b4440
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58648728"
---
# <a name="azure-stack-capacity-planning"></a>Planowanie pojemności platformy Azure w stos
Podczas obliczania rozwiązanie usługi Azure Stack, istnieją opcje konfiguracji sprzętu, które mają bezpośredni wpływ na ogólną pojemność chmury Azure Stack. Są to klasyczne możliwości procesora CPU, pamięci gęstości, konfiguracji magazynu i ogólną skalowania rozwiązania lub liczby serwerów. W przeciwieństwie do rozwiązania tradycyjną wirtualizację proste operacje arytmetyczne na tych składników w celu ustalenia pojemności do wykorzystania nie ma zastosowania. Pierwszym powodem tego jest, że usługi Azure Stack została zaprojektowana do obsługi składników infrastruktury ani zarządzania w obrębie samego rozwiązania. Drugi przyczyna jest część pojemności tego rozwiązania jest zarezerwowana w odniesieniu do odporności; Aktualizowanie oprogramowania rozwiązania w sposób, aby zminimalizować zakłócenia obciążenia dzierżaw.

> [!IMPORTANT]
> Informacje o planowaniu pojemności podana i towarzyszące arkusz kalkulacyjny, są przeznaczone tylko jako wskazówki ułatwiające Planowanie usługi Azure Stack i decyzji związanych z konfiguracją. Nie są przeznaczone do służyć jako substytut dla własnych analizy i analizy. 

## <a name="compute-and-storage-capacity-planning"></a>Moc obliczeniową i planowanie pojemności magazynu
Rozwiązanie Azure Stack jest kompilowany jako klaster hiperzbieżności wystąpień obliczeniowych, sieci i magazynu. To umożliwia efektywne wykorzystanie lub udostępnianie pojemności sprzętowych w klastrze, określane jako jednostki skalowania dla usługi Azure Stack w sposób, który zapewnia dostępność i skalowalność. Całe oprogramowanie infrastruktura znajduje się w zestawie maszyn wirtualnych (VM) i udostępnia te same serwery fizyczne jako maszyny wirtualne dzierżawcy. Wszystkie maszyny wirtualne są następnie zarządzane przez jednostki skalowania technologii klastrowania w systemie Windows Server i pojedynczych wystąpień funkcji Hyper-V. To podejście ułatwia pozyskiwanie i zarządzania rozwiązania usługi Azure Stack i w całej jednostki skalowania, daje w celu przenoszenia i skalowalności, wszystkich usług (dzierżawy i infrastruktury).

Zasób wyłącznie fizyczny, który nie jest nadmiernie aprowizowane w rozwiązaniu usługi Azure Stack to ilość pamięci serwera. Inne zasoby, procesora CPU rdzeni, przepustowości sieci i pojemności magazynu, będzie overprovisioned do najlepszego wykorzystania dostępnych zasobów. Podczas obliczania dostępną pojemność dla rozwiązania, pamięci serwera fizycznego jest głównego współautora. Wykorzystanie innych zasobów jest następnie interpretacji, aż współczynnik celi, który jest możliwe, i jakie będą akceptowane planowane obciążenie.

Około 30 maszyn wirtualnych są używane do obsługi infrastruktury usługi Azure Stack, a także, w sumie używanie około 230 GB pamięci i rdzeni wirtualnych 140. Uzasadnienie dla tej liczby maszyn wirtualnych jest spełniają separacji usługi potrzebne do spełnienia zabezpieczenia, skalowalność, obsługi i stosowanie poprawek wymagania. Ta struktura usługi wewnętrznej umożliwia przyszłego wprowadzenia nowych usług infrastruktury, ponieważ są one tworzone.

Do obsługi automatycznych aktualizacji wszystkich serwerów fizycznych i składniki infrastruktury oprogramowania lub poprawek i aktualizacji, infrastruktury i użytkownika maszyny Wirtualnej angażowania nie zużyje wszystkich zasobów pamięci jednostki skalowania. Kwota całkowita pamięć na wszystkich serwerach jednostki skalowania będzie nieprzydzielone w odniesieniu do wymagań dotyczących odporności tego rozwiązania. Na przykład serwer fizyczny, obraz systemu Windows Server jest aktualizowany, maszyn wirtualnych znajdujących się na serwerze są przenoszone gdzie indziej w jednostce skalowania podczas aktualizacji serwera obrazy systemu Windows Server. Po zakończeniu aktualizacji serwera jest uruchomiona ponownie oraz zostać zwrócone do obsługi obciążeń. Celem poprawek i aktualizacji rozwiązanie usługi Azure Stack jest aby uniknąć konieczności zatrzymania hostowanych maszyn wirtualnych. W fundusze osiągnąć ten cel, bez co najmniej jeden serwer pojemność pamięci jest nieprzydzielonego umożliwiające przeniesienie maszyn wirtualnych w ramach jednostki skalowania. Ta umieszczania i przenoszenie dotyczy zarówno maszynami wirtualnymi infrastruktury, jak i maszyny wirtualne utworzone w imieniu użytkownika lub dzierżawy usługi Azure Stack rozwiązania. Wyniki implementacji końcowe są takie, że ilość pamięci do obsługi wymaganych przenoszenia maszyny Wirtualnej może być znacznie więcej niż jednym serwerze wydajność ze względu na położenie wyzwania gdy maszyny wirtualne mają różne wymagania dotyczące pamięci. Dodatkowe obciążenie w kategorii użycia pamięci jest tego samego wystąpienia systemu Windows Server. Wystąpienia podstawowego systemu operacyjnego dla każdego serwera zużyje pamięci dla systemu operacyjnego i jego tabel strony wirtualnej wraz z pamięci używanej przez funkcję Hyper-V w zarządzaniu wszystkich hostowanych maszyn wirtualnych.

Wydajność maszyny Wirtualnej jest określany przez ilość dostępnej pamięci. Wirtualne rdzeni do współczynniki fizyczna core spróbujemy, jak gęstość maszyn wirtualnych ulegnie zmianie dostępnej pojemności procesora CPU, chyba że rozwiązanie składa się z większej liczby rdzeni fizycznych (wybrano innego Procesora). Dotyczy to pojemność magazynu i pojemności pamięci podręcznej.

Powyższe przykłady gęstość maszyn wirtualnych są wyłącznie w celach wyjaśnienie. Brak dalszych złożoności obliczeń w pomocy technicznej. Kontakt z firmą Microsoft lub partnerem rozwiązań jest wymagana, aby jeszcze lepiej zrozumieć opcje planowania wydajności i pojemności wynikowych, które są dostępne.

W pozostałej części tej sekcji opisano wymagania dotyczące wdrażania usługi Azure Stack zasobów obliczeniowych i magazynu. Te informacje służą do uzyskania podstawowej zrozumienie, jakie składniki są wymagane i ich wartości minimalnej konfiguracji. Do opisania konfiguracji rozwiązania w odniesieniu do dostępnej pojemności i potencjalne limity systemu w odniesieniu do dzierżawy pojemność i wydajność możliwości udostępniane są również dodatkowe informacje.

> [!NOTE]
> Wymagania dotyczące planowania pojemności dla sieci są minimalne, ponieważ można skonfigurować tylko rozmiar publicznych adresów VIP. Aby uzyskać informacje o dodawaniu większej liczby publicznych adresów IP do usługi Azure Stack zobacz [Dodaj publiczne adresy IP](azure-stack-add-ips.md).


## <a name="next-steps"></a>Kolejne kroki
[Obliczenia, planowania pojemności](capacity-planning-compute.md)

---
title: Planowanie wydajności dla ról serwera usługi Azure App Service w stosie Azure | Dokumentacja firmy Microsoft
description: Planowanie wydajności dla ról serwera usługi Azure App Service w stosie Azure
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: brenduns
ms.reviewer: anwestg
ms.openlocfilehash: 8926955d5e0260b5971e07b6988bb21df9980847
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29388587"
---
# <a name="capacity-planning-for-azure-app-service-server-roles-in-azure-stack"></a>Planowanie wydajności dla ról serwera usługi Azure App Service w stosie Azure
*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Aby udostępnić gotowy wdrożenia produkcyjnego usługi Azure App Service na stosie Azure, należy zaplanować pojemność oczekiwać systemu do obsługi.  Poniżej przedstawiono wskazówki dotyczące minimalnej liczby wystąpień i zasobów obliczeniowych jednostki SKU, należy użyć dla każdego wdrożenia produkcyjnego.

Można zaplanować strategii pojemność usługi App Service przy użyciu poniższych wskazówek. Kolejne wersje stosu Azure określi opcje wysokiej dostępności dla aplikacji usługi.

| Rola serwera usługi aplikacji | Minimalna zalecana liczba wystąpień | Zalecane obliczeń jednostki SKU|
| --- | --- | --- |
| Kontroler | 2 | A1 |
| Fronton | 2 | A1 |
| Zarządzanie | 2 | A3 |
| Wydawca | 2 | A1 |
| Pracownicy sieci Web — udostępnionych | 2 | A1 |
| Pracownicy sieci Web — w wersji dedykowanej | 2 na warstwy | A1 |

## <a name="controller-role"></a>Rolę kontrolera

**Minimalna zalecana**: dwa wystąpienia A1 standard

Kontroler usługi aplikacji Azure zazwyczaj napotyka niski zużycie procesora CPU, pamięci i zasobów sieciowych. Jednak wysokiej dostępności, musi mieć dwa kontrolery. Dwa kontrolery są również maksymalną liczbę dozwolonych kontrolerów. Możesz utworzyć drugi kontroler witryn sieci Web bezpośrednio z Instalatora podczas wdrażania.

## <a name="front-end-role"></a>Front End role

**Minimalna zalecana**: dwa wystąpienia A1 standard

Fronton kieruje żądania do pracowników w sieci Web, w zależności od dostępności sieci Web procesu roboczego. Wysoką dostępność powinien mieć więcej niż jeden frontonu i może mieć więcej niż dwa. Dla celów planowania pojemności należy wziąć pod uwagę że każdego rdzenia może obsłużyć około 100 żądań na sekundę.

## <a name="management-role"></a>Rola zarządzania

**Minimalna zalecana**: dwa wystąpienia A3

Rola zarządzania usługi aplikacji Azure jest odpowiedzialny za aplikacji usługi Azure Resource Manager i punkty końcowe interfejsu API, rozszerzenia portalu (administratora, dzierżawcy, funkcje portalu) i usługi danych. Rola serwera zarządzania zwykle wymaga tylko około 4 GB pamięci RAM w środowisku produkcyjnym. Jednak może ulegać wysokiego poziomu Procesora są wykonywane wiele zadań zarządzania (takich jak tworzenie witryny sieci web). Wysoką dostępność powinien mieć więcej niż jeden serwer przypisany do tej roli, i co najmniej dwa rdzenie na serwer.

## <a name="publisher-role"></a>Rola wydawcy

**Minimalna zalecana**: dwa wystąpienia A1

W przypadku wielu użytkowników jednocześnie publikowania, roli wydawcy mogą występować duże użycie procesora CPU. Wysoką dostępność udostępnić więcej niż jednej roli wydawcy.  Wydawcy obsługuje tylko ruch FTP/FTPS.

## <a name="web-worker-role"></a>Rola procesu roboczego sieci Web

**Minimalna zalecana**: dwa wystąpienia A1

Wysoką dostępność powinien mieć co najmniej cztery roli proces roboczy sieci Web, tryb udostępniania witryny sieci web i dla każdej warstwy dedykowanych procesów roboczych planowane oferty. Tryby dedykowanych obliczeń i udostępnione zapewnia różne poziomy usług dzierżawcom. Jeśli masz wielu klientów, może być konieczne więcej pracowników w sieci Web:
 - korzystania z warstw procesu roboczego Tryb dedykowany obliczeń, (które wymagają dużej ilości zasobów)
 - uruchomiona w trybie udostępnionego obliczeń.

Po użytkownika został utworzony Plan usługi App Service dla trybu obliczeń dedykowanej jednostki SKU, liczba pracowników w sieci Web, określona w tym planie usługi aplikacji nie będą już dostępne dla użytkowników.

Aby zapewnić usługi Azure Functions dla użytkowników w modelu planu zużycia, należy wdrożyć udostępnionych pracowników w sieci Web.

Przy podejmowaniu decyzji o liczby ról sieć Web proces roboczy udostępnionego do użycia, należy przejrzeć następujące zagadnienia:

- **Pamięć**: pamięci to najbardziej krytyczna zasobu dla roli sieć Web proces roboczy. Za mało pamięci ma wpływ na wydajność witryny sieci web, gdy pamięć wirtualna jest zamieniane z dysku. Każdy serwer wymaga około 1,2 GB pamięci RAM dla systemu operacyjnego. Pamięć RAM powyżej tego progu może służyć do uruchamiania witryny sieci web.
- **Odsetek aktywnych witryn sieci web**: zazwyczaj około 5 procent aplikacji w usłudze Azure App Service na wdrożenia stosu Azure są aktywne. Jednak procent aplikacje, które są aktywne w danym momencie może być wyższe lub niższe. Z równym 5 procent aktywnej aplikacji maksymalną liczbę aplikacji, które można umieścić w usłudze Azure App Service na wdrożenia stosu Azure powinna być mniejsza niż:
    - 20 razy liczba aktywnych witryn sieci web (5 x 20 = 100).
- **Zużycie pamięci średni**: zużycie pamięci średnia dla aplikacji w środowisku produkcyjnym wynosi około 70 MB. W związku z tym pamięć przydzielona dla wszystkich komputerów roli procesu roboczego sieci Web lub maszyn wirtualnych można obliczyć w następujący sposób:

    *Liczba aplikacji obsługiwane administracyjnie * 70 MB * 5% — (numer sieci Web roli proces roboczy * 1044 MB)*

   Na przykład w przypadku 5000 aplikacji w środowisku, na którym działa 10 role sieć Web proces roboczy każdej roli sieć Web proces roboczy maszyny Wirtualnej powinny mieć 7060 MB pamięci RAM:

   5000 * 70 * 0,05 — (10 * 1044) = 7060 (= około 7 GB)

   Aby uzyskać informacje dotyczące dodawania więcej wystąpień procesów roboczych, zobacz [Dodawanie jedną rolę procesu roboczego](azure-stack-app-service-add-worker-roles.md).

## <a name="file-server-role"></a>Rola serwera plików

Autonomiczny serwer plików dla roli serwera plików można użyć do tworzenia i testowania, na przykład podczas wdrażania usługi Azure App Service w zestawie deweloperskim stosu Azure można użyć tego szablonu - https://aka.ms/appsvconmasdkfstemplate. Do celów produkcyjnych należy użyć wstępnie skonfigurowanego serwera plików systemu Windows lub na serwerze plików z systemem innym niż Windows wstępnie skonfigurowane.

W środowiskach produkcyjnych rolę serwera plików występują we/wy dysku znacznym. Ponieważ przechowuje on wszystkie pliki zawartości i aplikacji dla witryny sieci web użytkownika, należy wstępnie skonfigurować jedną z następujących czynności dla tej roli:
- Serwer plików systemu Windows
- Klaster serwera plików
- na serwerze plików z systemem innym niż Windows
- klaster serwera plików
- Urządzenie NAS (Network Attached Storage), aby uzyskać więcej informacji, zobacz [obsługi administracyjnej serwera plików](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server).

## <a name="next-steps"></a>Kolejne kroki

[Przed rozpoczęciem pracy z usługi aplikacji Azure stosu](azure-stack-app-service-before-you-get-started.md)

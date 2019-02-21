---
title: Planowanie pojemności dla ról serwera usługi Azure App Service w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Planowanie pojemności dla ról serwera usługi Azure App Service w usłudze Azure Stack
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: anwestg
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 20b79b3c2581db94627746f52ed6837aa80b6be5
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447749"
---
# <a name="capacity-planning-for-azure-app-service-server-roles-in-azure-stack"></a>Planowanie pojemności dla ról serwera usługi Azure App Service w usłudze Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Aby skonfigurować wdrożenie produkcyjne gotowości usługi Azure App Service w usłudze Azure Stack, należy zaplanować pojemność oczekujesz, że system do obsługi.  

Ten artykuł zawiera wskazówki dotyczące minimalnej liczby wystąpień obliczeniowych i mocy obliczeniowej jednostki SKU należy używać w przypadku wszystkich wdrożeń produkcyjnych.

Możesz zaplanować swoją strategię pojemność usługi App Service przy użyciu następujących wytycznych.

| Rola serwera usługi App Service | Minimalna zalecana liczba wystąpień | Zalecane obliczeń jednostki SKU|
| --- | --- | --- |
| Kontroler | 2 | A1 |
| Fronton | 2 | A1 |
| Zarządzanie | 2 | A3 |
| Wydawca | 2 | A1 |
| Internetowych procesów roboczych - udostępnione | 2 | A1 |
| Internetowych procesów roboczych — w wersji dedykowanej | 2 na warstwę | A1 |

## <a name="controller-role"></a>Rola kontrolera

**Zalecana minimalna**: Dwa wystąpienia Standard A1

Kontroler usługi Azure App Service jest zazwyczaj środowisk niskie użycie procesora CPU, pamięci i zasobów sieciowych. Jednak wysokiej dostępności, musi mieć dwa kontrolery. Dwa kontrolery są również maksymalną liczbę dozwolonych kontrolerów. Możesz utworzyć drugi kontroler witryn sieci web bezpośredniego z poziomu Instalatora podczas wdrażania.

## <a name="front-end-role"></a>Rola frontonu

**Zalecana minimalna**: Dwa wystąpienia Standard A1

Fronton kieruje żądania do internetowych procesów roboczych w zależności od dostępności procesów roboczych w sieci web. Wysoką dostępność powinny mieć więcej niż jeden serwer sieci Web i może mieć więcej niż dwóch. Dla celów planowania pojemności należy wziąć pod uwagę że każdego rdzenia może obsługiwać około 100 żądań na sekundę.

## <a name="management-role"></a>Rola zarządzania

**Zalecana minimalna**: Dwa wystąpienia Standard A3

Rola zarządzania usługi Azure App Service jest odpowiedzialny za aplikację usługi Azure Resource Manager i punkty końcowe interfejsu API, rozszerzenia portalu (administratora, dzierżawcy, portalu funkcji) i usługi danych. Rola serwera zarządzania, która jest zwykle wymaga tylko o 4 GB pamięci RAM w środowisku produkcyjnym. Jednak gdy wykonywane są wiele zadań zarządzania (takich jak tworzenie witryny sieci web) może ulegać wysoki poziom użycia Procesora. Wysoką dostępność powinny mieć więcej niż jeden serwer przypisany do tej roli, i co najmniej dwa rdzenie na serwer.

## <a name="publisher-role"></a>Rola wydawcy

**Zalecana minimalna**: Dwa wystąpienia Standard A1

Jeśli jednoczesne publikowanie wielu użytkowników w roli wydawcy mogą występować duże użycie procesora CPU. Wysoką dostępność upewnij się, że dostępnych jest więcej niż jednej roli wydawcy. Wydawcy obsługuje tylko ruch FTP/FTPS.

## <a name="web-worker-role"></a>Rola procesu roboczego w sieci Web

**Zalecana minimalna**: Dwa wystąpienia Standard A1

Wysoka dostępność powinny mieć co najmniej cztery ról procesów roboczych w sieci web, tryb udostępnione witryny sieci web i dla każdej warstwy dedykowanych procesów roboczych, które mają do zaoferowania. Udostępnione i dedykowane obliczeniowe tryby zapewnia różne poziomy usług dzierżawcom. Możesz potrzebować więcej pracowników w sieci web, jeśli wielu klientów są:

- Za pomocą obliczeń dedykowane warstwy procesu roboczego trybu, (które są intensywnie korzystających z zasobów).
- Uruchamiany w współdzielony tryb obliczania.

Po użytkownik został utworzony plan usługi App Service dla dedykowany tryb obliczania jednostki SKU, liczba pracowników w sieci web, określone w tym planie usługi App Service nie jest już dostępna dla użytkowników.

Aby zapewnić usłudze Azure Functions dla użytkowników w modelu planu zużycie, należy wdrożyć udostępnionego internetowych procesów roboczych.

Podczas podejmowania decyzji o liczby ról procesów roboczych udostępniony w sieci web do używania, przejrzyj następujące zagadnienia:

- **Pamięć**: Pamięć jest najbardziej krytycznym zasobem dla roli procesu roboczego w sieci web. Za mało pamięci ma wpływ na wydajność witryny sieci web, gdy pamięć wirtualna jest wymieniany z dysku. Każdy serwer wymaga około 1,2 GB pamięci RAM dla systemu operacyjnego. Pamięć RAM powyżej tego progu może służyć do uruchomienia witryny sieci web.
- **Odsetek aktywnych witryn sieci web**: Zazwyczaj o około 5 procent aplikacji w usłudze Azure App Service w usłudze Azure Stack wdrożenia są aktywne. Jednak procent aplikacji, które są aktywne w danym momencie może być wyższe lub niższe. O częstotliwości 5 procent aktywnej aplikacji maksymalną liczbę aplikacji do umieszczenia w usłudze Azure App Service na wdrożenie usługi Azure Stack powinny być mniej niż 20 razy liczba aktywnych witryn sieci web (5 x 20 = 100).
- **Zużycie pamięci średni**: Średnia ilość pamięci zajmowaną dla aplikacji w środowiskach produkcyjnych wynosi około 70 MB. Korzystając z tego miejsca, ilość pamięci przydzielonej dla wszystkich komputerów roli procesu roboczego internetowych lub maszyn wirtualnych można obliczyć w następujący sposób:

   `Number of provisioned applications * 70 MB * 5% - (number of web worker roles * 1044 MB)`

   Na przykład jeśli istnieją 5000 aplikacji w środowisku, w którym działa 10 ról proces roboczy w sieci web, każda rola proces roboczy w sieci web, maszyn wirtualnych powinny mieć 7060 MB pamięci RAM:

   `5,000 * 70 * 0.05 – (10 * 1044) = 7060 (= about 7 GB)`

   Aby uzyskać informacje o dodawaniu większej liczby wystąpień procesu roboczego, zobacz [dodanie większej liczby ról procesów roboczych](azure-stack-app-service-add-worker-roles.md).

## <a name="file-server-role"></a>Rola serwera plików

Autonomiczny serwer plików dla roli serwera plików, można użyć do tworzenia i testowania; na przykład podczas wdrażania usługi Azure App Service w usłudze Azure Stack Development Kit (ASDK) możesz użyć tego szablonu: https://aka.ms/appsvconmasdkfstemplate. Do celów produkcyjnych należy użyć wstępnie skonfigurowanego serwera plików Windows lub serwer wstępnie skonfigurowane plików innych niż Windows.

W środowiskach produkcyjnych rolę serwera plików środowisk dysku intensywnie korzystających z operacji We/Wy. Ponieważ zawiera wszystkie wszystkie pliki zawartości i aplikacji, witryn sieci web użytkownika, należy wstępnie można skonfigurować jedną z następujących zasobów dla tej roli:

- Serwer plików Windows
- Klaster serwera plików Windows
- Plik non-Windows server
- Klaster serwera plików non-Windows
- Urządzenie NAS (magazyn podłączany sieci)

Aby uzyskać więcej informacji, zobacz [Aprowizowanie serwera plików](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server).

## <a name="next-steps"></a>Kolejne kroki

Zobacz następujący artykuł, aby uzyskać więcej informacji:

[Przed rozpoczęciem pracy z usługą App Service w usłudze Azure Stack](azure-stack-app-service-before-you-get-started.md)

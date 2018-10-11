---
title: Planowanie pojemności dla ról serwera usługi Azure App Service w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Planowanie pojemności dla ról serwera usługi Azure App Service w usłudze Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: sethm
ms.reviewer: anwestg
ms.openlocfilehash: 7cdcd8b7e9814c206255077fae0af2029fab6583
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078117"
---
# <a name="capacity-planning-for-azure-app-service-server-roles-in-azure-stack"></a>Planowanie pojemności dla ról serwera usługi Azure App Service w usłudze Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Aby skonfigurować wdrożenie produkcyjne gotowości usługi Azure App Service w usłudze Azure Stack, należy zaplanować pojemność oczekujesz, że system do obsługi.  

Ten artykuł zawiera wskazówki dotyczące minimalnej liczby wystąpień obliczeniowych i mocy obliczeniowej jednostki SKU należy używać w przypadku wszystkich wdrożeń produkcyjnych.

Możesz zaplanować swoją strategię pojemność usługi App Service przy użyciu następujących wytycznych. Przyszłe wersje usługi Azure Stack zapewni opcje wysokiej dostępności dla usługi App Service.

| Rola serwera usługi App Service | Minimalna zalecana liczba wystąpień | Zalecane obliczeń jednostki SKU|
| --- | --- | --- |
| Kontroler | 2 | A1 |
| Fronton | 2 | A1 |
| Zarządzanie | 2 | A3 |
| Wydawca | 2 | A1 |
| Internetowych procesów roboczych - udostępnione | 2 | A1 |
| Internetowych procesów roboczych — w wersji dedykowanej | 2 na warstwę | A1 |

## <a name="controller-role"></a>Rola kontrolera

**Minimalna zalecana**: dwa wystąpienia Standard A1

Azure App Service Controller środowisk zazwyczaj niskie użycie procesora CPU, pamięci i zasobów sieciowych. Jednak wysokiej dostępności, musi mieć dwa kontrolery. Dwa kontrolery są również maksymalną liczbę dozwolonych kontrolerów. Możesz utworzyć drugi kontroler witryn sieci Web bezpośredniego z poziomu Instalatora podczas wdrażania.

## <a name="front-end-role"></a>Front End role

**Minimalna zalecana**: dwa wystąpienia Standard A1

Fronton kieruje żądania do internetowych procesów roboczych, w zależności od dostępności internetowy proces roboczy. Wysoką dostępność powinny mieć więcej niż jeden serwer sieci Web i może mieć więcej niż dwóch. Dla celów planowania pojemności należy wziąć pod uwagę że każdego rdzenia może obsługiwać około 100 żądań na sekundę.

## <a name="management-role"></a>Rola zarządzania

**Minimalna zalecana**: dwa wystąpienia Standard A3

Rola zarządzania usługi aplikacji Azure jest odpowiedzialny za aplikację usługi Azure Resource Manager i punktów końcowych interfejsu API, rozszerzenia portalu (administratora, dzierżawcy, portalu funkcji) i usługi danych. Rola serwera zarządzania, która jest zwykle wymaga tylko o 4 GB pamięci RAM w środowisku produkcyjnym. Jednak gdy wykonywane są wiele zadań zarządzania (takich jak tworzenie witryny sieci web) może ulegać wysoki poziom użycia Procesora. Wysoką dostępność powinny mieć więcej niż jeden serwer przypisany do tej roli, i co najmniej dwa rdzenie na serwer.

## <a name="publisher-role"></a>Rola wydawcy

**Minimalna zalecana**: dwa wystąpienia Standard A1

Jeśli jednoczesne publikowanie wielu użytkowników w roli wydawcy mogą występować duże użycie procesora CPU. Aby uzyskać wysoką dostępność udostępnić więcej niż jednej roli wydawcy.  Wydawcy obsługuje tylko ruch FTP/FTPS.

## <a name="web-worker-role"></a>Rola procesu roboczego w sieci Web

**Minimalna zalecana**: dwa wystąpienia Standard A1

Wysoką dostępność powinien mieć co najmniej cztery ról procesów roboczych w sieci Web, współdzielona tryb witryny sieci web i dla każdej warstwy dedykowanych procesów roboczych możesz planować. Współdzielonych lub tryby dedykowanych obliczeń zapewnia różne poziomy usług dzierżawcom. Możesz potrzebować więcej pracowników w sieci Web, jeśli wielu klientów są:

- Za pomocą obliczeń dedykowane warstwy procesu roboczego trybu, (które są intensywnie korzystających z zasobów).
- Uruchamiany w współdzielony tryb obliczania.

Po użytkownik został utworzony Plan usługi App Service dla dedykowany tryb obliczania jednostki SKU i liczbę procesach roboczych w sieci Web, określone w tym planie usługi App Service nie będzie już dostępna dla użytkowników.

Aby zapewnić usłudze Azure Functions dla użytkowników w modelu planu zużycie, należy wdrożyć udostępnione internetowych procesów roboczych.

Przy podejmowaniu decyzji o liczbę udostępnionych ról internetowych procesów roboczych do użycia, należy przejrzeć następujące zagadnienia:

- **Pamięć**: pamięć jest najbardziej krytycznym zasobem dla roli proces roboczy w sieci Web. Za mało pamięci ma wpływ na wydajność witryny sieci web, gdy pamięć wirtualna jest wymieniany z dysku. Każdy serwer wymaga około 1,2 GB pamięci RAM dla systemu operacyjnego. Pamięć RAM powyżej tego progu może służyć do uruchomienia witryny sieci web.
- **Odsetek aktywnych witryn sieci web**: zazwyczaj o około 5 procent aplikacji w usłudze Azure App Service w usłudze Azure Stack wdrożenia są aktywne. Jednak procent aplikacji, które są aktywne w danym momencie może być wyższe lub niższe. Z równym 5 procent aktywnej aplikacji maksymalną liczbę aplikacji do umieszczenia w usłudze Azure App Service na wdrożenie usługi Azure Stack powinna być mniejsza niż:
  - 20 razy liczba aktywnych witryn sieci web (5 x 20 = 100).
- **Zużycie pamięci średni**: średnia ilość pamięci zajmowaną dla aplikacji w środowiskach produkcyjnych wynosi około 70 MB. Korzystając z tego miejsca, ilość pamięci przydzielonej dla wszystkich komputerów roli internetowych procesów roboczych lub maszyn wirtualnych można obliczyć w następujący sposób:

    *Liczba aplikacji Aprowizowana * 70 MB * 5% — (numer sieci Web ról procesów roboczych * 1044 MB)*

   Na przykład w przypadku 5000 aplikacji w środowisku, w którym działa 10 ról proces roboczy w sieci Web poszczególnych ról internetowego procesu roboczego maszyn wirtualnych powinny mieć 7060 MB pamięci RAM:

   5000 * 70 * 0,05 — (10 * 1044) = 7060 (= około 7 GB)

   Aby uzyskać informacje na temat dodawania większej liczby wystąpień procesu roboczego, zobacz [dodanie większej liczby ról procesów roboczych](azure-stack-app-service-add-worker-roles.md).

## <a name="file-server-role"></a>Rola serwera plików

Autonomiczny serwer plików dla roli serwera plików, można użyć opracowywania i testowania, na przykład podczas wdrażania usługi Azure App Service w usłudze Azure Stack Development Kit możesz użyć tego szablonu — <https://aka.ms/appsvconmasdkfstemplate>. Do celów produkcyjnych należy użyć wstępnie skonfigurowanego serwera plików Windows lub serwer wstępnie skonfigurowane plików innych niż Windows.

W środowiskach produkcyjnych w roli serwera plików środowisk dysku intensywnie korzystających z operacji We/Wy. Ponieważ zawiera wszystkie wszystkie pliki zawartości i aplikacji, witryn sieci web użytkownika, należy wstępnie skonfigurować jedną z następujących dla tej roli:

- Serwer plików Windows
- Klaster serwera plików Windows
- Serwer plików innych niż Windows
- klaster serwera plików innych niż Windows
- urządzenie NAS (magazyn podłączany sieci)

Aby uzyskać więcej informacji, zobacz [aprowizowanie serwera plików](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server).

## <a name="next-steps"></a>Kolejne kroki

[Przed rozpoczęciem pracy z usługą App Service w usłudze Azure Stack](azure-stack-app-service-before-you-get-started.md)

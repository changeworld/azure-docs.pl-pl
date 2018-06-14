---
title: Typowe zadania zarządzania usługi w chmurze | Dokumentacja firmy Microsoft
description: Informacje o sposobie zarządzania usługami w chmurze w portalu Azure. Te przykłady, użyj portalu Azure.
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
ms.assetid: cb218ad9-77d4-4149-83db-71159c00767e
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: adegeo
ms.openlocfilehash: e60bf5c82e68d49abaa44d80ac9fafba2d8265da
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2018
ms.locfileid: "29121199"
---
# <a name="manage-cloud-services-in-the-azure-portal"></a>Zarządzaj usługi w chmurze w portalu Azure
W **usługi w chmurze** obszaru portalu Azure, można wykonywać następujące czynności:

* Aktualizacja roli usługi lub wdrożenia.
* Podwyższ poziom wdrożenia etapowego do środowiska produkcyjnego.
* Połączyć zasoby z usługi w chmurze, tak aby mogli widzieć zależności zasobów i zasoby są skalowane razem.
* Usuwanie usługi w chmurze lub wdrożenia.

Aby uzyskać więcej informacji na temat skalowania usługi w chmurze, zobacz [skonfigurować automatyczne skalowanie dla usługi w chmurze w portalu](cloud-services-how-to-scale-portal.md).

## <a name="update-a-cloud-service-role-or-deployment"></a>Aktualizacja roli usługi w chmurze lub wdrożenia
Jeśli musisz zaktualizować kod aplikacji dla usługi w chmurze, użyj **aktualizacji** w bloku usługi w chmurze. Można aktualizować jedną rolę lub wszystkich ról. Aby zaktualizować, możesz przekazać pakiet usługi lub pliku konfiguracji usługi.

1. W [portalu Azure][Azure portal], wybierz usługę w chmurze do zaktualizowania. Ten krok powoduje otwarcie bloku wystąpienia usługi w chmurze.

2. W bloku, wybierz **aktualizacji**.

    ![Przycisk Aktualizuj](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Aktualizacja wdrożenia przy użyciu nowego pliku pakietu usługi (cspkg) i pliku konfiguracji usługi (cscfg).

    ![UpdateDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. Opcjonalnie zaktualizuj konto magazynu i etykietę wdrożenia.

5. Jeśli żadnych ról z tylko jednego wystąpienia roli, wybierz **Wdróż, nawet jeśli co najmniej jedna rola zawiera pojedyncze wystąpienie** pole wyboru, aby włączyć uaktualnienia kontynuować.

    Azure może zagwarantować tylko 99,95% dostępności usługi podczas aktualizacji usługi chmury, jeśli każda rola ma co najmniej dwa wystąpienia roli (maszyny wirtualne). Dwa wystąpienia roli jednej maszyny wirtualnej przetwarza żądania klientów podczas innych aktualizacji.

6. Wybierz **rozpocząć wdrażanie** pole wyboru, aby zastosować aktualizację, po zakończeniu przekazywania pakietu.

7. Wybierz **OK** zacząć aktualizacji usługi.

## <a name="swap-deployments-to-promote-a-staged-deployment-to-production"></a>Zamień wdrożeń, aby promować przemieszczanego wdrożenia do produkcji
Jeśli zdecydujesz się wdrożyć nową wersję usługi w chmurze, etap i przetestować w środowisku przemieszczania usługi chmury z nową wersją. Użyj **wymiany** przełączyć adresów URL, które są opisane dwa wdrożenia i wspierania nową wersję w środowisku produkcyjnym.

Można wymienić wdrożenia od **usługi w chmurze** strony lub pulpitu nawigacyjnego.

1. W [portalu Azure][Azure portal], wybierz usługę w chmurze do zaktualizowania. Ten krok powoduje otwarcie bloku wystąpienia usługi w chmurze.

2. W bloku, wybierz **wymiany**.

    ![Przycisk wymiany usługi w chmurze](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. Otwiera następujący komunikat potwierdzenia:

    ![Wymiany usługi w chmurze](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Po sprawdzeniu informacji o wdrożeniu, zaznacz **OK** można zamienić wdrożenia.

    Wymiany wdrożenia odbywa się szybko ponieważ jedynym elementem, który zmienia się wirtualne adresy IP (VIP) dla wdrożenia.

    W celu ograniczenia kosztów obliczeń, można usunąć tymczasowej wdrożenia po sprawdzeniu, czy wdrożenia produkcyjnego działa zgodnie z oczekiwaniami.

### <a name="common-questions-about-swapping-deployments"></a>Często zadawane pytania dotyczące wymiany wdrożenia

**Jakie są wymagania wstępne dotyczące wymiany wdrożenia?**

Istnieją dwa kluczowe wymagania wstępne dotyczące wymiany pomyślne wdrożenie:

- Jeśli chcesz użyć statycznego adresu IP dla Twojego miejsca produkcji, należy zarezerwować jeden dla miejsca przemieszczania również. W przeciwnym razie wymiany nie powiedzie się.

- Wszystkie wystąpienia ról musi być uruchomiona, zanim będzie można wykonać wymiany. Można sprawdzić stan swoich wystąpień **omówienie** bloku portalu Azure. Alternatywnie można użyć [Get-AzureRole](/powershell/module/azure/get-azurerole?view=azuresmps-3.7.0) polecenia w programie Windows PowerShell.

Należy pamiętać, że aktualizacje systemu operacyjnego gościa i usługi również naprawianie operacji może spowodować zamiany wdrożenia niepowodzenie. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z wdrażaniem usługi chmury](cloud-services-troubleshoot-deployment-problems.md).

**Zamiana wpływa negatywnie przestojów w przypadku mojej aplikacji? Jak należy ją obsługuje?**

Zgodnie z opisem w poprzedniej sekcji, wymiany wdrożenia jest zazwyczaj szybkie, ponieważ jest tylko zmianę konfiguracji usługi równoważenia obciążenia Azure. W niektórych przypadkach może potrwać 10 lub więcej sekund i wyników w przypadku błędów przejściowych połączenia. Aby ograniczyć wpływ na klientów, rozważ zaimplementowanie [Logika ponawiania klienta](../best-practices-retry-general.md).

## <a name="delete-deployments-and-a-cloud-service"></a>Usuwanie wdrożenia i usługi w chmurze
Aby można było usunąć usługi w chmurze, należy usunąć poszczególnych istniejącego wdrożenia.

W celu ograniczenia kosztów obliczeń, można usunąć tymczasowej wdrożenia po sprawdzeniu, czy wdrożenia produkcyjnego działa zgodnie z oczekiwaniami. Rozliczenie jest kosztów obliczeniowe dla wystąpień roli wdrożone, które zostały zatrzymane.

Użyj poniższej procedury można usunąć wdrożenia lub usługi w chmurze.

1. W [portalu Azure][Azure portal], wybierz usługę w chmurze do usunięcia. Ten krok powoduje otwarcie bloku wystąpienia usługi w chmurze.

2. W bloku, wybierz **usunąć**.

    ![Przycisk Usuń usługi w chmurze](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. Aby usunąć usługi w chmurze całego, wybierz **Cloud service i jej wdrożenia** pole wyboru. Można albo **wdrożenia produkcyjnego** lub **przemieszczania — wdrażanie** pole wyboru.

    ![Usuń usługi w chmurze](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. Wybierz **usunąć** u dołu.

5. Aby usunąć usługi w chmurze, wybierz **usługi w chmurze Delete**. W oknie monitu o potwierdzenie wybierz **tak**.

> [!NOTE]
> Po usunięciu usługi w chmurze i pełne monitorowania jest skonfigurowany, należy usunąć dane ręcznie z konta magazynu. Aby uzyskać informacje o tym, gdzie można znaleźć w tabelach metryki, zobacz [wprowadzenie do monitorowania usługi w chmurze](cloud-services-how-to-monitor.md).


## <a name="find-more-information-about-failed-deployments"></a>Więcej informacji o wdrożeniach nie powiodło się
**Omówienie** bloku ma pasek stanu u góry. Po wybraniu pasku nowy blok otwiera i wyświetla informacje o błędzie. Jeśli wdrożenie nie zawiera żadnych błędów, blok informacji jest pusta.

![Omówienie usług w chmurze](./media/cloud-services-how-to-manage-portal/status-info.png)



[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>Kolejne kroki
* [Konfiguracja ogólna usługi w chmurze](cloud-services-how-to-configure-portal.md).
* Dowiedz się, jak [wdrażania usługi w chmurze](cloud-services-how-to-create-deploy-portal.md).
* Skonfiguruj [niestandardowej nazwy domeny](cloud-services-custom-domain-name-portal.md).
* Skonfiguruj [certyfikaty SSL](cloud-services-configure-ssl-certificate-portal.md).

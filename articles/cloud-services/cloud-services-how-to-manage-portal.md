---
title: Typowe zadania zarządzania usługi w chmurze | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać usługami Cloud Services w witrynie Azure portal. Te przykłady użycia witryny Azure portal.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: cb218ad9-77d4-4149-83db-71159c00767e
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeconnoc
ms.openlocfilehash: d3d1ae759f0f3fa5edd417da61f1fa50b5d9cde7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61433968"
---
# <a name="manage-cloud-services-in-the-azure-portal"></a>Zarządzanie usługami w chmurze w witrynie Azure portal
W **usług w chmurze** obszarze witryny Azure portal, możesz:

* Aktualizacja roli usługi lub wdrożenia.
* Podwyższanie poziomu wdrożenia etapowego do środowiska produkcyjnego.
* Połączyć zasoby usługi w chmurze, aby można zobaczyć zależności zasobu, a jednocześnie skalować zasoby.
* Usuwanie usługi w chmurze lub wdrożenia.

Aby uzyskać więcej informacji o tym, jak skalować usługi w chmurze, zobacz [Konfigurowanie automatycznego skalowania usługi w chmurze w portalu](cloud-services-how-to-scale-portal.md).

## <a name="update-a-cloud-service-role-or-deployment"></a>Aktualizacja roli usługi w chmurze lub wdrożenia
Jeśli musisz zaktualizować kod aplikacji dla usługi w chmurze, użyj **aktualizacji** w bloku usługi w chmurze. Możesz zaktualizować jedną rolę lub wszystkich ról. Aby zaktualizować, możesz przekazać nowego pakietu usługi i pliku konfiguracji usługi.

1. W [witryny Azure portal][Azure portal], wybierz usługę w chmurze do zaktualizowania. Ten krok spowoduje otwarcie bloku wystąpienia usługi w chmurze.

2. W bloku, wybierz **aktualizacji**.

    ![Przycisk Aktualizuj](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Zaktualizuj wdrożenie za pomocą nowego pliku pakietu usługi (cspkg) i pliku konfiguracji usługi (cscfg).

    ![UpdateDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. Opcjonalnie można zaktualizować konta magazynu i etykiety wdrożenia.

5. Jeśli wszystkie role ma tylko jedno wystąpienie roli, wybierz opcję **Wdróż, nawet jeśli co najmniej jedna rola zawiera pojedyncze wystąpienie** pole wyboru, aby umożliwić uaktualnienie kontynuować.

    Azure może zagwarantować tylko dostępności wynoszącej 99,95 procent dostępności usługi podczas aktualizacji usługi w chmurze, jeśli każda rola ma co najmniej dwóch wystąpień roli (maszyny wirtualne). Z dwóch wystąpień roli jedna maszyna wirtualna przetwarza żądania klienta, podczas gdy druga jest aktualizowana.

6. Wybierz **rozpocząć wdrażanie** pole wyboru, aby zastosować aktualizację, po zakończeniu przekazywania pakietu.

7. Wybierz **OK** do rozpoczęcia aktualizowania usługi.

## <a name="swap-deployments-to-promote-a-staged-deployment-to-production"></a>Zamienić miejscami wdrożenia, aby promować wdrożenia etapowego do środowiska produkcyjnego
Jeśli zdecydujesz się wdrażania nowej wersji usługi w chmurze, etapu i testowanie Twojej nowej wersji w środowisku przejściowym usługi chmury. Użyj **wymiany** do przełączania adresów URL, według których dwa wdrożenia zostały rozwiązane i Promuj nową wersję do środowiska produkcyjnego.

Można wymienić wdrożeń z **usług w chmurze** strony lub pulpitu nawigacyjnego.

1. W [witryny Azure portal][Azure portal], wybierz usługę w chmurze do zaktualizowania. Ten krok spowoduje otwarcie bloku wystąpienia usługi w chmurze.

2. W bloku, wybierz **wymiany**.

    ![Przycisk wymiany usługi chmury](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. Zostanie otwarty następujący monit potwierdzenia:

    ![Cloud Services wymiany](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Po sprawdzeniu informacji o wdrożeniu, zaznacz **OK** zamienić miejscami wdrożenia.

    Deployment swap odbywa się szybko, ponieważ jedynym elementem, który zmienia się wirtualne adresy IP (VIP) dla wdrożeń.

    Aby zaoszczędzić koszty operacji obliczeniowych, można usunąć wdrożenia przejściowego, po upewnieniu się, że wdrożenie produkcyjne usługi działają zgodnie z oczekiwaniami.

### <a name="common-questions-about-swapping-deployments"></a>Często zadawane pytania dotyczące zamianę wdrożeń

**Jakie są wymagania wstępne dotyczące wymiany wdrożenia?**

Istnieją dwa kluczowe wymagania wstępne dotyczące wymiany pomyślne wdrożenie:

- Na potrzeby statyczny adres IP z miejscem produkcyjnym, możesz zarezerwować jedną dla miejsca przejściowego także. W przeciwnym razie wymiany kończy się niepowodzeniem.

- Wszystkie wystąpienia elementu role musi być uruchomiona, zanim będzie można wykonywać wymiany. Można sprawdzić stan swoich wystąpień **Przegląd** bloku witryny Azure portal. Alternatywnie, można użyć [Get AzureRole](/powershell/module/servicemanagement/azure/get-azurerole?view=azuresmps-3.7.0) polecenia w programie Windows PowerShell.

Należy pamiętać, że aktualizacje systemu operacyjnego gościa i usługa również korygujący operacji może spowodować zamiany wdrożenie nie powiedzie się. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z wdrażaniem usługi chmury](cloud-services-troubleshoot-deployment-problems.md).

**Wymiana pociągnąć za sobą przestojów dla mojej aplikacji? Jak należy go obsłużyć?**

Zgodnie z opisem w poprzedniej sekcji, wymiana wdrożenia jest zazwyczaj szybkie, ponieważ jest on tylko zmian w konfiguracji w module równoważenia obciążenia platformy Azure. W niektórych przypadkach może potrwać 10 lub więcej sekund i wynik połączenia przejściowe błędy. Aby ograniczyć wpływ na klientów, rozważ zaimplementowanie [logikę ponawiania próby klienta](../best-practices-retry-general.md).

## <a name="delete-deployments-and-a-cloud-service"></a>Usuwanie wdrożenia i usługi w chmurze
Aby można było usunąć usługi w chmurze, należy usunąć dla każdego istniejącego wdrożenia.

Aby zaoszczędzić koszty operacji obliczeniowych, można usunąć wdrożenia przejściowego, po upewnieniu się, że wdrożenie produkcyjne usługi działają zgodnie z oczekiwaniami. Opłaty są naliczane za koszty operacji obliczeniowych dla wystąpień ról wdrożonych, które zostały zatrzymane.

Użyj poniższej procedury można usunąć wdrożenia lub usługi w chmurze.

1. W [witryny Azure portal][Azure portal], wybierz usługę w chmurze do usunięcia. Ten krok spowoduje otwarcie bloku wystąpienia usługi w chmurze.

2. W bloku, wybierz **Usuń**.

    ![Przycisk Usuń usług w chmurze](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. Aby usunąć usługę w chmurze całego, wybierz **Cloud service i jej wdrożenia** pole wyboru. Lub możesz wybrać dowolną **wdrożenia produkcyjnego** lub **wdrożenie przejściowe** pole wyboru.

    ![Usuń usług w chmurze](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. Wybierz **Usuń** u dołu.

5. Aby usunąć usługę w chmurze, wybierz **usuwania usługi w chmurze**. W wierszu potwierdzenia wybierz **tak**.

> [!NOTE]
> Gdy usługa w chmurze zostanie usunięty i pełne monitorowanie jest skonfigurowany, dane trzeba usunąć ręcznie z poziomu konta magazynu. Aby uzyskać informacje o tym, gdzie można znaleźć w tabelach metryk, zobacz [wprowadzenie do monitorowania usługi w chmurze](cloud-services-how-to-monitor.md).


## <a name="find-more-information-about-failed-deployments"></a>Więcej informacji o wdrożeniach zakończonych niepowodzeniem
**Przegląd** blok zawiera pasek stanu u góry. Po zaznaczeniu pasku nowy blok otwiera i wyświetla informacje o błędzie. Jeśli wdrożenie nie zawiera żadnych błędów, blok informacji jest pusta.

![Omówienie usług w chmurze](./media/cloud-services-how-to-manage-portal/status-info.png)



[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>Kolejne kroki
* [Konfiguracja ogólna usługi w chmurze](cloud-services-how-to-configure-portal.md).
* Dowiedz się, jak [wdrożyć usługę w chmurze](cloud-services-how-to-create-deploy-portal.md).
* Konfigurowanie [niestandardowej nazwy domeny](cloud-services-custom-domain-name-portal.md).
* Konfigurowanie [certyfikaty SSL](cloud-services-configure-ssl-certificate-portal.md).

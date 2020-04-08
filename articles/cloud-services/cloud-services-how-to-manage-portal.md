---
title: Wspólne zadania związane z zarządzaniem usługami w chmurze | Dokumenty firmy Microsoft
description: Dowiedz się, jak zarządzać usługami w chmurze w witrynie Azure portal. W tych przykładach użyto witryny Azure portal.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: tagore
ms.openlocfilehash: 80481bc11933b0404079221f23b5054024f00acb
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811354"
---
# <a name="manage-cloud-services-in-the-azure-portal"></a>Zarządzanie usługami w chmurze w portalu azure
W obszarze Usługi w **chmurze** w witrynie Azure portal można:

* Aktualizowanie roli usługi lub wdrożenia.
* Promuj wdrożenie etapowe do produkcji.
* Połącz zasoby z usługą w chmurze, aby zobaczyć zależności zasobów i skalować zasoby razem.
* Usuń usługę w chmurze lub wdrożenie.

Aby uzyskać więcej informacji na temat skalowania usługi w chmurze, zobacz [Konfigurowanie automatycznego skalowania dla usługi w chmurze w portalu](cloud-services-how-to-scale-portal.md).

## <a name="update-a-cloud-service-role-or-deployment"></a>Aktualizowanie roli lub wdrożenia usługi w chmurze
Jeśli chcesz zaktualizować kod aplikacji dla usługi w chmurze, użyj **update** na bloku usługi w chmurze. Można zaktualizować jedną rolę lub wszystkie role. Aby zaktualizować, można przekazać nowy pakiet usług lub plik konfiguracji usługi.

1. W [witrynie Azure portal][Azure portal]wybierz usługę w chmurze, którą chcesz zaktualizować. Ten krok otwiera blok wystąpienia usługi w chmurze.

2. Na bloku wybierz pozycję **Aktualizuj**.

    ![Przycisk Aktualizuj](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Zaktualizuj wdrożenie za pomocą nowego pliku pakietu usługi (cspkg) i pliku konfiguracji usługi (cscfg).

    ![AktualizacjaDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. Opcjonalnie zaktualizuj konto magazynu i etykietę wdrożenia.

5. Jeśli wszystkie role mają tylko jedno wystąpienie roli, zaznacz **pole wyboru Wdrażanie, nawet jeśli jedna lub więcej ról zawiera jedno wystąpienie,** aby umożliwić kontynuowanie uaktualnienia.

    Platforma Azure może zagwarantować tylko 99,95 procent dostępności usług podczas aktualizacji usługi w chmurze, jeśli każda rola ma co najmniej dwa wystąpienia roli (maszyny wirtualne). Z dwóch wystąpień roli, jedna maszyna wirtualna przetwarza żądania klienta, podczas gdy drugi jest aktualizowany.

6. Zaznacz pole wyboru **Rozpocznij wdrażanie,** aby zastosować aktualizację po zakończeniu przekazywania pakietu.

7. Wybierz **przycisk OK,** aby rozpocząć aktualizowanie usługi.

## <a name="swap-deployments-to-promote-a-staged-deployment-to-production"></a>Zamiana wdrożeń w celu promowania wdrożenia etapowego w produkcji
Gdy zdecydujesz się wdrożyć nową wersję usługi w chmurze, etap i przetestować nową wersję w środowisku przejściowym usługi w chmurze. Użyj **swap,** aby przełączyć adresy URL, za pomocą których dwa wdrożenia są adresowane i promować nową wersję do produkcji.

Wdrożenia można zamienić ze strony **Usługi w chmurze** lub pulpitu nawigacyjnego.

1. W [witrynie Azure portal][Azure portal]wybierz usługę w chmurze, którą chcesz zaktualizować. Ten krok otwiera blok wystąpienia usługi w chmurze.

2. Na ostrzu wybierz opcję **Zamień**.

    ![Przycisk Zamiana usług w chmurze](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. Zostanie wyświetlony następujący monit o potwierdzenie:

    ![Wymiana usług w chmurze](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Po zweryfikowaniu informacji o wdrożeniu wybierz przycisk **OK,** aby zamienić wdrożenia.

    Zamiana wdrożenia odbywa się szybko, ponieważ jedyną rzeczą, która się zmienia, są wirtualne adresy IP (VIP) dla wdrożeń.

    Aby zaoszczędzić koszty obliczeń, można usunąć wdrożenie przejściowe po sprawdzeniu, czy wdrożenie produkcyjne działa zgodnie z oczekiwaniami.

### <a name="common-questions-about-swapping-deployments"></a>Typowe pytania dotyczące wymiany wdrożeń

**Jakie są wymagania wstępne dotyczące zamiany wdrożeń?**

Istnieją dwa kluczowe wymagania wstępne dotyczące pomyślnej wymiany wdrożenia:

- Jeśli chcesz użyć statycznego adresu IP dla gniazda produkcyjnego, musisz zarezerwować jeden dla miejsca przejściowego. W przeciwnym razie zamiana kończy się niepowodzeniem.

- Wszystkie wystąpienia ról muszą być uruchomione, zanim będzie można wykonać wymiany. Możesz sprawdzić stan wystąpień w **bloku Przegląd** witryny Azure Portal. Alternatywnie można użyć polecenia [Get-AzureRole](/powershell/module/servicemanagement/azure/get-azurerole?view=azuresmps-3.7.0) w programie Windows PowerShell.

Należy zauważyć, że aktualizacje systemu operacyjnego gościa i operacje leczenia usług również może spowodować niepowodzenie wymiany wdrożenia. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z wdrażaniem usług w chmurze](cloud-services-troubleshoot-deployment-problems.md).

**Czy swap wiąże się z przestojami dla mojej aplikacji? Jak sobie z tym poradzić?**

Jak opisano w poprzedniej sekcji, zamiana wdrożenia jest zazwyczaj szybki, ponieważ jest to tylko zmiana konfiguracji w modułu równoważenia obciążenia platformy Azure. W niektórych przypadkach może upłynąć 10 lub więcej sekund i spowodować przejściowe błędy połączenia. Aby ograniczyć wpływ na klientów, należy rozważyć wdrożenie [logiki ponawiania próby klienta](../best-practices-retry-general.md).

## <a name="delete-deployments-and-a-cloud-service"></a>Usuwanie wdrożeń i usługi w chmurze
Przed usunięciem usługi w chmurze należy usunąć każde istniejące wdrożenie.

Aby zaoszczędzić koszty obliczeń, można usunąć wdrożenie przejściowe po sprawdzeniu, czy wdrożenie produkcyjne działa zgodnie z oczekiwaniami. Opłaty są naliczane za koszty obliczeniowe dla wdrożonych wystąpień ról, które są zatrzymane.

Poniższa procedura służy do usuwania wdrożenia lub usługi w chmurze.

1. W [witrynie Azure portal][Azure portal]wybierz usługę w chmurze, którą chcesz usunąć. Ten krok otwiera blok wystąpienia usługi w chmurze.

2. Na bloku wybierz pozycję **Usuń**.

    ![Przycisk Usuń usługi w chmurze](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. Aby usunąć całą usługę w chmurze, zaznacz pole wyboru **Usługa w chmurze i jej wdrożenia.** Można też wybrać pole wyboru **Wdrożenie produkcyjne** lub **Wdrożenie przejściowe.**

    ![Usuwanie usług w chmurze](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. Wybierz **pozycję Usuń** u dołu.

5. Aby usunąć usługę w chmurze, wybierz pozycję **Usuń usługę w chmurze**. Następnie w wierszu potwierdzenia wybierz pozycję **Tak**.

> [!NOTE]
> Po usunięciu usługi w chmurze i skonfigurowaniu szczegółowego monitorowania należy ręcznie usunąć dane z konta magazynu. Aby uzyskać informacje o tym, gdzie można znaleźć tabele metryk, zobacz [Wprowadzenie do monitorowania usługi w chmurze](cloud-services-how-to-monitor.md).


## <a name="find-more-information-about-failed-deployments"></a>Znajdź więcej informacji o nieudanych wdrożeniach
**W górnej** części bloku Przegląd znajduje się pasek stanu. Po wybraniu paska otwiera się nowy blok i wyświetla wszelkie informacje o błędzie. Jeśli wdrożenie nie zawiera żadnych błędów, blok informacji jest pusty.

![Omówienie usług w chmurze](./media/cloud-services-how-to-manage-portal/status-info.png)



[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>Następne kroki
* [Ogólna konfiguracja usługi w chmurze](cloud-services-how-to-configure-portal.md).
* Dowiedz się, jak [wdrożyć usługę w chmurze](cloud-services-how-to-create-deploy-portal.md).
* Konfigurowanie [niestandardowej nazwy domeny](cloud-services-custom-domain-name-portal.md).
* Konfigurowanie [certyfikatów TLS/SSL](cloud-services-configure-ssl-certificate-portal.md).




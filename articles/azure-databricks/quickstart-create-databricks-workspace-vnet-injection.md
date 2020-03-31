---
title: Tworzenie obszaru roboczego usługi Azure Databricks we własnym przewodniku Szybki start sieci wirtualnej
description: W tym artykule opisano sposób wdrażania usługi Azure Databricks w sieci wirtualnej.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 63dd1b4d9396d340dd17a7afb92ff9c38a2b38b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132679"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-your-own-virtual-network"></a>Szybki start: tworzenie obszaru roboczego usługi Azure Databricks we własnej sieci wirtualnej

Domyślne wdrożenie usługi Azure Databricks tworzy nową sieć wirtualną, która jest zarządzana przez Databricks. Ten przewodnik Szybki start pokazuje, jak utworzyć obszar roboczy usługi Azure Databricks we własnej sieci wirtualnej. W tym obszarze roboczym utworzysz również klaster Platformy Spark Apache. 

Aby uzyskać więcej informacji na temat powodów, dla których można utworzyć obszar roboczy usługi Azure Databricks we własnej sieci wirtualnej, zobacz [Wdrażanie usługi Azure Databricks w sieci wirtualnej platformy Azure (iniekcja sieci wirtualnej).](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/databricks/).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [Portalu Azure](https://portal.azure.com/).

> [!Note]
> Tego samouczka nie można przeprowadzić przy użyciu **bezpłatnej subskrypcji próbnej platformy Azure.**
> Jeśli masz darmowe konto, przejdź do swojego profilu i zmień subskrypcję na **płatność zgodnie z rzeczywistymami.** Aby uzyskać więcej informacji, zobacz [Bezpłatne konto platformy Azure](https://azure.microsoft.com/free/). Następnie [usuń limit wydatków](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)i [poproś o zwiększenie przydziału](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) dla procesorów wirtualnych w twoim regionie. Podczas tworzenia obszaru roboczego usługi Azure Databricks, można wybrać **warstwę cenową trial (Premium — 14-dniowe bezpłatne dbu),** aby dać obszarowi roboczemu dostęp do bezpłatnych witryn dbu usługi Premium Azure Databricks przez 14 dni.

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

1. Z menu Portalu platformy Azure wybierz polecenie **Utwórz zasób**. Następnie wybierz **opcję Sieć > sieć wirtualna**.

    ![Tworzenie sieci wirtualnej w witrynie Azure portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network-portal.png)

2. W obszarze **Tworzenie sieci wirtualnej**zastosuj następujące ustawienia: 

    |Ustawienie|Sugerowana wartość|Opis|
    |-------|---------------|-----------|
    |Subskrypcja|\<Twoja subskrypcja\>|Wybierz subskrypcję platformy Azure, której chcesz użyć.|
    |Grupa zasobów|databricks-szybki start|Wybierz **pozycję Utwórz nowy** i wprowadź nową nazwę grupy zasobów dla swojego konta.|
    |Nazwa|databricks-szybki start|Wybierz nazwę sieci wirtualnej.|
    |Region|\<Wybierz region, który jest najbliżej Twoich użytkowników\>|Wybierz lokalizację geograficzną, w której można hostować sieć wirtualną. Użyj lokalizacji znajdującej się najbliżej użytkowników.|

    ![Podstawy sieci wirtualnej w witrynie Azure portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. Wybierz **dalej: Adresy IP >** i zastosuj następujące ustawienia. Następnie wybierz **pozycję Recenzja + utwórz**.
    
    |Ustawienie|Sugerowana wartość|Opis|
    |-------|---------------|-----------|
    |Przestrzeń adresowa IPv4|10.2.0.0/16|Zakres adresów sieci wirtualnej w notacji CIDR. Zakres CIDR musi naić od /16 do /24|
    |Nazwa podsieci|default|Wybierz nazwę domyślnej podsieci w sieci wirtualnej.|
    |Zakres adresów podsieci|10.2.0.0/24|Zakres adresów podsieci w notacji CIDR. Musi być zawarty przez przestrzeń adresową sieci wirtualnej. Nie można edytować używanego zakresu adresów podsieci.|

    ![Ustawianie konfiguracji adresów IP dla sieci wirtualnej w witrynie Azure portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network-ip-config.png)

4. Na karcie **Recenzja + tworzenie** wybierz pozycję **Utwórz,** aby wdrożyć sieć wirtualną. Po zakończeniu wdrażania przejdź do sieci wirtualnej i wybierz pozycję **Przestrzeń adresowa** w obszarze **Ustawienia**. W polu z *napisem Dodaj dodatkowy zakres adresów*, wstaw `10.179.0.0/16` i wybierz pozycję **Zapisz**.

    ![Przestrzeń adresowa sieci wirtualnej platformy Azure](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>Tworzenie obszaru roboczego usługi Azure Databricks

1. Z menu Portalu platformy Azure wybierz polecenie **Utwórz zasób**. Następnie wybierz **opcję Analytics > Databricks**.

    ![Tworzenie obszaru roboczego usługi Azure Databricks w witrynie Azure portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace-portal.png)

2. W obszarze **Usługa Azure Databricks**należy zastosować następujące ustawienia:

    |Ustawienie|Sugerowana wartość|Opis|
    |-------|---------------|-----------|
    |Nazwa obszaru roboczego|databricks-szybki start|Wybierz nazwę obszaru roboczego usługi Azure Databricks.|
    |Subskrypcja|\<Twoja subskrypcja\>|Wybierz subskrypcję platformy Azure, której chcesz użyć.|
    |Grupa zasobów|databricks-szybki start|Wybierz tę samą grupę zasobów, która została użyta w sieci wirtualnej.|
    |Lokalizacja|\<Wybierz region, który jest najbliżej Twoich użytkowników\>|Wybierz tę samą lokalizację co sieć wirtualna.|
    |Warstwa cenowa|Wybierz warstwę Standardowa lub Premium.|Aby uzyskać więcej informacji na temat warstw cenowych, zobacz [stronę cennika Databricks](https://azure.microsoft.com/pricing/details/databricks/).|

    ![Tworzenie podstaw obszaru roboczego usługi Azure Databricks](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. Po zakończeniu wprowadzania ustawień na stronie **Podstawy** wybierz pozycję **Dalej: >sieci** i zastosuj następujące ustawienia:

    |Ustawienie|Sugerowana wartość|Opis|
    |-------|---------------|-----------|
    |Wdrażanie obszaru roboczego usługi Azure Databricks w sieci wirtualnej (sieci wirtualnej)|Tak|To ustawienie umożliwia wdrożenie obszaru roboczego usługi Azure Databricks w sieci wirtualnej.|
    |Virtual Network|databricks-szybki start|Wybierz sieć wirtualną utworzoną w poprzedniej sekcji.|
    |Nazwa podsieci publicznej|podsieci publicznej|Użyj domyślnej nazwy podsieci publicznej.|
    |Zakres CIDR podsieci publicznej|10.179.64.0/18|Użyj zakresu CIDR do /26 włącznie.|
    |Nazwa podsieci prywatnej|podsieci prywatnej|Użyj domyślnej nazwy podsieci prywatnej.|
    |Zakres CIDR podsieci prywatnej|10.179.0.0/18|Użyj zakresu CIDR do /26 włącznie.|

    ![Dodawanie informacji o sieci wirtualnej do obszaru roboczego usługi Azure Databricks w witrynie Azure portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace-vnet-config.png)

3. Po zakończeniu wdrażania przejdź do zasobu usługi Azure Databricks. Należy zauważyć, że komunikacja równorzędna sieci wirtualnej jest wyłączona. Zwróć również uwagę na grupę zasobów i zarządzana grupa zasobów na stronie przeglądu. 

    ![Omówienie usługi Azure Databricks w witrynie Azure portal](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-overview-portal.png)

    Zarządzana grupa zasobów nie jest modyfikowalna i nie jest używana do tworzenia maszyn wirtualnych. Maszyn wirtualnych można tworzyć tylko w grupie zasobów, którą zarządzasz.

    ![Grupa zasobów zarządzanych przez usługi Azure Databricks](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group.png)

    Gdy wdrożenie obszaru roboczego nie powiedzie się, obszar roboczy jest nadal tworzony w stanie awarii. Usuń nieudany obszar roboczy i utwórz nowy obszar roboczy, który rozwiązuje błędy wdrażania. Po usunięciu nieudanego obszaru roboczego grupa zarządzanych zasobów i wszystkie pomyślnie wdrożone zasoby są również usuwane.

## <a name="create-a-cluster"></a>Tworzenie klastra

> [!NOTE]
> Aby użyć bezpłatnego konta do utworzenia klastra usługi Azure Databricks, przed utworzeniem klastra przejdź do swojego profilu i zmień swoją subskrypcję na **płatność zgodnie z rzeczywistym użyciem**. Aby uzyskać więcej informacji, zobacz [Bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

1. Wróć do usługi Azure Databricks i wybierz **uruchom obszar roboczy** na stronie **Przegląd.**

2. Wybierz **klastry** > **+ Utwórz klaster**. Następnie utwórz nazwę klastra, taką jak *databricks-quickstart-cluster,* i zaakceptuj pozostałe ustawienia domyślne. Wybierz **pozycję Utwórz klaster**.

    ![Tworzenie klastra usługi Azure Databricks](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. Po uruchomieniu klastra wróć do zarządzanej grupy zasobów w witrynie Azure portal. Zwróć uwagę na nowe maszyny wirtualne, dyski, adres IP i interfejsy sieciowe. Interfejs sieciowy jest tworzony w każdej z podsieci publicznych i prywatnych z adresami IP.  

    ![Grupa zasobów zarządzanych przez usługi Azure Databricks po utworzeniu klastra](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. Wróć do obszaru roboczego usługi Azure Databricks i wybierz utworzony klaster. Następnie przejdź do karty **Executors** na stronie **Interfejsu użytkownika platformy Spark.** Należy zauważyć, że adresy sterownika i executors znajdują się w zakresie podsieci prywatnej. W tym przykładzie sterownik jest 10.179.0.6 i wykonawców są 10.179.0.4 i 10.179.0.5. Twoje adresy IP mogą być różne.

    ![Executory interfejsu użytkownika platformy Azure Databricks Spark](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy skończysz korzystać z tego artykułu, możesz zakończyć działanie klastra. Aby to zrobić, w obszarze roboczym usługi Azure Databricks wybierz pozycję **Klastry** w lewym okienku. W obszarze klastra, którego działanie chcesz zakończyć, przesuń kursor na wielokropek w kolumnie **Akcje**, a następnie wybierz ikonę **Zakończ**. Spowoduje to zatrzymanie klastra.

Jeśli klaster nie zostanie ręcznie zakończony, zostanie on automatycznie wstrzymany, pod warunkiem, że podczas tworzenia klastra zostanie zaznaczone pole wyboru **Zakończ po \_ \_ minutach braku aktywności.** W takim przypadku nieaktywny klaster automatycznie zatrzymuje się po określonym czasie.

Jeśli nie chcesz ponownie używać klastra, możesz usunąć grupę zasobów utworzoną w witrynie Azure portal.

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzono klaster platformy Spark w usłudze Azure Databricks wdrożony w sieci wirtualnej. Przejdź do następnego artykułu, aby dowiedzieć się, jak zbadać kontener platformy Docker programu SQL Server Linux w sieci wirtualnej przy użyciu JDBC z notesu usługi Azure Databricks.  

> [!div class="nextstepaction"]
>[Wysyłanie zapytań do kontenera platformy Docker w systemie Linux z programem SQL Server w sieci wirtualnej z notesu usługi Azure Databricks](vnet-injection-sql-server.md)

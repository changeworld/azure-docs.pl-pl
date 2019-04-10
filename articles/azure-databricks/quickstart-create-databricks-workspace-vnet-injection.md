---
title: Tworzenie obszaru roboczego usługi Azure Databricks w sieci wirtualnej
description: W tym artykule opisano sposób wdrażania usługi Azure Databricks z siecią wirtualną.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 295b64b10f9f78ca6224d60fb84c6d1310aaa42e
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288204"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-a-virtual-network"></a>Szybki start: Tworzenie obszaru roboczego usługi Azure Databricks w sieci wirtualnej

Ten przewodnik Szybki Start pokazano, jak utworzyć obszar roboczy usługi Azure Databricks w sieci wirtualnej. Zostanie również utworzyć klaster Apache Spark w tym obszarze roboczym.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

1. W witrynie Azure portal wybierz **Utwórz zasób** > **sieć** > **sieć wirtualna**.

2. W obszarze **Utwórz sieć wirtualną**, zastosuj następujące ustawienia: 

    |Ustawienie|Sugerowana wartość|Opis|
    |-------|---------------|-----------|
    |Name (Nazwa)|databricks-quickstart|Wybierz nazwę sieci wirtualnej.|
    |Przestrzeń adresowa|10.1.0.0/16|Zakres adresów sieci wirtualnej w notacji CIDR.|
    |Subskrypcja|\<Twoja subskrypcja\>|Wybierz subskrypcję platformy Azure, której chcesz użyć.|
    |Grupa zasobów|databricks-quickstart|Wybierz **Utwórz nowy** i wprowadź nazwę nowej grupy zasobów dla swojego konta.|
    |Lokalizacja|\<Wybierz region, który jest najbliżej Twoich użytkowników\>|Wybierz lokalizację geograficzną, w której możesz hostować sieci wirtualnej. Użyj lokalizacji znajdującej się najbliżej użytkowników.|
    |Nazwa podsieci|default|Wybierz nazwę podsieci domyślne w Twojej sieci wirtualnej.|
    |Zakres adresów podsieci|10.1.0.0/24|Zakres adresów podsieci w notacji CIDR. Muszą być zawarte w przestrzeni adresowej sieci wirtualnej. Nie można edytować zakresu adresów podsieci, która jest używana.|

    ![Tworzenie sieci wirtualnej w witrynie Azure portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. Po zakończeniu wdrożenia przejdź do sieci wirtualnej i wybierz **przestrzeń adresowa** w obszarze **ustawienia**. W polu informującym *Dodaj dodatkowy zakres adresów*, Wstaw `10.179.0.0/16` i wybierz **Zapisz**.

    ![Przestrzeń adresowa sieci wirtualnej platformy Azure](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>Tworzenie obszaru roboczego usługi Azure Databricks

1. W witrynie Azure portal wybierz **Utwórz zasób** > **Analytics** > **Databricks**.

2. W obszarze **usługa Azure Databricks**, zastosuj następujące ustawienia:

    |Ustawienie|Sugerowana wartość|Opis|
    |-------|---------------|-----------|
    |Nazwa obszaru roboczego|databricks-quickstart|Wybierz nazwę obszaru roboczego usługi Azure Databricks.|
    |Subskrypcja|\<Twoja subskrypcja\>|Wybierz subskrypcję platformy Azure, której chcesz użyć.|
    |Grupa zasobów|databricks-quickstart|Wybierz grupę zasobów, używane dla sieci wirtualnej.|
    |Lokalizacja|\<Wybierz region, który jest najbliżej Twoich użytkowników\>|Wybierz tę samą lokalizację sieci wirtualnej.|
    |Warstwa cenowa|Wybierz między Standard lub Premium.|Aby uzyskać więcej informacji na temat warstw cenowych, zobacz [stronę usługi Databricks](https://azure.microsoft.com/pricing/details/databricks/).|
    |Wdrażanie obszaru roboczego usługi Azure Databricks w usłudze Virtual Network|Yes|To ustawienie umożliwia wdrażanie obszaru roboczego usługi Azure Databricks w usłudze virtual network.|
    |Virtual Network|databricks-quickstart|Wybierz sieć wirtualną, który został utworzony w poprzedniej sekcji.|
    |Nazwa podsieci publicznej|public-subnet|Użyj domyślnej nazwy podsieci publicznej.|
    |Zakres CIDR podsieci publicznej|10.179.64.0/18|Zakres CIDR dla tej podsieci powinien należeć do zakresu od /18 i /26.|
    |Nazwa podsieci prywatnej|private-subnet|Użyj domyślnej nazwy podsieci prywatnej.|
    |Zakres CIDR podsieci prywatnej|10.179.0.0/18|Zakres CIDR dla tej podsieci powinien należeć do zakresu od /18 i /26.|

    ![Tworzenie obszaru roboczego usługi Azure Databricks w witrynie Azure portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. Po zakończeniu wdrożenia przejdź do zasobu usługi Azure Databricks. Należy zauważyć, że komunikacja równorzędna sieci wirtualnych jest wyłączona. Ponadto grupy zasobów i zarządzanej grupy zasobów na stronie Przegląd. 

    ![Omówienie usługi Azure Databricks w witrynie Azure portal](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-overview-portal.png)

    Zarządzana grupa zasobów zawiera fizycznej lokalizacji konta magazynu (DBFS) procesu roboczego grupy zabezpieczeń (sieciowej grupy zabezpieczeń), pracowników sieci wirtualnej (sieci wirtualnej). Jest również lokalizację, w której zostanie utworzony maszyn wirtualnych, dysków, adres IP i interfejsu sieciowego. Ta grupa zasobów jest zablokowane domyślnie; Jednak po uruchomieniu klastra w sieci wirtualnej między pracowników między sieciami wirtualnymi w zarządzanej grupie zasobów i siecią wirtualną "Centrum" zostanie utworzony interfejs sieciowy.

    ![Usługa Azure Databricks zarządzanej grupy zasobów](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group.png)

## <a name="create-a-cluster"></a>Tworzenie klastra

> [!NOTE]
> Aby użyć bezpłatnego konta do utworzenia klastra usługi Azure Databricks, przed utworzeniem klastra przejdź do swojego profilu i zmień swoją subskrypcję na **płatność zgodnie z rzeczywistym użyciem**. Aby uzyskać więcej informacji, zobacz [Bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

1. Wróć do usługi Azure Databricks i wybierz pozycję **Uruchom obszar roboczy** na **Przegląd** strony.

2. Wybierz **klastrów** > **+ Utwórz klaster**. Następnie utwórz nazwę klastra, takie jak *klastra usługi databricks — Szybki Start*i zaakceptuj pozostałe domyślne ustawienia. Wybierz pozycję **Utwórz klaster**.

    ![Tworzenie klastra usługi Azure Databricks](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. Gdy klaster działa, wróć do zarządzanej grupy zasobów w witrynie Azure portal. Zwróć uwagę, nowych maszyn wirtualnych, dysków, adres IP i interfejsów sieciowych. Interfejs sieciowy jest tworzony w każdej podsieci publiczne i prywatne adresy IP.  

    ![Usługa Azure Databricks zarządzanej grupy zasobów po utworzeniu klastra](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. Wróć do obszaru roboczego usługi Azure Databricks i wybierz utworzony klaster. Następnie przejdź do **Executors** karcie **interfejsu użytkownika platformy Spark** strony. Należy zauważyć, że adresy dla sterownika i executors należą do zakresu podsieci prywatnej. W tym przykładzie sterownik jest 10.179.0.6 i funkcje wykonawcze to 10.179.0.4 i 10.179.0.5. Adresy IP mogą być inne.

    ![Usługa Azure executors interfejsu użytkownika platformy Spark usługi Databricks](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy skończysz korzystać z tego artykułu, możesz zakończyć działanie klastra. Aby to zrobić, w obszarze roboczym usługi Azure Databricks wybierz pozycję **Klastry** w lewym okienku. W obszarze klastra, którego działanie chcesz zakończyć, przesuń kursor na wielokropek w kolumnie **Akcje**, a następnie wybierz ikonę **Zakończ**. Spowoduje to zatrzymanie klastra.

Jeśli nie zakończysz działania klastra ręcznie, zostanie on automatycznie zatrzymany, o ile podczas tworzenia klastra zaznaczono pole wyboru **Zakończ po \_\_ min nieaktywności**. W takim przypadku nieaktywny klaster automatycznie zatrzymuje się po określonym czasie.

Jeśli nie chcesz ponownie użyć klastra, możesz usunąć grupę zasobów, utworzonej w witrynie Azure portal.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule utworzono klaster Spark w usłudze Azure Databricks, która została wdrożona w sieci wirtualnej. Przejdź do następnego artykułu, aby dowiedzieć się, jak wykonywać zapytania dla programu SQL Server Linux narzędziem w sieci wirtualnej przy użyciu sterownika JDBC z notesu usługi Azure Databricks.  

> [!div class="nextstepaction"]
>[Zapytanie SQL Server Linux narzędziem w sieci wirtualnej z notesu usługi Azure Databricks](vnet-injection-sql-server.md)

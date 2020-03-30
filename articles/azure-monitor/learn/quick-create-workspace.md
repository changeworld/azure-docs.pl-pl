---
title: Tworzenie obszaru roboczego usługi Log Analytics w witrynie Azure Portal | Dokumenty firmy Microsoft
description: Dowiedz się, jak utworzyć obszar roboczy usługi Log Analytics, aby umożliwić rozwiązania do zarządzania i zbieranie danych w środowisku chmury i środowiskach lokalnych w witrynie Azure portal.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2019
ms.openlocfilehash: 3c2e9d5634916c3713b7e3380c0496611d8f60a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656283"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Tworzenie obszaru roboczego usługi Log Analytics w witrynie Azure portal
Użyj menu **obszarów roboczych usługi Log Analytics,** aby utworzyć obszar roboczy usługi Log Analytics przy użyciu portalu Azure. Obszar roboczy usługi Log Analytics to unikatowe środowisko dla danych dziennika usługi Azure Monitor. Każdy obszar roboczy ma własne repozytorium danych i konfigurację, a źródła danych i rozwiązania są skonfigurowane do przechowywania danych w określonym obszarze roboczym. Jeśli zamierzasz zbierać dane z następujących źródeł, potrzebujesz obszaru roboczego usługi Log Analytics:

* Zasoby platformy Azure w ramach subskrypcji
* Komputery lokalne monitorowane przez program System Center Operations Manager
* Kolekcje urządzeń z programu Configuration Manager 
* Diagnostyka lub rejestrowanie danych z magazynu platformy Azure

W przypadku innych źródeł, takich jak maszyny wirtualne platformy Azure i maszyny wirtualne z systemem Windows lub Linux w twoim środowisku, zobacz następujące tematy:

*  [Zbieranie danych z maszyn wirtualnych platformy Azure](../learn/quick-collect-azurevm.md) 
*  [Zbieranie danych z hybrydowego komputera z systemem Linux](../learn/quick-collect-linux-computer.md)
*  [Zbieranie danych z hybrydowego komputera z systemem Windows](quick-collect-windows-computer.md)

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="sign-in-to-azure-portal"></a>Logowanie do witryny Azure Portal
Zaloguj się do witryny Azure portal w [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego
1. W witrynie Azure portal kliknij pozycję **Wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz **obszary robocze usługi Log Analytics**.

    ![Portal Azure](media/quick-create-workspace/azure-portal-01.png)
  
2. Kliknij **pozycję Dodaj**, a następnie wybierz opcje dla następujących elementów:

   * Podaj nazwę nowego **obszaru roboczego usługi Log Analytics,** takiego jak *DefaultLAWorkspace*. Ta nazwa musi być unikatowa globalnie we wszystkich subskrypcjach usługi Azure Monitor.
   * Wybierz **Subskrypcję** do połączenia poprzez wybór subskrypcji z listy rozwijanej, jeśli domyślnie wybrana subskrypcja jest niewłaściwa.
   * W przypadku **grupy zasobów**wybierz opcję użycia istniejącej grupy zasobów już skonfigurowanej lub utwórz nową.  
   * Wybierz dostępną **lokalizację**.  Aby uzyskać więcej informacji, zobacz, w których [regionach usługa Log Analytics jest dostępna](https://azure.microsoft.com/regions/services/) i wyszukaj usługę Azure Monitor w polu **Wyszukaj produkt.**  
   * W przypadku tworzenia obszaru roboczego w nowej subskrypcji utworzonej po 2 kwietnia 2018 r. zostanie automatycznie użyty plan cenowy *Na GB*, a opcja wyboru warstwy cenowej będzie niedostępna.  Jeśli tworzysz obszar roboczy dla istniejącej subskrypcji utworzonej przed 2 kwietnia lub subskrypcji powiązanej z istniejącą rejestracją w ramach umowy Enterprise Agreement (EA), wybierz preferowaną warstwę cenową.  Aby uzyskać więcej informacji na temat poszczególnych warstw, zobacz [Szczegóły cen usługi Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Tworzenie bloku zasobów usługi Log Analytics](media/quick-create-workspace/create-loganalytics-workspace-02.png)  

3. Po podaniu wymaganych informacji w okienku **Obszar roboczy usługi Log Analytics** kliknij przycisk **OK**.  

Podczas weryfikowania informacji i tworzenia obszaru roboczego możesz śledzić postęp w sekcji **Powiadomienia** z poziomu menu. 

## <a name="next-steps"></a>Następne kroki
Teraz, gdy masz dostępny obszar roboczy, można skonfigurować zbieranie danych telemetrycznych monitorowania, uruchomić wyszukiwania dzienników w celu przeanalizowania tych danych i dodać rozwiązanie do zarządzania, aby zapewnić dodatkowe dane i analizy analityczne. 

* Aby włączyć zbieranie danych z zasobów platformy Azure za pomocą usługi Azure Diagnostics lub usługi Azure storage, zobacz [Zbieranie dzienników i metryk usługi platformy Azure do użycia w usłudze Log Analytics.](../platform/collect-azure-metrics-logs.md)  
* [Dodaj program System Center Operations Manager jako źródło danych,](../platform/om-agents.md) aby zbierać dane od agentów raportujących grupę zarządzania programu Operations Manager i przechowywać ją w obszarze roboczym usługi Log Analytics. 
* Połącz [menedżera konfiguracji](../platform/collect-sccm.md) z komputerami importu, które są członkami kolekcji w hierarchii.  
* Przejrzyj dostępne [rozwiązania do monitorowania](../insights/solutions.md) i sposób dodawania lub usuwania rozwiązania z obszaru roboczego.

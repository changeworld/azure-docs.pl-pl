---
title: Wdrażanie do Azure Analysis Services przy użyciu programu Visual Studio (SSDT) | Microsoft Docs
description: Dowiedz się, jak wdrożyć model tabelaryczny na serwerze usług Azure Analysis Services przy użyciu programu SSDT.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2811685df678730b90f2fdb28d472f8fa268ec6a
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932454"
---
# <a name="deploy-a-model-from-visual-studio"></a>Wdrażanie modelu z programu Visual Studio

Po utworzeniu serwera w ramach subskrypcji platformy Azure wszystko jest gotowe do wdrożenia bazy danych modelu tabelarycznego. Program SSDT (SQL Server Data Tools) służy do tworzenia i wdrażania projektu modelu tabelarycznego, nad którymi pracujesz. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne będą następujące elementy:

* **Serwer usług Analysis Services** na platformie Azure. Aby dowiedzieć się więcej, zobacz artykuł [Create an Azure Analysis Services server](analysis-services-create-server.md) (Tworzenie serwera usług Azure Analysis Services).
* **Projekt modelu tabelarycznego** w programie SSDT lub istniejący model tabelaryczny na poziomie zgodności 1200 lub wyższym. Nie wiesz, jak go utworzyć? Spróbuj skorzystać z [samouczka sprzedaży modelowania tabelarycznego projektu Adventure Works Internet Sales](https://docs.microsoft.com/analysis-services/tutorial-tabular-1400/as-adventure-works-tutorial).
* **Brama lokalna** — jeśli co najmniej jedno źródło danych znajduje się w sieci organizacji, należy zainstalować [bramę danych lokalnych](analysis-services-gateway.md). Brama jest niezbędna, aby umożliwić serwerowi w chmurze łączenie się z lokalnymi źródłami danych w celu przetwarzania i odświeżania danych w modelu.

> [!TIP]
> Przed jej wdrożeniem upewnij się, że można przetwarzać dane w tabelach. W programie SSDT kliknij opcje **Model** > **Przetwarzaj** > **Przetwarzaj wszystko**. Jeśli przetwarzanie zakończy się niepowodzeniem, wdrożenie nie jest możliwe.
> 
> 

## <a name="get-the-server-name"></a>Pobierz nazwę serwera

Skopiuj nazwę serwera z **portalu Azure** > serwer > **Omówienie** > **Nazwa serwera**.
   
![Pobieranie nazwy serwera z systemu Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="to-deploy-from-ssdt"></a>Aby wdrożyć z SSDT

1. W programie SSDT > **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt > **Właściwości**. Następnie w obszarze **Wdrożenie** > **Serwer** wklej nazwę serwera.   
   
    ![Wklejanie nazwy serwera we właściwościach serwera wdrażania](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
2. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy **Właściwości**, a następnie kliknij przycisk **Wdróż**. Może zostać wyświetlony monit o zalogowanie się do platformy Azure.
   
    ![Wdrażanie na serwerze](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    Stan wdrożenia pojawia się w oknie danych wyjściowych i oknie wdrażania.
   
    ![Stan wdrożenia](./media/analysis-services-deploy/aas-deploy-status.png)

To wszystko!


## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli wdrożenie zakończy się niepowodzeniem podczas wdrażania metadanych, prawdopodobną przyczyną jest brak połączenia programu SSDT z serwerem. Upewnij się, że możesz połączyć się z serwerem przy użyciu programu SSMS. Upewnij się, że właściwość serwera wdrażania dla projektu jest poprawna.

Jeśli wdrożenie zakończy się niepowodzeniem dla tabeli, prawdopodobnie serwer nie mógł nawiązać połączenia ze źródłem danych. Jeśli źródło danych znajduje się w lokalnej sieci organizacji, należy zainstalować [bramę danych lokalnych](analysis-services-gateway.md).

## <a name="next-steps"></a>Następne kroki

Po wdrożeniu modelu tabelarycznego na serwerze możesz się z nim połączyć. W celu zarządzania modelem możesz [połączyć się przy użyciu programu SSMS](analysis-services-manage.md). Możesz również [nawiązać połączenie za pomocą narzędzia klienta](analysis-services-connect.md), takiego jak usługi Power BI, Power BI Desktop lub program Excel, i rozpocząć tworzenie raportów.


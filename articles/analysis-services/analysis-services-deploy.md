---
title: Wdrażanie modelu w usługach Azure Analysis Services przy użyciu programu Visual Studio | Dokumenty firmy Microsoft
description: Dowiedz się, jak wdrożyć model tabelaryczny na serwerze usług Azure Analysis Services przy użyciu programu Visual Studio.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 71b3b7815d2a4b0b4de3afdca9db93156f505445
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572875"
---
# <a name="deploy-a-model-from-visual-studio"></a>Wdrażanie modelu z programu Visual Studio

Po utworzeniu serwera w ramach subskrypcji platformy Azure wszystko jest gotowe do wdrożenia bazy danych modelu tabelarycznego. Program Visual Studio z projektami analysis services służy do tworzenia i wdrażania projektu modelu tabelaryczne, nad który pracujesz. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne będą następujące elementy:

* **Serwer usług Analysis Services** na platformie Azure. Aby dowiedzieć się więcej, zobacz artykuł [Create an Azure Analysis Services server](analysis-services-create-server.md) (Tworzenie serwera usług Azure Analysis Services).
* **Projekt modelu tabelaryczne** w programie Visual Studio lub istniejącego modelu tabelaryczne na poziomie zgodności 1200 lub wyższym. Nie wiesz, jak go utworzyć? Spróbuj skorzystać z [samouczka sprzedaży modelowania tabelarycznego projektu Adventure Works Internet Sales](https://docs.microsoft.com/analysis-services/tutorial-tabular-1400/as-adventure-works-tutorial).
* **Brama lokalna** — jeśli co najmniej jedno źródło danych znajduje się w sieci organizacji, należy zainstalować [bramę danych lokalnych](analysis-services-gateway.md). Brama jest niezbędna, aby umożliwić serwerowi w chmurze łączenie się z lokalnymi źródłami danych w celu przetwarzania i odświeżania danych w modelu.

> [!TIP]
> Przed jej wdrożeniem upewnij się, że można przetwarzać dane w tabelach. W programie Visual Studio kliknij pozycję Proces**procesu** >  **modelu** > **.** Jeśli przetwarzanie zakończy się niepowodzeniem, wdrożenie nie jest możliwe.
> 
> 

## <a name="get-the-server-name"></a>Pobierz nazwę serwera

Skopiuj nazwę serwera z **portalu Azure** > serwer > **Omówienie** > **Nazwa serwera**.
   
![Pobieranie nazwy serwera z systemu Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="to-deploy-from-visual-studio"></a>Aby wdrożyć w programie Visual Studio

1. W programie Visual Studio > **Solution Explorer**kliknij prawym przyciskiem myszy polecenie **Właściwości**projektu > . Następnie w**serwerze** **wdrażania** > wklej nazwę serwera.   
   
    ![Wklejanie nazwy serwera we właściwościach serwera wdrażania](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
2. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy **Właściwości**, a następnie kliknij przycisk **Wdróż**. Może zostać wyświetlony monit o zalogowanie się do platformy Azure.
   
    ![Wdrażanie na serwerze](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    Stan wdrożenia pojawia się w oknie danych wyjściowych i oknie wdrażania.
   
    ![Stan wdrożenia](./media/analysis-services-deploy/aas-deploy-status.png)

To wszystko!


## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli wdrożenie nie powiedzie się podczas wdrażania metadanych, prawdopodobnie program Visual Studio nie może połączyć się z serwerem. Upewnij się, że możesz połączyć się z serwerem przy użyciu programu SSMS. Upewnij się, że właściwość serwera wdrażania dla projektu jest poprawna.

Jeśli wdrożenie zakończy się niepowodzeniem dla tabeli, prawdopodobnie serwer nie mógł nawiązać połączenia ze źródłem danych. Jeśli źródło danych znajduje się w lokalnej sieci organizacji, należy zainstalować [bramę danych lokalnych](analysis-services-gateway.md).

## <a name="next-steps"></a>Następne kroki

Po wdrożeniu modelu tabelarycznego na serwerze możesz się z nim połączyć. Aby [nim zarządzać, można połączyć się z nim za pomocą programu SQL Server Management Studio (SSMS).](analysis-services-manage.md) Możesz również [nawiązać połączenie za pomocą narzędzia klienta](analysis-services-connect.md), takiego jak usługi Power BI, Power BI Desktop lub program Excel, i rozpocząć tworzenie raportów.


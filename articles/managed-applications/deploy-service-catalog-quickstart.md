---
title: Wdróż aplikację katalogu usług za pomocą witryny Azure portal | Dokumentacja firmy Microsoft
description: Pokazuje konsumentów Managed Applications, jak wdrożyć aplikację katalogu usług za pośrednictwem witryny Azure portal.
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: 4d2e8b442f70ee791fe65a32402e5272eda3f209
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60588902"
---
# <a name="deploy-service-catalog-app-through-azure-portal"></a>Wdróż aplikację katalogu usług za pośrednictwem witryny Azure portal

W [poprzedzających szybkiego startu](publish-managed-app-definition-quickstart.md), opublikowane definicję aplikacji zarządzanej. W tym przewodniku Szybki Start utworzysz aplikację katalogu usług z tej definicji.

## <a name="create-service-catalog-app"></a>Utwórz katalog usługi app service

W witrynie Azure portal wykonaj następujące czynności:

1. Wybierz pozycję **Utwórz zasób**.

   ![Tworzenie zasobu](./media/deploy-service-catalog-quickstart/create-new.png)

1. Wyszukaj **aplikacji zarządzanych katalogu usług** i wybierz ją z listy dostępnych opcji.

   ![Wyszukaj aplikację katalogu usług](./media/deploy-service-catalog-quickstart/select-service-catalog.png)

1. Opis usługi zarządzanej aplikacji zostanie wyświetlony. Wybierz pozycję **Utwórz**.

   ![Wybieranie pozycji Utwórz](./media/deploy-service-catalog-quickstart/create-service-catalog.png)

1. W portalu jest wyświetlany definicji aplikacji zarządzanej, które mają dostęp do. Dostępne definicje zaznacz ten, który chcesz wdrożyć. W tym przewodniku Szybki Start użyj **zarządzanego konta magazynu** definicji, który został utworzony w poprzednim przewodniku Szybki Start. Wybierz pozycję **Utwórz**.

   ![Wybierz definicję do wdrożenia](./media/deploy-service-catalog-quickstart/select-definition.png)

1. Podaj wartości dla **podstawy** kartę. Wybierz subskrypcję platformy Azure, aby wdrożyć aplikację katalogu usług. Utwórz nową grupę zasobów o nazwie **applicationGroup**. Wybierz lokalizację dla aplikacji. Po zakończeniu wybierz pozycję **OK**.

   ![Podanie wartości basic](./media/deploy-service-catalog-quickstart/provide-basics.png)

1. Podaj prefiks dla nazwy konta magazynu. Wybierz typ konta magazynu. Po zakończeniu wybierz pozycję **OK**.

   ![Podawanie wartości magazynu](./media/deploy-service-catalog-quickstart/provide-storage.png)

1. Przejrzyj podsumowanie. Po weryfikacji zakończy się powodzeniem, zaznacz **OK** do rozpoczęcia wdrożenia.

   ![Wyświetl podsumowanie](./media/deploy-service-catalog-quickstart/view-summary.png)

## <a name="view-results"></a>Wyświetlanie wyników

Po wdrożeniu usługi aplikacji wykazu, masz dwie nowe grupy zasobów. Jedna grupa zasobów zawiera aplikację katalogu usług. Grupa zasobów zawiera zasoby, które usługi katalogu aplikacji.

1. Wyświetl grupę zasobów o nazwie **applicationGroup** aby zobaczyć aplikację katalogu usług.

   ![Wyświetl aplikację](./media/deploy-service-catalog-quickstart/view-managed-application.png)

1. Wyświetl grupę zasobów o nazwie **applicationGroup {wyznaczania wartości skrótu characters}** do wyświetlania zasobów usługi katalogu aplikacji.

   ![Wyświetl zasoby](./media/deploy-service-catalog-quickstart/view-resources.png)

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się, jak utworzyć pliki definicji aplikacji zarządzanej, zobacz [tworzenie i publikowanie w definicji aplikacji zarządzanej](publish-service-catalog-app.md).
* Wiersza polecenia platformy Azure, zobacz [Wdróż usługi katalogu aplikacji za pomocą wiersza polecenia platformy Azure](./scripts/managed-application-cli-sample-create-application.md).
* W przypadku programu PowerShell, zobacz [wdrażanie usługi app Service catalog przy użyciu programu PowerShell](./scripts/managed-application-poweshell-sample-create-application.md).
---
title: Użyj Azure Portal, aby wdrożyć aplikację katalogu usług
description: Pokazuje odbiorców aplikacji zarządzanych, jak wdrożyć aplikację katalogu usług za pomocą Azure Portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: 3fa9709e096e908907772c940fc5e2f2895b7eb3
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75650788"
---
# <a name="deploy-service-catalog-app-through-azure-portal"></a>Wdróż aplikację katalogu usług za Azure Portal

W [poprzednim przewodniku szybki start](publish-managed-app-definition-quickstart.md)opublikowano definicję aplikacji zarządzanej. W tym przewodniku szybki start utworzysz aplikację katalogu usług z tej definicji.

## <a name="create-service-catalog-app"></a>Tworzenie aplikacji katalogu usług

W Azure Portal wykonaj następujące czynności:

1. Wybierz pozycję **Utwórz zasób**.

   ![Tworzenie zasobu](./media/deploy-service-catalog-quickstart/create-new.png)

1. Wyszukaj **aplikację zarządzaną katalogu usług** i wybierz ją z dostępnych opcji.

   ![Wyszukaj aplikację katalogu usług](./media/deploy-service-catalog-quickstart/select-service-catalog.png)

1. Zobaczysz Opis usługi zarządzanej aplikacji. Wybierz pozycję **Utwórz**.

   ![Wybieranie pozycji Utwórz](./media/deploy-service-catalog-quickstart/create-service-catalog.png)

1. W portalu zostaną wyświetlone definicje aplikacji zarządzanych, do których masz dostęp. Z dostępnych definicji Wybierz ten, który chcesz wdrożyć. W tym przewodniku szybki start Użyj definicji **zarządzanego konta magazynu** utworzonej w poprzednim przewodniku Szybki Start. Wybierz pozycję **Utwórz**.

   ![Wybierz definicję do wdrożenia](./media/deploy-service-catalog-quickstart/select-definition.png)

1. Podaj wartości dla karty **podstawy** . Wybierz subskrypcję platformy Azure, w której chcesz wdrożyć aplikację katalogu usług. Utwórz nową grupę zasobów o nazwie **aplikacja**. Wybierz lokalizację dla swojej aplikacji. Po zakończeniu wybierz przycisk **OK**.

   ![Podaj wartości dla warstwy Podstawowa](./media/deploy-service-catalog-quickstart/provide-basics.png)

1. Podaj prefiks dla nazwy konta magazynu. Wybierz typ konta magazynu do utworzenia. Po zakończeniu wybierz przycisk **OK**.

   ![Podaj wartości dla magazynu](./media/deploy-service-catalog-quickstart/provide-storage.png)

1. Przejrzyj podsumowanie. Po pomyślnym zakończeniu walidacji wybierz pozycję **OK** , aby rozpocząć wdrażanie.

   ![Wyświetl podsumowanie](./media/deploy-service-catalog-quickstart/view-summary.png)

## <a name="view-results"></a>Wyświetlanie wyników

Po wdrożeniu aplikacji katalogu usług będziesz mieć dwie nowe grupy zasobów. Jedna grupa zasobów zawiera aplikację katalogu usług. Inna grupa zasobów zawiera zasoby dla aplikacji katalogu usług.

1. Wyświetl grupę zasobów o nazwie **aplikacja** , aby wyświetlić aplikację katalogu usług.

   ![Wyświetl aplikację](./media/deploy-service-catalog-quickstart/view-managed-application.png)

1. Wyświetl grupę zasobów o nazwie **Application Group {hash-characters}** , aby wyświetlić zasoby dla aplikacji katalogu usług.

   ![Wyświetl zasoby](./media/deploy-service-catalog-quickstart/view-resources.png)

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak utworzyć pliki definicji dla aplikacji zarządzanej, zobacz [Tworzenie i publikowanie definicji aplikacji zarządzanej](publish-service-catalog-app.md).
* Aby zapoznać się z interfejsem wiersza polecenia platformy Azure, zobacz [Deploying Service Catalog app with Azure CLI](./scripts/managed-application-cli-sample-create-application.md).
* Aby uzyskać obsługę programu PowerShell, zobacz [wdrażanie aplikacji wykazu usług przy użyciu programu PowerShell](./scripts/managed-application-poweshell-sample-create-application.md).
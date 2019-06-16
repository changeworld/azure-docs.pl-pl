---
title: Ciągłe wdrażanie dla usługi Azure Functions | Dokumentacja firmy Microsoft
description: Publikować swoje funkcje za pomocą funkcji ciągłego wdrażania usługi Azure App Service.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/25/2016
ms.author: glenga
ms.openlocfilehash: d4d2f24a0a7b1f01627ed2cea4a5732ca0e001c9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67068392"
---
# <a name="continuous-deployment-for-azure-functions"></a>Ciągłe wdrażanie dla usługi Azure Functions

Usługa Azure Functions umożliwia wdrażanie ciągłe za pomocą kodu [Integracja kontroli źródła](functions-deployment-technologies.md#source-control). Dzięki temu, gdy kod aktualizuje wyzwalacza przepływu pracy wdrożenia na platformie Azure. Jeśli jesteś nowym użytkownikiem usługi Azure Functions, Rozpoczynanie pracy z usługą [omówienia usługi Azure Functions](functions-overview.md).

Ciągłe wdrażanie to świetna opcja dla projektów gdzie one integracji wielu i częste odzyskiwanie pamięci wkładów. Umożliwia również utrzymania pojedyncze źródło prawdziwych kodu funkcji. Można skonfigurować ciągłe wdrażanie w usłudze Azure Functions z następujących lokalizacji kodu źródłowego:

* [Azure Repos](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

Jednostka wdrożenia dla usługi Azure functions jest aplikacja funkcji. Oznacza to, że wszystkie funkcje w aplikacji funkcji są wdrażane w tym samym czasie. Po włączeniu ciągłego wdrażania, dostęp do kodu funkcji w witrynie Azure portal jest skonfigurowany jako *tylko do odczytu*, ponieważ źródło prawdziwych danych jest równa się gdzie indziej.

## <a name="requirements-for-continuous-deployment"></a>Wymagania dotyczące ciągłego wdrażania

Ciągłe wdrażanie zakończyło się sukcesem, strukturę katalogu musi być zgodny następującą strukturę folderów podstawowego, który oczekuje, że usługi Azure Functions:

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

## <a name="credentials"></a>Skonfiguruj ciągłe wdrażanie

Użyj tej procedury, aby skonfigurować ciągłe wdrażanie dla istniejącej aplikacji funkcji. Te kroki pokazują, integracja z repozytorium GitHub, ale podobne kroki mają zastosowanie dla repozytoriów platformy Azure lub innych repozytoriów kodu źródłowego.

1. W aplikacji funkcji w [witryny Azure portal](https://portal.azure.com), wybierz opcję **funkcje platformy** > **Centrum wdrażania**.

    ![Otwieranie Centrum wdrażania](./media/functions-continuous-deployment/platform-features.png)

2. Na **Centrum wdrażania**, wybierz opcję **GitHub** dla **kontroli źródła** > **Autoryzuj**.

    ![Centrum wdrażania](./media/functions-continuous-deployment/github.png)

3. Wybierz **autoryzować AzureAppService** > **nadal**.

    ![Autoryzowanie](./media/functions-continuous-deployment/authorize.png)

4. Wybierz jedną z następujących dostawców kompilacji:

    * **Usługa App Service Utwórz usługę** — jest to najlepsze, gdy kompilacja nie jest konieczne, lub jeśli potrzebujesz ogólnego kompilacji.
    * **Potoki usługi Azure (wersja zapoznawcza)** — najlepiej, gdy potrzebujesz większej kontroli nad kompilacji. Ten dostawca jest obecnie w wersji zapoznawczej.

    ![Wybieranie dostawcy kompilacji](./media/functions-continuous-deployment/build.png)

5. Skonfiguruj informacje specyficzne dla opcji kontroli źródła, określony. Usługi github, musisz podać **organizacji**, **repozytorium**, i **gałęzi** lokalizacji kodu. Następnie wybierz **Kontynuuj**.

    ![Konfigurowanie usługi GitHub](./media/functions-continuous-deployment/github-specifics.png)

6. Ponadto Przejrzyj wszystkie szczegółowe informacje, a następnie wybierz **Zakończ** do ukończenia konfiguracji wdrożenia.

    ![Podsumowanie](./media/functions-continuous-deployment/summary.png)

Po zakończeniu procesu, cały kod pochodzących z określonego źródła jest wdrażana do swojej aplikacji. W tym momencie zmiany w źródle wdrożenia to wyzwolenie wdrożenia tych zmian do aplikacji funkcji na platformie Azure.

## <a name="deployment-scenarios"></a>Scenariusze wdrażania

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>Przenoszenie istniejących funkcji do ciągłego wdrażania

Jeśli funkcje zostały już wpisane [witryny Azure portal](https://portal.azure.com) i chcesz pobrać zawartość aplikacji przed przełączeniem do ciągłego wdrażania, należy przejść do **Przegląd** kartę funkcji aplikacji i kliknij pozycję **Pobierz zawartość aplikacji** przycisku.

![Pobieranie zawartości aplikacji](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> Po skonfigurowaniu ciągłej integracji, nie będzie można edytować plików źródłowych w portalu usługi Functions.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)

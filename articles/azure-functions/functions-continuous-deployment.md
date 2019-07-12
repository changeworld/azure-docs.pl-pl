---
title: Ciągłe wdrażanie dla usługi Azure Functions | Dokumentacja firmy Microsoft
description: Użyj funkcji ciągłego wdrażania w usłudze Azure App Service do funkcji opublikowania.
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
ms.openlocfilehash: dd1605aa2f5fc9e0b4bc790bae2a1c20cb3ea048
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594534"
---
# <a name="continuous-deployment-for-azure-functions"></a>Ciągłe wdrażanie dla usługi Azure Functions

Usługa Azure Functions umożliwia ciągłe wdrażanie kodu za pomocą [Integracja kontroli źródła](functions-deployment-technologies.md#source-control). Integracja kontroli źródła Włącza przepływ pracy, w którym aktualizacji kodu wyzwala wdrażanie na platformie Azure. Jeśli jesteś nowym użytkownikiem usługi Azure Functions, rozpocząć od przejrzenia [omówienia usługi Azure Functions](functions-overview.md).

Ciągłego wdrażania jest dobrym rozwiązaniem dla projektów, w którym możesz wprowadzić integrację z wieloma i częste odzyskiwanie pamięci wkładów. Korzystając z ciągłym wdrażaniem, to Ty masz jednego źródła faktów w kodzie, co pozwala łatwo współpracy. Można skonfigurować ciągłe wdrażanie w usłudze Azure Functions z następujących lokalizacji kodu źródłowego:

* [Azure Repos](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

Jednostka wdrażania dla funkcji na platformie Azure jest aplikacja funkcji. Wszystkie funkcje w aplikacji funkcji są wdrażane w tym samym czasie. Po włączeniu ciągłego wdrażania, dostęp do kodu funkcji w witrynie Azure portal jest skonfigurowany jako *tylko do odczytu* ponieważ źródło prawdziwych danych jest równa się gdzie indziej.

## <a name="requirements-for-continuous-deployment"></a>Wymagania dotyczące ciągłego wdrażania

Do ciągłego wdrażania została wykonana pomyślnie strukturę katalogu musi być zgodna ze strukturą folder podstawowy, który oczekuje, że usługi Azure Functions.

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

## <a name="credentials"></a>Skonfiguruj ciągłe wdrażanie

Aby skonfigurować ciągłe wdrażanie dla istniejącej aplikacji funkcji, należy wykonać następujące kroki. Towarzyszącym integrację z repozytorium GitHub, ale podobne kroki mają zastosowanie dla repozytoriów platformy Azure lub innych repozytoriów kodu źródłowego.

1. W aplikacji funkcji w [witryny Azure portal](https://portal.azure.com), wybierz opcję **funkcje platformy** > **Centrum wdrażania**.

    ![Otwórz Centrum wdrażania](./media/functions-continuous-deployment/platform-features.png)

2. W **Centrum wdrażania**, wybierz opcję **GitHub**, a następnie wybierz pozycję **Autoryzuj**. Jeśli została już autoryzowana GitHub, wybierz opcję **Kontynuuj**. 

    ![Centrum wdrażania usługi aplikacji Azure](./media/functions-continuous-deployment/github.png)

3. W usłudze GitHub, wybierz **autoryzować AzureAppService** przycisku. 

    ![Zezwolić usłudze Azure App Service](./media/functions-continuous-deployment/authorize.png)
    
    W **Centrum wdrażania** w witrynie Azure portal wybierz **Kontynuuj**.

4. Wybierz jedną z następujących dostawców kompilacji:

    * **Usługa App Service Utwórz usługę**: Najlepiej, gdy kompilacja nie jest konieczne, lub jeśli potrzebujesz ogólnego kompilacji.
    * **Potoki usługi Azure (wersja zapoznawcza)** : Najlepiej, gdy potrzebujesz więcej kontrolę nad kompilacji. Ten dostawca jest obecnie w wersji zapoznawczej.

    ![Wybierz dostawcę kompilacji](./media/functions-continuous-deployment/build.png)

5. Skonfiguruj informacje specyficzne dla opcji kontroli źródła, określony. Usługi github, musisz wprowadzić lub wybrać wartości **organizacji**, **repozytorium**, i **gałęzi**. Wartości są oparte na lokalizacji kodu. Następnie wybierz **Kontynuuj**.

    ![Konfigurowanie usługi GitHub](./media/functions-continuous-deployment/github-specifics.png)

6. Przejrzyj wszystkie szczegółowe informacje, a następnie wybierz pozycję **Zakończ** do ukończenia konfiguracji wdrożenia.

    ![Podsumowanie](./media/functions-continuous-deployment/summary.png)

Po zakończeniu procesu całego kodu pochodzących z określonego źródła jest wdrażana do swojej aplikacji. W tym momencie zmiany w źródle wdrożenia to wyzwolenie wdrożenia tych zmian do aplikacji funkcji na platformie Azure.

## <a name="deployment-scenarios"></a>Scenariusze wdrażania

<a name="existing"></a>

### <a name="move-existing-functions-to-continuous-deployment"></a>Przenoszenie istniejących funkcji do ciągłego wdrażania

Jeśli funkcje zostały już wpisane [witryny Azure portal](https://portal.azure.com) i chcesz pobrać zawartość aplikacji, przed przejściem do ciągłego wdrażania, przejdź do **Przegląd** kartę aplikacji funkcji. Wybierz **Pobierz zawartość aplikacji** przycisku.

![Pobierz zawartość aplikacji](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> Po skonfigurowaniu ciągłej integracji, nie będzie można edytować plików źródłowych w portalu usługi Functions.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)

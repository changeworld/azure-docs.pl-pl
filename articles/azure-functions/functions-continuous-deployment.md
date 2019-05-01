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
ms.openlocfilehash: cb3f3ad3bb7b42429654ea4bf9b49f7e230db1da
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943879"
---
# <a name="continuous-deployment-for-azure-functions"></a>Ciągłe wdrażanie dla usługi Azure Functions
Usługa Azure Functions można łatwo wdrożyć aplikację funkcji przy użyciu ciągłej integracji. Functions można integrować z głównych repozytoriów i źródła wdrożenia. Integracja ta włącza przepływ pracy gdzie kod funkcji aktualizacje wprowadzone za pomocą jednej z tych usług wyzwalacza wdrożenia na platformie Azure. Jeśli jesteś nowym użytkownikiem usługi Azure Functions, skorzystaj z [omówienia usługi Azure Functions](functions-overview.md).

Ciągłe wdrażanie to świetna opcja dla projektów gdzie one integracji wielu i częste odzyskiwanie pamięci wkładów. Umożliwia ona także Obsługa kontroli źródła w kodzie funkcji. Usługa Azure Functions obsługuje następujące źródła wdrożenia:

* [Bitbucket](https://bitbucket.org/)
* [Dropbox](https://www.dropbox.com/)
* Repozytorium zewnętrzne (Git lub Mercurial)
* [Lokalne repozytorium Git](../app-service/deploy-local-git.md)
* [GitHub](https://github.com)
* [OneDrive](https://onedrive.live.com/)
* [Usługa Azure DevOps](https://azure.microsoft.com/services/devops/)

Wdrożenia są konfigurowane dla poszczególnych funkcji aplikacji. Po włączeniu ciągłego wdrażania, dostęp do kodu funkcji w portalu jest ustawiona na *tylko do odczytu*.

## <a name="requirements-for-continuous-deployment"></a>Wymagania dotyczące ciągłego wdrażania

Przed skonfigurowaniem ciągłego wdrażania, należy skonfigurować źródła wdrożenia oraz kod funkcji w źródło wdrożenia. We wdrożeniu aplikacji funkcji każda funkcja znajduje się w podkatalogu nazwanego, gdzie nazwa katalogu jest nazwa funkcji.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Aby można było wdrożyć z DevOps platformy Azure, musisz połączyć organizacji DevOps platformy Azure z subskrypcją platformy Azure. Aby uzyskać więcej informacji, zobacz [skonfiguruj rozliczenia dla Twojej organizacji DevOps platformy Azure](https://docs.microsoft.com/azure/devops/organizations/billing/set-up-billing-for-your-organization-vs?view=vsts#set-up-billing-via-the-azure-portal).

## <a name="set-up-continuous-deployment"></a>Konfigurowanie ciągłego wdrażania
Użyj tej procedury, aby skonfigurować ciągłe wdrażanie dla istniejącej aplikacji funkcji. Te kroki pokazują, integracja z repozytorium GitHub, ale podobne kroki mają zastosowanie dla DevOps platformy Azure lub innych usług wdrożenia.

1. W aplikacji funkcji w [witryny Azure portal](https://portal.azure.com), wybierz opcję **funkcje platformy** > **opcje wdrażania**. 
   
    ![Opcje otwierania opcje wdrażania](./media/functions-continuous-deployment/setup-deployment.png)
 
1. Na **wdrożeń** bloku wybierz **Instalatora**.
 
    ![Blok wdrożenia](./media/functions-continuous-deployment/setup-deployment-1.png)
   
1. Na **źródło wdrożenia** bloku wybierz **wybierz źródło**. Wprowadź informacje dla źródła wdrożenia wybrany, a następnie wybierz **OK**.
   
    ![Wybieranie źródła wdrożenia](./media/functions-continuous-deployment/choose-deployment-source.png)

Po skonfigurowaniu ciągłego wdrażania, wszystkie zmiany plików w źródle wdrażania są kopiowane do aplikacji funkcji i wdrażania witrynę w trybie pełnym, zostanie wywołany. Witryna jest ponownie wdrażana podczas aktualizacji plików w źródle.

## <a name="deployment-scenarios"></a>Scenariusze wdrażania

Wdrożenia typowe scenariusze obejmują tworzenie wdrażania przejściowego i przenoszenie istniejących funkcji ciągłe wdrażanie.

<a name="staging"></a>
### <a name="create-a-staging-deployment"></a>Tworzenie wdrożenia przejściowego

Aplikacje funkcji jeszcze nie obsługuje miejsc wdrożenia. Ale nadal można zarządzać oddzielnych wdrożeń środowisk przejściowych i produkcyjnych przy użyciu ciągłej integracji.

Proces konfigurowania i pracować z wdrażania przejściowego ogólnie wyglądają następująco:

1. Utworzyć dwie aplikacje funkcji w ramach subskrypcji: jeden dla kodu produkcyjnego i jeden dla przemieszczania. 

1. Utwórz źródło wdrożenia, jeśli nie masz jeszcze jeden. W tym przykładzie użyto [GitHub].

1. Dla aplikacji funkcji produkcji wykonaj poprzednie kroki w [Konfigurowanie wdrażania ciągłego](#set-up-continuous-deployment) i ustaw gałąź, wdrożenia do głównej gałęzi repozytorium GitHub.
   
    ![Wybrane opcje, aby wybrać gałąź wdrożenia](./media/functions-continuous-deployment/choose-deployment-branch.png)

1. Powtórz krok 3 dla tymczasowej aplikacji funkcji, ale zamiast tego wybierz tymczasową gałęzi w repozytorium GitHub. Jeśli źródła wdrożenia nie obsługuje gałęzi, należy użyć innego folderu.
    
1. Aktualizowanie kodu w gałęzi tymczasowej lub folder, a następnie sprawdź, czy wdrażania przejściowego uwzględni te zmiany.

1. Po zakończeniu testowania scalanie zmian z przemieszczania gałęzi z gałęzią główną. To scalanie wyzwala wdrożenia do aplikacji funkcji w środowisku produkcyjnym. Jeśli źródła wdrożenia nie obsługuje gałęzie, należy zastąpić pliki w folderze produkcji pliki z folderu przemieszczania.

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>Przenoszenie istniejących funkcji do ciągłego wdrażania
Jeśli masz istniejące funkcje, które zostały utworzone i obsługiwane w portalu, musisz pobrać plików kodu funkcji przy użyciu protokołu FTP i lokalnego repozytorium Git, zanim można skonfigurować ciągłe wdrażanie, zgodnie z wcześniejszym opisem. Można to zrobić w ustawieniach usługi Azure App Service dla aplikacji funkcji. Po pobraniu plików, należy przekazać je do źródła wybrany ciągłego wdrażania.

> [!NOTE]
> Po skonfigurowaniu ciągłej integracji, nie będzie można edytować plików źródłowych w portalu usługi Functions.

<a name="credentials"></a>
#### <a name="configure-deployment-credentials"></a>Skonfiguruj poświadczenia wdrożenia
Przed pobraniem plików z aplikacji funkcji przy użyciu protokołu FTP i lokalnego repozytorium Git, należy skonfigurować swoje poświadczenia, aby przejść do witryny. Poświadczenia są ustawiane na poziomie aplikacji funkcji. Aby ustawić poświadczenia wdrażania w witrynie Azure portal, wykonaj następujące kroki:

1. W aplikacji funkcji w [witryny Azure portal](https://portal.azure.com), wybierz opcję **funkcje platformy** > **poświadczenia wdrożenia**.
   
1. Wprowadź nazwę użytkownika i hasło, a następnie wybierz pozycję **Zapisz**. 

   ![Wybrane opcje, aby ustawić poświadczenia wdrażania lokalnego](./media/functions-continuous-deployment/setup-deployment-credentials.png)

Te poświadczenia można teraz używać dostęp do aplikacji funkcji z FTP lub wbudowane repozytorium Git.

<a name="downftp"></a>
#### <a name="download-files-by-using-ftp"></a>Pobieranie plików przy użyciu protokołu FTP

1. W aplikacji funkcji w [witryny Azure portal](https://portal.azure.com), wybierz opcję **funkcje platformy** > **właściwości**. Następnie skopiuj wartości pól **użytkownika serwera FTP/wdrożenia**, **nazwy hosta FTP**, i **nazwy hosta FTPS**.  

   **Użytkownik serwera FTP/wdrożenia** należy wprowadzić w postaci wyświetlanej w portalu, łącznie z nazwą aplikacji w celu zapewnienia prawidłowego kontekstu serwera FTP.
   
   ![Wybrane elementy, aby uzyskać informacje o wdrożeniu](./media/functions-continuous-deployment/get-deployment-credentials.png)

1. Z poziomu klienta FTP należy użyć informacji zebranych, aby podłączyć się do aplikacji i pobrania plików źródłowych dla funkcji.

<a name="downgit"></a>
#### <a name="download-files-by-using-a-local-git-repository"></a>Pobieranie plików przy użyciu lokalnego repozytorium Git

1. W aplikacji funkcji w [witryny Azure portal](https://portal.azure.com), wybierz opcję **funkcje platformy** > **opcje wdrażania**. 
   
    ![Opcje otwierania opcje wdrażania](./media/functions-continuous-deployment/setup-deployment.png)
 
1. Następnie na **wdrożeń** bloku wybierz **Instalatora**.
 
    ![Blok wdrożenia](./media/functions-continuous-deployment/setup-deployment-1.png)
   
1. Na **źródło wdrożenia** bloku wybierz **lokalnym repozytorium Git** > **OK**.

1. W **funkcje platformy**, wybierz opcję **właściwości** i zanotuj wartość adresu URL usługi Git. 
   
    ![Opcje w celu uzyskania adresu URL usługi Git](./media/functions-continuous-deployment/get-local-git-deployment-url.png)

1. Sklonuj repozytorium na komputerze lokalnym przy użyciu wiersza polecenia Git-aware lub ulubionego narzędzia Git. Git polecenie klonowania może wyglądać następująco:
   
        git clone https://username@my-function-app.scm.azurewebsites.net:443/my-function-app.git

1. Pobieranie plików z aplikacji funkcji do klonu komputera lokalnego, jak w poniższym przykładzie:
   
        git pull origin master
   
    Jeśli jest to wymagane, podaj swoje [skonfigurowane poświadczenia wdrożenia](#credentials).  

[GitHub]: https://github.com/

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)

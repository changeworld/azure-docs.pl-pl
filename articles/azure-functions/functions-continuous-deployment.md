---
title: Ciągłe wdrażanie dla usługi Azure Functions | Dokumentacja firmy Microsoft
description: Ciągłe wdrażanie urządzeń w usłudze Azure App Service umożliwia publikowanie funkcji platformy Azure.
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
ms.openlocfilehash: fd8fa690c508b8bf748490668c1e9aaa811ac247
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60731297"
---
# <a name="continuous-deployment-for-azure-functions"></a>Ciągłe wdrażanie dla usługi Azure Functions
Usługa Azure Functions sprawia, że można łatwo wdrożyć aplikację funkcji przy użyciu ciągłej integracji usługi App Service. Functions można integrować z usług BitBucket, Dropbox, GitHub i DevOps platformy Azure. Dzięki temu przepływu pracy gdzie kod funkcji aktualizacje wprowadzone przy użyciu jednej z tych wdrażanie wyzwalaczy zintegrowanych usług na platformie Azure. Jeśli jesteś nowym użytkownikiem usługi Azure Functions, skorzystaj z [Azure Functions — omówienie](functions-overview.md).

Ciągłe wdrażanie to doskonałe rozwiązanie dla projektów, w których ma miejsce częste współtworzenie wielu treści. Umożliwia także Obsługa kontroli źródła nad kodem funkcji. Obecnie obsługiwane są następujące źródła wdrożenia:

* [Bitbucket](https://bitbucket.org/)
* [Dropbox](https://www.dropbox.com/)
* Repozytorium zewnętrzne (Git lub Mercurial)
* [Lokalne repozytorium Git](../app-service/deploy-local-git.md)
* [GitHub](https://github.com)
* [OneDrive](https://onedrive.live.com/)
* [Usługa Azure DevOps](https://azure.microsoft.com/services/devops/)

Wdrożenia są konfigurowane dla poszczególnych funkcji aplikacji. Po włączeniu ciągłego wdrażania, dostęp do kodu funkcji w portalu jest ustawiona na *tylko do odczytu*.

## <a name="continuous-deployment-requirements"></a>Wymagania dotyczące wdrażania ciągłego

Konieczne jest posiadanie źródła wdrożenia skonfigurowane, a kod funkcji w źródło wdrożenia przed skonfigurowaniem ciągłego wdrażania. W przypadku wdrożenia aplikacji daną funkcję każda funkcja znajduje się w podkatalogu nazwanego, gdzie nazwa katalogu jest nazwa funkcji.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Aby można było wdrożyć z DevOps platformy Azure, musisz połączyć organizacji DevOps platformy Azure z subskrypcją platformy Azure. Aby uzyskać więcej informacji, zobacz [skonfiguruj rozliczenia dla Twojej organizacji DevOps platformy Azure](https://docs.microsoft.com/azure/devops/organizations/billing/set-up-billing-for-your-organization-vs?view=vsts#set-up-billing-via-the-azure-portal).

## <a name="set-up-continuous-deployment"></a>Konfigurowanie ciągłego wdrażania
Użyj tej procedury, aby skonfigurować ciągłe wdrażanie dla istniejącej aplikacji funkcji. Te kroki pokazują, integracja z repozytorium GitHub, ale podobne kroki mają zastosowanie dla DevOps platformy Azure lub innych usług wdrożenia.

1. W aplikacji funkcji w [witryny Azure portal](https://portal.azure.com), kliknij przycisk **funkcje platformy** i **opcje wdrażania**. 
   
    ![Konfigurowanie ciągłego wdrażania](./media/functions-continuous-deployment/setup-deployment.png)
 
2. Następnie w **wdrożeń** bloku kliknij pozycję **Instalatora**.
 
    ![Konfigurowanie ciągłego wdrażania](./media/functions-continuous-deployment/setup-deployment-1.png)
   
3. W **źródło wdrożenia** bloku kliknij **wybierz źródło**, a następnie wypełnij informacje dotyczące źródła wdrożenia wybranej i kliknij przycisk **OK**.
   
    ![Wybierz źródło wdrożenia](./media/functions-continuous-deployment/choose-deployment-source.png)

Po skonfigurowaniu ciągłego wdrażania, wszystkie zmiany plików w źródle wdrażania są kopiowane do aplikacji funkcji i wdrażania witrynę w trybie pełnym, zostanie wywołany. Witryna jest ponownie wdrażana podczas aktualizacji plików w źródle.

## <a name="deployment-options"></a>Opcje wdrożenia

Poniżej przedstawiono kilka scenariuszy typowe wdrożenie:

- [Tworzenie wdrożenia przejściowego](#staging)
- [Przenoszenie istniejących funkcji do ciągłego wdrażania](#existing)

<a name="staging"></a>
### <a name="create-a-staging-deployment"></a>Tworzenie wdrożenia przejściowego

Aplikacje funkcji jeszcze nie obsługuje miejsc wdrożenia. Można jednak nadal zarządzać oddzielnych wdrożeń środowisk przejściowych i produkcyjnych przy użyciu ciągłej integracji.

Proces konfigurowania i pracować z wdrażania przejściowego ogólnie wyglądają następująco:

1. Utworzyć dwie aplikacje funkcji w subskrypcji: jeden dla kodu produkcyjnego, a drugi dla przemieszczania. 

2. Utwórz źródło wdrożenia, jeśli nie masz jeszcze jeden. W tym przykładzie użyto [GitHub].

3. Dla aplikacji funkcji produkcji wykonaj poprzednie kroki w **Konfigurowanie wdrażania ciągłego** i ustaw gałąź, wdrożenia do głównej gałęzi repozytorium GitHub.
   
    ![Wybierz gałąź wdrożenia](./media/functions-continuous-deployment/choose-deployment-branch.png)

4. Powtórz ten krok dla tymczasowej aplikacji funkcji, ale zamiast tego wybierz tymczasową gałęzi w repozytorium GitHub. Jeśli źródła wdrożenia nie obsługuje gałęzi, należy użyć innego folderu.
    
5. Aktualizowanie kodu w gałęzi lub folder przemieszczania, a następnie sprawdź, czy te zmiany zostaną uwzględnione podczas wdrażania przejściowego.

6. Po zakończeniu testowania scalanie zmian z przemieszczania gałęzi z gałęzią główną. To scalanie wyzwala wdrożenia do aplikacji funkcji w środowisku produkcyjnym. Jeśli źródła wdrożenia nie obsługuje gałęzie, należy zastąpić pliki w folderze produkcji pliki z folderu przemieszczania.

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>Przenoszenie istniejących funkcji do ciągłego wdrażania
Jeśli masz istniejące funkcje, które zostały utworzone i obsługiwane w portalu, należy pobrać istniejących plików kodu funkcji przy użyciu protokołu FTP i lokalnego repozytorium Git, zanim można skonfigurować ciągłe wdrażanie, zgodnie z powyższym opisem. Można to zrobić w ustawieniach usługi App Service dla aplikacji funkcji. Po pobraniu plików, można przekazać je do źródła wybrany ciągłego wdrażania.

> [!NOTE]
> Po skonfigurowaniu ciągłej integracji, nie będzie można edytować plików źródłowych w portalu usługi Functions.

- [Instrukcje: Skonfiguruj poświadczenia wdrożenia](#credentials)
- [Instrukcje: Pobieranie plików przy użyciu protokołu FTP](#downftp)
- [Instrukcje: Pobieranie plików przy użyciu lokalnego repozytorium Git](#downgit)

<a name="credentials"></a>
#### <a name="how-to-configure-deployment-credentials"></a>Instrukcje: Skonfiguruj poświadczenia wdrożenia
Przed pobraniem plików z aplikacji funkcji przy użyciu protokołu FTP i lokalnego repozytorium Git, należy skonfigurować swoje poświadczenia, aby przejść do witryny. Poświadczenia są ustawiane na poziomie aplikacji funkcji. Aby ustawić poświadczenia wdrażania w witrynie Azure portal, wykonaj następujące kroki:

1. W aplikacji funkcji w [witryny Azure portal](https://portal.azure.com), kliknij przycisk **funkcje platformy** i **poświadczenia wdrożenia**.
   
    ![Ustaw poświadczenia wdrażania lokalnego](./media/functions-continuous-deployment/setup-deployment-credentials.png)

2. Wpisz nazwę użytkownika i hasło, a następnie kliknij przycisk **Zapisz**. Te poświadczenia można teraz używać dostęp do aplikacji funkcji z FTP lub wbudowane repozytorium Git.

<a name="downftp"></a>
#### <a name="how-to-download-files-using-ftp"></a>Instrukcje: Pobieranie plików przy użyciu protokołu FTP

1. W aplikacji funkcji w [witryny Azure portal](https://portal.azure.com), kliknij przycisk **funkcje platformy** i **właściwości**, następnie skopiuj wartości pól **użytkownika serwera FTP/wdrożenia**, **Nazwa hosta FTP**, i **nazwa hosta FTPS**.  

    **Użytkownik serwera FTP/wdrożenia** należy wprowadzić w postaci wyświetlanej w portalu, łącznie z nazwą aplikacji w celu zapewnienia prawidłowego kontekstu serwera FTP.
   
    ![Informacje o wdrożeniu](./media/functions-continuous-deployment/get-deployment-credentials.png)

2. Z poziomu klienta FTP należy użyć informacji o połączeniu zebrane do nawiązywania połączeń z aplikacją i pobrania plików źródłowych dla funkcji.

<a name="downgit"></a>
#### <a name="how-to-download-files-using-a-local-git-repository"></a>Instrukcje: Pobieranie plików przy użyciu lokalnego repozytorium Git

1. W aplikacji funkcji w [witryny Azure portal](https://portal.azure.com), kliknij przycisk **funkcje platformy** i **opcje wdrażania**. 
   
    ![Konfigurowanie ciągłego wdrażania](./media/functions-continuous-deployment/setup-deployment.png)
 
2. Następnie w **wdrożeń** bloku kliknij pozycję **Instalatora**.
 
    ![Konfigurowanie ciągłego wdrażania](./media/functions-continuous-deployment/setup-deployment-1.png)
   
2. W **źródło wdrożenia** bloku kliknij **lokalnym repozytorium Git** a następnie kliknij przycisk **OK**.

3. W **funkcje platformy**, kliknij przycisk **właściwości** i zanotuj wartość adresu URL usługi Git. 
   
    ![Konfigurowanie ciągłego wdrażania](./media/functions-continuous-deployment/get-local-git-deployment-url.png)

4. Sklonuj repozytorium na komputerze lokalnym przy użyciu wiersza polecenia Git-aware lub ulubionego narzędzia Git. Git polecenie klonowania może wyglądać następująco:
   
        git clone https://username@my-function-app.scm.azurewebsites.net:443/my-function-app.git

5. Pobieranie plików z aplikacji funkcji do klonu komputera lokalnego, jak w poniższym przykładzie:
   
        git pull origin master
   
    Jeśli jest to wymagane, podaj swoje [skonfigurowane poświadczenia wdrożenia](#credentials).  

[GitHub]: https://github.com/

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)

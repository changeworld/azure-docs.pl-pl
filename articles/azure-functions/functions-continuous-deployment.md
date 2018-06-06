---
title: Ciągłe wdrażanie dla usługi Azure Functions | Dokumentacja firmy Microsoft
description: Publikowanie funkcji platformy Azure przy użyciu urządzenia ciągłego wdrażania usługi Azure App Service.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/25/2016
ms.author: glenga
ms.openlocfilehash: db10cd957f4dc59f787e2ac625355a96c888356e
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34735707"
---
# <a name="continuous-deployment-for-azure-functions"></a>Ciągłe wdrażanie dla usługi Azure Functions
Środowisko Azure Functions ułatwia wdrażanie aplikacji funkcji przy użyciu ciągłej integracji usługi aplikacji. Funkcje integruje się z BitBucket, Dropbox, GitHub i Visual Studio Team Services (VSTS). Dzięki temu przepływu pracy gdzie kod funkcji aktualizacji przy użyciu jednej z tych wdrażanie wyzwalaczy zintegrowanych usług Azure. Jeśli jesteś nowym użytkownikiem usługi Azure Functions, Rozpocznij od [Azure Functions — omówienie](functions-overview.md).

Ciągłe wdrażanie to doskonałe rozwiązanie dla projektów, w których ma miejsce częste współtworzenie wielu treści. Umożliwia on również Obsługa kontroli źródła na kodzie funkcji. Obecnie obsługiwane są następujące źródła wdrożenia:

* [Bitbucket](https://bitbucket.org/)
* [Skrzynki](https://www.dropbox.com/)
* Repozytorium zewnętrznego (Git lub Mercurial)
* [Lokalne repozytorium Git](../app-service/app-service-deploy-local-git.md)
* [GitHub](https://github.com)
* [Usługi OneDrive](https://onedrive.live.com/)
* [Visual Studio Team Services](https://www.visualstudio.com/team-services/)

Wdrożenia są konfigurowane na poszczególnych funkcji aplikacji. Po włączeniu ciągłego wdrażania dostępu do kodu funkcji w portalu jest równa *tylko do odczytu*.

## <a name="continuous-deployment-requirements"></a>Wymagania dotyczące ciągłe wdrażanie

W źródle wdrożenia przed skonfigurowaniem ciągłe wdrażanie musi mieć źródło wdrożenia skonfigurowane i kodu funkcji. We wdrożeniu aplikacji daną funkcję każdej funkcji znajduje się w podkatalogu o nazwie, gdzie nazwa katalogu jest nazwa funkcji.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Aby można było wdrożyć z programu VSTS, możesz połączyć konta usługi VSTS z subskrypcją platformy Azure. Aby uzyskać więcej informacji, zobacz [Konfigurowanie rozliczeń dla swojego konta usługi VSTS](https://docs.microsoft.com/vsts/billing/set-up-billing-for-your-account-vs?view=vsts#set-up-billing-via-the-azure-portal).

## <a name="set-up-continuous-deployment"></a>Konfigurowanie ciągłego wdrażania
Użyj tej procedury, aby skonfigurować ciągłego wdrażania dla istniejącej aplikacji funkcji. Te kroki prezentują integracji z repozytorium GitHub, ale podobne kroki zastosować Visual Studio Team Services lub innych usługach wdrożenia.

1. W swoją aplikację przy użyciu funkcji [portalu Azure](https://portal.azure.com), kliknij przycisk **funkcji platformy** i **opcje wdrażania**. 
   
    ![Instalator ciągłe wdrażanie](./media/functions-continuous-deployment/setup-deployment.png)
 
2. Następnie w **wdrożeń** kliknij bloku **Instalatora**.
 
    ![Instalator ciągłe wdrażanie](./media/functions-continuous-deployment/setup-deployment-1.png)
   
2. W **źródło wdrożenia** bloku, kliknij przycisk **wybierz źródło**, następnie wypełnij informacje dotyczące źródła wybranego wdrożenia i kliknij przycisk **OK**.
   
    ![Wybierz źródło wdrożenia](./media/functions-continuous-deployment/choose-deployment-source.png)

Po skonfigurowaniu ciągłego wdrażania, wszystkie zmiany w pliku w źródle wdrożenia są kopiowane do aplikacji funkcji i wdrażania witrynę w trybie pełnym zostanie wywołany. Witryny jest wdrożone podczas aktualizacji plików w źródle.

## <a name="deployment-options"></a>Opcje wdrażania

Poniżej przedstawiono niektóre typowe wdrożeń:

- [Tworzenie tymczasowych wdrożenia](#staging)
- [Przenieś istniejących funkcji ciągłego wdrażania](#existing)

<a name="staging"></a>
### <a name="create-a-staging-deployment"></a>Tworzenie tymczasowych wdrożenia

Funkcja aplikacji jeszcze nie obsługuje miejsc wdrożenia. Można jednak nadal zarządzać oddzielnych wdrożeń tymczasowych i produkcyjnych przy użyciu ciągłej integracji.

Proces konfigurowania i pracować z wdrażania przejściowego wygląda zazwyczaj następująco:

1. Utwórz dwie aplikacje funkcji w subskrypcji, jeden dla kodu produkcyjnego i jeden dla przemieszczania. 

2. Utwórz źródło wdrożenia, jeśli nie został wcześniej. W tym przykładzie użyto [GitHub].

3. Dla aplikacji funkcja produkcji, pełną kroki **skonfigurowano ciągłe wdrażanie** i ustaw gałąź wdrożenia do głównej gałęzi repozytorium GitHub.
   
    ![Wybierz gałąź wdrożenia](./media/functions-continuous-deployment/choose-deployment-branch.png)

4. Powtórz ten krok dla tymczasowej aplikacjami funkcji, ale wybierz tymczasowej gałęzi zamiast w Twojego repozytorium GitHub. Jeśli źródło wdrożenia nie obsługuje tworzenia rozgałęzień, należy użyć innego folderu.
    
5. Aktualizowanie kodu tymczasowej gałęzi lub folder, a następnie sprawdź, czy te zmiany zostaną odzwierciedlone w tymczasowej wdrożenia.

6. Po zakończeniu testowania, Scal zmiany w tymczasowej gałęzi głównej gałęzi. Tę operację scalania wyzwala wdrożenia do produkcji funkcji aplikacji. Jeśli źródło wdrożenia nie obsługuje gałęzie, Zastąp pliki w folderze produkcji pliki z folderu przemieszczania.

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>Przenieś istniejących funkcji ciągłego wdrażania
Jeśli masz istniejące funkcje, które ma być tworzone i obsługiwane w portalu, należy pobrać istniejących plików kodu funkcji za pomocą protokołu FTP lub lokalnego repozytorium Git przed można skonfigurować ciągłego wdrażania, zgodnie z powyższym opisem. Można to zrobić w ustawieniach usługi aplikacji — dla aplikacji funkcja. Po pobraniu plików, może przekazywać je do źródła wybrany ciągłego wdrażania.

> [!NOTE]
> Po skonfigurowaniu integracji ciągłej już będzie możliwości edytowania plików źródłowych w portalu funkcji.

- [Porady: Konfigurowanie poświadczeń wdrożenia](#credentials)
- [Porady: pobieranie plików przy użyciu FTP](#downftp)
- [Porady: pobieranie plików przy użyciu lokalnego repozytorium Git](#downgit)

<a name="credentials"></a>
#### <a name="how-to-configure-deployment-credentials"></a>Porady: Konfigurowanie poświadczeń wdrożenia
Przed pobraniem plików z aplikacji funkcji za pomocą protokołu FTP lub lokalnego repozytorium Git, należy skonfigurować poświadczenia dostępu do witryny. Poświadczenia są ustawione na poziomie aplikacji funkcji. Aby ustawić poświadczenia wdrażania w portalu Azure, wykonaj następujące kroki:

1. W swoją aplikację przy użyciu funkcji [portalu Azure](https://portal.azure.com), kliknij przycisk **funkcji platformy** i **poświadczenia wdrażania**.
   
    ![Konfigurowanie poświadczeń wdrożenia lokalnego](./media/functions-continuous-deployment/setup-deployment-credentials.png)

2. Wpisz nazwę użytkownika i hasło, a następnie kliknij przycisk **zapisać**. Te poświadczenia można teraz używać dostęp do aplikacji funkcji z FTP lub wbudowanych repozytorium Git.

<a name="downftp"></a>
#### <a name="how-to-download-files-using-ftp"></a>Porady: pobieranie plików przy użyciu FTP

1. W swoją aplikację przy użyciu funkcji [portalu Azure](https://portal.azure.com), kliknij przycisk **funkcji platformy** i **właściwości**, następnie skopiować wartości **użytkownika serwera FTP/wdrożenia**, **nazwa hosta FTP**, i **nazwy hosta FTPS**.  

    **Użytkownika serwera FTP/wdrożenia** należy podać wyświetlaną w portalu, w tym nazwę aplikacji w celu zapewnienia prawidłowego kontekstu dla serwera FTP.
   
    ![Uzyskaj informacje wdrożenia](./media/functions-continuous-deployment/get-deployment-credentials.png)

2. Z tego klienta FTP należy użyć informacji o połączeniu zebranych Aby podłączyć się do aplikacji i Pobierz pliki źródłowe dla funkcji.

<a name="downgit"></a>
#### <a name="how-to-download-files-using-a-local-git-repository"></a>Porady: pobieranie plików przy użyciu lokalnego repozytorium Git

1. W swoją aplikację przy użyciu funkcji [portalu Azure](https://portal.azure.com), kliknij przycisk **funkcji platformy** i **opcje wdrażania**. 
   
    ![Instalator ciągłe wdrażanie](./media/functions-continuous-deployment/setup-deployment.png)
 
2. Następnie w **wdrożeń** kliknij bloku **Instalatora**.
 
    ![Instalator ciągłe wdrażanie](./media/functions-continuous-deployment/setup-deployment-1.png)
   
2. W **źródło wdrożenia** bloku, kliknij przycisk **repozytorium Git lokalnego** , a następnie kliknij przycisk **OK**.

3. W **funkcji platformy**, kliknij przycisk **właściwości** i zanotuj wartość adresu URL Git. 
   
    ![Instalator ciągłe wdrażanie](./media/functions-continuous-deployment/get-local-git-deployment-url.png)

4. Klonuj repozytorium na komputerze lokalnym przy użyciu wiersza polecenia programu Git-aware lub ulubionego narzędzia Git. Polecenie klonowania Git wygląda następująco:
   
        git clone https://username@my-function-app.scm.azurewebsites.net:443/my-function-app.git

5. Pobieranie plików z aplikacji funkcja klonu komputera lokalnego, jak w poniższym przykładzie:
   
        git pull origin master
   
    Jeśli jest to wymagane, podaj użytkownika [skonfigurowane poświadczenia wdrażania](#credentials).  

[GitHub]: https://github.com/

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)

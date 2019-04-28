---
title: Wdrażanie zawartości przy użyciu protokołu FTP/S — usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć aplikację w usłudze Azure App Service przy użyciu protokołu FTP i FTPS.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2018
ms.author: cephalin;dariac
ms.custom: seodec18
ms.openlocfilehash: db8445ec2b3dd8bdefa661d7f186e720c6fada09
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130113"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Wdrażanie aplikacji w usłudze Azure App Service przy użyciu protokołu FTP/S

W tym artykule dowiesz się, jak wdrażanie aplikacji sieci web, zaplecza aplikacji mobilnej lub aplikacji interfejsu API za pomocą protokołu FTP i FTPS [usługi Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714).

Punkt końcowy protokołu FTP/S dla aplikacji jest już aktywny. Konfiguracja nie jest niezbędne do obsługi wdrożenia protokołu FTP/S.

## <a name="open-ftp-dashboard"></a>Otwórz pulpit nawigacyjny FTP

W [witryny Azure portal](https://portal.azure.com), otwórz aplikacji [strony zasobu](../azure-resource-manager/manage-resources-portal.md#manage-resources).

Aby otworzyć pulpit nawigacyjny FTP, kliknij przycisk **Centrum wdrażania** > **FTP** > **pulpit nawigacyjny**.

![Otwórz pulpit nawigacyjny FTP](./media/app-service-deploy-ftp/open-dashboard.png)

## <a name="get-ftp-connection-information"></a>Pobieranie informacji o połączeniu FTP

Na pulpicie nawigacyjnym FTP kliknij **kopiowania** do skopiowania FTPS poświadczenia punktu końcowego i aplikacji.

![Skopiuj informacje protokołu FTP](./media/app-service-deploy-ftp/ftp-dashboard.png)

Zaleca się, że używasz **poświadczenia aplikacji** można wdrożyć do swojej aplikacji, ponieważ jest on unikatowy dla każdej aplikacji. Jednak jeśli klikniesz **poświadczenia użytkownika**, można ustawić poświadczenia na poziomie użytkownika, służące do logowania protokołu FTP/S do wszystkich aplikacji usługi App Service w ramach subskrypcji.

> [!NOTE]
> Uwierzytelnianie FTP/FTPS punkt końcowy przy użyciu requirers poświadczenia na poziomie użytkownika o nazwę użytkownika w następującym formacie: 
>
>`<app-name>\<user-name>`
>
> Ponieważ poświadczenia na poziomie użytkownika są połączone z kontem użytkownika i określonego zasobu, nazwa użytkownika musi być w następującym formacie, aby skierować działania logowania do endpoint odpowiedniej aplikacji.
>

## <a name="deploy-files-to-azure"></a>Wdrażanie plików na platformie Azure

1. Z poziomu klienta FTP (na przykład [programu Visual Studio](https://www.visualstudio.com/vs/community/), [Cyberduck](https://cyberduck.io/), lub [WinSCP](https://winscp.net/index.php)), informacji o połączeniu zebrane nawiązać połączenia z aplikacją.
2. Skopiuj pliki i odpowiednią strukturę katalogów do [ **/site/wwwroot** katalogu](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) na platformie Azure (lub **/site/wwwroot/App_Data/zadania/** katalog dla zadań Webjob).
3. Przejdź do adresu URL aplikacji, aby sprawdzić, czy aplikacja działa prawidłowo. 

> [!NOTE] 
> W odróżnieniu od [wdrożenia oparte na usłudze Git](deploy-local-git.md), wdrożenia protokołu FTP nie obsługuje następujących automatyzacji wdrażania: 
>
> - Przywracanie zależności (np. NuGet, NPM, narzędzie PIP i Composer automatyzacji)
> - Kompilacja pliki binarne .NET
> - Generowanie pliku Web.config (w tym miejscu jest [przykładu środowiska Node.js](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Generowanie tych niezbędne pliki ręcznie na komputerze lokalnym, a następnie wdrożyć je razem z aplikacją.
>

## <a name="enforce-ftps"></a>Wymuszanie protokołu FTPS

Aby zwiększyć bezpieczeństwo należy zezwalać FTP za pośrednictwem protokołu SSL tylko. Jeśli nie korzystasz z serwera FTP wdrożenia, można wyłączyć protokołu FTP i FTPS.

Na stronie zasobów aplikacji w [witryny Azure portal](https://portal.azure.com), wybierz opcję **ustawienia aplikacji** w nawigacji po lewej stronie.

Aby wyłączyć nieszyfrowanego protokołu FTP, wybierz **FTPS tylko**. Aby całkowicie wyłączyć FTP i FTPS, wybierz **wyłączyć**. Po skończeniu kliknij przycisk **Zapisz**. Jeśli przy użyciu **FTPS tylko** muszą wymuszać protokół TLS 1.2 lub nowszej, przechodząc do **ustawienia protokołu SSL** bloku aplikacji sieci web. Protokół TLS 1.0 i 1.1, nie są obsługiwane za pomocą **FTPS tylko**.

![Wyłączanie protokołu FTP/S](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="automate-with-scripts"></a>Automatyzowanie przy użyciu skryptów

Dla protokołu FTP wdrożeniu przy użyciu [wiersza polecenia platformy Azure](/cli/azure), zobacz [tworzenie aplikacji internetowej i wdrażanie plików za pomocą protokołu FTP (Azure CLI)](./scripts/cli-deploy-ftp.md).

Dla protokołu FTP wdrożeniu przy użyciu [programu Azure PowerShell](/cli/azure), zobacz [przekazywanie plików do aplikacji sieci web przy użyciu protokołu FTP (PowerShell)](./scripts/powershell-deploy-ftp.md).

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>Rozwiązywanie problemów z wdrożenia protokołu FTP

- [Jak można rozwiązywać wdrożenia protokołu FTP?](#how-can-i-troubleshoot-ftp-deployment)
- [I'm not able to FTP and publish my code. Jak można rozwiązać ten problem?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Jak można podłączyć do usługi FTP w usłudze Azure App Service za pośrednictwem tryb pasywny?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Jak można rozwiązywać wdrożenia protokołu FTP?

Pierwszym krokiem Rozwiązywanie problemów z wdrażaniem FTP jest izolowanie problem z wdrożeniem z problemu z aplikacją środowiska uruchomieniowego.

Problem z wdrożeniem zwykle powoduje nie plików lub nieprawidłowy wdrożony z aplikacją. Można rozwiązać, badanie wdrożenia protokołu FTP lub wybierając ścieżka alternatywnego wdrożenia (np. kontrolę źródła).

Problem dotyczy środowiska uruchomieniowego aplikacji zwykle powoduje właściwy zestaw plików wdrożona aplikacja, ale zachowanie nieprawidłowe aplikacji. Można rozwiązać, skupiając się na zachowanie kodu w czasie wykonywania i badanie awaria określonej ścieżki.

Aby ustalić problem wdrożenia lub w czasie wykonywania, zobacz [wdrożenia, a problemy w czasie wykonywania](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues).

### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>Nie jestem w stanie FTP i Publikuj mój kod. Jak można rozwiązać ten problem?
Sprawdź, czy wprowadzono poprawną nazwę hosta i [poświadczenia](#open-ftp-dashboard). Należy także sprawdzić, czy następujące porty FTP na tym komputerze nie są blokowane przez zaporę:

- Port połączenia sterowania FTP: 21
- Port połączenia danych FTP: 989, 10001-10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Jak można podłączyć do usługi FTP w usłudze Azure App Service za pośrednictwem tryb pasywny?
Usługa Azure App Service obsługuje nawiązywania połączenia za pośrednictwem tryb zarówno aktywnych i pasywnych. W trybie pasywnym jest preferowane, ponieważ Twoje wdrożenia. maszyny wirtualne są zazwyczaj zaporą (w systemie operacyjnym lub jako część głównej lub w sieci firmowej). Zobacz [przykładu z dokumentacji WinSCP](https://winscp.net/docs/ui_login_connection). 

## <a name="next-steps"></a>Kolejne kroki

Dla bardziej zaawansowanych scenariuszy wdrażania, spróbuj [wdrażania na platformie Azure przy użyciu narzędzia Git](deploy-local-git.md). Wdrożenie oparte na usłudze Git na platformie Azure umożliwia kontrolę wersji, Przywracanie pakietu, program MSBuild i.

## <a name="more-resources"></a>Więcej zasobów

* [Poświadczenia wdrożenia usługi Azure App Service](deploy-configure-credentials.md)

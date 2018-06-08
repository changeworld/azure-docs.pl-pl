---
title: Wdrażanie aplikacji w usłudze Azure App Service przy użyciu FTP/S | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć aplikację w usłudze Azure App Service przy użyciu FTP i FTPS.
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
ms.date: 06/05/2018
ms.author: cephalin;dariac
ms.openlocfilehash: 7e05e06a5abd02dd67f58a8e01bb246e318f51de
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850240"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Wdrażanie aplikacji w usłudze Azure App Service przy użyciu FTP/S

W tym artykule przedstawiono sposób użycia FTP i FTPS, aby wdrożyć aplikację sieci web, zaplecza aplikacji mobilnej lub aplikacji interfejsu API [usłudze Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714).

Punkt końcowy FTP/S dla aplikacji jest już aktywna. Konfiguracja nie jest niezbędne do obsługi wdrożenia FTP/S.

## <a name="open-ftp-dashboard"></a>Otwórz pulpit nawigacyjny FTP

W [portalu Azure](https://portal.azure.com), otwórz aplikacji [zasobu strony](../azure-resource-manager/resource-group-portal.md#manage-resources).

Aby otworzyć pulpit nawigacyjny FTP, kliknij przycisk **ciągłego dostarczania (wersja zapoznawcza)** > **FTP** > **pulpitu nawigacyjnego**.

![Otwórz pulpit nawigacyjny FTP](./media/app-service-deploy-ftp/open-dashboard.png)

## <a name="get-ftp-connection-information"></a>Uzyskiwanie informacji o połączeniu FTP

Na pulpicie nawigacyjnym FTP, kliknij przycisk **kopiowania** do skopiowania FTPS poświadczenia punktu końcowego i aplikacji.

![Kopiowanie informacji FTP](./media/app-service-deploy-ftp/ftp-dashboard.png)

Zaleca się, że używasz **poświadczeń aplikacji** można wdrożyć do aplikacji, ponieważ jest on unikatowy dla każdej aplikacji. Jednak jeśli klikniesz przycisk **poświadczenia użytkownika**, można ustawić poświadczeń na poziomie użytkownika, które służy do logowania FTP/S do wszystkich aplikacji usługi aplikacji w ramach subskrypcji.

## <a name="deploy-files-to-azure"></a>Wdrażanie plików na platformie Azure

1. Z tego klienta FTP (na przykład [programu Visual Studio](https://www.visualstudio.com/vs/community/) lub [FileZilla](https://filezilla-project.org/download.php?type=client)), Użyj zebranych do nawiązania połączenia aplikacji informacje o połączeniu.
3. Kopiowanie plików i ich struktury katalogów odpowiednich do [ **/lokacji/wwwroot** katalogu](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) na platformie Azure (lub **/lokacji/wwwroot/App_Data/zadania/** katalogu dla zadań Webjob).
4. Przejdź do adresu URL aplikacji, aby sprawdzić, czy aplikacja działa prawidłowo. 

> [!NOTE] 
> W odróżnieniu od [wdrożenia oparte na Git](app-service-deploy-local-git.md), wdrożenie FTP nie obsługuje następujących automatyzacji wdrażania: 
>
> - Przywraca zależności (na przykład automatyzacji NuGet, NPM, PIP i kompozytor)
> - Kompilacja plików binarnych .NET
> - Generowanie pliku Web.config (w tym miejscu jest [przykład Node.js](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Generowanie te niezbędne pliki ręcznie na komputerze lokalnym, a następnie wdrożyć je razem z aplikacji.
>
>

## <a name="enforce-ftps"></a>Wymuszanie FTPS

Aby zwiększyć bezpieczeństwo należy zezwalać FTP przy użyciu protokołu SSL tylko. Jeśli nie używasz serwera FTP wdrożenia można również wyłączyć zarówno FTP i FTPS.

Na stronie zasobów aplikacji w programie [portalu Azure](https://portal.azure.com), wybierz pozycję **ustawień aplikacji** na lewym pasku nawigacyjnym.

Aby wyłączyć niezaszyfrowane FTP, wybierz pozycję **FTPS tylko**. Aby całkowicie wyłączyć zarówno FTP i FTPS, wybierz pozycję **wyłączyć**. Gdy skończysz, kliknij przycisk **zapisać**.

![Wyłącz FTP/S](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="automate-with-scripts"></a>Automatyzowanie przy użyciu skryptów

Do wdrożenia przy użyciu FTP [interfejsu wiersza polecenia Azure](/cli/azure), zobacz [tworzenie aplikacji sieci web i wdrażanie plików przy użyciu FTP (Azure CLI)](./scripts/app-service-cli-deploy-ftp.md).

Do wdrożenia przy użyciu FTP [programu Azure PowerShell](/cli/azure), zobacz [przekazać pliki do aplikacji sieci web za pomocą protokołu FTP (PowerShell)](./scripts/app-service-powershell-deploy-ftp.md).

## <a name="troubleshoot-ftp-deployment"></a>Rozwiązywanie problemów z wdrażaniem FTP

- [Jak rozwiązywać problemy z serwera FTP wdrożenia?](#how-can-i-troubleshoot-ftp-deployment)
- [I jest niemożliwe FTP i Publikuj kod. Jak można rozwiązać ten problem?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Jak można podłączyć do usługi FTP w usłudze Azure App Service za pośrednictwem w trybie pasywnym?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Jak rozwiązywać problemy z serwera FTP wdrożenia?

Pierwszym krokiem Rozwiązywanie problemów z wdrażaniem FTP jest izolowanie problem wdrażania, problem aplikacji środowiska wykonawczego.

Problem wdrażania zazwyczaj wynikiem nie plików lub nieprawidłowe pliki wdrożonych aplikacji. Można rozwiązać, badanie wdrożenia FTP lub wybierając ścieżki alternatywnej rozmieszczania (na przykład kontroli źródła).

Problem aplikacji środowiska wykonawczego zazwyczaj wynikiem prawidłowego zestawu plików wdrożonych aplikacji, ale zachowanie aplikacji niepoprawne. Można rozwiązać przez koncentrujących się na zachowanie kodu w czasie wykonywania i badania ścieżki określony błąd.

Aby określić problem wdrożenia lub w czasie wykonywania, zobacz [wdrożenia, a problemy środowiska uruchomieniowego](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues).

### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>I jest niemożliwe FTP i Publikuj kod. Jak można rozwiązać ten problem?
Sprawdź, czy wprowadzono poprawną nazwę hosta i [poświadczenia](#step-1--set-deployment-credentials). Należy także sprawdzić, czy następujące porty FTP na tym komputerze nie są blokowane przez zaporę:

- Port połączenia sterowania FTP: 21
- Port połączenia danych FTP: 989, 10001 10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Jak można podłączyć do usługi FTP w usłudze Azure App Service za pośrednictwem w trybie pasywnym?
Usługa aplikacji Azure obsługuje nawiązywaniu połączeń za pośrednictwem trybu zarówno aktywnych i pasywnych. W trybie pasywnym jest preferowana, ponieważ maszynach wdrożenia są zwykle za zaporą (w systemie operacyjnym lub jako część pakietu domu lub sieci firmowej). Zobacz [przykład dokumentacji WinSCP](https://winscp.net/docs/ui_login_connection). 

## <a name="next-steps"></a>Kolejne kroki

W przypadku bardziej zaawansowanych scenariuszy wdrażania, spróbuj [wdrażanie na platformie Azure za pomocą narzędzia Git](app-service-deploy-local-git.md). Wdrożenie oparte na Git na platformie Azure umożliwia kontroli wersji, Przywracanie pakietu MSBuild i więcej.

## <a name="more-resources"></a>Więcej zasobów

* [Poświadczenia wdrożenia usługi aplikacji Azure](app-service-deploy-ftp.md)

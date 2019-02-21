---
title: Konfigurowanie źródeł wdrożenia dla usług aplikacji w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Jak administrator usługi można skonfigurować źródła wdrożenia (Git, GitHub, BitBucket, DropBox i OneDrive) dla usług aplikacji w usłudze Azure Stack
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: anwestg
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: f2101c685ff7b3820f826da1d2e1d52b687d26c6
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2019
ms.locfileid: "56446635"
---
# <a name="configure-deployment-sources"></a>Konfigurowanie źródeł wdrożenia

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Usługa App Service w usłudze Azure Stack obsługuje wdrożenia na żądanie od wielu dostawców kontroli źródła. Ta funkcja pozwala deweloperom aplikacji, wdrażanie funkcji bezpośrednie ich repozytoriów kontroli źródła. Jeśli użytkownik chce skonfigurować App Service, aby nawiązać połączenie z repozytoriach, operator chmury najpierw należy skonfigurować integrację między usługą App Service w usłudze Azure Stack i dostawca kontroli źródła.  

Oprócz lokalnego narzędzia Git obsługiwane są następujące dostawców kontroli źródła:

* GitHub
* BitBucket
* OneDrive
* DropBox

## <a name="view-deployment-sources-in-app-service-administration"></a>Widok źródła wdrożenia w witrynie Administracja usługi App Service

1. Zaloguj się do portalu administracyjnego usługi Azure Stack (https://adminportal.local.azurestack.external) jako administratora usługi.
2. Przejdź do **wszystkich usług** i wybierz **usługi App Service**.
    ![Administrator dostawcy zasobów usługi App Service][1]
3. Kliknij przycisk **konfiguracji kontroli źródła**. Można wyświetlić listę wszystkich źródeł skonfigurowanym wdrożenia.
    ![Konfiguracji kontroli źródła administrator dostawcy zasobów usługi App Service][2]

## <a name="configure-github"></a>Konfigurowanie usługi GitHub

Musi mieć konto usługi GitHub, aby zakończyć to zadanie. Można użyć konta organizacji, a nie konta osobistego.

1. Zaloguj się do usługi GitHub, przejdź do https://www.github.com/settings/developers, a następnie kliknij przycisk **zarejestrowania nowej aplikacji**.
    ![GitHub - rejestrowanie nowej aplikacji][3]
2. Wprowadź **Nazwa aplikacji**, na przykład **usługi App Service w usłudze Azure Stack**.
3. Wprowadź **adres URL strony głównej**. Adres URL strony głównej musi być adresem portalu usługi Azure Stack. Na przykład https://portal.local.azurestack.external.
4. Wprowadź **opis aplikacji**.
5. Wprowadź **adresów URL wywołania zwrotnego autoryzacji**. W przypadku wdrożenia usługi Azure Stack domyślny adres URL jest w formie https://portal.local.azurestack.external/TokenAuthorize. Jeśli pracujesz w innej domenie, należy zastąpić nazwę domeny local.azurestack.external.
6. Kliknij przycisk **zarejestrować aplikację**. Strona jest wyświetlana lista **identyfikator klienta** i **klucz tajny klienta** dla aplikacji.
    ![GitHub — rejestracja ukończona aplikacja][5]
7.  W nowej karcie przeglądarki lub w oknie Zaloguj się do portalu administracyjnego usługi Azure Stack (https://adminportal.local.azurestack.external) jako administratora usługi.
8.  Przejdź do **dostawców zasobów**i wybierz **administrator dostawcy zasobów usługi App**.
9. Kliknij przycisk **konfiguracji kontroli źródła**.
10. Skopiuj i Wklej **identyfikator klienta** i **klucz tajny klienta** do odpowiednich danych wejściowych pola usługi github.
11. Kliknij pozycję **Zapisz**.

## <a name="configure-bitbucket"></a>Konfigurowanie BitBucket

Musi mieć konto BitBucket, aby zakończyć to zadanie. Można użyć konta organizacji, a nie konta osobistego.

1. Zaloguj się w usłudze BitBucket, a następnie przejdź do **integracje** w ramach konta usługi.
    ![Pulpit nawigacyjny BitBucket — integracji][7]
2. Kliknij przycisk **OAuth** w obszarze zarządzania dostępem i **Dodaj odbiorców**.
    ![BitBucket Dodaj klientów uwierzytelniania OAuth][8]
3. Wprowadź **nazwa** dla użytkownika; na przykład **usługi App Service w usłudze Azure Stack**.
4. Wprowadź **opis** dla aplikacji.
5. Wprowadź **adresów URL wywołania zwrotnego**. W domyślnym wdrożeniu usługi Azure Stack wywołania zwrotnego adresu URL znajduje się w formularzu https://portal.local.azurestack.external/TokenAuthorize. Jeśli pracujesz w innej domenie, należy zastąpić nazwę domeny azurestack.local. Integracja BitBucket zakończyło się sukcesem adres URL muszą wykonać wielkość liter, wymienione w tym miejscu.
6. Wprowadź **adresu URL**. Ten adres URL powinien być portalu usługi Azure Stack URL; na przykład https://portal.local.azurestack.external.
7. Wybierz **uprawnienia** wymagane:
    - **Repozytoria**: *Odczyt*
    - **Elementy Webhook**: *Odczyt i zapis*
8. Kliknij pozycję **Zapisz**. Pojawi się nowa aplikacja, wraz z **klucz** i **klucz tajny**w obszarze **konsumentów OAuth**.
    ![Lista aplikacji BitBucket][9]
9.  W nowej karcie przeglądarki lub w oknie Zaloguj się do portalu administracyjnego usługi Azure Stack (https://adminportal.local.azurestack.external) jako administratora usługi.
10.  Przejdź do **dostawców zasobów** i wybierz **administrator dostawcy zasobów usługi App**.
11. Kliknij przycisk **konfiguracji kontroli źródła**.
12. Skopiuj i Wklej **klucz** do **identyfikator klienta** pola wejściowego i **klucz tajny** do **klucz tajny klienta** pole wejściowe dla BitBucket.
13. Kliknij pozycję **Zapisz**.

## <a name="configure-onedrive"></a>Konfigurowanie usługi OneDrive

Musi mieć Account Microsoft połączony z kontem usługi OneDrive, aby zakończyć to zadanie.  Można użyć konta organizacji, a nie konta osobistego.

> [!NOTE]
> W usłudze OneDrive dla firm konta nie są obecnie obsługiwane.

1. Przejdź do https://apps.dev.microsoft.com/?referrer=https%3A%2F%2Fdev.onedrive.com%2Fapp-registration.htm i zaloguj się przy użyciu Account firmy Microsoft.
2. W obszarze **aplikacje**, kliknij przycisk **Dodaj aplikację**.
![Aplikacje w usłudze OneDrive][10]
3. Wprowadź **nazwa** dla nowej rejestracji aplikacji: wprowadź **usługi App Service w usłudze Azure Stack**, a następnie kliknij przycisk **tworzenia aplikacji**
4. Następny ekran zawiera listę właściwości nowej aplikacji. Zapisz **identyfikator aplikacji** do tymczasowej lokalizacji.
![Właściwości aplikacji w usłudze OneDrive][11]
5. W obszarze **wpisów tajnych aplikacji**, kliknij przycisk **wygenerować nowe hasło**. Zwróć uwagę na **wygenerowano nowe hasło**. To jest klucz tajny aplikacji i nie jest możliwy do pobrania, po kliknięciu **OK**.
6. W obszarze **platform**, kliknij przycisk **Dodaj platformy**, a następnie wybierz pozycję **sieci Web**.
7. Wprowadź **identyfikator URI przekierowania**. W przypadku wdrożenia usługi Azure Stack domyślny identyfikator URI przekierowania jest w formie https://portal.local.azurestack.external/TokenAuthorize. Jeśli pracujesz w innej domenie, należy zastąpić nazwę domeny azurestack.local.
![Aplikacja usługi OneDrive — Dodaj platformę internetową][12]
8. Dodaj **uprawnień programu Microsoft Graph** - **delegowane uprawnienia**
    - **Files.ReadWrite.AppFolder**
    - **User.Read**  
      ![Aplikacja usługi OneDrive — uprawnień do programu Graph][13]
9. Kliknij pozycję **Zapisz**.
10.  W nowej karcie przeglądarki lub w oknie Zaloguj się do portalu administracyjnego usługi Azure Stack (https://adminportal.local.azurestack.external) jako administratora usługi.
11.  Przejdź do **dostawców zasobów** i wybierz **administrator dostawcy zasobów usługi App**.
12. Kliknij przycisk **konfiguracji kontroli źródła**.
13. Skopiuj i Wklej **identyfikator aplikacji** do **identyfikator klienta** pola wejściowego i **hasło** do **klucz tajny klienta** pole wejściowe dla usługi OneDrive.
14. Kliknij pozycję **Zapisz**.

## <a name="configure-dropbox"></a>Konfigurowanie usługi DropBox

> [!NOTE]
> Musi mieć konto usługi, aby zakończyć to zadanie. Można użyć konta organizacji, a nie konta osobistego.

1. Przejdź do https://www.dropbox.com/developers/apps i zaloguj się przy użyciu poświadczeń konta usługi DropBox.
2. Kliknij przycisk **tworzenie aplikacji**.

    ![Aplikacje usługi Dropbox][14]

3. Wybierz **interfejsu API usługi DropBox**.
4. Ustaw poziom dostępu **folderu aplikacji**.
5. Wprowadź **nazwa** dla aplikacji.
![Rejestrowanie aplikacji w usłudze Dropbox][15]
6. Kliknij przycisk **tworzenie aplikacji**. Zostanie wyświetlona strony z listą ustawień dla aplikacji, w tym **klucz aplikacji** i **klucz tajny aplikacji**.
7. Upewnij się, że **nazwa folderu aplikacji** ustawiono **usługi App Service w usłudze Azure Stack**.
8. Ustaw **OAuth 2 identyfikator URI przekierowania,** a następnie kliknij przycisk **Dodaj**. W przypadku wdrożenia usługi Azure Stack domyślny identyfikator URI przekierowania jest w formie https://portal.local.azurestack.external/TokenAuthorize. Jeśli pracujesz w innej domenie, należy zastąpić domenę w poszukiwaniu azurestack.local.
![Konfiguracja aplikacji usługi Dropbox][16]
9.  W nowej karcie przeglądarki lub w oknie Zaloguj się do portalu administracyjnego usługi Azure Stack (https://adminportal.local.azurestack.external) jako administratora usługi.
10.  Przejdź do **dostawców zasobów** i wybierz **administrator dostawcy zasobów usługi App**.
11. Kliknij przycisk **konfiguracji kontroli źródła**.
12. Skopiuj i Wklej **klucz aplikacji** do **identyfikator klienta** pola wejściowego i **klucz tajny aplikacji** do **klucz tajny klienta** pole wejściowe dla usługi DropBox.
13. Kliknij pozycję **Zapisz**.

## <a name="next-steps"></a>Kolejne kroki

Użytkownicy mogą teraz używać źródła wdrożenia dla elementów, takich jak [ciągłe wdrażanie](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment), [lokalne wdrożenie narzędzia Git](https://docs.microsoft.com/azure/app-service/deploy-local-git), i [folder synchronizacji w chmurze](https://docs.microsoft.com/azure/app-service/deploy-content-sync).

<!--Image references-->
[1]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin.png
[2]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-source-control-configuration.png
[3]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-developer-applications.png
[4]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-populated.png
[5]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-complete.png
[6]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-roles-management-server-repair-all.png
[7]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-dashboard.png
[8]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer.png
[9]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer-complete.png
[10]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-applications.png
[11]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-registration.png
[12]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-platform.png
[13]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-graph-permissions.png
[14]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-applications.png
[15]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-registration.png
[16]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-configuration.png

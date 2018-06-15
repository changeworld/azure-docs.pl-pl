---
title: Poświadczenia wdrożenia usługi Azure App Service | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać poświadczenia wdrożenia usługi Azure App Service.
services: app-service
documentationcenter: ''
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/05/2016
ms.author: dariagrigoriu
ms.openlocfilehash: d66b5aa4eb2ad90596dfe9e26bbc18996c967295
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2018
ms.locfileid: "27778551"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Skonfiguruj poświadczenia wdrażania dla usługi Azure App Service
[Usługa aplikacji Azure](http://go.microsoft.com/fwlink/?LinkId=529714) obsługuje dwa rodzaje poświadczenia [lokalnego wdrożenia Git](app-service-deploy-local-git.md) i [wdrożenia FTP/S](app-service-deploy-ftp.md). Nie są takie same, jak poświadczenia usługi Azure Active Directory.

* **Poświadczenia użytkownika na poziomie**: jeden zestaw poświadczeń dla całego konta platformy Azure. Może służyć do wdrożenia do usługi App Service dla dowolnej aplikacji, w żadnej subskrypcji z uprawnień dostępu do konta platformy Azure. Są to domyślny zestaw poświadczeń, skonfigurowanym w **usługi aplikacji** > **&lt;nazwa_aplikacji >** > **poświadczenia wdrażania**. Jest to domyślny zestaw, który jest wyświetlana w portalu graficznego interfejsu użytkownika (takich jak **— omówienie** i **właściwości** aplikacji sieci [zasobu strony](../azure-resource-manager/resource-group-portal.md#manage-resources)).

    > [!NOTE]
    > Podczas delegować dostęp do zasobów platformy Azure przy użyciu kontroli dostępu na podstawie ról (RBAC) lub współadministratora uprawnienia, każdy użytkownik usługi Azure, który uzyskuje dostęp do aplikacji można użyć jego osobistych poświadczeń na poziomie użytkownika do momentu dostęp został odwołany. Te poświadczenia wdrożenia nie powinny być udostępniać innym użytkownikom Azure.
    >
    >

* **Poświadczeń na poziomie aplikacji**: jeden zestaw poświadczeń dla każdej aplikacji. Może służyć do wdrożenia tej aplikacji tylko. Poświadczenia dla każdej aplikacji jest generowany automatycznie podczas tworzenia aplikacji i znajduje się w aplikacji profilu publikowania. Nie można ręcznie skonfigurować poświadczenia, ale można je zresetować w każdej chwili dla aplikacji.

    > [!NOTE]
    > Aby dać komuś dostęp do tych poświadczeń za pomocą roli na podstawie kontroli dostępu (RBAC), które należy rozważyć ich współautora lub nowszego w aplikacji sieci Web. Czytelnicy nie mogą opublikować i dlatego nie można uzyskać dostępu do tych poświadczeń.
    >
    >

## <a name="userscope"></a>Ustaw i zresetowanie poświadczeń na poziomie użytkownika

Poświadczenia na poziomie użytkownika można skonfigurować w dowolnej aplikacji [zasobu strony](../azure-resource-manager/resource-group-portal.md#manage-resources). Niezależnie od tego w aplikacji należy skonfigurować te poświadczenia, ma to zastosowanie do wszystkich aplikacji i dla wszystkich subskrypcji na koncie Azure. 

Aby skonfigurować poświadczeń na poziomie użytkownika:

1. W [portalu Azure](https://portal.azure.com), kliknij opcję usługi aplikacji >  **&lt;any_app >** > **poświadczenia wdrażania**.

    > [!NOTE]
    > W portalu musi mieć co najmniej jedną aplikację w celu uzyskania dostępu do strony poświadczeń wdrożenia. Jednak w przypadku [interfejsu wiersza polecenia Azure](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az_webapp_deployment_user_set), można skonfigurować poświadczeń na poziomie użytkownika bez istniejącej aplikacji.

2. Konfigurowanie nazwy użytkownika i hasła, a następnie kliknij przycisk **zapisać**.

    ![](./media/app-service-deployment-credentials/deployment_credentials_configure.png)

Po ustawieniu poświadczenia wdrażania, można znaleźć *Git* username wdrożenia w aplikacji **omówienie**,

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

i *FTP* username wdrożenia w aplikacji **właściwości**.

![](./media/app-service-deployment-credentials/deployment_credentials_properties.png)

> [!NOTE]
> Azure nie są wyświetlane hasło użytkownika na poziomie wdrożenia. Jeśli zapomnisz hasło, nie można go pobrać. Jednak można zresetować swoje poświadczenia, wykonując kroki opisane w tej sekcji.
>
>  

## <a name="appscope"></a>Pobierz i zresetowanie poświadczeń na poziomie aplikacji
Dla każdej aplikacji w usłudze App Service, jego poświadczeń na poziomie aplikacji są przechowywane w pliku XML profilu publikowania.

Aby uzyskać poświadczeń z poziomu aplikacji:

1. W [portalu Azure](https://portal.azure.com), kliknij opcję usługi aplikacji >  **&lt;any_app >** > **omówienie**.

2. Kliknij przycisk **... Więcej** > **profilu publikowania Get**, i uruchamia pobierania. Plik ustawień publikacji.

    ![](./media/app-service-deployment-credentials/publish_profile_get.png)

3. Otwórz. Plik ustawień publikacji i Znajdź `<publishProfile>` tag z atrybutem `publishMethod="FTP"`. Następnie należy pobrać jego `userName` i `password` atrybutów.
Są to poświadczeń na poziomie aplikacji.

    ![](./media/app-service-deployment-credentials/publish_profile_editor.png)

    Podobnie jak poświadczeń na poziomie użytkownika, nazwa użytkownika wdrożenia FTP jest w formacie `<app_name>\<username>`, i username wdrożenia Git jest po prostu `<username>` bez poprzedzających `<app_name>\`.

Aby zresetować poświadczeń na poziomie aplikacji:

1. W [portalu Azure](https://portal.azure.com), kliknij opcję usługi aplikacji >  **&lt;any_app >** > **omówienie**.

2. Kliknij przycisk **... Więcej** > **Resetowanie profilu publikowania**. Kliknij przycisk **tak** o potwierdzenie resetowania.

    Akcja reset unieważnia żadnego pobrane wcześniej. Pliki ustawień publikacji.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak używać tych poświadczeń do wdrożenia aplikacji z [lokalnego Git](app-service-deploy-local-git.md) lub przy użyciu [FTP/S](app-service-deploy-ftp.md).

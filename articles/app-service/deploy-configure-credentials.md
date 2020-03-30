---
title: Konfigurowanie poświadczeń wdrożenia
description: Dowiedz się, jakie typy poświadczeń wdrożenia znajdują się w usłudze Azure App Service oraz jak je skonfigurować i używać.
ms.topic: article
ms.date: 08/14/2019
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: a9d875e2c3899fa91b9cc41c0ee3b5a93ec5b8c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266080"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Konfigurowanie poświadczeń wdrażania usługi Azure App Service
[Usługa Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) obsługuje dwa typy poświadczeń dla [lokalnego wdrażania git](deploy-local-git.md) i [wdrożenia FTP/S](deploy-ftp.md). Te poświadczenia nie są takie same jak poświadczenia subskrypcji platformy Azure.

* **Poświadczenia na poziomie użytkownika:** jeden zestaw poświadczeń dla całego konta platformy Azure. Może służyć do wdrażania w usłudze App Service dla dowolnej aplikacji, w dowolnej subskrypcji, że konto platformy Azure ma uprawnienia dostępu. Jest to domyślny zestaw, który pojawia się w interfejsie użytkownika portalu (na przykład **przegląd** i **właściwości** [strony zasobów](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)aplikacji). Gdy użytkownikowi przyznaje się dostęp do aplikacji za pośrednictwem kontroli dostępu opartej na rolach (RBAC) lub uprawnień coadmin, użytkownik ten może używać własnych poświadczeń na poziomie użytkownika, dopóki dostęp nie zostanie odwołany. Nie należy udostępniać tych poświadczeń innym użytkownikom platformy Azure.

* **Poświadczenia na poziomie aplikacji:** jeden zestaw poświadczeń dla każdej aplikacji. Może służyć do wdrażania tylko do tej aplikacji. Poświadczenia dla każdej aplikacji są generowane automatycznie podczas tworzenia aplikacji. Nie można ich skonfigurować ręcznie, ale można je zresetować w dowolnym momencie. Aby użytkownikowi udzielono dostępu do poświadczeń na poziomie aplikacji za pośrednictwem (RBAC), ten użytkownik musi być współautorem lub wyższym w aplikacji (w tym wbudowana rola współautora witryny sieci Web). Czytelnicy nie mogą publikować i nie mogą uzyskać dostępu do tych poświadczeń.

## <a name="configure-user-level-credentials"></a><a name="userscope"></a>Konfigurowanie poświadczeń na poziomie użytkownika

Poświadczenia na poziomie użytkownika można skonfigurować na [stronie zasobów](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)dowolnej aplikacji . Niezależnie od tego, w której aplikacji można skonfigurować te poświadczenia, ma zastosowanie do wszystkich aplikacji i dla wszystkich subskrypcji na koncie platformy Azure. 

### <a name="in-the-cloud-shell"></a>W powłoce chmury

Aby skonfigurować użytkownika wdrażania w [aplikacji Cloud Shell,](https://shell.azure.com)uruchom polecenie [zestawu zestawu wdrażania aplikacji az webapp.](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) Zastąp \<> \<nazwy użytkownika i hasła> nazwą użytkownika wdrożenia i hasłem. 

- Nazwa użytkownika musi być unikatowa na platformie Azure, a dla lokalnych wypychań Git nie może zawierać symbolu "@". 
- Hasło musi mieć co najmniej osiem znaków, z dwoma z następujących trzech elementów: literami, cyframi i symbolami. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Dane wyjściowe JSON `null`pokazuje hasło jako . Jeśli wystąpił błąd `'Conflict'. Details: 409`, zmień nazwę użytkownika. Jeśli wystąpił błąd `'Bad Request'. Details: 400`, użyj silniejszego hasła. 

### <a name="in-the-portal"></a>W portalu

W witrynie Azure portal musi mieć co najmniej jedną aplikację, aby można było uzyskać dostęp do strony poświadczeń wdrożenia. Aby skonfigurować poświadczenia na poziomie użytkownika:

1. W [witrynie Azure portal](https://portal.azure.com)z lewego menu wybierz pozycję **App Services** > **\<any_app>**  >  **Deployment Center** > **FTP** > **Dashboard**.

    ![](./media/app-service-deployment-credentials/access-no-git.png)

    Jeśli już skonfigurowano wdrożenie git, wybierz pozycję **App Services** > **&lt;any_app>**  >  **Deployment Center** > **FTP/Credentials**.

    ![](./media/app-service-deployment-credentials/access-with-git.png)

2. Wybierz **pozycję Poświadczenia użytkownika,** skonfiguruj nazwę użytkownika i hasło, a następnie wybierz pozycję **Zapisz poświadczenia**.

Po ustawieniu poświadczeń wdrożenia nazwę użytkownika wdrożenia *git* można znaleźć na stronie **Przegląd** aplikacji,

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Jeśli wdrożenie Git jest skonfigurowane, strona pokazuje **nazwę użytkownika Git/deployment;** w przeciwnym razie **nazwa użytkownika FTP/wdrożenia**.

> [!NOTE]
> Platforma Azure nie wyświetla hasła wdrożenia na poziomie użytkownika. Jeśli zapomnisz hasła, możesz zresetować poświadczenia, wykonując czynności opisane w tej sekcji.
>
> 

## <a name="use-user-level-credentials-with-ftpftps"></a>Używanie poświadczeń na poziomie użytkownika z ftp/FTPS

Uwierzytelnianie w punkcie końcowym FTP/FTPS przy użyciu numerów poświadczeń na poziomie użytkownika wymaga nazwy użytkownika w następującym formacie:`<app-name>\<user-name>`

Ponieważ poświadczenia na poziomie użytkownika są połączone z użytkownikiem, a nie z określonym zasobem, nazwa użytkownika musi być w tym formacie, aby skierować akcję logowania do odpowiedniego punktu końcowego aplikacji.

## <a name="get-and-reset-app-level-credentials"></a><a name="appscope"></a>Poświadczenie na poziomie aplikacji i ich resetowanie
Aby uzyskać poświadczenia na poziomie aplikacji:

1. W [witrynie Azure portal](https://portal.azure.com), z lewego menu wybierz pozycję **App Services** > **&lt;any_app>**  >  **Deployment Center** > **FTP/Credentials**.

2. Wybierz **pozycję Poświadczenia aplikacji**i wybierz łącze **Kopiuj,** aby skopiować nazwę użytkownika lub hasło.

Aby zresetować poświadczenia na poziomie aplikacji, wybierz **pozycję Resetuj poświadczenia** w tym samym oknie dialogowym.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak używać tych poświadczeń do wdrażania aplikacji z [lokalnego gita](deploy-local-git.md) lub za pomocą [protokołu FTP/S.](deploy-ftp.md)

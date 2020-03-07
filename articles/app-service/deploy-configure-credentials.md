---
title: Konfigurowanie poświadczeń wdrożenia
description: Dowiedz się, jakie typy poświadczeń wdrożenia znajdują się w Azure App Service oraz jak je skonfigurować i jak z nich korzystać.
ms.topic: article
ms.date: 08/14/2019
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: a9d875e2c3899fa91b9cc41c0ee3b5a93ec5b8c8
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372079"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Skonfiguruj poświadczenia wdrażania dla Azure App Service
[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) obsługuje dwa typy poświadczeń dla [lokalnego wdrożenia git](deploy-local-git.md) i [wdrożenia FTP/S](deploy-ftp.md). Te poświadczenia nie są takie same, jak poświadczenia subskrypcji platformy Azure.

* **Poświadczenia na poziomie użytkownika**: jeden zestaw poświadczeń dla całego konta platformy Azure. Można go użyć do wdrożenia programu w celu App Service dla każdej aplikacji w dowolnej subskrypcji, do której konto platformy Azure ma uprawnienia dostępu. Jest to domyślny zestaw, który znajduje się w graficznym interfejsie użytkownika portalu (na przykład **Omówienie** i **Właściwości** [strony zasobu](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)aplikacji). Gdy użytkownik uzyskuje dostęp do aplikacji za pośrednictwem Access Control opartej na rolach (RBAC) lub współadministratora, może użyć własnych poświadczeń na poziomie użytkownika do momentu odwołania dostępu. Nie udostępniaj tych poświadczeń innym użytkownikom platformy Azure.

* **Poświadczenia na poziomie aplikacji**: jeden zestaw poświadczeń dla każdej aplikacji. Można go użyć do wdrożenia tylko w tej aplikacji. Poświadczenia dla każdej aplikacji są generowane automatycznie podczas tworzenia aplikacji. Nie można ich skonfigurować ręcznie, ale można je zresetować w dowolnym momencie. Aby użytkownik mógł uzyskać dostęp do poświadczeń na poziomie aplikacji za pośrednictwem (RBAC), użytkownik musi mieć uprawnienia współautora lub wyższą w aplikacji (w tym wbudowana rola współautor witryny sieci Web). Czytelnicy nie mogą publikować i nie mogą uzyskać dostępu do tych poświadczeń.

## <a name="userscope"></a>Konfigurowanie poświadczeń na poziomie użytkownika

Poświadczenia na poziomie użytkownika można skonfigurować na [stronie zasobów](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)dowolnej aplikacji. Niezależnie od tego, która aplikacja zostanie skonfigurowana, ma zastosowanie do wszystkich aplikacji i wszystkich subskrypcji na koncie platformy Azure. 

### <a name="in-the-cloud-shell"></a>W Cloud Shell

Aby skonfigurować użytkownika wdrożenia w [Cloud Shell](https://shell.azure.com), uruchom polecenie [AZ webapp Deployment User Set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) . Zastąp \<nazwy użytkownika > i \<hasło > nazwą użytkownika i hasłom do wdrożenia. 

- Nazwa użytkownika musi być unikatowa w ramach platformy Azure, a w przypadku lokalnych powiadomień wypychanych Git nie może zawierać symbolu ‘@’ 
- Hasło musi składać się z co najmniej ośmiu znaków, a dwa z następujących trzech elementów: litery, cyfry i symbole. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Dane wyjściowe JSON przedstawiają hasło jako `null`. Jeśli wystąpił błąd `'Conflict'. Details: 409`, zmień nazwę użytkownika. Jeśli wystąpił błąd `'Bad Request'. Details: 400`, użyj silniejszego hasła. 

### <a name="in-the-portal"></a>W portalu

W Azure Portal należy mieć co najmniej jedną aplikację, aby można było uzyskać dostęp do strony poświadczeń wdrożenia. Aby skonfigurować poświadczenia na poziomie użytkownika:

1. W [Azure Portal](https://portal.azure.com)z menu po lewej stronie wybierz pozycję **App Services** >  **\<any_app >**  > **Deployment Center** > **FTP** > **pulpit nawigacyjny**.

    ![](./media/app-service-deployment-credentials/access-no-git.png)

    Jeśli już skonfigurowano wdrożenie narzędzia Git, wybierz **App Services** >  **&lt;any_app >**  > **centrum wdrażania** > **FTP/poświadczenia**.

    ![](./media/app-service-deployment-credentials/access-with-git.png)

2. Wybierz pozycję **poświadczenia użytkownika**, skonfiguruj nazwę użytkownika i hasło, a następnie wybierz pozycję **Zapisz poświadczenia**.

Po ustawieniu poświadczeń wdrożenia można znaleźć nazwę użytkownika wdrożenia *narzędzia Git* na stronie **Przegląd** aplikacji,

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Jeśli skonfigurowano wdrożenie usługi git, na stronie zostanie wyświetlona **Nazwa użytkownika narzędzia Git/Deployment**; w przeciwnym razie **Nazwa użytkownika FTP/Deployment**.

> [!NOTE]
> Na platformie Azure nie jest wyświetlane hasło do wdrożenia na poziomie użytkownika. Jeśli zapomnisz hasła, możesz zresetować poświadczenia, wykonując kroki opisane w tej sekcji.
>
> 

## <a name="use-user-level-credentials-with-ftpftps"></a>Korzystanie z poświadczeń na poziomie użytkownika przy użyciu protokołu FTP/FTPS

Uwierzytelnianie w punkcie końcowym FTP/FTPS przy użyciu poświadczeń na poziomie użytkownika nazwa użytkownika w następującym formacie: `<app-name>\<user-name>`

Ponieważ poświadczenia na poziomie użytkownika są połączone z użytkownikiem, a nie konkretnym zasobem, nazwa użytkownika musi być w tym formacie, aby skierować akcję logowania do właściwego punktu końcowego aplikacji.

## <a name="appscope"></a>Pobieranie i resetowanie poświadczeń na poziomie aplikacji
Aby uzyskać poświadczenia na poziomie aplikacji:

1. W [Azure Portal](https://portal.azure.com)z menu po lewej stronie wybierz pozycję **App Services** >  **&lt;any_app >**  > **centrum wdrażania** > **FTP/poświadczenia**.

2. Wybierz pozycję **poświadczenia aplikacji**, a następnie wybierz link **Kopiuj** , aby skopiować nazwę użytkownika lub hasło.

Aby zresetować poświadczenia na poziomie aplikacji, wybierz pozycję **Zresetuj poświadczenia** w tym samym oknie dialogowym.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak używać tych poświadczeń do wdrażania aplikacji z [lokalnego systemu Git](deploy-local-git.md) lub przy użyciu [protokołu FTP/S](deploy-ftp.md).

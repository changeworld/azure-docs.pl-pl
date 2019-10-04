---
title: Implementowanie odzyskiwania po awarii przy użyciu funkcji tworzenia kopii zapasowych i przywracania na platformie Azure API Management | Microsoft Docs
description: Dowiedz się, jak za pomocą funkcji Backup i Restore wykonać odzyskiwanie po awarii na platformie Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/26/2019
ms.author: apimpm
ms.openlocfilehash: 9c97723687484e8af82d63b6fb4999401a69fb2c
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71958526"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Jak zaimplementować odzyskiwanie po awarii przy użyciu kopii zapasowej i przywracania usługi na platformie Azure API Management

Publikując interfejsy API i zarządzając nimi za pośrednictwem usługi Azure API Management, korzystasz z zalet odporności na uszkodzenia i infrastruktury, które w inny sposób projektujesz, implementują i zarządzają ręcznie. Platforma Azure ogranicza znaczną część potencjalnych awarii w ułamku kosztu.

Aby odzyskać problemy z dostępnością, które mają wpływ na region, w którym znajduje się usługa API Management, możesz przystąpić do odtworzenia usługi w innym regionie w dowolnym momencie. W zależności od celu czasu odzyskiwania warto zachować usługę w stanie wstrzymania w co najmniej jednym regionie. Możesz również spróbować zachować swoją konfigurację i zawartość w synchronizacji z aktywną usługą zgodnie z celem punktu odzyskiwania. Funkcje tworzenia kopii zapasowych i przywracania usługi zapewniają niezbędne bloki konstrukcyjne do implementowania strategii odzyskiwania po awarii.

Operacje tworzenia kopii zapasowych i przywracania mogą również służyć do replikowania konfiguracji usługi API Management między środowiskami operacyjnymi, np. programowaniem i przemieszczaniem. Uważaj, że dane środowiska uruchomieniowego, takie jak użytkownicy i subskrypcje, również zostaną skopiowane, co może być niewskazane.

W tym przewodniku pokazano, jak zautomatyzować operacje tworzenia kopii zapasowych i przywracania oraz jak zapewnić pomyślne uwierzytelnianie żądań tworzenia kopii zapasowych i przywracania przez Azure Resource Manager.

> [!IMPORTANT]
> Operacja przywracania nie zmienia niestandardowej konfiguracji nazwy hosta usługi docelowej. Zalecamy użycie tej samej niestandardowej nazwy hosta i certyfikatu TLS dla usług Active i standby, tak aby po zakończeniu operacji przywracania ruch można ponownie skierować do wystąpienia gotowości przez prostą zmianę CNAME w systemie DNS.
>
> Operacja tworzenia kopii zapasowej nie przechwytuje wstępnie zagregowanych danych dziennika używanych w raportach wyświetlanych w bloku analiza w Azure Portal.

> [!WARNING]
> Każda kopia zapasowa wygasa po 30 dniach. Jeśli podjęto próbę przywrócenia kopii zapasowej po upływie 30-dniowego okresu wygaśnięcia, przywracanie zakończy się niepowodzeniem z komunikatem `Cannot restore: backup expired`.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="authenticating-azure-resource-manager-requests"></a>Uwierzytelnianie Azure Resource Manager żądania

> [!IMPORTANT]
> Interfejs API REST do tworzenia kopii zapasowych i przywracania używa Azure Resource Manager i ma inny mechanizm uwierzytelniania niż interfejsy API REST do zarządzania jednostkami API Management. W procedurach przedstawionych w tej sekcji opisano sposób uwierzytelniania żądań Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie żądań Azure Resource Manager](/rest/api/index).

Wszystkie zadania dotyczące zasobów przy użyciu Azure Resource Manager muszą zostać uwierzytelnione przy użyciu Azure Active Directory, wykonując następujące czynności:

-   Dodaj aplikację do dzierżawy Azure Active Directory.
-   Ustaw uprawnienia dla dodanej aplikacji.
-   Pobierz token do uwierzytelniania żądań do Azure Resource Manager.

### <a name="create-an-azure-active-directory-application"></a>Tworzenie aplikacji Azure Active Directory

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
2. Korzystając z subskrypcji zawierającej wystąpienie usługi API Management, przejdź do karty **rejestracje aplikacji** w **Azure Active Directory** (Azure Active Directory > Zarządzaj/rejestracje aplikacji).

    > [!NOTE]
    > Jeśli domyślny katalog Azure Active Directory nie jest widoczny dla Twojego konta, skontaktuj się z administratorem subskrypcji platformy Azure, aby przyznać uprawnienia wymagane do Twojego konta.

3. Kliknij pozycję **Rejestrowanie nowej aplikacji**.

    Okno **tworzenia** pojawia się po prawej stronie. Jest to miejsce, w którym wprowadzasz informacje dotyczące aplikacji usługi AAD.

4. Wprowadź nazwę aplikacji.
5. W polu Typ aplikacji wybierz opcję **natywny**.
6. Wprowadź zastępczy adres URL, taki jak `http://resources` dla **identyfikatora URI przekierowania**, ponieważ jest to pole wymagane, ale wartość nie jest używana później. Kliknij pole wyboru, aby zapisać aplikację.
7. Kliknij przycisk **Utwórz**.

### <a name="add-an-application"></a>Dodawanie aplikacji

1. Po utworzeniu aplikacji kliknij pozycję **Ustawienia**.
2. Kliknij pozycję **wymagane uprawnienia**.
3. Kliknij pozycję **+ Dodaj**.
4. Naciśnij **pozycję Wybierz interfejs API**.
5. Wybierz pozycję **Windows** **Azure interfejs API zarządzania usługami**.
6. Naciśnij **pozycję Wybierz**.

    ![Dodawanie uprawnień](./media/api-management-howto-disaster-recovery-backup-restore/add-app.png)

7. Kliknij pozycję **uprawnienia delegowane** obok nowo dodanej aplikacji, zaznacz pole wyboru **dostęp do usługi Azure Service Management (wersja zapoznawcza)** .
8. Naciśnij **pozycję Wybierz**.
9. Kliknij pozycję **Udziel uprawnień**.

### <a name="configuring-your-app"></a>Konfigurowanie aplikacji

Przed wywołaniem interfejsów API, które generują kopię zapasową i przywracają ją, należy uzyskać token. W poniższym przykładzie zastosowano pakiet NuGet [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) w celu pobrania tokenu.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace GetTokenResourceManagerRequests
{
    class Program
    {
        static void Main(string[] args)
        {
            var authenticationContext = new AuthenticationContext("https://login.microsoftonline.com/{tenant id}");
            var result = authenticationContext.AcquireTokenAsync("https://management.azure.com/", "{application id}", new Uri("{redirect uri}"), new PlatformParameters(PromptBehavior.Auto)).Result;

            if (result == null) {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }

            Console.WriteLine(result.AccessToken);

            Console.ReadLine();
        }
    }
}
```

Zastąp `{tenant id}`, `{application id}` i `{redirect uri}`, korzystając z następujących instrukcji:

1. Zastąp wartość `{tenant id}` IDENTYFIKATORem dzierżawy utworzonej aplikacji Azure Active Directory. Aby uzyskać dostęp do tego identyfikatora, kliknij **rejestracje aplikacji** **punkty końcowe** -> .

    ![Punkty końcowe][api-management-endpoint]

2. Zastąp wartość `{application id}` wartością pobieraną, przechodząc do strony **Ustawienia** .
3. Zastąp wartość `{redirect uri}` wartością z karty **URI przekierowania** w aplikacji Azure Active Directory.

    Po określeniu wartości, przykład kodu powinien zwrócić token podobny do następującego:

    ![Token][api-management-arm-token]

    > [!NOTE]
    > Token może wygasnąć po upływie określonego czasu. Ponownie wykonaj próbkę kodu, aby wygenerować nowy token.

## <a name="calling-the-backup-and-restore-operations"></a>Wywoływanie operacji tworzenia kopii zapasowej i przywracania

Interfejsy API REST to [Usługa API Management — usługa zarządzania kopiami zapasowymi](/rest/api/apimanagement/2019-01-01/apimanagementservice/backup) i [interfejsem API — przywracanie](/rest/api/apimanagement/2019-01-01/apimanagementservice/restore).

Przed wywołaniem operacji "Backup and Restore" opisanych w poniższych sekcjach Ustaw nagłówek żądania autoryzacji dla wywołania REST.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="step1"> </a>Tworzenie kopii zapasowej usługi API Management

Aby utworzyć kopię zapasową usługi API Management, należy wydać następujące żądanie HTTP:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

miejscu

-   `subscriptionId` — Identyfikator subskrypcji zawierającej usługę API Management, której kopia zapasowa ma zostać utworzona
-   `resourceGroupName` — nazwa grupy zasobów usługi Azure API Management
-   `serviceName` — nazwa usługi API Management, w której tworzysz kopię zapasową określoną w momencie jego tworzenia
-   `api-version`-Zamień na `2018-06-01-preview`

W treści żądania należy określić docelową nazwę konta usługi Azure Storage, klucz dostępu, nazwę kontenera obiektów blob i nazwę kopii zapasowej:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Ustaw wartość `Content-Type` nagłówka żądania na `application/json`.

Kopia zapasowa to długotrwała operacja, która może trwać dłużej niż minutę. Jeśli żądanie zakończyło się pomyślnie, a proces tworzenia kopii zapasowej został rozpoczęty, otrzymasz kod stanu odpowiedzi `202 Accepted` z nagłówkiem `Location`. Utwórz żądania "GET" na adresie URL w nagłówku `Location`, aby sprawdzić stan operacji. Gdy trwa wykonywanie kopii zapasowej, nadal otrzymujesz kod stanu "202 zaakceptowany". Kod odpowiedzi `200 OK` wskazuje pomyślne zakończenie operacji tworzenia kopii zapasowej.

Podczas wykonywania żądania kopii zapasowej należy pamiętać o następujących ograniczeniach:

-   **Kontener** określony w treści żądania **musi istnieć**.
-   Gdy trwa wykonywanie kopii zapasowej, należy **unikać zmian w zarządzaniu usługami** , takich jak uaktualnianie lub obniżanie poziomu jednostki SKU, zmiana nazwy domeny i nie tylko.
-   Przywracanie **kopii zapasowej jest gwarantowane tylko przez 30 dni** od momentu jego utworzenia.
-   **Dane użycia** używane do tworzenia raportów analitycznych **nie są uwzględnione** w kopii zapasowej. Użyj [interfejsu API REST usługi Azure API Management][azure api management rest api] , aby okresowo pobierać raporty analityczne w celu zabezpieczenia.
-   Ponadto następujące elementy nie są częścią danych kopii zapasowej: niestandardowe certyfikaty SSL domen i wszystkie pośrednich lub głównych certyfikatów przekazanych przez klienta, zawartość portalu deweloperów i ustawienia integracji sieci wirtualnej.
-   Częstotliwość wykonywania kopii zapasowych usługi ma wpływ na cel punktu odzyskiwania. Aby zminimalizować, zalecamy wdrożenie zwykłych kopii zapasowych i wykonywanie kopii zapasowych na żądanie po wprowadzeniu zmian w usłudze API Management.
-   **Zmiany** wprowadzone w konfiguracji usługi (na przykład dotyczące interfejsów API, zasad i wyglądu portalu deweloperów), gdy operacja tworzenia kopii zapasowej jest w toku, **mogą zostać wykluczone z kopii zapasowej i zostaną utracone**.
-   **Zezwalaj na** dostęp z płaszczyzny kontroli do konta usługi Azure Storage. Aby utworzyć kopię zapasową, klient powinien otworzyć następujący zestaw przychodzących adresów IP na koncie magazynu. 
    > 13.84.189.17/32, 13.85.22.63/32, 23.96.224.175/32, 23.101.166.38/32, 52.162.110.80/32, 104.214.19.224/32, 13.64.39.16/32, 40.81.47.216/32, 51.145.179.78/32, 52.142.95.35/32, 40.90.185.46/32, 20.40.125.155/32
### <a name="step2"> </a>Przywracanie usługi API Management

Aby przywrócić usługę API Management z utworzonej wcześniej kopii zapasowej, należy wykonać następujące żądanie HTTP:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

miejscu

-   `subscriptionId` — Identyfikator subskrypcji zawierającej usługę API Management, w której jest przywracana kopia zapasowa
-   `resourceGroupName` — nazwa grupy zasobów, w której znajduje się usługa API Management platformy Azure, do której jest przywracana kopia zapasowa
-   `serviceName` — nazwa usługi API Management przywracanej do określonego czasu utworzenia
-   `api-version`-Zamień na `2018-06-01-preview`

W treści żądania Określ lokalizację pliku kopii zapasowej. Oznacza to, że należy dodać nazwę konta usługi Azure Storage, klucz dostępu, nazwę kontenera obiektów blob i nazwę kopii zapasowej:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Ustaw wartość `Content-Type` nagłówka żądania na `application/json`.

Przywracanie to długotrwała operacja, której ukończenie może potrwać do 30 minut. Jeśli żądanie zakończyło się pomyślnie, a proces przywracania został rozpoczęty, otrzymasz kod stanu odpowiedzi `202 Accepted` z nagłówkiem `Location`. Utwórz żądania "GET" na adresie URL w nagłówku `Location`, aby sprawdzić stan operacji. Gdy przywracanie jest w toku, nadal otrzymujesz kod stanu "202 zaakceptował". Kod odpowiedzi `200 OK` wskazuje pomyślne zakończenie operacji przywracania.

> [!IMPORTANT]
> **Jednostka SKU** przywracanej usługi **musi być zgodna** z jednostką SKU przywracanej usługi kopii zapasowej.
>
> **Zmiany** wprowadzone w konfiguracji usługi (na przykład interfejsy API, zasady, wygląd portalu deweloperów) podczas operacji przywracania w toku **mogą zostać nadpisywane**.

<!-- Dummy comment added to suppress markdown lint warning -->

> [!NOTE]
> Operacje tworzenia kopii zapasowych i przywracania można także wykonać przy użyciu poleceń programu PowerShell [_Backup-AzApiManagement_](/powershell/module/az.apimanagement/backup-azapimanagement) i [_Restore-AzApiManagement_](/powershell/module/az.apimanagement/restore-azapimanagement) .

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z poniższymi zasobami, aby zapoznać się z różnymi przewodnikami procesu tworzenia kopii zapasowej/przywracania.

-   [Replikowanie kont usługi Azure API Management](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
-   [Automatyzowanie tworzenia kopii zapasowej i przywracania w usłudze API Management za pomocą usługi Logic Apps](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps)
-   [Azure API Management: Tworzenie kopii zapasowej i przywracanie konfiguracji](https://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
    _podejście określone przez Stuarta nie jest zgodne z oficjalnymi wskazówkami, ale są interesujące._

[backup an api management service]: #step1
[restore an api management service]: #step2
[azure api management rest api]: https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest
[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png
[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png

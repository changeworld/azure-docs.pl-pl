---
title: Za pomocą odzyskiwania po awarii implementacji i przywracania kopii zapasowych w usłudze Azure API Management | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać kopii zapasowej i przywracania do odzyskiwania po awarii w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: e0c02829a2fef6e281794fdba6c9fb5d9b8a736b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241703"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Jak zaimplementować funkcje odzyskiwania po awarii przy użyciu usługi kopii zapasowej i przywracania w usłudze Azure API Management

Publikowanie i zarządzanie interfejsami API za pomocą usługi Azure API Management, teraz korzystając z zalet odporności na uszkodzenia i możliwości infrastruktury, które ma inny sposób projektowania, implementować oraz zarządzać nimi ręcznie. Platforma Azure zmniejsza dużą część potencjalnych awarii, za ułamek kosztów.

Aby odzyskać z problemów z dostępnością, które wpływają na region, który jest hostem usługi API Management, gotowość do odtworzenia w innym regionie świadczenia usługi w dowolnym momencie. W zależności od dostępności i cele czasu odzyskiwania można zarezerwować usługi kopii zapasowej w jednym lub kilku regionach. Może również próbować zachować synchronizację z usługą active swojej konfiguracji i zawartości. Funkcja "Kopia zapasowa i przywracanie" service udostępnia niezbędne bloków konstrukcyjnych dotyczące implementowania strategii odzyskiwania po awarii.

Ten przewodnik pokazuje, jak do uwierzytelniania żądań w usłudze Azure Resource Manager. Pokazano również, jak wykonać kopię zapasową i przywrócić Twojego wystąpienia usługi API Management.

> [!NOTE]
> Proces tworzenia kopii zapasowych i przywracanie wystąpienia usługi API Management do odzyskiwania po awarii może służyć także do replikowania wystąpienia usługi API Management dla scenariuszy, takich jak przejściowe.
>
> Każda kopia zapasowa wygasa po upływie 30 dni. Jeśli użytkownik podejmie próbę przywrócenia kopii zapasowej, po upływie 30-dniowy okres, przywracania zakończy się niepowodzeniem z `Cannot restore: backup expired` wiadomości.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="authenticating-azure-resource-manager-requests"></a>Żądania uwierzytelniania usługi Azure Resource Manager

> [!IMPORTANT]
> Interfejs API REST i przywracania kopii zapasowych korzysta z usługi Azure Resource Manager i ma inny mechanizm uwierzytelniania niż interfejsów API REST zarządzania jednostek usługi API Management. W tej sekcji opisano sposób uwierzytelniania żądań usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [żądań uwierzytelniania usługi Azure Resource Manager](/rest/api/index).

Wszystkie zadania, które wykonują zasobów przy użyciu usługi Azure Resource Manager musi zostać uwierzytelniony w usłudze Azure Active Directory wykonując następujące czynności:

* Dodawanie aplikacji do dzierżawy usługi Azure Active Directory.
* Ustawianie uprawnień dla aplikacji, który został dodany.
* Pobierz token do uwierzytelniania żądań do usługi Azure Resource Manager.

### <a name="create-an-azure-active-directory-application"></a>Tworzenie aplikacji usługi Azure Active Directory

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przy użyciu subskrypcji, która zawiera wystąpienia usługi API Management, przejdź do **rejestracje aplikacji** karcie **usługi Azure Active Directory** (Azure Active Directory > Rejestracje Zarządzaj/aplikacji).

    > [!NOTE]
    > Jeśli domyślny katalog usługi Azure Active Directory nie jest widoczny dla Twojego konta, skontaktuj się z administratorem subskrypcji platformy Azure do udzielenia wymaganych uprawnień do Twojego konta.
3. Kliknij pozycję **Rejestrowanie nowej aplikacji**.

    **Utwórz** po prawej stronie zostanie wyświetlone okno. To, gdzie możesz wprowadzić informacje dotyczące odpowiednich aplikacji usługi AAD.
4. Wprowadź nazwę aplikacji.
5. Typ aplikacji wybierz **natywnych**.
6. Wprowadź adres URL, symbol zastępczy, takich jak `http://resources` dla **identyfikator URI przekierowania**, ponieważ jest to wymagane pole, ale wartość nie jest używany później. Kliknij pole wyboru, aby zapisać aplikację.
7. Kliknij pozycję **Utwórz**.

### <a name="add-an-application"></a>Dodawanie aplikacji

1. Po utworzeniu aplikacji kliknij **ustawienia**.
2. Kliknij przycisk **wymagane uprawnienia**.
3. Kliknij przycisk **+ Dodaj**.
4. Naciśnij klawisz **wybierz interfejs API**.
5. Wybierz **Windows** **interfejsu API zarządzania usługami Azure**.
6. Naciśnij klawisz **wybierz**.

    ![Dodawanie uprawnień](./media/api-management-howto-disaster-recovery-backup-restore/add-app.png)

7. Kliknij przycisk **delegowane uprawnienia** obok nowo dodanych aplikacji, zaznacz pole wyboru, aby uzyskać **dostęp do usługi Azure Service Management (wersja zapoznawcza)** .
8. Naciśnij klawisz **wybierz**.
9. Kliknij przycisk **udzielić uprawnień**.

### <a name="configuring-your-app"></a>Konfigurowanie aplikacji

Przed wywołaniem metody interfejsów API, które generowania kopii zapasowych i przywrócić ją, należy uzyskać token. W poniższym przykładzie użyto [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) pakietu NuGet w celu pobrania tokenu.

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

Zastąp `{tenant id}`, `{application id}`, i `{redirect uri}` przy użyciu następujących instrukcji:

1. Zastąp `{tenant id}` o identyfikatorze dzierżawy utworzoną aplikację usługi Azure Active Directory. Identyfikator można skorzystać, klikając **rejestracje aplikacji** -> **punktów końcowych**.

    ![Punkty końcowe][api-management-endpoint]
2. Zastąp `{application id}` z wartością uzyskasz, przechodząc do **ustawienia** strony.
3. Zastąp `{redirect uri}` wartością z **identyfikatory URI przekierowań** kartę aplikacji usługi Azure Active Directory.

    Gdy wartości są określone, przykładowy kod powinien zwrócić token podobny do poniższego przykładu:

    ![Token][api-management-arm-token]

    > [!NOTE]
    > Token może być wygasają po upływie określonego czasu. Wykonaj przykładowy kod ponownie, aby wygenerować nowy token.

## <a name="calling-the-backup-and-restore-operations"></a>Podczas wywoływania operacji tworzenia kopii zapasowych i przywracania

Interfejsy API REST są [usługi Api Management — kopia zapasowa](/rest/api/apimanagement/2019-01-01/apimanagementservice/backup) i [usługi Api Management — Przywracanie](/rest/api/apimanagement/2019-01-01/apimanagementservice/restore).

Przed wywołaniem "Kopia zapasowa i przywracanie" czynności opisane w poniższych sekcjach, ustawić nagłówek autoryzacji żądania dla wywołania REST.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="step1"> </a>Tworzenie kopii zapasowej usługi API Management

Aby utworzyć kopię zapasową problem z usługą API Management następujące żądanie HTTP:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

gdzie:

* `subscriptionId` — Identyfikator subskrypcji, który zawiera usługi API Management, którą próbujesz utworzyć kopię zapasową
* `resourceGroupName` — Nazwa grupy zasobów usługi Azure API Management
* `serviceName` — Nazwa usługi API Management wprowadzasz kopii zapasowej określony w momencie jego tworzenia.
* `api-version` -Zamień `2018-06-01-preview`

W treści żądania Określ Nazwa docelowego konta magazynu platformy Azure, klucz dostępu, nazwa kontenera obiektów blob i nazwa kopii zapasowej:

```json
{
  "storageAccount": "{storage account name for the backup}",
  "accessKey": "{access key for the account}",
  "containerName": "{backup container name}",
  "backupName": "{backup blob name}"
}
```

Ustaw wartość `Content-Type` nagłówek żądania do `application/json`.

Kopia zapasowa jest operacją wymagającą dużo czasu, który może zająć więcej niż minutę.  Jeśli żądanie zakończyło się pomyślnie rozpoczął się proces tworzenia kopii zapasowej, pojawi się `202 Accepted` kod stanu odpowiedzi z `Location` nagłówka.  Upewnij się, "GET" żądania na adres URL w `Location` nagłówka, aby sprawdzić stan operacji. Podczas tworzenia kopii zapasowej jest w toku, nadal jest wyświetlany kod stanu "202 zaakceptowano". Kod odpowiedzi `200 OK` wskazuje pomyślne ukończenie operacji tworzenia kopii zapasowej.

Podczas wykonywania żądania utworzenia kopii zapasowej, należy zwrócić uwagę następujące ograniczenia:

* **Kontener** określona w treści żądania **musi istnieć**.
* Gdy trwa wykonywanie kopii zapasowej, **uniknąć zmian w zarządzaniu usługami** np. jednostkę SKU uaktualnienia lub starszą wersję, zmiana nazwy domeny i nie tylko.
* Przywracanie **kopii zapasowej jest gwarantowane tylko przez 30 dni** od chwili jego utworzenia.
* **Dane dotyczące użycia** używany do tworzenia raportów analitycznych **nie jest uwzględniona** w kopii zapasowej. Użyj [API REST usługi Azure API Management] [ Azure API Management REST API] okresowo pobieranie tymczasem do użycia raportów analitycznych.
* Częstotliwość, z którym wykonywanie kopii zapasowych usługi wpływa na celu punktu odzyskiwania. Aby je zminimalizować, firma Microsoft zaleca wdrażanie regularnie Twórz kopie zapasowe i wykonywanie kopii zapasowych na żądanie, po wprowadzeniu zmian do usługi API Management.
* **Zmiany** wprowadzone w konfiguracji usługi (na przykład interfejsów API, zasady i wyglądu portalu dla deweloperów) podczas kopii zapasowej operacja jest w toku **może być wykluczone z kopii zapasowej i zostaną utracone**.

### <a name="step2"> </a>Przywracanie usługi API Management

Aby usługi API Management należy przywrócić z kopii zapasowej utworzonej wcześniej, należy wprowadzić następujące żądanie HTTP:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

gdzie:

* `subscriptionId` — Identyfikator subskrypcji, która przechowuje usługi API Management, który jest przywrócenie kopii zapasowej do
* `resourceGroupName` — Nazwa grupy zasobów, która zawiera usługa Azure API Management jest przywrócenie kopii zapasowej do
* `serviceName` — Nazwa usługi API Management, usługa przywracany do określonej w czasie jego tworzenia
* `api-version` -Zamień `2018-06-01-preview`

W treści żądania Określ lokalizację pliku kopii zapasowej. Oznacza to Dodaj nazwę konta usługi Azure storage, klucz dostępu, nazwa kontenera obiektów blob i nazwa kopii zapasowej:

```json
{
  "storageAccount": "{storage account name for the backup}",
  "accessKey": "{access key for the account}",
  "containerName": "{backup container name}",
  "backupName": "{backup blob name}"
}
```

Ustaw wartość `Content-Type` nagłówek żądania do `application/json`.

Przywracanie jest operacją wymagającą dużo czasu, który może potrwać co najmniej 30 minut, aby zakończyć. Jeśli żądanie zakończyło się pomyślnie rozpoczął się proces przywracania, pojawi się `202 Accepted` kod stanu odpowiedzi z `Location` nagłówka. Upewnij się, "GET" żądania na adres URL w `Location` nagłówka, aby sprawdzić stan operacji. Podczas przywracania jest w toku, nadal jest wyświetlany "202 zaakceptowano" Kod stanu. Kod odpowiedzi `200 OK` wskazuje pomyślne ukończenie operacji przywracania.

> [!IMPORTANT]
> **Jednostka SKU** usługi przywracany do **musi odpowiadać** jednostki SKU usługi kopii zapasowej przywracanej.
>
> **Zmiany** wprowadzone w konfiguracji usługi (na przykład interfejsów API, zasady, wyglądu portalu dla deweloperów) podczas przywracania Trwa operacja **mogą zostać zastąpione**.

<!-- Dummy comment added to suppress markdown lint warning -->

> [!NOTE]
> Operacje tworzenia kopii zapasowych i przywracania można również wykonać przy użyciu programu PowerShell *AzApiManagement kopii zapasowej* i *AzApiManagement przywracania* odpowiednio poleceń.

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z poniższymi zasobami, aby różne wskazówki dotyczące procesu/przywracania kopii zapasowej.

* [Replikacja usługi Azure API Management kont](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
* [Automatyzowanie tworzenia kopii zapasowej i przywracania w usłudze API Management za pomocą usługi Logic Apps](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps)
* [Azure API Management: Wykonywanie kopii zapasowych i przywracanie konfiguracji](https://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
  *podejście szczegółowych przy Stuart jest niezgodna z oficjalnego wskazówki dotyczące, ale co ciekawe.*

[Backup an API Management service]: #step1
[Restore an API Management service]: #step2

[Azure API Management REST API]: https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest

[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png

[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png

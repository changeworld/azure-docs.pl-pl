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
ms.date: 08/15/2018
ms.author: apimpm
ms.openlocfilehash: ed8c34a7e1e11d431d9a3b416067736da0d1612c
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43046383"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Jak zaimplementować funkcje odzyskiwania po awarii przy użyciu usługi kopii zapasowej i przywracania w usłudze Azure API Management

Wybierając do publikowania i zarządzanie interfejsami API za pomocą usługi Azure API Management, wykorzystując wielu odporności na uszkodzenia i możliwości infrastruktury, które w przeciwnym razie trzeba byłoby zaprojektować, implementować oraz zarządzać nimi. Platforma Azure zmniejsza dużą część potencjalnych awarii, za ułamek kosztów.

Aby odzyskać z problemów wpływających na region, w którym jest hostowana usługa API Management z dostępnością, powinno być gotowe do odtworzenia usługi w różnych regionach, w dowolnym momencie. W zależności od dostępności i cel czasu odzyskiwania można zarezerwować usługi kopii zapasowej w jednym lub wielu regionach i próbuje zachować swojej konfiguracji i zawartości w synchronizacji z usługą active. Funkcja "Kopia zapasowa i przywracanie" service udostępnia niezbędne bloków konstrukcyjnych dotyczące implementowania strategii odzyskiwania po awarii.

Ten przewodnik pokazuje, jak do uwierzytelniania żądań w usłudze Azure Resource Manager oraz sposób tworzenia kopii i przywracania Twojego wystąpienia usługi API Management.

> [!NOTE]
> Proces tworzenia kopii zapasowych i przywracanie wystąpienia usługi API Management do odzyskiwania po awarii może służyć także do replikowania wystąpienia usługi API Management dla scenariuszy, takich jak przejściowe.
>
> Każda kopia zapasowa wygasa po upływie 30 dni. Jeśli użytkownik podejmie próbę przywrócenia kopii zapasowej, po upływie 30-dniowy okres, przywracania zakończy się niepowodzeniem z `Cannot restore: backup expired` wiadomości.
>
>

## <a name="authenticating-azure-resource-manager-requests"></a>Żądania uwierzytelniania usługi Azure Resource Manager

> [!IMPORTANT]
> Interfejs API REST i przywracania kopii zapasowych korzysta z usługi Azure Resource Manager i ma inny mechanizm uwierzytelniania niż interfejsów API REST zarządzania jednostek usługi API Management. W tej sekcji opisano sposób uwierzytelniania żądań usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [żądań uwierzytelniania usługi Azure Resource Manager](http://msdn.microsoft.com/library/azure/dn790557.aspx).

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
6. Wprowadź adres URL, symbol zastępczy, takich jak `http://resources` dla **identyfikator URI przekierowania**, ponieważ jest to wymagane pole, ale nie jest używana wartość później. Kliknij pole wyboru, aby zapisać aplikację.
7. Kliknij pozycję **Utwórz**.

### <a name="add-an-application"></a>Dodawanie aplikacji

1. Po utworzeniu aplikacji kliknij **ustawienia**.
2. Kliknij przycisk **wymagane uprawnienia**.
3. Kliknij przycisk **+ Dodaj**.
4. Naciśnij klawisz **wybierz interfejs API**.
5. Wybierz **Windows** **interfejsu API zarządzania usługami Azure**.
6. Naciśnij klawisz **wybierz**. 

    ![Dodawanie uprawnień](./media/api-management-howto-disaster-recovery-backup-restore/add-app.png)

7. Kliknij przycisk **delegowane uprawnienia** obok nowo dodanych aplikacji, zaznacz pole wyboru, aby uzyskać **dostęp do usługi Azure Service Management (wersja zapoznawcza)**.
8. Naciśnij klawisz **wybierz**.
9. Kliknij przycisk **udzielanych Grant**.

### <a name="configuring-your-app"></a>Konfigurowanie aplikacji

Przed wywoływania interfejsów API, które generowania kopii zapasowych i przywrócić ją, należy uzyskać token. W poniższym przykładzie użyto [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) pakietu NuGet w celu pobrania tokenu.

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

Zastąp `{tentand id}`, `{application id}`, i `{redirect uri}` przy użyciu następujących instrukcji:

1. Zastąp `{tenant id}` o identyfikatorze dzierżawy utworzoną aplikację usługi Azure Active Directory. Identyfikator można skorzystać, klikając **rejestracje aplikacji** -> **punktów końcowych**.

    ![Punkty końcowe][api-management-endpoint]
2. Zastąp `{application id}` z wartością uzyskasz, przechodząc do **ustawienia** strony.
3. Zastąp `{redirect uri}` wartością z **identyfikatory URI przekierowań** kartę aplikacji usługi Azure Active Directory.

    Gdy wartości są określone, przykładowy kod powinien zwrócić token podobny do poniższego przykładu:

    ![Token][api-management-arm-token]

    > [!NOTE]
    > Token może być wygasają po upływie określonego czasu. Wykonaj przykładowy kod ponownie, aby wygenerować nowy token.

## <a name="calling-the-backup-and-restore-operations"></a>Podczas wywoływania operacji tworzenia kopii zapasowych i przywracania

Interfejsy API REST są [usługi Api Management — kopia zapasowa](https://docs.microsoft.com/rest/api/apimanagement/apimanagementservice/backup) i [usługi Api Management — Przywracanie](https://docs.microsoft.com/rest/api/apimanagement/apimanagementservice/restore).

Przed wywołaniem "Kopia zapasowa i przywracanie" czynności opisane w poniższych sekcjach, ustawić nagłówek autoryzacji żądania dla wywołania REST.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="step1"> </a>Tworzenie kopii zapasowej usługi API Management
Aby utworzyć kopię zapasową problem z usługą API Management następujące żądanie HTTP:

```
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

Gdzie:

* `subscriptionId` — Identyfikator subskrypcji zawierającej usługi API Management, którą chcesz utworzyć kopię zapasową
* `resourceGroupName` — Nazwa grupy zasobów usługi Azure API Management
* `serviceName` — Nazwa usługi API Management wykonujesz kopię zapasową określony w momencie jego tworzenia.
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

Kopia zapasowa jest operacją wymagającą dużo czasu, który może potrwać kilka minut.  Jeśli żądanie zakończyło się pomyślnie i zainicjowano proces tworzenia kopii zapasowej, zostanie wyświetlony `202 Accepted` kod stanu odpowiedzi z `Location` nagłówka.  Upewnij się, "GET" żądania na adres URL w `Location` nagłówka, aby sprawdzić stan operacji. Podczas tworzenia kopii zapasowej jest w toku, nadal jest wyświetlany kod stanu "202 zaakceptowano". Kod odpowiedzi `200 OK` wskazuje pomyślne ukończenie operacji tworzenia kopii zapasowej.

Należy zauważyć następujące ograniczenia, podczas wykonywania żądania utworzenia kopii zapasowej.

* **Kontener** określona w treści żądania **musi istnieć**.
* Gdy trwa wykonywanie kopii zapasowej możesz **nie powinien podejmować żadnych operacji zarządzania usługi** takich jak jednostkę SKU uaktualnienia lub starszą wersję, zmiana nazwy domeny, itp.
* Przywracanie **kopii zapasowej jest gwarantowane tylko przez 30 dni** od chwili jego utworzenia.
* **Dane dotyczące użycia** używany do tworzenia raportów analitycznych **nie jest uwzględniony** w kopii zapasowej. Użyj [API REST usługi Azure API Management] [ Azure API Management REST API] okresowo pobieranie tymczasem do użycia raportów analitycznych.
* Częstotliwość, z którym wykonywanie kopii zapasowych usługi wpływa na celu punktu odzyskiwania. Aby je zminimalizować, zalecane jest implementowanie regularnie Twórz kopie zapasowe także wykonywanie kopii zapasowych na żądanie po wprowadzeniu istotnych zmian do usługi API Management.
* **Zmiany** wprowadzone w konfiguracji usługi (na przykład interfejsów API, zasady, wyglądu portalu dla deweloperów) podczas kopii zapasowej operacja jest w toku **mogą nie zostać uwzględnione w kopii zapasowej i w związku z tym utratę**.

### <a name="step2"> </a>Przywracanie usługi API Management
Aby przywrócić usługi API Management w ramach usługi kopii zapasowej utworzonej wcześniej upewnij następujące żądanie HTTP:

```
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

Gdzie:

* `subscriptionId` — Identyfikator subskrypcji zawierającej odbywa się przywracanie kopii zapasowej do usługi API Management
* `resourceGroupName` — Nazwa grupy zasobów zawierające odbywa się przywracanie kopii zapasowej do usługi Azure API Management
* `serviceName` — Nazwa usługi API Management, usługa przywracany do określonych w momencie jego tworzenia
* `api-version` -Zamień `2018-06-01-preview`

W treści żądania Określ lokalizację pliku kopii zapasowej, który jest, nazwa konta usługi Azure storage, klucz dostępu, nazwa kontenera obiektów blob i nazwa kopii zapasowej:

```json
{
  "storageAccount": "{storage account name for the backup}",
  "accessKey": "{access key for the account}",
  "containerName": "{backup container name}",
  "backupName": "{backup blob name}"
}
```

Ustaw wartość `Content-Type` nagłówek żądania do `application/json`.

Przywracanie jest operacją wymagającą dużo czasu, który może potrwać co najmniej 30 minut, aby zakończyć. Jeśli żądanie zakończyło się pomyślnie i zainicjowano proces przywracania, możesz otrzymać `202 Accepted` kod stanu odpowiedzi z `Location` nagłówka. Upewnij się, "GET" żądania na adres URL w `Location` nagłówka, aby sprawdzić stan operacji. Podczas przywracania jest w toku, nadal jest wyświetlany "202 zaakceptowano" Kod stanu. Kod odpowiedzi `200 OK` wskazuje pomyślne ukończenie operacji przywracania.

> [!IMPORTANT]
> **Jednostka SKU** usługi przywracany do **musi odpowiadać** jednostki SKU usługi kopii zapasowej przywracanej.
>
> **Zmiany** wprowadzone w konfiguracji usługi (na przykład interfejsów API, zasady, wyglądu portalu dla deweloperów) podczas przywracania Trwa operacja **mogą zostać zastąpione**.

> [!NOTE]
> Operacje tworzenia kopii zapasowych i przywracania można również wykonać przy użyciu programu Powershell *Backup-AzureRmApiManagement* i *Restore-AzureRmApiManagement* odpowiednio poleceń.

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z poniższymi zasobami, aby różne wskazówki dotyczące procesu/przywracania kopii zapasowej.

* [Replikacja usługi Azure API Management kont](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
* [Automatyzowanie interfejsu API zarządzania tworzenia kopii zapasowych i przywracania z usługą Logic Apps](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps)
* [Usługa Azure API Management: Wykonywanie kopii zapasowych i przywracanie konfiguracji](http://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
  *podejście szczegółowych przy Stuart jest niezgodna z oficjalnego wskazówki dotyczące, ale co ciekawe.*

[Backup an API Management service]: #step1
[Restore an API Management service]: #step2

[Azure API Management REST API]: http://msdn.microsoft.com/library/azure/dn781421.aspx

[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png

[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png

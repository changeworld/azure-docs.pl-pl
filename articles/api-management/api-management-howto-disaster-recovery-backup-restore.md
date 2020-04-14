---
title: Wdrażanie odzyskiwania po awarii przy użyciu kopii zapasowych i przywracania w usłudze API Management
titleSuffix: Azure API Management
description: Dowiedz się, jak używać kopii zapasowej i przywracania do odzyskiwania po awarii w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/03/2020
ms.author: apimpm
ms.openlocfilehash: e74d7dcf8764d167e0080c9d7cca5573bd69ef1d
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261010"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Jak zaimplementować odzyskiwanie po awarii przy użyciu funkcji tworzenia i przywracania kopii zapasowych w usłudze Azure API Management

Publikując interfejsy API i zarządzając nimi za pośrednictwem usługi Azure API Management, korzystasz z możliwości odporności na uszkodzenia i infrastruktury, które w przeciwnym razie projektujesz, implementujesz i zarządzasz ręcznie. Platforma Azure zmniejsza dużą część potencjalnych awarii za ułamek kosztów.

Aby odzyskać od problemów z dostępnością, które wpływają na region, który obsługuje usługę api Management, należy przygotować do odtworzenia usługi w innym regionie w dowolnym momencie. W zależności od celu czasu odzyskiwania można zachować usługę wstrzymania w co najmniej jednym regionie. Można również spróbować zachować ich konfiguracji i zawartości w synchronizacji z aktywną usługą zgodnie z celem punktu odzyskiwania. Funkcje tworzenia kopii zapasowych i przywracania usługi zapewnia niezbędne bloki konstrukcyjne do wdrażania strategii odzyskiwania po awarii.

Operacje tworzenia kopii zapasowych i przywracania mogą być również używane do replikowania konfiguracji usługi API Management między środowiskami operacyjnymi, np. Uważaj, że dane środowiska wykonawczego, takie jak użytkownicy i subskrypcje, zostaną również skopiowane, co może nie zawsze być pożądane.

W tym przewodniku pokazano, jak zautomatyzować operacje tworzenia kopii zapasowych i przywracania oraz jak zapewnić pomyślne uwierzytelnianie żądań tworzenia kopii zapasowych i przywracania przez usługę Azure Resource Manager.

> [!IMPORTANT]
> Operacja przywracania nie zmienia niestandardowej konfiguracji nazwy hosta usługi docelowej. Zaleca się używanie tej samej niestandardowej nazwy hosta i certyfikatu TLS dla usług aktywnych i rezerwowych, tak aby po zakończeniu operacji przywracania ruch mógł zostać przekierowany do wystąpienia wstrzymania przez prostą zmianę nazwy CNAME DNS.
>
> Operacja tworzenia kopii zapasowej nie przechwytuje wstępnie zagregowanych danych dziennika używanych w raportach wyświetlanych w bloku Analytics w witrynie Azure portal.

> [!WARNING]
> Każda kopia zapasowa wygasa po 30 dniach. Jeśli spróbujesz przywrócić kopię zapasową po upływie 30-dniowego okresu `Cannot restore: backup expired` wygaśnięcia, przywracanie zakończy się niepowodzeniem z komunikatem.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="authenticating-azure-resource-manager-requests"></a>Uwierzytelnianie żądań usługi Azure Resource Manager

> [!IMPORTANT]
> Interfejs API REST do tworzenia kopii zapasowych i przywracania używa usługi Azure Resource Manager i ma inny mechanizm uwierzytelniania niż interfejsy API REST do zarządzania jednostkami zarządzania interfejsami API. W krokach w tej sekcji opisano sposób uwierzytelniania żądań usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie żądań usługi Azure Resource Manager](/rest/api/index).

Wszystkie zadania wykonywane w zasobach przy użyciu usługi Azure Resource Manager muszą być uwierzytelnione za pomocą usługi Azure Active Directory przy użyciu następujących kroków:

-   Dodaj aplikację do dzierżawy usługi Azure Active Directory.
-   Ustaw uprawnienia dla dodanej aplikacji.
-   Pobierz token do uwierzytelniania żądań do usługi Azure Resource Manager.

### <a name="create-an-azure-active-directory-application"></a>Tworzenie aplikacji usługi Azure Active Directory

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Korzystając z subskrypcji zawierającej wystąpienie usługi zarządzania interfejsami API, przejdź do karty **Rejestracje aplikacji** w **usłudze Azure Active Directory** (Azure Active Directory > Manage/App registrations).

    > [!NOTE]
    > Jeśli domyślny katalog usługi Azure Active Directory nie jest widoczny dla Twojego konta, skontaktuj się z administratorem subskrypcji platformy Azure, aby udzielić wymaganych uprawnień do konta.

3. Kliknij pozycję **Rejestrowanie nowej aplikacji**.

    Po prawej stronie pojawi się okno **Utwórz.** W tym miejscu należy wprowadzić informacje istotne dla aplikacji AAD.

4. Wprowadź nazwę aplikacji.
5. Dla typu aplikacji wybierz opcję **Natywna**.
6. Wprowadź zastępczy adres `http://resources` URL, taki jak **adres URI przekierowania**, ponieważ jest to wymagane pole, ale wartość nie jest używana później. Kliknij to pole wyboru, aby zapisać aplikację.
7. Kliknij przycisk **Utwórz**.

### <a name="add-an-application"></a>Dodawanie aplikacji

1. Po utworzeniu aplikacji kliknij pozycję **Uprawnienia interfejsu API**.
2. Kliknij pozycję **+ Dodaj uprawnienie**.
4. Naciśnij **klawisze Select Microsoft API .**
5. Wybierz **pozycję Azure Service Management**.
6. Naciśnij **klawisz Select**.

    ![Dodawanie uprawnień](./media/api-management-howto-disaster-recovery-backup-restore/add-app.png)

7. Kliknij pozycję **Uprawnienia delegowane** obok nowo dodanej aplikacji, zaznacz pole wyboru **Zarządzanie usługami platformy Azure programu Access (wersja zapoznawcza)**.
8. Naciśnij **klawisz Select**.
9. Kliknij **pozycję Udziel uprawnień**.

### <a name="configuring-your-app"></a>Konfigurowanie aplikacji

Przed wywołaniem interfejsów API, które generują kopię zapasową i przywrócić go, należy uzyskać token. W poniższym przykładzie użyto pakietu [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet do pobrania tokenu.

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

`{tenant id}`Zastąp `{redirect uri}` i `{application id}`po użyciu następujących instrukcji:

1. Zamień `{tenant id}` identyfikator dzierżawy utworzonej aplikacji usługi Azure Active Directory. Dostęp do identyfikatora można uzyskać, klikając pozycję **Punkty końcowe** -> **Endpoints**rejestracji aplikacji .

    ![Punkty końcowe][api-management-endpoint]

2. Zamień `{application id}` na wartość, którą otrzymujesz, przechodząc do strony **Ustawienia.**
3. Zastąp `{redirect uri}` wartość z karty **Przekierowanie identyfikatorów URI** aplikacji usługi Azure Active Directory.

    Po określeniu wartości przykład kodu powinien zwracać token podobny do następującego przykładu:

    ![Token][api-management-arm-token]

    > [!NOTE]
    > Token może wygasnąć po pewnym czasie. Wykonaj próbkę kodu ponownie, aby wygenerować nowy token.

## <a name="calling-the-backup-and-restore-operations"></a>Wywoływanie operacji tworzenia kopii zapasowych i przywracania

Interfejsy API REST to [Usługa zarządzania interfejsami API — usługa tworzenia kopii zapasowych](/rest/api/apimanagement/2019-12-01/apimanagementservice/backup) i [zarządzania interfejsami API — przywracanie](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore).

Przed wywołaniem operacji "kopia zapasowa i przywracanie" opisanych w poniższych sekcjach ustaw nagłówek żądania autoryzacji dla wywołania REST.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="back-up-an-api-management-service"></a><a name="step1"> </a>Finansowanie usługi zarządzania interfejsami API

Aby zrobić kopii zapasowej usługi zarządzania interfejsami API, wystąpił następujący komunikat HTTP:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

gdzie:

-   `subscriptionId`- Identyfikator subskrypcji, która przechowuje usługę API Management, którą próbujesz zrobić, aby zrobić jej utworzenie kopii zapasowej
-   `resourceGroupName`- nazwa grupy zasobów usługi Azure API Management
-   `serviceName`- nazwę usługi API Management, którą tworzysz kopię zapasową określoną w momencie jej tworzenia
-   `api-version`- zastąpić na`2018-06-01-preview`

W treści żądania określ docelową nazwę konta magazynu platformy Azure, klucz dostępu, nazwę kontenera obiektów blob i nazwę kopii zapasowej:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Ustaw wartość nagłówka żądania `application/json`na `Content-Type` .

Kopia zapasowa to długotrwała operacja, która może potrwać dłużej niż minutę. Jeśli żądanie powiodło się i rozpoczął się `202 Accepted` proces tworzenia `Location` kopii zapasowej, otrzymasz kod stanu odpowiedzi z nagłówkiem. Rościj żądania "GET" `Location` do adresu URL w nagłówku, aby dowiedzieć się o stanie operacji. Podczas wykonywania kopii zapasowej nadal otrzymujesz kod stanu "202 zaakceptowane". A Kod `200 OK` odpowiedzi wskazuje pomyślne zakończenie operacji tworzenia kopii zapasowej.

Należy zwrócić uwagę na następujące ograniczenia podczas tworzenia żądania tworzenia kopii zapasowej lub przywracania:

-   **Kontener** określony w treści żądania **musi istnieć**.
-   Gdy trwa tworzenie kopii zapasowej, **należy unikać zmian w zarządzaniu w usłudze,** takich jak uaktualnienie lub obniżenie poziomu jednostki SKU, zmiana nazwy domeny i inne.
-   Przywracanie **kopii zapasowej jest gwarantowane tylko przez 30 dni** od momentu jej utworzenia.
-   **Dane użycia** używane do tworzenia raportów analitycznych **nie są uwzględniane** w kopii zapasowej. Użyj [interfejsu API REST usługi Azure API Management,][azure api management rest api] aby okresowo pobierać raporty analityczne w celu przechowania.
-   Ponadto następujące elementy nie są częścią danych kopii zapasowej: certyfikaty TLS/SSL domeny niestandardowej oraz wszelkie certyfikaty pośrednie lub główne przekazywane przez klienta, zawartość portalu dewelopera i ustawienia integracji sieci wirtualnej.
-   Częstotliwość wykonywania kopii zapasowych usługi wpływa na cel punktu odzyskiwania. Aby zminimalizować, zaleca się implementowanie regularnych kopii zapasowych i wykonywania kopii zapasowych na żądanie po wdrożeniu zmian w usłudze zarządzania interfejsami API.
-   **Zmiany** wprowadzone w konfiguracji usługi (na przykład interfejsy API, zasady i wygląd portalu dla deweloperów) podczas wykonywania kopii zapasowej **mogą zostać wykluczone z kopii zapasowej i zostaną utracone.**
-   **Zezwalaj na** dostęp z płaszczyzny kontroli do konta usługi Azure Storage, jeśli ma włączoną [zaporę.][azure-storage-ip-firewall] Klient powinien otworzyć zestaw [adresów IP płaszczyzny kontroli interfejsu API platformy Azure][control-plane-ip-address] na swoim koncie magazynu do tworzenia kopii zapasowych lub przywracania z. 

> [!NOTE]
> Jeśli spróbujesz wykonać kopię zapasową/przywrócić z/do usługi zarządzania interfejsem API przy użyciu konta magazynu, który ma [włączoną zaporę,][azure-storage-ip-firewall] w tym samym regionie platformy Azure, to nie będzie działać. Dzieje się tak, ponieważ żądania do usługi Azure Storage nie są sNATed do publicznego adresu IP z > obliczeniowej (Płaszczyzna sterowania usługi Azure Api Management). Żądanie magazynu między regionami zostanie SNATed.

### <a name="restore-an-api-management-service"></a><a name="step2"> </a>Przywracanie usługi zarządzania interfejsami API

Aby przywrócić usługę zarządzania interfejsami API z wcześniej utworzonej kopii zapasowej, należy wykonać następujące żądanie HTTP:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

gdzie:

-   `subscriptionId`- Identyfikator subskrypcji, która przechowuje usługę API Management, do której przywracasz kopię zapasową
-   `resourceGroupName`- nazwa grupy zasobów, która przechowuje usługę Azure API Management, do której przywracasz kopię zapasową
-   `serviceName`- nazwa usługi API Management przywracanej do określonej w czasie jej tworzenia
-   `api-version`- zastąpić na`2018-06-01-preview`

W treści żądania określ lokalizację pliku kopii zapasowej. Oznacza to, że dodaj nazwę konta magazynu platformy Azure, klucz dostępu, nazwę kontenera obiektów blob i nazwę kopii zapasowej:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Ustaw wartość nagłówka żądania `application/json`na `Content-Type` .

Przywracanie jest długotrwałą operacją, która może potrwać do 30 lub więcej minut. Jeśli żądanie powiodło się i rozpoczął się `202 Accepted` proces przywracania, otrzymasz kod stanu odpowiedzi z nagłówkiem. `Location` Rościj żądania "GET" `Location` do adresu URL w nagłówku, aby dowiedzieć się o stanie operacji. Gdy przywracanie jest w toku, nadal otrzymujesz kod stanu "202 Zaakceptowane". Kod odpowiedzi `200 OK` wskazuje pomyślne zakończenie operacji przywracania.

> [!IMPORTANT]
> **Jednostka SKU** przywróconej usługi **musi odpowiadać** jednostce SKU przywracanej usługi kopii zapasowej.
>
> **Zmiany** wprowadzone w konfiguracji usługi (na przykład interfejsy API, zasady, wygląd portalu dla deweloperów) podczas operacji przywracania **mogą zostać zastąpione**.

<!-- Dummy comment added to suppress markdown lint warning -->

> [!NOTE]
> Operacje tworzenia kopii zapasowych i przywracania można również wykonywać za pomocą poleceń programu PowerShell [_Backup-AzApiManagement_](/powershell/module/az.apimanagement/backup-azapimanagement) i [_Restore-AzApiManagement._](/powershell/module/az.apimanagement/restore-azapimanagement)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi zasobami dla różnych instruktajów procesu tworzenia kopii zapasowej/przywracania.

-   [Replikowanie kont zarządzania interfejsem API platformy Azure](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
-   [Automatyzowanie tworzenia kopii zapasowej i przywracania w usłudze API Management za pomocą usługi Logic Apps](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps)
-   [Usługa Azure API Management: tworzenie kopii zapasowych i przywracanie konfiguracji](https://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
    _Podejście opisane przez Stuart nie jest zgodne z oficjalnymi wytycznymi, ale jest interesujące._

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
[control-plane-ip-address]: api-management-using-with-vnet.md#control-plane-ips
[azure-storage-ip-firewall]: ../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range

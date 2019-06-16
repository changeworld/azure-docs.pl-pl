---
title: Zdalne monitorowanie kontrola dostępu — Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje dotyczące sposobu konfigurowania kontroli dostępu opartej na rolach (RBAC) w akceleratora rozwiązania monitorowania zdalnego
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: 9accb41cdb4d780bf137d6872cca022226f902e6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61443137"
---
# <a name="configure-role-based-access-controls-in-the-remote-monitoring-solution-accelerator"></a>Konfigurowanie kontroli dostępu opartej na rolach w akceleratora rozwiązania monitorowania zdalnego

Ten artykuł zawiera informacje o sposobie konfigurowania kontroli dostępu opartej na rolach w akceleratora rozwiązania monitorowania zdalnego. Kontrola dostępu oparta na rolach umożliwiają ograniczenie dostępu dla poszczególnych użytkowników do określonych funkcji w rozwiązaniu.

## <a name="default-settings"></a>Ustawienia domyślne

Przy pierwszym wdrożeniu rozwiązania do zdalnego monitorowania, dostępne są dwie role: **Administrator** i **tylko do odczytu**.

Żaden użytkownik w **administratora** rola ma pełny dostęp do rozwiązania, w tym następujące uprawnienia poniżej. Użytkownik w **tylko do odczytu** roli będzie miał dostęp tylko do wyświetlania rozwiązania.

| Uprawnienie            | Jednostka administracyjna | Tylko do odczytu |
|----------------       |-------|-----------|
| Wyświetl rozwiązania         | Tak   | Yes       |
| Alarmy aktualizacji         | Tak   | Nie        |
| Usuń alarmów         | Tak   | Nie        |
| Tworzenie urządzenia        | Tak   | Nie        |
| Aktualizowanie urządzenia        | Tak   | Nie        |
| Usuwanie urządzeń        | Tak   | Nie        |
| Tworzenie grup urządzeń  | Yes   | Nie        |
| Aktualizowanie grup urządzeń  | Tak   | Nie        |
| Usuwanie grupy urządzeń  | Tak   | Nie        |
| Tworzenie reguł          | Yes   | Nie        |
| Reguły aktualizacji          | Tak   | Nie        |
| Usuń reguły          | Tak   | Nie        |
| Tworzenie zadań           | Tak   | Nie        |
| Rozwiązanie Update SIM management | Tak   | Nie        |

Domyślnie użytkownik, który wdrożono rozwiązanie jest przypisywany **administratora** roli i właściciela aplikacji usługi Azure Active Directory. Jako właściciel aplikacji należy przypisać role do innych użytkowników za pośrednictwem witryny Azure portal. Jeśli chcesz, aby inny użytkownik, aby przypisać role w rozwiązaniu, również muszą zostać ustawione jako właściciel aplikacji w witrynie Azure portal.

> [!NOTE]
> Użytkownik będący wdrożono rozwiązanie **tylko osoba** kto może wyświetlać natychmiast po jego został utworzony. Aby udzielić osobom dostępu umożliwiającego wyświetlanie aplikacji jako tylko do odczytu, administrator lub rolę niestandardową, zobacz następujące instrukcje poniżej na dodawać i usuwać użytkowników.

## <a name="add-or-remove-users"></a>Dodawanie lub usuwanie użytkowników

Jako właściciel aplikacji usługi Azure Active Directory można użyć witryny Azure portal, aby dodać lub usunąć użytkownika do roli z rozwiązania do zdalnego monitorowania. Następujące kroki użycia [aplikacja dla przedsiębiorstw usługi Azure Active Directory](../active-directory/manage-apps/add-application-portal.md#find-your-azure-ad-tenant-application) utworzony podczas wdrażania rozwiązania do zdalnego monitorowania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Sprawdź [użytkownika znajduje się w katalogu](../active-directory/fundamentals/add-users-azure-active-directory.md) używasz. Wybrano katalog używany po zarejestrowaniu w usłudze [akceleratorów rozwiązań IoT Azure Microsoft](https://www.azureiotsolutions.com/Accelerators) lokacji. Nazwa katalogu jest widoczny w prawym górnym rogu [strony](https://www.azureiotsolutions.com/Accelerators).

1. Znajdź **aplikacja dla przedsiębiorstw** dla danego rozwiązania w witrynie Azure portal. Jeden raz, przefiltruj listę, ustawiając **typ aplikacji** do **wszystkie aplikacje**. Wyszukaj nazwę swojej aplikacji w aplikacji. Nazwa aplikacji jest nazwa rozwiązania do zdalnego monitorowania. Na poniższym zrzucie ekranu są nazwy wyświetlane rozwiązań i aplikacji **contoso rm4**.

    ![Aplikacja dla przedsiębiorstw](media/iot-accelerators-remote-monitoring-rbac/appregistration.png)

1. Sprawdź, jesteś właścicielem aplikacji, klikając ją, a następnie klikając polecenie **właścicieli**. Poniższy zrzut ekranu **administrator firmy Contoso** jest właścicielem **contoso rm4** aplikacji:

    ![Właściciele](media/iot-accelerators-remote-monitoring-rbac/owners.png)

    Jeśli nie jesteś właścicielem, musisz poprosić o dodanie Cię do listy istniejących właściciela. Tylko właściciele mogą przypisać role aplikacji takich jak **administratora** lub **tylko do odczytu** innym użytkownikom.

1. Aby wyświetlić listę użytkowników przypisanych do ról w aplikacji, kliknij przycisk **użytkowników i grup**.

1. Aby dodać użytkownika, kliknij przycisk **+ Dodaj użytkownika**, a następnie kliknij przycisk **użytkowników i grup, wybrana żadna** aby wybrać użytkownika z katalogu.

1. Aby przypisać użytkownika do roli, kliknij przycisk **wybierz rolę, wybrana żadna** a następnie wybierz opcję **administratora** lub **tylko do odczytu** rolę dla użytkownika. Kliknij przycisk **wybierz**, a następnie kliknij przycisk **przypisać**.

    ![Wybór roli](media/iot-accelerators-remote-monitoring-rbac/selectrole.png)

1. Użytkownik ma teraz dostęp rozwiązania do zdalnego monitorowania z uprawnieniami, zdefiniowane przez rolę.

1. Możesz usunąć użytkowników z aplikacji na **użytkowników i grup** strony w portalu.

## <a name="create-a-custom-role"></a>Tworzenie roli niestandardowej

Rozwiązania do zdalnego monitorowania zawiera **administratora** i **tylko do odczytu** role po jego pierwszym wdrożeniu. Można dodać ról niestandardowych za pomocą różnych zestawów uprawnień. Aby zdefiniować rolę niestandardową, należy:

- Dodaj nową rolę do aplikacji w witrynie Azure portal.
- Zdefiniuj zasady dla nowej roli w mikrousługach uwierzytelniania i autoryzacji.
- Aktualizowanie interfejsu użytkownika sieci web rozwiązania.

### <a name="define-a-custom-role-in-the-azure-portal"></a>Zdefiniuj niestandardową rolę w witrynie Azure portal

Poniżej opisano sposób dodawania roli do aplikacji w usłudze Azure Active Directory. W tym przykładzie utworzysz nową rolę, która umożliwia członkom tworzenie, aktualizowanie i usuwanie urządzeń w rozwiązaniu do zdalnego monitorowania.

1. Znajdź **rejestracji aplikacji** dla danego rozwiązania w witrynie Azure portal. Nazwa aplikacji jest nazwa rozwiązania do zdalnego monitorowania. Na poniższym zrzucie ekranu są nazwy wyświetlane rozwiązań i aplikacji **contoso rm4**.

    ![Rejestracja aplikacji](media/iot-accelerators-remote-monitoring-rbac/appregistration2.png)

1. Wybierz swoją aplikację, a następnie kliknij przycisk **manifestu**. Możesz zobaczyć dwa istniejące [role aplikacji](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) zdefiniowana dla aplikacji:

    ![Wyświetl manifestu](media/iot-accelerators-remote-monitoring-rbac/viewmanifest.png)

1. Edytuj manifest Aby dodać rolę o nazwie **ManageDevices** jak pokazano w poniższym fragmencie kodu. Potrzebujesz unikatowego ciągu, np. identyfikator GUID dla nowego identyfikatora roli. Możesz wygenerować nowy identyfikator GUID, takich jak przy użyciu usługi [Online GUID Generator](https://www.guidgenerator.com/):

    ```json
    "appRoles": [
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "Admin",
        "id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
        "isEnabled": true,
        "description": "Administrator access to the application",
        "value": "Admin"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "Read Only",
        "id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
        "isEnabled": true,
        "description": "Read only access to device information",
        "value": "ReadOnly"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "ManageDevices",
        "id": "ADD A NEW GUID HERE",
        "isEnabled": true,
        "description": "A new role for the application.",
        "value": "ManageDevices"
      }
    ],
    ```

    Zapisz zmiany.

### <a name="define-a-policy-for-the-new-role"></a>Zdefiniuj zasady dla nowej roli

Po można dodać roli do aplikacji w witrynie Azure portal, musisz zdefiniować zasady w [roles.json](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/auth/Services/data/policies/roles.json) dla roli, która przyznaje uprawnienia wymagane do zarządzania urządzeniami.

1. Klonuj [zdalnego monitorowania Mikrousług](https://github.com/Azure/remote-monitoring-services-dotnet) repozytorium z repozytorium GitHub na maszynie lokalnej.

1. Edytuj **auth/Services/data/policies/roles.json** plik, aby dodać zasady dla **ManageDevices** roli, jak pokazano w poniższym fragmencie kodu. **Identyfikator** i **roli** wartości muszą być zgodne definicji roli w manifeście aplikacji z poprzedniej sekcji. Na liście dozwolonych akcji umożliwia kogoś w **ManageDevices** roli w celu tworzenia, aktualizacji i usuwania urządzeń podłączonych do rozwiązania:

    ```json
    {
    "Items": [
      {
        "Id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
        "Role": "admin",
        "AllowedActions": [
          "UpdateAlarms",
          "DeleteAlarms",
          "CreateDevices",
          "UpdateDevices",
          "DeleteDevices",
          "CreateDeviceGroups",
          "UpdateDeviceGroups",
          "DeleteDeviceGroups",
          "CreateRules",
          "UpdateRules",
          "DeleteRules",
          "CreateJobs",
          "UpdateSimManagement"
        ]
      },
      {
        "Id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
        "Role": "readOnly",
        "AllowedActions": []
      },
      {
        "Id": "GUID FROM APP MANIFEST",
        "Role": "ManageDevices",
        "AllowedActions": [
          "CreateDevices",
          "UpdateDevices",
          "DeleteDevices"
        ]
      }
    ]
    }
    ```

1. Po zakończeniu edycji **Services/data/policies/roles.json** pliku, ponowne skompilowanie i wdrożenie mikrousług uwierzytelniania i autoryzacji, aby Twój akcelerator rozwiązań.

### <a name="how-the-web-ui-enforces-permissions"></a>Jak interfejs użytkownika sieci web wymusza uprawnienia

Korzysta z interfejsu użytkownika sieci web [uwierzytelniania i autoryzacji w mikrousługach](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) Aby określić, jakie czynności użytkownik będzie mógł take i kontrolki są widoczne w interfejsie użytkownika. Na przykład, jeśli rozwiązanie jest nazywany **contoso rm4**, interfejs użytkownika sieci web umożliwia pobranie listy dozwolonych akcji dla bieżącego użytkownika, wysyłając następujące żądanie:

```http
http://contoso-rm4.azurewebsites.net/v1/users/current
headers:
X-Source: true
Authorization: Bearer <JWT Token from ADAL>
```

Dla użytkownika o nazwie **Menedżera urządzeń** w **ManageDevices** roli odpowiedzi zawiera następujące dane JSON w treści:

```json
{
  "Id": "userIdExample",
  "Email": "devicemanager@contoso.com",
  "Name": "Device Manager",
  "AllowedActions": [
    "CreateDevices",
    "UpdateDevices",
    "DeleteDevices"
  ]
}
```

Poniższy fragment kodu z [deviceDelete.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/pages/devices/flyouts/deviceDelete/deviceDelete.js) w [interfejs użytkownika sieci web](https://github.com/Azure/pcs-remote-monitoring-webui/) pokazuje, jak uprawnienia są wymuszane deklaratywne:

```json
<FlyoutContent>
  <Protected permission={permissions.deleteDevices}>
    <form className="device-delete-container" onSubmit={this.deleteDevices}>
      ...
    </form>
  </Protected>
</FlyoutContent>
```

Aby uzyskać więcej informacji, zobacz [chronione składniki](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/shared/protected/README.md). Można zdefiniować dodatkowe uprawnienia w [authModel.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/services/models/authModels.js) pliku.

### <a name="how-the-microservices-enforce-permissions"></a>Jak mikrousługi wymusić uprawnień

Mikrousługi także Sprawdź uprawnienia, aby zapewnić ochronę przed nieautoryzowanym żądań interfejsu API. Kiedy mikrousługi odbiera żądania interfejsu API, dekoduje i sprawdza poprawność tokenu JWT, aby uzyskać identyfikator użytkownika i uprawnienia skojarzone z rolą użytkownika.

Poniższy fragment kodu z [DevicesController.cs](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/iothub-manager/WebService/v1/Controllers/DevicesController.cs) w pliku [mikrousług Menedżera IoTHub](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager), pokazuje, jak są wymuszane uprawnienia:

```csharp
[HttpDelete("{id}")]
[Authorize("DeleteDevices")]
public async Task DeleteAsync(string id)
{
    await this.devices.DeleteAsync(id);
}
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób opartej na rolach dostęp kontrolki są implementowane w akceleratora rozwiązania monitorowania zdalnego.

Zobacz [Konfigurowanie kontroli dostępu w Eksploratorze usługi Time Series Insights](iot-accelerators-remote-monitoring-rbac-tsi.md) uzyskać informacji na temat zarządzania dostępem do Eksploratora usługi Time Series Insights w akceleratora rozwiązania monitorowania zdalnego.

Aby uzyskać obszerniejszych informacji koncepcyjnych związanych akceleratora rozwiązania monitorowania zdalnego, zobacz [Architektura zdalnego monitorowania](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Aby uzyskać więcej informacji na temat dostosowywania rozwiązania do zdalnego monitorowania, zobacz [dostosowywanie i ponowne wdrażanie mikrousługi](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->
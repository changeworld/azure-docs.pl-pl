---
title: Kontrola dostępu do zdalnego monitorowania — Platforma Azure | Dokumenty firmy Microsoft
description: Ten artykuł zawiera informacje o tym, jak skonfigurować formanty dostępu oparte na rolach (RBAC) w akceleratorze rozwiązań do zdalnego monitorowania
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: b0c9699bccbb539c9617fac2f3296483139e7188
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67203154"
---
# <a name="configure-role-based-access-controls-in-the-remote-monitoring-solution-accelerator"></a>Konfigurowanie kontroli dostępu opartych na rolach w akceleratorze rozwiązań zdalnego monitorowania

Ten artykuł zawiera informacje dotyczące konfigurowania formantów dostępu opartych na rolach w akceleratorze rozwiązań do zdalnego monitorowania. Formanty dostępu oparte na rolach umożliwiają ograniczenie dostępu poszczególnych użytkowników do określonych funkcji rozwiązania.

## <a name="default-settings"></a>Ustawienia domyślne

Podczas pierwszego wdrażania rozwiązania do zdalnego monitorowania istnieją dwie role: **Administrator** i **Tylko do odczytu**.

Każdy użytkownik w roli **administrator** ma pełny dostęp do rozwiązania, w tym następujące uprawnienia poniżej. Użytkownik w roli **Tylko do odczytu** będzie miał dostęp tylko do wyświetlania rozwiązania.

| Uprawnienie            | Administrator | Tylko do odczytu |
|----------------       |-------|-----------|
| Zobacz rozwiązanie         | Tak   | Tak       |
| Aktualizowanie alarmów         | Tak   | Nie        |
| Usuwanie alarmów         | Tak   | Nie        |
| Tworzenie urządzeń        | Tak   | Nie        |
| Aktualizowanie urządzeń        | Tak   | Nie        |
| Usuwanie urządzeń        | Tak   | Nie        |
| Tworzenie grup urządzeń  | Tak   | Nie        |
| Aktualizowanie grup urządzeń  | Tak   | Nie        |
| Usuwanie grup urządzeń  | Tak   | Nie        |
| Tworzenie reguł          | Tak   | Nie        |
| Aktualizuj reguły          | Tak   | Nie        |
| Usuń reguły          | Tak   | Nie        |
| Tworzenie zadań           | Tak   | Nie        |
| Aktualizowanie zarządzania kartą SIM | Tak   | Nie        |

Domyślnie użytkownik, który wdrożył rozwiązanie, jest automatycznie przypisywany roli **administratora** i jest właścicielem aplikacji usługi Azure Active Directory. Jako właściciel aplikacji możesz przypisać role innym użytkownikom za pośrednictwem witryny Azure portal. Jeśli chcesz, aby inny użytkownik przypisał role w rozwiązaniu, muszą one również być ustawione jako właściciel aplikacji w witrynie Azure portal.

> [!NOTE]
> Użytkownik, który wdrożył rozwiązanie jest **jedyną osobą,** która może wyświetlić go natychmiast po jego utworzeniu. Aby udzielić innym dostępu do wyświetlania aplikacji jako roli Tylko do odczytu, Administrator lub Rola niestandardowa, zobacz poniższe wskazówki dotyczące dodawania lub usuwania użytkowników.

## <a name="add-or-remove-users"></a>Dodawanie lub usuwanie użytkowników

Jako właściciel aplikacji usługi Azure Active Directory możesz użyć witryny Azure Portal, aby dodać lub usunąć użytkownika do roli z rozwiązania do zdalnego monitorowania. Poniższe kroki używają [aplikacji przedsiębiorstwa usługi Azure Active Directory,](../active-directory/manage-apps/add-application-portal.md#find-your-azure-ad-tenant-application) która została utworzona podczas wdrażania rozwiązania do zdalnego monitorowania.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

1. Sprawdź, czy [użytkownik znajduje się w katalogu,](../active-directory/fundamentals/add-users-azure-active-directory.md) którego używasz. Wybrano katalog do użycia po zalogowaniu się do [witryny akceleratorów rozwiązań IoT platformy Microsoft Azure.](https://www.azureiotsolutions.com/Accelerators) Nazwa katalogu jest widoczna w prawym górnym rogu [strony](https://www.azureiotsolutions.com/Accelerators).

1. Znajdź **aplikację Enterprise** dla rozwiązania w witrynie Azure portal. Tam przefiltruj listę, ustawiając **typ aplikacji** na **Wszystkie aplikacje**. Wyszukaj aplikację według nazwy aplikacji. Nazwa aplikacji to nazwa rozwiązania do zdalnego monitorowania. Na poniższym zrzucie ekranu nazwy wyświetlane rozwiązania i aplikacji są **contoso-rm4**.

    ![Aplikacja dla przedsiębiorstw](media/iot-accelerators-remote-monitoring-rbac/appregistration.png)

1. Sprawdź, czy jesteś właścicielem aplikacji, klikając aplikację, a następnie klikając pozycję **Właściciele**. Na poniższym zrzucie ekranu **administrator contoso** jest właścicielem aplikacji **contoso-rm4:**

    ![Właściciele](media/iot-accelerators-remote-monitoring-rbac/owners.png)

    Jeśli nie jesteś właścicielem, musisz poprosić istniejącego właściciela o dodanie Cię do listy. Tylko właściciele mogą przypisywać role aplikacji, takie jak **Administrator** lub **Tylko do odczytu,** innym użytkownikom.

1. Aby wyświetlić listę użytkowników przypisanych do ról w aplikacji, kliknij pozycję **Użytkownicy i grupy**.

1. Aby dodać użytkownika, kliknij przycisk **+ Dodaj użytkownika**, a następnie kliknij pozycję **Użytkownicy i grupy, opcję Brak zaznaczone,** aby wybrać użytkownika z katalogu.

1. Aby przypisać użytkownika do roli, kliknij pozycję **Wybierz rolę, Opcję Brak zaznaczone** i wybierz dla użytkownika rolę **Administrator** lub **Tylko do odczytu.** Kliknij **pozycję Zaznacz**, a następnie kliknij pozycję **Przypisz**.

    ![Wybór roli](media/iot-accelerators-remote-monitoring-rbac/selectrole.png)

1. Użytkownik może teraz uzyskać dostęp do rozwiązania zdalnego monitorowania z uprawnieniami zdefiniowanymi przez rolę.

1. Użytkownicy z aplikacji można usunąć na stronie **Użytkownicy i grupy** w portalu.

## <a name="create-a-custom-role"></a>Tworzenie roli niestandardowej

Rozwiązanie do zdalnego monitorowania obejmuje role **Administrator** i **Tylko do odczytu,** gdy jest po raz pierwszy wdrożony. Można dodać role niestandardowe z różnymi zestawami uprawnień. Aby zdefiniować rolę niestandardową, należy:

- Dodaj nową rolę do aplikacji w witrynie Azure portal.
- Zdefiniuj zasady dla nowej roli w mikrousługi uwierzytelniania i autoryzacji.
- Zaktualizuj internetowy interfejs użytkownika rozwiązania.

### <a name="define-a-custom-role-in-the-azure-portal"></a>Definiowanie roli niestandardowej w witrynie Azure portal

W poniższych krokach opisano sposób dodawania roli do aplikacji w usłudze Azure Active Directory. W tym przykładzie utworzysz nową rolę, która umożliwia członkom tworzenie, aktualizowanie i usuwanie urządzeń w rozwiązaniu zdalnego monitorowania.

1. Znajdź **rejestrację aplikacji** dla rozwiązania w witrynie Azure portal. Nazwa aplikacji to nazwa rozwiązania do zdalnego monitorowania. Na poniższym zrzucie ekranu nazwy wyświetlane rozwiązania i aplikacji są **contoso-rm4**.

    ![Rejestracja aplikacji](media/iot-accelerators-remote-monitoring-rbac/app-registration-2.png)

1. Wybierz aplikację, a następnie kliknij przycisk **Manifest**. Możesz zobaczyć dwie istniejące [role aplikacji](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) zdefiniowane dla aplikacji:

    ![Wyświetl manifest](media/iot-accelerators-remote-monitoring-rbac/view-manifest.png)

1. Edytuj manifest, aby dodać rolę o nazwie **ManageDevices,** jak pokazano w poniższym urywek. Potrzebny jest unikatowy ciąg, taki jak identyfikator GUID dla nowego identyfikatora roli. Nowy identyfikator GUID można wygenerować za pomocą usługi, takiej jak [generator identyfikatorów GUID online:](https://www.guidgenerator.com/)

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

### <a name="define-a-policy-for-the-new-role"></a>Definiowanie zasad dla nowej roli

Po dodaniu roli do aplikacji w witrynie Azure portal, należy zdefiniować zasady w [roles.json](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/auth/Services/data/policies/roles.json) dla roli, która przypisuje uprawnienia potrzebne do zarządzania urządzeniami.

1. Sklonuj repozytorium [mikrousług monitorowania zdalnego](https://github.com/Azure/remote-monitoring-services-dotnet) z usługi GitHub na komputerze lokalnym.

1. Edytuj plik **eru/Services/data/policies/roles.json,** aby dodać zasady dotyczące roli **ManageDevices,** jak pokazano w poniższym urywek. Wartości **identyfikatora** i **roli** muszą być zgodne z definicją roli w manifeście aplikacji z poprzedniej sekcji. Lista dozwolonych akcji umożliwia innej osobie w roli **ManageDevices** tworzenie, aktualizowanie i usuwanie urządzeń podłączonych do rozwiązania:

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

1. Po zakończeniu edytowania **pliku Usługi/data/policies/roles.json** należy przebudować i ponownie wdrożyć mikrousługi uwierzytelniania i autoryzacji do akceleratora rozwiązań.

### <a name="how-the-web-ui-enforces-permissions"></a>Jak internetowy interfejs użytkownika wymusza uprawnienia

Internetowy interfejs użytkownika używa [mikrousług uwierzytelniania i autoryzacji,](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) aby określić, jakie akcje użytkownik może podjąć i jakie formanty są widoczne w interfejsie użytkownika. Na przykład jeśli rozwiązanie jest wywoływane **contoso-rm4**, interfejs użytkownika sieci web pobiera listę dozwolonych akcji dla bieżącego użytkownika, wysyłając następujące żądanie:

```http
http://contoso-rm4.azurewebsites.net/v1/users/current
headers:
X-Source: true
Authorization: Bearer <JWT Token from ADAL>
```

Dla użytkownika o nazwie **Menedżer urządzeń** w **manageDevices** roli odpowiedź zawiera następujące JSON w treści:

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

Poniższy fragment kodu z [deviceDelete.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/pages/devices/flyouts/deviceDelete/deviceDelete.js) w [interfejsie użytkownika sieci Web](https://github.com/Azure/pcs-remote-monitoring-webui/) pokazuje, jak uprawnienia są wymuszane deklaratywnie:

```json
<FlyoutContent>
  <Protected permission={permissions.deleteDevices}>
    <form className="device-delete-container" onSubmit={this.deleteDevices}>
      ...
    </form>
  </Protected>
</FlyoutContent>
```

Aby uzyskać więcej informacji, zobacz [Chronione składniki](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/shared/protected/README.md). Można zdefiniować dodatkowe uprawnienia w pliku [authModel.js.](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/services/models/authModels.js)

### <a name="how-the-microservices-enforce-permissions"></a>Jak mikrousług wymuszaj uprawnienia

Mikrousługi również sprawdzić uprawnienia do ochrony przed nieautoryzowanymi żądaniami interfejsu API. Gdy mikrousługa odbiera żądanie interfejsu API, dekoduje i sprawdza poprawność tokenu JWT, aby uzyskać identyfikator użytkownika i uprawnienia skojarzone z rolą użytkownika.

Następujący fragment kodu z pliku [DevicesController.cs](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/iothub-manager/WebService/v1/Controllers/DevicesController.cs) w [mikrousługach Usługi IoTHub Manager](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager)pokazuje, jak uprawnienia są wymuszane:

```csharp
[HttpDelete("{id}")]
[Authorize("DeleteDevices")]
public async Task DeleteAsync(string id)
{
    await this.devices.DeleteAsync(id);
}
```

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak formanty dostępu oparte na rolach są implementowane w akceleratorze rozwiązań zdalnego monitorowania.

Zobacz [Konfigurowanie kontrolek dostępu dla Eksploratora Usługi Time Series Insights,](iot-accelerators-remote-monitoring-rbac-tsi.md) aby uzyskać informacje na temat zarządzania dostępem do eksploratora Usługi Time Series Insights w akceleratorze rozwiązań do zdalnego monitorowania.

Aby uzyskać więcej informacji koncepcyjnych dotyczących akceleratora rozwiązań do zdalnego monitorowania, zobacz [Architektura zdalnego monitorowania](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Aby uzyskać więcej informacji na temat dostosowywania rozwiązania do zdalnego monitorowania, zobacz [Dostosowywanie i ponowne wdrożenie mikrousług](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->
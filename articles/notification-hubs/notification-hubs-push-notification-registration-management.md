---
title: Zarządzanie rejestracją
description: W tym temacie wyjaśniono, jak zarejestrować urządzenia z usługą notification hubs w celu odbierania powiadomień wypychanych.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: fd0ee230-132c-4143-b4f9-65cef7f463a1
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.author: jowargo
ms.date: 04/08/2019
ms.openlocfilehash: fffa6784702f239e0af0e9e88a4b9937d20b86ed
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67488625"
---
# <a name="registration-management"></a>Zarządzanie rejestracją

## <a name="overview"></a>Omówienie

W tym temacie wyjaśniono, jak zarejestrować urządzenia z usługą notification hubs w celu odbierania powiadomień wypychanych. Tematu opisano rejestracje na wysokim poziomie, a następnie wprowadza dwa wzorce główne dla rejestracji urządzeń: rejestrowanie urządzenia bezpośrednio do Centrum powiadomień i rejestrowanie przy użyciu zaplecza aplikacji.

## <a name="what-is-device-registration"></a>Co to jest rejestracja urządzenia

Rejestracja urządzenia w Centrum powiadomień odbywa się przy użyciu **rejestracji** lub **instalacji**.

### <a name="registrations"></a>Rejestracje

Rejestracji kojarzy uchwyt usług powiadomień platformy (PNS, Domain Name System) dla urządzenia przy użyciu tagów i ewentualnie szablonu. Identyfikator ChannelURI, token urządzenia lub identyfikator rejestracji usługi FCM, może być dojściem systemu powiadomień platformy. Znaczniki są używane do kierowania powiadomień do odpowiednich zestawów uchwyty urządzenia. Aby uzyskać więcej informacji, zobacz [routingu i wyrażenia tagu](notification-hubs-tags-segment-push-message.md). Szablony są używane do implementowania przekształcenia na rejestracji. Aby uzyskać więcej informacji, zobacz [Szablony](notification-hubs-templates-cross-platform-push-messages.md).

> [!NOTE]
> Usługa Azure Notification Hubs obsługuje maksymalnie 60 tagów na każdym urządzeniu.

### <a name="installations"></a>Instalacje

Instalacja jest rozszerzonych właściwości powiązanych z rejestracji, który zawiera zbiór wypychania. Jest to najnowsze i najlepsze podejście do rejestrowania urządzeń. Jednak nie jest obsługiwany przez zestaw SDK platformy .NET po stronie klienta ([zestawu SDK usługi Notification Hub dla operacji zaplecza](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)) jeszcze wdrożone.  Oznacza to, czy rejestrujesz na urządzeniu klienckim, trzeba użyć [interfejsu API REST centrów powiadomień](https://docs.microsoft.com/rest/api/notificationhubs/create-overwrite-installation) podejście do obsługi instalacji. Jeśli używasz usługi wewnętrznej bazy danych powinno być możliwe do użycia [zestawu SDK usługi Notification Hub dla operacji zaplecza](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Poniżej przedstawiono niektóre kluczowe zalety korzystania z instalacji:

- Tworzenie lub aktualizowanie instalacji jest w pełni idempotentne. Dlatego możesz ponowić próbę go bez żadnych problemów dotyczących rejestracji zduplikowane.
- Model instalacji obsługuje format specjalny znacznik (`$InstallationId:{INSTALLATION_ID}`), który umożliwia wysłanie powiadomienia bezpośrednio do określonego urządzenia. Na przykład, jeśli kod aplikacji Ustawia identyfikator instalacji `joe93developer` dla tego konkretnego urządzenia, deweloper można wskazać to urządzenie podczas wysyłania powiadomień do `$InstallationId:{joe93developer}` tagu. Dzięki temu można pod kątem określonego urządzenia bez konieczności dodatkowego kodowania.
- Przy użyciu instalacji umożliwia także wykonaj aktualizacje częściowe rejestracji. Zażądano częściową aktualizację instalacji przy użyciu metody PATCH [standard poprawki JSON](https://tools.ietf.org/html/rfc6902). Jest to przydatne, gdy chcesz aktualizacji tagów na rejestracji. Nie masz ściągnąć całego rejestracji, a następnie ponownie Wyślij ponownie wszystkie poprzednie tagi.

Instalacja może zawierać następujące właściwości. Aby uzyskać pełną listę właściwości instalacji, zobacz [utworzyć ani zastąpić instalacji przy użyciu interfejsu API REST](https://docs.microsoft.com/rest/api/notificationhubs/create-overwrite-installation) lub [właściwości instalacji](https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.installation).

```json
// Example installation format to show some supported properties
{
    installationId: "",
    expirationTime: "",
    tags: [],
    platform: "",
    pushChannel: "",
    ………
    templates: {
        "templateName1" : {
            body: "",
            tags: [] },
        "templateName2" : {
            body: "",
            // Headers are for Windows Store only
            headers: {
                "X-WNS-Type": "wns/tile" }
            tags: [] }
    },
    secondaryTiles: {
        "tileId1": {
            pushChannel: "",
            tags: [],
            templates: {
                "otherTemplate": {
                    bodyTemplate: "",
                    headers: {
                        ... }
                    tags: [] }
            }
        }
    }
}
```

> [!NOTE]
> Domyślnie instalacji i rejestracji wygasa.

Instalacji i rejestracji musi zawierać prawidłowe dojście systemu powiadomień platformy dla każdego urządzenia/kanału. Ponieważ dojść systemu powiadomień platformy można uzyskać tylko w aplikacji klienta, na urządzeniu, jest jednym ze wzorców, można zarejestrować bezpośrednio na tym urządzeniu, za pomocą aplikacji klienckiej. Z drugiej strony zagadnienia dotyczące zabezpieczeń i logikę biznesową, związane z tagami może wymagać zarządzać rejestracją urządzenia w aplikacji zaplecza.

> [!NOTE]
> Interfejs API instalacji nie obsługuje usługi Baidu (mimo że z interfejsu API rejestracji nie). 

### <a name="templates"></a>Szablony

Jeśli chcesz używać [szablony](notification-hubs-templates-cross-platform-push-messages.md), instalacji urządzeń zawiera również wszystkie szablony związane z danym urządzeniem w formacie JSON formatu (Zobacz przykład powyżej). Nazwy szablonów pomóc różne szablony docelowego dla tego samego urządzenia.

Nazwa każdego szablonu mapuje treści szablonu i opcjonalny zestaw znaczników. Ponadto każdej z platform może mieć właściwości dodatkowe szablony. Windows Store (za pomocą usługi WNS) i Windows Phone 8 (przy użyciu usługi MPNS), aby uzyskać dodatkowy zestaw nagłówków może być częścią szablonu. W przypadku usługi APNs można ustawić właściwości wygaśnięcia, albo stałą lub wyrażeniem szablonu. Aby uzyskać pełną listę, zobacz właściwości instalacji [utworzyć ani zastąpić instalację z użyciem usług REST](https://docs.microsoft.com/rest/api/notificationhubs/create-overwrite-installation) tematu.

### <a name="secondary-tiles-for-windows-store-apps"></a>Pomocniczy kafelków dla aplikacji Windows Store

Dla aplikacji klienckich Windows Store wysyłanie powiadomień do kafelków dodatkowej jest taka sama jak wysyłając główną. Jest to również obsługiwane w instalacjach. Kafelki dodatkowej ma inny identyfikator ChannelUri, która zestawu SDK w aplikacji klienckiej obsługuje w sposób niewidoczny dla użytkownika.

Słownik SecondaryTiles używa tego samego TileId, który służy do tworzenia obiektu SecondaryTiles w aplikacji Windows Store. Podobnie jak w przypadku podstawowego identyfikator ChannelUri, ChannelUris dodatkowej Kafelki można zmienić w dowolnym momencie. Aby zachować instalacji w Centrum powiadomień, zaktualizować, urządzenie musi odświeżać je bieżącego ChannelUris dodatkowej kafelków.

## <a name="registration-management-from-the-device"></a>Zarządzanie rejestracją urządzenia

Podczas zarządzania rejestracji urządzenia z aplikacji klienckich, zaplecze odpowiada tylko do wysyłania powiadomień. Aplikacje klienckie aktualnych dojść systemu powiadomień platformy, a następnie zarejestrować tagów. Poniższy obraz przedstawia tego wzorca.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

Urządzenia najpierw pobiera dojście systemu powiadomień platformy z systemu powiadomień platformy, a następnie rejestruje się w usłudze notification hubs bezpośrednio. Po pomyślnej rejestracji zaplecze aplikacji może wysłać powiadomienia przeznaczonych dla tej rejestracji. Aby uzyskać więcej informacji o tym, jak wysyłać powiadomienia, zobacz [routingu i wyrażenia tagu](notification-hubs-tags-segment-push-message.md).

W tym przypadku używasz tylko prawa do nasłuchiwania na dostęp do usługi notification hubs z urządzenia. Aby uzyskać więcej informacji, zobacz [zabezpieczeń](notification-hubs-push-notification-security.md).

Rejestracja urządzenia jest najprostsza metoda, ale ma pewne wady:

- Aplikacja kliencka może aktualizować tylko jego tagi aplikacja jest aktywna. Na przykład jeśli użytkownik ma dwa urządzenia, które rejestrują znaczniki powiązane z portem zespołów, podczas pierwszego urządzenia rejestruje tag dodatkowe (na przykład Seahawks), drugiego urządzenia nie będzie odbierać powiadomienia o Seahawks aż do aplikacji na urządzeniu z drugiego wykonane po raz drugi. Bardziej ogólnie rzecz biorąc gdy tagów jest narażony na wielu urządzeniach, zarządzanie tagi z wewnętrznej bazy danych jest pożądane opcji.
- Ponieważ aplikacje może stać się celem ataku, zabezpieczania rejestracji do określonych tagów wymaga szczególną ostrożność należy zachować zgodnie z opisem w sekcji "zabezpieczenia na poziomie tagu."

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>Przykładowy kod, aby zarejestrować za pomocą Centrum powiadomień z urządzeniem przy użyciu instalacji

W tym momencie jest to obsługiwane tylko za pomocą [interfejsu API REST centrów powiadomień](https://docs.microsoft.com/rest/api/notificationhubs/create-overwrite-installation).

Można również użyć przy użyciu metody PATCH [standard poprawki JSON](https://tools.ietf.org/html/rfc6902) aktualizacji instalacji.

```
class DeviceInstallation
{
    public string installationId { get; set; }
    public string platform { get; set; }
    public string pushChannel { get; set; }
    public string[] tags { get; set; }
}

private async Task<HttpStatusCode> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation,
        string hubName, string listenConnectionString)
{
    if (deviceInstallation.installationId == null)
        return HttpStatusCode.BadRequest;

    // Parse connection string (https://msdn.microsoft.com/library/azure/dn495627.aspx)
    ConnectionStringUtility connectionSaSUtil = new ConnectionStringUtility(listenConnectionString);
    string hubResource = "installations/" + deviceInstallation.installationId + "?";
    string apiVersion = "api-version=2015-04";

    // Determine the targetUri that we will sign
    string uri = connectionSaSUtil.Endpoint + hubName + "/" + hubResource + apiVersion;

    //=== Generate SaS Security Token for Authorization header ===
    // See, https://msdn.microsoft.com/library/azure/dn495627.aspx
    string SasToken = connectionSaSUtil.getSaSToken(uri, 60);

    using (var httpClient = new HttpClient())
    {
        string json = JsonConvert.SerializeObject(deviceInstallation);

        httpClient.DefaultRequestHeaders.Add("Authorization", SasToken);

        var response = await httpClient.PutAsync(uri, new StringContent(json, System.Text.Encoding.UTF8, "application/json"));
        return response.StatusCode;
    }
}

var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

string installationId = null;
var settings = ApplicationData.Current.LocalSettings.Values;

// If we have not stored an installation id in application data, create and store as application data.
if (!settings.ContainsKey("__NHInstallationId"))
{
    installationId = Guid.NewGuid().ToString();
    settings.Add("__NHInstallationId", installationId);
}

installationId = (string)settings["__NHInstallationId"];

var deviceInstallation = new DeviceInstallation
{
    installationId = installationId,
    platform = "wns",
    pushChannel = channel.Uri,
    //tags = tags.ToArray<string>()
};

var statusCode = await CreateOrUpdateInstallationAsync(deviceInstallation, 
                    "<HUBNAME>", "<SHARED LISTEN CONNECTION STRING>");

if (statusCode != HttpStatusCode.Accepted)
{
    var dialog = new MessageDialog(statusCode.ToString(), "Registration failed. Installation Id : " + installationId);
    dialog.Commands.Add(new UICommand("OK"));
    await dialog.ShowAsync();
}
else
{
    var dialog = new MessageDialog("Registration successful using installation Id : " + installationId);
    dialog.Commands.Add(new UICommand("OK"));
    await dialog.ShowAsync();
}
```

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>Przykładowy kod, aby zarejestrować za pomocą Centrum powiadomień z urządzeniem za pomocą rejestracji

Te metody, Utwórz lub zaktualizuj rejestracji dla urządzenia, na którym są wywoływane. Oznacza to, aby można było zaktualizować, dojście lub tagi, należy zaznaczyć opcję zastąpienia całej rejestracji. Należy pamiętać, że rejestracje przejściowy, dlatego zawsze powinien mieć niezawodne magazynu z bieżącej tagi, które wymaga określonego urządzenia.

```
// Initialize the Notification Hub
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

// The Device id from the PNS
var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

// If you are registering from the client itself, then store this registration id in device
// storage. Then when the app starts, you can check if a registration id already exists or not before
// creating.
var settings = ApplicationData.Current.LocalSettings.Values;

// If we have not stored a registration id in application data, store in application data.
if (!settings.ContainsKey("__NHRegistrationId"))
{
    // make sure there are no existing registrations for this push handle (used for iOS and Android)    
    string newRegistrationId = null;
    var registrations = await hub.GetRegistrationsByChannelAsync(pushChannel.Uri, 100);
    foreach (RegistrationDescription registration in registrations)
    {
        if (newRegistrationId == null)
        {
            newRegistrationId = registration.RegistrationId;
        }
        else
        {
            await hub.DeleteRegistrationAsync(registration);
        }
    }

    newRegistrationId = await hub.CreateRegistrationIdAsync();

    settings.Add("__NHRegistrationId", newRegistrationId);
}

string regId = (string)settings["__NHRegistrationId"];

RegistrationDescription registration = new WindowsRegistrationDescription(pushChannel.Uri);
registration.RegistrationId = regId;
registration.Tags = new HashSet<string>(YourTags);

try
{
    await hub.CreateOrUpdateRegistrationAsync(registration);
}
catch (Microsoft.WindowsAzure.Messaging.RegistrationGoneException e)
{
    settings.Remove("__NHRegistrationId");
}
```

## <a name="registration-management-from-a-backend"></a>Zarządzanie rejestracją z zaplecza

Zarządzanie rejestracje z zaplecza wymaga tworzenia dodatkowego kodu. Zaktualizowano systemu powiadomień platformy obsługują zaplecza każdym uruchomieniu aplikacji (wraz z tagi i szablony) i wewnętrznej bazy danych, musisz zaktualizować to dojście w Centrum powiadomień, należy podać aplikacji z urządzenia. Poniższy obraz przedstawia w tym projekcie.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

Zalety zarządzania rejestracje z zaplecza obejmują możliwość modyfikowania tagi do rejestracji, nawet wtedy, gdy odpowiednia aplikacja na urządzeniu jest nieaktywny i uwierzytelniania aplikacji klienckiej przed dodaniem tag do rejestracji.

### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>Przykładowy kod, aby zarejestrować za pomocą Centrum powiadomień z zaplecza przy użyciu instalacji

Urządzenie klienckie nadal pobiera jego dojściem systemu powiadomień platformy i Instalacja odpowiednich właściwości jako przed i wywołuje niestandardowy interfejs API do wewnętrznej bazy danych, które można wykonać rejestrację i autoryzować znaczników itp. Można korzystać z zaplecza [zestawu SDK usługi Notification Hub dla operacji zaplecza](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Można również użyć przy użyciu metody PATCH [standard poprawki JSON](https://tools.ietf.org/html/rfc6902) aktualizacji instalacji.

```
// Initialize the Notification Hub
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

// Custom API on the backend
public async Task<HttpResponseMessage> Put(DeviceInstallation deviceUpdate)
{

    Installation installation = new Installation();
    installation.InstallationId = deviceUpdate.InstallationId;
    installation.PushChannel = deviceUpdate.Handle;
    installation.Tags = deviceUpdate.Tags;

    switch (deviceUpdate.Platform)
    {
        case "mpns":
            installation.Platform = NotificationPlatform.Mpns;
            break;
        case "wns":
            installation.Platform = NotificationPlatform.Wns;
            break;
        case "apns":
            installation.Platform = NotificationPlatform.Apns;
            break;
        case "fcm":
            installation.Platform = NotificationPlatform.Fcm;
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }


    // In the backend we can control if a user is allowed to add tags
    //installation.Tags = new List<string>(deviceUpdate.Tags);
    //installation.Tags.Add("username:" + username);

    await hub.CreateOrUpdateInstallationAsync(installation);

    return Request.CreateResponse(HttpStatusCode.OK);
}
```

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>Przykładowy kod, aby zarejestrować za pomocą Centrum powiadomień z urządzeniem przy użyciu Identyfikatora rejestracji

Z zaplecza aplikacji możesz wykonywać podstawowe operacje CRUDS rejestracji. Na przykład:

```
var hub = NotificationHubClient.CreateClientFromConnectionString("{connectionString}", "hubName");

// create a registration description object of the correct type, e.g.
var reg = new WindowsRegistrationDescription(channelUri, tags);

// Create
await hub.CreateRegistrationAsync(reg);

// Get by id
var r = await hub.GetRegistrationAsync<RegistrationDescription>("id");

// update
r.Tags.Add("myTag");

// update on hub
await hub.UpdateRegistrationAsync(r);

// delete
await hub.DeleteRegistrationAsync(r);
```

Wewnętrznej bazy danych musi obsługiwać współbieżności między aktualizacjami rejestracji. Usługa Service Bus oferuje mechanizmu kontroli optymistycznej współbieżności dla Zarządzanie rejestracją. Na poziomie protokołu HTTP ten sposób jest implementowany przy użyciu elementu ETag na operacje zarządzania rejestracją. Ta funkcja jest przezroczysty używany przez Microsoft SDKs, który zgłosić wyjątek, jeśli aktualizacja zostanie odrzucone ze względów współbieżności. Zaplecze aplikacji jest odpowiedzialny za obsługę tych wyjątków i ponawianie próby aktualizacji, jeśli to konieczne.

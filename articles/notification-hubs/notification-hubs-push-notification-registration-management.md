---
title: Zarządzanie rejestracją
description: W tym temacie wyjaśniono, jak zarejestrować urządzenia w centrach powiadomień w celu otrzymywania powiadomień wypychanych.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: fd0ee230-132c-4143-b4f9-65cef7f463a1
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 03/17/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/08/2019
ms.openlocfilehash: 00de9c803ef796eda8da609a4009e0a8cfcb3664
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455371"
---
# <a name="registration-management"></a>Zarządzanie rejestracją

W tym temacie wyjaśniono, jak zarejestrować urządzenia w centrach powiadomień w celu otrzymywania powiadomień wypychanych. W temacie opisano rejestracje na wysokim poziomie, a następnie wprowadza dwa główne wzorce rejestracji urządzeń: rejestrowanie z urządzenia bezpośrednio do centrum powiadomień i rejestrowanie za pośrednictwem wewnętrznej bazy danych aplikacji.

## <a name="what-is-device-registration"></a>Co to jest rejestracja urządzenia

Rejestracja urządzenia w Centrum powiadomień odbywa się za pomocą **rejestracji** lub **instalacji**.

### <a name="registrations"></a>Rejestracje

Rejestracja kojarzy dojście usługi powiadomień platformy (PNS) dla urządzenia z tagami i ewentualnie szablonem. Dojście PNS może być ChannelURI, token urządzenia lub identyfikator rejestracji FCM. Tagi są używane do kierowania powiadomień do prawidłowego zestawu uchwytów urządzeń. Aby uzyskać więcej informacji, zobacz [Routing i Wyrażenia znaczników](notification-hubs-tags-segment-push-message.md). Szablony są używane do implementowania transformacji na rejestrację. Aby uzyskać więcej informacji, zobacz [Szablony](notification-hubs-templates-cross-platform-push-messages.md).

> [!NOTE]
> Usługa Azure Notification Hubs obsługuje maksymalnie 60 tagów na urządzenie.

### <a name="installations"></a>Instalacje

Instalacja jest rozszerzona rejestracja, która zawiera worek właściwości związanych z wypychaniem. Jest to najnowsze i najlepsze podejście do rejestracji urządzeń. Jednak nie jest obsługiwany przez po stronie klienta .NET SDK[(Notification Hub SDK dla operacji wewnętrznej bazy danych)](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)jeszcze.  Oznacza to, że jeśli rejestrujesz się z samego urządzenia klienckiego, należy użyć [podejścia interfejsu API rest usługi notification hubs](/rest/api/notificationhubs/create-overwrite-installation) do obsługi instalacji. Jeśli używasz usługi wewnętrznej bazy danych, powinieneś być w stanie używać [SDK Centrum powiadomień do operacji wewnętrznej bazy danych](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Oto kilka kluczowych zalet korzystania z instalacji:

- Tworzenie lub aktualizowanie instalacji jest w pełni idempotentne. Możesz więc ponowić próbę bez żadnych obaw dotyczących zduplikowanych rejestracji.
- Model instalacyjny obsługuje`$InstallationId:{INSTALLATION_ID}`specjalny format znacznika ( ), który umożliwia wysyłanie powiadomień bezpośrednio do określonego urządzenia. Jeśli na przykład kod aplikacji ustawi identyfikator `joe93developer` instalacji dla tego konkretnego urządzenia, deweloper może kierować reklamy na to urządzenie podczas wysyłania powiadomienia do tagu. `$InstallationId:{joe93developer}` Dzięki temu można kierować na określone urządzenie bez konieczności wykonywania dodatkowych kodowania.
- Korzystanie z instalacji umożliwia również wykonywanie aktualizacji rejestracji częściowej. Częściowa aktualizacja instalacji jest wymagana za pomocą metody PATCH przy użyciu [standardu JSON-Patch](https://tools.ietf.org/html/rfc6902). Jest to przydatne, gdy chcesz zaktualizować tagi w rejestracji. Nie musisz ściągnąć całej rejestracji, a następnie ponownie wysłać wszystkie poprzednie tagi.

Instalacja może zawierać następujące właściwości. Aby uzyskać pełną listę właściwości instalacji, zobacz [Tworzenie lub zastępowanie instalacji za pomocą interfejsu API REST](/rest/api/notificationhubs/create-overwrite-installation) lub właściwości [instalacji](/dotnet/api/microsoft.azure.notificationhubs.installation).

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
> Domyślnie rejestracje i instalacje nie wygasają.

Rejestracje i instalacje muszą zawierać prawidłowy uchwyt pns dla każdego urządzenia/kanału. Ponieważ uchwyty usługi PNS można uzyskać tylko w aplikacji klienckiej na urządzeniu, jeden wzorzec jest zarejestrować się bezpośrednio na tym urządzeniu za pomocą aplikacji klienckiej. Z drugiej strony zagadnienia dotyczące zabezpieczeń i logiki biznesowej związane z tagami mogą wymagać zarządzania rejestracją urządzenia w zapleczu aplikacji.

> [!NOTE]
> Interfejs API instalacji nie obsługuje usługi Baidu (chociaż nie ma interfejsu API rejestracji). 

### <a name="templates"></a>Szablony

Jeśli chcesz użyć [szablonów, instalacja](notification-hubs-templates-cross-platform-push-messages.md)urządzenia zawiera również wszystkie szablony skojarzone z tym urządzeniem w formacie JSON (patrz przykład powyżej). Nazwy szablonów pomagają kierować różne szablony dla tego samego urządzenia.

Każda nazwa szablonu jest mapowana na treść szablonu i opcjonalny zestaw znaczników. Ponadto każda platforma może mieć dodatkowe właściwości szablonu. W przypadku Sklepu Windows (przy użyciu WNS) i Windows Phone 8 (przy użyciu mpns) dodatkowy zestaw nagłówków może być częścią szablonu. W przypadku sieci APN można ustawić właściwość wygaśnięcia na stałą lub wyrażenie szablonu. Aby uzyskać pełną listę właściwości instalacji, zobacz [Tworzenie lub zastępowanie instalacji za pomocą tematu REST.](/rest/api/notificationhubs/create-overwrite-installation)

### <a name="secondary-tiles-for-windows-store-apps"></a>Kafelki pomocnicze dla aplikacji ze Sklepu Windows

W przypadku aplikacji klienckich ze Sklepu Windows wysyłanie powiadomień do kafelków pomocniczych jest takie samo jak wysyłanie ich do podstawowego. Jest to również obsługiwane w instalacjach. Kafelki pomocnicze mają inny ChannelUri, który SDK w aplikacji klienckiej obsługuje w sposób przezroczysty.

Słownik SecondaryTiles używa tego samego identyfikatora kafeide, który jest używany do tworzenia obiektu SecondaryTiles w aplikacji ze Sklepu Windows. Podobnie jak w przypadku podstawowego ChannelUri, ChannelUris płytek pomocniczych można zmienić w dowolnym momencie. Aby instalacje w centrum powiadomień były aktualizowane, urządzenie musi odświeżyć je przy obliczu bieżących rozmiarów kafelków pomocniczych.

## <a name="registration-management-from-the-device"></a>Zarządzanie rejestracją z urządzenia

Podczas zarządzania rejestracją urządzenia z aplikacji klienckich wewnętrznej bazy danych jest odpowiedzialny tylko za wysyłanie powiadomień. Aplikacje klienckie aktualizują obsługę usług PNS i rejestrują tagi. Poniższy rysunek ilustruje ten wzór.

![Rejestracja z urządzenia](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

Urządzenie najpierw pobiera dojście PNS z usługi PNS, a następnie rejestruje się bezpośrednio w centrum powiadomień. Po pomyślnym zakończeniu rejestracji zaplecze aplikacji może wysłać powiadomienie dotyczące tej rejestracji. Aby uzyskać więcej informacji na temat wysyłania powiadomień, zobacz [Routing i Wyrażenia znaczników](notification-hubs-tags-segment-push-message.md).

W takim przypadku używasz tylko prawa nasłuchiwać, aby uzyskać dostęp do centrów powiadomień z urządzenia. Aby uzyskać więcej informacji, zobacz [Bezpieczeństwo](notification-hubs-push-notification-security.md).

Rejestracja z urządzenia jest najprostszą metodą, ale ma pewne wady:

- Aplikacja kliencka może aktualizować swoje tagi tylko wtedy, gdy aplikacja jest aktywna. Na przykład, jeśli użytkownik ma dwa urządzenia, które rejestrują tagi związane z drużynami sportowymi, gdy pierwsze urządzenie zarejestruje się na dodatkowy tag (na przykład Seahawks), drugie urządzenie nie otrzyma powiadomień o Seahawks, dopóki aplikacja na drugim urządzeniu nie zostanie wykonano po raz drugi. Ogólnie rzecz biorąc, gdy na tagi ma wpływ wiele urządzeń, zarządzanie tagami z wewnętrznej bazy danych jest pożądaną opcją.
- Ponieważ aplikacje mogą być hacked, zabezpieczenie rejestracji do określonych tagów wymaga dodatkowej opieki, jak wyjaśniono w artykule [Bezpieczeństwo](notification-hubs-push-notification-security.md).

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>Przykładowy kod do zarejestrowania się w centrum powiadomień z urządzenia przy użyciu instalacji

Obecnie jest to obsługiwane tylko przy użyciu [interfejsu API REST centrum powiadomień.](/rest/api/notificationhubs/create-overwrite-installation)

Można również użyć metody PATCH przy użyciu [standardu JSON-Patch](https://tools.ietf.org/html/rfc6902) do aktualizacji instalacji.

```csharp
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

// If we have not stored an installation ID in application data, create and store as application data.
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

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>Przykładowy kod do zarejestrowania się w centrum powiadomień z urządzenia przy użyciu rejestracji

Te metody utworzyć lub zaktualizować rejestrację dla urządzenia, na którym są wywoływane. Oznacza to, że aby zaktualizować dojście lub znaczniki, należy zastąpić całą rejestrację. Pamiętaj, że rejestracje są przejściowe, więc zawsze powinieneś mieć niezawodny magazyn z bieżącymi tagami, których potrzebuje określone urządzenie.

```csharp
// Initialize the Notification Hub
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

// The Device ID from the PNS
var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

// If you are registering from the client itself, then store this registration ID in device
// storage. Then when the app starts, you can check if a registration ID already exists or not before
// creating.
var settings = ApplicationData.Current.LocalSettings.Values;

// If we have not stored a registration ID in application data, store in application data.
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

Zarządzanie rejestracjami z wewnętrznej bazy danych wymaga zapisania dodatkowego kodu. Aplikacja z urządzenia musi dostarczyć zaktualizowany dojście usługi PNS do wewnętrznej bazy danych przy każdym uruchomieniu aplikacji (wraz z tagami i szablonami), a wewnętrznej bazy danych musi zaktualizować ten uchwyt w centrum powiadomień. Poniższy obraz ilustruje ten projekt.

![Zarządzanie rejestracją](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

Zalety zarządzania rejestracjami z wewnętrznej bazy danych obejmują możliwość modyfikowania tagów do rejestracji, nawet wtedy, gdy odpowiednia aplikacja na urządzeniu jest nieaktywna, oraz do uwierzytelniania aplikacji klienckiej przed dodaniem tagu do jej rejestracji.

### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>Przykładowy kod do zarejestrowania się w centrum powiadomień z wewnętrznej bazy danych przy użyciu instalacji

Urządzenie klienckie nadal pobiera jego dojście PNS i odpowiednie właściwości instalacji, jak poprzednio i wywołuje niestandardowy interfejs API na wewnętrznej wejście, które można wykonać rejestracji i autoryzowania tagów itp. Wewnętrznej bazy danych można wykorzystać [SDK Centrum powiadomień dla operacji wewnętrznej bazy danych](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Można również użyć metody PATCH przy użyciu [standardu JSON-Patch](https://tools.ietf.org/html/rfc6902) do aktualizacji instalacji.

```csharp
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

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>Przykładowy kod do zarejestrowania się w centrum powiadomień z urządzenia przy użyciu identyfikatora rejestracji

Z wewnętrznej bazy danych aplikacji można wykonywać podstawowe operacje CRUDS na rejestracjach. Przykład:

```csharp
var hub = NotificationHubClient.CreateClientFromConnectionString("{connectionString}", "hubName");

// create a registration description object of the correct type, e.g.
var reg = new WindowsRegistrationDescription(channelUri, tags);

// Create
await hub.CreateRegistrationAsync(reg);

// Get by ID
var r = await hub.GetRegistrationAsync<RegistrationDescription>("id");

// update
r.Tags.Add("myTag");

// update on hub
await hub.UpdateRegistrationAsync(r);

// delete
await hub.DeleteRegistrationAsync(r);
```

Wewnętrznej bazy danych musi obsługiwać współbieżności między aktualizacjami rejestracji. Usługa Service Bus oferuje optymistyczną kontrolę współbieżności do zarządzania rejestracją. Na poziomie HTTP jest to realizowane przy użyciu ETag w operacjach zarządzania rejestracją. Ta funkcja jest w sposób przezroczysty używana przez pakiety MICROSOFT SDK, które zgłaszają wyjątek, jeśli aktualizacja zostanie odrzucona ze względu na współbieżność. Wewnętrznej bazy danych aplikacji jest odpowiedzialny za obsługę tych wyjątków i ponowić próbę aktualizacji, jeśli to konieczne.

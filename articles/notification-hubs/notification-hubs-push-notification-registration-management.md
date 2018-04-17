---
title: Zarządzanie rejestracji
description: W tym temacie wyjaśniono, jak zarejestrować urządzenia z usługą notification hubs w celu odbierania powiadomień wypychanych.
services: notification-hubs
documentationcenter: .net
author: ysxu
manager: erikre
editor: ''
ms.assetid: fd0ee230-132c-4143-b4f9-65cef7f463a1
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 969f6b9654200b7f742b6405faa2cff2b13ba537
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="registration-management"></a>Zarządzanie rejestracją
## <a name="overview"></a>Przegląd
W tym temacie wyjaśniono, jak zarejestrować urządzenia z usługą notification hubs w celu odbierania powiadomień wypychanych. Temat opisuje rejestracji na wysokim poziomie, a następnie wprowadza dwa główne wzorce do rejestracji urządzeń: rejestrację z urządzenia bezpośrednio w Centrum powiadomień i rejestrowanie za pomocą wewnętrznej bazy danych aplikacji. 

## <a name="what-is-device-registration"></a>Co to jest rejestracja urządzenia
Rejestracja urządzenia z Centrum powiadomień odbywa się przy użyciu **rejestracji** lub **instalacji**.

#### <a name="registrations"></a>Rejestracje
Rejestracja kojarzy dojścia usługi powiadomień platformy (PNS) dla urządzenia z tagami i prawdopodobnie szablonu. Dojście systemu powiadomień platformy można ChannelURI, token urządzenia lub identyfikator rejestracji usługi GCM. Tagi są używane do kierowania powiadomień do poprawny zestaw dojść urządzeń. Aby uzyskać więcej informacji, zobacz [routingu i wyrażeń tagów](notification-hubs-tags-segment-push-message.md). Szablony są używane do implementowania na rejestracji przekształcenia. Aby uzyskać więcej informacji, zobacz [szablony](notification-hubs-templates-cross-platform-push-messages.md).

#### <a name="installations"></a>Instalacje
Instalacja jest rozszerzonych właściwości powiązanych z rejestrację, która zawiera zbiór wypychania. Jest to najnowsze i najlepsze rozwiązanie z rejestracją urządzenia. Jednak nie jest obsługiwany przez klienta po stronie zestawu SDK programu .NET ([SDK Centrum powiadomień dla wewnętrznej bazy danych operacji](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)) jeszcze.  Oznacza to, w przypadku rejestracji na urządzeniu klienckim, należy użyć [interfejsu API REST centra powiadomień](https://msdn.microsoft.com/library/mt621153.aspx) podejście do obsługi instalacji. Jeśli używasz usługi wewnętrznej bazy danych powinno być możliwe do użycia [SDK Centrum powiadomień dla wewnętrznej bazy danych operacji](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Poniżej przedstawiono niektóre kluczowe zalety korzystania z instalacji:

* Tworzenie lub aktualizowanie instalacji jest w pełni idempotentności. Dlatego możesz ponowić próbę jej bez żadnych problemów dotyczących rejestracji duplikatów.
* Model instalacji ułatwia czy poszczególnych wypchnięć - przeznaczonych dla określonego urządzenia. Tag systemu **"$InstallationId: [identyfikator installationId]"** jest automatycznie dodawany z każdej instalacji na podstawie rejestracji. Dlatego należy wywołać Wyślij do tego znacznika do określonego urządzenia bez konieczności dodatkowe kodowania.
* Przy użyciu instalacji umożliwia także aktualizacje częściowe rejestracji. Zażądano częściowej aktualizacji instalacji z metody poprawki przy użyciu [standard JSON poprawki](https://tools.ietf.org/html/rfc6902). Jest to szczególnie przydatne, jeśli chcesz zaktualizować tagów do rejestracji. Nie trzeba rozwiń całego rejestracji, a następnie ponownie Wyślij ponownie wszystkie poprzednie tagi.

Instalacja produktu może zawierać następujące właściwości. Aby uzyskać pełną listę właściwości instalacji, zobacz [utworzyć ani zastąpić instalacji z interfejsu API REST](https://msdn.microsoft.com/library/azure/mt621153.aspx) lub [właściwości instalacji](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.installation_properties.aspx).

    // Example installation format to show some supported properties
    {,
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



Należy pamiętać, że rejestracji i instalacje domyślnie nie wygasają już.

Rejestracje i instalacji musi zawierać prawidłowe dojście systemu powiadomień platformy dla każdego urządzenia/kanału. Ponieważ dojść systemu powiadomień platformy można uzyskać tylko w aplikacji klienta, na urządzeniu, jeden wzorzec jest zarejestrować bezpośrednio na tym urządzeniu z aplikacji klienckiej. Z drugiej strony, zagadnienia dotyczące zabezpieczeń i powiązane z tagami logiki biznesowej może być konieczne umożliwia zarządzanie rejestracją urządzeń w aplikacji zaplecza. 

#### <a name="templates"></a>Szablony
Jeśli chcesz użyć [szablony](notification-hubs-templates-cross-platform-push-messages.md), instalacja urządzenia również przechowywać wszystkie szablony skojarzone z tym urządzeniem w formacie JSON formatu (Zobacz przykład powyżej). Nazwy szablonów pomocy różnych szablonów docelowego dla tego samego urządzenia.

Należy pamiętać, że każda nazwa szablonu jest mapowana treści szablonu i opcjonalny zestaw tagów. Ponadto każdej z platform może mieć właściwości dodatkowe szablonu. Dla Sklepu Windows (przy użyciu usługi WNS) i Windows Phone 8 (przy użyciu usługi MPNS) dodatkowe ustawienia nagłówków może być częścią szablonu. W przypadku usługi APNs można ustawić właściwości wygaśnięcia stałą lub wyrażeniem szablonu. Aby uzyskać pełną listę można znaleźć właściwości instalacji, [Tworzenie lub Zastąp instalacji REST](https://msdn.microsoft.com/library/azure/mt621153.aspx) tematu.

#### <a name="secondary-tiles-for-windows-store-apps"></a>Dodatkowej Kafelki aplikacji do Sklepu Windows
Dla aplikacji ze Sklepu Windows klienta wysyłanie powiadomień do dodatkowej Kafelki jest taka sama jak ich wysłaniem do główną. Ta jest obsługiwana w przypadku instalacji. Należy zauważyć, że dodatkowej Kafelki różnych ChannelUri, która zestawu SDK w aplikacji klienta obsługuje przezroczysty.

Słownik SecondaryTiles używa tego samego TileId, który służy do tworzenia obiektu SecondaryTiles w aplikacji ze Sklepu Windows.
Podobnie jak w przypadku podstawowego ChannelUri, ChannelUris dodatkowej Kafelki można zmienić w dowolnym momencie. Aby zachować instalacje zaktualizowano Centrum powiadomień, urządzenia należy odświeżyć je bieżącego ChannelUris dodatkowej kafelków.

## <a name="registration-management-from-the-device"></a>Zarządzanie rejestrację z urządzenia
Podczas zarządzania rejestracji urządzeń z aplikacji klienta, wewnętrznej bazy danych tylko jest odpowiedzialny za wysyłanie powiadomień. Aplikacje klienta aktualności dojść systemu powiadomień platformy i zarejestruj tagów. Poniżej przedstawiono tego wzorca.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

Urządzenia najpierw pobiera dojście systemu powiadomień platformy z systemu powiadomień platformy, a następnie rejestruje bezpośrednio z Centrum powiadomień. Po pomyślnym rejestracji zaplecze aplikacji może wysłać powiadomienia targeting tej rejestracji. Aby uzyskać więcej informacji na temat wysyłania powiadomień, zobacz [routingu i wyrażeń tagów](notification-hubs-tags-segment-push-message.md).
Należy pamiętać, że w takim przypadku użyjesz nasłuchiwał tylko prawa dostępu z urządzenia z usługi notification hubs. Aby uzyskać więcej informacji, zobacz [zabezpieczeń](notification-hubs-push-notification-security.md).

Rejestracja urządzenia jest najprostszą metodą, ale ma kilka wad.
Pierwszy wadą jest to, że aplikacja kliencka aktualizować tylko jego tagi, gdy aplikacja jest aktywna. Na przykład jeśli użytkownik ma dwa urządzenia, które rejestrować tagi związane z zespołów sport przy pierwszym urządzeniem rejestruje dodatkowe tagów (na przykład Seahawks), drugiego urządzenia nie otrzymają powiadomienia o Seahawks momentu aplikacji na drugiego urządzenia wykonywane po raz drugi. Ogólnie rzecz biorąc gdy tagi jest narażony na wielu urządzeniach, zarządzanie tagów z wewnętrznej bazy danych jest pożądane opcji.
Drugi zwrot management rejestracji w aplikacji klienta jest, ponieważ aplikacje mogą być zaatakowane, zabezpieczanie rejestracji w znacznikach wymaga szczególną uwagę, zgodnie z objaśnieniem w sekcji "zabezpieczenia na poziomie tagu."

#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>Przykładowy kod zarejestrować się w Centrum powiadomień z urządzeniem przy użyciu instalacji
W tej chwili ta jest obsługiwana tylko przy użyciu [interfejsu API REST centra powiadomień](https://msdn.microsoft.com/library/mt621153.aspx).

Można również użyć przy użyciu metody poprawki [standard JSON poprawki](https://tools.ietf.org/html/rfc6902) aktualizacji instalacji.

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

    // If we have not stored a installation id in application data, create and store as application data.
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



#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>Przykładowy kod zarejestrować się w Centrum powiadomień z urządzenia przy użyciu rejestracji
Te metody Utwórz lub zaktualizuj rejestracji dla urządzenia, na którym są one nazywane. Oznacza to, że aby można było zaktualizować, dojście lub znaczniki, musi zastąpić cały rejestracji. Należy pamiętać, że rejestracje przejściowy, dlatego powinien zawsze mieć niezawodny magazyn z bieżącym tagi, które wymaga określonego urządzenia.

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


## <a name="registration-management-from-a-backend"></a>Zarządzanie rejestracji z poziomu zaplecza
Zarządzanie rejestracje z wewnętrznej bazy danych wymaga zapisywania dodatkowy kod. Zaktualizowane systemu powiadomień platformy dojścia do wewnętrznej bazy danych zawsze uruchomieniu aplikacji (wraz z tagami i szablonów) i wewnętrznej bazy danych należy zaktualizować ta dojścia w Centrum powiadomień, należy podać aplikacji z urządzenia. Poniżej przedstawiono w tym projekcie.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

Zalety zarządzania z wewnętrznej bazy danych rejestracji obejmują możliwość modyfikowania tagów do rejestracji, nawet wtedy, gdy jest nieaktywne z odpowiedniej aplikacji na urządzeniu i uwierzytelniania aplikacji klienckiej przed dodaniem tag do rejestracji.

#### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>Przykładowy kod zarejestrować się w Centrum powiadomień z zaplecza, przy użyciu instalacji
Urządzenie klienckie nadal pobiera jego dojście systemu powiadomień platformy i właściwości instalacji w odpowiednich jako przed i wywołuje niestandardowego interfejsu API do wewnętrznej bazy danych, można dokonać rejestracji i autoryzować tagi itp. Wewnętrznej bazy danych można wykorzystać [SDK Centrum powiadomień dla wewnętrznej bazy danych operacji](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Można również użyć przy użyciu metody poprawki [standard JSON poprawki](https://tools.ietf.org/html/rfc6902) aktualizacji instalacji.

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
            case "gcm":
                installation.Platform = NotificationPlatform.Gcm;
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


#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>Przykładowy kod zarejestrować się w Centrum powiadomień z urządzenia przy użyciu identyfikatora rejestracji
Z poziomu zaplecza aplikacji można wykonywać podstawowe operacje CRUDS rejestracji. Na przykład:

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


Wewnętrznej bazy danych musi obsługiwać współbieżności między aktualizacjami rejestracji. Usługa Service Bus udostępnia optymistyczne sterowanie współbieżnością zarządzania rejestracji. Na poziomie protokołu HTTP ten sposób jest implementowany z użyciem ETag na operacji zarządzania rejestracji. Ta funkcja służy niewidocznie SDKs firmy Microsoft, które zgłosić wyjątek, jeśli aktualizacji zostało odrzucone ze względów współbieżności. Zaplecze aplikacji jest odpowiedzialny za obsługę tych wyjątków i ponawianie próby aktualizacji, jeśli jest to wymagane.


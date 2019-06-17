---
title: Jak używać usługi Notification Hubs za pomocą języka Java
description: Dowiedz się, jak używać usługi Azure Notification Hubs z zaplecza Java.
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 4c3f966d-0158-4a48-b949-9fa3666cb7e4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: java
ms.devlang: java
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 993eafd5a0b74be706d13fe8e06483c223f81eb6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61461208"
---
# <a name="how-to-use-notification-hubs-from-java"></a>Jak używać usługi Notification Hubs w języku Java

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

W tym temacie opisano kluczowe funkcje nowej w pełni obsługiwana oficjalne Azure powiadomień Centrum zestawu SDK Java.
Ten projekt jest projektem typu open source i wyświetlić cały kod zestawu SDK u [Zestaw SDK Java].

Ogólnie rzecz biorąc, są dostępne wszystkie funkcje usługi Notification Hubs z zaplecza Java/PHP/Python/Ruby przy użyciu interfejsu REST Centrum powiadomień, zgodnie z opisem w temacie w witrynie MSDN [interfejsów API REST usługi Notification Hubs](https://msdn.microsoft.com/library/dn223264.aspx). Ten zestaw Java SDK udostępnia otokę alokowania elastycznego za pośrednictwem tych interfejsów REST w języku Java.

Zestaw SDK aktualnie obsługuje:

* CRUD w usłudze Notification Hubs
* CRUD rejestracji
* Zarządzania instalacji
* Import/Export rejestracji
* Regularnie wysyła
* Wysyła zaplanowane
* Operacje asynchroniczne za pomocą języka Java NIO
* Obsługiwane platformy: Usługi APNS (iOS), usługi FCM (Android), WNS (Windows Store apps), MPNS (Windows Phone), usługi ADM (Amazon Kindle Fire), Baidu (Android bez usługi Google)

## <a name="sdk-usage"></a>Użycie zestawu SDK

### <a name="compile-and-build"></a>Kompilacji i budowania

Użyj [Maven]

Aby skompilować:

    mvn package

## <a name="code"></a>Kod

### <a name="notification-hub-cruds"></a>Notification Hub CRUDs

**Utwórz NamespaceManager:**

    ```java
    NamespaceManager namespaceManager = new NamespaceManager("connection string")
    ```

**Tworzenie Centrum powiadomień:**

    ```java
    NotificationHubDescription hub = new NotificationHubDescription("hubname");
    hub.setWindowsCredential(new WindowsCredential("sid","key"));
    hub = namespaceManager.createNotificationHub(hub);
    ```

 LUB

    ```java
    hub = new NotificationHub("connection string", "hubname");
    ```

**Pobierz Centrum powiadomień:**

    ```java
    hub = namespaceManager.getNotificationHub("hubname");
    ```

**Zaktualizuj Centrum powiadomień:**

    ```java
    hub.setMpnsCredential(new MpnsCredential("mpnscert", "mpnskey"));
    hub = namespaceManager.updateNotificationHub(hub);
    ```

**Usuwanie Centrum powiadomień:**

    ```java
    namespaceManager.deleteNotificationHub("hubname");
    ```

### <a name="registration-cruds"></a>CRUDs rejestracji

**Tworzenie klienta Centrum powiadomień:**

    ```java
    hub = new NotificationHub("connection string", "hubname");
    ```

**Utwórz rejestrację Windows:**

    ```java
    WindowsRegistration reg = new WindowsRegistration(new URI(CHANNELURI));
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);
    ```

**Utwórz rejestrację systemu iOS:**

    ```java
    AppleRegistration reg = new AppleRegistration(DEVICETOKEN);
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);
    ```

Podobnie można utworzyć rejestracji dla systemu Android (FCM), Windows Phone (MPNS) i urządzenia Kindle Fire (ADM).

**Tworzenie szablonu rejestracji:**

    ```java
    WindowsTemplateRegistration reg = new WindowsTemplateRegistration(new URI(CHANNELURI), WNSBODYTEMPLATE);
    reg.getHeaders().put("X-WNS-Type", "wns/toast");
    hub.createRegistration(reg);
    ```

**Tworzenie rejestracji za pomocą Utwórz identyfikator rejestracji + upsert wzorca:**

Usuwa duplikaty ze względu na żadnych odpowiedzi utracone, jeśli przechowywania identyfikatorów rejestracji na urządzeniu:

    ```java
    String id = hub.createRegistrationId();
    WindowsRegistration reg = new WindowsRegistration(id, new URI(CHANNELURI));
    hub.upsertRegistration(reg);
    ```

**Aktualizowanie rejestracji:**

    ```java
    hub.updateRegistration(reg);
    ```

**Usuwanie rejestracji:**

    ```java
    hub.deleteRegistration(regid);
    ```

**Zapytania rejestracji:**

* **Pobierz pojedynczy rejestracji:**

    ```java
    hub.getRegistration(regid);
    ```

* **Pobierz wszystkie rejestracje w Centrum:**

    ```java
    hub.getRegistrations();
    ```

* **Pobierz rejestracji za pomocą tagu:**

    ```java
    hub.getRegistrationsByTag("myTag");
    ```

* **Pobierz rejestracji przez kanał:**

    ```java
    hub.getRegistrationsByChannel("devicetoken");
    ```

Wszystkie zapytania kolekcji obsługuje tokeny $top i kontynuacji.

### <a name="installation-api-usage"></a>Użycie instalacji interfejsu API

Instalacja interfejsu API jest alternatywny mechanizm umożliwiający Zarządzanie rejestracją. Zamiast zajmować się utrzymywaniem wiele rejestracji, które nie są proste i można łatwo przeprowadzić nieprawidłowo lub nieefektywnie, użytkownik może teraz używać jednej instalacji obiektu.

Instalacja zawiera wszystko, czego potrzebujesz: wypychanie kanału (tokenu urządzenia), tagi, szablony, Kafelki dodatkowej (w przypadku usługi WNS i APNS). Nie trzeba wywołać usługę numer płatnika już — po prostu wygenerować identyfikatora GUID lub innego identyfikatora, Zachowaj ją na urządzeniu oraz wysyłać wewnętrzną bazą danych wraz z kanału wypychania (tokenu urządzenia).

Do wewnętrznej bazy danych, należy wykonywać tylko jedno wywołanie `CreateOrUpdateInstallation`; w pełni są idempotentne, więc możesz ponowić próbę, jeśli to konieczne.

Jako przykład Amazon Kindle Fire:

    ```java
    Installation installation = new Installation("installation-id", NotificationPlatform.Adm, "adm-push-channel");
    hub.createOrUpdateInstallation(installation);
    ```

Jeśli chcesz go zaktualizować:

    ```java
    installation.addTag("foo");
    installation.addTemplate("template1", new InstallationTemplate("{\"data\":{\"key1\":\"$(value1)\"}}","tag-for-template1"));
    installation.addTemplate("template2", new InstallationTemplate("{\"data\":{\"key2\":\"$(value2)\"}}","tag-for-template2"));
    hub.createOrUpdateInstallation(installation);
    ```

W przypadku zaawansowanych scenariuszy Użyj funkcji częściową aktualizację, co umożliwia modyfikowanie tylko określonej właściwości obiektu instalacji. Częściową aktualizację jest podzbiorem operacje poprawki JSON, które możesz uruchomić względem obiektu instalacji.

    ```java
    PartialUpdateOperation addChannel = new PartialUpdateOperation(UpdateOperationType.Add, "/pushChannel", "adm-push-channel2");
    PartialUpdateOperation addTag = new PartialUpdateOperation(UpdateOperationType.Add, "/tags", "bar");
    PartialUpdateOperation replaceTemplate = new PartialUpdateOperation(UpdateOperationType.Replace, "/templates/template1", new InstallationTemplate("{\"data\":{\"key3\":\"$(value3)\"}}","tag-for-template1")).toJson());
    hub.patchInstallation("installation-id", addChannel, addTag, replaceTemplate);
    ```

Usuń instalacji:

    ```java
    hub.deleteInstallation(installation.getInstallationId());
    ```

`CreateOrUpdate`, `Patch`, i `Delete` są ostatecznie spójny z `Get`. Żądana operacja po prostu przechodzi do kolejki systemu podczas wywołania i jest wykonywane w tle. Pobierz nie jest przeznaczony dla scenariusza głównego środowiska uruchomieniowego, ale tylko dla debugowania i rozwiązywania problemów, wydajność jest ściśle ograniczana przez usługę.

Wyślij przepływ w przypadku instalacji jest taka sama, jak w przypadku rejestracji. Docelowy powiadomienia do danej instalacji — po prostu użyj tagu "InstallationId: {desired-id}". W przypadku ten kod jest:

    ```java
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.sendNotification(n, "InstallationId:{installation-id}");
    ```

Dla jednego z kilku szablonów:

    ```java
    Map<String, String> prop =  new HashMap<String, String>();
    prop.put("value3", "some value");
    Notification n = Notification.createTemplateNotification(prop);
    hub.sendNotification(n, "InstallationId:{installation-id} && tag-for-template1");
    ```

### <a name="schedule-notifications-available-for-standard-tier"></a>Planuj powiadomienia (dostępne w warstwie standardowa)

Taka sama jak regularnego wysyłania, ale jeden dodatkowy parametr - samych parametrach scheduledTime, informujący o tym, kiedy dostarczania powiadomienia. Usługa akceptuje jakimś momencie now + 5 minut i teraz + 7 dni.

**Zaplanuj powiadomienie macierzystych Windows:**

    ```java
    Calendar c = Calendar.getInstance();
    c.add(Calendar.DATE, 1);
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.scheduleNotification(n, c.getTime());
    ```

### <a name="importexport-available-for-standard-tier"></a>Import/Export (dostępne w warstwie standardowa)

Może być konieczne wykonanie operacji zbiorczej dla rejestracji. Zazwyczaj jest integrację z innego systemu lub dużych poprawki aktualizacji tagów. Nie zaleca się korzystania z usługi flow pobieranie/aktualizowanie, jeśli biorących udział tysiące rejestracji. Możliwość importowania/eksportowania systemu zaprojektowano w celu pokrycia w scenariuszu. Dostęp do kontenera obiektów blob w ramach konta usługi storage udostępni jako źródło przychodzące dane i lokalizację danych wyjściowych.

**Przesyłanie zadania eksportu:**

    ```java
    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ExportRegistrations);
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);
    ```

**Przesyłanie zadania importu:**

    ```java
    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ImportCreateRegistrations);
    job.setImportFileUri("input file uri with SAS signature");
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);
    ```

**Zaczekaj, aż zadanie jest wykonywane:**

    ```java
    while(true){
        Thread.sleep(1000);
        job = hub.getNotificationHubJob(job.getJobId());
        if(job.getJobStatus() == NotificationHubJobStatus.Completed)
            break;
    }
    ```

**Pobierz wszystkie zadania:**

    ```java
    List<NotificationHubJob> jobs = hub.getAllNotificationHubJobs();
    ```

**Identyfikator URI przy użyciu sygnatura dostępu Współdzielonego:**

 Ten adres URL jest adres URL pliku obiektu blob lub kontenera obiektów blob oraz zestaw parametrów, takich jak uprawnienia i czas wygaśnięcia oraz podpis wszystkie te rzeczy zostało nawiązane przy użyciu klucza sygnatury dostępu Współdzielonego konta. Zestaw SDK Java usługi Azure Storage ma rozbudowane możliwości, takie jak tworzenie tych identyfikatorów. Jako alternatywę proste, zapoznaj się z `ImportExportE2E` testu klasy (z lokalizacji usługi GitHub), która zawiera implementację podstawowego i compact algorytm podpisywania.

### <a name="send-notifications"></a>Wysyłanie powiadomień

Obiekt powiadomień jest po prostu treści z nagłówkami, niektóre metody narzędziowe pomoc w tworzeniu obiektów powiadomienia natywne i szablonu.

* **Windows Store i Windows Phone 8.1 (bez użycia platformy Silverlight)**

    ```java
    String toast = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello from Java!</text></binding></visual></toast>";
    Notification n = Notification.createWindowsNotification(toast);
    hub.sendNotification(n);
    ```

* **iOS**

    ```java
    String alert = "{\"aps\":{\"alert\":\"Hello from Java!\"}}";
    Notification n = Notification.createAppleNotification(alert);
    hub.sendNotification(n);
    ```

* **Android**

    ```java
    String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
    Notification n = Notification.createFcmNotification(message);
    hub.sendNotification(n);
    ```

* **Windows Phone 8.0 i 8.1 Silverlight**

    ```java
    String toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                    "<wp:Toast>" +
                        "<wp:Text1>Hello from Java!</wp:Text1>" +
                    "</wp:Toast> " +
                "</wp:Notification>";
    Notification n = Notification.createMpnsNotification(toast);
    hub.sendNotification(n);
    ```

* **Kindle Fire**

    ```java
    String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
    Notification n = Notification.createAdmNotification(message);
    hub.sendNotification(n);
    ```

* **Wyślij do tagów**
  
    ```java
    Set<String> tags = new HashSet<String>();
    tags.add("boo");
    tags.add("foo");
    hub.sendNotification(n, tags);
    ```

* **Wyślij do wyrażenia tagu**

    ```java
    hub.sendNotification(n, "foo && ! bar");
    ```

* **Wysłać powiadomienia szablonu**

    ```java
    Map<String, String> prop =  new HashMap<String, String>();
    prop.put("prop1", "v1");
    prop.put("prop2", "v2");
    Notification n = Notification.createTemplateNotification(prop);
    hub.sendNotification(n);
    ```

Uruchamianie kodu języka Java powinien teraz wygenerowanie powiadomienie, które pojawiają się na urządzeniu docelowym.

## <a name="next-steps"></a>Następne kroki

W tym temacie pokazano, jak utworzyć proste klienta Java REST dla usługi Notification Hubs. W tym miejscu możesz wykonywać następujące czynności:

* Pobierz pełną [Zestaw SDK Java], który zawiera cały kod zestawu SDK.
* Poeksperymentuj z próbek:
  * [Rozpoczynanie pracy z usługą Notification Hubs]
  * [Wysyłanie najważniejszych wiadomości]
  * [Wysyłanie najważniejszych zlokalizowane wiadomości]
  * [Wysyłanie powiadomień do uwierzytelnionych użytkowników]
  * [Wysyłanie powiadomień na różne platformy do uwierzytelnionych użytkowników]

[Zestaw SDK Java]: https://github.com/Azure/azure-notificationhubs-java-backend
[Get started tutorial]: notification-hubs-ios-apple-push-notification-apns-get-started.md
[Rozpoczynanie pracy z usługą Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Wysyłanie najważniejszych wiadomości]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Wysyłanie najważniejszych zlokalizowane wiadomości]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Wysyłanie powiadomień do uwierzytelnionych użytkowników]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Wysyłanie powiadomień na różne platformy do uwierzytelnionych użytkowników]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Maven]: https://maven.apache.org/

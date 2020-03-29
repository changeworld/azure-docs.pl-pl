---
title: Jak korzystać z usługi Azure Notification Hubs w języku Java
description: Dowiedz się, jak korzystać z usługi Azure Notification Hubs z zaplecza Java.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 4c3f966d-0158-4a48-b949-9fa3666cb7e4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: java
ms.devlang: java
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: d48973cc7c5ed1fc7ae3f96128d488f3f1df3a05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263867"
---
# <a name="how-to-use-notification-hubs-from-java"></a>Jak korzystać z Centrów powiadomień z języka Java

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

W tym temacie opisano kluczowe funkcje nowego w pełni obsługiwanego oficjalnego sdk Java Java Usługi Azure Notification Hub.
Ten projekt jest projektem typu open source i można wyświetlić cały kod SDK w [Java SDK].

Ogólnie rzecz biorąc, można uzyskać dostęp do wszystkich funkcji Centrum powiadomień z zaplecza Java/PHP/Python/Ruby za pomocą interfejsu REST centrum powiadomień, zgodnie z opisem w temacie [MsDN Notification Hubs REST API](https://msdn.microsoft.com/library/dn223264.aspx). Ten java SDK zapewnia cienką otokę nad tymi interfejsami REST w języku Java.

Zestaw SDK obsługuje obecnie:

* CRUD na centrach powiadomień
* CRUD na rejestracje
* Zarządzanie instalacją
* Rejestracje importu/eksportu
* Regularne wysyła
* Zaplanowane wysłanie
* Operacje asynchronizowe przez Java NIO
* Obsługiwane platformy: APNS (iOS), FCM (Android), WNS (aplikacje ze Sklepu Windows), MPNS (Windows Phone), ADM (Amazon Kindle Fire), Baidu (Android bez usług Google)

## <a name="sdk-usage"></a>Użycie SDK

### <a name="compile-and-build"></a>Kompilowanie i tworzenie kompilacji

Użyj [Maven]

Aby zbudować:

    mvn package

## <a name="code"></a>Code

### <a name="notification-hub-cruds"></a>Cruds Centrum powiadomień

**Utwórz menedżer obszaru nazw:**

    ```java
    NamespaceManager namespaceManager = new NamespaceManager("connection string")
    ```

**Utwórz Centrum powiadomień:**

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

**Centrum powiadomień o aktualizacji:**

    ```java
    hub.setMpnsCredential(new MpnsCredential("mpnscert", "mpnskey"));
    hub = namespaceManager.updateNotificationHub(hub);
    ```

**Usuń Centrum powiadomień:**

    ```java
    namespaceManager.deleteNotificationHub("hubname");
    ```

### <a name="registration-cruds"></a>Rejestracja CRUDs

**Utwórz klienta Centrum powiadomień:**

    ```java
    hub = new NotificationHub("connection string", "hubname");
    ```

**Utwórz rejestrację systemu Windows:**

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

Podobnie można utworzyć rejestracje dla systemu Android (FCM), Windows Phone (MPNS) i Kindle Fire (ADM).

**Tworzenie rejestracji szablonów:**

    ```java
    WindowsTemplateRegistration reg = new WindowsTemplateRegistration(new URI(CHANNELURI), WNSBODYTEMPLATE);
    reg.getHeaders().put("X-WNS-Type", "wns/toast");
    hub.createRegistration(reg);
    ```

**Tworzenie rejestracji przy użyciu wzorca create registration ID + upsert:**

Usuwa duplikaty z powodu utraty odpowiedzi, jeśli przechowują identyfikatory rejestracji na urządzeniu:

    ```java
    String id = hub.createRegistrationId();
    WindowsRegistration reg = new WindowsRegistration(id, new URI(CHANNELURI));
    hub.upsertRegistration(reg);
    ```

**Aktualizuj rejestracje:**

    ```java
    hub.updateRegistration(reg);
    ```

**Usuń rejestracje:**

    ```java
    hub.deleteRegistration(regid);
    ```

**Rejestracje kwerend:**

* **Uzyskaj pojedynczą rejestrację:**

    ```java
    hub.getRegistration(regid);
    ```

* **Pobierz wszystkie rejestracje w centrum:**

    ```java
    hub.getRegistrations();
    ```

* **Uzyskaj rejestracje za pomocą tagu:**

    ```java
    hub.getRegistrationsByTag("myTag");
    ```

* **Uzyskaj rejestracje według kanału:**

    ```java
    hub.getRegistrationsByChannel("devicetoken");
    ```

Wszystkie zapytania kolekcji obsługują tokeny $top i kontynuacji.

### <a name="installation-api-usage"></a>Użycie interfejsu API instalacji

Interfejs API instalacji jest alternatywnym mechanizmem zarządzania rejestracją. Zamiast utrzymywania wielu rejestracji, które nie są trywialne i mogą być łatwo wykonane niepoprawnie lub nieefektywnie, jest teraz możliwe użycie pojedynczego obiektu instalacji.

Instalacja zawiera wszystko, czego potrzebujesz: kanał wypychania (token urządzenia), tagi, szablony, kafelki pomocnicze (dla WNS i APNS). Nie musisz już dzwonić do usługi, aby uzyskać identyfikator — wystarczy wygenerować identyfikator GUID lub inny identyfikator, zachować go na urządzeniu i wysłać do wewnętrznej bazy danych wraz z kanałem wypychania (token urządzenia).

Na wewnętrznej stronie wewnętrznej należy wykonać tylko `CreateOrUpdateInstallation`jedno wywołanie ; jest w pełni idempotentny, więc nie krępuj się ponowić próbę w razie potrzeby.

Na przykład amazon Kindle Fire:

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

W przypadku scenariuszy zaawansowanych należy użyć możliwości częściowej aktualizacji, która umożliwia modyfikowanie tylko określonych właściwości obiektu instalacyjnego. Częściowa aktualizacja jest podzbiorem operacji JSON Patch, które można uruchomić względem obiektu instalacji.

    ```java
    PartialUpdateOperation addChannel = new PartialUpdateOperation(UpdateOperationType.Add, "/pushChannel", "adm-push-channel2");
    PartialUpdateOperation addTag = new PartialUpdateOperation(UpdateOperationType.Add, "/tags", "bar");
    PartialUpdateOperation replaceTemplate = new PartialUpdateOperation(UpdateOperationType.Replace, "/templates/template1", new InstallationTemplate("{\"data\":{\"key3\":\"$(value3)\"}}","tag-for-template1")).toJson());
    hub.patchInstallation("installation-id", addChannel, addTag, replaceTemplate);
    ```

Usuń instalację:

    ```java
    hub.deleteInstallation(installation.getInstallationId());
    ```

`CreateOrUpdate`, `Patch`i `Delete` są ostatecznie `Get`zgodne z . Żądana operacja po prostu przechodzi do kolejki systemowej podczas wywołania i jest wykonywana w tle. Get nie jest przeznaczony do głównego środowiska uruchomieniowego scenariusz, ale tylko do celów debugowania i rozwiązywania problemów, jest ściśle ograniczona przez usługę.

Wyślij przepływ dla instalacji jest taki sam jak w przypadku rejestracji. Aby kierować powiadomienia do konkretnej instalacji - wystarczy użyć tagu "InstallationId:{desired-id}". W tym przypadku kod jest:

    ```java
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.sendNotification(n, "InstallationId:{installation-id}");
    ```

W przypadku jednego z kilku szablonów:

    ```java
    Map<String, String> prop =  new HashMap<String, String>();
    prop.put("value3", "some value");
    Notification n = Notification.createTemplateNotification(prop);
    hub.sendNotification(n, "InstallationId:{installation-id} && tag-for-template1");
    ```

### <a name="schedule-notifications-available-for-standard-tier"></a>Powiadomienia o harmonogramie (dostępne dla warstwy STANDARD)

Tak samo jak regularne wysyłanie, ale z jednym dodatkowym parametrem - scheduledTime, który mówi, kiedy powiadomienie powinno zostać dostarczone. Usługa akceptuje dowolny punkt czasu od teraz + 5 minut do teraz + 7 dni.

**Zaplanuj powiadomienie natywne systemu Windows:**

    ```java
    Calendar c = Calendar.getInstance();
    c.add(Calendar.DATE, 1);
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.scheduleNotification(n, c.getTime());
    ```

### <a name="importexport-available-for-standard-tier"></a>Import/eksport (dostępne dla warstwy STANDARD)

Może być konieczne wykonanie operacji zbiorczej względem rejestracji. Zazwyczaj jest to do integracji z innym systemem lub masywne poprawki, aby zaktualizować tagi. Nie zaleca się korzystania z przepływu Pobierz/Aktualizuj, jeśli w zaangażowaniu są tysiące rejestracji. System import/eksport możliwości jest przeznaczony do pokrycia scenariusza. Zapewnisz dostęp do kontenera obiektów blob w ramach konta magazynu jako źródło przychodzących danych i lokalizacji dla danych wyjściowych.

**Prześlij zadanie eksportu:**

    ```java
    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ExportRegistrations);
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);
    ```

**Prześlij zadanie importu:**

    ```java
    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ImportCreateRegistrations);
    job.setImportFileUri("input file uri with SAS signature");
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);
    ```

**Poczekaj, aż zadanie zostanie wykonane:**

    ```java
    while(true){
        Thread.sleep(1000);
        job = hub.getNotificationHubJob(job.getJobId());
        if(job.getJobStatus() == NotificationHubJobStatus.Completed)
            break;
    }
    ```

**Zdobądź wszystkie oferty pracy:**

    ```java
    List<NotificationHubJob> jobs = hub.getAllNotificationHubJobs();
    ```

**Identyfikator URI z podpisem sygnatury Sygnatury dostępu Współdzielon**

 Ten adres URL jest adresem URL kontenera pliku obiektu blob lub obiektu blob oraz zestawem parametrów, takich jak uprawnienia i czas wygaśnięcia oraz podpis wszystkich tych rzeczy wykonanych przy użyciu klucza Sygnatury dostępu Współdzielonego konta. Zestaw Java SDK usługi Azure Storage ma zaawansowane funkcje, w tym tworzenie tych identyfikatorów URI. Jako prostą alternatywę, `ImportExportE2E` spójrz na klasę testową (z lokalizacji GitHub), która ma podstawową i kompaktową implementację algorytmu podpisywania.

### <a name="send-notifications"></a>Wysyłanie powiadomień

Notification Obiekt jest po prostu treści z nagłówków, niektóre metody narzędzia pomóc w tworzeniu obiektów powiadomień natywnych i szablonów.

* **Sklep Windows i Windows Phone 8.1 (inne niż Silverlight)**

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

* **System Windows Phone 8.0 i 8.1 Silverlight**

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

* **Rozpakuj ogień**

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

* **Wyślij do wyrażenia znacznika**

    ```java
    hub.sendNotification(n, "foo && ! bar");
    ```

* **Wyślij powiadomienie o szablonie**

    ```java
    Map<String, String> prop =  new HashMap<String, String>();
    prop.put("prop1", "v1");
    prop.put("prop2", "v2");
    Notification n = Notification.createTemplateNotification(prop);
    hub.sendNotification(n);
    ```

Uruchomienie kodu Java powinno teraz spowodować wyświetlenie powiadomienia na urządzeniu docelowym.

## <a name="next-steps"></a><a name="next-steps"></a>Kolejne kroki

W tym temacie pokazano, jak utworzyć prosty klient Java REST dla centrów powiadomień. W tym miejscu można wykonać następujące czynności:

* Pobierz pełny [sdk Java], który zawiera cały kod SDK.
* Zagraj z próbkami:
  * [Wprowadzenie do centrów powiadomień]
  * [Wysyłaj najświeższe wiadomości]
  * [Wysyłanie zlokalizowanych wiadomości]
  * [Wysyłanie powiadomień do uwierzytelnionych użytkowników]
  * [Wysyłanie powiadomień między platformami do uwierzytelnionych użytkowników]

[Java SDK]: https://github.com/Azure/azure-notificationhubs-java-backend
[Get started tutorial]: notification-hubs-ios-apple-push-notification-apns-get-started.md
[Wprowadzenie do centrów powiadomień]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Wysyłaj najświeższe wiadomości]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Wysyłanie zlokalizowanych wiadomości]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Wysyłanie powiadomień do uwierzytelnionych użytkowników]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Wysyłanie powiadomień między platformami do uwierzytelnionych użytkowników]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Maven]: https://maven.apache.org/

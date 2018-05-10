---
title: Jak używać usługi Notification Hubs z językiem Java
description: Dowiedz się, jak używać usługi Azure Notification Hubs z zaplecza Java.
services: notification-hubs
documentationcenter: ''
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 4c3f966d-0158-4a48-b949-9fa3666cb7e4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: java
ms.devlang: java
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 88e3ab3cc03cc1e760672120bc5c484af1ba4722
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-use-notification-hubs-from-java"></a>Jak używać usługi Notification Hubs za pomocą języka Java
[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

W tym temacie opisano najważniejsze funkcje nowej pełnej oficjalnego powiadomienia Centrum Java zestawu SDK platformy Azure. Ten projekt jest projekt typu open source i można wyświetlić cały kod zestawu SDK w [zestawu Java SDK]. 

Ogólnie rzecz biorąc, są dostępne wszystkie funkcje usługi Notification Hubs zaplecza Java/PHP/Python/Ruby przy użyciu interfejsu REST Centrum powiadomień, zgodnie z opisem w temacie MSDN [interfejsów API REST centra powiadomień](http://msdn.microsoft.com/library/dn223264.aspx). Zestaw SDK Java udostępnia cienką otoką w porównaniu z tych interfejsów REST w języku Java. 

Zestaw SDK obsługuje obecnie:

* CRUD na centra powiadomień 
* CRUD rejestracji
* Zarządzanie instalacji
* Import/Eksport rejestracji
* Wysyła regularnych
* Wysyła według harmonogramu
* Operacje asynchroniczne za pośrednictwem Java NIO
* Obsługiwane platformy: APNS (iOS), usługi GCM (Android), usługi WNS (aplikacje ze Sklepu Windows), usługi MPNS (Windows Phone), ADM (Amazon Kindle Fire), Baidu (Android bez usług Google) 

## <a name="sdk-usage"></a>Użycie zestawu SDK
### <a name="compile-and-build"></a>Kompilowanie i kompilacji
Użyj [Maven]

Aby utworzyć:

    mvn package

## <a name="code"></a>Kod
### <a name="notification-hub-cruds"></a>CRUDs Centrum powiadomień
**Utwórz NamespaceManager:**

    NamespaceManager namespaceManager = new NamespaceManager("connection string")

**Tworzenie Centrum powiadomień:**

    NotificationHubDescription hub = new NotificationHubDescription("hubname");
    hub.setWindowsCredential(new WindowsCredential("sid","key"));
    hub = namespaceManager.createNotificationHub(hub);

 LUB

    hub = new NotificationHub("connection string", "hubname");

**Pobierz Centrum powiadomień:**

    hub = namespaceManager.getNotificationHub("hubname");

**Zaktualizuj Centrum powiadomień:**

    hub.setMpnsCredential(new MpnsCredential("mpnscert", "mpnskey"));
    hub = namespaceManager.updateNotificationHub(hub);

**Usunąć Centrum powiadomień:**

    namespaceManager.deleteNotificationHub("hubname");

### <a name="registration-cruds"></a>CRUDs rejestracji
**Tworzenie klienta Centrum powiadomień:**

    hub = new NotificationHub("connection string", "hubname");

**Utwórz rejestracji systemu Windows:**

    WindowsRegistration reg = new WindowsRegistration(new URI(CHANNELURI));
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");    
    hub.createRegistration(reg);

**Utwórz rejestracji systemu iOS:**

    AppleRegistration reg = new AppleRegistration(DEVICETOKEN);
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);

Podobnie można utworzyć rejestracji dla systemu Android (GCM), Windows Phone (MPNS) i Kindle Fire (ADM).

**Tworzenie szablonu rejestracji:**

    WindowsTemplateRegistration reg = new WindowsTemplateRegistration(new URI(CHANNELURI), WNSBODYTEMPLATE);
    reg.getHeaders().put("X-WNS-Type", "wns/toast");
    hub.createRegistration(reg);

**Tworzenie rejestracji za pomocą Utwórz identyfikator rejestracji + upsert wzorca**

Usuwa duplikaty z powodu żadnych odpowiedzi utracone, jeśli rejestracji identyfikatorów są przechowywane na urządzeniu:

    String id = hub.createRegistrationId();
    WindowsRegistration reg = new WindowsRegistration(id, new URI(CHANNELURI));
    hub.upsertRegistration(reg);

**Aktualizacja rejestracji:**

    hub.updateRegistration(reg);

**Usuwanie rejestracji:**

    hub.deleteRegistration(regid);

**Rejestracje zapytania:**

* **Pobierz pojedynczy rejestracji:**
  
        hub.getRegistration(regid);

* **Pobieranie wszystkich rejestracji w Centrum:**
  
        hub.getRegistrations();

* **Pobierz rejestracje z tagiem:**
  
        hub.getRegistrationsByTag("myTag");

* **Uzyskać rejestracji za pomocą kanału:**
  
        hub.getRegistrationsByChannel("devicetoken");


Wszystkie zapytania kolekcji obsługuje tokenów $top i kontynuacji.

### <a name="installation-api-usage"></a>Użycie instalacji interfejsu API
Instalacja interfejsu API jest alternatywny mechanizm zarządzania rejestracji. Zamiast obsługi wielu rejestracji, które nie są prosta i mogą łatwo zrobić nieprawidłowo lub Niewydajne, obecnie istnieje możliwość użycia obiektu jednej instalacji. Instalacja zawiera wszystkie elementy potrzebne: push kanał (token urządzenia), tagi, szablony, dodatkowej kafelków (w przypadku usługi WNS i APNS). Nie należy do wywołania tej usługi do już Pobierz identyfikator — tylko generowania identyfikatora GUID lub innego identyfikatora, zachowaj go na urządzeniu i wysyłania z wewnętrzną bazą danych wraz z kanału wypychanych (token urządzenia). Do wewnętrznej bazy danych, należy wykonywać tylko jednego wywołania: CreateOrUpdateInstallation, jest całkowicie idempotentności, więc możesz ponowić próbę, jeśli to konieczne.

Jako przykład Amazon Kindle Fire:

    Installation installation = new Installation("installation-id", NotificationPlatform.Adm, "adm-push-channel");
    hub.createOrUpdateInstallation(installation);

Jeśli chcesz zaktualizować go: 

    installation.addTag("foo");
    installation.addTemplate("template1", new InstallationTemplate("{\"data\":{\"key1\":\"$(value1)\"}}","tag-for-template1"));
    installation.addTemplate("template2", new InstallationTemplate("{\"data\":{\"key2\":\"$(value2)\"}}","tag-for-template2"));
    hub.createOrUpdateInstallation(installation);

Dla zaawansowanych scenariuszy Użyj funkcji częściowej aktualizacji, dzięki czemu można zmodyfikować właściwości tylko określonego obiektu instalacji. Częściowej aktualizacji jest podzbiorem operacje JSON poprawki, które można uruchomić dla obiekt instalacji.

    PartialUpdateOperation addChannel = new PartialUpdateOperation(UpdateOperationType.Add, "/pushChannel", "adm-push-channel2");
    PartialUpdateOperation addTag = new PartialUpdateOperation(UpdateOperationType.Add, "/tags", "bar");
    PartialUpdateOperation replaceTemplate = new PartialUpdateOperation(UpdateOperationType.Replace, "/templates/template1", new InstallationTemplate("{\"data\":{\"key3\":\"$(value3)\"}}","tag-for-template1")).toJson());
    hub.patchInstallation("installation-id", addChannel, addTag, replaceTemplate);

Usuń instalacji:

    hub.deleteInstallation(installation.getInstallationId());

CreateOrUpdate, poprawki i Delete są ostatecznie zgodne z Get. Żądana operacja po prostu przechodzi do kolejki systemu podczas wywołania i są wykonywane w tle. Get nie jest przeznaczony dla scenariusza głównego środowiska uruchomieniowego, ale tylko dla debugowania i rozwiązywania problemów, jego jest ściśle ograniczany przez usługę.

Przepływ wysyłania dla instalacji jest taka sama jak w przypadku rejestracji. Celem powiadomień do danej instalacji — wystarczy użyć tagu "InstallationId: {desired-id}". W przypadku ten kod jest:

    Notification n = Notification.createWindowsNotification("WNS body");
    hub.sendNotification(n, "InstallationId:{installation-id}");

Dla jednego z kilku szablonów:

    Map<String, String> prop =  new HashMap<String, String>();
    prop.put("value3", "some value");
    Notification n = Notification.createTemplateNotification(prop);
    hub.sendNotification(n, "InstallationId:{installation-id} && tag-for-template1");

### <a name="schedule-notifications-available-for-standard-tier"></a>Zaplanuj powiadomienia (dostępne do warstwy standardowa)
Taka sama jak regularne send, ale jeden dodatkowy parametr - samych parametrach scheduledTime, jago dostarczenia powiadomienia. Usługa akceptuje dowolnego punktu czas między teraz + 5 minut, a teraz + 7 dni.

**Harmonogram powiadomień macierzystego systemu Windows:**

    Calendar c = Calendar.getInstance();
    c.add(Calendar.DATE, 1);    
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.scheduleNotification(n, c.getTime());

### <a name="importexport-available-for-standard-tier"></a>Import/Eksport (dostępne do warstwy standardowa)
Czasami jest wymagane do wykonania operacji zbiorczej rejestracji. Zazwyczaj jest integrację z innym systemem lub właśnie ogromną poprawkę znaczy aktualizowania elementów tag. Nie zaleca się do używania przepływu Get/aktualizacji, jeśli są związane z tysiącami rejestracji. Możliwość importowania i eksportowania zaprojektowano w celu pokrycia w scenariuszu. Zasadniczo musisz podać dostępu do niektórych kontenera obiektów blob na koncie magazynu jako źródła przychodzących danych oraz lokalizację dla danych wyjściowych.

**Prześlij zadanie eksportu:**

    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ExportRegistrations);
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);


**Prześlij zadanie importu:**

    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ImportCreateRegistrations);
    job.setImportFileUri("input file uri with SAS signature");
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);

**Zaczekaj, aż pracę:**

    while(true){
        Thread.sleep(1000);
        job = hub.getNotificationHubJob(job.getJobId());
        if(job.getJobStatus() == NotificationHubJobStatus.Completed)
            break;
    }       

**Pobierz wszystkie zadania:**

    List<NotificationHubJob> jobs = hub.getAllNotificationHubJobs();

**Identyfikator URI przy użyciu podpisu sygnatury dostępu Współdzielonego:** ten adres URL jest adres URL niektóre pliku blob lub kontenera obiektów blob oraz zestaw parametrów, takich jak uprawnienia i czas wygaśnięcia plus podpisu wszystkie elementy utworzone za pomocą klucza sygnatury dostępu Współdzielonego dla konta. Zestawu SDK Java usługi Magazyn Azure ma rozbudowane możliwości, takie jak tworzenie takie identyfikatory URI. Jako alternatywne prosty może zająć przyjrzeć się klasy testu ImportExportE2E (z lokalizacji github), która ma podstawowych i compact implementację algorytmu podpisywania.

### <a name="send-notifications"></a>Wysyłanie powiadomień
Obiekt powiadomień jest po prostu treść z nagłówkami, niektóre metody narzędziowe ułatwić tworzenie obiektów powiadomienia macierzystego i szablonu.

* **Sklep Windows i Windows Phone 8.1 (z systemem innym niż platformy Silverlight)**
  
        String toast = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello from Java!</text></binding></visual></toast>";
        Notification n = Notification.createWindowsNotification(toast);
        hub.sendNotification(n);
* **iOS**
  
        String alert = "{\"aps\":{\"alert\":\"Hello from Java!\"}}";
        Notification n = Notification.createAppleNotification(alert);
        hub.sendNotification(n);
* **Android**
  
        String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
        Notification n = Notification.createGcmNotification(message);
        hub.sendNotification(n);
* **Windows Phone 8.0 i 8.1 Silverlight**
  
        String toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                    "<wp:Notification xmlns:wp=\"WPNotification\">" +
                       "<wp:Toast>" +
                            "<wp:Text1>Hello from Java!</wp:Text1>" +
                       "</wp:Toast> " +
                    "</wp:Notification>";
        Notification n = Notification.createMpnsNotification(toast);
        hub.sendNotification(n);
* **Kindle Fire**
  
        String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
        Notification n = Notification.createAdmNotification(message);
        hub.sendNotification(n);
* **Wyślij do tagów**
  
        Set<String> tags = new HashSet<String>();
        tags.add("boo");
        tags.add("foo");
        hub.sendNotification(n, tags);
* **Wyślij do wyrażenia etykiety**       
  
        hub.sendNotification(n, "foo && ! bar");
* **Wyślij powiadomienie szablonu**
  
        Map<String, String> prop =  new HashMap<String, String>();
        prop.put("prop1", "v1");
        prop.put("prop2", "v2");
        Notification n = Notification.createTemplateNotification(prop);
        hub.sendNotification(n);

Uruchamianie kodu języka Java teraz powinna generować powiadomienia znajdujących się na urządzeniu docelowym.

## <a name="next-steps"></a>Następne kroki
W tym temacie pokazano, jak utworzyć prosty Java REST klienta usługi Notification Hubs. W tym miejscu można wykonywać następujące czynności:

* Pobierz pełny [zestawu Java SDK], który zawiera cały kod zestawu SDK. 
* Odtwarzanie z próbek:
  * [Rozpoczynanie pracy z usługą Notification Hubs]
  * [Wysyłanie najważniejszych wiadomości]
  * [Wysyłanie najważniejszych zlokalizowanych wiadomości]
  * [Wyślij powiadomienia do uwierzytelnionych użytkowników]
  * [Wysyłanie powiadomień i platform dla uwierzytelnionych użytkowników]

[zestawu Java SDK]: https://github.com/Azure/azure-notificationhubs-java-backend
[Get started tutorial]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Rozpoczynanie pracy z usługą Notification Hubs]: http://www.windowsazure.com/manage/services/notification-hubs/getting-started-windows-dotnet/
[Wysyłanie najważniejszych wiadomości]: http://www.windowsazure.com/manage/services/notification-hubs/breaking-news-dotnet/
[Wysyłanie najważniejszych zlokalizowanych wiadomości]: http://www.windowsazure.com/manage/services/notification-hubs/breaking-news-localized-dotnet/
[Wyślij powiadomienia do uwierzytelnionych użytkowników]: http://www.windowsazure.com/manage/services/notification-hubs/notify-users/
[Wysyłanie powiadomień i platform dla uwierzytelnionych użytkowników]: http://www.windowsazure.com/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Maven]: http://maven.apache.org/


---
title: Migrowanie z usług Mobile Services do aplikacji mobilnej usługi App Service
description: Dowiedz się, jak łatwo przeprowadzić migrację aplikacji usług Mobile Services do aplikacji mobilnej usługi App Service
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 07507ea2-690f-4f79-8776-3375e2adeb9e
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile
ms.devlang: na
ms.topic: article
ms.date: 10/03/2016
ms.author: crdun
ms.openlocfilehash: dfc5e2923215b1669b0a3300653ad0cae7379655
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122476"
---
# <a name="article-top"></a>Migrowanie istniejącej usługi mobilnej platformy Azure w usłudze Azure App Service
Za pomocą [ogólnie dostępne w usłudze Azure App Service], witryn usług Azure Mobile Services mogą zostać łatwo zmigrowane w miejscu można korzystać ze wszystkich funkcji usługi Azure App Service.  W tym dokumencie opisano, czego można oczekiwać podczas migracji lokacji z usług Azure Mobile Services w usłudze Azure App Service.

## <a name="what-does-migration-do"></a>Co robi migracji do witryny
Migracja usługi mobilnej Azure włącza usługi mobilnej do [Azure App Service] aplikacji bez wpływu na kod.  Twoje usługi Notification Hubs, połączenie danych SQL, ustawienia uwierzytelniania, zaplanowane zadania i nazwy domeny pozostaną niezmienione.  Klienci mobilni przy użyciu usługi Microsoft Azure Mobile w dalszym ciągu działać normalnie.  Usługi migracji powoduje ponowne uruchomienie, gdy zostanie przesłany do usługi Azure App Service.

[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

## <a name="why-migrate"></a>Dlaczego należy zmigrować witryny
Microsoft to zalecanie migracji usługi mobilnej Azure, aby móc korzystać z funkcji usługi Azure App Service, w tym:

* Nowe funkcje hosta, w tym [WebJobs] i [Niestandardowe nazwy domen].
* Monitorowanie i rozwiązywanie problemów z [Application Insights].
* Wbudowane narzędzia DevOps, łącznie z [miejsc przejściowych], wycofywania i w środowisku produkcyjnym testowania.
* [Automatyczne skalowanie], równoważenie obciążenia, i [Monitorowanie wydajności].

Aby uzyskać więcej informacji na temat zalet usługi Azure App Service, zobacz [Mobile Services a App Service] tematu.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Przed rozpoczęciem wszelkie prace głównej witryny, należy wykonać kopię zapasową skryptów usługi mobilnej i bazy danych SQL.

## <a name="migrating-site"></a>Migrowanie witryn
Proces migracji powoduje migrację wszystkich lokacji w ramach jednego regionu platformy Azure.

Aby przeprowadzić migrację witryny:

1. Zaloguj się do [klasyczny portal Azure].
2. Wybierz usługę mobilną, w regionie, w którym chcesz przeprowadzić migrację.
3. Kliknij przycisk **migracja do usługi App Service** przycisku.

   ![Przycisk migracji][0]
4. Przeczytaj migracja do usługi App Service w oknie dialogowym.
5. Wprowadź nazwę usługi mobilnej w odpowiednim polu.  Na przykład, jeśli nazwa domeny to contoso.azure mobile.net, wprowadź *contoso* w odpowiednim polu.
6. Kliknij przycisk znaczników.

Monitorować stan migracji w monitorze działania. Witryna jest wymieniony jako *Migrowanie* w klasycznym portalu Azure.

  ![Monitorowanie działania migracji][1]

Każdą migrację może potrwać od 3 do 15 minut za usługę mobilną migrowane.  Lokacji pozostają dostępne podczas migracji.
Witryna zostanie ponownie uruchomiony, pod koniec procesu migracji.  Witryna jest niedostępna podczas procesu ponownego uruchamiania może trwać kilka sekund.

## <a name="finalizing-migration"></a>Kończenie migracji
Należy zaplanować i przetestuj swoją witrynę, z poziomu klienta mobilnego po zakończeniu procesu migracji.  Upewnij się, że można wykonywać wszystkie typowe Akcje klienta bez zmian w kliencie mobilnym.  

### <a name="update-app-service-tier"></a>Wybierz odpowiednią usługę App Service warstwy cenowej
Masz większą elastyczność w zmienionych cennikach po przeprowadzeniu migracji do usługi Azure App Service.

1. Zaloguj się do witryny [Azure Portal].
2. Wybierz **wszystkie zasoby** lub **App Services** kliknij nazwę usługi mobilnej zmigrowane.
3. Domyślnie zostanie otwarty blok ustawienia.
4. Kliknij przycisk **planu usługi App Service** w menu Ustawienia.
5. Kliknij przycisk **warstwy cenowej** kafelka.
6. Kliknij Kafelek, które są odpowiednie do własnych potrzeb, a następnie kliknij przycisk **wybierz**.  Może być konieczne kliknięcie **Wyświetl wszystkie** wyświetlić dostępnych warstw cenowych.

Jako punktu wyjścia zaleca się następujące warstwy:

| Warstwa cenowa usługi mobilnej | Warstwa cenowa usługi App Service |
|:--- |:--- |
| Bezpłatna |Bezpłatna F1 |
| Podstawowa |Podstawowa B1 |
| Standardowa (Standard) |Standardowa S1 |

Brak znaczną elastyczność w wyborze po prawej stronie ceny warstwy aplikacji.  Zapoznaj się [Cennik usługi aplikacji] szczegółowe informacje o cenach nowej usługi aplikacji.

> [!TIP]
> Warstwy usługi aplikacji w wersji Standard zawiera dostęp do wielu funkcji, które chcesz użyć, w tym [miejsc przejściowych], automatyczne tworzenie kopii zapasowych i skalowanie automatyczne.  Sprawdź nowe możliwości, gdy istnieje!
>
>

### <a name="review-migration-scheduler-jobs"></a>Przejrzyj zadania zmigrowane usługi Scheduler
Zadania harmonogramu nie będzie widoczny aż do około 30 minut po zakończeniu migracji.  Zaplanowane zadania będą nadal działać w tle.
Aby wyświetlić Twoje zaplanowane zadania, gdy będą widoczne ponownie:

1. Zaloguj się do witryny [Azure Portal].
2. Wybierz **Przeglądaj >**, wprowadź **harmonogram** w *filtru* polu, a następnie wybierz **kolekcje usługi Scheduler**.

Istnieje ograniczona liczba harmonogramu — warstwa bezpłatna zadania dostępne po migracji.  Wśród danych użycia i [Plany usługi Azure Scheduler].

### <a name="configure-cors"></a>Konfigurowanie mechanizmu CORS w razie potrzeby
Współużytkowanie zasobów między źródłami jest techniką, aby zezwolić na dostęp do internetowego interfejsu API z innej domeny do witryny sieci Web.  Jeśli używasz usługi Azure Mobile Services za pomocą witryny sieci Web skojarzony, należy Konfigurowanie mechanizmu CORS w ramach migracji.  Jeśli uzyskujesz dostęp do usług Azure Mobile Services, wyłącznie na urządzeniach przenośnych, mechanizm CORS nie muszą zostać skonfigurowane z wyjątkiem sytuacji, w rzadkich przypadkach.

Zmigrowane ustawienia mechanizmu CORS są dostępne jako **MS_CrossDomainWhitelist** ustawienia aplikacji.  Aby przeprowadzić migrację witryny w obiekcie mechanizmu CORS usługi App Service:

1. Zaloguj się do witryny [Azure Portal].
2. Wybierz **wszystkie zasoby** lub **App Services** kliknij nazwę usługi mobilnej zmigrowane.
3. Domyślnie zostanie otwarty blok ustawienia.
4. Kliknij przycisk **CORS** w menu interfejsu API.
5. Wprowadź wszelkie dozwolone źródła w odpowiednim polu, naciskając klawisz Enter po każdym.
6. Po liście dozwolone źródła jest poprawny, kliknij przycisk Zapisz.

> [!TIP]
> Jest jedną z zalet za pomocą usługi Azure App Service, można uruchomić witryny sieci web i usługi mobilnej w na tej samej lokacji.  Aby uzyskać więcej informacji, zobacz [następne kroki](#next-steps) sekcji.
>
>

### <a name="download-publish-profile"></a>Pobierz nowy profil publikowania
Profil publikowania witryny jest zmieniany, gdy migracja do usługi Azure App Service.  Jeśli zamierzasz opublikować witryny z poziomu programu Visual Studio, konieczne będzie nowy profil publikowania.  Aby pobrać nowy profil publikowania:

1. Zaloguj się do witryny [Azure Portal].
2. Wybierz **wszystkie zasoby** lub **App Services** kliknij nazwę usługi mobilnej zmigrowane.
3. Kliknij przycisk **Pobierz profil publikowania**.

Plik ustawień publikacji zostanie pobrana do komputera.  Jest to zwykle *sitename*. PublishSettings.  Importowanie ustawień publikowania do istniejącego projektu:

1. Otwórz program Visual Studio i projekt usługi mobilnej Azure.
2. Kliknij prawym przyciskiem myszy projekt w **Eksploratora rozwiązań** i wybierz **publikowania...**
3. Kliknij pozycję **Importuj**
4. Kliknij przycisk **Przeglądaj** i wybierz z pobranego pliku ustawień publikowania.  Kliknij przycisk **OK**.
5. Kliknij przycisk **Waliduj połączenie** zapewnienie pracy ustawień publikowania.
6. Kliknij przycisk **Publikuj** opublikowanie witryny.

## <a name="working-with-your-site"></a>Praca z Twojej lokacji po migracji
Rozpoczęcie pracy z Twojej nowej usługi App Service w [Azure Portal] po migracji.  Poniżej przedstawiono niektóre uwagi dotyczące określonych operacji, których użyto podczas wykonywania w [klasyczny portal Azure], wraz z ich odpowiednika usługi App Service.

### <a name="publishing-your-site"></a>Pobieranie i publikowanie migrowanych witryny
Witryna jest dostępna za pośrednictwem usługi git i ftp i można ponownie opublikować przy użyciu różnych mechanizmów różne, w tym WebDeploy, TFS, Mercurial, GitHub i FTP.  Poświadczenia wdrożenia są migrowane z pozostałą częścią witryny.  Jeśli nie określono poświadczeń wdrożenia lub możesz nie pamiętasz, można je zresetować:

1. Zaloguj się do witryny [Azure Portal].
2. Wybierz **wszystkie zasoby** lub **App Services** kliknij nazwę usługi mobilnej zmigrowane.
3. Domyślnie zostanie otwarty blok ustawienia.
4. Kliknij przycisk **poświadczenia wdrożenia** w publikowania menu.
5. Wprowadź nowe poświadczenia wdrażania w odpowiednich polach, a następnie kliknij przycisk Zapisz.

Można użyć tych poświadczeń, sklonować lokacji za pomocą narzędzia git lub skonfigurować używanie wdrożeń zautomatyzowanych z usługi GitHub, TFS lub Mercurial.  Aby uzyskać więcej informacji zobacz [dokumentację wdrażania usługi Azure App Service].

### <a name="appsettings"></a>Ustawienia aplikacji
Większość ustawień zmigrowanych usługi mobilnej są dostępne za pośrednictwem ustawień aplikacji.  Możesz uzyskać listę ustawień aplikacji z [Azure Portal].
Aby wyświetlić lub zmienić ustawienia aplikacji:

1. Zaloguj się do witryny [Azure Portal].
2. Wybierz **wszystkie zasoby** lub **App Services** kliknij nazwę usługi mobilnej zmigrowane.
3. Domyślnie zostanie otwarty blok ustawienia.
4. Kliknij przycisk **ustawienia aplikacji** w menu Ogólne.
5. Przewiń do sekcji ustawień aplikacji i Znajdź ustawienia swojej aplikacji.
6. Kliknij wartość ustawienia aplikacji, aby edytować wartość.  Kliknij przycisk **Zapisz** można zapisać wartości.

W tym samym czasie, możesz zaktualizować wiele ustawień aplikacji.

> [!TIP]
> Istnieją dwa ustawienia aplikacji z taką samą wartość.  Na przykład może zostać wyświetlony *wartości ApplicationKey* i *MS\_wartości ApplicationKey*.  W tym samym czasie, należy zaktualizować oba ustawienia aplikacji.
>
>

### <a name="authentication"></a>Uwierzytelnianie
Wszystkie ustawienia uwierzytelniania są dostępne jako ustawienia aplikacji w witrynie zmigrowane.  Aby zaktualizować ustawienia uwierzytelniania, należy zmienić ustawienia odpowiednich aplikacji.  W poniższej tabeli przedstawiono ustawienia aplikacji właściwe dla dostawcy uwierzytelniania:

| Dostawca | Identyfikator klienta | Wpis tajny klienta | Inne ustawienia |
|:--- |:--- |:--- |:--- |
| Konto Microsoft |**MS\_MicrosoftClientID** |**MS\_MicrosoftClientSecret** |**MS\_MicrosoftPackageSID** |
| Facebook |**MS\_FacebookAppID** |**MS\_FacebookAppSecret** | |
| Twitter |**MS\_TwitterConsumerKey** |**MS\_TwitterConsumerSecret** | |
| Google |**MS\_GoogleClientID** |**MS\_GoogleClientSecret** | |
| Azure AD |**MS\_AadClientID** | |**MS\_AadTenants** |

Uwaga: **MS\_AadTenants** są przechowywane jako rozdzielana przecinkami lista domen dzierżawy (pola "Dozwolone Tenants" w portalu usługi Mobile Services).

> [!WARNING]
> **Nie używa mechanizmów uwierzytelniania w menu Ustawienia**
>
> Usługa Azure App Service zapewnia osobne "bez użycia kodu" uwierzytelnianie i autoryzacja system w obszarze *uwierzytelniania / autoryzacji* menu Ustawienia, a (przestarzałe) *uwierzytelniania Mobile* opcji w menu Ustawienia.  Te opcje są niezgodne z migrowanych usługi mobilnej Azure.  Możesz [uaktualnić lokację](app-service-mobile-net-upgrading-from-mobile-services.md) może korzystać z uwierzytelniania usługi Azure App Service.
>
>

### <a name="easytables"></a>Data
*Danych* kartę w usłudze Mobile Services została zastąpiona *łatwych tabel* w witrynie Azure portal.  Dostęp do łatwych tabel:

1. Zaloguj się do witryny [Azure Portal].
2. Wybierz **wszystkie zasoby** lub **App Services** kliknij nazwę usługi mobilnej zmigrowane.
3. Domyślnie zostanie otwarty blok ustawienia.
4. Kliknij przycisk **łatwych tabel** w menu urządzenia PRZENOŚNEGO.

Można dodać tabelę, klikając **Dodaj** przycisk lub skorzystać z istniejącymi tabelami, klikając nazwę tabeli.  Istnieją różne operacje, które można wykonać z poziomu tego bloku, w tym:

* Zmienianie uprawnień tabeli
* Edycji skryptów operacyjne
* Zarządzanie schematem tabeli
* Usuwanie tabeli
* Czyszczenie zawartości tabeli
* Usunąć wybrane wiersze w tabeli

### <a name="easyapis"></a>API
*API* kartę w usłudze Mobile Services została zastąpiona *łatwe interfejsy API* w witrynie Azure portal.  Dostęp do łatwe interfejsy API:

1. Zaloguj się do witryny [Azure Portal].
2. Wybierz **wszystkie zasoby** lub **App Services** kliknij nazwę usługi mobilnej zmigrowane.
3. Domyślnie zostanie otwarty blok ustawienia.
4. Kliknij przycisk **łatwe interfejsy API** w menu urządzenia PRZENOŚNEGO.

Migrowanych interfejsów API są już wyświetlane w bloku.  Można również dodać interfejs API z poziomu tego bloku.  Aby zarządzać konkretnego interfejsu API, kliknij opcję interfejsu API.
W nowym bloku możesz dostosować uprawnienia i Edytuj skrypty interfejsu API.

### <a name="on-demand-jobs"></a>Zadania usługi Scheduler
Wszystkie zadania usługi scheduler są dostępne za pośrednictwem sekcji kolekcje zadań usługi Scheduler.  Dostęp do zadań usługi scheduler:

1. Zaloguj się do witryny [Azure Portal].
2. Wybierz **Przeglądaj >**, wprowadź **harmonogram** w *filtru* polu, a następnie wybierz **kolekcje usługi Scheduler**.
3. Wybierz kolekcję zadań dla danej witryny.  Jest on nazwany *sitename*-zadania.
4. Kliknij pozycję **Ustawienia**.
5. Kliknij przycisk **zadania usługi Scheduler** w obszarze Zarządzanie.

Zaplanowane zadania są wyświetlane z częstotliwością, jaką określono przed migracją.  Zadania na żądanie są wyłączone.  Aby uruchomić zadanie na żądanie:

1. Zaznacz zadanie, które chcesz uruchomić.
2. W razie potrzeby kliknij **Włącz** można włączyć zadania.
3. Kliknij przycisk **ustawienia**, następnie **harmonogram**.
4. Wybierz opcję ponownego **raz**, następnie kliknij przycisk **Zapisz**

Zadania na żądanie znajdują się w `App_Data/config/scripts/scheduler post-migration`.  Zalecamy przekonwertowanie wszystkich zadań na żądanie [webjobs] lub [Funkcje].  Pisanie nowego zadania usługi scheduler jako [WebJobs] lub [Funkcje].

### <a name="notification-hubs"></a>Notification Hubs
Usługi Mobile Services korzysta z usługi Notification Hubs dla powiadomień wypychanych.  Poniższe ustawienia aplikacji są używane do łączenia z Centrum powiadomień do usługi mobilnej po migracji:

| Ustawienie aplikacji | Opis |
|:--- |:--- |
| **MS\_PushEntityNamespace** |Namespace Centrum powiadomień |
| **MS\_NotificationHubName** |Nazwa centrum powiadomień |
| **MS\_NotificationHubConnectionString** |Parametry połączenia Centrum powiadomień |
| **MS\_NamespaceName** |Alias MS_PushEntityNamespace |

Twoje Centrum powiadomień jest zarządzana za pośrednictwem [Azure Portal].  Zanotuj nazwę Centrum powiadomień (można je znaleźć przy użyciu ustawień aplikacji):

1. Zaloguj się do witryny [Azure Portal].
2. Wybierz **Przeglądaj**>, a następnie wybierz **usługi Notification Hubs**
3. Kliknij nazwę Centrum powiadomień, skojarzone z usługą mobilną.

> [!NOTE]
> Jeśli Twoje Centrum powiadomień jest typu "Mixed", nie jest widoczna.  "Mieszany" Typ powiadomienia, że koncentratory korzystanie z usługi Notification Hubs i funkcje starszej wersji usługi Service Bus.  [Konwertuj mieszane przestrzeniami nazw usługi] przed kontynuowaniem.  Po zakończeniu konwersji Centrum powiadomień zostanie wyświetlony w [Azure Portal].
>
>

Aby uzyskać więcej informacji, zobacz [Notification Hubs] dokumentacji.

> [!TIP]
> Funkcje zarządzania centra powiadomień w [Azure Portal] są nadal w wersji zapoznawczej.  [klasyczny portal Azure] pozostają dostępne do zarządzania wszystkie centra powiadomień.
>
>

### <a name="legacy-push"></a>Ustawienia wypychania starszej wersji
Jeśli wypychane jest skonfigurowane na usługę mobilną przed wprowadzeniem w usłudze Notification Hubs, używasz *starszych wypychania*.  Jeśli używasz wypychania i nie ma na liście w konfiguracji centrum powiadomień, a następnie prawdopodobnie używasz *starszych wypychania*.  Ta funkcja jest migrowana z innymi funkcjami.  Jednak zaleca się uaktualnienie do usługi Notification Hubs po zakończeniu migracji.

W międzyczasie wszystkie ustawienia wypychania starszej wersji (z wyjątkiem istotne certyfikat usługi APN) są dostępne w ustawieniach aplikacji.  Zaktualizuj certyfikat usługi APNS, zastępując odpowiedniego pliku w systemie plików.

### <a name="app-settings"></a>Inne ustawienia aplikacji
Następujące ustawienia dodatkowe aplikacji są migrowane w ramach usługi mobilnej i dostępnych w ramach *ustawienia* > *ustawienia aplikacji*:

| Ustawienie aplikacji | Opis |
|:--- |:--- |
| **MS\_MobileServiceName** |Nazwa aplikacji |
| **MS\_MobileServiceDomainSuffix** |Prefiks domeny. i.e azure-mobile.net |
| **MS\_wartości ApplicationKey** |Klucz aplikacji |
| **MS\_MasterKey** |Twój klucz główny aplikacji |

Klucz aplikacji i klucz główny są identyczne klucze aplikacji z usługi mobilnej, oryginalnym.  W szczególności w celu zweryfikowania ich użycie mobilnego interfejsu API klucz aplikacji są wysyłane przez klientów mobilnych.

### <a name="cliequivalents"></a>Odpowiedniki wiersza polecenia
Można już korzystać z *usługi mobilne azure* polecenia Zarządzaj swoją witryną usług Azure Mobile Services.  Zamiast tego zostały zastąpione wiele funkcji *usługi azure site* polecenia.  Skorzystaj z poniższej tabeli, aby znaleźć odpowiedniki Typowe polecenia:

| *Azure Mobile* Command | Równoważne *witryny w systemie Azure* polecenia |
|:--- |:--- |
| lokalizacje mobilnych |Lista lokalizacji witryny |
| listy dla urządzeń przenośnych |Lista witryn |
| Pokaż przenośnych *nazwy* |Pokaż lokacji *nazwy* |
| przenośne ponownego uruchomienia *nazwy* |ponowne uruchamianie witryny *nazwy* |
| ponowne wdrażanie mobilnej *nazwy* |ponowne wdrażanie wdrożenia lokacji *commitId* *nazwy* |
| przenośne zestawu kluczy *nazwa* *typu* *wartość* |Usuń element appsetting lokacji *klucz* *nazwy* <br/> Dodaj element appsetting lokacji *klucz*=*wartość* *nazwy* |
| Lista konfiguracji urządzenia przenośnego *nazwy* |Lista appsetting witryn *nazwy* |
| Pobieranie konfiguracji urządzenia przenośnego *nazwa* *klucza* |Pokaż element appsetting lokacji *klucz* *nazwy* |
| zestaw konfiguracji urządzenia przenośnego *nazwa* *klucza* |Usuń element appsetting lokacji *klucz* *nazwy* <br/> Dodaj element appsetting lokacji *klucz*=*wartość* *nazwy* |
| Lista domen przenośnych *nazwy* |Lista domen lokacji *nazwy* |
| Dodawanie domeny przenośnych *nazwa* *domeny* |Dodawanie domeny witryny *domeny* *nazwy* |
| Usuń przenośnych domeny *nazwy* |Usuwanie domeny witryny *domeny* *nazwy* |
| Pokaż skalowanie mobilnych *nazwy* |Pokaż lokacji *nazwy* |
| Zmiana skali przenośnych *nazwy* |Tryb skalowania lokacji *tryb* *nazwy* <br /> lokacji wystąpień skali *wystąpień* *nazwy* |
| listy przenośnych appsetting *nazwy* |Lista appsetting witryn *nazwy* |
| Dodaj element appsetting przenośnych *nazwa* *klucz* *wartość* |Dodaj element appsetting lokacji *klucz*=*wartość* *nazwy* |
| Usuń element appsetting przenośnych *nazwa* *klucza* |Usuń element appsetting lokacji *klucz* *nazwy* |
| Pokaż przenośnych appsetting *nazwa* *klucza* |Usuń element appsetting lokacji *klucz* *nazwy* |

Aktualizacja uwierzytelniania lub ustawienia powiadomień wypychanych, aktualizując odpowiednie ustawienie aplikacji.
Edytowanie plików i opublikować swoją witrynę przy użyciu protokołu ftp lub git.

### <a name="diagnostics"></a>Rejestrowanie i Diagnostyka
Rejestrowanie diagnostyczne zwykle jest wyłączona w usłudze Azure App Service.  Aby włączyć rejestrowanie diagnostyczne:

1. Zaloguj się do witryny [Azure Portal].
2. Wybierz **wszystkie zasoby** lub **App Services** kliknij nazwę usługi mobilnej zmigrowane.
3. Domyślnie zostanie otwarty blok ustawienia.
4. Wybierz **dzienniki diagnostyczne** menu funkcji.
5. Kliknij przycisk **ON** dla następujących dzienników: **Rejestrowanie aplikacji (system plików)**, **szczegółowe komunikaty o błędach**, i **śledzenie nieudanych żądań**
6. Kliknij przycisk **System plików** dla rejestrowanie serwera sieci Web
7. Kliknij pozycję **Zapisz**

Aby wyświetlić dzienniki:

1. Zaloguj się do witryny [Azure Portal].
2. Wybierz **wszystkie zasoby** lub **App Services** kliknij nazwę usługi mobilnej zmigrowane.
3. Kliknij przycisk **narzędzia** przycisku
4. Wybierz **Stream dziennika** menu OBSERVE.

Dzienniki są wyświetlane w oknie, ponieważ są one generowane.  Można również pobrać dzienniki do późniejszej analizy przy użyciu poświadczeń wdrożenia. Aby uzyskać więcej informacji, zobacz [Logging] dokumentacji.

## <a name="known-issues"></a>Znane problemy
### <a name="deleting-a-migrated-mobile-app-clone-causes-a-site-outage"></a>Usuwanie migracji klonowania aplikacji mobilnej powoduje, że awarii lokacji
Jeśli możesz sklonować zmigrowane usługi mobilnej za pomocą programu Azure PowerShell, a następnie usuń klonu, wpis DNS dla produkcyjnej usługi zostaną usunięte.  Witryny jest nie będą dostępne z Internetu.  

Rozwiązanie: Jeśli chcesz sklonować witryny to zrobić za pośrednictwem portalu.

### <a name="changing-webconfig-does-not-work"></a>Zmienianie pliku Web.config nie działa
Jeśli masz witrynę programu ASP.NET, zmieni się na `Web.config` pliku nie zastosowane.  Azure App Service tworzy odpowiedni `Web.config` pliku podczas uruchamiania systemu do obsługi środowiska uruchomieniowego usług Mobile Services.  Niektóre ustawienia (takie jak nagłówki niestandardowe) można zastąpić za pomocą transformacji pliku XML.  Utwórz plik w o nazwie `applicationHost.xdt` — ten plik musi pozostać w `D:\home\site` katalogu usługi Azure.  Przekaż `applicationHost.xdt` bezpośrednio przy użyciu narzędzia Kudu lub plik przez skrypt wdrożenia niestandardowego.  Poniżej przedstawiono przykładowy dokument:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <system.webServer>
    <httpProtocol>
      <customHeaders>
        <add name="X-Frame-Options" value="DENY" xdt:Transform="Replace" />
        <remove name="X-Powered-By" xdt:Transform="Insert" />
      </customHeaders>
    </httpProtocol>
    <security>
      <requestFiltering removeServerHeader="true" xdt:Transform="SetAttributes(removeServerHeader)" />
    </security>
  </system.webServer>
</configuration>
```

Aby uzyskać więcej informacji, zobacz [przykłady przekształcania XDT] dokumentacji w witrynie GitHub.

### <a name="migrated-mobile-services-cannot-be-added-to-traffic-manager"></a>Nie można dodać zmigrowanych usług Mobile Services do usługi Traffic Manager
Podczas tworzenia profilu usługi Traffic Manager nie może bezpośrednio wybierz zmigrowane usługi mobilnej do profilu.  Użyj "zewnętrzny punkt końcowy."  Jest zewnętrzny punkt końcowy można dodawać tylko przy użyciu programu PowerShell.  Aby uzyskać więcej informacji, zobacz [samouczka usługi Traffic Manager](https://azure.microsoft.com/blog/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/).

## <a name="next-steps"></a>Następne kroki
Teraz, gdy aplikacja jest migrowana do usługi App Service, istnieją nawet więcej funkcji, których można użyć:

* Wdrożenie [miejsc przejściowych] umożliwiają przygotuj zmiany do swojej witryny i testowanie A / B, testowanie.
* [WebJobs] nie dostarczają zamiennika dla zadania zaplanowane na żądanie.
* Możesz [ciągłe wdrażanie] witryny tworząc witryny GitHub, TFS lub Mercurial.
* Możesz użyć [Application Insights] do monitorowania witryny.
* Obsługiwać witryny sieci Web i mobilnych interfejs API z tego samego kodu.

### <a name="upgrading-your-site"></a>Uaktualnianie witryny usług Mobile Services do usługi Azure Mobile Apps SDK
* W przypadku projektów opartych na środowisku Node.js server nowy [Zestaw SDK środowiska Node.js w aplikacjach mobilnych] oferuje następujące nowe funkcje. Na przykład możesz można teraz lokalne programowanie i debugowanie, użyj dowolnego środowiska Node.js w wersji nowszej 0.10 i dostosować przy użyciu dowolnego oprogramowania pośredniczącego Express.js.
* Aby uzyskać. Projekty z serwera opartego na sieci, nowa [pakiety NuGet zestawu SDK usługi Mobile Apps](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) mają większą swobodę w zależności NuGet.  Te pakiety obsługi uwierzytelniania przy użyciu nowej usługi App Service i narzędzia compose z jakimkolwiek projektem platformy ASP.NET. Aby dowiedzieć się więcej na temat uaktualniania, zobacz [uaktualnienia istniejącej usługi mobilnej platformy .NET w usłudze App Service](app-service-mobile-net-upgrading-from-mobile-services.md).

<!-- Images -->
[0]: ./media/app-service-mobile-migrating-from-mobile-services/migrate-to-app-service-button.PNG
[1]: ./media/app-service-mobile-migrating-from-mobile-services/migration-activity-monitor.png
[2]: ./media/app-service-mobile-migrating-from-mobile-services/triggering-job-with-postman.png

<!-- Links -->
[Cennik usługi aplikacji]: https://azure.microsoft.com/pricing/details/app-service/
[Application Insights]: ../azure-monitor/app/app-insights-overview.md
[Automatyczne skalowanie]: ../app-service/web-sites-scale.md
[Azure App Service]: ../app-service/overview.md
[klasyczny portal Azure]: https://manage.windowsazure.com
[Azure Portal]: https://portal.azure.com
[Azure Region]: https://azure.microsoft.com/regions/
[Plany usługi Azure Scheduler]: ../scheduler/scheduler-plans-billing.md
[ciągłe wdrażanie]: ../app-service/deploy-continuous-deployment.md
[Konwertuj mieszane przestrzeniami nazw usługi]: https://azure.microsoft.com/blog/updates-from-notification-hubs-independent-nuget-installation-model-pmt-and-more/
[curl]: https://curl.haxx.se/
[Niestandardowe nazwy domen]: ../app-service/app-service-web-tutorial-custom-domain.md
[Fiddler]: https://www.telerik.com/fiddler
[ogólnie dostępne w usłudze Azure App Service]: https://azure.microsoft.com/blog/announcing-general-availability-of-app-service-mobile-apps/
[Hybrid Connections]: ../app-service/app-service-hybrid-connections.md
[Logging]: ../app-service/troubleshoot-diagnostic-logs.md
[Zestaw SDK środowiska Node.js w aplikacjach mobilnych]: https://github.com/azure/azure-mobile-apps-node
[Mobile Services a App Service]: app-service-mobile-value-prop-migration-from-mobile-services.md
[Notification Hubs]: ../notification-hubs/notification-hubs-push-notification-overview.md
[Monitorowanie wydajności]: ../app-service/web-sites-monitor.md
[Postman]: https://www.getpostman.com/
[miejsc przejściowych]: ../app-service/deploy-staging-slots.md
[VNet]: ../app-service/web-sites-integrate-with-vnet.md
[Przykłady przekształcania XDT]: https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples
[Funkcje]: ../azure-functions/functions-overview.md

---
title: Migracja z usług mobilnych do aplikacji mobilnej usługi aplikacji
description: Dowiedz się, jak łatwo przeprowadzić migrację aplikacji usługi Mobile Services do aplikacji usługi Mobile App Service
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
ms.openlocfilehash: 5001704f47af0c7b07744f1dceb7aa58bdb6448c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32158872"
---
# <a name="article-top"></a>Migracji istniejącej usługi mobilnej Azure w usłudze Azure App Service
Z [ogólnej dostępności usługi Azure App Service], lokacji usług Azure Mobile Services można łatwo migracji w miejscu, aby korzystać ze wszystkich funkcji usługi Azure App Service.  W tym dokumencie opisano, czego można oczekiwać podczas migracji lokacji z usług Azure Mobile Services w usłudze Azure App Service.

## <a name="what-does-migration-do"></a>Migracja czego do swojej witryny
Migracja usługi mobilnej Azure włącza usługi mobilnej do [usłudze Azure App Service] aplikacji bez wpływu na kod.  Centra powiadomień, połączenie danych SQL, ustawień uwierzytelniania, zaplanowane zadania i nazwy domeny pozostają niezmienione.  Klientów mobilnych za pomocą usługi mobilnej Azure w dalszym ciągu działać normalnie.  Migracji powoduje ponowne uruchomienie usługi, gdy są przesyłane do usługi Azure App Service.

[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

## <a name="why-migrate"></a>Dlaczego należy dokonanie migracji lokacji
Microsoft jest rekomendowania dokonać migracji z usługi mobilnej Azure, aby móc korzystać z funkcji usługi aplikacji Azure, w tym:

* Nowe funkcje hosta, w tym [Webjob] i [niestandardowych nazw domen].
* Monitorowanie i rozwiązywanie problemów z [usługi Application Insights].
* Wbudowane narzędzia DevOps, łącznie z [przemieszczania miejsc], wycofywania i w środowisku produkcyjnym testowania.
* [Automatyczne skalowanie], równoważenie obciążenia, a [monitorowania wydajności].

Aby uzyskać więcej informacji o zaletach usługi Azure App Service, zobacz [vs usług Mobile Services. Usługi aplikacji] tematu.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Przed rozpoczęciem głównych pracę, witryny, należy kopię zapasową skryptów usługi mobilnej i bazy danych SQL.

## <a name="migrating-site"></a>Migrowanie witryn
Zostaną zmigrowane wszystkie lokacje w jednym regionie Azure.

Aby przeprowadzić migrację witryny:

1. Zaloguj się do [klasycznego portalu Azure].
2. Wybierz usługi mobilnej w regionie, w którym chcesz przeprowadzić migrację.
3. Kliknij przycisk **migracji do usługi App Service** przycisku.

   ![Przycisk migracji][0]
4. Przeczytaj migracji do okna dialogowego z usługi aplikacji.
5. Wprowadź nazwę usługi mobilnej w odpowiednim polu.  Na przykład, jeśli nazwa domeny to contoso.azure mobile.net, wprowadź *contoso* w odpowiednim polu.
6. Kliknij przycisk znaczników.

Monitorowanie stanu migracji w monitorze działania. Witryna jest wymienione jako *Migrowanie* w klasycznym portalu Azure.

  ![Monitorowanie aktywności migracji][1]

Każdy migracja może zająć od 3 do 15 minut na migrowanym usługi mobilnej.  Witryna jest nadal dostępny w trakcie migracji.
Witryna jest uruchamiany ponownie po zakończeniu procesu migracji.  Witryna jest niedostępna podczas procesu ponownego uruchomienia komputera, który może trwać kilka sekund.

## <a name="finalizing-migration"></a>Kończenie migracji
Warto zaplanować testowanie witryny z klienta mobilnego po zakończeniu procesu migracji.  Upewnij się, że można wykonywać wszystkie typowe Akcje klienta bez wprowadzania zmian do klientów urządzeń przenośnych.  

### <a name="update-app-service-tier"></a>Wybierz odpowiednią usługę aplikacji warstwy cenowej
Uzyskuje się większą elastyczność w cenowej po zakończeniu migracji do usługi Azure App Service.

1. Zaloguj się do witryny [portalu Azure].
2. Wybierz **wszystkie zasoby** lub **usługi aplikacji** następnie kliknij nazwę zmigrowane usługi mobilnej.
3. Domyślnie zostanie otwarty blok ustawień.
4. Kliknij przycisk **planu usługi App Service** w menu Ustawienia.
5. Kliknij przycisk **warstwy cenowej** kafelka.
6. Kliknij odpowiednie do własnych potrzeb, a następnie kliknij przycisk **wybierz**.  Może być konieczne kliknięcie **Wyświetl wszystkie** aby zobaczyć dostępne warstwy cenowe.

Jako punkt początkowy zaleca się następujące poziomy:

| Warstwy cenowej usługi mobilnej | Usługi aplikacji warstwy cenowej |
|:--- |:--- |
| Bezpłatna |Bezpłatna F1 |
| Podstawowa |B1 Basic |
| Standardowa (Standard) |Standardowa S1 |

Brak dużą elastyczność w wyborze prawo warstwę cenową dla aplikacji.  Zapoznaj się [App Service — ceny] szczegółowe informacje o cenach nowej usługi aplikacji.

> [!TIP]
> Zawiera warstwy standardowa usługi aplikacji dostęp do wielu funkcji, które chcesz użyć, w tym [przemieszczania miejsc], automatycznego tworzenia kopii zapasowej i automatyczne skalowanie.  Zapoznaj się z nowych funkcji, gdy istnieje!
>
>

### <a name="review-migration-scheduler-jobs"></a>Przejrzyj zadania migrowanych harmonogramu
Harmonogram zadań nie będą widoczne dopiero po zakończeniu migracji około 30 minut.  Zaplanowane zadania nadal uruchomione w tle.
Aby wyświetlić Twoje zaplanowane zadania są widoczne ponownie:

1. Zaloguj się do witryny [portalu Azure].
2. Wybierz **Przeglądaj >**, wprowadź **harmonogram** w *filtru* polu, a następnie wybierz **kolekcje harmonogramu**.

Istnieje ograniczona liczba wolnych harmonogram zadania dostępne po migracji.  Przejrzyj użycie i [plany harmonogramu Azure].

### <a name="configure-cors"></a>Konfigurowanie mechanizmu CORS w razie potrzeby
Współużytkowanie zasobów między źródłami to technika, aby umożliwić dostęp interfejs API sieci Web w innej domenie do witryny sieci Web.  Jeśli korzystasz z usług Azure Mobile Services z skojarzone witryny sieci Web, następnie należy skonfigurować CORS w ramach migracji.  Jeśli uzyskują dostęp do usług Azure Mobile Services wyłącznie z urządzeń przenośnych, CORS nie muszą być konfigurowane z wyjątkiem w rzadkich przypadkach.

Zmigrowane ustawienia mechanizmu CORS są dostępne jako **MS_CrossDomainWhitelist** ustawienia aplikacji.  Aby przeprowadzić migrację witryny w obiekcie mechanizmu CORS usługi App Service:

1. Zaloguj się do witryny [portalu Azure].
2. Wybierz **wszystkie zasoby** lub **usługi aplikacji** następnie kliknij nazwę zmigrowane usługi mobilnej.
3. Domyślnie zostanie otwarty blok ustawień.
4. Kliknij przycisk **CORS** w menu interfejsu API.
5. Wprowadź wszelkie dozwolone źródła w odpowiednim polu, naciskając klawisz Enter po każdym.
6. Po liście dozwolone źródła jest poprawny, kliknij przycisk Zapisz.

> [!TIP]
> Jest jedną z zalet funkcji Azure App Service można uruchamiać witryn sieci web i usługi mobilnej w na tej samej lokacji.  Aby uzyskać więcej informacji, zobacz [następne kroki](#next-steps) sekcji.
>
>

### <a name="download-publish-profile"></a>Pobierz nowy profil publikowania
Profil publikowania witryny jest zmieniany podczas migracji do usługi Azure App Service.  Zamierzasz opublikować witrynę sieci z poziomu programu Visual Studio, należy najpierw profilu publikowania.  Aby pobrać nowy profil publikowania:

1. Zaloguj się do witryny [portalu Azure].
2. Wybierz **wszystkie zasoby** lub **usługi aplikacji** następnie kliknij nazwę zmigrowane usługi mobilnej.
3. Kliknij przycisk **profilu publikowania Get**.

Plik ustawień publikacji zostanie pobrana do komputera.  Jest to zwykle *sitename*. Ustawień publikacji.  Importowanie ustawień publikowania do istniejącego projektu:

1. Otwórz program Visual Studio i projekt usługi mobilnej Azure.
2. Kliknij prawym przyciskiem myszy projekt w **Eksploratora rozwiązań** i wybierz **publikowania...**
3. Kliknij pozycję **Importuj**
4. Kliknij przycisk **Przeglądaj** i wybierz z pobranego pliku ustawień publikowania.  Kliknij przycisk **OK**.
5. Kliknij przycisk **sprawdzania poprawności połączenia** aby zapewnić działanie ustawień publikowania.
6. Kliknij przycisk **publikowania** opublikowanie witryny.

## <a name="working-with-your-site"></a>Praca z Twojej lokacji po migracji
Rozpocząć pracę z usługą nowych aplikacji w [portalu Azure] po migracji.  Poniżej przedstawiono niektóre informacje o na określonych operacji, które zostały użyte do wykonania w [klasycznego portalu Azure], wraz z ich odpowiedniki usługi aplikacji.

### <a name="publishing-your-site"></a>Pobieranie i publikowanie migrowanych witryny
Witryna jest dostępna za pośrednictwem git i ftp i należy ponownie opublikować z różnych mechanizmów różne, w tym WebDeploy, TFS Mercurial, GitHub i FTP.  Poświadczenia wdrożenia są migrowane z resztą witryny.  Jeśli nie określono poświadczeń wdrożenia lub nie pamiętać, można zresetować je:

1. Zaloguj się do witryny [portalu Azure].
2. Wybierz **wszystkie zasoby** lub **usługi aplikacji** następnie kliknij nazwę zmigrowane usługi mobilnej.
3. Domyślnie zostanie otwarty blok ustawień.
4. Kliknij przycisk **poświadczenia wdrażania** w publikacji menu.
5. Wprowadź nowe poświadczenia wdrożenia w odpowiednich polach, a następnie kliknij przycisk Zapisz.

Te poświadczenia można użyć do witryny za pomocą narzędzia git clone lub konfigurowanie zautomatyzowanych wdrożeń z usługi GitHub, TFS lub Mercurial.  Aby uzyskać więcej informacji zobacz [dokumentacja wdrażania usługi aplikacji Azure].

### <a name="appsettings"></a>Ustawienia aplikacji
Większość ustawień zmigrowanych usługi mobilnej są dostępne za pośrednictwem ustawień aplikacji.  Zostanie wyświetlona lista ustawień aplikacji z [portalu Azure].
Aby wyświetlić lub zmienić ustawienia aplikacji:

1. Zaloguj się do witryny [portalu Azure].
2. Wybierz **wszystkie zasoby** lub **usługi aplikacji** następnie kliknij nazwę zmigrowane usługi mobilnej.
3. Domyślnie zostanie otwarty blok ustawień.
4. Kliknij przycisk **ustawienia aplikacji** w menu Ogólne.
5. Przewiń do sekcji ustawień aplikacji i znajdowanie ustawienia aplikacji.
6. Kliknij wartość ustawienia aplikacji, aby edytować wartość.  Kliknij przycisk **zapisać** można zapisać wartości.

W tym samym czasie, należy zaktualizować wiele ustawień aplikacji.

> [!TIP]
> Istnieją dwa ustawienia aplikacji o tej samej wartości.  Na przykład może zostać wyświetlony *ApplicationKey* i *MS\_ApplicationKey*.  Zaktualizuj ustawienia zarówno aplikacji w tym samym czasie.
>
>

### <a name="authentication"></a>Uwierzytelnianie
Wszystkie ustawienia uwierzytelniania są dostępne jako ustawienia aplikacji w witrynie zmigrowane.  Aby zaktualizować ustawienia uwierzytelniania, w przypadku modyfikowania ustawień odpowiednich aplikacji.  W poniższej tabeli przedstawiono ustawienia aplikacji właściwe dla dostawcy uwierzytelniania:

| Dostawca | Identyfikator klienta | Klucz tajny klienta | Inne ustawienia |
|:--- |:--- |:--- |:--- |
| Konto Microsoft |**MS\_MicrosoftClientID** |**MS\_MicrosoftClientSecret** |**MS\_MicrosoftPackageSID** |
| Facebook |**MS\_FacebookAppID** |**MS\_FacebookAppSecret** | |
| Twitter |**MS\_TwitterConsumerKey** |**MS\_TwitterConsumerSecret** | |
| Google |**MS\_GoogleClientID** |**MS\_GoogleClientSecret** | |
| Azure AD |**MS\_AadClientID** | |**MS\_AadTenants** |

Uwaga: **MS\_AadTenants** jest przechowywana jako rozdzielanej przecinkami listy domen dzierżawy (pola "Dozwolone dzierżawcy" w portalu usług mobilnych).

> [!WARNING]
> **Nie używa mechanizmów uwierzytelniania w menu Ustawienia**
>
> Usługa aplikacji Azure zapewnia osobne "bez kodu" uwierzytelnianie i autoryzacja system w obszarze *uwierzytelniania / autoryzacji* (przestarzałe) i ustawień menu *uwierzytelniania Mobile* opcji w menu Ustawienia.  Te opcje są niezgodne z migrowanych usługi mobilnej Azure.  Możesz [uaktualniania lokacji](app-service-mobile-net-upgrading-from-mobile-services.md) mógł korzystać z uwierzytelniania usługi Azure App Service.
>
>

### <a name="easytables"></a>Dane
*Danych* karty w usłudze Mobile Services została zastąpiona przez *łatwe tabel* w portalu Azure.  Aby uzyskać dostęp do tabel łatwe:

1. Zaloguj się do witryny [portalu Azure].
2. Wybierz **wszystkie zasoby** lub **usługi aplikacji** następnie kliknij nazwę zmigrowane usługi mobilnej.
3. Domyślnie zostanie otwarty blok ustawień.
4. Kliknij przycisk **łatwe tabel** w przenośnych menu.

Tabelę można dodać, klikając **Dodaj** przycisk lub dostępu do istniejących tabel, klikając nazwę tabeli.  Istnieją różne operacje, które można wykonać z poziomu tego bloku, w tym:

* Zmienianie uprawnień tabeli
* Edytowanie operacyjne skryptów
* Zarządzanie schematem tabeli
* Usuwanie tabeli
* Czyszczenie zawartości tabeli
* Usuwanie określonych wierszy w tabeli

### <a name="easyapis"></a>API
*Interfejsu API* karty w usłudze Mobile Services została zastąpiona przez *łatwe interfejsów API* w portalu Azure.  Aby uzyskać dostęp do interfejsów API łatwe:

1. Zaloguj się do witryny [portalu Azure].
2. Wybierz **wszystkie zasoby** lub **usługi aplikacji** następnie kliknij nazwę zmigrowane usługi mobilnej.
3. Domyślnie zostanie otwarty blok ustawień.
4. Kliknij przycisk **łatwe interfejsów API** w przenośnych menu.

Swoje migrowanych interfejsy API są już wyświetlane w bloku.  Można również dodać interfejsu API z poziomu tego bloku.  Aby zarządzać określonego interfejsu API, kliknij przycisk interfejsu API.
Za pomocą nowego bloku można dostosowanie uprawnień i edytowania skryptów dla interfejsu API.

### <a name="on-demand-jobs"></a>Zadania harmonogramu
Wszystkie zadania harmonogramu są dostępne za pośrednictwem sekcji kolekcji zadań harmonogramu.  Aby uzyskać dostęp do harmonogramu zadań:

1. Zaloguj się do witryny [portalu Azure].
2. Wybierz **Przeglądaj >**, wprowadź **harmonogram** w *filtru* polu, a następnie wybierz **kolekcje harmonogramu**.
3. Wybierz kolekcję zadań dla witryny.  Szablon ma nazwę *sitename*-zadania.
4. Kliknij przycisk **ustawienia**.
5. Kliknij przycisk **zadania harmonogramu** w obszarze Zarządzanie.

Zaplanowane zadania są wyświetlane z częstotliwością określone przed migracją.  Zadania na żądanie są wyłączone.  Aby uruchomić zadanie na żądanie:

1. Wybierz zadania, które chcesz uruchomić.
2. W razie potrzeby kliknij **włączyć** można włączyć zadania.
3. Kliknij przycisk **ustawienia**, następnie **harmonogram**.
4. Wybierz opcję ponownego **raz**, następnie kliknij przycisk **Zapisz**

Twoje zadania na żądanie znajdują się w `App_Data/config/scripts/scheduler post-migration`.  Zalecamy przekształcenie wszystkie zadania na żądanie do [Webjob] lub [funkcji].  Napisać nowy harmonogram zadań [Webjob] lub [funkcji].

### <a name="notification-hubs"></a>Centra powiadomień
Mobile Services używa centra powiadomień dla powiadomień wypychanych.  Następujących ustawień aplikacji są używane do łączenia z Centrum powiadomień z usługą Mobile po migracji:

| Ustawienia aplikacji | Opis |
|:--- |:--- |
| **MS\_PushEntityNamespace** |Namespace Centrum powiadomień |
| **MS\_NotificationHubName** |Nazwa centrum powiadomień |
| **MS\_NotificationHubConnectionString** |Parametry połączenia Centrum powiadomień |
| **MS\_NamespaceName** |Alias MS_PushEntityNamespace |

Centrum powiadomień jest zarządzana za pomocą [portalu Azure].  Zanotuj nazwę Centrum powiadomień (można go znaleźć za pomocą ustawień aplikacji):

1. Zaloguj się do witryny [portalu Azure].
2. Wybierz **Przeglądaj**>, a następnie wybierz pozycję **centra powiadomień**
3. Kliknij nazwę Centrum powiadomień, skojarzone z usługi mobilnej.

> [!NOTE]
> Jeśli Centrum powiadomień jest typu "Mixed", nie jest widoczne.  "Mieszany" wpisz powiadomień koncentratory wykorzystywać zarówno usługi Notification Hubs i starszych funkcji usługi Service Bus.  [Konwertuj mieszanych obszary nazw] przed kontynuowaniem.  Po zakończeniu konwersji Centrum powiadomień zostanie wyświetlony w [portalu Azure].
>
>

Aby uzyskać więcej informacji, przejrzyj [usługi Notification Hubs] dokumentacji.

> [!TIP]
> Funkcje zarządzania centra powiadomień w [portalu Azure] są nadal w wersji zapoznawczej.  [klasycznego portalu Azure] pozostaje dostępna, zarządzanie centrów powiadomień.
>
>

### <a name="legacy-push"></a>Ustawienia starszych wypychania
Skonfigurowanie na usługi mobilnej przed wprowadzeniem na centra powiadomień wypychanych, czy używasz *starszych wypychania*.  Jeśli używasz wypychania i nie ma na liście konfiguracji centrum powiadomień, a następnie prawdopodobnie używasz *starszych wypychania*.  Ta funkcja jest migrowana z innymi funkcjami.  Jednak zaleca się uaktualnienie do usługi Notification Hubs zaraz po zakończeniu migracji.

W międzyczasie wszystkie ustawienia push starszej wersji (z wyjątkiem zauważalne certyfikat usługi APN) są dostępne w ustawieniach aplikacji.  Zaktualizuj certyfikat usługi APN za pomocą zastąpienia odpowiedniego pliku w systemie plików.

### <a name="app-settings"></a>Inne ustawienia aplikacji
Następujące ustawienia dodatkowe aplikacji są migrowane z usługi mobilnej i dostępnych w ramach *ustawienia* > *ustawień aplikacji*:

| Ustawienia aplikacji | Opis |
|:--- |:--- |
| **MS\_MobileServiceName** |Nazwa aplikacji |
| **MS\_MobileServiceDomainSuffix** |Prefiks domeny. tj azure-mobile.net |
| **MS\_ApplicationKey** |Klucz aplikacji |
| **MS\_MasterKey** |Klucz główny aplikacji |

Klucz aplikacji i klucz główny są identyczne z kluczy aplikacji z oryginalnego usługi mobilnej.  W szczególności klucz aplikacji są wysyłane przez klientów mobilnych można sprawdzić poprawności ich stosowania przenośnych interfejsu API.

### <a name="cliequivalents"></a>Odpowiedniki wiersza polecenia
Można już używać *azure przenośnych* polecenia do zarządzania witryną usług Azure Mobile Services.  Zamiast tego zostały zastąpione wiele funkcji *usługi azure site* polecenia.  Skorzystaj z poniższej tabeli, aby znaleźć odpowiedniki dla typowych poleceń:

| *Azure Mobile* polecenia | Odpowiednik *usługi Azure Site* polecenia |
|:--- |:--- |
| lokalizacje przenośnych |Lista lokalizacji lokacji |
| listy dla urządzeń przenośnych |Lista witryn |
| Pokaż przenośnych *nazwy* |Pokaż lokacji *nazwy* |
| ponowne uruchomienie przenośnych *nazwy* |ponowne uruchomienie witryny *nazwy* |
| Wdróż go ponownie przenośnych *nazwy* |Wdróż go ponownie wdrożenia lokacji *commitId* *nazwy* |
| przenośne zestawu kluczy *nazwa* *typu* *wartości* |Usuń appsetting lokacji *klucza* *nazwy* <br/> Dodaj witrynę appsetting *klucza*=*wartość* *nazwy* |
| Lista przenośnych konfiguracji *nazwy* |Lista appsetting lokacji *nazwy* |
| Pobierz przenośnych config *nazwa* *klucza* |Pokaż appsetting lokacji *klucza* *nazwy* |
| zestaw przenośnych config *nazwa* *klucza* |Usuń appsetting lokacji *klucza* *nazwy* <br/> Dodaj witrynę appsetting *klucza*=*wartość* *nazwy* |
| Lista przenośnych domeny *nazwy* |Lista domeny lokacji *nazwy* |
| Dodawanie domeny przenośnych *nazwa* *domeny* |Dodawanie domeny lokacji *domeny* *nazwy* |
| Usuń domeny przenośnych *nazwy* |Usuwanie domeny witryny *domeny* *nazwy* |
| Pokaż przenośnych skali *nazwy* |Pokaż lokacji *nazwy* |
| Zmiana skali przenośnych *nazwy* |Tryb skali witryn *tryb* *nazwy* <br /> lokacji wystąpień skali *wystąpień* *nazwy* |
| Lista przenośnych appsetting *nazwy* |Lista appsetting lokacji *nazwy* |
| Dodaj przenośnych appsetting *nazwa* *klucza* *wartości* |Dodaj witrynę appsetting *klucza*=*wartość* *nazwy* |
| Usuń przenośnych appsetting *nazwa* *klucza* |Usuń appsetting lokacji *klucza* *nazwy* |
| Pokaż przenośnych appsetting *nazwa* *klucza* |Usuń appsetting lokacji *klucza* *nazwy* |

Aktualizacja uwierzytelniania lub ustawienia powiadomień wypychanych, aktualizując odpowiednie ustawienie aplikacji.
Edytowanie plików i opublikować swoją witrynę przy użyciu protokołu ftp lub git.

### <a name="diagnostics"></a>Rejestrowanie i Diagnostyka
Rejestrowanie diagnostyczne zwykle jest wyłączony w usłudze Azure App Service.  Aby włączyć rejestrowanie diagnostyczne:

1. Zaloguj się do witryny [portalu Azure].
2. Wybierz **wszystkie zasoby** lub **usługi aplikacji** następnie kliknij nazwę zmigrowane usługi mobilnej.
3. Domyślnie zostanie otwarty blok ustawień.
4. Wybierz **dzienników diagnostycznych** w menu funkcji.
5. Kliknij przycisk **ON** dla następujące dzienniki: **rejestrowania aplikacji (systemu plików)**, **szczegółowe komunikaty o błędach**, i **śledzenie nieudanych żądań**
6. Kliknij przycisk **systemu plików** dla rejestrowania serwera sieci Web
7. Kliknij pozycję **Zapisz**

Aby wyświetlić dzienniki:

1. Zaloguj się do witryny [portalu Azure].
2. Wybierz **wszystkie zasoby** lub **usługi aplikacji** następnie kliknij nazwę zmigrowane usługi mobilnej.
3. Kliknij przycisk **narzędzia** przycisku
4. Wybierz **strumienia dziennika** w OBSERVE menu.

Dzienniki są wyświetlane w oknie, ponieważ są one generowane.  Możesz również pobrać dzienniki dla późniejszej analizy przy użyciu poświadczeń wdrożenia. Aby uzyskać więcej informacji, zobacz [rejestrowanie] dokumentacji.

## <a name="known-issues"></a>Znane problemy
### <a name="deleting-a-migrated-mobile-app-clone-causes-a-site-outage"></a>Usuwanie klon migracji aplikacji mobilnej powoduje awarii lokacji
Jeśli klonowanie zmigrowane usługi mobilnej przy użyciu programu Azure PowerShell, należy usunąć klonu wpis DNS dla usługi produkcji zostaną usunięte.  Witryny nie jest dostępny z Internetu.  

Rozwiązanie: Jeśli chcesz sklonować lokacji, to zrobić za pośrednictwem portalu.

### <a name="changing-webconfig-does-not-work"></a>Zmienianie pliku Web.config nie działa
Jeśli masz witryny ASP.NET zmieni się na `Web.config` pliku nie zostać zastosowane.  Usługa aplikacji Azure tworzy odpowiedniej `Web.config` pliku podczas uruchamiania do obsługi środowiska uruchomieniowego usługi Mobile Services.  Można zastąpić niektórych ustawień (np. nagłówki niestandardowe) przy użyciu pliku transformacji XML.  Utwórz plik w nazwie `applicationHost.xdt` — ten plik musi kończyć w `D:\home\site` katalogu w usłudze Azure.  Przekaż `applicationHost.xdt` plików za pomocą skryptu wdrażania niestandardowych lub bezpośrednio za pomocą Kudu.  Poniżej przedstawiono przykładowy dokument:

```
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

Aby uzyskać więcej informacji, zobacz [przykłady przekształcenie XDT] dokumentacji w witrynie GitHub.

### <a name="migrated-mobile-services-cannot-be-added-to-traffic-manager"></a>Nie można dodać zmigrowane usługi mobilnej do usługi Traffic Manager
Podczas tworzenia profilu usługi Traffic Manager nie można bezpośrednio wybrać zmigrowane usługi mobilnej do profilu.  Użyj "zewnętrznego punktu końcowego."  Zewnętrznego punktu końcowego można dodawać tylko za pomocą programu PowerShell.  Aby uzyskać więcej informacji, zobacz [samouczek Menedżera ruchu](https://azure.microsoft.com/blog/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/).

## <a name="next-steps"></a>Następne kroki
Teraz, gdy aplikacja jest migrowana do usługi App Service, istnieją nawet więcej funkcji, których można użyć:

* Wdrożenie [przemieszczania miejsc] umożliwiają etap zmian witryny i wykonywać A / B, testowanie.
* [Webjob] zapewnieniu jej zamiennika dla zadania zaplanowane na żądanie.
* Możesz [stale wdrażanie] witryny przez łączenie z witryny GitHub, TFS lub Mercurial.
* Można użyć [usługi Application Insights] do monitorowania witryny.
* Udostępniają witryny sieci Web i interfejs API Mobile z tego samego kodu.

### <a name="upgrading-your-site"></a>Uaktualnianie lokacji usług Mobile Services do zestawu SDK usługi Azure Mobile Apps
* Dla projektów z serwera opartego na Node.js nowe [zestaw SDK usługi Mobile Apps Node.js] udostępnia kilka nowych funkcji. Na przykład można teraz nie lokalne działania projektowe i debugowanie, użyj dowolnej wersji środowiska Node.js powyżej 0.10 i dostosować za pomocą wszystkich programów pośredniczących Express.js.
* Aby uzyskać. Projekty serwera opartego na sieci, nowa [pakietów NuGet zestawu SDK aplikacji mobilnych](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) zależności NuGet uzyskuje się większą elastyczność.  Te pakiety obsługi nowych uwierzytelniania usługi aplikacji i tworzą z żadnym projektem platformy ASP.NET. Aby dowiedzieć się więcej na temat uaktualniania, zobacz [uaktualnienia istniejącej usługi mobilnej platformy .NET do usługi App Service](app-service-mobile-net-upgrading-from-mobile-services.md).

<!-- Images -->
[0]: ./media/app-service-mobile-migrating-from-mobile-services/migrate-to-app-service-button.PNG
[1]: ./media/app-service-mobile-migrating-from-mobile-services/migration-activity-monitor.png
[2]: ./media/app-service-mobile-migrating-from-mobile-services/triggering-job-with-postman.png

<!-- Links -->
[App Service — ceny]: https://azure.microsoft.com/pricing/details/app-service/
[usługi Application Insights]: ../application-insights/app-insights-overview.md
[Automatyczne skalowanie]: ../app-service/web-sites-scale.md
[usłudze Azure App Service]: ../app-service/app-service-web-overview.md
[klasycznego portalu Azure]: https://manage.windowsazure.com
[portalu Azure]: https://portal.azure.com
[Azure Region]: https://azure.microsoft.com/regions/
[plany harmonogramu Azure]: ../scheduler/scheduler-plans-billing.md
[stale wdrażanie]: ../app-service/app-service-continuous-deployment.md
[Konwertuj mieszanych obszary nazw]: https://azure.microsoft.com/blog/updates-from-notification-hubs-independent-nuget-installation-model-pmt-and-more/
[curl]: http://curl.haxx.se/
[niestandardowych nazw domen]: ../app-service/app-service-web-tutorial-custom-domain.md
[Fiddler]: http://www.telerik.com/fiddler
[ogólnej dostępności usługi Azure App Service]: https://azure.microsoft.com/blog/announcing-general-availability-of-app-service-mobile-apps/
[Hybrid Connections]: ../app-service/app-service-hybrid-connections.md
[rejestrowanie]: ../app-service/web-sites-enable-diagnostic-log.md
[zestaw SDK usługi Mobile Apps Node.js]: https://github.com/azure/azure-mobile-apps-node
[vs usług Mobile Services. Usługi aplikacji]: app-service-mobile-value-prop-migration-from-mobile-services.md
[usługi Notification Hubs]: ../notification-hubs/notification-hubs-push-notification-overview.md
[monitorowania wydajności]: ../app-service/web-sites-monitor.md
[Postman]: http://www.getpostman.com/
[przemieszczania miejsc]: ../app-service/web-sites-staged-publishing.md
[VNet]: ../app-service/web-sites-integrate-with-vnet.md
[przykłady przekształcenie XDT]: https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples
[funkcji]: ../azure-functions/functions-overview.md

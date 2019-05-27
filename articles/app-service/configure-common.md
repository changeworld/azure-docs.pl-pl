---
title: Konfigurowanie aplikacji w portalu — usłudze Azure App Service
description: Dowiedz się skonfigurować typowych ustawień dla aplikacji usługi App Service w witrynie Azure portal.
keywords: Aplikacja sieci web, ustawienia aplikacji, zmienne środowiskowe w usłudze Azure app service
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: bcc970375120f76e4ec8a90f487d251296f92dba
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957913"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>Konfigurowanie aplikacji usługi App Service w witrynie Azure portal

W tym temacie opisano sposób konfigurowania typowych ustawień dla aplikacji sieci web, zapleczem aplikacji mobilnej lub aplikacji interfejsu API za pomocą [Azure Portal].

## <a name="configure-app-settings"></a>Konfigurowanie ustawień aplikacji

W usłudze App Service możesz użyć ustawienia aplikacji, takie jak zmienne środowiskowe. W [Azure Portal], przejdź do strony zarządzania aplikacji. W menu po lewej stronie aplikacji, kliknij przycisk **konfiguracji** > **ustawienia aplikacji**.

![Ustawienia aplikacji](./media/configure-common/open-ui.png)

Dla deweloperów platformy ASP.NET i ASP.NET Core, ustawienie ustawienia aplikacji w usłudze App Service są podobne do ustawiania ich w `<appSettings>` w *Web.config*, ale wartości w usłudze App Service przesłonięte w *Web.config*. Ustawienia środowiska deweloperskiego (np. lokalne hasło MySQL) można przechowywać w *Web.config*, ale tajemnice produkcji (np. hasło bazy danych Azure MySQL) bezpieczne w usłudze App Service. Ten sam kod używa ustawienia środowiska deweloperskiego, gdy debugujesz lokalnie i używa tajnych produkcji podczas wdrażania na platformie Azure.

Inne stosy języka podobnie, uzyskaj ustawienia aplikacji jako zmienne środowiskowe w czasie wykonywania. Aby poznać konkretne kroki stos języka zobacz:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Kontenerów niestandardowych](containers/configure-custom-container.md#configure-environment-variables)

Ustawienia aplikacji są zawsze szyfrowane, gdy przechowywany (zaszyfrowanych danych w spoczynku).

> [!NOTE]
> Ustawienia aplikacji może być również rozwiązana z [usługi Key Vault](/azure/key-vault/) przy użyciu [odwołuje się do usługi Key Vault](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Pokaż ukryte wartości

Domyślnie wartości ustawień aplikacji są ukryte w portalu dla zabezpieczeń. Aby wyświetlić ukryte wartości ustawienia aplikacji, kliknij **wartość** pola tego ustawienia. Aby wyświetlić wartości wszystkich ustawień aplikacji, kliknij przycisk **Pokaż wartość** przycisku.

### <a name="add-or-edit"></a>Dodawanie lub edytowanie

Aby dodać nowe ustawienie aplikacji, kliknij **nowe ustawienie aplikacji**. W oknie dialogowym możesz [się działać zgodnie z ustawieniem bieżącym gnieździe](deploy-staging-slots.md#which-settings-are-swapped).

Aby zmodyfikować to ustawienie, kliknij **Edytuj** przycisk po prawej stronie.

Po zakończeniu kliknij przycisk **aktualizacji**. Nie zapomnij kliknąć **Zapisz** w **konfiguracji** strony.

> [!NOTE]
> Domyślny kontener systemu Linux lub niestandardowego kontenera systemu Linux, wszelkie zagnieżdżone JSON struktury klucza w Nazwa ustawienia aplikacji, takich jak `ApplicationInsights:InstrumentationKey` musi być skonfigurowana w usłudze App Service jako `ApplicationInsights__InstrumentationKey` jako nazwę klucza. Innymi słowy, dowolnej `:` powinna zostać zastąpiona przez `__` (podwójne podkreślenie).
>

### <a name="edit-in-bulk"></a>Edytuj zbiorczo

Aby dodać lub edytować ustawienia aplikacji w trybie zbiorczym, kliknij przycisk **Zaawansowane edytowanie** przycisku. Po zakończeniu kliknij przycisk **aktualizacji**. Nie zapomnij kliknąć **Zapisz** w **konfiguracji** strony.

Ustawienia aplikacji są następujące formatowanie JSON:

```json
[
  {
    "name": "<key-1>",
    "value": "<value-1>",
    "slotSetting": false
  },
  {
    "name": "<key-2>",
    "value": "<value-2>",
    "slotSetting": false
  },
  ...
]
```

## <a name="configure-connection-strings"></a>Konfigurowanie parametrów połączenia

W [Azure Portal], przejdź do strony zarządzania aplikacji. W menu po lewej stronie aplikacji, kliknij przycisk **konfiguracji** > **ustawienia aplikacji**.

![Ustawienia aplikacji](./media/configure-common/open-ui.png)

Dla deweloperów platformy ASP.NET i ASP.NET Core, ustawienie parametrów połączenia w usłudze App Service są podobne do ustawiania ich w `<connectionStrings>` w *Web.config*, ale wartościami ustawionymi w usłudze App Service przesłonięte w *wplikuWeb.config*. Ustawienia środowiska deweloperskiego (np. plik bazy danych) można przechowywać w *Web.config* i produkcji wpisów tajnych, (np. poświadczenia bazy danych SQL) bezpieczne w usłudze App Service. Ten sam kod używa ustawienia środowiska deweloperskiego, gdy debugujesz lokalnie i używa tajnych produkcji podczas wdrażania na platformie Azure.

Dla innych stosów język jest lepiej używać [ustawienia aplikacji](#configure-app-settings) zamiast tego, ponieważ parametry połączenia wymagają specjalne formatowanie w zmiennej kluczy w, aby uzyskać dostęp do wartości. W tym miejscu jest jednak jeden wyjątek: niektóre typy bazy danych Azure kopie zapasowe są tworzone wraz z jej konfigurowania ich parametrów połączenia w aplikacji. Aby uzyskać więcej informacji, zobacz [kopiami zapasowymi](manage-backup.md#what-gets-backed-up). Jeśli nie potrzebujesz zautomatyzowane tworzenie kopii zapasowej, użyj ustawień aplikacji.

W czasie wykonywania parametry połączenia są dostępne jako zmienne środowiskowe, prefiks z następujących typów połączeń:

* Program SQL Server: `SQLCONNSTR_`
* MySQL: `MYSQLCONNSTR_`
* Baza danych SQL: `SQLAZURECONNSTR_`
* Niestandardowy: `CUSTOMCONNSTR_`

Na przykład parametry połączenia MySql o nazwie *connectionstring1* może być dostępna jako zmienną środowiskową `MYSQLCONNSTR_connectionString1`. Aby poznać konkretne kroki stos języka zobacz:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Kontenerów niestandardowych](containers/configure-custom-container.md#configure-environment-variables)

Parametry połączenia są zawsze szyfrowane, gdy przechowywany (zaszyfrowanych danych w spoczynku).

> [!NOTE]
> Parametry połączenia można również rozwiązana z [usługi Key Vault](/azure/key-vault/) przy użyciu [odwołuje się do usługi Key Vault](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Pokaż ukryte wartości

Domyślnie wartości dla parametrów połączeń są ukryte w portalu dla zabezpieczeń. Aby wyświetlić ukryte wartość parametrów połączenia, wystarczy kliknąć **wartość** pola tego ciągu. Aby wyświetlić wartości wszystkich parametrów połączenia, kliknij przycisk **Pokaż wartość** przycisku.

### <a name="add-or-edit"></a>Dodawanie lub edytowanie

Aby dodać nowe parametry połączenia, kliknij **nowe parametry połączenia**. W oknie dialogowym możesz [używany ciąg połączenia w bieżącym gnieździe](deploy-staging-slots.md#which-settings-are-swapped).

Aby zmodyfikować to ustawienie, kliknij **Edytuj** przycisk po prawej stronie.

Po zakończeniu kliknij przycisk **aktualizacji**. Nie zapomnij kliknąć **Zapisz** w **konfiguracji** strony.

### <a name="edit-in-bulk"></a>Edytuj zbiorczo

Aby dodać lub edytować parametrów połączenia w trybie zbiorczym, kliknij przycisk **Zaawansowane edytowanie** przycisku. Po zakończeniu kliknij przycisk **aktualizacji**. Nie zapomnij kliknąć **Zapisz** w **konfiguracji** strony.

Parametry połączenia mają następujące formatowanie JSON:

```json
[
  {
    "name": "name-1",
    "value": "conn-string-1",
    "type": "SQLServer",
    "slotSetting": false
  },
  {
    "name": "name-2",
    "value": "conn-string-2",
    "type": "PostgreSQL",
    "slotSetting": false
  },
  ...
]
```

<a name="platform"></a>
<a name="alwayson"></a>

## <a name="configure-general-settings"></a>Konfigurowanie ogólnych ustawień

W [Azure Portal], przejdź do strony zarządzania aplikacji. W menu po lewej stronie aplikacji, kliknij przycisk **konfiguracji** > **ustawienia aplikacji**.

![Ustawienia ogólne](./media/configure-common/open-general.png)

W tym miejscu można skonfigurować niektóre typowe ustawienia dla aplikacji. Niektóre ustawienia wymagają [skalowanie w górę do wyższej warstwy cenowej](web-sites-scale.md).

- **Stack — ustawienia**: Stos oprogramowania, aby uruchomić aplikację, w tym języku i za pomocą wersji zestawu SDK. W przypadku aplikacji systemu Linux i aplikacji kontenera niestandardowego można również ustawić opcjonalny uruchamiania polecenia lub pliku.
- **Ustawienia platformy**: Umożliwia skonfigurowanie ustawień dla platformy hostingowej, w tym:
    - **Liczba bitów**: 32-bitową lub 64-bitowych.
    - **Protokół WebSocket**: Aby uzyskać [ASP.NET SignalR] lub [biblioteki socket.io](https://socket.io/), na przykład.
    - **Zawsze włączone**: Zachowaj ją załadować nawet wtedy, gdy nie występuje ruch. Należy ją włączyć w przypadku ciągłych zadań Webjob lub zadań Webjob, które są wyzwalane za pomocą wyrażeń CRON.
    - **Wersja potoku zarządzanego**: Usługi IIS [tryb potokowy]. Ustaw ją na **klasycznego** w przypadku starszych aplikacji, która wymaga starszej wersji usług IIS.
    - **Wersja HTTP**: Ustaw **2.0** włączyć obsługę [HTTPS/2](https://wikipedia.org/wiki/HTTP/2) protokołu.
    > [!NOTE]
    > Większość nowoczesnych przeglądarek obsługuje protokół HTTP/2 za pośrednictwem protokołu TLS tylko wtedy, gdy niezaszyfrowane ruch w dalszym ciągu używa protokołu HTTP/1.1. Aby upewnić się, że klient przeglądarki połączyć się z aplikacji przy użyciu protokołu HTTP/2 albo [zakup certyfikatu usługi App Service](web-sites-purchase-ssl-web-site.md) dla domeny niestandardowej aplikacji lub [powiązać certyfikat innej](app-service-web-tutorial-custom-ssl.md).
    - **Koligacja ARR**: W przypadku wdrożenia w wielu wystąpieniach upewnij się, że klient jest kierowane do tego samego wystąpienia, przez cały okres istnienia sesji. Można ustawić tę opcję, **poza** dla aplikacji bezstanowych.
- **Debugowanie**: Włączanie debugowania zdalnego dla [ASP.NET](troubleshoot-dotnet-visual-studio.md#remotedebug), [platformy ASP.NET Core](/visualstudio/debugger/remote-debugging-azure), lub [Node.js](containers/configure-language-nodejs.md#debug-remotely) aplikacji. Ta opcja powoduje wyłączenie automatycznie po upływie 48 godzin.
- **Przychodzące certyfikaty klientów**: Wymagaj certyfikatów klienta w [wzajemnego uwierzytelniania](app-service-web-configure-tls-mutual-auth.md).

## <a name="configure-default-documents"></a>Konfigurowanie dokumentów domyślnych

To ustawienie dotyczy tylko aplikacji Windows.

W [Azure Portal], przejdź do strony zarządzania aplikacji. W menu po lewej stronie aplikacji, kliknij przycisk **konfiguracji** > **domyślne dokumenty**.

![Ustawienia ogólne](./media/configure-common/open-documents.png)

Dokument domyślny jest strony sieci web, która jest wyświetlana na główny adres URL witryny sieci Web. Pierwszy plik dopasowania na liście jest używany. Aby dodać nowy dokument domyślny, kliknij **nowy dokument**. Nie zapomnij kliknąć **Zapisz**.

Jeśli aplikacja korzysta z modułów, które kierują na podstawie adresu URL zamiast obsługująca zawartość statyczną, nie ma potrzeby dla dokumentów domyślnych.

## <a name="configure-path-mappings"></a>Skonfiguruj mapowania ścieżki

W [Azure Portal], przejdź do strony zarządzania aplikacji. W menu po lewej stronie aplikacji, kliknij przycisk **konfiguracji** > **mapowania ścieżki**.

![Ustawienia ogólne](./media/configure-common/open-path.png)

**Ścieżka mapowania** strona zawiera różnych rzeczy, w zależności od typu systemu operacyjnego.

### <a name="windows-apps-uncontainerized"></a>Aplikacje Windows (uncontainerized)

Dla aplikacji Windows można dostosować mapowania programu obsługi usług IIS i wirtualne aplikacje i katalogi.

Mapowania obsługi umożliwiają dodawanie procesorów skryptu niestandardowego do obsługi żądań dla określonych rozszerzeń plików. Aby dodać niestandardowy program obsługi, kliknij **nowy program obsługi**. Skonfiguruj program obsługi w następujący sposób:

- **Rozszerzenie**. Rozszerzenie pliku, które mają być obsługiwane, takich jak  *\*PHP* lub *handler.fcgi*.
- **Procesor skryptów**. Ścieżka bezwzględna procesora skryptów do Ciebie. Żądania dostępu do plików, zgodne z rozszerzeniem pliku są przetwarzane przez procesora skryptów. Użyj ścieżki `D:\home\site\wwwroot` do odwoływania się do katalogu głównego aplikacji.
- **Argumenty**. Opcjonalne argumenty wiersza polecenia dla procesora skryptów.

Każda aplikacja ma domyślna ścieżka katalogu głównego (`/`) mapowany do `D:\home\site\wwwroot`, których Twój kod jest wdrażany domyślnie. Jeśli bezpieczeństwo głównego aplikacji znajduje się w innym folderze lub repozytorium ma więcej niż jedną aplikację, można edytować lub dodawać wirtualne aplikacje i katalogi, w tym miejscu. Kliknij przycisk **nową wirtualną aplikację lub katalog**.

Aby skonfigurować wirtualne aplikacje i katalogi, określ każdy katalog wirtualny i jego odpowiedniego ścieżka fizyczna względem katalogu głównego witryny sieci Web (`D:\home`). Opcjonalnie można wybrać **aplikacji** pole wyboru, aby oznaczyć katalog wirtualny jako aplikacja.

### <a name="containerized-apps"></a>Konteneryzowanych aplikacji

Możesz [Dodaj niestandardowy magazyn dla aplikacji konteneryzowanych](containers/how-to-serve-content-from-azure-storage.md). Konteneryzowanych aplikacji obejmują wszystkie aplikacje systemu Linux, a także Windows i Linux niestandardowe kontenery działające w usłudze App Service. Kliknij przycisk **instalacji nowego magazynu Azure** i skonfiguruj magazyn niestandardowy w następujący sposób:

- **Nazwa**: Nazwa wyświetlana.
- **Opcje konfiguracji**: **Podstawowe** lub **zaawansowane**.
- **Konta magazynu**: Konto magazynu z kontenerem, który chcesz.
- **Typ magazynu**: **Obiekty BLOB platformy Azure** lub **usługi Azure Files**.
  > [!NOTE]
  > Aplikacje kontenerów Windows obsługuje tylko usługi Azure Files.
- **Kontener magazynu**: Podstawowa konfiguracja kontenera ma.
- **Nazwa udziału**: Aby konfigurować ustawienia zaawansowane nazwa udziału pliku.
- **Klucz dostępu**: Aby konfigurować ustawienia zaawansowane, klucz dostępu.
- **Ścieżkę instalacji**: Ścieżka bezwzględna w Twoim kontenerze, aby zainstalować magazynu niestandardowego.

Aby uzyskać więcej informacji, zobacz [obsługiwać zawartość z usługi Azure Storage w usłudze App Service w systemie Linux](containers/how-to-serve-content-from-azure-storage.md).

## <a name="configure-language-stack-settings"></a>Konfigurowanie ustawień stos języka

Dla aplikacji systemu Linux zobacz:

- [ASP.NET Core](containers/configure-language-dotnetcore.md)
- [Node.js](containers/configure-language-nodejs.md)
- [PHP](containers/configure-language-php.md)
- [Python](containers/how-to-configure-python.md)
- [Java](containers/configure-language-java.md)
- [Ruby](containers/configure-language-ruby.md)

## <a name="configure-custom-containers"></a>Konfigurowanie kontenerów niestandardowych

Zobacz [Konfigurowanie niestandardowego kontenera systemu Linux dla usługi Azure App Service](containers/configure-custom-container.md)

## <a name="next-steps"></a>Kolejne kroki

- [Konfigurowanie niestandardowej nazwy domeny w usłudze Azure App Service]
- [Konfigurowanie środowisk przejściowych w usłudze Azure App Service]
- [Włącz protokół HTTPS dla aplikacji w usłudze Azure App Service]
- [Włączanie dzienników diagnostycznych](troubleshoot-diagnostic-logs.md)
- [Skalowanie aplikacji w usłudze Azure App Service]
- [Podstawy monitorowania w usłudze Azure App Service]
- [Zmień ustawienia applicationHost.config z applicationHost.xdt](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET SignalR]: https://www.asp.net/signalr
[Azure Portal]: https://portal.azure.com/
[Konfigurowanie niestandardowej nazwy domeny w usłudze Azure App Service]: ./app-service-web-tutorial-custom-domain.md
[Konfigurowanie środowisk przejściowych w usłudze Azure App Service]: ./deploy-staging-slots.md
[Włącz protokół HTTPS dla aplikacji w usłudze Azure App Service]: ./app-service-web-tutorial-custom-ssl.md
[How to: Monitor web endpoint status]: https://go.microsoft.com/fwLink/?LinkID=279906
[Podstawy monitorowania w usłudze Azure App Service]: ./web-sites-monitor.md
[Tryb potokowy]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Skalowanie aplikacji w usłudze Azure App Service]: ./web-sites-scale.md

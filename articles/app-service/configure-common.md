---
title: Konfigurowanie aplikacji w portalu
description: Dowiedz się, jak skonfigurować typowe ustawienia aplikacji usługi App Service w witrynie Azure portal. Ustawienia aplikacji, parametry połączenia, platforma, stos językowy, kontener itp.
keywords: usługa aplikacji azure, aplikacja internetowa, ustawienia aplikacji, zmienne środowiskowe
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.topic: article
ms.date: 08/13/2019
ms.custom: seodec18
ms.openlocfilehash: 18469c94b66acab27b58243e8d15eb924843319b
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811122"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>Konfigurowanie aplikacji usługi App Service w witrynie Azure portal

W tym temacie wyjaśniono, jak skonfigurować typowe ustawienia dla aplikacji sieci Web, zaplecza mobilnego lub aplikacji interfejsu API przy użyciu [witryny Azure portal].

## <a name="configure-app-settings"></a>Konfigurowanie ustawień aplikacji

W usłudze App Service ustawienia aplikacji są zmiennymi przekazywanymi jako zmienne środowiskowe do kodu aplikacji. W przypadku aplikacji systemu Linux i kontenerów niestandardowych `--env` usługa App Service przekazuje ustawienia aplikacji do kontenera przy użyciu flagi, aby ustawić zmienną środowiskową w kontenerze.

W [witrynie Azure portal]wyszukaj i wybierz pozycję **Usługi aplikacji**, a następnie wybierz aplikację. 

![Wyszukiwanie usług aplikacji](./media/configure-common/search-for-app-services.png)

W menu po lewej stronie aplikacji wybierz pozycję**Ustawienia aplikacji** **konfiguracyjnej** > .

![Ustawienia aplikacji](./media/configure-common/open-ui.png)

W przypadku ASP.NET i ASP.NET core deweloperzy, ustawienie ustawień aplikacji w `<appSettings>` usłudze App Service jest jak ustawienie ich w *witrynie Web.config* lub *appsettings.json*, ale wartości w usłudze App Service zastępują te w *witrynie Web.config* lub *appsettings.json*. Ustawienia programistyczne (na przykład lokalne hasło MySQL) można zachować w *witrynie Web.config* lub *appsettings.json*, ale wpisy tajne produkcji (na przykład hasło bazy danych Azure MySQL) są bezpieczne w usłudze app service. Ten sam kod używa ustawień deweloperskich podczas debugowania lokalnie i używa wpisów tajnych produkcji po wdrożeniu na platformie Azure.

Inne stosy języka, podobnie, uzyskać ustawienia aplikacji jako zmienne środowiskowe w czasie wykonywania. Aby zapoznać się z określonymi krokami dotyczącymi stosu językowego, zobacz:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Kontenery niestandardowe](containers/configure-custom-container.md#configure-environment-variables)

Ustawienia aplikacji są zawsze szyfrowane podczas przechowywania (zaszyfrowane w spoczynku).

> [!NOTE]
> Ustawienia aplikacji można również rozwiązać z [usługi Key Vault](/azure/key-vault/) przy użyciu [odwołań do usługi Key Vault](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Pokaż ukryte wartości

Domyślnie wartości ustawień aplikacji są ukryte w portalu dla zabezpieczeń. Aby wyświetlić ukrytą wartość ustawienia aplikacji, kliknij pole **Wartość** tego ustawienia. Aby wyświetlić wartości wszystkich ustawień aplikacji, kliknij przycisk **Pokaż wartość.**

### <a name="add-or-edit"></a>Dodawanie lub edytowanie

Aby dodać nowe ustawienie aplikacji, kliknij pozycję **Nowe ustawienie aplikacji**. W oknie dialogowym można [przykleić to ustawienie do bieżącego gniazda](deploy-staging-slots.md#which-settings-are-swapped).

Aby edytować ustawienie, kliknij przycisk **Edytuj** po prawej stronie.

Po zakończeniu kliknij przycisk **Aktualizuj**. Nie zapomnij kliknąć **przycisku Zapisz** z powrotem na stronie **Konfiguracja.**

> [!NOTE]
> W domyślnym kontenerze systemu Linux lub niestandardowym kontenerze systemu Linux `ApplicationInsights:InstrumentationKey` każda zagnieżdżona `ApplicationInsights__InstrumentationKey` struktura kluczy JSON w nazwie ustawienia aplikacji, taka jak musi być skonfigurowana w usłudze App Service, jak w przypadku nazwy klucza. Innymi słowy, `:` każdy powinien `__` zostać zastąpiony przez (podwójne podkreślenie).
>

### <a name="edit-in-bulk"></a>Edytuj zbiorczo

Aby zbiorczo dodawać lub edytować ustawienia aplikacji, kliknij przycisk **Edycja zaawansowana.** Po zakończeniu kliknij przycisk **Aktualizuj**. Nie zapomnij kliknąć **przycisku Zapisz** z powrotem na stronie **Konfiguracja.**

Ustawienia aplikacji mają następujące formatowanie JSON:

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

W [witrynie Azure portal]wyszukaj i wybierz pozycję **Usługi aplikacji**, a następnie wybierz aplikację. W menu po lewej stronie aplikacji wybierz pozycję**Ustawienia aplikacji** **konfiguracyjnej** > .

![Ustawienia aplikacji](./media/configure-common/open-ui.png)

W przypadku ASP.NET i ASP.NET Core deweloperzy, ustawianie ciągów połączeń w `<connectionStrings>` usłudze App Service jest jak ustawienie ich w *witrynie Web.config*, ale wartości ustawione w usłudze App Service zastępują te w *witrynie Web.config*. Ustawienia programistyczne (na przykład plik bazy danych) w *witrynie Web.config* i wpisy tajne produkcji (na przykład poświadczenia bazy danych SQL) można bezpiecznie przechowywać w usłudze App Service. Ten sam kod używa ustawień deweloperskich podczas debugowania lokalnie i używa wpisów tajnych produkcji po wdrożeniu na platformie Azure.

W przypadku stosów innych języków lepiej jest używać [ustawień aplikacji,](#configure-app-settings) ponieważ parametry połączenia wymagają specjalnego formatowania w kluczach zmiennych, aby uzyskać dostęp do wartości. Oto jeden wyjątek, jednak: niektóre typy bazy danych platformy Azure są archiwizowane wraz z aplikacją, jeśli skonfigurujesz ich parametry połączenia w aplikacji. Aby uzyskać więcej informacji, zobacz [Co zostanie utworzone kopia zapasowa](manage-backup.md#what-gets-backed-up). Jeśli nie potrzebujesz tej automatycznej kopii zapasowej, użyj ustawień aplikacji.

W czasie wykonywania parametry połączenia są dostępne jako zmienne środowiskowe, poprzedzone następującymi typami połączeń:

* Sqlserver:`SQLCONNSTR_`  
* Mysql:`MYSQLCONNSTR_` 
* SQLAzure:`SQLAZURECONNSTR_` 
* Niestandardowe:`CUSTOMCONNSTR_`
* Postgresql:`POSTGRESQLCONNSTR_`  

Na przykład ciąg połączenia MySql o nazwie *connectionstring1* jest `MYSQLCONNSTR_connectionString1`dostępny jako zmienna środowiskowa . Aby zapoznać się z określonymi krokami dotyczącymi stosu językowego, zobacz:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Kontenery niestandardowe](containers/configure-custom-container.md#configure-environment-variables)

Parametry połączenia są zawsze szyfrowane podczas przechowywania (zaszyfrowane w spoczynku).

> [!NOTE]
> Parametry połączenia można również rozpoznać z [usługi Key Vault](/azure/key-vault/) przy użyciu [odwołań do magazynu kluczy](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Pokaż ukryte wartości

Domyślnie wartości dla ciągów połączeń są ukryte w portalu dla zabezpieczeń. Aby wyświetlić ukrytą wartość ciągu połączenia, wystarczy kliknąć pole **Wartość** tego ciągu. Aby wyświetlić wartości wszystkich ciągów połączeń, kliknij przycisk **Pokaż wartość.**

### <a name="add-or-edit"></a>Dodawanie lub edytowanie

Aby dodać nowy ciąg połączenia, kliknij pozycję **Nowy ciąg połączenia**. W oknie dialogowym można [przykleić ciąg połączenia do bieżącego gniazda](deploy-staging-slots.md#which-settings-are-swapped).

Aby edytować ustawienie, kliknij przycisk **Edytuj** po prawej stronie.

Po zakończeniu kliknij przycisk **Aktualizuj**. Nie zapomnij kliknąć **przycisku Zapisz** z powrotem na stronie **Konfiguracja.**

### <a name="edit-in-bulk"></a>Edytuj zbiorczo

Aby zbiorczo dodawać lub edytować parametry połączenia, kliknij przycisk **Edycja zaawansowana.** Po zakończeniu kliknij przycisk **Aktualizuj**. Nie zapomnij kliknąć **przycisku Zapisz** z powrotem na stronie **Konfiguracja.**

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

## <a name="configure-general-settings"></a>Konfigurowanie ustawień ogólnych

W [witrynie Azure portal]wyszukaj i wybierz pozycję **Usługi aplikacji**, a następnie wybierz aplikację. W menu po lewej stronie aplikacji wybierz pozycję**Ustawienia ogólne** **konfiguracji** > .

![Ustawienia ogólne](./media/configure-common/open-general.png)

W tym miejscu można skonfigurować niektóre typowe ustawienia dla aplikacji. Niektóre ustawienia wymagają [skalowania do wyższych warstw cenowych](manage-scale-up.md).

- **Ustawienia stosu:** stos oprogramowania do uruchamiania aplikacji, w tym wersji języka i SDK. W przypadku aplikacji systemu Linux i niestandardowych aplikacji kontenerów można również ustawić opcjonalne polecenie lub plik startowy.
- **Ustawienia platformy**: Umożliwia skonfigurowanie ustawień platformy hostingowej, w tym:
    - **Bitness:** 32-bitowy lub 64-bitowy.
    - **Protokół WebSocket**: Na przykład dla [ASP.NET SignalR] lub [socket.io.](https://socket.io/)
    - **Zawsze włączony:** Trzymaj aplikację załadowaną nawet wtedy, gdy nie ma ruchu. Jest to wymagane dla ciągłych WebJobs lub WebJobs, które są wyzwalane przy użyciu wyrażenia CRON.
      > [!NOTE]
      > Dzięki funkcji Zawsze włączone nie można kontrolować punktu końcowego. Zawsze wysyła żądanie do katalogu głównego aplikacji.
    - **Wersja potoku zarządzanego:** [tryb potoku]usług IIS . Ustaw go na **klasyczny,** jeśli masz starszą aplikację, która wymaga starszej wersji iIS.
    - **Wersja HTTP**: Ustaw na **2.0,** aby włączyć obsługę protokołu [HTTPS/2.](https://wikipedia.org/wiki/HTTP/2)
    > [!NOTE]
    > Większość nowoczesnych przeglądarek obsługuje protokół HTTP/2 tylko za pośrednictwem protokołu TLS, podczas gdy ruch niezaszyfrowany nadal używa protokołu HTTP/1.1. Aby upewnić się, że przeglądarki klienckie łączą się z aplikacją za pomocą protokołu HTTP/2, zabezpiecz niestandardową nazwę DNS. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie niestandardowej nazwy DNS za pomocą powiązania TLS/SSL w usłudze Azure App Service](configure-ssl-bindings.md).
    - **Koligacja ARR:** We wdrożeniu wielu wystąpień upewnij się, że klient jest kierowany do tego samego wystąpienia przez cały okres sesji. Tę opcję można ustawić na **Wyłączone** dla aplikacji bezstanowych.
- **Debugowanie:** Włącz zdalne debugowanie dla [aplikacji ASP.NET](troubleshoot-dotnet-visual-studio.md#remotedebug), [ASP.NET Core](/visualstudio/debugger/remote-debugging-azure)lub [Node.js.](containers/configure-language-nodejs.md#debug-remotely) Ta opcja wyłącza się automatycznie po 48 godzinach.
- **Przychodzące certyfikaty klienta:** wymagają certyfikatów klienta w [uwierzytelnianiu wzajemnym](app-service-web-configure-tls-mutual-auth.md).

## <a name="configure-default-documents"></a>Konfigurowanie dokumentów domyślnych

To ustawienie dotyczy tylko aplikacji systemu Windows.

W [witrynie Azure portal]wyszukaj i wybierz pozycję **Usługi aplikacji**, a następnie wybierz aplikację. W menu po lewej stronie aplikacji wybierz polecenie**Domyślne dokumenty** **konfiguracji** > .

![Dokumenty domyślne](./media/configure-common/open-documents.png)

Domyślnym dokumentem jest strona internetowa wyświetlana pod głównym adresem URL witryny sieci Web. Używany jest pierwszy pasujący plik na liście. Aby dodać nowy dokument domyślny, kliknij pozycję **Nowy dokument**. Nie zapomnij kliknąć przycisk **Zapisz**.

Jeśli aplikacja używa modułów tej trasy na podstawie adresu URL zamiast wyświetlania zawartości statycznej, nie ma potrzeby stosowania dokumentów domyślnych.

## <a name="configure-path-mappings"></a>Konfigurowanie mapowań ścieżek

W [witrynie Azure portal]wyszukaj i wybierz pozycję **Usługi aplikacji**, a następnie wybierz aplikację. W menu po lewej stronie aplikacji wybierz polecenie**Mapowania ścieżki** **konfiguracji** > .

![Mapowania ścieżek](./media/configure-common/open-path.png)

Na stronie **Mapowania ścieżek** przedstawiono różne rzeczy na podstawie typu systemu operacyjnego.

### <a name="windows-apps-uncontainerized"></a>Aplikacje systemu Windows (niezawierane)

W przypadku aplikacji systemu Windows można dostosować mapowania obsługi usług IIS oraz aplikacje i katalogi wirtualne.

Mapowania obsługi umożliwiają dodawanie niestandardowych procesorów skryptów do obsługi żądań dotyczących określonych rozszerzeń plików. Aby dodać program obsługi niestandardowej, kliknij pozycję **Nowy program obsługi**. Skonfiguruj program obsługi w następujący sposób:

- **Rozszerzenie**. Rozszerzenie pliku, które chcesz obsłużyć, takie jak * \*.php* lub *handler.fcgi*.
- **Procesor skryptów**. Bezwzględna ścieżka procesora skryptów do Ciebie. Żądania do plików, które pasują do rozszerzenia pliku są przetwarzane przez procesor skryptów. Użyj ścieżki, `D:\home\site\wwwroot` aby odwołać się do katalogu głównego aplikacji.
- **Argumenty**. Opcjonalne argumenty wiersza polecenia dla procesora skryptów.

Każda aplikacja ma domyślną`/`ścieżkę `D:\home\site\wwwroot`główną ( ) mapowane do , gdzie kod jest wdrażany domyślnie. Jeśli katalog główny aplikacji znajduje się w innym folderze lub jeśli repozytorium ma więcej niż jedną aplikację, możesz edytować lub dodać wirtualne aplikacje i katalogi tutaj. Kliknij **pozycję Nowa aplikacja wirtualna lub katalog**.

Aby skonfigurować aplikacje wirtualne i katalogi, określ każdy katalog wirtualny i odpowiadającą mu ścieżkę fizyczną względem katalogu głównego witryny (`D:\home`). Opcjonalnie można zaznaczyć pole wyboru **Aplikacja,** aby oznaczyć katalog wirtualny jako aplikację.

### <a name="containerized-apps"></a>Aplikacje konteneryzowane

Możesz [dodać niestandardowy magazyn dla aplikacji konteneryzowanej](containers/how-to-serve-content-from-azure-storage.md). Konteneryzowane aplikacje obejmują wszystkie aplikacje systemu Linux, a także niestandardowe kontenery systemu Windows i Linux działające w usłudze App Service. Kliknij **pozycję Nowy zestaw do instalacji usługi Azure Storage** i skonfiguruj niestandardowy magazyn w następujący sposób:

- **Nazwa**: Nazwa wyświetlana.
- **Opcje konfiguracji**: **Podstawowe** lub **Zaawansowane**.
- **Konta magazynu:** konto magazynu z kontenerem, który chcesz.
- **Typ magazynu:** **obiekty blob azure** lub **pliki azure**.
  > [!NOTE]
  > Aplikacje kontenerów systemu Windows obsługują tylko usługi Azure Files.
- **Kontener magazynu:** Dla konfiguracji podstawowej, kontenera, który chcesz.
- **Nazwa udziału**: W przypadku konfiguracji zaawansowanej nazwa udziału plików.
- **Klucz dostępu:** Dla zaawansowanej konfiguracji, klucz dostępu.
- **Ścieżka instalacji:** ścieżka bezwzględna w kontenerze do zainstalowania magazynu niestandardowego.

Aby uzyskać więcej informacji, zobacz [Obsługa zawartości z usługi Azure Storage w usłudze App Service w systemie Linux](containers/how-to-serve-content-from-azure-storage.md).

## <a name="configure-language-stack-settings"></a>Konfigurowanie ustawień stosu językowego

Zobacz też: W przypadku aplikacji dla systemu Linux:

- [ASP.NET Core](containers/configure-language-dotnetcore.md)
- [Node.js](containers/configure-language-nodejs.md)
- [PHP](containers/configure-language-php.md)
- [Python](containers/how-to-configure-python.md)
- [Java](containers/configure-language-java.md)
- [Ruby](containers/configure-language-ruby.md)

## <a name="configure-custom-containers"></a>Konfigurowanie kontenerów niestandardowych

Zobacz [Konfigurowanie niestandardowego kontenera systemu Linux dla usługi Azure App Service](containers/configure-custom-container.md)

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie niestandardowej nazwy domeny w usłudze Azure App Service]
- [Konfigurowanie środowisk przejściowych w usłudze Azure App Service]
- [Zabezpiecz niestandardową nazwę DNS za pomocą powiązania TLS/SSL w usłudze Azure App Service](configure-ssl-bindings.md)
- [Włączanie dzienników diagnostycznych](troubleshoot-diagnostic-logs.md)
- [Skalowanie aplikacji w usłudze Azure App Service]
- [Podstawy monitorowania w usłudze Azure App Service]
- [Zmienianie ustawień aplikacjiHost.config za pomocą aplikacjiHost.xdt](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET SignalR]: https://www.asp.net/signalr
[Azure Portal]: https://portal.azure.com/
[Konfigurowanie niestandardowej nazwy domeny w usłudze Azure App Service]: ./app-service-web-tutorial-custom-domain.md
[Konfigurowanie środowisk przejściowych w usłudze Azure App Service]: ./deploy-staging-slots.md
[How to: Monitor web endpoint status]: https://go.microsoft.com/fwLink/?LinkID=279906
[Podstawy monitorowania w usłudze Azure App Service]: ./web-sites-monitor.md
[tryb potoku]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Skalowanie aplikacji w usłudze Azure App Service]: ./manage-scale-up.md

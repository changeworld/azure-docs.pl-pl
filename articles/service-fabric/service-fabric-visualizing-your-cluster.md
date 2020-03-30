---
title: Wizualizacja klastra przy użyciu Eksploratora usług Azure
description: Eksplorator sieci szkieletowej usług to aplikacja do sprawdzania i zarządzania aplikacjami i węzłami w chmurze w klastrze sieci szkieletowej usług Microsoft Azure.
author: mikkelhegn
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: mikhegn
ms.openlocfilehash: 80e3d990b6e8026c57ffff0048d0447a95529564
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258189"
---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>Wizualizowanie klastra przy użyciu narzędzia Service Fabric Explorer

Eksplorator sieci szkieletowej usług (SFX) to narzędzie typu open source do inspekcji klastrów sieci szkieletowej usług Azure i zarządzania nimi. Service Fabric Explorer to aplikacja klasyczna dla systemów Windows, macOS i Linux.

## <a name="service-fabric-explorer-download"></a>Pobieranie eksploratora sieci szkieletowej usług

Użyj następujących łączy, aby pobrać Eksploratora sieci szkieletowej usług jako aplikację klasyczną:

- Windows
  - https://aka.ms/sfx-windows

- Linux
  - https://aka.ms/sfx-linux-x86
  - https://aka.ms/sfx-linux-x64

- macOS
  - https://aka.ms/sfx-macos

> [!NOTE]
> Klasyczna wersja Eksploratora sieci szkieletowej usług może mieć więcej lub mniej funkcji niż obsługa klastra. Można wrócić do wersji Eksploratora sieci szkieletowej usług wdrożonej w klastrze, aby zapewnić pełną zgodność funkcji.
>
>

### <a name="running-service-fabric-explorer-from-the-cluster"></a>Uruchamianie Eksploratora sieci szkieletowej usług z klastra

Eksplorator sieci szkieletowej usług jest również hostowany w punkcie końcowym zarządzania HTTP klastra sieci szkieletowej usług. Aby uruchomić SFX w przeglądarce sieci web, przejdź do punktu końcowego zarządzania\/HTTP klastra z dowolnej przeglądarki — na przykład https: /clusterFQDN:19080.

W przypadku konfiguracji stacji roboczych dla deweloperów można uruchomić Eksploratora sieci szkieletowej usług w klastrze lokalnym, przechodząc do pliku https://localhost:19080/Explorer. Zapoznaj się z tym [artykułem,](service-fabric-get-started.md)aby przygotować środowisko programistyczne .

> [!NOTE]
> Jeśli klaster jest zabezpieczony certyfikatem z podpisem własnym, zostanie wyświetlony komunikat o błędzie z przeglądarki internetowej "Ta witryna nie jest bezpieczna". Możesz po prostu przejść przez większość nowoczesnych przeglądarek internetowych, zastępując ostrzeżenie. W środowisku produkcyjnym klaster powinien być zabezpieczony przy użyciu nazwy pospolitej i certyfikatu wystawionego przez urząd certyfikacji. 
>
>

## <a name="connect-to-a-service-fabric-cluster"></a>Nawiązywanie połączenia z klastrem usługi Service Fabric
Aby połączyć się z klastrem sieci szkieletowej usług, potrzebujesz punktu końcowego zarządzania klastrami (FQDN/IP) i portu końcowego punktu końcowego zarządzania HTTP (domyślnie 19080). Na przykład\:https //mysfcluster.westus.cloudapp.azure.com:19080. Użyj pola wyboru "Połącz z localhost", aby połączyć się z lokalnym klastrem na stacji roboczej.

### <a name="connect-to-a-secure-cluster"></a>Nawiązywanie połączenia z zabezpieczonym klastrem
Można kontrolować dostęp klienta do klastra sieci szkieletowej usług za pomocą certyfikatów lub przy użyciu usługi Azure Active Directory (AAD).

Jeśli spróbujesz połączyć się z bezpiecznym klastrem, w zależności od konfiguracji klastra będziesz musiał przedstawić certyfikat klienta lub zalogować się przy użyciu usługi AAD.

## <a name="understand-the-service-fabric-explorer-layout"></a>Opis układu Eksploratora sieci szkieletowej usług
Explorer sieci szkieletowej usług można poruszać się za pomocą drzewa po lewej stronie. W katalogu głównym drzewa pulpit nawigacyjny klastra zawiera omówienie klastra, w tym podsumowanie kondycji aplikacji i węzła.

![Pulpit nawigacyjny klastra Eksploratora sieci szkieletowej usług][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>Wyświetlanie układu klastra
Węzły w klastrze sieci szkieletowej usług są umieszczane w dwuwymiarowej siatce domen błędów i domen uaktualniania. To miejsce docelowe gwarantuje, że aplikacje pozostają dostępne w obecności awarii sprzętu i uaktualnień aplikacji. Można wyświetlić sposób rozmieszczenia bieżącego klastra przy użyciu mapy klastra.

![Mapa klastra Eksploratora sieci szkieletowej usług][sfx-cluster-map]

### <a name="view-applications-and-services"></a>Wyświetlanie aplikacji i usług
Klaster zawiera dwa poddrzewy: jeden dla aplikacji i drugi dla węzłów.

Za pomocą widoku aplikacji można poruszać się po hierarchii logicznej sieci szkieletowej usług: aplikacje, usługi, partycje i repliki.

W poniższym przykładzie aplikacja **MyApp** składa się z dwóch usług, **MyStatefulService** i **WebService**. Ponieważ **MyStatefulService** jest stanowy, zawiera partycję z jedną repliką podstawową i dwiema replikami pomocniczymi. Z drugiej strony WebSvcService jest bezstanowy i zawiera jedno wystąpienie.

![Widok aplikacji Eksploratora sieci szkieletowej usług][sfx-application-tree]

Na każdym poziomie drzewa główne okienko pokazuje istotne informacje o elemencie. Na przykład można zobaczyć stan kondycji i wersji dla określonej usługi.

![Podstawowe okienko Eksploratora sieci szkieletowej usług][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>Wyświetlanie węzłów klastra
Widok węzła przedstawia fizyczny układ klastra. Dla danego węzła można sprawdzić, które aplikacje mają kod wdrożony w tym węźle. W szczególności można zobaczyć, które repliki są obecnie uruchomione.

## <a name="actions"></a>Akcje
Eksplorator sieci szkieletowej usług oferuje szybki sposób wywoływania akcji w węzłach, aplikacjach i usługach w klastrze.

Na przykład, aby usunąć wystąpienie aplikacji, wybierz aplikację z drzewa po lewej stronie, a następnie wybierz polecenie **Akcje** > **Usuń aplikację**.

![Usuwanie aplikacji w Eksploratorze sieci szkieletowej usług][sfx-delete-application]

> [!TIP]
> Te same akcje można wykonać, klikając wielokropek obok każdego elementu.
>
> Każda akcja, która może być wykonana za pośrednictwem Eksploratora sieci szkieletowej usług można również wykonać za pośrednictwem programu PowerShell lub interfejsu API REST, aby włączyć automatyzację.
>
>

Explorer sieci szkieletowej usług służy również do tworzenia wystąpień aplikacji dla danego typu aplikacji i wersji. Wybierz typ aplikacji w widoku drzewa, a następnie kliknij łącze **Utwórz wystąpienie aplikacji** obok wersji, która ma być dostępna w prawym okienku.

![Tworzenie wystąpienia aplikacji w Eksploratorze sieci szkieletowej usług][sfx-create-app-instance]

> [!NOTE]
> Eksplorator sieci szkieletowej usług nie obsługuje parametrów podczas tworzenia wystąpień aplikacji. Wystąpienia aplikacji używają domyślnych wartości parametrów.
>
>

## <a name="event-store"></a>Sklep z wydarzeniami
EventStore to funkcja oferowana przez platformę, która udostępnia zdarzenia platformy sieci szkieletowej usług dostępne w Eksploratorze sieci szkieletowej usług i za pośrednictwem interfejsu API REST. Możesz zobaczyć widok migawki tego, co dzieje się w klastrze dla każdej jednostki, na przykład węzeł, usługa, aplikacja i kwerenda na podstawie czasu zdarzenia. Możesz również przeczytać więcej o EventStore w [Przeglądzie EventStore](service-fabric-diagnostics-eventstore.md).   

![Sklep eventowy][sfx-eventstore]

>[!NOTE]
>Od usługi Service Fabric w wersji 6.4. EventStore nie jest domyślnie włączony i musi być włączony w szablonie Menedżera zasobów

>[!NOTE]
>Od usługi Service Fabric w wersji 6.4. interfejsy API eventstore są dostępne tylko dla klastrów systemu Windows uruchomionych tylko na platformie Azure. Pracujemy nad przeniesieniem tej funkcji do systemu Linux, a także naszych autonomicznych klastrów.

## <a name="image-store-viewer"></a>Przeglądarka magazynu obrazów
Przeglądarka magazynu obrazów jest funkcją oferowaną w przypadku korzystania z magazynu obrazów natywnych, która umożliwia przeglądanie bieżącej zawartości magazynu obrazów i uzyskanie informacji o plikach i folderach, a także usuwanie plików/folderów.

![Mapa klastra Eksploratora sieci szkieletowej usług][sfx-imagestore]

## <a name="backup-and-restore"></a>Wykonywanie kopii zapasowych i przywracanie
Eksplorator sieci szkieletowej usług oferuje możliwość interfejsu z [kopii zapasowej i przywracania](./service-fabric-reliable-services-backup-restore.md). Aby wyświetlić funkcje tworzenia kopii zapasowych i przywracania w SFX, tryb zaawansowany musi być włączony.

![Włącz tryb zaawansowany][0]
 
Możliwe są następujące operacje:

* Tworzenie, edytowanie i usuwanie zasad tworzenia kopii zapasowych.
* Włączanie i wyłączanie kopii zapasowej dla aplikacji, usługi lub partycji.
* Wstrzymanie i wznowienie wykonywania kopii zapasowej dla aplikacji, usługi lub partycji.
* Wyzwalanie i śledzenie kopii zapasowej partycji.
* Wyzwalanie i śledzenie przywracania dla partycji.

Aby uzyskać więcej informacji na temat usługi Kopia zapasowa i przywracanie, zobacz [odwołanie do interfejsu API REST](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore).
## <a name="next-steps"></a>Następne kroki
* [Zarządzanie aplikacjami sieci szkieletowej usług w programie Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Wdrażanie aplikacji sieci szkieletowej usług przy użyciu programu PowerShell](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/sfx-cluster-dashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/sfx-cluster-map.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/sfx-application-tree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/sfx-service-essentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/sfx-delete-application.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/sfx-create-app-instance.png
[sfx-eventstore]: ./media/service-fabric-diagnostics-eventstore/eventstore.png
[sfx-imagestore]: ./media/service-fabric-visualizing-your-cluster/sfx-image-store.png
[0]: ./media/service-fabric-backuprestoreservice/advanced-mode.png
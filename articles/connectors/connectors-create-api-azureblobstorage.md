---
title: Łączenie się z usługą Azure Blob Storage
description: Tworzenie obiektów blob na kontach magazynu platformy Azure i zarządzanie nimi przy użyciu aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: eb943bfe36be10d1e95d569a5c1bf48563e909c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247360"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-by-using-azure-logic-apps"></a>Tworzenie obiektów blob i zarządzanie nimi w usłudze Azure Blob Storage przy użyciu aplikacji Azure Logic Apps

W tym artykule pokazano, jak można uzyskać dostęp do plików przechowywanych jako obiekty blob na koncie usługi Azure storage i zarządzać nimi z wewnątrz aplikacji logiki za pomocą łącznika usługi Azure Blob Storage. W ten sposób można tworzyć aplikacje logiki, które automatyzują zadania i przepływy pracy do zarządzania plikami. Na przykład możesz tworzyć aplikacje logiki, które tworzą, pobierają, aktualizują i usuwają pliki na koncie magazynu.

Załóżmy, że masz narzędzie, które jest aktualizowane w witrynie sieci Web platformy Azure. który działa jako wyzwalacz dla aplikacji logiki. W przypadku wystąpienia tego zdarzenia, można mieć aplikacji logiki zaktualizować plik w kontenerze magazynu obiektów blob, który jest działanie w aplikacji logiki.

Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest Usługa Azure Logic Apps](../logic-apps/logic-apps-overview.md) i szybki [start: Utwórz pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby uzyskać informacje techniczne dotyczące łącznika, zobacz [odwołanie do łącznika usługi Azure Blob Storage](https://docs.microsoft.com/connectors/azureblobconnector/).

> [!IMPORTANT]
> Aplikacje logiki nie mogą bezpośrednio uzyskiwać dostępu do kont magazynu, które znajdują się za zaporami, jeśli znajdują się w tym samym regionie. Aby obejść ten problem, możesz mieć swoje aplikacje logiki i konto magazynu w różnych regionach. Aby uzyskać więcej informacji na temat włączania dostępu z usługi Azure Logic Apps do kont magazynu za zaporami, zobacz sekcję [Konta magazynu programu Access za zaporami](#storage-firewalls) w dalszej części tego tematu.

<a name="blob-storage-limits"></a>

## <a name="limits"></a>Limity

* Domyślnie akcje usługi Azure Blob Storage mogą odczytywać lub zapisywać pliki o rozmiarze *50 MB lub mniejszym*. Aby obsłużyć pliki o rozmiarze większym niż 50 MB, ale do 1024 MB, akcje usługi Azure Blob Storage obsługują [tworzenie fragmentów wiadomości.](../logic-apps/logic-apps-handle-large-messages.md) Akcja **Pobierz zawartość obiektu blob** niejawnie używa fragmentowania.

* Wyzwalacze usługi Azure Blob Storage nie obsługują fragmentowania. Podczas żądania zawartości pliku wyzwalacze wybierają tylko pliki o rozmiarze 50 MB lub mniejszym. Aby uzyskać pliki większe niż 50 MB, wykonaj następujące schematy:

  * Użyj wyzwalacza usługi Azure Blob Storage, który zwraca właściwości pliku, takie jak **Gdy obiekt blob jest dodawany lub modyfikowany (tylko właściwości).**

  * Postępuj zgodnie z wyzwalaczem z akcji azure blob storage **get blob content,** która odczytuje kompletny plik i niejawnie używa fragmentowania.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* [Konto magazynu platformy Azure i kontener magazynu](../storage/blobs/storage-quickstart-blobs-portal.md)

* Aplikacja logiki, w której potrzebujesz dostępu do konta magazynu obiektów blob platformy Azure. Aby uruchomić aplikację logiki za pomocą wyzwalacza usługi Azure Blob Storage, potrzebujesz [pustej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Dodawanie wyzwalacza magazynu obiektów blob

W usłudze Azure Logic Apps każda aplikacja logiki musi zaczynać się od [wyzwalacza,](../logic-apps/logic-apps-overview.md#logic-app-concepts)który uruchamia się, gdy występuje określone zdarzenie lub gdy zostanie spełniony określony warunek. Za każdym razem, gdy wyzwalacz jest uruchamiany, aparat aplikacji logiki tworzy wystąpienie aplikacji logiki i uruchamia przepływ pracy aplikacji.

W tym przykładzie pokazano, jak można uruchomić przepływ pracy aplikacji logiki z **When obiektu blob jest dodawany lub modyfikowany (tylko właściwości)** wyzwalacz, gdy właściwości obiektu blob zostanie dodany lub zaktualizowany w kontenerze magazynu.

1. W [witrynie Azure portal](https://portal.azure.com) lub visual studio utwórz pustą aplikację logiki, która otwiera projektanta aplikacji logiki. W tym przykładzie użyto witryny Azure portal.

2. W polu wyszukiwania wprowadź "azure blob" jako filtr. Z listy wyzwalaczy wybierz odpowiedni wyzwalacz.

   W tym przykładzie użyto tego wyzwalacza: **gdy obiekt blob jest dodawany lub modyfikowany (tylko właściwości)**

   ![Wybieranie wyzwalacza usługi Azure Blob Storage](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-trigger.png)

3. Jeśli zostanie wyświetlony monit o podanie szczegółów połączenia, [utwórz połączenie magazynu obiektów blob teraz](#create-connection). Lub, jeśli połączenie już istnieje, podaj niezbędne informacje dla wyzwalacza.

   W tym przykładzie wybierz kontener i folder, który chcesz monitorować.

   1. W polu **Kontener** wybierz ikonę folderu.

   2. Na liście folderów wybierz nawias pod **>** kątem prostym ( ), a następnie przejrzyj, aż znajdziesz i wybierz odpowiedni folder.

      ![Wybieranie folderu magazynu do użycia z wyzwalaczem](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Wybierz interwał i częstotliwość, jak często wyzwalacz ma sprawdzać folder pod kątem zmian.

4. Po zakończeniu na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

5. Teraz kontynuuj dodawanie jednej lub więcej akcji do aplikacji logiki dla zadań, które chcesz wykonać z wynikami wyzwalacza.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Dodawanie akcji magazynu obiektów blob

W usłudze Azure Logic Apps [akcja](../logic-apps/logic-apps-overview.md#logic-app-concepts) jest krokiem w przepływie pracy, który następuje wyzwalacz lub innej akcji. W tym przykładzie aplikacja logiki rozpoczyna się od [wyzwalacza cyklu](../connectors/connectors-native-recurrence.md).

1. W [witrynie Azure portal](https://portal.azure.com) lub visual studio otwórz aplikację logiki w logice App Designer. W tym przykładzie użyto witryny Azure portal.

2. W projektancie aplikacji logiki w obszarze wyzwalacz lub akcja wybierz pozycję **Nowy krok**.

   ![Dodawanie nowego kroku do przepływu pracy aplikacji logiki](./media/connectors-create-api-azureblobstorage/add-new-step-logic-app-workflow.png) 

   Aby dodać akcję między istniejącymi krokami, przesuń wskaźnik myszy na strzałkę łączącą. Wybierz wyświetlony znak**+** plus ( ) i wybierz pozycję **Dodaj akcję**.

3. W polu wyszukiwania wprowadź "azure blob" jako filtr. Z listy akcji wybierz odpowiednią akcję.

   W tym przykładzie użyto tej akcji: **Pobierz zawartość obiektu blob**

   ![Wybierz akcję usługi Azure Blob Storage](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-action.png)

4. Jeśli zostanie wyświetlony monit o podanie szczegółów połączenia, [utwórz połączenie usługi Azure Blob Storage teraz](#create-connection).
Lub, jeśli połączenie już istnieje, podaj informacje niezbędne do działania.

   W tym przykładzie wybierz odpowiedni plik.

   1. W polu **Obiekt Blob** wybierz ikonę folderu.
  
      ![Wybieranie folderu magazynu do użycia z akcją](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Znajdź i wybierz odpowiedni plik na podstawie numeru **identyfikatora obiektu** blob. Ten numer **identyfikatora** można znaleźć w metadanych obiektu blob, który jest zwracany przez poprzednio opisane wyzwalacz magazynu obiektów blob.

5. Po zakończeniu na pasku narzędzi projektanta wybierz pozycję **Zapisz**.
Aby przetestować aplikację logiki, upewnij się, że wybrany folder zawiera obiekt blob.

W tym przykładzie pobiera tylko zawartość obiektu blob. Aby wyświetlić zawartość, należy dodać inną akcję, która tworzy plik z obiektu blob przy użyciu innego łącznika. Na przykład dodaj akcję usługi OneDrive, która tworzy plik na podstawie zawartości obiektu blob.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Łączenie się z kontem magazynu

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Po wyświetleniu monitu o utworzenie połączenia podaj następujące informacje:

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Nazwa połączenia** | Tak | <*nazwa połączenia*> | Nazwa do utworzenia dla połączenia |
   | **Konto magazynu** | Tak | <*konto magazynu*> | Wybierz konto magazynu z listy. |
   ||||

   Przykład:

   ![Tworzenie połączenia konta magazynu obiektów Blob platformy Azure](./media/connectors-create-api-azureblobstorage/create-storage-account-connection.png) 

1. Gdy będziesz gotowy, wybierz pozycję **Utwórz**

1. Po utworzeniu połączenia przejdź do [artykułu Dodaj wyzwalacz magazynu obiektów blob](#add-trigger) lub [Dodaj akcję magazynu obiektów blob](#add-action).

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać więcej informacji technicznych na temat tego łącznika, takich jak wyzwalacze, akcje i limity opisane w pliku Swagger łącznika, zobacz [stronę odwołania łącznika](https://docs.microsoft.com/connectors/azureblobconnector/).

> [!NOTE]
> W przypadku aplikacji logiki w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)wersja z etykietą ISE tego łącznika używa [limitów komunikatów ŚRODOWISKA ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

<a name="storage-firewalls"></a>

## <a name="access-storage-accounts-behind-firewalls"></a>Dostęp do kont magazynu za zaporami

Zabezpieczenia sieciowe można dodać do konta magazynu platformy Azure, ograniczając dostęp za pomocą [reguł zapory i zapory](../storage/common/storage-network-security.md). Jednak ta konfiguracja tworzy wyzwanie dla platformy Azure i innych usług firmy Microsoft, które wymagają dostępu do konta magazynu. Komunikacja lokalna w centrum danych wyodrębnia wewnętrzne adresy IP, więc nie można skonfigurować reguł zapory z ograniczeniami ip. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](../storage/common/storage-network-security.md).

Poniżej przedstawiono różne opcje uzyskiwania dostępu do kont magazynu za zaporami z usługi Azure Logic Apps przy użyciu łącznika usługi Azure Blob Storage lub innych rozwiązań:

* Łącznik usługi Azure Storage Blob

  * [Uzyskiwanie dostępu do kont magazynu w innych regionach](#access-other-regions)
  * [Uzyskiwanie dostępu do kont magazynu za pośrednictwem zaufanej sieci wirtualnej](#access-trusted-virtual-network)

* Inne rozwiązania

  * [Uzyskiwanie dostępu do kont magazynu jako zaufanej usługi z tożsamościami zarządzanymi](#access-trusted-service)
  * [Uzyskiwanie dostępu do kont magazynu za pośrednictwem usługi Azure API Management](#access-api-management)

<a name="access-other-regions"></a>

### <a name="problems-accessing-storage-accounts-in-the-same-region"></a>Problemy z uzyskiwaniem dostępu do kont magazynu w tym samym regionie

Aplikacje logiki nie mogą bezpośrednio uzyskiwać dostępu do kont magazynu za zaporami, gdy są w tym samym regionie. Aby obejść ten problem, umieść aplikacje logiki w regionie, który różni się od konta magazynu i uadź dostęp do [wychodzących adresów IP dla zarządzanych łączników w twoim regionie](../logic-apps/logic-apps-limits-and-config.md#outbound).

> [!NOTE]
> To rozwiązanie nie ma zastosowania do łącznika usługi Azure Table Storage i łącznika usługi Azure Queue Storage. Zamiast tego, aby uzyskać dostęp do magazynu tabel lub magazynu kolejek, użyj wbudowanego wyzwalacza HTTP i akcji.

<a name="access-trusted-virtual-network"></a>

### <a name="access-storage-accounts-through-a-trusted-virtual-network"></a>Uzyskiwanie dostępu do kont magazynu za pośrednictwem zaufanej sieci wirtualnej

Konto magazynu można umieścić w zarządzanej sieci wirtualnej platformy Azure, a następnie dodać tę sieć wirtualną do listy zaufanych sieci wirtualnych. Aby aplikacja logiki uzyskała dostęp do konta magazynu za pośrednictwem [zaufanej sieci wirtualnej,](../virtual-network/virtual-networks-overview.md)należy wdrożyć tę aplikację logiki w [środowisku usługi integracji (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)które może łączyć się z zasobami w sieci wirtualnej. Następnie można dodać podsieci w tej ise do listy zaufanych. Łączniki usługi Azure Storage, takie jak łącznik magazynu obiektów Blob, mogą bezpośrednio uzyskiwać dostęp do kontenera magazynu. Ta konfiguracja jest taka sama jak przy użyciu punktów końcowych usługi z środowiska ISE.

<a name="access-trusted-service"></a>

### <a name="access-storage-accounts-as-a-trusted-service-with-managed-identities"></a>Uzyskiwanie dostępu do kont magazynu jako zaufanej usługi z tożsamościami zarządzanymi

Aby przyznać zaufanym usługom firmy Microsoft dostęp do konta magazynu za pośrednictwem zapory, można skonfigurować wyjątek na tym koncie magazynu dla tych usług. To rozwiązanie zezwala na usługi platformy Azure, które obsługują [tożsamości zarządzane do uwierzytelniania,](../active-directory/managed-identities-azure-resources/overview.md) aby uzyskać dostęp do kont magazynu za zaporami jako zaufane usługi. W szczególności dla aplikacji logiki w globalnej platformy Azure wielu dzierżawców, aby uzyskać dostęp do tych kont magazynu, najpierw [włączyć obsługę tożsamości zarządzanej](../logic-apps/create-managed-service-identity.md) w aplikacji logiki. Następnie użyj akcji HTTP lub wyzwalacza w aplikacji logiki i [ustaw ich typ uwierzytelniania, aby używać tożsamości zarządzanej aplikacji logiki](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity). W tym scenariuszu można użyć *tylko* akcji HTTP lub wyzwalacza.

Aby skonfigurować obsługę wyjątków i tożsamości zarządzanych, wykonaj następujące ogólne kroki:

1. Na koncie magazynu w obszarze **Ustawienia**wybierz pozycję **Zapory i sieci wirtualne**. W obszarze **Zezwalaj na dostęp z**wybierz opcję **Wybrane sieci,** aby były wyświetlane powiązane ustawienia.

1. W obszarze **Wyjątki**wybierz pozycję **Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu**, a następnie wybierz pozycję **Zapisz**.

   ![Wybierz wyjątek, który zezwala na zaufane usługi firmy Microsoft](./media/connectors-create-api-azureblobstorage/allow-trusted-services-firewall.png)

1. W ustawieniach aplikacji logiki [włącz obsługę tożsamości zarządzanej](../logic-apps/create-managed-service-identity.md).

1. W przepływie pracy aplikacji logiki dodaj i skonfiguruj akcję HTTP lub wyzwalacz, aby uzyskać dostęp do konta magazynu lub encji.

   > [!IMPORTANT]
   > W przypadku wychodzących wywołań HTTP lub wyzwalaczy na kontach usługi Azure Storage upewnij się, że nagłówek żądania zawiera `x-ms-version` właściwość i wersję interfejsu API dla operacji, którą chcesz uruchomić na koncie magazynu. Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie dostępu za pomocą tożsamości zarządzanej](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) i [przechowywanie wersji dla usług Azure Storage](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests).

1. W tej akcji [wybierz tożsamość zarządzana](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) do użycia do uwierzytelniania.

<a name="access-api-management"></a>

### <a name="access-storage-accounts-through-azure-api-management"></a>Uzyskiwanie dostępu do kont magazynu za pośrednictwem usługi Azure API Management

Jeśli używasz dedykowanej warstwy do [zarządzania interfejsem API,](../api-management/api-management-key-concepts.md)można front interfejsu API magazynu przy użyciu zarządzania interfejsami API i zezwalając na jego adresy IP za pośrednictwem zapory. Zasadniczo dodaj sieć wirtualną platformy Azure, która jest używana przez usługę API Management, do ustawienia zapory konta magazynu. Następnie można użyć akcji zarządzania interfejsami API lub akcji HTTP, aby wywołać interfejsy API usługi Azure Storage. Jednak jeśli wybierzesz tę opcję, musisz sam obsłużyć proces uwierzytelniania. Aby uzyskać więcej informacji, zobacz [Architektura prostej integracji przedsiębiorstwa](https://aka.ms/aisarch).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)

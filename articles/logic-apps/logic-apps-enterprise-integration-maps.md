---
title: Przekształć element XML przy użyciu map XSLT — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Dodaj XSLT mapuje przekształcać pliki XML w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
manager: carmonm
ms.topic: article
ms.assetid: 90f5cfc4-46b2-4ef7-8ac4-486bb0e3f289
ms.date: 02/06/2019
ms.openlocfilehash: d0d40ca0ae6ccd4f709d7d94d52764d4affcc215
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244699"
---
# <a name="transform-xml-with-maps-in-azure-logic-apps-with-enterprise-integration-pack"></a>Przekształć element XML z mapami w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw

Na przesyłanie danych XML między formatami integracji dla scenariuszy dla przedsiębiorstw w usłudze Azure Logic Apps, aplikacja logiki można użyć, mapy lub dokładniej, arkusz stylów Extensible, który mapuje języka przekształcenia (XSLT). Mapa jest dokument XML, który opisuje sposób konwertowania danych z dokumentu XML do innego formatu. 

Na przykład załóżmy, że regularnie otrzymywać zleceń B2B lub faktur od klienta, który używa formatu daty YYYMMDD. Jednak Twoja organizacja korzysta z MMDDYYY format daty. Można zdefiniować i użyć mapy, który przekształca format daty YYYMMDD do formatu MMDDYYY przed zapisaniem szczegóły zamówienia lub faktury w bazie danych działań klientów.

Limity dotyczące kont integracji i artefaktów, takich jak mapy, zobacz [limity i informacje o konfiguracji dla usługi Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* [Konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) przechowywania map i innych artefaktów dla integracji dla przedsiębiorstw i rozwiązań biznesowych (B2B).

* Jeśli mapa odwołuje się do zestawu zewnętrznego, musisz przekazać *zarówno w zestawie, jak i mapy* na koncie integracji. Upewnij się, że [ *Przekaż pierwszy zestaw*](#add-assembly), a następnie przekaż mapę, która odwołuje się do zestawu.

  Jeśli zestaw jest 2 MB lub mniejszych, można dodać zestaw na koncie integracji *bezpośrednio* w witrynie Azure portal. Jednak jeśli Twoje zestawu lub mapy jest większy niż 2 MB, ale nie większy niż [limit rozmiaru dla zestawów lub mapy](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits), jest podjęcie następujących kroków:

  * Dla zestawów należy kontenera obiektów blob platformy Azure, gdzie możesz przekazać zestawu i lokalizacji tego kontenera. W ten sposób można podać tej lokalizacji później po dodaniu zestawu na koncie integracji. 
  Dla tego zadania potrzebne są następujące elementy:

    | Element | Opis |
    |------|-------------|
    | [Konto usługi Azure Storage](../storage/common/storage-account-overview.md) | Na tym koncie należy utworzyć kontener obiektów blob platformy Azure dla swojego zestawu. Dowiedz się, [sposób tworzenia konta magazynu](../storage/common/storage-quickstart-create-account.md). |
    | Kontener obiektów blob | W tym kontenerze możesz przekazać zestawu. Należy również lokalizację tego kontenera po dodaniu zestawu na koncie integracji. Dowiedz się, jak [Utwórz kontener obiektów blob](../storage/blobs/storage-quickstart-blobs-portal.md). |
    | [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) | To narzędzie ułatwia więcej łatwe zarządzanie kontami magazynu i kontenerach obiektów blob. Aby użyć Eksploratora usługi Storage, albo [pobieranie i instalowanie Eksploratora usługi Azure Storage](https://www.storageexplorer.com/). Następnie połącz Eksplorator usługi Storage do konta magazynu, wykonując kroki opisane w [wprowadzenie do Eksploratora usługi Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md). Aby dowiedzieć się więcej, zobacz [Szybki start: Tworzenie obiektu blob w magazynie obiektów przy użyciu usługi Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md). <p>Lub, w witrynie Azure portal Znajdź i wybierz swoje konto magazynu. Wybierz z menu konta magazynu, **Eksploratora usługi Storage**. |
    |||

  * Dla map, można obecnie dodawanie map na większych przy użyciu [mapuje API REST usługi Azure Logic Apps —](https://docs.microsoft.com/rest/api/logic/maps/createorupdate).

Nie ma potrzeby podczas tworzenia i dodawania mapy aplikacji logiki. Jednak aby używać mapy, potrzebuje aplikacja logiki łączenia z kontem integracji, gdzie przechowywane są mapowane. Dowiedz się, [sposobu łączenia aplikacji logiki z konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Dowiedz się, jeśli nie masz jeszcze aplikacji logiki, [jak tworzyć aplikacje logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-assembly"></a>

## <a name="add-referenced-assemblies"></a>Dodaj przywoływanych zestawach

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu poświadczeń konta Azure.

1. Aby znaleźć i otworzyć konto integracji w głównym menu platformy Azure, wybierz pozycję **wszystkich usług**. 
   W polu wyszukiwania wprowadź "konto integracji". 
   Wybierz **kont integracji**.

   ![Znajdź konto integracji](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Wybierz konto integracji, w której chcesz dodać zestaw, na przykład:

   ![Wybierz konto integracji](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. Na koncie integracji **Przegląd** w obszarze **składniki**, wybierz opcję **zestawy** kafelka.

   ![Wybierz pozycję "Zespoły"](./media/logic-apps-enterprise-integration-maps/select-assemblies.png)

1. Po **zestawy** zostanie otwarta strona, wybierz polecenie **Dodaj**.

   ![Wybieranie pozycji "Dodaj"](./media/logic-apps-enterprise-integration-maps/add-assembly.png)

Na podstawie pliku zestawu rozmiaru, wykonaj kroki przekazywania zestawu, który jest [do 2 MB](#smaller-assembly) lub [więcej niż 2 MB, ale tylko do 8 MB](#larger-assembly).
Limity ilości zestaw konta integracji, zobacz [limity i konfiguracja dla usługi Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits).

> [!NOTE]
> Zmiana zestawu, należy również zaktualizować mapę, czy mapa ma zmiany.

<a name="smaller-assembly"></a>

### <a name="add-assemblies-up-to-2-mb"></a>Dodaj zestawy do 2 MB

1. W obszarze **Dodaj zestaw**, wprowadź nazwę zestawu. Zachowaj **mały plik** wybrane. Obok pozycji **zestawu** , wybierz ikonę folderu. Znajdź i wybierz zestaw, który jest przekazywany, na przykład:

   ![Przekaż mniejszy zestaw](./media/logic-apps-enterprise-integration-maps/upload-assembly-file.png)

   W **nazwy zestawu** właściwości nazwy pliku zestawu pojawia się automatycznie po wybraniu zestawu.

1. Gdy wszystko będzie gotowe, wybierz pozycję **OK**.

   Po zakończeniu przekazywania pliku zestawu zestawu pojawia się w **zestawy** listy.

   ![Lista przekazanych zestawów](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies-list.png)

   Na koncie integracji **Przegląd** w obszarze **składniki**, **zestawy** teraz kafelkowi liczba przekazanych zestawów, na przykład:

   ![Przekazano zestawów](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies.png)

<a name="larger-assembly"></a>

### <a name="add-assemblies-more-than-2-mb"></a>Dodaj zestawy, więcej niż 2 MB

Aby dodać większych zestawów, możesz przekazać zestawu do kontenera obiektów blob platformy Azure na koncie magazynu platformy Azure. Twoje kroki, aby dodać zestawy różnią się w zależności czy kontenera obiektów blob jest publiczny dostęp do odczytu. Dlatego najpierw należy sprawdzić, czy kontenera obiektów blob ma publiczny dostęp do odczytu, wykonaj następujące czynności: [Ustaw poziom dostępu publicznego do kontenera obiektów blob](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Sprawdź poziom dostępu do kontenera

1. Otwórz Eksplorator usługi Azure Storage. W oknie Eksploratora rozwiń subskrypcji platformy Azure, jeśli nie jest jeszcze rozwinięty.

1. Rozwiń **kont magazynu** > {*swoje konto magazynu*} > **kontenerach obiektów Blob**. Wybierz kontener obiektów blob.

1. Wybierz z menu skrótów dla kontenera obiektów blob, **Ustaw poziom dostępu publicznego**.

   * Jeśli co najmniej publicznego dostępu do kontenera obiektów blob, wybierz **anulować**i wykonaj następujące czynności w późniejszym czasie na tę stronę: [Przekaż do kontenerów przy użyciu dostępu publicznego](#public-access-assemblies)

     ![Dostęp publiczny](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Jeśli kontener obiektów blob nie ma publicznego dostępu, wybierz opcję **anulować**i wykonaj następujące czynności w późniejszym czasie na tę stronę: [Przekazywanie do kontenerów, bez dostępu publicznego](#no-public-access-assemblies)

     ![Brak dostępu publicznego](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-assemblies"></a>

#### <a name="upload-to-containers-with-public-access"></a>Przekaż do kontenerów przy użyciu dostępu publicznego

1. Przekaż zestawu do swojego konta magazynu. 
   W oknie po prawej stronie wybierz **przekazywanie**.

1. Po zakończeniu przekazywania wybierz przekazany zestaw. Na pasku narzędzi wybierz **URL kopii** tak, aby skopiować adres URL zestawu.

1. Wróć do witryny Azure portal gdzie **Dodaj zestaw** okienko jest otwarte. 
   Wprowadź nazwę zestawu. 
   Wybierz **duży plik (więcej niż 2 MB)** .

   **Identyfikator URI zawartości** pojawi się pole, a nie **zestawu** pole.

1. W **identyfikator URI zawartości** pole, wklej adres URL swojego zestawu. 
   Zakończ, dodawanie zestawu.

Po zakończeniu przekazywania Twojego zestawu schematu jest wyświetlana w **zestawy** listy.
Na koncie integracji **Przegląd** w obszarze **składniki**, **zestawy** Kafelek pokazuje teraz liczba przekazanych zestawów.

<a name="no-public-access-assemblies"></a>

#### <a name="upload-to-containers-without-public-access"></a>Przekazywanie do kontenerów, bez dostępu publicznego

1. Przekaż zestawu do swojego konta magazynu. 
   W oknie po prawej stronie wybierz **przekazywanie**.

1. Po zakończeniu przekazywania Generowanie sygnatury dostępu współdzielonego (SAS) dla Twojego zestawu. 
   Wybierz z menu skrótów swojego zestawu **Uzyskaj sygnaturę dostępu współdzielonego**.

1. W **udostępnionej sygnatury dostępu** okienku wybierz **identyfikatora URI sygnatury dostępu współdzielonego na poziomie kontenera Generuj** > **Utwórz**. 
   Po adres URL sygnatury dostępu Współdzielonego pobiera wygenerowany, obok **adresu URL** wybierz **kopiowania**.

1. Wróć do witryny Azure portal gdzie **Dodaj zestaw** okienko jest otwarte. 
   Wprowadź nazwę zestawu. 
   Wybierz **duży plik (więcej niż 2 MB)** .

   **Identyfikator URI zawartości** pojawi się pole, a nie **zestawu** pole.

1. W **identyfikator URI zawartości** pole, wklej identyfikator URI sygnatury dostępu Współdzielonego, wcześniej wygenerowany. Zakończ, dodawanie zestawu.

Po zakończeniu przekazywania Twojego zestawu zestawu pojawia się w **schematów** listy. Na koncie integracji **Przegląd** w obszarze **składniki**, **zestawy** Kafelek pokazuje teraz liczba przekazanych zestawów.

## <a name="create-maps"></a>Tworzenie map

Aby utworzyć dokument XSLT, ale można użyć jako mapę, należy użyć programu Visual Studio 2015 do tworzenia projektu integracja BizTalk przy użyciu [pakiet integracyjny dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md). W tym projekcie można utworzyć pliku mapy integracji, który pozwala wizualnie mapować elementy między dwoma plikami schematu XML. Po skompilowaniu projektu możesz uzyskać dokument XSLT.
Limity dotyczące ilości mapy w ramach kont integracji, zobacz [limity i konfiguracja dla usługi Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits). 

## <a name="add-maps"></a>Dodawanie map

Po załadowaniu wszystkich zestawów, do których odwołuje się do mapy możesz teraz przekazać mapy.

1. Jeśli użytkownik jeszcze nie zostało to zrobione, zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu poświadczeń konta platformy Azure. 

1. Jeśli na koncie integracji nie jest jeszcze otwarty, w menu głównym platformy Azure, wybierz **wszystkich usług**. 
   W polu wyszukiwania wprowadź "konto integracji". 
   Wybierz **kont integracji**.

   ![Znajdź konto integracji](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Wybierz konto integracji, w której chcesz dodać mapę, na przykład:

   ![Wybierz konto integracji](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. Na koncie integracji **Przegląd** w obszarze **składniki**, wybierz opcję **mapy** kafelka.

   ![Wybierz pozycję "Mapy"](./media/logic-apps-enterprise-integration-maps/select-maps.png)

1. Po **mapy** zostanie otwarta strona, wybierz polecenie **Dodaj**.

   ![Wybieranie pozycji "Dodaj"](./media/logic-apps-enterprise-integration-maps/add-map.png)  

<a name="smaller-map"></a>

### <a name="add-maps-up-to-2-mb"></a>Dodawanie map do 2 MB

1. W obszarze **Dodaj mapę**, wprowadź nazwę dla mapy. 

1. W obszarze **mapy typu**, wybierz typ, na przykład: **Płynne**, **XSLT**, **XSLT 2.0**, lub **XSLT 3.0**.

1. Zachowaj **mały plik** wybrane. Obok pozycji **mapy** , wybierz ikonę folderu. Znajdź i wybierz mapę, który jest przekazywany, na przykład:

   ![Przekaż mapy](./media/logic-apps-enterprise-integration-maps/upload-map-file.png)

   Jeśli pozostawiono **nazwa** właściwość jest pusta, nazwa pliku mapy automatycznie pojawia się w tej właściwości automatycznie po wybraniu pliku mapy. 
   Można jednak użyć dowolną unikatową nazwę.

1. Gdy wszystko będzie gotowe, wybierz pozycję **OK**. 
   Po zakończeniu przekazywania pliku mapy mapy pojawia się w **mapy** listy.

   ![Lista przekazanych mapy](./media/logic-apps-enterprise-integration-maps/uploaded-maps-list.png)

   Na koncie integracji **Przegląd** w obszarze **składniki**, **mapuje** teraz kafelkowi liczba przekazanych mapy, na przykład:

   ![Przekazano mapy](./media/logic-apps-enterprise-integration-maps/uploaded-maps.png)

<a name="larger-map"></a>

### <a name="add-maps-more-than-2-mb"></a>Dodawanie map, więcej niż 2 MB

Obecnie, aby dodać większej map, użyj [mapuje API REST usługi Azure Logic Apps —](https://docs.microsoft.com/rest/api/logic/maps/createorupdate).

<!--

To add larger maps, you can upload your map to 
an Azure blob container in your Azure storage account. 
Your steps for adding maps differ based whether your 
blob container has public read access. So first, check 
whether or not your blob container has public read 
access by following these steps: 
[Set public access level for blob container](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### Check container access level

1. Open Azure Storage Explorer. In the Explorer window, 
   expand your Azure subscription if not already expanded.

1. Expand **Storage Accounts** > {*your-storage-account*} > 
   **Blob Containers**. Select your blob container.

1. From your blob container's shortcut menu, 
   select **Set Public Access Level**.

   * If your blob container has at least public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers with public access](#public-access)

     ![Public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * If your blob container doesn't have public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers without public access](#public-access)

     ![No public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-maps"></a>

### Add maps to containers with public access

1. Upload the map to your storage account. 
   In the right-hand window, choose **Upload**. 

1. After you finish uploading, select your 
   uploaded map. On the toolbar, choose **Copy URL** 
   so that you copy the map's URL.

1. Return to the Azure portal where the 
   **Add Map** pane is open. Choose **Large file**. 

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste your map's URL. 
   Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

<a name="no-public-access-maps"></a>

### Add maps to containers with no public access

1. Upload the map to your storage account. 
   In the right-hand window, choose **Upload**.

1. After you finish uploading, generate a 
   shared access signature (SAS) for your schema. 
   From your map's shortcut menu, 
   select **Get Shared Access Signature**.

1. In the **Shared Access Signature** pane, select 
   **Generate container-level shared access signature URI** > **Create**. 
   After the SAS URL gets generated, next to the **URL** box, choose **Copy**.

1. Return to the Azure portal where the 
   **Add Maps** pane is open. Choose **Large file**.

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste the SAS URI 
   you previously generated. Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

-->

## <a name="edit-maps"></a>Edytuj mapy

Aby zaktualizować istniejącą mapę, masz Przekaż nowy plik mapy, który zawiera zmiany, które mają. Jednakże można najpierw pobrać istniejącej mapy do edycji.

1. W [witryny Azure portal](https://portal.azure.com), Znajdź i Otwórz swoje konto integracji, jeśli nie już otwarty.

1. W głównym menu platformy Azure, wybierz **wszystkich usług**. W polu wyszukiwania wprowadź "konto integracji". Wybierz **kont integracji**.

1. Wybierz konto integracji, które chcesz zaktualizować mapę.

1. Na koncie integracji **Przegląd** w obszarze **składniki**, wybierz opcję **mapy** kafelka.

1. Po **mapy** zostanie otwarta strona, wybierz mapę. 
   Aby pobrać i Edytuj mapę najpierw, wybierz opcję **Pobierz**i Zapisz mapy.

1. Kiedy wszystko będzie gotowe do przekazania zaktualizowano mapę na **mapy** Wybierz mapę, o których chcesz zaktualizować, a następnie wybierz pozycję **aktualizacji**.

1. Znajdź i wybierz zaktualizowano mapę, którą chcesz przekazać. 
   Po zakończeniu przekazywania pliku mapy zaktualizowano mapę pojawia się w **mapy** listy.

## <a name="delete-maps"></a>Usuwanie mapy

1. W [witryny Azure portal](https://portal.azure.com), Znajdź i Otwórz swoje konto integracji, jeśli nie już otwarty.

1. W głównym menu platformy Azure, wybierz **wszystkich usług**. 
   W polu wyszukiwania wprowadź "konto integracji". 
   Wybierz **kont integracji**.

1. Wybierz konto integracji, które chcesz usunąć mapy.

1. Na koncie integracji **Przegląd** w obszarze **składniki**, wybierz opcję **mapy** kafelka.

1. Po **mapy** zostanie otwarta strona, wybierz mapę, a następnie wybierz **Usuń**.

1. Aby upewnić się, aby usunąć mapy, wybierz opcję **tak**.

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się więcej na temat pakietu integracyjnego dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md)  
* [Dowiedz się więcej na temat schematów](../logic-apps/logic-apps-enterprise-integration-schemas.md)
* [Dowiedz się więcej na temat przekształceń](../logic-apps/logic-apps-enterprise-integration-transform.md)

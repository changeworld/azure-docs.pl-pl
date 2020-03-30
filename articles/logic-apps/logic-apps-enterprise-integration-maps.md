---
title: Przekształcanie xml za pomocą map XSLT
description: Dodawanie map XSLT w celu przekształcenia kodu XML w usłudze Azure Logic Apps z pakietem integracji przedsiębiorstwa
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/06/2019
ms.openlocfilehash: e186b9713c8464f8f37e1e0bf112c4118621925c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979405"
---
# <a name="transform-xml-with-maps-in-azure-logic-apps-with-enterprise-integration-pack"></a>Przekształć kod XML przy użyciu map w usłudze Azure Logic Apps z Pakietem integracyjnym dla przedsiębiorstw

Aby przenieść dane XML między formatami scenariuszy integracji przedsiębiorstwa w usłudze Azure Logic Apps, aplikacja logiki może używać map, a dokładniej map przekształceń języka arkusza stylów rozszerzalnych (XSLT). Mapa to dokument XML, który opisuje sposób konwertowania danych z dokumentu XML na inny format. 

Załóżmy na przykład, że regularnie otrzymujesz zamówienia lub faktury B2B od odbiorcy, który używa formatu daty RRRRMDD. Jednak organizacja używa formatu daty MMDDYYY. Można zdefiniować i użyć mapy, która przekształca format daty RRRRMMDD w format MMDDYYY przed zapisaniem szczegółów zamówienia lub faktury w bazie danych aktywności klienta.

Aby uzyskać limity związane z kontami integracji i artefaktami, takimi jak mapy, zobacz [Limity i informacje o konfiguracji usługi Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* [Konto integracji,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) na którym przechowujesz mapy i inne artefakty dla rozwiązań integracji przedsiębiorstwa i między przedsiębiorstwami (B2B).

* Jeśli mapa odwołuje się do zestawu zewnętrznego, należy przekazać *zestaw i mapę* do konta integracji. Upewnij się, że [*najpierw prześlesz zestaw*](#add-assembly), a następnie przekaż mapę, która odwołuje się do zestawu.

  Jeśli zestaw jest 2 MB lub mniejszy, można dodać zestawu do konta integracji bezpośrednio z *witryny* Azure portal. Jeśli jednak złożenie lub mapa jest większa niż 2 MB, ale nie większa niż [limit rozmiaru dla zestawów lub map,](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)dostępne są następujące opcje:

  * W przypadku zestawów potrzebny jest kontener obiektów blob platformy Azure, w którym można przekazać zestaw i lokalizację tego kontenera. W ten sposób można podać tę lokalizację później po dodaniu zestawu do konta integracji. 
  Do tego zadania potrzebne są następujące elementy:

    | Element | Opis |
    |------|-------------|
    | [Konto magazynu platformy Azure](../storage/common/storage-account-overview.md) | Na tym koncie utwórz kontener obiektów blob platformy Azure dla zestawu. Dowiedz [się, jak utworzyć konto magazynu](../storage/common/storage-account-create.md). |
    | Kontener obiektów blob | W tym kontenerze można przekazać zestaw. Po dodaniu zestawu do konta integracji potrzebna jest również lokalizacja tego kontenera. Dowiedz się, jak [utworzyć kontener obiektów blob](../storage/blobs/storage-quickstart-blobs-portal.md). |
    | [Eksplorator usługi Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) | To narzędzie ułatwia łatwiejsze zarządzanie kontami magazynu i kontenerami obiektów blob. Aby użyć Eksploratora magazynu, [pobierz i zainstaluj Eksploratora usługi Azure Storage](https://www.storageexplorer.com/). Następnie należy połączyć Eksploratora magazynu z kontem magazynu, wykonując czynności opisane w programie [Wprowadzenie do Eksploratora magazynu](../vs-azure-tools-storage-manage-with-storage-explorer.md). Aby dowiedzieć się więcej, zobacz [Szybki start: Tworzenie obiektu blob w magazynie obiektów za pomocą Eksploratora usługi Azure Storage](../storage/blobs/storage-quickstart-blobs-storage-explorer.md). <p>Lub w witrynie Azure portal znajdź i wybierz swoje konto magazynu. Z menu konta magazynu wybierz pozycję **Eksplorator magazynu**. |
    |||

  * W przypadku map można obecnie dodawać większe mapy za pomocą [interfejsu API REST aplikacji Azure Logic Apps — Mapy](https://docs.microsoft.com/rest/api/logic/maps/createorupdate).

Podczas tworzenia i dodawania map nie jest potrzebna aplikacja logiki. Jednak aby użyć mapy, aplikacja logiki musi połączyć się z kontem integracji, na którym można przechowywać tę mapę. Dowiedz [się, jak połączyć aplikacje logiki z kontami integracji.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account) Jeśli nie masz jeszcze aplikacji logiki, dowiedz się, [jak tworzyć aplikacje logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-assembly"></a>

## <a name="add-referenced-assemblies"></a>Dodawanie zestawów odniesienia

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu poświadczeń konta Azure.

1. Aby znaleźć i otworzyć konto integracji, w głównym menu platformy Azure wybierz pozycję **Wszystkie usługi**. 
   W polu wyszukiwania wpisz "konto integracji". 
   Wybierz **konta integracji**.

   ![Znajdowanie konta integracji](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Wybierz konto integracji, na którym chcesz dodać zestaw, na przykład:

   ![Wybierz konto integracji](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. Na stronie **Przegląd** konta integracji w obszarze **Komponenty**wybierz kafelek **Zestawy.**

   ![Wybierz "Złożenia"](./media/logic-apps-enterprise-integration-maps/select-assemblies.png)

1. Po otwarciu strony **Złożenia** wybierz pozycję **Dodaj**.

   ![Wybierz "Dodaj"](./media/logic-apps-enterprise-integration-maps/add-assembly.png)

Na podstawie rozmiaru pliku złożenia wykonaj kroki przesyłania zestawu o [rozmiarze do 2 MB](#smaller-assembly) lub [większym niż 2 MB, ale tylko do 8 MB.](#larger-assembly)
Aby uzyskać limity ilości zestawów na kontach integracji, zobacz [Limity i konfiguracja usługi Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits).

> [!NOTE]
> Jeśli zmienisz zestaw, musisz również zaktualizować mapę, niezależnie od tego, czy mapa ma ulec zmianie.

<a name="smaller-assembly"></a>

### <a name="add-assemblies-up-to-2-mb"></a>Dodawanie zestawów do 2 MB

1. W obszarze **Dodaj złożenie**wprowadź nazwę zestawu. Zachowaj mały **plik** zaznaczony. Obok pola **Montaż** wybierz ikonę folderu. Znajdź i wybierz przesłany zestaw, na przykład:

   ![Przekazywanie mniejszego zestawu](./media/logic-apps-enterprise-integration-maps/upload-assembly-file.png)

   We właściwości **Nazwa zestawu** nazwa pliku zestawu pojawia się automatycznie po wybraniu zestawu.

1. Gdy będziesz gotowy, wybierz przycisk **OK**.

   Po zakończeniu przekazywania pliku złożenia zestaw pojawi się na liście **Zestawy.**

   ![Lista przesłanych zestawów](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies-list.png)

   Na stronie **Przegląd** konta integracji w obszarze **Komponenty**kafelek **Zestawy** pokazuje teraz liczbę przekazanych zestawów, na przykład:

   ![Przekazane zestawy](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies.png)

<a name="larger-assembly"></a>

### <a name="add-assemblies-more-than-2-mb"></a>Dodawanie zestawów o więcej niż 2 MB

Aby dodać większe zestawy, można przekazać zestaw do kontenera obiektów blob platformy Azure na koncie usługi Azure storage. Kroki dodawania zestawów różnią się w zależności od tego, czy kontener obiektów blob ma publiczny dostęp do odczytu. Dlatego najpierw sprawdź, czy kontener obiektów blob ma publiczny dostęp do odczytu, wykonując następujące kroki: [Ustaw poziom dostępu publicznego dla kontenera obiektów blob](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Sprawdzanie poziomu dostępu do kontenera

1. Otwórz Eksploratora usługi Azure Storage. W oknie Eksploratora rozwiń subskrypcję platformy Azure, jeśli nie została jeszcze rozwinięta.

1. Rozwiń **rozszerz konta magazynu** > {*konto magazynu*} > **kontenery obiektów blob**. Wybierz kontener obiektów blob.

1. Z menu skrótów kontenera obiektów blob wybierz polecenie **Ustaw publiczny poziom dostępu**.

   * Jeśli kontener obiektów blob ma co najmniej publiczny dostęp, wybierz pozycję **Anuluj**i wykonaj następujące kroki później na tej stronie: [Przekaż do kontenerów z dostępem publicznym](#public-access-assemblies)

     ![Dostęp publiczny](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Jeśli kontener obiektów blob nie ma dostępu publicznego, wybierz pozycję **Anuluj**i wykonaj następujące czynności później na tej stronie: [Przekaż do kontenerów bez dostępu publicznego](#no-public-access-assemblies)

     ![Brak publicznego dostępu](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-assemblies"></a>

#### <a name="upload-to-containers-with-public-access"></a>Przekazywanie do kontenerów z dostępem publicznym

1. Prześlij zestaw na swoje konto magazynu. 
   W oknie po prawej stronie wybierz pozycję **Przekaż**.

1. Po zakończeniu przesyłania wybierz przesłany zestaw. Na pasku narzędzi wybierz pozycję **Kopiuj adres URL,** aby skopiować adres URL zestawu.

1. Wróć do witryny Azure portal, w którym jest otwarte okienko **Dodaj zestaw.** 
   Wprowadź nazwę zestawu. 
   Wybierz **opcję Duży plik (większy niż 2 MB).**

   Teraz pojawi się pole **Identyfikator URI zawartości,** a nie pole **Zestawu.**

1. W polu **Identyfikator URI zawartości** wklej adres URL zestawu. 
   Zakończ dodawanie złożenia.

Po zakończeniu przekazywania zestawu schemat pojawia się na liście **Zestawy.**
Na stronie **Przegląd** konta integracji w obszarze **Komponenty**kafelek **Zestawy** pokazuje teraz liczbę przekazanych zestawów.

<a name="no-public-access-assemblies"></a>

#### <a name="upload-to-containers-without-public-access"></a>Przesyłanie do kontenerów bez dostępu publicznego

1. Prześlij zestaw na swoje konto magazynu. 
   W oknie po prawej stronie wybierz pozycję **Przekaż**.

1. Po zakończeniu przekazywania wygeneruj sygnaturę dostępu współdzielonego (SAS) dla zestawu. 
   Z menu skrótów zestawu wybierz pozycję **Pobierz podpis dostępu współdzielonego**.

1. W okienku **Podpis dostępu udostępnionego** wybierz pozycję **Generuj identyfikator URI** > podpisu dostępu współdzielonego na poziomie**kontenera**. 
   Po wygenerowaniu adresu URL sygnatury dostępu Współdzielonego obok pola **adresu URL** wybierz pozycję **Kopiuj**.

1. Wróć do witryny Azure portal, w którym jest otwarte okienko **Dodaj zestaw.** 
   Wprowadź nazwę zestawu. 
   Wybierz **opcję Duży plik (większy niż 2 MB).**

   Teraz pojawi się pole **Identyfikator URI zawartości,** a nie pole **Zestawu.**

1. W polu **Identyfikator URI zawartości** wklej poprzednio wygenerowany identyfikator URI sygnatury dostępu Współdzielonego. Zakończ dodawanie złożenia.

Po zakończeniu przekazywania zestawu zestaw pojawia się na liście **Schematy.** Na stronie **Przegląd** konta integracji w obszarze **Komponenty**kafelek **Zestawy** pokazuje teraz liczbę przekazanych zestawów.

## <a name="create-maps"></a>Tworzenie map

Aby utworzyć dokument XSLT, którego można użyć jako mapy, można użyć programu Visual Studio 2015 do utworzenia projektu integracji systemu BizTalk przy użyciu [pakietu Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md). W tym projekcie można utworzyć plik mapy integracji, który umożliwia wizualną mapę elementów między dwoma plikami schematu XML. Po utworzeniu tego projektu otrzymasz dokument XSLT.
Aby uzyskać limity ilości map na kontach integracji, zobacz [Limity i konfiguracja usługi Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits). 

## <a name="add-maps"></a>Dodawanie map

Po przesłaniu wszystkich zestawów, do których odwołuje się mapa, możesz teraz przesłać mapę.

1. Jeśli jeszcze się nie zalogowałeś, zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu poświadczeń konta platformy Azure. 

1. Jeśli twoje konto integracji nie jest jeszcze otwarte, w głównym menu platformy Azure wybierz pozycję **Wszystkie usługi**. 
   W polu wyszukiwania wpisz "konto integracji". 
   Wybierz **konta integracji**.

   ![Znajdowanie konta integracji](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Wybierz konto integracji, na którym chcesz dodać mapę, na przykład:

   ![Wybierz konto integracji](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. Na stronie **Przegląd** konta integracji w obszarze **Składniki**wybierz kafelek **Mapy.**

   ![Wybierz "Mapy"](./media/logic-apps-enterprise-integration-maps/select-maps.png)

1. Po otwarciu strony **Mapy** wybierz pozycję **Dodaj**.

   ![Wybierz "Dodaj"](./media/logic-apps-enterprise-integration-maps/add-map.png)  

<a name="smaller-map"></a>

### <a name="add-maps-up-to-2-mb"></a>Dodawanie map do 2 MB

1. W obszarze **Dodaj mapę**wprowadź nazwę mapy. 

1. W obszarze **Typ mapy**wybierz typ, na przykład: **Liquid**, **XSLT,** **XSLT 2.0**lub **XSLT 3.0**.

1. Zachowaj mały **plik** zaznaczony. Obok pola **Mapa** wybierz ikonę folderu. Znajdź i wybierz mapę, którą przesyłasz, na przykład:

   ![Prześlij mapę](./media/logic-apps-enterprise-integration-maps/upload-map-file.png)

   Jeśli **właściwość Nazwa** pozostanie pusta, nazwa pliku mapy zostanie automatycznie wyświetlena w tej właściwości automatycznie po wybraniu pliku mapy. 
   Można jednak użyć dowolnej unikatowej nazwy.

1. Gdy będziesz gotowy, wybierz przycisk **OK**. 
   Po zakończeniu przesyłania pliku mapy mapa pojawi się na liście **Mapy.**

   ![Przesłana lista map](./media/logic-apps-enterprise-integration-maps/uploaded-maps-list.png)

   Na stronie **Przegląd** konta integracji w obszarze **Komponenty**kafelek **Mapy** pokazuje teraz liczbę przesłanych map, na przykład:

   ![Przesłane mapy](./media/logic-apps-enterprise-integration-maps/uploaded-maps.png)

<a name="larger-map"></a>

### <a name="add-maps-more-than-2-mb"></a>Dodawanie map o więcej niż 2 MB

Obecnie, aby dodać większe mapy, użyj [interfejsu API REST aplikacji azure logic apps — Mapy](https://docs.microsoft.com/rest/api/logic/maps/createorupdate).

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

## <a name="edit-maps"></a>Edytowanie map

Aby zaktualizować istniejącą mapę, musisz przekazać nowy plik mapy, który zawiera żądane zmiany. Można jednak najpierw pobrać istniejącą mapę do edycji.

1. W [witrynie Azure portal](https://portal.azure.com)znajdź i otwórz swoje konto integracji, jeśli nie jest jeszcze otwarty.

1. W głównym menu platformy Azure wybierz pozycję **Wszystkie usługi**. W polu wyszukiwania wpisz "konto integracji". Wybierz **konta integracji**.

1. Wybierz konto integracji, na którym chcesz zaktualizować mapę.

1. Na stronie **Przegląd** konta integracji w obszarze **Składniki**wybierz kafelek **Mapy.**

1. Po otwarciu strony **Mapy** wybierz mapę. 
   Aby najpierw pobrać i edytować mapę, wybierz pozycję **Pobierz**i zapisz mapę.

1. Gdy będziesz gotowy do przesłania zaktualizowanej mapy, na stronie **Mapy** wybierz mapę, którą chcesz zaktualizować, i wybierz pozycję **Aktualizuj**.

1. Znajdź i wybierz zaktualizowaną mapę, którą chcesz przesłać. 
   Po zakończeniu przesyłania pliku mapy zaktualizowana mapa pojawi się na liście **Mapy.**

## <a name="delete-maps"></a>Usuwanie map

1. W [witrynie Azure portal](https://portal.azure.com)znajdź i otwórz swoje konto integracji, jeśli nie jest jeszcze otwarty.

1. W głównym menu platformy Azure wybierz pozycję **Wszystkie usługi**. 
   W polu wyszukiwania wpisz "konto integracji". 
   Wybierz **konta integracji**.

1. Wybierz konto integracji, na którym chcesz usunąć mapę.

1. Na stronie **Przegląd** konta integracji w obszarze **Składniki**wybierz kafelek **Mapy.**

1. Po otwarciu strony **Mapy** wybierz mapę i wybierz pozycję **Usuń**.

1. Aby potwierdzić, że chcesz usunąć mapę, wybierz pozycję **Tak**.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o pakiecie integracji dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md)  
* [Dowiedz się więcej o schematach](../logic-apps/logic-apps-enterprise-integration-schemas.md)
* [Dowiedz się więcej o transformacjach](../logic-apps/logic-apps-enterprise-integration-transform.md)

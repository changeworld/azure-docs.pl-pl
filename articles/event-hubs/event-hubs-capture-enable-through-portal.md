---
title: Centra zdarzeń — przechwytywanie zdarzeń przesyłania strumieniowego przy użyciu portalu Azure
description: W tym artykule opisano, jak włączyć przechwytywanie zdarzeń przesyłanych strumieniowo za pomocą usługi Azure Event Hubs przy użyciu witryny Azure Portal.
services: event-hubs
documentationcenter: ''
author: spelluru
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.custom: seodec18
ms.devlang: na
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: 8a6d9456b00e5520e6f4fbb9ccb77b0260731ddd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77187392"
---
# <a name="enable-capturing-of-events-streaming-through-azure-event-hubs"></a>Włączanie przechwytywania zdarzeń przesyłanych strumieniowo za pomocą usługi Azure Event Hubs

[Przechwytywanie usługi][capture-overview] Azure Event Hubs umożliwia automatyczne dostarczanie danych przesyłania strumieniowego w centrach zdarzeń do [magazynu obiektów Blob platformy Azure](https://azure.microsoft.com/services/storage/blobs/) lub konta usługi Azure Data Lake Storage [Gen1 lub Gen 2](https://azure.microsoft.com/services/data-lake-store/) do wyboru.

Przy użyciu witryny [Azure Portal](https://portal.azure.com) można skonfigurować funkcję przechwytywania podczas tworzenia centrum zdarzeń. Można przechwycić dane do kontenera [magazynu obiektów Blob](https://azure.microsoft.com/services/storage/blobs/) platformy Azure lub do konta [usługi Azure Data Lake Storage Gen 1 lub Gen 2.](https://azure.microsoft.com/services/data-lake-store/)

Aby uzyskać więcej informacji, zobacz [omówienie funkcji przechwytywania usługi Event Hubs][capture-overview].

## <a name="capture-data-to-azure-storage"></a>Przechwytywanie danych w usłudze Azure Storage

Podczas tworzenia centrum zdarzeń można włączyć funkcję przechwytywania przez kliknięcie przycisku **Wł** na ekranie portalu **Tworzenie centrum zdarzeń**. Następnie można określić konto magazynu i kontener przez kliknięcie pozycji **Azure Storage** w polu **Dostawca przechwytywania**. Ponieważ do uwierzytelniania w magazynie funkcja przechwytywania usługi Event Hubs korzysta z uwierzytelniania między usługami, nie trzeba podawać parametrów połączenia z magazynem. Selektor zasobów automatycznie wybiera identyfikator URI zasobu dla konta magazynu. Jeśli używasz usługi Azure Resource Manager, musisz jawnie podać ten identyfikator URI jako ciąg.

Domyślny przedział czasu to 5 minut. Minimalna wartość to 1, a maksymalna — 15. Okno **Rozmiar** ma zakres 10–500 MB.

![Przedział czasu przechwytywania][1]

> [!NOTE]
> Emitowanie pustych plików przy braku zdarzeń podczas okna przechwytywania można włączyć lub wyłączyć. 

## <a name="capture-data-to-azure-data-lake-storage-gen-2"></a>Przechwytywanie danych do usługi Azure Data Lake Storage Gen 2 

1. Postępuj zgodnie z artykułu [Utwórz konto magazynu,](../storage/common/storage-account-create.md?tabs=azure-portal#create-a-storage-account) aby utworzyć konto usługi Azure Storage. Ustaw **hierarchiczny obszar nazw** na **Włączone** na karcie **Zaawansowane,** aby uczynić go kontem usługi Azure Data Lake Storage Gen 2.
2. Podczas tworzenia centrum zdarzeń wykonaj następujące czynności: 

    1. Wybierz **opcję Włączone** dla **przechwytywania**. 
    2. Wybierz **usługę Azure Storage** jako dostawcę przechwytywania. Opcja **usługi Azure Data Lake Store,** którą widzisz dla dostawcy **przechwytywania,** jest dla genu 1 usługi Azure Data Lake Storage. Aby użyć gen 2 usługi Azure Data Lake Storage, należy wybrać **usługę Azure Storage**.
    2. Wybierz przycisk **Wybierz kontener.** 

        ![Włącz przechwytywanie do magazynu w uł.2 w ucho.](./media/event-hubs-capture-enable-through-portal/data-lake-storage-gen2.png)
3. Wybierz konto **usługi Azure Data Lake Storage Gen 2** z listy. 

    ![Wybierz magazyn danych Lake Gen 2](./media/event-hubs-capture-enable-through-portal/select-data-lake-storage-gen2.png)
4. Wybierz **kontener** (system plików w data lake storage gen 2).

    ![Wybieranie systemu plików w magazynie](./media/event-hubs-capture-enable-through-portal/select-file-system-data-lake-storage.png)
5. Na stronie **Tworzenie Centrum zdarzeń** wybierz pozycję **Utwórz**. 

    ![Wybierz przycisk Utwórz](./media/event-hubs-capture-enable-through-portal/create-event-hub-data-lake-storage.png)

    > [!NOTE]
    > Kontener utworzony w usłudze Azure Data Lake Storage Gen 2 przy użyciu tego interfejsu użytkownika (UI) jest wyświetlany w obszarze **Systemy plików** w **Eksploratorze magazynu**. Podobnie system plików utworzony na koncie Data Lake Storage Gen 2 jest wyświetlany jako kontener w tym interfejsie użytkownika. 


## <a name="capture-data-to-azure-data-lake-storage-gen-1"></a>Przechwytywanie danych do usługi Azure Data Lake Storage Gen 1 

Aby przechwycić dane do usługi Azure Data Lake Storage Gen 1, należy utworzyć konto magazynu usługi Data Lake Gen 1 i centrum zdarzeń:

### <a name="create-an-azure-data-lake-storage-gen-1-account-and-folders"></a>Tworzenie konta i folderów usługi Azure Data Storage Gen 1

1. Utwórz konto magazynu usługi Data Lake, postępując zgodnie z instrukcjami w [wprowadzenie do usługi Azure Data Lake Storage Gen 1 przy użyciu portalu Azure.](../data-lake-store/data-lake-store-get-started-portal.md)
2. Postępuj zgodnie z instrukcjami w sekcji [Przypisywanie uprawnień do centrów zdarzeń,](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs) aby utworzyć folder na koncie Data Lake Storage Gen 1, na którym chcesz przechwycić dane z centrum zdarzeń, i przypisać uprawnienia do centrów zdarzeń, aby można było zapisywać dane na koncie Data Lake Storage Gen 1.  


### <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

1. Centrum zdarzeń musi znajdować się w tej samej subskrypcji platformy Azure, co utworzone konto usługi Azure Data Lake Storage Gen 1. Aby utworzyć centrum zdarzeń, kliknij przycisk **Wł** w obszarze **Przechwytywanie** na stronie portalu **Tworzenie centrum zdarzeń**. 
2. Na stronie portalu **Tworzenie centrum zdarzeń** wybierz pozycję **Azure Data Lake Store** w polu **Dostawca przechwytywania**.
3. W **obszarze Wybierz sklep** obok listy rozwijanej Magazynu danych **Lake** Store określ konto Data Lake Storage Gen 1 utworzone wcześniej, a w polu Ścieżka **jeziora danych** wprowadź ścieżkę do utworzonego folderu danych.

    ![Wybieranie konta usługi Data Lake Storage][3]


## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>Dodawanie lub konfigurowanie funkcji przechwytywania w istniejącym centrum zdarzeń

Przechwytywanie można skonfigurować w istniejących centrach zdarzeń, które znajdują się w przestrzeniach nazw usługi Event Hubs. Aby włączyć funkcję przechwytywania w istniejącym centrum zdarzeń lub zmienić ustawienia przechwytywania, kliknij przestrzeń nazw w celu załadowania ekranu przeglądu, a następnie kliknij centrum zdarzeń, w którym chcesz włączyć przechwytywanie lub zmienić jego ustawienia. Na końcu kliknij opcję **Przechwyć** z lewej strony otwartej strony i zmodyfikuj ustawienia, jak pokazano na poniższych ilustracjach:

### <a name="azure-blob-storage"></a>Azure Blob Storage

![Konfigurowanie usługi Azure Blob Storage][2]

### <a name="azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2

![Konfigurowanie usługi Azure Data Lake Storage Gen 2](./media/event-hubs-capture-enable-through-portal/configure-data-lake-storage-gen2.png)

### <a name="azure-data-lake-storage-gen-1"></a>Usługa Azure Data Lake Storage Gen 1 

![Konfigurowanie usługi Azure Data Lake Storage][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat funkcji przechwytywania usługi Event Hubs, przeczytaj [omówienie funkcji przechwytywania usługi Event Hubs][capture-overview].
- Funkcję przechwytywania usługi Event Hubs możesz również skonfigurować za pomocą szablonów usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [Enable Capture using an Azure Resource Manager template (Włączanie funkcji przechwytywania przy użyciu szablonu usługi Azure Resource Manager)](event-hubs-resource-manager-namespace-event-hub-enable-capture.md).
- [Dowiedz się, jak utworzyć subskrypcję usługi Azure Event Grid przy użyciu przestrzeni nazw usługi Event Hubs jako źródła](store-captured-data-data-warehouse.md)
- [Wprowadzenie do usługi Azure Data Lake Store przy użyciu portalu Azure](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md

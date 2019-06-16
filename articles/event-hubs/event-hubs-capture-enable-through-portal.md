---
title: Przechwytywanie zdarzeń przesyłanych strumieniowo za pomocą witryny Azure Portal — Azure Event Hubs | Microsoft Docs
description: W tym artykule opisano, jak włączyć przechwytywanie zdarzeń przesyłanych strumieniowo za pomocą usługi Azure Event Hubs przy użyciu witryny Azure Portal.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.custom: seodec18
ms.devlang: na
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: shvija
ms.openlocfilehash: 9108c52529319288fba48dbad3c6f8aa6cb5f725
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60822549"
---
# <a name="enable-capturing-of-events-streaming-through-azure-event-hubs"></a>Włączanie przechwytywania zdarzeń przesyłanych strumieniowo za pomocą usługi Azure Event Hubs

[Funkcja przechwytywania usługi Azure Event Hubs][capture-overview] pozwala automatycznie dostarczać strumień danych usługi Event Hubs na wybrane konto usługi [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) lub [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/).

Przy użyciu witryny [Azure Portal](https://portal.azure.com) można skonfigurować funkcję przechwytywania podczas tworzenia centrum zdarzeń. Dane mogą być przechwytywane do kontenera usługi Azure [Blob Storage](https://azure.microsoft.com/services/storage/blobs/) lub na konto usługi [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/).

Aby uzyskać więcej informacji, zobacz [omówienie funkcji przechwytywania usługi Event Hubs][capture-overview].

## <a name="capture-data-to-an-azure-storage-account"></a>Przechwytywanie danych na konto usługi Azure Storage  

Podczas tworzenia centrum zdarzeń można włączyć funkcję przechwytywania przez kliknięcie przycisku **Wł** na ekranie portalu **Tworzenie centrum zdarzeń**. Następnie można określić konto magazynu i kontener przez kliknięcie pozycji **Azure Storage** w polu **Dostawca przechwytywania**. Ponieważ do uwierzytelniania w magazynie funkcja przechwytywania usługi Event Hubs korzysta z uwierzytelniania między usługami, nie trzeba podawać parametrów połączenia z magazynem. Selektor zasobów automatycznie wybiera identyfikator URI zasobu dla konta magazynu. Jeśli używasz usługi Azure Resource Manager, musisz jawnie podać ten identyfikator URI jako ciąg.

Domyślny przedział czasu to 5 minut. Minimalna wartość to 1, a maksymalna — 15. Okno **Rozmiar** ma zakres 10–500 MB.

![Przedział czasu przechwytywania][1]

> [!NOTE]
> Emitowanie pustych plików przy braku zdarzeń podczas okna przechwytywania można włączyć lub wyłączyć. 

## <a name="capture-data-to-an-azure-data-lake-store-account"></a>Przechwytywanie danych na konto usługi Azure Data Lake Store

Aby przechwycić dane do usługi Azure Data Lake Store, należy utworzyć konto usługi Data Lake Store i centrum zdarzeń:

### <a name="create-an-azure-data-lake-store-account-and-folders"></a>Tworzenie konta i folderów usługi Azure Data Lake Store

> [!NOTE]
> Obecnie funkcja przechwytywania usługi Event Hubs obsługuje tylko Gen 1 z usługi Azure Data Lake Store, nie Gen 2. 

1. Tworzenie konta Data Lake Store Gen 1, postępując zgodnie z instrukcjami w [Rozpoczynanie pracy z usługą Azure Data Lake Store, przy użyciu witryny Azure portal](../data-lake-store/data-lake-store-get-started-portal.md).
2. Wykonaj instrukcje opisane w sekcji [Przydzielanie uprawnień usłudze Event Hubs](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs), aby utworzyć folder na koncie usługi Data Lake Store, na którym chcesz przechwytywać dane z usługi Event Hubs, i przydzielić uprawnienia usłudze Event Hubs, aby mogła ona zapisywać dane na Twoim koncie usługi Data Lake Store.  


### <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

1. Centrum zdarzeń musi należeć do tej samej subskrypcji platformy Azure co właśnie utworzone konto usługi Azure Data Lake Store. Aby utworzyć centrum zdarzeń, kliknij przycisk **Wł** w obszarze **Przechwytywanie** na stronie portalu **Tworzenie centrum zdarzeń**. 
2. Na stronie portalu **Tworzenie centrum zdarzeń** wybierz pozycję **Azure Data Lake Store** w polu **Dostawca przechwytywania**.
3. W polu **Wybierz usługę Data Lake Store** określ utworzone wcześniej konto usługi Data Lake Store, a w polu **Ścieżka w usłudze Data Lake** wprowadź ścieżkę do utworzonego folderu danych.

    ![Wybieranie konta usługi Data Lake Storage][3]

## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>Dodawanie lub konfigurowanie funkcji przechwytywania w istniejącym centrum zdarzeń

Przechwytywanie można skonfigurować w istniejących centrach zdarzeń, które znajdują się w przestrzeniach nazw usługi Event Hubs. Aby włączyć funkcję przechwytywania w istniejącym centrum zdarzeń lub zmienić ustawienia przechwytywania, kliknij przestrzeń nazw w celu załadowania ekranu przeglądu, a następnie kliknij centrum zdarzeń, w którym chcesz włączyć przechwytywanie lub zmienić jego ustawienia. Na końcu kliknij opcję **Przechwyć** z lewej strony otwartej strony i zmodyfikuj ustawienia, jak pokazano na poniższych ilustracjach:

### <a name="azure-blob-storage"></a>Azure Blob Storage

![Konfigurowanie usługi Azure Blob Storage][2]

### <a name="azure-data-lake-store"></a>Azure Data Lake Store

![Konfigurowanie usługi Azure Data Lake Storage][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat funkcji przechwytywania usługi Event Hubs, przeczytaj [omówienie funkcji przechwytywania usługi Event Hubs][capture-overview].
- Funkcję przechwytywania usługi Event Hubs możesz również skonfigurować za pomocą szablonów usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [Enable Capture using an Azure Resource Manager template (Włączanie funkcji przechwytywania przy użyciu szablonu usługi Azure Resource Manager)](event-hubs-resource-manager-namespace-event-hub-enable-capture.md).
- [Dowiedz się, jak utworzyć subskrypcję usługi Azure Event Grid przy użyciu przestrzeni nazw usługi Event Hubs jako źródła](store-captured-data-data-warehouse.md)
- [Rozpoczynanie pracy z usługą Azure Data Lake Store za pośrednictwem witryny Azure Portal](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md

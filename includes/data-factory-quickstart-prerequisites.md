---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 06/27/2019
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 4d77cb8128105a40143a40e48ebe450115f7cf1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164171"
---
## <a name="prerequisites"></a>Wymagania wstępne

### <a name="azure-subscription"></a>Subskrypcja platformy Azure
Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

### <a name="azure-roles"></a>Role platformy Azure
Aby utworzyć wystąpienia usługi Data Factory, konto użytkownika używane do logowania się na platformie Azure musi być członkiem roli *współautora* lub *właściciela* albo *administratorem* subskrypcji platformy Azure. Aby wyświetlić uprawnienia, które masz w subskrypcji, przejdź do [witryny Azure portal](https://portal.azure.com), wybierz swoją nazwę użytkownika w prawym górnym rogu, wybierz ikonę "**...**", aby uzyskać więcej opcji, a następnie wybierz pozycję **Moje uprawnienia**. Jeśli masz dostęp do wielu subskrypcji, wybierz odpowiednią subskrypcję.

Aby utworzyć zasoby podrzędne — w tym zestawy danych, połączone usługi, potoki, wyzwalacze i środowiska Integration Runtime — oraz nimi zarządzać w usłudze Data Factory, należy spełnić następujące wymagania:

- Aby utworzyć zasoby podrzędne i nimi zarządzać, trzeba należeć do roli **Współautor w usłudze Data Factory** na poziomie grupy zasobów lub wyższej.
- W przypadku tworzenia zasobów podrzędnych i zarządzania nimi za pomocą programu PowerShell lub zestawu SDK rola **współautora** na poziomie grupy zasobów lub wyższej jest wystarczająca.

Aby uzyskać przykładowe instrukcje dotyczące dodawania użytkownika do roli, zobacz artykuł [Add roles (Dodawanie ról)](../articles/cost-management-billing/manage/add-change-subscription-administrator.md).

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- [Data Factory Contributor role (Rola współautora w usłudze Data Factory)](../articles/role-based-access-control/built-in-roles.md#data-factory-contributor)
- [Roles and permissions for Azure Data Factory (Role i uprawnienia w usłudze Data Factory)](../articles/data-factory/concepts-roles-permissions.md)

### <a name="azure-storage-account"></a>Konto usługi Azure Storage
Ogólne przeznaczenie konta usługi Azure Storage (w szczególności magazynu obiektów Blob) jako *źródłowych* i *docelowych* magazynów danych w tym przewodniku Szybki start. Jeśli nie masz konta usługi Azure Storage ogólnego przeznaczenia, zobacz [Tworzenie konta magazynu,](../articles/storage/common/storage-account-create.md) aby je utworzyć. 

#### <a name="get-the-storage-account-name"></a>Pobierz nazwę konta magazynu
Nazwa konta usługi Azure Storage będzie potrzebna dla tego przewodnika Szybki start. Poniższa procedura zawiera kroki, aby uzyskać nazwę konta magazynu: 

1. W przeglądarce sieci Web przejdź do [witryny Azure portal](https://portal.azure.com) i zaloguj się przy użyciu nazwy użytkownika i hasła platformy Azure.
2. Z menu Portal platformy Azure wybierz **pozycję Wszystkie usługi**, a następnie wybierz pozycję **Storage** > Konta**magazynu**. Możesz również wyszukiwać i *wybierać konta magazynu* z dowolnej strony.
3. Na stronie **Konta magazynu** filtruj konto magazynu (w razie potrzeby), a następnie wybierz konto magazynu. 

Możesz również wyszukiwać i *wybierać konta magazynu* z dowolnej strony.

#### <a name="create-a-blob-container"></a>Tworzenie kontenera obiektów blob
W tej sekcji utworzysz kontener obiektów blob o nazwie **adftutorial** w usłudze Azure Blob Storage.

1. Na stronie konta magazynu wybierz pozycję **Kontenery** > **przeglądowe**.
2. Na * \<pasku *narzędzi strony Nazwa konta> -  **Kontenery** wybierz pozycję **Kontener**.
3. W oknie dialogowym **Nowy kontener** wprowadź jako nazwę **adftutorial**, a następnie wybierz przycisk **OK**. * \<Nazwa konta>*  -  **kontenery** strona jest aktualizowana w celu uwzględnienia **adftutorial** na liście kontenerów.

   ![Lista kontenerów](media/data-factory-quickstart-prerequisites/list-of-containers.png)

#### <a name="add-an-input-folder-and-file-for-the-blob-container"></a>Dodawanie folderu wejściowego i pliku kontenera obiektów blob
W tej sekcji utworzysz folder o nazwie **dane wejściowe** w właśnie utworzonym kontenerze, a następnie przekaższ przykładowy plik do folderu wejściowego. Przed rozpoczęciem otwórz edytor tekstu, taki jak **Notatnik,** i utwórz plik o nazwie **emp.txt** z następującą zawartością:

```emp.txt
John, Doe
Jane, Doe
```

Zapisz plik w folderze **C:\ADFv2QuickStartPSH.** (Jeśli folder jeszcze nie istnieje, utwórz go). Następnie wróć do witryny Azure portal i wykonaj następujące kroki:

1. Na stronie * \<Nazwa konta>*  -  **kontenery,** na której zostało przerwane, wybierz **adftutorial** ze zaktualizowanej listy kontenerów.

   1. Jeśli okno zostało zamknięte lub przeszło do innej strony, zaloguj się ponownie do [witryny Azure portal.](https://portal.azure.com)
   1. Z menu Portal platformy Azure wybierz **pozycję Wszystkie usługi**, a następnie wybierz pozycję **Storage** > Konta**magazynu**. Możesz również wyszukiwać i *wybierać konta magazynu* z dowolnej strony.
   1. Wybierz swoje konto magazynu, a następnie wybierz **pozycję Kontenery** > **adftutorial .**

2. Na pasku narzędzi strony kontenera **adftutorial** wybierz pozycję **Przekaż**.
3. Na stronie **Przekazywanie obiektów blob** zaznacz pole **Pliki,** a następnie przejdź do pliku **emp.txt** i wybierz go.
4. Rozwiń nagłówek **Zaawansowane.** Strona jest teraz wyświetlana w następujący sposób:

   ![Wybieranie linku Zaawansowane](media/data-factory-quickstart-prerequisites/upload-blob-advanced.png)
5. W polu **Przekaż do folderu** wprowadź **wprowadź dane wejściowe**.
6. Wybierz przycisk **Przekaż**. Na liście powinien pojawić się plik **emp.txt** i stan przekazywania.
7. Wybierz ikonę **Zamknij** **(X),** aby zamknąć stronę **Przekaż obiekt blob.**

Zachowaj otwartą stronę **kontenera adftutorial.** Będzie ona używana do weryfikowania danych wyjściowych na końcu tego samouczka Szybki start.
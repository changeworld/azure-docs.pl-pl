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
ms.openlocfilehash: 26793c91c85ba4cc33a07358ab8ff318becb325f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490677"
---
## <a name="prerequisites"></a>Wymagania wstępne

### <a name="azure-subscription"></a>Subskrypcja platformy Azure
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

### <a name="azure-roles"></a>Role platformy Azure
Aby utworzyć wystąpienia usługi Data Factory, konto użytkownika używane do logowania się na platformie Azure musi być członkiem roli *współautora* lub *właściciela* albo *administratorem* subskrypcji platformy Azure. Aby wyświetlić uprawnienia, które masz w subskrypcji, przejdź do [Azure Portal](https://portal.azure.com), wybierz swoją nazwę użytkownika w prawym górnym rogu, wybierz pozycję **więcej opcji** (...), a następnie wybierz pozycję **Moje uprawnienia**. Jeśli masz dostęp do wielu subskrypcji, wybierz odpowiednią subskrypcję.

Aby utworzyć zasoby podrzędne — w tym zestawy danych, połączone usługi, potoki, wyzwalacze i środowiska Integration Runtime — oraz nimi zarządzać w usłudze Data Factory, należy spełnić następujące wymagania:

- Aby utworzyć zasoby podrzędne i nimi zarządzać, trzeba należeć do roli **Współautor w usłudze Data Factory** na poziomie grupy zasobów lub wyższej.
- W przypadku tworzenia zasobów podrzędnych i zarządzania nimi za pomocą programu PowerShell lub zestawu SDK rola **współautora** na poziomie grupy zasobów lub wyższej jest wystarczająca.

Aby uzyskać przykładowe instrukcje dotyczące dodawania użytkownika do roli, zobacz artykuł [Add roles (Dodawanie ról)](../articles/billing/billing-add-change-azure-subscription-administrator.md).

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- [Data Factory Contributor role (Rola współautora w usłudze Data Factory)](../articles/role-based-access-control/built-in-roles.md#data-factory-contributor)
- [Roles and permissions for Azure Data Factory (Role i uprawnienia w usłudze Data Factory)](../articles/data-factory/concepts-roles-permissions.md)

### <a name="azure-storage-account"></a>Konto magazynu Azure
W tym samouczku Szybki start używasz konta usługi Azure Storage ogólnego przeznaczenia (w szczególności usługi Blob Storage) jako *źródłowego* i *docelowego* magazynu danych. Jeśli nie masz konta usługi Azure Storage ogólnego przeznaczenia, zobacz [Tworzenie konta magazynu](../articles/storage/common/storage-quickstart-create-account.md), aby uzyskać informacje o jego tworzeniu. 

#### <a name="get-the-storage-account-name"></a>Pobieranie nazwy konta magazynu
Do tego przewodnika Szybki Start potrzebna jest nazwa konta usługi Azure Storage. Poniższa procedura zawiera opis czynności w celu uzyskania nazwy konta magazynu: 

1. W przeglądarce internetowej przejdź do [Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu nazwy użytkownika i hasła platformy Azure.
2. Z menu Azure Portal wybierz pozycję **wszystkie usługi**, a następnie wybierz pozycję **Magazyn** > **konta magazynu**. Możesz również wyszukać i wybrać *konta magazynu* z dowolnej strony.
3. Na stronie **konta magazynu** odfiltruj konto magazynu (w razie potrzeby), a następnie wybierz swoje konto magazynu. 

Możesz również wyszukać i wybrać *konta magazynu* z dowolnej strony.

#### <a name="create-a-blob-container"></a>Tworzenie kontenera obiektów blob
W tej sekcji utworzysz kontener obiektów blob o nazwie **adftutorial** w usłudze Azure Blob Storage.

1. Na stronie konto magazynu wybierz pozycję **przegląd** > **obiekty blob**.
2. Na pasku narzędzi *\<nazwa konta >*  - **obiektów BLOB** wybierz pozycję **kontener**.
3. W oknie dialogowym **Nowy kontener** wprowadź jako nazwę **adftutorial**, a następnie wybierz przycisk **OK**. *Nazwa konta\<>* Strona - **obiektów BLOB** została zaktualizowana w celu uwzględnienia **adftutorial** na liście kontenerów.

   ![Lista kontenerów](media/data-factory-quickstart-prerequisites/list-of-containers.png)

#### <a name="add-an-input-folder-and-file-for-the-blob-container"></a>Dodaj folder wejściowy i plik dla kontenera obiektów BLOB
W tej sekcji utworzysz folder o nazwie **Input** w kontenerze, który właśnie został utworzony, a następnie przekażesz przykładowy plik do folderu Input. Przed rozpoczęciem należy otworzyć Edytor tekstu, taki jak **Notatnik**, i utworzyć plik o nazwie **EMP. txt** z następującą zawartością:

```emp.txt
John, Doe
Jane, Doe
```

Zapisz plik w folderze **C:\ADFv2QuickStartPSH** . (Jeśli folder jeszcze nie istnieje, utwórz go). Następnie wróć do Azure Portal i wykonaj następujące kroki:

1. Na stronie *\<nazwa konta >*  - **obiektów BLOB** , w której zostałeś pozostawiony, wybierz pozycję **adftutorial** z zaktualizowanej listy kontenerów.

   1. Jeśli zamknięto okno lub przeszedł do innej strony, zaloguj się ponownie do [Azure Portal](https://portal.azure.com) .
   1. Z menu Azure Portal wybierz pozycję **wszystkie usługi**, a następnie wybierz pozycję **Magazyn** > **konta magazynu**. Możesz również wyszukać i wybrać *konta magazynu* z dowolnej strony.
   1. Wybierz konto magazynu, a następnie wybierz pozycję **obiekty blob** > **adftutorial**.

2. Na pasku narzędzi strony kontenera **adftutorial** wybierz pozycję **Przekaż**.
3. Na stronie **przekazywanie obiektu BLOB** zaznacz pole **pliki** , a następnie wyszukaj i wybierz plik **EMP. txt** .
4. Rozwiń nagłówek **Zaawansowane** . Zostanie wyświetlona strona, jak pokazano:

   ![Wybieranie linku Zaawansowane](media/data-factory-quickstart-prerequisites/upload-blob-advanced.png)
5. W polu **Przekaż do folderu** wprowadź **dane wejściowe**.
6. Wybierz przycisk **Przekaż**. Na liście powinien pojawić się plik **emp.txt** i stan przekazywania.
7. Wybierz ikonę **Zamknij** ( **symbol X**), aby zamknąć stronę **przekazywanie obiektu BLOB** .

Pozostaw otwartą stronę kontenera **adftutorial** . Będzie ona używana do weryfikowania danych wyjściowych na końcu tego samouczka Szybki start.
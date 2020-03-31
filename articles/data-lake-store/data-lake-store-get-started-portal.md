---
title: Wprowadzenie do usługi Azure Data Lake Storage Gen1 — portal
description: Użyj witryny Azure Portal, aby utworzyć konto Data Lake Storage Gen1 i wykonać podstawowe operacje na koncie.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 1bfb9362aa0e893a8bf1a758e5a0069e85ab227b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265586"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-the-azure-portal"></a>Wprowadzenie do usługi Azure Data Lake Storage Gen1 przy użyciu portalu Azure

> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [Powershell](data-lake-store-get-started-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Dowiedz się, jak za pomocą witryny Azure Portal utworzyć konto usługi Data Lake Storage Gen1 i wykonać podstawowe operacje, takie jak tworzenie folderów, przekazywanie i pobieranie plików danych, usuwanie konta itp. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Data Lake Storage Gen1](data-lake-store-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed przystąpieniem do wykonywania kroków opisanych w tym samouczku musisz mieć poniższe:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-storage-gen1-account"></a>Tworzenie konta usługi Data Lake Storage Gen1

1. Zaloguj się do nowego [portalu Azure](https://portal.azure.com).
2. Kliknij **pozycję Utwórz zasób > Magazyn > Magazyn danych Gen1**.
3. W **bloku New Data Lake Storage Gen1** podaj wartości, jak pokazano na poniższym zrzucie ekranu:

    ![Tworzenie nowego konta Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Tworzenie nowego konta Data Lake Storage Gen1")

   * **Nazwa**. Wprowadź unikatową nazwę konta Data Lake Storage Gen1.
   * **Subskrypcja**. Wybierz subskrypcję, w ramach której chcesz utworzyć nowe konto Data Lake Storage Gen1.
   * **Grupa zasobów**. Wybierz istniejącą grupę zasobów lub użyj opcji **Utwórz nową**, aby utworzyć taką grupę. Grupa zasobów to kontener, który zawiera powiązane zasoby dla aplikacji. Aby uzyskać więcej informacji, zobacz [Grupy zasobów na platformie Azure](../azure-resource-manager/management/overview.md#resource-groups).
   * **Lokalizacja:** Wybierz lokalizację, w której chcesz utworzyć konto Data Lake Storage Gen1.
   * **Ustawienia szyfrowania**. Dostępne są trzy opcje:

     * **Nie włączaj szyfrowania**.
     * **Użyj kluczy zarządzanych przez Data Lake Storage Gen1**, jeśli chcesz, aby data lake storage Gen1 zarządzał kluczami szyfrowania.
     * **Użyj kluczy z własnej usługi Key Vault**. Możesz wybrać istniejącą usługę Azure Key Vault lub utworzyć nową usługę Key Vault. Aby użyć kluczy z usługi Key Vault, należy przypisać uprawnienia do konta Data Lake Storage Gen1, aby uzyskać dostęp do usługi Azure Key Vault. Aby uzyskać instrukcje, zobacz [Przypisywanie uprawnień do usługi Azure Key Vault](#assign-permissions-to-azure-key-vault).

        ![Szyfrowanie Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/adls-encryption-2.png "Szyfrowanie Data Lake Storage Gen1")

        Kliknij przycisk **OK** w bloku **Ustawienia szyfrowania**.

        Aby uzyskać więcej informacji, zobacz [Szyfrowanie danych w usłudze Azure Data Lake Storage Gen1](./data-lake-store-encryption.md).

4. Kliknij przycisk **Utwórz**. Jeśli zdecydujesz się przypiąć konto do pulpitu nawigacyjnego, nastąpi powrót do pulpitu nawigacyjnego i zobaczysz postęp inicjowania obsługi administracyjnej konta usługi Data Lake Storage Gen1. Po udostępnieniu konta Data Lake Storage Gen1 pojawi się blok konta.

## <a name="assign-permissions-to-azure-key-vault"></a><a name="assign-permissions-to-azure-key-vault"></a>Przypisywanie uprawnień do usługi Azure Key Vault

Jeśli do skonfigurowania szyfrowania na koncie Usługi Data Lake Storage Gen1 użyto kluczy z usługi Azure Key Vault, należy skonfigurować dostęp między kontem Data Lake Storage Gen1 a kontem usługi Azure Key Vault. W tym celu wykonaj poniższe kroki.

1. Jeśli użyto kluczy z usługi Azure Key Vault, w bloku dla konta Data Lake Storage Gen1 wyświetla ostrzeżenie u góry. Kliknij ostrzeżenie, aby otworzyć obszar **Szyfrowanie**.

    ![Szyfrowanie Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/adls-encryption-3.png "Szyfrowanie Data Lake Storage Gen1")
2. Blok zawiera dwie opcje umożliwiające skonfigurowanie dostępu.

    ![Szyfrowanie Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/adls-encryption-4.png "Szyfrowanie Data Lake Storage Gen1")

   * W pierwszej opcji kliknij pozycję **Udziel uprawnień** w celu skonfigurowania dostępu. Pierwsza opcja jest włączona tylko wtedy, gdy użytkownik, który utworzył konto Data Lake Storage Gen1 jest również administratorem usługi Azure Key Vault.
   * Druga opcja polega na uruchomieniu polecenia cmdlet programu PowerShell wyświetlonego w bloku. Musisz być właścicielem usługi Azure Key Vault lub mieć możliwość udzielania uprawnień w usłudze Azure Key Vault. Po uruchomieniu polecenia cmdlet wróć do bloku i kliknij przycisk **Włącz**, aby skonfigurować dostęp.

> [!NOTE]
> Można również utworzyć konto Data Lake Storage Gen1 przy użyciu szablonów usługi Azure Resource Manager. Te szablony są dostępne na stronie [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?term=data+lake+store):
> * Bez szyfrowania danych: [wdrażanie konta usługi Azure Data Lake Storage Gen1 bez szyfrowania danych](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/).
> * Szyfrowanie danych przy użyciu usługi Data Lake Storage Gen1: [Wdrażanie konta Data Lake Storage Gen1 z szyfrowaniem (Data Lake)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/).
> * Szyfrowanie danych przy użyciu usługi Azure Key Vault: [Wdrażanie konta Data Lake Storage Gen1 z szyfrowaniem (Key Vault)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/).
>
>

## <a name="create-folders"></a><a name="createfolder"></a>Tworzenie folderów

Foldery można tworzyć na koncie Data Lake Storage Gen1, aby zarządzać danymi i przechowywać je.

1. Otwórz utworzone konto Data Lake Storage Gen1. W lewym okienku kliknij pozycję **Wszystkie zasoby**, a następnie w bloku **Wszystkie zasoby** kliknij nazwę konta, pod którym chcesz utworzyć foldery. Jeśli konto jest przypięte do tablicy startowej, kliknij kafelek konta.
2. W bloku konta Data Lake Storage Gen1 kliknij pozycję **Eksplorator danych**.

    ![Tworzenie folderów na koncie Gen1 magazynu usługi Data Lake](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Tworzenie folderów na koncie Gen1 magazynu usługi Data Lake")
3. W bloku Eksplorator danych kliknij pozycję **Nowy folder**, wprowadź nazwę nowego folderu, a następnie kliknij przycisk **OK**.

    ![Tworzenie folderów na koncie Gen1 magazynu usługi Data Lake](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Tworzenie folderów na koncie Gen1 magazynu usługi Data Lake")

    Nowo utworzony folder jest widoczny na liście w bloku **Eksplorator danych**. Foldery zagnieżdżone można tworzyć do dowolnego poziomu.

    ![Tworzenie folderów na koncie usługi Data Lake](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Tworzenie folderów na koncie usługi Data Lake")

## <a name="upload-data"></a><a name="uploaddata"></a>Przekazywanie danych

Dane można przekazać do konta Data Lake Storage Gen1 bezpośrednio na poziomie głównym lub do folderu utworzonego na koncie.

1. W bloku **Eksplorator danych** kliknij przycisk **Przekaż**.
2. W bloku **Przekaż pliki** przejdź do plików, które chcesz przekazać, a następnie kliknij pozycję **Dodaj wybrane pliki**. Możesz też wybrać więcej niż jeden plik do przekazania.

    ![Przekazywanie danych](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Przekazywanie danych")

Jeśli szukasz przykładowych danych do przekazania, możesz pobrać folder **Ambulance Data** z [repozytorium Git usługi Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="actions-available-on-the-stored-data"></a><a name="properties"></a>Akcje dostępne dla przechowywanych danych

Kliknij ikonę wielokropka dla pliku, a następnie w menu podręcznym kliknij akcję, którą chcesz wykonać na danych.

![Właściwości danych](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Właściwości danych")

## <a name="secure-your-data"></a>Zabezpieczanie danych

Dane przechowywane na koncie Usługi Data Lake Storage Gen1 można zabezpieczyć przy użyciu usługi Azure Active Directory i kontroli dostępu (Listy ACL). Aby uzyskać instrukcje dotyczące sposobu wykonywania tego celu, zobacz [Zabezpieczanie danych w usłudze Azure Data Lake Storage Gen1](data-lake-store-secure-data.md).

## <a name="delete-your-account"></a>Usuń konto

Aby usunąć konto Data Lake Storage Gen1, z bloku Data Lake Storage Gen1 kliknij pozycję **Usuń**. W celu potwierdzenia akcji zostanie wyświetlony monit o wprowadzenie nazwy konta, które chcesz usunąć. Wprowadź nazwę konta, a następnie kliknij pozycję **Usuń**.

![Usuń konto Gen1 magazynu usługi Data Lake](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Usuwanie konta usługi Data Lake")

## <a name="next-steps"></a>Następne kroki

* [Korzystanie z usługi Azure Data Lake Storage Gen1 dla wymagań dotyczących dużych zbiorów danych](data-lake-store-data-scenarios.md)
* [Zabezpieczanie danych w usłudze Data Lake Storage 1. generacji](data-lake-store-secure-data.md)
* [Korzystanie z usługi Azure Data Lake Analytics z usługą Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Korzystanie z usługi Azure HDInsight z usługą Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

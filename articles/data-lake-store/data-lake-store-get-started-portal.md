---
title: Rozpoczynanie pracy z usługą Azure Data Lake Storage Gen1 za pomocą witryny Azure portal | Dokumentacja firmy Microsoft
description: Aby utworzyć konto usługi Azure Data Lake Storage Gen1 i wykonywać podstawowe operacje w ramach konta Data Lake Storage Gen1, należy użyć witryny Azure portal.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: e021d8c056028c03ac71d2a27c9128272f374da6
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58883585"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-the-azure-portal"></a>Rozpoczynanie pracy z usługą Azure Data Lake Storage Gen1 przy użyciu witryny Azure portal

> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](data-lake-store-get-started-cli-2.0.md)
>
> 

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Dowiedz się, jak używać witryny Azure portal, aby utworzyć konto usługi Azure Data Lake Storage Gen1 i wykonywać podstawowe operacje, takie jak tworzenie folderów, przekazywanie i pobieranie plików danych, usuwanie konta itp. Aby uzyskać więcej informacji, zobacz [przegląd z usługi Azure Data Lake magazynu Gen1](data-lake-store-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne
Przed przystąpieniem do wykonywania kroków opisanych w tym samouczku musisz mieć poniższe:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-storage-gen1-account"></a>Tworzenie konta Data Lake Storage Gen1

1. Zaloguj się w nowej witrynie [Azure Portal](https://portal.azure.com).
2. Kliknij przycisk **Utwórz zasób > Magazyn > Data Lake Storage Gen1**.
3. W **nowe Data Lake Storage Gen1** bloku, podaj wartości, jak pokazano na poniższym zrzucie ekranu:
   
    ![Tworzenie nowego konta Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "utworzenie nowego konta Data Lake Storage Gen1")
   
   * **Nazwa**. Wprowadź unikatową nazwę konta Data Lake Storage Gen1.
   * **Subskrypcja**. Wybierz subskrypcję, w ramach której chcesz utworzyć nowe konto Data Lake Storage Gen1.
   * **Grupa zasobów**. Wybierz istniejącą grupę zasobów lub użyj opcji **Utwórz nową**, aby utworzyć taką grupę. Grupa zasobów to kontener, który zawiera powiązane zasoby dla aplikacji. Aby uzyskać więcej informacji, zobacz [Grupy zasobów na platformie Azure](../azure-resource-manager/resource-group-overview.md#resource-groups).
   * **Lokalizacja**: Wybierz lokalizację, w której chcesz utworzyć konta Data Lake Storage Gen1.
   * **Ustawienia szyfrowania**. Dostępne są trzy opcje:
     
     * **Nie włączaj szyfrowania**.
     * **Użyj kluczy zarządzanych przez Data Lake Storage Gen1**, jeśli chcesz, aby Data Lake Storage Gen1 zarządzać kluczami szyfrowania.
     * **Użyj kluczy z własnej usługi Key Vault**. Możesz wybrać istniejącą usługę Azure Key Vault lub utworzyć nową usługę Key Vault. Aby użyć kluczy z usługi Key Vault, należy przypisać uprawnienia dla konta Data Lake Storage Gen1 dostępu do usługi Azure Key Vault. Aby uzyskać instrukcje, zobacz [Przypisywanie uprawnień do usługi Azure Key Vault](#assign-permissions-to-azure-key-vault).
       
        ![Data Lake Storage Gen1 szyfrowania](./media/data-lake-store-get-started-portal/adls-encryption-2.png "Data Lake Storage Gen1 szyfrowania")
       
        Kliknij przycisk **OK** w bloku **Ustawienia szyfrowania**.

        Aby uzyskać więcej informacji, zobacz [szyfrowanie danych w usłudze Azure Data Lake magazynu Gen1](./data-lake-store-encryption.md).

4. Kliknij pozycję **Utwórz**. Jeśli wybrano opcję przypięcia konta do pulpitu nawigacyjnego, nastąpi powrót do pulpitu nawigacyjnego i widoczny postęp aprowizowania konta Data Lake Storage Gen1. Po aprowizacji konta Data Lake Storage Gen1 pojawi się blok konta.

## <a name="assign-permissions-to-azure-key-vault"></a>Przypisywanie uprawnień do usługi Azure Key Vault
Jeśli używasz kluczy z usługi Azure Key Vault do skonfigurowania szyfrowania konta Data Lake Storage Gen1, należy skonfigurować dostęp między konta Data Lake Storage Gen1 i konta usługi Azure Key Vault. W tym celu wykonaj poniższe kroki.

1. Jeśli używasz kluczy z usługi Azure Key Vault, w bloku konta Data Lake Storage Gen1 zostanie wyświetlone ostrzeżenie, u góry. Kliknij ostrzeżenie, aby otworzyć obszar **Szyfrowanie**.
   
    ![Data Lake Storage Gen1 szyfrowania](./media/data-lake-store-get-started-portal/adls-encryption-3.png "Data Lake Storage Gen1 szyfrowania")
2. Blok zawiera dwie opcje umożliwiające skonfigurowanie dostępu.

    ![Data Lake Storage Gen1 szyfrowania](./media/data-lake-store-get-started-portal/adls-encryption-4.png "Data Lake Storage Gen1 szyfrowania")
   
   * W pierwszej opcji kliknij pozycję **Udziel uprawnień** w celu skonfigurowania dostępu. Pierwsza opcja jest włączona, tylko wtedy, gdy użytkownik, który utworzył konta Data Lake Storage Gen1 jest również administratorem usługi Azure Key Vault.
   * Druga opcja polega na uruchomieniu polecenia cmdlet programu PowerShell wyświetlonego w bloku. Musisz być właścicielem usługi Azure Key Vault lub mieć możliwość udzielania uprawnień w usłudze Azure Key Vault. Po uruchomieniu polecenia cmdlet wróć do bloku i kliknij przycisk **Włącz**, aby skonfigurować dostęp.

> [!NOTE]
> Można również utworzyć konta Data Lake Storage Gen1, za pomocą szablonów usługi Azure Resource Manager. Te szablony są dostępne na stronie [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?term=data+lake+store):
> - Bez szyfrowania danych: [Wdrażanie konta usługi Azure Data Lake Storage Gen1 bez szyfrowania danych](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/).
> - Za pomocą szyfrowania danych przy użyciu Data Lake Storage Gen1: [Wdrażanie konta Data Lake Storage Gen1 z szyfrowaniem (Data Lake)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/).
> - Za pomocą szyfrowania danych przy użyciu usługi Azure Key Vault: [Wdrażanie konta Data Lake Storage Gen1 z szyfrowaniem (Key Vault)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/).
> 
> 



## <a name="createfolder"></a>Tworzenie folderów w ramach konta Data Lake Storage Gen1
Można tworzyć foldery w ramach konta usługi Data Lake Storage Gen1 do przechowywania danych i zarządzania nimi.

1. Otwórz konto Data Lake Storage Gen1, który został utworzony. W okienku po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie w bloku Wszystkie zasoby kliknij nazwę konta, w ramach którego chcesz utworzyć foldery. Jeśli konto jest przypięte do tablicy startowej, kliknij kafelek konta.
2. W bloku konta usługi Data Lake Storage Gen1 kliknij **Eksplorator danych**.
   
    ![Tworzenie folderów w ramach konta Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "tworzenie folderów w ramach konta Data Lake Storage Gen1")
3. W bloku Eksplorator danych kliknij pozycję **Nowy folder**, wprowadź nazwę nowego folderu, a następnie kliknij przycisk **OK**.
   
    ![Tworzenie folderów w ramach konta Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "tworzenie folderów w ramach konta Data Lake Storage Gen1")
   
    Nowo utworzony folder jest widoczny na liście w bloku **Eksplorator danych**. Można utworzyć folderów zagnieżdżonych do dowolnego poziomu.
   
    ![Tworzenie folderów na koncie usługi Data Lake](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "tworzenie folderów na koncie usługi Data Lake")

## <a name="uploaddata"></a>Przekazywanie danych do konta Data Lake Storage Gen1
Możesz przekazać dane do konta Data Lake Storage Gen1 bezpośrednio na poziomie katalogu głównego lub do folderu, który został utworzony w ramach konta. 

1. W bloku **Eksplorator danych** kliknij przycisk **Przekaż**. 
2. W bloku **Przekaż pliki** przejdź do plików, które chcesz przekazać, a następnie kliknij pozycję **Dodaj wybrane pliki**. Możesz też wybrać więcej niż jeden plik do przekazania.

    ![Przekazywanie danych](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Przekazywanie danych")

Jeśli szukasz przykładowych danych do przekazania, możesz pobrać folder **Ambulance Data** z [repozytorium Git usługi Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="properties"></a>Akcje dostępne dla przechowywanych danych
Kliknij ikonę wielokropka dla pliku, a następnie w menu podręcznym kliknij akcję, którą chcesz wykonać na danych.

![Właściwości danych](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Właściwości danych") 

## <a name="secure-your-data"></a>Zabezpieczanie danych
Można zabezpieczyć dane przechowywane na koncie usługi Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory i kontroli dostępu (ACL). Aby uzyskać instrukcje, jak to zrobić, zobacz [Zabezpieczanie danych w usłudze Azure Data Lake magazynu Gen1](data-lake-store-secure-data.md).

## <a name="delete-a-data-lake-storage-gen1-account"></a>Usuwanie konta Data Lake Storage Gen1
Aby usunąć konta Data Lake Storage Gen1, z bloku usługi Data Lake Storage Gen1, kliknij przycisk **Usuń**. W celu potwierdzenia akcji zostanie wyświetlony monit o wprowadzenie nazwy konta, które chcesz usunąć. Wprowadź nazwę konta, a następnie kliknij pozycję **Usuń**.

![Usuwanie konta Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "konta usługi Data Lake Delete")

## <a name="next-steps"></a>Kolejne kroki
* [Użyj usługi Azure Data Lake Storage Gen1 dla wymagających danych big Data](data-lake-store-data-scenarios.md) 
* [Zabezpieczanie danych w Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Za pomocą usług Azure Data Lake Analytics Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usługa Azure HDInsight za pomocą programu Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

